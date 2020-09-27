---
title: Использование протокола концентратора MessagePack в SignalR для ASP.NET Core
author: bradygaster
description: Добавьте протокол концентратора MessagePack в ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 15ae8911e2ffba43c7bb885efd153d8b6803bc2a
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393682"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a>Использование протокола концентратора MessagePack в SignalR для ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Что такое MessagePack?

[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации. Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/). Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack. SignalR имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.

## <a name="configure-messagepack-on-the-server"></a>Настройка MessagePack на сервере

Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет в приложении. В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `AddSignalR` вызову, чтобы включить поддержку MessagePack на сервере.

> [!NOTE]
> JSON включен по умолчанию. Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров. В этом делегате `SerializerOptions` свойство можно использовать для настройки параметров сериализации MessagePack. Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.

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
> Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления. Например, вызов `.WithSecurity(MessagePackSecurity.UntrustedData)` при замене `SerializerOptions` .

## <a name="configure-messagepack-on-the-client"></a>Настройка MessagePack на клиенте

> [!NOTE]
> JSON включен по умолчанию для поддерживаемых клиентов. Клиенты могут поддерживать только один протокол. Добавление поддержки MessagePack заменит все ранее настроенные протоколы.

### <a name="net-client"></a>Клиент .NET

Чтобы включить MessagePack в клиенте .NET, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.

### <a name="javascript-client"></a>Клиент JavaScript

Поддержка MessagePack для клиента JavaScript предоставляется [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) пакетом NPM. Установите пакет, выполнив следующую команду в командной оболочке:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

В браузере `msgpack5` также должна быть ссылка на библиотеку. Используйте `<script>` тег для создания ссылки. Библиотеку можно найти по адресу *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> При использовании `<script>` элемента важен порядок. Если на *signalr-protocol-msgpack.js* имеется ссылка до *msgpack5.js*, то при попытке соединения с MessagePack возникает ошибка. *signalr.js* также требуется перед *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.

## <a name="messagepack-quirks"></a>Особенности MessagePack

Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack учитывает регистр

В протоколе MessagePack учитывается регистр. Например, рассмотрим следующий класс C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#. Пример:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Использование `camelCased` имен не будет правильно привязано к классу C#. Это можно обойти, используя атрибут, `Key` чтобы указать другое имя для свойства MessagePack. Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind не сохраняется при сериализации или десериализации

Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime` . В результате при десериализации даты протокол концентратора MessagePack преобразуется в формат UTC, если в `DateTime.Kind` `DateTimeKind.Local` противном случае он не будет затрагивать время и передать его как есть. При работе со `DateTime` значениями рекомендуется преобразовать в формат UTC перед отправкой. Преобразуйте их из времени в формате UTC в местное время при их получении.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue не поддерживается в MessagePack в JavaScript

Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая SignalR клиентом JavaScript, не поддерживает `timestamp96` тип в MessagePack. Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем). Значение `DateTime.MinValue` равно `January 1, 0001` , которое должно быть закодировано в `timestamp96` значении. По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается. При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Обычно `DateTime.MinValue` используется для кодирования «отсутствующего» или `null` значения. Если необходимо закодировать это значение в MessagePack, используйте `DateTime` значение null ( `DateTime?` ) или закодировать отдельное `bool` значение, указывающее, существует ли дата.

Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Поддержка MessagePack в среде предварительной компиляции "до времени"

Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации. Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity). В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации. Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Проверки типов более строго в MessagePack

Протокол JSON-концентратора выполнит преобразования типов во время десериализации. Например, если входящий объект имеет значение свойства, которое является числом (), `{ foo: 42 }` но свойство класса .NET имеет тип `string` , значение будет преобразовано. Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Связанные ресурсы

* [Приступая к работе](xref:tutorials/signalr)
* [Клиент .NET](xref:signalr/dotnet-client)
* [Клиент JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Что такое MessagePack?

[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации. Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/). Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack. SignalR имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.

## <a name="configure-messagepack-on-the-server"></a>Настройка MessagePack на сервере

Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет в приложении. В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `AddSignalR` вызову, чтобы включить поддержку MessagePack на сервере.

> [!NOTE]
> JSON включен по умолчанию. Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров. В этом делегате `FormatterResolvers` свойство можно использовать для настройки параметров сериализации MessagePack. Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.

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
> Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления. Например, присвоение `MessagePackSecurity.Active` статическому свойству значения `MessagePackSecurity.UntrustedData` . Для установки параметра `MessagePackSecurity.Active` требуется ручная установка [версии 1,9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). При установке `MessagePack` обновлений 1,9. x SignalR используется версия. `MessagePack` Версия 2. x предоставила критические изменения и несовместима с SignalR версиями 3,1 и более ранних версий. Если `MessagePackSecurity.Active` параметр не имеет значение `MessagePackSecurity.UntrustedData` , вредоносный клиент может вызвать отказ в обслуживании. Задайте `MessagePackSecurity.Active` в `Program.Main` , как показано в следующем коде:

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Настройка MessagePack на клиенте

> [!NOTE]
> JSON включен по умолчанию для поддерживаемых клиентов. Клиенты могут поддерживать только один протокол. Добавление поддержки MessagePack заменит все ранее настроенные протоколы.

### <a name="net-client"></a>Клиент .NET

Чтобы включить MessagePack в клиенте .NET, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.

### <a name="javascript-client"></a>Клиент JavaScript

Поддержка MessagePack для клиента JavaScript предоставляется [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) пакетом NPM. Установите пакет, выполнив следующую команду в командной оболочке:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

В браузере `msgpack5` также должна быть ссылка на библиотеку. Используйте `<script>` тег для создания ссылки. Библиотеку можно найти по адресу *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> При использовании `<script>` элемента важен порядок. Если на *signalr-protocol-msgpack.js* имеется ссылка до *msgpack5.js*, то при попытке соединения с MessagePack возникает ошибка. *signalr.js* также требуется перед *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.

## <a name="messagepack-quirks"></a>Особенности MessagePack

Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack учитывает регистр

В протоколе MessagePack учитывается регистр. Например, рассмотрим следующий класс C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#. Пример:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Использование `camelCased` имен не будет правильно привязано к классу C#. Это можно обойти, используя атрибут, `Key` чтобы указать другое имя для свойства MessagePack. Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind не сохраняется при сериализации или десериализации

Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime` . В результате при десериализации даты протокол концентратора MessagePack предполагает, что входящая Дата находится в формате UTC. Если вы работаете со `DateTime` значениями в местном времени, перед отправкой рекомендуется преобразовать в формат UTC. Преобразуйте их из времени в формате UTC в местное время при их получении.

Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue не поддерживается в MessagePack в JavaScript

Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая SignalR клиентом JavaScript, не поддерживает `timestamp96` тип в MessagePack. Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем). Значение `DateTime.MinValue` равно `January 1, 0001` , которое должно быть закодировано в `timestamp96` значении. По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается. При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Обычно `DateTime.MinValue` используется для кодирования «отсутствующего» или `null` значения. Если необходимо закодировать это значение в MessagePack, используйте `DateTime` значение null ( `DateTime?` ) или закодировать отдельное `bool` значение, указывающее, существует ли дата.

Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Поддержка MessagePack в среде предварительной компиляции "до времени"

Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации. Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity). В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации. Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Проверки типов более строго в MessagePack

Протокол JSON-концентратора выполнит преобразования типов во время десериализации. Например, если входящий объект имеет значение свойства, которое является числом (), `{ foo: 42 }` но свойство класса .NET имеет тип `string` , значение будет преобразовано. Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Связанные ресурсы

* [Приступая к работе](xref:tutorials/signalr)
* [Клиент .NET](xref:signalr/dotnet-client)
* [Клиент JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этой статье предполагается, что читатель знаком с разделами, изложенными в разделе [Приступая к работе](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Что такое MessagePack?

[MessagePack](https://msgpack.org/index.html) — это быстрый и компактный формат двоичной сериализации. Это полезно, когда производительность и пропускная способность являются важным фактором, поскольку они создают меньшие сообщения по сравнению с [JSON](https://www.json.org/). Двоичные сообщения недоступны для чтения при просмотре трассировки сети и журналов, если только эти байты не передаются через средство синтаксического анализа MessagePack. SignalR имеет встроенную поддержку формата MessagePack и предоставляет API-интерфейсы для использования клиентом и сервером.

## <a name="configure-messagepack-on-the-server"></a>Настройка MessagePack на сервере

Чтобы включить протокол концентратора MessagePack на сервере, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет в приложении. В `Startup.ConfigureServices` методе добавьте `AddMessagePackProtocol` к `AddSignalR` вызову, чтобы включить поддержку MessagePack на сервере.

> [!NOTE]
> JSON включен по умолчанию. Добавление MessagePack обеспечивает поддержку для клиентов JSON и MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Чтобы настроить, как MessagePack будет форматировать данные, `AddMessagePackProtocol` принимает делегат для настройки параметров. В этом делегате `FormatterResolvers` свойство можно использовать для настройки параметров сериализации MessagePack. Дополнительные сведения о том, как работают арбитры конфликтов, см. в библиотеке MessagePack по адресу [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Атрибуты можно использовать для объектов, которые необходимо сериализовать, чтобы определить, как они должны обрабатываться.

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
> Настоятельно рекомендуем ознакомиться с [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) и применить Рекомендуемые исправления. Например, присвоение `MessagePackSecurity.Active` статическому свойству значения `MessagePackSecurity.UntrustedData` . Для установки параметра `MessagePackSecurity.Active` требуется ручная установка [версии 1,9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). При установке `MessagePack` обновлений 1,9. x SignalR используется версия. Если параметр `MessagePackSecurity.Active` не имеет значение `MessagePackSecurity.UntrustedData` , вредоносный клиент может вызвать отказ в обслуживании. Задайте `MessagePackSecurity.Active` в `Program.Main` , как показано в следующем коде:

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Настройка MessagePack на клиенте

> [!NOTE]
> JSON включен по умолчанию для поддерживаемых клиентов. Клиенты могут поддерживать только один протокол. Добавление поддержки MessagePack заменит все ранее настроенные протоколы.

### <a name="net-client"></a>Клиент .NET

Чтобы включить MessagePack в клиенте .NET, установите `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` пакет и вызовите `AddMessagePackProtocol` его `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Этот `AddMessagePackProtocol` вызов принимает делегат для настройки параметров так же, как и сервер.

### <a name="javascript-client"></a>Клиент JavaScript

Поддержка MessagePack для клиента JavaScript предоставляется [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) пакетом NPM. Установите пакет, выполнив следующую команду в командной оболочке:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

После установки пакета NPM модуль можно использовать непосредственно через загрузчик модуля JavaScript или импортировать в браузер, обратившись к следующему файлу:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

В браузере `msgpack5` также должна быть ссылка на библиотеку. Используйте `<script>` тег для создания ссылки. Библиотеку можно найти по адресу *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> При использовании `<script>` элемента важен порядок. Если на *signalr-protocol-msgpack.js* имеется ссылка до *msgpack5.js*, то при попытке соединения с MessagePack возникает ошибка. *signalr.js* также требуется перед *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Добавление `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` в `HubConnectionBuilder` приведет к настройке клиента для использования протокола MessagePack при соединении с сервером.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> В настоящее время параметры конфигурации для протокола MessagePack на клиенте JavaScript отсутствуют.

## <a name="messagepack-quirks"></a>Особенности MessagePack

Существует несколько вопросов, которые следует учитывать при использовании протокола концентратора MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack учитывает регистр

В протоколе MessagePack учитывается регистр. Например, рассмотрим следующий класс C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

При отправке из клиента JavaScript необходимо использовать `PascalCased` имена свойств, поскольку регистр должен точно соответствовать классу C#. Пример:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Использование `camelCased` имен не будет правильно привязано к классу C#. Это можно обойти, используя атрибут, `Key` чтобы указать другое имя для свойства MessagePack. Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind не сохраняется при сериализации или десериализации

Протокол MessagePack не предоставляет способ кодирования `Kind` значения `DateTime` . В результате при десериализации даты протокол концентратора MessagePack предполагает, что входящая Дата находится в формате UTC. Если вы работаете со `DateTime` значениями в местном времени, перед отправкой рекомендуется преобразовать в формат UTC. Преобразуйте их из времени в формате UTC в местное время при их получении.

Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue не поддерживается в MessagePack в JavaScript

Библиотека [msgpack5](https://github.com/mcollina/msgpack5) , используемая SignalR клиентом JavaScript, не поддерживает `timestamp96` тип в MessagePack. Этот тип используется для кодирования очень больших значений даты (на ранних этапах прошлого или слишком далеко в будущем). Значение, `DateTime.MinValue` `January 1, 0001` которое должно быть закодировано в `timestamp96` значении. По этой причине отправка `DateTime.MinValue` в клиент JavaScript не поддерживается. При `DateTime.MinValue` получении клиентом JavaScript возникает следующая ошибка:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Обычно `DateTime.MinValue` используется для кодирования «отсутствующего» или `null` значения. Если необходимо закодировать это значение в MessagePack, используйте `DateTime` значение null ( `DateTime?` ) или закодировать отдельное `bool` значение, указывающее, существует ли дата.

Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Поддержка MessagePack в среде предварительной компиляции "до времени"

Библиотека [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , используемая клиентом и сервером .NET, использует создание кода для оптимизации сериализации. Поэтому он не поддерживается по умолчанию в средах, использующих предварительную компиляцию (например, Xamarin iOS или Unity). В этих средах можно использовать MessagePack, выполнив предварительное создание кода сериализатора или десериализации. Дополнительные сведения см. [в документации по MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). После предварительного создания сериализаторов их можно зарегистрировать с помощью делегата конфигурации, переданного в `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Проверки типов более строго в MessagePack

Протокол JSON-концентратора выполнит преобразования типов во время десериализации. Например, если входящий объект имеет значение свойства, которое является числом (), `{ foo: 42 }` но свойство класса .NET имеет тип `string` , значение будет преобразовано. Однако MessagePack не выполняет это преобразование и вызовет исключение, которое можно увидеть в журналах на стороне сервера (и в консоли):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Дополнительные сведения об этом ограничении см. в разделе Проблема с GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Связанные ресурсы

* [Приступая к работе](xref:tutorials/signalr)
* [Клиент .NET](xref:signalr/dotnet-client)
* [Клиент JavaScript](xref:signalr/javascript-client)

::: moniker-end
