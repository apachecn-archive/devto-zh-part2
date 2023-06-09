# TDD NIF 验证

> 原文：<https://dev.to/franiglesias/tdd-de-un-validador-de-nif-27jj>

后面有 NIF 验证器，但使用 TDD 编写一个验证器是一个有趣的练习，可以用来开发一些测试策略。

DNI 是一个由八位数字和一个字母组成的标识符，用作控制数字。在某些特殊情况下，第一个数字由字母取代，而字母又由一个数字取代，以用于后续的有效性计算。

验证 NIF 的算法非常简单:取单据编号的一部分，除以 23，得到其馀部分。其馀部分是目录中的索引。索引的字母应为有效的 DNI。因此，如果 DNI 的字母与我们得到的字母一致，那就是它是有效的。

所涉表格如下:

| 餐馆 | 莱特拉 |
| --- | --- |
| Zero | T |
| one | 稀有 |
| Two | W |
| three | A |
| four | G |
| five | M |
| six | Y |
| seven | F |
| eight | P |
| nine | D |
| Ten | X |
| Eleven | B |
| Twelve | 普通 |
| Thirteen | J |
| Fourteen | Z |
| Fifteen | S |
| Sixteen | Q |
| Seventeen | V |
| Eighteen | H |
| Nineteen | L |
| Twenty | C |
| Twenty-one | K |
| Twenty-two | E |

像往常一样，我们从测试开始。

首先我要做一个失败的测试等待一个有效的 NIF。我们可以使用已知的，我们知道它是，或者计算一个简单的，我们知道它必须是有效的。00000000T 就是这种情况。事实上，如果我们将 23 乘以 0 到 22 之间的数字，我们将得到每个字母的有效 nif，但之后我们会处理。

```
class ValidateNifTest extends TestCase
{
    public function testShouldValidateAValidNif()
    {
        $nif = '00000000T';
        $validateNif = new ValidateNif();
        $this->assertTrue($validateNif->do($nif));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要通过这个测试，有三件事需要做:

*   创建 validatenif 类
*   添加 do 方法
*   使其返回 true

```
class ValidateNif
{

    public function do(string $nif): bool
    {
        return true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经通过了第一次测试。

## 测试什么是无效的

测试验证器或任何返回布尔结果的方法都有一些技巧，因为我们只有两个可能的答案。因此，我们通常希望先从一个案例开始，产生我们第一个最小、最灵活的实施，然后开始探索与此相冲突的案例。

这次我们要遵循的战略是，首先关注无效的 nif 案例。现在我们需要一个新的测试失败，希望下一个 NIF 无效。

事实上，有几个 NIF 的例子是无效的，因为它们不符合基本的字符数要求，所以我们可以从让我们的验证者拒绝它们开始。

我们要做的不是测试验证器只支持九个字符的字符串(八个数字和一个字母)，而是测试超过九个字符的“`strings`”永远无效。

下面是失败的测试:

```
public function testTooLongStringsAreNotValid()
    {
        $nif = '000000000T';
        $validateNif = new ValidateNif();
        $this->assertFalse($validateNif->do($nif));
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们把它放在这里:t0]

```
public function do(string $nif): bool
    {
        if (strlen($nif) > 9) {
            return false;
        }
        return true;
    } 
```

Enter fullscreen mode Exit fullscreen mode

关键在于:我们的第一个不灵活的实现总是返回“`true`”，因此，如果我们测试 NIF 的无效示例，我们的测试将始终失败，这为我们提供了信息。

通过添加代码来传递这些测试，我们需要处理这些不应包含在验证算法中的案例。比如那些太短的。

关于这种情况的说明:我们可能已经有了用户输入的数据的前端验证，但这种验证并不多。另一个可能的数据输入源是 CSV 文件或 API 响应，其中我们可能没有第一个防御屏障。

最后，回到主题，这里我们有一个测试来证明我们班处理 nif 太短。

```
public function testTooShortStringsAreNotValid()
    {
        $nif = '0000000T';
        $validateNif = new ValidateNif();
        $this->assertFalse($validateNif->do($nif));
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过以下代码的测试:

```
class ValidateNif
{

    public function do(string $nif) : bool
    {
        if (strlen($nif) > 9) {
            return false;
        }
        if (strlen($nif) < 9) {
            return false;
        }

        return true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## Toca 重构

重复出现了，我们都看到了。在生产代码中，我们已经可以看到代码重复，现在是开始重构的好时机。

其目的是使测试保持绿色，并更改代码以消除此类重复和其他可能的 *smells* 。事实上，我们也有一个神奇的数字，9 号，我们应该对此做些什么。

```
class ValidateNif
{

    const NIF_LENGTH = 9;

    public function do(string $nif) : bool
    {
        if (strlen($nif) !== self::NIF_LENGTH) {
            return false;
        }

        return true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为测试一直在进行，所以我们做得很好。也是减少测试本身重复的好时机:

```
class ValidateNifTest extends TestCase
{
    private $validateNif;

    public function setUp()
    {
        $this->validateNif = new ValidateNif();
    }
    public function testShouldValidateAValidNif()
    {
        $nif = '00000000T';
        $this->assertTrue($this->validateNif->do($nif));
    }

    public function testTooLongStringsAreNotValid()
    {
        $nif = '000000000T';
        $this->assertFalse($this->validateNif->do($nif));
    }

    public function testTooShortStringsAreNotValid()
    {
        $nif = '0000000T';
        $this->assertFalse($this->validateNif->do($nif));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 新的无效案件

没有一个字母或两个以上字母的情况也是无效的。其馀字符为数字。

事实上，如果字符串中的最后一个字符不是字母，则 NIF 无效。

```
public function testLastCharIsNotAlfabeticalIsInvalid()
    {
        $nif = '000000000';
        $this->assertFalse($this->validateNif->do($nif));
    } 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个通过测试的代码:

```
class ValidateNif
{

    const NIF_LENGTH = 9;

    public function do(string $nif) : bool
    {
        if (strlen($nif) !== self::NIF_LENGTH) {
            return false;
        }

        if (!preg_match('/[a-z]$/i', $nif)) {
            return false;
        }

        return true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

事实上，这种控制是有点允许的，因为它忽略了任何字母，有些字母被排除为控制数字(I、ni、o、u)，以避免混淆。

我们可以现在处理，也可以以后再处理，这就是我们要做的。

## 两个按一个的价格

该算法也适用于 NIE(外国人身份证号码)，它不同于 NIF，nif 以字母 x、y、z 开头。

所以我们要做一个测试来确保我们正确拒绝不是以数字或三个字母中的一个开头的文档。

测试铝康托:

```
public function testFirstCharIsNotAllowedAlfaIsInvalid()
    {
        $nif = 'A0000000T';
        $this->assertFalse($this->validateNif->do($nif));
    } 
```

Enter fullscreen mode Exit fullscreen mode

这是密码:t0]

```
class ValidateNif
{

    const NIF_LENGTH = 9;

    public function do(string $nif) : bool
    {
        if (strlen($nif) !== self::NIF_LENGTH) {
            return false;
        }

        if (!preg_match('/[a-z]$/i', $nif)) {
            return false;
        }

        if (!preg_match('/^[\dxyz]/i', $nif)) {
            return false;
        }

        return true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们可以看到，我们在生产守则上有一些重复，尽管解决起来并不像以前那么容易，而且我们看不到一个明显的解决方法(不要太超前)。

情况是，我们正在检查链的末端是否无效，我们还希望确保链的其馀部分也不能无效。

轮到介绍一个新的测试让我们测试一下:

```
public function testNonNumericCharsINTheMiddeleIsInvalid()
    {
        $nif = '00abc000T';
        $this->assertFalse($this->validateNif->do($nif));
    } 
```

Enter fullscreen mode Exit fullscreen mode

随着测试失败，我们开始实施一个解决方案:

```
class ValidateNif
{

    const NIF_LENGTH = 9;

    public function do(string $nif) : bool
    {
        if (strlen($nif) !== self::NIF_LENGTH) {
            return false;
        }

        if (!preg_match('/[a-z]$/i', $nif)) {
            return false;
        }

        if (!preg_match('/^[\dxyz]/i', $nif)) {
            return false;
        }

        if (!preg_match('/^[\dxyz]\d{7,7}/i', $nif)) {
            return false;
        }

        return true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用此代码，测试将通过，我们可以考虑重构以消除我们现在强调得更多的重复项。

使用正则表达式控制其匹配情况的三个条件可以合并为一个条件。

并且在做反应器时，我们保持试验通过:

```
class ValidateNif
{

    const NIF_LENGTH = 9;

    public function do(string $nif) : bool
    {
        if (strlen($nif) !== self::NIF_LENGTH) {
            return false;
        }

        if (!preg_match('/^[\dxyz]\d{7,7}[a-z]$/i', $nif)) {
            return false;
        }

        return true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这已经很好了，但可能会更好。如果你还没意识到，我们的 *regexp* 重复控制字符串长度的逻辑。因为我们有测试，所以不怕测试这种变化:

```
class ValidateNif
{
    public function do(string $nif) : bool
    {
        if (!preg_match('/^[\dxyz]\d{7,7}[a-z]$/i', $nif)) {
            return false;
        }

        return true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

更明确地说:

```
class ValidateNif
{

    private const NIF_FORMAT = '/^[\dxyz]\d{7,7}[a-z]$/i';

    public function do(string $nif) : bool
    {
        if (!preg_match(self::NIF_FORMAT, $nif)) {
            return false;
        }

        return true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以排除几乎所有训练有素的 NIF 病例。现在该由我们自己来实现算法了。

我们的第一个有效 NIF 案例已通过第一次测试，因此我们必须继续使用无效但格式正确的 NIF 进行测试，从而迫使我们为算法编写代码。

例如 NIF 将会失败，因为它应该有字母 r 而不是 t。

```
public function testBadControlDigitShoulfBeInvalid()
    {
        $nif = '00000001T';
        $this->assertFalse($this->validateNif->do($nif));
    } 
```

Enter fullscreen mode Exit fullscreen mode

其无效的原因是在计算馀数除以 23 时，会给出 1。

情况是，既然我们已经知道了算法，我们就开始实施了，虽然这样，从现在起:

```
class ValidateNif
{

    private const NIF_FORMAT = '/^[\dxyz]\d{7,7}[a-z]$/i';

    public function do(string $nif) : bool
    {
        if (!preg_match(self::NIF_FORMAT, $nif)) {
            return false;
        }

        $number = (int) substr($nif, 0, 8);
        $modulus = $number % 23;
        $control = strtolower(substr($nif, -1));

        if ($modulus === 0 && $control === 't') {
            return true;
        }

        return false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

目前，这是一个有点麻烦的实施，但我们有两件事:

*   我们引入了算法，实现不再完全僵化。
*   现在，该算法将对有效的 NIF 失败，因此我们可以去测试迫使我们实现参考表的有效案例。

所以我们可以介绍一个`dataProvider`，上面有一个有效的病例清单，例如:

| 卡索斯 |
| --- |
| 0000000 吨 |
| 00000001R |
| 00000002W |
| 00000003A |
| 00000004G |
| 00000005 米 |
| 00000006Y |
| 0x0000007f |
| 00000008P |
| 00000009D |
| 00000010X |
| 0000011B |
| 00000012N |
| 00000013J |
| 00000014Z |
| 00000015S |
| 00000016Q |
| 00000017V |
| 00000018H |
| 00000019L |
| 0000020C |
| 00000021K |
| 00000022E |

这里有一个测试:

```
 /** @dataProvider validNIFS */
    public function testShouldBeValidNifs($nif)
    {
        $this->assertTrue($this->validateNif->do($nif));
    }

    public function validNIFS()
    {
        return [
            'T' => ['00000000T'],
            'R' => ['00000001R'],
            'W' => ['00000002W'],
            'A' => ['00000003A'],
            'G' => ['00000004G'],
            'M' => ['00000005M'],
            'Y' => ['00000006Y'],
            'F' => ['00000007F'],
            'P' => ['00000008P'],
            'D' => ['00000009D'],
            'X' => ['00000010X'],
            'B' => ['00000011B'],
            'N' => ['00000012N'],
            'J' => ['00000013J'],
            'Z' => ['00000014Z'],
            'S' => ['00000015S'],
            'Q' => ['00000016Q'],
            'V' => ['00000017V'],
            'H' => ['00000018H'],
            'L' => ['00000019L'],
            'C' => ['00000020C'],
            'K' => ['00000021K'],
            'E' => ['00000022E']
        ];
    } 
```

Enter fullscreen mode Exit fullscreen mode

希望除了第一个我们已经重复过的病例之外，所有病例都会下降，所以我们现在不需要它就可以删除它。

一会儿工作后，我们会有一个大致如下的实现:

```
class ValidateNif
{

    private const NIF_FORMAT = '/^[\dxyz]\d{7,7}[a-z]$/i';

    private const CONTROL_DIGITS = 'trwagmyfpdxbnjzsqvhlcke';

    public function do(string $nif) : bool
    {
        $nif = strtolower($nif);

        if (!preg_match(self::NIF_FORMAT, $nif)) {
            return false;
        }

        $modulus = ((int) substr($nif, 0, 8)) % 23;

        if (substr($nif, -1) === self::CONTROL_DIGITS[$modulus]) {
            return true;
        }

        return false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

此代码通过所有测试。这是很好的，特别是在可读性方面。

此外，我们还需要处理其他几个案件，例如采用以下格式的 NIE 号码实际上，算法变化不大，只需将字母 x、y、z 分别替换为数字 0、1、2 即可。

让我们做一个测试，证明我们可以给出有效的否定正确:

```
 /** @dataProvider validNIES */
    public function testShouldBeValidNies($nif)
    {
        $this->assertTrue($this->validateNif->do($nif));
    }

    public function validNIES()
    {
        return [
            'XT' => ['X0000000T'],
            'YZ' => ['Y0000000Z'],
            'ZM' => ['Z0000000M']
        ];
    } 
```

Enter fullscreen mode Exit fullscreen mode

本次测试通过的代码原则上如下:

```
class ValidateNif
{

    private const NIF_FORMAT = '/^[\dxyz]\d{7,7}[a-z]$/i';

    private const CONTROL_DIGITS = 'trwagmyfpdxbnjzsqvhlcke';

    public function do(string $nif) : bool
    {
        $nif = strtolower($nif);

        if (!preg_match(self::NIF_FORMAT, $nif)) {
            return false;
        }

        $numberPart = substr($nif, 0, 8);
        $numberPart = str_replace(['x', 'y', 'z'], [0, 1, 2], $numberPart);
        $modulus = ((int) $numberPart) % 23;

        if (substr($nif, -1) === self::CONTROL_DIGITS[$modulus]) {
            return true;
        }

        return false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为太丑了，所以我们会试着把它修好，因为我们有完整的测试复盖范围。我们将为此提取一些方法:

```
class ValidateNif
{

    private const NIF_FORMAT = '/^[\dxyz]\d{7,7}[a-z]$/i';
    private const CONTROL_DIGITS = 'trwagmyfpdxbnjzsqvhlcke';
    private const MAGIC_DIVISOR = 23;
    private const NIE_STARTING_LETTERS = ['x', 'y', 'z'];
    private const NIE_STARTING_LETTERS_REPLACEMENTS = [0, 1, 2];

    public function do(string $nif) : bool
    {
        $nif = strtolower($nif);

        if (!$this->nifHasTheRightFormat($nif)) {
            return false;
        }

        if ($this->controlDigitIsCorrect($nif)) {
            return true;
        }

        return false;
    }

    private function nifHasTheRightFormat(string $nif)
    {
        return preg_match(self::NIF_FORMAT, $nif);
    }

    private function controlDigitIsCorrect(string $nif) : bool
    {
        $modulus = $this->calculateModulusOfNumberPart($nif);

        return substr($nif, -1) === self::CONTROL_DIGITS[$modulus];
    }

    private function calculateModulusOfNumberPart(string $nif) : int
    {
        $numberPart = str_replace(
            self::NIE_STARTING_LETTERS,
            self::NIE_STARTING_LETTERS_REPLACEMENTS,
            substr($nif, 0, 8)
        );
        $modulus = ((int) $numberPart) % self::MAGIC_DIVISOR;

        return $modulus;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于无效的 NIF 字母，我们还有待处理。你认为会发生吗？

```
/** @dataProvider invalidNIFS */
    public function testInvalidLetters($nif)
    {
        $this->assertFalse($this->validateNif->do($nif));
    }

    public function invalidNIFS()
    {
        return [
            'I' => ['00000000I'],
            'O' => ['00000000O'],
            'U' => ['00000000U'],
            'Ñ' => ['00000000Ñ']
        ];
    } 
```

Enter fullscreen mode Exit fullscreen mode

因为我们的实施已经解决了这个案子。

## 总结

我们可以使实施更加紧凑，但也相当难以阅读。但是，练习的有趣之处在于练习了如何从返回布尔值的方法中制作 TDD。

窍门是从等待两个值之一的测试开始，然后测试导致相反值的所有情况。