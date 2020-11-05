---
title: 'Среды ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: Сведения о средах в :::no-loc(Blazor):::, в том числе о настройке среды для приложения :::no-loc(Blazor WebAssembly):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/environments
ms.openlocfilehash: 61d46e0bd83d8bd82bf7faaf9d8f2fecbacc2ffa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056040"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="5d9bc-103">Среды ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="5d9bc-103">ASP.NET Core :::no-loc(Blazor)::: environments</span></span>

> [!NOTE]
> <span data-ttu-id="5d9bc-104">Эта статья относится к :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-104">This topic applies to :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="5d9bc-105">Общие рекомендации по настройке приложений ASP.NET Core см. в разделе <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5d9bc-106">При локальном запуске приложения среда по умолчанию имеет значение Development.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="5d9bc-107">При публикации приложения среда по умолчанию имеет значение Production.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="5d9bc-108">Размещенное приложение :::no-loc(Blazor WebAssembly)::: выбирает среду на сервере через ПО промежуточного слоя, которое передает данные о среде в браузер, добавляя заголовок `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-108">A hosted :::no-loc(Blazor WebAssembly)::: app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="5d9bc-109">В заголовке содержится среда.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-109">The value of the header is the environment.</span></span> <span data-ttu-id="5d9bc-110">Размещенное приложение :::no-loc(Blazor)::: и серверное приложение совместно используют одну и ту же среду.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-110">The hosted :::no-loc(Blazor)::: app and the server app share the same environment.</span></span> <span data-ttu-id="5d9bc-111">Дополнительные сведения, в том числе о настройке среды, см. на странице <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5d9bc-112">Для автономного приложения, выполняемого локально, сервер разработки добавляет заголовок `blazor-environment`, чтобы указать среду разработки.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="5d9bc-113">Чтобы указать среду для других сред размещения, добавьте заголовок `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="5d9bc-114">В следующем примере для служб IIS добавьте пользовательский заголовок в опубликованный файл `web.config`.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-114">In the following example for IIS, add the custom header to the published `web.config` file.</span></span> <span data-ttu-id="5d9bc-115">Файл `web.config` находится в папке `bin/Release/{TARGET FRAMEWORK}/publish`.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="5d9bc-116">Сведения об использовании пользовательского файла `web.config` для служб IIS, который не перезаписывается при публикации приложения в папку `publish`, см. в разделе <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="5d9bc-117">Чтобы получить среду приложения в компоненте, вставьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> и прочтите свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>:</span><span class="sxs-lookup"><span data-stu-id="5d9bc-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="5d9bc-118">Во время запуска <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> раскрывает <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> с помощью свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, которое позволяет разработчикам реализовать в своем коде логику для конкретной среды:</span><span class="sxs-lookup"><span data-stu-id="5d9bc-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="5d9bc-119">Следующие удобные методы расширения допускают проверку текущей среды на признак среды разработки, тестирования, коммерческой среды или пользовательской среды:</span><span class="sxs-lookup"><span data-stu-id="5d9bc-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="5d9bc-120">Свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> можно использовать во время запуска, если служба <xref:Microsoft.AspNetCore.Components.NavigationManager> недоступна.</span><span class="sxs-lookup"><span data-stu-id="5d9bc-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5d9bc-121">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5d9bc-121">Additional resources</span></span>

* <xref:fundamentals/environments>
