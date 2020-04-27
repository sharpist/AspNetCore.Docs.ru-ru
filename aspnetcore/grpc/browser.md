---
title: Использование gRPC в приложениях на основе браузера
author: jamesnk
description: Узнайте, как настроить службы gRPC на платформе ASP.NET Core для вызова из приложений браузера с помощью gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
uid: grpc/browser
ms.openlocfilehash: a20e604488b1fb919f18932599ba690bfa308f0c
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440770"
---
# <a name="use-grpc-in-browser-apps"></a>Использование gRPC в приложениях на основе браузера

Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)

> [!IMPORTANT]
> **Поддержка gRPC-Web реализуется в .NET в экспериментальном режиме**
>
> gRPC-Web для .NET — это экспериментальный проект, а не готовый продукт. Наши задачи:
>
> * Проверить, что наш подход к реализации gRPC-Web работает.
> * Получить отзыв о том, был ли этот подход полезен разработчикам .NET в сравнении с традиционным способом настройки gRPC-Web через прокси-сервер.
>
> Оставьте отзыв на сайте [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet), чтобы мы понимали, что создаем полезный и эффективный продукт для разработчиков.

Вызвать службу HTTP/2 gRPC из приложения на основе браузера невозможно. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) — это протокол, позволяющий приложениям JavaScript и Blazor на основе браузера вызывать службы gRPC. В этой статье описывается использование gRPC-Web в .NET Core.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web в ASP.NET Core или Envoy

Существует два способа добавления gRPC-Web в приложение ASP.NET Core.

* Поддержка gRPC-Web вместе с gRPC HTTP/2 в ASP.NET Core. Этот параметр использует ПО промежуточного слоя, предоставленное пакетом `Grpc.AspNetCore.Web`.
* Используйте поддержку gRPC-Web в [прокси-сервере Envoy](https://www.envoyproxy.io/), чтобы транслировать gRPC-Web в gRPC HTTP/2. Затем переведенный вызов перенаправляется в приложение ASP.NET Core.

Есть свои плюсы и минусы этого подхода. Если вы уже используете Envoy в качестве прокси-сервера в среде приложения, имеет смысл также использовать его для поддержки gRPC-Web. Если требуется простое решение для gRPC-Web, которое требует только ASP.NET Core, выбирайте `Grpc.AspNetCore.Web`.

## <a name="configure-grpc-web-in-aspnet-core"></a>Настройка gRPC-Web в ASP.NET Core

Службы gRPC, размещенные в ASP.NET Core, можно настроить на поддержку gRPC-Web вместе с HTTP/2 gRPC. gRPC-Web не требует вносить изменения в службы. Единственного изменения потребует конфигурация запуска.

Чтобы включить gRPC-Web со службой gRPC ASP.NET Core, выполните следующие действия.

* Добавьте ссылку на пакет [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web).
* Настройте приложение на использование gRPC-Web, добавив `AddGrpcWeb` и `UseGrpcWeb` в файл *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Предыдущий код:

* Добавляет ПО промежуточного слоя gRPC-Web (`UseGrpcWeb`) после маршрутизации и перед конечными точками.
* Указывает метод `endpoints.MapGrpcService<GreeterService>()` с поддержкой gRPC-Web с `EnableGrpcWeb`. 

Кроме того, можно настроить все службы на поддержку gRPC-Web, добавив `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` в ConfigureServices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> Существует известная ошибка, приводящая к сбою gRPC-Web при [, размещенной в HTTP. sys](xref:fundamentals/servers/httpsys) в .NET Core 3.x.
>
> Обходной путь для получения gRPC-Web на HTTP. sys доступен [здесь](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web и CORS

Система безопасности браузера предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу. Это ограничение применяется к вызовам gRPC-Web с приложениями браузера. Например, приложение браузера, обслуживаемое `https://www.contoso.com`, блокирует вызов служб gRPC-Web, размещенных на `https://services.contoso.com`. Можно использовать общий доступ к ресурсам независимо от источника (CORS), чтобы ослабить это ограничение.

Чтобы разрешить приложению браузера выполнять вызовы gRPC-Web независимо от источника, настройте [CORS в ASP.NET Core](xref:security/cors). Используйте встроенную поддержку CORS и предоставьте заголовки, относящиеся к gRPC, с помощью <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Предыдущий код:

* Вызывает `AddCors` для добавления служб CORS и настраивает политику CORS, которая предоставляет заголовки, относящиеся к gRPC.
* Вызывает `UseCors` для добавления ПО промежуточного слоя CORS после маршрутизации и перед конечными точками.
* Указывает метод `endpoints.MapGrpcService<GreeterService>()` с поддержкой CORS с `RequiresCors`.

## <a name="call-grpc-web-from-the-browser"></a>Вызов gRPC-Web из браузера

Приложения браузера могут использовать gRPC-Web для вызова служб gRPC. При вызове служб gRPC с помощью gRPC-Web из браузера существует ряд требований и ограничений.

* Сервер должен быть настроен для поддержки gRPC-Web.
* Потоковая передача клиента и вызовы двунаправленной потоковой передачи не поддерживаются. Потоковая передача сервера поддерживается.
* Для вызова служб gRPC в другом домене требуется настроить [CORS](xref:security/cors) на сервере.

### <a name="javascript-grpc-web-client"></a>Клиент gRPC-Web JavaScript

Существует клиент gRPC-Web JavaScript. Инструкции по использованию gRPC-Web из JavaScript см. в статье, посвященной [написанию кода клиента JavaScript с gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Настройка gRPC-Web с помощью клиента .NET gRPC

Клиент .NET gRPC можно настроить для выполнения вызовов gRPC-Web. Это полезно для приложений [Blazor WebAssembly](xref:blazor/index#blazor-webassembly), которые размещаются в браузере и имеют те же ограничения HTTP, что и код JavaScript. Вызов gRPC-Web с помощью клиента .NET выполняется [так же, как и HTTP/2 gRPC](xref:grpc/client). Единственным изменением является то, как создается канал.

Чтобы использовать gRPC-Web:

* Добавьте ссылку на пакет [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web).
* Убедитесь, что ссылаетесь на пакет [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) версии 2.27.0 или более поздней.
* Настройте канал на использование `GrpcWebHandler`:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Предыдущий код:

* Настраивает канал для использования gRPC-Web.
* Создает клиент и выполняет вызов с помощью канала.

При создании `GrpcWebHandler` имеет следующие параметры конфигурации:

* **InnerHandler**: базовый <xref:System.Net.Http.HttpMessageHandler>, который выполняет HTTP-запрос gRPC, например `HttpClientHandler`.
* **Режим**. Тип перечисления, указывающий, является ли HTTP-запрос gRPC `Content-Type` `application/grpc-web` или `application/grpc-web-text`.
    * `GrpcWebMode.GrpcWeb` настраивает содержимое для отправки без кодировки. Значение по умолчанию.
    * `GrpcWebMode.GrpcWebText` настраивает содержимое в кодировке Base64. Требуется для вызовов потоковой передачи сервера в браузерах.
* **HttpVersion**: `Version` протокола HTTP, используемая для задания [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) в базовом HTTP-запросе gRPC. gRPC-Web не требует определенной версии и не переопределяет значение по умолчанию, если не указано иное.

> [!IMPORTANT]
> Созданные клиенты gRPC имеют синхронные и асинхронные методы для вызова унарных методов. Например, `SayHello` является синхронным, а `SayHelloAsync` — асинхронным. Вызов синхронного метода в приложении Blazor WebAssembly приведет к тому, что приложение перестанет отвечать на запросы. В Blazor WebAssembly всегда следует использовать асинхронные методы.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Проект GitHub — gRPC для веб-клиентов](https://github.com/grpc/grpc-web)
* <xref:security/cors>
