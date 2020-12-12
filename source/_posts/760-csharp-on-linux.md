---
title: 'Zero to Project: C# on Linux'
date: 2020-12-11 12:45:50
tags:
---

Continuing with how to setup languages, this time with C#.NET 5.0.

## Installing

- Visual Studio Code is the only officially supported "IDE" for developing C# on Linux. Get it here https://code.visualstudio.com/docs/setup/linux
- Install the C# extension from Microsoft https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
- Install .NET Core https://docs.microsoft.com/en-us/dotnet/core/install/linux-ubuntu
  1. Either use Snap, but note that it will not be confined

         sudo snap install --classic dotnet-sdk

  2. Or install through apt using Microsoft's repo:

         wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
         sudo dpkg -i packages-microsoft-prod.deb; \
         sudo apt-get update; \
         sudo apt-get install -y apt-transport-https && \
         sudo apt-get update && \
         sudo apt-get install -y dotnet-sdk-5.0

`dotnet --version` should print correctly, and that's it. The C# extension will call dotnet from PATH.

## Creating a solution

Create a new solution:

    dotnet new sln -o MySolution

In the folder that was created, create a new CLI project (run in a blank folder):

    dotnet new console -o MyProject #or (dotnet new classlib for a library project)
    dotnet build
    dotnet run

To debug through VS code, create a launch.json automatically and run. This builds the project using `dotnet build` (with whatever is in your `.csproj`) and runs with debug. To disable the lengthy module load logs, add this to the launch configuration:

```js
"logging": {
    "moduleLoad": false
}
```

You can change the main class by adding this to the property group in the `.csproj` under `<PropertyGroup>`:

```xml
<StartupObject>csharp.Day10</StartupObject>
```

### Dependencies

Adding dependencies [is easy](https://docs.microsoft.com/en-us/dotnet/core/tools/dependencies) too, for example to add the YamlDotNet library through NuGet:

    dotnet add package YamlDotNet

This simple adds a dependency to your `.csproj`:

```xml
<ItemGroup>
    <PackageReference Include="YamlDotNet" Version="9.1.0" />
</ItemGroup>
```

### Unit tests

To add unit tests, create an `xunit` project under the same solution with tests in it, and add the original project as a dependency:

    dotnet new xunit -o MyProject.Tests
    dotnet add ./MyProject.Tests/MyProject.Tests.csproj reference ./MyProject/MyProject.csproj
    dotnet sln add ./MyProject.Tests/MyProject.Tests.csproj

Unfortunately, the C# extension must be in the project folder to work well, so making a single .vscode folder for the entire solution is not a good idea.

Now to run it:

- You can click on `Debug test` above a unit test to launch it (or `Debug All Tests` to run a unit test class).
- The [.NET Core Test Explorer](https://marketplace.visualstudio.com/items?itemName=formulahendry.dotnet-test-explorer) can assist in watching many unit tests, but it cannot debug.
- You can create a main method that calls all the unit tests and launch it with a `launch.json` but this is obviously not ideal.

More on [how to organize a .NET Core project](https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-dotnet-test) and [C# Unit tests](https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-dotnet-test). I ended up just using the "debug test" button.

My resulting project is [here](https://github.com/fedidat/AdventOfCode2020/tree/master/csharp).

## I'm new to C#, what's next?

- Take the [language tour](https://docs.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/) to get a basic idea of how things work
- Look at [C# Examples](https://www.csharp-examples.net/)
- From there on use the [the reference](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/) as needed.
