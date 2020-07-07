---
title: Конфигурация модели размещения ASP.NET Core Blazor
author: guardrex
description: Подробные сведения о дополнительных сценариях для конфигурации модели размещения ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 236dffd829bcd7c30ae1145242ce07cd8e9857e6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402953"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="6f360-103">Конфигурация модели размещения ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6f360-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="6f360-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6f360-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6f360-105">В этой статье рассматривается конфигурация модели размещения.</span><span class="sxs-lookup"><span data-stu-id="6f360-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="6f360-106">Согласование независимо от источника для проверки подлинности для SignalR</span><span class="sxs-lookup"><span data-stu-id="6f360-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="6f360-107">*Этот раздел относится к Blazor WebAssembly.*</span><span class="sxs-lookup"><span data-stu-id="6f360-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="6f360-108">Чтобы настроить базовый клиент SignalRдля отправки учетных данных, таких как файлы cookie или заголовки проверки подлинности HTTP:</span><span class="sxs-lookup"><span data-stu-id="6f360-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="6f360-109">используйте <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>, чтобы задать <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> в независящих от источника запросах [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch):</span><span class="sxs-lookup"><span data-stu-id="6f360-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="6f360-110">присвойте <xref:System.Net.Http.HttpMessageHandler> параметру <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:</span><span class="sxs-lookup"><span data-stu-id="6f360-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="6f360-111">Для получения дополнительной информации см. <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="6f360-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="6f360-112">Отражение состояния соединения в пользовательском интерфейсе</span><span class="sxs-lookup"><span data-stu-id="6f360-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="6f360-113">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="6f360-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="6f360-114">Когда клиент обнаруживает, что соединение было потеряно, пользовательский интерфейс по умолчанию отображается пользователю, в то время как клиент пытается восстановить соединение.</span><span class="sxs-lookup"><span data-stu-id="6f360-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="6f360-115">Если происходит повторный сбой подключения, пользователю предоставляется возможность повторить попытку.</span><span class="sxs-lookup"><span data-stu-id="6f360-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="6f360-116">Чтобы настроить пользовательский интерфейс, определите значение `id` элемента `components-reconnect-modal` в блоке `<body>` страницы Razor `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="6f360-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="6f360-117">В следующей таблице описаны классы CSS, применяемые к элементу `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="6f360-117">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="6f360-118">Класс CSS</span><span class="sxs-lookup"><span data-stu-id="6f360-118">CSS class</span></span>                       | <span data-ttu-id="6f360-119">Означает&hellip;</span><span class="sxs-lookup"><span data-stu-id="6f360-119">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="6f360-120">Потеря соединения.</span><span class="sxs-lookup"><span data-stu-id="6f360-120">A lost connection.</span></span> <span data-ttu-id="6f360-121">Клиент пытается повторно подключиться.</span><span class="sxs-lookup"><span data-stu-id="6f360-121">The client is attempting to reconnect.</span></span> <span data-ttu-id="6f360-122">Отобразить модальное окно.</span><span class="sxs-lookup"><span data-stu-id="6f360-122">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="6f360-123">Активное подключение к серверу восстановлено.</span><span class="sxs-lookup"><span data-stu-id="6f360-123">An active connection is re-established to the server.</span></span> <span data-ttu-id="6f360-124">Скрыть модальное окно.</span><span class="sxs-lookup"><span data-stu-id="6f360-124">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="6f360-125">Сбой повторного подключения, возможно, из-за сбоя сети.</span><span class="sxs-lookup"><span data-stu-id="6f360-125">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="6f360-126">Чтобы повторить попытку подключения, вызовите метод `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="6f360-126">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="6f360-127">Повторное подключение отклонено.</span><span class="sxs-lookup"><span data-stu-id="6f360-127">Reconnection rejected.</span></span> <span data-ttu-id="6f360-128">Сервер был достигнут, но отклонил подключение, и состояние пользователя на сервере потеряно.</span><span class="sxs-lookup"><span data-stu-id="6f360-128">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="6f360-129">Чтобы перезагрузить приложение, вызовите метод `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="6f360-129">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="6f360-130">Это состояние соединения может появиться в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="6f360-130">This connection state may result when:</span></span><ul><li><span data-ttu-id="6f360-131">произошел сбой в цепи на стороне сервера;</span><span class="sxs-lookup"><span data-stu-id="6f360-131">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="6f360-132">клиент был отключен достаточно долго, чтобы сервер сбросил состояние пользователя.</span><span class="sxs-lookup"><span data-stu-id="6f360-132">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="6f360-133">Экземпляры компонентов, с которыми взаимодействовал пользователь, удаляются;</span><span class="sxs-lookup"><span data-stu-id="6f360-133">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="6f360-134">сервер был перезагружен или был выполнен перезапуск рабочего процесса приложения.</span><span class="sxs-lookup"><span data-stu-id="6f360-134">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="6f360-135">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="6f360-135">Render mode</span></span>

<span data-ttu-id="6f360-136">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="6f360-136">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="6f360-137">Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено подключение клиента с сервером.</span><span class="sxs-lookup"><span data-stu-id="6f360-137">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="6f360-138">Настройка выполняется на странице Razor `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="6f360-138">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="6f360-139">Параметр <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> настраивает одно из следующих поведений компонента:</span><span class="sxs-lookup"><span data-stu-id="6f360-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="6f360-140">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="6f360-140">Is prerendered into the page.</span></span>
* <span data-ttu-id="6f360-141">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="6f360-141">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="6f360-142">Описание</span><span class="sxs-lookup"><span data-stu-id="6f360-142">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="6f360-143">Преобразует компонент в статический HTML и включает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="6f360-143">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="6f360-144">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="6f360-144">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="6f360-145">Отображает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="6f360-145">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="6f360-146">Выходные данные компонента не включаются.</span><span class="sxs-lookup"><span data-stu-id="6f360-146">Output from the component isn't included.</span></span> <span data-ttu-id="6f360-147">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="6f360-147">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="6f360-148">Преобразует компонент в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="6f360-148">Renders the component into static HTML.</span></span> |

<span data-ttu-id="6f360-149">Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="6f360-149">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="6f360-150">Настройка клиента SignalR для приложений Blazor Server</span><span class="sxs-lookup"><span data-stu-id="6f360-150">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="6f360-151">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="6f360-151">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="6f360-152">Иногда необходимо настроить клиент SignalR, используемый приложениями Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="6f360-152">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="6f360-153">Например, может потребоваться настроить ведение журнала на клиенте SignalR для диагностики проблемы с подключением.</span><span class="sxs-lookup"><span data-stu-id="6f360-153">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="6f360-154">Чтобы настроить клиент SignalR в файле `Pages/_Host.cshtml`, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="6f360-154">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="6f360-155">добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;</span><span class="sxs-lookup"><span data-stu-id="6f360-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="6f360-156">вызовите `Blazor.start` и передайте объект конфигурации, указывающий построитель SignalR.</span><span class="sxs-lookup"><span data-stu-id="6f360-156">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="6f360-157">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6f360-157">Additional resources</span></span>

* <xref:fundamentals/logging/index>
