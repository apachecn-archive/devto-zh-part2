# ★使 Nova 字段可翻译

> 原文：<https://dev.to/freekmurze/making-nova-fields-translatable-4dnb>

Laravel Nova 是一个很酷的软件包，可以在 Laravel 应用程序中快速创建管理界面。不幸的是，不支持开箱即用的多种语言环境。不久前，我们发布了[一个名为 nova-translatable](https://github.com/spatie/nova-translatable) 的包，使得任何内置的[字段类型](https://nova.laravel.com/docs/1.0/resources/fields.html#fields)都是可翻译的。

## 使用包

为了使用 nova-translatable，您必须将[spatie/laravel-translatable](https://github.com/spatie/laravel-translatable)安装到您的 Laravel 应用程序中。简而言之，laravel-translatable 会将模型的翻译存储在表的 json 列中。

您可以通过调用`defaultLocales`让`nova-translatable`知道它应该使用哪些语言环境。你可以把这个代码放在 AppServiceProvider 或者你自己的专用服务提供商:

```
\Spatie\NovaTranslatable\Translatable::defaultLocales(['en', 'nl']); 
```

现在让我们来看看简单的 Nova 资源。

```
public function fields(Request $request)
{
    return [
        ID::make()->sortable(),
        Text::make('title'),
        Trix::make('text'),
    ];
} 
```

为了使`title`和`text`字段可翻译，您唯一需要做的就是将它们传递给`Spatie\NovaTranslatable\Translatable`的一个实例。

```
public function fields(Request $request)
{
    return [
        ID::make()->sortable(),

        Translatable::make([
            Text::make('title'),
            Trix::make('text'),
        ]),
    ];
} 
```

准备就绪后，您的 Nova 资源将如下所示:

[![nova-translatable in action](../Images/7c512c2704eb56dcaf7131b269c0039b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h0gOSnqV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/nova-translatable/screenshot.png)

## 幕后

实际上，让`nova-translatable`工作并不需要太多代码。[主`Translatable`级](https://github.com/spatie/nova-translatable/blob/f9ae979eb05fbcd1d313ff3a68ef54709e601766/src/Translatable.php)只有 140 行长。`Translatable`扩展了 Nova 的原生`MergeValue`类，用于分组字段(一个 [Nova 字段面板](https://nova.laravel.com/docs/1.0/resources/fields.html#field-panels)是一个`MergeValue`工具)。

`MergeValue`的一个实例将其字段保存在`data`属性中。每当一个新的`Translatable`出现时，我们将[调用一个名为`createTranslatableFields`的函数](https://github.com/spatie/nova-translatable/blob/f9ae979eb05fbcd1d313ff3a68ef54709e601766/src/Translatable.php#L48)，它将替换那个数据。让我们来看看这个函数。

```
protected function createTranslatableFields()
{
    if ($this->onIndexPage()) {
        $this->data = $this->originalFields;
        return;
    }

    $this->data = [];

    collect($this->locales)
        ->crossJoin($this->originalFields)
        ->eachSpread(function (string $locale, Field $field) {
            $this->data[] = $this->createTranslatedField($field, $locale);
        });
} 
```

如果我们在索引上，也就是资源的列表表示，我们不会替换原来的字段。我们假设你只是想要原始字段。但是，如果我们在资源的任何其他屏幕上，我们将开始替换数据。对于字段和所需地区的每个组合，我们将在`data`中创建一个新条目。如果你想了解更多关于`crossJoin`和`eachSpread`的信息，请阅读[这篇文章](https://freek.dev/avoid-nesting-using-the-crossjoin-and-eachspread-collection-functions)。

下面是`createTranslatedField`的代码。我添加了一些评论，这样你可以更好地理解正在发生的事情。

```
protected function createTranslatedField(Field $originalField, string $locale): Field
{
    /*
     * Let's start by clone the original field so we can make some
     * modifications for the given $locale
     */
    $translatedField = clone $originalField;

    /*
     * To make this work, the attribute most be an attribute that is actually on
     * the underlying model. You don't need to understand why this happens. Hopefully
     * this will not be needed anymore in future version of Nova.
     *
     * More info in this issue on Nova:
     * https://github.com/laravel/nova-issues/issues/827
     */
    $originalAttribute = $translatedField->attribute;
    $translatedField->attribute = 'translations';

    /*
     * If your app uses more than 1 locale we're going to change the label next to it a little
     * By default `displayLocalizedNameUsingCallback` contains a closure that will append
     * the locale between brackets to the label, so `name` will become `name (en)`.
     */
    $translatedField->name = (count($this->locales) > 1)
        ? ($this->displayLocalizedNameUsingCallback)($translatedField, $locale)
        : $translatedField->name;

    /*
     * `resolveUsing` will get called before the field gets displayed. It should return the value
     * of the form field being displayed
     */
    $translatedField
        ->resolveUsing(function ($value, Model $model) use ($translatedField, $locale, $originalAttribute) {
            /*
             * Here we sneakily update the `attribute`. It will get used as the `id` of the form field 
             * that Nova will render. When the resource gets saved this name will be used in the response.
             */
            $translatedField->attribute = 'translations_'.$originalAttribute.'_'.$locale;

            /*
             * Here we will return the translation of the $locale we're processing. This value
             * will get displayed in the form field that Nova will render.
             */
            return $model->translations[$originalAttribute][$locale] ?? '';
        });

    /*
     * `fillUsing` will get called when the user submitted the form. It contains logic on how
     * the response values should get mapped to the underlying model.
     */
    $translatedField->fillUsing(function ($request, $model, $attribute, $requestAttribute) {
        /*
         * Remember that `attribute` we set `resolveUsing`? Here we are going to split it up
         * again so we know which field and which locale we should store the value from the request.
         */
        $requestAttributeParts = explode('_', $requestAttribute);
        $locale = array_last($requestAttributeParts);

        array_shift($requestAttributeParts);
        array_pop($requestAttributeParts);

        $key = implode('_', $requestAttributeParts);

        $model->setTranslation($key, $locale, $request->get($requestAttribute));
    });

    return $translatedField;
} 
```

## 在关闭

您可能想知道为什么我们不在标签、面板中呈现可翻译的字段，或者在它们旁边显示神奇的独角兽。事实是，每个人都希望翻译显示有点不同。这就是为什么我们现在选择保持它非常简单。如果 Nova 获得了在本地更好地构建长表单的能力，我们可能会在新的主要版本中利用它。

如果你喜欢 [nova-translatable](https://github.com/spatie/nova-translatable) 的话，一定要看看其他的 nova 包:

*   [新星-尾巴-工具](https://github.com/spatie/nova-tail-tool)
*   [新星-备份-工具](https://github.com/spatie/nova-backup-tool)
*   [新星标签字段](https://github.com/spatie/nova-tags-field)

即使你没有使用 Laravel Nova，你也可能会在 Spatie 团队之前制作的[这个包](https://spatie.be/open-source/packages) [列表中为你的下一个项目找到一些东西。](https://spatie.be)