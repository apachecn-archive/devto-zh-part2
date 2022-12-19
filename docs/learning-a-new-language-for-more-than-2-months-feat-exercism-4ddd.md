# 学习一门新语言超过 2 个月(专长。练习)

> 原文：<https://dev.to/jeffrey04/learning-a-new-language-for-more-than-2-months-feat-exercism-4ddd>

通常我会花大约一周的时间学习一门新语言，这样我就可以开始用它做一些真正的 T2 工作。毕竟，编程语言(至少是高级和动态语言)只是赋值、计算、分支、循环和重用(在某些情况下，还有并发性/并行性，但不会深入定义它们的区别)。嗯，那是真的，直到我开始学习 Rust，部分是为了我自己的休闲。

写一个完整的 Rust 代码我还是不放心。

虽然我真的很喜欢这门语言。

所以在我目前的工作中，我的一些同事主要写 Golang 代码。我尝试学习了一段时间，但考虑到我来自 Python，我非常讨厌它(而且我还不够喜欢 Python，主要是由于 lambda 的限制)。是的，它完成了工作，但是它只是感觉非常...原始的，过度简化的。作为一个主要功能性的人，如何表达一个想法的限制令人沮丧(好像静态类型化的部分还不够糟糕)。

这不是一个客观地告诉为什么 Golang 很烂的帖子，这只是纯粹不是我会编码的东西。我最终继续用 Python 写代码，因为这对我的工作更有意义。

这些年来，研究小组断断续续地讨论铁锈问题。今年早些时候，我终于更认真地审视了它。然而，直到 6 月份左右，我才开始投入时间和精力去浏览[文档](https://doc.rust-lang.org/book/2018-edition/foreword.html)。同时，只是为了练习，我找到了[练习](https://exercism.io)，发现它足够好，可以作为一个 [4Clojure](http://www.4clojure.com/) 等同于其他语言。

在我开始努力解决这些难题后不久，exercism 经历了一次重新设计。

回来后，我仍然不太欣赏导师制度(尽管我签了名)。

学习 Rust 既有趣又令人沮丧。作为一名高级的动态类型语言程序员，我在技术上并不真正需要它。因此，安全、快速不是我觉得有吸引力的东西。高级语言并不以速度快著称，尽管它们通常是安全的。

所以我继续解决更多的编程难题/招式/练习，并开始碰壁。我喜欢重新设计后的练习的一点是核心练习和可选练习的分离。核心练习通常是为了让学习者学习概念，一次一个，这是数学和编程问题的一个很好的结合(在这里学习一门新语言时，不要费心去解决复杂的数学问题)。

接下来我会列举一些值得注意的习题并做笔记。

## 毕达哥拉斯三联体

我不是数学家，所以对我来说[问题](https://stackoverflow.com/questions/2817848/find-pythagorean-triplet-for-which-a-b-c-1000)是循环，循环，更多的循环。求两个平方数之和等于另一个平方数，这三个数之和为 1000。所以在我早期的迭代中，我提交了这个

```
pub fn find() -> Option<u32> {
    let mut result: u32 = 0;

    for a in 0u32.. {
        if a < 998 {
            for b in (a + 1u32).. {
                if a + b < 999 {
                    for c in (b + 1u32).. {
                        if a.pow(2) + b.pow(2) == c.pow(2) && a + b + c == 1000 {
                            result = a * b * c;
                            break;
                        } else if a + b + c > 1000 {
                            break;
                        }
                    }
                } else {
                    break;
                }
            }
        } else {
            break;
        }
    }

    Some(result)
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，看起来很难看。然后它变成了下面的版本，因为我当时的导师告诉我，它可以在没有所有过度分支的情况下完成。(我显然不太在乎效率)

```
pub fn find() -> Option<u32> {
    let mut result: Option<u32> = None;

    for a in 0u32..998 {
        for b in (a + 1u32)..(999 - a) {
            for c in (b + 1u32)..(1001 - a - b) {
                if a.pow(2) + b.pow(2) == c.pow(2) && a + b + c == 1000 {
                    result = Some(a * b * c);
                    break;
                }
            }
        }
    }

    result
} 
```

Enter fullscreen mode Exit fullscreen mode

干净得多，但被告知我不需要循环第三个数字，因为我已经知道它是什么，如果我有前两个。

```
pub fn find() -> Option<u32> {
    let mut result: Option<u32> = None;

    for a in 0u32..998 {
        for b in (a + 1u32)..(999 - a) {
            let c = 1000 - a - b;

            if a.pow(2) + b.pow(2) == c.pow(2) {
                result = Some(a * b * c);
                break;
            }
        }
    }

    result
} 
```

Enter fullscreen mode Exit fullscreen mode

够好了，不是最有效的解决方案，但已经获得了前进的绿灯。我个人仍然不喜欢它，因为它看起来不够实用，几个月后我再次尝试。

```
pub fn find() -> Option<u32> {
    (1u32..998)
        .filter_map(|a| {
            (a + 1..999 - a)
                .filter_map(|b| {
                    let c = 1000 - a - b;

                    match a.pow(2) + b.pow(2) == c.pow(2) {
                        true => Some(a * b * c),
                        false => None,
                    }
                })
                .next()
        })
        .next()
} 
```

Enter fullscreen mode Exit fullscreen mode

稍微干净一点，虽然可能不容易解释(取决于你问谁)，但肯定可以做得更好。然而，我非常感谢我的导师的帮助。

## 鞍点

简而言之，我们的目标是找到一个比它的所有同室成员都大，但在所有同室成员中最小的条目。像往常一样，我的第一次尝试很笨拙，不知何故开始了与我的导师关于代码可读性的讨论。

```
pub fn find_saddle_points(input: &[Vec<u64>]) -> Vec<(usize, usize)> {
    input
        .iter()
        .enumerate()
        .map(|(row_idx, row)| match find_max(row) {
            Some(max) => row.iter()
                .enumerate()
                .filter(|(_idx, &value)| value == max)
                .map(|(col_idx, &value)| {
                    let col = input.iter().map(|crow| crow[col_idx]).collect::<Vec<u64>>();
                    let min = find_min(&col);

                    if value == min {
                        Some((row_idx, col_idx))
                    } else {
                        None
                    }
                })
                .filter(|x| x.is_some())
                .map(|x| x.unwrap())
                .collect::<Vec<(usize, usize)>>(),
            _ => vec![],
        })
        .fold(vec![], |mut result: Vec<(usize, usize)>, incoming| {
            result.append(&mut incoming.clone());
            result
        })
}

fn find_min(input: &Vec<u64>) -> u64 {
    input.iter().fold(
        input[0],
        |result, &incoming| {
            if result <= incoming {
                result
            } else {
                incoming
            }
        },
    )
}

fn find_max(input: &Vec<u64>) -> Option<u64> {
    match input.len() {
        0 => None,
        _ => input.iter().fold(Some(input[0]), |result, &incoming| {
            if result.unwrap() >= incoming {
                result
            } else {
                Some(incoming)
            }
        }),
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

克隆不是最有效的解决方案，可能是不必要的，仍然不熟悉借用检查器，但代码以某种方式工作。然后我被告知我在文档中完全忽略的 flat_map。经过一些迭代后，代码变成了

```
use std::cmp::{max, min};
use std::u64::MAX;

pub fn find_saddle_points(input: &[Vec<u64>]) -> Vec<(usize, usize)> {
    input
        .iter()
        .enumerate()
        .flat_map(|(row_idx, row)| match find_max(&row) {
            Some(max) => row.iter()
                .enumerate()
                .filter(|(_idx, &value)| value == max)
                .flat_map(|(col_idx, &value)| {
                    match find_min(&input.iter().map(|crow| crow[col_idx]).collect::<Vec<u64>>()) {
                        min if value == min => vec![(row_idx, col_idx)],
                        _ => vec![],
                    }
                })
                .collect::<Vec<(usize, usize)>>(),
            _ => vec![],
        })
        .collect::<Vec<(usize, usize)>>()
}

fn find_min(input: &Vec<u64>) -> u64 {
    input.iter().cloned().fold(MAX, min)
}

fn find_max(input: &Vec<u64>) -> Option<u64> {
    match input.len() {
        0 => None,
        _ => Some(input.iter().cloned().fold(0, max)),
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不确定如果我今天重做(就像我对之前练习所做的那样)，看起来是否会有所不同。然而，在整个同行评审过程中，我学到了很多。

然后练习变得更难。

## 鲁恩

然后有这个校验和验证器。从右边开始，将奇数索引中的每个数字加倍，如果超过 9，则减去 9，然后将得到的数字以及偶数索引的数字相加。总和的结果必须能被 10 整除。

```
pub fn is_valid(code: &str) -> bool {
    let _code = code.chars().filter(|x| !x.is_whitespace());

    match _code.clone().count() {
        _invalid if _invalid <= 1 => false,
        _ => {
            let result = _code
                .map(|x| x.to_digit(10))
                .rev()
                .enumerate()
                .map(|(idx, x)| match x {
                    Some(n) => Some(match (idx % 2 == 1, n * 2) {
                        (true, result) if result > 9 => result - 9,
                        (true, result) => result,
                        _ => n,
                    }),
                    None => None,
                })
                .fold(Some(0), |current, incoming| {
                    current.and_then(|x| match incoming {
                        Some(n) => Some(x + n),
                        None => None,
                    })
                });

            match result {
                Some(n) if n % 10 == 0 => true,
                _ => false,
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是较早的提交之一，不知何故，我仍然在这里的所有权斗争。然后我开始对选项 enum 的[功能感兴趣。然后我有了这个](https://cslai.coolsilon.com/2018/07/24/reinventing-wheels-with-cheats-using-rustlang/) 

```
pub fn is_valid(code: &str) -> bool {
    let _code = code
        .chars()
        .filter(|x| !x.is_whitespace())
        .collect::<Vec<char>>();

    match _code.len() {
        _invalid if _invalid <= 1 => false,
        _ => _code
            .iter()
            .map(|x| x.to_digit(10))
            .rev()
            .enumerate()
            .map(|(idx, x)| {
                x.and_then(move |n| {
                    Some(match (idx % 2 == 1, n * 2) {
                        (true, result) if result > 9 => result - 9,
                        (true, result) => result,
                        _ => n,
                    })
                })
            }).fold(Some(0), |current, incoming| {
                current.and_then(|x| match incoming {
                    Some(n) => Some(x + n),
                    None => None,
                })
            }).map_or(false, |n| n % 10 == 0),
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 阿特巴什密码

我喜欢打破东西，尽管我讨厌事情没有按预期进行时的挫败感。这一次我试图将一个闭包作为参数发送到一个函数中。当然，这不一定是最好的解决方案，但是我花了相当长的时间才达到这种状态，因为我和借货检查器发生了冲突。

```
pub fn encode(plain: &str) -> String {
    convert(
        plain,
        Some(&|idx, x| format!("{}{}", x, if (idx + 1) % 5 == 0 { " " } else { "" })),
    )
}

/// "Decipher" with the Atbash cipher.
pub fn decode(cipher: &str) -> String {
    convert(cipher, None::<&Fn(usize, char) -> String>)
}

fn transform(x: char) -> char {
    match x {
        x if x.is_alphabetic() => (219u8 - x as u8) as char,
        x => x,
    }
}

fn preprocess(text: &str) -> Vec<char> {
    text.to_ascii_lowercase()
        .chars()
        .filter(|x| x.is_alphanumeric() && x.is_ascii())
        .collect()
}

fn convert(text: &str, decorator: Option<&Fn(usize, char) -> String>) -> String {
    (match decorator {
        Some(_decorator) => preprocess(text)
            .into_iter()
            .enumerate()
            .map(|(idx, x)| _decorator(idx, transform(x)))
            .collect::<String>(),
        None => preprocess(text)
            .into_iter()
            .map(|x| transform(x))
            .collect::<String>(),
    }).trim()
        .to_string()
} 
```

Enter fullscreen mode Exit fullscreen mode

有时有一个有经验的人作为指导是一件好事，他立即指出一些可以使代码更简单的部分，产生了这个

```
/// "Encipher" with the Atbash cipher.
pub fn encode(plain: &str) -> String {
    convert(
        plain,
        Some(&|idx, x| format!("{}{}", if idx != 0 && idx % 5 == 0 { " " } else { "" }, x)),
    )
}

/// "Decipher" with the Atbash cipher.
pub fn decode(cipher: &str) -> String {
    convert(cipher, None::<&Fn(usize, char) -> String>)
}

fn transform(x: char) -> char {
    match x {
        x if x.is_alphabetic() => (219u8 - x as u8) as char,
        x => x,
    }
}

fn convert(text: &str, decorator: Option<&Fn(usize, char) -> String>) -> String {
    let preprocess = || {
        text.chars()
            .flat_map(char::to_lowercase)
            .filter(|x| x.is_alphanumeric() && x.is_ascii())
    };

    (match decorator {
        Some(_decorator) => preprocess()
            .enumerate()
            .map(|(idx, x)| _decorator(idx, transform(x)))
            .collect::<String>(),
        None => preprocess().map(|x| transform(x)).collect::<String>(),
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

与上面显示的早期迭代相比，这个想法或多或少是相同的，只是稍微干净一些。

## 支架推动

简而言之，这个练习是为了检查支架是否平衡。这里没有什么太大的问题，但是我的导师告诉我，Rust 生成的代码处理递归已经足够好了。似乎有一些 TCO 到位，虽然我真的不在乎。我通常只在必要的时候(在我打开堆栈之后)将递归代码交换回普通循环。

```
pub struct Brackets(Vec<char>);

impl<'a> From<&'a str> for Brackets {
    fn from(input: &str) -> Self {
        Brackets(
            input
                .chars()
                .filter(|x| vec!['{', '}', '[', ']', '(', ')'].contains(x))
                .collect::<Vec<char>>(),
        )
    }
}

impl Brackets {
    pub fn are_balanced(&self) -> bool {
        Self::balance_test(true, &[], &self.0[..])
    }

    fn balance_test(result: bool, stack: &[char], brackets: &[char]) -> bool {
        match (stack.first(), brackets.first()) {
            (Some(o), Some(c)) if [('{', '}'), ('[', ']'), ('(', ')')].contains(&(*o, *c)) => {
                Self::balance_test(result, &stack[1..], &brackets[1..])
            }
            (_, Some(n)) if ['}', ')', ']'].contains(n) => false,
            (_, Some(n)) => Self::balance_test(
                result,
                &vec![vec![*n], stack.to_vec()].concat().as_slice(),
                &brackets[1..],
            ),
            (_, None) => result && stack.len() == 0,
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，这个练习值得注意的部分是我被告知关于 [rpds 板条箱](https://crates.io/crates/rpds)。我想这对于有功能的人来说是一个很棒的机箱，因为它并不真的原地突变，这让我很头疼。知道锈菌以安全的方式使物质变异是一回事，但想要以不同的方式完全消除变异是另一回事。

不幸的是，板条箱的使用似乎会稍微影响性能。

## 子列表

再一次，在开始的时候是一个相当笨拙的提交。

```
#[derive(Debug, PartialEq)]
pub enum Comparison {
    Equal,
    Unequal,
    Sublist,
    Superlist,
}

pub fn sublist<T: PartialEq>(alpha: &[T], beta: &[T]) -> Comparison {
    match (alpha.len(), beta.len()) {
        (a, b) if a < b => is_sublist(&alpha, &beta),
        (a, b) if a > b => is_superlist(&alpha, &beta),
        _ => is_equal(&alpha, &beta),
    }
}

fn is_sublist<T: PartialEq>(alpha: &[T], beta: &[T]) -> Comparison {
    if alpha.len() == 0 {
        return Comparison::Sublist;
    }

    let _result = (0..(1 + beta.len() - alpha.len()))
        .any(|idx_start| alpha == &beta[idx_start..(idx_start + alpha.len())]);

    match _result {
        true => Comparison::Sublist,
        false => Comparison::Unequal,
    }
}

fn is_superlist<T: PartialEq>(alpha: &[T], beta: &[T]) -> Comparison {
    match is_sublist(&beta, &alpha) {
        Comparison::Sublist => Comparison::Superlist,
        _result => _result,
    }
}

fn is_equal<T: PartialEq>(alpha: &[T], beta: &[T]) -> Comparison {
    match alpha == beta {
        true => Comparison::Equal,
        false => Comparison::Unequal,
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

问题之一是我到处滥用匹配语句，它实际上应该写成 if 语句。通过回顾对话，我还被告知窗口方法，因此我可以依靠它而不是自己计算子列表索引。

```
#[derive(Debug, PartialEq)]
pub enum Comparison {
    Equal,
    Unequal,
    Sublist,
    Superlist,
}

pub fn sublist<T: PartialEq>(alpha: &[T], beta: &[T]) -> Comparison {
    if is_sublist(&alpha, &beta) {
        Comparison::Sublist
    } else if is_sublist(&beta, &alpha) {
        Comparison::Superlist
    } else if alpha == beta {
        Comparison::Equal
    } else {
        Comparison::Unequal
    }
}

fn is_sublist<T: PartialEq>(alpha: &[T], beta: &[T]) -> bool {
    alpha.len() < beta.len()
        && (alpha.len() == 0 || beta.windows(alpha.len()).any(|window| window == alpha))
} 
```

Enter fullscreen mode Exit fullscreen mode

Rust 中似乎有很多生活质量类的方法调用(这使得它成为一种有趣的学习语言),而我总是忽略它们。虽然文档化需要我花一些时间来适应，但是像 window、flat_map 这样的方法不是我会在文档中寻找的东西(这就是为什么我通常最终会编写自己的版本)。

在这个阶段，我开始习惯于借用检查器，并且有时会通过读取错误获得一些关于如何修复错误的直觉。这里“直觉”一词的使用是有意选择的，因为每当出现错误时，它有时会成为一种反射动作，而不去想是什么导致了错误。

## 扑克

这是一个比较难的练习，我不认为我的解决方案是好的。我最初的计划是用一个大的数字来表示分数，但是这个数字变得太大了，所以我用一个向量来表示分数。

```
/// Given a list of poker hands, return a list of those hands which win.
///
/// Note the type signature: this function should return _the same_ reference to
/// the winning hand(s) as were passed in, not reconstructed strings which happen to be equal.
use std::cmp::Ordering;

#[derive(Debug, Eq)]
struct Hand {
    spade: Vec<usize>,
    heart: Vec<usize>,
    club: Vec<usize>,
    diamond: Vec<usize>,
}

impl Hand {
    fn new(hand: &str) -> Hand {
        hand.split(" ").fold(
            Hand {
                spade: vec![0; 14],
                heart: vec![0; 14],
                club: vec![0; 14],
                diamond: vec![0; 14],
            },
            |current, incoming| {
                let index = Self::get_index(
                    &incoming
                        .chars()
                        .take(incoming.len() - 1)
                        .collect::<String>(),
                );

                match incoming.chars().last().unwrap() {
                    'S' => Hand {
                        spade: Self::suit_increment(&current.spade, index),
                        heart: current.heart,
                        club: current.club,
                        diamond: current.diamond,
                    },
                    'H' => Hand {
                        heart: Self::suit_increment(&current.heart, index),
                        spade: current.spade,
                        club: current.club,
                        diamond: current.diamond,
                    },
                    'C' => Hand {
                        club: Self::suit_increment(&current.club, index),
                        spade: current.spade,
                        heart: current.heart,
                        diamond: current.diamond,
                    },
                    _ => Hand {
                        diamond: Self::suit_increment(&current.diamond, index),
                        spade: current.spade,
                        heart: current.heart,
                        club: current.club,
                    },
                }
            },
        )
    }

    fn suit_increment(original: &Vec<usize>, index: usize) -> Vec<usize> {
        original
            .clone()
            .iter()
            .enumerate()
            .map(|(idx, &x)| {
                if (index == 0 && idx == 13) || index == idx {
                    x + 1
                } else {
                    x
                }
            })
            .collect::<Vec<usize>>()
    }

    fn flatten(&self) -> Vec<usize> {
        vec![0; 14]
            .iter()
            .enumerate()
            .map(|(idx, _)| {
                &self.spade[idx] + &self.heart[idx] + &self.club[idx] + &self.diamond[idx]
            })
            .collect()
    }

    //
    // Score vector
    // [type] [# of threes/fours] [# of pair/largest] [#High A] [#B] .. [#2] [#Low A]
    // only exception is straight flush A1234, then set [#High A] to 0
    //
    fn score(&self) -> Vec<usize> {
        let flattened = self.flatten();

        (0..17)
            .into_iter()
            .map(|x| {
                if x == 0 {
                    self.type_bit()
                } else if x == 1 {
                    if self.has_threes() && self.has_pairs(1) {
                        flattened.iter().rposition(|&y| y == 3).unwrap()
                    } else if self.is_four_of_a_kind() {
                        flattened.iter().rposition(|&y| y == 4).unwrap()
                    } else {
                        0
                    }
                } else if x == 2 {
                    if self.has_threes() && self.has_pairs(1) {
                        flattened.iter().rposition(|&y| y == 2).unwrap()
                    } else if self.is_four_of_a_kind() {
                        flattened.iter().rposition(|&y| y == 1).unwrap()
                    } else {
                        0
                    }
                } else if x == 3 && flattened.iter().take(5).all(|&x| x > 0) {
                    0
                } else {
                    flattened[flattened.len() + 2 - x]
                }
            })
            .collect()
    }

    fn type_bit(&self) -> usize {
        if self.is_flush() && self.is_straight() {
            8
        } else if self.is_four_of_a_kind() {
            7
        } else if self.has_threes() && self.has_pairs(1) {
            6
        } else if self.is_flush() {
            5
        } else if self.is_straight() {
            4
        } else if self.has_threes() {
            3
        } else if self.has_pairs(2) {
            2
        } else if self.has_pairs(1) {
            1
        } else {
            0
        }
    }

    fn has_threes(&self) -> bool {
        self.has_repeat(3) > 0
    }

    fn has_pairs(&self, count: usize) -> bool {
        self.has_repeat(2) == count
    }

    fn has_repeat(&self, repeat: usize) -> usize {
        self.flatten()
            .iter()
            .take(13)
            .filter(|&x| *x == repeat)
            .count()
    }

    fn is_straight(&self) -> bool {
        let flattened = self.flatten();

        (0..flattened.len() - 4).any(|x| flattened.iter().skip(x).take(5).all(|&x| x > 0))
    }

    fn is_flush(&self) -> bool {
        [&self.spade, &self.heart, &self.club, &self.diamond]
            .iter()
            .any(|&x| x.into_iter().sum::<usize>() == 5)
    }

    fn is_four_of_a_kind(&self) -> bool {
        self.has_repeat(4) > 0
    }

    fn get_index(value: &str) -> usize {
        let cards = [
            "A", "2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K",
        ];

        cards.iter().position(|&x| x == value).unwrap()
    }
}

impl PartialEq for Hand {
    fn eq(&self, other: &Hand) -> bool {
        self.score() == other.score()
    }
}

impl PartialOrd for Hand {
    fn partial_cmp(&self, other: &Hand) -> Option<Ordering> {
        self.score().partial_cmp(&other.score())
    }
}

impl Ord for Hand {
    fn cmp(&self, other: &Hand) -> Ordering {
        self.score().as_slice().cmp(other.score().as_slice())
    }
}

pub fn winning_hands<'a>(hands: &[&'a str]) -> Option<Vec<&'a str>> {
    let shands = hands.iter().map(|x| Hand::new(x)).collect::<Vec<Hand>>();

    if hands.len() == 1 {
        Some(hands.to_vec())
    } else {
        let scores = shands
            .iter()
            .map(|x| x.score())
            .collect::<Vec<Vec<usize>>>();
        let max = &scores.iter().max().unwrap();

        Some(
            scores
                .iter()
                .enumerate()
                .filter(|(_, x)| x == max)
                .map(|(idx, _)| hands[idx])
                .collect::<Vec<&str>>(),
        )
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如计分法所示，我使用一个 17 维的向量来表示一手牌的分数。在某种程度上，它有点像一个数字，只是在比较的时候不那么有效。我不太喜欢这个难题的复杂性(对于学习语言来说太复杂了)，所以我没有花太多时间来完善解决方案。

## 变位词

在很多方面，我认为这个问题和索引文档一样。只是在这种情况下，我们是索引单词。在很多文档索引算法中，单词都被假设为 iid，每个单词都有些独立。因此，两个措辞相似但顺序不同的文档最终可能会被相似地索引。

所以在这个解决方案中，我将单词分解成一个 unicode 字符的向量(以字符串形式，因为 Rust 不做神奇的 unicode 处理)。就像两个单词顺序不同的文档一样，变位词只是两个单词的相同字符顺序不同。这可能不是解决习题最高效的算法，但对我来说是最直观的解决方法。

```
use std::collections::HashSet;

pub fn anagrams_for<'a>(word: &str, possible_anagrams: &[&'a str]) -> HashSet<&'a str> {
    let words = word_vec(&word, &possible_anagrams);
    let dictionary = char_dictionary(&words);

    let encoded = words
        .iter()
        .map(|&x| word_encode(&break_string(&x), &dictionary))
        .collect::<Vec<Vec<usize>>>();

    (1..words.len())
        .filter(|&x| encoded[x as usize] == encoded[0])
        .map(|x| possible_anagrams[x - 1])
        .fold(HashSet::new(), |mut current, incoming| {
            current.insert(&incoming);
            current
        })
}

fn word_encode(broken_word: &Vec<String>, dictionary: &Vec<String>) -> Vec<usize> {
    dictionary
        .iter()
        .map(|x| {
            broken_word
                .iter()
                .filter(|y| y.to_lowercase() == *x)
                .count()
        }).collect()
}

fn word_vec<'a>(word: &'a str, possible_anagrams: &[&'a str]) -> Vec<&'a str> {
    possible_anagrams
        .into_iter()
        .filter(|x| x.to_lowercase() != word.to_lowercase())
        .fold(vec![&word], |mut current, incoming| {
            current.push(incoming);
            current
        })
}

fn char_dictionary(words: &Vec<&str>) -> Vec<String> {
    words
        .into_iter()
        .flat_map(|&x| break_string(x))
        .fold(Vec::new(), |mut current, incoming| {
            if !current.contains(&incoming.to_lowercase()) {
                current.push(incoming.to_lowercase());
            }

            current
        })
}

fn break_string(word: &str) -> Vec<String> {
    let idx: Vec<(usize, char)> = format!("{}\0", word).char_indices().collect();
    let stack: Vec<(usize, char)> = Vec::new();
    let result: Vec<String> = Vec::new();

    word.char_indices()
        .zip(idx[1..idx.len()].iter())
        .enumerate()
        .fold(
            (stack, result),
            |(mut stack, mut result), (j, (start, end))| {
                if end.0 - start.0 == 1 {
                    // we don't know yet, keep it in the stack
                    if let Some(last) = stack.pop() {
                        // pop the previous
                        result.push(last.1.to_string());
                    }

                    if j == word.char_indices().count() - 1 {
                        result.push(start.1.to_string());
                    } else {
                        stack.push(start);
                    }
                } else {
                    // so this is a fat char
                    match stack.pop() {
                        Some(last) => {
                            // merge it with the stack
                            result.push(format!("{}{}", last.1, start.1));
                        }
                        None => {
                            // stack is empty, so just u
                            result.push(start.1.to_string());
                        }
                    }
                }

                (stack, result)
            },
        ).1
} 
```

Enter fullscreen mode Exit fullscreen mode

## 扫雷舰

我有滥用一些语言结构的倾向，我真的是为了其他的目的，而不是为它设计的。以前是模式匹配语句，这次是选项枚举。

```
pub fn annotate(minefield: &[&str]) -> Vec<String> {
    let minefield: Vec<Vec<u8>> = minefield.iter().map(|&x| Vec::from(x)).collect();

    minefield
        .iter()
        .cloned()
        .enumerate()
        .map(|(row_idx, row)| {
            row.iter()
                .enumerate()
                .map(|(col_idx, &cell)| {
                    Some(cell)
                        .filter(|&x| x == b' ')
                        .map(|_| match count(&minefield, row_idx, col_idx) {
                            0 => String::from(" "),
                            n => n.to_string(),
                        }).unwrap_or((cell as char).to_string())
                }).collect::<String>()
        }).collect()
}

fn count(minefield: &Vec<Vec<u8>>, row_idx: usize, col_idx: usize) -> usize {
    neighbours(&minefield, row_idx, col_idx)
        .iter()
        .filter(|(row_idx, col_idx)| minefield[*row_idx][*col_idx] == b'*')
        .count()
}

fn neighbours(minefield: &Vec<Vec<u8>>, row_idx: usize, col_idx: usize) -> Vec<(usize, usize)> {
    (row_idx.saturating_sub(1)..=max(row_idx, minefield.len()))
        .flat_map(|r_idx| {
            (col_idx.saturating_sub(1)..=max(col_idx, minefield.first().map_or(0, |x| x.len())))
                .map(move |c_idx| (r_idx, c_idx))
        }).filter(|neighbour| *neighbour != (row_idx, col_idx))
        .collect()
}

fn max(idx: usize, length: usize) -> usize {
    (idx + 1).min(length - 1)
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然与我刚开始使用时相比，我现在使用 borrow checker 相对更加得心应手，但在处理生命周期时却不是这样。直到现在，我仍然觉得最复杂的事情是，我不能真正向别人解释它。作为一个使用高级语言的程序员，垃圾收集已经到位，我甚至不需要去想它们，这让我感到很失望。

当我第一次开始时，我甚至不关心栈和堆，因为它们是在我使用的语言中抽象出来的。

```
pub fn annotate(minefield: &[&str]) -> Vec<String> {
    minefield
        .iter()
        .enumerate()
        .map(|(row_idx, row)| {
            row.chars()
                .enumerate()
                .map(
                    |(col_idx, cell)| match (cell, count(&minefield, row_idx, col_idx)) {
                        (' ', n) if n != 0 => n.to_string(),
                        _ => cell.to_string(),
                    },
                ).collect::<String>()
        }).collect()
}

fn count(minefield: &[&str], row_idx: usize, col_idx: usize) -> usize {
    neighbours(&minefield, row_idx, col_idx)
        .filter(|(row_idx, col_idx)| {
            minefield[*row_idx]
                .chars()
                .nth(*col_idx)
                .map_or(false, |x| x == '*')
        }).count()
}

fn neighbours<'a>(
    minefield: &'a [&str],
    row_idx: usize,
    col_idx: usize,
) -> impl Iterator<Item = (usize, usize)> + 'a {
    (row_idx.saturating_sub(1)..=max(row_idx, minefield.len()))
        .flat_map(move |r_idx| {
            (col_idx.saturating_sub(1)..=max(col_idx, minefield.first().map_or(0, |x| x.len())))
                .map(move |c_idx| (r_idx, c_idx))
        }).filter(move |neighbour| *neighbour != (row_idx, col_idx))
}

fn max(idx: usize, length: usize) -> usize {
    (idx + 1).min(length - 1)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 平行信频

最后是某种并发/并行代码。然而，这意味着如果你不小心(不使用提高生活质量的板条箱)，终身管理将会受到很大影响。

```
use std::collections::HashMap;
use std::sync::mpsc;
use std::thread;

pub fn frequency(input: &[&str], worker_count: usize) -> HashMap<char, usize> {
    if input.len() == 0 {
        return HashMap::new();
    }

    let (tx, rx) = mpsc::channel();

    input
        .chunks((input.len() as f64 / worker_count as f64).ceil() as usize)
        .for_each(move |chunk| {
            let ttx = mpsc::Sender::clone(&tx);
            let chunk = chunk.iter().map(|&x| String::from(x)).collect::<Vec<_>>();

            thread::spawn(move || {
                chunk.iter().for_each(|sentence| {
                    ttx.send(
                        sentence
                            .to_lowercase()
                            .chars()
                            .filter(|x| x.is_alphabetic())
                            .fold(HashMap::new(), |mut current, incoming| {
                                *current.entry(incoming).or_insert(0) += 1;
                                current
                            }),
                    ).unwrap()
                });
            });
        });

    rx.iter().fold(HashMap::new(), |result, incoming| {
        incoming
            .into_iter()
            .fold(result, |mut current, (key, count)| {
                *current.entry(key).or_insert(0) += count;
                current
            })
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

奇怪的是，我并没有在我的电脑上看到太多的性能提升，也许是因为 docker 或其他东西一直在运行，占用了大量的 CPU 时间。然而，它似乎在我的导师那里工作，所以我想这是我在 Rust 中的第一个并发代码。它的工作方式，至少这个例子让我想起了 go 频道和一点 Go 例程。我对深入研究它到底是如何工作的不太感兴趣，我只是为了好玩才学习这种语言的:

我的导师告诉我，crossbeam 和 rayon 似乎可以修复我与 lifetime 的斗争，这样我就可以避免编写除了在第 16 行克隆之外什么也不做的代码。

将来会引入 async/await，我想它类似于 Python 3 中的 asyncio/coroutine。我想这和 goroutine 的工作方式更相似。

## 第四解释者

在进行到一半时，我切换到了 2018 版，因为该版本即将发布。此外，我没有任何需要移植的遗留代码这一事实也有所帮助。这个练习显然在 2015 版中根本不编译。这是解释器的最终版本。我在这上面做了大量的迭代，甚至重写了两遍，仅仅是为了澄清所有权和变异。

```
use std::collections::HashMap;
use std::rc::Rc;

pub type Value = i32;
pub type ForthResult = Result<(), Error>;

#[derive(Debug, PartialEq)]
pub enum Error {
    DivisionByZero,
    StackUnderflow,
    UnknownWord,
    InvalidWord,
}

#[derive(Clone)]
pub enum Token {
    Value(Value),
    BinaryOperator(Rc<dyn Fn(Value, Value) -> Result<Vec<Value>, Error>>),
    UnaryOperator(Rc<dyn Fn(Value) -> Result<Vec<Value>, Error>>),
}

pub struct Forth {
    symbols: HashMap<String, Vec<Token>>,
    stack: Vec<Value>,
}

impl Forth {
    pub fn new() -> Forth {
        Forth {
            symbols: Self::symbols_init(),
            stack: vec![],
        }
    }

    pub fn stack(&self) -> Vec<Value> {
        self.stack.to_vec()
    }

    pub fn eval(&mut self, input: &str) -> ForthResult {
        for incoming in self.tokenize(vec![], Self::input_split(input).as_slice())? {
            match incoming {
                Token::Value(value) => {
                    self.stack.push(value);
                }
                Token::BinaryOperator(operator) => {
                    let y = self.stack.pop().ok_or(Error::StackUnderflow)?;
                    let x = self.stack.pop().ok_or(Error::StackUnderflow)?;

                    self.stack.extend(operator(x, y)?);
                }
                Token::UnaryOperator(operator) => {
                    let x = self.stack.pop().ok_or(Error::StackUnderflow)?;

                    self.stack.extend(operator(x)?);
                }
            }
        }
        Ok(())
    }

    fn symbols_init() -> HashMap<String, Vec<Token>> {
        let mut result = HashMap::new();

        result.insert(
            "+".to_string(),
            vec![Token::BinaryOperator(Rc::new(|x, y| Ok(vec![x + y])))],
        );
        result.insert(
            "-".to_string(),
            vec![Token::BinaryOperator(Rc::new(|x, y| Ok(vec![x - y])))],
        );
        result.insert(
            "*".to_string(),
            vec![Token::BinaryOperator(Rc::new(|x, y| Ok(vec![x * y])))],
        );
        result.insert(
            "/".to_string(),
            vec![Token::BinaryOperator(Rc::new(|x, y| match (x, y) {
                (_, 0) => Err(Error::DivisionByZero),
                (m, n) => Ok(vec![m / n]),
            }))],
        );
        result.insert(
            "dup".to_string(),
            vec![Token::UnaryOperator(Rc::new(|x| Ok(vec![x, x])))],
        );
        result.insert(
            "drop".to_string(),
            vec![Token::UnaryOperator(Rc::new(|_| Ok(vec![])))],
        );
        result.insert(
            "swap".to_string(),
            vec![Token::BinaryOperator(Rc::new(|x, y| Ok(vec![y, x])))],
        );
        result.insert(
            "over".to_string(),
            vec![Token::BinaryOperator(Rc::new(|x, y| Ok(vec![x, y, x])))],
        );

        result
    }

    fn tokenize(&mut self, mut result: Vec<Token>, input: &[String]) -> Result<Vec<Token>, Error> {
        match input.first() {
            None => Ok(result),
            Some(value) => match (i32::from_str_radix(value, 10), self.symbols.get(value)) {
                (Ok(value), _) => {
                    result.push(Token::Value(value));
                    self.tokenize(result, &input[1..])
                }
                (_, Some(tokens)) => {
                    result.extend_from_slice(tokens.as_slice());
                    self.tokenize(result, &input[1..])
                }
                _ => {
                    let slice = self.tokenize_word(None, None, &input)?;
                    self.tokenize(result, slice)
                }
            },
        }
    }

    fn tokenize_word<'a>(
        &mut self,
        name: Option<String>,
        word: Option<Vec<Token>>,
        input: &'a [String],
    ) -> Result<&'a [String], Error> {
        match (input.first(), word.is_none()) {
            (Some(t), true) if t == ":" => self.tokenize_word(None, Some(vec![]), &input[1..]),
            (Some(t), _) if t == ";" => {
                self.symbols.insert(name.unwrap(), word.unwrap());
                Ok(&input[1..])
            }
            (Some(n), false) if name.is_none() => match i32::from_str_radix(n, 10) {
                Ok(_) => Err(Error::InvalidWord),
                Err(_) => self.tokenize_word(Some(n.to_string()), word, &input[1..]),
            },
            (Some(value), _) => match (i32::from_str_radix(value, 10), self.symbols.get(value)) {
                (Ok(value), _) => self.tokenize_word(
                    name,
                    word.map(|mut w| {
                        w.push(Token::Value(value));
                        w
                    }),
                    &input[1..],
                ),
                (_, Some(tokens)) => self.tokenize_word(
                    name,
                    word.map(|mut w| {
                        w.extend_from_slice(tokens);
                        w
                    }),
                    &input[1..],
                ),
                _ => Err(Error::UnknownWord),
            },
            _ => Err(Error::InvalidWord),
        }
    }

    fn input_split(input: &str) -> Vec<String> {
        input
            .split_terminator(|x| "  \u{0000}\u{0001}\r\n\t".contains(x))
            .map(|x| x.to_lowercase())
            .collect()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

仍然不完全满意这个第 12 次修订，但我想这是我目前能提供的最好的。这个解释器的想法是读取扔给它的任何东西，然后首先把它分解成令牌。这些记号然后被转换成实际的东西，如果是一个单词，就是一个函数，也可以是实际的数字。所有内置的单词/操作符都存储在 hashmap 中，作为某种可以替换的字典(如单元测试中所定义的)。

然后，在令牌被转换成 rust 对象之后，代码从左到右对它们进行求值。在这个版本中，每当遇到一个错误，它会立即被抛出。

就是这样，我想我总共花了至少 3 个月而不是一周来完成所有的核心练习，但仍然感觉不舒服。不确定在做完这些之后我是否还想继续剩下的练习。非常感谢在整个学习过程中帮助我的所有导师，希望将来有更多的人注册成为 rust track 的导师，因为这些天积压的工作太多了。