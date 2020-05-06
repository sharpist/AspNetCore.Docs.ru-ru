---
title: Использование протокола концентратора MessagePack SignalR в для ASP.NET Core
author: bradygaster
description: Добавьте протокол концентратора MessagePack в SignalRASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 8db7598d978848f13bf5b21a873340b38154e9a8
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777206"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="5b60a-103">Использование протокола концентратора MessagePack SignalR в для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5b60a-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5b60a-104">В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="5b60a-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="5b60a-105">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="5b60a-105">What is MessagePack?</span></span>

<span data-ttu-id="5b60a-106">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="5b60a-107">Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="5b60a-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="5b60a-108">Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="5b60a-109">имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="5b60a-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="5b60a-110">Настройка MessagePack на сервере</span><span class="sxs-lookup"><span data-stu-id="5b60a-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="5b60a-111">Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="5b60a-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="5b60a-112">В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `AddSignalR` вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="5b60a-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="5b60a-113">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5b60a-113">JSON is enabled by default.</span></span> <span data-ttu-id="5b60a-114">Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="5b60a-115">Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="5b60a-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="5b60a-116">В этом делегате `SerializerOptions` свойство можно использовать для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="5b60a-117">Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="5b60a-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="5b60a-118">Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="5b60a-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="5b60a-119">Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления.</span><span class="sxs-lookup"><span data-stu-id="5b60a-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="5b60a-120">Например, вызов `.WithSecurity(MessagePackSecurity.UntrustedData)` при замене `SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="5b60a-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="5b60a-121">Настройка MessagePack на клиенте</span><span class="sxs-lookup"><span data-stu-id="5b60a-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="5b60a-122">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="5b60a-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="5b60a-123">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="5b60a-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="5b60a-124">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="5b60a-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="5b60a-125">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="5b60a-125">.NET client</span></span>

<span data-ttu-id="5b60a-126">Чтобы включить MessagePack в клиенте .NET, установите пакет `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5b60a-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="5b60a-127">Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.</span><span class="sxs-lookup"><span data-stu-id="5b60a-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="5b60a-128">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b60a-128">JavaScript client</span></span>

<span data-ttu-id="5b60a-129">Поддержка MessagePack для клиента JavaScript предоставляется пакетом [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM.</span><span class="sxs-lookup"><span data-stu-id="5b60a-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="5b60a-130">Установите пакет, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="5b60a-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="5b60a-131">После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:</span><span class="sxs-lookup"><span data-stu-id="5b60a-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="5b60a-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="5b60a-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="5b60a-133">В браузере также должна быть `msgpack5` ссылка на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="5b60a-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="5b60a-134">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="5b60a-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="5b60a-135">Библиотеку можно найти по адресу *node_modules \msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="5b60a-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="5b60a-136">При использовании `<script>` элемента важен порядок.</span><span class="sxs-lookup"><span data-stu-id="5b60a-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="5b60a-137">Если перед *msgpack5. js*указана ссылка на *сигналр-протокол-мсгпакк. js* , возникает ошибка при попытке подключения с помощью MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="5b60a-138">*SignalR. js* также требуется перед *сигналр-протокол-мсгпакк. js*.</span><span class="sxs-lookup"><span data-stu-id="5b60a-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="5b60a-139">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.</span><span class="sxs-lookup"><span data-stu-id="5b60a-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="5b60a-140">В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="5b60a-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="5b60a-141">Особенности MessagePack</span><span class="sxs-lookup"><span data-stu-id="5b60a-141">MessagePack quirks</span></span>

<span data-ttu-id="5b60a-142">Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="5b60a-143">MessagePack учитывает регистр</span><span class="sxs-lookup"><span data-stu-id="5b60a-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="5b60a-144">В протоколе MessagePack учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="5b60a-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="5b60a-145">Например, рассмотрим следующий класс C#:</span><span class="sxs-lookup"><span data-stu-id="5b60a-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="5b60a-146">При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#.</span><span class="sxs-lookup"><span data-stu-id="5b60a-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="5b60a-147">Например:</span><span class="sxs-lookup"><span data-stu-id="5b60a-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="5b60a-148">Использование `camelCased` имен не будет правильно привязано к классу C#.</span><span class="sxs-lookup"><span data-stu-id="5b60a-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="5b60a-149">Это можно обойти, используя `Key` атрибут, чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="5b60a-150">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="5b60a-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="5b60a-151">DateTime. Kind не сохраняется при сериализации или десериализации</span><span class="sxs-lookup"><span data-stu-id="5b60a-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="5b60a-152">Протокол MessagePack не предоставляет способ кодирования `Kind` значения. `DateTime`</span><span class="sxs-lookup"><span data-stu-id="5b60a-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="5b60a-153">В результате при десериализации даты протокол концентратора MessagePack преобразуется в формат UTC, если в `DateTime.Kind` `DateTimeKind.Local` противном случае он не будет затрагивать время и передать его как есть.</span><span class="sxs-lookup"><span data-stu-id="5b60a-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="5b60a-154">При работе со `DateTime` значениями рекомендуется преобразовать в формат UTC перед отправкой.</span><span class="sxs-lookup"><span data-stu-id="5b60a-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="5b60a-155">Преобразуйте их из времени в формате UTC в местное время при их получении.</span><span class="sxs-lookup"><span data-stu-id="5b60a-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="5b60a-156">DateTime. MinValue не поддерживается в MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b60a-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="5b60a-157">Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая клиентом SignalR JavaScript, не поддерживает `timestamp96` тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="5b60a-158">Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="5b60a-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="5b60a-159">Значение `DateTime.MinValue` равно `January 1, 0001`, которое должно быть закодировано в `timestamp96` значении.</span><span class="sxs-lookup"><span data-stu-id="5b60a-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="5b60a-160">По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="5b60a-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="5b60a-161">При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="5b60a-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="5b60a-162">`DateTime.MinValue` Обычно используется для кодирования «отсутствующего» или `null` значения.</span><span class="sxs-lookup"><span data-stu-id="5b60a-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="5b60a-163">Если необходимо закодировать это значение в MessagePack, используйте значение NULL `DateTime` (`DateTime?`) или закодировать отдельное `bool` значение, указывающее, существует ли дата.</span><span class="sxs-lookup"><span data-stu-id="5b60a-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="5b60a-164">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="5b60a-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="5b60a-165">Поддержка MessagePack в среде предварительной компиляции "до времени"</span><span class="sxs-lookup"><span data-stu-id="5b60a-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="5b60a-166">Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="5b60a-167">Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="5b60a-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="5b60a-168">В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="5b60a-169">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="5b60a-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="5b60a-170">После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="5b60a-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="5b60a-171">Проверки типов более строго в MessagePack</span><span class="sxs-lookup"><span data-stu-id="5b60a-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="5b60a-172">Протокол JSON-концентратора выполнит преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="5b60a-173">Например, если входящий объект имеет значение свойства, которое является числом (`{ foo: 42 }`), но свойство класса .NET имеет тип `string`, значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="5b60a-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="5b60a-174">Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="5b60a-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="5b60a-175">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="5b60a-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="5b60a-176">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5b60a-176">Related resources</span></span>

* [<span data-ttu-id="5b60a-177">Приступая к работе</span><span class="sxs-lookup"><span data-stu-id="5b60a-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5b60a-178">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="5b60a-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="5b60a-179">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b60a-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="5b60a-180">В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="5b60a-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="5b60a-181">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="5b60a-181">What is MessagePack?</span></span>

<span data-ttu-id="5b60a-182">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="5b60a-183">Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="5b60a-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="5b60a-184">Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="5b60a-185">имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="5b60a-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="5b60a-186">Настройка MessagePack на сервере</span><span class="sxs-lookup"><span data-stu-id="5b60a-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="5b60a-187">Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="5b60a-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="5b60a-188">В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `AddSignalR` вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="5b60a-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="5b60a-189">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5b60a-189">JSON is enabled by default.</span></span> <span data-ttu-id="5b60a-190">Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="5b60a-191">Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="5b60a-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="5b60a-192">В этом делегате `FormatterResolvers` свойство можно использовать для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="5b60a-193">Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="5b60a-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="5b60a-194">Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="5b60a-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="5b60a-195">Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления.</span><span class="sxs-lookup"><span data-stu-id="5b60a-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="5b60a-196">Например, присвоение `MessagePackSecurity.Active` статическому свойству значения `MessagePackSecurity.UntrustedData`.</span><span class="sxs-lookup"><span data-stu-id="5b60a-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="5b60a-197">Для `MessagePackSecurity.Active` установки параметра требуется ручная установка [версии 1,9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="5b60a-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="5b60a-198">При `MessagePack` установке обновлений 1,9. x используется SignalR версия.</span><span class="sxs-lookup"><span data-stu-id="5b60a-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="5b60a-199">Если `MessagePackSecurity.Active` параметр не имеет значение `MessagePackSecurity.UntrustedData`, вредоносный клиент может вызвать отказ в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="5b60a-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="5b60a-200">Задайте `MessagePackSecurity.Active` в `Program.Main`, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="5b60a-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="5b60a-201">Настройка MessagePack на клиенте</span><span class="sxs-lookup"><span data-stu-id="5b60a-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="5b60a-202">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="5b60a-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="5b60a-203">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="5b60a-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="5b60a-204">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="5b60a-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="5b60a-205">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="5b60a-205">.NET client</span></span>

<span data-ttu-id="5b60a-206">Чтобы включить MessagePack в клиенте .NET, установите пакет `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5b60a-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="5b60a-207">Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.</span><span class="sxs-lookup"><span data-stu-id="5b60a-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="5b60a-208">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b60a-208">JavaScript client</span></span>

<span data-ttu-id="5b60a-209">Поддержка MessagePack для клиента JavaScript предоставляется пакетом [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM.</span><span class="sxs-lookup"><span data-stu-id="5b60a-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="5b60a-210">Установите пакет, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="5b60a-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="5b60a-211">После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:</span><span class="sxs-lookup"><span data-stu-id="5b60a-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="5b60a-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="5b60a-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="5b60a-213">В браузере также должна быть `msgpack5` ссылка на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="5b60a-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="5b60a-214">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="5b60a-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="5b60a-215">Библиотеку можно найти по адресу *node_modules \msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="5b60a-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="5b60a-216">При использовании `<script>` элемента важен порядок.</span><span class="sxs-lookup"><span data-stu-id="5b60a-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="5b60a-217">Если перед *msgpack5. js*указана ссылка на *сигналр-протокол-мсгпакк. js* , возникает ошибка при попытке подключения с помощью MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="5b60a-218">*SignalR. js* также требуется перед *сигналр-протокол-мсгпакк. js*.</span><span class="sxs-lookup"><span data-stu-id="5b60a-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="5b60a-219">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.</span><span class="sxs-lookup"><span data-stu-id="5b60a-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="5b60a-220">В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="5b60a-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="5b60a-221">Особенности MessagePack</span><span class="sxs-lookup"><span data-stu-id="5b60a-221">MessagePack quirks</span></span>

<span data-ttu-id="5b60a-222">Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="5b60a-223">MessagePack учитывает регистр</span><span class="sxs-lookup"><span data-stu-id="5b60a-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="5b60a-224">В протоколе MessagePack учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="5b60a-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="5b60a-225">Например, рассмотрим следующий класс C#:</span><span class="sxs-lookup"><span data-stu-id="5b60a-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="5b60a-226">При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#.</span><span class="sxs-lookup"><span data-stu-id="5b60a-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="5b60a-227">Например:</span><span class="sxs-lookup"><span data-stu-id="5b60a-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="5b60a-228">Использование `camelCased` имен не будет правильно привязано к классу C#.</span><span class="sxs-lookup"><span data-stu-id="5b60a-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="5b60a-229">Это можно обойти, используя `Key` атрибут, чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="5b60a-230">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="5b60a-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="5b60a-231">DateTime. Kind не сохраняется при сериализации или десериализации</span><span class="sxs-lookup"><span data-stu-id="5b60a-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="5b60a-232">Протокол MessagePack не предоставляет способ кодирования `Kind` значения. `DateTime`</span><span class="sxs-lookup"><span data-stu-id="5b60a-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="5b60a-233">В результате при десериализации даты протокол концентратора MessagePack предполагает, что входящая Дата находится в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="5b60a-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="5b60a-234">Если вы работаете со `DateTime` значениями в местном времени, перед отправкой рекомендуется преобразовать в формат UTC.</span><span class="sxs-lookup"><span data-stu-id="5b60a-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="5b60a-235">Преобразуйте их из времени в формате UTC в местное время при их получении.</span><span class="sxs-lookup"><span data-stu-id="5b60a-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="5b60a-236">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="5b60a-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="5b60a-237">DateTime. MinValue не поддерживается в MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b60a-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="5b60a-238">Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая клиентом SignalR JavaScript, не поддерживает `timestamp96` тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="5b60a-239">Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="5b60a-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="5b60a-240">Значение `DateTime.MinValue` равно `January 1, 0001`, которое должно быть закодировано в `timestamp96` значении.</span><span class="sxs-lookup"><span data-stu-id="5b60a-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="5b60a-241">По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="5b60a-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="5b60a-242">При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="5b60a-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="5b60a-243">`DateTime.MinValue` Обычно используется для кодирования «отсутствующего» или `null` значения.</span><span class="sxs-lookup"><span data-stu-id="5b60a-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="5b60a-244">Если необходимо закодировать это значение в MessagePack, используйте значение NULL `DateTime` (`DateTime?`) или закодировать отдельное `bool` значение, указывающее, существует ли дата.</span><span class="sxs-lookup"><span data-stu-id="5b60a-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="5b60a-245">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="5b60a-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="5b60a-246">Поддержка MessagePack в среде предварительной компиляции "до времени"</span><span class="sxs-lookup"><span data-stu-id="5b60a-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="5b60a-247">Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="5b60a-248">Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="5b60a-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="5b60a-249">В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="5b60a-250">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="5b60a-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="5b60a-251">После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="5b60a-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="5b60a-252">Проверки типов более строго в MessagePack</span><span class="sxs-lookup"><span data-stu-id="5b60a-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="5b60a-253">Протокол JSON-концентратора выполнит преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="5b60a-254">Например, если входящий объект имеет значение свойства, которое является числом (`{ foo: 42 }`), но свойство класса .NET имеет тип `string`, значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="5b60a-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="5b60a-255">Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="5b60a-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="5b60a-256">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="5b60a-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="5b60a-257">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5b60a-257">Related resources</span></span>

* [<span data-ttu-id="5b60a-258">Приступая к работе</span><span class="sxs-lookup"><span data-stu-id="5b60a-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5b60a-259">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="5b60a-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="5b60a-260">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b60a-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5b60a-261">В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="5b60a-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="5b60a-262">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="5b60a-262">What is MessagePack?</span></span>

<span data-ttu-id="5b60a-263">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="5b60a-264">Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="5b60a-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="5b60a-265">Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="5b60a-266">имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="5b60a-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="5b60a-267">Настройка MessagePack на сервере</span><span class="sxs-lookup"><span data-stu-id="5b60a-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="5b60a-268">Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="5b60a-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="5b60a-269">В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `AddSignalR` вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="5b60a-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="5b60a-270">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5b60a-270">JSON is enabled by default.</span></span> <span data-ttu-id="5b60a-271">Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="5b60a-272">Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="5b60a-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="5b60a-273">В этом делегате `FormatterResolvers` свойство можно использовать для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="5b60a-274">Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="5b60a-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="5b60a-275">Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="5b60a-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="5b60a-276">Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления.</span><span class="sxs-lookup"><span data-stu-id="5b60a-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="5b60a-277">Например, присвоение `MessagePackSecurity.Active` статическому свойству значения `MessagePackSecurity.UntrustedData`.</span><span class="sxs-lookup"><span data-stu-id="5b60a-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="5b60a-278">Для `MessagePackSecurity.Active` установки параметра требуется ручная установка [версии 1,9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="5b60a-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="5b60a-279">При `MessagePack` установке обновлений 1,9. x используется SignalR версия.</span><span class="sxs-lookup"><span data-stu-id="5b60a-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="5b60a-280">Если `MessagePackSecurity.Active` параметр не имеет значение `MessagePackSecurity.UntrustedData`, вредоносный клиент может вызвать отказ в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="5b60a-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="5b60a-281">Задайте `MessagePackSecurity.Active` в `Program.Main`, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="5b60a-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="5b60a-282">Настройка MessagePack на клиенте</span><span class="sxs-lookup"><span data-stu-id="5b60a-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="5b60a-283">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="5b60a-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="5b60a-284">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="5b60a-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="5b60a-285">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="5b60a-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="5b60a-286">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="5b60a-286">.NET client</span></span>

<span data-ttu-id="5b60a-287">Чтобы включить MessagePack в клиенте .NET, установите пакет `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5b60a-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="5b60a-288">Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.</span><span class="sxs-lookup"><span data-stu-id="5b60a-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="5b60a-289">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b60a-289">JavaScript client</span></span>

<span data-ttu-id="5b60a-290">Поддержка MessagePack для клиента JavaScript предоставляется пакетом [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) NPM.</span><span class="sxs-lookup"><span data-stu-id="5b60a-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="5b60a-291">Установите пакет, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="5b60a-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="5b60a-292">После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:</span><span class="sxs-lookup"><span data-stu-id="5b60a-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="5b60a-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="5b60a-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="5b60a-294">В браузере также должна быть `msgpack5` ссылка на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="5b60a-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="5b60a-295">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="5b60a-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="5b60a-296">Библиотеку можно найти по адресу *node_modules \msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="5b60a-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="5b60a-297">При использовании `<script>` элемента важен порядок.</span><span class="sxs-lookup"><span data-stu-id="5b60a-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="5b60a-298">Если перед *msgpack5. js*указана ссылка на *сигналр-протокол-мсгпакк. js* , возникает ошибка при попытке подключения с помощью MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="5b60a-299">*SignalR. js* также требуется перед *сигналр-протокол-мсгпакк. js*.</span><span class="sxs-lookup"><span data-stu-id="5b60a-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="5b60a-300">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.</span><span class="sxs-lookup"><span data-stu-id="5b60a-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="5b60a-301">В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="5b60a-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="5b60a-302">Особенности MessagePack</span><span class="sxs-lookup"><span data-stu-id="5b60a-302">MessagePack quirks</span></span>

<span data-ttu-id="5b60a-303">Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="5b60a-304">MessagePack учитывает регистр</span><span class="sxs-lookup"><span data-stu-id="5b60a-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="5b60a-305">В протоколе MessagePack учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="5b60a-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="5b60a-306">Например, рассмотрим следующий класс C#:</span><span class="sxs-lookup"><span data-stu-id="5b60a-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="5b60a-307">При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#.</span><span class="sxs-lookup"><span data-stu-id="5b60a-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="5b60a-308">Например:</span><span class="sxs-lookup"><span data-stu-id="5b60a-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="5b60a-309">Использование `camelCased` имен не будет правильно привязано к классу C#.</span><span class="sxs-lookup"><span data-stu-id="5b60a-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="5b60a-310">Это можно обойти, используя `Key` атрибут, чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="5b60a-311">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="5b60a-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="5b60a-312">DateTime. Kind не сохраняется при сериализации или десериализации</span><span class="sxs-lookup"><span data-stu-id="5b60a-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="5b60a-313">Протокол MessagePack не предоставляет способ кодирования `Kind` значения. `DateTime`</span><span class="sxs-lookup"><span data-stu-id="5b60a-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="5b60a-314">В результате при десериализации даты протокол концентратора MessagePack предполагает, что входящая Дата находится в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="5b60a-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="5b60a-315">Если вы работаете со `DateTime` значениями в местном времени, перед отправкой рекомендуется преобразовать в формат UTC.</span><span class="sxs-lookup"><span data-stu-id="5b60a-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="5b60a-316">Преобразуйте их из времени в формате UTC в местное время при их получении.</span><span class="sxs-lookup"><span data-stu-id="5b60a-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="5b60a-317">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="5b60a-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="5b60a-318">DateTime. MinValue не поддерживается в MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b60a-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="5b60a-319">Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая клиентом SignalR JavaScript, не поддерживает `timestamp96` тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="5b60a-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="5b60a-320">Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="5b60a-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="5b60a-321">Значение `DateTime.MinValue` , `January 1, 0001` которое должно быть закодировано в `timestamp96` значении.</span><span class="sxs-lookup"><span data-stu-id="5b60a-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="5b60a-322">По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="5b60a-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="5b60a-323">При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="5b60a-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="5b60a-324">`DateTime.MinValue` Обычно используется для кодирования «отсутствующего» или `null` значения.</span><span class="sxs-lookup"><span data-stu-id="5b60a-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="5b60a-325">Если необходимо закодировать это значение в MessagePack, используйте значение NULL `DateTime` (`DateTime?`) или закодировать отдельное `bool` значение, указывающее, существует ли дата.</span><span class="sxs-lookup"><span data-stu-id="5b60a-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="5b60a-326">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="5b60a-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="5b60a-327">Поддержка MessagePack в среде предварительной компиляции "до времени"</span><span class="sxs-lookup"><span data-stu-id="5b60a-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="5b60a-328">Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="5b60a-329">Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="5b60a-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="5b60a-330">В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="5b60a-331">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="5b60a-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="5b60a-332">После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="5b60a-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="5b60a-333">Проверки типов более строго в MessagePack</span><span class="sxs-lookup"><span data-stu-id="5b60a-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="5b60a-334">Протокол JSON-концентратора выполнит преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="5b60a-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="5b60a-335">Например, если входящий объект имеет значение свойства, которое является числом (`{ foo: 42 }`), но свойство класса .NET имеет тип `string`, значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="5b60a-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="5b60a-336">Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="5b60a-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="5b60a-337">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="5b60a-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="5b60a-338">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5b60a-338">Related resources</span></span>

* [<span data-ttu-id="5b60a-339">Приступая к работе</span><span class="sxs-lookup"><span data-stu-id="5b60a-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5b60a-340">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="5b60a-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="5b60a-341">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b60a-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
