# Method Reference

## Learning Goals

- Define the different types of method references.
- Compare a lambda expression with an equivalent method reference.
- Pass a method reference as a sort key function to `Comparator` methods.

## What are Method References?

Method references are a special type of lambda expression that make our code more concise and
readable. There are four types of method references:

- Static method reference
- Instance method reference of a particular object
- Instance method reference of an arbitrary object of a particular type
- Constructor reference

The table shows equivalent syntax between a lambda expression and a method reference:

| Method Reference                                                      | Lambda Expression                                    | Method Reference              |
|-----------------------------------------------------------------------|------------------------------------------------------|-------------------------------|
| Static method reference                                               | `(args) ->  ClassName.staticMethodName(args)`        | ClassName::staticMethodName   |
| Instance method reference of a particular object                      | `(args) ->  object.instanceMethodName(args)`         | object::instanceMethodName    |
| Instance method reference of an arbitrary object of a particular type | `(object, args) ->  object.instanceMethodName(args)` | ClassName::instanceMethodName |
| Constructor reference                                                 | `(args) -> new ClassName(args)`                      | ClassName::new                |


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

## Instance Method Reference of a Particular Object

A method reference `object::instanceMethodName`  can replace a lambda expression that
calls an instance method on the object with the supplied parameters `(args) ->  object.instanceMethodName(args)`.
The variable `object` is not passed into the lambda expression as a parameter, but must be within scope of the lambda expression
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

## Instance Method Reference of an Arbitrary Object of a Particular Type

A method reference `ClassName::instanceMethodName`  can replace a lambda expression that calls an
instance method with the supplied parameters `(object, args) ->  object.instanceMethodName(args)`, where `ClassName` is
the type of parameter `object`.

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


## Revisiting `Comparator` with method references

Recall from the lesson on sorting the `Arrays.sort` method
accepts a `Comparator` to determine order.

The `List` interface also defines a `sort()` method
that accepts a `Comparator`.

Given the `Employee` class shown below,
we may want to sort a list of employees by name, salary, or age.

```java
public class Employee {
    private String name;
    private double salary;
    private int age;

    public Employee(String name, double salary, int age) {
        this.name = name;
        this.salary = salary;
        this.age = age;
    }

    public String getName() { return name; }

    public double getSalary() { return salary; }

    public int getAge() { return age; }

    @Override
    public String toString() {
        return "Employee{" +
                "name='" + name + '\'' +
                ", salary=" + salary +
                ", age=" + age +
                '}';
    }
}
```

We've seen how to create a new class that implements `Comparator`
to produce a particular order.  The `Comparator` interface also defines
a static function `Comparator.comparing` that takes a sort key `Function`
for a type `T` as a parameter and returns a `Comparator` for type `T`.

`static <T> Comparator<T>	comparing(Function<? super T> keyExtractor)`

The variants `Comparator.comparingInt`, `Comparator.comparingDouble`,
and `Comparator.comparingLong` extract a sort key function for
data types `int`, `double`, and `long` respectively.

We can pass an instance method reference as the sort key function:

- `Comparator.comparing(Employee::getName)`
- `Comparator.comparingDouble(Employee::getSalary)`
- `Comparator.comparingInt(Employee::getAge)`


For example, we can easily order the list of employees by name: 

```java
import java.util.Arrays;
import java.util.Comparator;
import java.util.List;

public class Example {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
                new Employee("Anna", 45000.0, 24),
                new Employee("Fred", 58000.0, 32),
                new Employee("Abel", 40000.0, 24));

        employees.sort(Comparator.comparing(Employee::getName));
        System.out.println(employees);
    }
}
```

The program prints the employees in alphabetical order by name:

```text
[Employee{name='Abel', salary=40000.0, age=24}, Employee{name='Anna', salary=45000.0, age=24}, Employee{name='Fred', salary=58000.0, age=32}]
```

The `Comparator` interface defines a method `reversed` that
returns a comparator imposing a reverse order.

`Comparator.comparingDouble(Employee::getSalary).reversed()`
will order the employees in descending order of salary:

```java
public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("Anna", 45000.0, 24),
            new Employee("Fred", 58000.0, 32),
            new Employee("Abel", 40000.0, 24));

        //Sort by salary in reverse order
        employees.sort(Comparator.comparingDouble(Employee::getSalary).reversed());
        System.out.println(employees);
    }
```

The program prints:

```text
[Employee{name='Fred', salary=58000.0, age=32}, Employee{name='Anna', salary=45000.0, age=24}, Employee{name='Abel', salary=40000.0, age=24}]
```

The `Comparator` interface defines a method `thenComparing` that
supports a primary sort on one key, followed by a secondary sort on another key.
The variants `thenComparingInt`, `thenComparingDouble`, and `thenComparingLong`
work with sort key functions for primitive data types.

For example, to sort first by age and then by salary:
 
```java
   public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
                new Employee("Fred", 58000.0, 32),
                new Employee("Anna", 45000.0, 24),
                new Employee("Abel", 40000.0, 24));

        //Primary sort by age, secondary sort by salary
        employees.sort(Comparator.comparingInt(Employee::getAge).thenComparingDouble(Employee::getSalary));
        System.out.println(employees);
```

The two employees with the same age are listed in ascending order of salary:

```text
[Employee{name='Abel', salary=40000.0, age=24}, Employee{name='Anna', salary=45000.0, age=24}, Employee{name='Fred', salary=58000.0, age=32}]
```

## Summary

A method reference is a concise way to write a lambda expression
when the lambda body calls a static, instance, or constructor method with the supplied parameters.

## Resources

[Java 11 Comparator](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Comparator.html#thenComparingDouble(java.util.function.ToDoubleFunction))