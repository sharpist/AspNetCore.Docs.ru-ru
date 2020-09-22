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
ms.openlocfilehash: 359aa2b9e6b7f826d75f10645b7f2b565ab48b7a
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847693"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="591dd-103">ASP.NET Core SignalR клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="591dd-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="591dd-104">Автор: [Рэйчел Аппель](https://twitter.com/rachelappel) (Rachel Appel)</span><span class="sxs-lookup"><span data-stu-id="591dd-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="591dd-105">SignalRКлиентская библиотека ASP.NET Core JavaScript позволяет разработчикам вызывать код концентратора на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="591dd-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="591dd-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="591dd-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="591dd-107">Установка SignalR клиентского пакета</span><span class="sxs-lookup"><span data-stu-id="591dd-107">Install the SignalR client package</span></span>

<span data-ttu-id="591dd-108">SignalRКлиентская библиотека JavaScript доставляется в виде пакета [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="591dd-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="591dd-109">В следующих разделах описаны различные способы установки клиентской библиотеки.</span><span class="sxs-lookup"><span data-stu-id="591dd-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="591dd-110">Установка с помощью NPM</span><span class="sxs-lookup"><span data-stu-id="591dd-110">Install with npm</span></span>

<span data-ttu-id="591dd-111">Для Visual Studio выполните следующие команды из **консоли диспетчера пакетов** , находясь в корневой папке.</span><span class="sxs-lookup"><span data-stu-id="591dd-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="591dd-112">Для Visual Studio Code выполните следующие команды в **интегрированном терминале**.</span><span class="sxs-lookup"><span data-stu-id="591dd-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="591dd-113">NPM устанавливает содержимое пакета в папку \*node_modules \\ @microsoft\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="591dd-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="591dd-114">Создайте новую папку с именем *SignalR* в папке *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="591dd-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="591dd-115">Скопируйте файл *signalr.js* в папку *ввврут\либ\сигналр*</span><span class="sxs-lookup"><span data-stu-id="591dd-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="591dd-116">Сослаться на SignalR клиент JavaScript в `<script>` элементе.</span><span class="sxs-lookup"><span data-stu-id="591dd-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="591dd-117">Пример:</span><span class="sxs-lookup"><span data-stu-id="591dd-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="591dd-118">Использование сети доставки содержимого (CDN)</span><span class="sxs-lookup"><span data-stu-id="591dd-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="591dd-119">Чтобы использовать клиентскую библиотеку без необходимых компонентов NPM, сослаться на копию клиентской библиотеки, размещенную в CDN.</span><span class="sxs-lookup"><span data-stu-id="591dd-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="591dd-120">Пример:</span><span class="sxs-lookup"><span data-stu-id="591dd-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="591dd-121">Клиентская библиотека доступна в следующих CDN:</span><span class="sxs-lookup"><span data-stu-id="591dd-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="591dd-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="591dd-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="591dd-123">жсделивр</span><span class="sxs-lookup"><span data-stu-id="591dd-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="591dd-124">унпкг</span><span class="sxs-lookup"><span data-stu-id="591dd-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="591dd-125">Установка с помощью Либман</span><span class="sxs-lookup"><span data-stu-id="591dd-125">Install with LibMan</span></span>

<span data-ttu-id="591dd-126">[Либман](xref:client-side/libman/index) можно использовать для установки конкретных файлов клиентской библиотеки из клиентской библиотеки, размещенной в CDN.</span><span class="sxs-lookup"><span data-stu-id="591dd-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="591dd-127">Например, добавьте в проект только файл JavaScript минифицированные.</span><span class="sxs-lookup"><span data-stu-id="591dd-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="591dd-128">Дополнительные сведения об этом подходе см. в разделе [Добавление SignalR клиентской библиотеки](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="591dd-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="591dd-129">Подключение к концентратору</span><span class="sxs-lookup"><span data-stu-id="591dd-129">Connect to a hub</span></span>

<span data-ttu-id="591dd-130">Следующий код создает и запускает соединение.</span><span class="sxs-lookup"><span data-stu-id="591dd-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="591dd-131">Имя центра не учитывает регистр:</span><span class="sxs-lookup"><span data-stu-id="591dd-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="591dd-132">Подключения между источниками</span><span class="sxs-lookup"><span data-stu-id="591dd-132">Cross-origin connections</span></span>

<span data-ttu-id="591dd-133">Как правило, Браузеры загружают соединения из того же домена, что и запрошенная страница.</span><span class="sxs-lookup"><span data-stu-id="591dd-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="591dd-134">Однако бывают случаи, когда требуется подключение к другому домену.</span><span class="sxs-lookup"><span data-stu-id="591dd-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="591dd-135">Чтобы предотвратить чтение конфиденциальных данных с другого сайта вредоносным сайтом, [межисточниковые подключения](xref:security/cors) отключены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="591dd-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="591dd-136">Чтобы разрешить запрос между источниками, включите его в `Startup` классе:</span><span class="sxs-lookup"><span data-stu-id="591dd-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="591dd-137">Методы концентратора вызовов от клиента</span><span class="sxs-lookup"><span data-stu-id="591dd-137">Call hub methods from the client</span></span>

<span data-ttu-id="591dd-138">Клиенты JavaScript вызывают открытые методы на концентраторах с помощью метода [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) [хубконнектион](/javascript/api/%40microsoft/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="591dd-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="591dd-139">`invoke`Метод принимает:</span><span class="sxs-lookup"><span data-stu-id="591dd-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="591dd-140">Имя метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="591dd-140">The name of the hub method.</span></span>
* <span data-ttu-id="591dd-141">Любые аргументы, определенные в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="591dd-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="591dd-142">В следующем примере имя метода в концентраторе — `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="591dd-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="591dd-143">Второй и третий аргументы, передаваемые для `invoke` сопоставлений с `user` аргументами и аргументов метода концентратора `message` :</span><span class="sxs-lookup"><span data-stu-id="591dd-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="591dd-144">Вызов методов концентратора из клиента поддерживается только при использовании SignalR службы Azure в режиме *по умолчанию* .</span><span class="sxs-lookup"><span data-stu-id="591dd-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="591dd-145">Дополнительные сведения см. в разделе [часто задаваемые вопросы (репозиторий GitHub Azure SignalR)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="591dd-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="591dd-146">`invoke`Метод возвращает [обещание](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript.</span><span class="sxs-lookup"><span data-stu-id="591dd-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="591dd-147">`Promise`Разрешение разрешается с возвращаемым значением (если таковое имеется), когда метод на сервере возвращает значение.</span><span class="sxs-lookup"><span data-stu-id="591dd-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="591dd-148">Если метод на сервере вызывает ошибку, объект `Promise` отклоняется с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="591dd-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="591dd-149">`async` `await` `Promise` `then` `catch` Для решения этих случаев используйте методы и или.</span><span class="sxs-lookup"><span data-stu-id="591dd-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="591dd-150">Клиенты JavaScript также могут вызывать открытые методы на концентраторах через метод [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) объекта `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="591dd-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="591dd-151">В отличие от `invoke` метода, `send` метод не ждет ответа от сервера.</span><span class="sxs-lookup"><span data-stu-id="591dd-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="591dd-152">`send`Метод возвращает код JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="591dd-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="591dd-153">`Promise`Разрешение разрешается, когда сообщение было отправлено на сервер.</span><span class="sxs-lookup"><span data-stu-id="591dd-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="591dd-154">Если при отправке сообщения произошла ошибка, объект `Promise` отклоняется с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="591dd-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="591dd-155">`async` `await` `Promise` `then` `catch` Для решения этих случаев используйте методы и или.</span><span class="sxs-lookup"><span data-stu-id="591dd-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="591dd-156">Использование `send` не ждет, пока сервер не получит сообщение.</span><span class="sxs-lookup"><span data-stu-id="591dd-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="591dd-157">Следовательно, невозможно вернуть данные или ошибки с сервера.</span><span class="sxs-lookup"><span data-stu-id="591dd-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="591dd-158">Вызов методов клиента из концентратора</span><span class="sxs-lookup"><span data-stu-id="591dd-158">Call client methods from the hub</span></span>

<span data-ttu-id="591dd-159">Чтобы получить сообщения от концентратора, определите метод с помощью метода [On](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) класса `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="591dd-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="591dd-160">Имя клиентского метода JavaScript.</span><span class="sxs-lookup"><span data-stu-id="591dd-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="591dd-161">Аргументы, которые центр передает в метод.</span><span class="sxs-lookup"><span data-stu-id="591dd-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="591dd-162">В следующем примере имя метода — `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="591dd-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="591dd-163">Имена аргументов `user` `message` : и.</span><span class="sxs-lookup"><span data-stu-id="591dd-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="591dd-164">Приведенный выше код `connection.on` выполняется, когда код на стороне сервера вызывает его с помощью <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> метода:</span><span class="sxs-lookup"><span data-stu-id="591dd-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="591dd-165">SignalR Определяет, какой клиентский метод следует вызывать, сопоставляя имя и аргументы метода, определенные в `SendAsync` и `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="591dd-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="591dd-166">Рекомендуется вызвать метод [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) для `HubConnection` After `on` .</span><span class="sxs-lookup"><span data-stu-id="591dd-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="591dd-167">Это гарантирует, что обработчики будут зарегистрированы до получения сообщений.</span><span class="sxs-lookup"><span data-stu-id="591dd-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="591dd-168">Обработка ошибок и ведение журнала</span><span class="sxs-lookup"><span data-stu-id="591dd-168">Error handling and logging</span></span>

<span data-ttu-id="591dd-169">`try` `catch` `async` `await` `Promise` `catch` Для управления ошибками на стороне клиента используйте и с методом, или.</span><span class="sxs-lookup"><span data-stu-id="591dd-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="591dd-170">Используйте `console.error` для вывода ошибок в консоль браузера:</span><span class="sxs-lookup"><span data-stu-id="591dd-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="591dd-171">Настройте трассировку журнала на стороне клиента, передав средства ведения журнала и тип события в журнал, когда соединение установлено.</span><span class="sxs-lookup"><span data-stu-id="591dd-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="591dd-172">Сообщения записываются с указанным уровнем ведения журнала и выше.</span><span class="sxs-lookup"><span data-stu-id="591dd-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="591dd-173">Доступны следующие уровни ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="591dd-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="591dd-174">`signalR.LogLevel.Error`: Сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="591dd-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="591dd-175">Записывает `Error` только сообщения.</span><span class="sxs-lookup"><span data-stu-id="591dd-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="591dd-176">`signalR.LogLevel.Warning`: Предупреждающие сообщения о возможных ошибках.</span><span class="sxs-lookup"><span data-stu-id="591dd-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="591dd-177">Журналы `Warning` и `Error` сообщения.</span><span class="sxs-lookup"><span data-stu-id="591dd-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="591dd-178">`signalR.LogLevel.Information`: Сообщения о состоянии без ошибок.</span><span class="sxs-lookup"><span data-stu-id="591dd-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="591dd-179">Журналы `Information` , `Warning` и `Error` сообщения.</span><span class="sxs-lookup"><span data-stu-id="591dd-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="591dd-180">`signalR.LogLevel.Trace`: Сообщения трассировки.</span><span class="sxs-lookup"><span data-stu-id="591dd-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="591dd-181">Заносит в журнал все данные, включая транспорт данных между концентратором и клиентом.</span><span class="sxs-lookup"><span data-stu-id="591dd-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="591dd-182">Чтобы настроить уровень ведения журнала, используйте метод [конфигурелоггинг](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) в [хубконнектионбуилдер](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="591dd-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="591dd-183">Сообщения регистрируются в консоли браузера:</span><span class="sxs-lookup"><span data-stu-id="591dd-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="591dd-184">Повторное подключение клиентов</span><span class="sxs-lookup"><span data-stu-id="591dd-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="591dd-185">Автоматическое повторное подключение</span><span class="sxs-lookup"><span data-stu-id="591dd-185">Automatically reconnect</span></span>

<span data-ttu-id="591dd-186">Клиент JavaScript для SignalR можно настроить для автоматического повторного подключения с помощью `withAutomaticReconnect` метода в [хубконнектионбуилдер](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="591dd-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="591dd-187">По умолчанию оно не будет автоматически восстановлено.</span><span class="sxs-lookup"><span data-stu-id="591dd-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="591dd-188">Без каких бы то ни было параметров, `withAutomaticReconnect()` настраивает клиент на ожидание 0, 2, 10 и 30 секунд соответственно, прежде чем пытаться выполнить каждую попытку повторного подключения, останавливая после четырех неудачных попыток.</span><span class="sxs-lookup"><span data-stu-id="591dd-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="591dd-189">Перед запуском всех попыток повторного подключения компонент `HubConnection` перейдет в `HubConnectionState.Reconnecting` состояние и порождает свои `onreconnecting` обратные вызовы вместо перехода в `Disconnected` состояние и запуска своих `onclose` обратных вызовов, например, `HubConnection` без автоматического повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="591dd-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="591dd-190">Это дает возможность предупредить пользователей о потере подключения и отключении элементов пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="591dd-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="591dd-191">Если клиент успешно повторно подключится в течение первых четырех попыток, компонент `HubConnection` вернется к `Connected` состоянию и порождает его `onreconnected` обратные вызовы.</span><span class="sxs-lookup"><span data-stu-id="591dd-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="591dd-192">Это дает возможность информировать пользователей о повторном установлении соединения.</span><span class="sxs-lookup"><span data-stu-id="591dd-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="591dd-193">Так как соединение выглядит совершенно не так, как и сервер, `connectionId` для обратного вызова будет предоставлен новый объект `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="591dd-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="591dd-194">`onreconnected`Параметр обратного вызова `connectionId` будет неопределенным, если `HubConnection` был настроен на [пропуск согласования](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="591dd-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="591dd-195">`withAutomaticReconnect()` не настраивается `HubConnection` для повтора начальных сбоев запуска, поэтому ошибки запуска должны быть обработаны вручную:</span><span class="sxs-lookup"><span data-stu-id="591dd-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="591dd-196">Если клиент не будет успешно подключаться в течение первых четырех попыток, компонент `HubConnection` перейдет в `Disconnected` состояние и порождает его обратные вызовы [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="591dd-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="591dd-197">Это дает возможность информировать пользователей о том, что подключение было безвозвратно утрачено, и рекомендует обновить страницу:</span><span class="sxs-lookup"><span data-stu-id="591dd-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="591dd-198">Чтобы настроить настраиваемое число попыток повторного подключения перед отключением или изменением времени повторного подключения, `withAutomaticReconnect` принимает массив чисел, представляющих задержку в миллисекундах, прежде чем начинать каждую попытку повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="591dd-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="591dd-199">В предыдущем примере настраивается `HubConnection` для запуска попытки повторного подключения сразу после потери соединения.</span><span class="sxs-lookup"><span data-stu-id="591dd-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="591dd-200">Это также справедливо для конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="591dd-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="591dd-201">Если первая попытка повторного подключения завершается неудачно, вторая попытка повторного подключения также начнется немедленно, а не подождать 2 секунды, как в конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="591dd-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="591dd-202">Если вторая попытка повторного подключения завершается неудачно, третья попытка повторного подключения начнется через 10 секунд, что опять же, как и конфигурация по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="591dd-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="591dd-203">Затем пользовательское поведение снова рассходится от поведения по умолчанию, останавливаясь после третьей попытки повторного подключения, вместо того, чтобы предпринимать попытку повторного подключения еще через 30 секунд, как в конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="591dd-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="591dd-204">Если требуется даже больший контроль над временем и числом попыток автоматического повторного подключения, `withAutomaticReconnect` принимает объект, реализующий `IRetryPolicy` интерфейс, который имеет единственный метод с именем `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="591dd-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="591dd-205">`nextRetryDelayInMilliseconds` принимает один аргумент с типом `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="591dd-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="591dd-206">`RetryContext`Имеет три свойства: `previousRetryCount` , `elapsedMilliseconds` и, а `retryReason` — `number` , `number` и `Error` соответственно.</span><span class="sxs-lookup"><span data-stu-id="591dd-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="591dd-207">Перед первой попыткой повторного подключения обе `previousRetryCount` и `elapsedMilliseconds` будут равны нулю, и `retryReason` будет являться ошибкой, вызвавшей потерю соединения.</span><span class="sxs-lookup"><span data-stu-id="591dd-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="591dd-208">После каждой неудачной попытки повтора `previousRetryCount` значение будет увеличено на единицу, `elapsedMilliseconds` будет обновлено с учетом времени, затраченного на повторное подключение к этому моменту в миллисекундах, и `retryReason` будет являться ошибкой, которая привела к сбою последней попытки повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="591dd-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="591dd-209">`nextRetryDelayInMilliseconds` должен возвращать число, представляющее число миллисекунд ожидания перед следующей попыткой повторного подключения, или `null` значение, если `HubConnection` должно быть прервано повторное подключение.</span><span class="sxs-lookup"><span data-stu-id="591dd-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="591dd-210">Кроме того, можно написать код, который будет повторно подключаться к клиенту вручную, как показано в [подсоединении вручную](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="591dd-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="591dd-211">Повторное подключение вручную</span><span class="sxs-lookup"><span data-stu-id="591dd-211">Manually reconnect</span></span>

<span data-ttu-id="591dd-212">В следующем коде показан типичный способ повторного подключения вручную:</span><span class="sxs-lookup"><span data-stu-id="591dd-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="591dd-213">Для запуска подключения создается функция (в данном случае это `start` функция).</span><span class="sxs-lookup"><span data-stu-id="591dd-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="591dd-214">Вызовите `start` функцию в `onclose` обработчике событий соединения.</span><span class="sxs-lookup"><span data-stu-id="591dd-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="591dd-215">В реальной реализации будет использоваться экспоненциальная отсрочка или повторная попытка заданное число раз.</span><span class="sxs-lookup"><span data-stu-id="591dd-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="591dd-216">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="591dd-216">Additional resources</span></span>

* [<span data-ttu-id="591dd-217">Справочник по API JavaScript</span><span class="sxs-lookup"><span data-stu-id="591dd-217">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="591dd-218">Учебник по JavaScript</span><span class="sxs-lookup"><span data-stu-id="591dd-218">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="591dd-219">Руководство по пакету и TypeScript</span><span class="sxs-lookup"><span data-stu-id="591dd-219">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="591dd-220">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="591dd-220">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="591dd-221">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="591dd-221">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="591dd-222">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="591dd-222">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="591dd-223">Запросы между источниками (CORS)</span><span class="sxs-lookup"><span data-stu-id="591dd-223">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="591dd-224">SignalRДокументация, посвященная бессерверной службе Azure</span><span class="sxs-lookup"><span data-stu-id="591dd-224">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="591dd-225">Автор: [Рэйчел Аппель](https://twitter.com/rachelappel) (Rachel Appel)</span><span class="sxs-lookup"><span data-stu-id="591dd-225">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="591dd-226">SignalRКлиентская библиотека ASP.NET Core JavaScript позволяет разработчикам вызывать код концентратора на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="591dd-226">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="591dd-227">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="591dd-227">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="591dd-228">Установка SignalR клиентского пакета</span><span class="sxs-lookup"><span data-stu-id="591dd-228">Install the SignalR client package</span></span>

<span data-ttu-id="591dd-229">SignalRКлиентская библиотека JavaScript доставляется в виде пакета [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="591dd-229">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="591dd-230">В следующих разделах описаны различные способы установки клиентской библиотеки.</span><span class="sxs-lookup"><span data-stu-id="591dd-230">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="591dd-231">Установка с помощью NPM</span><span class="sxs-lookup"><span data-stu-id="591dd-231">Install with npm</span></span>

<span data-ttu-id="591dd-232">При использовании Visual Studio выполните следующие команды из **консоли диспетчера пакетов** , находясь в корневой папке.</span><span class="sxs-lookup"><span data-stu-id="591dd-232">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="591dd-233">Для Visual Studio Code выполните следующие команды в **интегрированном терминале**.</span><span class="sxs-lookup"><span data-stu-id="591dd-233">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="591dd-234">NPM устанавливает содержимое пакета в папку \*node_modules \\ @aspnet\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="591dd-234">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="591dd-235">Создайте новую папку с именем *SignalR* в папке *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="591dd-235">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="591dd-236">Скопируйте файл *signalr.js* в папку *ввврут\либ\сигналр*</span><span class="sxs-lookup"><span data-stu-id="591dd-236">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="591dd-237">Сослаться на SignalR клиент JavaScript в `<script>` элементе.</span><span class="sxs-lookup"><span data-stu-id="591dd-237">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="591dd-238">Пример:</span><span class="sxs-lookup"><span data-stu-id="591dd-238">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="591dd-239">Использование сети доставки содержимого (CDN)</span><span class="sxs-lookup"><span data-stu-id="591dd-239">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="591dd-240">Чтобы использовать клиентскую библиотеку без необходимых компонентов NPM, сослаться на копию клиентской библиотеки, размещенную в CDN.</span><span class="sxs-lookup"><span data-stu-id="591dd-240">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="591dd-241">Пример:</span><span class="sxs-lookup"><span data-stu-id="591dd-241">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="591dd-242">Клиентская библиотека доступна в следующих CDN:</span><span class="sxs-lookup"><span data-stu-id="591dd-242">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="591dd-243">cdnjs</span><span class="sxs-lookup"><span data-stu-id="591dd-243">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="591dd-244">жсделивр</span><span class="sxs-lookup"><span data-stu-id="591dd-244">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="591dd-245">унпкг</span><span class="sxs-lookup"><span data-stu-id="591dd-245">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="591dd-246">Установка с помощью Либман</span><span class="sxs-lookup"><span data-stu-id="591dd-246">Install with LibMan</span></span>

<span data-ttu-id="591dd-247">[Либман](xref:client-side/libman/index) можно использовать для установки конкретных файлов клиентской библиотеки из клиентской библиотеки, размещенной в CDN.</span><span class="sxs-lookup"><span data-stu-id="591dd-247">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="591dd-248">Например, добавьте в проект только файл JavaScript минифицированные.</span><span class="sxs-lookup"><span data-stu-id="591dd-248">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="591dd-249">Дополнительные сведения об этом подходе см. в разделе [Добавление SignalR клиентской библиотеки](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="591dd-249">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="591dd-250">Подключение к концентратору</span><span class="sxs-lookup"><span data-stu-id="591dd-250">Connect to a hub</span></span>

<span data-ttu-id="591dd-251">Следующий код создает и запускает соединение.</span><span class="sxs-lookup"><span data-stu-id="591dd-251">The following code creates and starts a connection.</span></span> <span data-ttu-id="591dd-252">Имя центра не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="591dd-252">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="591dd-253">Подключения между источниками</span><span class="sxs-lookup"><span data-stu-id="591dd-253">Cross-origin connections</span></span>

<span data-ttu-id="591dd-254">Как правило, Браузеры загружают соединения из того же домена, что и запрошенная страница.</span><span class="sxs-lookup"><span data-stu-id="591dd-254">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="591dd-255">Однако бывают случаи, когда требуется подключение к другому домену.</span><span class="sxs-lookup"><span data-stu-id="591dd-255">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="591dd-256">Чтобы предотвратить чтение конфиденциальных данных с другого сайта вредоносным сайтом, [межисточниковые подключения](xref:security/cors) отключены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="591dd-256">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="591dd-257">Чтобы разрешить запрос между источниками, включите его в `Startup` классе.</span><span class="sxs-lookup"><span data-stu-id="591dd-257">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="591dd-258">Методы концентратора вызовов от клиента</span><span class="sxs-lookup"><span data-stu-id="591dd-258">Call hub methods from client</span></span>

<span data-ttu-id="591dd-259">Клиенты JavaScript вызывают открытые методы на концентраторах с помощью метода [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [хубконнектион](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="591dd-259">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="591dd-260">`invoke`Метод принимает два аргумента:</span><span class="sxs-lookup"><span data-stu-id="591dd-260">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="591dd-261">Имя метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="591dd-261">The name of the hub method.</span></span> <span data-ttu-id="591dd-262">В следующем примере имя метода в концентраторе — `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="591dd-262">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="591dd-263">Любые аргументы, определенные в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="591dd-263">Any arguments defined in the hub method.</span></span> <span data-ttu-id="591dd-264">В следующем примере имя аргумента — `message` .</span><span class="sxs-lookup"><span data-stu-id="591dd-264">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="591dd-265">В примере кода используется синтаксис функции стрелок, который поддерживается в текущих версиях всех основных браузеров, кроме Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="591dd-265">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="591dd-266">Вызов методов концентратора из клиента поддерживается только при использовании SignalR службы Azure в режиме *по умолчанию* .</span><span class="sxs-lookup"><span data-stu-id="591dd-266">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="591dd-267">Дополнительные сведения см. в разделе [часто задаваемые вопросы (репозиторий GitHub Azure SignalR)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="591dd-267">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="591dd-268">`invoke`Метод возвращает [обещание](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript.</span><span class="sxs-lookup"><span data-stu-id="591dd-268">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="591dd-269">`Promise`Разрешение разрешается с возвращаемым значением (если таковое имеется), когда метод на сервере возвращает значение.</span><span class="sxs-lookup"><span data-stu-id="591dd-269">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="591dd-270">Если метод на сервере вызывает ошибку, объект `Promise` отклоняется с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="591dd-270">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="591dd-271">Используйте `then` методы и для `catch` `Promise` самого себя, чтобы обрабатывали такие случаи (или `await` синтаксис).</span><span class="sxs-lookup"><span data-stu-id="591dd-271">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="591dd-272">`send`Метод возвращает код JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="591dd-272">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="591dd-273">`Promise`Разрешение разрешается, когда сообщение было отправлено на сервер.</span><span class="sxs-lookup"><span data-stu-id="591dd-273">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="591dd-274">Если при отправке сообщения произошла ошибка, объект `Promise` отклоняется с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="591dd-274">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="591dd-275">Используйте `then` методы и для `catch` `Promise` самого себя, чтобы обрабатывали такие случаи (или `await` синтаксис).</span><span class="sxs-lookup"><span data-stu-id="591dd-275">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="591dd-276">Использование `send` не ждет, пока сервер не получит сообщение.</span><span class="sxs-lookup"><span data-stu-id="591dd-276">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="591dd-277">Следовательно, невозможно вернуть данные или ошибки с сервера.</span><span class="sxs-lookup"><span data-stu-id="591dd-277">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="591dd-278">Вызов методов клиента из концентратора</span><span class="sxs-lookup"><span data-stu-id="591dd-278">Call client methods from hub</span></span>

<span data-ttu-id="591dd-279">Чтобы получить сообщения от концентратора, определите метод с помощью метода [On](/javascript/api/%40aspnet/signalr/hubconnection#on) класса `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="591dd-279">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="591dd-280">Имя клиентского метода JavaScript.</span><span class="sxs-lookup"><span data-stu-id="591dd-280">The name of the JavaScript client method.</span></span> <span data-ttu-id="591dd-281">В следующем примере имя метода — `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="591dd-281">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="591dd-282">Аргументы, которые центр передает в метод.</span><span class="sxs-lookup"><span data-stu-id="591dd-282">Arguments the hub passes to the method.</span></span> <span data-ttu-id="591dd-283">В следующем примере аргумент имеет значение `message` .</span><span class="sxs-lookup"><span data-stu-id="591dd-283">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="591dd-284">Приведенный выше код `connection.on` выполняется, когда код на стороне сервера вызывает его с помощью <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> метода.</span><span class="sxs-lookup"><span data-stu-id="591dd-284">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="591dd-285">SignalR Определяет, какой клиентский метод следует вызывать, сопоставляя имя и аргументы метода, определенные в `SendAsync` и `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="591dd-285">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="591dd-286">Рекомендуется вызвать метод [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) для `HubConnection` After `on` .</span><span class="sxs-lookup"><span data-stu-id="591dd-286">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="591dd-287">Это гарантирует, что обработчики будут зарегистрированы до получения сообщений.</span><span class="sxs-lookup"><span data-stu-id="591dd-287">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="591dd-288">Обработка ошибок и ведение журнала</span><span class="sxs-lookup"><span data-stu-id="591dd-288">Error handling and logging</span></span>

<span data-ttu-id="591dd-289">Привязать `catch` метод к концу `start` метода для управления ошибками на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="591dd-289">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="591dd-290">Используется `console.error` для вывода ошибок в консоль браузера.</span><span class="sxs-lookup"><span data-stu-id="591dd-290">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="591dd-291">Настройте трассировку журнала на стороне клиента, передав средства ведения журнала и тип события в журнал, когда соединение установлено.</span><span class="sxs-lookup"><span data-stu-id="591dd-291">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="591dd-292">Сообщения записываются с указанным уровнем ведения журнала и выше.</span><span class="sxs-lookup"><span data-stu-id="591dd-292">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="591dd-293">Доступны следующие уровни ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="591dd-293">Available log levels are as follows:</span></span>

* <span data-ttu-id="591dd-294">`signalR.LogLevel.Error`: Сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="591dd-294">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="591dd-295">Записывает `Error` только сообщения.</span><span class="sxs-lookup"><span data-stu-id="591dd-295">Logs `Error` messages only.</span></span>
* <span data-ttu-id="591dd-296">`signalR.LogLevel.Warning`: Предупреждающие сообщения о возможных ошибках.</span><span class="sxs-lookup"><span data-stu-id="591dd-296">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="591dd-297">Журналы `Warning` и `Error` сообщения.</span><span class="sxs-lookup"><span data-stu-id="591dd-297">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="591dd-298">`signalR.LogLevel.Information`: Сообщения о состоянии без ошибок.</span><span class="sxs-lookup"><span data-stu-id="591dd-298">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="591dd-299">Журналы `Information` , `Warning` и `Error` сообщения.</span><span class="sxs-lookup"><span data-stu-id="591dd-299">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="591dd-300">`signalR.LogLevel.Trace`: Сообщения трассировки.</span><span class="sxs-lookup"><span data-stu-id="591dd-300">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="591dd-301">Заносит в журнал все данные, включая транспорт данных между концентратором и клиентом.</span><span class="sxs-lookup"><span data-stu-id="591dd-301">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="591dd-302">Чтобы настроить уровень ведения журнала, используйте метод [конфигурелоггинг](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) в [хубконнектионбуилдер](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="591dd-302">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="591dd-303">Сообщения записываются в консоль браузера.</span><span class="sxs-lookup"><span data-stu-id="591dd-303">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="591dd-304">Повторное подключение клиентов</span><span class="sxs-lookup"><span data-stu-id="591dd-304">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="591dd-305">Повторное подключение вручную</span><span class="sxs-lookup"><span data-stu-id="591dd-305">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="591dd-306">До 3,0 клиент JavaScript для SignalR не выполняет автоматическое повторное подключение.</span><span class="sxs-lookup"><span data-stu-id="591dd-306">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="591dd-307">Необходимо написать код, который будет повторно подключаться к клиенту вручную.</span><span class="sxs-lookup"><span data-stu-id="591dd-307">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="591dd-308">В следующем коде показан типичный способ повторного подключения вручную:</span><span class="sxs-lookup"><span data-stu-id="591dd-308">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="591dd-309">Для запуска подключения создается функция (в данном случае это `start` функция).</span><span class="sxs-lookup"><span data-stu-id="591dd-309">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="591dd-310">Вызовите `start` функцию в `onclose` обработчике событий соединения.</span><span class="sxs-lookup"><span data-stu-id="591dd-310">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="591dd-311">В реальной реализации будет использоваться экспоненциальная отсрочка или повторная попытка заданное число раз.</span><span class="sxs-lookup"><span data-stu-id="591dd-311">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="591dd-312">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="591dd-312">Additional resources</span></span>

* [<span data-ttu-id="591dd-313">Справочник по API JavaScript</span><span class="sxs-lookup"><span data-stu-id="591dd-313">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="591dd-314">Учебник по JavaScript</span><span class="sxs-lookup"><span data-stu-id="591dd-314">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="591dd-315">Руководство по пакету и TypeScript</span><span class="sxs-lookup"><span data-stu-id="591dd-315">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="591dd-316">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="591dd-316">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="591dd-317">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="591dd-317">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="591dd-318">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="591dd-318">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="591dd-319">Запросы между источниками (CORS)</span><span class="sxs-lookup"><span data-stu-id="591dd-319">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="591dd-320">SignalRДокументация, посвященная бессерверной службе Azure</span><span class="sxs-lookup"><span data-stu-id="591dd-320">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
