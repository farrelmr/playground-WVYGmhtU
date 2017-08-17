# Java8 – Methods in Interfaces

My previous post on [streams](https://www.javabullets.com/2017/02/07/java-8-streams-cookbook/) demonstrates how useful this feature is to Java8. However it created a problem for the API designers. The problem was how to we extend the existing Collection’s API without breaking existing Collections implementations(guava, apache).

The solution was to allow methods in interfaces, meaning that any implementations already carry an implementation of the extension.

A good example is –

```java
public interface Collection extends Iterable {
    // ....
    default Stream stream() {
        return StreamSupport.stream(spliterator(), false);
    }
    // ....
}
```

# Rules on default methods

* public
* default – keyword

# Multiple Inheritance

Java always had multiple inheritance in interfaces, but it wasn’t an issue as it didn’t matter which version of the inherited method was implemented.

```java
interface Fred {
    void run();
}
public class OldSkoolMultipleInheritance implements Runnable, Fred {
    @Override
    public void run() {
        System.out.println("Runnable and Fred share the method signature run()");
    }
}
```

It’s more complex when default methods are involved, and java 8 has an order of precedence –

* classes > interfaces
* children > parent
* else select or override implementation

# Examples

```java
interface Interface1 {
    default void defaultMethod() {
        System.out.println("print me!");
    }
}
interface Interface2 {
    default void defaultMethod() {
        System.out.println("no print me!");
    }
}
```

* Classes > Interfaces

```java
public class DefaultMethodMultipleInheritance implements Interface1, Interface2 {
    @Override
    public void defaultMethod() {
        System.out.println("Class wins");
    }
}
```

* Child Interfaces > Parent Interfaces


```java runnable
interface Interface3 extends Interface2 {
    default void defaultMethod() {
        System.out.println("No no print me!!!");
    }
}
public class DefaultMethodMultipleInheritance implements Interface2, Interface3 {
    public static void main(String[] args) {
        new DefaultMethodMultipleInheritance().defaultMethod();
    }
}
```

* Re-implement method in the DefaultMethodsClass and call the specific implementation using super

```java
Interface1.super.newWave(); or Interface2.super.newWave();
```

eg -

```java
public class DefaultMethodMultipleInheritance implements Interface1, Interface2 {
    @Override
    public void defaultMethod() {
        Interface1.super.defaultMethod();
    }
}
```

# static methods in interfaces

Keep static methods specific to your interface in one location –

* public
* static
* Never inherited
