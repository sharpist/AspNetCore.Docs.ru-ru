---
title: Конфигурация модели размещения ASP.NET Core Blazor
author: guardrex
description: Подробные сведения о дополнительных сценариях для конфигурации модели размещения ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
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
ms.openlocfilehash: dbad91e46a95d9ab5ec62d66e0d9a18938ff4520
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014468"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="ad75f-103">Конфигурация модели размещения ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ad75f-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="ad75f-104">Авторы: Дэниэл Рот ([Daniel Roth](https://github.com/danroth27)), Маккиннон Бак ([Mackinnon Buck](https://github.com/MackinnonBuck)) и Люк Лэтэм ([Luke Latham](https://github.com/guardrex))</span><span class="sxs-lookup"><span data-stu-id="ad75f-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ad75f-105">В этой статье рассматривается конфигурация модели размещения.</span><span class="sxs-lookup"><span data-stu-id="ad75f-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="ad75f-106">Согласование независимо от источника для проверки подлинности для SignalR</span><span class="sxs-lookup"><span data-stu-id="ad75f-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="ad75f-107">*Этот раздел относится к Blazor WebAssembly.*</span><span class="sxs-lookup"><span data-stu-id="ad75f-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="ad75f-108">Чтобы настроить базовый клиент SignalR для отправки учетных данных, таких как файлы cookie или заголовки проверки подлинности HTTP:</span><span class="sxs-lookup"><span data-stu-id="ad75f-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="ad75f-109">используйте <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>, чтобы задать <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> в независящих от источника запросах [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch):</span><span class="sxs-lookup"><span data-stu-id="ad75f-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="ad75f-110">присвойте <xref:System.Net.Http.HttpMessageHandler> параметру <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:</span><span class="sxs-lookup"><span data-stu-id="ad75f-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="ad75f-111">Для получения дополнительной информации см. <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="ad75f-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="ad75f-112">Отражение состояния соединения в пользовательском интерфейсе</span><span class="sxs-lookup"><span data-stu-id="ad75f-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="ad75f-113">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="ad75f-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ad75f-114">Когда клиент обнаруживает, что соединение было потеряно, пользовательский интерфейс по умолчанию отображается пользователю, в то время как клиент пытается восстановить соединение.</span><span class="sxs-lookup"><span data-stu-id="ad75f-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="ad75f-115">Если происходит повторный сбой подключения, пользователю предоставляется возможность повторить попытку.</span><span class="sxs-lookup"><span data-stu-id="ad75f-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="ad75f-116">Чтобы настроить пользовательский интерфейс, определите значение `id` элемента `components-reconnect-modal` в блоке `<body>` страницы Razor `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="ad75f-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="ad75f-117">Добавьте следующее в таблицу стилей приложения (`wwwroot/css/app.css` или `wwwroot/css/site.css`):</span><span class="sxs-lookup"><span data-stu-id="ad75f-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="ad75f-118">В следующей таблице описаны классы CSS, применяемые к элементу `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="ad75f-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="ad75f-119">Класс CSS</span><span class="sxs-lookup"><span data-stu-id="ad75f-119">CSS class</span></span>                       | <span data-ttu-id="ad75f-120">Означает&hellip;</span><span class="sxs-lookup"><span data-stu-id="ad75f-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="ad75f-121">Потеря соединения.</span><span class="sxs-lookup"><span data-stu-id="ad75f-121">A lost connection.</span></span> <span data-ttu-id="ad75f-122">Клиент пытается повторно подключиться.</span><span class="sxs-lookup"><span data-stu-id="ad75f-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="ad75f-123">Отобразить модальное окно.</span><span class="sxs-lookup"><span data-stu-id="ad75f-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="ad75f-124">Активное подключение к серверу восстановлено.</span><span class="sxs-lookup"><span data-stu-id="ad75f-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="ad75f-125">Скрыть модальное окно.</span><span class="sxs-lookup"><span data-stu-id="ad75f-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="ad75f-126">Сбой повторного подключения, возможно, из-за сбоя сети.</span><span class="sxs-lookup"><span data-stu-id="ad75f-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="ad75f-127">Чтобы повторить попытку подключения, вызовите метод `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="ad75f-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="ad75f-128">Повторное подключение отклонено.</span><span class="sxs-lookup"><span data-stu-id="ad75f-128">Reconnection rejected.</span></span> <span data-ttu-id="ad75f-129">Сервер был достигнут, но отклонил подключение, и состояние пользователя на сервере потеряно.</span><span class="sxs-lookup"><span data-stu-id="ad75f-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="ad75f-130">Чтобы перезагрузить приложение, вызовите метод `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="ad75f-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="ad75f-131">Это состояние соединения может появиться в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="ad75f-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="ad75f-132">произошел сбой в цепи на стороне сервера;</span><span class="sxs-lookup"><span data-stu-id="ad75f-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="ad75f-133">клиент был отключен достаточно долго, чтобы сервер сбросил состояние пользователя.</span><span class="sxs-lookup"><span data-stu-id="ad75f-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="ad75f-134">Экземпляры компонентов, с которыми взаимодействовал пользователь, удаляются;</span><span class="sxs-lookup"><span data-stu-id="ad75f-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="ad75f-135">сервер был перезагружен или был выполнен перезапуск рабочего процесса приложения.</span><span class="sxs-lookup"><span data-stu-id="ad75f-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="ad75f-136">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="ad75f-136">Render mode</span></span>

<span data-ttu-id="ad75f-137">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="ad75f-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ad75f-138">Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено подключение клиента с сервером.</span><span class="sxs-lookup"><span data-stu-id="ad75f-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="ad75f-139">Настройка выполняется на странице Razor `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="ad75f-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="ad75f-140">Параметр <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> настраивает одно из следующих поведений компонента:</span><span class="sxs-lookup"><span data-stu-id="ad75f-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="ad75f-141">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="ad75f-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="ad75f-142">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="ad75f-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="ad75f-143">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="ad75f-143">Render mode</span></span> | <span data-ttu-id="ad75f-144">Описание</span><span class="sxs-lookup"><span data-stu-id="ad75f-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="ad75f-145">Преобразует компонент в статический HTML и включает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="ad75f-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="ad75f-146">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="ad75f-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="ad75f-147">Отображает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="ad75f-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="ad75f-148">Выходные данные компонента не включаются.</span><span class="sxs-lookup"><span data-stu-id="ad75f-148">Output from the component isn't included.</span></span> <span data-ttu-id="ad75f-149">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="ad75f-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="ad75f-150">Преобразует компонент в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="ad75f-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="ad75f-151">Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="ad75f-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a><span data-ttu-id="ad75f-152">Настройка клиента SignalR для приложений Blazor Server</span><span class="sxs-lookup"><span data-stu-id="ad75f-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="ad75f-153">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="ad75f-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ad75f-154">Настройте клиент SignalR, используемый приложениями Blazor Server, в файле `Pages/_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="ad75f-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="ad75f-155">Разместите скрипт, который вызывает `Blazor.start` после скрипта `_framework/blazor.server.js` и внутри тега `</body>`.</span><span class="sxs-lookup"><span data-stu-id="ad75f-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="ad75f-156">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="ad75f-156">Logging</span></span>

<span data-ttu-id="ad75f-157">Чтобы настроить ведение журнала для клиента SignalR, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="ad75f-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="ad75f-158">добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;</span><span class="sxs-lookup"><span data-stu-id="ad75f-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="ad75f-159">передайте объект конфигурации (`configureSignalR`), который вызывает `configureLogging` с уровнем журнала для построителя клиента.</span><span class="sxs-lookup"><span data-stu-id="ad75f-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

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

<span data-ttu-id="ad75f-160">В предыдущем примере `information` эквивалентно уровню журнала <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="ad75f-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="ad75f-161">Изменение обработчика повторного подключения</span><span class="sxs-lookup"><span data-stu-id="ad75f-161">Modify the reconnection handler</span></span>

<span data-ttu-id="ad75f-162">Для событий подключения канала обработчика повторного подключения можно настроить пользовательское поведение, например следующим способом:</span><span class="sxs-lookup"><span data-stu-id="ad75f-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="ad75f-163">уведомлять пользователя, если подключение было разорвано;.</span><span class="sxs-lookup"><span data-stu-id="ad75f-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="ad75f-164">вносить записи в журнал (со стороны клиента) при подключении канала.</span><span class="sxs-lookup"><span data-stu-id="ad75f-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="ad75f-165">Чтобы изменить события подключения, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ad75f-165">To modify the connection events:</span></span>

* <span data-ttu-id="ad75f-166">добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;</span><span class="sxs-lookup"><span data-stu-id="ad75f-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="ad75f-167">регистрируйте обратные вызовы по изменениям подключений для разорванных подключений (`onConnectionDown`) и установленных (в том числе повторно) подключений (`onConnectionUp`),</span><span class="sxs-lookup"><span data-stu-id="ad75f-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="ad75f-168">при этом укажите **оба** метода `onConnectionDown` и `onConnectionUp`.</span><span class="sxs-lookup"><span data-stu-id="ad75f-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="ad75f-169">Настройка числа попыток и интервала повторного подключения</span><span class="sxs-lookup"><span data-stu-id="ad75f-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="ad75f-170">Чтобы настроить число попыток и интервал повторного подключения, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="ad75f-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="ad75f-171">добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;</span><span class="sxs-lookup"><span data-stu-id="ad75f-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="ad75f-172">задайте число повторных попыток (`maxRetries`) и периодичность в миллисекундах для каждой повторной попытки (`retryIntervalMilliseconds`).</span><span class="sxs-lookup"><span data-stu-id="ad75f-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="ad75f-173">Скрытие или замена отображаемого элемента повторного подключения</span><span class="sxs-lookup"><span data-stu-id="ad75f-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="ad75f-174">Чтобы скрыть отображаемый элемент повторного подключения, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="ad75f-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="ad75f-175">добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;</span><span class="sxs-lookup"><span data-stu-id="ad75f-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="ad75f-176">задайте для свойства `_reconnectionDisplay` обработчика повторных подключений пустой объект (`{}` или `new Object()`).</span><span class="sxs-lookup"><span data-stu-id="ad75f-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

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

<span data-ttu-id="ad75f-177">Чтобы заменить отображаемый элемент повторного подключения, задайте для `_reconnectionDisplay` в предыдущем примере нужный элемент:</span><span class="sxs-lookup"><span data-stu-id="ad75f-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="ad75f-178">Заполнитель `{ELEMENT ID}` — это идентификатор элемента HTML для отображения.</span><span class="sxs-lookup"><span data-stu-id="ad75f-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="ad75f-179">Влияние элементов тегов HTML `<head>`</span><span class="sxs-lookup"><span data-stu-id="ad75f-179">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="ad75f-180">*Этот раздел относится к предстоящему выпуску ASP.NET Core 5.0 Blazor WebAssembly и Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="ad75f-180">*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="ad75f-181">После преобразования для просмотра компоненты `Title`, `Link` и `Meta` добавляют или обновляют данные в элементах тегов HTML `<head>`:</span><span class="sxs-lookup"><span data-stu-id="ad75f-181">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="ad75f-182">В предыдущем примере заполнители для `{TITLE}`, `{URL}` и `{DESCRIPTION}` являются строковыми значениями, переменными Razor или выражениями Razor.</span><span class="sxs-lookup"><span data-stu-id="ad75f-182">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="ad75f-183">Применяются следующие характеристики:</span><span class="sxs-lookup"><span data-stu-id="ad75f-183">The following characteristics apply:</span></span>

* <span data-ttu-id="ad75f-184">Поддерживается предварительный рендеринг на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="ad75f-184">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="ad75f-185">Параметр `Value` является единственным допустимым параметром для компонента `Title`.</span><span class="sxs-lookup"><span data-stu-id="ad75f-185">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="ad75f-186">Атрибуты HTML, предоставляемые компонентам `Meta` и `Link`, фиксируются в [дополнительных атрибутах](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) и передаются в отрисованный HTML-тег.</span><span class="sxs-lookup"><span data-stu-id="ad75f-186">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="ad75f-187">Для нескольких компонентов `Title` заголовок страницы отражает `Value` из последних отрисованных компонентов `Title`.</span><span class="sxs-lookup"><span data-stu-id="ad75f-187">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="ad75f-188">Если несколько компонентов `Meta` или `Link` включены с одинаковыми атрибутами, то для каждого компонента `Meta` или `Link` отрисовывается ровно один HTML-тег.</span><span class="sxs-lookup"><span data-stu-id="ad75f-188">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="ad75f-189">Два компонента `Meta` или `Link` не могут ссылаться на один и тот же отрисованный тег HTML.</span><span class="sxs-lookup"><span data-stu-id="ad75f-189">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="ad75f-190">Изменения параметров существующих компонентов `Meta` или `Link` отражаются в отрисованных HTML-тегах.</span><span class="sxs-lookup"><span data-stu-id="ad75f-190">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="ad75f-191">Если компоненты `Link` или `Meta` больше не отрисовываются и поэтому удаляются платформой, их отрисованные HTML-теги будут удалены.</span><span class="sxs-lookup"><span data-stu-id="ad75f-191">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="ad75f-192">Когда один из компонентов платформы используется в дочернем компоненте, отрисованный HTML-тег влияет на любой другой дочерний компонент родительского компонента, пока выполняется отрисовка дочернего компонента, содержащего компонент платформы.</span><span class="sxs-lookup"><span data-stu-id="ad75f-192">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="ad75f-193">Различие между использованием одного из этих компонентов инфраструктуры в дочернем компоненте и помещение HTML-тега в `wwwroot/index.html` или `Pages/_Host.cshtml` состоит в том, что отрисованный HTML-тег компонента платформы выглядит так:</span><span class="sxs-lookup"><span data-stu-id="ad75f-193">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="ad75f-194">Может быть изменен состоянием приложения.</span><span class="sxs-lookup"><span data-stu-id="ad75f-194">Can be modified by application state.</span></span> <span data-ttu-id="ad75f-195">Жестко заданный HTML-тег не может быть изменен состоянием приложения.</span><span class="sxs-lookup"><span data-stu-id="ad75f-195">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="ad75f-196">Удаляется из HTML `<head>`, когда родительский компонент больше не отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="ad75f-196">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ad75f-197">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ad75f-197">Additional resources</span></span>

* <xref:fundamentals/logging/index>
