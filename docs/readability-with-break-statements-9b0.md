# break 语句的可读性

> 原文：<https://dev.to/wrschneider/readability-with-break-statements-9b0>

人们普遍认为`break`和`continue`语句是一种糟糕的编程实践。[查看这个 StackOverflow 线程进行讨论。如果他们的意图不明确，他们会降低代码的可读性。不过，在某些情况下，我认为它们实际上比其他选择更好，可以提高可读性。](https://softwareengineering.stackexchange.com/questions/58237/are-break-and-continue-bad-programming-practices)

这里有一个最近在 Python 中使用 AWS 的例子。

```
ssm = boto3.client("ssm")
response = ssm.send_command(...)
command_id = response["Command"]["CommandId"]
while True:
  invocation = ssm.get_command_invocation(... command_id ...)
  if invocation['Status'] not in ['Pending', 'InProgress']
    break
  time.sleep(5) 
```

这段代码向 AWS SSM 发送一个命令，并在一个循环中获取调用的状态，完成后退出。它也会在重试前暂停。

注意，终止这个循环的唯一方法是`break`语句。

我想到了一些替代方案来避免失败，但实际上我不太喜欢它们。

一种选择是在循环之外引导一个初始请求，这样就可以在`while`条件中直接包含状态:

```
invocation = ssm.get_command_invocation(... command_id ...)
while invocation['Status'] not in ['Pending', 'InProgress']:
  time.sleep(5)
  invocation = ssm.get_command_invocation(... command_id ...) 
```

因为重复，我不太喜欢这个。

解决这个问题的另一种方法是使用标志来指示完成:

```
done = False
while not done:
  invocation = ssm.get_command_invocation(... command_id ...)
  done = invocation['Status'] not in ['Pending', 'InProgress']
  time.sleep(5) 
```

这里没有重复，但是 flag 变量是额外的混乱，并不比`break`版本更易读，因为从`while`语句本身看不出什么条件会导致循环终止。此外，即使循环在第一次迭代中完成，在退出循环之前，您仍然必须`sleep`——除非您添加了一个`break`，在这种情况下，该标志是无用的。

与其他选项相比，带有`while True / break`的原始版本感觉是最不糟糕的，即使这个结论是不直观的。