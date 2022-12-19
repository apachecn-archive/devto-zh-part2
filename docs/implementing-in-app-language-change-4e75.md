# 实现应用内语言更改

> 原文：<https://dev.to/brightdevs/implementing-in-app-language-change-4e75>

Android 根据系统区域设置解析特定于语言和文化的资源。这是一个非常有意义的预期行为。尽管官方不鼓励这样做，但你可能还是需要编写一个应用程序，让用户不用离开就能改变语言。在本文中，我将向您展示实现这种特性的一种可能的方法。

# 大局

在开始编写代码之前，让我解释一下整个机制是如何工作的。

我将在生命周期回调期间翻译当前应用程序屏幕上可见的所有字符串(例如`Activity.onResume()`和`Fragment.onResume()`)。这种方法很容易实现和理解，目前它涵盖了我的所有需求。我不会试图强迫应用程序神奇地自我翻译。这意味着需要重新初始化所有需要翻译的文本和视图。

下图说明了通过活动获取本地化字符串的流程:

[![Getting a string](../Images/306b5c58dfe1247f04702db7a5e4fb6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XJgjJKtj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9g1hmdzgrofl9q2tikya.png)

该流程涉及到`StringLocalization`对象，它有一个包含所有可用的`Resources`的映射，并使用`AppSettings`来获取用户设置的当前语言。

# 存储用户偏好

为了在应用程序重启之间保持所选的语言，我们将把偏好存储在`SharedPreferences`中。但是首先，让我们声明一个 enum 类来表示我们的语言和一个带有地区代码的对象，以避免在不同的地方硬编码值。

```
object LocaleCodes {
    const val ENGLISH = "en"
    const val POLISH = "pl"
    const val GERMAN = "de"
}

enum class Language(val locale: Locale) {
    English(Locale(LocaleCodes.ENGLISH)),
    German(Locale(LocaleCodes.GERMAN)),
    Polish(Locale(LocaleCodes.POLISH));

    companion object {
        val DEFAULT = English

        fun fromLocale(locale: Locale): Language =
                values().firstOrNull { it.locale.language == locale.language } ?: DEFAULT
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，enum 类有一个静态的`DEFAULT`值和`fromLocale`函数，我们将在后面使用。

现在，让我们实现一个存储用户偏好的类:

```
interface AppSettings {
    var currentLanguage: Language
}

class SharedPrefAppSettings(
    private val sharedPref: SharedPreferences,
    private val androidConfiguration: Configuration
) : AppSettings {

    private var currentLanguageCache: Language? = null

    override var currentLanguage: Language
        get() {
            val cachedValue = currentLanguageCache

            return if (cachedValue == null) {
                val storedValue = sharedPref.getString(APP_LANGUAGE_KEY, "")
                val storedLanguage = try {
                    Language.valueOf(storedValue)
                } catch (ex: Exception) {
                    null
                }

                val language = storedLanguage ?: getDefaultLanguage()

                currentLanguage = language

                language
            } else cachedValue
        }
        set(value) {
            currentLanguageCache = value
            sharedPref.edit().putString(APP_LANGUAGE_KEY, value.toString()).apply()
        }

    private fun getDefaultLanguage(): Language {
        val locale = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
            androidConfiguration.locales[0]
        } else {
            androidConfiguration.locale
        }

        return Language.fromLocale(locale)
    }

    companion object {
        private const val APP_LANGUAGE_KEY = "app_language"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我不喜欢说代码是不言自明的，所以让我告诉你发生了什么。
存储选择的语言非常简单:我们将它的值缓存在`currentLanguageCache`中，并存储在`APP_LANGUAGE_KEY`的`SharedPreferences`中。
获取语言有点复杂，尤其是当应用程序第一次启动时:

*   如果缓存的值存在，我们就使用它
*   如果我们还没有，我们从`SharedPreferences`开始读
*   然后，我们尝试从该值中获取枚举
*   如果我们不能正确地获取它(因为在`SharedPrefereces`中没有值，或者我们读取了不能由我们的枚举表示的东西)，我们使用`getDefaultLanguage()`获得一个默认语言

    *   我们从`Configuration`开始阅读应用程序的`Locale`(它来自`context.resources.configuration`
    *   然后我们尝试从那个`Locale`中获取枚举值
    *   在最坏的情况下，我们以`Language.DEFAULT`值结束
*   我们通过调用 setter: `currentLanguage = language`来缓存结果语言

由于这个简单的模块:
，我们将使用 Dagger 来注入`AppSettings`接口的`SharedPrefAppSettings`实现

```
@Module
class AppSettingsModule {
    @Provides
    @Singleton
    fun provideAppSettings(context: Context, configuration: Configuration): AppSettings =
            SharedPrefAppSettings(PreferenceManager.getDefaultSharedPreferences(context), configuration)
} 
```

Enter fullscreen mode Exit fullscreen mode

# 初始化特定于语言环境的资源

为了访问为特定区域设置定义的资源，我们需要执行以下操作:

*   制作当前资源的新副本`Configuration`
*   用`setLocale()`设置所需的语言环境
*   使用`context.createConfigurationContext()`创建配置上下文
*   从新上下文中获取资源

所以代码可以是这样的:

```
val conf = Configuration(context.resources.configuration)
conf.setLocale(Locale(LocaleCodes.ENGLISH))
val localizedContext = context.createConfigurationContext(conf)
val localizedResources = localizedContext.resources 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以为每种语言生成一个资源图。像往常一样，有很多方法可以做到这一点。在这个例子中，我们使用匕首[地图多绑定](https://google.github.io/dagger/multibindings#map-multibindings)。

首先，让我们定义一个`LanguageKey`注释，这样我们就可以得到一个带有`Language`枚举键:
的地图

```
@MustBeDocumented
@Target(AnnotationTarget.FUNCTION)
@Retention(AnnotationRetention.RUNTIME)
@MapKey
annotation class LanguageKey(val value: Language) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们用它来生成多个`Resources` :

```
@Module
class LocalizationModule {

    @Provides
    @IntoMap
    @LanguageKey(Language.English)
    fun providesEnglishResources(context: Context): Resources =
        getLocalizedResources(context, Language.English.locale)

    @Provides
    @IntoMap
    @LanguageKey(Language.Polish)
    fun providesPolishResources(context: Context): Resources =
        getLocalizedResources(context, Language.Polish.locale)

    @Provides
    @IntoMap
    @LanguageKey(Language.German)
    fun providesGermanResources(context: Context): Resources =
        getLocalizedResources(context, Language.German.locale)

    private fun getLocalizedResources(context: Context, locale: Locale): Resources {
        val conf = Configuration(context.resources.configuration)
        conf.setLocale(locale)
        val localizedContext = context.createConfigurationContext(conf)
        return localizedContext.resources
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

瞧，我们现在可以用匕首注射了。

# 提供字符串资源

最后，是时候添加一个助手类了，它将从生成的映射中提供字符串资源。

```
@Singleton
class StringsLocalization @Inject constructor(
        private val settings: AppSettings,
        private val resMap: Map<Language, @JvmSuppressWildcards Resources>
) {

    private val log = logger

    fun getString(@StringRes stringId: Int): String = resMap
            .getOrElse(settings.currentLanguage, this::getFallbackResources)
            .getString(stringId)

    private fun getFallbackResources(): Resources {
        val defaultLanguage =
                if (Language.DEFAULT in resMap) Language.DEFAULT
                else resMap.keys.firstOrNull()

        if (defaultLanguage != null) {
            log.error("Current language resources not found. Fallback to: {}", defaultLanguage)
            settings.currentLanguage = defaultLanguage

            return resMap[defaultLanguage]!!
        } else {
            throw ResourcesNotFoundException("String resources not found")
        }
    }
}

class ResourcesNotFoundException(message: String) : RuntimeException(message) 
```

Enter fullscreen mode Exit fullscreen mode

`AppSettings`(即`SharedPrefAppSettings`)和资源地图都会被匕首注入。
您可以看到接受字符串资源 ID 的`getString()`方法，就像常规的`Activity.getString()`方法一样。它将从`AppSettings`获取语言，并使用它从地图中检索合适的`Resources`。如果由于某种原因，map 在那个语言键上没有包含任何内容，我们将使用`getFallbackResources()`方法作为提供字符串失败前的最后手段。

# 使其工作

我们现在要做的就是把`StringsLocalization` object 注入到活动和片段中，用它来翻译我们需要的任何东西。
但是首先，让我们编写一个助手接口来使这个任务不那么样板化:

```
interface HasStringsLocalization {
    val stringsLocalization: StringsLocalization
}

fun HasStringsLocalization.getLocalizedString(@StringRes stringId: Int): String =
        stringsLocalization.getString(stringId) 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们可以像这样轻松地访问带有`getLocalizedString`的字符串:

```
class WelcomeActivity : AppCompatActivity(), HasStringsLocalization {

    @Inject
    override lateinit var stringsLocalization: StringsLocalization

    override fun onResume() {
        super.onResume()
        setTranslatedTexts()
    }

    private fun setTranslatedTexts() {
        welcomeText.text = getLocalizedString(R.string.WELCOME)
        // and more...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

附注:为了让这个例子更简短，可读性更强，我跳过了一些代码。完整的实现可能涉及某种匕首注入、布局膨胀等。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

光明发明
[博客](https://azabost.com/)，[电子邮件](//andrzej.zabost@brightinventions.pl)
u