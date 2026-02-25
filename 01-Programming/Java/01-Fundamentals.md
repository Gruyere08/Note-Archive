# Java Fundamentals

## Entry point

To Execute Java code you need an entry point.

```java
    public class Main {
        public static void main(String[] args) {
            System.out.println("Hello World");
        }
    }
```

Notes:
public → access modifier
static → belongs to class, not instance
void → returns nothing
String[] args → command-line arguments
Everything lives inside a class
Java is case-sensitive

## Primitive types

| Type    | Size           | Example         |

| ------- | -------------- | --------------- |
| byte    | 8-bit          | `byte b = 1;`   |
| short   | 16-bit         |                 |
| int     | 32-bit         |                 |
| long    | 64-bit         | `10L`           |
| float   | 32-bit         | `10.5f`         |
| double  | 64-bit         | default decimal |
| char    | 16-bit Unicode |                 |
| boolean | true/false     |                 |

### Default values for fields

numbers → 0
boolean → false
objects → null

## Variables and scope

```java
// Variable
int x = 10;
// Constant
final int CONSTANT = 5;
```

- Java is pass-by-value (**IMPORTANT!**)
  - Primitives → copied value
  - Objects → copy of reference

## Control Flow

### Conditional structures

#### If / Switch

```java
if (x > 10) { }
else if (...) { }
else { }
```

#### Switch statement (modern)

```java
switch (value) {
    case 1 -> System.out.println("One");
    case 2 -> System.out.println("Two");
    default -> {}
}
```

#### Loops

```java
//Counter
for (int i = 0; i < 10; i++) {}

//Pre test
while (condition) {}

//Post test
do {} while (condition);
```

enchanced for:

```java
// List loop
for (String s : list) {}
```

## Arrays

### Declaration

```java
int[] arr = new int[5];
int[] arr = {1,2,3};
```

- Fixed size
- Indexed (0-based)
- arr.length → property, NOT method

#### Multidimensional

```java
int[][] matrix = new int[4][5];
```

Jagged arrays allowed.

#### Common arrays operations

- Sorting → Arrays.sort(arr);
- Binary search → Arrays.binarySearch(arr, key);
- Copy → Arrays.copyOf(arr, newLength);
- Compare → Arrays.equals(a, b);

## OOP Core syntax

### Class structure

```java
//Class definition
Public class Person {
    //Field definition
    private String name;
    private int age;

    // Basic constructor
    public Person(int age, String name){
        this.name = name;
        this.age = age;
    }

    //Getter
    public String getName(){return name;}

    //Setter
    public void setAge(int age){this.age = age}

}
```

#### Key concepts

- Encapsulation -> private fields + getters/setters
- `this` -> current instance
- Constructors have no return type
- If no constructor -> default no-arg constructor

### Static vs Instance

- `static` field -> shared across all instances
- `static` method -> cannot access instance fields directly

### Inheritance

```java
class Animal {
    void speak(){
        //Code related to general animal speech
    }
}

class Dog extends animal {
    @Override
    void speak() {
        //Code related to dog speech
    }
}
```

- Single inheritance only
- `super()` -> parent constructor
- `final` method -> cannot override
- `final` class -> cannot extend

### Polymorphism

```java
Animal a = new Dog();
a.speak(); // dinamic dispatch
```

Method resolution is runtime (for overridden methods).

### Interfaces

```java
interface Flyable {
    void fly();
}
```

- All methods implicitly public
- Fields are `public static final`
- Multiple interfaces allowed
- Since Java 8:
  - `default` methods
  - `static` methods

### Abstract Classes

- Can have state
- Can have constructors
- Can have abstract + concrete methods

Use abstract class when:

- Shared behaviour + state

Use interface when:

- Capability contract

#### Object Methods to remember

From `Object` class:

- `toString()`
- `equals()`
- `hashcode()`
- `clone()`
- `finalize()` (deprecated conceptually)
- `getClass()`

If you override `equals()`, you **MUST** override `hashCode() consistently.
