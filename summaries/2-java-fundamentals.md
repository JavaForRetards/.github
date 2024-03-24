# Lesson 2: Java Fundamentals
In this lesson we covered the basic constructs of the Java programming language, the building blocks that make up the majority of programs. Here's a summary of information about the key concepts of the course, and as usual, some optional links to dwelve deeper into the subject.

## Variables
- A variable is, put simply, a stored value you assign a mnemonic name to. A declaration looks like this: `int x = 1;`. In the example, I declared a variable named `x`, of `int` (integer) type and assigned it a value of `1`.
- In Java, variables are always mutable; this is true in most programming languages, but it's not a given (for instance, Rust makes a distinction between variables and "mutable" variables). You may create a *constant* - a stored unchangeable value with a mnemonic name - by using the keyword `final`, as in `final int x = 1;`.
- Java is a *statically typed* language, meaning that variable types are known at compile time and unchangeable - meaning that a variable that has been assigned to store `int`s cannot store values of other types within the same scope. Examples of *dynamically typed* languages (i.e. langauges in which the type of each variable cannot be known until execution) include Python and Javascript. Statically typed languages allow the compiler to make many assumptions about the code, which allows for far more optimisation and generally results in faster code.
	- Some other statically typed languages, such as Rust, introduce something called "shadowing", which essentially means that you are able to reuse a name within the same scope for a variable of a different type but overwrite it.
	- Some other languages (not Java) introduce something called *duck typing*. The name comes from the saying "if it quacks like a duck, and walks like a duck, then it's probably a duck". The idea is to have typing based on behaviour: if two different types act in a *de facto* equivalent way, then they are considered the same; this is opposed to the far more common *nominative typing* (used in Java), in which two types are considered the same only if they are declared as such.

## Primitives
The basic types of Java are called *primitives*. You can easily spot them because they aren't capitalised.
- `int`, represented in memory as a *signed* (meaning that it can be positive or negative) 32-bit integer. As you might remember, I mentioned that computers are based on representing in binary (aka base two); being a signed integer, it's represented in a form called "two's complement"; I won't get into the details of how it works, all that matters to you is that it halves the highest possible number representable to allow representation of negative values. As a result, `int` can store any value included between -2³¹ and 2³¹-1 (zero is also a value). `int` is by far the most common type you'll encounter, and it's the "default" for integer values: in expressions, integer numbers that are not stored inside typed values are assumed by the compiler to be `int` unless it can deduce otherwiseby the context.
- `short`, represented in memory as a signed 16-bit integer. The same things I said about `int` apply: being signed, it uses two's complement and as a result it can store anything between -2¹⁵ (-32768) and 2¹⁵-1 (32767).
	- If this looks familiar to you, that's because it is. Guess what - block game durability values are stored as `short`s. Thus, by combining negative-durability helmets until the resulting value drops below the lower limit, a *negative overflow* (sometimes improperly called, even by me, an *underflow*) occurs, and thus the number *rolls over* to the highest number representable, creating "infinite" durability helmets. I won't really explain to you why it happens, if you choose to read up on two's complement works it should become clear though.
- `byte`, represented in memory as a signed 8-bit integer. Also two's complement, so it can only store a value between -2⁷ (-128) and 2⁷-1 (127). This one is actually rather useful when you go down to dealing with files transmitted over the internet, as they are generally represented as *byte arrays* (more on arrays later).
- `long`, represented in memory as a signed 64-bit integer. It's used to contain numbers that can't fit in the `int` type, with its staggering range of representable numbers going from -2⁶³ to 2⁶³-1. Usually, you don't need this, but it's good to know it's there.
	- If you ever need to go *above* this, there exists a type called `BigInteger`, which allows for representation of arbitrary-size integer numbers. However, the infinite-range clause comes at the price of performance, so you should be careful about using it for number crunching. I won't get into detail about how it works, I've frankly never used it.
- `boolean`, a type that can only ever contain one of these two values: `true` (1) or `false` (0). You guessed it - it's a single bit. Not in memory, though: for efficiency reason, internally the compiler adds "padding" (several 0s to the left of the number) to represent it as an 8-bit integer. While I did say that each of the two possible values are indeed numbers, you may not do arithmetical operations between `boolean`s and other integer types.
- `char`, representing a single character, for instance `a`; in memory it's stored as an *unsigned* (always positive) 16-bit integer, and as a result its maximum value is 2¹⁶-1 (65535). Each of these numbers is translated on the Unicode table to represent characters. Unlike `boolean`, you may add other number types to `char`, resulting in a different character. This can be surprisingly useful, when used intelligently. Important note: `char` values are always contained within single quotes: `char c = 'a';`.
- `float`, representing a 32-bit *floating point* (aka with decimal figures) number. It's represented in memory as a *single-precision* floating point number according to the IEEE 754 standard. Explaining that to you goes far beyond the scope of this.
- `double`, representing a 64-bit *floating point* number. It's represented in memory as a *double-precision* floating point number according to the IEEE 754 standard. As the name suggests, it allows a much larger range of representable numbers.
	- Similarly to `BigInteger`, there exists a `BigDecimal` type with the same strengths and limitations. I must note, however, that you should *never* use `float` or `double` in the real world, in code representing values where precision is paramount, such as monetary values, and instead should use `BigDecimal`. This is because `float` and `double` are *approximations* and may result in outright loss of values when computing together numbers of vastly different magnitudes (i.e. 1000.0 and 0.0001).
- For `long`, `float` and `double` you may add the first letter of the type to the end of a number to specify that it's to be seen by the compiler as that (i.e. `0.1F`, `3.4D`, `15L`).
- The compiler will, in most cases, stop you from using a primitive variable with no declared value. However, sometimes, you might be able to bypass this; in those situations, the default value is assumed to be 0 (for `char` it's the character with index 0 on the Unicode table, and for `boolean` it's `false`).

### Further reading
- You can find [here](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html) Oracle's official docs about primitives, pretty much a manual page about them. You can read up, but I feel like it's worded in a pretty complicated way, so do it at your own risk.
- The Wikipedia [article](https://en.wikipedia.org/wiki/Two%27s_complement) about two's complement is actually fairly good. Read up if you're curious, but don't despair if you don't get it. It's not strictly necessary. It can be helpful to understand some stuff, but the whole reason humans created computers was to have them crunch numbers in our place.
- You can read up on IEEE 754 on [its Wikipedia article](https://en.wikipedia.org/wiki/IEEE_754). Fair warning: it's complicated. Only single and double precisions are relevant to us.
- If you found the discussion about `float` and `double` interesting, check out the relevant section of the Java Language Specification (the document governing how Java implementations should work) [here](https://docs.oracle.com/javase/specs/jls/se8/html/jls-4.html#jls-4.2.3).

## Objects and memory
Not all variables are of primitive types. There is another entire genre of types, called *objects*. An object is best explained as a "container of data and code". Object type names *should*, according to convention, all start with a capital letter. It's a fair assumption, but as they say: hope for the best, but prepare for the worse. You may even define custom objects, but that's a topic for a later lesson - probably the next one.
- An example of object type is `String`. `String`s represent sequences of characters, and are declared between double quotes: `String s = "this is a String";`. You may now recognise that the `"Hello world!"` from the last lesson was in fact a `String`.
- It's time we talked about how all of this is stored in memory. There are two regions of memory that are relevant to us: the *stack* and the *heap*. 
    - The stack gets its name from a common analogy used to explain it. Think of a pile of plates: you can never add another plate at the bottom of the pile, nor can you take one from it. You may only do that from the top. The stack is rather similar: it follows a "first in, last out" philosophy, meaning that you can only add *on top* of the stack (an operation called *pushing*), and only take *from the top* of the stack (an operation called *popping*). The stack is very fast and efficient, but it can only ever work with values whose size in memory (as in, the number of bits that have to be reserved) are known at compile time.
    - The heap is different: it's larger, and it's address-based. You can retrieve a value from any part of the heap, and as a result the heap doesn't need to make assumptions about size. To find a value in the heap, you need to know its location, represented by a *memory address*; in other words, you need a *reference* to the value stored on the heap.
- Primitives are all of known size, so they can be safely stored on the stack. As I mentioned, objects are "containers" of data. As a result, unlike primitives, they do not have a fixed size, and as a result the compiler cannot make assumptions when storing them. Objects are therefore stored on the heap, and only a *reference* to their location is pushed on the stack.
    - As you might've guessed from the names, `BigInteger` and `BigDecimal` are object types. Them being stored on the heap is part of the reason they're slower.
- Objects (some, like `String`, are exceptions) are generally initialised with the `new` keyword. More specifically, `new` calls a specific *constructor* of that object, but we'll see that in detail in the next lesson. For now just know that's a thing.
- Object types all have a default value when initialised: `null`. You can think of `null` as a "refernce to nothing". There are use cases for it: occasionally, you may want to have a way to check whether or not an object has been assigned a value, and you may also want to restore it to its default state. This, however, has blown back spectacularly. One trait of objects, as we'll see in the next lesson, is the existance of shared behaviour between objects of the same type. As `null` is not actually pointing to anything, it cannot have those behaviours. As a result, when a `null` value is put in a situation in which it's expected to act like its nominal type, the program throws the dreaded `NullPointerException`, and all hell breaks loose, often opening gaping security holes. It's gotten to the point where the person who originally invented the concept of `null`, Tony Hoare², dubbed it his "billion-dollar mistake". Like, literally, he was estimating how much money security holes caused by his invention costed humanity. Java chose to also implement `null`, and as a result we'll have to deal with the consequences. We'll see in the future how to mitigate `null`, for now simply stick to using it as little as possible.

### Further reading
- Here's an interesting (not so intellectual, but nonetheless interesting) [article](https://hinchman-amanda.medium.com/null-pointer-references-the-billion-dollar-mistake-1e616534d485) about `null`.

## Operations
Since computers are, at the end of the day, glorified calculators, it stands to reason that one may do operations with variables, through the use of *operators*. There are five big families of operators; most of them can only ever be used with primitives, but there are exceptions¹.
- *Arithmetical operators* are the ones you learned in elementary school. `+` represents a sum, `-` represents a subtraction, `*` a multiplication, `/` a division. To these, add the `%` operator, which represents the *modulo* operator - in other words, it gets you the remainder of a division. Generally speaking, they take as input two values of the same type, usually a number, and output a value also of the same type.
    - `String`s have a special implementation of the `+` operator that allows for string concatenation. For instance, `String s = "hel" + "lo";` results in a value of `"hello"`.
- *Logical operators* are a category of operators that output a `boolean` value. This category includes operators used to compare numbers, such as `==` ("equals"), `!=` ("not equals"), `<` ("lower than"), `>` ("higher than"), `<=` ("lower or equal to"), `>=` ("higher or equal to"). However, operators that compare `boolean`s are also part of this category: `&&` (logical AND, returns `true` only if both values are `true`), `||` (logical OR, returns `true` only if at least one of the values is `true`) and `!` (logical NOT, which returns the inverted value of a *single* boolean value).
    - `==` and `!=` compare only the value stored on the stack. As a result, they are will only return true when used to compare objects if those two objects are *literally* the same - as in, they point to the same heap location. The correct way to compare two objects is using the `equals` method: cfor instance, you may compare two `String`s `s1` and `s2` by doing `s1.equals(s2);`.
    - There's actually one more logical operator, called `instanceof`, which checks whether one object is an *instance* of another. For now, just know it exists, we'll see it in a later lesson.
- *Bitwise operators* are a category of operators that apply changes to the individual bits that make up your numbers. There's a number of these:
    - `&` (bitwise AND) performs an AND operation on the individual bits of each number. As a result, `1 & 2` equals `0`. This is because `1` in binary is `00000001` and `2` is `00000010`. Aligning the two, and performing an AND on each couple of bits, the result is `00000000`, as each comparison will be `false`.
    - `|` (bitwise OR) performs an OR operation on the individual bits of each number. As a result, `1 | 2` equals `3`. This is because `1` in binary is `00000001` and `2` is `00000010`. Aligning the two, and performing an OR on each couple of bits, the result is `00000011`, or `3`.
    - `^` (bitwise XOR) performs a XOR (exclusive OR, which returns `true` only if the two values are different) operation on each individual bit. As a result, `3 ^ 5` equals `6` (3 being `00000011`, 5 being `00000101` and 6 being `00000110`).
    - `~` (bitwise NOT) performs a NOT on each individual bit of a number, inverting it. So, `~1` becomes `11111110`, which is `254` if unsigned and `-2` if signed.
    - `<<` (left shift) "shifts" the binary value of the number to the left by a given value. So, for instance, `1 << 2` is `4`. This is because `1`'s binary value, `00000001`, when shifted to the left by `2` positions, becomes `00000100`, which is `4`.
    - `>>` (right shift) "shifts" the binary value of the number to the right by two. It's the inverse operation to `<<`: `4 >> 2` becomes `1`, logically.
    - `>>>` (unsigned right shift) also shift to the right, interpreting the result as an *unsigned* number. You can read more on the difference between signed and unsigned shift [here](https://stackoverflow.com/questions/2244387/bitwise-shift-operators-signed-and-unsigned), I don't think I can explain it better than this.
- *Unary operators* are essentially shorthands for simple sums. They are `++` and `--`, and are respectively equivalent to `+1` and `-1`. So, if you have `int a = 1;` and then run `a++;` the new value of `a` becomes `2`. Important note: you may put the operator both before and after the variable, so `++a` is also valid. The difference is in the order of operations: when it's put *before*, it first increments and then returns the value; when it's put *after*, the opposite happens. To really see what I mean, try declaring `int a = 0;` and try both `System.out.println(a++);` and `System.out.println(++a);`.
- *Ternary operators* are actually just a shorthand for conditional assignment: `int x = condition ? valueIfTrue : valueIfFalse;`. So, say you have an `i` integer variable. `int x = i > 3 ? 1 : 0;`. So, `x` will be `1` if `i > 3`, `0` otherwise. 

## Accepting input
You'll probably never use this in actual development, but it has its uses when making small console applications. There are a number of ways to do this, but we'll do it with the `Scanner` class. Here's a code sample about accepting input:

```java
Scanner scan = new Scanner(System.in); //System.in represents console input, just like System.out represents console output
String s = scan.nextLine(); //this will take in the next string sent
scan.close(); //by the way, those starting with "//" are called comments and are text ignored by the compiler, useful for leaving notes
```

Instead of using `nextLine()` you may use `nextInt()` to get directly a number instead. You'll have to `import` the `Scanner` class. IntelliJ can do that for you. When it shows `Scanner` in red, move your cursor there and press Alt+Enter, or alternatively left click while holding down Ctrl `Scanner`; IntelliJ will show you options to resolve the problem, which in this case is just a missing import. Press ENTER or click on the relevant menu entry.

## Control flow
So-called "control flow" operations are possibly the most important construct of any programming language. They allow you to execute a certain block of code *only* if a certain condition is `true`. The most basic one is `if`:

```java
boolean check = true;
if(check) {
    //code to execute only if check is true
}
```

You can also add an `else` branch to specify what should happen otherwise:

```java
int i = 1;
if(i > 0) {
    //code to execute if i is higher than 0
} else {
    //code to execute otherwise
}
```

You can also perform multiple *exclusive* checks with `else if`:

```java
int i = -2;
if(i > 0) {
    //code to execute if i is higher than 0
} else if(i >= -3) {
    //code to execute if i is NOT higher than 0, but is higher or equal -3
} else {
    //code to execute if i is neither higher than 0, nor higher or equal to -3
}
```

You can chain as many `else if` branches as you want, but if what you are performing is a simple `==` check, there's a faster way to do it:

```java
int i = 4;
switch(i) {
    case 1:
    case 4:
        //code to execute if i is either 1 or 4
        break; //this is what stops it, otherwise it will enter the next branch
    case 5:
        //code to execute if i is 5
        //break is not specified, so here it enters the next one, also executing the "3" branch
    case 3:
        //code to execute if i is 3
        break;
    default:
        //code to execute if none of the specified cases are true
        break; //since it's the last one, it's actually optional, but i like to always specify it
}
```

## Loops
There are three main types of loop in Java. `while` loops execute a given block of code over and over again, indefinitely, while a certain condition remains `true`. The following code will be execute five times:

```java
int i = 5;
while(i > 0) {
    //some code
    i--;
}
```

Similar, but minutely different, is the `do-while` loop. It's similar, but it performs the check at the end of the loop rather than at the start. I suppose I should mention that `do-while` is minutely more efficient than `while`. This is also executed five times:

```java
int i = 5;
while(i > 0) {
    //some code
    i--;
}
```

Finally, comes the most common one, the `for` loop. It essentially allows to condense the previous code in a single instruction. It behaves like a `while` loop, so it's very minorly less efficient than a `do-while`. Still, it's handier, and really shines when it's time to do more complicated loops.

```java
for(int i = 5; i > 0; i--) {
    //some code
}
```

There's a special type of `for` loop which you'll be able to see in the next section.

## Arrays
An *array* is a fixed-size, ordered sequence of values of the same type. If you're familiar with the concept of vector in mathematics, that's what an array is. Generally, though, you shouldn't refer to arrays as "vectors", to avoid misunderstandings: many languages - Java included - use the term "vector" to refer to a special type of array of growable size, which we won't cover today. Unfortunately, far too many programmers are barely literate, and as a result the subtle difference between the mathematical meaning and the language-specific meaning flies completely over their head.

```java
int[] arr = new int[3]; //create new empty array of 3 elements

//you can write/read elements from an array freely
arr[0] = 1; //the first element has index 0!
arr[1] = 2;
arr[2] = 3;

//if you already know all elements you want in the array as soon as you declare it, you can do this:
int[] arr2 = { 1, 2, 3};

int s = arr2.length; //you can access the array's size like this
```

Historically, declaring arrays of non-constant sizes - that is, sizes unknown at compile time - has been a rather complicated matter. Java simply places all arrays on the heap, giving up on treating them like primitives altogether³, allowing for dynamically computed array sizes:

```java
//the following compiles no problem
for(int i = 0; i < 4, i++) {
    int[] arr = new int[i];
    //do stuff with it
}
```

Finally, let's talk about the enhanced for loop, also called for-each loop. It's a simplified way of iterating through arrays⁴, which can be very useful if you don't actually work with the index, but only ever use it to access the corresponding array value:

```java
String[] arr = { "lol", "lmao", "rofl" };
for(String s : arr) {
    System.out.println(s);
}
```

The output will be each element of the array, placed on a different line. That is because `s`, in each loop, assumes the value of the previous array member.

I didn't really mention this in the lesson, because you're unlikely to use it unless you get really into number crunching, but arrays can have other arrays as members. Introducing: multidimensional arrays:

```java
int[][] mat = new int[2][2];
int[0][1] = 1;
```

A bidimensional array is called a *matrix*, but arrays can have infinite dimensions, though you should be wary about going too deep, as that can get really slow.

## Notes
¹The `==` and `!=` operators can be used on anything, but as we'll see it's not really a good idea to use it on objects. Other operators may be used in some "special" Java types, such as `String`, but this is rare: *operator overloading* - the act of defining custom operator behaviour for custom object types - is not allowed in Java, and is only done for a very small subset of default types.

²Tony Hoare is not exactly inexperienced. He's a brilliant computer scientist, a pioneer with far too many achievements to list, winner of the Turing Award - the greatest award for computer science one can get. Goes to show that even the great ones can fuck up spectacularly.

³Actually, arrays in Java are full-fledged objects. This becomes evident when you think about how you get the size: you call `array.length` - that is, a child value. As a rule of thumb, everything that has children in Java is guaranteed to be an object (but not every object has children).

⁴Actually, it works on all types that implement the `Iterable` `interface`, which includes but is not limited to arrays. More on that in a future lesson.
