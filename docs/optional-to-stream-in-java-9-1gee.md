# java 9 中的可选流

> 原文：<https://dev.to/jeetmp3/optional-to-stream-in-java-9-1gee>

去年，Java 9 推出了大量新特性。在这篇博文中，我将解释`Optional`(在 Java8 中引入)中的流。现在你可以让流组成`Optional`，我认为这是一个很好的特性。

下面的例子显示了如何在 Java 8 和 Java 9 中从可选的。

```
package com.jbisht.blogs.java9.usingoptional;

import java.util.Optional;
import java.util.stream.Stream;

public class OptionalDemo {

    public static void main(String[] args) {
        // Java 9 style
        getPerson().stream()
                .map(Person::getName)
                .map("Java 9 "::concat)
                .forEach(System.out::println);

        getEmptyPerson().stream()
                .map(Person::getName)
                .map("Java 9 "::concat)
                .forEach(System.out::println);

        // Java 8 Style
        getPerson()
                .map(Stream::of).orElseGet(Stream::empty)
                .map(Person::getName)
                .map("Java 8 - "::concat)
                .forEach(System.out::println);
    }

    private static Optional<Person> getEmptyPerson() {
        return Optional.empty();
    }

    private static Optional<Person> getPerson() {
        return Optional.of(new Person("JITENDRA SINGH"));
    }

    static class Person {

        private String name;

        public Person(String name) {
            this.name = name;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }
} 
```

快乐编码😀😀😀！！！如果您有任何反馈，请在下面评论。