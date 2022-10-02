# Method Reference

## Learning Goals

- Define the different types of method references.
- Compare a lambda expression with an equivalent method reference.

## What are Method References?

Method references are a special type of lambda expression that make our code more concise and
readable. There are four types of method references:

- Static method reference
- Instance method reference of a specific object
- Instance method reference of a parameter object
- Constructor reference

The table shows equivalent syntax between a lambda expression and a method reference:

| Method Reference                              | Lambda Expression                              | Method Reference               |
|-----------------------------------------------|------------------------------------------------|--------------------------------|
| Static method reference                       | `(args) ->  ClassName.staticMethodName(args)`  | ClassName::staticMethodName    |
| Instance method reference of specific object  | `(args) ->  o.instanceMethodName(args)`        | o::instanceMethodName          |
| Instance method reference of parameter object | `(o, args) ->  o.instanceMethodName(args)`     | ClassName::instanceMethodName  |
| Constructor reference                         | `(args) -> new ClassName(args)`                | ClassName::new                 |


Let’s look at examples of each of these different types of references.

## Static Method Reference

A method reference `ClassName::staticMethodName`  can replace a lambda expression that
calls a static method with the supplied arguments `(args) ->  ClassName.staticMethodName(args)`.

```java
public class StringHelper {
    public static int combinedLength(String s1, String s2) { 
        return s1.length() + s2.length(); 
    }
}
```
```java
import java.util.function.*;

//Static method reference
public class Example {
    public static void main(String[] args) {
        //Example#1
        DoubleSupplier lambda1 = () -> Math.random();
        DoubleSupplier methodRef1 = Math::random;
        System.out.println(lambda1.getAsDouble()); //0 .. 0.99999
        System.out.println(methodRef1.getAsDouble()); //0 .. 0.99999

        //Example#2
        ToIntBiFunction<String, String> lambda2 = (a,b) -> StringHelper.combinedLength(a,b);
        ToIntBiFunction<String, String> methodRef2 = StringHelper::combinedLength;
        System.out.println(lambda2.applyAsInt("hi", "bye"));  //5
        System.out.println(methodRef2.applyAsInt("apple", "pear"));  //9
    }
}
```

The lambda expression referenced by `lambda1` takes no parameters and returns
the double value produced by static method `Math.random`. The equivalent method reference is assigned to the variable `methodRef1`.  

Recall the functional interface `java.util.function.Supplier` defines an abstract method named `get`.
The `DoubleSupplier` variation renames the abstract method to `getAsDouble`.
The lambda expression  and the method reference both implement 
`getAsDouble` by calling the static method `Math.random`, which returns a random double.

The second example shows a lambda expression `lambda2` and equivalent method
reference `methodRef2` that call the
static method `combinedLength` in the `StringHelper` class.
The  functional interface `ToIntBiFunction` is used since the static method
takes 2 String parameters and returns an int. The functional interface defines the
abstract method `applyAsInt`, which is implemented by 
the lambda expression and the method reference to call the static `combinedLength` method.

## Instance Method Reference of a Specific Object

A method reference `o::instanceMethodName`  can replace a lambda expression that
calls an instance method on object `o` with the supplied parameters `(args) ->  o.instanceMethodName(args)`.
Since variable `o` is not passed into the lambda expression as a parameter,
it must be within scope of the lambda expression
(i.e. a local variable of enclosing block, instance variable, or static variable).

```java
 public class BankAccount  {
    private String id;
    private double balance;

    public BankAccount(String id, double balance) {
        this.id = id;
        this.balance = balance;
    }

    public double getBalance() {return balance;}
    public void setBalance(double balance) {this.balance = balance;}
}
```

```java
import java.util.function.*;
//Instance method reference of a specific object
public class Example {

    public static void main(String[] args) {
        //Example#1
        Consumer<String> lambda3 = s -> System.out.println(s); 
        Consumer<String> methodRef3 = System.out::println;
        lambda3.accept("hello"); //hello
        methodRef3.accept("hi there");  //hi there

        //Example#2
        BankAccount account = new BankAccount("abc123", 5000);
        Consumer<Integer> lambda4 = b -> account.setBalance(b);
        Consumer<Integer> methodRef4 = account::setBalance;
        lambda4.accept(1000); //balance is 1000
        methodRef4.accept(500); //balance is 500
    }
}
```

In the first example, `System.out` references an object of the built-in Java class `java.lang.PrintStream`.
The instance method `println` is called on the `PrintStream` object.
The lambda and the method reference both implement the `accept` method of `Consumer` functional interface
to print the parameter string.

The second example creates a `BankAccount` object referenced by the variable `account`. 
We then call the instance method `setBalance` on the object, passing the balance into the parameter.
The lambda expression and the method reference both override the `accept` abstract method
of functional interface `Consumer` by calling the `setBalance` instance method.

## Instance Method Reference To A Parameter Object

A method reference `o::instanceMethodName`  can replace a lambda expression that calls an
instance method with the supplied parameters `(o, args) ->  o.instanceMethodName(args)`.
The object reference `o` is passed into the lambda expression as a parameter.

```java
import java.util.function.BiConsumer;
import java.util.function.UnaryOperator;

//Instance method reference of a parameter object
public class Example {
    public static void main(String[] args) {
        //Example#1
        UnaryOperator<String> lambda5 = s -> s.toLowerCase();
        UnaryOperator<String> methodRef5 = String::toLowerCase;
        System.out.println(lambda5.apply("HeLLo")); //hello
        System.out.println(methodRef5.apply("HAHA")); //haha

        //Example#2
        BiConsumer<BankAccount,Integer> lambda6 = (account, b) -> account.setBalance(b);
        BiConsumer<BankAccount,Integer>  methodRef6 = BankAccount::setBalance;
        BankAccount b1 = new BankAccount("abc123", 100);
        BankAccount b2 = new BankAccount("xyz456",3000);
        lambda6.accept(b1, 500); //balance is 500 for account abc123
        lambda6.accept(b2, 0); //balance is 0 for account xyz456
    }
}
```

In the first example, the lambda expression takes a `String`  as a parameter and calls instance method `toLowerCase`.
The method reference shows the equivalent functionality of calling the instance method using the class name.
A `String` object must be passed into the `accept` method of the functional interface to bind the parameter to the
method body.

The second example defines a lambda expression and equivalent method reference that take two parameters.
The return type of `setBalance` is void; thus, we use the `BiConsumer<BankAccount, Integer>` functional interface. 
The lambda expression and method reference both override the abstract method `accept`,
calling instance method `setBalance` on the bank account object passed as the first parameter.

## Constructor Reference

A method reference `ClassName::new`  can replace a lambda expression that calls
a constructor with the supplied parameters `(args) -> new ClassName(args)`.

```java
import java.util.function.BiFunction;
import java.util.function.UnaryOperator;

//Constructor reference
public class Example {
    public static void main(String[] args) {
        //Example#1
        UnaryOperator<String> lambda7 = s -> new String(s);
        UnaryOperator<String> methodRef7 = String::new;
        System.out.println(lambda7.apply("hi")); //hi
        System.out.println(methodRef7.apply("hello")); //hello

        //Example#2
        BiFunction<String, Double, BankAccount> lambda8 = (id, balance) -> new BankAccount(id, balance);
        BiFunction<String, Double, BankAccount> methodRef8 = BankAccount::new;
        BankAccount b1 = lambda8.apply("abc123", 1000.0);
        BankAccount b2 = lambda8.apply("xyz456", 50.75);
    }
}
```

## Summary

A method reference is a concise way to write a lambda expression
when the lambda body calls a static, instance, or constructor method with the supplied parameters.
