# Vaadin 流量和反作用弹簧

> 原文：<https://dev.to/committedsw/vaadin-flow-and-reactive-spring-2iik>

[![philip swinburn 60220 unsplash](img/7574a037e49bd9df820f81037887a181.png)T2】](///static/philip-swinburn-60220-unsplash-9a57445ecc294e14d46690f9f649dd95-7a1db.jpg)

# 瓦丁 11 和 Spring Boot 2

几周前，Vaadin 11 发布了[，它建立在最近对](https://vaadin.com/blog/vaadin-11-is-now-available-with-gradle-support-and-new-components) [Vaadin 10](https://vaadin.com/blog/vaadin-10-is-out-) 的重大改变之上。 [Vaadin](https://vaadin.com/) 允许 Java 开发者用 Java 组件用 Java 构建 web 用户界面。

由于我们是 Java，尤其是 Spring 的忠实用户，Vaadin 对我们来说是一项有趣的技术。在这篇博客文章中，我们将看看 Spring Boot 2 的一些新功能与 Vaadin 的使用。

这不是对瓦丁的介绍或教程，他们已经有了一些很棒的资源和 T2 文档。我们将讨论使用 Vaadin 和 Spring 时的一些技巧(和陷阱)。

这里的一切都适用于 Vaadin 10，这是 LTS 版本，但由于 Vaadin 现在遵循 3 个月的发布周期，大多数开发者都希望保持他们的应用程序最新。

## 春天和春天

Vaadin 10+与 [Spring Boot 2](https://spring.io/projects/spring-boot#learn) 配合得很好，并且有一些可以在组件中使用的特定注释。这在[使用 Spring](https://vaadin.com/docs/v10/flow/spring/tutorial-spring-basic.html) 流文档中有整整一章的内容。

我们强烈建议将 Vaadin 与 Spring 一起使用。如果您习惯于 Vaadin，Spring 集成非常简单，但是 Spring 将为您提供大量额外的工程支持:

*   数据库访问，支持
*   对本地和外部缓存的访问
*   身份验证，包括社交登录
*   消息支持
*   外部配置机制
*   可执行 JAR 和服务包装

简而言之，结合 Spring 和 Vaadin，在您自己编写任何代码之前，您就有了一个完整的堆栈。

如果你是从 Vaadin 开始的，他们有一个针对 Spring 的[项目启动器](https://vaadin.com/start)和基于 Spring 的[项目基础](https://vaadin.com/start/latest/project-base-spring)。

如果你有付费的 Vaadin 订阅，他们也有完整的 Vaadin Spring starter。

## 使用反应弹簧

Spring Framework 5 和 Spring Boot 2 提供了使用[反应类型](https://spring.io/blog/2016/04/19/understanding-reactive-types) `Flux`和`Mono`的选项。

反应式类型是一个复杂的话题，但是可以把它们看作是 Java 的`Stream`(现在是反应式的`Flux`)和`Optional`(现在是反应式的`Mono`)的改进的、标准化的和更加灵活的版本。在幕后，它们是为高吞吐量和无阻塞操作而设计的。反应类型是在应用程序中处理数据的一种有效且可组合的方式。

反应式类型提供了发布、处理和消费线程事件的灵活性。像许多 UI 框架一样，Vaadin 对哪个线程可以更新 UI 组件进行严格控制。

这里我们有一个项目的计数器，我们可以通过点击刷新按钮来更新:

```
Text text = new Text("...");
Button button = new Button("Refresh", event -> {
  int count = 20;
  text.setValue(count + " items")
}) 
```

当按钮被点击时，事件回调以允许 UI 更新和文本值更新的方式运行*。*

到目前为止，还没有反应。

假设我们有一个反应式 Spring 数据仓库:

```
// The repository
public interface PersonRepository extends ReactiveCrudRepository<Person, String> {

} 
```

并按以下方式使用

```
PersonRepository repo = ... // Typically injected by Spring

Text text = new Text("...");
Button button = new Button("Refresh", event -> {

  // Ask for the count
  Mono<Long> mono = repo.count();

  // WARNING: DOESN'T WORK!
  mono.subscribe(count -> text.setValue(count + " items"));
}) 
```

这里`subscribe`将调用 lambda ( `count -> text.setValue`)当计数操作完成时被调用。这将异步发生——上面的代码基本上只是连接一个“完成计数”回调。它很可能发生在非 UI 线程上，因此没有 Vaadin 要求的正确的 UI 锁定。单击该按钮将导致 Vaadin 出现异常。

您可以`mono.block()`强制等待计数，但这失去了反应型的好处。你不会真的想让你的 UI 等待长时间运行的查询。

要在 UI 线程上运行代码，可以使用`UI.access()`方法。您可以使用返回`Optional`的 Vaadin `Component`上的`getUI()`方法来访问 UI。因此，上述内容的一个实现是:

```
PersonRepository repo = ... // Auto wired / injected by Spring

Text text = new Text("...");
Button button = new Button("Refresh", event -> {
  Mono<Long> mono = repo.count();

  // In effect this says:
  // - when you get the count
  // - if you can get the UI
  // - schedule a ui update
  // - in that ui update, set the text value.
  mono.subscribe(count -> {
    getUI().ifPresent(ui -> {
      ui.access(() -> text.setValue(count + " items"));
    });
  }
}) 
```

这是相当多的代码，阅读起来相当复杂。

退一步说，我们真正想要的是:

*   当单声道回来时
*   将其转换为一组命令(`text.setValue`)
*   将命令传递给 UI 来执行

这可以更灵活地完成:

```
repository.count()
    .map(count -> () -> text.setValue(count + "items"))
    .subscribe(doInUi(getUi()));

// where
public static Consumer<Command> doInUi(Optional<Ui> ui) {
  return command -> ui.ifPresent(u -> u.access(command));
} 
```

你可以用 Spring 让它变得更好，通过创建一个`UiSupport`组件，你可以把它注入你的组件:

```
@Component
@UIScope
public class UiSupport {

  private final UI ui;

  public UiSupport(@Inject UI ui) {
    this.ui = ui;
  }

  public Consumer<Command> doOnUi() {
    return command -> updateUi(command);
  }

  public void updateUi(Command command) {
    ui.access(command);
  }
} 
```

请注意，该组件的范围是每个单独的 Vaadin UI ( `@UiScope`)，因为每个 Vaadin UI 需要一个`UiSupport`(即每个选项卡中的每个用户一个)。

所以我们的回调变成:

```
@Autowired
    public MyComponent(UiSupport uiSupport) {

      Button button = new Button("Refresh", event -> {
          repository.count()
            .map(count -> () -> text.setValue(count + "items"))
            .subscribe(uiSupport.doOnUi());
      });
      //...
    } 
```

或者也许更干净利落地从 lambdas:

```
@Autowired
    public MyComponent(UiSupport uiSupport) {
      this.uiSupport = uiSupport;

      Button button = new Button("Refresh", event -> refreshData());
      //...
    }

    private void refreshData() {
        repository.count()
          .map(this::setTextValue)
          .subscribe(uiSupport.doOnUi());
    }

    private Command setTextValue(long count) {
      return () -> text.setValue(count + "items")
    } 
```

## 作为数据提供者的反应式知识库

Vaadin 使用 DataProviders 为表等提供数据项。如果您使用的是 Spring，您通常会希望从 Spring 数据存储库中生成。

您可以使用`fromCallbacks`方法:
巧妙地将一个反应性存储库包装到一个`DataProvider`

```
public class ReactiveDataProvider {

  public static <T> DataProvider<T,?> fromReactiveRepository(ReactiveCrudRepository<T, ?> repository) {
    return DataProvider.fromCallbacks(
        query -> repository.findAll()
            // Basic sorting will work if you implement Comparable<T> on your data item (T)
            // but it's better to so this in the database using Sort
            // which is more performant and flexible
            // .sort(query.getInMemorySorting())
            .skip(query.getOffset())
            .take(query.getLimit())
            .toStream(),
        query -> repository.count().map(Math::toIntExact).block()
    );
  }
} 
```

上面没有使用 DataProvider 查询，所以如果您想访问一个过滤的结果集，就要多花一点功夫。对于一种简单而灵活的过滤方法，请看一下`QueryByExampleExecutor<T>`，它提供了一种基于数据项类传递过滤器的简洁方法。

`ReactiveMongoRespository`已经通过例子查询了，所以我们可以使用:

```
public class ReactiveDataProvider {

  /// ...

  public static <T> DataProvider<T,Example<T>> fromReactiveMongoRepository(ReactiveMongoRepository<T, ?> repository) {
    return DataProvider.fromFilteringCallbacks(
        query -> 
            findByExample(repository, query.getFilter())
            .skip(query.getOffset())
            .take(query.getLimit())
            .toStream(),
        query -> countByExample(repository, query.getFilter())
            .map(Math::toIntExact).block()
    );
  }

  private static <T> Flux<T> findByExample(ReactiveMongoRepository<T,?> repository, Optional<Example<T>> filter) {
    if(filter.isPresent()) {
      return repository.findAll(filter.get());
    } else {
      return repository.findAll();
    }
  }

  private static <T> Mono<Long> countByExample(ReactiveMongoRepository<T,?> repository, Optional<Example<T>> filter) {
    if(filter.isPresent()) {
      return repository.count(filter.get());
    } else {
      return repository.count();
    }
  }
} 
```

您可以在您的组件中使用:

```
dataProvider = ReactiveDataProvider.fromReactiveMongoRepository(repository);
filteredDataProvider = dataProvider.withConfigurableFilter();

// You'd likely set this in response to user input but...
filteredDataProvider.setFilter(Example.of(new Person("hello"))); 
```

使用`ExampleMatcher` s，你可以用`Example`做更多的事情

## 广播器和 Spring 应用程序

由于 Vaadin 代码存在于服务器上，因此它可以访问应用程序的完整状态。例如，所有登录的用户。

因此，广播消息或共享状态等功能很容易在 Vaadin 中实现。Vaadin 文档使用[广播模式](https://vaadin.com/docs/v10/flow/advanced/tutorial-push-broadcaster.html)来实现这一点。

在 Spring 中，我们已经有了使用`@EventListener`在应用程序中传递事件的良好功能。然而，似乎你不能在 Vaadin 组件中使用这个，见 [Github 问题 272](https://github.com/vaadin/spring/issues/272) 。

为了将 Spring 事件连接到 Vaadin，我们使用相同的广播模式——通过注册的监听器和 Spring 组件组合广播:

```
// This is a unified broadcaster outputting to Spring and (registered) Vaadin components
public abstract class AbstractBroadcaster<T> {
  private Executor executor = Executors.newSingleThreadExecutor();

  private LinkedList<Consumer<T>> listeners = new LinkedList<>();

  @Autowired
  private ApplicationEventPublisher publisher;

  public synchronized BroadcastRegistration register(Consumer<T> listener) {
    listeners.add(listener);

    return () -> {
      synchronized (Broadcaster.class) {
        listeners.remove(listener);
      }
    };
  }

  public synchronized void broadcast(T message) {
    // Send message to Spring components
    publisher.publishEvent(message);

    // Send message to our listeners in Vaadin
    for (Consumer<T> listener : listeners) {
      executor.execute(() -> listener.accept(message));
    }
  }
} 
```

然后，您可以使用
为您的特定活动创建自己的广播公司

```
@Service
public class PersonChangedBroadcaster extends AbstractBroadcaster<PersonsChangedEvent> {

} 
```

您现在可以注册一个 Vaadin 路由来响应事件。请注意，我们向广播公司`onAttach`注册，并取消注册`onDetach`。

```
@Route
@Push
public class MainView extends VerticalLayout {

  private final VaadinFlux vf;
  private final PersonRepository repository;
  private final PersonChangedBroadcaster notifier;
  private final DataProvider<Person,Example<Person>> dataProvider;
  private BroadcastRegistration notifierBroadcastRegistration;

  public MainView(VaadinFlux vf,
      @Autowired PersonRepository repository, PersonChangedBroadcaster notifier) {
    this.template = template;
    this.vf = vf;
    this.repository = repository;
    this.notifier = notifier;

    dataProvider = ReactiveDataProvider.fromReactiveMongoRepository(repository);

    // Create components here...
  }

  private void refreshData() {
    // When we are prompted refresh the data
    vf.updateUi(() -> dataProvider.refreshAll());
  }

  @Override
  protected void onAttach(AttachEvent attachEvent) {
    super.onAttach(attachEvent);

    // Register for events as soon as we attach
    this.notifierBroadcastRegistration = notifier.register(p -> this.refreshData());
  }

  @Override
  protected void onDetach(DetachEvent detachEvent) {
    super.onDetach(detachEvent);

    // When we 'detach' we no longer want events any more
    if (notifierBroadcastRegistration != null) {
      notifierBroadcastRegistration.deregister();
      notifierBroadcastRegistration = null;
    }
  }
} 
```

每当发送一条`PersonChangedEvent`消息，我们的主视图就会自动更新(多亏了`@Push`)。

Spring 内部的一个典型用例是在任何用户添加或删除数据项时自动刷新所有用户的 UI。在 Spring Mongo 中，我们可以利用`AbstractMongoEventListener`来监听保存和删除功能。然后，我们可以将新事件推送到我们的广播公司:

```
@Service
public class PersonChangedListener extends AbstractMongoEventListener<Person> {

  private final PersonChangedBroadcaster notifier;

  public PersonChangedListener(ApplicationEventPublisher publisher,
      PersonChangedBroadcaster notifier) {
    this.notifier = notifier;
  }

  @Override
  public void onAfterSave(AfterSaveEvent<Person> event) {
    super.onAfterSave(event);
    notifyListeners(new PersonsChangedEvent(event.getSource()));
  }

  @Override
  public void onAfterDelete(AfterDeleteEvent<Person> event) {
    super.onAfterDelete(event);
    notifyListeners(new PersonsChangedEvent(event.getSource()));
  }

  private void notifyListeners(PersonsChangedEvent event) {
    notifier.broadcast(event);
  }
} 
```

## 使用 Vaadin

如果你像我们一样，用 React 这样的框架开发你的 Web UI，那么回到 Java 的想法可能看起来有点奇怪。

我们认为 Vaadin 真正出色的地方是支持我们的后端服务。这些没有消费者用户界面，但它们受益于管理用户界面。一个例子可能是数据微服务，我们提供了一个 API 来以编程方式创建、列出或删除数据，但我们也希望提供一个管理 UI 来允许系统管理员轻松输入。我们还可以通过 Vaadin UI 公开其他功能，比如数据导出和批量导入，这些功能不是 API 的一部分。

对于这些类型的应用，Vaadin 允许我们快速开发一个 UI，它:

*   为服务提供了一个简单统一的构建过程，因为所有代码都是通过 Maven 编译的
*   被部署为服务本身的一部分，集成到单个 JAR 中
*   利用公共 Java 代码库(没有代码重复或数据传输对象分离的机会)

在这个用例之外，Vaadin 在服务器状态复杂且需要共享的地方大放异彩。上面显示了我们如何用几行代码向所有用户更新由数据库触发的重要状态变化。感谢 Spring 和 Reactor，我们有了非阻塞的、高性能的代码。

菲利普·斯温伯恩在 [Unsplash](https://unsplash.com/search/photos/reindeer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。