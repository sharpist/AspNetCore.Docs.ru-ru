---
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Конфигурация модели размещения ASP.NET Core Blazor

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

В этой статье рассматривается конфигурация модели размещения.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

### <a name="environment"></a>Среда

При локальном запуске приложения среда по умолчанию имеет значение Development. При публикации приложения среда по умолчанию имеет значение Production.

Размещенное приложение Blazor WebAssembly выбирает среду на сервере через ПО промежуточного слоя, которое передает данные о среде в браузер, добавляя заголовок `blazor-environment`. В заголовке содержится среда. Размещенное приложение Blazor и серверное приложение совместно используют одну и ту же среду. Дополнительные сведения, в том числе о настройке среды, см. на странице <xref:fundamentals/environments>.

Для автономного приложения, выполняемого локально, сервер разработки добавляет заголовок `blazor-environment`, чтобы указать среду разработки. Чтобы указать среду для других сред размещения, добавьте заголовок `blazor-environment`.

В следующем примере для IIS добавьте пользовательский заголовок в опубликованный *файл Web. config*. Файл *web.config* размещен в папке *bin/Release/{TARGET FRAMEWORK}/publish*:

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
> Сведения об использовании пользовательского файла *web.config* для служб IIS, который не перезаписывается при публикации приложения в папку *Publish*, см. в разделе <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.

Чтобы получить среду приложения в компоненте, вставьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> и прочтите свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Во время запуска <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> раскрывает <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> с помощью свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, которое позволяет разработчикам реализовать в своем коде логику для конкретной среды:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Следующие удобные методы расширения допускают проверку текущей среды на признак среды разработки, тестирования, коммерческой среды или пользовательской среды:

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

Свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> можно использовать во время запуска, если служба <xref:Microsoft.AspNetCore.Components.NavigationManager> недоступна.

### <a name="configuration"></a>Параметр Configuration

Blazor WebAssembly загружает конфигурацию из следующих источников:

* Файлы параметров приложения по умолчанию
  * *wwwroot/appsettings.json*
  * *wwwroot/appsettings.{ENVIRONMENT}.json*
* Другие [поставщики конфигурации](xref:fundamentals/configuration/index), зарегистрированные приложением. Не все поставщики подходят для приложений Blazor WebAssembly. Сведения о том, какие поставщики поддерживаются для Blazor WebAssembly, можно найти в разделе [Поставщики конфигурации для Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).

> [!WARNING]
> Конфигурация в приложении Blazor WebAssembly видна пользователям. **Не храните учетные данные или секреты приложения в конфигурации.**

Дополнительные сведения о поставщиках конфигурации см. в разделе <xref:fundamentals/configuration/index>.

#### <a name="app-settings-configuration"></a>Конфигурация параметров приложения

*wwwroot/appsettings.json*:

```json
{
  "message": "Hello from config!"
}
```

Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>Конфигурация поставщика

В следующем примере используется <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> для предоставления дополнительной конфигурации.

`Program.Main`.

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

Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:

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

Для чтения других файлов конфигурации из папки *wwwroot* в конфигурацию используйте <xref:System.Net.Http.HttpClient> для получения содержимого файла. При использовании этого подхода существующая регистрация службы <xref:System.Net.Http.HttpClient> может использовать локальный клиент, созданный для чтения файла, как показано в следующем примере.

*wwwroot/cars.json*:

```json
{
    "size": "tiny"
}
```

`Program.Main`.

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

#### <a name="authentication-configuration"></a>Конфигурация проверки подлинности

*wwwroot/appsettings.json*:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`.

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a>Конфигурация ведения журнала

*wwwroot/appsettings.json*:

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

`Program.Main`.

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>Конфигурация построителя узлов

`Program.Main`.

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Кэшированная конфигурация

Файлы конфигурации кэшируются для автономного использования. При использовании [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app) файлы конфигурации можно изменять только при создании развертывания. Изменение файлов конфигурации между развертываниями не работает по следующим причинам:

* У пользователей есть кэшированные версии файлов, которые они продолжают использовать.
* Файлы *service-worker.js* и *service-worker-assets.js* PWA должны быть перестроены при компиляции, что при следующем входе пользователя в сеть сообщает приложению о том, что приложение было развернуто повторно.

Дополнительные сведения об обработке фоновых изменений с помощью PWA см. на странице <xref:blazor/progressive-web-app#background-updates>.

### <a name="logging"></a>Ведение журнала

Дополнительные сведения о поддержке ведения журнала Blazor WebAssembly см. в разделе <xref:fundamentals/logging/index#create-logs-in-blazor>.

## <a name="blazor-server"></a>Сервер Blazor

### <a name="reflect-the-connection-state-in-the-ui"></a>Отражение состояния соединения в пользовательском интерфейсе

Когда клиент обнаруживает, что соединение было потеряно, пользовательский интерфейс по умолчанию отображается пользователю, в то время как клиент пытается восстановить соединение. Если происходит повторный сбой подключения, пользователю предоставляется возможность повторить попытку.

Чтобы настроить пользовательский интерфейс, определите значение `id` элемента `components-reconnect-modal` в блоке `<body>` страницы Razor *_Host.cshtml*.

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

В следующей таблице описаны классы CSS, применяемые к элементу `components-reconnect-modal`.

| Класс CSS                       | Означает&hellip; |
| ---
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---------------- | --- название: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Конфигурация модели размещения ASP.NET Core Blazor автор: описание: Сведения о конфигурации модели размещения Blazor и о том, как интегрировать компоненты Razor с Razor Pages и приложениями MVC.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

--------- | | `components-reconnect-show`     | Прерванное подключение. Клиент пытается повторно подключиться. Отобразить модальное окно. | | `components-reconnect-hide`     | Активное подключение к серверу восстановлено. Скрыть модальное окно. | | `components-reconnect-failed`   | Сбой повторного подключения, возможно, из-за сбоя сети. Чтобы повторить попытку подключения, вызовите метод `window.Blazor.reconnect()`. | | `components-reconnect-rejected` | Повторное подключение отклонено. Сервер был достигнут, но отклонил подключение, и состояние пользователя на сервере потеряно. Чтобы перезагрузить приложение, вызовите метод `location.reload()`. Это состояние соединения может появиться в следующих случаях:<ul><li>произошел сбой в цепи на стороне сервера;</li><li>клиент был отключен достаточно долго, чтобы сервер сбросил состояние пользователя. Экземпляры компонентов, с которыми взаимодействовал пользователь, удаляются;</li><li>сервер был перезагружен или был выполнен перезапуск рабочего процесса приложения.</li></ul> |

### <a name="render-mode"></a>Режим обработки

Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено подключение клиента с сервером. Это поведение настраивается на странице Razor *_Host.cshtml*.

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

Параметр <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> настраивает одно из следующих поведений компонента:

* компонент предварительно преобразуется в страницу;
* компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | Описание |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Преобразует компонент в статический HTML и включает метку приложения Blazor Server. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Отображает метку приложения Blazor Server. Выходные данные компонента не включаются. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Преобразует компонент в статический HTML. |

Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Настройка клиента SignalR для приложений Blazor Server

Иногда необходимо настроить клиент SignalR, используемый приложениями Blazor Server. Например, может потребоваться настроить ведение журнала на клиенте SignalR для диагностики проблемы с подключением.

Чтобы настроить клиент SignalR, внесите следующие изменения в файле *Pages/_Host.cshtml*:

* добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;
* вызовите `Blazor.start` и передайте объект конфигурации, указывающий построитель SignalR.

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

### <a name="logging"></a>Ведение журнала

Сведения о поддержке ведения журнала в Blazor Server см. в разделе <xref:fundamentals/logging/index#create-logs-in-blazor>.
