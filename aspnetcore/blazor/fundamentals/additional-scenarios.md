---
title: Конфигурация модели размещения ASP.NET Core Blazor
author: guardrex
description: Подробные сведения о дополнительных сценариях для конфигурации модели размещения ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: ef37c539d377f14a2744c3ead28234d8497df700
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637682"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>Конфигурация модели размещения ASP.NET Core Blazor

Авторы: Дэниэл Рот ([Daniel Roth](https://github.com/danroth27)), Маккиннон Бак ([Mackinnon Buck](https://github.com/MackinnonBuck)) и Люк Лэтэм ([Luke Latham](https://github.com/guardrex))

В этой статье рассматривается конфигурация модели размещения.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>Согласование независимо от источника для проверки подлинности для SignalR

*Этот раздел относится к Blazor WebAssembly.*

Чтобы настроить базовый клиент SignalR для отправки учетных данных, таких как файлы cookie или заголовки проверки подлинности HTTP:

* используйте <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>, чтобы задать <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> в независящих от источника запросах [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch):

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* присвойте <xref:System.Net.Http.HttpMessageHandler> параметру <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Для получения дополнительной информации см. <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Отражение состояния соединения в пользовательском интерфейсе

*Этот раздел относится к Blazor Server.*

Когда клиент обнаруживает, что соединение было потеряно, пользовательский интерфейс по умолчанию отображается пользователю, в то время как клиент пытается восстановить соединение. Если происходит повторный сбой подключения, пользователю предоставляется возможность повторить попытку.

Чтобы настроить пользовательский интерфейс, определите значение `id` элемента `components-reconnect-modal` в блоке `<body>` страницы Razor `_Host.cshtml`.

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Добавьте следующее в таблицу стилей приложения (`wwwroot/css/app.css` или `wwwroot/css/site.css`):

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

В следующей таблице описаны классы CSS, применяемые к элементу `components-reconnect-modal`.

| Класс CSS                       | Означает&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Потеря соединения. Клиент пытается повторно подключиться. Отобразить модальное окно. |
| `components-reconnect-hide`     | Активное подключение к серверу восстановлено. Скрыть модальное окно. |
| `components-reconnect-failed`   | Сбой повторного подключения, возможно, из-за сбоя сети. Чтобы повторить попытку подключения, вызовите метод `window.Blazor.reconnect()`. |
| `components-reconnect-rejected` | Повторное подключение отклонено. Сервер был достигнут, но отклонил подключение, и состояние пользователя на сервере потеряно. Чтобы перезагрузить приложение, вызовите метод `location.reload()`. Это состояние соединения может появиться в следующих случаях:<ul><li>произошел сбой в цепи на стороне сервера;</li><li>клиент был отключен достаточно долго, чтобы сервер сбросил состояние пользователя. Экземпляры компонентов, с которыми взаимодействовал пользователь, удаляются;</li><li>сервер был перезагружен или был выполнен перезапуск рабочего процесса приложения.</li></ul> |

## <a name="render-mode"></a>Режим обработки

::: moniker range=">= aspnetcore-5.0"

*Этот раздел относится к размещенным Blazor WebAssembly и Blazor Server.*

Приложения Blazor по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере. Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

*Этот раздел относится к Blazor Server.*

Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено подключение клиента с сервером. Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

## <a name="initialize-the-no-locblazor-circuit"></a>Инициализация канала Blazor

*Этот раздел относится к Blazor Server.*

Настройте ручной запуск [ канала SignalR](xref:blazor/hosting-models#circuits) приложения Blazor Server в файле `Pages/_Host.cshtml`:

* добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;
* Разместите скрипт, который вызывает `Blazor.start` после тега скрипта `blazor.server.js` и внутри закрывающего тега `</body>`.

При отключении `autostart` все части приложения, которые не зависят от канала, работают нормально. Например, работает маршрутизация на стороне клиента. Однако все аспекты, зависящие от канала, не будут работать до вызова `Blazor.start`. Пока не будет установлен канал, поведение приложения будет непредсказуемым. Например, когда канал отключен, методы компонентов не выполняются.

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a>Инициализация Blazor при готовности документа

Чтобы инициализировать приложение Blazor, когда документ готов, используйте следующий код:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>Связывание с `Promise` в результате запуска вручную

Для выполнения дополнительных задач, таких как инициализация взаимодействия JS, используйте `then` для привязки к объекту `Promise`, полученному в результате запуска приложения Blazor вручную.

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a>Настройка клиента SignalR

#### <a name="logging"></a>Ведение журнала

Чтобы настроить ведение журнала клиента SignalR, передайте объект конфигурации (`configureSignalR`), который вызывает `configureLogging` с уровнем журнала для построителя клиента:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

В предыдущем примере `information` эквивалентно уровню журнала <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.

### <a name="modify-the-reconnection-handler"></a>Изменение обработчика повторного подключения

Для событий подключения канала обработчика повторного подключения можно настроить пользовательское поведение, например следующим способом:

* уведомлять пользователя, если подключение было разорвано;.
* вносить записи в журнал (со стороны клиента) при подключении канала.

Чтобы изменить события соединения, зарегистрируйте обратные вызовы для следующих изменений соединения:

* для прерванных соединений используется `onConnectionDown`;
* для устанавливаемых и повторно устанавливаемых соединений используется `onConnectionUp`.

Необходимо задать **как** `onConnectionDown`, так и `onConnectionUp`:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Настройка числа попыток и интервала повторного подключения

Чтобы настроить число попыток и интервал повторного подключения, задайте число повторных попыток (`maxRetries`) и периодичность в миллисекундах для каждой повторной попытки (`retryIntervalMilliseconds`):

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a>Скрытие или замена отображаемого элемента повторного подключения

Чтобы скрыть отображаемый элемент повторного подключения, задайте для свойства `_reconnectionDisplay` обработчика повторных подключений пустой объект (`{}` или `new Object()`):

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

Чтобы заменить отображаемый элемент повторного подключения, задайте для `_reconnectionDisplay` в предыдущем примере нужный элемент:

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

Заполнитель `{ELEMENT ID}` — это идентификатор элемента HTML для отображения.

::: moniker range=">= aspnetcore-5.0"

Чтобы настроить задержку перед появлением отображаемого элемента повторного подключения, задайте свойство `transition-delay` в файле CSS приложения (`wwwroot/css/site.css`) для модального элемента. В следующем примере задержка перехода изменяется с 500 мс (по умолчанию) на 1000 мс (1 секунда):

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a>Отключение канала Blazor от клиента

По умолчанию канал Blazor отключается при активации [события страницы `unload`](https://developer.mozilla.org/docs/Web/API/Window/unload_event). Чтобы отключить канал от клиента в других сценариях, вызовите `Blazor.disconnect` в соответствующем обработчике событий. В следующем примере канал отключается, когда скрывается страница ([событие `pagehide`](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="static-files"></a>Статические файлы

*Этот раздел относится к Blazor Server.*

Чтобы создать дополнительные сопоставления файлов с помощью <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> или настроить другие <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, используйте **один** из следующих способов. В следующих примерах заполнитель `{EXTENSION}` является расширением файла, а заполнитель `{CONTENT TYPE}` — типом содержимого.

* Настройте параметры с помощью [внедрения зависимостей (DI)](xref:blazor/fundamentals/dependency-injection) в `Startup.ConfigureServices` (`Startup.cs`) с помощью <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  Так как в этом способе настраивается тот же поставщик файлов, который используется для обслуживания `blazor.server.js`, убедитесь, что настраиваемая конфигурация не мешает обслуживать `blazor.server.js`. Например, не удаляйте сопоставление для файлов JavaScript, настраивая поставщик с помощью `provider.Mappings.Remove(".js")`.

* Используйте два вызова <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> в `Startup.Configure` (`Startup.cs`).
  * Настройте пользовательский поставщик файлов при первом вызове с помощью <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.
  * Второе ПО промежуточного слоя обслуживает `blazor.server.js`, в котором используется конфигурация статических файлов по умолчанию, предоставляемая платформой Blazor.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* Чтобы избежать конфликтов при обслуживании `_framework/blazor.server.js`, можете использовать <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> для выполнения пользовательского ПО промежуточного слоя для статических файлов.

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/logging/index>
* [События повторного подключения и события жизненного цикла Blazor Server](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
