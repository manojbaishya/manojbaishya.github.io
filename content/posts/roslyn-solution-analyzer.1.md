+++
title = "C# Class View: Analyzing a .NET Solution using Roslyn APIs - Part 1"
description = "Part 1 of this series talks about the backend code of 'C# Class View', a Visual Studio Code extension to introspect the logical structure of your .NET code."
date = 2025-02-07T11:00:00+05:30
tags = ["vscode", "extensions", "lowleveldesign"]
+++

## Introduction

This post of a two-part series outlines the architecture and design principles of the server component for the [C# Class View](https://marketplace.visualstudio.com/items?itemName=manojbaishya.csharp-class-view) extension. The server code can be found in the `server/` directory of the GitHub repository at [github.com/manojbaishya/vscode-csharp-class-view](https://github.com/manojbaishya/vscode-csharp-class-view/tree/main/server).

## Architecture

![Architecture of C# Class View](/roslyn-solution-analyzer/CsharpClassView.webp)

## Code Organization

The server entry point is located in the `CsharpClassView/Server.cs` file, which serves as a standard ASP.NET Core startup class. The gRPC service `RoslynSyntaxTree` is defined within the `CsharpClassView/Protos/syntaxtree.proto` file. This definition specifies the procedure signature:

```protobuf
rpc Parse(SolutionLocation) returns (RoslynSolutionMessage);
```

- **SolutionLocation:** A message that encapsulates the file path of the solution.
- **RoslynSolutionMessage:** A Data Transfer Object (DTO) returned to the client upon successful compilation and analysis of the solution.

ASP.NET Core provides comprehensive support for gRPC services. The `RoslynSolutionService` class extends the auto-generated `RoslynSyntaxTree` service and implements the `Parse()` method. This method is responsible for analyzing the solution's code structure. The heavy lifting of code analysis is delegated to the model file `CsharpClassView/Models/RoslynSolution.cs`.

## Model Structure

The model file `RoslynSolution.cs` defines key components used for solution analysis, including:

- **RoslynSolution:** Represents a .NET solution.
- **RoslynProject:** Encapsulates individual projects.
- **RoslynNamespace:** Represents namespaces within a project.
- **RoslynInterface, RoslynClass, RoslynEnum:** These classes model specific code units and implement the `IRoslynUnit` interface.

## Design

The model file `CsharpClassView/Models/RoslynSolution.cs` represents a .NET Solution containing projects, namespaces, etc. 

The `RoslynSolution` class contains the method `AnalyzeSolutionAsync()`, which performs the following tasks:

1. **Compilation:** Invokes MSBuild to compile the solution.
2. **Analysis:** Utilizes Roslyn APIs to introspect the compilation results.
3. **Data Mapping:** Maps the analysis results into a nested data structure defined as:

   ```csharp
   SortedDictionary<RoslynProject, SortedDictionary<RoslynNamespace, HashSet<IRoslynUnit>>>
   ```

This structure was chosen to ensure uniqueness, efficient lookups, and ordered storage of the data.

The compilation results are initially retrieved as instances of the [`Solution`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.codeanalysis.solution?view=roslyn-dotnet-4.9.0) class, containing a list of [`Projects`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.codeanalysis.project?view=roslyn-dotnet-4.9.0). Each `Project` object includes a list of [`Documents`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.codeanalysis.document?view=roslyn-dotnet-4.9.0). The analysis logic traverses each document, extracts its namespaces, and identifies child code units and their members (such as classes, enums, and interfaces). These elements are then populated into the `RoslynSolution.Projects` dictionary.

Upon successful data population, the `RoslynSolutionMapper` class maps the information from `RoslynSolution.Projects` to a DTO, which is returned to the client.

The server is configured to use HTTP/2 exclusively, ensuring efficient and fast data transfers over the wire.

## Conclusion

In this post, we have seen how an ASP.NET Core gRPC service is implemented for the C# Class View server component. By leveraging Roslyn APIs, the service offers a robust solution for analyzing .NET solutions and returning detailed code structure information to the client.
