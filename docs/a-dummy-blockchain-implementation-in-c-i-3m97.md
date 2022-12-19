# C (I)中的一个伪区块链实现

> 原文：<https://dev.to/devmarchan/a-dummy-blockchain-implementation-in-c-i-3m97>

## 用区块链投票

成千上万的文章都是关于区块链以及它将如何改变世界的。我不想谈为什么我不明白这怎么可能。无论是比特币，这(从我的错误观点来看)只是人们试图赚取美元的一种方式:比特币的最终目标是等待足够的时间，并将其兑换成美元(真诚地说，革命在哪里？).

但这不是主题。**这是一个用 C 语言一步步实现区块链算法的描述**。为了稍作改变，它将模拟投票系统，而不是货币交易。

首先，有必要了解区块链的基本概念。如果你还不知道，我推荐你[这个](https://blockgeeks.com/guides/what-is-blockchain-technology/)、[这个](https://medium.com/the-mission/a-simple-explanation-on-how-blockchain-works-e52f75da6e9a)和[这个 5 分钟的介绍视频](https://www.youtube.com/watch?v=SSo_EIwHSd4&feature=youtu.be)。

现在我们只知道什么是事务、块和散列，我们准备好了:**区块链在其核心**非常简单。它是由计算机科学中一种众所周知的数据结构形成的:链表。

[![A linked list is a sequence of nodes. Each node contains a bulk of data and a link to the next node of the list.](../Images/7a6d3cebe2b7ee0ce5db042ea66b69ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7AgWRnR7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/myram.xyz/wp-content/uploads/2018/09/408px-Singly-linked-list.svg_.png%3Fw%3D408%26ssl%3D1)

### 1。街区

```
typedef int hash;
typedef char *transaction;

typedef struct Block_T {
    hash previous_block_hash;
    hash block_hash;
    transaction transactions;    
}block_t; 
```

它用前一个块的散列、当前块的散列和一个事务来声明一个块。该块只有一个事务，而不是一组事务:单个字符串。

### 2。杂烩

```
int string_hash(void *string)
{
    /* This is the djb2 string hash function */
    int result = 5381;
    unsigned char *p;
    p = (unsigned char *) string;

    while (*p != '\0') {
        result = (result << 5) + result + *p;
        ++p;
    }

    return result;
} 
```

这个函数是从某处复制来的，它接受一个字符串并返回一个整数，这个整数是惟一的散列。

### 3。名单

我也不会解释列表是如何工作的，因为它是一个非常基本的数据结构，很容易在互联网上找到资源。然而，对我来说，在 c 语言中找到一个清晰、干净和简单的列表实现并不容易，所以我选择了一个并对其进行了修改。

```
typedef struct {
    block_t info;
} DATA;

typedef struct node {
    DATA data;
    struct node* next;
} NODE; 
```

我们的列表节点的数据是前面定义的 block_t 结构。

```
NODE* add(NODE* node, DATA data);
void print_list(NODE* head);
NODE * reverse(NODE * node);
void get_list_transactions(NODE* head,unsigned char *list_trans); 
```

而 list 的主要功能是 add，以便在末尾插入新的节点，并打印，这将显示该节点的数据信息，也就是该块。

那些是主要成分。在下一篇文章中，我将把所有的东西放在一起，看一场区块链赛跑。

(原帖在 [myram](https://myram.xyz/c-blockchain-implementation-1/) 中)