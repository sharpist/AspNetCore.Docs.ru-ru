---
title: Различия между SignalR и ASP.NET CoreSignalR
author: bradygaster
description: Различия между SignalR и ASP.NET CoreSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: b5899f816dc5a5f8ff4c3f05c8e2c54ded5fc47b
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756045"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>Различия между ASP.NET SignalR и ASP.NET CoreSignalR

ASP.NET Core SignalR несовместим с клиентами или серверами для ASP.NET SignalR . В этой статье описываются функции, которые были удалены или изменены в ASP.NET Core SignalR .

## <a name="how-to-identify-the-signalr-version"></a>Определение SignalR версии

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NETSignalR | ASP.NET CoreSignalR |
| -------------------- | --------------- | -------------------- |
| Пакет NuGet сервера | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | Нет. Входит в состав общей платформы [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . |
| Клиентские пакеты NuGet | [Microsoft. AspNet. SignalR . Компьютера](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR . Компьютера](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Пакет NPM клиента JavaScript | [SignalR](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| Клиент Java | [Репозиторий GitHub](https://github.com/SignalR/java-client) (не рекомендуется)  | Пакет Maven [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Тип серверного приложения | ASP.NET (System. Web) или OWIN Self-Host | ASP.NET Core |
| Поддерживаемые серверные платформы | .NET Framework 4,5 или более поздней версии | .NET Core 3,0 или более поздней версии |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NETSignalR | ASP.NET CoreSignalR |
| -------------------- | --------------- | -------------------- |
| Пакет NuGet сервера | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Клиентские пакеты NuGet | [Microsoft. AspNet. SignalR . Компьютера](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR . Компьютера](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Пакет NPM клиента JavaScript | [SignalR](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| Клиент Java | [Репозиторий GitHub](https://github.com/SignalR/java-client) (не рекомендуется)  | Пакет Maven [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Тип серверного приложения | ASP.NET (System. Web) или OWIN Self-Host | ASP.NET Core |
| Поддерживаемые серверные платформы | .NET Framework 4,5 или более поздней версии | .NET Framework 4.6.1 или более поздней версии<br>.NET Core 2,1 или более поздней версии |

::: moniker-end

## <a name="feature-differences"></a>Различия в функциях

### <a name="automatic-reconnects"></a>Автоматические повторное подключения

::: moniker range=">= aspnetcore-3.0"

В ASP.NET SignalR :

* По умолчанию SignalR пытается повторно подключиться к серверу при разрыве соединения. 

В ASP.NET Core SignalR :

* Автоматические повторное подключение — это явное согласие с [клиентом .NET](xref:signalr/dotnet-client#automatically-reconnect) и [клиентом JavaScript](xref:signalr/javascript-client#automatically-reconnect):

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

До ASP.NET Core 3,0 SignalR не поддерживает автоматическое повторное подключение. Если Клиент отключен, пользователь должен явно запустить новое подключение для повторного подключения. В ASP.NET SignalR SignalR пытается повторно подключиться к серверу при разрыве соединения.

::: moniker-end

### <a name="protocol-support"></a>Поддержка протоколов

ASP.NET Core SignalR поддерживает JSON, а также новый двоичный протокол на основе [MessagePack](xref:signalr/messagepackhubprotocol). Кроме того, можно создавать пользовательские протоколы.

### <a name="transports"></a>Транспорты

Непрерывный многокадровый транспорт не поддерживается в ASP.NET Core SignalR .

## <a name="differences-on-the-server"></a>Различия на сервере

ASP.NET Core SignalR серверные библиотеки включены в [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), который используется в шаблоне **веб-приложения ASP.NET Core** для Razor проектов и и MVC.

ASP.NET Core SignalR — это ASP.NET Core по промежуточного слоя. Он должен быть настроен путем вызова <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> в `Startup.ConfigureServices` .

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Чтобы настроить маршрутизацию, сопоставьте маршруты с концентраторами внутри <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> вызова метода в `Startup.Configure` методе.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Чтобы настроить маршрутизацию, сопоставьте маршруты с концентраторами внутри <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> вызова метода в `Startup.Configure` методе.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Закрепленные сеансы

Модель масштабирования для ASP.NET SignalR позволяет клиентам повторно подключаться и передавать сообщения на любой сервер в ферме. В ASP.NET Core SignalR клиент должен взаимодействовать с тем же сервером на время соединения. Для масштабирования с помощью Redis, это означает, что требуются прикрепленные сеансы. Для масштабирования с помощью [ SignalR службы Azure](/azure/azure-signalr/)прикрепленные сеансы не требуются, так как служба обрабатывает соединения с клиентами.

### <a name="single-hub-per-connection"></a>Один концентратор на подключение

В ASP.NET Core SignalR модель подключения была упрощена. Подключения осуществляются непосредственно в одном концентраторе, а не в одном соединении, используемом для совместного доступа к нескольким концентраторам.

### <a name="streaming"></a>Потоковые операторы

ASP.NET Core SignalR теперь поддерживает [потоковую передачу данных](xref:signalr/streaming) от концентратора клиенту.

### <a name="state"></a>Состояние

Удалена возможность передачи произвольного состояния между клиентами и концентратором (часто называемым `HubState` ), а также поддержка сообщений о ходе выполнения. В данный момент не существует аналога прокси-серверов концентратора.

### <a name="persistentconnection-removal"></a>Удаление Персистентконнектион

В ASP.NET Core SignalR класс [персистентконнектион](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) был удален.

### <a name="globalhost"></a>GlobalHost

ASP.NET Core в платформе встроена встраивание зависимостей (DI). Службы могут использовать DI для доступа к [хубконтекст](xref:signalr/hubcontext). `GlobalHost`Объект, используемый в ASP.NET SignalR для получения, `HubContext` не существует в ASP.NET Core SignalR .

### <a name="hubpipeline"></a>хубпипелине

ASP.NET Core SignalR не поддерживает `HubPipeline` модули.

## <a name="differences-on-the-client"></a>Различия в клиенте

### <a name="typescript"></a>TypeScript

Клиент ASP.NET Core SignalR записывается в [TypeScript](https://www.typescriptlang.org/). При использовании [клиента JavaScript](xref:signalr/javascript-client)можно писать на JavaScript или TypeScript.

### <a name="the-javascript-client-is-hosted-at-npm"></a>Клиент JavaScript размещается по адресу NPM

::: moniker range=">= aspnetcore-3.0"

В ASP.NET версиях клиент JavaScript был получен с помощью пакета NuGet в Visual Studio. В ASP.NET Core версиях [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) пакет NPM содержит библиотеки JavaScript. Этот пакет не входит в шаблон **веб-приложения ASP.NET Core** . Используйте NPM для получения и установки `@microsoft/signalr` пакета NPM.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

В ASP.NET версиях клиент JavaScript был получен с помощью пакета NuGet в Visual Studio. В ASP.NET Core версиях [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) пакет NPM содержит библиотеки JavaScript. Этот пакет не входит в шаблон **веб-приложения ASP.NET Core** . Используйте NPM для получения и установки `@aspnet/signalr` пакета NPM.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

Зависимость от jQuery удалена, однако проекты по-прежнему могут использовать jQuery.

### <a name="internet-explorer-support"></a>Поддержка Internet Explorer

Для ASP.NET Core SignalR требуется Microsoft Internet Explorer 11 или более поздняя версия (ASP.NET SignalR поддерживал Microsoft Internet Explorer 8 и более поздние версии).

### <a name="javascript-client-method-syntax"></a>Синтаксис клиентского метода JavaScript

::: moniker range=">= aspnetcore-3.0"

Синтаксис JavaScript был изменен с версии ASP.NET SignalR . Вместо использования `$connection` объекта создайте подключение с помощью API [хубконнектионбуилдер](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Используйте метод [On](/javascript/api/@microsoft/signalr/HubConnection#on) , чтобы указать клиентские методы, которые может вызывать концентратор.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Синтаксис JavaScript был изменен с версии ASP.NET SignalR . Вместо использования `$connection` объекта создайте подключение с помощью API [хубконнектионбуилдер](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Используйте метод [On](/javascript/api/@aspnet/signalr/HubConnection#on) , чтобы указать клиентские методы, которые может вызывать концентратор.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

После создания клиентского метода запустите подключение концентратора. Привязать метод [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) для ведения журнала или обработке ошибок.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>Прокси-серверы концентратора

::: moniker range=">= aspnetcore-3.0"

Прокси-серверы концентратора больше не создаются автоматически. Вместо этого имя метода передается в API [вызова](/javascript/api/@microsoft/signalr/hubconnection#invoke) в виде строки.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Прокси-серверы концентратора больше не создаются автоматически. Вместо этого имя метода передается в API [вызова](/javascript/api/@aspnet/signalr/hubconnection#invoke) в виде строки.

::: moniker-end

### <a name="net-and-other-clients"></a>.NET и другие клиенты

Элемент [Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Пакет NuGet клиента содержит клиентские библиотеки .NET для ASP.NET Core SignalR .

Используйте <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> для создания и построения экземпляра соединения с концентратором.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Различия масштабирования

ASP.NET SignalR поддерживает SQL Server и Redis. ASP.NET Core SignalR поддерживает SignalR службу Azure и Redis.

### <a name="aspnet"></a>ASP.NET

* [SignalRМасштабирование с помощью служебной шины Azure](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalRМасштабирование с помощью Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalRМасштабирование с помощью SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [SignalRСлужба Azure](/azure/azure-signalr/)
* [Объединительная плата Redis](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Концентраторы](xref:signalr/hubs)
* [Клиент JavaScript](xref:signalr/javascript-client)
* [Клиент .NET](xref:signalr/dotnet-client)
* [Поддерживаемые платформы](xref:signalr/supported-platforms)
