---
title: Используйте протокол MessagePack Концентратор для SignalR ASP.NET Core
author: bradygaster
description: Добавить протокол MessagePack SignalRконцентратору ASP.NET core .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277240"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="90c96-103">Используйте протокол MessagePack Концентратор для SignalR ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="90c96-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="90c96-104">Эта статья предполагает, что читатель знаком с темами, охватываемыми в [Get Started](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="90c96-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="90c96-105">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="90c96-105">What is MessagePack?</span></span>

<span data-ttu-id="90c96-106">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат бинарной сериализации.</span><span class="sxs-lookup"><span data-stu-id="90c96-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="90c96-107">Это полезно, когда производительность и пропускная способность являются проблемой, поскольку он создает меньше сообщений по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="90c96-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="90c96-108">Двоичные сообщения нечитаемы при взгляде на сетевые следы и журналы, если байты не проходят через parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="90c96-109">имеет встроенную поддержку формата MessagePack и предоставляет AI для клиента и сервера для использования.</span><span class="sxs-lookup"><span data-stu-id="90c96-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="90c96-110">Настройка Пакета сообщений на сервере</span><span class="sxs-lookup"><span data-stu-id="90c96-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="90c96-111">Чтобы включить протокол MessagePack Hub на `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` сервере, установите пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="90c96-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="90c96-112">В `Startup.ConfigureServices` методе `AddMessagePackProtocol` добавьте `AddSignalR` к вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="90c96-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="90c96-113">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="90c96-113">JSON is enabled by default.</span></span> <span data-ttu-id="90c96-114">Добавление MessagePack позволяет поддерживать как клиентов JSON, так и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="90c96-115">Чтобы настроить форматирование данных MessagePack, `AddMessagePackProtocol` требуется делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="90c96-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="90c96-116">В этом делеге свойство `SerializerOptions` может быть использовано для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="90c96-117">Для получения дополнительной информации о том, как работают решатели, посетите библиотеку MessagePack в [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="90c96-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="90c96-118">Атрибуты могут быть использованы на объектах, которые вы хотите сериализовать, чтобы определить, как они должны быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="90c96-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="90c96-119">Мы настоятельно рекомендуем пересмотреть [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить рекомендуемые патчи.</span><span class="sxs-lookup"><span data-stu-id="90c96-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="90c96-120">Например, `.WithSecurity(MessagePackSecurity.UntrustedData)` вызов при `SerializerOptions`замене .</span><span class="sxs-lookup"><span data-stu-id="90c96-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="90c96-121">Настройка Пакета сообщений на клиента</span><span class="sxs-lookup"><span data-stu-id="90c96-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="90c96-122">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="90c96-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="90c96-123">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="90c96-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="90c96-124">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="90c96-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="90c96-125">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="90c96-125">.NET client</span></span>

<span data-ttu-id="90c96-126">Чтобы включить MessagePack в клиенте `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`установите пакет и позвоните по телефону.</span><span class="sxs-lookup"><span data-stu-id="90c96-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="90c96-127">Этот `AddMessagePackProtocol` вызов требует делегата для настройки параметров так же, как сервер.</span><span class="sxs-lookup"><span data-stu-id="90c96-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="90c96-128">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="90c96-128">JavaScript client</span></span>

<span data-ttu-id="90c96-129">Поддержка клиента MessagePack для клиента JavaScript обеспечивается пакетом [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm.</span><span class="sxs-lookup"><span data-stu-id="90c96-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="90c96-130">Установите пакет, выполняя следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="90c96-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="90c96-131">После установки пакета npm модуль может быть использован непосредственно через модуль JavaScript погрузчик или импортирован в браузер, ссылаясь на следующий файл:</span><span class="sxs-lookup"><span data-stu-id="90c96-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="90c96-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="90c96-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="90c96-133">В браузере `msgpack5` также необходимо ссылаться на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="90c96-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="90c96-134">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="90c96-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="90c96-135">Библиотеку можно найти по адресу *node_modules'msgpack5'dist-msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="90c96-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="90c96-136">При использовании элемента `<script>` важен порядок.</span><span class="sxs-lookup"><span data-stu-id="90c96-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="90c96-137">Если *сигнальный протокол-msgpack.js* ссылается до *msgpack5.js*, ошибка возникает при попытке связаться с MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="90c96-138">*signalr.js* также требуется перед *сигнальщиком-протоколом-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="90c96-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="90c96-139">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` к `HubConnectionBuilder` настроенной клиенту для использования протокола MessagePack при подключении к серверу.</span><span class="sxs-lookup"><span data-stu-id="90c96-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="90c96-140">В настоящее время в клиенте JavaScript нет вариантов конфигурации для протокола MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="90c96-141">Причуды MessagePack</span><span class="sxs-lookup"><span data-stu-id="90c96-141">MessagePack quirks</span></span>

<span data-ttu-id="90c96-142">При использовании протокола MessagePack Hub необходимо узнать несколько проблем.</span><span class="sxs-lookup"><span data-stu-id="90c96-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="90c96-143">MessagePack является чувствительным к случаям</span><span class="sxs-lookup"><span data-stu-id="90c96-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="90c96-144">Протокол MessagePack является чувствительным к случаям.</span><span class="sxs-lookup"><span data-stu-id="90c96-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="90c96-145">Например, рассмотрим следующий класс C:</span><span class="sxs-lookup"><span data-stu-id="90c96-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="90c96-146">При отправке из клиента JavaScript `PascalCased` необходимо использовать имена свойств, так как корпус должен точно соответствовать классу C'.</span><span class="sxs-lookup"><span data-stu-id="90c96-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="90c96-147">Пример:</span><span class="sxs-lookup"><span data-stu-id="90c96-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="90c96-148">Использование `camelCased` имен не будет должным образом связано с классом C.'</span><span class="sxs-lookup"><span data-stu-id="90c96-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="90c96-149">Вы можете обойти это, `Key` используя атрибут, чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="90c96-150">Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="90c96-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="90c96-151">DateTime.Kind не сохраняется при сериализации/десериализации</span><span class="sxs-lookup"><span data-stu-id="90c96-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="90c96-152">Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="90c96-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="90c96-153">В результате, при десериализации даты, протокол MessagePack Концентратор преобразуется в формат UTC, если `DateTime.Kind` в `DateTimeKind.Local` противном случае он не коснется времени и передает его как есть.</span><span class="sxs-lookup"><span data-stu-id="90c96-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="90c96-154">Если вы работаете `DateTime` с значениями, мы рекомендуем перейти в UTC перед отправкой.</span><span class="sxs-lookup"><span data-stu-id="90c96-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="90c96-155">Преобразуйте их из UTC в местное время, когда вы их получите.</span><span class="sxs-lookup"><span data-stu-id="90c96-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="90c96-156">DateTime.MinValue не поддерживается MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="90c96-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="90c96-157">Библиотека [msgpack5,](https://github.com/mcollina/msgpack5) используемая клиентом SignalR JavaScript, `timestamp96` не поддерживает тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="90c96-158">Этот тип используется для кодирования очень больших значений дат (либо в самом начале прошлого, либо очень далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="90c96-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="90c96-159">Значение, `DateTime.MinValue` `January 1, 0001`которое должно быть закодировано в значении. `timestamp96`</span><span class="sxs-lookup"><span data-stu-id="90c96-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="90c96-160">Из-за этого `DateTime.MinValue` отправка клиенту JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="90c96-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="90c96-161">Когда `DateTime.MinValue` клиент JavaScript получает:</span><span class="sxs-lookup"><span data-stu-id="90c96-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="90c96-162">Как `DateTime.MinValue` правило, используется для кодирования `null` "пропавших без вести" или значение.</span><span class="sxs-lookup"><span data-stu-id="90c96-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="90c96-163">Если вам нужно кодировать это значение в MessagePack, используйте необоснованное `DateTime` значение ()`DateTime?`или закодируйте отдельное `bool` значение с указанием наличия даты.</span><span class="sxs-lookup"><span data-stu-id="90c96-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="90c96-164">Для получения дополнительной информации об этом ограничении, см. GitHub вопрос [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="90c96-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="90c96-165">Поддержка MessagePack в среде компиляции «досрочное время»</span><span class="sxs-lookup"><span data-stu-id="90c96-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="90c96-166">Библиотека [MessagePack-CSharp,](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) используемая клиентом и сервером .NET, использует генерацию кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="90c96-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="90c96-167">В результате он не поддерживается по умолчанию для сред, которые используют компиляцию «досрочного времени» (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="90c96-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="90c96-168">В этих средах можно использовать MessagePack, «предварительно генерируя» код serializer/deserializer.</span><span class="sxs-lookup"><span data-stu-id="90c96-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="90c96-169">Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="90c96-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="90c96-170">После того как вы предварительно сгенерировали сериализаторы, вы `AddMessagePackProtocol`можете зарегистрировать их с помощью делегата конфигурации, переданного:</span><span class="sxs-lookup"><span data-stu-id="90c96-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="90c96-171">Проверки типов являются более строгими в MessagePack</span><span class="sxs-lookup"><span data-stu-id="90c96-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="90c96-172">Протокол JSON Hub будет выполнять преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="90c96-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="90c96-173">Например, если входящий объект имеет значение свойства,`{ foo: 42 }`которое является числом (), `string`но свойство на классе .NET имеет тип, значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="90c96-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="90c96-174">Тем не менее, MessagePack не выполняет это преобразование и будет бросать исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="90c96-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="90c96-175">Для получения дополнительной информации об этом ограничении, с [SignalR#2937м.](https://github.com/aspnet/SignalR/issues/2937)</span><span class="sxs-lookup"><span data-stu-id="90c96-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="90c96-176">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="90c96-176">Related resources</span></span>

* [<span data-ttu-id="90c96-177">Начало работы</span><span class="sxs-lookup"><span data-stu-id="90c96-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="90c96-178">клиент .NET</span><span class="sxs-lookup"><span data-stu-id="90c96-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="90c96-179">Клиент на JavaScript</span><span class="sxs-lookup"><span data-stu-id="90c96-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="90c96-180">Эта статья предполагает, что читатель знаком с темами, охватываемыми в [Get Started](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="90c96-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="90c96-181">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="90c96-181">What is MessagePack?</span></span>

<span data-ttu-id="90c96-182">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат бинарной сериализации.</span><span class="sxs-lookup"><span data-stu-id="90c96-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="90c96-183">Это полезно, когда производительность и пропускная способность являются проблемой, поскольку он создает меньше сообщений по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="90c96-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="90c96-184">Двоичные сообщения нечитаемы при взгляде на сетевые следы и журналы, если байты не проходят через parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="90c96-185">имеет встроенную поддержку формата MessagePack и предоставляет AI для клиента и сервера.</span><span class="sxs-lookup"><span data-stu-id="90c96-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="90c96-186">Настройка Пакета сообщений на сервере</span><span class="sxs-lookup"><span data-stu-id="90c96-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="90c96-187">Чтобы включить протокол MessagePack Hub на `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` сервере, установите пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="90c96-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="90c96-188">В `Startup.ConfigureServices` методе `AddMessagePackProtocol` добавьте `AddSignalR` к вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="90c96-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="90c96-189">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="90c96-189">JSON is enabled by default.</span></span> <span data-ttu-id="90c96-190">Добавление MessagePack позволяет поддерживать как клиентов JSON, так и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="90c96-191">Чтобы настроить форматирование данных MessagePack, `AddMessagePackProtocol` требуется делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="90c96-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="90c96-192">В этом делеге свойство `FormatterResolvers` может быть использовано для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="90c96-193">Для получения дополнительной информации о том, как работают решатели, посетите библиотеку MessagePack в [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="90c96-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="90c96-194">Атрибуты могут быть использованы на объектах, которые вы хотите сериализовать, чтобы определить, как они должны быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="90c96-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="90c96-195">Мы настоятельно рекомендуем пересмотреть [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить рекомендуемые патчи.</span><span class="sxs-lookup"><span data-stu-id="90c96-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="90c96-196">Например, установка `MessagePackSecurity.Active` статического `MessagePackSecurity.UntrustedData`свойства для .</span><span class="sxs-lookup"><span data-stu-id="90c96-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="90c96-197">Установка `MessagePackSecurity.Active` требует ручной установки [1.9.x версии MessagePack.](https://www.nuget.org/packages/MessagePack/1.9.3)</span><span class="sxs-lookup"><span data-stu-id="90c96-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="90c96-198">Установка `MessagePack` 1.9.x обновляет версию. SignalR</span><span class="sxs-lookup"><span data-stu-id="90c96-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="90c96-199">Когда `MessagePackSecurity.Active` не `MessagePackSecurity.UntrustedData`установлен, злонамеренный клиент может привести к отказу в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="90c96-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="90c96-200">`MessagePackSecurity.Active` Установить `Program.Main`в , как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="90c96-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="90c96-201">Настройка Пакета сообщений на клиента</span><span class="sxs-lookup"><span data-stu-id="90c96-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="90c96-202">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="90c96-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="90c96-203">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="90c96-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="90c96-204">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="90c96-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="90c96-205">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="90c96-205">.NET client</span></span>

<span data-ttu-id="90c96-206">Чтобы включить MessagePack в клиенте `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`установите пакет и позвоните по телефону.</span><span class="sxs-lookup"><span data-stu-id="90c96-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="90c96-207">Этот `AddMessagePackProtocol` вызов требует делегата для настройки параметров так же, как сервер.</span><span class="sxs-lookup"><span data-stu-id="90c96-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="90c96-208">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="90c96-208">JavaScript client</span></span>

<span data-ttu-id="90c96-209">Поддержка клиента MessagePack для клиента JavaScript обеспечивается пакетом [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm.</span><span class="sxs-lookup"><span data-stu-id="90c96-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="90c96-210">Установите пакет, выполняя следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="90c96-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="90c96-211">После установки пакета npm модуль может быть использован непосредственно через модуль JavaScript погрузчик или импортирован в браузер, ссылаясь на следующий файл:</span><span class="sxs-lookup"><span data-stu-id="90c96-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="90c96-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="90c96-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="90c96-213">В браузере `msgpack5` также необходимо ссылаться на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="90c96-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="90c96-214">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="90c96-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="90c96-215">Библиотеку можно найти по адресу *node_modules'msgpack5'dist-msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="90c96-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="90c96-216">При использовании элемента `<script>` важен порядок.</span><span class="sxs-lookup"><span data-stu-id="90c96-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="90c96-217">Если *сигнальный протокол-msgpack.js* ссылается до *msgpack5.js*, ошибка возникает при попытке связаться с MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="90c96-218">*signalr.js* также требуется перед *сигнальщиком-протоколом-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="90c96-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="90c96-219">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` к `HubConnectionBuilder` настроенной клиенту для использования протокола MessagePack при подключении к серверу.</span><span class="sxs-lookup"><span data-stu-id="90c96-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="90c96-220">В настоящее время в клиенте JavaScript нет вариантов конфигурации для протокола MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="90c96-221">Причуды MessagePack</span><span class="sxs-lookup"><span data-stu-id="90c96-221">MessagePack quirks</span></span>

<span data-ttu-id="90c96-222">При использовании протокола MessagePack Hub необходимо узнать несколько проблем.</span><span class="sxs-lookup"><span data-stu-id="90c96-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="90c96-223">MessagePack является чувствительным к случаям</span><span class="sxs-lookup"><span data-stu-id="90c96-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="90c96-224">Протокол MessagePack является чувствительным к случаям.</span><span class="sxs-lookup"><span data-stu-id="90c96-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="90c96-225">Например, рассмотрим следующий класс C:</span><span class="sxs-lookup"><span data-stu-id="90c96-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="90c96-226">При отправке из клиента JavaScript `PascalCased` необходимо использовать имена свойств, так как корпус должен точно соответствовать классу C'.</span><span class="sxs-lookup"><span data-stu-id="90c96-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="90c96-227">Пример:</span><span class="sxs-lookup"><span data-stu-id="90c96-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="90c96-228">Использование `camelCased` имен не будет должным образом связано с классом C.'</span><span class="sxs-lookup"><span data-stu-id="90c96-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="90c96-229">Вы можете обойти это, `Key` используя атрибут, чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="90c96-230">Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="90c96-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="90c96-231">DateTime.Kind не сохраняется при сериализации/десериализации</span><span class="sxs-lookup"><span data-stu-id="90c96-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="90c96-232">Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="90c96-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="90c96-233">В результате при десериализации даты протокол MessagePack Hub предполагает, что входящие даты указаны в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="90c96-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="90c96-234">Если вы работаете `DateTime` с значениями в местное время, мы рекомендуем перейти в UTC перед отправкой.</span><span class="sxs-lookup"><span data-stu-id="90c96-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="90c96-235">Преобразуйте их из UTC в местное время, когда вы их получите.</span><span class="sxs-lookup"><span data-stu-id="90c96-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="90c96-236">Для получения дополнительной информации об этом ограничении, с [SignalR#2632м.](https://github.com/aspnet/SignalR/issues/2632)</span><span class="sxs-lookup"><span data-stu-id="90c96-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="90c96-237">DateTime.MinValue не поддерживается MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="90c96-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="90c96-238">Библиотека [msgpack5,](https://github.com/mcollina/msgpack5) используемая клиентом SignalR JavaScript, `timestamp96` не поддерживает тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="90c96-239">Этот тип используется для кодирования очень больших значений дат (либо в самом начале прошлого, либо очень далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="90c96-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="90c96-240">Значение, `DateTime.MinValue` `January 1, 0001`которое должно быть закодировано в значении. `timestamp96`</span><span class="sxs-lookup"><span data-stu-id="90c96-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="90c96-241">Из-за этого `DateTime.MinValue` отправка клиенту JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="90c96-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="90c96-242">Когда `DateTime.MinValue` клиент JavaScript получает:</span><span class="sxs-lookup"><span data-stu-id="90c96-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="90c96-243">Как `DateTime.MinValue` правило, используется для кодирования `null` "пропавших без вести" или значение.</span><span class="sxs-lookup"><span data-stu-id="90c96-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="90c96-244">Если вам нужно кодировать это значение в MessagePack, используйте необоснованное `DateTime` значение ()`DateTime?`или закодируйте отдельное `bool` значение с указанием наличия даты.</span><span class="sxs-lookup"><span data-stu-id="90c96-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="90c96-245">Для получения дополнительной информации об этом ограничении, см. GitHub вопрос [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="90c96-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="90c96-246">Поддержка MessagePack в среде компиляции «досрочное время»</span><span class="sxs-lookup"><span data-stu-id="90c96-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="90c96-247">Библиотека [MessagePack-CSharp,](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) используемая клиентом и сервером .NET, использует генерацию кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="90c96-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="90c96-248">В результате он не поддерживается по умолчанию для сред, которые используют компиляцию «досрочного времени» (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="90c96-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="90c96-249">В этих средах можно использовать MessagePack, «предварительно генерируя» код serializer/deserializer.</span><span class="sxs-lookup"><span data-stu-id="90c96-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="90c96-250">Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="90c96-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="90c96-251">После того как вы предварительно сгенерировали сериализаторы, вы `AddMessagePackProtocol`можете зарегистрировать их с помощью делегата конфигурации, переданного:</span><span class="sxs-lookup"><span data-stu-id="90c96-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="90c96-252">Проверки типов являются более строгими в MessagePack</span><span class="sxs-lookup"><span data-stu-id="90c96-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="90c96-253">Протокол JSON Hub будет выполнять преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="90c96-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="90c96-254">Например, если входящий объект имеет значение свойства,`{ foo: 42 }`которое является числом (), `string`но свойство на классе .NET имеет тип, значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="90c96-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="90c96-255">Тем не менее, MessagePack не выполняет это преобразование и будет бросать исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="90c96-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="90c96-256">Для получения дополнительной информации об этом ограничении, с [SignalR#2937м.](https://github.com/aspnet/SignalR/issues/2937)</span><span class="sxs-lookup"><span data-stu-id="90c96-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="90c96-257">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="90c96-257">Related resources</span></span>

* [<span data-ttu-id="90c96-258">Начало работы</span><span class="sxs-lookup"><span data-stu-id="90c96-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="90c96-259">клиент .NET</span><span class="sxs-lookup"><span data-stu-id="90c96-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="90c96-260">Клиент на JavaScript</span><span class="sxs-lookup"><span data-stu-id="90c96-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90c96-261">Эта статья предполагает, что читатель знаком с темами, охватываемыми в [Get Started](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="90c96-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="90c96-262">Что такое MessagePack?</span><span class="sxs-lookup"><span data-stu-id="90c96-262">What is MessagePack?</span></span>

<span data-ttu-id="90c96-263">[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат бинарной сериализации.</span><span class="sxs-lookup"><span data-stu-id="90c96-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="90c96-264">Это полезно, когда производительность и пропускная способность являются проблемой, поскольку он создает меньше сообщений по сравнению с [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="90c96-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="90c96-265">Двоичные сообщения нечитаемы при взгляде на сетевые следы и журналы, если байты не проходят через parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="90c96-266">имеет встроенную поддержку формата MessagePack и предоставляет AI для клиента и сервера.</span><span class="sxs-lookup"><span data-stu-id="90c96-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="90c96-267">Настройка Пакета сообщений на сервере</span><span class="sxs-lookup"><span data-stu-id="90c96-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="90c96-268">Чтобы включить протокол MessagePack Hub на `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` сервере, установите пакет в приложении.</span><span class="sxs-lookup"><span data-stu-id="90c96-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="90c96-269">В `Startup.ConfigureServices` методе `AddMessagePackProtocol` добавьте `AddSignalR` к вызову, чтобы включить поддержку MessagePack на сервере.</span><span class="sxs-lookup"><span data-stu-id="90c96-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="90c96-270">JSON включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="90c96-270">JSON is enabled by default.</span></span> <span data-ttu-id="90c96-271">Добавление MessagePack позволяет поддерживать как клиентов JSON, так и MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="90c96-272">Чтобы настроить форматирование данных MessagePack, `AddMessagePackProtocol` требуется делегат для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="90c96-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="90c96-273">В этом делеге свойство `FormatterResolvers` может быть использовано для настройки параметров сериализации MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="90c96-274">Для получения дополнительной информации о том, как работают решатели, посетите библиотеку MessagePack в [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="90c96-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="90c96-275">Атрибуты могут быть использованы на объектах, которые вы хотите сериализовать, чтобы определить, как они должны быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="90c96-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="90c96-276">Мы настоятельно рекомендуем пересмотреть [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить рекомендуемые патчи.</span><span class="sxs-lookup"><span data-stu-id="90c96-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="90c96-277">Например, установка `MessagePackSecurity.Active` статического `MessagePackSecurity.UntrustedData`свойства для .</span><span class="sxs-lookup"><span data-stu-id="90c96-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="90c96-278">Установка `MessagePackSecurity.Active` требует ручной установки [1.9.x версии MessagePack.](https://www.nuget.org/packages/MessagePack/1.9.3)</span><span class="sxs-lookup"><span data-stu-id="90c96-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="90c96-279">Установка `MessagePack` 1.9.x обновляет версию. SignalR</span><span class="sxs-lookup"><span data-stu-id="90c96-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="90c96-280">Когда `MessagePackSecurity.Active` не `MessagePackSecurity.UntrustedData`установлен, злонамеренный клиент может привести к отказу в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="90c96-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="90c96-281">`MessagePackSecurity.Active` Установить `Program.Main`в , как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="90c96-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="90c96-282">Настройка Пакета сообщений на клиента</span><span class="sxs-lookup"><span data-stu-id="90c96-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="90c96-283">JSON включен по умолчанию для поддерживаемых клиентов.</span><span class="sxs-lookup"><span data-stu-id="90c96-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="90c96-284">Клиенты могут поддерживать только один протокол.</span><span class="sxs-lookup"><span data-stu-id="90c96-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="90c96-285">Добавление поддержки MessagePack заменит все ранее настроенные протоколы.</span><span class="sxs-lookup"><span data-stu-id="90c96-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="90c96-286">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="90c96-286">.NET client</span></span>

<span data-ttu-id="90c96-287">Чтобы включить MessagePack в клиенте `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`установите пакет и позвоните по телефону.</span><span class="sxs-lookup"><span data-stu-id="90c96-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="90c96-288">Этот `AddMessagePackProtocol` вызов требует делегата для настройки параметров так же, как сервер.</span><span class="sxs-lookup"><span data-stu-id="90c96-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="90c96-289">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="90c96-289">JavaScript client</span></span>

<span data-ttu-id="90c96-290">Поддержка клиента MessagePack для клиента JavaScript обеспечивается пакетом [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm.</span><span class="sxs-lookup"><span data-stu-id="90c96-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="90c96-291">Установите пакет, выполняя следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="90c96-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="90c96-292">После установки пакета npm модуль может быть использован непосредственно через модуль JavaScript погрузчик или импортирован в браузер, ссылаясь на следующий файл:</span><span class="sxs-lookup"><span data-stu-id="90c96-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="90c96-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="90c96-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="90c96-294">В браузере `msgpack5` также необходимо ссылаться на библиотеку.</span><span class="sxs-lookup"><span data-stu-id="90c96-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="90c96-295">Используйте `<script>` тег для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="90c96-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="90c96-296">Библиотеку можно найти по адресу *node_modules'msgpack5'dist-msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="90c96-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="90c96-297">При использовании элемента `<script>` важен порядок.</span><span class="sxs-lookup"><span data-stu-id="90c96-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="90c96-298">Если *сигнальный протокол-msgpack.js* ссылается до *msgpack5.js*, ошибка возникает при попытке связаться с MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="90c96-299">*signalr.js* также требуется перед *сигнальщиком-протоколом-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="90c96-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="90c96-300">Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` к `HubConnectionBuilder` настроенной клиенту для использования протокола MessagePack при подключении к серверу.</span><span class="sxs-lookup"><span data-stu-id="90c96-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="90c96-301">В настоящее время в клиенте JavaScript нет вариантов конфигурации для протокола MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="90c96-302">Причуды MessagePack</span><span class="sxs-lookup"><span data-stu-id="90c96-302">MessagePack quirks</span></span>

<span data-ttu-id="90c96-303">При использовании протокола MessagePack Hub необходимо узнать несколько проблем.</span><span class="sxs-lookup"><span data-stu-id="90c96-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="90c96-304">MessagePack является чувствительным к случаям</span><span class="sxs-lookup"><span data-stu-id="90c96-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="90c96-305">Протокол MessagePack является чувствительным к случаям.</span><span class="sxs-lookup"><span data-stu-id="90c96-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="90c96-306">Например, рассмотрим следующий класс C:</span><span class="sxs-lookup"><span data-stu-id="90c96-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="90c96-307">При отправке из клиента JavaScript `PascalCased` необходимо использовать имена свойств, так как корпус должен точно соответствовать классу C'.</span><span class="sxs-lookup"><span data-stu-id="90c96-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="90c96-308">Пример:</span><span class="sxs-lookup"><span data-stu-id="90c96-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="90c96-309">Использование `camelCased` имен не будет должным образом связано с классом C.'</span><span class="sxs-lookup"><span data-stu-id="90c96-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="90c96-310">Вы можете обойти это, `Key` используя атрибут, чтобы указать другое имя для свойства MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="90c96-311">Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="90c96-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="90c96-312">DateTime.Kind не сохраняется при сериализации/десериализации</span><span class="sxs-lookup"><span data-stu-id="90c96-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="90c96-313">Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="90c96-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="90c96-314">В результате при десериализации даты протокол MessagePack Hub предполагает, что входящие даты указаны в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="90c96-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="90c96-315">Если вы работаете `DateTime` с значениями в местное время, мы рекомендуем перейти в UTC перед отправкой.</span><span class="sxs-lookup"><span data-stu-id="90c96-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="90c96-316">Преобразуйте их из UTC в местное время, когда вы их получите.</span><span class="sxs-lookup"><span data-stu-id="90c96-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="90c96-317">Для получения дополнительной информации об этом ограничении, с [SignalR#2632м.](https://github.com/aspnet/SignalR/issues/2632)</span><span class="sxs-lookup"><span data-stu-id="90c96-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="90c96-318">DateTime.MinValue не поддерживается MessagePack в JavaScript</span><span class="sxs-lookup"><span data-stu-id="90c96-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="90c96-319">Библиотека [msgpack5,](https://github.com/mcollina/msgpack5) используемая клиентом SignalR JavaScript, `timestamp96` не поддерживает тип в MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90c96-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="90c96-320">Этот тип используется для кодирования очень больших значений дат (либо в самом начале прошлого, либо очень далеко в будущем).</span><span class="sxs-lookup"><span data-stu-id="90c96-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="90c96-321">Значение, `DateTime.MinValue` `January 1, 0001` которое должно быть закодировано в значении. `timestamp96`</span><span class="sxs-lookup"><span data-stu-id="90c96-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="90c96-322">Из-за этого `DateTime.MinValue` отправка клиенту JavaScript не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="90c96-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="90c96-323">Когда `DateTime.MinValue` клиент JavaScript получает:</span><span class="sxs-lookup"><span data-stu-id="90c96-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="90c96-324">Как `DateTime.MinValue` правило, используется для кодирования `null` "пропавших без вести" или значение.</span><span class="sxs-lookup"><span data-stu-id="90c96-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="90c96-325">Если вам нужно кодировать это значение в MessagePack, используйте необоснованное `DateTime` значение ()`DateTime?`или закодируйте отдельное `bool` значение с указанием наличия даты.</span><span class="sxs-lookup"><span data-stu-id="90c96-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="90c96-326">Для получения дополнительной информации об этом ограничении, см. GitHub вопрос [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="90c96-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="90c96-327">Поддержка MessagePack в среде компиляции «досрочное время»</span><span class="sxs-lookup"><span data-stu-id="90c96-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="90c96-328">Библиотека [MessagePack-CSharp,](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) используемая клиентом и сервером .NET, использует генерацию кода для оптимизации сериализации.</span><span class="sxs-lookup"><span data-stu-id="90c96-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="90c96-329">В результате он не поддерживается по умолчанию для сред, которые используют компиляцию «досрочного времени» (например, Xamarin iOS или Unity).</span><span class="sxs-lookup"><span data-stu-id="90c96-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="90c96-330">В этих средах можно использовать MessagePack, «предварительно генерируя» код serializer/deserializer.</span><span class="sxs-lookup"><span data-stu-id="90c96-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="90c96-331">Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="90c96-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="90c96-332">После того как вы предварительно сгенерировали сериализаторы, вы `AddMessagePackProtocol`можете зарегистрировать их с помощью делегата конфигурации, переданного:</span><span class="sxs-lookup"><span data-stu-id="90c96-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="90c96-333">Проверки типов являются более строгими в MessagePack</span><span class="sxs-lookup"><span data-stu-id="90c96-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="90c96-334">Протокол JSON Hub будет выполнять преобразования типов во время десериализации.</span><span class="sxs-lookup"><span data-stu-id="90c96-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="90c96-335">Например, если входящий объект имеет значение свойства,`{ foo: 42 }`которое является числом (), `string`но свойство на классе .NET имеет тип, значение будет преобразовано.</span><span class="sxs-lookup"><span data-stu-id="90c96-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="90c96-336">Тем не менее, MessagePack не выполняет это преобразование и будет бросать исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):</span><span class="sxs-lookup"><span data-stu-id="90c96-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="90c96-337">Для получения дополнительной информации об этом ограничении, с [SignalR#2937м.](https://github.com/aspnet/SignalR/issues/2937)</span><span class="sxs-lookup"><span data-stu-id="90c96-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="90c96-338">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="90c96-338">Related resources</span></span>

* [<span data-ttu-id="90c96-339">Начало работы</span><span class="sxs-lookup"><span data-stu-id="90c96-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="90c96-340">клиент .NET</span><span class="sxs-lookup"><span data-stu-id="90c96-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="90c96-341">Клиент на JavaScript</span><span class="sxs-lookup"><span data-stu-id="90c96-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
