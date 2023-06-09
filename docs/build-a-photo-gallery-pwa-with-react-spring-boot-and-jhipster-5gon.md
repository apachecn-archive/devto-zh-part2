# 与 React、Spring Boot 和杰普斯特一起建立一个照片图库 PWA

> 原文：<https://dev.to/oktadev/build-a-photo-gallery-pwa-with-react-spring-boot-and-jhipster-5gon>

从本质上讲，React 只是一个 UI 工具包，ala GWT，但它周围有一个非常健康的生态系统，为您提供构建一个出色的渐进式 web 应用程序(PWA)所需的一切。pwa 很酷，因为如果做得好，它们可以为你的用户提供类似本机的体验，允许他们安装你的应用程序，并在离线时使用它。

但是，“为什么要反应？”你现在可能会问自己，对吧？嗯，你可能听说过 Angular 是想学习 JavaScript 的 Java 开发人员的入门药物。如果您是一名了解 Angular 的经验丰富的 Java 开发人员，那么您很可能是从 AngularJS 开始的。AngularJS 与 Java MVC 框架有类似的概念，比如控制器、服务和指令(我相信这与 JSP 标签 IMHO 类似)。如果您仍然在进行 Angular 开发，那么您可能在这个过程中学到了 TypeScript。您喜欢 TypeScript 是因为它有像 Java 这样的类型，而且它也是一种非常好的语言！

我敢打赌，如果你已经知道 Angular，你可能想了解一下它的主要竞争对手 React。编写 web 应用程序总会有几种方法，React 提供了一种完全不同的方法，您也可以使用 TypeScript！

在本文中，我将向您展示如何构建一个安全的 PWA 来上传和处理图像，在类似 Flickr 的网格中显示图像，并使用 Spring Boot 作为后端。

## 和 Spring Boot 一起上手反应过来

开始使用 React 最简单的方法之一是使用 [Create React App](https://github.com/facebookincubator/create-react-app) (CRA)。您在本地安装它，然后运行`create-react-app $projectName`来生成一个具有最小依赖性的 React 应用程序框架。它使用 webpack 来构建项目，启动 web 服务器，并运行它的测试。

Spring Boot 有一个类似的工具，叫做 [Spring Initializr](https://start.spring.io) 。Spring Initializer 和 CRA 有点不同，因为它是一个你用来创建应用程序的网站(和 API)。

这两个工具都值得研究，你可以通过阅读我的[boot iful Development with Spring Boot 和 React](/blog/2017/12/06/bootiful-development-with-spring-boot-and-react) 教程来学习如何用它们创建一个基本的应用程序。

今天，我将向你展示如何用 React 和 Spring Boot 为照片构建一个 CRUD 应用程序。但是，我要作弊。我将使用 [JHipster](https://www.jhipster.tech) ，而不是从头开始构建一切。JHipster 是一个应用程序生成器，最初只支持角度和 Spring Boot。在其 5.0 版本中，它增加了对 React、webpack 4 和 Spring Boot 2 的支持。

JHipster 附带了许多每个应用程序都需要的特性，包括身份验证/授权、单元和端到端测试支持，以及便于部署到云的工具。

## JHipster 5 入门

要开始使用 JHipster，您需要有一个互联网连接并安装了 [Node.js](https://nodejs.org/) 。该项目建议您使用最新的 LTS(长期支持)版本，在撰写本文时是 8.3.11。你可以使用 npm，但是 JHipster 会使用 [Yarn](https://yarnpkg.org/) 如果你已经安装了的话。要运行这个应用程序，你需要安装 [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 。如果您安装了 Git，JHipster 将在创建项目后自动提交项目，并允许您在不同版本之间升级。

运行以下命令安装 JHipster:

```
npm i -g generator-jhipster@5.0.1 
```

要使用 JHipster 创建照片库应用程序，创建一个目录，并在其中运行`jhipster`。

```
mkdir gallery
cd gallery
jhipster 
```

JHipster 询问了许多关于您想要创建的应用程序类型以及您想要包含哪些技术的问题。下表显示了您想要做出的选择:

| 问题 | 回答 |
| --- | --- |
| 申请类型？ | `Monolithic application` |
| 名字？ | `gallery` |
| Java 包名？ | `com.okta.developer` |
| 使用 JHipster 注册表？ | `No` |
| 认证类型？ | `OAuth 2.0 / OIDC` |
| 数据库的类型？ | `SQL` |
| 生产数据库？ | `PostgreSQL` |
| 开发数据库？ | `H2 with disk-based persistence` |
| 使用 Spring cache？ | `Yes, with Ehcache` |
| 使用休眠二级缓存？ | `Yes` |
| 腹部还是阴道？ | `Maven` |
| 其他技术？ | `<blank>` |
| 客户端框架？ | `React` |
| 启用 SASS 支持？ | `No` |
| 启用 i18n？ | `Yes` |
| 应用程序的母语？ | `English` |
| 其他语言？ | `French` |
| 额外的测试框架？ | `Protractor` |
| 安装其他发电机？ | `No` |

回答完所有这些问题后，JHipster 将在当前目录下创建大量文件，并运行`yarn`(或`npm install`)来安装`package.json`中指定的所有依赖项。

### 用量角器和钥匙锁验证一切正常

当您选择 OAuth 2.0 和 OIDC 进行身份验证时，用户存储在应用程序之外，而不是存储在应用程序中。这意味着你需要一个身份提供商(IdP)来存储你的用户，并允许你的应用程序检索关于他们的信息。默认情况下，JHipster 附带了一个用于 Docker 编写的 [Keycloak](https://keycloak.org) 文件。启动时会导入一组默认的用户和组，并且它有一个为您的 JHipster 应用程序注册的客户端。

下面是应用程序的`src/main/docker`目录中的`keycloak.yml`的样子:

```
version: '2'
services:
  keycloak:
    image: jboss/keycloak:4.0.0.Final
    command: ["-b", "0.0.0.0", "-Dkeycloak.migration.action=import", "-Dkeycloak.migration.provider=dir", "-Dkeycloak.migration.dir=/opt/jboss/keycloak/realm-config", "-Dkeycloak.migration.strategy=OVERWRITE_EXISTING", "-Djboss.socket.binding.port-offset=1000"]
    volumes:
      - ./realm-config:/opt/jboss/keycloak/realm-config
    environment:
      - KEYCLOAK_USER=admin
      - KEYCLOAK_PASSWORD=admin
    ports:
      - 9080:9080
      - 9443:9443
      - 10990:10990 
```

要启动 Keycloak，你需要[安装 Docker Compose](https://docs.docker.com/compose/install/) 。然后在终端窗口中运行以下命令:

```
docker-compose -f src/main/docker/keycloak.yml up 
```

您可以通过在一个终端中使用 Maven 启动应用程序来验证一切是否正常:

```
./mvnw 
```

然后在另一个终端中运行所有量角器测试:

```
yarn e2e 
```

如果您的环境设置正确，您将看到如下输出:

```
yarn run v1.7.0
$ protractor src/test/javascript/protractor.conf.js
(node:97048) [DEP0022] DeprecationWarning: os.tmpDir() is deprecated. Use os.tmpdir() instead.
[15:36:33] W/configParser - pattern ./e2e/entities/**/*.spec.ts did not match any files.
[15:36:33] I/launcher - Running 1 instances of WebDriver
[15:36:33] I/direct - Using ChromeDriver directly...

  Account
    ✓ should fail to login with bad password
    ✓ should login with admin account (2720ms)

  Administration
    ✓ should load metrics
    ✓ should load health
    ✓ should load configuration
    ✓ should load audits
    ✓ should load logs

  7 passing (10s)

[15:36:45] I/launcher - 0 instance(s) of WebDriver still running
[15:36:45] I/launcher - chrome #01 passed
✨ Done in 13.67s. 
```

### 在 Keycloak 中为您的 React + Spring Boot 应用启用用户注册

在 JHipster 中使用 OIDC 认证时，似乎缺少的一个特性是用户注册。如果你使用会话或 JWT 认证，主页上有一个注册链接。对于 OIDC，您需要在您的 IdP 中启用它。对于 Keycloak，你可以通过导航到`http://localhost:9080`并点击**管理控制台**来完成。用`admin/admin`登录，点击**登录**标签。此屏幕允许您启用忘记密码、记住我，以及通过电子邮件进行验证。

[![Keycloak User Registration](img/53b0b256d9426f043dbbcf0c7c865fd4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--glgrqOBM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/react-photo-gallery-pwa/keycloak-registration-c598822c1db8884cf1d35a170fc1a4e1f196fbb26225b3b4df57da126a1b6f8c.png)

启用此设置后，您将在 Keycloak 的登录表单上看到一个**注册**链接。

[![Keycloak Login Form with Register link](img/4d11db7c67ea93e0d076627604a0fb08.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1bHAdIxx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/react-photo-gallery-pwa/keycloak-login-with-registration-13938c9fc2f2ce64cd6407e27c13154d542d4490b250682015220eb81a39662d.png)

您需要在 Keycloak 中为新用户配置一个默认角色。导航到**角色**并点击**默认角色**选项卡。选择`ROLE_USER`并点击**添加选中的**。要配置默认组，请转到**组** > **默认组**。点击`Users`和**添加**。添加默认组是必要的，因为 JHipster 希望用户拥有一个`ROLE_USER`或`ROLE_ADMIN`组(或角色)作为他们的 ID 令牌声明的一部分。

### 保存 JPA 关系的用户数据

我给 JHipster 添加的一个功能是我喜欢称之为*保存用户快照*。当使用 JPA 时，能够与 JHipster 的`User`实体建立关系是很好的。这允许你说“这个用户拥有这个相册”之类的话，并根据这些信息限制访问。

默认情况下，此功能处于打开状态，工作方式如下:

1.  登录后，向`/api/account`发出请求。
2.  `AccountResource.java`中的`getAccount()`方法被映射到这个端点，它交给`UserService#getUserFromAuthentication()`来提取用户的详细信息。
3.  `getUserFromAuthentication()`方法从 Spring Security 中提取用户的详细信息，将 ID 令牌中的组/角色映射到权限，并在数据库中添加/更新用户。

此功能允许您创建与`User`实体的关系。唯一的缺点是，当你有一个用户关系的实体，用户下拉列表将只包含登录到你的应用程序的用户。

## 将您的身份提供商更改为 Okta

JHipster 利用 Spring Security 的 OAuth 2.0 支持来配置应该从哪个 IdP 获取用户信息。在 Spring Boot 中使用 Spring Security 时，您可以在属性文件中配置大多数配置设置。您甚至可以用环境变量覆盖属性。

要从 Keycloak 切换到 Okta(或任何其他 IdP)，您可以覆盖默认属性(对于 Spring Security OAuth)。

> **为什么是 Okta 而不是 Keycloak？**
> 
> Keycloak 在开发中工作得很好，Okta 有免费的多因素身份验证、电子邮件支持和用于生产的出色性能。你可以在[developer.okta.com/pricing](https://developer.okta.com/pricing/)看到其他免费功能和我们的透明定价。

要了解它是如何工作的，创建一个具有以下属性的`~/.okta.env`文件:

```
export SECURITY_OAUTH2_CLIENT_ACCESS_TOKEN_URI="https://{yourOktaDomain}/oauth2/default/v1/token"
export SECURITY_OAUTH2_CLIENT_USER_AUTHORIZATION_URI="https://{yourOktaDomain}/oauth2/default/v1/authorize"
export SECURITY_OAUTH2_RESOURCE_USER_INFO_URI="https://{yourOktaDomain}/oauth2/default/v1/userinfo"
export SECURITY_OAUTH2_CLIENT_CLIENT_ID="{clientId}"
export SECURITY_OAUTH2_CLIENT_CLIENT_SECRET="{clientSecret}" 
```

您需要在 Okta 中创建一个新的 OIDC 客户端，并填充变量。完成后，您可以运行下面的命令来设置这些环境变量。

```
source ~/.okta.env 
```

重启你的应用程序，瞧，你现在正在使用 Okta！

如果你不知道如何在 Okta 上设置 OIDC 应用程序，这里有一个快速总结。

### 在 Okta 上设置一个 OIDC App

[登录](https://login.okta.com/?SAMLRequest=fc%2B7CsJAEAXQXvAflu1NNJUMeZBGELTx1a%2FrYILJTtyZGD%2FfSBRiYzlw77lMnD3rSj3Qc0ku0YtgrhU6S5fSXRN9PKxmS52l00nMpq6iBvJWCrfDe4ss6vStRe9aDzmGIZfo1jsgwyWDMzUyiIV9vt1AH4XGk5ClSvewUgMNa%2BYW%2FVj5jxhm9NLP67QQaSAMu64L6CYmsFSHlnzT4ZlLwTgcL6Sf8%2FeX9AU%3Dhttps://login.okta.com/?SAMLRequest=fc%2B7CsJAEAXQXvAflu1NNJUMeZBGELTx1a%2FrYILJTtyZGD%2FfSBRiYzlw77lMnD3rSj3Qc0ku0YtgrhU6S5fSXRN9PKxmS52l00nMpq6iBvJWCrfDe4ss6vStRe9aDzmGIZfo1jsgwyWDMzUyiIV9vt1AH4XGk5ClSvewUgMNa%2BYW%2FVj5jxhm9NLP67QQaSAMu64L6CYmsFSHlnzT4ZlLwTgcL6Sf8%2FeX9AU%3D)到你的 Okta 开发者账户(或者[注册](https://developer.okta.com/signup/)，如果你没有账户，然后导航到**应用** > **添加应用**。点击**网页**并点击**下一个**。给这个应用取一个你容易记住的名字，并将`http://localhost:8080/login`指定为登录重定向 URI。点击**完成**并记下客户端 ID 和密码。你需要马上将它们复制/粘贴到一个文件中。

创建一个`ROLE_ADMIN`和`ROLE_USER`组(**用户** > **组** > **添加组**)并向其中添加用户。我建议将您注册的帐户添加到`ROLE_ADMIN`，并创建一个新用户(**用户** > **添加人员**)添加到`ROLE_USER`。

导航到 **API** > **授权服务器**，点击名为**默认**的授权服务器进行编辑。点击**索赔**标签和**添加索赔**。将其命名为“roles”，并将其包含在 ID 标记中。将值类型设置为“Groups”，并将过滤器设置为`.*`的正则表达式。点击**创建**完成该过程。

### 什么是 Okta？

简而言之，我们使[身份管理](https://developer.okta.com/product/user-management/)比您可能习惯的更容易、更安全、更可扩展。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

想要一个月一千用户免费？[注册一个免费的开发者账户](https://developer.okta.com/signup/)，当你完成后，请回来，这样你就可以了解更多关于使用 Spring Boot 2.0 和 JHipster 构建 React PWA 的信息！

### 启用 Okta 自助注册

要在 Okta 中启用自助注册，您需要从 Okta 开发人员仪表板导航到 Classic UI。在屏幕的左上角有一个链接可以在两者之间切换。

[![Classic UI Toggle](img/951e7322680be237448bc0554504d8fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--89txnJGb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/react-photo-gallery-pwa/classic-ui-toggle-f4b4c2108b4d7878d0a04aba8e5ec83b888ebb63e33b97f9cc5b83df847a7915.png)

然后导航到**目录** > **自助注册**，点击**启用注册**。将默认组设置为`ROLE_USER`，**默认重定向**到一个自定义 URL，其值为`http://localhost:8080`，点击**保存**。

**注意:**如果你得到一个显示`'http://localhost:8080' is not a valid redirect URI`的错误，那是因为你需要在**安全** > **API** > **可信起源**下添加`http://localhost:8080`作为可信重定向。进行此更改后，导航至**目录** > **自助注册**并编辑设置以再次配置自定义 URL。这次应该能成功。

[![Okta Self-Service Registration Settings](img/5bc91de0dfb4a2bb8eb1c03d058a1c3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--21jTX_Qz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/react-photo-gallery-pwa/registration-settings-055bb514524fd134b99f0ca3aa95496204e9149b531b7df127a8c787e837c8b8.png)

**提示:**在你部署好你的应用程序后，你需要将**默认重定向**改为你的生产 URL。

### Okta 定制选项

除了允许自我注册之外，Okta 还允许您定制其登录屏幕的外观和感觉，以及使用定制的域和电子邮件。你可以在我们的[登录小工具指南](https://developer.okta.com/code/javascript/okta_sign-in_widget)中了解更多信息。

你也可以尝试使用我们的便捷的 [live widget](https://developer.okta.com/live-widget/) 页面实时定制 widget。

## 创建实体以允许您的照片库中出现 CRUD

我已经花了很多时间讨论如何保护您的应用程序，现在让我们实际构建它吧！JHipster 有一个 JDL (JHipster 领域语言)特性，允许你在应用中建模数据，并从中生成实体。你可以使用它的 [JDL 工作室](https://start.jhipster.tech/jdl-studio/)功能在线完成这项工作，并在完成后保存在本地。

我为这个应用程序创建了一个数据模型，它有一个`Album`、`Photo`和`Tag`实体，并在它们之间建立了关系。下面是 JDL 工作室的截图。

[![Photo Gallery JDL in JDL Studio](img/5f2e3185513c379bb865117446773fac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ub3R-ffH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/react-photo-gallery-pwa/photo-gallery-jdl-studio-5027bab7a49e1cb39a6a9bd43634e3dc814748e7d204cbbaa6879f4962d53c8d.png)

为了方便起见，您可以复制下面的 JDL，并将其保存在项目根目录下的一个`gallery.jh`文件中。

```
entity Album {
    title String required,
    description TextBlob,
    created Instant
}

entity Photo {
    title String required,
    description TextBlob,
    image ImageBlob required,
    height Integer,
    width Integer,
    taken Instant,
    uploaded Instant
}

entity Tag {
    name String required minlength(2)
}

relationship ManyToOne {
    Album{user(login)} to User,
    Photo{album(title)} to Album
}

relationship ManyToMany {
    Photo{tag(name)} to Tag{photo}
}

paginate Album with pagination
paginate Photo, Tag with infinite-scroll 
```

你可以生成实体和 CRUD 代码(Java for Spring Boot；React 的类型脚本和 JSX)使用以下命令:

```
jhipster import-jdl gallery.jh 
```

出现提示时，键入`a`以允许覆盖现有文件。

这个过程将创建 [Liquibase](https://www.liquibase.org/) changelog 文件(创建数据库表)、实体、存储库、Spring MVC 控制器以及创建、读取、更新和删除数据对象所需的所有 React 代码。它甚至可以生成 Jest 单元测试和量角器端到端测试！

在这个过程完成后，你可以重启你的应用程序(Ctrl+C 这个`./mvnw`过程并重启它),然后再次运行`yarn e2e`,以快速确认一切都正确生成了。

现在，您可以看到 JHipster 非常强大。它识别出您有一个`ImageBlob`类型的`image`属性，并自动创建您需要上传和存储图像到您的数据库中的管道！*万岁！*

## 在你的 Spring Boot API 中添加图像 EXIF 处理

`Photo`实体有一些属性，可以通过从上传的照片中读取 EXIF(可交换图像文件格式)数据来计算。你可能会问，用 Java 怎么做到的？

谢天谢地，德鲁·诺克斯创建了一个[元数据提取器](https://github.com/drewnoakes/metadata-extractor)库来做这件事。将对 Drew 库的依赖添加到您的`pom.xml`:

```
<dependency>
    <groupId>com.drewnoakes</groupId>
    <artifactId>metadata-extractor</artifactId>
    <version>2.11.0</version>
</dependency> 
```

然后修改`PhotoResource#createPhoto()`方法，在上传图像时设置元数据。

```
import com.drew.imaging.ImageMetadataReader;
import com.drew.imaging.ImageProcessingException;
import com.drew.metadata.Metadata;
import com.drew.metadata.MetadataException;
import com.drew.metadata.exif.ExifSubIFDDirectory;
import com.drew.metadata.jpeg.JpegDirectory;

import javax.xml.bind.DatatypeConverter;
import java.io.BufferedInputStream;
import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;

import java.time.Instant;
import java.util.Date;

public class PhotoResource {
    ...

    public ResponseEntity<Photo> createPhoto(@Valid @RequestBody Photo photo) throws Exception {
        log.debug("REST request to save Photo : {}", photo);
        if (photo.getId() != null) {
            throw new BadRequestAlertException("A new photo cannot already have an ID", ENTITY_NAME, "idexists");
        }

        try {
            photo = setMetadata(photo);
        } catch (ImageProcessingException ipe) {
            log.error(ipe.getMessage());
        }

        Photo result = photoRepository.save(photo);
        return ResponseEntity.created(new URI("/api/photos/" + result.getId()))
            .headers(HeaderUtil.createEntityCreationAlert(ENTITY_NAME, result.getId().toString()))
            .body(result);
    }

    private Photo setMetadata(Photo photo) throws ImageProcessingException, IOException, MetadataException {
        String str = DatatypeConverter.printBase64Binary(photo.getImage());
        byte[] data2 = DatatypeConverter.parseBase64Binary(str);
        InputStream inputStream = new ByteArrayInputStream(data2);
        BufferedInputStream bis = new BufferedInputStream(inputStream);
        Metadata metadata = ImageMetadataReader.readMetadata(bis);
        ExifSubIFDDirectory directory = metadata.getFirstDirectoryOfType(ExifSubIFDDirectory.class);

        if (directory != null) {
            Date date = directory.getDateDigitized();
            if (date != null) {
                photo.setTaken(date.toInstant());
            }
        }

        if (photo.getTaken() == null) {
            log.debug("Photo EXIF date digitized not available, setting taken on date to now...");
            photo.setTaken(Instant.now());
        }

        photo.setUploaded(Instant.now());

        JpegDirectory jpgDirectory = metadata.getFirstDirectoryOfType(JpegDirectory.class);
        if (jpgDirectory != null) {
            photo.setHeight(jpgDirectory.getImageHeight());
            photo.setWidth(jpgDirectory.getImageWidth());
        }

        return photo;
    }
    ...
} 
```

因为您正在提取信息，所以您可以从 UI 和测试中移除字段，这样用户就不能设置这些值。

在`src/main/webapp/app/entities/photo/photo-update.tsx`中，添加`metadata`和`metadataRows`变量，以及使它们在添加照片时隐藏和在更新照片时只读的逻辑。找到下面代码块的第一行，并用下面的代码替换该行。

```
const { description, image, imageContentType } = photoEntity;

const metadata = (
  <div>
    <AvGroup>
      <Label id="heightLabel" for="height">
        <Translate contentKey="galleryApp.photo.height">Height</Translate>
      </Label>
      <AvField id="photo-height" type="number" className="form-control" name="height" readOnly />
    </AvGroup>
    <AvGroup>
      <Label id="widthLabel" for="width">
        <Translate contentKey="galleryApp.photo.width">Width</Translate>
      </Label>
      <AvField id="photo-width" type="number" className="form-control" name="width" readOnly />
    </AvGroup>
    <AvGroup>
      <Label id="takenLabel" for="taken">
        <Translate contentKey="galleryApp.photo.taken">Taken</Translate>
      </Label>
      <AvInput
        id="photo-taken"
        type="datetime-local"
        className="form-control"
        name="taken"
        readOnly
        value={isNew ? null : convertDateTimeFromServer(this.props.photoEntity.taken)}
      />
    </AvGroup>
    <AvGroup>
      <Label id="uploadedLabel" for="uploaded">
        <Translate contentKey="galleryApp.photo.uploaded">Uploaded</Translate>
      </Label>
      <AvInput
        id="photo-uploaded"
        type="datetime-local"
        className="form-control"
        name="uploaded"
        readOnly
        value={isNew ? null : convertDateTimeFromServer(this.props.photoEntity.uploaded)}
      />
    </AvGroup>
  </div> );
const metadataRows = isNew ? '' : metadata; 
```

然后，在`return`块中，删除`image`属性和`album`属性之间的 JSX，替换为`{metadataRows}`。

```
 <input id="file_image" type="file" onChange={this.onBlobChange(true, 'image')} accept="image/*" />
  </AvGroup>
</AvGroup>
{metadataRows}
<AvGroup>
  <Label for="album.title">
    <Translate contentKey="galleryApp.photo.album">Album</Translate>
  </Label> 
```

在`src/test/javascript/e2e/entities/photo/photo.spec.ts`中，删除设置这些字段数据的代码:

```
photoUpdatePage.setHeightInput('5');
expect(await photoUpdatePage.getHeightInput()).to.eq('5');
photoUpdatePage.setWidthInput('5');
expect(await photoUpdatePage.getWidthInput()).to.eq('5');
photoUpdatePage.setTakenInput('01/01/2001' + protractor.Key.TAB + '02:30AM');
expect(await photoUpdatePage.getTakenInput()).to.contain('2001-01-01T02:30');
photoUpdatePage.setUploadedInput('01/01/2001' + protractor.Key.TAB + '02:30AM');
expect(await photoUpdatePage.getUploadedInput()).to.contain('2001-01-01T02:30'); 
```

您还可以删除`src/test/javascript/e2e/entities/photo/photo-update.page-object.ts`中这些字段的所有 getters 和 setters:

```
setHeightInput(height) {
  this.heightInput.sendKeys(height);
}

getHeightInput() {
  return this.heightInput.getAttribute('value');
}

setWidthInput(width) {
  this.widthInput.sendKeys(width);
}

getWidthInput() {
  return this.widthInput.getAttribute('value');
}

setTakenInput(taken) {
  this.takenInput.sendKeys(taken);
}

getTakenInput() {
  return this.takenInput.getAttribute('value');
}

setUploadedInput(uploaded) {
  this.uploadedInput.sendKeys(uploaded);
}

getUploadedInput() {
  return this.uploadedInput.getAttribute('value');
} 
```

停止您的 Maven 进程，运行`yarn webpack:build`，再次启动 Maven，然后运行`yarn e2e`以确保一切仍然工作。如果你上传了一张用智能手机拍摄的图片，那么高度、宽度和拍摄值都应该被填充。如果不是，很可能你的图像中没有数据。

> 需要一些 EXIF 数据的样本照片吗？你可以从 Flickr 上的相册下载我 1966 年的大众汽车照片。

## 将 React 照片库添加到 React PWA 中

您已经在后端添加了元数据提取，但您的照片仍然显示在列表中，而不是网格中(像 Flickr)。要解决这个问题，您可以使用 [React 照片库](https://github.com/neptunian/react-photo-gallery)组件。用纱线安装:

```
yarn add react-photo-gallery@6.0.28 
```

或 npm:

```
npm i --save-exact react-photo-gallery@6.0.28 
```

**注意:**我第一次尝试使用[雷桑·卡兹伯罗娃的](https://www.linkedin.com/in/lkazberova/) [react-photo-feed](https://github.com/lkazberova/react-photo-feed) ，但是在把它添加到我的项目中之后，发现它导致了编译错误。

在`src/main/webapp/app/entities/photo/photo.tsx`中，为`Gallery`添加一个导入:

```
import Gallery from 'react-photo-gallery'; 
```

然后在`render()`方法中添加一个`photoSet`变量，并在`</h2>`结束后添加`<Gallery>`组件。

```
render() {
  const { photoList, match } = this.props;
  const photoSet = photoList.map(photo => ({
    src: `data:${photo.imageContentType};base64,${photo.image}`,
    width: photo.height > photo.width ? 3 : photo.height === photo.width ? 1 : 4,
    height: photo.height > photo.width ? 4 : photo.height === photo.width ? 1 : 3
  }));

  return (
    <div>
      <h2 id="photo-heading">
        ...
      </h2>
      <Gallery photos={photoSet} />
      ...
  );
} 
```

因为您只修改了前端代码，所以您可以运行`yarn start`来启动 webpack-dev-server 的一个实例，该实例将请求代理到后端，并在每次您更改任何 React 文件时自动刷新您的浏览器(使用 Browsersync)。

登录并在顶部导航栏中导航至**实体** > **照片**。你应该能够上传照片，并在列表顶部的一个漂亮的网格中看到结果。

[![Gallery with Photos](img/095899b7fd7951573c8fa8ae9d746fec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QqhtdSlF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/react-photo-gallery-pwa/photo-gallery-7ef745b8ec67972172408e77e70ac09a15a57b708764a1376bcf405e41fb2aeb.png)

你还可以在网格中添加一个“灯箱”功能，这样你就可以点击照片并放大。React 照片库文档展示了如何做到这一点。我已经将它集成到本文的示例中，但是为了简洁起见，我不会在这里展示代码。你可以在 GitHub 上看到添加了 Lightbox 的 [final `photo.tsx`或者一个必要修改的](https://github.com/oktadeveloper/okta-react-photo-gallery-example/blob/master/src/main/webapp/app/entities/photo/photo.tsx)[diff](https://github.com/oktadeveloper/okta-react-photo-gallery-example/commit/47f9ceab2b00f1d7f41d286686c9159f79decc11)。

## 把你的 React + Spring Boot App 做成 PWA

成为 PWA 需要具备三个特征:

1.  您的应用程序必须在 HTTPS 提供
2.  您的应用程序必须注册一个服务工作者，以便它可以缓存请求并离线工作
3.  您的应用程序必须有一个包含安装信息和图标的 webapp 清单

对于 HTTPS，您可以[为本地主机](https://letsencrypt.org/docs/certificates-for-localhost/)设置一个证书，或者(甚至更好)，将它部署到生产环境中！像 Heroku 和 Cloud Foundry 这样的云提供商将为你提供现成的 HTTPS，但他们不会强迫 T4 使用 HTTPS。要强制 HTTPS，请打开`src/main/java/com/okta/developer/config/SecurityConfiguration.java`并添加一条规则，以便在发送`X-Forwarded-Proto`报头时强制使用安全通道。

```
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        ...
    .and()
        .headers()
        .frameOptions()
        .disable()
    .and()
        .requiresChannel()
        .requestMatchers(r -> r.getHeader("X-Forwarded-Proto") != null)
        .requiresSecure()
    .and()
        .authorizeRequests()
        ...
} 
```

已经配置了 [workbox-webpack-plugin](https://developers.google.com/web/tools/workbox/modules/workbox-webpack-plugin) 用于生成服务工作者，但是它仅在使用生产配置文件运行您的应用程序时才起作用。这很好，因为这意味着在开发时，数据不会缓存在浏览器中。

要注册一个服务工作者，打开`src/main/webapp/index.html`并取消对下面代码块的注释。

```
<script>
    if ('serviceWorker' in navigator) {
         navigator.serviceWorker
            .register('./service-worker.js')
            .then(function() { console.log('Service Worker Registered'); });
    }
</script> 
```

最后一个特性——web app 清单——包含在`src/main/webapp/manifest.webapp`中。它定义了应用程序的名称、颜色和图标。你可能需要调整这些来适应你的应用。

## 将您的 React + Spring Boot 应用部署到 Heroku

要将你的应用部署到 Heroku，你首先需要安装 [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) 。您可以通过运行`heroku --version`来确认其已安装。

> 如果你没有 Heroku 账户，请到[heroku.com](https://www.heroku.com/)注册。别担心，这是免费的，而且你很可能会喜欢这种体验。

运行`heroku login`登录您的帐户，然后使用 JHipster 启动部署过程:

```
jhipster heroku 
```

这将启动 [Heroku 子生成器](https://www.jhipster.tech/heroku/)，它会问你几个关于你的应用程序的问题:你想给它起什么名字，你是想把它部署到美国地区还是欧盟。然后它会提示你选择在本地构建还是在 Heroku 的服务器上用 Git 构建。选择 Git，这样就不用上传 fat JAR 了，部署过程就开始了。

如果你有一个稳定和快速的互联网连接，你的应用程序应该在六分钟左右上线！

```
remote: -----> Compressing...
remote: Done: 134.5M
remote: -----> Launching...
remote: Released v5
remote: https://gallery-pwa.herokuapp.com/ deployed to Heroku
remote:
remote: Verifying deploy... done.
To https://git.heroku.com/gallery-pwa.git
 * [new branch] HEAD -> master

Your app should now be live. To view it run
    heroku open
And you can view the logs with this command
    heroku logs --tail
After application modification, redeploy it with
    jhipster heroku
Congratulations, JHipster execution is complete!
Execution time: 5 min. 31 s. sec 
```

### 为 Okta 配置你的 React + Spring Boot App，用 Lighthouse 分析你的 PWA 分数

要配置您的应用程序在 Heroku 上使用 Okta，运行以下命令将您的本地 Okta 相关环境变量传输到 Heroku。

```
heroku config:set \
  SECURITY_OAUTH2_CLIENT_ACCESS_TOKEN_URI="$SECURITY_OAUTH2_CLIENT_ACCESS_TOKEN_URI" \
  SECURITY_OAUTH2_CLIENT_USER_AUTHORIZATION_URI="$SECURITY_OAUTH2_CLIENT_USER_AUTHORIZATION_URI" \
  SECURITY_OAUTH2_RESOURCE_USER_INFO_URI="$SECURITY_OAUTH2_RESOURCE_USER_INFO_URI" \
  SECURITY_OAUTH2_CLIENT_CLIENT_ID="$SECURITY_OAUTH2_CLIENT_CLIENT_ID" \
  SECURITY_OAUTH2_CLIENT_CLIENT_SECRET="$SECURITY_OAUTH2_CLIENT_CLIENT_SECRET" 
```

Heroku 重启你的应用后，登录，然后用 Lighthouse 测试。看起来不错，是吧？！💯

[![Lighthouse Score 💯](img/dbfdb7fd1fcf287eecfc476311f7b7eb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VhqeLtx1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/react-photo-gallery-pwa/lighthouse-score-4f95c4462e97330d1566ec3e8844a1a712c20aed9bf47cdfe10b0736b7f8289e.png)

## 了解更多关于 React、Spring Boot、JHipster 和 OAuth 的信息

这篇教程向你展示了如何用 Spring Boot，React，JHipster 和 OIDC 开发一个图片库 PWA。它向您展示了一些有用的开源库来简化实现，甚至生成测试。

如果您想了解 React、Spring Boot 或 OAuth 2.0 的更多信息，我建议您查看以下资源:

*   [与 Spring Boot 和 React 的良好发展](/blog/2017/12/06/bootiful-development-with-spring-boot-and-react)
*   [通过 JHipster 使用 OpenID 连接支持](https://developer.okta.com/blog/2017/10/20/oidc-with-jhipster)
*   [什么是 OAuth 2.0 授权码授予类型？](/blog/2018/04/10/oauth-authorization-code-grant-type)
*   [使用 JHipster 文档中的 React(带 Redux)](https://www.jhipster.tech/using-react/)
*   [使用 OAuth 2.0 和 JHipster 开发微服务架构](https://developer.okta.com/blog/2018/03/01/develop-microservices-jhipster-oauth)

如果您有任何问题，请在下面留下评论，在 [Twitter @mraible](https://twitter.com/mraible) 上 ping 我，或者在我们的[开发者论坛](https://devforum.okta.com/)上发帖。如果你喜欢这篇文章，请关注 [@oktadev](https://twitter.com/oktadev) ，当其他人喜欢这篇文章时，你会收到通知。