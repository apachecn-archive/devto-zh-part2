# 我参加了我的第一次黑客马拉松！！:D

> 原文：<https://dev.to/leanminmachine/went-for-my-first-hackathon-d-4ihb>

伙计们。！我现在感觉很开心，我刚刚花了 1.5 天把一些东西放在一起。这没什么了不起的(基本上只是一个反应式应用程序，用户填写一些信息，然后将这些信息与其他用户的信息进行比较，并根据评分公式返回适当的匹配)。我对 React atm 有点生疏了，当我最后一次接触它是在 1 年前，从那以后很多事情都变了！

还是。我真的好开心因为我一直没怎么编码(一直忙着用户访谈！).这是为数不多的几天，我真的可以每天花 12 小时编码(怀念那些日子的人)，并让我意识到全心全意地将时间投入到一个副业项目可以让我在短时间内学到很多东西。虽然没有黑客马拉松，我觉得这样做的动力更少

无论如何，这是我从我的记忆中，从构建我的小应用程序中学到/更新的基本知识。

*   **this.setState 是异步执行的**，需要使用一个更新箭头函数才能真正正确地设置状态【https://reactjs.org/docs/faq-state.html

```
 handleSubmit = () => {
        const value = this._form.getValue(); // use that ref to get the form value

        this.setState((prevState) => {
            return {mentor: value}
        });

        console.log('state', this.state);

      } 
```

*   **在一个屏幕上设置参数并传递到下一个屏幕:** [javascript - React Native:如何传递从一个屏幕导航到另一个屏幕的道具-堆栈溢出](https://stackoverflow.com/questions/44636886/react-native-how-to-pass-props-navigating-from-one-screen-to-another)

**从前一屏幕:**

```
this.props.navigation.navigate('Results', {mentor: this.state.mentor}); 
```

首先将对象存储在屏幕的本地状态中。

**下一屏:**
渲染方法:
this . props . navigation . state . params

*   **从本地文件导入 JSON】**

'导入学员数据自'../data/mentee _ data . JSON '；

我不知道它是如此容易的 lol。

*   **对于函数方法** [javascript -如何跳过中的一个元素。map()？-堆栈溢出](https://stackoverflow.com/questions/24806772/how-to-skip-over-an-element-in-map)记住在回调函数内返回元素，在回调函数外返回函数方法的数组结果

```
 filterByTiming = (mentor, mentees) => {
        let matches = mentees.filter(mentee => {
            console.log('*** mapped mentor ***', mentor);
            console.log('*** mapped mentee timing ***', mentee.timing);

            if (mentor.timing == mentee.timing) {
                console.log('matches currently: ', matches);
                return mentee;
            } else {
                return false; //skip
            }

        }).map(mentee => {return mentee; });

        console.log('matches : ', matches);
        return matches;
    } 
```

*   **检查 JavaScript 对象中是否存在关键字** [数组——检查 Javascript 对象中是否存在关键字？-堆栈溢出](https://stackoverflow.com/questions/1098040/checking-if-a-key-exists-in-a-javascript-object)

您应该使用 in 运算符:

obj // true 中的“key ”,不考虑实际值

*   **使用排序函数** [按属性对对象数组进行排序使用 sort(fn)](https://davidwalsh.name/array-sort) 按属性对对象数组进行排序

排序比较器功能！

```
 // returns a sorted list, from highest score to lowest score
    sortByScore = (arr) => {
    arr.sort(function(obj1, obj2) {
        return obj2.score - obj1.score;
    });
    return arr;
    } 
```

*   **有趣的是**，除了使用 slice(读取数组)和 splice(变异数组)之外，我还可以使用。截断数组的长度？！ [jquery -如何用 JavaScript 轻松截断数组？-堆栈溢出](https://stackoverflow.com/questions/953071/how-to-easily-truncate-an-array-with-javascript)

*   **什么时候用这个关键字 vs 不用这个？**当我从上一个屏幕加载一些参数，并存储在 render 方法内部的一个变量中，那么我就不需要使用 this 关键字了？使用它会给我一个错误。嗯，这个我需要多读一些！

*   有用教程:[https://medium . com/react-native-development/easy-build-forms-in-react-native-9006 fcd 2 a 73 b](https://medium.com/react-native-development/easily-build-forms-in-react-native-9006fcd2a73b)for forms！