# Enkrip 简介

> 原文：<https://dev.to/kuntoaji/introducing-enkrip-2nip>

[Enkrip](https://github.com/kuntoaji/enkrip) 是 Ruby gem，让你的活动记录模型无缝加密解密你想要的属性。

基本上[en clip](https://github.com/kuntoaji/enkrip)只是使用活动记录回调和[Active support::message encryptor](https://api.rubyonrails.org/v5.2.1/classes/ActiveSupport/MessageEncryptor.html)进行加密和解密。

请参见 [Enkrip 示例 Rails 应用程序](https://github.com/kuntoaji/enkrip_example)进行演示。

### 目标

*   无缝加密和解密字符串和数字属性的值
*   与主动模型验证兼容
*   自动将数字属性转换为所需格式

### 局限性

*   在`numeric_attributes`中定义的所有属性将被强制使用 UTF-8 编码
*   Enkrip 需要活动记录 5.2 或更高版本
*   与[活动记录导入](https://rubygems.org/gems/activerecord-import)不兼容

### 安装

将`enkrip`添加到你的 Rails 应用程序的 Gemfile 中，并运行捆绑包安装:

```
gem 'enkrip' 
```

### 配置

安装完成后，需要定义`ENKRIP_LENGTH`、`ENKRIP_SALT`、`ENKRIP_SECRET`环境变量:

```
# example
export ENKRIP_LENGTH=32 # 32 is default value from ActiveSupport::MessageEncryptor.key_len
export ENKRIP_SALT=random_salt_with_length_32 # you can generate from SecureRandom.random_bytes(YOUR_ENKRIP_LENGTH)
export ENKRIP_SECRET=random_secret_with_length_32 
```

### 用法

对加密属性使用文本数据类型

```
# migration

class CreatePosts < ActiveRecord::Migration[5.2]
  def change
    create_table :posts do |t|
      t.text :my_string
      t.text :my_numeric

      t.timestamps
    end
  end
end 
```

运行迁移后，定义你加密的属性

```
# Active Record model

class Post < ActiveRecord::Base
  include Enkrip::Model

  enkrip_configure do |config|
    config.string_attributes << :my_string
    config.numeric_attributes << :my_numeric
    config.purpose = :example # optional, default is nil
    config.convert_method_for_numeric_attribute = :to_f # optional, default is to_i
    config.default_value_if_numeric_attribute_blank = 0.0 # optional, default is 0
  end

  validates :my_numeric, numericality: { greater_than: 0 }
  validates :my_string, presence: true
end 
```

您可以使用原始查询
从 rails 控制台检查加密值

```
# Rails 5.2 console
post = Post.new => #<Post id: nil, my_string: nil, my_numeric: nil, created_at: nil, updated_at: nil>
post.valid? # => false
post.errors.full_messages # => ["My numeric must be greater than 0", "My string can't be blank"]

post.my_string = "aloha" # => "aloha"
post.my_numeric = 5 # => 5
post.save # => true

raw = ActiveRecord::Base.connection.exec_query 'SELECT my_string, my_numeric FROM posts limit 1'

raw.rows.first[raw.columns.find_index('my_string')]
# => "TUVQcnRBck5oYzMvRlRZUWR3Mzlzdz09LS1tZ09xNGNYbnkzdFc2d1duMEIrdUdBPT0=--ffae1f04753ca5c636915746a4c6fccf81897138"

raw.rows.first[raw.columns.find_index('my_numeric')]
# => "TkdSbURRNzVMbUF6MjF0bjI2ZEtmQT09LS1rRHhqQ2xpWGhYaHBoRlhCRnVZSmh3PT0=--74e45e6c96df78258a1731994a71a74c5047d655"

post.reload
post.my_string # => "aloha"
post.my_numeric # => 5 
```

您可以使用`Enkrip::Engine.encrypt`和`Enkrip::Engine.decrypt`来加密和解密一个值。

```
my_string = 'hello world'

encrypted_my_string = Enkrip::Engine.encrypt my_string
# => "MzZ1M0RDSWdQQ0VaRVJXT3NBYlVTWExWVnVSbXNBeXRMSC9wYWdoeW5Ddz0tLVNVT2l6NDJCd1ZxbW1lYnl2eC9PakE9PQ==--c7436c403595c18fef802a51be29f73d5bb73f19"

Enkrip::Engine.decrypt encrypted_my_string
# => "hello world" 
```

### 执照

Enkrip 是在麻省理工学院的许可下发布的。