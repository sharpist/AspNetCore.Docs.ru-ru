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
ms.openlocfilehash: b7fc3710fe5ad1efba907edf98f590a42e2a83ae
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485879"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="d13a2-103">Конфигурация модели размещения ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d13a2-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="d13a2-104">Авторы: Дэниэл Рот ([Daniel Roth](https://github.com/danroth27)), Маккиннон Бак ([Mackinnon Buck](https://github.com/MackinnonBuck)) и Люк Лэтэм ([Luke Latham](https://github.com/guardrex))</span><span class="sxs-lookup"><span data-stu-id="d13a2-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d13a2-105">В этой статье рассматривается конфигурация модели размещения.</span><span class="sxs-lookup"><span data-stu-id="d13a2-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="d13a2-106">Согласование независимо от источника для проверки подлинности для SignalR</span><span class="sxs-lookup"><span data-stu-id="d13a2-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="d13a2-107">*Этот раздел относится к Blazor WebAssembly.*</span><span class="sxs-lookup"><span data-stu-id="d13a2-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="d13a2-108">Чтобы настроить базовый клиент SignalR для отправки учетных данных, таких как файлы cookie или заголовки проверки подлинности HTTP:</span><span class="sxs-lookup"><span data-stu-id="d13a2-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="d13a2-109">используйте <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>, чтобы задать <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> в независящих от источника запросах [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch):</span><span class="sxs-lookup"><span data-stu-id="d13a2-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="d13a2-110">присвойте <xref:System.Net.Http.HttpMessageHandler> параметру <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:</span><span class="sxs-lookup"><span data-stu-id="d13a2-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="d13a2-111">Для получения дополнительной информации см. <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="d13a2-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="d13a2-112">Отражение состояния соединения в пользовательском интерфейсе</span><span class="sxs-lookup"><span data-stu-id="d13a2-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="d13a2-113">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="d13a2-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="d13a2-114">Когда клиент обнаруживает, что соединение было потеряно, пользовательский интерфейс по умолчанию отображается пользователю, в то время как клиент пытается восстановить соединение.</span><span class="sxs-lookup"><span data-stu-id="d13a2-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="d13a2-115">Если происходит повторный сбой подключения, пользователю предоставляется возможность повторить попытку.</span><span class="sxs-lookup"><span data-stu-id="d13a2-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="d13a2-116">Чтобы настроить пользовательский интерфейс, определите значение `id` элемента `components-reconnect-modal` в блоке `<body>` страницы Razor `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="d13a2-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="d13a2-117">Добавьте следующее в таблицу стилей приложения (`wwwroot/css/app.css` или `wwwroot/css/site.css`):</span><span class="sxs-lookup"><span data-stu-id="d13a2-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="d13a2-118">В следующей таблице описаны классы CSS, применяемые к элементу `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="d13a2-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="d13a2-119">Класс CSS</span><span class="sxs-lookup"><span data-stu-id="d13a2-119">CSS class</span></span>                       | <span data-ttu-id="d13a2-120">Означает&hellip;</span><span class="sxs-lookup"><span data-stu-id="d13a2-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="d13a2-121">Потеря соединения.</span><span class="sxs-lookup"><span data-stu-id="d13a2-121">A lost connection.</span></span> <span data-ttu-id="d13a2-122">Клиент пытается повторно подключиться.</span><span class="sxs-lookup"><span data-stu-id="d13a2-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="d13a2-123">Отобразить модальное окно.</span><span class="sxs-lookup"><span data-stu-id="d13a2-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="d13a2-124">Активное подключение к серверу восстановлено.</span><span class="sxs-lookup"><span data-stu-id="d13a2-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="d13a2-125">Скрыть модальное окно.</span><span class="sxs-lookup"><span data-stu-id="d13a2-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="d13a2-126">Сбой повторного подключения, возможно, из-за сбоя сети.</span><span class="sxs-lookup"><span data-stu-id="d13a2-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="d13a2-127">Чтобы повторить попытку подключения, вызовите метод `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="d13a2-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="d13a2-128">Повторное подключение отклонено.</span><span class="sxs-lookup"><span data-stu-id="d13a2-128">Reconnection rejected.</span></span> <span data-ttu-id="d13a2-129">Сервер был достигнут, но отклонил подключение, и состояние пользователя на сервере потеряно.</span><span class="sxs-lookup"><span data-stu-id="d13a2-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="d13a2-130">Чтобы перезагрузить приложение, вызовите метод `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="d13a2-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="d13a2-131">Это состояние соединения может появиться в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="d13a2-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="d13a2-132">произошел сбой в цепи на стороне сервера;</span><span class="sxs-lookup"><span data-stu-id="d13a2-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="d13a2-133">клиент был отключен достаточно долго, чтобы сервер сбросил состояние пользователя.</span><span class="sxs-lookup"><span data-stu-id="d13a2-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="d13a2-134">Экземпляры компонентов, с которыми взаимодействовал пользователь, удаляются;</span><span class="sxs-lookup"><span data-stu-id="d13a2-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="d13a2-135">сервер был перезагружен или был выполнен перезапуск рабочего процесса приложения.</span><span class="sxs-lookup"><span data-stu-id="d13a2-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="d13a2-136">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="d13a2-136">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d13a2-137">*Этот раздел относится к размещенным Blazor WebAssembly и Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="d13a2-137">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="d13a2-138">Приложения Blazor по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере.</span><span class="sxs-lookup"><span data-stu-id="d13a2-138">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="d13a2-139">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="d13a2-139">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d13a2-140">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="d13a2-140">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="d13a2-141">Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено подключение клиента с сервером.</span><span class="sxs-lookup"><span data-stu-id="d13a2-141">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="d13a2-142">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="d13a2-142">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="d13a2-143">Инициализация канала Blazor</span><span class="sxs-lookup"><span data-stu-id="d13a2-143">Initialize the Blazor circuit</span></span>

<span data-ttu-id="d13a2-144">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="d13a2-144">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="d13a2-145">Настройте ручной запуск [ канала SignalR](xref:blazor/hosting-models#circuits) приложения Blazor Server в файле `Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="d13a2-145">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="d13a2-146">добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;</span><span class="sxs-lookup"><span data-stu-id="d13a2-146">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="d13a2-147">Разместите скрипт, который вызывает `Blazor.start` после тега скрипта `blazor.server.js` и внутри закрывающего тега `</body>`.</span><span class="sxs-lookup"><span data-stu-id="d13a2-147">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="d13a2-148">При отключении `autostart` все части приложения, которые не зависят от канала, работают нормально.</span><span class="sxs-lookup"><span data-stu-id="d13a2-148">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="d13a2-149">Например, работает маршрутизация на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="d13a2-149">For example, client-side routing is operational.</span></span> <span data-ttu-id="d13a2-150">Однако все аспекты, зависящие от канала, не будут работать до вызова `Blazor.start`.</span><span class="sxs-lookup"><span data-stu-id="d13a2-150">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="d13a2-151">Пока не будет установлен канал, поведение приложения будет непредсказуемым.</span><span class="sxs-lookup"><span data-stu-id="d13a2-151">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="d13a2-152">Например, когда канал отключен, методы компонентов не выполняются.</span><span class="sxs-lookup"><span data-stu-id="d13a2-152">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="d13a2-153">Инициализация Blazor при готовности документа</span><span class="sxs-lookup"><span data-stu-id="d13a2-153">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="d13a2-154">Чтобы инициализировать приложение Blazor, когда документ готов, используйте следующий код:</span><span class="sxs-lookup"><span data-stu-id="d13a2-154">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="d13a2-155">Связывание с `Promise` в результате запуска вручную</span><span class="sxs-lookup"><span data-stu-id="d13a2-155">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="d13a2-156">Для выполнения дополнительных задач, таких как инициализация взаимодействия JS, используйте `then` для привязки к объекту `Promise`, полученному в результате запуска приложения Blazor вручную.</span><span class="sxs-lookup"><span data-stu-id="d13a2-156">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="d13a2-157">Настройка клиента SignalR</span><span class="sxs-lookup"><span data-stu-id="d13a2-157">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="d13a2-158">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="d13a2-158">Logging</span></span>

<span data-ttu-id="d13a2-159">Чтобы настроить ведение журнала клиента SignalR, передайте объект конфигурации (`configureSignalR`), который вызывает `configureLogging` с уровнем журнала для построителя клиента:</span><span class="sxs-lookup"><span data-stu-id="d13a2-159">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="d13a2-160">В предыдущем примере `information` эквивалентно уровню журнала <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="d13a2-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="d13a2-161">Изменение обработчика повторного подключения</span><span class="sxs-lookup"><span data-stu-id="d13a2-161">Modify the reconnection handler</span></span>

<span data-ttu-id="d13a2-162">Для событий подключения канала обработчика повторного подключения можно настроить пользовательское поведение, например следующим способом:</span><span class="sxs-lookup"><span data-stu-id="d13a2-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="d13a2-163">уведомлять пользователя, если подключение было разорвано;.</span><span class="sxs-lookup"><span data-stu-id="d13a2-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="d13a2-164">вносить записи в журнал (со стороны клиента) при подключении канала.</span><span class="sxs-lookup"><span data-stu-id="d13a2-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="d13a2-165">Чтобы изменить события соединения, зарегистрируйте обратные вызовы для следующих изменений соединения:</span><span class="sxs-lookup"><span data-stu-id="d13a2-165">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="d13a2-166">для прерванных соединений используется `onConnectionDown`;</span><span class="sxs-lookup"><span data-stu-id="d13a2-166">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="d13a2-167">для устанавливаемых и повторно устанавливаемых соединений используется `onConnectionUp`.</span><span class="sxs-lookup"><span data-stu-id="d13a2-167">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="d13a2-168">Необходимо задать **как** `onConnectionDown`, так и `onConnectionUp`:</span><span class="sxs-lookup"><span data-stu-id="d13a2-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="d13a2-169">Настройка числа попыток и интервала повторного подключения</span><span class="sxs-lookup"><span data-stu-id="d13a2-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="d13a2-170">Чтобы настроить число попыток и интервал повторного подключения, задайте число повторных попыток (`maxRetries`) и периодичность в миллисекундах для каждой повторной попытки (`retryIntervalMilliseconds`):</span><span class="sxs-lookup"><span data-stu-id="d13a2-170">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="d13a2-171">Скрытие или замена отображаемого элемента повторного подключения</span><span class="sxs-lookup"><span data-stu-id="d13a2-171">Hide or replace the reconnection display</span></span>

<span data-ttu-id="d13a2-172">Чтобы скрыть отображаемый элемент повторного подключения, задайте для свойства `_reconnectionDisplay` обработчика повторных подключений пустой объект (`{}` или `new Object()`):</span><span class="sxs-lookup"><span data-stu-id="d13a2-172">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="d13a2-173">Чтобы заменить отображаемый элемент повторного подключения, задайте для `_reconnectionDisplay` в предыдущем примере нужный элемент:</span><span class="sxs-lookup"><span data-stu-id="d13a2-173">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="d13a2-174">Заполнитель `{ELEMENT ID}` — это идентификатор элемента HTML для отображения.</span><span class="sxs-lookup"><span data-stu-id="d13a2-174">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d13a2-175">Чтобы настроить задержку перед появлением отображаемого элемента повторного подключения, задайте свойство `transition-delay` в файле CSS приложения (`wwwroot/css/site.css`) для модального элемента.</span><span class="sxs-lookup"><span data-stu-id="d13a2-175">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="d13a2-176">В следующем примере задержка перехода изменяется с 500 мс (по умолчанию) на 1000 мс (1 секунда):</span><span class="sxs-lookup"><span data-stu-id="d13a2-176">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="d13a2-177">Отключение канала Blazor от клиента</span><span class="sxs-lookup"><span data-stu-id="d13a2-177">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="d13a2-178">По умолчанию канал Blazor отключается при активации [события страницы `unload`](https://developer.mozilla.org/docs/Web/API/Window/unload_event).</span><span class="sxs-lookup"><span data-stu-id="d13a2-178">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="d13a2-179">Чтобы отключить канал от клиента в других сценариях, вызовите `Blazor.disconnect` в соответствующем обработчике событий.</span><span class="sxs-lookup"><span data-stu-id="d13a2-179">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="d13a2-180">В следующем примере канал отключается, когда скрывается страница ([событие `pagehide`](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="d13a2-180">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

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

## <a name="static-files"></a><span data-ttu-id="d13a2-181">Статические файлы</span><span class="sxs-lookup"><span data-stu-id="d13a2-181">Static files</span></span>

<span data-ttu-id="d13a2-182">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="d13a2-182">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="d13a2-183">Чтобы создать дополнительные сопоставления файлов с помощью <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> или настроить другие <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, используйте **один** из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="d13a2-183">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="d13a2-184">В следующих примерах заполнитель `{EXTENSION}` является расширением файла, а заполнитель `{CONTENT TYPE}` — типом содержимого.</span><span class="sxs-lookup"><span data-stu-id="d13a2-184">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="d13a2-185">Настройте параметры с помощью [внедрения зависимостей (DI)](xref:blazor/fundamentals/dependency-injection) в `Startup.ConfigureServices` (`Startup.cs`) с помощью <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="d13a2-185">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

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

  <span data-ttu-id="d13a2-186">Так как в этом способе настраивается тот же поставщик файлов, который используется для обслуживания `blazor.server.js`, убедитесь, что настраиваемая конфигурация не мешает обслуживать `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="d13a2-186">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="d13a2-187">Например, не удаляйте сопоставление для файлов JavaScript, настраивая поставщик с помощью `provider.Mappings.Remove(".js")`.</span><span class="sxs-lookup"><span data-stu-id="d13a2-187">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="d13a2-188">Используйте два вызова <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> в `Startup.Configure` (`Startup.cs`).</span><span class="sxs-lookup"><span data-stu-id="d13a2-188">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="d13a2-189">Настройте пользовательский поставщик файлов при первом вызове с помощью <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="d13a2-189">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="d13a2-190">Второе ПО промежуточного слоя обслуживает `blazor.server.js`, в котором используется конфигурация статических файлов по умолчанию, предоставляемая платформой Blazor.</span><span class="sxs-lookup"><span data-stu-id="d13a2-190">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="d13a2-191">Чтобы избежать конфликтов при обслуживании `_framework/blazor.server.js`, можете использовать <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> для выполнения пользовательского ПО промежуточного слоя для статических файлов.</span><span class="sxs-lookup"><span data-stu-id="d13a2-191">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="d13a2-192">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d13a2-192">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* [<span data-ttu-id="d13a2-193">События повторного подключения и события жизненного цикла Blazor Server</span><span class="sxs-lookup"><span data-stu-id="d13a2-193">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
