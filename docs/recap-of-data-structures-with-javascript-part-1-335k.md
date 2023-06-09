# 用 Javascript 回顾数据结构第 1 部分

> 原文：<https://dev.to/bnorbertjs/recap-of-data-structures-with-javascript-part-1-335k>

[![](img/5500259705f4e3ab1dbdb5c99bd1ff05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ya6RkoLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l77pg255rwfmhj9spdbf.jpg) 
我决定写一篇关于实现通用数据结构的文章。重点主要是 javascript 中的编码，而不是理论上的解释。

# 链接列表

链表是由节点组成的线性数据结构。根据链表的类型，节点有不同的属性。

*   单链表:2 个属性，数据和指向下一个节点的指针
*   双向链表:3 个属性，数据，一个指向下一个节点的指针，和另一个指向前一个节点的指针。

为了遍历列表，我们只需要访问起始节点(head)。

### 任务

### *任务一:创建简单链表*

```
//LinkedList.js
const Node = (value) => ({
    value,
    next: null
})

const LinkedList = (head = null) =>({
    length: 0,
    set head(node){
        head = node
    },
    get head(){ return head }
})

export default LinkedList 
```

最初，头部为空，长度等于 0。让我们将节点添加到列表中。

### *任务二:添加功能*

```
//LinkedList.js
...
add(value){
        if(!this.head){
            this.head = Node(value)
            this.length++
            return this
        }
        let current = this.head
        while(current.next){
            current = current.next
        }
        current.next = Node(value)
        this.length++
        return this
    },
... 
```

首先，我们检查头部是否为空。如果是，我们将头部设置为新节点。如果不为空，我们开始循环，直到到达列表中的最后一个节点。在`while`循环之后，`current`将指向最后一个节点。最后，我们将新节点添加到列表的末尾。我喜欢返回`this`，因为这样我可以像这样链接函数调用:`list.add(5).add(6)`。

如果您想要一些额外的练习，您可以实现一个`addTo`函数，它接受一个值和位置参数并将节点放在那个位置。

### *任务三:移除功能*

```
//LinkedList.js
...
remove(value){
        let current = this.head
        let previous = null
        //deleting the head
        if(current.value === value){
            this.head = current.next
            this.length--
            return this
        }
        //delete from the middle
        while(current){
            if(current.value === value){
                previous.next = current.next
                this.length--
                return this
            }
            previous = current
            current = current.next
        }
        return this
    },
... 
```

如你所见，我们有两个场景。如果我们想删除头部本身，我们只需改变头部指针，并减少长度。如果我们需要从列表的中间或末尾删除一些东西，我们需要循环直到我们得到值。诀窍在于，在每次迭代中，我们也存储前一个节点。
`previous.next = current.next`是这里的关键。如果我们想把`2`从这样一个列表中去掉:
`1 -> 2 -> 3`一旦控制流跳转到 If 语句中，变量`previous`就会是`1`，`current`就会是`2`，`current.next`就会是`3`。所以我们要做的就是把`1`和`3`而不是`2`进行“连接”。

### *任务 4:找出列表中是否包含元素*

```
//LinkedList.js
...
contains(value){
        let current = this.head

        while(current){
            if(current.value === value){
                return true
            }
            current = current.next
        }
        return false
    }
... 
```

很简单。我们遍历列表，如果得到的值等于`value`参数，则返回 true。

### 测试

我想用 mocha & chai 来测试这些函数的实现，但是我不确定这篇文章会有多长，所以我宁愿节省空间。我创建了一个`index.js`文件来检查这些函数是否正常工作。

```
//index.js

import LinkedList from "./LinkedList"

const myList = LinkedList()

myList.add(1).add(2).add(3)

console.log(JSON.stringify(myList))

myList.remove(1)
myList.remove(2)
myList.remove(3)
console.log(JSON.stringify(myList))

console.log(myList.contains(1))
console.log(myList.contains(0)) 
```

# 树木

树是一种递归数据结构，由节点组成，就像链表一样。然而，树却大不相同。在这种情况下，起始节点称为根节点。每棵树至少有一个根节点，每个根节点有零个或多个子节点。有几种类型的树，在这篇文章中，我将重点介绍二叉树。

### 二叉树

二叉树是一种特殊类型的树，其中每个节点有 0、1 或 2 个子节点(左、右)。

### 二叉查找树- BST

好吧，那么另一个“子类”。二叉查找树是一棵二叉树，但它的节点按以下方式排序:

*   每个左边的节点必须小于当前节点。
*   每个右节点必须大于当前节点。

### 任务

### *任务 1:创建简单的二叉树*

```
//BinarySearchTree.js

export const Node = (value) => ({
    value,
    right: null,
    left: null
})

export const SimpleBinTree = (root = null) => ({
    get root() {return root},
    set root(node){ root = node},
})

//That's it. Our dummy binary tree is ready to use.
//index.js

import {SimpleBinTree, Node} from "./BinarySearchTree"

const root = Node(5)
root.left = Node(3)
root.right = Node(10)

const tree = SimpleBinTree(root) 
```

所以，`tree`看起来是这样的:

[![](img/2ad956656a4d96cb689457fc7871b9fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WK53IZ1q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/61ir9eyipwn53wylp7i2.png)

### *任务 2:遍历树并访问每个节点*

```
//BinarySearchTree.js
//add these functions
//to the SimpleBinTree object under the
//getter and setter
inOrder (node) {
    if(node){
      this.inOrder(node.left)
      console.log(node)
      this.inOrder(node.right)
    }
},
preOrder (node) {
    if(node){
      console.log(node)
      this.preOrder(node.left)
      this.preOrder(node.right)
    }
},
postOrder (node) {
    if(node){
      this.postOrder(node.left)
      this.postOrder(node.right)
      console.log(node)
    }
} 
```

递归遍历一棵树有三种不同的方法。`inOrder`方法首先访问树的左侧，然后是根，最后是右侧。`preOrder`和`postOrder`应该很简单，它们非常相似，但是它们访问节点的顺序不同。

```
//you can call these functions like this
//index.js
tree.inOrder(tree.root) // output should be 3,5,10 (left, root, right) 
```

### *任务三:创建一个二叉查找树*

好的，让我们创建一个比前一个更具体的树。姑且称之为`BST`。由于`SimpleBinTree`已经有几个我不想再次实现的函数，我将确保我的`BST`将从`SimpleBinTree`中“继承”每一个函数。

```
//BinarySearchTree.js
export const BST = (root = null) => Object.assign(SimpleBinTree(root),{
    //binary search tree specific functions
}) 
```

首先，我们需要`add`功能来填充树。

```
//BinarySearchTree.js
...
add(val){
   if(!this.root){
      this.root = Node(val)
   }else{
      searchTreeToAdd(val, this.root)
   }
},
...

//this function is not part of the object.
const searchTreeToAdd = (val, node) => {
    if(val <= node.value){
        //add to the left side
        node.left ? searchTreeToAdd(val, node.left) :  node.left = Node(val)
    }else{
        //add to the right side
        node.right ? searchTreeToAdd(val, node.right) : node.right = Node(val)
    }
} 
```

首先，我们检查根是否存在。如果它是`null`，我们的新节点将是根。
如果已经有一个根，那么我们需要检查新节点的值。如果它小于当前节点，这意味着我们需要把它放在树的左边。如果节点的值大于当前值，我们把它放在右边的某个地方。

现在，让我们确定树的最小值。

```
//BinarySearchTree.js
...

getMin(node = this.root){
   while(node.left){
      node = node.left
   }
   return node
},
... 
```

这是一个很容易实现的函数，我们在树的左边迭代来找到最小值。

困难的部分来了。从树中删除节点。

```
//BinarySearchTree.js
...
remove(value){
   this.root = this.removeNode(value, this.root)
},
removeNode(value, node){
  if(node.value === value){
     if(!node.right && !node.left){
        //node got 0 child
        return null
      }else if(!node.left){
         //node doesn't have a left child so link the right to its parent
        return node.right
      }else if(!node.right){
         //node doesn't have a right child so link the left to its parent
         return node.left
      }else{
         //node has 2 children
         //get the minimum value on the right side
         const minNode = this.getMin(node.right)
         node.value = minNode.value
         node.right = this.removeNode(node.value, node.right)
         return node
      }

   }else if(value < node.value){
         //value is smaller, we search on the left side recursively
         node.left = this.removeNode(value, node.left)
         return node
   }else if(value > node.value){
         //value is bigger, we search on the right side recursively
         node.right = this.removeNode(value, node.right)
         return node
   }
}
... 
```

首先，我们寻找想要删除的值。如果我们得到了值(`node.value === value`)，那么我们需要检查那个节点上的子节点的数量。如果它没有孩子，我们就删除它。如果它有一个左边或右边的孩子，我们将它连接到它的父节点。如果节点有 2 个子节点，我们需要搜索右边最小的元素，这样我们可以用它替换当前节点。

### 测试

创建 index.js 文件并导入您的二叉查找树。

```
//index.js
import {BST} from "./BinarySearchTree"

const myBST = BST()

myBST.add(10)
myBST.add(9)
myBST.add(16)

console.log(myBST.remove(10))
console.log(myBST.root)

console.log(myBST.getMin()) 
```

# 哈希表

哈希表是一种非常强大的键值数据结构。人们主要使用它是因为它高效的查找。为了更好的理解，我给你看一张图。
[![](img/23f02a89fa799a235f58efd5108813c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K5RjFPTJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b4m9nh33fpqrnahydazo.png) 
你提供一个键，这个键通过一个散列函数返回这个键的索引。之后，您可以在数组中查找常量时间的值，因为您知道它的索引。然而，你可能会有冲突。这意味着您的哈希函数为不同的键返回相同的索引。在这种情况下，您必须遍历数组并找到与该键相关联的值。(这效率较低，需要 O(N ),其中 N 是特定索引的冲突数量)。

### 任务

### *任务 1:创建一个简单的哈希表*

```
//HashTable.js
const HashTable = () => ({
    storage: [],
    storageLen: 4,
}) 
```

就这样，我们有了一个带有`storage`属性的`HashTable`，其中将存储【键，值】对和一个`storageLen`。现在它的值是 4，但是如果你想避免冲突，你需要给它分配一个更大的值。

### *任务 2:创建返回关键字索引的散列函数*

```
//HashTable.js
//this function is private. Not part of the HashTable, and I do not export it.
const hashKey = (key, len) => {
    const hash = key
        .split("")
        .reduce( (a, b, index) => a + b.charCodeAt(), "")

    return hash % len
} 
```

如果`len`很小，这是一个非常简单的哈希函数，会产生很多冲突。函数的`len`参数将始终是`HashTable`的`storageLen`属性。所以每次我们调用这个函数，它都会给我们一个 0 到 4 之间的索引(`return hash % len`)。如果您将`storageLen`属性更改为 15，那么它将给我们一个从 0 到 15 的索引。

### *任务三:向哈希表添加值*

```
//HashTable.js
...
//place this function inside the HashTable object
add(key, value){
        //base case. index is unique, just push the key/value pair to the storage
        const index = hashKey(key, this.storageLen)
        if(!this.storage[index]){
            this.storage[index] = [[key, value]]
            return this
        }
        //index already exists
        const isKeyExists = this.storage[index].some(x => key === x[0])

        if(isKeyExists){
            //key already exists, overwrite the previous value
            this.storage[index] = [[key, value]]
        }else{
            //key doesn't exists, but index is not unique -> we have a collision here
            this.storage[index].push([key, value])
        }
    }
... 
```

我试图尽可能多的评论，所以我希望这个函数是简单明了的。

### *任务四:获取函数(查找)*

```
//HashTable.js
...
get(key){
        const index = hashKey(key, this.storageLen)
        const keyIndex = 0
        const valueIndex = 1
        const hasCollision = this.storage[index].length > 1
        //base scenario: index is unique so we got O(1) lookup
        if(!hasCollision){
            return this.storage[index][keyIndex][valueIndex]
        }

        //if we have a collision O(n)
        for(const item of this.storage[index]){
            if(item[keyIndex] === key){
                return item[valueIndex]
            }
        }
    }
... 
```

我们可以很容易地发现，如果我们有一个特定的索引`const hasCollision = this.storage[index].length > 1`的冲突。如果是，我们需要在数组上迭代，如果键是相同的，就立即返回项目。

### 测试

为了测试这些函数，创建一个 index.js 并导入我们的`HashTable`。

```
import HashTable from "./HashTable"

const hm = HashTable()

hm.add("Goji", "Cica")
hm.add("Pici Bear", 6)
hm.add("Pici Bear", 1)
hm.add("Pici", 8)

console.log(hm.get("Pici Bear"))
console.log(hm) 
```

# 结束

感谢阅读。在第二部分，我计划实现数据结构，比如队列、图、栈、Bloom filters :O 和其他类似的东西。