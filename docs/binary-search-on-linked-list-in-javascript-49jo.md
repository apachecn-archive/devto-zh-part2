# Javascript 中链表上的二分搜索法。

> 原文：<https://dev.to/abhishekmittal/binary-search-on-linked-list-in-javascript-49jo>

我们从 javascript 和二分搜索法、堆排序、BFS、DFS 等算法中获得的可能性一直是我们学术界的重要组成部分。如果我们能用 javascript 实现同样的目标会怎么样。

最近我一直在深入学习 JS 和 javascript 的精华，最终我用 javascript 实现了所有的算法。如果能在节点环境下和他们合作会有多大帮助。

让我们看一个关于如何在单链表中实现简单二分搜索法的例子。

我们需要做的就是创建一个文件 linkedList.js

mkdir link-list-bs
CD link-list-bs
touch linkList.js index . js
在我们的 linklist . js 里面添加以下代码片段:

```
class Node {
    constructor(value, next = null) {
        this.value = value;
        this.next = next;
    }
} 
```

在上面的代码中，它看起来非常类似于 C 语言中的 struct 或 Java 或 Python 中的 class，我们在这里做了同样的事情，创建一个类，它在你的链表中创建一个节点的单个实例，每次我们向它添加一个新的。

现在让我们添加这段代码，看看我们是如何处理链表的

```
// linkedList.js
// ...

module.exports = class LinkedList {
    /**
     *Creates an instance of LinkedList.
     */
    constructor() {
        this.head = null;
        this.tail = null;
        // to make sure of the valuethis.compare = (a, b) => a === b;
    }

    /**
     * @description add a new node to the linked list
     *
     * @param {*} value
     * @returns
     */
    append(value) {
        const newNode = new Node(value);

        if (!this.head) {
            this.head = newNode;
            this.tail = newNode;
            return this;
        }

        this.tail.next = newNode;
        this.tail = newNode;
        return this;
    }

    /**
     * @description delete a node from the linked list
     *
     * @param {*} value
     * @returns
     */
    delete(value) {
        if (!this.head) {
            return null;
        }

        let deletedNode = null;

        // if the value is in the first nodewhile (this.head && this.compare(this.head.value, value)) {
            deletedNode = this.head;
            this.head = this.head.next;
        }

        let currentNode = this.head;

        // if current node is not null travel through the linked list// to find the node with the value and replace the next with the current nodeif (currentNode !== null) {while (currentNode.next) {
                if (this.compare(currentNode.next.value, value)) {
                    deletedNode = currentNode.next;
                    currentNode.next = currentNode.next.next;
                } else {
                    currentNode = currentNode.next;
                }
            }
        }

        // two nodes in the list remainsif (this.compare(this.tail.value, value)) {this.tail = currentNode;
        }

        return deletedNode;
    }
}; 
```

现在我们创建另一个导出的类，这样我们每次创建新的链表时都可以创建一个实例。

在类链表中，我们需要成员函数:

append(value) : append 函数创建一个新的节点，它为这个类实例设置头，尾指向链表的最后一个值。
delete(value): delete 函数从链表中删除节点。
让我们回到 index.js

const LinkList = require('。/linked list ')；
添加上面一行，将链表导入到您的游戏中。让我们为二分搜索法做更多的准备。

```
 const init = () => {
    const ll = new LinkList();

    // adding few elements already sorted
    try {
        ll.append(10);
        ll.append(20);
        ll.append(30);
        ll.append(40);
        ll.append(50);
        ll.append(60);
    } catch (error) {
        console.log(error);
    }
};
init(); 
```

太好了！，我们都准备好了。现在我们有了这些元素的链表。如果你想知道发生了什么，你可以通过安慰来偷看一下:

console.log('我的链表: '，JSON . stringify(ll))；现在让我们在 index.js 中编写二分搜索法函数

```
// index.js

const binarySearch = (head, value) => {
    let start = head;
    let last = null;

    return null;
}; 
```

这似乎不对，我们需要在这里添加真正的方法

```
/**
 *
 * @description Binary Search
 * @param {*} head
 * @param {*} value
 * @returns
 */
const binarySearch = (head, value) => {
    let start = head;
    let last = null;
    let mid;
    do {
        if (start === last && last && last.value != value) return null;
        mid = middle(start, last);

        if (!mid) return null;

        if (mid.value === value) return mid;
        else {
            mid.value < value ? (start = mid.next) : (last = mid);
        }
    } while (last != null || last === start);

    return null;
}; 
```

现在我们的 binarySearch 函数看起来像这样。但是只有在中间值之前，它才起作用，让我们加上:

```
/**
 *
 * @description Middle Function
 * @param {*} start
 * @param {*} last
 * @returns
 */
const middle = (start, last) => {
    if (start === null) {
        return null;
    }

    let slow = start;
    let fast = start.next;

    while (fast != last) {

        fast = fast.next;
        if (fast != last) {
            slow = slow.next;
            fast = fast.next;
        }
    }

    return slow;
}; 
```

在这里，我们找到了中间节点，这有助于我们找到最后的值。

一切就绪，这是对我们的 init 函数的最后添加:

```
// find the value 10 in the linked list
console.log('LL: ', binarySearch(ll.head, 10) ? 'found' : 'does not exist'); 
```

现在，让我们运行这个。

```
node index.js
// output 
LL:  found 
```

所以我们看到，javascript 可以在任何地方使用。

用 js 试试别的。