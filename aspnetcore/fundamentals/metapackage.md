---
title: Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x and later
author: Rick-Anderson
description: The Microsoft.AspNetCore.All metapackage includes all supported ASP.NET Core and Entity Framework Core packages, along with their dependencies.
manager: wpickett
ms.author: riande
ms.date: 09/20/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/metapackage
---

#Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x

This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.

The [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) metapackage for ASP.NET Core includes:

* All supported packages by the ASP.NET Core team.
* All supported packages by the Entity Framework Core. 
* Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core. 

All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package. The default project templates use this package.

The version number of the `Microsoft.AspNetCore.All` metapackage represents the ASP.NET Core version and Entity Framework Core version (aligned with the .NET Core version).

Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the [.NET Core Runtime Store](https://docs.microsoft.com/dotnet/core/deploying/runtime-store). The Runtime Store contains all the runtime assets needed to run ASP.NET Core 2.x applications. When you use the `Microsoft.AspNetCore.All` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application &mdash; the .NET Core Runtime Store contains these assets. The assets in the Runtime Store are precompiled to improve application startup time.

You can use the package trimming process to remove packages that you don't use. Trimmed packages are excluded in published application output.

The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:

[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]
