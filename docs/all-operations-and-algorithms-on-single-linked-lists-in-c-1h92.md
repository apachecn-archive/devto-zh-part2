# 单链表-数据结构

> 原文：<https://dev.to/mkhy19/all-operations-and-algorithms-on-single-linked-lists-in-c-1h92>

[![alt Linked list](img/6b2e75640138b7f11c7b5cd078edb83e.png "Single Linked list")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TU2MSnFv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/mohamedkhaledyousef/Crash-courses/blob/master/img/linked-list.png%3Fraw%3Dtrue)

# 链表是一种数据结构，它有一个元素列表，每个元素都有一个指向列表中下一个元素的引用。

关于链表，我们要知道的第一件事是，我们有一个头指针，它指向一个节点，这个节点有一些数据，然后指向另一个节点，依此类推，直到一个节点不再指向其他节点。
And 节点包含 2 个元素，值或键(数字如 7)和一个指向下一个包含(另一个数字如 10)的节点的指针

7 →10

## 有一些操作可以在链表上完成，叫做 List API

**1-在列表的前面:**
1-PushFront(键):添加到前面
2-键 TopFront():返回前面的元素
3-PopFront():删除前面的元素

**2-在列表的末尾**
1-推回(key):添加到后面…也称为追加
2-Key TopBack():返回后面的元素
3-PopBack():删除后面的元素
运行时前后操作有区别

**3-我们还可以做:**
1-布尔查找(Key):Key 是否在列表中？
2-Erase(Key):从列表中移除键
3-Boolean Empty():空列表？
4-AddBefore(Node，key):在节点前添加键
5-AddAfter(Node，Key):在节点后添加键

## 举例:

我们有一个空列表，做如下操作:
push back(a)；
push front(b)；
推后(d)；
push front(c)；
PopBack()；

**解:**
推后(一)；a
push front(b)；b 一
的推辞(d)；b a d
push front(c)；c b a d
PopBack()；c b a
所以，最后的结果是= > c b a

**某些操作的运行时间**
push front(Key):add to front in O(1)
Key top front():return of front element in O(1)
pop front():remove front element O(1)
push back(Key):add to back in O(n)with tail
push back(Key):add to back in O(1)with tail
Key top back():return in O(n)with tail
Key top back():返回

# 备注::

**当我们想在后面推的时候？**
1-如果我们没有尾指针，那么这将是一个昂贵的操作。为什么？因为我们从开头开始，沿着列表往下走，直到最后，我们添加了一个节点，所以它将是 O(n)。类似地，如果我们想要 TopBack 或 PopBack
2——如果我们有一个尾指针，这将变得更简单。为什么？因为我们既有指向头部元素的头部指针，也有指向尾部元素的尾部指针。于是第一个元素和最后一个元素就成了廉价操作。
**举个例子**:当我们有了尾指针想要插入元素
——我们首先分配一个节点并放入我们的新键
——然后更新当前尾的下一个指针指向这个新尾
——然后更新尾指针本身
——运行时间将是 O(1)

**当我们有尾指针，我们想删除或弹出元素:**
——这里我们没有从最后一个元素指向前一个元素(之前的元素)的指针
-我们只有一个从前一个元素到最后一个元素的指针，而这个指针并不能帮助我们返回
-所以我们要做的是:
-我们从头开始向下走，直到我们找到指向当前尾部(最后一个元素)的前一个元素或节点
-然后更新我们的尾部指针以指向那个(前一个最后一个元素)
-然后更新尾部指针为空， 这意味着尾指针指向之前的最后一个元素，它变成了尾元素
——然后我们可以删除最后一个元素
——最后的运行时间将是 O(n)，因为我们必须一直走下去

# 单链表算法

**推锋**

```
node <- new node 
node.next <- head 
head <- node 
if tail = nil: //an empty list 
    tail <- head 
```

**PopFront**

```
if head = nil:
    ERROR: empty list
head <- head.next
if head = nil:
    tail <- nil 
```

**推后**

```
node <- new node 
node.key <- key 
node.next = nil 
if tail = nil; 
    head <- tail <- node
else: 
    tail.next <- node, 
    tail <- node 
```

**PopBack**

```
if head = nil : 
    ERROR: empty list
if head = tail
    head <- tail <- nil
else 
    p <- head
    while p.next.next != nil:
         p <- p.next
    p.next <- nil;
    tail <- p 
```

**添加后一个节点:AddAfter(node，key)**

```
node <- new node
node.key <- key
node.next = node.next
node.next = node
if tail = node: //one element in list and this is the same node that we want to add key after that
    tail <- node 
```

**添加 before 和 Pop back 有同样的问题，我们没有返回到前一个元素的链接，所以 AddBefore 是 O(n)**

# 在这里你可以找到我们可以在单链表上进行的所有主要操作:

```
#include<iostream>
using namespace std;

//built node .... node = (data and pointer)
struct node     
{
    int data;   //data item
    node* next; //pointer to next node
};
//built linked list
class linkedlist
{
    private:
        node* head;
    public:
        linkedlist()    //constructor
        {
            head=NULL;  //head pointer points to null 
        }
        void addElementFirst(int d);           
        void addElementEnd(int d);         
        void addElementAfter(int d,int b);    
        void deleteElement(int d);
        void display();                    
};

//Push front code
void linkedlist::addElementFirst(int d)
{
    node* newNode=new node;        
    newNode->data=d;               
    newNode->next=head;            
    head=newNode;                   
}

//Push back code
void linkedlist::addElementEnd(int x)
{
    node* newNode=new node;    
    node* temp=new node;        
    temp=head;                 
    newNode->data=x;            
    if(temp==NULL)             
    {
        newNode->next=NULL;
        head=newNode;
        return;             
    }

    while(temp->next!=NULL)
    {
        temp=temp->next;      
    }

    newNode->next=NULL;       
    temp->next=newNode;        
}

//head->10->5->8->NULL
//if d=5,key=2
//head->10->5->2->8->NULL
void linkedlist::addElementAfter(int d,int key)    
{
    node* search=new node;   
    search=head;              
    while(search!=NULL)      
    {
        if(search->data==d)  
        {
            node* newNode=new node; 
            newNode->data=key;      
            newNode->next=search->next;   
            search->next=newNode;         
            return;
        }
        search=search->next;
    }

    if(search==NULL)  
        cout<<"The link not inserted because there is not found the node "<<d<<" in the LinkedList. "<<endl;
}

void linkedlist::deleteElement(int d)
{
    node* del;  
    del=head;  
    if(del==NULL)  
    {
        cout<<"Linked list is empty"<<endl;
        return;   
    }
    if(del->data==d)    
    {
        head=del->next; 
        return;
    }
    if(del->next==NULL) 
    {
        cout<<"Is not here, So not deleted."<<endl;
        return;
    }
    //if here more one nodes...one node points to another node ... bigger than 2 nodes .. at least 2 nodes
    while(del->next!=NULL)
    {
        if(del->next->data==d)
        {
            del->next=del->next->next;
            return;
        }

        del=del->next;
     }

     cout<<"Is not here, So not deleted."<<endl;
}

//void linkedlist::display(node *head)
void linkedlist::display()
{
    int n=0;             //counter for number of node
    node* current=head;  //current is pointer points to where head point
    if (current==NULL)
        cout<<"This is empty linked list."<<endl;
    while(current!=NULL) //until current reach to null(last element)
    {
        cout<<"The node data number "<<++n<<" is "<<current->data<<endl;  
        current=current->next;
    }
    cout<<endl;
}

int main()
{
    linkedlist li;      //li is object from linkedlist class
    li.display();       //empty list
    li.addElementFirst(25);  //head->25->NULL
    li.addElementFirst(36);  //head->36->25->NULL
    li.addElementFirst(49);  //head->49->36->25->NULL
    li.addElementFirst(64);  //head->64->49->36->25->NULL
    cout<<"After adding in the first of linked list"<<endl;
    li.display();
        //64
        //49
        //36
        //25
    cout<<endl;
    //head->64->49->36->25->NULL //current linked list from prev addElementFirst method

    cout<<"After adding in the end of linked list"<<endl;
    li.addElementEnd(25);  //head->25->NULL
    li.addElementEnd(36);  //head->25->36->NULL
    li.addElementEnd(49);  //head->25->36->49->NULL
    li.addElementEnd(64);  //head->25->36->49->64->NULL
    li.display();
    //head->64->49->36->25->25->36->49->64->NULL
    cout<<endl;
    //head->64->49->36->25->25->36->49->64->NULL
    cout<<"linked list after adding 10 after node that has data = 49"<<endl;
    li.addElementAfter(49,10);
    li.display();
    //head->64->49->10->36->25->25->36->49->64->NULL
    //head->64->49->10->36->25->25->36->49->64->NULL
    cout<<"linked list after adding deleting 49"<<endl;
    li.deleteElement(49);
    li.display();
    //head->64->10->36->25->25->36->49->64->NULL
    //Notice :delete the first 49 ... not permission for duplicates
return 0;
} 
```

**最后要说的是，感谢您的阅读，我希望这对您有所帮助，请不要犹豫发送您的反馈和建议:)**

# 源代码在 C++ [这里](https://github.com/mohamedkhaledyousef/Crash-courses/blob/master/LinkedLists/Single%20Linked%20Lists/main.cpp)