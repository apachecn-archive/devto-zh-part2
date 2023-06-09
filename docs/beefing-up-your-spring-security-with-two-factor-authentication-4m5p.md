# 使用双因素身份验证增强您的 Spring 安全性

> 原文：<https://dev.to/cr0wst/beefing-up-your-spring-security-with-two-factor-authentication-4m5p>

双因素身份验证通过要求用户提供第二种形式的身份验证，为您的 web 应用程序增加了一层额外的安全性。常见的第二个因素包括:

*   认证代码
*   生物测定学
*   电子邮件或短信代码

让我们探索一下如何利用 Nexmo 将双因素身份验证添加到现有的 web 应用程序中。

# 开始之前

为了跟随教程，您需要以下内容:

*   对 Java 和企业 Java 技术的一般理解
*   安装在您电脑上的 [Java SDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
*   一个 [Nexmo 开发者账户](https://developer.nexmo.com/)以及一个 API 密钥和秘密
*   GitHub 上 [`getting-started`](https://github.com/cr0wst/demo-twofactor/tree/getting-started) 分支的克隆

# 获取代码

克隆`getting-started`分支。

```
git clone https://github.com/cr0wst/demo-twofactor.git -b getting-started
cd demo-twofactor 
```

# 让我们看看我们在做什么

这个示例应用程序是使用 [Spring Boot](https://projects.spring.io/spring-boot/) 构建的。如果您的系统上安装了 [Gradle](https://gradle.org/) ，您应该能够执行`bootRun`任务来启动应用程序。

如果没有，不用担心；存储库包含一个 Gradle 包装器，它仍然允许您执行任务。

```
./gradlew bootRun 
```

这将下载所有依赖项，编译应用程序，并启动嵌入式服务器。

一旦服务器启动，您应该能够导航到 [http://localhost:8080](http://localhost:8080) 来查看示例应用程序。

有三页:

*   每个人都可以访问的主页。
*   [登录页面](http://localhost:8080/login)——允许用户输入用户名和密码(默认为`demo` / `demo`)。
*   [秘密页面](http://localhost:8080/secret)——只有`Role.USERS`角色的用户才能访问。

# 添加双因素认证

当用户登录时，我们唯一的接受标准是他们提供了用户名和密码。如果这些信息被窃取了呢？我们可以使用的物理位置靠近用户的东西是什么？

我保证，几乎 90%的你和我们的用户，都有触手可及的东西。一部手机。

事情是这样的:

1.  用户将像平常一样登录我们的应用程序。
2.  系统会提示他们输入一个四位数的验证码。
3.  同时，一个四位数的验证码将被发送到他们帐户上的电话号码。如果他们的帐户上没有电话号码，我们将允许他们绕过双重身份验证。
4.  他们输入的代码将被检查，以确保它与我们发送给他们的代码相同。

我们将利用 Nexmo [Verify API](https://developer.nexmo.com/verify/overview) 来生成代码，并检查他们输入的代码是否有效。

## 创建新角色

第一步是创建一个新角色。该角色将用于使通过身份验证的用户处于炼狱状态，直到我们验证了他们的身份。

将`PRE_VERIFICATION_USER`角色添加到`Role`枚举中。

```
// src/main/net/smcrow/demo/twofactor/user/Role.java
public enum Role implements GrantedAuthority {
    USER, PRE_VERIFICATION_USER;
    // ...
} 
```

为了将它作为默认角色应用，我们需要更新`StandardUserDetails`类的`getAuthorities()`方法。

```
// src/main/net/smcrow/demo/twofactor/user/StandardUserDetails.java
@Override
public Collection<? extends GrantedAuthority> getAuthorities() {
    Set<GrantedAuthority> authorities = new HashSet<>();
    authorities.add(Role.PRE_VERIFICATION_USER);
    return authorities;
} 
```

## 处理验证信息

Nexmo 将向我们提供一个*请求 id* ，该 id 将在确认用户提供的代码时使用。我们可以通过多种方式存储这些信息。在本教程中，我们将把它保存到数据库中。

### 存储`Verification`信息

首先，在`verify`包中创建一个`Verification`类。

```
// src/main/net/smcrow/demo/twofactor/verify/Verification.java
@Entity
public class Verification {
    @Id
    @Column(unique = true, nullable = false)
    private String phone;

    @Column(nullable = false)
    private String requestId;

    @Column(nullable = false)
    private Date expirationDate;

    @PersistenceConstructor
    public Verification() {
        // Empty constructor for JPA
    }

    public Verification(String phone, String requestId, Date expirationDate) {
        this.phone = phone;
        this.requestId = requestId;
        this.expirationDate = expirationDate;
    }

    // ... Getters and Setters
} 
```

注意，我们还存储了`expirationDate`。默认情况下，验证 API 请求仅在五分钟内有效。在以下任一情况下，它们将从表中删除:

*   用户已成功验证其身份。
*   它们过期了。

我们将利用 Spring scheduler 来定期清理它们。

### 处理验证信息

在`verify`包中创建`VerificationRepository`接口。

```
// src/mainnet/smcrow/demo/twofactor/verify/VerificationRepository.java
@Repository
public interface VerificationRepository extends JpaRepository<Verification, String> {
    Optional<Verification> findByPhone(String phone);

    void deleteByExpirationDateBefore(Date date);
} 
```

### 删除过期请求

在`twofactor`包中，创建下面的配置类。

```
// src/main/net/smcrow/demo/twofactor/ScheduleConfiguration.java
@Configuration
@EnableScheduling
public class ScheduleConfiguration {
    @Autowired
    private VerificationRepository verificationRepository;

    @Scheduled(fixedDelay = 1000)
    @Transactional
    public void purgeExpiredVerifications() {
        verificationRepository.deleteByExpirationDateBefore(new Date());
    }
} 
```

这将建立一个每秒执行一次的预定命令，查询任何过期的`Verification`实体并删除它们。

## 设置 Nexmo 客户端

我们将使用 [nexmo-java](https://github.com/Nexmo/nexmo-java) 客户端与 nexmo 进行交互。

### 声明依赖关系

首先，在`build.gradle`文件中声明下面的依赖关系。

```
dependencies {
    // .. other dependencies
    compile('com.nexmo:client:3.3.0')
} 
```

### 提供信息

现在，在`application.properties`文件中定义以下信息。

```
# Add your nexmo credentials
nexmo.api.key=your-api-key
nexmo.api.secret=your-api-secret 
```

### 定义豆子

接下来，我们将把`NexmoClient`和`VerifyClient`定义为 beans。这将允许 Spring 将它们作为依赖项注入到我们的`NexmoVerificationService`中。

将以下定义添加到`TwoFactorApplication`类中。

```
// src/main/net/smcrow/demo/twofactor/TwofactorApplication.java
@Bean
public NexmoClient nexmoClient(Environment environment) {
    AuthMethod auth = new TokenAuthMethod(
            environment.getProperty("nexmo.api.key"),
            environment.getProperty("nexmo.api.secret")
    );
    return new NexmoClient(auth);
}

@Bean
public VerifyClient nexmoVerifyClient(NexmoClient nexmoClient) {
    return nexmoClient.getVerifyClient();
} 
```

### 创建`NexmoVerificationService`

我们将创建一个服务，允许我们指示客户端发出请求。

将`NexmoVerificationService`添加到`verify`包中。

```
// src/main/net/smcrow/demo/twofactor/verify/NexmoVerificationService.java
@Service
public class NexmoVerificationService {
    private static final String APPLICATION_BRAND = "2FA Demo";
    private static final int EXPIRATION_INTERVALS = Calendar.MINUTE;
    private static final int EXPIRATION_INCREMENT = 5;
    @Autowired
    private VerificationRepository verificationRepository;

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private VerifyClient verifyClient;

    public Verification requestVerification(String phone) throws VerificationRequestFailedException {
        Optional<Verification> matches = verificationRepository.findByPhone(phone);
        if (matches.isPresent()) {
            return matches.get();
        }

        return generateAndSaveNewVerification(phone);
    }

    public boolean verify(String phone, String code) throws VerificationRequestFailedException {
        try {
            Verification verification = retrieveVerification(phone);
            if (verifyClient.check(verification.getRequestId(), code).getStatus() == 0) {
                verificationRepository.delete(phone);
                return true;
            }

            return false;
        } catch (VerificationNotFoundException e) {
            requestVerification(phone);
            return false;
        } catch (IOException | NexmoClientException e) {
            throw new VerificationRequestFailedException(e);
        }
    }

    private Verification retrieveVerification(String phone) throws VerificationNotFoundException {
        Optional<Verification> matches = verificationRepository.findByPhone(phone);
        if (matches.isPresent()) {
            return matches.get();
        }

        throw new VerificationNotFoundException();
    }

    private Verification generateAndSaveNewVerification(String phone) throws VerificationRequestFailedException {
        try {
            VerifyResult result = verifyClient.verify(phone, APPLICATION_BRAND);
            if (StringUtils.isBlank(result.getErrorText())) {
                String requestId = result.getRequestId();
                Calendar now = Calendar.getInstance();
                now.add(EXPIRATION_INTERVALS, EXPIRATION_INCREMENT);

                Verification verification = new Verification(phone, requestId, now.getTime());
                return verificationRepository.save(verification);
            }
        } catch (IOException | NexmoClientException e) {
            throw new VerificationRequestFailedException(e);
        }

        throw new VerificationRequestFailedException();
    }
} 
```

这个类中有两个主要的方法:

*   用来，嗯，请求验证。
*   `verify`用于验证用户提供的代码。

#### `requestVerification`法

该方法首先检查我们是否已经有对用户电话号码的未决验证请求。这允许我们在用户试图再次登录应用程序时向用户提供相同的请求 id。

如果之前没有任何验证，则会请求一个新的验证码并保存到数据库中。如果由于某种原因，我们不能给它们分配一个新的代码，就会抛出一个`VerificationRequestFailedException`。

将该异常添加到`verify`包中。

```
// src/main/net/smcrow/demo/twofactor/verify/VerificationRequestFailedException.java
public class VerificationRequestFailedException extends Throwable {
    public VerificationRequestFailedException() {
        this("Failed to verify request.");
    }

    public VerificationRequestFailedException(String message) {
        super(message);
    }

    public VerificationRequestFailedException(Throwable cause) {
        super(cause);
    }
} 
```

#### `verify`法

`verify`方法将请求 id 和代码发送给 Nexmo 进行验证。如果验证成功，Nexmo 将返回零状态。验证成功后，从数据库中删除`Verification`实体，并返回`true`。

如果我们找不到`Verification`实体，可能它已经过期，我们请求一个新的并返回 false。如果有任何验证问题，我们抛出一个`VerificationRequestFailedException`。

如果没有找到`Verification`，那么`retrieveVerification`方法将抛出一个`VerificationNotFoundException`。

将该异常添加到`verify`包中。

```
// src/main/net/smcrow/demo/twofactor/verify/VerificationNotFoundException.java

public class VerificationNotFoundException extends Throwable {
    public VerificationNotFoundException() {
        this("Failed to find verification.");
    }

    public VerificationNotFoundException(String message) {
        super(message);
    }
} 
```

## 使用`NexmoVerificationService`

我们将使用该服务来发送代码和验证代码。成功认证后发送代码。

### 触发验证请求

让我们实现一个自定义的`AuthenticationSuccessHandler`，它将在用户成功认证后被调用。

将`TwoFactorAuthenticationSuccessHandler`添加到`verify`包中。

```
// src/main/net/smcrow/demo/twofactor/verify/TwoFactorAuthenticationSuccessHandler.java
@Component
public class TwoFactorAuthenticationSuccessHandler implements AuthenticationSuccessHandler {
    private static final String VERIFICATION_URL = "/verify";
    private static final String INDEX_URL = "/";

    @Autowired
    private NexmoVerificationService verificationService;

    @Autowired
    private UserRepository userRepository;

    @Override
    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException {
        String phone = ((StandardUserDetails) authentication.getPrincipal()).getUser().getPhone();
        if (phone == null || !requestAndRegisterVerification(phone)) {
            bypassVerification(request, response, authentication);
            return;
        }

        new DefaultRedirectStrategy().sendRedirect(request, response, VERIFICATION_URL);
    }

    private boolean requestAndRegisterVerification(String phone) {
        try {
            return verificationService.requestVerification(phone) != null;
        } catch (VerificationRequestFailedException e) {
            return false;
        }
    }

    private void bypassVerification(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException {
        verificationService.updateAuthentication(authentication);
        new DefaultRedirectStrategy().sendRedirect(request, response, INDEX_URL);
    }
} 
```

当用户成功通过身份验证后，我们会检查他们是否有电话号码。

如果他们有电话号码，就会向他们的设备发送一个代码。如果他们没有电话号码，或者我们无法发送代码，我们允许他们绕过验证。

`bypassVerification`方法依赖于`NexmoVerificationService`的`updateAuthentication`方法。

将此添加到`NexmoVerificationService` :

```
// src/main/net/smcrow/demo/twofactor/verify/NexmoVerificationService.java
public void updateAuthentication(Authentication authentication) {
    Role role = retrieveRoleFromDatabase(authentication.getName());
    List<GrantedAuthority> authorities = new ArrayList<>();
    authorities.add(role);

    Authentication newAuthentication = new UsernamePasswordAuthenticationToken(
            authentication.getPrincipal(),
            authentication.getCredentials(),
            authorities
    );

    SecurityContextHolder.getContext().setAuthentication(newAuthentication);
}

private Role retrieveRoleFromDatabase(String username) {
    Optional<User> match = userRepository.findByUsername(username);
    if (match.isPresent()) {
        return match.get().getRole();
    }

    throw new UsernameNotFoundException("Username not found.");
} 
```

该方法用于将数据库中定义的`role`分配给当前用户，并删除`PRE_VERIFICATION_USER`角色。

### 提示用户输入代码

一旦用户收到一个代码，他们就会被转到[验证](http://localhost:8080/verify)页面。接下来让我们来创建这个页面。

在`resources/templates`目录中创建一个名为`verify.html`的新 HTML 文件。

```
<!DOCTYPE html>
<html lang="en"
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorator="default">
<head>
    <meta charset="UTF-8" />
    Two Factor Authorization Demo
</head>
<body>
<div layout:fragment="content" class="container">
    <div class="col-lg-12 alert alert-danger text-center" th:if="${param.error}">There was an error with your login.</div>
    <div class="col-lg-4 offset-lg-4 text-left">
        <form th:action="@{/verify}" method="post">
            <h1>Verify</h1>
            <p>A text message has been sent to your mobile device. Please enter the code below:</p>
            <div class="form-group">
                <label for="code">Verification Code</label>
                <input type="text" class="form-control" id="code" name="code" placeholder="4-Digit Code" />
            </div>
            <button type="submit" class="btn btn-primary">Verify</button>
        </form>
    </div>
</div>
</body>
</html> 
```

我们还需要一个控制器来为用户提供页面。在`verify`包中创建`VerificationController`。

```
// src/main/net/smcrow/demo/twofactor/verify/VerificationController.java
@Controller
public class VerificationController {
    @Autowired
    private NexmoVerificationService verificationService;

    @PreAuthorize("hasRole('PRE_VERIFICATION_USER')")
    @GetMapping("/verify")
    public String index() {
        return "verify";
    }

    @PreAuthorize("hasRole('PRE_VERIFICATION_USER')")
    @PostMapping("/verify")
    public String verify(@RequestParam("code") String code, Authentication authentication) {
        User user = ((StandardUserDetails) authentication.getPrincipal()).getUser();
        try {
            if (verificationService.verify(user.getPhone(), code)) {
                verificationService.updateAuthentication(authentication);
                return "redirect:/";
            }

            return "redirect:verify?error";
        } catch (VerificationRequestFailedException e) {
            // Having issues generating keys let them through.
            verificationService.updateAuthentication(authentication);
            return "redirect:/";
        }
    }
} 
```

这个控制器通过`index`方法服务于验证页面，并通过`verify`方法处理表单提交。

只有具有`PRE_VERIFICATION_USER`角色的用户才能访问该页面。在成功验证后，`updateAuthentication`方法再次被用来将这个角色替换为他们持久化的角色。

### 完成验证链

最后一步是更新`AppSecurityConfiguration`来使用我们的`TwoFactorAuthenticationSuccessHandler`。

修改`AppSecurityConfiguration`来连接我们的处理程序，并通过`successHandler`方法使用它。

```
// src/main/net/smcrow/demo/twofactor/AppSecurityConfiguration.java
@Autowired
private TwoFactorAuthenticationSuccessHandler twoFactorAuthenticationSuccessHandler;

@Override
protected void configure(HttpSecurity httpSecurity) throws Exception {
    // Webjar resources
    httpSecurity.authorizeRequests().antMatchers("/webjars/**").permitAll()
    .and().formLogin().loginPage("/login").permitAll()
            .successHandler(twoFactorAuthenticationSuccessHandler)
    .and().logout().permitAll();
} 
```

## 试试看！

您需要将您的电话号码添加到`data.sql`文件中。

我们不会对电话号码进行任何验证，它需要采用 [E.164](https://en.wikipedia.org/wiki/E.164) 格式。

```
INSERT INTO user (username, password, role, phone) VALUES
    ('phone', 'phone', 'USER', 15555555555); 
```

您现在应该可以正常运行了。启动应用程序，并尝试登录。假设您的 API 密钥、API 密码和植入的电话号码是正确的；您应该会收到一条带有四位数代码的短信。

## 我们做了什么？

我们做了一个 ***lot*** 的事情。

简而言之，我们实现了双因素身份验证来更好地保护我们的应用程序。我们通过以下方式做到了这一点:

*   创建一个自定义的`AuthenticationSuccessHandler`，在为用户提供一个代码后，将用户转到一个验证页面。
*   使用`nexmo-java`库，通过将它包装在`NexmoVerificationService`中，向我们的用户发送验证码。
*   利用 Spring 调度器删除过期的验证码。
*   建立一个页面，让用户输入他们的验证码。

在 GitHub 上查看本教程的[最终代码。](https://github.com/cr0wst/demo-twofactor/tree/final-with-auth-success-handler)

## 展望未来

有多种方法可以实现双因素身份认证。如果您对示例代码中使用的任何框架和技术感兴趣，这里有一个纲要:

*   [Spring Boot](http://projects.spring.io/spring-boot/)
*   [春天的安全](https://projects.spring.io/spring-security/)
*   【T0 度】T1

不要忘记*你*可以成为 [nexmo-java](https://github.com/Nexmo/nexmo-java) 客户端的 Nexmo 贡献者。