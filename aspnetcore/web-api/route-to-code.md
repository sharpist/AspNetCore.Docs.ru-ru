---
title: Базовые API-интерфейсы JSON с Route-to-code в ASP.NET Core
author: jamesnk
description: Узнайте, как использовать Route-to-code методы расширения JSON для создания упрощенных веб-интерфейсов API JSON.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: f8a3804a887ebfa0f5284d8991e903c978b18208
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556610"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>Базовые API-интерфейсы JSON с Route-to-code в ASP.NET Core

Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://github.com/jamesnk)

ASP.NET Core поддерживает несколько способов создания веб-API JSON.

* [ASP.NET Core веб-API](xref:web-api/index) предоставляет полную платформу для создания интерфейсов API. Служба создается путем наследования от <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Некоторые функции, предоставляемые платформой, включают в себя привязку модели, проверку, согласование содержимого, форматирование ввода и вывода, а также OpenAPI.
* Route-to-code является альтернативой, отличной от платформы ASP.NET Core веб-API. Route-to-code подключает [ASP.NET Coreную маршрутизацию](xref:fundamentals/routing) непосредственно к коду. Код считывает данные из запроса и записывает ответ. Route-to-code не имеет дополнительных функций веб-API, но для его использования не требуется никакой настройки.

Route-to-code является хорошим подходом при создании небольших и базовых веб-API JSON.

## <a name="create-json-web-apis"></a>Создание веб-API JSON

ASP.NET Core предоставляет вспомогательные методы, упрощающие создание веб-API JSON.

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> проверяет `Content-Type` заголовок для типа содержимого JSON.
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> считывает JSON из запроса и десериализует его в указанный тип.
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> Записывает указанное значение как JSON в текст ответа и задает тип содержимого ответа `application/json` .

Упрощенные API-интерфейсы JSON на основе маршрутов указываются в *Startup.CS*. Логика маршрута и API-интерфейса настраивается в <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> составе конвейера запросов приложения.

### <a name="write-json-response"></a>Запись ответа JSON

Рассмотрим следующий код, который настраивает API JSON для приложения:

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

Предыдущий код:

* Добавляет конечную точку HTTP GET API с в `/hello/{name:alpha}` качестве шаблона маршрута.
* При сопоставлении маршрута API считывает `name` значение маршрута из запроса.
* Записывает анонимный тип как ответ JSON с помощью `WriteAsJsonAsync` .

### <a name="read-json-request"></a>Чтение запроса JSON

`HasJsonContentType` и `ReadFromJsonAsync` могут использоваться для десериализации ответа JSON в API-интерфейсе JSON на основе маршрута:

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

Предыдущий код:

* Добавляет конечную точку API HTTP POST в `/weather` качестве шаблона маршрута.
* При сопоставлении маршрута `HasJsonContentType` проверяет тип содержимого запроса. Тип содержимого, не относящийся к JSON, возвращает код состояния 415.
* Если тип содержимого — JSON, содержимое запроса десериализуется `ReadFromJsonAsync` .

### <a name="configure-json-serialization"></a>Настройка сериализации JSON

Существует два способа настройки сериализации JSON.

* Параметры сериализации по умолчанию можно настроить с помощью <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> `Startup.ConfigureServices` метода.
* `WriteAsJsonAsync` и `ReadFromJsonAsync` имеют перегрузки, принимающие <xref:System.Text.Json.JsonSerializerOptions> объект. Этот объект параметров переопределяет параметры по умолчанию.

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>Аутентификация и авторизация

Route-to-code поддерживает проверку подлинности и авторизацию. Атрибуты, такие как `[Authorize]` и `[AllowAnonymous]` , не могут быть помещены в конечные точки, сопоставленные с делегатом запроса. Вместо этого метаданные авторизации добавляются с помощью <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> методов расширения и.

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>Внедрение зависимостей

[Внедрение зависимостей (DI)](xref:fundamentals/dependency-injection) с помощью конструктора не поддерживается в Route-to-code . Веб-API создает контроллер для вас с помощью служб, внедренных в конструктор. Тип не создается при выполнении конечной точки, поэтому службы должны быть разрешены вручную.

API-интерфейсы на основе маршрутов могут использоваться <xref:System.IServiceProvider> для разрешения служб:

* Временные и ограниченные службы времени существования, такие как `DbContext` , должны быть разрешены из [HttpContext. рекуестсервицес](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) внутри делегата запроса конечной точки.
* Службы времени жизни Singleton, такие как `ILogger` , могут быть разрешены из [Иендпоинтраутебуилдер. serviceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider). Службы могут разрешаться вне делегатов запросов и совместно использоваться конечными точками.

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

API-интерфейсы, использующие DI, должны использовать тип приложения ASP.NET Core, поддерживающий DI. Например, ASP.NET Core веб-API. Внедрение службы с помощью конструктора контроллера проще, чем ручное разрешение служб.

## <a name="api-project-structure"></a>Структура проекта API

Интерфейсы API на основе маршрутов не должны размещаться в *Startup.CS*. API-интерфейсы могут размещаться в других файлах и сопоставляться при запуске с `UseEndpoints` . Такой подход сокращает размер файла конфигурации запуска.

Рассмотрим следующий статический `UserApi` класс, определяющий `Map` метод. Метод сопоставляет API на основе маршрутов.

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

В `Startup.Configure` методе `Map` метод и статические методы другого класса вызываются в `UseEndpoints` :

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>Важные отсутствующие компоненты по сравнению с веб-API

Route-to-code предназначен для базовых API-интерфейсов JSON. Она не поддерживает многие дополнительные возможности, предоставляемые ASP.NET Core веб-API.

Функции, не предоставляемые, Route-to-code включают:

* Привязка модели
* Проверка модели
* OpenAPI/Swagger
* Согласование содержимого
* Внедрение зависимостей конструктора
* `ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))

Рекомендуется использовать [веб-api ASP.NET Core](xref:web-api/index) для создания API, если для него требуются некоторые функции из предыдущего списка.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
