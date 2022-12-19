# 使用策略和工厂设计模式为客户端实现定制逻辑

> 原文：<https://dev.to/codebyamir/implement-custom-logic-for-clients-using-the-strategy-and-factory-design-patterns-20ph>

*最初发表在我的博客上[www.codebyamir.com](http://www.codebyamir.com)T3】*

# 概述

为客户端实现定制逻辑是软件开发中的常见场景。

在本文中，我们将探索设计模式如何帮助我们编写干净的代码。代码示例是用 Java 编写的，但是设计模式是与语言无关的。

* * *

# 问题

假设我们已经开发了一个企业销售应用程序，它支持来自单个代码库的多个客户端。

我们最大的客户(“MegaCorp”)要求我们添加特定的验证功能，以便他们能够遵守新的法规。这项规定不适用于其他客户，因此我们无法在全球范围内实施。

* * *

# 设计拙劣的解决方案

## 条件逻辑- If 语句

天真的方法是将 if 语句添加到代码中需要针对受监管公司采取不同行为的地方:

### 请求选择器. java

```
public class RequestValidator {
  private static final String MEGACORP = "MegaCorp, Inc.";

  public ValidationResult validate(final RequestModel request) {
    ValidationResult result = null;
    CompanyModel company = request.getCompany();

    if (MEGACORP.equals(company.getName())) {
      // Regulation-specific validation
    } else {
      // Default validation
    }

    return result;
  }
} 
```

这看起来无害，但会导致严重的问题。我们实现的每一组定制逻辑都会使代码变得更加混乱。假设我们期待一系列适用于其他公司的新法规。我们最终会得到一些很难维护的东西。

或许，我们预计将来其他公司也将受到同样的监管。我们可以向 CompanyModel 类添加一个名为 isRegulated()的布尔方法，并检查:

```
public class RequestValidator {
  public ValidationResult validate(Request request) {
    ValidationResult result = null;
    CompanyModel company = request.getCompany();

    if (company.isRegulated())) {
      // Regulation-specific validation
    } else {
      // Default validation
    }

    return result;
  }
} 
```

虽然这使代码变得更加简洁，但是我们仍然要用复杂的方法来处理许多不同的场景。

* * *

# 精心设计的解决方案

设计模式的知识对于解决这样的问题很有用。我们的最优解使用两种模式:策略和工厂。

## 策略模式

策略模式让我们在运行时选择正确的验证行为。在我们的情况下，我们有两种可能的行为:默认行为和监管行为。

首先，我们将创建一个接口来标识验证的输入和输出:

### ValidationStrategy.java

```
public interface ValidationStrategy {
  ValidationResult validate(RequestModel request);
} 
```

接下来，我们将定义两个以不同方式实现接口的具体类:

### DefaultValidationStrategy.java

```
public class DefaultValidationStrategy implements ValidationStrategy {
  @Override
  public ValidationResult validate(final RequestModel request) {
    // Default validation 
  }
} 
```

### RegulationValidationStrategy.java

```
public class RegulationValidationStrategy implements ValidationStrategy {
  @Override
  public ValidationResult validate(final RequestModel request) {
    // Regulation-specific validation 
  }
} 
```

## 工厂模式

工厂模式用于创建新对象。我们可以用它来决定根据公司返回哪个策略对象。

### ValidationStrategyFactory.java

```
public class ValidationStrategyFactory {
  private final ValidationStrategy defaultValidationStrategy = new DefaultValidationStrategy();
  private final ValidationStrategy regulationValidationStrategy = new RegulationValidationStrategy();

  public ValidationStrategy getStrategy(CompanyModel company) {
    return (company.isRegulated()) ? regulationValidationStrategy : defaultValidationStrategy;
  }
} 
```

*   第 2 行和第 3 行只是创建了验证策略对象。
*   第 6 行基于我们之前看到的布尔方法 isRegulated()返回适当的验证策略。

我们现在可以对 RequestValidator 类进行必要的更改，以包含新的模式:

### 请求选择器. java

```
public class RequestValidator {
  private final ValidationStrategyFactory factory = new ValidationStrategyFactory();

  public ValidationResult validate(final RequestModel request) {
    ValidationStrategy strategy = factory.getStrategy(request.getCompany());
    return strategy.validate(request);
  }
} 
```

*   第 2 行实例化了一个工厂对象。
*   第 5 行通过将公司从工厂传递到 getStrategy()方法来获取适当的策略。注意这里的类型是接口。
*   第 6 行执行适用于所选策略的验证。

* * *

# 常见问题

### 如果另一家公司需要法规验证，需要做哪些更改？

我们只需在数据库中将他们的 isRegulated 标志设置为 true。不需要更改代码。

### 如果另一家公司需要新的法规验证，需要做哪些更改？

如果我们需要支持更多形式的验证，我们可以将布尔方法 isRegulated()替换为返回表示特定规则的枚举的方法。然后我们可以更新我们的工厂方法来引用一个映射，用 enums 作为键，用不同的策略对象作为值。