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
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>Используйте протокол MessagePack Концентратор для SignalR ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

Эта статья предполагает, что читатель знаком с темами, охватываемыми в [Get Started](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Что такое MessagePack?

[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат бинарной сериализации. Это полезно, когда производительность и пропускная способность являются проблемой, поскольку он создает меньше сообщений по сравнению с [JSON](https://www.json.org/). Двоичные сообщения нечитаемы при взгляде на сетевые следы и журналы, если байты не проходят через parser MessagePack. SignalRимеет встроенную поддержку формата MessagePack и предоставляет AI для клиента и сервера для использования.

## <a name="configure-messagepack-on-the-server"></a>Настройка Пакета сообщений на сервере

Чтобы включить протокол MessagePack Hub на `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` сервере, установите пакет в приложении. В `Startup.ConfigureServices` методе `AddMessagePackProtocol` добавьте `AddSignalR` к вызову, чтобы включить поддержку MessagePack на сервере.

> [!NOTE]
> JSON включен по умолчанию. Добавление MessagePack позволяет поддерживать как клиентов JSON, так и MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Чтобы настроить форматирование данных MessagePack, `AddMessagePackProtocol` требуется делегат для настройки параметров. В этом делеге свойство `SerializerOptions` может быть использовано для настройки параметров сериализации MessagePack. Для получения дополнительной информации о том, как работают решатели, посетите библиотеку MessagePack в [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Атрибуты могут быть использованы на объектах, которые вы хотите сериализовать, чтобы определить, как они должны быть обработаны.

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
> Мы настоятельно рекомендуем пересмотреть [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить рекомендуемые патчи. Например, `.WithSecurity(MessagePackSecurity.UntrustedData)` вызов при `SerializerOptions`замене .

## <a name="configure-messagepack-on-the-client"></a>Настройка Пакета сообщений на клиента

> [!NOTE]
> JSON включен по умолчанию для поддерживаемых клиентов. Клиенты могут поддерживать только один протокол. Добавление поддержки MessagePack заменит все ранее настроенные протоколы.

### <a name="net-client"></a>Клиент .NET

Чтобы включить MessagePack в клиенте `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`установите пакет и позвоните по телефону.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Этот `AddMessagePackProtocol` вызов требует делегата для настройки параметров так же, как сервер.

### <a name="javascript-client"></a>Клиент JavaScript

Поддержка клиента MessagePack для клиента JavaScript обеспечивается пакетом [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm. Установите пакет, выполняя следующую команду в командной оболочке:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

После установки пакета npm модуль может быть использован непосредственно через модуль JavaScript погрузчик или импортирован в браузер, ссылаясь на следующий файл:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

В браузере `msgpack5` также необходимо ссылаться на библиотеку. Используйте `<script>` тег для создания ссылки. Библиотеку можно найти по адресу *node_modules'msgpack5'dist-msgpack5.js*.

> [!NOTE]
> При использовании элемента `<script>` важен порядок. Если *сигнальный протокол-msgpack.js* ссылается до *msgpack5.js*, ошибка возникает при попытке связаться с MessagePack. *signalr.js* также требуется перед *сигнальщиком-протоколом-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` к `HubConnectionBuilder` настроенной клиенту для использования протокола MessagePack при подключении к серверу.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> В настоящее время в клиенте JavaScript нет вариантов конфигурации для протокола MessagePack.

## <a name="messagepack-quirks"></a>Причуды MessagePack

При использовании протокола MessagePack Hub необходимо узнать несколько проблем.

### <a name="messagepack-is-case-sensitive"></a>MessagePack является чувствительным к случаям

Протокол MessagePack является чувствительным к случаям. Например, рассмотрим следующий класс C:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

При отправке из клиента JavaScript `PascalCased` необходимо использовать имена свойств, так как корпус должен точно соответствовать классу C'. Пример:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Использование `camelCased` имен не будет должным образом связано с классом C.' Вы можете обойти это, `Key` используя атрибут, чтобы указать другое имя для свойства MessagePack. Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind не сохраняется при сериализации/десериализации

Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime`. В результате, при десериализации даты, протокол MessagePack Концентратор преобразуется в формат UTC, если `DateTime.Kind` в `DateTimeKind.Local` противном случае он не коснется времени и передает его как есть. Если вы работаете `DateTime` с значениями, мы рекомендуем перейти в UTC перед отправкой. Преобразуйте их из UTC в местное время, когда вы их получите.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue не поддерживается MessagePack в JavaScript

Библиотека [msgpack5,](https://github.com/mcollina/msgpack5) используемая клиентом SignalR JavaScript, `timestamp96` не поддерживает тип в MessagePack. Этот тип используется для кодирования очень больших значений дат (либо в самом начале прошлого, либо очень далеко в будущем). Значение, `DateTime.MinValue` `January 1, 0001`которое должно быть закодировано в значении. `timestamp96` Из-за этого `DateTime.MinValue` отправка клиенту JavaScript не поддерживается. Когда `DateTime.MinValue` клиент JavaScript получает:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Как `DateTime.MinValue` правило, используется для кодирования `null` "пропавших без вести" или значение. Если вам нужно кодировать это значение в MessagePack, используйте необоснованное `DateTime` значение ()`DateTime?`или закодируйте отдельное `bool` значение с указанием наличия даты.

Для получения дополнительной информации об этом ограничении, см. GitHub вопрос [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Поддержка MessagePack в среде компиляции «досрочное время»

Библиотека [MessagePack-CSharp,](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) используемая клиентом и сервером .NET, использует генерацию кода для оптимизации сериализации. В результате он не поддерживается по умолчанию для сред, которые используют компиляцию «досрочного времени» (например, Xamarin iOS или Unity). В этих средах можно использовать MessagePack, «предварительно генерируя» код serializer/deserializer. Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). После того как вы предварительно сгенерировали сериализаторы, вы `AddMessagePackProtocol`можете зарегистрировать их с помощью делегата конфигурации, переданного:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Проверки типов являются более строгими в MessagePack

Протокол JSON Hub будет выполнять преобразования типов во время десериализации. Например, если входящий объект имеет значение свойства,`{ foo: 42 }`которое является числом (), `string`но свойство на классе .NET имеет тип, значение будет преобразовано. Тем не менее, MessagePack не выполняет это преобразование и будет бросать исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Для получения дополнительной информации об этом ограничении, с [SignalR#2937м.](https://github.com/aspnet/SignalR/issues/2937)

## <a name="related-resources"></a>Связанные ресурсы

* [Начало работы](xref:tutorials/signalr)
* [клиент .NET](xref:signalr/dotnet-client)
* [Клиент на JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Эта статья предполагает, что читатель знаком с темами, охватываемыми в [Get Started](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Что такое MessagePack?

[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат бинарной сериализации. Это полезно, когда производительность и пропускная способность являются проблемой, поскольку он создает меньше сообщений по сравнению с [JSON](https://www.json.org/). Двоичные сообщения нечитаемы при взгляде на сетевые следы и журналы, если байты не проходят через parser MessagePack. SignalRимеет встроенную поддержку формата MessagePack и предоставляет AI для клиента и сервера.

## <a name="configure-messagepack-on-the-server"></a>Настройка Пакета сообщений на сервере

Чтобы включить протокол MessagePack Hub на `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` сервере, установите пакет в приложении. В `Startup.ConfigureServices` методе `AddMessagePackProtocol` добавьте `AddSignalR` к вызову, чтобы включить поддержку MessagePack на сервере.

> [!NOTE]
> JSON включен по умолчанию. Добавление MessagePack позволяет поддерживать как клиентов JSON, так и MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Чтобы настроить форматирование данных MessagePack, `AddMessagePackProtocol` требуется делегат для настройки параметров. В этом делеге свойство `FormatterResolvers` может быть использовано для настройки параметров сериализации MessagePack. Для получения дополнительной информации о том, как работают решатели, посетите библиотеку MessagePack в [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Атрибуты могут быть использованы на объектах, которые вы хотите сериализовать, чтобы определить, как они должны быть обработаны.

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
> Мы настоятельно рекомендуем пересмотреть [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить рекомендуемые патчи. Например, установка `MessagePackSecurity.Active` статического `MessagePackSecurity.UntrustedData`свойства для . Установка `MessagePackSecurity.Active` требует ручной установки [1.9.x версии MessagePack.](https://www.nuget.org/packages/MessagePack/1.9.3) Установка `MessagePack` 1.9.x обновляет версию. SignalR Когда `MessagePackSecurity.Active` не `MessagePackSecurity.UntrustedData`установлен, злонамеренный клиент может привести к отказу в обслуживании. `MessagePackSecurity.Active` Установить `Program.Main`в , как показано в следующем коде:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Настройка Пакета сообщений на клиента

> [!NOTE]
> JSON включен по умолчанию для поддерживаемых клиентов. Клиенты могут поддерживать только один протокол. Добавление поддержки MessagePack заменит все ранее настроенные протоколы.

### <a name="net-client"></a>Клиент .NET

Чтобы включить MessagePack в клиенте `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`установите пакет и позвоните по телефону.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Этот `AddMessagePackProtocol` вызов требует делегата для настройки параметров так же, как сервер.

### <a name="javascript-client"></a>Клиент JavaScript

Поддержка клиента MessagePack для клиента JavaScript обеспечивается пакетом [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm. Установите пакет, выполняя следующую команду в командной оболочке:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

После установки пакета npm модуль может быть использован непосредственно через модуль JavaScript погрузчик или импортирован в браузер, ссылаясь на следующий файл:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

В браузере `msgpack5` также необходимо ссылаться на библиотеку. Используйте `<script>` тег для создания ссылки. Библиотеку можно найти по адресу *node_modules'msgpack5'dist-msgpack5.js*.

> [!NOTE]
> При использовании элемента `<script>` важен порядок. Если *сигнальный протокол-msgpack.js* ссылается до *msgpack5.js*, ошибка возникает при попытке связаться с MessagePack. *signalr.js* также требуется перед *сигнальщиком-протоколом-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` к `HubConnectionBuilder` настроенной клиенту для использования протокола MessagePack при подключении к серверу.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> В настоящее время в клиенте JavaScript нет вариантов конфигурации для протокола MessagePack.

## <a name="messagepack-quirks"></a>Причуды MessagePack

При использовании протокола MessagePack Hub необходимо узнать несколько проблем.

### <a name="messagepack-is-case-sensitive"></a>MessagePack является чувствительным к случаям

Протокол MessagePack является чувствительным к случаям. Например, рассмотрим следующий класс C:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

При отправке из клиента JavaScript `PascalCased` необходимо использовать имена свойств, так как корпус должен точно соответствовать классу C'. Пример:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Использование `camelCased` имен не будет должным образом связано с классом C.' Вы можете обойти это, `Key` используя атрибут, чтобы указать другое имя для свойства MessagePack. Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind не сохраняется при сериализации/десериализации

Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime`. В результате при десериализации даты протокол MessagePack Hub предполагает, что входящие даты указаны в формате UTC. Если вы работаете `DateTime` с значениями в местное время, мы рекомендуем перейти в UTC перед отправкой. Преобразуйте их из UTC в местное время, когда вы их получите.

Для получения дополнительной информации об этом ограничении, с [SignalR#2632м.](https://github.com/aspnet/SignalR/issues/2632)

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue не поддерживается MessagePack в JavaScript

Библиотека [msgpack5,](https://github.com/mcollina/msgpack5) используемая клиентом SignalR JavaScript, `timestamp96` не поддерживает тип в MessagePack. Этот тип используется для кодирования очень больших значений дат (либо в самом начале прошлого, либо очень далеко в будущем). Значение, `DateTime.MinValue` `January 1, 0001`которое должно быть закодировано в значении. `timestamp96` Из-за этого `DateTime.MinValue` отправка клиенту JavaScript не поддерживается. Когда `DateTime.MinValue` клиент JavaScript получает:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Как `DateTime.MinValue` правило, используется для кодирования `null` "пропавших без вести" или значение. Если вам нужно кодировать это значение в MessagePack, используйте необоснованное `DateTime` значение ()`DateTime?`или закодируйте отдельное `bool` значение с указанием наличия даты.

Для получения дополнительной информации об этом ограничении, см. GitHub вопрос [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Поддержка MessagePack в среде компиляции «досрочное время»

Библиотека [MessagePack-CSharp,](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) используемая клиентом и сервером .NET, использует генерацию кода для оптимизации сериализации. В результате он не поддерживается по умолчанию для сред, которые используют компиляцию «досрочного времени» (например, Xamarin iOS или Unity). В этих средах можно использовать MessagePack, «предварительно генерируя» код serializer/deserializer. Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). После того как вы предварительно сгенерировали сериализаторы, вы `AddMessagePackProtocol`можете зарегистрировать их с помощью делегата конфигурации, переданного:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Проверки типов являются более строгими в MessagePack

Протокол JSON Hub будет выполнять преобразования типов во время десериализации. Например, если входящий объект имеет значение свойства,`{ foo: 42 }`которое является числом (), `string`но свойство на классе .NET имеет тип, значение будет преобразовано. Тем не менее, MessagePack не выполняет это преобразование и будет бросать исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Для получения дополнительной информации об этом ограничении, с [SignalR#2937м.](https://github.com/aspnet/SignalR/issues/2937)

## <a name="related-resources"></a>Связанные ресурсы

* [Начало работы](xref:tutorials/signalr)
* [клиент .NET](xref:signalr/dotnet-client)
* [Клиент на JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Эта статья предполагает, что читатель знаком с темами, охватываемыми в [Get Started](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Что такое MessagePack?

[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат бинарной сериализации. Это полезно, когда производительность и пропускная способность являются проблемой, поскольку он создает меньше сообщений по сравнению с [JSON](https://www.json.org/). Двоичные сообщения нечитаемы при взгляде на сетевые следы и журналы, если байты не проходят через parser MessagePack. SignalRимеет встроенную поддержку формата MessagePack и предоставляет AI для клиента и сервера.

## <a name="configure-messagepack-on-the-server"></a>Настройка Пакета сообщений на сервере

Чтобы включить протокол MessagePack Hub на `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` сервере, установите пакет в приложении. В `Startup.ConfigureServices` методе `AddMessagePackProtocol` добавьте `AddSignalR` к вызову, чтобы включить поддержку MessagePack на сервере.

> [!NOTE]
> JSON включен по умолчанию. Добавление MessagePack позволяет поддерживать как клиентов JSON, так и MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Чтобы настроить форматирование данных MessagePack, `AddMessagePackProtocol` требуется делегат для настройки параметров. В этом делеге свойство `FormatterResolvers` может быть использовано для настройки параметров сериализации MessagePack. Для получения дополнительной информации о том, как работают решатели, посетите библиотеку MessagePack в [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Атрибуты могут быть использованы на объектах, которые вы хотите сериализовать, чтобы определить, как они должны быть обработаны.

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
> Мы настоятельно рекомендуем пересмотреть [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить рекомендуемые патчи. Например, установка `MessagePackSecurity.Active` статического `MessagePackSecurity.UntrustedData`свойства для . Установка `MessagePackSecurity.Active` требует ручной установки [1.9.x версии MessagePack.](https://www.nuget.org/packages/MessagePack/1.9.3) Установка `MessagePack` 1.9.x обновляет версию. SignalR Когда `MessagePackSecurity.Active` не `MessagePackSecurity.UntrustedData`установлен, злонамеренный клиент может привести к отказу в обслуживании. `MessagePackSecurity.Active` Установить `Program.Main`в , как показано в следующем коде:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Настройка Пакета сообщений на клиента

> [!NOTE]
> JSON включен по умолчанию для поддерживаемых клиентов. Клиенты могут поддерживать только один протокол. Добавление поддержки MessagePack заменит все ранее настроенные протоколы.

### <a name="net-client"></a>Клиент .NET

Чтобы включить MessagePack в клиенте `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`установите пакет и позвоните по телефону.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Этот `AddMessagePackProtocol` вызов требует делегата для настройки параметров так же, как сервер.

### <a name="javascript-client"></a>Клиент JavaScript

Поддержка клиента MessagePack для клиента JavaScript обеспечивается пакетом [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm. Установите пакет, выполняя следующую команду в командной оболочке:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

После установки пакета npm модуль может быть использован непосредственно через модуль JavaScript погрузчик или импортирован в браузер, ссылаясь на следующий файл:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

В браузере `msgpack5` также необходимо ссылаться на библиотеку. Используйте `<script>` тег для создания ссылки. Библиотеку можно найти по адресу *node_modules'msgpack5'dist-msgpack5.js*.

> [!NOTE]
> При использовании элемента `<script>` важен порядок. Если *сигнальный протокол-msgpack.js* ссылается до *msgpack5.js*, ошибка возникает при попытке связаться с MessagePack. *signalr.js* также требуется перед *сигнальщиком-протоколом-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` к `HubConnectionBuilder` настроенной клиенту для использования протокола MessagePack при подключении к серверу.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> В настоящее время в клиенте JavaScript нет вариантов конфигурации для протокола MessagePack.

## <a name="messagepack-quirks"></a>Причуды MessagePack

При использовании протокола MessagePack Hub необходимо узнать несколько проблем.

### <a name="messagepack-is-case-sensitive"></a>MessagePack является чувствительным к случаям

Протокол MessagePack является чувствительным к случаям. Например, рассмотрим следующий класс C:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

При отправке из клиента JavaScript `PascalCased` необходимо использовать имена свойств, так как корпус должен точно соответствовать классу C'. Пример:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Использование `camelCased` имен не будет должным образом связано с классом C.' Вы можете обойти это, `Key` используя атрибут, чтобы указать другое имя для свойства MessagePack. Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind не сохраняется при сериализации/десериализации

Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime`. В результате при десериализации даты протокол MessagePack Hub предполагает, что входящие даты указаны в формате UTC. Если вы работаете `DateTime` с значениями в местное время, мы рекомендуем перейти в UTC перед отправкой. Преобразуйте их из UTC в местное время, когда вы их получите.

Для получения дополнительной информации об этом ограничении, с [SignalR#2632м.](https://github.com/aspnet/SignalR/issues/2632)

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue не поддерживается MessagePack в JavaScript

Библиотека [msgpack5,](https://github.com/mcollina/msgpack5) используемая клиентом SignalR JavaScript, `timestamp96` не поддерживает тип в MessagePack. Этот тип используется для кодирования очень больших значений дат (либо в самом начале прошлого, либо очень далеко в будущем). Значение, `DateTime.MinValue` `January 1, 0001` которое должно быть закодировано в значении. `timestamp96` Из-за этого `DateTime.MinValue` отправка клиенту JavaScript не поддерживается. Когда `DateTime.MinValue` клиент JavaScript получает:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Как `DateTime.MinValue` правило, используется для кодирования `null` "пропавших без вести" или значение. Если вам нужно кодировать это значение в MessagePack, используйте необоснованное `DateTime` значение ()`DateTime?`или закодируйте отдельное `bool` значение с указанием наличия даты.

Для получения дополнительной информации об этом ограничении, см. GitHub вопрос [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Поддержка MessagePack в среде компиляции «досрочное время»

Библиотека [MessagePack-CSharp,](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) используемая клиентом и сервером .NET, использует генерацию кода для оптимизации сериализации. В результате он не поддерживается по умолчанию для сред, которые используют компиляцию «досрочного времени» (например, Xamarin iOS или Unity). В этих средах можно использовать MessagePack, «предварительно генерируя» код serializer/deserializer. Для получения дополнительной информации смотрите [документацию MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). После того как вы предварительно сгенерировали сериализаторы, вы `AddMessagePackProtocol`можете зарегистрировать их с помощью делегата конфигурации, переданного:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Проверки типов являются более строгими в MessagePack

Протокол JSON Hub будет выполнять преобразования типов во время десериализации. Например, если входящий объект имеет значение свойства,`{ foo: 42 }`которое является числом (), `string`но свойство на классе .NET имеет тип, значение будет преобразовано. Тем не менее, MessagePack не выполняет это преобразование и будет бросать исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Для получения дополнительной информации об этом ограничении, с [SignalR#2937м.](https://github.com/aspnet/SignalR/issues/2937)

## <a name="related-resources"></a>Связанные ресурсы

* [Начало работы](xref:tutorials/signalr)
* [клиент .NET](xref:signalr/dotnet-client)
* [Клиент на JavaScript](xref:signalr/javascript-client)

::: moniker-end
