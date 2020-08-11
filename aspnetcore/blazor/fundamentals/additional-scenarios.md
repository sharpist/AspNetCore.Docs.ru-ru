---
title: Конфигурация модели размещения ASP.NET Core Blazor
author: guardrex
description: Подробные сведения о дополнительных сценариях для конфигурации модели размещения ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b32710e515d111b7dd6556f1db55082cd56a82b5
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819006"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>Конфигурация модели размещения ASP.NET Core Blazor

Авторы: Дэниэл Рот ([Daniel Roth](https://github.com/danroth27)), Маккиннон Бак ([Mackinnon Buck](https://github.com/MackinnonBuck)) и Люк Лэтэм ([Luke Latham](https://github.com/guardrex))

В этой статье рассматривается конфигурация модели размещения.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>Согласование независимо от источника для проверки подлинности для SignalR

*Этот раздел относится к Blazor WebAssembly.*

Чтобы настроить базовый клиент SignalRдля отправки учетных данных, таких как файлы cookie или заголовки проверки подлинности HTTP:

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

*Этот раздел относится к Blazor Server.*

Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено подключение клиента с сервером. Настройка выполняется на странице Razor `_Host.cshtml`.

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

| Режим обработки | Описание |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Преобразует компонент в статический HTML и включает метку приложения Blazor Server. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Отображает метку приложения Blazor Server. Выходные данные компонента не включаются. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Преобразует компонент в статический HTML. |

Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a>Настройка клиента SignalR для приложений Blazor Server

*Этот раздел относится к Blazor Server.*

Настройте клиент SignalR, используемый приложениями Blazor Server, в файле `Pages/_Host.cshtml`. Разместите скрипт, который вызывает `Blazor.start` после скрипта `_framework/blazor.server.js` и внутри тега `</body>`.

### <a name="logging"></a>Ведение журнала

Чтобы настроить ведение журнала для клиента SignalR, выполните следующие действия:

* добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;
* передайте объект конфигурации (`configureSignalR`), который вызывает `configureLogging` с уровнем журнала для построителя клиента.

```cshtml
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

Чтобы изменить события подключения, сделайте следующее:

* добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;
* регистрируйте обратные вызовы по изменениям подключений для разорванных подключений (`onConnectionDown`) и установленных (в том числе повторно) подключений (`onConnectionUp`), при этом укажите **оба** метода `onConnectionDown` и `onConnectionUp`.

```cshtml
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

Чтобы настроить число попыток и интервал повторного подключения, выполните следующие действия:

* добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;
* задайте число повторных попыток (`maxRetries`) и периодичность в миллисекундах для каждой повторной попытки (`retryIntervalMilliseconds`).

```cshtml
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

### <a name="hide-or-replace-the-reconnection-display"></a>Скрытие или замена отображаемого элемента повторного подключения

Чтобы скрыть отображаемый элемент повторного подключения, выполните следующие действия:

* добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;
* задайте для свойства `_reconnectionDisplay` обработчика повторных подключений пустой объект (`{}` или `new Object()`).

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
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

## <a name="influence-html-head-tag-elements"></a>Влияние элементов тегов HTML `<head>`

*Этот раздел относится к Blazor WebAssembly и Blazor Server.*

После преобразования для просмотра компоненты `Title`, `Link` и `Meta` добавляют или обновляют данные в элементах тегов HTML `<head>`:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

В предыдущем примере заполнители для `{TITLE}`, `{URL}` и `{DESCRIPTION}` являются строковыми значениями, переменными Razor или выражениями Razor.

Применяются следующие характеристики:

* Поддерживается предварительный рендеринг на стороне сервера.
* Параметр `Value` является единственным допустимым параметром для компонента `Title`.
* Атрибуты HTML, предоставляемые компонентам `Meta` и `Link`, фиксируются в [дополнительных атрибутах](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) и передаются в отрисованный HTML-тег.
* Для нескольких компонентов `Title` заголовок страницы отражает `Value` из последних отрисованных компонентов `Title`.
* Если несколько компонентов `Meta` или `Link` включены с одинаковыми атрибутами, то для каждого компонента `Meta` или `Link` отрисовывается ровно один HTML-тег. Два компонента `Meta` или `Link` не могут ссылаться на один и тот же отрисованный тег HTML.
* Изменения параметров существующих компонентов `Meta` или `Link` отражаются в отрисованных HTML-тегах.
* Если компоненты `Link` или `Meta` больше не отрисовываются и поэтому удаляются платформой, их отрисованные HTML-теги будут удалены.

Когда один из компонентов платформы используется в дочернем компоненте, отрисованный HTML-тег влияет на любой другой дочерний компонент родительского компонента, пока выполняется отрисовка дочернего компонента, содержащего компонент платформы. Различие между использованием одного из этих компонентов инфраструктуры в дочернем компоненте и помещение HTML-тега в `wwwroot/index.html` или `Pages/_Host.cshtml` состоит в том, что отрисованный HTML-тег компонента платформы выглядит так:

* Может быть изменен состоянием приложения. Жестко заданный HTML-тег не может быть изменен состоянием приложения.
* Удаляется из HTML `<head>`, когда родительский компонент больше не отрисовывается.

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/logging/index>
