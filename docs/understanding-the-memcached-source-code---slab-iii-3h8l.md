# 理解 Memcached 源代码- Slab III

> 原文：<https://dev.to/holmeshe/understanding-the-memcached-source-code---slab-iii-3h8l>

[![](img/7c4eac2443aa17f5de9f0e916c990eac.png) slab 分配器(I，](https://dev.to/holmeshe/understanding-the-memcached-source-code---slab-i-165h) [ II，](https://dev.to/holmeshe/understanding-the-memcached-source-code---slab-ii-2kc3)III——本文)是缓存系统的核心模块，它在很大程度上决定了瓶颈资源——内存的利用效率。其他 3 个部分，即:

[![](img/d6aed08203351c706eec17806267e7b8.png) LRU 算法(I ](https://holmeshe.me/understanding-memcached-source-code-IV/) [，II)](https://holmeshe.me/understanding-memcached-source-code-V/) 为条目到期；和一个

[![](img/490f5358184017957ba4e8a0cf607710.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tzc6mlU3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../gallery/club.svg) 基于 libevent 的事件驱动模型(不完整)；和

[![](img/06e0627871e52e5e85a37c2488182acf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g3N4Oenm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../gallery/diamond.svg) 对数据分布一致苛刻(不完整)、

都是围绕着它建立的。

上一次我们看到了内存分配过程，它进一步阐述了**块**和派生的“自由列表”(也称为`slots`)。这次我们将研究如何利用已建立的数据结构来**“slab allocate/release”**内存块，这些内存块将用于存储**项**。

# Slab alloc

首先，我们看看

## do_slabs_alloc

与[相对的是`do_slabs_free`所讨论的](https://holmeshe.me/understanding-memcached-source-code-II//#do-slabs-free)。

注意，`do_slabs_alloc`的“公共”接口是 slabs_alloc，它基本上是一个线程安全的包装器，锁定由配置为多线程的 **Memcached** 实例操作的核心数据结构。

```
void *slabs_alloc(size_t size, unsigned int id, unsigned int *total_chunks,
        unsigned int flags) {
    void *ret;

    pthread_mutex_lock(&slabs_lock);
    ret = do_slabs_alloc(size, id, total_chunks, flags);
    pthread_mutex_unlock(&slabs_lock);
    return ret;
} 
```

<center>[slabs_alloc@slabs.c](mailto:slabs_alloc@slabs.c)</center>

```
...
        case 't':
            settings.num_threads = atoi(optarg);
            if (settings.num_threads <= 0) {
                fprintf(stderr, "Number of threads must be greater than 0\n");
                return 1;
            }
            /* There're other problems when you get above 64 threads.
             * In the future we should portably detect # of cores for the
             * default.
             */
            if (settings.num_threads > 64) {
                fprintf(stderr, "WARNING: Setting a high number of worker"
                                "threads is not recommended.\n"
                                " Set this value to the number of cores in"
                                " your machine or less.\n");
            }
            break;
... 
```

<center>[main@memcached.c](mailto:main@memcached.c):5572</center>

```
static void *do_slabs_alloc(const size_t size, unsigned int id, unsigned int *total_chunks,
        unsigned int flags) {
    slabclass_t *p;
    void *ret = NULL;
    item *it = NULL;
...
    p = &slabclass[id]; // scr: ----------------------------------------> 1)
...
    if (total_chunks != NULL) {
        *total_chunks = p->slabs * p->perslab; // scr: -----------------> 2)
    }
    /* fail unless we have space at the end of a recently allocated page,
       we have something on our freelist, or we could allocate a new page */
    if (p->sl_curr == 0 && flags != SLABS_ALLOC_NO_NEWPAGE) { // scr: --> *)
        do_slabs_newslab(id); // scr: ----------------------------------> 3)
    }

    if (p->sl_curr != 0) {
        /* return off our freelist */
        it = (item *)p->slots; // scr: ---------------------------------> 4)
        p->slots = it->next;
        if (it->next) it->next->prev = 0;
        /* Kill flag and initialize refcount here for lock safety in slab
         * mover's freeness detection. */
        it->it_flags &= ~ITEM_SLABBED; // scr: -------------------------> 5)
        it->refcount = 1;
        p->sl_curr--;
        ret = (void *)it; // scr: --------------------------------------> 6)
    } else {
        ret = NULL;
    }

...
    return ret;
} 
```

<center>[do_slabs_alloc@slabs.c](mailto:do_slabs_alloc@slabs.c)</center>

1)对于*项目分配*，`id`表示最适合所请求*项目尺寸*的**板坯类别**。换句话说，`id`是使用实际的*项目大小*选择的，其过程将很快讨论。

2) `total_chunks`是输出 **slab 类**可用的*内存块*(*空闲列表*中的条目)总数的参数。`if (total_chunks != NULL)`表明参数是可选的。

*)顾名思义，`SLABS_ALLOC_NO_NEWPAGE` ( `flags`)防止该方法在没有可用内存块时分配新的**块**。在*项目分配*的正常路径中不使用该选项，因此暂时忽略。

3)当没有空闲存储块时，分配新的块。这里的`p->sl_curr`表示可用的*块*的数量，其值在每次调用该方法时减少(在下面的步骤 5 中)。

相反，该字段在 [do_slabs_free](https://holmeshe.me/understanding-memcached-source-code-II/#do-slabs-free) 中增加。注意*新板坯*也已经从[覆盖到这里](https://holmeshe.me/understanding-memcached-source-code-II/#New-slab)。

4)从*空闲列表*中移除前面的元素( **f** ，并将其设置为`it`。

在 [do_slabs_free](https://holmeshe.me/understanding-memcached-source-code-II/#do-slabs-free) 中，一个元素被添加到*空闲列表*的前面。

5)清除卡盘的`ITEM_SLABBED`(**f**)，将其参考计数设置为 1，并将`p->sl_curr`减少`1`。

同样，该标志在[do _ slbs _ free](https://holmeshe.me/understanding-memcached-source-code-II/#do-slabs-free)中设置。

6)回车( **f** )。

接下来，我们来看看基于*项目大小*确定`id`的过程，其主要方法是

## 板坯 _clsid

```
unsigned int slabs_clsid(const size_t size) {
    int res = POWER_SMALLEST;

    if (size == 0)
        return 0;
    while (size > slabclass[res].size)
        if (res++ == power_largest)     /* won't fit in the biggest slab */
            return 0;
    return res;
} 
```

<center>[do_slabs_alloc@slabs.c](mailto:do_slabs_alloc@slabs.c)</center>

`slabs_clsid`主要由一个`while`循环组成，该循环线性搜索可能最小的**板坯类**，该板坯类可以包含所请求的`size`。该方法在`slabs_alloc`之前从 do_item_alloc 调用。我们将在下面的帖子中讨论 do_item_alloc。

```
item *do_item_alloc(char *key, const size_t nkey, const unsigned int flags,
                    const rel_time_t exptime, const int nbytes,
                    const uint32_t cur_hv) {
...
    unsigned int id = slabs_clsid(ntotal);
    if (id == 0)
        return 0;
...
        it = slabs_alloc(ntotal, id, &total_chunks, 0);
... 
```

<center>[do_item_alloc@items.c](mailto:do_item_alloc@items.c)</center>