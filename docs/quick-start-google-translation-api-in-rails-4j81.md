# 快速入门:Rails 中的 Google 翻译 API

> 原文：<https://dev.to/nodefiend/quick-start-google-translation-api-in-rails-4j81>

照片认证:[亚历山大·斯马金](https://unsplash.com/@dotzero)

第一次运行目录

```
//NOTE we need to have google cloud dependency library installed to run google cloud-translate 
```

Enter fullscreen mode Exit fullscreen mode

`gem install google-cloud`
`gem install google-cloud-translate`
T2】

阅读这里了解为你的 rails 应用设置 google cloud api 的信息:[https://cloud.google.com/ruby/docs/setup](https://cloud.google.com/ruby/docs/setup)

设置身份验证:

要运行客户端库，必须首先通过创建服务帐户并设置适当的环境变量来设置身份验证。

[云文档认证](https://cloud.google.com/docs/authentication/production)

### 让我们开始吧！

`application.rb`

```
# Imports the Google Cloud client library
require "google/cloud/translate" 
```

Enter fullscreen mode Exit fullscreen mode

从谷歌:[https://cloud.google.com/docs/authentication/production?获得一个项目 id & json 密钥 authuser = 1 # auth-cloud-implicit-ruby](https://cloud.google.com/docs/authentication/production?authuser=1#auth-cloud-implicit-ruby)

您将在这个页面上获得您的 project_id，并下载您需要的 json 密钥的副本

现在你有了你的密匙，创建这个文件`config/local_env.yml`

`local_env.yml`

```
CLOUD_PROJECT_ID: 'your_project_id'
GOOGLE_APPLICATION_CREDENTIALS: 'config/google_cloud.json' 
```

Enter fullscreen mode Exit fullscreen mode

现在在`/config`中创建一个 google_cloud.json 文件，并将从 google 收到的 json 文件粘贴到其中。

(它看起来会像这样)

`google_cloud.json`

```
{  "type":  "service_account",  "project_id":  "randomApp",  "private_key_id":  "04e0c8e8470",  "private_key":  "-----BEGIN PRIVATE KEY-----\nMIIEvQIBADANBg...p0Tg7WibPz76wqUFpGj/qshvY2pqFc2H94\nxkgZT44GHXagW5WOW5ofXJo=\n-----END PRIVATE KEY-----\n",  "client_email":  "random@random.iam.gserviceaccount.com",  "client_id":  "111111111111111",  "auth_uri":  "https://accounts.google.com/o/oauth2/auth",  "token_uri":  "https://oauth2.googleapis.com/token",  "auth_provider_x509_cert_url":  "https://www.googleapis.com/oauth2/v1/certs",  "client_x509_cert_url":  "https://www.googleapis.com/robot/v1/metadata/x509/random%random.iam.gserviceaccount.com"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要添加新的环境文件以及 JSON 键到`.gitignore`

`.gitignore`

```
/config/local_env.yml
/config/google_cloud.json 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须设置 rails 应用程序来读取这个环境文件:

```
config.before_configuration do
  env_file = File.join(Rails.root, 'config', 'local_env.yml')
  YAML.load(File.open(env_file)).each do |key, value|
    ENV[key.to_s] = value
  end if File.exists?(env_file)
end 
```

Enter fullscreen mode Exit fullscreen mode

## 带谷歌翻译的 Hello world

```
# Your Google Cloud Platform project ID
project_id = ENV["CLOUD_PROJECT_ID"]

# Instantiates a client
translate = Google::Cloud::Translate.new project: project_id

# The text to translate
text = "Hello, world!"
# The target language
target = "ru"

# Translates some text into Russian
translation = translate.translate text, to: target

puts "Text: #{text}"
puts "Translation: #{translation}" 
```

Enter fullscreen mode Exit fullscreen mode

当您尝试运行您的 rails 服务器时，您可能会得到:

```
accessNotConfigured: Access Not Configured. Cloud Storage JSON API has not been used in project 12314123123123 before or it is disabled. Enable it by visiting https://console.developers.google.com/apis/api/storage-api.googleapis.com/overview?project=1231214123123 then retry. If you enabled this API recently, wait a few minutes for the action to propagate to our systems and retry. (Google::Cloud::PermissionDeniedError) 
```

Enter fullscreen mode Exit fullscreen mode

确保在您的开发人员控制台中启用它，就像错误所说的那样

如果你得到:

```
`rescue in execute': invalid: Unknown project id: 0 (Google::Cloud::InvalidArgumentError) 
```

Enter fullscreen mode Exit fullscreen mode

您可能为您的应用程序设置了错误的`project_id`

检查您的项目 id 的`config/google_cloud.json`文件，它将在那里

```
project_id = ENV["CLOUD_PROJECT_ID"]
language_code = 'en'

translate = Google::Cloud::Translate.new project: project_id
languages = translate.languages language.code

 puts 'Supported languages:'
 languages.each do |language|
  puts '#{language.code} #{language.name}'
 end 
```

Enter fullscreen mode Exit fullscreen mode

## 太棒了！现在我们的 rails 服务器应该已经启动并运行了 google cloud translation api！

来源:
谷歌翻译 CLOUTH AUTH:
【https://cloud.google.com/docs/authentication/production? T2】authuser = 1 # auth-cloud-implicit-ruby

在 RAILS 中设置本地键:
[https://qiita.com/alokrawat050/items/0d7791b3915579f95791](https://qiita.com/alokrawat050/items/0d7791b3915579f95791)