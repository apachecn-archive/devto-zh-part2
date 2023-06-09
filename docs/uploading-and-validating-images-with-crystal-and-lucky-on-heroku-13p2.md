# 使用 Heroku 上的 Crystal 和 Lucky 上传和验证图像

> 原文：<https://dev.to/mikeeus/uploading-and-validating-images-with-crystal-and-lucky-on-heroku-13p2>

今天我们将上传 Lucky 和 crystal 的图片！为了演示这一点，我将制作一个应用程序，允许将图像上传到与您的 ip 地址关联的图库。由于我们将在 heroku 上托管这个应用程序，我们可以使用 heroku 的`X-FORWARDED_FOR`头来获取用户的 ip 地址。

请注意，由于代理和潜在的 ip 欺骗，**这不是限制用户访问的安全方法**，我不建议对任何重要数据使用它。

## 完成代码

要查看完成的代码并在本地运行它，您可以克隆 repo 并检查`image-uploads`分支。

```
git clone git@github.com:mikeeus/lucky_demo.git
git checkout image-uploads

cd image-uploads
bin/setup
lucky db.create && lucky db.migrate 
```

Enter fullscreen mode Exit fullscreen mode

您可以运行规范来查看漂亮的绿色结果:)。

```
crystal spec spec/flows/images_spec.cr 
```

Enter fullscreen mode Exit fullscreen mode

## 图像表

首先让我们创建图像表。

```
lucky gen.migration CreateImages 
```

Enter fullscreen mode Exit fullscreen mode

我们将添加以下列来保存文件名、所有者的 ip 地址，我们甚至将记录用户查看图像的次数。

```
class Image::V20180728000000 < LuckyMigrator::Migration::V1
  def migrate
    create :images do
      add filename : String
      add owner_ip : String
      add views : Int32
    end

    execute "CREATE INDEX owner_ip_index ON images (owner_ip);"
    execute "CREATE UNIQUE INDEX filename_index ON images (filename);"  
  end

  def rollback
    drop :images
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们还将在文件名上添加一个唯一索引，在`owner_ip`列上添加一个普通索引，这样我们就可以基于它快速获得图像集合。

## [规格](#specs)

当允许上传到我们的应用程序时，我们希望通过类型和可能的维度来限制文件。我们将创建规格来检查这一点。不幸的是，Crystal 没有给我们关于图像尺寸的信息，所以我们稍后将使用 [crymagic](https://github.com/imdrasil/crymagick) 来获取这些信息。

我们对上传的限制如下:

*   格式:JPG，GIF，PNG
*   最大尺寸:1000x1000
*   最大容量:250kb

我已经在我们的资产文件夹中添加了一些打破这些规则的图片，以及一个完美的图片。

另外:我从这个神奇的网站得到了这些图片:[africandigitalart.com](http://africandigitalart.com/category/posts/concept-art/)，我推荐你去看看。

```
public/
  assets/
    images/
      test/
        perfect_960x981_56kb.jpg
        too_big_900x900_256kb.jpg
        too_tall_1001x1023_95kb.jpg
        wrong_format_240x245_235kb.bmp 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们将创建一个`ImageBox`，以防我们需要在测试中实例化图像。

```
# spec/support/boxes/image_box.cr
class ImageBox < LuckyRecord::Box
  def initialize
    filename "perfect_960x981_56kb.jpg"
    owner_ip "0.0.0.0"
    views 1
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 幸运之流

Lucky 使用了流的概念，流是封装浏览器测试行为的类。我们现在将创建一个在我们的主页上上传图像，并有两种方法来检查它是否成功。

我们可以通过将文件的完整路径添加到表单的文件输入中来模拟上传文件。然后，`click "@upload-image"`方法将在页面上寻找带有`[flow_id=upload-image]`标签的元素并点击它。

```
# spec/support/flows/images_flow.cr
class ImagesFlow < BaseFlow
  def upload_image(filepath)
    visit Home::Index

    fill_form ImageForm,
      image: File.expand_path(filepath)
    click "@upload-image"
  end

  def image_should_be_created(filepath)
    image = find_image_by_filename?(File.basename(filepath))
    image.should_not be_nil
  end

  def image_should_not_be_created(filepath)
    image = find_image_by_filename?(File.basename(filepath))
    image.should be_nil
  end

  private def find_image_by_filename?(filename)
    ImageQuery.new.filename.ilike("%#{filename}%").first?
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以使用这个流程和我们的测试图像来编写我们的规格。Crystal 对规范有一流的支持，我们可以通过编写它们的简单程度来看出来。我们使用`Spec.after_each`通过`delete!`方法清除图像，这也将在每个规范之后删除底层文件。

```
# specs/flows/images_spec.cr
require "../spec_helper"

describe "Images flow" do
  Spec.after_each do
    ImageQuery.new.map(&.delete!)
  end

  describe "uploading" do
    it "works with valid image" do
      flow = ImagesFlow.new

      flow.upload_image(valid_image_path)
      flow.image_should_be_created(valid_image_path)
    end

    it "doesnt work with image above 250kb" do
      flow = ImagesFlow.new

      flow.upload_image(too_big_image_path)
      flow.image_should_not_be_created(too_big_image_path)
    end

    it "doesnt work with dimensions over 1000x1000" do
      flow = ImagesFlow.new

      flow.upload_image(too_tall_image_path)
      flow.image_should_not_be_created(too_tall_image_path)
    end

    it "doesnt work with image of the wrong format" do
      flow = ImagesFlow.new

      flow.upload_image(wrong_format_image_path)
      flow.image_should_not_be_created(wrong_format_image_path)
    end
  end
end

private def valid_image_path
  "public/asseimg/test/perfect_960x981_56kb.jpg"
end

private def too_tall_image_path
  "public/asseimg/test/too_tall_1001x1023_95kb.jpg"
end

private def too_big_image_path
  "public/asseimg/test/too_big_900x900_256kb.jpg"
end

private def wrong_format_image_path
  "public/asseimg/test/wrong_format_240x245_235kb.bmp"
end 
```

Enter fullscreen mode Exit fullscreen mode

运行这些规范会导致它们失败，因为我们还没有实现任何东西。现在让我们构建我们的模型、动作和页面，使它们能够工作。

## 图像模型

我们需要持久引用我们的图像，它们的所有者的 ip 和数据库的浏览量。因此，让我们生成一个模型来做这件事。

```
lucky gen.model Image 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用它的列和一些助手方法来填充`Image`模型，以构建路径、url 和句柄删除。这些图片将在`public/asseimg/...`保存，并在`www.example.com/asseimg/...`公开发布。我们还将为存储在`public/asseimg/test/`目录下的测试图像添加一个案例。

```
# src/models/image.cr
class Image < BaseModel
  table :images do
    column filename : String
    column owner_ip : String
    column views : Int32
  end

  def url
    "#{Lucky::RouteHelper.settings.base_uri}#{path}"
  end

  def path
    if Lucky::Env.test?
      "/asseimg/test/#{self.filename}"
    else
      "/asseimg/#{self.filename}"
    end
  end

  def full_path
    "public#{path}"
  end

  def delete!
    File.delete(full_path)
    delete
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们可以填写我们的`ImageForm`。Lucky 中的表单负责创建和更新模型。我们使用`fillable`来声明我们将更新哪些列，并且我们将声明一个`virtual`字段`image`来保存我们上传的图像，直到我们可以保存它。我们还将添加`needs file`和`needs ip`，因为我们将在实例化表单时传递它们。

`uuid`用于确保我们有唯一的文件名，并使没有文件名的人几乎不可能查看图像。

我们将所有这些放在保存图像并设置列的`prepare`方法中。它目前不做任何验证，但我们将在稍后进行验证。

```
require "uuid"

class ImageForm < Image::BaseForm
  fillable filename
  fillable views
  fillable owner_ip

  virtual image : String

  needs file : Lucky::UploadedFile, on: :create
  needs ip : String, on: :create

  getter new_filename

  def prepare
    save_image
    views.value = 1
    filename.value = new_filename
    owner_ip.value = ip
  end

  private def uploaded
    file.not_nil!
  end

  private def save_image
    File.write(save_path, File.read(uploaded.tempfile.path))
  end

  private def new_filename
    @new_filename ||= "#{UUID.random}_#{uploaded.metadata.filename}"
  end

  private def image_path
    if Lucky::Env.test?
      "asseimg/test/" + new_filename
    else
      "asseimg/" + new_filename
    end
  end

  private def save_path
    "public/" + image_path
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要创建 UI 来允许上传和保存表单的操作。

## 首页

目前，我们的应用程序显示幸运的默认主页。我们将创建一个新的主页来保存我们的表单，并允许我们上传文件。让我们生成页面。

```
lucky gen.page Home::IndexPage 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将添加一个包含`enctype: "multipart/form-data"`和 posts 到`Images::Create`的表单，它将处理创建我们的图像。我们添加`needs form : ImageForm`来告诉呈现这个页面的动作以新的形式传递。我们还将在输入下方的列表中呈现任何错误。

```
class Home::IndexPage < GuestLayout
  needs form : ImageForm

  def content
    render_form(@form)
  end

  private def render_form(f)
    form_for Images::Create, enctype: "multipart/form-data" do
      text_input f.image, type: "file", flow_id: "file-input"      

      ul do
        f.image.errors.each do |err|
          li "Image #{err}", class: "error"
        end
      end

      submit "Upload Image", flow_id: "upload-image"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们更改我们的`Home::Index`操作，显示我们的索引页面，而不是 Lucky 的欢迎页面。

```
# src/actions/home/index.cr
class Home::Index < BrowserAction
  include Auth::SkipRequireSignIn
  unexpose current_user

  get "/" do
    if current_user?
      redirect Me::Show
    else
      render Home::IndexPage
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 在行动中获取当前 ip

我们不会使用`current_user`进行认证，而是需要获取请求的 ip 地址。当我们的应用程序在 heroku 上时，我们可以使用自动设置的`X-FORWARDED-FOR`标题。在本地，我们将它设置为`local`。

我们将在`BrowserAction`中添加这些方法。由于我们的其他操作继承了它的`class Home::Index < BrowserAction`，它将使这些方法对我们可用。

```
# src/actions/browser_action.cr
abstract class BrowserAction < Lucky::Action
  ...
  def current_ip
    current_ip?.not_nil!
  end

  private def current_ip?
    if Lucky::Env.production?
      context.request.headers["X-FORWARDED-FOR"]?
    else
      "local"
    end
  end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

## 图片创建动作

现在我们需要一个动作来处理在主页上提交表单后的图像创建。让我们用:
生成一个

```
lucky gen.action.browser Images::Create 
```

Enter fullscreen mode Exit fullscreen mode

关于如何行动的更多信息，你可以查看[幸运指南](https://luckyframework.org/guides/actions-and-routing)。

该操作将从参数中获取文件，格式为`{ "image": { "image": "file is here" }}`。如果不为零，我们将把文件和`current_ip`一起传递给`ImageForm`，它将验证并保存我们的新图像。

为了检查我们的文件是否存在，我们将确保它不为空，并且文件名存在。

```
# src/actioimg/create.cr
class Images::Create < BrowserAction
  include Auth::SkipRequireSignIn
  unexpose current_user

  route do # lucky expands this to: post "/images"
    file = params.nested_file?(:image)["image"]?

    if is_invalid(file)
      flash.danger = "Please select a file to upload"
      redirect to: Home::Index
    else
      ImageForm.create(file: file.not_nil!, ip: current_ip) do |form, image|
        if image
          flash.success = "Image successfuly uploaded from #{current_ip}!"
          redirect to: Home::Index
        else
          flash.danger = "Image upload failed"
          render Home::IndexPage, form: form
        end
      end
    end
  end

  private def is_invalid(file)
    file.nil? || file.metadata.filename.nil? || file.not_nil!.metadata.filename.not_nil!.empty?    
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。我们的应用程序现在可以处理图片上传。

如果我们用`lucky spec spec/flows/images_spec.cr`运行规范，我们将会看到第一个检查有效图像的规范将会通过，但是由于我们还没有实现图像验证，其余的将会失败。

## 验证

为了检查图像的文件大小、类型和尺寸，我们将使用一个名为 [crymagick](https://github.com/imdrasil/crymagick) 的碎片。它需要安装 ImageMagick，幸运的是 Heroku 上默认安装了 ImageMagick。如果你的本地机器上没有安装，你可以从[的官方网站](https://www.imagemagick.org/script/index.php)获得。

让我们通过将碎片添加到`shard.yml`中的依赖项底部并运行`shards`来安装碎片。

```
# shard.yml
...
dependencies:
  ...
  crymagick:
    github: imdrasil/crymagick 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在我们的`ImageForm`中使用它来验证我们的图像。我们添加了三种方法`validate_is_correct_size`、`validate_is_correct_dimensions`和`validate_is_correct_type`，它们将使用`CryMagick::Image`来检查文件的类型、大小和尺寸。如果没有错误，我们继续保存文件并设置图像的列。

```
require "uuid"
require "crymagick"

class ImageForm < Image::BaseForm
  ...
  getter crymagick_image : CryMagick::Image?

  def prepare
    validate_is_correct_size
    validate_is_correct_dimensions
    validate_is_correct_type

    if errors.empty? # save if validations pass
      save_image

      views.value = 1
      filename.value = new_filename
      owner_ip.value = ip
    end
  end

  private def validate_is_correct_type
    ext = crymagick_image.type

    unless Image::VALID_FORMATS.includes? "#{ext}".downcase
      image.add_error "type should be jpg, jpeg, gif or png but was #{ext}"
    end
  end

  private def validate_is_correct_size
    size = crymagick_image.size # returns size in bytes

    if size > 250_000 # 250kb limit
      image.add_error "size should be less than 250kb but was #{size / 1000}kb"
    end
  end

  private def validate_is_correct_dimensions
  dimensions = crymagick_image.dimensions # returns (width, height)

    if dimensions.first > 1000
      image.add_error "width should be less than 1000px, but was #{dimensions.first}px"
    end

    if dimensions.last > 1000
      image.add_error "height should be less than 1000px, but was #{dimensions.last}px"
    end
  end

  private def crymagick_image # To avoid opening the file multiple times
    @crymagick_image ||= CryMagick::Image.open(uploaded.tempfile.path)
  end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们运行规格，我们会看到它们都通过了！万岁！

现在剩下的就是添加删除和查看图片的支持。

## 显示和删除图像

我们想要的是在主页上显示我们的图像作为一个画廊。每个图像应该有一个按钮来删除，并应显示其网址。

让我们从一个访问主页并检查屏幕上图像的规范开始，另一个点击删除按钮并检查图像是否被删除。

```
# specs/flows/images_spec.cr
require "../spec_helper"

describe "Images flow" do
  ...
  describe "displays" do
    it "own images" do
      flow = ImagesFlow.new

      owned = ImageBox.new.owner_ip("local").create
      not_owned = ImageBox.new.owner_ip("not-owned").create

      flow.homepage_should_display_image(owned.id)
      flow.homepage_should_not_display_image(not_owned.id)
    end
  end

  describe "deleting" do
    it "is allowed for owner" do
      flow = ImagesFlow.new

      flow.upload_image(valid_image_path)

      image = ImageQuery.new.first

      flow.delete_image_from_homepage(image.id)
      flow.image_should_not_exist(image.id)
    end

    it "is not allowed for other ip addresses" do
      flow = ImagesFlow.new
      not_owned = ImageBox.new.owner_ip("not-local").create

      flow.delete_image_from_action(not_owned.id)
      flow.image_should_exist(not_owned.id)
    end
  end
end
... 
```

Enter fullscreen mode Exit fullscreen mode

让我们添加将访问主页、检查图像、检查数据库中的图像以及通过按下按钮或直接访问动作来删除图像的流。

```
class ImagesFlow < BaseFlow
  ...
  def homepage_should_display_image(id)
    visit Home::Index
    image(id).should be_on_page    
  end

  def homepage_should_not_display_image(id)
    visit Home::Index
    image(id).should_not be_on_page    
  end

  def delete_image_from_homepage(id)
    visit Home::Index
    click "@delete-image-#{id}"
  end

  def delete_image_from_action(id)
    visit Images::Delete.with(id: id)
  end

  def image_should_exist(id)
    ImageQuery.find(id).should_not be_nil
  end

  def image_should_not_exist(id)
    ImageQuery.new.id(id).first?.should be_nil    
  end

  ...

  private def image(id)
    el("@image-#{id}")
  end 
```

Enter fullscreen mode Exit fullscreen mode

我们的测试现在将会失败，所以让我们通过更新我们的`Home::IndexPage`来添加对显示图像的支持。我们将要求页面用一个图像道具来呈现，这个图片道具是一个`ImageQuery`。然后，我们将在一个新的 gallery 方法中使用这些图像，该方法呈现每个图像，包括删除图像的链接和显示图像的 url。

```
class Home::IndexPage < GuestLayout
  needs form : ImageForm
  needs images : ImageQuery # ADD THIS!

  def content
    div class: "homepage-container" do
      render_form(@form)

      gallery # add gallery erhere
    end
  end

  private def gallery # define it here
    h2 "Image Gallery"

    ul class: "image-gallery" do
      @images.map do |image|
        li class: "image", flow_id: "image-#{image.id}" do
          div class: "picture", style: "background-image: url(#{image.path});" do
            div "Views: #{image.views}", class: "views"
          end

          link to: Images::Delete.with(image.id), flow_id: "delete-image-#{image.id}" do
            img src: asset("images/x.png")
          end

          div image.url, class: "image-url",  flow_id: "image-url-#{image.id}"
        end
      end
    end
  end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

我还为`src/css/app.scss`添加了一些样式，我不会在本文中介绍。

为了实现这一点，我们需要更新呈现`Home::IndexPage`的动作，以便它们传入图像。

```
# src/actions/home/index.cr
class Home::Index < BrowserAction
  ...

  get "/" do
    if current_user?
      redirect Me::Show
    else
      images = ImageQuery.new.owner_ip(current_ip)
      render Home::IndexPage, form: ImageForm.new, images: images # pass it in here
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

而在我们的`Images::Create`行动中。

```
# src/actioimg/create.cr
class Images::Create < BrowserAction
  ...
  route do
    if is_invalid(file)
      ...
    else
      ImageForm.create(file: file.not_nil!, ip: current_ip) do |form, image|
        if image
          ...
        else
          ...
          images = ImageQuery.new.owner_ip(current_ip)
          render Home::IndexPage, form: form, images: images # pass it in here
        end
      end
    end
  end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

一切就绪！`Home::IndexPage`不会抱怨没有`images`通过。但是它会抱怨到`Images::Delete`的链接还没有实现。所以让我们现在就开始吧。

```
lucky gen.action.browser Images::Delete 
```

Enter fullscreen mode Exit fullscreen mode

`Images::Delete`动作应该检查`current_ip`是否与图像的`owner_ip`匹配，如果匹配，调用`delete!`。

```
# src/actioimg/delete.cr
class Images::Delete < BrowserAction
  include Auth::SkipRequireSignIn
  unexpose current_user

  route do # expands to: deleteimg/:id"
    image = ImageQuery.find(id)

    if image.owner_ip == current_ip
      image.delete!
      flash.success = "Image succesfully deleted!"
      redirect to: Home::Index
    else
      flash.danger = "You are not authorized to delete this image"
      redirect to: Home::Index
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在运行测试...嘣！绿色。

## 显示单张图像

我们要实现的最后一件事是为每张图片创建一个显示页面，更新查看次数。让我们生成动作、页面和表单来为我们更新图像。

```
lucky gen.action.browser Images::Show
lucky gen.page Images::ShowPage

touch src/forms/image_views_form.cr # no generater for forms atm 
```

Enter fullscreen mode Exit fullscreen mode

该表格将是简单的，只用于增加价值。可以这样用:`ImageViewsForm.update!(image)`。

```
# src/forms/image_views_form.cr
class ImageViewsForm < Image::BaseForm
  fillable views
  fillable filename
  fillable owner_ip

  def prepare
    views.value = views.value.not_nil! + 1
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

对于我们的操作，我们将使用一个自定义路线，这样我们的路线参数就可以作为`filename`而不是`id`使用。然后我们检查它是否存在，增加视图并呈现页面，否则我们重定向到`Home::Index`动作。

```
# src/actioimg/show.cr
class Images::Show < BrowserAction
  include Auth::SkipRequireSignIn
  unexpose current_user

  getimg/:filename" do
    image = ImageQuery.new.filename(filename).first?
    if image.nil?
      flash.danger = "Image with filename: #{filename} not found"
      redirect to: Home::Index
    else
      ImageViewsForm.update!(image)
      render Images::ShowPage, image: image
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

显示页面将非常简单。我们使用最小样式显示文件名、视图和图像，以保持一切居中，同时允许图像伸展到其最大尺寸。

```
# src/pagimg/show_page.cr
class Images::ShowPage < GuestLayout
  needs image : Image

  def content
    div style: "text-align: center;" do
      h1 @image.filename
      h2 "Views: #{@image.views}"
      img src: @image.path, style: "max-width: 100%; height: auto;"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将使主页上显示的图像链接到`Images::ShowPage`。

```
class Home::IndexPage < GuestLayout
  ...
    ul class: "image-gallery" do
      @images.map do |image|
        li class: "image", flow_id: "image-#{image.id}" do
          link to: Images::Show.with(image.filename), # Changed this to link: ..
               class: "picture",
               style: "background-image: url(#{image.path});" do
            div "Views: #{image.views}", class: "views"
          end
          ...
        end
      end
    end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

我们完事了。测试应该都是绿色的，应用程序正常工作。

## 加入我们

我希望你喜欢这个教程，并发现它很有用。加入我们的 [Lucky gitter 频道](https://gitter.im/luckyframework/Lobby)了解框架的最新进展，或者[查看文档](https://luckyframework.org/guides)了解如何通过 Lucky 将您的应用理念付诸实践的更多信息。