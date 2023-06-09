# 异步/等待-暂停什么？

> 原文：<https://dev.to/bidipeppercrap/asyncawait---confusing-part-3gnk>

困惑于`async/await`？这里有一些 TLDR；关于它的信息。

### Await 仅暂停其父异步函数

```
const messages = return new Promise((resolve), () => {
    setTimeout(() => {
        resolve(["Hey", "Good day"]);
    }, 5000);
});

async function sendMessages() { // <-- Await only pauses this function
    console.log("Sending message...");
    console.log("Message sent: ", await messages); // <-- If await reached, it executes code outside this async function, and pause this async function
}

// Let's proof it
console.log("Start");
sendMessages();
console.log("End");

// Output:

// Start
// Sending message...                  <-- sendMessage() function
// End                                 <-- Proof, await makes execution continue outside its function
// Message sent: ["Hey", "Good day"]   <-- After 5 seconds, this appears 
```

这证明了无论何时调用,`await`都会在异步函数之外跳转代码执行。
你可以看到`await`只暂停了它的父异步函数，而不是该函数之外的全部代码。
见`console.log("End")`前呼后拥的`await`。

但是我不想要它，我希望我的代码有序/逐步/同步执行！

我明白了，来解决这个问题:

```
const messages = return new Promise((resolve), () => {
    setTimeout(() => {
        resolve(["Hey", "Good day"]);
    }, 5000);
});

async function sendMessages() {
    console.log("Sending message...");
    console.log("Message sent: ", await messages);
}

// -- Old Code
// console.log("Start");
// sendMessages();
// console.log("End");

// -- New Code
async function log() {
    console.log("Start");
    await sendMessages();
    console.log("End");
}
log();

// Output:
// Start
// Sending message...                  <-- sendMessage() function
// Message sent: ["Hey", "Good day"]   <-- After 5 seconds, this appears
// End 
```

我做了什么？将所有要有序执行的东西包装在一个异步函数中，将 await 添加到一个“承诺”函数中。