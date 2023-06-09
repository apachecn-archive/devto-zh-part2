# 具有 onActive 和 on active 侦听器的自定义 LiveData 已测试

> 原文：<https://dev.to/autonomousapps/a-custom-livedata-that-has-onactive-and-oninactive-listeners----tested-4gbl>

我期待着我的下一个帖子继续我在`dagger.android`的[系列](https://dev.to/autonomousapps/the-daggerandroid-missing-documentation-33kj)，但是今天的冒险太奇怪了，不能不与他人联系起来。

需要:一个当它有一个活动的观察者时会“做某事”的对象。

观察:`LiveData`有两个钩子:`onActive()`和`onInactive()`就是为了这个目的而设计的。

计划:用一个名为`ActiveMutableLiveData`的类来扩展`MutableLiveData`，这个类用这些钩子做一些事情。

进一步观察:如果我要将它推向生产，我应该测试它，以向自己证明我理解正在发生的事情，并且我的新类的行为符合预期。

# `ActiveMutableLiveData`

```
/**
 * A form of [MutableLiveData][android.arch.lifecycle.MutableLiveData] that 
 * provides hooks 
for for [onActive][android.arch.lifecycle.MutableLiveData.onActive] and
 * [onInactive][android.arch.lifecycle.MutableLiveData.onInactive]. Use these to be 
 * notified when this [LiveData][android.arch.lifecycle.LiveData] gains and loses an 
 * active observer.
 */
class ActiveMutableLiveData<T>(
    private val onActiveListener: OnActiveListener
) : MutableLiveData<T>() {

    constructor(onActive: () -> Unit = {}, onInactive: () -> Unit = {}) : this(
        onActiveListenerFromLambdas(onActive, onInactive)
    )

    override fun onActive() {
        onActiveListener.onActive()
    }

    override fun onInactive() {
        onActiveListener.onInactive()
    }
}

fun onActiveListenerFromLambdas(
    onActive: () -> Unit, 
    onInactive: () -> Unit
): OnActiveListener {

    return object : OnActiveListener {
        override fun onActive() {
            onActive.invoke()
        }

        override fun onInactive() {
            onInactive.invoke()
        }
    }
}

interface OnActiveListener {
    fun onActive()
    fun onInactive()
} 
```

Enter fullscreen mode Exit fullscreen mode

# `ActiveMutableLiveDataTest`

```
// Using JUnit5
internal class ActiveMutableLiveDataTest {

    private val counter = AtomicInteger()
    private val liveData = ActiveMutableLiveData<Boolean>(onActive = {
        counter.incrementAndGet() 
    })

    @Test fun whenObjectBecomesActive_thenOnActiveListenerIsTriggered() {
        // Given
        val lifecycle = TestLifecycle()
        liveData.observe(lifecycle, Observer {
            println("$it")
        })

        // verify initial condition
        assertEquals(0, counter.get())

        // When
        lifecycle.state = Lifecycle.State.STARTED

        // Then
        assertEquals(1, counter.get())
    }

    @Test fun expectOnActiveListenerTriggeredEveryTime() {
        // Given
        val lifecycle = TestLifecycle()
        liveData.observe(lifecycle, Observer {
            println("$it")
        })

        // verify initial condition
        assertEquals(0, counter.get())

        // When
        lifecycle.state = Lifecycle.State.STARTED
        lifecycle.state = Lifecycle.State.CREATED
        lifecycle.state = Lifecycle.State.STARTED

        // Then
        assertEquals(2, counter.get())
    }

    class TestLifecycle : LifecycleOwner {

        var state: Lifecycle.State = Lifecycle.State.INITIALIZED
            set(value) {
                registry.markState(value)
            }

        private val lifecycle = object : Lifecycle() {
            override fun addObserver(observer: LifecycleObserver) {
                // don't care
            }

            override fun removeObserver(observer: LifecycleObserver) {
                // don't care
            }

            override fun getCurrentState(): State = state
        }

        private val registry = LifecycleRegistry({ lifecycle })

        override fun getLifecycle() = registry
    }
} 
```

Enter fullscreen mode Exit fullscreen mode