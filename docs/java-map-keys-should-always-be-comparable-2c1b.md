# Java 映射键应该总是可比较的

> 原文：<https://dev.to/carey/java-map-keys-should-always-be-comparable-2c1b>

这篇文章的灵感来自于多年前的发现，即攻击者可以操纵查询参数，通过使所有参数名称都出现在同一个哈希桶中，将许多 web 服务器中的地图数据结构变成一个链表，这是 CVE-2018-0875 提醒我的。你可以在证书 [VU#903934](https://www.kb.cert.org/vuls/id/903934) 中阅读原始发现，在[完全披露列表档案](https://seclists.org/fulldisclosure/2011/Dec/477)中阅读原始帖子，或者从 28c3 在 [YouTube 上阅读。](https://www.youtube.com/watch?v=R2Cq3CLI6H8)

以 Java 为例，字符串`"fg"`、`"gH"`和`"h)"`的哈希码都是 3265，所以攻击者可以构建类似`fgfg=0&fggH=0&fgh)=0&gHfg=0`等查询字符串来浪费服务器的时间，挂载有效的拒绝服务。防御起来并不难。我知道 Jetty 仍然将表单内容限制为 200KB 和 1000 个键，其他服务器也有类似的限制。

在 Java 7 发布的早期，Java 本身在`String`类中实现了一个防御，在 Java 7 中的[集合框架增强中有描述。`String`类获得了一个新的](https://docs.oracle.com/javase/7/docs/technotes/guides/collections/changes7.html) [`hash32`](http://hg.openjdk.java.net/jdk7u/jdk7u6/jdk/file/8c2c5d63a17e/src/share/classes/java/lang/String.java#l3078) 方法和字段，它使用了带有随机种子的 MurmurHash3。这对于字符串和`HashMap`很好，但是对于自定义键就不那么好了。

一个更好的方法在 [JEP 180](http://openjdk.java.net/jeps/180) 为 Java 8 实现。现在，如果有太多的 hash 码映射到 map 中的同一个 bucket，那么条目列表可以改成平衡二叉树，先按 hash 码排序，再按每个键的`compareTo`方法排序，只要键是`Comparable`。字符串显然是这种情况(在`HashMap`中是特例)，但用作映射键的每个类*也应该是这种情况，尤其是当数据可能由恶意用户提供时。*

## 实现可比

为一个数据类正确实现`equals()`和`hashCode()`很简单，虽然有点冗长:

```
public final class SearchKey {
    private final String site;
    private final long userId;
    private final String query;

    public SearchKey(String site, long userId, String query) {
        this.site = Objects.requireNonNull(site);
        this.userId = userId;
        this.query = Objects.requireNonNull(query);
    }

    // getters ...

    @Override
    public int hashCode() {
        return (site.hashCode() * 31 + Long.hashCode(userId)) * 31 + query.hashCode();
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (!(obj instanceof SearchKey)) {
            return false;
        }
        SearchKey other = (SearchKey) obj;
        return userId == other.userId && site.equals(other.site) && query.equals(other.query);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

(我在哈希代码中使用 31 作为乘数，因为它是质数，`x * 31`可以优化为`(x << 5) - x`以便在某些平台上更快。)

如上所述，可以为不同的值生成相同的哈希码，如`new SearchKey("fg", 12345, "fg")`、`new SearchKey("fg", 12345, "gH")`和`new SearchKey("gH", 12345, "fg")`，它们都哈希到 3，523，625。如果我们将这些作为基于地图的缓存中的键，攻击者可以通过对我们的服务使用相同的哈希进行多次查询来发起拒绝服务攻击。

`compareTo`方法可以通过繁琐的逐个字段比较来实现:

```
public final class SearchKey implements Comparable<SearchKey> {
    // ...

    @Override
    public int compareTo(SearchKey o) {
        int c = site.compareTo(o.site);
        if (c != 0) {
            return c;
        }
        c = Long.compare(userId, o.userId);
        if (c != 0) {
            return c;
        }
        return query.compareTo(o.query);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是从 Java 8 开始，可以更简洁的实现:

```
public final class SearchKey implements Comparable<SearchKey> {
    private static final Comparator<SearchKey> COMPARATOR =
        Comparator.comparing(SearchKey::getSite)
            .thenComparingLong(SearchKey::getUserId)
            .thenComparing(SearchKey::getQuery);

    // ...

    @Override
    public int compareTo(SearchKey o) {
        return COMPARATOR.compare(this, o);
    } 
```

Enter fullscreen mode Exit fullscreen mode