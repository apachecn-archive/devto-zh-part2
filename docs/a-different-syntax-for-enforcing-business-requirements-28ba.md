# 实施业务需求的不同语法

> 原文：<https://dev.to/shalvah/a-different-syntax-for-enforcing-business-requirements-28ba>

您是否曾经不得不在多种条件下实现一些棘手的业务需求？有时候，需求可能很容易用人类语言来表达，但是当涉及到代码时，你就很难用一种其他人容易理解的方式来表达。

例如，假设你有一个虚拟的学校门户网站，学生可以在那里申请经济援助。你有一个要求:

> 绩点低于 2.0 的学生不应该被允许申请，除非他们是最后一年或者有健康问题。

你可以这样写:

```
if ($student->hasGpaLessThan(2.0)) {
    if (!$student->isInFinalYear() || !$student->hasMedicalCondition()) {
        throw new BadRequestException("We can't provide you with aid right now.")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是另一种方式:

```
if ($student->hasGpaLessThan(2.0) 
    && (!$student->isInFinalYear() || !$student->hasMedicalCondition())
) {
    throw new BadRequestException("We can't provide you with aid right now.");
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你可以这样写呢？

```
when($student->hasGpaLessThan(2.0))
    ->ensure($student->isInFinalYear() || $student->hasMedicalCondition())
    ->orElseDeny("We can't provide you with aid right now.") 
```

Enter fullscreen mode Exit fullscreen mode

我想你会同意我的观点，第三个版本是最容易理解的，并且更接近需求的措辞。它也比前两个更不容易出错。

再举一个例子。假设我们有第二个需求:

> 学生只有在学习一门符合资助条件的课程或者他们有校长的信时才可以申请资助。

*(无视我们疯狂的规则；这是一所虚构的学校🤭)*

这能行:

```
if (!$student->isStudyingEligibleCourse() && !$student->hasPresidentsLetter()) {
    throw new BadRequestException("Sorry, no aid for you.");
} 
```

Enter fullscreen mode Exit fullscreen mode

使用我们的`ensure`语法，这将是:

```
ensure($student->isStudyingEligibleCourse() || $student->hasPresidentsLetter())
    ->orElseDeny("Sorry, no aid for you."); 
```

Enter fullscreen mode Exit fullscreen mode

这两种语法非常相似，但是在我看来，第二种更容易理解。

值得一提的是，我并不打算重新发明或消除`if` s，我只是相信在某些情况下使用`when...ensure...orElseDeny`会更有表现力。我也想听听你的想法。

如果你喜欢这个语法，我把它做成[一个包](https://github.com/shalvah/ensure)，所以你可以通过运行`composer require shalvah/ensure`在你的项目中使用它。如果觉得有用请明星分享！