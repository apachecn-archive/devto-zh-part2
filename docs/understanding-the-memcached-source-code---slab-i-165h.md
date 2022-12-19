# 理解 Memcached 源代码

> 原文：<https://dev.to/holmeshe/understanding-the-memcached-source-code---slab-i-165h>

[![](img/7c4eac2443aa17f5de9f0e916c990eac.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--HW4WLIte--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../gallery/spade.svg) slab allocator (I -本文[，II ](https://dev.to/holmeshe/understanding-the-memcached-source-code---slab-ii-2kc3) [，III)](https://holmeshe.me/understanding-memcached-source-code-III/) 是缓存系统的核心模块，很大程度上决定了瓶颈资源——内存的利用效率。其他 3 个部分，即:

[![](img/d6aed08203351c706eec17806267e7b8.png) LRU 算法(I ](https://holmeshe.me/understanding-memcached-source-code-IV/) [，II)](https://holmeshe.me/understanding-memcached-source-code-V/) 为条目到期；和一个

[![](img/490f5358184017957ba4e8a0cf607710.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tzc6mlU3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../gallery/club.svg) 基于 libevent 的事件驱动模型(不完整)；和

[![](img/06e0627871e52e5e85a37c2488182acf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g3N4Oenm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../gallery/diamond.svg) 对数据分布一致苛刻(不完整)、

都是围绕着它建立的。

**slab 分配器**的变体在其他系统中实现，比如 nginx 和 Linux 内核，以对抗一个叫做**内存碎片**的常见问题。当然，本文将关注 memcached 的算法实现。

**memcached 版本:1.4.28**

首先，让我们回答一些问题。

# 简介

## 什么是石板

**slab** s 是预先分配的 1M 内存块，可以细分为多个对象。它们被分组为**平板类** es，为各种尺寸的分配请求提供服务。

## 什么是内存碎片，它是如何发生的

特别是，**片分配器**抑制了**内存碎片**。这种碎片存在于分配的内存块中。例如，在 OS 内核环境中，内存管理子系统分配的基本单元被称为*页*。

> 另一方面，**外存碎片**跨组块存在，其解(关键字:buddy)属于另一个故事。

**内部碎片**导致问题的最常见现象如下:

1) `malloc`对小物体的多次调用；与此同时。

2) `free`那些物体被多次调用。

上述过程会产生(大量)无法利用的名义上的“空闲”内存，因为各种大小的离散孔或**碎片**无法被后续的`malloc`重新用于任何大于它们的对象。

## 内存碎片为什么不好

**内存碎片**的影响类似于**内存泄漏**的影响——每当碎片累积到一定程度时，周期性的系统重启是不可避免的，这增加了系统操作的复杂性，甚至更糟，导致用户体验不佳。

## 问题是如何解决的

**板坯分配器**没有消除**内部碎片**。取而代之的是，它将碎片聚合在一起，并将它们锁定在固定的记忆位置。这是通过 1)将相似大小的物体分类到**类**中来实现的；以及 2)将属于同一**类**的对象仅分配在同一组**片** s】或**片类**上。

细节魔鬼在代码中，所以我们开始阅读代码。

**提醒:memcached 版本是 1.4.28**

正在使用的核心数据结构

```
typedef struct {
    unsigned int size;      /* sizes of items */
    unsigned int perslab;   /* how many items per slab */

    void *slots;           /* list of item ptrs */
    unsigned int sl_curr;   /* total free items in list */

    unsigned int slabs;     /* how many slabs were allocated for this class */

    void **slab_list;       /* array of slab pointers */
    unsigned int list_size; /* size of prev array */

    size_t requested; /* The number of requested bytes */
} slabclass_t;

static slabclass_t slabclass[MAX_NUMBER_OF_SLAB_CLASSES]; 
```

<center>[slabclass_t@slabs.c](mailto:slabclass_t@slabs.c)</center>

# 模块初始化

在本节中，我们将研究初始化`slabclass[MAX_NUMBER_OF_SLAB_CLASSES]`数组的`slabs_init`。特别地，该过程初始化两个字段的值，即`slabclass_t.size`，每个**片层类别**的项目(对象)大小，以及第一个**片层**包含的项目号`slabclass_t.perslab`。在逻辑进入*主偶数循环*之前，从这里调用该方法作为 *init* 步骤之一。

```
...
    assoc_init(settings.hashpower_init);
    conn_init();
    slabs_init(settings.maxbytes, settings.factor, preallocate,
               use_slab_sizes ? slab_sizes : NULL);
... 
```

<center>[main@memcached.c](mailto:main@memcached.c):5977</center>

在该步骤中，slab_sizes 和 settings.factor 共同控制决定每个**层块类别**尺寸的路线，它们是:

```
 uint32_t slab_sizes[MAX_NUMBER_OF_SLAB_CLASSES]; 
```

<center>[main@memcached.c](mailto:main@memcached.c):5372</center>

```
settings.factor = 1.25; 
```

<center>[settings_init@memcached.c](mailto:settings_init@memcached.c):217</center>

a)如果 slab_sizes 不为`NULL`，则直接使用数组内的值；和

b)否则，尺寸计算为*基本尺寸* × *n* × `settings.factor`，其中 *n* 为`slabclass`内的指数。

除了默认值，这两个参数也可以在运行时设置。

```
...
        case 'f':
            settings.factor = atof(optarg);
            if (settings.factor <= 1.0) {
                fprintf(stderr, "Factor must be greater than 1\n");
                return 1;
            }
            break;
...
        case 'o': /* It's sub-opts time! */
...
            case SLAB_SIZES:
                if (_parse_slab_sizes(subopts_value, slab_sizes)) {
                    use_slab_sizes = true;
                } else {
                    return 1;
                }
            break;
... 
```

<center>[main@memcached.c](mailto:main@memcached.c):5558, 5810</center>

这个方法的另外两个参数`settings.maxbytes`和`preallocate`将在[稍后](https://dev.to/holmeshe/understanding-the-memcached-source-code---slab-ii-2kc3)讨论。现在我们将`false`设置为`preallocate`，并忽略相关的逻辑流程。

接下来我们来看看`slabs_init`本身。

```
void slabs_init(const size_t limit, const double factor, const bool prealloc, const uint32_t *slab_sizes) {
    int i = POWER_SMALLEST /* scr: 1 */ - 1;
    unsigned int size = sizeof(item) + settings.chunk_size; // scr: ---------> b 1)
...
    memset(slabclass, 0, sizeof(slabclass));

    while (++i < MAX_NUMBER_OF_SLAB_CLASSES-1) {
        if (slab_sizes != NULL) { // scr: -----------------------------------> a 1)
            if (slab_sizes[i-1] == 0)
                break;
            size = slab_sizes[i-1];
        } else if (size >= settings.item_size_max / factor) {
            break;
        }
        /* Make sure items are always n-byte aligned */
        if (size % CHUNK_ALIGN_BYTES) // scr: ---------------------------------> 2)
            size += CHUNK_ALIGN_BYTES - (size % CHUNK_ALIGN_BYTES);

        slabclass[i].size = size;
        slabclass[i].perslab = settings.item_size_max / slabclass[i].size; // -> 3)
        if (slab_sizes == NULL)
            size *= factor; // scr: -----------------------------------------> b 4)
        if (settings.verbose > 1) {
            fprintf(stderr, "slab class %3d: chunk size %9u perslab %7u\n",
                    i, slabclass[i].size, slabclass[i].perslab);
        }
    }
    // scr: -------------------------------------------------------------------> 5)
    power_largest = i;
    slabclass[power_largest].size = settings.item_size_max;
    slabclass[power_largest].perslab = 1;
...
} 
```

<center>[slabs_init@slabs.c](mailto:slabs_init@slabs.c)</center>

## 路线一

1)使用`slab_sizes`中的值；

2)将`size`对准`CHUNK_ALIGN_BYTES`，并将结果交给`slabclass[i].size`；

3)计算`slabclass[i].perslab`；

5)使用`settings.item_size_max`初始化最后一个**平板类别**。

请注意，settings.item_size_max 是每个**层板**的大小，因此它也是在*层板*上分配的项目的最大大小。同样，settings.item_size_max 的值可以在运行时决定。

```
 settings.item_size_max = 1024 * 1024; 
```

<center>[settings_init@memcached.c](mailto:settings_init@memcached.c):226</center>

```
 case 'I':
            buf = strdup(optarg);
            unit = buf[strlen(buf)-1];
            if (unit == 'k' || unit == 'm' ||
                unit == 'K' || unit == 'M') {
                buf[strlen(buf)-1] = '\0';
                size_max = atoi(buf);
                if (unit == 'k' || unit == 'K')
                    size_max *= 1024;
                if (unit == 'm' || unit == 'M')
                    size_max *= 1024 * 1024;
                settings.item_size_max = size_max;
            } else {
                settings.item_size_max = atoi(buf);
            }
            free(buf);
            if (settings.item_size_max < 1024) {
                fprintf(stderr, "Item max size cannot be less than 1024 bytes.\n");
                return 1;
            }
            if (settings.item_size_max > 1024 * 1024 * 128) {
                fprintf(stderr, "Cannot set item size limit higher than 128 mb.\n");
                return 1;
            }
            if (settings.item_size_max > 1024 * 1024) {
                fprintf(stderr, "WARNING: Setting item max size above 1MB is not"
                    " recommended!\n"
                    " Raising this limit increases the minimum memory requirements\n"
                    " and will decrease your memory efficiency.\n"
                );
            }
            break; 
```

<center>[main@memcached.c](mailto:main@memcached.c):5626</center>

## 路线 b

1)用*设置计算*基本大小*. chunk _ size*加上元数据的额外字节(`item`将在后续文章中讨论)；

2)将`size`对准`CHUNK_ALIGN_BYTES`，并将结果交给`slabclass[i].size`；(与路线 a 相同)

3)计算`slabclass[i].perslab`；(与路线 a 相同)

4)使用`factor` ( `settings.factor`)计算下一个`slab class`的尺寸；

5)使用`settings.item_size_max`初始化最后一个**平板类别**。(与路线 a 相同)

# 参考文献

[memcached 维基](https://github.com/memcached/memcached/wiki)

[第 2 回 memcachedのメモリストレージを理解する](http://gihyo.jp/dev/feature/01/memcached/0002)

[Memcached 源码分析之存储机制板坯(7)](https://blog.csdn.net/initphp/article/details/44888555)

[了解 Malloc](https://gokulvasanblog.wordpress.com/2016/07/11/understanding-malloc-part1/)

[Ch8 -板坯分配器](https://www.kernel.org/doc/gorman/html/understand/understand011.html)