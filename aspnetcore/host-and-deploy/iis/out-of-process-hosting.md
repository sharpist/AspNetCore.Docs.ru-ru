---
title: Внепроцессное размещение в службах IIS и ASP.NET Core
author: rick-anderson
description: Сведения о внепроцессном размещении с использованием служб IIS и модуля ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060421"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>Внепроцессное размещение в службах IIS и ASP.NET Core 

Так как приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, модуль ASP.NET Core обрабатывает управление процессами. Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое или завершении работы. Это, по сути, совпадает с поведением приложений, выполняемых внутрипроцессно и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением, размещенным вне процесса.

![Модуль ASP.NET Core в сценарии внепроцессного размещения](index/_static/ancm-outofprocess.png)

1. Запросы поступают из Интернета в драйвер HTTP.sys в режиме ядра.
1. Драйвер направляет запросы к службам IIS на настроенный порт веб-сайта. Обычно это порт 80 (HTTP) или порт 443 (HTTPS).
1. Модуль перенаправляет запросы Kestrel на случайный порт для приложения. В качестве случайного порта не могут использоваться порты 80 и 443.

<!-- make this a bullet list -->
Модуль ASP.NET Core задает порт с помощью переменной среды при запуске. Расширение <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> настраивает сервер для прослушивания `http://localhost:{PORT}`. Выполняются дополнительные проверки, и запросы не из модуля отклоняются. Модуль не поддерживает переадресацию по HTTPS. Запросы переадресовываются по протоколу HTTP, даже если были получены IIS по протоколу HTTPS.

После того как Kestrel получает запрос из модуля, запрос пересылается в конвейер ПО промежуточного слоя ASP.NET Core. Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения. ПО промежуточного слоя, добавленное интеграцией IIS, обновляет схему, удаленный IP-адрес и базовый путь для переадресации запроса в Kestrel. Ответ приложения передается обратно в службу IIS, которая пересылает его HTTP-клиенту, инициировавшему запрос.

Инструкции по настройке модуля ASP.NET Core см. в статье <xref:host-and-deploy/aspnet-core-module>.

Дополнительные сведения о размещении см. в разделе [Размещение в ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Настройка приложения

### <a name="enable-the-iisintegration-components"></a>Включение компонентов IISIntegration

При создании узла в `CreateHostBuilder` (`Program.cs`) вызовите метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, чтобы включить интеграцию IIS:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Дополнительные сведения о методе `CreateDefaultBuilder` см. в разделе <xref:fundamentals/host/generic-host#default-builder-settings>.


**Модель размещения вне процесса**

Чтобы настроить параметры IIS, включите конфигурацию служб для <xref:Microsoft.AspNetCore.Builder.IISOptions> в <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. В следующем примере приложению запрещается заполнение `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Параметр                         | Значение по умолчанию | Параметр |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Если значение — `true`, [ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components) задает свойство `HttpContext.User`, которое прошло [проверку подлинности Windows](xref:security/authentication/windowsauth). Если значение — `false`, ПО промежуточного слоя только предоставляет идентификатор для `HttpContext.User` и отвечает на явные запросы защиты от `AuthenticationScheme`. Для работы `AutomaticAuthentication` необходимо включить в службах IIS проверку подлинности Windows. Дополнительные сведения см. в статье о [проверке подлинности Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Задает отображаемое имя для пользователей на страницах входа. |
| `ForwardClientCertificate`     | `true`  | Если значение — `true` и если присутствует заголовок запроса `MS-ASPNETCORE-CLIENTCERT`, происходит заполнение `HttpContext.Connection.ClientCertificate`. |


### <a name="proxy-server-and-load-balancer-scenarios"></a>Сценарии использования прокси-сервера и подсистемы балансировки нагрузки

[ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components) и модуль ASP.NET Core настроены для пересылки:

* схемы (HTTP/HTTPS);
* удаленного IP-адреса, на котором возник запрос.

[ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components) настраивает ПО промежуточного слоя переадресации заголовков.

Для приложений, размещенных за дополнительными прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка. Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).


### <a name="out-of-process-hosting-model"></a>Модель размещения вне процесса

Чтобы настроить приложение для внепроцессного размещения, задайте для свойства `<AspNetCoreHostingModel>` значение `OutOfProcess` в файле проекта (`.csproj`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Для внутрипроцессного размещения указано значение `InProcess`, которое является значением по умолчанию.

Значение `<AspNetCoreHostingModel>` не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.

Сервер [Kestrel](xref:fundamentals/servers/kestrel) используется вместо HTTP-сервера IIS (`IISHttpServer`).

При размещении вне процесса [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> в следующих целях.

* Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.
* Настройка перехвата ошибок запуска на узле.

### <a name="process-name"></a>Имя процесса

`Process.GetCurrentProcess().ProcessName` сообщает `w3wp`/`iisexpress` (внутри процесса) или `dotnet` (вне процесса).

Многие собственные модули, такие как проверка подлинности Windows, остаются активными. Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.

Дополнительные возможности модуля ASP.NET Core:

* Задание переменных среды для рабочего процесса.
* Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.
* Переадресация токенов проверки подлинности Windows.
