---
title: 'Использование протокола концентратора MessagePack в :::no-loc(SignalR)::: для ASP.NET Core'
author: bradygaster
description: 'Добавьте протокол концентратора MessagePack в ASP.NET Core :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: e7d19a42e48048d2be4b87d6b0ac1ba6b2596ff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058172"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="082f5-103">Использование протокола концентратора MessagePack в :::no-loc(SignalR)::: для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="082f5-103">Use MessagePack Hub Protocol in :::no-loc(SignalR)::: for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="082f5-104">В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="082f5-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="082f5-105">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="082f5-105">What is MessagePack?</span></span>

<span data-ttu-id="082f5-106">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="082f5-107">Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="082f5-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="082f5-108">Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="082f5-109">:::no-loc(SignalR)::: имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="082f5-109">:::no-loc(SignalR)::: has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="082f5-110">Настройка MessagePack на сервере</span><span class="sxs-lookup"><span data-stu-id="082f5-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="082f5-111">Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="082f5-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="082f5-112">В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `Add:::no-loc(SignalR):::` вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="082f5-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="082f5-113">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="082f5-113">JSON is enabled by default.</span></span> <span data-ttu-id="082f5-114">Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="082f5-115">Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="082f5-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="082f5-116">В этом делегате `SerializerOptions` свойство можно использовать для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="082f5-117">Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="082f5-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="082f5-118">Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="082f5-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="082f5-119">Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления.</span><span class="sxs-lookup"><span data-stu-id="082f5-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="082f5-120">Например, вызов `.WithSecurity(MessagePackSecurity.UntrustedData)` при замене `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="082f5-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="082f5-121">Настройка MessagePack на клиенте</span><span class="sxs-lookup"><span data-stu-id="082f5-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="082f5-122">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="082f5-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="082f5-123">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="082f5-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="082f5-124">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="082f5-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="082f5-125">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="082f5-125">.NET client</span></span>

<span data-ttu-id="082f5-126">Чтобы включить MessagePack в клиенте .NET, установите `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` пакет и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="082f5-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="082f5-127">Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.</span><span class="sxs-lookup"><span data-stu-id="082f5-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="082f5-128">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="082f5-128">JavaScript client</span></span>

<span data-ttu-id="082f5-129">Поддержка MessagePack для клиента JavaScript предоставляется [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) пакетом NPM.</span><span class="sxs-lookup"><span data-stu-id="082f5-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="082f5-130">Установите пакет, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="082f5-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="082f5-131">После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:</span><span class="sxs-lookup"><span data-stu-id="082f5-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="082f5-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="082f5-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="082f5-133">В браузере `msgpack5` также должна быть ссылка на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="082f5-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="082f5-134">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="082f5-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="082f5-135">Библиотеку можно найти по адресу *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="082f5-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="082f5-136">При использовании `<script>` элемента важен порядок.</span><span class="sxs-lookup"><span data-stu-id="082f5-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="082f5-137">Если на *signalr-protocol-msgpack.js* имеется ссылка до *msgpack5.js* , то при попытке соединения с MessagePack возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="082f5-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="082f5-138">*signalr.js* также требуется перед *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="082f5-138">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="082f5-139">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.</span><span class="sxs-lookup"><span data-stu-id="082f5-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="082f5-140">В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="082f5-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="082f5-141">Особенности MessagePack</span><span class="sxs-lookup"><span data-stu-id="082f5-141">MessagePack quirks</span></span>

<span data-ttu-id="082f5-142">Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="082f5-143">MessagePack учитывает регистр</span><span class="sxs-lookup"><span data-stu-id="082f5-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="082f5-144">В протоколе MessagePack учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="082f5-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="082f5-145">Например, рассмотрим следующий класс C#:</span><span class="sxs-lookup"><span data-stu-id="082f5-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="082f5-146">При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#.</span><span class="sxs-lookup"><span data-stu-id="082f5-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="082f5-147">Пример:</span><span class="sxs-lookup"><span data-stu-id="082f5-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="082f5-148">Использование `camelCased` имен не будет правильно привязано к классу C#.</span><span class="sxs-lookup"><span data-stu-id="082f5-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="082f5-149">Это можно обойти, используя атрибут, `Key` чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="082f5-150">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="082f5-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="082f5-151">DateTime. Kind не сохраняется при сериализации или десериализации</span><span class="sxs-lookup"><span data-stu-id="082f5-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="082f5-152">Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="082f5-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="082f5-153">В результате при десериализации даты протокол концентратора MessagePack преобразуется в формат UTC, если в `DateTime.Kind` `DateTimeKind.Local` противном случае он не будет затрагивать время и передать его как есть.</span><span class="sxs-lookup"><span data-stu-id="082f5-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="082f5-154">При работе со `DateTime` значениями рекомендуется преобразовать в формат UTC перед отправкой.</span><span class="sxs-lookup"><span data-stu-id="082f5-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="082f5-155">Преобразуйте их из времени в формате UTC в местное время при их получении.</span><span class="sxs-lookup"><span data-stu-id="082f5-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="082f5-156">DateTime. MinValue не поддерживается в MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="082f5-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="082f5-157">Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая :::no-loc(SignalR)::: клиентом JavaScript, не поддерживает `timestamp96` тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="082f5-158">Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="082f5-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="082f5-159">Значение `DateTime.MinValue` равно `January 1, 0001` , которое должно быть закодировано в `timestamp96` значении.</span><span class="sxs-lookup"><span data-stu-id="082f5-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="082f5-160">По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="082f5-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="082f5-161">При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="082f5-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="082f5-162">Обычно `DateTime.MinValue` используется для кодирования «отсутствующего» или `null` значения.</span><span class="sxs-lookup"><span data-stu-id="082f5-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="082f5-163">Если необходимо закодировать это значение в MessagePack, используйте `DateTime` значение null ( `DateTime?` ) или закодировать отдельное `bool` значение, указывающее, существует ли дата.</span><span class="sxs-lookup"><span data-stu-id="082f5-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="082f5-164">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="082f5-164">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="082f5-165">Поддержка MessagePack в среде предварительной компиляции "до времени"</span><span class="sxs-lookup"><span data-stu-id="082f5-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="082f5-166">Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="082f5-167">Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="082f5-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="082f5-168">В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="082f5-169">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="082f5-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="082f5-170">После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="082f5-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="082f5-171">Проверки типов более строго в MessagePack</span><span class="sxs-lookup"><span data-stu-id="082f5-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="082f5-172">Протокол JSON-концентратора выполнит преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="082f5-173">Например, если входящий объект имеет значение свойства, которое является числом (), `{ foo: 42 }` но свойство класса .NET имеет тип `string` , значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="082f5-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="082f5-174">Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="082f5-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="082f5-175">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="082f5-175">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="082f5-176">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="082f5-176">Related resources</span></span>

* [<span data-ttu-id="082f5-177">Начало работы</span><span class="sxs-lookup"><span data-stu-id="082f5-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="082f5-178">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="082f5-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="082f5-179">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="082f5-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="082f5-180">В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="082f5-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="082f5-181">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="082f5-181">What is MessagePack?</span></span>

<span data-ttu-id="082f5-182">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="082f5-183">Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="082f5-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="082f5-184">Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="082f5-185">:::no-loc(SignalR)::: имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="082f5-185">:::no-loc(SignalR)::: has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="082f5-186">Настройка MessagePack на сервере</span><span class="sxs-lookup"><span data-stu-id="082f5-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="082f5-187">Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="082f5-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="082f5-188">В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `Add:::no-loc(SignalR):::` вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="082f5-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="082f5-189">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="082f5-189">JSON is enabled by default.</span></span> <span data-ttu-id="082f5-190">Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="082f5-191">Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="082f5-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="082f5-192">В этом делегате `FormatterResolvers` свойство можно использовать для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="082f5-193">Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="082f5-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="082f5-194">Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="082f5-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="082f5-195">Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления.</span><span class="sxs-lookup"><span data-stu-id="082f5-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="082f5-196">Например, присвоение `MessagePackSecurity.Active` статическому свойству значения `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="082f5-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="082f5-197">Для установки параметра `MessagePackSecurity.Active` требуется ручная установка [версии 1,9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="082f5-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="082f5-198">При установке `MessagePack` обновлений 1,9. x :::no-loc(SignalR)::: используется версия.</span><span class="sxs-lookup"><span data-stu-id="082f5-198">Installing `MessagePack` 1.9.x upgrades the version :::no-loc(SignalR)::: uses.</span></span> <span data-ttu-id="082f5-199">`MessagePack` Версия 2. x предоставила критические изменения и несовместима с :::no-loc(SignalR)::: версиями 3,1 и более ранних версий.</span><span class="sxs-lookup"><span data-stu-id="082f5-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with :::no-loc(SignalR)::: versions 3.1 and earlier.</span></span> <span data-ttu-id="082f5-200">Если `MessagePackSecurity.Active` параметр не имеет значение `MessagePackSecurity.UntrustedData` , вредоносный клиент может вызвать отказ в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="082f5-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="082f5-201">Задайте `MessagePackSecurity.Active` в `Program.Main` , как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="082f5-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="082f5-202">Настройка MessagePack на клиенте</span><span class="sxs-lookup"><span data-stu-id="082f5-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="082f5-203">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="082f5-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="082f5-204">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="082f5-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="082f5-205">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="082f5-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="082f5-206">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="082f5-206">.NET client</span></span>

<span data-ttu-id="082f5-207">Чтобы включить MessagePack в клиенте .NET, установите `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` пакет и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="082f5-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="082f5-208">Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.</span><span class="sxs-lookup"><span data-stu-id="082f5-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="082f5-209">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="082f5-209">JavaScript client</span></span>

<span data-ttu-id="082f5-210">Поддержка MessagePack для клиента JavaScript предоставляется [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) пакетом NPM.</span><span class="sxs-lookup"><span data-stu-id="082f5-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="082f5-211">Установите пакет, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="082f5-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="082f5-212">После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:</span><span class="sxs-lookup"><span data-stu-id="082f5-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="082f5-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="082f5-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="082f5-214">В браузере `msgpack5` также должна быть ссылка на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="082f5-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="082f5-215">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="082f5-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="082f5-216">Библиотеку можно найти по адресу *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="082f5-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="082f5-217">При использовании `<script>` элемента важен порядок.</span><span class="sxs-lookup"><span data-stu-id="082f5-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="082f5-218">Если на *signalr-protocol-msgpack.js* имеется ссылка до *msgpack5.js* , то при попытке соединения с MessagePack возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="082f5-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="082f5-219">*signalr.js* также требуется перед *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="082f5-219">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="082f5-220">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.</span><span class="sxs-lookup"><span data-stu-id="082f5-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="082f5-221">В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="082f5-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="082f5-222">Особенности MessagePack</span><span class="sxs-lookup"><span data-stu-id="082f5-222">MessagePack quirks</span></span>

<span data-ttu-id="082f5-223">Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="082f5-224">MessagePack учитывает регистр</span><span class="sxs-lookup"><span data-stu-id="082f5-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="082f5-225">В протоколе MessagePack учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="082f5-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="082f5-226">Например, рассмотрим следующий класс C#:</span><span class="sxs-lookup"><span data-stu-id="082f5-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="082f5-227">При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#.</span><span class="sxs-lookup"><span data-stu-id="082f5-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="082f5-228">Пример:</span><span class="sxs-lookup"><span data-stu-id="082f5-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="082f5-229">Использование `camelCased` имен не будет правильно привязано к классу C#.</span><span class="sxs-lookup"><span data-stu-id="082f5-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="082f5-230">Это можно обойти, используя атрибут, `Key` чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="082f5-231">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="082f5-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="082f5-232">DateTime. Kind не сохраняется при сериализации или десериализации</span><span class="sxs-lookup"><span data-stu-id="082f5-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="082f5-233">Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="082f5-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="082f5-234">В результате при десериализации даты протокол концентратора MessagePack предполагает, что входящая Дата находится в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="082f5-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="082f5-235">Если вы работаете со `DateTime` значениями в местном времени, перед отправкой рекомендуется преобразовать в формат UTC.</span><span class="sxs-lookup"><span data-stu-id="082f5-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="082f5-236">Преобразуйте их из времени в формате UTC в местное время при их получении.</span><span class="sxs-lookup"><span data-stu-id="082f5-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="082f5-237">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ :::no-loc(SignalR)::: #2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="082f5-237">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="082f5-238">DateTime. MinValue не поддерживается в MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="082f5-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="082f5-239">Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая :::no-loc(SignalR)::: клиентом JavaScript, не поддерживает `timestamp96` тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="082f5-240">Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="082f5-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="082f5-241">Значение `DateTime.MinValue` равно `January 1, 0001` , которое должно быть закодировано в `timestamp96` значении.</span><span class="sxs-lookup"><span data-stu-id="082f5-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="082f5-242">По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="082f5-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="082f5-243">При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="082f5-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="082f5-244">Обычно `DateTime.MinValue` используется для кодирования «отсутствующего» или `null` значения.</span><span class="sxs-lookup"><span data-stu-id="082f5-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="082f5-245">Если необходимо закодировать это значение в MessagePack, используйте `DateTime` значение null ( `DateTime?` ) или закодировать отдельное `bool` значение, указывающее, существует ли дата.</span><span class="sxs-lookup"><span data-stu-id="082f5-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="082f5-246">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="082f5-246">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="082f5-247">Поддержка MessagePack в среде предварительной компиляции "до времени"</span><span class="sxs-lookup"><span data-stu-id="082f5-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="082f5-248">Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="082f5-249">Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="082f5-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="082f5-250">В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="082f5-251">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="082f5-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="082f5-252">После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="082f5-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="082f5-253">Проверки типов более строго в MessagePack</span><span class="sxs-lookup"><span data-stu-id="082f5-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="082f5-254">Протокол JSON-концентратора выполнит преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="082f5-255">Например, если входящий объект имеет значение свойства, которое является числом (), `{ foo: 42 }` но свойство класса .NET имеет тип `string` , значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="082f5-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="082f5-256">Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="082f5-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="082f5-257">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="082f5-257">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="082f5-258">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="082f5-258">Related resources</span></span>

* [<span data-ttu-id="082f5-259">Начало работы</span><span class="sxs-lookup"><span data-stu-id="082f5-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="082f5-260">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="082f5-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="082f5-261">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="082f5-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="082f5-262">В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="082f5-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="082f5-263">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="082f5-263">What is MessagePack?</span></span>

<span data-ttu-id="082f5-264">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="082f5-265">Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="082f5-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="082f5-266">Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="082f5-267">:::no-loc(SignalR)::: имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="082f5-267">:::no-loc(SignalR)::: has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="082f5-268">Настройка MessagePack на сервере</span><span class="sxs-lookup"><span data-stu-id="082f5-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="082f5-269">Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="082f5-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="082f5-270">В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `Add:::no-loc(SignalR):::` вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="082f5-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="082f5-271">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="082f5-271">JSON is enabled by default.</span></span> <span data-ttu-id="082f5-272">Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="082f5-273">Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="082f5-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="082f5-274">В этом делегате `FormatterResolvers` свойство можно использовать для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="082f5-275">Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="082f5-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="082f5-276">Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="082f5-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="082f5-277">Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления.</span><span class="sxs-lookup"><span data-stu-id="082f5-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="082f5-278">Например, присвоение `MessagePackSecurity.Active` статическому свойству значения `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="082f5-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="082f5-279">Для установки параметра `MessagePackSecurity.Active` требуется ручная установка [версии 1,9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="082f5-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="082f5-280">При установке `MessagePack` обновлений 1,9. x :::no-loc(SignalR)::: используется версия.</span><span class="sxs-lookup"><span data-stu-id="082f5-280">Installing `MessagePack` 1.9.x upgrades the version :::no-loc(SignalR)::: uses.</span></span> <span data-ttu-id="082f5-281">Если параметр `MessagePackSecurity.Active` не имеет значение `MessagePackSecurity.UntrustedData` , вредоносный клиент может вызвать отказ в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="082f5-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="082f5-282">Задайте `MessagePackSecurity.Active` в `Program.Main` , как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="082f5-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="082f5-283">Настройка MessagePack на клиенте</span><span class="sxs-lookup"><span data-stu-id="082f5-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="082f5-284">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="082f5-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="082f5-285">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="082f5-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="082f5-286">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="082f5-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="082f5-287">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="082f5-287">.NET client</span></span>

<span data-ttu-id="082f5-288">Чтобы включить MessagePack в клиенте .NET, установите `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` пакет и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="082f5-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="082f5-289">Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.</span><span class="sxs-lookup"><span data-stu-id="082f5-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="082f5-290">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="082f5-290">JavaScript client</span></span>

<span data-ttu-id="082f5-291">Поддержка MessagePack для клиента JavaScript предоставляется [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) пакетом NPM.</span><span class="sxs-lookup"><span data-stu-id="082f5-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="082f5-292">Установите пакет, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="082f5-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="082f5-293">После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:</span><span class="sxs-lookup"><span data-stu-id="082f5-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="082f5-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="082f5-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="082f5-295">В браузере `msgpack5` также должна быть ссылка на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="082f5-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="082f5-296">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="082f5-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="082f5-297">Библиотеку можно найти по адресу *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="082f5-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="082f5-298">При использовании `<script>` элемента важен порядок.</span><span class="sxs-lookup"><span data-stu-id="082f5-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="082f5-299">Если на *signalr-protocol-msgpack.js* имеется ссылка до *msgpack5.js* , то при попытке соединения с MessagePack возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="082f5-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="082f5-300">*signalr.js* также требуется перед *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="082f5-300">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="082f5-301">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.</span><span class="sxs-lookup"><span data-stu-id="082f5-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="082f5-302">В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="082f5-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="082f5-303">Особенности MessagePack</span><span class="sxs-lookup"><span data-stu-id="082f5-303">MessagePack quirks</span></span>

<span data-ttu-id="082f5-304">Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="082f5-305">MessagePack учитывает регистр</span><span class="sxs-lookup"><span data-stu-id="082f5-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="082f5-306">В протоколе MessagePack учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="082f5-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="082f5-307">Например, рассмотрим следующий класс C#:</span><span class="sxs-lookup"><span data-stu-id="082f5-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="082f5-308">При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#.</span><span class="sxs-lookup"><span data-stu-id="082f5-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="082f5-309">Пример:</span><span class="sxs-lookup"><span data-stu-id="082f5-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="082f5-310">Использование `camelCased` имен не будет правильно привязано к классу C#.</span><span class="sxs-lookup"><span data-stu-id="082f5-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="082f5-311">Это можно обойти, используя атрибут, `Key` чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="082f5-312">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="082f5-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="082f5-313">DateTime. Kind не сохраняется при сериализации или десериализации</span><span class="sxs-lookup"><span data-stu-id="082f5-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="082f5-314">Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="082f5-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="082f5-315">В результате при десериализации даты протокол концентратора MessagePack предполагает, что входящая Дата находится в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="082f5-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="082f5-316">Если вы работаете со `DateTime` значениями в местном времени, перед отправкой рекомендуется преобразовать в формат UTC.</span><span class="sxs-lookup"><span data-stu-id="082f5-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="082f5-317">Преобразуйте их из времени в формате UTC в местное время при их получении.</span><span class="sxs-lookup"><span data-stu-id="082f5-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="082f5-318">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ :::no-loc(SignalR)::: #2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="082f5-318">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="082f5-319">DateTime. MinValue не поддерживается в MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="082f5-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="082f5-320">Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая :::no-loc(SignalR)::: клиентом JavaScript, не поддерживает `timestamp96` тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="082f5-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="082f5-321">Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="082f5-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="082f5-322">Значение, `DateTime.MinValue` `January 1, 0001` которое должно быть закодировано в `timestamp96` значении.</span><span class="sxs-lookup"><span data-stu-id="082f5-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="082f5-323">По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="082f5-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="082f5-324">При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="082f5-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="082f5-325">Обычно `DateTime.MinValue` используется для кодирования «отсутствующего» или `null` значения.</span><span class="sxs-lookup"><span data-stu-id="082f5-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="082f5-326">Если необходимо закодировать это значение в MessagePack, используйте `DateTime` значение null ( `DateTime?` ) или закодировать отдельное `bool` значение, указывающее, существует ли дата.</span><span class="sxs-lookup"><span data-stu-id="082f5-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="082f5-327">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="082f5-327">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="082f5-328">Поддержка MessagePack в среде предварительной компиляции "до времени"</span><span class="sxs-lookup"><span data-stu-id="082f5-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="082f5-329">Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="082f5-330">Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="082f5-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="082f5-331">В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="082f5-332">Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="082f5-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="082f5-333">После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="082f5-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="082f5-334">Проверки типов более строго в MessagePack</span><span class="sxs-lookup"><span data-stu-id="082f5-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="082f5-335">Протокол JSON-концентратора выполнит преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="082f5-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="082f5-336">Например, если входящий объект имеет значение свойства, которое является числом (), `{ foo: 42 }` но свойство класса .NET имеет тип `string` , значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="082f5-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="082f5-337">Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="082f5-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="082f5-338">Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="082f5-338">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="082f5-339">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="082f5-339">Related resources</span></span>

* [<span data-ttu-id="082f5-340">Начало работы</span><span class="sxs-lookup"><span data-stu-id="082f5-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="082f5-341">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="082f5-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="082f5-342">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="082f5-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
