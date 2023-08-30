# Lesson 3: Methods and Classes
In this lesson we covered the concepts of methods and classes, and first introduced some notions of Object-Oriented Programming. After this lesson, you effectively know all you need to make a functional Java program.

Here's a summary of information about the key concepts of the course, and as usual, some optional links to dwelve deeper into the subject.

## Functions and Methods
- A function is a named series of instructions which may have zero or more input values, called *arguments*, and one output value, called *return value*. For instance, you may have a `sum` function that takes two integer values as *arguments*, and *returns* their sum.
- A method is a function that is associated with a class. For instance, you may use classes to group together functions logically; perhaps you may have a `MathUtils` class that contains a series of functions to aid with calculation, such as the `sum` function from earlier. More on what a class is later; for now just know that, *in Java*, all functions are necessarily methods, as all code must be contained within a class. This is not the case in pretty much all other programming languages (or, at least, I can't think of any others sharing this feature).

You can declare the `sum` method from earlier like this:

```java
int sum(int a, int b) {
	return a + b;
}
```

As you might have guessed, a method can contain any amount of *statements*, but in this case it's small enough to fit into one line. The first `int` indicates the return type; `int a` and `int b` declare the two arguments, stipulating in this case that they must be integers. Methods can also not have a return value: in many cases, it's not needed. You do that by declaring the method with the `void` return type.

Now, remember last lesson's talk about the stack and the heap? Take this example:

```java
public static void main(String[] args) {
	int[] arr = new int[] { 1, 2, 3, 4, 5 };
	sum(arr);
}

//don't think about why this is static for now
static void sum(int[] arr) {
	//this method takes an array as input and sums all of its values
	int res = 0;
	for(int i : arr)
		res += i;
	//then sets the first array's value to the sum
	int[0] = res;
}
```

What do you think printing the array at the end of `main` would show? Answer: it's `[ 15, 2, 3, 4, 5 ]`. Now take this other example:

```java
public static void main(String[] args) {
	int[] arr = new int[] { 1, 2, 3, 4, 5 };
	fun(arr);
}

static void fun(int[] arr) {
	arr = new int[] { 6, 7, 8 };
}
```

What now? If I printed `arr` at the end of `main`, what would be its value? The answer might surprise you: it's going to be unchanged. It will contain `[ 1, 2, 3, 4, 5 ]`. And this:

```java
public static void main(String[] args) {
	int a = 1;
	int b = 2;
	fun(a, b);
}

static void fun(int a, int b) {
	int tmp = b;
	b = a;
	a = tmp;
}
```

What would be the values of `a` and `b` at the end of `main`? They'd also be unchanged. What gives? 

In Java, when you pass a variable as argument, what methods actually receive as arguments is a *copy* of whatever is on the stack for that variable. Remember last lesson? Primitives have their own value stored on the stack, so the last example is unchanged because I only swapped copies, not the actual variables. Arrays and objects store a *reference* to their memory area instead. This means that only the *reference* is copied. In the very first case, we are accessing the reference to change a value of the original array, so the original array changes accordingly. In the second case, we are overwriting the *reference* (not the object itself!) with a new declaration: thus, the original array remains unchanged.

## Classes
Classes are the key to Java. Understand classes well, and you'll have Object-Oriented Programming figured out. In the simplest possible terms, a class is a collection of data and code. In more practical terms, they are a way to represent logical categories for your data. In Java, each class is its own file, unless it's an *inner class* (more on that later).

A class may contain *members* - that is, methods and *fields* (variables) that are tied to each class' *instance*. What does this mean? Think of a class as the blueprints for its *instances* - that is, the objects of the type defined by the class. For example, you might have a `Cat` class, defining what a cat is and what it can do; let's say you'd stipulate in the class that each cat must necessarily have a name (a `String`) and an age (an `int`), and that it should be able to meow. You might then create an instance representing a 5-years-old `Cat` called "Mr. Whiskers", and then another one representing a 3-years-old cat called "Fluffles".

If all cats must have a name and an age, then you must ask whoever is creating the `Cat` instance to specify them. You do that via a special method called *constructor*, which takes as arguments whatever values are required to create the instance and handles whatever extra logic is needed to create it (there isn't any in our example, but you can easily imagine that it could occasionally be needed). Other programming languages also require you to create a method called a *destructor*, but that is not needed in Java¹.

Let's go and implement what I just described:

```java
public class Cat {
	final String name; //good practice to mark as "final" values which won't change
	int age;
	
	//this is what a constructor looks like
	Cat(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	void meow() {
		System.out.println("meow!");
	}
	
	void call() {
		//String.format is more efficient than String concatenation
		System.out.printf("Come here, %s!\n", this.name);
		//this is equivalent to
		//System.out.println("Come here, " + this.name + "!");
	}
	
	void ageUp() {
		this.age++;
	}
}

public class Main {
	public static void main(String[] args) {
		//create the two instances
		Cat whiskers = new Cat("Mr. Whiskers", 5);
		Cat fluffles = new Cat("Fluffles", 3);
		whiskers.call(); //will print "Come here, Mr. Whiskers!"
		fluffles.call(); //will print "Come here, Fluffles!"
	}
}
```

The `this` keyword is used to reference the current instance of the class. Its main purpose is to resolve naming conflicts with local variables (for instance, in the constructor we have arguments with the same name as the class fields; this is standard practice), but I personally like to *always* specify it when accessing class fields. I find it makes the code more readable.

Now that you understand what an instance is, you can finally understand what `static` means. Put simply, it's a keyword to express that a members is associated with the class itself, rather than with any specific instance. For instance, let's add this method to `Cat`:

```java
static void printScientificName() {
	System.out.println("Felis catus");
}
```

As a matter of fact, all cats are Felis catus; it's not a trait specific to either Mr. Whiskers or Fluffles. This method is associated with the *concept* of a Cat, rather than with any specific cat. As a result, we can make it static. The advantage is that, since it's `static`, you don't need to have an instance of `Cat` to call it: you can call it directly from the class itself. Like here:

```java
public class Main {
	public static void main(String[] args) {
		Cat.printScientificName();
	}
}
```

If it wasn't static, I would've needed to create an instance of `Cat` in order to know the scientific name, which is nonsensical as that value remains unchanged for all `Cat`s. Under the hood, on the bytecode level, the difference is that *instance methods* (AKA non-static ones) are all passed an invisible first argument, corresponding to `this`, whereas `static` methods aren't.

Now for *access modifiers*. There are four of them: `public`, `private`, `protected` and *package-private*, which is what you get when you don't specify any access modifier. You can apply access modifiers to both classes and members. Specifically:

- `public` means that it's accessible from anywhere, at any time.
- `private` means that it's only accessible from within the class.
- `protected` means that it's only accessible from within the class *or* a child class (see the next section for details on what that is)
- *package-private* means that it's only accessible from within the class or from classes within the same package.

Congrats, you now have the knowledge you need to dissect the "Hello World" you wrote in the first lesson! I suggest you go back and do that, before proceeding further.

Before we move on to the next section, allow me to specify something. It's generally considered good Java practice to disallow direct access to class fields. They should be made `private` and then *getters* and *setters* should provide access to them. So for a `String` field called `name`, it should be declared as `private`, and have a method `getName()` that returns its value and, if `name` is not `final`, have a method called `setName(String s)` that allows to write a new value to it. This is considered good practice because it allows to force extra behaviour in the getter/setter which the user may skip if the field is public, but many (including me) find this to be needless boilerplate unless there is a very good reason to do it. Ultimately, this one's up to you. 

### Class inheritance
Now comes the interesting part. Suppose that now you also have to keep track of dogs. You'd write a class like this:

```java
public class Dog {
	String name;
	int age;
	
	Dog(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	void bark() {
		System.out.println("woof!");
	}
	
	void call() {
		System.out.printf("Come here, %s!\n", this.name);
	}
	
	void ageUp() {
		this.age++;
	}
	
	static void printScientificName() {
		System.out.println("Canis familiaris");
	}
}
```

And yet, isn't this class near-identical to the `Cat` one? Surely there must be a way to recycle all that code, since it's a bother to write it twice. And even worse, if you were to change how `call()` works, you'd need to change it in two different classes now, which is not exactly handy. This is where class inheritance comes into play: you can have classes inherit behavior from a *superclass*. Much like `this`, the keyword `super` is used to reference the superclass and its methods and fields. 

A child class can also provide its own implementation of all the parent's methods, by *overriding* them. It's good practice (although not a strict requirement) to write `@Override`² just above all methods that override a parent. Keep in mind that you can never override methods that are `private` or `static`; so, with that in mind, we should probably make `printScientificName()` non-static as well. Overridden methods may have a "higher" (as in "more open") access modifier than the one they are overriding, but never a lower one. One method that must always be overridden is the constructor: if the superclass provide one, you must always call it as first statement within the child class' constructor.

In Java, by default, all classes can be extended and all methods can be overridden (*virtual* methods); you can change this behaviour by adding the `final` keyword to your method or class declaration, but you'll want to do this sparingly and only when you're absolutely sure it's unavoidable, as others working with your program may want to extend its features.

Let's create an `Animal` class, stipulating that animals must have a name and an age, and adapt the existing `Dog` and `Cat` classes to inherit from it. And, while I'm at it, I'll add sensible access modifiers to the classes, as you generally don't want everything to be *package-private*.

```java
public class Animal {
	protected final String name;
	protected int age;
	
	protected Animal(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	public void makeSound() {}
	
	public void call() {
		System.out.printf("Come here, %s!\n", this.name);
	}
	
	public void ageUp() {
		this.age++;
	}
	
	public void printScientificName() {}
}

public class Cat extends Animal {
	public Cat(String name, int age) {
		super(name, age); //call parent's constructor
	}
	
	@Override
	public void makeSound() {
		System.out.println("meow!");
	}
	
	@Override
	public void printScientificName() {
		System.out.println("Felis catus");
	}
}

public class Dog extends Animal {
	public Dog(String name, int age) {
		super(name, age);
	}
	
	@Override
	public void makeSound() {
		System.out.println("woof!");
	}
	
	@Override
	public void printScientificName() {
		System.out.println("Canis familiaris");
	}
}
```

Isn't that much cleaner? Now we can do this:

```java
public static void main(String[] args) {
	Animal[] arr = new Animal[3];
	arr[0] = new Cat("Mr. Whiskers", 5);
	arr[1] = new Cat("Fluffles", 3);
	arr[2] = new Dog("Buster", 3);
	
	for(Animal a : arr) {
		a.call();
		a.makeSound();
	}
}
```

That is - we can treat all child classes of `Animal` as if they were instances of `Animal`, and access their shared behavior that way. There are still some problems to be address. First, there are two empty methods (`makeSound()` and `printScientificName()`) in Animal, which can still be called even if the child class has not overridden them with actual behaviour. Second, nothing is stopping people from declaring a `new Animal("Name", 1)` of no species in particular. This is where `abstract` classes come in.

Classes defined as `abstract` can never be used to create instances: their *concrete* child classes must be used instead. Furthermore, `abstract` classes can declare `abstract` methods, which are methods that force their *concrete* (because `abstract` classes can extend each other as well) children to provide implementations for them. 

With this in mind, let's rewrite `Animal`:

```java
public abstract class Animal {
	protected final String name;
	protected int age;
	
	protected Animal(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	public abstract void makeSound();
	
	public void call() {
		System.out.printf("Come here, %s!\n", this.name);
	}
	
	public void ageUp() {
		this.age++;
	}
	
	public abstract void printScientificName();
}
```

Now, attempting to call `new Animal()` or to create a child without overriding `makeSound()` and `printScientificName()` will throw a compilation error.

## Interfaces
Abstract classes have one important limitation: each class can only ever have a *single* superclass. But what if you need to provide behaviour on top of the existing stuff? That's where interfaces come in. An `interface` is rather similar to an abstract class, with some differences:

- Each class may only *extend* one superclass, but may *implement* infinite interfaces.
- All methods in an interface are abstract unless specified otherwise with the `default` keyword (see below).
- An interface may not have any fields - and, as a result, may not provide a constructor which children need to reference.

A common Java convention is to have all interface names start with a capital I, followed by UpperCamelCase, i.e. `IInterfaceName`. Typically, InterfaceName should also be either an adjective or adjective-like.

I should also point out that interfaces can also `extend` each other, to have one interface imply another, but it's a relatively rare case.

Let's put this in practical terms, though. Not all `Animal`s will have a sound, and not everything that makes a sound is an `Animal`. And not everything that has a scientific name is an `Animal`, but all `Animal`s will have one. With that in mind, let's rewrite our example:

```java
public interface ITalkative {
	void makeSound();
}

public interface IScientificallyNameable {
	void printScientificName();
}

public abstract class Animal implements IScientificallyNameable {
	protected final String name;
	protected int age;
	
	protected Animal(String name, int age) {
		this.name = name;
		this.age = age;
	}
		
	public void call() {
		System.out.printf("Come here, %s!\n", this.name);
	}
	
	public void ageUp() {
		this.age++;
	}
}

public class Cat extends Animal implements ITalkative {
	public Cat(String name, int age) {
		super(name, age); //call parent's constructor
	}
	
	@Override
	public void makeSound() {
		System.out.println("meow!");
	}
	
	@Override
	public void printScientificName() {
		System.out.println("Felis catus");
	}
}

public class Dog extends Animal implements ITalkative {
	public Dog(String name, int age) {
		super(name, age);
	}
	
	@Override
	public void makeSound() {
		System.out.println("woof!");
	}
	
	@Override
	public void printScientificName() {
		System.out.println("Canis familiaris");
	}
}
```

As `Animal` implements `IScientificallyNameable`, all its children will implement it implicitly; since `Animal` is `abstract` and does not provide in itself an implementation for `printScientificName()`, which is also `abstract`, its concrete children are all forced to override it. Vice versa, `Dog` and `Cat` both implement `ITalkative`, but `Animal` itself does not. Keeping in mind type boundaries, we should now rewrite the `main` method like this:

```java
public static void main(String[] args) {
	Animal[] arr = new Animal[3];
	arr[0] = new Cat("Mr. Whiskers", 5);
	arr[1] = new Cat("Fluffles", 3);
	arr[2] = new Dog("Buster", 3);
	
	for(Animal a : arr) {
		a.printScientificName(); //we can do this as all Animals are nameable
		a.call(); //this is a method belonging to Animal, so we can do this
		//makeSound() is however part of another interface, so what now?
		if(a instanceof ITalkative) {
			ITalkative t = (ITalkative) a;
			t.makeSound();
			//same thing in a single statement: ((ITalkative) a).makeSound() 
		}
	}
}
```

As you can see in the example above, `x instanceof Y` is a boolean operator that tells you whether `x` can be seen as an instance of `Y`; in other words, it returns `true` if one of the following conditions is met:

- `x` is of type `Y`
- `Y` is a superclass extended by `x`'s type
- `Y` is an interface implemented by `x`'s type.

Even though I haven't used it, interfaces may use the `default` keyword to provide a default implementation of a method. This is equivalent to an abstract class providing a concrete method. It's done like this:

```java
public interface IInterface {
	default void methodWithDefault() {
		//the method's default behaviour
	}
	
	void methodWithoutDefault();
}
```

## Inner classes
Java forces you to have one top-level class per file, and the class' name *must* match the file name. For instance, let's say you have a file containing only this code:

```java
package org.example;

public class SomeClass {
	//pretend there's some contents here
}
```

This file *must* be called `SomeClass.java` and placed within the `org/example` subfolder of your sources root. This file may also never contain other root-level class definitions. That is not not to say that there can only be one class definition per file: classes may contain other classes; this type of class is called an *inner class*.

Inner classes may or may not be `static`; strictly speaking, a non-static inner class always is associated with an instance of its container class, and as a result may directly reference its parent's fields and methods; a `static` inner class, on the other hand, behaves exactly like a normal top-level class (top-level classes are implicitly `static`).

So for instance:

```java
package org.example;

public class SomeClass {
	String containerClassField = "lol";
	
	public class InnerClass {
		String innerClassField = containerClassField; //legal!
	}
	
	public static class StaticInnerClass {
		String staticInnerClassField = containerClassField; //illegal!
	}
}
```

## Enums
Enums are a special type of class of which there only ever exists a specific number of instances, which are always `public` and `static`. Say we want an `enum` to store whether an `Animal` has two or four legs:

```java
public enum LegNumber {
	BIPED, QUADRUPED;
}
```

We can't do much with the `LegNumber` enum as is; let's define behaviour to store the corresponding `int` of legs and a way to print a message saying how many legs this animal has.

```java
public enum LegNumber {
	BIPED(2, "two"),
	QUADRUPED(4, "four");
	
	private final int legNumber;
	private final String legNumberName;
	
	LegNumber(int legNumber, String legNumberName) {
		this.legNumber = legNumber;
		this.legNumberName = legNumberName;
	}
	
	public int getLegNumber() {
		return this.legNumber;
	}
	
	public void printLegNumber() {
		System.out.printf("This animal has %s legs!\n", this.legNumberName);
	}
}
```

Now let's adjust the other classes to work with this `enum`:

```java
public abstract class Animal implements IScientificallyNameable {
	protected final String name;
	protected int age;
	protected final LegNumber legNumber;
	
	protected Animal(String name, int age, LegNumber legNumber) {
		this.name = name;
		this.age = age;
		this.legNumber = legNumber;
	}
		
	public void call() {
		System.out.printf("Come here, %s!\n", this.name);
	}
	
	public void ageUp() {
		this.age++;
	}
	
	public void printLegNumber() {
		this.legNumber.printLegNumber();
	}
}

public class Cat extends Animal implements ITalkative {
	public Cat(String name, int age) {
		super(name, age, LegNumber.QUADRUPED); //cats are all quadrupeds
	}
	
	@Override
	public void makeSound() {
		System.out.println("meow!");
	}
	
	@Override
	public void printScientificName() {
		System.out.println("Felis catus");
	}
}

public class Dog extends Animal implements ITalkative {
	public Dog(String name, int age) {
		super(name, age, LegNumber.QUADRUPED);
	}
	
	@Override
	public void makeSound() {
		System.out.println("woof!");
	}
	
	@Override
	public void printScientificName() {
		System.out.println("Canis familiaris");
	}
}
```

Now calling `printLegNumber()` on an instance of `Cat` or `Dog` will print "This animal has four legs!".

Enums hold one key advantage. As you may remember, you can't really compare objects with `==` and should use `equals()` instead, because `==` only compares the reference. However, for `enum`s only the instances specified by the class author ever exist, so they can be compared with `==`. For the same reason, `enum`s may also be used in `switch` statements.

## Notes
¹Java is a "garbage-collected" language. It means that memory is generally not handled manually by the programmer, but rather by a part of the JVM called the "garbage collector" that automatically free up memory when by deleting unused things. Example of garbage-collected languages are Java and Python; examples of languages with manual memory management are C and C++; others, such as Rust, employ other ways of determining when to free up memory. Despite this, there *are* ways to handle memory manually in Java, to a degree, but if I even cover it in this course, it'll be way down the line, especially because I myself am not an expert. And also because it's extremely unlikely you'll ever need it; I only ever used this feature once, when working with the JNI (a way to make Java and native code work with each other).

²`@Override` is what you call an *annotation*. Put simply, they provide metadata about classes, fields, methods, arguments and more. Some of them are built into the JDK and have special behaviours, but we'll see them over time. As for custom annotations and how they work, well, we'll see them way later, near the end of the Java section. But we'll see them, I promise. I'm something of an expert on the subject, so I want to flex.
