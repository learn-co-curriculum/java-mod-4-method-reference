# Method Reference

## Learning Goals

- Understand method references.
- Use the different types of method references.

## What are Method References?

Java allows us to reference methods which makes our code more concise and
readable. There are four types of method references:

- Static method reference
- Instance method reference of a specific object
- Instance method reference without object creation
- Constructor reference

Let’s look at examples of each of these different types of references

## Static Method Reference

We can reference static methods with the `ClassName::staticMethodName` syntax.

```java
public class Example {
    public static int getLength(String s) {
        return s.length();
    }

    public static void main(String[] args) {
        List<String> fruits = List.of("banana", "apple", "grape", "orange", "pear");

        // without method reference
        fruits.stream()
                .mapToInt(fruit -> getLength(fruit))
                .forEach(length -> System.out.println(length));

        // with method reference
        fruits.stream()
                .mapToInt(Example::getLength)
                .forEach(System.out::println);
    }
}
```

We can also store method references in functional interface variables.

```java
import java.util.function.Function;

public class Example {
    public static void main(String[] args) {
        Function<Double, Double> sqrt = Math::sqrt;
        System.out.println(sqrt.apply(25d)); // 5.0
    }
}
```

## Instance Method Reference of a Specific Object

We can reference methods on initialized objects using the
`objectVariable::instanceMethodName` syntax.

```java
class MyString {
    public int getLength(String s) {
        return s.length();
    }
}

public class Example {
    public static void main(String[] args) {
        List<String> fruits = List.of("banana", "apple", "grape", "orange", "pear");
        MyString str = new MyString();

        fruits.stream()
                .mapToInt(str::getLength)
                .forEach(System.out::println);
    }
}
```

We created an object of type `MyString` and stored its reference in the `str`
variable. We are then able to pass the reference of the `getLength` instance
method to the `mapToInt` method.

## Instance Method Reference without Object Creation

We can also reference instance methods directly without creating objects.

```java
public class Example {
    public static void main(String[] args) {
        List<String> fruits = List.of("banana", "apple", "grape", "orange", "pear");

        fruits.stream()
                .mapToInt(String::length)
                .forEach(System.out::println);
    }
}
```

The `length` method is an instance method but we are referencing it in the
`mapToInt` method without referencing any specific objects.

## Constructor Reference

The constructor method can be referenced similar to how we reference instance
methods. The general form is `ClassName::new`.

```java
class Fruit {
    private String name;
    private int price;

    public Fruit(String name, int price) {
        this.name = name;
        this.price = price;
    }
}

public class Example {
    public static void main(String[] args) {
        BiFunction<String, Integer, Fruit> fruitCreator = Fruit::new;
        Fruit apple = fruitCreator.apply("Apple", 1);
        System.out.println(apple);
    }
}
```
