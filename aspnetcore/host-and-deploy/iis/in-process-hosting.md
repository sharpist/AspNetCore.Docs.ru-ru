---
title: Внутрипроцессное размещение в службах IIS и ASP.NET Core
author: rick-anderson
description: Сведения о внутрипроцессном размещении с использованием служб IIS и модуля ASP.NET Core.
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058484"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>Внутрипроцессное размещение в службах IIS и ASP.NET Core 

При внутрипроцессном размещении приложение ASP.NET Core выполняется в том же процессе, что и рабочий процесс IIS. При этом повышается производительность по сравнению с внепроцессным размещением, так как запросы не передаются через адаптер замыкания на себя (сетевой интерфейс, который возвращает исходящий сетевой трафик на тот же компьютер).

На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением, размещенным внутри процесса.

![Модуль ASP.NET Core в сценарии внутрипроцессного размещения](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>Включение внутрипроцессного размещения

Начиная с ASP.NET Core 3.0, внутрипроцессное размещение включено по умолчанию для всех приложений, развертываемых в службах IIS.

Чтобы явным образом настроить внутрипроцессное размещение для приложения, задайте для свойства `<AspNetCoreHostingModel>` значение `InProcess` в файле проекта (`.csproj`).

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>Общая архитектура

В общем виде запрос выполняется и обрабатывается следующим образом.

1. Запрос поступает из Интернета в драйвер HTTP.sys в режиме ядра.
1. Драйвер направляет собственный запрос к IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS).
1. Модуль ASP.NET Core получает собственный запрос и передает его на HTTP-сервер IIS (`IISHttpServer`). HTTP-сервер IIS — это реализация внутрипроцессного сервера для IIS, в которой запрос преобразовывается из собственной формы в управляемую.

После того как HTTP-сервер IIS обработает запрос:

1. Запрос отправляется в конвейер ПО промежуточного слоя ASP.NET Core.
1. Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.
1. Ответ приложения передается обратно в службы IIS через HTTP-сервер IIS.
1. IIS отправляет ответ клиенту, который инициировал запрос.

`CreateDefaultBuilder` добавляет экземпляр <xref:Microsoft.AspNetCore.Hosting.Server.IServer>. При этом вызывается метод <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> для загрузки [CoreCLR](/dotnet/standard/glossary#coreclr) и размещения приложения внутри рабочего процесса IIS (`w3wp.exe` или `iisexpress.exe`). Тесты производительности показывают, что размещение приложения .NET Core внутри процесса позволяет обрабатывать значительно больше запросов, чем при размещении приложения вне процесса с перенаправлением запросов к [Kestrel](xref:fundamentals/servers/kestrel).

Приложения, опубликованные в виде одного исполняемого файла, не могут быть загружены по модели внутрипроцессного размещения.

## <a name="application-configuration"></a>Настройка приложения

Чтобы настроить параметры IIS, включите конфигурацию служб для <xref:Microsoft.AspNetCore.Builder.IISServerOptions> в <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. В следующем примере показано, как отключить AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Параметр | Значение по умолчанию | Параметр |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | Если значение — `true`, сервер IIS задает свойство `HttpContext.User`, использующее [проверку подлинности Windows](xref:security/authentication/windowsauth). Если значение — `false`, сервер только предоставляет идентификатор для `HttpContext.User` и отвечает на явные запросы защиты от `AuthenticationScheme`. Для работы `AutomaticAuthentication` необходимо включить в службах IIS проверку подлинности Windows. Дополнительные сведения: [Проверка подлинности Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName` | `null` | Задает отображаемое имя для пользователей на страницах входа. |
| `AllowSynchronousIO` | `false` | Разрешены ли синхронные операции ввода-вывода для `HttpContext.Request` и `HttpContext.Response`. |
| `MaxRequestBodySize` | `30000000` | Возвращает или задает максимальный размер текста запроса для `HttpRequest`. Обратите внимание, что сами службы IIS ограничены параметром `maxAllowedContentLength`, который обрабатывается перед тем, как `MaxRequestBodySize` задается в `IISServerOptions`. Изменение `MaxRequestBodySize` не влияет на `maxAllowedContentLength`. Чтобы увеличить `maxAllowedContentLength`, добавьте запись в `web.config`, чтобы задать `maxAllowedContentLength` большее значение. Дополнительные сведения см. в разделе [Конфигурация](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>Различия между внутрипроцессным и внепроцессным размещением

При внутрипроцессном размещении применимы следующие характеристики:

* Вместо сервера [Kestrel](xref:fundamentals/servers/kestrel) используется HTTP-сервер IIS (`IISHttpServer`). При внутрипроцессном размещении [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> в следующих целях.

  * Регистрация `IISHttpServer`.
  * Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.
  * Настройка перехвата ошибок запуска на узле.

* [Атрибут `requestTimeout`](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) не применяется к внутрипроцессному размещению.

* Совместное использование пула приложений среди приложений не поддерживается. Используйте один пул приложений для каждого приложения.

* Архитектура (разрядность) приложения и установленная среда выполнения (x64 или x86) должны соответствовать архитектуре пула приложений. Например, для приложений, опубликованных для 32-разрядной архитектуры (x86), необходимо включить поддержку этой архитектуры для пулов приложений IIS. Дополнительные сведения см. в разделе [Создание сайта IIS](xref:tutorials/publish-to-iis#create-the-iis-site).

* Обнаружены отключения клиентов. Маркер отмены [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) аннулируется при отключении клиента.

* При размещении в процессе <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> не вызывается внутри для инициализации пользователя. Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию. При преобразовании утверждений с реализацией <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> вызовите <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> для добавления служб проверки подлинности:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* [Развертывания веб-пакета (однофайлового)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) не поддерживаются.
