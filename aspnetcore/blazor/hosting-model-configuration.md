---
title: Конфигурация модели размещения ASP.NET Core Blazor
author: guardrex
description: Сведения о конфигурации модели размещения Blazor, в том числе о том, как интегрировать компоненты Razor в Razor Pages и приложения MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 17ed43a12643f067da73658bec72400acbe1be43
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772078"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="603a4-103">Конфигурация модели размещения ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="603a4-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="603a4-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="603a4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="603a4-105">В этой статье рассматривается конфигурация модели размещения.</span><span class="sxs-lookup"><span data-stu-id="603a4-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="603a4-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="603a4-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="603a4-107">Среда</span><span class="sxs-lookup"><span data-stu-id="603a4-107">Environment</span></span>

<span data-ttu-id="603a4-108">При локальном запуске приложения среда по умолчанию имеет значение Development.</span><span class="sxs-lookup"><span data-stu-id="603a4-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="603a4-109">При публикации приложения среда по умолчанию имеет значение Production.</span><span class="sxs-lookup"><span data-stu-id="603a4-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="603a4-110">Размещенное приложение Blazor WebAssembly выбирает среду на сервере через прошивку, которая передает среду в браузер, добавляя заголовок `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="603a4-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="603a4-111">В заголовке содержится среда.</span><span class="sxs-lookup"><span data-stu-id="603a4-111">The value of the header is the environment.</span></span> <span data-ttu-id="603a4-112">Размещенное приложение Blazor и серверное приложение совместно используют одну и ту же среду.</span><span class="sxs-lookup"><span data-stu-id="603a4-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="603a4-113">Дополнительные сведения, в том числе о настройке среды, см. на странице <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="603a4-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="603a4-114">Для автономного приложения, выполняемого локально, сервер разработки добавляет заголовок `blazor-environment`, чтобы указать среду разработки.</span><span class="sxs-lookup"><span data-stu-id="603a4-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="603a4-115">Чтобы указать среду для других сред размещения, добавьте заголовок `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="603a4-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="603a4-116">В следующем примере для IIS добавьте пользовательский заголовок в опубликованный *файл Web. config*.</span><span class="sxs-lookup"><span data-stu-id="603a4-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="603a4-117">Файл *web.config* размещен в папке *bin/Release/{TARGET FRAMEWORK}/publish*:</span><span class="sxs-lookup"><span data-stu-id="603a4-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="603a4-118">Сведения об использовании пользовательского файла *web.config* для служб IIS, который не перезаписывается при публикации приложения в папку *Publish*, см. в разделе <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="603a4-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="603a4-119">Чтобы получить среду приложения в компоненте, вставьте `IWebAssemblyHostEnvironment` и прочтите свойство `Environment`:</span><span class="sxs-lookup"><span data-stu-id="603a4-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="603a4-120">Во время запуска `WebAssemblyHostBuilder` раскрывает `IWebAssemblyHostEnvironment` с помощью свойства `HostEnvironment`, которое позволяет разработчикам реализовать в своем коде логику для конкретной среды:</span><span class="sxs-lookup"><span data-stu-id="603a4-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="603a4-121">Следующие удобные методы расширения допускают проверку текущей среды на признак среды разработки, тестирования, коммерческой среды или пользовательской среды:</span><span class="sxs-lookup"><span data-stu-id="603a4-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="603a4-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span><span class="sxs-lookup"><span data-stu-id="603a4-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="603a4-123">Свойство `IWebAssemblyHostEnvironment.BaseAddress` можно использовать во время запуска, если служба `NavigationManager` недоступна.</span><span class="sxs-lookup"><span data-stu-id="603a4-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="603a4-124">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="603a4-124">Configuration</span></span>

<span data-ttu-id="603a4-125">Blazor WebAssembly загружает конфигурацию из следующих источников.</span><span class="sxs-lookup"><span data-stu-id="603a4-125">Blazor WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="603a4-126">Файлы параметров приложения по умолчанию</span><span class="sxs-lookup"><span data-stu-id="603a4-126">App settings files by default:</span></span>
  * <span data-ttu-id="603a4-127">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="603a4-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="603a4-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="603a4-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="603a4-129">Другие [поставщики конфигурации](xref:fundamentals/configuration/index), зарегистрированные приложением.</span><span class="sxs-lookup"><span data-stu-id="603a4-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="603a4-130">Не все поставщики подходят для приложений Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="603a4-130">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="603a4-131">Сведения о том, какие поставщики поддерживаются для Blazor WebAssembly, можно найти в разделе [Поставщики конфигурации для Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="603a4-131">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="603a4-132">Конфигурация в приложении Blazor WebAssembly видна пользователям.</span><span class="sxs-lookup"><span data-stu-id="603a4-132">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="603a4-133">**Не храните учетные данные или секреты приложения в конфигурации.**</span><span class="sxs-lookup"><span data-stu-id="603a4-133">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="603a4-134">Дополнительные сведения о поставщиках конфигурации см. в разделе <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="603a4-134">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="603a4-135">Конфигурация параметров приложения</span><span class="sxs-lookup"><span data-stu-id="603a4-135">App settings configuration</span></span>

<span data-ttu-id="603a4-136">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="603a4-136">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="603a4-137">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="603a4-137">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="603a4-138">Конфигурация поставщика</span><span class="sxs-lookup"><span data-stu-id="603a4-138">Provider configuration</span></span>

<span data-ttu-id="603a4-139">В следующем примере используется <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> для предоставления дополнительной конфигурации.</span><span class="sxs-lookup"><span data-stu-id="603a4-139">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="603a4-140">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="603a4-140">`Program.Main`:</span></span>

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

<span data-ttu-id="603a4-141">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="603a4-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="603a4-142">Для чтения других файлов конфигурации из папки *wwwroot* в конфигурацию используйте `HttpClient` для получения содержимого файла.</span><span class="sxs-lookup"><span data-stu-id="603a4-142">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="603a4-143">При использовании этого подхода существующая регистрация службы `HttpClient` может использовать локальный клиент, созданный для чтения файла, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="603a4-143">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="603a4-144">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="603a4-144">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="603a4-145">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="603a4-145">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="603a4-146">Конфигурация проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="603a4-146">Authentication configuration</span></span>

<span data-ttu-id="603a4-147">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="603a4-147">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="603a4-148">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="603a4-148">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="603a4-149">Конфигурация ведения журнала</span><span class="sxs-lookup"><span data-stu-id="603a4-149">Logging configuration</span></span>

<span data-ttu-id="603a4-150">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="603a4-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="603a4-151">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="603a4-151">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="603a4-152">Конфигурация построителя узлов</span><span class="sxs-lookup"><span data-stu-id="603a4-152">Host builder configuration</span></span>

<span data-ttu-id="603a4-153">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="603a4-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="603a4-154">Кэшированная конфигурация</span><span class="sxs-lookup"><span data-stu-id="603a4-154">Cached configuration</span></span>

<span data-ttu-id="603a4-155">Файлы конфигурации кэшируются для автономного использования.</span><span class="sxs-lookup"><span data-stu-id="603a4-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="603a4-156">При использовании [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app) файлы конфигурации можно изменять только при создании развертывания.</span><span class="sxs-lookup"><span data-stu-id="603a4-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="603a4-157">Изменение файлов конфигурации между развертываниями не работает по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="603a4-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="603a4-158">У пользователей есть кэшированные версии файлов, которые они продолжают использовать.</span><span class="sxs-lookup"><span data-stu-id="603a4-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="603a4-159">Файлы *service-worker.js* и *service-worker-assets.js* PWA должны быть перестроены при компиляции, что при следующем входе пользователя в сеть сообщает приложению о том, что приложение было развернуто повторно.</span><span class="sxs-lookup"><span data-stu-id="603a4-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="603a4-160">Дополнительные сведения об обработке фоновых изменений с помощью PWA см. на странице <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="603a4-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="603a4-161">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="603a4-161">Logging</span></span>

<span data-ttu-id="603a4-162">Дополнительные сведения о поддержке ведения журнала Blazor WebAssembly см. в разделе <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="603a4-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="603a4-163">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="603a4-163">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="603a4-164">Отражение состояния соединения в пользовательском интерфейсе</span><span class="sxs-lookup"><span data-stu-id="603a4-164">Reflect the connection state in the UI</span></span>

<span data-ttu-id="603a4-165">Когда клиент обнаруживает, что соединение было потеряно, пользовательский интерфейс по умолчанию отображается пользователю, в то время как клиент пытается восстановить соединение.</span><span class="sxs-lookup"><span data-stu-id="603a4-165">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="603a4-166">Если происходит повторный сбой подключения, пользователю предоставляется возможность повторить попытку.</span><span class="sxs-lookup"><span data-stu-id="603a4-166">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="603a4-167">Чтобы настроить пользовательский интерфейс, определите элемент с `id` равным `components-reconnect-modal` в блоке `<body>` страницы Razor *__Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="603a4-167">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="603a4-168">В следующей таблице описаны классы CSS, применяемые к элементу `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="603a4-168">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="603a4-169">Класс CSS</span><span class="sxs-lookup"><span data-stu-id="603a4-169">CSS class</span></span>                       | <span data-ttu-id="603a4-170">Означает&hellip;</span><span class="sxs-lookup"><span data-stu-id="603a4-170">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="603a4-171">Потеря соединения.</span><span class="sxs-lookup"><span data-stu-id="603a4-171">A lost connection.</span></span> <span data-ttu-id="603a4-172">Клиент пытается повторно подключиться.</span><span class="sxs-lookup"><span data-stu-id="603a4-172">The client is attempting to reconnect.</span></span> <span data-ttu-id="603a4-173">Отобразить модальное окно.</span><span class="sxs-lookup"><span data-stu-id="603a4-173">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="603a4-174">Активное подключение к серверу восстановлено.</span><span class="sxs-lookup"><span data-stu-id="603a4-174">An active connection is re-established to the server.</span></span> <span data-ttu-id="603a4-175">Скрыть модальное окно.</span><span class="sxs-lookup"><span data-stu-id="603a4-175">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="603a4-176">Сбой повторного подключения, возможно, из-за сбоя сети.</span><span class="sxs-lookup"><span data-stu-id="603a4-176">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="603a4-177">Чтобы повторить попытку подключения, вызовите метод `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="603a4-177">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="603a4-178">Повторное подключение отклонено.</span><span class="sxs-lookup"><span data-stu-id="603a4-178">Reconnection rejected.</span></span> <span data-ttu-id="603a4-179">Сервер был достигнут, но отклонил подключение, и состояние пользователя на сервере потеряно.</span><span class="sxs-lookup"><span data-stu-id="603a4-179">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="603a4-180">Чтобы перезагрузить приложение, вызовите метод `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="603a4-180">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="603a4-181">Это состояние соединения может появиться в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="603a4-181">This connection state may result when:</span></span><ul><li><span data-ttu-id="603a4-182">произошел сбой в цепи на стороне сервера;</span><span class="sxs-lookup"><span data-stu-id="603a4-182">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="603a4-183">клиент был отключен достаточно долго, чтобы сервер сбросил состояние пользователя.</span><span class="sxs-lookup"><span data-stu-id="603a4-183">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="603a4-184">Экземпляры компонентов, с которыми взаимодействовал пользователь, удаляются;</span><span class="sxs-lookup"><span data-stu-id="603a4-184">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="603a4-185">сервер был перезагружен или был выполнен перезапуск рабочего процесса приложения.</span><span class="sxs-lookup"><span data-stu-id="603a4-185">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="603a4-186">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="603a4-186">Render mode</span></span>

<span data-ttu-id="603a4-187">Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено клиентское соединение с сервером.</span><span class="sxs-lookup"><span data-stu-id="603a4-187">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="603a4-188">Это поведение настраивается на странице Razor *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="603a4-188">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="603a4-189">Параметр `RenderMode` настраивает одно из следующих поведений компонента:</span><span class="sxs-lookup"><span data-stu-id="603a4-189">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="603a4-190">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="603a4-190">Is prerendered into the page.</span></span>
* <span data-ttu-id="603a4-191">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="603a4-191">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="603a4-192">Описание</span><span class="sxs-lookup"><span data-stu-id="603a4-192">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="603a4-193">Преобразует компонент в статический HTML и включает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="603a4-193">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="603a4-194">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="603a4-194">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="603a4-195">Отображает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="603a4-195">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="603a4-196">Выходные данные компонента не включаются.</span><span class="sxs-lookup"><span data-stu-id="603a4-196">Output from the component isn't included.</span></span> <span data-ttu-id="603a4-197">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="603a4-197">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="603a4-198">Преобразует компонент в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="603a4-198">Renders the component into static HTML.</span></span> |

<span data-ttu-id="603a4-199">Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="603a4-199">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="603a4-200">Настройка клиента SignalR для приложений Blazor Server</span><span class="sxs-lookup"><span data-stu-id="603a4-200">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="603a4-201">Иногда необходимо настроить клиент SignalR, используемый приложениями Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="603a4-201">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="603a4-202">Например, может потребоваться настроить ведение журнала на клиенте SignalR для диагностики проблемы с подключением.</span><span class="sxs-lookup"><span data-stu-id="603a4-202">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="603a4-203">Чтобы настроить клиент SignalR, внесите следующие изменения в файле *Pages/_Host.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="603a4-203">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="603a4-204">добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;</span><span class="sxs-lookup"><span data-stu-id="603a4-204">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="603a4-205">вызовите `Blazor.start` и передайте объект конфигурации, указывающий построитель SignalR.</span><span class="sxs-lookup"><span data-stu-id="603a4-205">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="603a4-206">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="603a4-206">Logging</span></span>

<span data-ttu-id="603a4-207">Сведения о поддержке ведения журнала в Blazor Server см. в разделе <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="603a4-207">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
