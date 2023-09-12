# Lesson 4: Methods and Classes II
In this lesson we pick up where we left off last time, to cover some more advanced topics related to classes and abstraction. This lesson effectively concludes the part of the course related to syntax. After this, you should be able to effectively read any bit of Java (8) code that comes your way. While this is not the end of the course, you now have the ability to effectively learn by reading what others have made. While a course-like environment helps you pick up the pace, you *do* learn the most on the field: there is no replacement for experience, as I said, but studying actual (good) code in action comes pretty close.

Here's a summary of the topics covered by this lesson, as usual.

## Advanced abstraction
Last lesson, we learned that we children of a class *can* be treated as if they belonged to their parent. As it happens, all classes in Java implicitly extend a special class: `Object`. 

`Object` provides a number of default methods as well, but you needn't concern yourself with those for now. What matters to us is the implication of this: `Object` effectively becomes something capable of storing *any* reference. A variable that is declared as being of `Object` can effectively store any type of value:

```java
Object a = 1; //remember? "Integer" is a class as well, equivalent but separate from "int"! 
Object b = "hello";
Object c = new Object();
Object d = new Scanner();
```

Of course, storing something as `Object` makes methods and data specific to the type inaccessible to you, but they are still there, which means you can regain them simply by *casting* it back to its type:

```java
Object o = new YourClass(); //suppose this is your special class, who contains a "run()" method
//o.run(); //this code is commented out, because it's not valid!
YourClass q = (YourClass) o; //casting is valid, since that's its actual type
o.run(); //works!
```

By doing this you can effectively transfer as `Object` variables of any kind, allowing you to recycle many functions that otherwise would've been bound by specific types. Nonetheless, this is pretty wasteful in many cases: having to cast back forces you to write extra code; not to mention, ot's pretty ugly, and you have no guarantees that what is actually stored inside that variable is of the correct type, which could lead to errors.

In the vast majority of cases, you *won't* need to use `Object` that way. *Usually*, if it comes to that, you are either approaching the problem in the wrong way, or you can instead solve it using *generics*.

### Generics
*Generics* are a feature of Java (and many other languages too, of course) allowing you to treat items of an unknown, but constant throughout the scope, type. Generics can be scoped to methods, but also to whole classes; here's an example of the former, to make things clearer:

```java
public static <T> void functionWithGenerics(T arg) {
	//code here
}
```

That `<T>` line at the start specifies that this function has a single, *unbounded*, generic type. You could've also declared any number of generics within the function with the syntax `<T, S, R>` and so on. Within the function, they'll be treated as unknown, but constant types; while the only features you'll be able to access are those of `Object`, these will be considered to be different types, and as a result you won't be able to assign values of type `T` to variables of type `S`.

In our specific example, `arg` will be considered to be of a hypothetical type, which I named `T`, which will be treated as fixed but unspecified. The actual meaning of `T` is only inferred by the compiler when the function is called, by looking at how you are treating it: in our case, it'll guess it by looking at what we're passing it as `arg` parameter.

```java
functionWithGenerics(1.f);
functionWithGenerics("hello");
```

This code is valid: `T` will be `float` for the duration of the first call, and it will be `String` for the duration of the second. Generics are a powerful feature in abstraction, and really shine when used in classes:

```java
public class YourClass<T> {
	private T fieldOfGenericType;
	
	public YourClass(T arg) {
		this.fieldOfGenericType = arg;
	}
	
	public T getField() {
		return this.fieldOfGenericType;
	}
}
```

You'll be using these far more than you're thinking right now. They are often lifesavers, sparing you tens if not hundreds of code lines that you otherwise would've had to repeat with minimal or even no change at all. We can make them a bit more useful by specifying *boundaries*. 

You can declare *bounded generics*, which are similar to *unbounded* ones, with the `extends` keword: `<T extends SomeClass>`. In this case, `T` must be either `SomeClass` or one of its child classes. Confusingly, it's `extends` instead of `implements` even in interfaces: `<T extends ISomeInterface>`.

Suppose we want to declare an instance of `YourClass<T>`; how would we go about that?

```java
YourClass<String> example = new YourClass("hello");
System.out.println(example.getField()); //prints "hello"
YourClass<Integer> otherExample = new YourClass(1);
System.out.println(example.getField()); //prints "1"
otherExample = example; //this is an error! they aren't the same type!
```

Now let's suppose the opposite situation; we have (let's say we got it the JDK or some other library) some instances of a *parameterised* class - that is, a class with a generic - but really don't care about their generic types at all. In fact, they are getting in the way, because we'd like to store them all in an array - which we can't do, because they are parameterised.

There are actually two solutions to this problem; we can use *raw types* or *wildcards*. Raw types are considered to be bad practice, so you really shouldn't do that, but I'm still showing you because you may encounter that. Both compile and work completely fine, but wildcards are to be preferred as they are more explicit in what they do.

To use the type "raw" essentially means to make a declaration stripped of the generic. It looks something like this:

```java
YourClass example = new YourClass("hello");
```

This still works, as the generic type still exists underneath, but the compiler will warn you about it, because it conceals that `YourClass` is actually a parameterised type. The correct way to go about it would be to use the wildcard `?` symbol:

```java
YourClass<?> example = new YourClass("hello");
```

Wildcards can also be bounded, exactly like generics, to provide the same type of access that generics can give you:

```java
YourClass<? extends CharSequence> example = new YourClass("hello");
//CharSequence is an interface implemented by String, by the way
```

The advantage of doing it like this is that the field within `example` can still be treated as a `CharSequence`, with access to all its methods, whereas with raw types or an unbounded wildcard it would've only been considered an `Object`.

### Local classes
What follows is one of the most hideous features ever produced by the Java language; while it works, using the accursed feature known as *local classes* is a crime in of itself, and should be discouraged at all costs. Put simply, you write a class declaration inside a method, and that class is only considered accessible within the method's scope:

```java
public void badMethod() {
	//some bad code here
	class SomeClass {
		//some bad code here
	}
	//some more bad code here
}
```

Local classes considerably worsen the readability of your code, and I have yet to found a genuine use for them. I've only ever seen them used once in some obscure part of the Minecraft code - of course, if there is something awful you can do in Java, it's common practice over at Mojang.

### Anonymous classes
Also evil, but slightly less since they serve a genuine purpose, are anonymous classes. Anonymous classes allow you to declare instances of abstract classes and interface on the fly and to use them quickly. Let's say you have a class called `YourClassRegistry`, which just stores instances of `AbstractYourClass` via a `register()`. `AbstractYourClass` looks like this:

```java
public class AbstractYourClass {
	public int i = 1;
	
	public void someMethod() {
		System.out.println(implementMe());
	}
	
	abstract String implementMe();
}
```

The layout of the class is pretty simple, and you only ever really need to implement one method. Let's assume that the following is the *only* use case the classes extending `AbstractYourClass` ever see:

```java
public static void main(String[] args) {
	YourClassRegistry registry = new YourClassRegistry();
	
	registry.register(new SomeYourClassChild());
	registry.register(new SomeOtherYourClassChild());
	registry.register(new YetAnotherYourClassChild());
}
```

In this very specific case, writing three classes sounds pretty wasteful. You can instead do something like this:

```java
registry.register(new AbstractYourClass() {
	@Override
	String implementMe() {
		return "hello";
	}
});
```

That class you just declared this way is called an *anonymous* class, because as you can see it doesn't have an actual name. Internally, it's treated like a class called `ParentClass$N`, where `ParentClass` is the class containing the method which in turn contains the declaration, and `N` is an incremental index assigned by the compiler to the various anonymous classes declared within `ParentClass`.

That being said, anonymous classes are considered bad practice in most other cases, so carefully consider your other options before writing one.

## Error handling
Error handling is one of the most important parts of programming. Java's error handling, specifically, leaves much to be desired. Its strongest point is its simplicity; its weakest point is how comically verbose it is. 

Many newbies mistakenly equate errors to bugs: they aren't. Errors are a tool in the hands of the programmer to provide debug information; they may occur because of a bug, but also during the normal execution of a program in some circumstances. Often, errors are deliberately *thrown* by the programmer when an unrecoverable situation occurs, in order to give the reader some idea of what went wrong. 

Suppose your program needs to access a file, and the user inputs an invalid path. That, of course, should yield an error, but it doesn't mean there's a bug in your program. In Minecraft terms, maybe you received a bad or corrupted packet from the server and failed to read it. Doesn't mean there's a problem on your side, but it's still worth throwing an error so the other parts of the program relying on that packet being read know that something is up.

In Java, anything that goes wrong within the program is represented by an instance of `Throwable`, typically containing an error message and often other debug informations. Throwable itself is pretty much never instantiated directly: from its children, the two main types of error come:

- `Error` is the class representing serious underlying problems. They are remarkably rare, as the compiler goes the extra mile to try and prevent these from ever happening at runtime. We are talking about things like `DivideByZeroError` and `StackOverflowError` (thrown, for instance, when you enter an endless loop resulting in your stack being filled up and *overflowing*).
- `Exception` is the class representing errors that can happen during normal execution of your program. If your code can provoke an `Exception`, the compiler will force you to handle it. This is true for all children of `Exception`, *except* for `RuntimeException` and its descendants: this branch of the family contains those exceptions that will just crash the program when they happen, and are often unpredictable at compile time.

It's worth noting that, while you are only forced to handle `Exception`s, you *can* handle any `Throwable` in Java if you so wish. Error handling in Java is done via so-called *try-catch* blocks. They look like this:

```java
try {
	//some code that may cause an exception
} catch(SomeException e) {
	//code handling the exception
}
```

The code within the `try` block will be executed either until completion or until an exception of type `SomeException` (or one of its children types) is thrown. In the second case, the execution of the `try` block is aborted, and the program moves on to the contents of the `catch` block, which are instead skipped in the first case. 

The `catch` block must specify some kind of `Throwable`; anything that is *thrown* within the method that matches the specified type will be caught. As a result, `Exception` will catch every exception that happens during normal execution, and catching `Throwable` will catch literally everything. Nonetheless, this is considered bad practice: you should always only catch strictly what you need. Sometimes, it's better for the program to crash than to continue on in a degraded state.

You can also `catch` multiple types of exception for the same `try` block:

```java
try {
	//some code that may cause an exception
} catch(SomeException e) {
	//code handling the exception
} catch(SomeOtherException e) {
	//code handling the other exception
}
```

Or, if you are going to handle them the same way:

```java
try {
	//some code that may cause an exception
} catch(SomeException | SomeOtherException e) {
	//code handling the exception
}
```

While you can do literally anything in a `catch` block, generally exception handling consists in providing an alternative default value to replace the block that couldn't be completed, or merely logging that the error has happened and moving on, or logging before crashing the program manually.

You can declare your own `Exceptions` like any other class:

```java
public class YourException extends Exception { //you can also extend RuntimeException!
	public YourException() {
		super("some error message");
	}
}
```

Then, in your code, you can simply do `throw new YourException()` to raise an error of your new type. The compiler will force you to write `try` blocks for this error type in all methods, and to declare that the error can happen in the method signature:

```java
public void someMethod() {
	try {
		someOtherMethod();
	} catch(YourException e) {
		//some handling
	}
}

public void someOtherMethod() throws YourException {
	//code here
	throw new YourException();
}
```

If this is undesirable, and you think `YourException` should signal an unrecoverable state or one not worth recovering, you can simply `extend RuntimeException` instead. People will still be able to catch it, if they want to, but they won't be *forced* to anymore.

## Functional Interfaces
A functional interface is a Java interface providing *only one abstract method*; it may still possess any number of non-abstract methods and static fields. You should (but don't *need* to) also annotate it with `@FunctionalInterface`:

```java
@FunctionalInterface
public interface YourFunctionalInterface {
	int doSomething(int i);
	
	default int doSomethingElse(int i) {
		return i*i;
	}
}
```

Why does this specific type of interface matter? Because Java has a special syntax to make it useful, called lambda syntax. Suppose we have a method like this:

```java
public static someMethod(int i, YourFunctionalInterface fun) {
	fun.doSomething(i);
}
```

Functional interfaces allow for things called *lambdas*, which are effectively a special form of anonymous class. Unlike regular anonymous classes, though, lambdas are concise and aren't considered bad practice, so knock yourself out. You can call that method with a lambda like this:

```java
int res = someMethod(3, n -> n + 1);
System.out.println(res); //will print 4!
```

In the example above, I declared a lambda function taking in an integer, which I called `n`, and returning `n + 1`. As you may have noticed, this matches the signature of the abstract method of `YourFunctionalInterface`.

You can use write more elaborate code as well, by just putting braces in your lambda:

```java
int res = someMethod(3, n -> {
	for(int i = 0; i < 4; i++)
		n = n + 1;
	return n;
});
System.out.println(res); //will print 7!
```

You can even pass other functions, provided that they match the signature specified by the functional interface:

```java
public class SomeClass {
	public static void methodThatCalls() {
		System.out.println(someMethod(3, SomeClass::methodGettingCalled)); //will print 7!
	}

	public static int methodGettingCalled(int i) {
		for(int i = 0; i < 4; i++)
			n = n + 1;
		return n;
	}
}
```

You specify the class name only if the methods are static; if they weren't we would've written `this::methodGettingCalled`, and if `methodThatCalls()` wasn't within the same class I would've had to use an instance of `SomeClass` instead.

This is all very handy, and the good news is that you almost never need to write your own functional interfaces. Java provides a lot of built in ones, all within the `java.function` package: `Runnable` (no args, no return), `Supplier` (no args, one return value), `Consumer` (one argument, one return value), `Function` (one argument, one return value), `BiFunction` (two arguments, one return value) and many others.

## Afterword
The contents of this less might seem tremendously abstract to you. Fear not, though: you'll see them at work in the next lesson, which will be focused on some common solutions to problems offered by JDK. For now, focus on truly understanding what you did so far. Next lesson will be comparatively very easy: the worst is behind us.
