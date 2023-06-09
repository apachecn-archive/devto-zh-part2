# Spring Boot 2 中的密码编码器

> 原文：<https://dev.to/vadym_dudnyk/password-encoder-in-spring-boot-2-1cjh>

嗨，由于 Spring boot 2.x 在 Spring Security 中有一些变化，所以，我将向您展示如何在 Spring boot 2 中对密码进行编码(它与新的 Spring Security 5 一起提供)。

最重要的变化:

`DelegatingPasswordEncoder`这是新的默认密码编码器(它不会像`BcryptPasswordEncoder`那样将您束缚在特定的编码器实现上)

`NoOpPasswordEncoder`现在被认为是不推荐使用的。

*   如何创建密码编码器 bean:

```
 @Bean
    public PasswordEncoder passwordEncoder() {
        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    } 
```

*   如何对密码进行编码(下面将使用 Bcrypt 实现):

```
 String encodedPassword = passwordEncoder.encode(rawPassword); 
```

*   编码密码的外观:

`{bcrypt}$2a$10$GJpYuiP0cDOcE.WRlctpHOC1ROz35m9jCJ5BXFoMgnzkUjsxc6yHS`
其中“{bcrypt}”决定使用哪个编码器进行编码。

*   如何检查原始密码是否与编码密码匹配:

```
 if (!passwordEncoder.matches(rawPassword, encodedPassword)) {
        throw new BadCredentialsException("Bad password");
    } 
```