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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>'Blazor'

'Identity'

'Let's Encrypt'

## <a name="blazor-webassembly"></a>'Razor'

### <a name="environment"></a>'SignalR' uid: blazor/hosting-model-configuration

Конфигурация модели размещения ASP.NET Core Blazor Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

В этой статье рассматривается конфигурация модели размещения. Blazor WebAssembly Среда При локальном запуске приложения среда по умолчанию имеет значение Development.

При публикации приложения среда по умолчанию имеет значение Production. Размещенное приложение Blazor WebAssembly выбирает среду на сервере через ПО промежуточного слоя, которое передает данные о среде в браузер, добавляя заголовок `blazor-environment`.

В заголовке содержится среда. Размещенное приложение Blazor и серверное приложение совместно используют одну и ту же среду.

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
> Дополнительные сведения, в том числе о настройке среды, см. на странице <xref:fundamentals/environments>.

Для автономного приложения, выполняемого локально, сервер разработки добавляет заголовок `blazor-environment`, чтобы указать среду разработки.

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Чтобы указать среду для других сред размещения, добавьте заголовок `blazor-environment`.

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

В следующем примере для IIS добавьте пользовательский заголовок в опубликованный *файл Web. config*.

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

Файл *web.config* размещен в папке *bin/Release/{TARGET FRAMEWORK}/publish*:

### <a name="configuration"></a>Сведения об использовании пользовательского файла *web.config* для служб IIS, который не перезаписывается при публикации приложения в папку *Publish*, см. в разделе <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.

Чтобы получить среду приложения в компоненте, вставьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> и прочтите свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>:

* Во время запуска <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> раскрывает <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> с помощью свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, которое позволяет разработчикам реализовать в своем коде логику для конкретной среды:
  * Следующие удобные методы расширения допускают проверку текущей среды на признак среды разработки, тестирования, коммерческой среды или пользовательской среды:
  * Свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> можно использовать во время запуска, если служба <xref:Microsoft.AspNetCore.Components.NavigationManager> недоступна.
* Параметр Configuration Blazor WebAssembly загружает конфигурацию из следующих источников: Файлы параметров приложения по умолчанию

> [!WARNING]
> *wwwroot/appsettings.json* *wwwroot/appsettings.{ENVIRONMENT}.json*

Другие [поставщики конфигурации](xref:fundamentals/configuration/index), зарегистрированные приложением.

#### <a name="app-settings-configuration"></a>Не все поставщики подходят для приложений Blazor WebAssembly.

Сведения о том, какие поставщики поддерживаются для Blazor WebAssembly, можно найти в разделе [Поставщики конфигурации для Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).

```json
{
  "message": "Hello from config!"
}
```

Конфигурация в приложении Blazor WebAssembly видна пользователям.

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>**Не храните учетные данные или секреты приложения в конфигурации.**

Дополнительные сведения о поставщиках конфигурации см. в разделе <xref:fundamentals/configuration/index>.

Конфигурация параметров приложения

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

*wwwroot/appsettings.json*:

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

Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации: Конфигурация поставщика

В следующем примере используется <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> для предоставления дополнительной конфигурации.

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

#### <a name="authentication-configuration"></a>Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:

Для чтения других файлов конфигурации из папки *wwwroot* в конфигурацию используйте <xref:System.Net.Http.HttpClient> для получения содержимого файла.

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

При использовании этого подхода существующая регистрация службы <xref:System.Net.Http.HttpClient> может использовать локальный клиент, созданный для чтения файла, как показано в следующем примере.

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a>*wwwroot/cars.json*:

`Program.Main`.

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

Конфигурация проверки подлинности

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

*wwwroot/appsettings.json*:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>`Program.Main`.

Конфигурация ведения журнала

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Добавьте ссылку на пакет для [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/).

*wwwroot/appsettings.json*: `Program.Main`. Конфигурация построителя узлов

* `Program.Main`.
* Кэшированная конфигурация

Файлы конфигурации кэшируются для автономного использования.

### <a name="logging"></a>При использовании [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app) файлы конфигурации можно изменять только при создании развертывания.

Изменение файлов конфигурации между развертываниями не работает по следующим причинам:

## <a name="blazor-server"></a>У пользователей есть кэшированные версии файлов, которые они продолжают использовать.

### <a name="reflect-the-connection-state-in-the-ui"></a>Файлы *service-worker.js* и *service-worker-assets.js* PWA должны быть перестроены при компиляции, что при следующем входе пользователя в сеть сообщает приложению о том, что приложение было развернуто повторно.

Дополнительные сведения об обработке фоновых изменений с помощью PWA см. на странице <xref:blazor/progressive-web-app#background-updates>. Ведение журнала

Дополнительные сведения о поддержке ведения журнала Blazor WebAssembly см. в разделе <xref:fundamentals/logging/index#create-logs-in-blazor>.

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Сервер Blazor

| Отражение состояния соединения в пользовательском интерфейсе                       | Когда клиент обнаруживает, что соединение было потеряно, пользовательский интерфейс по умолчанию отображается пользователю, в то время как клиент пытается восстановить соединение. |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Если происходит повторный сбой подключения, пользователю предоставляется возможность повторить попытку. Чтобы настроить пользовательский интерфейс, определите значение `id` элемента `components-reconnect-modal` в блоке `<body>` страницы Razor *_Host.cshtml*. В следующей таблице описаны классы CSS, применяемые к элементу `components-reconnect-modal`. |
| `components-reconnect-hide`     | Класс CSS Означает&hellip; |
| `components-reconnect-failed`   | Потеря соединения. Клиент пытается повторно подключиться. |
| `components-reconnect-rejected` | Отобразить модальное окно. Активное подключение к серверу восстановлено. Скрыть модальное окно. Сбой повторного подключения, возможно, из-за сбоя сети.<ul><li>Чтобы повторить попытку подключения, вызовите метод `window.Blazor.reconnect()`.</li><li>Повторное подключение отклонено. Сервер был достигнут, но отклонил подключение, и состояние пользователя на сервере потеряно.</li><li>Чтобы перезагрузить приложение, вызовите метод `location.reload()`.</li></ul> |

### <a name="render-mode"></a>Это состояние соединения может появиться в следующих случаях:

произошел сбой в цепи на стороне сервера; клиент был отключен достаточно долго, чтобы сервер сбросил состояние пользователя.

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

Экземпляры компонентов, с которыми взаимодействовал пользователь, удаляются;

* сервер был перезагружен или был выполнен перезапуск рабочего процесса приложения.
* Режим обработки

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено подключение клиента с сервером. |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Это поведение настраивается на странице Razor *_Host.cshtml*. Параметр <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> настраивает одно из следующих поведений компонента: |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | компонент предварительно преобразуется в страницу; компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя. Описание |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Преобразует компонент в статический HTML и включает метку приложения Blazor Server. |

При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Отображает метку приложения Blazor Server.

Выходные данные компонента не включаются. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.

Преобразует компонент в статический HTML.

* Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.
* Настройка клиента SignalR для приложений Blazor Server

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

### <a name="logging"></a>Иногда необходимо настроить клиент SignalR, используемый приложениями Blazor Server.

Например, может потребоваться настроить ведение журнала на клиенте SignalR для диагностики проблемы с подключением.
