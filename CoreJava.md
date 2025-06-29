# Encapsulation and Class Access in Object-Oriented Programming

**Encapsulation** is a key concept in object-oriented programming (OOP). It allows a software component to hide its data from other components, protecting the data from being updated without the component’s approval or knowledge.

## Class Access
What does it mean to access a class? When we say code from one class (class A) has access to another class (class B), it means class A can do one of three things:
- **Create an instance** of class B.
- **Extend class B** (i.e., become a subclass of class B).
- **Access certain methods and variables** within class B, depending on the access control of those methods and variables.

In effect, access means **visibility**. If class A can’t see class B, the access level of the methods and variables within class B won’t matter; class A won’t have any way to access them.

### Default Access
A class with **default access** has no modifier preceding it in the declaration! It’s also known as **package-level access**, meaning that a class with default access can only be seen by other classes within the same package.

### Public Access
A class declared with the `public` keyword allows **all classes** from any package to access it. All classes in the Java universe (JU) have access to a `public` class. However, if the public class is in a different package, you’ll still need to import it.

## Nonaccess Class Modifiers
You can modify a class declaration using the keywords `final`, `abstract`, or `strictfp`.

### Final Classes
A class declared `final` cannot be subclassed. Any attempt to extend a `final` class will result in a compiler error.

### Abstract Classes
An **abstract class** can never be instantiated. Its primary purpose is to be extended by other classes.

## Interface
Think of an interface as a **100% abstract class**. While an abstract class can define both abstract and non-abstract methods, an interface generally has only abstract methods. Interfaces also have strict rules for method and variable declarations:
- Interface methods are **implicitly public** and **abstract**, unless declared as `default` or `static`.
- All variables in an interface are implicitly `public`, `static`, and `final`—interfaces can declare only constants, not instance variables.
- Interface methods cannot be marked as `final`, `strictfp`, or `native`.
- An interface can **extend** one or more other interfaces, but it **cannot implement** another interface or class.
- An interface must be declared using the `interface` keyword.

### Declaring Interface Constants
Constants declared in an interface are always `public static final`, even if you don’t explicitly declare them as such. Any class implementing the interface will have access to the constants as if they were inherited.

## Default Methods (Java 8+)
As of Java 8, interfaces can include inheritable methods with concrete implementations, called **default methods**:
- Default methods are declared using the `default` keyword and are **public** by default.
- Default methods cannot be marked as `private`, `protected`, `static`, `final`, or `abstract`.
- Default methods must have a concrete method body.

## Static Interface Methods (Java 8+)
Java 8 also introduced **static methods** in interfaces, which are declared with the `static` keyword:
- Static interface methods are **public** by default.
- They cannot be marked as `private`, `protected`, `final`, or `abstract`.
- Static methods must include a concrete method body.
- To invoke a static interface method, the **interface name** must be used in the method invocation.

# Access Modifiers

## Public Members
When a method or variable member is declared `public`, it means all other classes, regardless of the package they belong to, can access the member (assuming the class itself is visible).

## Private Members
Members marked `private` can’t be accessed by code in any class other than the class in which the private member was declared. When a member is declared `private`, a subclass can’t inherit it. However, a matching method can be declared in the subclass, but it is not considered an overriding method.

## Protected and Default Members
The `protected` and `default` access control levels are similar, but with a key difference. A **default** member can only be accessed by classes in the same package. A **protected** member, however, can be accessed by subclasses, even if the subclass is in a different package.

- **Default**: Restricts access to within the same package.
- **Protected**: Grants access to subclasses outside the package through inheritance. However, protected members are not public to the subclass, and access is only possible through inheritance, not through a reference to the superclass object.

## Final Methods
The `final` keyword prevents a method from being overridden in a subclass and is often used to enforce API functionality.

# Variables

## Local (Automatic/Stack/Method) Variables
A local variable is declared and initialized within a method and destroyed when the method completes. Local variables reside on the stack, while object references stored in local variables point to objects on the heap. Local variables can be marked `final` and must be initialized before use.

## Final Variables
Declaring a variable with `final` makes it impossible to reassign it once it has been initialized with an explicit value. For primitives, the value cannot be altered after assignment. For objects, the reference cannot be reassigned, though the object's internal state can still be changed.

## Transient Variables
Marking an instance variable as `transient` tells the JVM to ignore the variable when serializing the object.

# Declaring Enums
Java enums restrict a variable to having one of a few predefined values, representing an enumerated list.

# Relationships

## IS-A
The **IS-A** relationship in object-oriented programming (OOP) is based on inheritance or interface implementation. It signifies that one class is a type of another class (e.g., `Mustang IS-A Horse`). This relationship is represented in Java using the `extends` and `implements` keywords.

## HAS-A
The **HAS-A** relationship is based on composition or aggregation. One class **HAS-A** another class if it holds a reference to an instance of that class (e.g., `Horse HAS-A Halter`).

# Polymorphism

## Compile-time Polymorphism (Static Binding)
Also known as method overloading, this occurs when multiple methods have the same name but different signatures. The method to be called is determined at compile time.

## Runtime Polymorphism (Dynamic Binding)
Occurs through method overriding. A subclass can provide its own implementation of a superclass method, with the actual method called determined at runtime based on the object's type.

# Abstraction
Abstraction in OOP focuses on hiding the internal implementation details of an object and exposing only the necessary functionality.

# Memory

## Stack and Heap
- **Instance variables** and **objects** live on the heap.
- **Local variables** live on the stack.

# Garbage Collector
The garbage collector ensures that the heap has as much free space as possible by deleting unreachable objects. The JVM controls garbage collection, and while you can request it, there are no guarantees that the JVM will comply. The `finalize()` method can be used to run code before an object is deleted, but it is not guaranteed to run.

# equals() Method

## equals() in Class Object
The `equals()` method in `Object` checks if two references point to the same object, returning `true` if they do.

## equals() in Class String
The `String` class overrides `equals()`, comparing the values of the strings rather than their references. String comparison is case-sensitive.

# Exceptions

## Exception Hierarchy
All exception classes derive from `Throwable`, which has two main subclasses:
- **Exception**: Used for recoverable issues.
- **Error**: Represents serious issues that are not normally recoverable (e.g., out of memory).

# String, StringBuilder, and StringBuffer

- **String** objects are immutable.
- **StringBuilder** and **StringBuffer** classes are used for mutable strings.
- **StringBuilder** is faster than **StringBuffer** but is not thread-safe.

# equals() Contract
1. Reflexive: `x.equals(x)` must return `true`.
2. Symmetric: If `x.equals(y)` is true, then `y.equals(x)` must also be true.
3. Transitive: If `x.equals(y)` and `y.equals(z)` are true, then `x.equals(z)` must be true.
4. Consistent: Multiple invocations of `x.equals(y)` must return the same result as long as neither object is modified.
5. `x.equals(null)` must return `false`.

# hashCode()
Hashcodes are used to improve the performance of large collections. Collections such as `HashMap` and `HashSet` use hashcodes to store and retrieve objects. However, hashcodes are not necessarily unique for each object.
  

--------

## Coping with changing requirements

-  In the context of a farm-inventory application, you have to implement a functionality to filter green apples from a list.

    ```java
    public static List<Apple> filterGreenApples(List<Apple> inventory) {
        List<Apple> result = new ArrayList<>();
        for(Apple apple: inventory){
            if( GREEN.equals(apple.getColor() ) {
                result.add(apple);
            }
        }
        return result;
    }
    ```
-  The highlighted line shows the condition required to select green apples.
-  But now the farmer changes his mind and wants to also filter red apples. What can you do? A naïve solution would be to duplicate your method, rename it as filterRedApples, and change the if condition to match red apples. However, this approach doesn’t cope well with changes if the farmer wants multiple colors.
-  Second attempt: parameterizing the color. To parameterize the color and be more flexible to such changes, what you could do is add a parameter to your method:
    ```java
    public static List<Apple> filterApplesByColor(List<Apple> inventory,
    Color color) {
        List<Apple> result = new ArrayList<>();
        for (Apple apple: inventory) {
            if ( apple.getColor().equals(color) ) {
                result.add(apple);
            }
        }
        return result;
    }
    // You can now make the farmer happy and invoke your method as follows:
    
    List<Apple> greenApples = filterApplesByColor(inventory, GREEN);
    List<Apple> redApples = filterApplesByColor(inventory, RED);
    ```
-  The farmer comes back to you and says, `“It would be really cool to differentiate between light apples and heavy apples. Heavy apples typically have a weight greater than 150 g.”` Wearing your software engineering hat, you realize in advance that the farmer may want to vary the weight. So you create the following method to cope with various weights through an additional parameter:
  ```java
  public static List<Apple> filterApplesByWeight(List<Apple> inventory,
  int weight) {
      List<Apple> result = new ArrayList<>();
      For (Apple apple: inventory){
          if ( apple.getWeight() > weight ) {
              result.add(apple);
          }
      }
      return result;
  }
  ```
-  Third attempt: filtering with every attribute you can think of
  ```java
  public static List<Apple> filterApples(List<Apple> inventory, Color color,
                                         int weight, boolean flag) {
      List<Apple> result = new ArrayList<>();
      for (Apple apple: inventory) {
          if ( (flag && apple.getColor().equals(color)) ||
               (!flag && apple.getWeight() > weight) ){
              result.add(apple);
          }
      }
      return result;
  }

  List<Apple> greenApples = filterApples(inventory, GREEN, 0, true);
  List<Apple> heavyApples = filterApples(inventory, null, 150, false);
  ```

-  Behavior parameterization: You saw in the previous section that you need a better way than adding lots of parameters to cope with changing requirements. Let’s step back and find a better level of abstraction. One possible solution is to model your selection criteria: you’re working with apples and returning a boolean based on some attributes of Apple. For example, is it green? Is it heavier than 150 g? We call this a predicate (a function that returns a boolean). Let’s therefore define an interface to model the selection criteria:
  
  ```java
    public interface ApplePredicate{
        boolean test (Apple apple);
    }

    public class AppleHeavyWeightPredicate implements ApplePredicate {
        public boolean test(Apple apple) {
            return apple.getWeight() > 150;
        }
    }
    public class AppleGreenColorPredicate implements ApplePredicate {
        public boolean test(Apple apple) {
            return GREEN.equals(apple.getColor());
        }
    }
  ```
-  You can see these criteria as different behaviors for the filter method. What you just did is related to the **strategy design pattern** (see http://en.wikipedia.org/wiki/Strategy_pattern), which lets you define a family of algorithms, encapsulate each algorithm (called a strategy), and select an algorithm at run time. In this case the family of algorithms is `ApplePredicate` and the different strategies are `AppleHeavyWeightPredicate` and `AppleGreenColorPredicate`.
-  You need your filterApples method to accept ApplePredicate objects to test a condition on an Apple. This is what behavior parameterization means: the ability to tell a method to take multiple behaviors (or strategies) as parameters and use them internally to accomplish different behaviors.

  ```java
    public static List<Apple> filterApples(List<Apple> inventory,
                                           ApplePredicate p) {
        List<Apple> result = new ArrayList<>();
        for(Apple apple: inventory) {
            if(p.test(apple)) {
                result.add(apple);
            }
        }
        return result;
    }
  ```
-  You can now create different ApplePredicate objects and pass them to the filterApples method. Free flexibility! For example, if the farmer asks you to find all red apples that are heavier than 150 g, all you need to do is create a class that implements the ApplePredicate accordingly.

  ```java
    public class AppleRedAndHeavyPredicate implements ApplePredicate {
            public boolean test(Apple apple){
                    return RED.equals(apple.getColor())
                           && apple.getWeight() > 150;
            }
    }
    List<Apple> redAndHeavyApples = filterApples(inventory, new AppleRedAndHeavyPredicate());
  ```
-  By using lambdas, you can directly pass the expression RED.equals(apple.getColor()) && apple.getWeight() > 150 to the filterApples method without having to define multiple ApplePredicate classes. This removes unnecessary verbosity.

## Anonymous classes

-  Anonymous classes are like the local classes (a class defined in a block) that you’re already familiar with in Java. But anonymous classes don’t have a name. They allow you to declare and instantiate a class at the same time.
-  The following code shows how to rewrite the filtering example by creating an object that implements ApplePredicate using an anonymous class:
  ```java
    List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
        public boolean test(Apple apple){
            return RED.equals(apple.getColor());
        }
    });
  ```
-  Using a `lambda expression`:
  ```java
    List<Apple> result =
    filterApples(inventory, (Apple apple) -> RED.equals(apple.getColor()));
  ```

## Lambda expressions

-  A `lambda expression` can be understood as a concise representation of an anonymous function that can be passed around. It doesn’t have a name, but it has a list of parameters, a body, a return type, and also possibly a list of exceptions that can be thrown. That’s one big definition; let’s break it down:

**Anonymous**— We say anonymous because it doesn’t have an explicit name like a method would normally have; less to write and think about!
**Function**— We say function because a lambda isn’t associated with a particular class like a method is. But like a method, a lambda has a list of parameters, a body, a return type, and a possible list of exceptions that can be thrown.
**Passed around**— A lambda expression can be passed as argument to a method or stored in a variable.
**Concise**— You don’t need to write a lot of boilerplate like you do for anonymous classes.

  ```java
    // Before
    Comparator<Apple> byWeight = new Comparator<Apple>() {
        public int compare(Apple a1, Apple a2){
            return a1.getWeight().compareTo(a2.getWeight());
        }
    };

    // After
    Comparator<Apple> byWeight = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
  ```
-  A lambda expression is composed of parameters, an arrow, and a body.
  -  **A list of parameters**— In this case it mirrors the parameters of the compare method of a Comparator—two Apples.
  -  **An arrow**— The arrow -> separates the list of parameters from the body of the lambda.
  -  **The body of the lambda**— Compare two Apples using their weights. The expression is considered the lambda’s return value.
    
  ```java
    (String s) -> s.length()
    (Apple a) -> a.getWeight() > 150
    (int x, int y) -> {
        System.out.println("Result:");
        System.out.println(x + y);
    }
    () -> 42
    (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())

    // (parameters) -> expression or (parameters) -> { statements; }
  ```
-  Where exactly can you use lambdas? You can use a lambda expression in the context of a functional interface. In the code shown here, you can pass a lambda as second argument to the method filter because it expects an object of type Predicate<T>, which is a functional interface.

## Functional interface

-  a **functional interface** is an interface that specifies exactly one abstract method.
-  Example of Functional Interfaces
  ```java
    public interface Comparator<T> {
        int compare(T o1, T o2);
    }
    public interface Runnable {
        void run();
    }
  ```
-  An interface is still a functional interface if it has many default methods as long as it specifies only one abstract method.
-  Lambda expressions let you provide the implementation of the abstract method of a functional interface directly inline and treat the whole expression as an instance of a functional interface (more technically speaking, an instance of a concrete implementation of the functional interface).
-  The signature of the abstract method of the functional interface describes the signature of the lambda expression. We call this abstract method a **function descriptor**.
-  `Lambda expression` can be assigned to a variable or passed to a method expecting a functional interface as argument, provided the lambda expression has the same signature as the abstract method of the functional interface.
-  functional interfaces are generally annotated with @FunctionalInterface. This annotation is used to indicate that the interface is intended to be a functional interface and is therefore useful for documentation. In addition, the compiler will return a meaningful error if you define an interface using the @FunctionalInterface annotation, and it isn’t a functional interface. @FunctionalInterface annotation isn’t mandatory, but it’s good practice to use it when an interface is designed for that purpose. You can think of it like the @Override notation to indicate that a method is overridden.
-  Functional interfaces are useful because the signature of the abstract method can describe the signature of a lambda expression. The signature of the abstract method of a functional interface is called a **function descriptor**.
-  The Java library designers for Java 8 have helped you by introducing several new functional interfaces inside the java.util.function package.

### Predicate

-  The `java.util.function.Predicate<T>` interface defines an abstract method named test that accepts an object of generic type `T` and returns a `boolean`.
-  Using the in-built Predicate<T> instaead of a custome FI.
    ```java
      @FunctionalInterface
      public interface Predicate<T> {
          boolean test(T t);
      }
      public <T> List<T> filter(List<T> list, Predicate<T> p) {
          List<T> results = new ArrayList<>();
          for(T t: list) {
              if(p.test(t)) {
                  results.add(t);
              }
          }
          return results;
      }
      Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
      List<String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate);
    ```
### Consumer

-  The `java.util.function.Consumer<T>` interface defines an abstract method named accept that takes an object of generic type `T` and returns no result (`void`). You might use this interface when you need to access an object of type T and perform some operations on it.
    ```java
      @FunctionalInterface
      public interface Consumer<T> {
          void accept(T t);
      }
      public <T> void forEach(List<T> list, Consumer<T> c) {
          for(T t: list) {
              c.accept(t);
          }
      }
      forEach(
               Arrays.asList(1,2,3,4,5),
              (Integer i) -> System.out.println(i)
             );
    ```
    
### Function

-  The `java.util.function.Function<T, R>` interface defines an abstract method named apply that takes an object of generic type `T` as input and returns an object of generic type `R`. You might use this interface when you need to define a lambda that maps information from an `input` object to an `output` (for example, extracting the weight of an apple or mapping a string to its length).
    ```java
      @FunctionalInterface
      public interface Function<T, R> {
          R apply(T t);
      }
      public <T, R> List<R> map(List<T> list, Function<T, R> f) {
          List<R> result = new ArrayList<>();
          for(T t: list) {
              result.add(f.apply(t));
          }
          return result;
      }
      // [7, 2, 6]
      List<Integer> l = map(
                             Arrays.asList("lambdas", "in", "action"),
                             (String s) -> s.length()
                     );
    ```

### Primitive specializations

-  Boxed values are a wrapper around primitive types and are stored on the heap. Therefore, boxed values use more memory and require additional memory lookups to fetch the wrapped primitive value.
-  Java 8 also added a specialized version of the functional interfaces we described earlier in order to avoid autoboxing operations when the inputs or outputs are primitives. For example, in the following code, using an IntPredicate avoids a boxing operation of the value 1000, whereas using a Predicate<Integer> would box the argument 1000 to an Integer object:
  
  ```java
    public interface IntPredicate {
        boolean test(int t);
    }
    IntPredicate evenNumbers = (int i) -> i % 2 == 0;
    evenNumbers.test(1000);
    Predicate<Integer> oddNumbers = (Integer i) -> i % 2 != 0;
    oddNumbers.test(1000);
  ```

### Type checking

-  The type of a lambda is deduced from the context in which the lambda is used. The type expected for the lambda expression inside the context (for example, a method parameter that it’s passed to or a local variable that it’s assigned to) is called the target- type.
  
  ```java
    List<Apple> heavierThan150g = filter(inventory, (Apple apple) -> apple.getWeight() > 150);
  ```

- The type-checking process is deconstructed as follows:
  - First, you look up the declaration of the filter method.
  - Second, it expects, as the second formal parameter, an object of type Predicate<Apple> (the target type).
  - Third, Predicate<Apple> is a functional interface defining a single abstract method called test.
  - Fourth, the test method describes a function descriptor that accepts an Apple and returns a boolean.
  - Finally, any argument to the filter method needs to match this requirement.
  
### Type inference

  ```java
    List<Apple> greenApples = filter(inventory, apple -> GREEN.equals(apple.getColor()));

    Comparator<Apple> c = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
    Comparator<Apple> c = (a1, a2) -> a1.getWeight().compareTo(a2.getWeight());
  ```


### Composing lambda expressions

-  Several functional interfaces in the Java 8 API contain convenience methods. Specifically, many functional interfaces such as Comparator, Function, and Predicate that are used to pass lambda expressions provide methods that allow composition.
-  For example, you can combine two predicates into a larger predicate that performs an or operation between the two predicates. You can also compose functions such that the result of one becomes the input of another function.
-  The `Predicate` interface includes three methods that let you reuse an existing Predicate to create more complicated ones: `negate`, `and`, and `or`.
  
  ```java
    Predicate<Apple> notRedApple = redApple.negate();
    Predicate<Apple> redAndHeavyApple = redApple.and(apple -> apple.getWeight() > 150);
    Predicate<Apple> redAndHeavyAppleOrGreen = redApple.and(apple -> apple.getWeight() > 150)
                                                       .or(apple -> GREEN.equals(a.getColor()));

  // Note that the precedence of methods and and or in the chain is from left to right
  // So a.or(b).and(c) must be read as (a || b) && c. Similarly, a.and(b).or(c) must be read as as (a && b) || c
  ```

## Streams

-  Streams are “a sequence of elements from a source that supports data-processing operations.” . Its an update to the Java API that let you manipulate collections of data in a declarative way (you express a query rather than code an ad hoc implementation for it). Streams can be processed in parallel transparently, without you having to write any multithreaded code
  ```java
    import static java.util.Comparator.comparing;
    import static java.util.stream.Collectors.toList;
    List<String> lowCaloricDishesName =
                   menu.stream()
                       .filter(d -> d.getCalories() < 400)
                       .sorted(comparing(Dish::getCalories))
                       .map(Dish::getName)
                       .collect(toList());
  ```
-  The code is written in a declarative way: you specify what you want to achieve (filter dishes that are low in calories) as opposed to specifying how to implement an operation.
-  We first get a stream from the list of dishes by calling the stream method on menu. The data source is the list of dishes (the menu) and it provides a sequence of elements to the stream. Next, you apply a series of data-processing operations on the stream: filter, map, limit, and collect. All these operations except collect return another stream so they can be connected to form a pipeline, which can be viewed as a query on the source. Finally, the collect operation starts processing the pipeline to return a result (it’s different because it returns something other than a stream—here, a List). No result is produced, and indeed no element from menu is even selected, until collect is invoked.

### Streams vs. Collections
-  A collection is an in-memory data structure that holds all the values the data structure currently has—every element in the collection has to be computed before it can be added to the collection.
-  By contrast, a stream is a conceptually fixed data structure (you can’t add or remove elements from it) whose elements are computed on demand. This gives rise to significant programming benefits. Its simple to construct a stream containing all the prime numbers (2, 3, 5, 7, 11, . . .) even though there are an infinite number of them. The idea is that a user will extract only the values they require from a stream and these elements are produced—invisibly to the user—only as and when required.

### External vs. internal iteration

-  Using the Collection interface requires iteration to be done by the user (for example, using for-each); this is called external iteration. The Streams library, by contrast, uses internal iteration—it does the iteration for you and takes care of storing the resulting stream value somewhere; you merely provide a function saying what’s to be done.
  ```java
    List<String> names = new ArrayList<>();
    for(Dish dish: menu) {
        names.add(dish.getName());̨̨
    }

    List<String> names = new ArrayList<>();
    Iterator<String> iterator = menu.iterator();
    while(iterator.hasNext()) {
        Dish dish = iterator.next();
        names.add(dish.getName());
    }

    List<String> names = menu.stream()
                         .map(Dish::getName)
                         .collect(toList());
  ```

### Stream operations

-  The streams interface in java.util.stream.Stream defines many operations. They can be classified into two categories.
-  Stream operations that can be connected are called `intermediate operations`, and operations that close a stream are called `terminal operations`.
-  Intermediate operations such as filter or sorted return another stream as the return type. This allows the operations to be connected to form a query. What’s important is that intermediate operations don’t perform any processing until a terminal operation is invoked on the stream pipeline—they’re lazy.
-  To summarize, working with streams in general involves three items:

  -  A data source (such as a collection) to perform a query on
  -  A chain of intermediate operations that form a stream pipeline
  -  A terminal operation that executes the stream pipeline and produces a result
  -  The idea behind a stream pipeline is similar to the builder pattern (see http://en.wikipedia.org/wiki/Builder_pattern). In the builder pattern, there’s a chain of calls to set up a configuration (for streams this is a chain of intermediate operations), followed by a call to a build method (for streams this is a terminal operation).

-  The terminal operations you’ve seen either return a boolean (allMatch and so on), void (forEach), or an Optional object (findAny and so on). You’ve also been using collect to combine all elements in a stream into a List.
-  We can combine elements of a stream to express more complicated queries such as “Calculate the sum of all calories in the menu,” or “What is the highest calorie dish in the menu?” using the reduce operation. Such queries combine all the elements in the stream repeatedly to produce a single value such as an Integer. These queries can be classified as **reduction operations** (a stream is reduced to a value).
  ```java
    int sum = numbers.stream().reduce(0, (a, b) -> a + b);
  ```



