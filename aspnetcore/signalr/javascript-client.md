---
title: ASP.NET Core SignalR клиент JavaScript
author: bradygaster
description: Общие сведения о ASP.NET Core SignalR клиента JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
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
uid: signalr/javascript-client
ms.openlocfilehash: 6fc586d144547585ef75d653bf54193def5c8b7f
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606682"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="144f4-103">ASP.NET Core SignalR клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="144f4-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="144f4-104">Автор: [Рэйчел Аппель](https://twitter.com/rachelappel) (Rachel Appel)</span><span class="sxs-lookup"><span data-stu-id="144f4-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="144f4-105">SignalRКлиентская библиотека ASP.NET Core JavaScript позволяет разработчикам вызывать код концентратора на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="144f4-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="144f4-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="144f4-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="144f4-107">Установка SignalR клиентского пакета</span><span class="sxs-lookup"><span data-stu-id="144f4-107">Install the SignalR client package</span></span>

<span data-ttu-id="144f4-108">SignalRКлиентская библиотека JavaScript доставляется в виде пакета [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="144f4-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="144f4-109">В следующих разделах описаны различные способы установки клиентской библиотеки.</span><span class="sxs-lookup"><span data-stu-id="144f4-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="144f4-110">Установка с помощью NPM</span><span class="sxs-lookup"><span data-stu-id="144f4-110">Install with npm</span></span>

<span data-ttu-id="144f4-111">Для Visual Studio выполните следующие команды из **консоли диспетчера пакетов** , находясь в корневой папке.</span><span class="sxs-lookup"><span data-stu-id="144f4-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="144f4-112">Для Visual Studio Code выполните следующие команды в **интегрированном терминале**.</span><span class="sxs-lookup"><span data-stu-id="144f4-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="144f4-113">NPM устанавливает содержимое пакета в папку \*node_modules \\ @microsoft\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="144f4-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="144f4-114">Создайте новую папку с именем *SignalR* в папке *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="144f4-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="144f4-115">Скопируйте файл *signalr.js* в папку *ввврут\либ\сигналр*</span><span class="sxs-lookup"><span data-stu-id="144f4-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="144f4-116">Сослаться на SignalR клиент JavaScript в `<script>` элементе.</span><span class="sxs-lookup"><span data-stu-id="144f4-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="144f4-117">Пример:</span><span class="sxs-lookup"><span data-stu-id="144f4-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="144f4-118">Использование сети доставки содержимого (CDN)</span><span class="sxs-lookup"><span data-stu-id="144f4-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="144f4-119">Чтобы использовать клиентскую библиотеку без необходимых компонентов NPM, сослаться на копию клиентской библиотеки, размещенную в CDN.</span><span class="sxs-lookup"><span data-stu-id="144f4-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="144f4-120">Пример:</span><span class="sxs-lookup"><span data-stu-id="144f4-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="144f4-121">Клиентская библиотека доступна в следующих CDN:</span><span class="sxs-lookup"><span data-stu-id="144f4-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="144f4-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="144f4-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="144f4-123">жсделивр</span><span class="sxs-lookup"><span data-stu-id="144f4-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="144f4-124">унпкг</span><span class="sxs-lookup"><span data-stu-id="144f4-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="144f4-125">Установка с помощью Либман</span><span class="sxs-lookup"><span data-stu-id="144f4-125">Install with LibMan</span></span>

<span data-ttu-id="144f4-126">[Либман](xref:client-side/libman/index) можно использовать для установки конкретных файлов клиентской библиотеки из клиентской библиотеки, размещенной в CDN.</span><span class="sxs-lookup"><span data-stu-id="144f4-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="144f4-127">Например, добавьте в проект только файл JavaScript минифицированные.</span><span class="sxs-lookup"><span data-stu-id="144f4-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="144f4-128">Дополнительные сведения об этом подходе см. в разделе [Добавление SignalR клиентской библиотеки](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="144f4-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="144f4-129">Подключение к концентратору</span><span class="sxs-lookup"><span data-stu-id="144f4-129">Connect to a hub</span></span>

<span data-ttu-id="144f4-130">Следующий код создает и запускает соединение.</span><span class="sxs-lookup"><span data-stu-id="144f4-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="144f4-131">Имя центра не учитывает регистр:</span><span class="sxs-lookup"><span data-stu-id="144f4-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="144f4-132">Подключения между источниками</span><span class="sxs-lookup"><span data-stu-id="144f4-132">Cross-origin connections</span></span>

<span data-ttu-id="144f4-133">Как правило, Браузеры загружают соединения из того же домена, что и запрошенная страница.</span><span class="sxs-lookup"><span data-stu-id="144f4-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="144f4-134">Однако бывают случаи, когда требуется подключение к другому домену.</span><span class="sxs-lookup"><span data-stu-id="144f4-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="144f4-135">Чтобы предотвратить чтение конфиденциальных данных с другого сайта вредоносным сайтом, [межисточниковые подключения](xref:security/cors) отключены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="144f4-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="144f4-136">Чтобы разрешить запрос между источниками, включите его в `Startup` классе:</span><span class="sxs-lookup"><span data-stu-id="144f4-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="144f4-137">Методы концентратора вызовов от клиента</span><span class="sxs-lookup"><span data-stu-id="144f4-137">Call hub methods from the client</span></span>

<span data-ttu-id="144f4-138">Клиенты JavaScript вызывают открытые методы на концентраторах с помощью метода [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) [хубконнектион](/javascript/api/%40microsoft/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="144f4-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="144f4-139">`invoke`Метод принимает:</span><span class="sxs-lookup"><span data-stu-id="144f4-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="144f4-140">Имя метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="144f4-140">The name of the hub method.</span></span>
* <span data-ttu-id="144f4-141">Любые аргументы, определенные в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="144f4-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="144f4-142">В следующем примере имя метода в концентраторе — `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="144f4-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="144f4-143">Второй и третий аргументы, передаваемые для `invoke` сопоставлений с `user` аргументами и аргументов метода концентратора `message` :</span><span class="sxs-lookup"><span data-stu-id="144f4-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="144f4-144">Вызов методов концентратора из клиента поддерживается только при использовании SignalR службы Azure в режиме *по умолчанию* .</span><span class="sxs-lookup"><span data-stu-id="144f4-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="144f4-145">Дополнительные сведения см. в разделе [часто задаваемые вопросы (репозиторий GitHub Azure SignalR)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="144f4-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="144f4-146">`invoke`Метод возвращает [обещание](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript.</span><span class="sxs-lookup"><span data-stu-id="144f4-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="144f4-147">`Promise`Разрешение разрешается с возвращаемым значением (если таковое имеется), когда метод на сервере возвращает значение.</span><span class="sxs-lookup"><span data-stu-id="144f4-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="144f4-148">Если метод на сервере вызывает ошибку, объект `Promise` отклоняется с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="144f4-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="144f4-149">`async` `await` `Promise` `then` `catch` Для решения этих случаев используйте методы и или.</span><span class="sxs-lookup"><span data-stu-id="144f4-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="144f4-150">Клиенты JavaScript также могут вызывать открытые методы на концентраторах через метод [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) объекта `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="144f4-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="144f4-151">В отличие от `invoke` метода, `send` метод не ждет ответа от сервера.</span><span class="sxs-lookup"><span data-stu-id="144f4-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="144f4-152">`send`Метод возвращает код JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="144f4-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="144f4-153">`Promise`Разрешение разрешается, когда сообщение было отправлено на сервер.</span><span class="sxs-lookup"><span data-stu-id="144f4-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="144f4-154">Если при отправке сообщения произошла ошибка, объект `Promise` отклоняется с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="144f4-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="144f4-155">`async` `await` `Promise` `then` `catch` Для решения этих случаев используйте методы и или.</span><span class="sxs-lookup"><span data-stu-id="144f4-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="144f4-156">Использование `send` не ждет, пока сервер не получит сообщение.</span><span class="sxs-lookup"><span data-stu-id="144f4-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="144f4-157">Следовательно, невозможно вернуть данные или ошибки с сервера.</span><span class="sxs-lookup"><span data-stu-id="144f4-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="144f4-158">Вызов методов клиента из концентратора</span><span class="sxs-lookup"><span data-stu-id="144f4-158">Call client methods from the hub</span></span>

<span data-ttu-id="144f4-159">Чтобы получить сообщения от концентратора, определите метод с помощью метода [On](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) класса `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="144f4-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="144f4-160">Имя клиентского метода JavaScript.</span><span class="sxs-lookup"><span data-stu-id="144f4-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="144f4-161">Аргументы, которые центр передает в метод.</span><span class="sxs-lookup"><span data-stu-id="144f4-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="144f4-162">В следующем примере имя метода — `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="144f4-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="144f4-163">Имена аргументов `user` `message` : и.</span><span class="sxs-lookup"><span data-stu-id="144f4-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="144f4-164">Приведенный выше код `connection.on` выполняется, когда код на стороне сервера вызывает его с помощью <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> метода:</span><span class="sxs-lookup"><span data-stu-id="144f4-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="144f4-165">SignalR Определяет, какой клиентский метод следует вызывать, сопоставляя имя и аргументы метода, определенные в `SendAsync` и `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="144f4-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="144f4-166">Рекомендуется вызвать метод [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) для `HubConnection` After `on` .</span><span class="sxs-lookup"><span data-stu-id="144f4-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="144f4-167">Это гарантирует, что обработчики будут зарегистрированы до получения сообщений.</span><span class="sxs-lookup"><span data-stu-id="144f4-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="144f4-168">Обработка ошибок и ведение журнала</span><span class="sxs-lookup"><span data-stu-id="144f4-168">Error handling and logging</span></span>

<span data-ttu-id="144f4-169">`try` `catch` `async` `await` `Promise` `catch` Для управления ошибками на стороне клиента используйте и с методом, или.</span><span class="sxs-lookup"><span data-stu-id="144f4-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="144f4-170">Используйте `console.error` для вывода ошибок в консоль браузера:</span><span class="sxs-lookup"><span data-stu-id="144f4-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="144f4-171">Настройте трассировку журнала на стороне клиента, передав средства ведения журнала и тип события в журнал, когда соединение установлено.</span><span class="sxs-lookup"><span data-stu-id="144f4-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="144f4-172">Сообщения записываются с указанным уровнем ведения журнала и выше.</span><span class="sxs-lookup"><span data-stu-id="144f4-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="144f4-173">Доступны следующие уровни ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="144f4-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="144f4-174">`signalR.LogLevel.Error`: Сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="144f4-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="144f4-175">Записывает `Error` только сообщения.</span><span class="sxs-lookup"><span data-stu-id="144f4-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="144f4-176">`signalR.LogLevel.Warning`: Предупреждающие сообщения о возможных ошибках.</span><span class="sxs-lookup"><span data-stu-id="144f4-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="144f4-177">Журналы `Warning` и `Error` сообщения.</span><span class="sxs-lookup"><span data-stu-id="144f4-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="144f4-178">`signalR.LogLevel.Information`: Сообщения о состоянии без ошибок.</span><span class="sxs-lookup"><span data-stu-id="144f4-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="144f4-179">Журналы `Information` , `Warning` и `Error` сообщения.</span><span class="sxs-lookup"><span data-stu-id="144f4-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="144f4-180">`signalR.LogLevel.Trace`: Сообщения трассировки.</span><span class="sxs-lookup"><span data-stu-id="144f4-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="144f4-181">Заносит в журнал все данные, включая транспорт данных между концентратором и клиентом.</span><span class="sxs-lookup"><span data-stu-id="144f4-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="144f4-182">Чтобы настроить уровень ведения журнала, используйте метод [конфигурелоггинг](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) в [хубконнектионбуилдер](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="144f4-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="144f4-183">Сообщения регистрируются в консоли браузера:</span><span class="sxs-lookup"><span data-stu-id="144f4-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="144f4-184">Повторное подключение клиентов</span><span class="sxs-lookup"><span data-stu-id="144f4-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="144f4-185">Автоматическое повторное подключение</span><span class="sxs-lookup"><span data-stu-id="144f4-185">Automatically reconnect</span></span>

<span data-ttu-id="144f4-186">Клиент JavaScript для SignalR можно настроить для автоматического повторного подключения с помощью `withAutomaticReconnect` метода в [хубконнектионбуилдер](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="144f4-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="144f4-187">По умолчанию оно не будет автоматически восстановлено.</span><span class="sxs-lookup"><span data-stu-id="144f4-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="144f4-188">Без каких бы то ни было параметров, `withAutomaticReconnect()` настраивает клиент на ожидание 0, 2, 10 и 30 секунд соответственно, прежде чем пытаться выполнить каждую попытку повторного подключения, останавливая после четырех неудачных попыток.</span><span class="sxs-lookup"><span data-stu-id="144f4-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="144f4-189">Перед запуском всех попыток повторного подключения компонент `HubConnection` перейдет в `HubConnectionState.Reconnecting` состояние и порождает свои `onreconnecting` обратные вызовы вместо перехода в `Disconnected` состояние и запуска своих `onclose` обратных вызовов, например, `HubConnection` без автоматического повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="144f4-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="144f4-190">Это дает возможность предупредить пользователей о потере подключения и отключении элементов пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="144f4-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="144f4-191">Если клиент успешно повторно подключится в течение первых четырех попыток, компонент `HubConnection` вернется к `Connected` состоянию и порождает его `onreconnected` обратные вызовы.</span><span class="sxs-lookup"><span data-stu-id="144f4-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="144f4-192">Это дает возможность информировать пользователей о повторном установлении соединения.</span><span class="sxs-lookup"><span data-stu-id="144f4-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="144f4-193">Так как соединение выглядит совершенно не так, как и сервер, `connectionId` для обратного вызова будет предоставлен новый объект `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="144f4-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="144f4-194">`onreconnected`Параметр обратного вызова `connectionId` будет неопределенным, если `HubConnection` был настроен на [пропуск согласования](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="144f4-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="144f4-195">`withAutomaticReconnect()` не настраивается `HubConnection` для повтора начальных сбоев запуска, поэтому ошибки запуска должны быть обработаны вручную:</span><span class="sxs-lookup"><span data-stu-id="144f4-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="144f4-196">Если клиент не будет успешно подключаться в течение первых четырех попыток, компонент `HubConnection` перейдет в `Disconnected` состояние и порождает его обратные вызовы [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="144f4-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="144f4-197">Это дает возможность информировать пользователей о том, что подключение было безвозвратно утрачено, и рекомендует обновить страницу:</span><span class="sxs-lookup"><span data-stu-id="144f4-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="144f4-198">Чтобы настроить настраиваемое число попыток повторного подключения перед отключением или изменением времени повторного подключения, `withAutomaticReconnect` принимает массив чисел, представляющих задержку в миллисекундах, прежде чем начинать каждую попытку повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="144f4-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="144f4-199">В предыдущем примере настраивается `HubConnection` для запуска попытки повторного подключения сразу после потери соединения.</span><span class="sxs-lookup"><span data-stu-id="144f4-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="144f4-200">Это также справедливо для конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="144f4-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="144f4-201">Если первая попытка повторного подключения завершается неудачно, вторая попытка повторного подключения также начнется немедленно, а не подождать 2 секунды, как в конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="144f4-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="144f4-202">Если вторая попытка повторного подключения завершается неудачно, третья попытка повторного подключения начнется через 10 секунд, что опять же, как и конфигурация по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="144f4-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="144f4-203">Затем пользовательское поведение снова рассходится от поведения по умолчанию, останавливаясь после третьей попытки повторного подключения, вместо того, чтобы предпринимать попытку повторного подключения еще через 30 секунд, как в конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="144f4-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="144f4-204">Если требуется даже больший контроль над временем и числом попыток автоматического повторного подключения, `withAutomaticReconnect` принимает объект, реализующий `IRetryPolicy` интерфейс, который имеет единственный метод с именем `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="144f4-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="144f4-205">`nextRetryDelayInMilliseconds` принимает один аргумент с типом `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="144f4-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="144f4-206">`RetryContext`Имеет три свойства: `previousRetryCount` , `elapsedMilliseconds` и, а `retryReason` — `number` , `number` и `Error` соответственно.</span><span class="sxs-lookup"><span data-stu-id="144f4-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="144f4-207">Перед первой попыткой повторного подключения обе `previousRetryCount` и `elapsedMilliseconds` будут равны нулю, и `retryReason` будет являться ошибкой, вызвавшей потерю соединения.</span><span class="sxs-lookup"><span data-stu-id="144f4-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="144f4-208">После каждой неудачной попытки повтора `previousRetryCount` значение будет увеличено на единицу, `elapsedMilliseconds` будет обновлено с учетом времени, затраченного на повторное подключение к этому моменту в миллисекундах, и `retryReason` будет являться ошибкой, которая привела к сбою последней попытки повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="144f4-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="144f4-209">`nextRetryDelayInMilliseconds` должен возвращать число, представляющее число миллисекунд ожидания перед следующей попыткой повторного подключения, или `null` значение, если `HubConnection` должно быть прервано повторное подключение.</span><span class="sxs-lookup"><span data-stu-id="144f4-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="144f4-210">Кроме того, можно написать код, который будет повторно подключаться к клиенту вручную, как показано в [подсоединении вручную](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="144f4-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="144f4-211">Повторное подключение вручную</span><span class="sxs-lookup"><span data-stu-id="144f4-211">Manually reconnect</span></span>

<span data-ttu-id="144f4-212">В следующем коде показан типичный способ повторного подключения вручную:</span><span class="sxs-lookup"><span data-stu-id="144f4-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="144f4-213">Для запуска подключения создается функция (в данном случае это `start` функция).</span><span class="sxs-lookup"><span data-stu-id="144f4-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="144f4-214">Вызовите `start` функцию в `onclose` обработчике событий соединения.</span><span class="sxs-lookup"><span data-stu-id="144f4-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="144f4-215">В реальной реализации будет использоваться экспоненциальная отсрочка или повторная попытка заданное число раз.</span><span class="sxs-lookup"><span data-stu-id="144f4-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="troubleshoot-websocket-handshake-errors"></a><span data-ttu-id="144f4-216">Устранение ошибок подтверждения WebSocket</span><span class="sxs-lookup"><span data-stu-id="144f4-216">Troubleshoot WebSocket handshake errors</span></span>

<span data-ttu-id="144f4-217">В этом разделе содержится справка по исключению *"ошибка при подтверждении согласования WebSocket"* , возникающей при попытке установить подключение к SignalR концентратору ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="144f4-217">This section provides help on the *"Error during WebSocket handshake"* exception that occurs when trying to establish connection to ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="144f4-218">Код ответа 400 или 503</span><span class="sxs-lookup"><span data-stu-id="144f4-218">Response code 400 or 503</span></span>

<span data-ttu-id="144f4-219">Для следующей ошибки:</span><span class="sxs-lookup"><span data-stu-id="144f4-219">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="144f4-220">Эта ошибка обычно вызвана тем, что клиент использует только транспорт WebSockets, но протокол WebSockets не включен на сервере.</span><span class="sxs-lookup"><span data-stu-id="144f4-220">The error is usually caused by client only use WebSockets transport but WebSockets protocol is not enabled on server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="144f4-221">Код ответа 307</span><span class="sxs-lookup"><span data-stu-id="144f4-221">Response code 307</span></span>

```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="144f4-222">Часто это происходит при использовании SignalR сервера концентратора:</span><span class="sxs-lookup"><span data-stu-id="144f4-222">Frequently this occurs when the SignalR hub server:</span></span>

* <span data-ttu-id="144f4-223">Прослушивает протоколы HTTP и HTTPS и реагирует на них.</span><span class="sxs-lookup"><span data-stu-id="144f4-223">Listens to and responds over both HTTP and HTTPS.</span></span>
* <span data-ttu-id="144f4-224">Настроен на принудительное применение протокола HTTPS путем вызова `UseHttpsRedirection` в `Startup` или принудительно использует HTTPS с помощью правила переопределения URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="144f4-224">Is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="144f4-225">Эта ошибка может быть вызвана указанием URL-адреса HTTP на стороне клиента с помощью `.withUrl("http://xxx/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="144f4-225">This error can be caused by specifying the HTTP URL on client side using `.withUrl("http://xxx/HubName")`.</span></span> <span data-ttu-id="144f4-226">Исправление для этого случая заключается в изменении кода для использования URL-адреса HTTPS.</span><span class="sxs-lookup"><span data-stu-id="144f4-226">The fix for this case is modifying the code to use an HTTPS URL.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="144f4-227">Код ответа 404</span><span class="sxs-lookup"><span data-stu-id="144f4-227">Response code 404</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

<span data-ttu-id="144f4-228">Если приложение работает на локальном узле, но возвращает эту ошибку после публикации на сервере IIS:</span><span class="sxs-lookup"><span data-stu-id="144f4-228">If the app works on localhost, but returns this error after publishing to IIS server:</span></span>

* <span data-ttu-id="144f4-229">Убедитесь, что ASP.NET Core SignalR приложение размещено как подприложение IIS.</span><span class="sxs-lookup"><span data-stu-id="144f4-229">Verify the ASP.NET Core SignalR app is hosted as an IIS sub-application.</span></span>
* <span data-ttu-id="144f4-230">Не задавайте URL-адрес на стороне клиента пасбасе для этого вспомогательного приложения SignalR `.withUrl("/SubAppName/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="144f4-230">Don't set URL with the sub-app's pathbase on SignalR JavaScript client side `.withUrl("/SubAppName/HubName")`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="144f4-231">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="144f4-231">Additional resources</span></span>

* [<span data-ttu-id="144f4-232">Справочник по API JavaScript</span><span class="sxs-lookup"><span data-stu-id="144f4-232">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="144f4-233">Учебник по JavaScript</span><span class="sxs-lookup"><span data-stu-id="144f4-233">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="144f4-234">Руководство по пакету и TypeScript</span><span class="sxs-lookup"><span data-stu-id="144f4-234">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="144f4-235">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="144f4-235">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="144f4-236">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="144f4-236">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="144f4-237">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="144f4-237">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="144f4-238">Запросы между источниками (CORS)</span><span class="sxs-lookup"><span data-stu-id="144f4-238">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="144f4-239">SignalRДокументация, посвященная бессерверной службе Azure</span><span class="sxs-lookup"><span data-stu-id="144f4-239">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="144f4-240">Автор: [Рэйчел Аппель](https://twitter.com/rachelappel) (Rachel Appel)</span><span class="sxs-lookup"><span data-stu-id="144f4-240">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="144f4-241">SignalRКлиентская библиотека ASP.NET Core JavaScript позволяет разработчикам вызывать код концентратора на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="144f4-241">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="144f4-242">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="144f4-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="144f4-243">Установка SignalR клиентского пакета</span><span class="sxs-lookup"><span data-stu-id="144f4-243">Install the SignalR client package</span></span>

<span data-ttu-id="144f4-244">SignalRКлиентская библиотека JavaScript доставляется в виде пакета [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="144f4-244">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="144f4-245">В следующих разделах описаны различные способы установки клиентской библиотеки.</span><span class="sxs-lookup"><span data-stu-id="144f4-245">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="144f4-246">Установка с помощью NPM</span><span class="sxs-lookup"><span data-stu-id="144f4-246">Install with npm</span></span>

<span data-ttu-id="144f4-247">При использовании Visual Studio выполните следующие команды из **консоли диспетчера пакетов** , находясь в корневой папке.</span><span class="sxs-lookup"><span data-stu-id="144f4-247">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="144f4-248">Для Visual Studio Code выполните следующие команды в **интегрированном терминале**.</span><span class="sxs-lookup"><span data-stu-id="144f4-248">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="144f4-249">NPM устанавливает содержимое пакета в папку \*node_modules \\ @aspnet\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="144f4-249">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="144f4-250">Создайте новую папку с именем *SignalR* в папке *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="144f4-250">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="144f4-251">Скопируйте файл *signalr.js* в папку *ввврут\либ\сигналр*</span><span class="sxs-lookup"><span data-stu-id="144f4-251">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="144f4-252">Сослаться на SignalR клиент JavaScript в `<script>` элементе.</span><span class="sxs-lookup"><span data-stu-id="144f4-252">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="144f4-253">Пример:</span><span class="sxs-lookup"><span data-stu-id="144f4-253">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="144f4-254">Использование сети доставки содержимого (CDN)</span><span class="sxs-lookup"><span data-stu-id="144f4-254">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="144f4-255">Чтобы использовать клиентскую библиотеку без необходимых компонентов NPM, сослаться на копию клиентской библиотеки, размещенную в CDN.</span><span class="sxs-lookup"><span data-stu-id="144f4-255">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="144f4-256">Пример:</span><span class="sxs-lookup"><span data-stu-id="144f4-256">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="144f4-257">Клиентская библиотека доступна в следующих CDN:</span><span class="sxs-lookup"><span data-stu-id="144f4-257">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="144f4-258">cdnjs</span><span class="sxs-lookup"><span data-stu-id="144f4-258">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="144f4-259">жсделивр</span><span class="sxs-lookup"><span data-stu-id="144f4-259">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="144f4-260">унпкг</span><span class="sxs-lookup"><span data-stu-id="144f4-260">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="144f4-261">Установка с помощью Либман</span><span class="sxs-lookup"><span data-stu-id="144f4-261">Install with LibMan</span></span>

<span data-ttu-id="144f4-262">[Либман](xref:client-side/libman/index) можно использовать для установки конкретных файлов клиентской библиотеки из клиентской библиотеки, размещенной в CDN.</span><span class="sxs-lookup"><span data-stu-id="144f4-262">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="144f4-263">Например, добавьте в проект только файл JavaScript минифицированные.</span><span class="sxs-lookup"><span data-stu-id="144f4-263">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="144f4-264">Дополнительные сведения об этом подходе см. в разделе [Добавление SignalR клиентской библиотеки](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="144f4-264">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="144f4-265">Подключение к концентратору</span><span class="sxs-lookup"><span data-stu-id="144f4-265">Connect to a hub</span></span>

<span data-ttu-id="144f4-266">Следующий код создает и запускает соединение.</span><span class="sxs-lookup"><span data-stu-id="144f4-266">The following code creates and starts a connection.</span></span> <span data-ttu-id="144f4-267">Имя центра не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="144f4-267">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="144f4-268">Подключения между источниками</span><span class="sxs-lookup"><span data-stu-id="144f4-268">Cross-origin connections</span></span>

<span data-ttu-id="144f4-269">Как правило, Браузеры загружают соединения из того же домена, что и запрошенная страница.</span><span class="sxs-lookup"><span data-stu-id="144f4-269">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="144f4-270">Однако бывают случаи, когда требуется подключение к другому домену.</span><span class="sxs-lookup"><span data-stu-id="144f4-270">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="144f4-271">Чтобы предотвратить чтение конфиденциальных данных с другого сайта вредоносным сайтом, [межисточниковые подключения](xref:security/cors) отключены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="144f4-271">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="144f4-272">Чтобы разрешить запрос между источниками, включите его в `Startup` классе.</span><span class="sxs-lookup"><span data-stu-id="144f4-272">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="144f4-273">Методы концентратора вызовов от клиента</span><span class="sxs-lookup"><span data-stu-id="144f4-273">Call hub methods from client</span></span>

<span data-ttu-id="144f4-274">Клиенты JavaScript вызывают открытые методы на концентраторах с помощью метода [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [хубконнектион](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="144f4-274">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="144f4-275">`invoke`Метод принимает два аргумента:</span><span class="sxs-lookup"><span data-stu-id="144f4-275">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="144f4-276">Имя метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="144f4-276">The name of the hub method.</span></span> <span data-ttu-id="144f4-277">В следующем примере имя метода в концентраторе — `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="144f4-277">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="144f4-278">Любые аргументы, определенные в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="144f4-278">Any arguments defined in the hub method.</span></span> <span data-ttu-id="144f4-279">В следующем примере имя аргумента — `message` .</span><span class="sxs-lookup"><span data-stu-id="144f4-279">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="144f4-280">В примере кода используется синтаксис функции стрелок, который поддерживается в текущих версиях всех основных браузеров, кроме Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="144f4-280">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="144f4-281">Вызов методов концентратора из клиента поддерживается только при использовании SignalR службы Azure в режиме *по умолчанию* .</span><span class="sxs-lookup"><span data-stu-id="144f4-281">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="144f4-282">Дополнительные сведения см. в разделе [часто задаваемые вопросы (репозиторий GitHub Azure SignalR)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="144f4-282">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="144f4-283">`invoke`Метод возвращает [обещание](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript.</span><span class="sxs-lookup"><span data-stu-id="144f4-283">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="144f4-284">`Promise`Разрешение разрешается с возвращаемым значением (если таковое имеется), когда метод на сервере возвращает значение.</span><span class="sxs-lookup"><span data-stu-id="144f4-284">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="144f4-285">Если метод на сервере вызывает ошибку, объект `Promise` отклоняется с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="144f4-285">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="144f4-286">Используйте `then` методы и для `catch` `Promise` самого себя, чтобы обрабатывали такие случаи (или `await` синтаксис).</span><span class="sxs-lookup"><span data-stu-id="144f4-286">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="144f4-287">`send`Метод возвращает код JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="144f4-287">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="144f4-288">`Promise`Разрешение разрешается, когда сообщение было отправлено на сервер.</span><span class="sxs-lookup"><span data-stu-id="144f4-288">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="144f4-289">Если при отправке сообщения произошла ошибка, объект `Promise` отклоняется с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="144f4-289">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="144f4-290">Используйте `then` методы и для `catch` `Promise` самого себя, чтобы обрабатывали такие случаи (или `await` синтаксис).</span><span class="sxs-lookup"><span data-stu-id="144f4-290">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="144f4-291">Использование `send` не ждет, пока сервер не получит сообщение.</span><span class="sxs-lookup"><span data-stu-id="144f4-291">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="144f4-292">Следовательно, невозможно вернуть данные или ошибки с сервера.</span><span class="sxs-lookup"><span data-stu-id="144f4-292">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="144f4-293">Вызов методов клиента из концентратора</span><span class="sxs-lookup"><span data-stu-id="144f4-293">Call client methods from hub</span></span>

<span data-ttu-id="144f4-294">Чтобы получить сообщения от концентратора, определите метод с помощью метода [On](/javascript/api/%40aspnet/signalr/hubconnection#on) класса `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="144f4-294">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="144f4-295">Имя клиентского метода JavaScript.</span><span class="sxs-lookup"><span data-stu-id="144f4-295">The name of the JavaScript client method.</span></span> <span data-ttu-id="144f4-296">В следующем примере имя метода — `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="144f4-296">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="144f4-297">Аргументы, которые центр передает в метод.</span><span class="sxs-lookup"><span data-stu-id="144f4-297">Arguments the hub passes to the method.</span></span> <span data-ttu-id="144f4-298">В следующем примере аргумент имеет значение `message` .</span><span class="sxs-lookup"><span data-stu-id="144f4-298">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="144f4-299">Приведенный выше код `connection.on` выполняется, когда код на стороне сервера вызывает его с помощью <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> метода.</span><span class="sxs-lookup"><span data-stu-id="144f4-299">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="144f4-300">SignalR Определяет, какой клиентский метод следует вызывать, сопоставляя имя и аргументы метода, определенные в `SendAsync` и `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="144f4-300">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="144f4-301">Рекомендуется вызвать метод [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) для `HubConnection` After `on` .</span><span class="sxs-lookup"><span data-stu-id="144f4-301">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="144f4-302">Это гарантирует, что обработчики будут зарегистрированы до получения сообщений.</span><span class="sxs-lookup"><span data-stu-id="144f4-302">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="144f4-303">Обработка ошибок и ведение журнала</span><span class="sxs-lookup"><span data-stu-id="144f4-303">Error handling and logging</span></span>

<span data-ttu-id="144f4-304">Привязать `catch` метод к концу `start` метода для управления ошибками на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="144f4-304">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="144f4-305">Используется `console.error` для вывода ошибок в консоль браузера.</span><span class="sxs-lookup"><span data-stu-id="144f4-305">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="144f4-306">Настройте трассировку журнала на стороне клиента, передав средства ведения журнала и тип события в журнал, когда соединение установлено.</span><span class="sxs-lookup"><span data-stu-id="144f4-306">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="144f4-307">Сообщения записываются с указанным уровнем ведения журнала и выше.</span><span class="sxs-lookup"><span data-stu-id="144f4-307">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="144f4-308">Доступны следующие уровни ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="144f4-308">Available log levels are as follows:</span></span>

* <span data-ttu-id="144f4-309">`signalR.LogLevel.Error`: Сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="144f4-309">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="144f4-310">Записывает `Error` только сообщения.</span><span class="sxs-lookup"><span data-stu-id="144f4-310">Logs `Error` messages only.</span></span>
* <span data-ttu-id="144f4-311">`signalR.LogLevel.Warning`: Предупреждающие сообщения о возможных ошибках.</span><span class="sxs-lookup"><span data-stu-id="144f4-311">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="144f4-312">Журналы `Warning` и `Error` сообщения.</span><span class="sxs-lookup"><span data-stu-id="144f4-312">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="144f4-313">`signalR.LogLevel.Information`: Сообщения о состоянии без ошибок.</span><span class="sxs-lookup"><span data-stu-id="144f4-313">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="144f4-314">Журналы `Information` , `Warning` и `Error` сообщения.</span><span class="sxs-lookup"><span data-stu-id="144f4-314">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="144f4-315">`signalR.LogLevel.Trace`: Сообщения трассировки.</span><span class="sxs-lookup"><span data-stu-id="144f4-315">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="144f4-316">Заносит в журнал все данные, включая транспорт данных между концентратором и клиентом.</span><span class="sxs-lookup"><span data-stu-id="144f4-316">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="144f4-317">Чтобы настроить уровень ведения журнала, используйте метод [конфигурелоггинг](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) в [хубконнектионбуилдер](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="144f4-317">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="144f4-318">Сообщения записываются в консоль браузера.</span><span class="sxs-lookup"><span data-stu-id="144f4-318">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="144f4-319">Повторное подключение клиентов</span><span class="sxs-lookup"><span data-stu-id="144f4-319">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="144f4-320">Повторное подключение вручную</span><span class="sxs-lookup"><span data-stu-id="144f4-320">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="144f4-321">До 3,0 клиент JavaScript для SignalR не выполняет автоматическое повторное подключение.</span><span class="sxs-lookup"><span data-stu-id="144f4-321">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="144f4-322">Необходимо написать код, который будет повторно подключаться к клиенту вручную.</span><span class="sxs-lookup"><span data-stu-id="144f4-322">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="144f4-323">В следующем коде показан типичный способ повторного подключения вручную:</span><span class="sxs-lookup"><span data-stu-id="144f4-323">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="144f4-324">Для запуска подключения создается функция (в данном случае это `start` функция).</span><span class="sxs-lookup"><span data-stu-id="144f4-324">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="144f4-325">Вызовите `start` функцию в `onclose` обработчике событий соединения.</span><span class="sxs-lookup"><span data-stu-id="144f4-325">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="144f4-326">В реальной реализации будет использоваться экспоненциальная отсрочка или повторная попытка заданное число раз.</span><span class="sxs-lookup"><span data-stu-id="144f4-326">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="144f4-327">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="144f4-327">Additional resources</span></span>

* [<span data-ttu-id="144f4-328">Справочник по API JavaScript</span><span class="sxs-lookup"><span data-stu-id="144f4-328">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="144f4-329">Учебник по JavaScript</span><span class="sxs-lookup"><span data-stu-id="144f4-329">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="144f4-330">Руководство по пакету и TypeScript</span><span class="sxs-lookup"><span data-stu-id="144f4-330">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="144f4-331">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="144f4-331">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="144f4-332">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="144f4-332">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="144f4-333">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="144f4-333">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="144f4-334">Запросы между источниками (CORS)</span><span class="sxs-lookup"><span data-stu-id="144f4-334">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="144f4-335">SignalRДокументация, посвященная бессерверной службе Azure</span><span class="sxs-lookup"><span data-stu-id="144f4-335">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
