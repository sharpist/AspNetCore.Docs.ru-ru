---
<span data-ttu-id="cd087-101">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-103">'Blazor'</span></span>
- <span data-ttu-id="cd087-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-104">'Identity'</span></span>
- <span data-ttu-id="cd087-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-106">'Razor'</span></span>
- <span data-ttu-id="cd087-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="cd087-108">Конфигурация модели размещения ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="cd087-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="cd087-109">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cd087-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cd087-110">В этой статье рассматривается конфигурация модели размещения.</span><span class="sxs-lookup"><span data-stu-id="cd087-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="cd087-111"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="cd087-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="cd087-112">Среда</span><span class="sxs-lookup"><span data-stu-id="cd087-112">Environment</span></span>

<span data-ttu-id="cd087-113">При локальном запуске приложения среда по умолчанию имеет значение Development.</span><span class="sxs-lookup"><span data-stu-id="cd087-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="cd087-114">При публикации приложения среда по умолчанию имеет значение Production.</span><span class="sxs-lookup"><span data-stu-id="cd087-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="cd087-115">Размещенное приложение Blazor WebAssembly выбирает среду на сервере через ПО промежуточного слоя, которое передает данные о среде в браузер, добавляя заголовок `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="cd087-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="cd087-116">В заголовке содержится среда.</span><span class="sxs-lookup"><span data-stu-id="cd087-116">The value of the header is the environment.</span></span> <span data-ttu-id="cd087-117">Размещенное приложение Blazor и серверное приложение совместно используют одну и ту же среду.</span><span class="sxs-lookup"><span data-stu-id="cd087-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="cd087-118">Дополнительные сведения, в том числе о настройке среды, см. на странице <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="cd087-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="cd087-119">Для автономного приложения, выполняемого локально, сервер разработки добавляет заголовок `blazor-environment`, чтобы указать среду разработки.</span><span class="sxs-lookup"><span data-stu-id="cd087-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="cd087-120">Чтобы указать среду для других сред размещения, добавьте заголовок `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="cd087-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="cd087-121">В следующем примере для IIS добавьте пользовательский заголовок в опубликованный *файл Web. config*.</span><span class="sxs-lookup"><span data-stu-id="cd087-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="cd087-122">Файл *web.config* размещен в папке *bin/Release/{TARGET FRAMEWORK}/publish*:</span><span class="sxs-lookup"><span data-stu-id="cd087-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="cd087-123">Сведения об использовании пользовательского файла *web.config* для служб IIS, который не перезаписывается при публикации приложения в папку *Publish*, см. в разделе <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="cd087-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="cd087-124">Чтобы получить среду приложения в компоненте, вставьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> и прочтите свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>:</span><span class="sxs-lookup"><span data-stu-id="cd087-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="cd087-125">Во время запуска <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> раскрывает <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> с помощью свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, которое позволяет разработчикам реализовать в своем коде логику для конкретной среды:</span><span class="sxs-lookup"><span data-stu-id="cd087-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="cd087-126">Следующие удобные методы расширения допускают проверку текущей среды на признак среды разработки, тестирования, коммерческой среды или пользовательской среды:</span><span class="sxs-lookup"><span data-stu-id="cd087-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="cd087-127">Свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> можно использовать во время запуска, если служба <xref:Microsoft.AspNetCore.Components.NavigationManager> недоступна.</span><span class="sxs-lookup"><span data-stu-id="cd087-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="cd087-128">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="cd087-128">Configuration</span></span>

Blazor<span data-ttu-id="cd087-129"> WebAssembly загружает конфигурацию из следующих источников:</span><span class="sxs-lookup"><span data-stu-id="cd087-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="cd087-130">Файлы параметров приложения по умолчанию</span><span class="sxs-lookup"><span data-stu-id="cd087-130">App settings files by default:</span></span>
  * <span data-ttu-id="cd087-131">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="cd087-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="cd087-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="cd087-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="cd087-133">Другие [поставщики конфигурации](xref:fundamentals/configuration/index), зарегистрированные приложением.</span><span class="sxs-lookup"><span data-stu-id="cd087-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="cd087-134">Не все поставщики подходят для приложений Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="cd087-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="cd087-135">Сведения о том, какие поставщики поддерживаются для Blazor WebAssembly, можно найти в разделе [Поставщики конфигурации для Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="cd087-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="cd087-136">Конфигурация в приложении Blazor WebAssembly видна пользователям.</span><span class="sxs-lookup"><span data-stu-id="cd087-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="cd087-137">**Не храните учетные данные или секреты приложения в конфигурации.**</span><span class="sxs-lookup"><span data-stu-id="cd087-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="cd087-138">Дополнительные сведения о поставщиках конфигурации см. в разделе <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="cd087-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="cd087-139">Конфигурация параметров приложения</span><span class="sxs-lookup"><span data-stu-id="cd087-139">App settings configuration</span></span>

<span data-ttu-id="cd087-140">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cd087-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="cd087-141">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="cd087-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="cd087-142">Конфигурация поставщика</span><span class="sxs-lookup"><span data-stu-id="cd087-142">Provider configuration</span></span>

<span data-ttu-id="cd087-143">В следующем примере используется <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> для предоставления дополнительной конфигурации.</span><span class="sxs-lookup"><span data-stu-id="cd087-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="cd087-144">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="cd087-144">`Program.Main`:</span></span>

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

<span data-ttu-id="cd087-145">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="cd087-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="cd087-146">Для чтения других файлов конфигурации из папки *wwwroot* в конфигурацию используйте <xref:System.Net.Http.HttpClient> для получения содержимого файла.</span><span class="sxs-lookup"><span data-stu-id="cd087-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="cd087-147">При использовании этого подхода существующая регистрация службы <xref:System.Net.Http.HttpClient> может использовать локальный клиент, созданный для чтения файла, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="cd087-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="cd087-148">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="cd087-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="cd087-149">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="cd087-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="cd087-150">Конфигурация проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="cd087-150">Authentication configuration</span></span>

<span data-ttu-id="cd087-151">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cd087-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="cd087-152">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="cd087-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a><span data-ttu-id="cd087-153">Конфигурация ведения журнала</span><span class="sxs-lookup"><span data-stu-id="cd087-153">Logging configuration</span></span>

<span data-ttu-id="cd087-154">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cd087-154">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="cd087-155">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="cd087-155">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="cd087-156">Конфигурация построителя узлов</span><span class="sxs-lookup"><span data-stu-id="cd087-156">Host builder configuration</span></span>

<span data-ttu-id="cd087-157">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="cd087-157">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="cd087-158">Кэшированная конфигурация</span><span class="sxs-lookup"><span data-stu-id="cd087-158">Cached configuration</span></span>

<span data-ttu-id="cd087-159">Файлы конфигурации кэшируются для автономного использования.</span><span class="sxs-lookup"><span data-stu-id="cd087-159">Configuration files are cached for offline use.</span></span> <span data-ttu-id="cd087-160">При использовании [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app) файлы конфигурации можно изменять только при создании развертывания.</span><span class="sxs-lookup"><span data-stu-id="cd087-160">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="cd087-161">Изменение файлов конфигурации между развертываниями не работает по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="cd087-161">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="cd087-162">У пользователей есть кэшированные версии файлов, которые они продолжают использовать.</span><span class="sxs-lookup"><span data-stu-id="cd087-162">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="cd087-163">Файлы *service-worker.js* и *service-worker-assets.js* PWA должны быть перестроены при компиляции, что при следующем входе пользователя в сеть сообщает приложению о том, что приложение было развернуто повторно.</span><span class="sxs-lookup"><span data-stu-id="cd087-163">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="cd087-164">Дополнительные сведения об обработке фоновых изменений с помощью PWA см. на странице <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="cd087-164">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="cd087-165">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="cd087-165">Logging</span></span>

<span data-ttu-id="cd087-166">Дополнительные сведения о поддержке ведения журнала Blazor WebAssembly см. в разделе <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="cd087-166">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="cd087-167">Сервер Blazor</span><span class="sxs-lookup"><span data-stu-id="cd087-167">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="cd087-168">Отражение состояния соединения в пользовательском интерфейсе</span><span class="sxs-lookup"><span data-stu-id="cd087-168">Reflect the connection state in the UI</span></span>

<span data-ttu-id="cd087-169">Когда клиент обнаруживает, что соединение было потеряно, пользовательский интерфейс по умолчанию отображается пользователю, в то время как клиент пытается восстановить соединение.</span><span class="sxs-lookup"><span data-stu-id="cd087-169">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="cd087-170">Если происходит повторный сбой подключения, пользователю предоставляется возможность повторить попытку.</span><span class="sxs-lookup"><span data-stu-id="cd087-170">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="cd087-171">Чтобы настроить пользовательский интерфейс, определите значение `id` элемента `components-reconnect-modal` в блоке `<body>` страницы Razor *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="cd087-171">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="cd087-172">В следующей таблице описаны классы CSS, применяемые к элементу `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="cd087-172">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="cd087-173">Класс CSS</span><span class="sxs-lookup"><span data-stu-id="cd087-173">CSS class</span></span>                       | <span data-ttu-id="cd087-174">Означает&hellip;</span><span class="sxs-lookup"><span data-stu-id="cd087-174">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="cd087-175">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-175">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-177">'Blazor'</span></span>
- <span data-ttu-id="cd087-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-178">'Identity'</span></span>
- <span data-ttu-id="cd087-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-180">'Razor'</span></span>
- <span data-ttu-id="cd087-181">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-182">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-182">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-184">'Blazor'</span></span>
- <span data-ttu-id="cd087-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-185">'Identity'</span></span>
- <span data-ttu-id="cd087-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-187">'Razor'</span></span>
- <span data-ttu-id="cd087-188">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-189">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-189">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-191">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-191">'Blazor'</span></span>
- <span data-ttu-id="cd087-192">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-192">'Identity'</span></span>
- <span data-ttu-id="cd087-193">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-193">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-194">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-194">'Razor'</span></span>
- <span data-ttu-id="cd087-195">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-195">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-196">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-196">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-198">'Blazor'</span></span>
- <span data-ttu-id="cd087-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-199">'Identity'</span></span>
- <span data-ttu-id="cd087-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-201">'Razor'</span></span>
- <span data-ttu-id="cd087-202">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-203">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-203">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-205">'Blazor'</span></span>
- <span data-ttu-id="cd087-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-206">'Identity'</span></span>
- <span data-ttu-id="cd087-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-208">'Razor'</span></span>
- <span data-ttu-id="cd087-209">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-210">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-210">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-212">'Blazor'</span></span>
- <span data-ttu-id="cd087-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-213">'Identity'</span></span>
- <span data-ttu-id="cd087-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-215">'Razor'</span></span>
- <span data-ttu-id="cd087-216">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-217">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-217">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-219">'Blazor'</span></span>
- <span data-ttu-id="cd087-220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-220">'Identity'</span></span>
- <span data-ttu-id="cd087-221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-221">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-222">'Razor'</span></span>
- <span data-ttu-id="cd087-223">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-224">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-224">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-226">'Blazor'</span></span>
- <span data-ttu-id="cd087-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-227">'Identity'</span></span>
- <span data-ttu-id="cd087-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-229">'Razor'</span></span>
- <span data-ttu-id="cd087-230">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-231">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-231">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-233">'Blazor'</span></span>
- <span data-ttu-id="cd087-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-234">'Identity'</span></span>
- <span data-ttu-id="cd087-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-236">'Razor'</span></span>
- <span data-ttu-id="cd087-237">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-237">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-238">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-238">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-240">'Blazor'</span></span>
- <span data-ttu-id="cd087-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-241">'Identity'</span></span>
- <span data-ttu-id="cd087-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-243">'Razor'</span></span>
- <span data-ttu-id="cd087-244">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-245">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-245">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-247">'Blazor'</span></span>
- <span data-ttu-id="cd087-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-248">'Identity'</span></span>
- <span data-ttu-id="cd087-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-250">'Razor'</span></span>
- <span data-ttu-id="cd087-251">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-252">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-252">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-254">'Blazor'</span></span>
- <span data-ttu-id="cd087-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-255">'Identity'</span></span>
- <span data-ttu-id="cd087-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-257">'Razor'</span></span>
- <span data-ttu-id="cd087-258">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-259">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-259">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-261">'Blazor'</span></span>
- <span data-ttu-id="cd087-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-262">'Identity'</span></span>
- <span data-ttu-id="cd087-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-264">'Razor'</span></span>
- <span data-ttu-id="cd087-265">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-265">'SignalR' uid:</span></span> 

<span data-ttu-id="cd087-266">---------------- | --- название: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-266">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-268">'Blazor'</span></span>
- <span data-ttu-id="cd087-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-269">'Identity'</span></span>
- <span data-ttu-id="cd087-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-271">'Razor'</span></span>
- <span data-ttu-id="cd087-272">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-273">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-273">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-275">'Blazor'</span></span>
- <span data-ttu-id="cd087-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-276">'Identity'</span></span>
- <span data-ttu-id="cd087-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-278">'Razor'</span></span>
- <span data-ttu-id="cd087-279">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-280">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-280">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-282">'Blazor'</span></span>
- <span data-ttu-id="cd087-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-283">'Identity'</span></span>
- <span data-ttu-id="cd087-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-285">'Razor'</span></span>
- <span data-ttu-id="cd087-286">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-287">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-287">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-289">'Blazor'</span></span>
- <span data-ttu-id="cd087-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-290">'Identity'</span></span>
- <span data-ttu-id="cd087-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-292">'Razor'</span></span>
- <span data-ttu-id="cd087-293">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-294">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-294">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-296">'Blazor'</span></span>
- <span data-ttu-id="cd087-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-297">'Identity'</span></span>
- <span data-ttu-id="cd087-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-299">'Razor'</span></span>
- <span data-ttu-id="cd087-300">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cd087-301">title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="cd087-301">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cd087-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cd087-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cd087-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cd087-303">'Blazor'</span></span>
- <span data-ttu-id="cd087-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cd087-304">'Identity'</span></span>
- <span data-ttu-id="cd087-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cd087-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="cd087-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cd087-306">'Razor'</span></span>
- <span data-ttu-id="cd087-307">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="cd087-307">'SignalR' uid:</span></span> 

<span data-ttu-id="cd087-308">--------- | | `components-reconnect-show`     | Прерванное подключение.</span><span class="sxs-lookup"><span data-stu-id="cd087-308">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="cd087-309">Клиент пытается повторно подключиться.</span><span class="sxs-lookup"><span data-stu-id="cd087-309">The client is attempting to reconnect.</span></span> <span data-ttu-id="cd087-310">Отобразить модальное окно.</span><span class="sxs-lookup"><span data-stu-id="cd087-310">Show the modal.</span></span> <span data-ttu-id="cd087-311">| | `components-reconnect-hide`     | Активное подключение к серверу восстановлено.</span><span class="sxs-lookup"><span data-stu-id="cd087-311">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="cd087-312">Скрыть модальное окно.</span><span class="sxs-lookup"><span data-stu-id="cd087-312">Hide the modal.</span></span> <span data-ttu-id="cd087-313">| | `components-reconnect-failed`   | Сбой повторного подключения, возможно, из-за сбоя сети.</span><span class="sxs-lookup"><span data-stu-id="cd087-313">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="cd087-314">Чтобы повторить попытку подключения, вызовите метод `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="cd087-314">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="cd087-315">| | `components-reconnect-rejected` | Повторное подключение отклонено.</span><span class="sxs-lookup"><span data-stu-id="cd087-315">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="cd087-316">Сервер был достигнут, но отклонил подключение, и состояние пользователя на сервере потеряно.</span><span class="sxs-lookup"><span data-stu-id="cd087-316">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="cd087-317">Чтобы перезагрузить приложение, вызовите метод `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="cd087-317">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="cd087-318">Это состояние соединения может появиться в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="cd087-318">This connection state may result when:</span></span><ul><li><span data-ttu-id="cd087-319">произошел сбой в цепи на стороне сервера;</span><span class="sxs-lookup"><span data-stu-id="cd087-319">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="cd087-320">клиент был отключен достаточно долго, чтобы сервер сбросил состояние пользователя.</span><span class="sxs-lookup"><span data-stu-id="cd087-320">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="cd087-321">Экземпляры компонентов, с которыми взаимодействовал пользователь, удаляются;</span><span class="sxs-lookup"><span data-stu-id="cd087-321">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="cd087-322">сервер был перезагружен или был выполнен перезапуск рабочего процесса приложения.</span><span class="sxs-lookup"><span data-stu-id="cd087-322">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="cd087-323">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="cd087-323">Render mode</span></span>

<span data-ttu-id="cd087-324">Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено подключение клиента с сервером.</span><span class="sxs-lookup"><span data-stu-id="cd087-324">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="cd087-325">Это поведение настраивается на странице Razor *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="cd087-325">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="cd087-326">Параметр <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> настраивает одно из следующих поведений компонента:</span><span class="sxs-lookup"><span data-stu-id="cd087-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="cd087-327">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="cd087-327">Is prerendered into the page.</span></span>
* <span data-ttu-id="cd087-328">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="cd087-328">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="cd087-329">Описание</span><span class="sxs-lookup"><span data-stu-id="cd087-329">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="cd087-330">Преобразует компонент в статический HTML и включает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="cd087-330">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="cd087-331">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="cd087-331">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="cd087-332">Отображает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="cd087-332">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="cd087-333">Выходные данные компонента не включаются.</span><span class="sxs-lookup"><span data-stu-id="cd087-333">Output from the component isn't included.</span></span> <span data-ttu-id="cd087-334">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="cd087-334">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="cd087-335">Преобразует компонент в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="cd087-335">Renders the component into static HTML.</span></span> |

<span data-ttu-id="cd087-336">Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="cd087-336">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="cd087-337">Настройка клиента SignalR для приложений Blazor Server</span><span class="sxs-lookup"><span data-stu-id="cd087-337">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="cd087-338">Иногда необходимо настроить клиент SignalR, используемый приложениями Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="cd087-338">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="cd087-339">Например, может потребоваться настроить ведение журнала на клиенте SignalR для диагностики проблемы с подключением.</span><span class="sxs-lookup"><span data-stu-id="cd087-339">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="cd087-340">Чтобы настроить клиент SignalR, внесите следующие изменения в файле *Pages/_Host.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="cd087-340">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="cd087-341">добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;</span><span class="sxs-lookup"><span data-stu-id="cd087-341">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="cd087-342">вызовите `Blazor.start` и передайте объект конфигурации, указывающий построитель SignalR.</span><span class="sxs-lookup"><span data-stu-id="cd087-342">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="cd087-343">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="cd087-343">Logging</span></span>

<span data-ttu-id="cd087-344">Сведения о поддержке ведения журнала в Blazor Server см. в разделе <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="cd087-344">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
