# Testing
As C# is a strictly typed language, we do not have to test for as many things as in JavaScript - for example, each method has a pre-determined return value type, and if it returns another the compiler will not build the code for us. Similarly, if the constructor of a class requires particular parameters to create an instance, the compiler will not let build the code until we provide the respective type of arguments needed. Instead, we can focus on how our code behaves and use the tests to simulate how each class will be used in isolation or conjunction with others. 
C# makes it easy to get started with testing. In this section we will look at testing with the inbuilt `xunit`, and then add `nfluent` for some additional assertions.
## Using a namespace from another project
As discussed previously, a testing project is separate from the source project, but together they can be part of the same solution. But in C# we cannot outright use classes from a different project; we need to reference and include the external project or package. To do this we use the command `dotnet add reference <PATH>` where "path" is the local path to the project we need. 
When you add a project, you can see the reference to it on the `.csproj` file, which is an XML-based variation to the `package.json` file in a NodeJS workspace. All classes marked with `public` access can now be used. 
```xml
  <ItemGroup>
    <ProjectReference Include="..\..\src\PokemonGame\PokemonGame.csproj" />
  </ItemGroup>
```
## xunit
When we run the command `dotnet new` in the terminal, .NET shows the possible kind of projects we can create. One of them is `xunit`, which is a testing suite. When we run `dotnet new xunit` a testing suite boilerplate is already created for us: 
```csharp
using System;
using Xunit;
namespace <TestNamespaceName>
{
    public class UnitTest1
    {
        [Fact]
        public void Test1()
        {
        }
    }
}
```
At the top, we specify the namespaces we will use in the project. Initially these are `System` and `Xunit`. We follow the standard project set up - as we cannot have functions and variables declared in global, we need to include our tests as methods of a class and all classes are part of a namespace. It is convention to name the namespace as "Test<ProjectName>". 
We can rename the name of the class to more accurately reflect what it will be testing - `TestPokemon`, `TestBankAccount`, etc. 
Each test method is prefaced with `[Fact]`. This is part of xunit and tells the test system we will be making assertions in the method, therefore it should run it as a testing method. We can write helper methods if we need to, which wouldn't be considered tests, so prefacing each testing method with `[Fact], is important. The name of the test will be what we see in the console, so naming it something appropriate can be helpful. 
It is common to use the following pattern to write our tests: _arrange - act - assert_, or _AAA_. In the "arrange" part we create all the variables we require for the current test. In "act" we invoke any methods we need to see the outcome of. And in "assert" we use the `Assert` class from `xunit` to assert that the reality of the code matches our expectations of it. 
The following example demonstrates this pattern:
```csharp
using Xunit;
namespace TestPets
{
    public class TestPet
    {
        [Fact]
        public void PetHasBehaviours()
        {
            // arrange
            var cat = new Pet("meow")
            // act
            var sound = cat.makeSound()
            // assert
            Assert.Equal("meow", sound);
        }
    }
}
```
An xunit cheat sheet: https://lukewickstead.wordpress.com/2013/01/16/xunit-cheat-sheet/.
## Unit testing
You should already be familiar with TDD from testing JavaScript, but it's worth re-emphasising the keys benefits you get from unit testing your code with TDD: a framework for designing your class methods. You may find that you have to do a little more in way of designing your classes and how you expect them to work, before you write your first test, in C#. But tests can usually come before implementation, and they can help define the method units of the class and their behaviours. 
## nfluent
If you find the `Assert` class insufficient, or want to mix it up a bit, you can include external packages which will help you testing. To do this, you go through `nuget` - a package manager akin to `npm` for NodeJS. The syntax to add a package is similar to requiring a project: `dotnet add package <PACKAGE_NAME>`. `nuget` will then make the package available for us in the current project and will add a reference to it in the `.csproj` file. 
```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.NET.Test.Sdk" Version="16.0.1" />
    <PackageReference Include="nfluent" Version="2.5.0" />  ----> nfluent is added!
    <PackageReference Include="xunit" Version="2.4.0" />
    <PackageReference Include="xunit.runner.visualstudio" Version="2.4.0" />
  </ItemGroup>
```
The syntax `nfluent` is more semantically descriptive, somewhat like the JS package `chai`:
```csharp
    var integers = new int[] { 1, 2, 3, 4, 5, 666 };
    Check.That(integers).Contains(3, 5, 666);
    var camus = new Person() { Name = "Camus" };
    var sartre = new Person() { Name = "Sartre" };
    Check.That(camus).IsNotEqualTo(sartre).And.IsInstanceOf<Person>();
```
More on nfluent here: http://www.n-fluent.net/.