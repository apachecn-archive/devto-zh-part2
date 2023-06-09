# 用函数式 Java 8 实现 Fluent Builder

> 原文：<https://dev.to/ikysil/implementing-fluent-builder-with-functional-java-8-d9k>

```
package playground;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collection;
import java.util.List;
import java.util.Objects;
import java.util.function.BiConsumer;
import java.util.function.Consumer;
import java.util.function.Function;
import java.util.function.Supplier;
import javax.swing.*;

import static playground.JavaPlayground.Builder.of;

public class FunctionalBuilderDemo {

    static class Builder<T> {

        private final T instance;

        private Builder(final T instance) {
            Objects.requireNonNull(instance, "instance is required");
            this.instance = instance;
        }

        static <T> Builder<T> of(final T instance) {
            return new Builder<>(instance);
        }

        static <T> Builder<T> of(final Supplier<T> supplier) {
            return new Builder<>(supplier.get());
        }

        public Builder<T> with(final Consumer<T> c) {
            c.accept(instance);
            return this;
        }

        public <V> Builder<T> with(final BiConsumer<T, ? super V> c, final V value) {
            c.accept(instance, value);
            return this;
        }

        public <V> Builder<T> add(final Supplier<Collection<? super V>> c, final V value) {
            c.get().add(value);
            return this;
        }

        public <V> Builder<T> add(final Function<T, Collection<? super V>> c, final V v) {
            c.apply(instance).add(v);
            return this;
        }

        public <V> Builder<T> add(final Function<T, Collection<? super V>> c, final V... v) {
            c.apply(instance).addAll(Arrays.asList(v));
            return this;
        }

        public <V> CollectionBuilder<T, V> collectInto(final Function<T, Collection<? super V>> c) {
            return new CollectionBuilder<>(this, c);
        }

        public <V> CollectionBuilder2<T, V> collect(final Collection<V> c) {
            return new CollectionBuilder2<>(this, c);
        }

        public <V> CollectionBuilder2<T, V> collect(final Supplier<Collection<V>> c) {
            return collect(c.get());
        }

        public <V> CollectionBuilder2<T, V> collect() {
            return collect(new ArrayList<>());
        }

        public T instance() {
            return instance;
        }

    }

    static class CollectionBuilder<T, V> {

        private final Builder<T> builder;

        private final Function<T, Collection<? super V>> c;

        public CollectionBuilder(final Builder<T> builder, final Function<T, Collection<? super V>> c) {
            this.builder = builder;
            this.c = c;
        }

        public Builder<T> collect() {
            return builder;
        }

        public CollectionBuilder<T, V> add(final V value) {
            c.apply(builder.instance()).add(value);
            return this;
        }

    }

    static class CollectionBuilder2<T, V> {

        private final Builder<T> builder;

        private final Collection<V> c;

        public CollectionBuilder2(final Builder<T> builder, final Collection<V> c) {
            this.builder = builder;
            this.c = c;
        }

        public Builder<T> into(final Function<T, Collection<? super V>> finalCollector) {
            finalCollector.apply(builder.instance()).addAll(c);
            return builder;
        }

        public CollectionBuilder2<T, V> add(final V value) {
            c.add(value);
            return this;
        }

    }

    public static class Victim {

        private final Collection<String> strings = new ArrayList<>();

        private final Collection<Number> numbers = new ArrayList<>();

        Collection<String> getStrings() {
            return strings;
        }

        Collection<Number> getNumbers() {
            return numbers;
        }

        @Override
        public String toString() {
            return "Victim{" +
                "strings=" + strings +
                ", numbers=" + numbers +
                '}';
        }

    }

    public static void main(String[] args) {
        System.out.println("Functional Builder Demo");
        final JLabel jLabelInstance = of(new JLabel())
            .with(o -> o.setText("test"))
            .with(JLabel::setText, "test")
            .with(JLabel::setText, null)
            .with(JLabel::setDisabledIcon, of(new ImageIcon())
                .with(ImageIcon::setDescription, "icon description")
                .instance()
            )
            .instance();
        System.out.println(jLabelInstance);
        final ArrayList<JLabel> arrayListInstance = of(ArrayList<JLabel>::new)
            .with(List::add, new JLabel())
            .with(List::add, jLabelInstance)
            .instance();
        System.out.println(arrayListInstance);
        final Object instance = of(Victim::new)
            .add(Victim::getStrings, "asd")
            .add(Victim::getStrings, "asd", "def")
            .collectInto(Victim::getStrings)
            .add("qwe")
            .collect()
            .collect(ArrayList<String>::new)
            .add("poi")
            .into(Victim::getStrings)
            .<Integer>collect()
            .add(123)
            .into(Victim::getNumbers)
            .instance();
        System.out.println(instance);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode