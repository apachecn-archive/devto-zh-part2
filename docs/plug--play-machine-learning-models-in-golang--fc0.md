# GoLang 中的即插即用机器学习模型

> 原文：<https://dev.to/michaeljtaylor0/plug--play-machine-learning-models-in-golang--fc0>

## 问题

没有一个模型能适用于所有可能的情况。-没有免费的午餐。DH 沃伯特。

## 解决方案

不到 200 行代码的单元可测试、依赖可注入和回溯可测试模型。

### 依赖注入

为什么？依赖注入允许我们在机器学习管道中的模型内轻松热交换/注入模型和分类器。

#### 让我们来看一个最终结果示例:

```
func main() {
 // SpamHamModel with a Naive Bayes Classifier plugged in
 spamNb := SpamHamModel{classifier: &NBClassifier{}}

 // Exact Same SpamHamModel with a SVM Classifier plugged in
 spamSVM := SpamHamModel{classifier: &SVMClassifier{}}

 // Exact Same SpamHamModel with a Neural Network Classifier plugged in
 spamNN := SpamHamModel{classifier: &NNClassifier{}}
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 谢谢你到目前为止的时间...让我们就此展开讨论。

##### 数据结构和接口

```
// Step 1 - Define the Structure of Input Data
// You got mail!
type Email struct {
    Author string
    Body   string
    Flag   string //Spam/Ham
}

// Step 2 - Define a ML Classifier Contract
// Binary Classifier Interface - Examples SVM, NN, NB
type Classifier interface {
    Learn(emails []Email)
    Predict(email Email) string
}

// Step 3 - Create a Model with a "Plug & Play" Classifer Field
// You got mail! - Is it Spam or Ham? (Model Example)
type SpamHamModel struct {
    Classifier Classifier
}

func (model *SpamHamModel) Learn(emails []Email) {
    model.Classifier.Learn(emails)
}

func (model *SpamHamModel) Predict(email Email) string {
    return model.Classifier.Predict(email)
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 朴素贝叶斯分类器

```
// Step 4 - Implement Classifier(s). Per the Contracts Terms.
// You got mail! - Is it Spam or Ham? (Model's Brain/Classifier)
type NBClassifer struct {
    classifier *bayesian.Classifier
    output     []bayesian.Class
}

func (c *NBClassifier) Learn(emails []models.Email) {
    c.output = distinctFlags(emails)
    c.classifier = bayesian.NewClassifierTfIdf(c.output...)
    for i := 0; i < len(emails); i++ {
        c.classifier.Learn(strings.Split(emails[i].Body, " "), bayesian.Class(emails[i].Flag))
    }
    c.classifier.ConvertTermsFreqToTfIdf()
}

func (c *NBClassifier) Predict(email models.Email) string {
    scores, _, _ := c.classifier.LogScores(strings.Split(email.Body, " "))
    results := models.Results{}
    for i := 0; i < len(scores); i++ {
        results = append(results, models.Result{ID: i, Score: scores[i]})
    }

    sort.Sort(sort.Reverse(results))

    flags := []string{}
    for i := 0; i < len(results); i++ {
        flags = append(flags, string(c.output[results[i].ID]))
    }
    return flags[0]
}

func distinctFlags(emails []models.Email) []bayesian.Class {
    result := []bayesian.Class{}
    j := 0
    for i := 0; i < len(emails); i++ {
        for j = 0; j < len(result); j++ {
            if emails[i].Flag == string(result[j]) {
                break
            }
        }
        if j == len(result) {
            result = append(result, bayesian.Class(emails[i].Flag))
        }
    }
    return result
} 
```

Enter fullscreen mode Exit fullscreen mode

### 单元测试

将这样的接口用于关键的产品代码片段，可以更容易地遵循 TDD 等开发方法。

#### 示例:

```
func CreateTrainingEmails() []models.Email {
    return []models.Email{
        models.Email{Body: "opportunity to earn extra money", Flag: "Spam"},
        models.Email{Body: "druggists blame classy gentry Aladdin", Flag: "Spam"},
        models.Email{Body: "please take a look at this report", Flag: "Ham"},
        models.Email{Body: "lunch at noon?", Flag: "Ham"},
    }
}

func CreateValidationEmails() []models.Email {
    return []models.Email{
        models.Email{Body: "opportunity to earn extra money", Flag: "Spam"},
        models.Email{Body: "druggists blame classy gentry Aladdin", Flag: "Spam"},
        models.Email{Body: "please take a look at this report", Flag: "Ham"},
        models.Email{Body: "lunch at noon?", Flag: "Ham"},
    }
}

func TestLearn(t *testing.T) {
    nbModel := models.SpamHamModel{Classifier: &NBClassifier{}}
    trainingSet := CreateTrainingEmails()
    validationSet := CreateValidationEmails()

    nbModel.Learn(trainingSet)

    for i := 0; i < len(validationSet); i++ {
        input := validationSet[i].Body
        expected := validationSet[i].Flag
        actual := nbModel.Predict(validationSet[i])
        Assert(t, expected, actual, input)
    }
}

func Assert(t *testing.T, expected string, actual string, input string) {
    if actual != expected {
        t.Error(
            "\nFOR:       ", input,
            "\nEXPECTED:  ", expected,
            "\nACTUAL:    ", actual,
        )
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 回溯测试(请继续关注第二部分...)

github 上也有代码(如果你想在本地测试的话):[https://github.com/heupr/resources/tree/master/plugnplay](https://github.com/heupr/resources/tree/master/plugnplay)