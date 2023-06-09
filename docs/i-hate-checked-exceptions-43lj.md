# 我讨厌检查异常

> 原文：<https://dev.to/chochos/i-hate-checked-exceptions-43lj>

我讨厌检查异常。真的。他们真的应该在 Java 8 中去掉它们。他们阻碍了许多新的东西；大多数其他 JVM 语言没有检查异常，这并不是巧合。

`BEGIN RANT`

我在一个类中重写了一个抽象方法:

```
@Override
protected Runnable createTask(Request req) {
  Response resp = new Response(Errors.CANT_CONNECT);
  if (req.getType() == Request.Something) {
    return () -> {
      try {
        oneThing(req, resp);
      } catch (IOException ex) {
        log.error("Doing something with {}", req, ex);
      } finally {
        process(resp);
      }
    };
  } else if (req.getType() == Request.SomethingElse) {
    return () -> {
      try {
        somethingElse(req, resp);
      } catch (IOException ex) {
        log.error("doing something else with {}", req, ex);
      } finally {
        process(resp);
      }
    };
  } else if (req.getType() == Request.YetAnotherThing) {
    return () -> {
      try {
        anotherThing(req, resp);
      } catch (IOException ex) {
        log.error("Doing yet another thing with {}", req, ex);
      } finally {
        process(resp);
      }
    };
  }
  return null;
} 
```

如你所见，这是非常重复的。一个代码块基本上重复 3 次，只有很小的变化。一个明显的机会把它放在别的地方，对不对？像这样的东西看起来更好:

```
private Runnable execute(BiConsumer<Request, Response> m,
        Request req, String msg) {
  Response resp = new Response(Errores.CANT_CONNECT);
  return () -> {
    try {
      m.accept(req, resp);
    } catch (IOException ex) {
      log.error(msg, ex);
    } finally {
      process(resp);
    }
  };
}

@Override
public Runnable createTask(Request req) {
  if (req.getType() == Request.Something) {
    return ejecutar(this::oneThing, req, "Doing something with {}");
  } else if (req.getType() == Request.SomethingElse) {
    return ejecutar(this::somethingElse, req, "doing something else with {}");
  } else if (req.getType() == Request.YetAnotherThing) {
    return ejecutar(this::anotherThing, req, "Doing yet another thing with {}");
  }
  return null;
} 
```

好多了。是啊。

只是我做不到。为什么不呢？嗯，`BiConsumer`不投`IOException`，所以我没接住。另外，我不能给我的方法传递引用，因为它们抛出了`IOException`。

为了做到这一点，我需要在我的方法的每个中捕获*内的`IOException`，只需将它转换成一个`RuntimeException`，然后我可以在`execute`中捕获它。*

我现在真的不想那样做。所以... So ...

`END RANT`