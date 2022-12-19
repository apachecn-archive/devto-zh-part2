# Dart 快速提示:查找表

> 原文：<https://dev.to/ronlobo/dart-quick-tip-lookup-tables-43hp>

嘿，你好，

最近在阅读另一种编程语言中对集合的重构时，我很好奇这个概念如何应用于 Dart。

考虑下面这个取自最近出版的 [cqrs](https://github.com/Jaguar-dart/cqrs) 库的例子:

```
part of 'cqrs_example.dart';

///
/// AccountAggregate, a unit for data manipulation and management of
/// consistency(*1) using the account model as aggregate root.
///
class AccountAggregate extends Aggregate<Account> {
  final String name = 'account';

  @override
  Account initializeModel() => Account();

  ///
  /// Applies domain events to the account domain model emitted by the commands
  ///
  @override
  Future<void> apply(Account model, DomainEvent event) async {
    if (event is AccountCreatedEvent) {
      model.id = event.id;
      model.owner = event.owner;
      model.amount = 0.0;
    } else if (event is DepositPerformedEvent) {
      model.amount += event.amount;
    } else if (event is WithdrawalPerformedEvent) {
      model.amount -= event.amount;
    } else {
      throw Exception("Unknown event!");
    }
  }

  ///
  /// Handles commands to the account domain model
  ///
  @override
  Future<void> handleCommand(
      Command cmd, Account model, CommandOutput out) async {
    if (cmd is CreateAccountCmd) {
      if (model.id != null) {
        out.setError("Model with id ${cmd.modelId} already exists!");
        return;
      }
      out.addEvent(AccountCreatedEvent(id: cmd.modelId, owner: cmd.owner));
    } else if (cmd is DepositCmd) {
      out.addEvent(DepositPerformedEvent(id: cmd.modelId, amount: cmd.amount));
    } else if (cmd is WithdrawCmd) {
      if (model.amount < cmd.amount) {
        out.setError("Not enough balance!");
        return;
      }
      out.addEvent(
          WithdrawalPerformedEvent(id: cmd.modelId, amount: cmd.amount));
      return;
    } else {
      throw UnsupportedError(cmd.runtimeType.toString());
    }
  }
} 
```

AccountAggregate 类公开了两个公共方法来处理命令和应用域事件。这两种方法都严重依赖条件检查来执行所需的业务逻辑。想象一下，我们想要在未来添加更多的命令和事件，导致一个更大、更不可读的 if/else 主干。怎么才能美化这个呢？

查表来救援:

```
part of 'cqrs_example.dart';

class AccountAggregate extends Aggregate<Account> {
  final String name = 'account';

  final Map<String, Function> _events = {
    "$AccountCreatedEvent": _applyAccountCreated,
    "$DepositPerformedEvent": _applyDepositPerformed,
    "$WithdrawalPerformedEvent": _applyWithdrawalPerformed,
  };

  final Map<String, Function> _commands = {
    "$CreateAccountCmd": _handleCreateAccount,
    "$DepositCmd": _handleDeposit,
    "$WithdrawCmd": _handleWithdraw,
  };

  @override
  Account initializeModel() => Account();

  @override
  Future<void> apply(Account model, DomainEvent event) async =>
      _events.containsKey(event.runtimeType.toString())
          ? await Function.apply(
              _events[event.runtimeType.toString()], [model, event])
          : throw Exception("Unknown event!");

  @override
  Future<void> handleCommand(
          Command cmd, Account model, CommandOutput out) async =>
      _commands.containsKey(cmd.runtimeType.toString())
          ? await Function.apply(
              _commands[cmd.runtimeType.toString()], [cmd, model, out])
          : throw UnsupportedError(cmd.runtimeType.toString());

  static Future<void> _handleCreateAccount(
      CreateAccountCmd cmd, Account model, CommandOutput out) async {
    if (model.id != null) {
      out.setError("Model with id ${cmd.modelId} already exists!");
      return;
    }
    out.addEvent(AccountCreatedEvent(id: cmd.modelId, owner: cmd.owner));
  }

  static Future<void> _handleDeposit(
      DepositCmd cmd, Account model, CommandOutput out) async {
    out.addEvent(DepositPerformedEvent(id: cmd.modelId, amount: cmd.amount));
  }

  static Future<void> _handleWithdraw(
      WithdrawCmd cmd, Account model, CommandOutput out) async {
    if (model.amount < cmd.amount) {
      out.setError("Not enough balance!");
      return;
    }
    out.addEvent(WithdrawalPerformedEvent(id: cmd.modelId, amount: cmd.amount));
  }

  static Future<void> _applyAccountCreated(
      Account model, AccountCreatedEvent event) async {
    model.id = event.id;
    model.owner = event.owner;
    model.amount = 0.0;
  }

  static Future<void> _applyDepositPerformed(
      Account model, DepositPerformedEvent event) async {
    model.amount += event.amount;
  }

  static Future<void> _applyWithdrawalPerformed(
      Account model, WithdrawalPerformedEvent event) async {
    model.amount -= event.amount;
  }
} 
```

在重构版本中，为每个可能的命令和相应的事件引入了两个映射，对要处理的特定命令和要应用的事件使用单独的方法。这不仅使添加新的命令和事件变得更加容易，我们还能够模拟和测试每一个命令和事件方法。

感谢您的反馈和想法:)

附注:感谢拉维·泰贾·古达帕提的 cqrs 图书馆！

#### 链接

(*1) [NoSQL 蒸馏:多语言持久性新兴世界简要指南](https://www.amazon.com/NoSQL-Distilled-Emerging-Polyglot-Persistence/dp/0321826620)