---
title: Конфигурация модели размещения ASP.NET Core Blazor
author: guardrex
description: Подробные сведения о дополнительных сценариях для конфигурации модели размещения ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: e62cb2ab865fbf57166d5ec3d1344183c00c2095
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059846"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="08cfd-103">Конфигурация модели размещения ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="08cfd-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="08cfd-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="08cfd-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="08cfd-105">В этой статье рассматривается конфигурация модели размещения.</span><span class="sxs-lookup"><span data-stu-id="08cfd-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="08cfd-106">Согласование независимо от источника для проверки подлинности для SignalR</span><span class="sxs-lookup"><span data-stu-id="08cfd-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="08cfd-107">*Этот раздел относится к Blazor WebAssembly.*</span><span class="sxs-lookup"><span data-stu-id="08cfd-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="08cfd-108">Чтобы настроить базовый клиент SignalRдля отправки учетных данных, таких как файлы cookie или заголовки проверки подлинности HTTP:</span><span class="sxs-lookup"><span data-stu-id="08cfd-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="08cfd-109">используйте <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>, чтобы задать <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> в независящих от источника запросах [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch):</span><span class="sxs-lookup"><span data-stu-id="08cfd-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="08cfd-110">присвойте <xref:System.Net.Http.HttpMessageHandler> параметру <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:</span><span class="sxs-lookup"><span data-stu-id="08cfd-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="08cfd-111">Для получения дополнительной информации см. <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="08cfd-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="08cfd-112">Отражение состояния соединения в пользовательском интерфейсе</span><span class="sxs-lookup"><span data-stu-id="08cfd-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="08cfd-113">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="08cfd-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="08cfd-114">Когда клиент обнаруживает, что соединение было потеряно, пользовательский интерфейс по умолчанию отображается пользователю, в то время как клиент пытается восстановить соединение.</span><span class="sxs-lookup"><span data-stu-id="08cfd-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="08cfd-115">Если происходит повторный сбой подключения, пользователю предоставляется возможность повторить попытку.</span><span class="sxs-lookup"><span data-stu-id="08cfd-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="08cfd-116">Чтобы настроить пользовательский интерфейс, определите значение `id` элемента `components-reconnect-modal` в блоке `<body>` страницы Razor `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="08cfd-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="08cfd-117">Добавьте следующее в таблицу стилей приложения (`wwwroot/css/app.css` или `wwwroot/css/site.css`):</span><span class="sxs-lookup"><span data-stu-id="08cfd-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="08cfd-118">В следующей таблице описаны классы CSS, применяемые к элементу `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="08cfd-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="08cfd-119">Класс CSS</span><span class="sxs-lookup"><span data-stu-id="08cfd-119">CSS class</span></span>                       | <span data-ttu-id="08cfd-120">Означает&hellip;</span><span class="sxs-lookup"><span data-stu-id="08cfd-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="08cfd-121">Потеря соединения.</span><span class="sxs-lookup"><span data-stu-id="08cfd-121">A lost connection.</span></span> <span data-ttu-id="08cfd-122">Клиент пытается повторно подключиться.</span><span class="sxs-lookup"><span data-stu-id="08cfd-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="08cfd-123">Отобразить модальное окно.</span><span class="sxs-lookup"><span data-stu-id="08cfd-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="08cfd-124">Активное подключение к серверу восстановлено.</span><span class="sxs-lookup"><span data-stu-id="08cfd-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="08cfd-125">Скрыть модальное окно.</span><span class="sxs-lookup"><span data-stu-id="08cfd-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="08cfd-126">Сбой повторного подключения, возможно, из-за сбоя сети.</span><span class="sxs-lookup"><span data-stu-id="08cfd-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="08cfd-127">Чтобы повторить попытку подключения, вызовите метод `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="08cfd-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="08cfd-128">Повторное подключение отклонено.</span><span class="sxs-lookup"><span data-stu-id="08cfd-128">Reconnection rejected.</span></span> <span data-ttu-id="08cfd-129">Сервер был достигнут, но отклонил подключение, и состояние пользователя на сервере потеряно.</span><span class="sxs-lookup"><span data-stu-id="08cfd-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="08cfd-130">Чтобы перезагрузить приложение, вызовите метод `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="08cfd-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="08cfd-131">Это состояние соединения может появиться в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="08cfd-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="08cfd-132">произошел сбой в цепи на стороне сервера;</span><span class="sxs-lookup"><span data-stu-id="08cfd-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="08cfd-133">клиент был отключен достаточно долго, чтобы сервер сбросил состояние пользователя.</span><span class="sxs-lookup"><span data-stu-id="08cfd-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="08cfd-134">Экземпляры компонентов, с которыми взаимодействовал пользователь, удаляются;</span><span class="sxs-lookup"><span data-stu-id="08cfd-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="08cfd-135">сервер был перезагружен или был выполнен перезапуск рабочего процесса приложения.</span><span class="sxs-lookup"><span data-stu-id="08cfd-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="08cfd-136">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="08cfd-136">Render mode</span></span>

<span data-ttu-id="08cfd-137">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="08cfd-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="08cfd-138">Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено подключение клиента с сервером.</span><span class="sxs-lookup"><span data-stu-id="08cfd-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="08cfd-139">Настройка выполняется на странице Razor `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="08cfd-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="08cfd-140">Параметр <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> настраивает одно из следующих поведений компонента:</span><span class="sxs-lookup"><span data-stu-id="08cfd-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="08cfd-141">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="08cfd-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="08cfd-142">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="08cfd-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="08cfd-143">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="08cfd-143">Render mode</span></span> | <span data-ttu-id="08cfd-144">Описание</span><span class="sxs-lookup"><span data-stu-id="08cfd-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="08cfd-145">Преобразует компонент в статический HTML и включает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="08cfd-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="08cfd-146">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="08cfd-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="08cfd-147">Отображает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="08cfd-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="08cfd-148">Выходные данные компонента не включаются.</span><span class="sxs-lookup"><span data-stu-id="08cfd-148">Output from the component isn't included.</span></span> <span data-ttu-id="08cfd-149">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="08cfd-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="08cfd-150">Преобразует компонент в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="08cfd-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="08cfd-151">Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="08cfd-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="08cfd-152">Настройка клиента SignalR для приложений Blazor Server</span><span class="sxs-lookup"><span data-stu-id="08cfd-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="08cfd-153">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="08cfd-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="08cfd-154">Иногда необходимо настроить клиент SignalR, используемый приложениями Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="08cfd-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="08cfd-155">Например, может потребоваться настроить ведение журнала на клиенте SignalR для диагностики проблемы с подключением.</span><span class="sxs-lookup"><span data-stu-id="08cfd-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="08cfd-156">Чтобы настроить клиент SignalR в файле `Pages/_Host.cshtml`, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="08cfd-156">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="08cfd-157">добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;</span><span class="sxs-lookup"><span data-stu-id="08cfd-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="08cfd-158">вызовите `Blazor.start` и передайте объект конфигурации, указывающий построитель SignalR.</span><span class="sxs-lookup"><span data-stu-id="08cfd-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="08cfd-159">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="08cfd-159">Additional resources</span></span>

* <xref:fundamentals/logging/index>
