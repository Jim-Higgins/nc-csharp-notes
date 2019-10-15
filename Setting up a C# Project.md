# Setting up a C# Project
## Installation
### Installing .NET Core
Visit the following site to download .NET Core for your platform: https://dotnet.microsoft.com/download. Make sure you select the 'Build Apps' button.
In a terminal window use the `dotnet` command to confirm you have installed .NET Core.
### Installing C# extension to VS Code
Open VS Code and go to the extensions tab. Search for C# and install the extension with Microsoft for author. A link to it can be found here: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp.
When you create a project using the `dotnet` command, VSCode should ask you if you would like to apply the extension you just installed. Select yes, and this will create a folder with some system files which will allow you to use the debugger.
If you do not see this notification (bottom right corner, usually), the extension may not have been applied. Restarting VS Code should resolve this.
### Installing Visual Studio
As an alternative, you can install Visual Studio, https://visualstudio.microsoft.com/downloads/, which has a free tier called 'Community'. Make sure you download and install the latest version, so it is compatible with .NET Core. Visual Studio is not available for Linux based machines.
## "Hello, World!"
To start your first project, navigate the terminal to a folder where you would like to create your first "Hello, World!".
Run `dotnet new` in the terminal. This will bring a message up saying you need to tell .NET what kind of project you would like to create and a list of all available projects. Note that amongst the options, there a few called `xunit`, `nunit`, `mstest`, etc - these are testing project templates.
Running a basic console based project with `dotnet new console` will create several files and folders:
- `obj` folder, which .NET uses internally
- `Program.cs`, which will be the main entry point to your project
- each project is has an associated XML file with an extension of `.vbproj`, `.csproj` or `.vcxproj`, where the structure of the project is recorded. This file is used by the compiler to make the code in the project executable, and is not intended to be changed by hand
When you click on the `Program.cs` file, VS Code will prompt you if you would like to apply the "C#" extension, select "Yes". This will create the following folders:
- `bin`, which is used by the debugger
- `.vscode`, which has a couple of `.json` files: `launch.json` and `tasks.json`. You can use the array under the "args" in `launch.json` to provide arguments to `Program.cs` when you start debugging.
### Code breakdown
In `Program.cs` you can see the following code:
```csharp
using System;
namespace <MyProject>
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```
Let's break this code down line by line:
- `using System` is similar to requiring a package in JS. We need access to the System `namespace` to use `Console` later on in the code.
- `namespace <ProjectName>` - the collection of `classes` we will be building within the current project. The default name is that of the project, but you can change that.
- `class Program` - within the namespace, we have a `class` called `Program`. Every project must have a class with this name, which .NET will access when the code is run.
- `static void Main(string[] args)` - this is the method on `Program` which will be ran by .NET when our project is started. To break this down further:
  - `static` means the method will be called on the class itself, not on a specific object instance - not something to worry about at this time.
  - `void` means the method will not be returning anything. In JS this would default to 'undefined', however C# requires us to explicitly determine what the return value of a method will be in advance, and if that method will not return anything, we use the keyword `void`.
  - `Main` is the name of the method, and as this is the entry point to our project, it has to be called `Main` - .NET will look for a static method with this name to trigger when running the program. As in JS, we provide parameters in `()`. Here we determine that we will have an array of strings called `args`, where all arguments passed to our application will be held
- `Console.WriteLine("Hello, World!")` - `Console` is part of the `System` namespace, and has a method of `WriteLine`, which will output whatever we pass as an argument to the console
### Running a project
To run our project and see "Hello, World!" in the console, we can use the command:
`dotnet run`
This command will trigger a two key processes - firstly, the project will be _built_ (aka _compiled_) into _bytecode_. Next, the bytecode will run. If a build is not required, this will be skipped and the code will be ran fairly quickly. Sometimes, if the build required is large, it may take some time to compile.
You can run the `dotnet build` command separately as well. This will be useful when we want to test the code we have written, but do not want to run the entire project.
Alternatively, if you want to run a project from a different folder, you can use `dotnet run <path-to-project>` where the path navigates to the `.csproj` file directory.
## Directory structure
When working in C# you will encounter a set way of organising the functionality you have. When you create an app, website, plug-in, etc, you create a new _project_ to host all your files there - everything, including images, icons, data files, code files, etc. When we run the command `dotnet new console` we create a new project with a console template inside it. We already saw what files this command creates in the previous section. A project can also contain compiler settings, setup configurations and others.
One or more projects can be grouped in _solutions_. A solution is merely a text file with the _.sln_ extension and can be created using `dotnet new sln`. This file contains a variety of build settings, editor settings, etc and is also not intended to be edited by hand. We will use an _.sln_ file when we have a console app project and a testing project for it, so we can build both simultaneously and run the test suite afterwards.
```
<app-name>
  └── src/
      └── Project1/
          └── bin/
          └── obj/
          └── Project1.csproj
          └── Program.cs
          └── class-folders-or-files.cs
  └── test/
      └── Project1-Test/
          └── bin/
          └── obj/
          └── Project1-Test.csproj
          └── test-files.cs
  └── app.sln
```
C# makes it easy to implement agile practices like TDD. The above folder structure shows a generic setup for an application with a testing project. If you are using VS Code, there may also be a folder where VS Code stores setting for this workspace called `.vscode`. The main project and its corresponding test suite are treated as separate projects, or units of code.
### Namespaces
Each C sharp project has multiple classes, and they are all contained within _namespaces_. You can think of namespaces as packages or collections of classes.
Namespaces are capitalised and declared with the keyword `namespace`. The name must be unique to avoid overriding of functionality. Each class you write has to be contained within a namespace. You can have multiple namespaces per project, as well as nested namespaces. More on naming and nesting best practices can be found here: https://docs.microsoft.com/en-us/dotnet/standard/design-guidelines/names-of-namespaces.
in the below example, `System` is a namespace, from which we use the `Console` class.
```cs
using System;
namespace ProjectName
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```
Code can appear in different files but under the same namespace. As far as the C# compiler is concerned, the classes within a namespace are all potentially accessible from each other.
Namespaces can be nested, or their hierarchy can be explicitly expressed with dot notation. For example, some useful data structures in C# can be accessed by `using System.Collections.Generic`. This would mean that in the source code, the desired namespace would have been structured something like this:
```cs
namespace System
{
  namespace Collection
  {
    namespace Generic
    {
      // data structure classes here
    }
  }
}
```
or this:
```cs
namespace System.Collections.Generic
{
  // data structure classes here
}
```
This structure allows for variations of functionality. In the example above, `Console` is a class in `System` namespace, but if we wanted to make our own Console class which behaves differently, we can create a class of Console within our namespace, give it the methods we want, and both Console classes will be available, as they are under different namespaces. That's not a recommended example, but you can imagine a large codebase spanning multiple applications wanting to reuse class names without fear of confusing the compiler - namespaces are useful here.