---
title: Конфигурация Blazor ASP.NET Core
author: guardrex
description: Сведения о настройке приложений Blazor, включая параметры приложений, проверку подлинности и конфигурацию ведения журнала.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: b43eae03c71cabbaafa2bc0d704765e89f743279
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103443"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="b299c-103">Конфигурация Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b299c-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="b299c-104">Эта статья относится к Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b299c-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="b299c-105">Общие рекомендации по настройке приложений ASP.NET Core см. в разделе <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b299c-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor<span data-ttu-id="b299c-106"> WebAssembly загружает конфигурацию из следующих источников:</span><span class="sxs-lookup"><span data-stu-id="b299c-106"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="b299c-107">Файлы параметров приложения по умолчанию</span><span class="sxs-lookup"><span data-stu-id="b299c-107">App settings files by default:</span></span>
  * <span data-ttu-id="b299c-108">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="b299c-108">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="b299c-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="b299c-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="b299c-110">Другие [поставщики конфигурации](xref:fundamentals/configuration/index), зарегистрированные приложением.</span><span class="sxs-lookup"><span data-stu-id="b299c-110">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="b299c-111">Не все поставщики подходят для приложений Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b299c-111">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="b299c-112">Сведения о том, какие поставщики поддерживаются для Blazor WebAssembly, можно найти в разделе [Поставщики конфигурации для Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="b299c-112">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="b299c-113">Конфигурация в приложении Blazor WebAssembly видна пользователям.</span><span class="sxs-lookup"><span data-stu-id="b299c-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="b299c-114">**Не храните учетные данные или секреты приложения в конфигурации.**</span><span class="sxs-lookup"><span data-stu-id="b299c-114">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="b299c-115">Дополнительные сведения о поставщиках конфигурации см. в разделе <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b299c-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="b299c-116">Конфигурация параметров приложения</span><span class="sxs-lookup"><span data-stu-id="b299c-116">App settings configuration</span></span>

<span data-ttu-id="b299c-117">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b299c-117">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="b299c-118">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="b299c-118">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="b299c-119">Конфигурация поставщика</span><span class="sxs-lookup"><span data-stu-id="b299c-119">Provider configuration</span></span>

<span data-ttu-id="b299c-120">В следующем примере используется <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> для предоставления дополнительной конфигурации.</span><span class="sxs-lookup"><span data-stu-id="b299c-120">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="b299c-121">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="b299c-121">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="b299c-122">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="b299c-122">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="b299c-123">Для чтения других файлов конфигурации из папки *wwwroot* в конфигурацию используйте <xref:System.Net.Http.HttpClient> для получения содержимого файла.</span><span class="sxs-lookup"><span data-stu-id="b299c-123">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="b299c-124">При использовании этого подхода существующая регистрация службы <xref:System.Net.Http.HttpClient> может использовать локальный клиент, созданный для чтения файла, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="b299c-124">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="b299c-125">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="b299c-125">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="b299c-126">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="b299c-126">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="b299c-127">Конфигурация проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b299c-127">Authentication configuration</span></span>

<span data-ttu-id="b299c-128">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b299c-128">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="b299c-129">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="b299c-129">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="b299c-130">Конфигурация ведения журнала</span><span class="sxs-lookup"><span data-stu-id="b299c-130">Logging configuration</span></span>

<span data-ttu-id="b299c-131">Добавьте ссылку на пакет для [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/).</span><span class="sxs-lookup"><span data-stu-id="b299c-131">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="b299c-132">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b299c-132">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="b299c-133">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="b299c-133">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="b299c-134">Конфигурация построителя узлов</span><span class="sxs-lookup"><span data-stu-id="b299c-134">Host builder configuration</span></span>

<span data-ttu-id="b299c-135">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="b299c-135">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="b299c-136">Кэшированная конфигурация</span><span class="sxs-lookup"><span data-stu-id="b299c-136">Cached configuration</span></span>

<span data-ttu-id="b299c-137">Файлы конфигурации кэшируются для автономного использования.</span><span class="sxs-lookup"><span data-stu-id="b299c-137">Configuration files are cached for offline use.</span></span> <span data-ttu-id="b299c-138">При использовании [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app) файлы конфигурации можно изменять только при создании развертывания.</span><span class="sxs-lookup"><span data-stu-id="b299c-138">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="b299c-139">Изменение файлов конфигурации между развертываниями не работает по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="b299c-139">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="b299c-140">У пользователей есть кэшированные версии файлов, которые они продолжают использовать.</span><span class="sxs-lookup"><span data-stu-id="b299c-140">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="b299c-141">Файлы *service-worker.js* и *service-worker-assets.js* PWA должны быть перестроены при компиляции, что при следующем входе пользователя в сеть сообщает приложению о том, что приложение было развернуто повторно.</span><span class="sxs-lookup"><span data-stu-id="b299c-141">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="b299c-142">Дополнительные сведения об обработке фоновых изменений с помощью PWA см. на странице <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="b299c-142">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
