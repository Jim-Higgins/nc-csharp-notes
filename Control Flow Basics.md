# Control Flow Basics
These notes focus on some of the ways you can control the flow of your code through logic.
## if statements
_if statements_ branch your code depending on a condition. They work very similarly to JavaScript if statements:
```cs
if (x == 42) 
{
  Console.WriteLine("Time to work out the question...");
}
```
Like in JavaScript, they can be followed by `else` and `else if` statements.
It's important to note that in if statements, and across C#, there is no concept _truthy / falsy_ values. The condition must always evaluate to a boolean.
## Loops
Using loops to iterate is common in C#. The keywords `break` and `continue` can be used in any loop:
- `break` will end the execution of the code block and prevent any further iterations.
- `continue` will end the execution of the current code block, but resume it again at the next one.
### `while` loop
The C# `while` loop works exactly the same as JavaScript: 
```csharp
int n = 9;
while (n < 10) // condition when the loop will stop
{
  Console.WriteLine(n); // action
  n++ // step to get closer to the end condition
}
```
### `do... while` loop
A while loop, but the code in the `do` block is executed before the condition is checked, so you are guaranteed one execution. These do also exist in JavaScript. 
```csharp
int n = 0;
do 
{
    Console.WriteLine(n);
    n++;
} while (n < 5);
```
### `for` loop
`for loops` have the same syntax as in JavaScript; an initialiser, condition and iterator make up the expressions.
```csharp
for (int i = 0; i < arr.Length; i++){
  // logic to execute
}
```
### `foreach... in` loop
`foreach` isn't a method on arrays like JavaScript, but can be used on any iterable type, like `Array`s and `List`s. Structurally, it's more similar to the JavaScript `for... in` loop:
```csharp
var fibNumbers = new List<int> { 0, 1, 1, 2, 3, 5, 8, 13 };
int count = 0;
foreach (int element in fibNumbers)
{
    count++;
    Console.WriteLine($"Element #{count}: {element}");
}
```
### switch statements
Switch statements are perhaps more commonly found in C# than JavaScript. At a basic level they work very similarly:
```cs
int score = GetScore();
switch (score)
{
  case 10:
    Console.WriteLine("perfect score!");
    break;
  case 9:
    Console.WriteLine("nearly there!");
    break;
  // etc...
  case 0:
    Console.WriteLine("you really messed that up!");
    break;
  default:
    Console.WriteLine("invalid score");
    break;
}
```
Note that each case requires a `break` to prevent the code from 'falling through' or executing unwanted statements.
Later versions of C# have made `switch`es much more powerful in a number of ways. A useful one variation allows you to define a variable that meets a condition to determine which case is used, using the `when` keyword. This is called _pattern matching_:
```c#
int score = GetScore();
switch (score)
{
  case var x when x > 100:
    Console.WriteLine("you've achieved more than you could ever have imagined!");
    break;
  //etc
}
```
## Expressions
C# has an almost unnecessarily long list of operators: https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/.
Arithmetical ones behave in the same way as in JavaScript, as do the conditional `&&` and `||` operators. In C# there is no `===`, as there is no coercion which happens with `==`. 
An operator you may not have encountered before is called the "null-coalescing" operator `??`. In the following example it returns `x` if it is _not_ null and `y` otherwise: 
`x ?? y`
What is knows as "ternary" operator in JS is knows as "conditional" operator in C#.
## Throwing exceptions
There will be plenty of times where you want to prevent code from executing further, or otherwise control the thread of your code, when methods are used incorrectly. C# manages this by allowing you to `throw` and _exception_.
```cs
void AssignNames(string[] names)
{
    if (names.Length == 0)
    {
        throw new ArgumentException("At least one name must be provided");
    }
}
```
There are many built-in exception classes in C#, and you should use these when appropriate to make your code behaviour more predictable, but you can create your own exceptions too.
In general, if a method throws, then execution of the program stops. This is often desired behaviour - perhaps a network is down, and it would be pointless to continue the runtime. Sometimes, however, you may wish to _try_ a method knowing that in some cases, it will throw; if it does throw, you can _catch_ the error, and do something different:
```cs
string[] attendees = getCurrentAttendees();
try 
{
    AssignNames(attendees)
}
catch (ArgumentException)
{
    Console.WriteLine("Assignment failed as nobody has arrived yet");
}
```
A few other behaviours to take advantage of:
- You can 'stack' catch blocks that could be called with different types of exceptions.
- You can declare a `finally` block that executes code whether the `try` block succeeds, or it `catches` - a good time to close a connection, for example.
- You can _repropagate_ an error by `throw`ing it again in the catch block, at which point it will be caught by the next `try / catch` in the call stack, if such a place exists.
