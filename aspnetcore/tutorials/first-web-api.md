---
title: Create a Web API with ASP.NET Core and Visual Studio for Windows
author: rick-anderson
description: Build a web API with ASP.NET Core MVC and Visual Studio for Windows
manager: wpickett
ms.author: riande
ms.date: 08/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/first-web-api
---

#Create a web API with ASP.NET Core and Visual Studio for Windows

By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)

This tutorial builds a web API for managing a list of "to-do" items. A user interface (UI) isn't created.

There are 3 versions of this tutorial:

* Windows: Web API with Visual Studio for Windows (This tutorial)
* macOS: [Web API with Visual Studio for Mac](xref:tutorials/first-web-api-mac)
* macOS, Linux, Windows: [Web API with Visual Studio Code](xref:tutorials/web-api-vsc)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## Prerequisites

[!INCLUDE[install 2.0](../includes/install2.0.md)]

See [this PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) for the ASP.NET Core 1.1 version.

## Create the project

From Visual Studio, select **File** menu, > **New** > **Project**.

Select the **ASP.NET Core Web Application (.NET Core)** project template. Name the project `TodoApi` and select **OK**.

![New project dialog](first-web-api/_static/new-project.png)

In the **New ASP.NET Core Web Application - TodoApi** dialog, select the **Web API** template. Select **OK**. Do **not** select **Enable Docker Support**.

![New ASP.NET Web Application dialog with Web API project template selected from ASP.NET Core Templates](first-web-api/_static/web-api-project.png)

### Launch the app

In Visual Studio, press CTRL+F5 to launch the app. Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly chosen port number. Chrome, Microsoft Edge, and Firefox display the following output:

```
["value1","value2"]
```

### Add a model class

A model is an object that represents the data in the app. In this case, the only model is a to-do item.

Add a folder named "Models". In Solution Explorer, right-click the project. Select **Add** > **New Folder**. Name the folder *Models*.

Note: The model classes go anywhere in the project. The *Models* folder is used by convention for model classes.

Add a `TodoItem` class. Right-click the *Models* folder and select **Add** > **Class**. Name the class `TodoItem` and select **Add**.

Update the `TodoItem` class with the following code:

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

The database generates the `Id` when a `TodoItem` is created.

### Create the database context

The *database context* is the main class that coordinates Entity Framework functionality for a given data model. This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.

Add a `TodoContext` class. Right-click the *Models* folder and select **Add** > **Class**. Name the class `TodoContext` and select **Add**.

Replace the class with the following code:

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### Add a controller

In Solution Explorer, right-click the *Controllers* folder. Select **Add** > **New Item**. In the **Add New Item** dialog, select the **Web API Controller Class** template. Name the class `TodoController`.

![Add new Item dialog with controller in search box and web API controller selected](first-web-api/_static/new_controller.png)

Replace the class with the following code:

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### Launch the app

In Visual Studio, press CTRL+F5 to launch the app. Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly chosen port number. Navigate to the `Todo` controller at `http://localhost:port/api/todo`.

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]

