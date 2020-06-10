---
title: "title: 'Конфигурация модели размещения ASP.NET Core Blazor' author: guardrex description: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC."
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.автор: riande ms.custom: mvc ms.дата: 28.05.2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: e3b8b91a570210e77f307c49f7be21eeab714daa
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355114"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="98ec8-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98ec8-103">'Blazor'</span></span>

<span data-ttu-id="98ec8-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98ec8-104">'Identity'</span></span>

<span data-ttu-id="98ec8-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98ec8-105">'Let's Encrypt'</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="98ec8-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98ec8-106">'Razor'</span></span>

### <a name="environment"></a><span data-ttu-id="98ec8-107">'SignalR' uid: blazor/hosting-model-configuration</span><span class="sxs-lookup"><span data-stu-id="98ec8-107">'SignalR' uid: blazor/hosting-model-configuration</span></span>

<span data-ttu-id="98ec8-108">Конфигурация модели размещения ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="98ec8-108">ASP.NET Core Blazor hosting model configuration</span></span> <span data-ttu-id="98ec8-109">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="98ec8-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="98ec8-110">В этой статье рассматривается конфигурация модели размещения.</span><span class="sxs-lookup"><span data-stu-id="98ec8-110">This article covers hosting model configuration.</span></span> Blazor<span data-ttu-id="98ec8-111"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="98ec8-111"> WebAssembly</span></span> <span data-ttu-id="98ec8-112">Среда</span><span class="sxs-lookup"><span data-stu-id="98ec8-112">Environment</span></span> <span data-ttu-id="98ec8-113">При локальном запуске приложения среда по умолчанию имеет значение Development.</span><span class="sxs-lookup"><span data-stu-id="98ec8-113">When running an app locally, the environment defaults to Development.</span></span>

<span data-ttu-id="98ec8-114">При публикации приложения среда по умолчанию имеет значение Production.</span><span class="sxs-lookup"><span data-stu-id="98ec8-114">When the app is published, the environment defaults to Production.</span></span> <span data-ttu-id="98ec8-115">Размещенное приложение Blazor WebAssembly выбирает среду на сервере через ПО промежуточного слоя, которое передает данные о среде в браузер, добавляя заголовок `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="98ec8-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span>

<span data-ttu-id="98ec8-116">В заголовке содержится среда.</span><span class="sxs-lookup"><span data-stu-id="98ec8-116">The value of the header is the environment.</span></span> <span data-ttu-id="98ec8-117">Размещенное приложение Blazor и серверное приложение совместно используют одну и ту же среду.</span><span class="sxs-lookup"><span data-stu-id="98ec8-117">The hosted Blazor app and the server app share the same environment.</span></span>

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
> <span data-ttu-id="98ec8-118">Дополнительные сведения, в том числе о настройке среды, см. на странице <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="98ec8-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="98ec8-119">Для автономного приложения, выполняемого локально, сервер разработки добавляет заголовок `blazor-environment`, чтобы указать среду разработки.</span><span class="sxs-lookup"><span data-stu-id="98ec8-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="98ec8-120">Чтобы указать среду для других сред размещения, добавьте заголовок `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="98ec8-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="98ec8-121">В следующем примере для IIS добавьте пользовательский заголовок в опубликованный *файл Web. config*.</span><span class="sxs-lookup"><span data-stu-id="98ec8-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span>

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

<span data-ttu-id="98ec8-122">Файл *web.config* размещен в папке *bin/Release/{TARGET FRAMEWORK}/publish*:</span><span class="sxs-lookup"><span data-stu-id="98ec8-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

### <a name="configuration"></a><span data-ttu-id="98ec8-123">Сведения об использовании пользовательского файла *web.config* для служб IIS, который не перезаписывается при публикации приложения в папку *Publish*, см. в разделе <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="98ec8-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="98ec8-124">Чтобы получить среду приложения в компоненте, вставьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> и прочтите свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>:</span><span class="sxs-lookup"><span data-stu-id="98ec8-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

* <span data-ttu-id="98ec8-125">Во время запуска <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> раскрывает <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> с помощью свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, которое позволяет разработчикам реализовать в своем коде логику для конкретной среды:</span><span class="sxs-lookup"><span data-stu-id="98ec8-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>
  * <span data-ttu-id="98ec8-126">Следующие удобные методы расширения допускают проверку текущей среды на признак среды разработки, тестирования, коммерческой среды или пользовательской среды:</span><span class="sxs-lookup"><span data-stu-id="98ec8-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>
  * <span data-ttu-id="98ec8-127">Свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> можно использовать во время запуска, если служба <xref:Microsoft.AspNetCore.Components.NavigationManager> недоступна.</span><span class="sxs-lookup"><span data-stu-id="98ec8-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>
* <span data-ttu-id="98ec8-128">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="98ec8-128">Configuration</span></span> Blazor<span data-ttu-id="98ec8-129"> WebAssembly загружает конфигурацию из следующих источников:</span><span class="sxs-lookup"><span data-stu-id="98ec8-129"> WebAssembly loads configuration from:</span></span> <span data-ttu-id="98ec8-130">Файлы параметров приложения по умолчанию</span><span class="sxs-lookup"><span data-stu-id="98ec8-130">App settings files by default:</span></span>

> [!WARNING]
> <span data-ttu-id="98ec8-131">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="98ec8-131">*wwwroot/appsettings.json*</span></span> <span data-ttu-id="98ec8-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="98ec8-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="98ec8-133">Другие [поставщики конфигурации](xref:fundamentals/configuration/index), зарегистрированные приложением.</span><span class="sxs-lookup"><span data-stu-id="98ec8-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="98ec8-134">Не все поставщики подходят для приложений Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="98ec8-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span>

<span data-ttu-id="98ec8-135">Сведения о том, какие поставщики поддерживаются для Blazor WebAssembly, можно найти в разделе [Поставщики конфигурации для Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="98ec8-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="98ec8-136">Конфигурация в приложении Blazor WebAssembly видна пользователям.</span><span class="sxs-lookup"><span data-stu-id="98ec8-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="98ec8-137">**Не храните учетные данные или секреты приложения в конфигурации.**</span><span class="sxs-lookup"><span data-stu-id="98ec8-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="98ec8-138">Дополнительные сведения о поставщиках конфигурации см. в разделе <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="98ec8-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="98ec8-139">Конфигурация параметров приложения</span><span class="sxs-lookup"><span data-stu-id="98ec8-139">App settings configuration</span></span>

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

<span data-ttu-id="98ec8-140">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="98ec8-140">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="98ec8-141">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="98ec8-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span> <span data-ttu-id="98ec8-142">Конфигурация поставщика</span><span class="sxs-lookup"><span data-stu-id="98ec8-142">Provider configuration</span></span>

<span data-ttu-id="98ec8-143">В следующем примере используется <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> для предоставления дополнительной конфигурации.</span><span class="sxs-lookup"><span data-stu-id="98ec8-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="98ec8-144">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="98ec8-144">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="98ec8-145">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="98ec8-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

<span data-ttu-id="98ec8-146">Для чтения других файлов конфигурации из папки *wwwroot* в конфигурацию используйте <xref:System.Net.Http.HttpClient> для получения содержимого файла.</span><span class="sxs-lookup"><span data-stu-id="98ec8-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="98ec8-147">При использовании этого подхода существующая регистрация службы <xref:System.Net.Http.HttpClient> может использовать локальный клиент, созданный для чтения файла, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="98ec8-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a><span data-ttu-id="98ec8-148">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="98ec8-148">*wwwroot/cars.json*:</span></span>

<span data-ttu-id="98ec8-149">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="98ec8-149">`Program.Main`:</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="98ec8-150">Конфигурация проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="98ec8-150">Authentication configuration</span></span>

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

<span data-ttu-id="98ec8-151">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="98ec8-151">*wwwroot/appsettings.json*:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="98ec8-152">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="98ec8-152">`Program.Main`:</span></span>

<span data-ttu-id="98ec8-153">Конфигурация ведения журнала</span><span class="sxs-lookup"><span data-stu-id="98ec8-153">Logging configuration</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="98ec8-154">Добавьте ссылку на пакет для [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/).</span><span class="sxs-lookup"><span data-stu-id="98ec8-154">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

<span data-ttu-id="98ec8-155">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="98ec8-155">*wwwroot/appsettings.json*:</span></span> <span data-ttu-id="98ec8-156">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="98ec8-156">`Program.Main`:</span></span> <span data-ttu-id="98ec8-157">Конфигурация построителя узлов</span><span class="sxs-lookup"><span data-stu-id="98ec8-157">Host builder configuration</span></span>

* <span data-ttu-id="98ec8-158">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="98ec8-158">`Program.Main`:</span></span>
* <span data-ttu-id="98ec8-159">Кэшированная конфигурация</span><span class="sxs-lookup"><span data-stu-id="98ec8-159">Cached configuration</span></span>

<span data-ttu-id="98ec8-160">Файлы конфигурации кэшируются для автономного использования.</span><span class="sxs-lookup"><span data-stu-id="98ec8-160">Configuration files are cached for offline use.</span></span>

### <a name="logging"></a><span data-ttu-id="98ec8-161">При использовании [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app) файлы конфигурации можно изменять только при создании развертывания.</span><span class="sxs-lookup"><span data-stu-id="98ec8-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span>

<span data-ttu-id="98ec8-162">Изменение файлов конфигурации между развертываниями не работает по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="98ec8-162">Editing configuration files between deployments has no effect because:</span></span>

## <a name="blazor-server"></a><span data-ttu-id="98ec8-163">У пользователей есть кэшированные версии файлов, которые они продолжают использовать.</span><span class="sxs-lookup"><span data-stu-id="98ec8-163">Users have cached versions of the files that they continue to use.</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="98ec8-164">Файлы *service-worker.js* и *service-worker-assets.js* PWA должны быть перестроены при компиляции, что при следующем входе пользователя в сеть сообщает приложению о том, что приложение было развернуто повторно.</span><span class="sxs-lookup"><span data-stu-id="98ec8-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="98ec8-165">Дополнительные сведения об обработке фоновых изменений с помощью PWA см. на странице <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="98ec8-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span> <span data-ttu-id="98ec8-166">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="98ec8-166">Logging</span></span>

<span data-ttu-id="98ec8-167">Дополнительные сведения о поддержке ведения журнала Blazor WebAssembly см. в разделе <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="98ec8-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="98ec8-168">Сервер Blazor</span><span class="sxs-lookup"><span data-stu-id="98ec8-168">Blazor Server</span></span>

| <span data-ttu-id="98ec8-169">Отражение состояния соединения в пользовательском интерфейсе</span><span class="sxs-lookup"><span data-stu-id="98ec8-169">Reflect the connection state in the UI</span></span>                       | <span data-ttu-id="98ec8-170">Когда клиент обнаруживает, что соединение было потеряно, пользовательский интерфейс по умолчанию отображается пользователю, в то время как клиент пытается восстановить соединение.</span><span class="sxs-lookup"><span data-stu-id="98ec8-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="98ec8-171">Если происходит повторный сбой подключения, пользователю предоставляется возможность повторить попытку.</span><span class="sxs-lookup"><span data-stu-id="98ec8-171">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="98ec8-172">Чтобы настроить пользовательский интерфейс, определите значение `id` элемента `components-reconnect-modal` в блоке `<body>` страницы Razor *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="98ec8-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span> <span data-ttu-id="98ec8-173">В следующей таблице описаны классы CSS, применяемые к элементу `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="98ec8-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="98ec8-174">Класс CSS</span><span class="sxs-lookup"><span data-stu-id="98ec8-174">CSS class</span></span> <span data-ttu-id="98ec8-175">Означает&hellip;</span><span class="sxs-lookup"><span data-stu-id="98ec8-175">Indicates&hellip;</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="98ec8-176">Потеря соединения.</span><span class="sxs-lookup"><span data-stu-id="98ec8-176">A lost connection.</span></span> <span data-ttu-id="98ec8-177">Клиент пытается повторно подключиться.</span><span class="sxs-lookup"><span data-stu-id="98ec8-177">The client is attempting to reconnect.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="98ec8-178">Отобразить модальное окно.</span><span class="sxs-lookup"><span data-stu-id="98ec8-178">Show the modal.</span></span> <span data-ttu-id="98ec8-179">Активное подключение к серверу восстановлено.</span><span class="sxs-lookup"><span data-stu-id="98ec8-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="98ec8-180">Скрыть модальное окно.</span><span class="sxs-lookup"><span data-stu-id="98ec8-180">Hide the modal.</span></span> <span data-ttu-id="98ec8-181">Сбой повторного подключения, возможно, из-за сбоя сети.</span><span class="sxs-lookup"><span data-stu-id="98ec8-181">Reconnection failed, probably due to a network failure.</span></span><ul><li><span data-ttu-id="98ec8-182">Чтобы повторить попытку подключения, вызовите метод `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="98ec8-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span></li><li><span data-ttu-id="98ec8-183">Повторное подключение отклонено.</span><span class="sxs-lookup"><span data-stu-id="98ec8-183">Reconnection rejected.</span></span> <span data-ttu-id="98ec8-184">Сервер был достигнут, но отклонил подключение, и состояние пользователя на сервере потеряно.</span><span class="sxs-lookup"><span data-stu-id="98ec8-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span></li><li><span data-ttu-id="98ec8-185">Чтобы перезагрузить приложение, вызовите метод `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="98ec8-185">To reload the app, call `location.reload()`.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="98ec8-186">Это состояние соединения может появиться в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="98ec8-186">This connection state may result when:</span></span>

<span data-ttu-id="98ec8-187">произошел сбой в цепи на стороне сервера;</span><span class="sxs-lookup"><span data-stu-id="98ec8-187">A crash in the server-side circuit occurs.</span></span> <span data-ttu-id="98ec8-188">клиент был отключен достаточно долго, чтобы сервер сбросил состояние пользователя.</span><span class="sxs-lookup"><span data-stu-id="98ec8-188">The client is disconnected long enough for the server to drop the user's state.</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="98ec8-189">Экземпляры компонентов, с которыми взаимодействовал пользователь, удаляются;</span><span class="sxs-lookup"><span data-stu-id="98ec8-189">Instances of the components that the user is interacting with are disposed.</span></span>

* <span data-ttu-id="98ec8-190">сервер был перезагружен или был выполнен перезапуск рабочего процесса приложения.</span><span class="sxs-lookup"><span data-stu-id="98ec8-190">The server is restarted, or the app's worker process is recycled.</span></span>
* <span data-ttu-id="98ec8-191">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="98ec8-191">Render mode</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="98ec8-192">Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено подключение клиента с сервером.</span><span class="sxs-lookup"><span data-stu-id="98ec8-192">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="98ec8-193">Это поведение настраивается на странице Razor *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="98ec8-193">This is set up in the *_Host.cshtml* Razor page:</span></span> <span data-ttu-id="98ec8-194">Параметр <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> настраивает одно из следующих поведений компонента:</span><span class="sxs-lookup"><span data-stu-id="98ec8-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="98ec8-195">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="98ec8-195">Is prerendered into the page.</span></span> <span data-ttu-id="98ec8-196">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="98ec8-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span> <span data-ttu-id="98ec8-197">Описание</span><span class="sxs-lookup"><span data-stu-id="98ec8-197">Description</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="98ec8-198">Преобразует компонент в статический HTML и включает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="98ec8-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> |

<span data-ttu-id="98ec8-199">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="98ec8-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="98ec8-200">Отображает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="98ec8-200">Renders a marker for a Blazor Server app.</span></span>

<span data-ttu-id="98ec8-201">Выходные данные компонента не включаются.</span><span class="sxs-lookup"><span data-stu-id="98ec8-201">Output from the component isn't included.</span></span> <span data-ttu-id="98ec8-202">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="98ec8-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span>

<span data-ttu-id="98ec8-203">Преобразует компонент в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="98ec8-203">Renders the component into static HTML.</span></span>

* <span data-ttu-id="98ec8-204">Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="98ec8-204">Rendering server components from a static HTML page isn't supported.</span></span>
* <span data-ttu-id="98ec8-205">Настройка клиента SignalR для приложений Blazor Server</span><span class="sxs-lookup"><span data-stu-id="98ec8-205">Configure the SignalR client for Blazor Server apps</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a><span data-ttu-id="98ec8-206">Иногда необходимо настроить клиент SignalR, используемый приложениями Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="98ec8-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span>

<span data-ttu-id="98ec8-207">Например, может потребоваться настроить ведение журнала на клиенте SignalR для диагностики проблемы с подключением.</span><span class="sxs-lookup"><span data-stu-id="98ec8-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
