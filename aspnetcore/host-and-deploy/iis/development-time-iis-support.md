---
title: Development-time IIS support in Visual Studio for ASP.NET Core
author: shirhatti
description: Discover support for debugging ASP.NET Core apps when running behind IIS on Windows Server.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 09/13/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/iis/development-time-iis-support
---
# Development-time IIS support in Visual Studio for ASP.NET Core

By: [Sourabh Shirhatti](https://twitter.com/sshirhatti)

This article describes [Visual Studio](https://www.visualstudio.com/vs/) support for debugging ASP.NET Core apps running behind IIS on Windows Server. This topic walks through enabling this feature and setting up a project.

## Prerequisites

* Visual Studio (2017/version 15.3 or later)
* ASP.NET and web development workload *OR* the .NET Core cross-platform development workload

## Enable IIS

Enable IIS. Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen). Select the **Internet Information Services** checkbox.

![Windows Features showing Internet Information Services checkbox checked as a black square (not a checkmark) indicating that some of the IIS features are enabled](development-time-iis-support/_static/enable_iis.png)

If the IIS installation requires a restart, restart the system.

## Enable development-time IIS support

Launch the Visual Studio installer. Select the **Development time IIS support** component. The component is listed as optional in the **Summary** panel for the **ASP.NET and web development** workload. This installs the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps.

![Modifying Visual Studio features: The Workloads tab is selected. In the Web and Cloud section, the ASP.NET and web development panel is selected. On the right in the Optional area of the Summary panel, there's a checkbox for Development time IIS support.](development-time-iis-support/_static/development_time_support.png)

## Configure the project

Create a new launch profile to add development-time IIS support. In Visual Studio's **Solution Explorer**, right-click the project and select **Properties**. Select the **Debug** tab. Select **IIS** from the **Launch** dropdown. Confirm that the **Launch browser** feature is enabled with the correct URL.

![Project properties window with the Debug tab selected. The Profile and Launch settings are set to IIS. The Launch browser feature is enabled with an address of http://localhost/WebApplication2. The same address is also provided in the App URL field of the Web Server Settings area with Enable Anonymous Authentication enabled.](development-time-iis-support/_static/project_properties.png)

Alternatively, manually add a launch profile to the [launchSettings.json](http://json.schemastore.org/launchsettings) file in the app:

```json
{
    "iisSettings": {
        "windowsAuthentication": false,
        "anonymousAuthentication": true,
        "iis": {
            "applicationUrl": "http://localhost/WebApplication2",
            "sslPort": 0
        }
    },
    "profiles": {
        "IIS": {
            "commandName": "IIS",
            "launchBrowser": "true",
            "launchUrl": "http://localhost/WebApplication2",
            "environmentVariables": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    }
}
```

Visual Studio may prompt a restart if not running as an administrator. If prompted, restart Visual Studio.

## Additional resources

* [Host ASP.NET Core on Windows with IIS](xref:host-and-deploy/iis/index)
* [Introduction to ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module)
* [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module)
