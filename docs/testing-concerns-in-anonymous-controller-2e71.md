# 关注匿名控制器的测试

> 原文：<https://dev.to/amplifr/testing-concerns-in-anonymous-controller-2e71>

一旦你有这样一个控制器问题:

```
module BillingErrorRescuer
  extend ActiveSupport::Concern

  included do
    rescue_from Billing::LimitExceeded, with: :rescue_billing_limits

    def rescue_billing_limits
      render status: 402, json: Billing::PlanPresenter.new(@account).to_json
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个想法是在匿名控制器中单独测试:

```
require 'spec_helper'

describe BillingErrorRescuer, type: :controller do
  controller do
    include BillingErrorRescuer

    def action
      @account = FactoryBot.create :account
      raise Billing::LimitExceeded
    end
  end

  before do
    routes.draw { get :action, to: "anonymous#action" }
    get :action
  end

  let(:account) { create :account, state: :trial }

  it 'renders json with billing data' do
    expect(response.body).to match_json_schema('controllers/billing_error_rescuer')
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个测试是做什么的？

1.  启用 rspec 中的测试控制器功能
2.  定义匿名控制器以包含关注扩展
3.  画出行动的路线
4.  使用默认控制器管道调用操作

就是这样！
感谢`rspec-rails` gem，让我们几乎所有的东西都开箱即用！