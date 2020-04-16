---
title: ASP.NET SignalR основной клиент JavaScript
author: bradygaster
description: Обзор ASP.NET клиента SignalR Core JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 43b2cacf9f415ec422a00b28246f30c8ad74de29
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440861"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="c1980-103">ASP.NET SignalR основной клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1980-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="c1980-104">Автор: [Рэйчел Аппель](https://twitter.com/rachelappel) (Rachel Appel)</span><span class="sxs-lookup"><span data-stu-id="c1980-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="c1980-105">Библиотека клиентов Core JavaScript SignalR ASP.NET Позволяет разработчикам вызывать код концентратора на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="c1980-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="c1980-106">[Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) [(как скачать)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c1980-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="c1980-107">Установка SignalR клиентского пакета</span><span class="sxs-lookup"><span data-stu-id="c1980-107">Install the SignalR client package</span></span>

<span data-ttu-id="c1980-108">Библиотека SignalR клиентов JavaScript поставляется в виде пакета [npm.](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c1980-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="c1980-109">В следующих разделах описаны различные способы установки клиентской библиотеки.</span><span class="sxs-lookup"><span data-stu-id="c1980-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="c1980-110">Установка с npm</span><span class="sxs-lookup"><span data-stu-id="c1980-110">Install with npm</span></span>

<span data-ttu-id="c1980-111">При использовании Visual Studio выполняй следующие команды из **консоли Package Manager,** находясь в корневой папке.</span><span class="sxs-lookup"><span data-stu-id="c1980-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="c1980-112">Для Visual Studio Code выполняйте следующие команды из **интегрированного терминала.**</span><span class="sxs-lookup"><span data-stu-id="c1980-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="c1980-113">npm устанавливает содержимое упаковки в \*папку node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="c1980-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="c1980-114">Создайте новую папку с именем *сигнальный сигнальник* под папкой *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="c1980-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="c1980-115">Копируйте файл *signalr.js* в папку *wwwroot'lib.signalr.*</span><span class="sxs-lookup"><span data-stu-id="c1980-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="c1980-116">npm устанавливает содержимое упаковки в \*папку node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="c1980-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="c1980-117">Создайте новую папку с именем *сигнальный сигнальник* под папкой *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="c1980-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="c1980-118">Копируйте файл *signalr.js* в папку *wwwroot'lib.signalr.*</span><span class="sxs-lookup"><span data-stu-id="c1980-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="c1980-119">Ссылка SignalR на клиент `<script>` JavaScript в элементе.</span><span class="sxs-lookup"><span data-stu-id="c1980-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="c1980-120">Пример:</span><span class="sxs-lookup"><span data-stu-id="c1980-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="c1980-121">Использование сети доставки контента (CDN)</span><span class="sxs-lookup"><span data-stu-id="c1980-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="c1980-122">Чтобы использовать клиентскую библиотеку без предварительного условия npm, перевесредните cdN-хозяйничаемую копию библиотеки клиента.</span><span class="sxs-lookup"><span data-stu-id="c1980-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="c1980-123">Пример:</span><span class="sxs-lookup"><span data-stu-id="c1980-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="c1980-124">Библиотека клиентов доступна на следующих CDNs:</span><span class="sxs-lookup"><span data-stu-id="c1980-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="c1980-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="c1980-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="c1980-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="c1980-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="c1980-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="c1980-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="c1980-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="c1980-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="c1980-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="c1980-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="c1980-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="c1980-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="c1980-131">Установка с LibMan</span><span class="sxs-lookup"><span data-stu-id="c1980-131">Install with LibMan</span></span>

<span data-ttu-id="c1980-132">[LibMan](xref:client-side/libman/index) может быть использован для установки определенных файлов библиотеки клиентов из библиотеки клиентов, хозяемых CDN.</span><span class="sxs-lookup"><span data-stu-id="c1980-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="c1980-133">Например, только добавить в проект minified файл JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c1980-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="c1980-134">Подробную информацию об этом подходе можно узнать в [ SignalR библиотеке клиента.](xref:tutorials/signalr#add-the-signalr-client-library)</span><span class="sxs-lookup"><span data-stu-id="c1980-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="c1980-135">Подключение к концентратору</span><span class="sxs-lookup"><span data-stu-id="c1980-135">Connect to a hub</span></span>

<span data-ttu-id="c1980-136">Следующий код создает и запускает соединение.</span><span class="sxs-lookup"><span data-stu-id="c1980-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="c1980-137">Название концентратора является нечувствительным.</span><span class="sxs-lookup"><span data-stu-id="c1980-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="c1980-138">Соединения поперечному происхождению</span><span class="sxs-lookup"><span data-stu-id="c1980-138">Cross-origin connections</span></span>

<span data-ttu-id="c1980-139">Как правило, браузеры загружают соединения из того же домена, что и запрашиваемый на странице.</span><span class="sxs-lookup"><span data-stu-id="c1980-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="c1980-140">Однако бывают случаи, когда требуется подключение к другому домену.</span><span class="sxs-lookup"><span data-stu-id="c1980-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="c1980-141">Чтобы предотвратить чтение вредоносным сайтом конфиденциальных данных с другого сайта, [соединения кросс-происхождения](xref:security/cors) отключены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c1980-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="c1980-142">Чтобы разрешить запрос на перекрестное `Startup` происхождение, включите его в классе.</span><span class="sxs-lookup"><span data-stu-id="c1980-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="c1980-143">Методы вызова концентратора от клиента</span><span class="sxs-lookup"><span data-stu-id="c1980-143">Call hub methods from client</span></span>

<span data-ttu-id="c1980-144">Клиенты JavaScript называют общедоступные методы на концентратах с помощью метода [вызова](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [HubConnection.](/javascript/api/%40aspnet/signalr/hubconnection)</span><span class="sxs-lookup"><span data-stu-id="c1980-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="c1980-145">Метод `invoke` принимает два аргумента:</span><span class="sxs-lookup"><span data-stu-id="c1980-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="c1980-146">Название метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="c1980-146">The name of the hub method.</span></span> <span data-ttu-id="c1980-147">В следующем примере имя метода `SendMessage`на концентраторе находится под названием .</span><span class="sxs-lookup"><span data-stu-id="c1980-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="c1980-148">Любые аргументы, определенные в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="c1980-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="c1980-149">В следующем примере имя `message`аргумента .</span><span class="sxs-lookup"><span data-stu-id="c1980-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="c1980-150">В примере кода используется синтаксис функции стрелка, который поддерживается в текущих версиях всех основных браузеров, кроме Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="c1980-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="c1980-151">Если вы используете SignalR службу Azure в *режиме Serverless,* вы не можете вызвать методы концентратора от клиента.</span><span class="sxs-lookup"><span data-stu-id="c1980-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="c1980-152">Для получения дополнительной информации смотрите [ SignalR документацию службы](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="c1980-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="c1980-153">Метод `invoke` возвращает [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span><span class="sxs-lookup"><span data-stu-id="c1980-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="c1980-154">Проблема `Promise` определяется значением возврата (если таковое значение имеется) при возврате метода на сервере.</span><span class="sxs-lookup"><span data-stu-id="c1980-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="c1980-155">Если метод на сервере бросает ошибку, `Promise` отклоняется сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="c1980-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="c1980-156">Используйте `then` `catch` и методы `Promise` на себе для `await` обработки этих случаях (или синтаксиса).</span><span class="sxs-lookup"><span data-stu-id="c1980-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="c1980-157">Метод `send` возвращает JavaScript `Promise`.</span><span class="sxs-lookup"><span data-stu-id="c1980-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="c1980-158">Проблема `Promise` решается при отправке сообщения на сервер.</span><span class="sxs-lookup"><span data-stu-id="c1980-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="c1980-159">Если есть ошибка отправки сообщения, `Promise` отклоняется с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="c1980-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="c1980-160">Используйте `then` `catch` и методы `Promise` на себе для `await` обработки этих случаях (или синтаксиса).</span><span class="sxs-lookup"><span data-stu-id="c1980-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="c1980-161">Использование `send` не дожидается получения сообщением сервером.</span><span class="sxs-lookup"><span data-stu-id="c1980-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="c1980-162">Следовательно, невозможно вернуть данные или ошибки с сервера.</span><span class="sxs-lookup"><span data-stu-id="c1980-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="c1980-163">Вызов методов клиента из концентратора</span><span class="sxs-lookup"><span data-stu-id="c1980-163">Call client methods from hub</span></span>

<span data-ttu-id="c1980-164">Для получения сообщений из концентратора определите метод, используя [метод](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection`.</span><span class="sxs-lookup"><span data-stu-id="c1980-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="c1980-165">Название метода клиента JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c1980-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="c1980-166">В следующем примере имя `ReceiveMessage`метода .</span><span class="sxs-lookup"><span data-stu-id="c1980-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="c1980-167">Аргументы, которые концентратор переходит к методу.</span><span class="sxs-lookup"><span data-stu-id="c1980-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="c1980-168">В следующем примере значение `message`аргумента .</span><span class="sxs-lookup"><span data-stu-id="c1980-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="c1980-169">Предыдущий код `connection.on` выполняется, когда код сервера вызывает его с помощью [метода SendAsync.](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)</span><span class="sxs-lookup"><span data-stu-id="c1980-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="c1980-170">определяет, какой метод клиента вызывать путем сопоставления имени метода и аргументов, определенных в `SendAsync` и `connection.on`.</span><span class="sxs-lookup"><span data-stu-id="c1980-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="c1980-171">В качестве наилучшей практики, `HubConnection` вызов `on` [метод ана-старта](/javascript/api/%40aspnet/signalr/hubconnection#start) на after .</span><span class="sxs-lookup"><span data-stu-id="c1980-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="c1980-172">Это гарантирует, что ваши обработчики будут зарегистрированы до получения сообщений.</span><span class="sxs-lookup"><span data-stu-id="c1980-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="c1980-173">Обработка ошибок и ведение журнала</span><span class="sxs-lookup"><span data-stu-id="c1980-173">Error handling and logging</span></span>

<span data-ttu-id="c1980-174">Цепочка `catch` метода до `start` конца метода для обработки ошибок на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="c1980-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="c1980-175">Используйте `console.error` для вывода ошибок на консоль браузера.</span><span class="sxs-lookup"><span data-stu-id="c1980-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="c1980-176">Настройка отслеживания журналов на стороне клиента путем передачи регистратора и типа события для входа при подключении.</span><span class="sxs-lookup"><span data-stu-id="c1980-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="c1980-177">Сообщения регистрируются с указанным уровнем журнала и выше.</span><span class="sxs-lookup"><span data-stu-id="c1980-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="c1980-178">Доступные уровни журнала следующие:</span><span class="sxs-lookup"><span data-stu-id="c1980-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="c1980-179">`signalR.LogLevel.Error`&ndash; Сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="c1980-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="c1980-180">Только `Error` журналы сообщений.</span><span class="sxs-lookup"><span data-stu-id="c1980-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="c1980-181">`signalR.LogLevel.Warning`&ndash; Предупреждающие сообщения о возможных ошибках.</span><span class="sxs-lookup"><span data-stu-id="c1980-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="c1980-182">Входы `Warning`и `Error` сообщения.</span><span class="sxs-lookup"><span data-stu-id="c1980-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="c1980-183">`signalR.LogLevel.Information`&ndash; Сообщения о состоянии без ошибок.</span><span class="sxs-lookup"><span data-stu-id="c1980-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="c1980-184">Входы `Information` `Warning`, `Error` и сообщения.</span><span class="sxs-lookup"><span data-stu-id="c1980-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="c1980-185">`signalR.LogLevel.Trace`&ndash; Отслеживаемые сообщения.</span><span class="sxs-lookup"><span data-stu-id="c1980-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="c1980-186">Регистрирует все, включая данные, перевозимые между концентратором и клиентом.</span><span class="sxs-lookup"><span data-stu-id="c1980-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="c1980-187">Используйте метод [настройки](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) на [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) для настройки уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="c1980-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="c1980-188">Сообщения регистрируются на консоль браузера.</span><span class="sxs-lookup"><span data-stu-id="c1980-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="c1980-189">Повторное подключение клиентов</span><span class="sxs-lookup"><span data-stu-id="c1980-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="c1980-190">Автоматическое подключение</span><span class="sxs-lookup"><span data-stu-id="c1980-190">Automatically reconnect</span></span>

<span data-ttu-id="c1980-191">Для клиента JavaScript SignalR можно настроить сятворно `withAutomaticReconnect` ею для автоматического повторного подключения с помощью метода на [HubConnectionBuilder.](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)</span><span class="sxs-lookup"><span data-stu-id="c1980-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="c1980-192">Он не будет автоматически воссоединяться по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c1980-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="c1980-193">Без каких-либо параметров, `withAutomaticReconnect()` настраивает клиента ждать 0, 2, 10 и 30 секунд соответственно, прежде чем пытаться каждую попытку повторного подключения, останавливаясь после четырех неудачных попыток.</span><span class="sxs-lookup"><span data-stu-id="c1980-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="c1980-194">Перед началом любых попыток `HubConnection` повторного `HubConnectionState.Reconnecting` подключения, будет `onreconnecting` переход в состояние и `Disconnected` запустить его обратные вызовы вместо перехода к состоянию и запуска его `onclose` обратных вызовов, как `HubConnection` без автоматического переподключения настроены.</span><span class="sxs-lookup"><span data-stu-id="c1980-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="c1980-195">Это дает возможность предупредить пользователей о потере соединения и отключить элементы пользовательного иного пользовательного идентимного.</span><span class="sxs-lookup"><span data-stu-id="c1980-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="c1980-196">Если клиент успешно воссоединяется в течение `HubConnection` первых четырех `Connected` попыток, `onreconnected` будет переход обратно в состояние и запустить его обратные вызовы.</span><span class="sxs-lookup"><span data-stu-id="c1980-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="c1980-197">Это дает возможность информировать пользователей о том, что соединение восстановлено.</span><span class="sxs-lookup"><span data-stu-id="c1980-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="c1980-198">Поскольку соединение выглядит совершенно новым для `connectionId` сервера, новый будет предоставлен обратный `onreconnected` вызов.</span><span class="sxs-lookup"><span data-stu-id="c1980-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="c1980-199">Параметр `onreconnected` обратного вызова будет неопределенным, если `HubConnection` он был настроен, чтобы [пропустить переговоры.](xref:signalr/configuration#configure-client-options) `connectionId`</span><span class="sxs-lookup"><span data-stu-id="c1980-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="c1980-200">`withAutomaticReconnect()`не будет настраивать `HubConnection` для повторной попытки первоначальных сбоев запуска, поэтому сбои запуска должны обрабатываться вручную:</span><span class="sxs-lookup"><span data-stu-id="c1980-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="c1980-201">Если клиент не успешно воссоединится в течение `HubConnection` первых четырех `Disconnected` попыток, будет переход к состоянию и огонь его [закрытых](/javascript/api/%40aspnet/signalr/hubconnection#onclose) обратных вызовов.</span><span class="sxs-lookup"><span data-stu-id="c1980-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="c1980-202">Это дает возможность информировать пользователей о том, что подключение было навсегда утеряно, и рекомендует обновить страницу:</span><span class="sxs-lookup"><span data-stu-id="c1980-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="c1980-203">Для настройки пользовательского числа попыток повторного подключения перед отключением или `withAutomaticReconnect` изменением времени повторного подключения принимает массив чисел, представляющих задержку в миллисекундах, чтобы подождать перед началом каждой попытки повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="c1980-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="c1980-204">Предыдущий пример `HubConnection` настраивает, чтобы начать попытку повторного подключения сразу после потери соединения.</span><span class="sxs-lookup"><span data-stu-id="c1980-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="c1980-205">Это также верно для конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c1980-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="c1980-206">Если первая попытка повторного подключения не удается, вторая попытка повторного подключения также начнется немедленно, а не ждать 2 секунды, как это было бы в конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c1980-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="c1980-207">Если вторая попытка повторного подключения не удается, третья попытка повторного подключения начнется через 10 секунд, что опять же похоже на конфигурацию по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c1980-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="c1980-208">Пользовательское поведение затем снова отклоняется от поведения по умолчанию, останавливаясь после третьего сбоя попытки повторного подключения вместо того, чтобы попытаться еще одну попытку повторного подключения в другой 30 секунд, как это было бы в конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c1980-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="c1980-209">Если вы хотите еще больше контроля над временем и `withAutomaticReconnect` числом попыток `IRetryPolicy` автоматического повторного подключения, `nextRetryDelayInMilliseconds`принимает объект, реализующий интерфейс, который имеет один метод под названием.</span><span class="sxs-lookup"><span data-stu-id="c1980-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="c1980-210">`nextRetryDelayInMilliseconds`принимает один аргумент с `RetryContext`типом .</span><span class="sxs-lookup"><span data-stu-id="c1980-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="c1980-211">Имеет `RetryContext` три `previousRetryCount`свойства: `elapsedMilliseconds` `retryReason` , и `number`которые `number` являются `Error` , а и соответственно.</span><span class="sxs-lookup"><span data-stu-id="c1980-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="c1980-212">Перед первой попыткой повторного подключения, оба `previousRetryCount` и `elapsedMilliseconds` будет равен нулю, и `retryReason` будет ошибка, которая вызвала соединение будет потеряно.</span><span class="sxs-lookup"><span data-stu-id="c1980-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="c1980-213">После каждой неудачной `previousRetryCount` попытки повтора, будет `elapsedMilliseconds` приравлечен один, будет обновляться, чтобы отразить количество `retryReason` времени, затрачиваемого на повторное подключение до сих пор в миллисекундах, и будет ошибка, которая вызвала последнюю попытку повторного подключения к сбою.</span><span class="sxs-lookup"><span data-stu-id="c1980-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="c1980-214">`nextRetryDelayInMilliseconds`необходимо вернуть либо число, представляющее количество миллисекунд, чтобы `null` ждать `HubConnection` до следующей попытки повторного подключения, либо если оно должно прекратить повторное подключение.</span><span class="sxs-lookup"><span data-stu-id="c1980-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
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

<span data-ttu-id="c1980-215">Кроме того, можно написать код, который будет повторно подключать клиента вручную, как показано в [Ручном воссоединении.](#manually-reconnect)</span><span class="sxs-lookup"><span data-stu-id="c1980-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="c1980-216">Ручное подключение</span><span class="sxs-lookup"><span data-stu-id="c1980-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="c1980-217">До 3.0 клиент SignalR JavaScript не переподключается автоматически.</span><span class="sxs-lookup"><span data-stu-id="c1980-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="c1980-218">Вы должны написать код, который будет подключить клиента вручную.</span><span class="sxs-lookup"><span data-stu-id="c1980-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="c1980-219">Следующий код демонстрирует типичный ручной подход к воссоединению:</span><span class="sxs-lookup"><span data-stu-id="c1980-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="c1980-220">Функция (в данном случае `start` функция) создается для запуска соединения.</span><span class="sxs-lookup"><span data-stu-id="c1980-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="c1980-221">Вызов `start` функции в `onclose` обработчике событий соединения.</span><span class="sxs-lookup"><span data-stu-id="c1980-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="c1980-222">В реальной реализации будет использоватьэксивентивеемый бэк-офф или повторить определенное количество раз, прежде чем сдаваться.</span><span class="sxs-lookup"><span data-stu-id="c1980-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1980-223">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c1980-223">Additional resources</span></span>

* [<span data-ttu-id="c1980-224">Справочник по JavaScript API</span><span class="sxs-lookup"><span data-stu-id="c1980-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="c1980-225">Учебник JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1980-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c1980-226">Учебник WebPack и TypeScript</span><span class="sxs-lookup"><span data-stu-id="c1980-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="c1980-227">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="c1980-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="c1980-228">клиент .NET</span><span class="sxs-lookup"><span data-stu-id="c1980-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c1980-229">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="c1980-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="c1980-230">Запросы на кросс-происхождение (CORS)</span><span class="sxs-lookup"><span data-stu-id="c1980-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="c1980-231">[Безсерверная документация Службы Azure SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="c1980-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
