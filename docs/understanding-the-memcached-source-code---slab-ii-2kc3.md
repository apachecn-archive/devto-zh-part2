# 理解 Memcached 源代码- Slab II

> 原文：<https://dev.to/holmeshe/understanding-the-memcached-source-code---slab-ii-2kc3>

[![](img/7c4eac2443aa17f5de9f0e916c990eac.png) slab allocator (I，](https://dev.to/holmeshe/understanding-the-memcached-source-code---slab-i-165h)II——本文[，III)](https://holmeshe.me/understanding-memcached-source-code-III/) 是缓存系统的核心模块，很大程度上决定了瓶颈资源——内存的利用效率。其他 3 个部分，即:

[![](img/d6aed08203351c706eec17806267e7b8.png) LRU 算法(I ](https://holmeshe.me/understanding-memcached-source-code-IV/) [，II)](https://holmeshe.me/understanding-memcached-source-code-V/) 为条目到期；和一个

[![](img/490f5358184017957ba4e8a0cf607710.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tzc6mlU3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../gallery/club.svg) 基于 libevent 的事件驱动模型(不完整)；和

[![](img/06e0627871e52e5e85a37c2488182acf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g3N4Oenm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../gallery/diamond.svg) 对数据分布一致苛刻(不完整)、

都是围绕着它建立的。

这次我们继续检查 **slab** 的内存是如何分配的。

首先，我们来看一下前一篇文章中忽略的两个参数。第一个是 settings.maxbytes，它限制了 **memcached** 实例可以使用的总内存。在 slabs_init 中，settings.maxbytes 的值被赋给了全局变量 mem_limit，这个变量很快就会用到。

```
void slabs_init(const size_t limit, const double factor, const bool prealloc, const uint32_t *slab_sizes) {
...
    mem_limit = limit; // scr: here
... 
```

<center>[slabs_init@memcached.c](mailto:slabs_init@memcached.c)</center>

```
...
  settings.maxbytes = 64 * 1024 * 1024; /* default is 64MB */
...
        case 'm':
            settings.maxbytes = ((size_t)atoi(optarg)) * 1024 * 1024;
            break;
... 
```

<center>[memcached.c:210,5493](https://play.google.com/store/apps/details?id=me.holmeshe.sc)</center>

```
 static size_t mem_limit = 0; 
```

<center>[memcached.c:43](https://play.google.com/store/apps/details?id=me.holmeshe.sc)</center>

另一个论点是`preallocate`。它决定是否为每个**板坯类别**预分配 **板坯***。该参数由`L`命令行参数切换。*

```
...
   bool preallocate = false;
...
        case 'L' :
            if (enable_large_pages() == 0) {
                preallocate = true;
            } else {
                fprintf(stderr, "Cannot enable large pages on this system\n"
                    "(There is no Linux support as of this version)\n");
                return 1;
            }
            break;
... 
```

<center>[main@memcached.c](mailto:main@memcached.c):5350,5597</center>

接下来，我们看一下**片**内存分配本身的方法。

# 新建石板

## do_slabs_newslab

更具体地说，该方法为参数`id`指示的**板坯类别**分配一个 1M 大小的**板坯**。

```
static int do_slabs_newslab(const unsigned int id) {
    slabclass_t *p = &slabclass[id]; // scr: ----------------------------> 1)
    slabclass_t *g = &slabclass[SLAB_GLOBAL_PAGE_POOL]; // scr: ---------> *)
    int len = settings.slab_reassign ? settings.item_size_max // scr: ---> 2)
        : p->size * p->perslab;
    char *ptr;

    if ((mem_limit && mem_malloced + len > mem_limit && p->slabs > 0 // -> 3)
         && g->slabs == 0)) {
        mem_limit_reached = true;
        MEMCACHED_SLABS_SLABCLASS_ALLOCATE_FAILED(id);
        return 0;
    }

    if ((grow_slab_list(id) == 0) || // scr: ----------------------------> 4)
        (((ptr = get_page_from_global_pool()) == NULL) && // scr: -------> *)
        ((ptr = memory_allocate((size_t)len)) == 0))) { // scr: ---------> 5)

        MEMCACHED_SLABS_SLABCLASS_ALLOCATE_FAILED(id);
        return 0;
    }

    memset(ptr, 0, (size_t)len);
    split_slab_page_into_freelist(ptr, id); // scr: ---------------------> 6)

    p->slab_list[p->slabs++] = ptr; // scr: -----------------------------> 7)
    MEMCACHED_SLABS_SLABCLASS_ALLOCATE(id);

    return 1;
} 
```

<center>[do_slabs_newslab@slabs.c](mailto:do_slabs_newslab@slabs.c)</center>

1) `slabclass[id]`是**板坯类**之一，其初始化在[上篇](https://holmeshe.me/understanding-memcached-source-code-I/#Module-initialization)中讨论。

2) settings.slab_reassign 决定是否使用一个*再平衡机制*，该机制回收未使用的**板**，并在**板类**之间重新分配它们。这要求所有**板坯类别**中包含的**板坯**具有相同的尺寸，因此该*设置*也决定是否使用一致的(即`settings.item_size_max`或在之前提到的[的 1M)或不一致的(即`p->size * p->perslab` ) **板坯**。除了相关的命令行参数“slab_reassign”之外，该值还可以由另一个参数“modern”控制。对于“现代”这个名称所暗示的积极意义，本文通篇将使用 1M。](https://holmeshe.me/understanding-memcached-source-code-I/#What-is-a-slab)

```
...
    settings.slab_reassign = false;
...
            case SLAB_REASSIGN:
                settings.slab_reassign = true;
            break;
... 
```

<center>[main@memcached.c](mailto:main@memcached.c):238,5694</center>

```
 case MODERN:
                /* Modernized defaults. Need to add equivalent no_* flags
                 * before making truly default. */
                settings.slab_reassign = true;
                settings.slab_automove = 1;
...
            break; 
```

<center>[main@memcached.c](mailto:main@memcached.c):5820</center>

注意*，*再平衡机制*将在我们更好地理解 LRU 模块后讨论。

3)检查内存使用量是否会超过上限。

4) grow_slab_list 检查我们是否需要增加 [slabclass_t.slab_list](https://holmeshe.me/understanding-memcached-source-code-I/#core_ds) ，如果需要，就增加它。

```
static int grow_slab_list (const unsigned int id) {
    slabclass_t *p = &slabclass[id];
    if (p->slabs == p->list_size) {
        size_t new_size =  (p->list_size != 0) ? p->list_size * 2 : 16;
        void *new_list = realloc(p->slab_list, new_size * sizeof(void *));
        if (new_list == 0) return 0;
        p->list_size = new_size;
        p->slab_list = new_list;
    }
    return 1;
} 
```

<center>[grow_slab_list@slabs.c](mailto:grow_slab_list@slabs.c)</center>

5) memory_allocate 为**层块**分配实际内存。如前所述，这里`len`的值是 1M。

```
static void *memory_allocate(size_t size) {
    void *ret;

    if (mem_base == NULL) {
        /* We are not using a preallocated large memory chunk */
        ret = malloc(size);
    } else { // scr: when preallocate is set to true
... 
```

<center>[memory_allocate@slabs.c](mailto:memory_allocate@slabs.c)</center>

6) `split_slab_page_into_freelist`初始化(释放)新分配的`slab`为对象存储做准备。这种方法将在下一节讨论。

7)将新分配的`slab`添加到 [slabclass_t.slab_list](https://holmeshe.me/understanding-memcached-source-code-I/#core_ds) 中。

到目前为止发生的事情可以用下图来总结，(我们假设`do_slabs_newslab(n)`被调用了两次)

[![new slabs](img/7153769d58a0c24ab0fb40c81c2ed07b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E7LxyxpS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/irsa62d1t65dlw22mb8t.png)

现在我们来看看第 6 步中的 1M 厚**厚片**的内部。

## split _ slab _ page _ into _ freelist

```
static void split_slab_page_into_freelist(char *ptr, const unsigned int id) {
    slabclass_t *p = &slabclass[id];
    int x;
    for (x = 0; x < p->perslab; x++) {
        do_slabs_free(ptr, 0, id);
        ptr += p->size;
    }
} 
```

<center>[split_slab_page_into_freelist@slabs.c](mailto:split_slab_page_into_freelist@slabs.c)</center>

该方法遍历一个 **slab** 内的所有 _item chunk_s(大小为 [slabclass_t.size](https://holmeshe.me/understanding-memcached-source-code-I/#core_ds) )。对于它们中的每一个，该方法通过调用`do_slabs_free`来初始化它的元数据。解释这一过程的另一种方式是“将一个片分割成**项**空闲列表”。正如您可能已经想到的，这个“空闲列表”将来会被**项目分配**使用。

## do_slabs_free

```
static void do_slabs_free(void *ptr, const size_t size, unsigned int id) {
    slabclass_t *p;
    item *it;
...
    p = &slabclass[id];

    it = (item *)ptr;
    it->it_flags = ITEM_SLABBED; // scr: ---------------> 1)
    it->slabs_clsid = 0;
    it->prev = 0; // scr: ------------------------------> 2)
    it->next = p->slots;
    if (it->next) it->next->prev = it;
    p->slots = it;

    p->sl_curr++; // scr: ------------------------------> 3)
    p->requested -= size;
    return;
} 
```

<center>[do_slabs_free@slabs.c](mailto:do_slabs_free@slabs.c)</center>

该方法对填充在 **项目块** 开始处的项目元数据起作用。

```
typedef struct _stritem {
    /* Protected by LRU locks */
    struct _stritem *next;
    struct _stritem *prev;
...
    uint8_t         it_flags;   /* ITEM_* above */
    uint8_t         slabs_clsid;/* which slab class we're in */
...
} item; 
```

<center>[main@memcached.c](mailto:main@memcached.c):5820</center>

1)初始化一些字段。item 是另一个核心数据结构，我们稍后会回到 **item** 数据结构。

2)将**项**添加到链表的前面(也叫自由列表)。并更新列表头， [slabclass_t.slots](https://holmeshe.me/understanding-memcached-source-code-I/#core_ds) 。

3)更新可用(空闲列表)槽计数，[slab class _ t . sl _ curr](https://holmeshe.me/understanding-memcached-source-code-I/#core_ds)；并更新 [slabclass_t.requested](https://holmeshe.me/understanding-memcached-source-code-I/#core_ds) 进行统计。注意，这里我们实际上并没有释放一个**项**，所以传递的`size`是 0。

[![free list](img/0410cfbfc5fb2fbd8dafd4692d32f789.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kObVFJoD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n445ucqde12rcab2wued.png)

# 板坯预分配

接下来我们看看`do_slabs_newslab`是如何使用的。当`preallocate`被设置为`true`时，它被调用的一个地方是来自所讨论的 slabs_init，

```
void slabs_init(const size_t limit, const double factor, const bool prealloc, const uint32_t *slab_sizes) {
...
    if (prealloc) {
        slabs_preallocate(power_largest);
    }
} 
```

<center>[slabs_init@slabs.c](mailto:slabs_init@slabs.c)</center>

```
static void slabs_preallocate (const unsigned int maxslabs) {
    int i;
    unsigned int prealloc = 0;

    /* pre-allocate a 1MB slab in every size class so people don't get
       confused by non-intuitive "SERVER_ERROR out of memory"
       messages.  this is the most common question on the mailing
       list.  if you really don't want this, you can rebuild without
       these three lines.  */

    for (i = POWER_SMALLEST /* scr: 1 */; i < MAX_NUMBER_OF_SLAB_CLASSES; i++) {
        if (++prealloc > maxslabs)
            return;
        if (do_slabs_newslab(i) == 0) {
            fprintf(stderr, "Error while preallocating slab memory!\n"
                "If using -L or other prealloc options, max memory must be "
                "at least %d megabytes.\n", power_largest);
            exit(1);
        }
    }

} 
```

<center>[slabs_preallocate@slabs.c](mailto:slabs_preallocate@slabs.c)</center>

该方法简单地从 POWER _ minimum 开始遍历 [slabclass](https://holmeshe.me/understanding-memcached-source-code-I/#core_ds) ，即第一个条目，并为每个条目分配一个 **slab** 。请注意，第 0 个是上述*再平衡机构*使用的特殊**板坯类别**。

```
#define POWER_SMALLEST 1
#define POWER_LARGEST 256 /* actual cap is 255 */ #define SLAB_GLOBAL_PAGE_POOL 0 /* magic slab class for storing pages for reassignment */ 
```

<center>[memcached.h:88](https://play.google.com/store/apps/details?id=me.holmeshe.sc)</center>

# 参考文献

同上[上一篇](https://holmeshe.me/understanding-memcached-source-code-I/#References)。