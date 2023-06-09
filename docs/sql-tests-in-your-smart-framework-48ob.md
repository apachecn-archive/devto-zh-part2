# 智能框架中的 SQL 测试

> 原文：<https://dev.to/riter/sql-tests-in-your-smart-framework-48ob>

许多人抱怨 sql 代码以这样或那样的形式很快变得不受支持，事实上，甚至不值得开始用 sql 编写任何逻辑。“只使用 sql 作为表，并实现代码中的所有逻辑”，他们说，同时也不无狡猾。

那么，告诉我，一些特定的数据类型在 sql 中仍然是允许的，不是吗？比如用 enum 代替 string，是被认为可耻，还是仍然可以接受？甚至在代码纯度的狂热捍卫者看来，这似乎也是允许的。那么使用具有一定精度(精度和小数位数)的浮点数又如何呢——它仍然合格吗？或者像`created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP`这样合理的默认值，我们到目前为止都是被允许使用的吧？

如果以上所有问题的答案都是肯定的，那么很可能我们还可以添加数据验证，以确保符合比如说字符串的格式。我们的意思是，例如，检查电子邮件地址的格式，在地址栏的标识符字段中没有危险字符(`slug`或`uid`)，或者甚至验证特定记录子集中的唯一性(`scope`)。

这是一条通向存储过程的捷径。比方说，您有一个“名称”字段，您想确保该字段的值是唯一的，而不需要匹配大小写。很典型的任务吧？传统的 MVP 框架说验证应该提前完成，像`SELECT 1 FROM table_name WHERE lower(name) = $1`，如果记录存在，那么保存过程甚至不能开始。数据库，作为一个概念，告诉我们应该在记录的时候进行检查。在某些情况下，表上的约束帮助了我们，在其他情况下，我们可以应用规则和触发器。一般来说，用数据库来做要方便得多，只是对编写代码的支持变得更加复杂。

在我们的项目[riter.co](https://riter.co)中，我们提出了一些简单的规则，遵守这些规则，你就不用担心 sql 代码，就像担心项目的其他部分一样，例如 javascript 代码或 css 规则。

*   当你创建一个规则、一个触发器和一个约束时，有必要清楚地**区分情况**。规则应该在一个单独的项目中由你自己更好地定义，但是无论如何你肯定会遵循一些创建的优先级。比如，如果可以创建约束，那么就创建一个约束。如果没有，您应该创建一个规则。作为最后的手段，如果规则不适合你的情况，那就使用触发器。

*   **命名标准**触发器和规则。如果您想在表上创建某种类型的触发器，名称必须唯一定义。我建议从`"#{table_name}_#{affected_colum_name}_#{verb}_on_#{action}"`开始考虑这个命名规则。你会得到类似`"companies_subdomain_lowercase_on_insert"`的东西。除此之外，再用面具寻找现有规则就会容易得多:

```
 SELECT routine_name AS name
  FROM information_schema.routines
  WHERE
    routine_name LIKE 'companies_%_on_insert' AND
    routine_type ='FUNCTION' AND
    specific_schema='public'; 
```

Enter fullscreen mode Exit fullscreen mode

*   亲亲。您数据库中的所有程序**必须是简单的**。复杂的事情导致复杂的 bug，数据库层面的 bug 相当吓人。让我们保持简单。最好把复杂的逻辑留给高级编程语言和它们的框架。例如，我们可以将所有内容都转换成小写，或者在数据库中生成一个惟一的标识符，但是如果要检查时间间隔的交集，那么最好在代码中完成。

*   **测试**。其实整篇帖子就是为了这一点而写的，上面说的都更像是序言。

测试应该一如既往地以三种方式呈现:集成、单元和验收。

单元测试的意义要小得多，在绝大多数情况下，它们必须用高级代码编写。为一些独立于数据库当前状态的单个过程编写单元测试是一个很好的实践。例如，我们有这样一个在单元测试环境中看起来非常好的过程:

```
CREATE FUNCTION public.generate_slug(slug character varying, table_name character varying) RETURNS character varying
    LANGUAGE plpgsql
    AS $$
      DECLARE
        key varchar;
        query text;
        found varchar;
      BEGIN
        query := 'SELECT slug FROM ' || quote_ident(table_name) || ' WHERE slug = ';
        key := slug;

        EXECUTE query || quote_literal(slug) INTO found;

          WHILE found IS NOT NULL LOOP
            key := encode(gen_random_bytes(5), 'base64');
            key := replace(key, '/', '-');
            key := replace(key, '+', '-');
            key := replace(key, '=', '');
            key := slug || '-' || key;

            EXECUTE query || quote_literal(key) INTO found;
          END LOOP;

        RETURN key;
      END;
      $$; 
```

Enter fullscreen mode Exit fullscreen mode

总的来说，实现一个完整的模拟和存根是不可能的，所以真正的单元测试也无法编写。

集成测试应该用为项目选择的框架语言来编写，并且他们必须检查你所有的 sql 触发器的动作都反映在你的程序的对象和结构中。比如:

```
subject { create(:company, name: 'CamelCase') }
its(:name) { is_expected.to eq('camelcase') } 
```

Enter fullscreen mode Exit fullscreen mode

首先，它将检查正常工作的`RETURNING name`。其次，它将测试你的框架的工作以及它与数据库合作的意愿。例如，如果您的框架准备好监控某些字段中数据的正确性，那么这样的测试可以揭示冲突，并且可以及时解决。

那么**验收测试**呢，这就是事情变得有趣的地方。毕竟，究竟是什么使得 name 字段中的所有字母都是小写的并不重要，无论是触发器还是规则。确信结果要重要得多。

首先，你需要在结构上达成一致。我们考虑了几个选项，并得出了最合理的一个——对于每个单独的测试，我们创建一个单独的函数，然后调用它并检查返回值。当然，我们希望这些测试与所有其他测试一起运行，所以我们在框架中使用了一个小包装器。

下面是测试本身:

```
it { expect(ApplicationRecord.connection.execute("SELECT #{f.function_name}()").to_a.first[f.function_name]).to eq('ok') } 
```

Enter fullscreen mode Exit fullscreen mode

非常简单，不是吗？当然，在运行测试之前，您需要创建这个函数。我们就在测试前这样做:

```
before do
  ApplicationRecord.connection.execute("CREATE OR REPLACE FUNCTION #{f.function_name}() RETURNS varchar LANGUAGE plpgsql AS $$ #{f.content} $$;")
end 
```

Enter fullscreen mode Exit fullscreen mode

测试后，我们立即删除函数:

```
after do
  ApplicationRecord.connection.execute("DROP FUNCTION IF EXISTS #{f.function_name}()")
rescue ActiveRecord::StatementInvalid
  nil
end 
```

Enter fullscreen mode Exit fullscreen mode

现在说说这个函数的内容。最方便的解决方案是将内容保存在扩展名为`.sql`的单独文件中，并在创建 sql-function:
之前读取它

```
 Dir[File.expand_path('**/*_spec.sql', __dir__)].map do |path|
      content = IO.read(path).strip
      basename = Pathname.new(path)
                         .relative_path_from(Rails.root.join('spec', 'sql'))
                         .to_s
                         .gsub('/', '__')
                         .gsub(/_spec.sql\z/, '')
      OpenStruct.new(path: path.to_s, content: content, empty?: content.empty?, basename: basename, function_name: "rspec_#{basename}")
    end 
```

Enter fullscreen mode Exit fullscreen mode

函数的名称，即`function_name`，可以从带有可靠前缀的文件名中生成，这样您就可以将 test-function 与所有其他函数区分开来。然后，在运行所有测试之前和之后，您将能够再次确保数据库中没有多余的内容:

```
DO $$
DECLARE
  routine record;
BEGIN
  FOR routine IN
    SELECT routine_name AS name
    FROM information_schema.routines
    WHERE
      routine_name LIKE 'rspec_%' AND
      routine_type ='FUNCTION' AND
      specific_schema='public'
  LOOP
    EXECUTE 'DROP FUNCTION ' || quote_ident(routine.name);
  END LOOP;
END $$ LANGUAGE plpgsql; 
```

Enter fullscreen mode Exit fullscreen mode

测试本身是一个技术问题。例如，这里有一个单独保存的`company_name_spec.sql`，它将检查`companies`表中`name`字段的唯一性。

```
DECLARE
  company_name companies.name%TYPE;
BEGIN

  INSERT INTO companies (name) VALUES ('qwe1') RETURNING name INTO company_name;
  ASSERT company_name = 'qwe1', 'companies.name is allowed to be a string';

  BEGIN
    INSERT INTO companies (name) VALUES ('qwe1') RETURNING name INTO company_name;
    ASSERT FALSE, 'companies.name should raise unique violation';
  EXCEPTION
    WHEN unique_violation THEN
      ASSERT TRUE;
  END;

  RETURN 'ok';
END; 
```

Enter fullscreen mode Exit fullscreen mode

我想说的不是结论，而是智能数据库是必须进行全面测试的地方。把你的借口留在过去，现在你知道怎么写了。