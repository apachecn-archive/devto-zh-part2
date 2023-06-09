# 如何通过 Uni CPO 为 WooCommerce 产品添加自定义选项

> 原文：<https://dev.to/mrpsiho/how-to-add-custom-options-to-woocommerce-products-via-uni-cpo-12bl>

Uni CPO WooCommerce 选项和价格计算公式插件可以为基于 WooCommerce 的产品添加自定义(额外)选项。拥有定制选项对于某些产品来说至关重要，尤其是那些依赖于客户定义的值/定制选项的产品。Uni CPO 插件可以帮助您为您的产品创建一个报价计算器，并可以立即订购报价配置的产品。

视频版本可在此获得:[https://www.youtube.com/watch?v=dX7-T4gVJ_I](https://www.youtube.com/watch?v=dX7-T4gVJ_I)

这里有免费版的插件:[https://WordPress . org/plugins/uni-woo-custom-product-options/](https://wordpress.org/plugins/uni-woo-custom-product-options/)所以，快去抢吧，和 WooCommerce 一起安装。现在创建一个产品，并将其价格设置为 smth。其实什么都行，哪怕是‘1’。这是必要的，因此 WC 不会将产品视为免费。这就是开始产品配置所需的全部内容。

Uni CPO 有一个非常强大的可视化表单生成器。但是，它非常易于使用和直观。所以，激活构建器模式。通过从构建器面板中拖放具有该名称的模块来添加一行。对列进行同样的操作。现在是定制选项的时候了。添加文本输入选项(同样，通过从构建器面板拖放它)。将鼠标悬停在齿轮图标上并单击，将会打开选项的设置模式窗口。定义选项的 slug 和其他设置，如使其成为必需的、步长值、默认值等。保存选项。

创建整个列的副本-此操作会导致复制列和选项。这是添加类似选项的更快方法。重复选项的配置，但选择不同的段塞名称，然后保存。

现在你有两个自定义选项-宽度和高度。单击构建器面板上的“保存”按钮保存构建器内容。现在打开“公式和条件逻辑”模态窗口，定义你的价格计算公式。这就像写一个任意的数学公式一样。省省吧。

单击构建器面板上的“产品常规设置”图标，打开带有产品特定常规选项的模式窗口。在这里先启用两个设置。它们是“显示自定义选项”和“启用产品价格计算”。保存设置。

仅此而已！您的产品有两个定制的额外选项-宽度和高度-以及基于这些选项值的价格计算。简单吧。:)

此外，请检查插件的专业版:[https://builderius.io/cpo/](https://builderius.io/cpo/)它有更多的功能，大量的功能！