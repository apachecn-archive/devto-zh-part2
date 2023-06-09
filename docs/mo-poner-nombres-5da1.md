# 如何命名

> [https://dev . to/stripes/CMO-put-names-5 da 1](https://dev.to/franiglesias/cmo-poner-nombres-5da1)的缩写

命名事物被认为是编程中最困难的方面之一的原因是什么？

[在这个 Quora](https://www.quora.com/Why-is-naming-things-hard-in-computer-science-and-how-can-it-can-be-made-easier) 的问题上，提出了为什么很难命名的一些想法。答复类型可分为:

*   我们必须不断地输入名字。
*   设计不断发展变化，引入了新的元素。
*   我们需要名字来代表我们正在努力解决的问题和解决办法，并能够谈论和解释这些问题和解决办法。

内心深处有点矛盾。毕竟，编写一个程序是为了模拟现实，代表已经存在的概念和关系。而这正是我们的一个良好开端。

## 到面包、面包、酒、酒

因为我们所代表的东西都有自己的名字，所以最简单的办法就是用那个名字来指它们。这不是一个坏的开始。

问题是，把事物用人类语言命名很少能顺利地转到一个程序上。人们玩一些编程语言中不存在的优势。

其中一个优势是我们处理多义问题的能力，拥有几个词来指定几个意思。在自然语言中，我们可以用一个词来指不同的概念，这取决于上下文，而在一个容易发生冲突的计算机程序中，我们无法做到这一点。

因此，我们一方面需要借助同义词词典，最重要的是需要与领域专家进行长时间的对话，以开发出共同的语言和独特的术语。您可以建立个别前后关联，其中一个术语在其他前后关联中可能具有不同的意义。这就是领域驱动的设计:语言。

除此之外，让我们看看我们名字的一些实用标准。

### 用法的一致性

名称应在整个应用程序中一致使用。我的意思是，一个地方不可能意味着一件事，而另一个地方可能意味着另一件事。

我们还应该一致地利用我们通过的公约和建议。

最好是开发某种基本词汇，特别是常用动作(如 get、save 等)，以用于创建复合名称或等效动作。此外，如果这些股票有它们的价值，我们应该始终如一地匹配它们(store/retrieve、read/write 等)。

### 经度

应避免使用单一字母名称和缩写。我们的编辑器和 IDE 应用工具应该可以方便地搜索名称，以便正常工作，如果找到更好的名称，也可以对其进行更改，并且只需最少的长度即可。

```
for ($i = 0; $i < $t; $i++) {
    $r = $i * $i + $i - 3;
}

// vs

for ($counter = 0; $counter < $total; $counter++) {
    $result = $counter * $counter + $counter - 3;
} 
```

此外，建议不要使用缩写，除非是 Id，而且可能还有其他一些已经非常确定的缩写。

最大长度？在这方面有一些建议，例如不要超过 20 个字符。或者，由于长名称通常是多个名称的组合，因此最多只能包含四个字。

但是，好的名字必须要有足够长的长度，不要多，也不要少。

理想的做法是，可以用一个词来命名事物，在第一个词后面加上其他词，从而使事物的含义更加准确。

### 不同名称

黄金法则是每件事的一个词，至少在明确界定的范围内是如此。但是，也应避免使用过于相似的名称，以免造成混淆:

```
$student = $students[1];

//vs

$student = $studentsGroup[0]; 
```

当我们需要处理同一概念的两个副本时，我们必须想办法给它们取具有语义价值的名称，以便于区分它们。例如:

```
$address;
$address2;
$otherAddress;

// vs

$currentAddress;
$billingAddress;
$shippingAddress; 
```

### Número

使用单数和复数来指示操作是影响一个元素还是多个元素。

```
$loggedUser = //...;

$allStudents = $studentsRepository->findAll(); 
```

如果可以使用一个集体名称，比复数名称或带有某种技术后缀的名称更好:

```
$users;
$usersCollection;

// vs

$staff; 
```

## 变量(或对象属性)

### 内容和类型

PHP 具有动态性质，因此无法将类型指定给变数。事实上，我们可以用一种类型实例化一个变量，然后通过为其指定另一种类型的值来更改该变量:

```
$variable = 'contenido';
$variable = 5;

echo $variable; //--> 5 
```

我们无法避免这种情况，但用我们描述的名称记录变量的行为似乎是一个好主意:

*   他的意思。
*   保存的内容类型。

这样，虽然我们不能强迫他的类型，但至少我们知道应该是哪一种:

```
$user = $this->usersRepository->getByEmailOrFail($email); 
```

我们在上面的代码中设置的变量非常清楚地告诉我们它们存储的内容类型。

但是，它们在含义上仍然有些模糊，因为它们完全是一般性的。

例如，此行可能用于客户服务系统的代码中，代理可以通过该代码查找通过其电子邮件进行服务的用户。在这种情况下，可能有意义:

```
$requestingCustomer = $this->usersRepository->getByEmailOrFail($customerEmail); 
```

这将是另一种情况:

```
$loggedUser = $this->usersRepository->getByEmailOrFail($email); 
```

请注意，这是完全相同的代码，但在每个情况下都有不同的含义。

### 额外信息

目前，我们已经讨论了变量的内容和类型，但有时可能需要更多的信息。一般来说，如果您觉得需要添加注释来澄清有关变量值的某些内容，则表明变量名称仍有改进的馀地。

例如，假设一个变量包含一个整数以指示文件的大小:

```
$fileSize = $upload->size(); 
```

少了什么吗？这个尺寸应该表示哪个单位？

除了考虑使用 value object 储存大小和单位的值是否最合适之外，还可以重新命名变数以指示要使用的单位:

```
$fileSizeInBytes = $upload->size(); 
```

这个名字消除了我们的疑虑。它甚至会在调试时帮助我们，因为它不仅清楚地表明变量包含给定文件的大小，而且还告诉我们，它应该以字节为单位，如果值使我们怀疑其它任何内容，就会告诉我们存在问题。

关于 Value Object 主题，使用如此详细的名称仍然是一个好主意:

```
class FileSize
{
    / **@var int** /
    private $size;
    / **@var string** /
    private $unit;

    public function __construct(int $size, string $unit)
    {
        $this->size = $size;
        $this->unit = $unit;
    }

    public function inBytes()
    {
        return new self($this->size, 'B');
    }

    public function inKiloBytes()
    {
        return new self($sizeInKiloBytes, 'KB');
    }
} 
```

另一个例子可以说明消除歧义如何帮助我们减少错误。在这种情况下，我们是否要检查文件系统路径:是否要终止目录分隔符？绝对还是相对？如果有疑问，请代我解释:

```
$pathToResources = 'path/to/resources/';
$pathToResources = 'path/to/resources';
$pathToResources = '/path/to/resources/';
$pathToResources = '/path/to/resources';

// vs

$relativePathToResourcesWithTrailingSlash = 'path/to/resources/'; 
```

## 常数

一般来说，我们对变量所说的话可以应用于常量。总之，名称:

*   描述所代表的概念
*   指定数据类型(如果需要)
*   澄清可能不明确或支持多种可能选项的信息

## 梅托多斯

方法名称应该描述两件事:

*   他的目的。
*   其后果。

### 普罗波西托

假设有一个学生资料库:

```
interface StudentsRepository
{
} 
```

通过了解学习 Id(或任何其他数据)获取学习的方法可以是:

```
interface StudentsRepository
{
    public function getById(StudentId $studentId): Student;
} 
```

此方法告诉我们，我们将使用您的 Id 作为选择对象的标准来获取(get)对象`Student`。

另一个名称可以是:

```
interface StudentsRepository
{
    public function retrieveById(StudentId $studentId): Student;
} 
```

在这两种情况下，目的都是明确的。使用其中一个动词可能是品味问题。

多亏了类型体系(*类型提示*和*返回类型*)，我们不需要进一步说明。在语言的早期版本中，写这样的东西是个好主意:

```
interface StudentsRepository
{
    public function retrieveStudentByStudentId($studentId);
} 
```

### 后果

现在让我们来看看后果的处理方法。很明显，我们的方法允许我们从其 Id 中检索一个对象`Student`。也就是说，调用它的后果是得到一个`Student`。但是，如果没有这样的 Id，会发生什么？

```
interface StudentsRepository
{
    public function retrieveById(StudentId $studentId): Student;
} 
```

没有什么能明确地告诉我们，但根据我们的经验，我们知道至少有三件事可能发生:

*   出现异常。
*   返回 null(但 return type 告诉我们这是不可能的)
*   返回 null object Student(这在本例中没有多大意义)

因此，我们可以推断，如果找不到具有该 Id 的学生对象，该方法将抛出异常。

理由如下:如果我们有 Id(或类似的东西)，或者是存储库中某个学生的 Id，或者是格式不正确或错误的 id。通常情况下，我们会尝试在希望有具有此 Id 的“`Student`”的情况下使用此方法，因此如果没有理由认为这是特殊情况。这就是例外。

我们可以用方法本身的名义表达这一点，并避免试图证实我们的怀疑所带来的负担:

```
interface StudentsRepository
{
    public function retrieveByIdOrFail(StudentId $studentId): Student;
} 
```

我们应该具体说明什么？

任何方法都可能引发异常，即使不是明确的。例如，我们的“`StudentsRepository`”可能会出现数据库连接问题，这将导致异常，无论我们是否启动我们的“`StudentNotFoundException`”。

例外情况与另一例外情况的区别在于，我们存储库中的客户代码可以处理这种情况`StudentNotFoundException`，因为它只是意味着没有找到具有该 Id 的用户，这种情况很可能会发生，而且会以某种方式成为业务规则的一部分。

当然，这里可能出现的其他例外情况是不可预见的。这些是我们无法处理或恢复的运行时故障。

我们建议您明确指出，如果我们的方法无法返回预期的结果，则会引发某种异常。在这方面，了解“[checked 和 unckeck exceptions〖t1〗的 Java 概念很有意思。](https://www.geeksforgeeks.org/checked-vs-unchecked-exceptions-in-java/)

另一方面，如果*返回类型*无效，则无需在名称中注明:

```
interface StudentsRepository
{
    public function retrieveById(StudentId $studentId): ?Student;
} 
```

现在，完全签名告诉我们，如果不存在 Student 对象或 null 对象，则该方法可以返回该对象。因此，无需作进一步解释。

是的，这种方法能否返回 null 是非常值得怀疑的。

### 设定者 y 变异者

一般来说，*setters、*和*mutter*方法会改变物体的状态。*扇区*启动一个状态(或其一部分)，而*诱变器*则改变它。把这些意思混为一谈，用 *setters* 来改变物体的状态是比较容易的。

#### 为什么他们想换的时候叫它固定

让我们看一个例子。假设有一节课`Book`。通常情况下，我们创建的对象`Book`有标题，至少有一个作者，还有许多其他数据。为此，单纯的 *setters* 没有多大意义。

也就是说，以下内容不符合:

```
class Book
{
    public function __construct(string $title, string $author)
    {
    }

    public function setTitle(string $title)
    {
    }
} 
```

不匹配，因为书的标题是在创建书时分配的。重新分配是没有意义的。方法似乎告诉我们书可以没有标题地创作。

也许有意义的是标题，因为原来的记录中出现了错误，而且出现了拼写错误，或者甚至可能输入了不正确的标题。无论如何，以下内容更好地表达了所发生的情况:

```
class Book
{
    public function __construct(string $title, string $author)
    {
    }

    public function changeTitle(string $title)
    {
    }
} 
```

我们甚至可以要求您添加一些信息，说明标题更改的原因，这可能会在内部引发事件或将其记录在历史记录中。

```
class Book
{
    public function __construct(string $title, string $author)
    {
    }

    public function changeTitleBecauseReason(string $title, string $reason)
    {
    }
} 
```

#### 为什么要说加的时候叫固定呢

现在让我们来看看提交人的案例。许多书都有多个作者，因此创建 Book 对象时，我们应该传递一个数组或作者集合。当然，在创建对象时，我们可能没有所有作者，因此我们需要在稍后添加更多作者。

在这种情况下，∞这意味着什么？：

```
class Book
{
    public function __construct(string $title, string $author)
    {
    }

    public function setAuthor(string author)
    {
    }
} 
```

因为这意味着方法的名称会误导我们:

*   ∞那本书没有作者，我们现在给你指出来了吗？
*   我们正在改变这本书的作者吗？
*   我们是否正在添加一个新作者？

一切都是关于对行为的完整描述，甚至是非常完整的描述:

```
class Book
{
    public function __construct(string $title, string $author)
    {
    }

    public function addAnotherAuthor(string author)
    {
    }
} 
```

#### 为什么要说别的话就叫固定

一般而言，涉及变更物件状态的任何作业都需要调整物件的一个或多个性质。但这些调整是对业务有一定意义的交易的一部分。

以上述范例为例，假设我们的 Book 类别是包含借用选项的程式库管理系统的一部分。在借书时，这种借贷过程可能涉及下列行动:

*   将可用性状态设置为不可用。
*   添加被借用的用户。
*   计算并记录必须返回帐簿的截止日期。

```
$book->setAvailable(false);
$book->setUser($user);
$book->setReturnDate(strtotime('+ 2 weeks')); 
```

实际上，这一组操作可以封装在一个更有意义的操作中:

```
$book->lendToUser($user);

class Book
{
    public function __construct(string $title, string $author)
    {
    }

    public function lendToUser(User $user)
    {
        $this->available = false;
        $this->user = $user;
        $this->returnDate = strtotime('+ 2 weeks');
    }
} 
```

除了其他问题之外，这是一个如何在实体中定义业务规则的示例:使用无处不在的语言。

## 学习更多

彼得·希尔顿:如何命名事物:编程中最难的问题

彼得·希尔顿:职业程序员命名指南