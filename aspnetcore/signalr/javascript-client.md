---
title: ASP.NET Core SignalR клиент JavaScript
author: bradygaster
description: Общие сведения о ASP.NET Core SignalR клиента JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: cb2b3ddc3eba2d6e1ea91c1e7f6715ffa9ad1b08
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756019"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="6bba5-103">ASP.NET Core SignalR клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="6bba5-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="6bba5-104">Автор: [Рэйчел Аппель](https://twitter.com/rachelappel) (Rachel Appel)</span><span class="sxs-lookup"><span data-stu-id="6bba5-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="6bba5-105">SignalRКлиентская библиотека ASP.NET Core JavaScript позволяет разработчикам вызывать код концентратора на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="6bba5-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="6bba5-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6bba5-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="6bba5-107">Установка SignalR клиентского пакета</span><span class="sxs-lookup"><span data-stu-id="6bba5-107">Install the SignalR client package</span></span>

<span data-ttu-id="6bba5-108">SignalRКлиентская библиотека JavaScript доставляется в виде пакета [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="6bba5-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="6bba5-109">В следующих разделах описаны различные способы установки клиентской библиотеки.</span><span class="sxs-lookup"><span data-stu-id="6bba5-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="6bba5-110">Установка с помощью NPM</span><span class="sxs-lookup"><span data-stu-id="6bba5-110">Install with npm</span></span>

<span data-ttu-id="6bba5-111">При использовании Visual Studio выполните следующие команды из **консоли диспетчера пакетов** , находясь в корневой папке.</span><span class="sxs-lookup"><span data-stu-id="6bba5-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="6bba5-112">Для Visual Studio Code выполните следующие команды в **интегрированном терминале**.</span><span class="sxs-lookup"><span data-stu-id="6bba5-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="6bba5-113">NPM устанавливает содержимое пакета в папку \*node_modules \\ @microsoft\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="6bba5-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="6bba5-114">Создайте новую папку с именем *SignalR* в папке *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="6bba5-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="6bba5-115">Скопируйте файл *signalr.js* в папку *ввврут\либ\сигналр*</span><span class="sxs-lookup"><span data-stu-id="6bba5-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="6bba5-116">NPM устанавливает содержимое пакета в папку \*node_modules \\ @aspnet\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="6bba5-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="6bba5-117">Создайте новую папку с именем *SignalR* в папке *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="6bba5-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="6bba5-118">Скопируйте файл *signalr.js* в папку *ввврут\либ\сигналр*</span><span class="sxs-lookup"><span data-stu-id="6bba5-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="6bba5-119">Сослаться на SignalR клиент JavaScript в `<script>` элементе.</span><span class="sxs-lookup"><span data-stu-id="6bba5-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="6bba5-120">Пример:</span><span class="sxs-lookup"><span data-stu-id="6bba5-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="6bba5-121">Использование сети доставки содержимого (CDN)</span><span class="sxs-lookup"><span data-stu-id="6bba5-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="6bba5-122">Чтобы использовать клиентскую библиотеку без необходимых компонентов NPM, сослаться на копию клиентской библиотеки, размещенную в CDN.</span><span class="sxs-lookup"><span data-stu-id="6bba5-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="6bba5-123">Пример:</span><span class="sxs-lookup"><span data-stu-id="6bba5-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="6bba5-124">Клиентская библиотека доступна в следующих CDN:</span><span class="sxs-lookup"><span data-stu-id="6bba5-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="6bba5-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="6bba5-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="6bba5-126">жсделивр</span><span class="sxs-lookup"><span data-stu-id="6bba5-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="6bba5-127">унпкг</span><span class="sxs-lookup"><span data-stu-id="6bba5-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="6bba5-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="6bba5-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="6bba5-129">жсделивр</span><span class="sxs-lookup"><span data-stu-id="6bba5-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="6bba5-130">унпкг</span><span class="sxs-lookup"><span data-stu-id="6bba5-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="6bba5-131">Установка с помощью Либман</span><span class="sxs-lookup"><span data-stu-id="6bba5-131">Install with LibMan</span></span>

<span data-ttu-id="6bba5-132">[Либман](xref:client-side/libman/index) можно использовать для установки конкретных файлов клиентской библиотеки из клиентской библиотеки, размещенной в CDN.</span><span class="sxs-lookup"><span data-stu-id="6bba5-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="6bba5-133">Например, добавьте в проект только файл JavaScript минифицированные.</span><span class="sxs-lookup"><span data-stu-id="6bba5-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="6bba5-134">Дополнительные сведения об этом подходе см. в разделе [Добавление SignalR клиентской библиотеки](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="6bba5-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="6bba5-135">Подключение к концентратору</span><span class="sxs-lookup"><span data-stu-id="6bba5-135">Connect to a hub</span></span>

<span data-ttu-id="6bba5-136">Следующий код создает и запускает соединение.</span><span class="sxs-lookup"><span data-stu-id="6bba5-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="6bba5-137">Имя центра не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="6bba5-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="6bba5-138">Подключения между источниками</span><span class="sxs-lookup"><span data-stu-id="6bba5-138">Cross-origin connections</span></span>

<span data-ttu-id="6bba5-139">Как правило, Браузеры загружают соединения из того же домена, что и запрошенная страница.</span><span class="sxs-lookup"><span data-stu-id="6bba5-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="6bba5-140">Однако бывают случаи, когда требуется подключение к другому домену.</span><span class="sxs-lookup"><span data-stu-id="6bba5-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="6bba5-141">Чтобы предотвратить чтение конфиденциальных данных с другого сайта вредоносным сайтом, [межисточниковые подключения](xref:security/cors) отключены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6bba5-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="6bba5-142">Чтобы разрешить запрос между источниками, включите его в `Startup` классе.</span><span class="sxs-lookup"><span data-stu-id="6bba5-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="6bba5-143">Методы концентратора вызовов от клиента</span><span class="sxs-lookup"><span data-stu-id="6bba5-143">Call hub methods from client</span></span>

<span data-ttu-id="6bba5-144">Клиенты JavaScript вызывают открытые методы на концентраторах с помощью метода [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [хубконнектион](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="6bba5-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="6bba5-145">`invoke`Метод принимает два аргумента:</span><span class="sxs-lookup"><span data-stu-id="6bba5-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="6bba5-146">Имя метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="6bba5-146">The name of the hub method.</span></span> <span data-ttu-id="6bba5-147">В следующем примере имя метода в концентраторе — `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="6bba5-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="6bba5-148">Любые аргументы, определенные в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="6bba5-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="6bba5-149">В следующем примере имя аргумента — `message` .</span><span class="sxs-lookup"><span data-stu-id="6bba5-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="6bba5-150">В примере кода используется синтаксис функции стрелок, который поддерживается в текущих версиях всех основных браузеров, кроме Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="6bba5-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="6bba5-151">Если вы используете службу Azure SignalR в *бессерверном режиме*, вы не можете вызывать методы концентратора из клиента.</span><span class="sxs-lookup"><span data-stu-id="6bba5-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="6bba5-152">Дополнительные сведения см. в [ SignalR документации по службе](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="6bba5-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="6bba5-153">`invoke`Метод возвращает [обещание](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6bba5-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="6bba5-154">`Promise`Разрешение разрешается с возвращаемым значением (если таковое имеется), когда метод на сервере возвращает значение.</span><span class="sxs-lookup"><span data-stu-id="6bba5-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="6bba5-155">Если метод на сервере вызывает ошибку, объект `Promise` отклоняется с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="6bba5-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="6bba5-156">Используйте `then` методы и для `catch` `Promise` самого себя, чтобы обрабатывали такие случаи (или `await` синтаксис).</span><span class="sxs-lookup"><span data-stu-id="6bba5-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="6bba5-157">`send`Метод возвращает код JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="6bba5-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="6bba5-158">`Promise`Разрешение разрешается, когда сообщение было отправлено на сервер.</span><span class="sxs-lookup"><span data-stu-id="6bba5-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="6bba5-159">Если при отправке сообщения произошла ошибка, объект `Promise` отклоняется с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="6bba5-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="6bba5-160">Используйте `then` методы и для `catch` `Promise` самого себя, чтобы обрабатывали такие случаи (или `await` синтаксис).</span><span class="sxs-lookup"><span data-stu-id="6bba5-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="6bba5-161">Использование `send` не ждет, пока сервер не получит сообщение.</span><span class="sxs-lookup"><span data-stu-id="6bba5-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="6bba5-162">Следовательно, невозможно вернуть данные или ошибки с сервера.</span><span class="sxs-lookup"><span data-stu-id="6bba5-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="6bba5-163">Вызов методов клиента из концентратора</span><span class="sxs-lookup"><span data-stu-id="6bba5-163">Call client methods from hub</span></span>

<span data-ttu-id="6bba5-164">Чтобы получить сообщения от концентратора, определите метод с помощью метода [On](/javascript/api/%40aspnet/signalr/hubconnection#on) класса `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="6bba5-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="6bba5-165">Имя клиентского метода JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6bba5-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="6bba5-166">В следующем примере имя метода — `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="6bba5-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="6bba5-167">Аргументы, которые центр передает в метод.</span><span class="sxs-lookup"><span data-stu-id="6bba5-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="6bba5-168">В следующем примере аргумент имеет значение `message` .</span><span class="sxs-lookup"><span data-stu-id="6bba5-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="6bba5-169">Приведенный выше код `connection.on` выполняется, когда код на стороне сервера вызывает его с помощью метода [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .</span><span class="sxs-lookup"><span data-stu-id="6bba5-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="6bba5-170">Определяет, какой клиентский метод следует вызывать, сопоставляя имя и аргументы метода, определенные в `SendAsync` и `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="6bba5-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="6bba5-171">Рекомендуется вызвать метод [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) для `HubConnection` After `on` .</span><span class="sxs-lookup"><span data-stu-id="6bba5-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="6bba5-172">Это гарантирует, что обработчики будут зарегистрированы до получения сообщений.</span><span class="sxs-lookup"><span data-stu-id="6bba5-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="6bba5-173">Обработка ошибок и ведение журнала</span><span class="sxs-lookup"><span data-stu-id="6bba5-173">Error handling and logging</span></span>

<span data-ttu-id="6bba5-174">Привязать `catch` метод к концу `start` метода для управления ошибками на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="6bba5-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="6bba5-175">Используется `console.error` для вывода ошибок в консоль браузера.</span><span class="sxs-lookup"><span data-stu-id="6bba5-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="6bba5-176">Настройте трассировку журнала на стороне клиента, передав средства ведения журнала и тип события в журнал, когда соединение установлено.</span><span class="sxs-lookup"><span data-stu-id="6bba5-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="6bba5-177">Сообщения записываются с указанным уровнем ведения журнала и выше.</span><span class="sxs-lookup"><span data-stu-id="6bba5-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="6bba5-178">Доступны следующие уровни ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="6bba5-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="6bba5-179">`signalR.LogLevel.Error`: Сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="6bba5-179">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="6bba5-180">Записывает `Error` только сообщения.</span><span class="sxs-lookup"><span data-stu-id="6bba5-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="6bba5-181">`signalR.LogLevel.Warning`: Предупреждающие сообщения о возможных ошибках.</span><span class="sxs-lookup"><span data-stu-id="6bba5-181">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="6bba5-182">Журналы `Warning` и `Error` сообщения.</span><span class="sxs-lookup"><span data-stu-id="6bba5-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="6bba5-183">`signalR.LogLevel.Information`: Сообщения о состоянии без ошибок.</span><span class="sxs-lookup"><span data-stu-id="6bba5-183">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="6bba5-184">Журналы `Information` , `Warning` и `Error` сообщения.</span><span class="sxs-lookup"><span data-stu-id="6bba5-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="6bba5-185">`signalR.LogLevel.Trace`: Сообщения трассировки.</span><span class="sxs-lookup"><span data-stu-id="6bba5-185">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="6bba5-186">Заносит в журнал все данные, включая транспорт данных между концентратором и клиентом.</span><span class="sxs-lookup"><span data-stu-id="6bba5-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="6bba5-187">Чтобы настроить уровень ведения журнала, используйте метод [конфигурелоггинг](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) в [хубконнектионбуилдер](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="6bba5-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="6bba5-188">Сообщения записываются в консоль браузера.</span><span class="sxs-lookup"><span data-stu-id="6bba5-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="6bba5-189">Повторное подключение клиентов</span><span class="sxs-lookup"><span data-stu-id="6bba5-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="6bba5-190">Автоматическое повторное подключение</span><span class="sxs-lookup"><span data-stu-id="6bba5-190">Automatically reconnect</span></span>

<span data-ttu-id="6bba5-191">Клиент JavaScript для SignalR можно настроить для автоматического повторного подключения с помощью `withAutomaticReconnect` метода в [хубконнектионбуилдер](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6bba5-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="6bba5-192">По умолчанию оно не будет автоматически восстановлено.</span><span class="sxs-lookup"><span data-stu-id="6bba5-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="6bba5-193">Без каких бы то ни было параметров, `withAutomaticReconnect()` настраивает клиент на ожидание 0, 2, 10 и 30 секунд соответственно, прежде чем пытаться выполнить каждую попытку повторного подключения, останавливая после четырех неудачных попыток.</span><span class="sxs-lookup"><span data-stu-id="6bba5-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="6bba5-194">Перед запуском всех попыток повторного подключения компонент `HubConnection` перейдет в `HubConnectionState.Reconnecting` состояние и порождает свои `onreconnecting` обратные вызовы вместо перехода в `Disconnected` состояние и запуска своих `onclose` обратных вызовов, например, `HubConnection` без автоматического повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="6bba5-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="6bba5-195">Это дает возможность предупредить пользователей о потере подключения и отключении элементов пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="6bba5-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="6bba5-196">Если клиент успешно повторно подключится в течение первых четырех попыток, компонент `HubConnection` вернется к `Connected` состоянию и порождает его `onreconnected` обратные вызовы.</span><span class="sxs-lookup"><span data-stu-id="6bba5-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="6bba5-197">Это дает возможность информировать пользователей о повторном установлении соединения.</span><span class="sxs-lookup"><span data-stu-id="6bba5-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="6bba5-198">Так как соединение выглядит совершенно не так, как и сервер, `connectionId` для обратного вызова будет предоставлен новый объект `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="6bba5-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="6bba5-199">`onreconnected`Параметр обратного вызова `connectionId` будет неопределенным, если `HubConnection` был настроен на [пропуск согласования](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="6bba5-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="6bba5-200">`withAutomaticReconnect()`не настраивается `HubConnection` для повтора начальных сбоев запуска, поэтому ошибки запуска должны быть обработаны вручную:</span><span class="sxs-lookup"><span data-stu-id="6bba5-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="6bba5-201">Если клиент не будет успешно подключаться в течение первых четырех попыток, компонент `HubConnection` перейдет в `Disconnected` состояние и порождает его обратные вызовы [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="6bba5-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="6bba5-202">Это дает возможность информировать пользователей о том, что подключение было безвозвратно утрачено, и рекомендует обновить страницу:</span><span class="sxs-lookup"><span data-stu-id="6bba5-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="6bba5-203">Чтобы настроить настраиваемое число попыток повторного подключения перед отключением или изменением времени повторного подключения, `withAutomaticReconnect` принимает массив чисел, представляющих задержку в миллисекундах, прежде чем начинать каждую попытку повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="6bba5-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="6bba5-204">В предыдущем примере настраивается `HubConnection` для запуска попытки повторного подключения сразу после потери соединения.</span><span class="sxs-lookup"><span data-stu-id="6bba5-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="6bba5-205">Это также справедливо для конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6bba5-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="6bba5-206">Если первая попытка повторного подключения завершается неудачно, вторая попытка повторного подключения также начнется немедленно, а не подождать 2 секунды, как в конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6bba5-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="6bba5-207">Если вторая попытка повторного подключения завершается неудачно, третья попытка повторного подключения начнется через 10 секунд, что опять же, как и конфигурация по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6bba5-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="6bba5-208">Затем пользовательское поведение снова рассходится от поведения по умолчанию, останавливаясь после третьей попытки повторного подключения, вместо того, чтобы предпринимать попытку повторного подключения еще через 30 секунд, как в конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6bba5-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="6bba5-209">Если требуется даже больший контроль над временем и числом попыток автоматического повторного подключения, `withAutomaticReconnect` принимает объект, реализующий `IRetryPolicy` интерфейс, который имеет единственный метод с именем `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="6bba5-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="6bba5-210">`nextRetryDelayInMilliseconds`принимает один аргумент с типом `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="6bba5-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="6bba5-211">`RetryContext`Имеет три свойства: `previousRetryCount` , `elapsedMilliseconds` и, а `retryReason` — `number` , `number` и `Error` соответственно.</span><span class="sxs-lookup"><span data-stu-id="6bba5-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="6bba5-212">Перед первой попыткой повторного подключения обе `previousRetryCount` и `elapsedMilliseconds` будут равны нулю, и `retryReason` будет являться ошибкой, вызвавшей потерю соединения.</span><span class="sxs-lookup"><span data-stu-id="6bba5-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="6bba5-213">После каждой неудачной попытки повтора `previousRetryCount` значение будет увеличено на единицу, `elapsedMilliseconds` будет обновлено с учетом времени, затраченного на повторное подключение к этому моменту в миллисекундах, и `retryReason` будет являться ошибкой, которая привела к сбою последней попытки повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="6bba5-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="6bba5-214">`nextRetryDelayInMilliseconds`должен возвращать число, представляющее число миллисекунд ожидания перед следующей попыткой повторного подключения, или `null` значение, если `HubConnection` должно быть прервано повторное подключение.</span><span class="sxs-lookup"><span data-stu-id="6bba5-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="6bba5-215">Кроме того, можно написать код, который будет повторно подключаться к клиенту вручную, как показано в [подсоединении вручную](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="6bba5-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="6bba5-216">Повторное подключение вручную</span><span class="sxs-lookup"><span data-stu-id="6bba5-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="6bba5-217">До 3,0 клиент JavaScript для SignalR не выполняет автоматическое повторное подключение.</span><span class="sxs-lookup"><span data-stu-id="6bba5-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="6bba5-218">Необходимо написать код, который будет повторно подключаться к клиенту вручную.</span><span class="sxs-lookup"><span data-stu-id="6bba5-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="6bba5-219">В следующем коде показан типичный способ повторного подключения вручную:</span><span class="sxs-lookup"><span data-stu-id="6bba5-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="6bba5-220">Для запуска подключения создается функция (в данном случае это `start` функция).</span><span class="sxs-lookup"><span data-stu-id="6bba5-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="6bba5-221">Вызовите `start` функцию в `onclose` обработчике событий соединения.</span><span class="sxs-lookup"><span data-stu-id="6bba5-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="6bba5-222">В реальной реализации будет использоваться экспоненциальная отсрочка или повторная попытка заданное число раз.</span><span class="sxs-lookup"><span data-stu-id="6bba5-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6bba5-223">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6bba5-223">Additional resources</span></span>

* [<span data-ttu-id="6bba5-224">Справочник по API JavaScript</span><span class="sxs-lookup"><span data-stu-id="6bba5-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="6bba5-225">Учебник по JavaScript</span><span class="sxs-lookup"><span data-stu-id="6bba5-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="6bba5-226">Руководство по пакету и TypeScript</span><span class="sxs-lookup"><span data-stu-id="6bba5-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="6bba5-227">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="6bba5-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="6bba5-228">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="6bba5-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="6bba5-229">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="6bba5-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="6bba5-230">Запросы между источниками (CORS)</span><span class="sxs-lookup"><span data-stu-id="6bba5-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="6bba5-231">[SignalRДокументация, посвященная бессерверной службе Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="6bba5-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
