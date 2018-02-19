---
title: ASP.NET Core Module
author: tdykstra
description: Introduces ASP.NET Core Module (ANCM), an IIS module that lets the Kestrel web server use IIS or IIS Express as a reverse proxy server.
manager: wpickett
ms.author: tdykstra
ms.custom: H1Hack27Feb2017
ms.date: 08/03/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/servers/aspnet-core-module
---
# Introduction to ASP.NET Core Module

By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), and [Chris Ross](https://github.com/Tratcher) 

ASP.NET Core Module (ANCM) lets you run ASP.NET Core applications behind IIS, using IIS for what it's good at (security, manageability, and lots more) and using [Kestrel](kestrel.md) for what it's good at (being really fast), and getting the benefits from both technologies at once. **ANCM works only with Kestrel; it isn't compatible with WebListener (in ASP.NET Core 1.x) or HTTP.sys (in 2.x).** 

Supported Windows versions:

* Windows 7 and Windows Server 2008 R2 and later

[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))

## What ASP.NET Core Module does

ANCM is a native IIS module that hooks into the IIS pipeline and redirects traffic to the backend ASP.NET Core application. Most other modules, such as windows authentication, still get a chance to run. ANCM only takes control when a handler is selected for the request, and handler mapping is defined in the application *web.config* file.

Because ASP.NET Core applications run in a process separate from the IIS worker process, ANCM also does process management. ANCM starts the process for the ASP.NET Core application when the first request comes in and restarts it when it crashes. This is essentially the same behavior as classic ASP.NET applications that run in-process in IIS and are managed by WAS (Windows Activation Service).

Here's a diagram that illustrates the relationship between IIS, ANCM, and ASP.NET Core applications.

![ASP.NET Core Module](aspnet-core-module/_static/ancm.png)

Requests come in from the Web and hit the kernel mode Http.Sys driver which routes them into IIS on the primary port (80) or SSL port (443). ANCM forwards the requests to the ASP.NET Core application on the HTTP port configured for the application, which isn't port 80/443.

Kestrel listens for traffic coming from ANCM.  ANCM specifies the port via environment variable at startup, and the [UseIISIntegration](#call-useiisintegration) method configures the server to listen on `http://localhost:{port}`. There are additional checks to reject requests not from ANCM. (ANCM doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.)

Kestrel picks up requests from ANCM and pushes them into the ASP.NET Core middleware pipeline, which then handles them and passes them on as `HttpContext` instances to application logic. The application's responses are then passed back to IIS, which pushes them back out to the HTTP client that initiated the requests.

ANCM has a few other functions as well:

* Sets environment variables.
* Logs `stdout` output to file storage.
* Forwards Windows authentication tokens.

## How to use ANCM in ASP.NET Core apps

This section provides an overview of the process for setting up an IIS server and ASP.NET Core application. For detailed instructions, see [Host on Windows with IIS](xref:host-and-deploy/iis/index).

### Install ANCM

The ANCM is installed in IIS on Windows Server and in IIS Express on Windows desktop operating systems. For servers and development machines, the ANCM is included in the [.NET Core Windows Server Hosting bundle](https://aka.ms/dotnetcore-2-windowshosting). If installing Visual Studio, the ANCM is automatically installed in IIS Express (and in IIS, if present on the machine).

### .NET Core Windows Server Hosting bundle

The [.NET Core Windows Server Hosting bundle](https://aka.ms/dotnetcore-2-windowshosting) installs the .NET Core Runtime, .NET Core Library, and the ANCM. For more information, see [Install the .NET Core Windows Server Hosting bundle](
xref:host-and-deploy/iis/index#install-the-net-core-windows-server-hosting-bundle).

### Install the IISIntegration NuGet package

# [ASP.NET Core 2.x](#tab/aspnetcore2x)

The [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) package is included in the ASP.NET Core metapackages ([Microsoft.AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore/) and [Microsoft.AspNetCore.All](xref:fundamentals/metapackage)). If you don't use one of the metapackages, install `Microsoft.AspNetCore.Server.IISIntegration` separately. The `IISIntegration` package is an interoperability pack that reads environment variables broadcast by ANCM to set up your app. The environment variables provide configuration information, such as the port to listen on. 

# [ASP.NET Core 1.x](#tab/aspnetcore1x)

In your application, install [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/). The `IISIntegration` package  is an interoperability pack that reads environment variables broadcast by ANCM to set up your app. The environment variables provide configuration information, such as the port to listen on. 

---

### Call UseIISIntegration

# [ASP.NET Core 2.x](#tab/aspnetcore2x)

The `UseIISIntegration` extension method on [`WebHostBuilder`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder) is called automatically when you run with IIS.

If you aren't using one of the ASP.NET Core metapackages and haven't installed the  `Microsoft.AspNetCore.Server.IISIntegration` package, you get a runtime error. If you call `UseIISIntegration` explicitly, you get a compile time error if the package isn't installed.

# [ASP.NET Core 1.x](#tab/aspnetcore1x)

In your application's `Main` method, call the `UseIISIntegration` extension method on [`WebHostBuilder`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder). 

[!code-csharp[](aspnet-core-module/sample/Program.cs?name=snippet_Main&highlight=12)]

---

The `UseIISIntegration` method looks for environment variables that ANCM sets, and it no-ops if they aren't found. This behavior facilitates scenarios like developing and testing on macOS or Linux and deploying to a server that runs IIS. While running on macOS or Linux, Kestrel acts as the web server; but when the app is deployed to the IIS environment, it automatically uses ANCM and IIS.

### ANCM port binding overrides other port bindings

# [ASP.NET Core 2.x](#tab/aspnetcore2x)

ANCM generates a dynamic port to assign to the back-end process. The `UseIISIntegration` method picks up this dynamic port and configures Kestrel to listen on `http://locahost:{dynamicPort}/`. This overrides other URL configurations, such as calls to `UseUrls` or [Kestrel's Listen API](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration). Therefore, you don't need to call `UseUrls` or Kestrel's `Listen` API when you use ANCM. If you do call `UseUrls` or `Listen`, Kestrel listens on the port you specify when you run the app without IIS.

# [ASP.NET Core 1.x](#tab/aspnetcore1x)

ANCM generates a dynamic port to assign to the back-end process. The `UseIISIntegration` method picks up this dynamic port and configures Kestrel to listen on `http://locahost:{dynamicPort}/`. This overrides other URL configurations, such as calls to `UseUrls`. Therefore, you don't need to call `UseUrls` when you use ANCM. If you do call `UseUrls`, Kestrel listens on the port you specify when you run the app without IIS.

In ASP.NET Core 1.0, if you call `UseUrls`, call it **before** you call `UseIISIntegration` so that the ANCM-configured port doesn't get overwritten. This calling order isn't required in ASP.NET Core 1.1, because the ANCM setting overrides `UseUrls`.

---

### Configure ANCM options in Web.config

Configuration for the ASP.NET Core Module is stored in the *web.config* file that's located in the application's root folder. Settings in this file point to the startup command and arguments that start your ASP.NET Core app. For sample *web.config* code and guidance on configuration options, see [ASP.NET Core Module Configuration Reference](xref:host-and-deploy/aspnet-core-module).

### Run with IIS Express in development

IIS Express can be launched by Visual Studio using the default profile defined by the ASP.NET Core templates.

## Proxy configuration uses HTTP protocol and a pairing token

The proxy created between the ANCM and Kestrel uses the HTTP protocol. Using HTTP is a performance optimization where the traffic between the ANCM and Kestrel takes place on a loopback address off of the network interface. There's no risk of eavesdropping the traffic between the ANCM and Kestrel from a location off of the server.

A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source. The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the ANCM. The pairing token is also set into a header (`MSAspNetCoreToken`) on every proxied request. IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value. If the token values are mismatched, the request is logged and rejected. The pairing token environment variable and the traffic between the ANCM and Kestrel aren't accessible from a location off of the server. Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.

## Next steps

For more information, see the following resources:

* [Sample app for this article](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample)
* [ASP.NET Core Module source code](https://github.com/aspnet/AspNetCoreModule)
* [ASP.NET Core Module Configuration Reference](xref:host-and-deploy/aspnet-core-module)
* [Host on Windows with IIS](xref:host-and-deploy/iis/index)
