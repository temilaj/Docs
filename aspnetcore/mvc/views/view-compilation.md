---
title: Razor view compilation and precompilation
author: rick-anderson
description: A reference document explaining how to enable MVC Razor view compilation and precompilation in ASP.NET Core applications.
manager: wpickett
ms.author: riande
ms.date: 12/13/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: mvc/views/view-compilation
---
# Razor view compilation and precompilation in ASP.NET Core

By [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor views are compiled at runtime when the view is invoked. ASP.NET Core 1.1.0 and higher can optionally compile Razor views and deploy them with the app&mdash;a process known as precompilation. The ASP.NET Core 2.x project templates enable precompilation by default.

> [!IMPORTANT]
> Razor view precompilation is currently unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0. The feature will be available for SCDs when 2.1 releases. For more information, see [View compilation fails when cross-compiling for Linux on Windows](https://github.com/aspnet/MvcPrecompilation/issues/102).

Precompilation considerations:

* Precompiling views results in a smaller published bundle and faster startup time.
* You can't edit Razor files after you precompile views. The edited views won't be present in the published bundle. 

To deploy precompiled views:

# [ASP.NET Core 2.x](#tab/aspnetcore2x)

If your project targets .NET Framework, include a package reference to [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):

```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.ViewCompilation" Version="2.0.0" PrivateAssets="All" />
```

If your project targets .NET Core, no changes are necessary.

The ASP.NET Core 2.x project templates implicitly set `MvcRazorCompileOnPublish` to `true` by default, which means this node can be safely removed from the *.csproj* file. If you prefer to be explicit, there's no harm in setting the `MvcRazorCompileOnPublish` property to `true`. The following *.csproj* sample highlights this setting:

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=5)]

# [ASP.NET Core 1.x](#tab/aspnetcore1x)

Set `MvcRazorCompileOnPublish` to `true`, and include a package reference to `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`. The following *.csproj* sample highlights these settings:

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]

---

Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) by executing a command such as the following at the project root:

```console
dotnet publish -c Release
```

A *<project_name>.PrecompiledViews.dll* file, containing the compiled Razor views, is produced when precompilation succeeds. For example, the screenshot below depicts the contents of *Index.cshtml* inside of *WebApplication1.PrecompiledViews.dll*:

![Razor views inside DLL](view-compilation/_static/razor-views-in-dll.png)
