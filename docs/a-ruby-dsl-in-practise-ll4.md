# 实践中的 Ruby DSL

> 原文：<https://dev.to/tomk32/a-ruby-dsl-in-practise-ll4>

我想分享一些更多的知识和我针对我的具体问题所做的具体实现。

我正在开发的软件是一款[个人理财网络应用](https://budget-fox.com/?utm_source=dev.to)，为了让注册过程和第一步尽可能简单，我使用了编程模板，可以用来在注册过程中填写一些表格。用户将有几个模板可供选择。

我在功能上缩短了代码，如果你想使用它，它是麻省理工学院许可的。

让我们从数据输入文件开始，它是 Ruby 代码，非常容易阅读和理解:

```
ledger "Ledger for #{Date.today.year}" do
  unit '€'

  # Looks verbose, but the first it the name, the second argument the type.
  # The app is in German so the name would differ there
  account 'Equity', 'equity'
  account 'Assets', 'assets'
  account 'Expenses', 'expenses'
  account 'Income', 'income'
  account 'Liabilities', 'liabilities'

  transaction 'Bank account' do
    date Date.today
    entry 'Equity:Current', -200
    entry 'Assets:Bank:Current'
  end
  transaction 'Car credit' do
    entry 'Equity:Car', 5000
    entry 'Liabilities:Car'
  end
  recurring_transaction 'Health Insurance', monthly: 3, day_of_month: 15 do
    entry 'Assets:Bank:Current', -1550
    entry 'Expenses:Insurance'
  end
  recurring_transaction 'Job', weekly: 2, day: :tuesday do
    entry 'Income:Job', -2350
    entry 'Assets:Bank:Current'
  end
  recurring_transaction 'Rent', monthly: 1, day_of_month: -2 do
    entry 'Expenses:Living:Rent', 650.23
    entry 'Assets:Bank:Current'
  end
  recurring_transaction 'Envolopes', weekly: 1, day: :monday do
    entry 'Expenses:Food', 40
    entry 'Expenses:Fun', 20
    entry 'Expenses:Other', 30
    entry 'Assets:Bank:Current'
  end
  recurring_transaction 'Savings', monthly: 1, day_of_month: 1 do
    entry 'Assets:Bank:Savings', 75
    entry 'Assets:Bank:Current'
  end
  recurring_transaction 'Ratenkredit', monthly: 1, day_of_month: 1 do
    entry 'Liabilities:Car', 350
    entry 'Assets:Bank:Current'
  end
end 
```

这就是处理这些输入文件的`TemplateProcessor`，并将它们处理成包含交易、经常性交易和账户的分类账。显然有一些分类帐、交易等数据模型，在我的例子中，我使用的是 MongoId，但 DSL 与此无关。我的扩展版`TemplateProcessor`也有几行不存储数据的测试。

您可以清楚地看到`instance_eval`正在运行分类账和交易的内部程序块。

来自输入文件`date`和`unit`的调用通过`method_missing`和
传递给各自的对象

```
require 'cleanroom'
module TemplateProcessor
  def self.process(template, user, ledger = nil)
    dsl = DSL.new
    dsl.user = user
    dsl.ledger = ledger
    dsl.evaluate(template)
  end

  class DSL
    attr_accessor :user
    attr_accessor :ledger
    attr_accessor :ledgers

    def method_missing(method, *args)
      if @transaction && @transaction.respond_to?(method)
        @transaction.send("#{method}=", *args)
        return
      end
      @ledger.send("#{method}=", *args)
    end

    def ledger(name, &block)
      @ledger ||= Ledger.new(name: name)
      @ledger.ledger_roles.new(creator: user, user: user)
      @ledger.save!
      instance_eval(&block)
      @ledgers ||= []
      @ledgers.push @ledger
      @ledger = nil
    end
    expose :ledger

    def account(name, account_type)
      account = @ledger.accounts.new(name: name, account_type: account_type)
      account.save!
    end

    def transaction(description, &block)
      @transaction = @ledger.transactions.new(description: description)
      @transaction.date = Date.today
      @amount = 0
      instance_eval(&block)
      @transaction.save!
      @transaction = nil
    end
    expose :transaction

    def entry(account_name, amount = nil)
      if amount.nil?
        amount = - @amount
        @amount = 0
      else
        @amount += amount
      end
      @transaction.entries.new(account_name: account_name, amount: amount)
    end
    expose :entry

    def recurring_transaction(description, rule, &block)
      @transaction = @ledger.recurring_transactions.new(description: description)
      @transaction.rule = IceCube::Rule
      rule.each_pair do |method, args|
        @transaction.rule = @transaction.rule.send(method, *args)
      end
      @amount = 0
      instance_eval(&block)
      @transaction.save!
      @transaction = nil
    end
    expose :recurring_transaction
  end
end 
```