# 一个拯救生命的 Javascript 技巧

> 原文：<https://dev.to/kepta/one-life-saving-javascript-tip--611>

### 永远提前退出！

```
function mostComplicatedFunction(data) {

    if (!data) {
      return; // << Always exit early whenever you can!
    }

    // Bang Bang!

    var {words, ii, position, word, current, sentence} = data;

    /**
     * @default 100
     */
    num_words = num_words || 100;

    words = [LoremIpsum.WORDS[0], LoremIpsum.WORDS[1]];
    num_words -= 2;

    for (ii = 0; ii < num_words; ii++) {
    position = Math.floor(Math.random() * LoremIpsum.WORDS.length);
    word = LoremIpsum.WORDS[position];

    if (ii > 0 && words[ii - 1] === word) {
        ii -= 1;
    } else {
        words[ii] = word;
    }
    }

    sentences = [];
    current = 0;

    while (num_words > 0) {
    sentence_length = this.getRandomSentenceLength();

    if (num_words - sentence_length < 4) {
        sentence_length = num_words;
    }

    num_words -= sentence_length;

    sentence = [];

    for (ii = current; ii < current + sentence_length; ii++) {
        sentence.push(words[ii]);
    }

    sentence = this.punctuate(sentence);
    current += sentence_length;
    sentences.push(sentence.join("  "));
    }

    return sentences.join("  ");

} 
```

Enter fullscreen mode Exit fullscreen mode