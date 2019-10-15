# Data Basics

This provides a very basic introduction to some of the everyday value data types you will come across in C#, and some ways you can work with them. Though _everything_ in C# can be boiled down to an object, many common data types are _stored by value_ - explained more below. These are the data types that these notes focus on, as well as _strings_, which are **not** stored by value, but in some ways behave as though they are.

## Data types

In C#, we can divide the built in data types in two depending on whether they are stored as _values_, or as _references_.

Data types stored by _value_ are:

- _booleans_
- _integral numeric types_
- _floating-point numeric types_
- _char_

_Structs_ and _enums_ are also stored by value, but will be discussed at a later time.

Value types are also referred to as _simple_ or _primitive_ types. A variable of a value type contains a specific value of that type (eg: `int a = 32`). When you assign a new value to a variable of this type, the value is copied. The simple types are identified by a keyword, which is an alias for the corresponding .NET type. For example, `int` is an alias for `System.Int32`.

Value types cannot be `null` by default - they may be assigned default values under some conditions.

### Booleans

The default value of a bool variable is `false`. We can assign a boolean literal, invoke bool with the new keyword, or assign the value of an evaluation:

```csharp
bool isTrue = true; // will log true to console
bool isFalse = new bool(); // will give the default value of false
bool isAlsoTrue = (5 > 0) // right hand side is evaluated before assignment
```

Only bool values can be used when conditions are evaluated in `if`, `while` and other statements. 

```csharp
int x = 123;

if (x)   // Error: "Cannot implicitly convert type 'int' to 'bool'" - the compiler will not allow you to run this statement
{
    Console.Write("The value of x is nonzero.");
}

if (x != 0)   // The C# way
{
    Console.Write("The value of x is nonzero.");
}
```

C# supports three-valued boolean logic, which is used by some databases. To declare a three-value boolean we use `bool?`, with a default value of `null`. This means the boolean can be nullable, and its behaviour is described in detail here: https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#nullable-boolean-logical-operators.

### Numbers

Unlike in JavaScript, where there is little variety in the world of numbers, C# offers a wide variety of value types suitable for different situations.

#### Integral Numeric types

Whole numbers, positive and negative, fall under Integral Numeric types. C#, unlike JavaScript, can handle large numbers well, which is because of the variety of memory assigned to store each type. The most common integral numeric type is `int`, in which you can store a number from -2,147,483,648 to 2,147,483,647. Other commonly used integral types are:

- `long`, with storage between -9,223,372,036,854,775,808 and 9,223,372,036,854,775,807 
- `short`, storing between -32,768 and 32,767

The default value of an `int` value is 0.

```csharp
var defaultValue = new int(); // defaults to 0
```

If we do not use a keyword to declare the type of an integer we want to store the C# compiler will automatically assign the first type which can store the integer we want:

1. `int`
2. `unit`
3. `long`
4. `ulong`

If we attempt to store a number larger than the max value allowed, a compiler error will occur.

An integral type with smaller storage capacity is implicitly converted into one with higher storage capacity when required. This is called _widening conversion_. For example, `int` can be implicitly converted into a `long` because the range of `int` is a proper subset of `long`. Implicit conversion can also happen between an integral and a floating-point type, but not the other way around. 

An exhaustive list of the possible integral numeric types  and further information can be found here: https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types.

#### Floating-point Numeric types

All numbers with a float fall under the floating-point numeric types, in addition positive and negative 0, positive and negative infinity and NaN can be stored as well. There are three pre-defined floating-point types, which have a different approximate range allocated to them, and due to this different memory size allocated as well. These are:

- `float` - range: ±1.5 x 10−45 to ±3.4 x 1038, with precision ~6-9 digits
- `double` - range: ±5.0 × 10−324 to ±1.7 × 10308, with precision ~15-17 digits
- `decimal` - range: ±1.0 x 10-28 to ±7.9228 x 1028, with precision 28-29 digits

The `decimal` type has most precision, though smaller range, which makes it suitable for financial and monetary calculations. Each of the three types has a `MinValue` and `MaxValue` constants. In addition to these, the `double` type provides `Double.NaN`, `Double.NegativeInfinity` and `Double.PositiveInfinity`

The default value of floating-point types is once again 0. Widening conversion is possible between `float` and `double` as no precision is lost and `float` is a subset of `double`. 

Integrals and float-points can be mixed in arithmetics following the following rule: if one of the numbers is a `double`, the evaluation is cast to a `double`, and if there is no `double` but one of the numbers is a `float`, the result is a `float`.

#### Working with numbers

All standard operations are available (+, -, *, /), with the same order of priority as in mathematics (multiplication and division first). However, when dividing integral numbers, even if we would expect the operation to result in a floating-point number, we receive an integer instead. The docs describe this as 'interesting behaviour'.

The modulo operator `%` is used to find a remainder, as it is in JavaScript.

Another thing to keep in mind is the limits to C# numbers - both integral and floating-point ones have limits, or minimum and maximum values they can hold. If an operation results in a value over the max value or under the min value, *overflow* or *underflow* occurs respectively. For example if we added 3 to the max value the result will be the min value + 2.

The `System.Math` offers a wide range of methods for manipulating and calculating numbers, across arithmetics, trigonometry and logarithms, such as `Math.Abs()`, `Math.Log10()`, `Math.Truncate()` etc. 

### Chars

The `char` type encompasses all Unicode characters in the range U+0000 to U+FFFF, and is used to represent a single character from that range. 

Variables which contain a value from the char type can be expressed in the following ways:

```csharp
char x = "X"; //character literal
char x2 = "\x0058"; // hexadecimal
char x3 = (char)88; //cast from integral type
char x4 = "\u0058"; // unicode

// all the above will print the letter "X" if written to the console
```

The default value of char '\0', a character constant called 'nul'. Not 'null'. 'nul'.

There are many `char` methods, including `ToString()`, which will convert the `char` to a single character string.

### Strings

The _string_ data type is stored by reference, but is a bit of a special case. It has added properties for equality operators, which make them behave like a value type when compared. A string type can represent zero or more Unicode characters.

`+` and `+=` can be used for the concatenation of strings.

Just like in JavaScript, strings in C# are _iterable_ and we can use the `[]` for read only access to a character at a designated index position. They are also _immutable_, even though the way we work with them may mislead us to think they can be. Under the hood, in the below example, a brand new variable 'b' is created to hold the outcome of the concatenation, with the old one becoming viable for garbage collection. 

```csharp
string b = "h";
b += "ello";
```

We can have a string as a string literal using `""`, which allows us to escape characters or write in unicode. We can also have a verbatim string literal using `@` before the quotations, in which case we cannot escape characters. This many be useful when writing file paths, for example. C# does not use single quotes, therefore to use double quotes in a string we have to double them: `"""Hello!"" they shouted back."` will output: `"Hello!" they shouted back.`.

To evaluate a variable inside a string we use `$` _before_ the `""` and `{}` around the word we want to be evaluated as a variable, like so: `$"Hello, my name is {name}"`, where `name` is a variable.

#### Concatenation

Below are a few ways in which we can use string concatenation. 

```csharp
// Using `+`:
string hello = "h" + "el" + "lo"

// Using `string.Concat()`:
var strings = new List<string>(){"concat", "me"};
string concatenated = string.Concat(strings)

// Using `string.Join()`:
`string joined = string.Join(" ", "join", "me") // "join me" - the first argument to .Join() is the character on which we wish to do the joining.`

// Using `StringBuilder()`
StringBuilder sb = new StringBuilder();
sb.Append("This ").Append("is ").Append("a ").Append("built ").Append("string.");
string concatenatedFour = sb.ToString();
```

Note: Strings created using `StringBuilder` may cause some issues with the equality operator `==`. this is because if we had two strings declared using string literals `""` with the same value, their references will also be the same. Using the `StringBuilder` to create a string with the same value gives it a different reference point. 

More on the StringBuilder can be found here: https://docs.microsoft.com/en-us/dotnet/standard/base-types/stringbuilder.

#### String methods

Some of the more frequently used string methods include:

- `Concat()` - appends one or more strings in order of arguments to the end of an existing string

- `EndsWith()` - takes a string, returns a boolean

- `IndexOf()` - takes a character and returns the index position of it, or -1 if not found

- `Insert()` - takes the index at which do do the insertion and the string which needs to be inserted, returns the new string with the insertion completed

- `Remove()` - takes index at which to start removing characters from, returns new strings with characters removed 

- `Replace()` - takes a character which needs to be replaced, and a character which needs to be replaced, return 

- `Split()` - this acts somewhat differently to the JavaScript method. Whilst the return value is a string array of a fixed length consisting of the substrings, the method can take either a character (working the same as in JS), char and int, where the int specifies the maximum number of substrings. It could potentially also accept split options, or a string value instead of a char value as first argument. Docs on the matter: https://docs.microsoft.com/en-us/dotnet/api/system.string.split?view=netframework-4.8

`ToUpper()`, `ToLower()` and `Trim()` also exist, behave in similar way to the equivalents in JavaScript.

You can find a full list of methods on the left hand side under 'Methods' here: https://docs.microsoft.com/en-us/dotnet/api/system.string?view=netframework-4.8.

