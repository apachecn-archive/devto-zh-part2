# SQL on Rails 概念

> 原文：<https://dev.to/riter/sql-on-rails-concept-1f5m>

首先，这篇文章不是关于我有多爱 Rails。其次，这不是我有多讨厌它。我们可以用完全不同的方式对待 Rails，只有我们做出改变，它才会变得更好。但是，如果我们试图对 Rails 进行修正，它会变得更糟。好吧，不管怎样，你已经被警告了，我希望你抓到我了。

ActiveRecord 的一个基本概念是，数据库本质上是相当功利的，可以很容易地修改。所以，你正坐着为 MySQL 编写模型，突然你发现用 MongoDB 替换 MySQL 是如此简单。嗯，没有这么彻底，但是，比方说，你可能有理由用 PostgreSQL 替换 MySQL。反之亦然，我并不反对 MySQL。

在这里，ActiveRecord 声称可以派上用场，因为它应该是小菜一碟。毕竟，作用域、前/后过滤器和关联足够抽象，不用担心在数据库上生成查询，只关注应用程序的逻辑。不用编写`WHERE is_archived = TRUE`，你可以很容易地编写`where(is_archived: true)`，ActiveRecord 会为你完成剩下的工作。

[![](img/a70bbcf3f8896f2e7d2a445cbce49a63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oO9tKpx7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.pinimg.com/originals/a1/c1/9c/a1c19cc429092ebb57ee218f34d7a525.gif)

但是事情并没有那么简单！在实践中，事实证明这一抽象层充满了缺口，就像《金鱼的故事》中的破槽一样。而且许多基本功能无法使用，比如比较日期或使用数组。我们有带强制`where("#{quoted_table_name}.finished_at >= ?", Date.current)`或`where("#{quoted_table_name}.other_ids <@ ARRAY[?]", ids)`的示波器。对此 ActiveRecord 给出了一个完全有意识的、意料之中的答案:不要用这个。使用 habtm-association 而不是数组，如果需要比较日期，请接受这个。而且天理不容，你在这样的范围内错过了`quoted_table_name`——第一个`includes`或者`joins`会把一切都放回原处。尽可能随时随地添加它们会更容易，这样你就不会失去技能。

当然，一旦你决定干涉 ActiveRecord 的工作，就没有回头路了。不仅仅是机会，而且无痛过渡到另一个数据库的模糊希望也将消失。把这段代码打印出来烧了会好很多。当然，没有其他理由不在应用程序中使用额外的数据库功能。欢迎大家使用，让大家做！

当发现 models 文件夹中使用 extra-opportunities 的范围超过一半时，很明显 ActiveRecord 只是一个方便的包装器，用于将一段带标签的代码与另一段代码集成在一起。像`where(is_archived: true).joins(:sprint).merge(Sprint.archived)`这样的示波器会工作得很好，它们的组合不会比煮鸡蛋更困难，不是吗？

[![](img/9d3ad6b0d0880aea5e95e3f72c9ce73e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DlZbX4zA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://gifs.com.ua/uploads/gifs-com-ua-736459401.gif)

下一个阶段是反规格化。当然，反规格化一直都在，也没有在任何地方消失，但是解决这个问题的重任落在了 Rails 和 ActiveRecord 的肩上，而且你知道，这两个家伙在资源需求方面并没有受到过度的限制。比方说，`counter_cache: true`是反规格化的第一步，毕竟 ActiveRecord 不会让你这么容易就只做`COUNT(*) AS sprints_count`(我的意思是，你不会用`select`的方法吧？).并且`counter_cache`远非理想，并且在某些情况下，实际数量可能与缓存数量不同步。当然不是批评，而是不愉快。并且这只是第一个安置在数据库中并且不加载 Ruby 机器的负担头的候选者。只需几个触发器，它就完成了！在 A 表中删除和添加条目时，需要重新计算 B 表中的记录数，仅此而已，对吗？当然，当编辑一个条目时，如果`foreign_key`已经被改变，你也要做同样的事情，因为请求`UPDATE B SET a_id = $1 WHERE id = $2`将对旧表和新表分解`counter_cache`。

```
 CREATE OR REPLACE FUNCTION update_#{parent_table}_#{child_table}_counter_on_insert() RETURNS TRIGGER AS $$
  BEGIN
    UPDATE #{parent_table} SET #{counter_column} = COALESCE((SELECT COUNT(id) FROM #{child_table} GROUP BY #{foreign_column} HAVING #{foreign_column} = NEW.#{foreign_column}), 0) WHERE (#{parent_table}.id = NEW.#{foreign_column});
    RETURN NULL;
  END;
  $$ LANGUAGE plpgsql; 
```

Enter fullscreen mode Exit fullscreen mode

数据库的下一项工作将与日期-时间相关。首先，让`created_at`和`updated_at`字段在数据库本身得到服务，幸运的是，这要简单得多。首先，让我们设置默认值:

```
change_column_default :table_name, :created_at, -> { 'CURRENT_TIMESTAMP' }
change_column_default :table_name, :updated_at, -> { 'CURRENT_TIMESTAMP' } 
```

Enter fullscreen mode Exit fullscreen mode

为了一次在所有地方都这样做，我们可以在所有有这些字段的表中组织一个循环。除了`schema_migrations`和`ar_internal_metadata`表，当然:

```
(tables - %w(schema_migrations ar_internal_metadata)).each { ... } 
```

Enter fullscreen mode Exit fullscreen mode

就这样，现在这些表的默认值将和我们需要的完全一样。现在是时候确保 Rails 不会触及这些字段了。框架的配置中有一个选项，负责这个:

```
Rails.application.config.active_record.record_timestamps = false 
```

Enter fullscreen mode Exit fullscreen mode

因此，下一步是在记录改变时改变`updated_at`字段。很简单:

```
CREATE OR REPLACE FUNCTION touch_for_#{table_name}_on_update() RETURNS TRIGGER AS $$
  BEGIN
    SELECT CURRENT_TIMESTAMP INTO NEW.updated_at;
    RETURN NEW;
  END;
  $$ LANGUAGE plpgsql; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要在模型中完全去掉`touch: true`。这东西和射击场的靶子很像——也是完全漏的。我甚至不会解释为什么，因为你已经知道所有这些案件。这并没有多难，你只需要在需要的地方更新`updated_at`:

```
CREATE OR REPLACE FUNCTION touch_for_#{table_name}_on_update() RETURNS TRIGGER AS $$
  BEGIN
    UPDATE foreign_table_name SET updated_at = CURRENT_TIMESTAMP WHERE id = NEW.foreign_column_name;
    SELECT CURRENT_TIMESTAMP INTO NEW.updated_at;
    RETURN NEW;
  END;
  $$ LANGUAGE plpgsql; 
```

Enter fullscreen mode Exit fullscreen mode

当然，这种触发器的调用顺序会导致额外的动作，但是 Postgres 没有提供任何合理的机制来调用触发器而不改变记录本身。你可以试着写`SET title = title`，但这并不比`SET updated_at = CURRENT_TIMESTAMP`好多少。

完全相同的触发器用于插入，只是`update_at`不是必需的:

```
CREATE OR REPLACE FUNCTION touch_for_#{table_name}_on_insert() RETURNS TRIGGER AS $$
  BEGIN
    UPDATE foreign_table_name SET updated_at = CURRENT_TIMESTAMP WHERE id = NEW.foreign_column_name;
    RETURN NEW;
  END;
  $$ LANGUAGE plpgsql; 
```

Enter fullscreen mode Exit fullscreen mode

当然，您可以尝试用一个函数来编写这个函数，方法是像`IF TG_OP = 'UPDATE' THEN`一样将对当前事件的检查直接添加到触发器中，但是最好是使所有的触发器都尽可能简单，以减少出错的可能性。

您可能希望以某种方式自动生成这样的触发器，然后您很可能需要找到当前表和其余表之间的所有外部关系。在这里，您可以通过这个请求轻松地做到这一点:

```
SELECT ccu.table_name AS foreign_table_name, kcu.column_name AS column_name
  FROM information_schema.table_constraints AS tc
    JOIN information_schema.key_column_usage AS kcu
    ON tc.constraint_name = kcu.constraint_name
    JOIN information_schema.constraint_column_usage AS ccu
    ON ccu.constraint_name = tc.constraint_name
  WHERE constraint_type = 'FOREIGN KEY' AND tc.table_name = '#{table_name}'
  ORDER BY ccu.table_name; 
```

Enter fullscreen mode Exit fullscreen mode

还有一个非常有用的提示。命名模板中的所有触发器，以便能够通过一个请求验证所需触发器的存在与否。例如，该查询将查找所有触摸插入触发器:

```
SELECT routine_name AS name
  FROM information_schema.routines
  WHERE
    routine_name LIKE 'touch_for_%_on_insert' AND
    routine_type ='FUNCTION' AND
    specific_schema='public'; 
```

Enter fullscreen mode Exit fullscreen mode

而最后剩下的才是最可怕的。事实上，Rails 并不是为最起码的智能数据库而设计的，它绝对不关心可以在那里修改什么，除了 ID-field，即使这样也只是在向表中插入数据时。因此，没有合理的方法将`RETURNING id, updated_at`添加到更新查询中，您需要一头扎进 Rails 灌木丛中。

[![](img/5a394566e356cff81560244777f3e314.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DJKUIXJn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rd55kvyvbl4bicpgi0e6.jpg)

Monkey patch 结果并不那么整洁，但主要目标是尽可能减少对当前框架工作的抵触。下面是最后的样子:

```
module ActiveRecord
  module Persistence
    # https://github.com/rails/rails/blob/v5.2.0/activerecord/lib/active_record/persistence.rb#L729-L741
    def _create_record(attribute_names = self.attribute_names)
      attribute_names &= self.class.column_names
      attributes_values = attributes_with_values_for_create(attribute_names)

      an_id, *affected_rows = self.class._insert_record(attributes_values).dup
      self.id ||= an_id if self.class.primary_key
      Hash[ApplicationRecord.custom_returning_columns(self.class.quoted_table_name, :create).take(affected_rows.size).zip(affected_rows)].each do |column_name, value|
        public_send("#{column_name}=", self.class.attribute_types[column_name.to_s].deserialize(value)) if value
      end

      @new_record = false

      yield(self) if block_given?

      id
    end
    private :_create_record

    # https://github.com/rails/rails/blob/v5.2.0/activerecord/lib/active_record/persistence.rb#L710-L725
    def _update_record(attribute_names = self.attribute_names)
      attribute_names &= self.class.column_names
      attribute_names = attributes_for_update(attribute_names)

      if attribute_names.empty?
        affected_rows = []
        @_trigger_update_callback = true
      else
        affected_rows = _update_row(attribute_names)
        @_trigger_update_callback = affected_rows.any?
      end

      Hash[ApplicationRecord.custom_returning_columns(self.class.quoted_table_name, :update).take(affected_rows.size).zip(affected_rows)].each do |column_name, value|
        public_send("#{column_name}=", self.class.attribute_types[column_name.to_s].deserialize(value))
      end

      yield(self) if block_given?

      affected_rows.none? ? 0 : 1
    end
    private :_update_record
  end

  module ConnectionAdapters
    module PostgreSQL
      module DatabaseStatements
        # https://github.com/rails/rails/blob/v5.2.0/activerecord/lib/active_record/connection_adapters/postgresql/database_statements.rb#L93-L96
        def exec_update(sql, name = nil, binds = [])
          execute_and_clear(sql_with_returning(sql), name, binds) { |result| Array.wrap(result.values.first) }
        end

        # https://github.com/rails/rails/blob/v5.2.0/activerecord/lib/active_record/connection_adapters/abstract/database_statements.rb#L147-L152
        def insert(arel, name = nil, pk = nil, _id_value = nil, sequence_name = nil, binds = [])
          sql, binds = to_sql_and_binds(arel, binds)
          exec_insert(sql, name, binds, pk, sequence_name).rows.first
        end
        alias create insert

        # https://github.com/rails/rails/blob/v5.2.0/activerecord/lib/active_record/connection_adapters/postgresql/database_statements.rb#L98-L111
        def sql_for_insert(sql, pk, id_value, sequence_name, binds) # :nodoc:
          table_ref = extract_table_ref_from_insert_sql(sql)
          if pk.nil?
            # Extract the table from the insert sql. Yuck.
            pk = primary_key(table_ref) if table_ref
          end

          returning_columns = quote_returning_column_names(table_ref, pk, :create)
          if returning_columns.any?
            sql = "#{sql} RETURNING #{returning_columns.join(', ')}"
          end

          super
        end

        # No source in original repo
        def quote_returning_column_names(table_ref, pk, action)
          returning_columns = []
          returning_columns << pk if suppress_composite_primary_key(pk)
          returning_columns += ApplicationRecord.custom_returning_columns(table_ref, action)
          returning_columns.map { |column| quote_column_name(column) }
        end

        # No source in original repo
        def sql_with_returning(sql)
          table_ref = extract_table_ref_from_update_sql(sql)

          returning_columns = quote_returning_column_names(table_ref, nil, :update)

          return sql if returning_columns.blank?
          "#{sql} RETURNING #{returning_columns.join(', ')}"
        end

        # No source in original repo
        def extract_table_ref_from_update_sql(sql)
          sql[/update\s("[A-Za-z0-9_."\[\]\s]+"|[A-Za-z0-9_."\[\]]+)\s*set/im]
          Regexp.last_match(1)&.strip
        end
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

最重要的是，这里我们转向`ApplicationRecord.custom_returning_columns`来找出除了 id 之外，我们还对哪些列感兴趣。这个方法大概是这样的:

```
class << self
    def custom_returning_columns(table_ref, action)
      return [] if ['"schema_migrations"', '"ar_internal_metadata"'].include?(table_ref)

      res = []
      res << :created_at if action == :create
      res << :updated_at

      res += case table_ref
             when '"user_applications"'
               [:api_token]
             when '"users"'
               [:session_salt, :password_changed_at]
             # ...
             else
               []
             end

      res
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

所有的例子都是为 PostgreSQL 给出的，而不是为 MySQL 给出的，所以 MySQL 的追随者将不得不重新发明他们自己的轮子。

代替结论，我们可以说，现在痛苦的铁路的头会痛得少一点。像`counter_cache`和`touch`这样的常规过程将会被遗忘，下一篇文章将会关注一些更全局的东西，比如删除悬挂空间、验证数据、级联数据删除，或者删除偏执和 sql 的 rspec。如果这篇文章被证明是有趣的，当然。