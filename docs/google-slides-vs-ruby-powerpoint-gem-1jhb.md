# 谷歌幻灯片 vs Ruby Powerpoint Gem

> 原文：<https://dev.to/jhiatt/google-slides-vs-ruby-powerpoint-gem-1jhb>

## Ruby on Rails 的解决方案。ppt 或者。pptx 导出

对于大量处理向外部客户端报告的 Ruby on Rails 应用程序来说，如果能够导出更加用户友好的报告，通常会增加很多价值。如果客户收到的报告更具视觉吸引力，并且数据的组织方式讲述了一个故事，他们可能会觉得从您的服务中获得了更多。

导出 pdf 的缺点是它不可编辑。如果你的应用程序发布了很多看起来很棒的信息，但你的客户经理想在发送之前做一些修改，该怎么办？导出 powerpoint 是获得可编辑的、视觉上吸引人的、易于打开的报告格式的一种方式。作为一个额外的奖励，你当然可以创建漂亮的演示文稿，以 powerpoint 的方式呈现给客户。

在我们公司，我们决定最终目标是导出一个扩展名为. ppt 的文件(而不是。因为几乎所有的幻灯片演示软件都可以打开。ppt 格式。经过大量研究，我们将 Ruby on Rails 平台的选择缩小到两个，通过 Google Slides API 创建 Google Slides，或者在 Rails 中使用 Powerpoint gem。

### 谷歌幻灯片 API 设置

直到我成为一名开发人员，我才意识到谷歌是多么的庞大和乏味。我见过很多 API 做基本和复杂的事情，但进入谷歌 API 文档感觉像进入外国。谷歌的生态系统非常庞大，他们处理事情的方式与普通的 API 调用或添加 ruby gem 非常不同。

谷歌有一个很棒的快速入门指南，你可以在这里找到。我不会重复这一过程，但我认为如果您是 Rails 环境中编程和工作的新手，可以添加一些内容。

在将项目添加到 Google 并下载配置文件的第一步之后，将文件`credentials.json`放在 rails 应用程序中。我把它放在了根目录下，把`credentials.json`添加到你的`.gitignore`文件中可能是一个安全的想法，这样你的 Github 就不会添加这个文件了。

第三步，Google 给你 quickstart.rb 文件。我将前 30 行代码(通过“authorize”方法的结尾)放入初始化文件`config/initializers/google-slides-service.rb`

```
require 'google/apis/slides_v1'
require 'googleauth'
require 'googleauth/stores/file_token_store'
require 'fileutils'
OOB_URI = 'urn:ietf:wg:oauth:2.0:oob'.freeze
APPLICATION_NAME = 'Google Slides API Ruby Quickstart'.freeze
CREDENTIALS_PATH = 'credentials.json'.freeze
TOKEN_PATH = 'token.yaml'.freeze
SCOPE = Google::Apis::SlidesV1::AUTH_PRESENTATIONS_READONLY
##
# Ensure valid credentials, either by restoring from the saved credentials
# files or intitiating an OAuth2 authorization. If authorization is required,
# the user's default browser will be launched to approve the request.
#
# @return [Google::Auth::UserRefreshCredentials] OAuth2 credentials
def authorize
client_id = Google::Auth::ClientId.from_file(CREDENTIALS_PATH)
token_store = Google::Auth::Stores::FileTokenStore.new(file: TOKEN_PATH)
authorizer = Google::Auth::UserAuthorizer.new(client_id, SCOPE, token_store)
user_id = 'default'
credentials = authorizer.get_credentials(user_id)
if credentials.nil?
url = authorizer.get_authorization_url(base_url: OOB_URI)
puts 'Open the following URL in the browser and enter the ' \
"resulting code after authorization:\n" + url
code = gets
credentials = authorizer.get_and_store_credentials_from_code(
user_id: user_id, code: code, base_url: OOB_URI
)
end
credentials
end 
```

然后在 lib 文件夹`lib/services/google-slides.rb`中创建了一个服务文件，我将其余的代码放在那里。

```
service = Google::Apis::SlidesV1::SlidesService.new
service.client_options.application_name = APPLICATION_NAME
service.authorization = authorize
# Prints the number of slides and elements in a sample presentation:
# https://docs.google.com/presentation/d/1EAYk18WDjIG-zp_0vLm3CsfQh_i8eXc67Jo2O9C6Vuc/edit
presentation_id = '1EAYk18WDjIG-zp_0vLm3CsfQh_i8eXc67Jo2O9C6Vuc'
presentation = service.get_presentation(presentation_id)
puts "The presentation contains #{presentation.slides.count} slides:"
presentation.slides.each_with_index do |slide, i|
puts "- Slide \##{i + 1} contains #{slide.page_elements.count} elements."
end
# [END slides_quickstart] 
```

最终我会想创建一个类或模块来创建和编辑幻灯片，但现在我只想让事情正常运行。

为了测试代码，我将服务文件(google-slides.rb)中的代码行复制并粘贴到我的 rails 控制台中。然后，控制台给了我来自谷歌的指令，让我访问一个特定的网址。访问并登录后，我将它给我的代码复制回我的 rails 控制台，然后按下 enter。之后，连接到谷歌的 API，我能够正常运行命令。

### 谷歌能提供什么？

根据我的快速研究，看起来你可以在谷歌幻灯片的 web 应用程序上做的所有事情都可以通过 API 来完成。创建新的演示文稿、创建幻灯片甚至从 Google Drive 导出文件看起来都很容易。API 甚至可以让你添加过渡、演讲者注释和主题。API 可以变得像你希望的那样复杂，而且看起来可以通过 API 进行最细微的细节修改。

因为您能够从 API 编辑幻灯片，所以使用 Google Slides 还有另一个优势。使用谷歌网络应用程序，你可以在谷歌应用程序上创建任何类型的主题或模板，然后通过你的应用程序进行任何类型的编辑。这在开始时会很有帮助。你甚至可以创建一个 MVP 产品，让你在 Google app 中完成大部分工作，然后通过 Google API 添加你的数据。

另一方面，如果您使用 Google API，您就被绑定到了 Google world。这意味着你将在 Google Drive 上存储你所有的幻灯片和演示文稿。因为 Google 是一个充满活力的成长中的公司，你也比使用版本控制的 gem 更有可能遇到 API 的变化。总的来说，你放弃了一些控制权，但是你在适应性上的收获是巨大的。

### Powerpoint 宝石

毫无疑问，Powerpoint 应用程序的最大优势是易于设置。你可以在这里找到设置[，但是它只是字面上安装宝石，然后你就可以走了。](https://github.com/pythonicrubyist/powerpoint)

然而，你能用宝石做的事情是有限的。添加幻灯片和创建演示文稿真的很容易。你需要做的就是创建一个新的对象

`@deck = Powerpoint::Presentation.new`

并添加幻灯片(您可以选择文本幻灯片、图片幻灯片或两者的混合)

`@deck.add_pictorial_slide title, image_path`

“title”和“image_path”是你提前设置的变量。最后，保存新的演示文稿

`@deck.save(‘test.pptx’)`

它将文件导出为. pptx 格式，保存在您指定的任何文件路径下。您可以相对容易地调整图片位置，但这似乎是内置定制的范围。

设置和使用 Powerpoint gem 再简单不过了，但是，除了添加文本和图片之外，我还没有发现任何可以让你修改幻灯片的东西。gem 是干净地构建的，所以我认为自己构建它会相对简单。

最后，这真的取决于你的最终目标。如果你想把你的数据导出到设计精美的幻灯片中，搞清楚 Google API 可能是值得的。如果你更关心拥有你的内容和构建你控制的代码基础设施，那么 ruby gem 可能是最好的。