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
ms.openlocfilehash: 1f5f532053f8f5ca7f73df8c1a910a484e2488d9
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513100"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="be42b-103">Базовые API-интерфейсы JSON с Route-to-code в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="be42b-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="be42b-104">Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="be42b-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="be42b-105">ASP.NET Core поддерживает несколько способов создания веб-API JSON.</span><span class="sxs-lookup"><span data-stu-id="be42b-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="be42b-106">[ASP.NET Core веб-API](xref:web-api/index) предоставляет полную платформу для создания интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="be42b-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="be42b-107">Служба создается путем наследования от <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="be42b-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="be42b-108">Некоторые функции, предоставляемые платформой, включают в себя привязку модели, проверку, согласование содержимого, форматирование ввода и вывода, а также OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="be42b-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="be42b-109">Route-to-code является альтернативой, отличной от платформы ASP.NET Core веб-API.</span><span class="sxs-lookup"><span data-stu-id="be42b-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="be42b-110">Route-to-code подключает [ASP.NET Coreную маршрутизацию](xref:fundamentals/routing) непосредственно к коду.</span><span class="sxs-lookup"><span data-stu-id="be42b-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="be42b-111">Код считывает данные из запроса и записывает ответ.</span><span class="sxs-lookup"><span data-stu-id="be42b-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="be42b-112">Route-to-code не имеет дополнительных функций веб-API, но для его использования не требуется никакой настройки.</span><span class="sxs-lookup"><span data-stu-id="be42b-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="be42b-113">Route-to-code является хорошим подходом при создании небольших и базовых веб-API JSON.</span><span class="sxs-lookup"><span data-stu-id="be42b-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="be42b-114">Создание веб-API JSON</span><span class="sxs-lookup"><span data-stu-id="be42b-114">Create JSON web APIs</span></span>

<span data-ttu-id="be42b-115">ASP.NET Core предоставляет вспомогательные методы, упрощающие создание веб-API JSON.</span><span class="sxs-lookup"><span data-stu-id="be42b-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="be42b-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> проверяет `Content-Type` заголовок для типа содержимого JSON.</span><span class="sxs-lookup"><span data-stu-id="be42b-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="be42b-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> считывает JSON из запроса и десериализует его в указанный тип.</span><span class="sxs-lookup"><span data-stu-id="be42b-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="be42b-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> Записывает указанное значение как JSON в текст ответа и задает тип содержимого ответа `application/json` .</span><span class="sxs-lookup"><span data-stu-id="be42b-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="be42b-119">Упрощенные API-интерфейсы JSON на основе маршрутов указываются в *Startup.CS*.</span><span class="sxs-lookup"><span data-stu-id="be42b-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="be42b-120">Логика маршрута и API-интерфейса настраивается в <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> составе конвейера запросов приложения.</span><span class="sxs-lookup"><span data-stu-id="be42b-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="be42b-121">Запись ответа JSON</span><span class="sxs-lookup"><span data-stu-id="be42b-121">Write JSON response</span></span>

<span data-ttu-id="be42b-122">Рассмотрим следующий код, который настраивает API JSON для приложения:</span><span class="sxs-lookup"><span data-stu-id="be42b-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="be42b-123">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="be42b-123">The preceding code:</span></span>

* <span data-ttu-id="be42b-124">Добавляет конечную точку HTTP GET API с в `/hello/{name:alpha}` качестве шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="be42b-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="be42b-125">При сопоставлении маршрута API считывает `name` значение маршрута из запроса.</span><span class="sxs-lookup"><span data-stu-id="be42b-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="be42b-126">Записывает анонимный тип как ответ JSON с помощью `WriteAsJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="be42b-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="be42b-127">Чтение запроса JSON</span><span class="sxs-lookup"><span data-stu-id="be42b-127">Read JSON request</span></span>

<span data-ttu-id="be42b-128">`HasJsonContentType` и `ReadFromJsonAsync` могут использоваться для десериализации ответа JSON в API-интерфейсе JSON на основе маршрута:</span><span class="sxs-lookup"><span data-stu-id="be42b-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="be42b-129">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="be42b-129">The preceding code:</span></span>

* <span data-ttu-id="be42b-130">Добавляет конечную точку API HTTP POST в `/weather` качестве шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="be42b-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="be42b-131">При сопоставлении маршрута `HasJsonContentType` проверяет тип содержимого запроса.</span><span class="sxs-lookup"><span data-stu-id="be42b-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="be42b-132">Тип содержимого, не относящийся к JSON, возвращает код состояния 415.</span><span class="sxs-lookup"><span data-stu-id="be42b-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="be42b-133">Если тип содержимого — JSON, содержимое запроса десериализуется `ReadFromJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="be42b-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="be42b-134">Настройка сериализации JSON</span><span class="sxs-lookup"><span data-stu-id="be42b-134">Configure JSON serialization</span></span>

<span data-ttu-id="be42b-135">Существует два способа настройки сериализации JSON.</span><span class="sxs-lookup"><span data-stu-id="be42b-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="be42b-136">Параметры сериализации по умолчанию можно настроить с помощью `JsonOptions` `Startup.ConfigureServices` метода.</span><span class="sxs-lookup"><span data-stu-id="be42b-136">Default serialization options can be configured with `JsonOptions` in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="be42b-137">`WriteAsJsonAsync` и `ReadFromJsonAsync` имеют перегрузки, принимающие `JsonSerializerOptions` объект.</span><span class="sxs-lookup"><span data-stu-id="be42b-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a `JsonSerializerOptions` object.</span></span> <span data-ttu-id="be42b-138">Этот `JsonSerializerOptions` объект переопределяет параметры по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="be42b-138">This `JsonSerializerOptions` object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="be42b-139">Аутентификация и авторизация</span><span class="sxs-lookup"><span data-stu-id="be42b-139">Authentication and authorization</span></span>

<span data-ttu-id="be42b-140">Route-to-code поддерживает проверку подлинности и авторизацию.</span><span class="sxs-lookup"><span data-stu-id="be42b-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="be42b-141">Атрибуты, такие как `[Authorize]` и `[AllowAnonymous]` , не могут быть помещены в конечные точки, сопоставленные с делегатом запроса.</span><span class="sxs-lookup"><span data-stu-id="be42b-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="be42b-142">Вместо этого метаданные авторизации добавляются с помощью `RequireAuthorization` `AllowAnonymous` методов расширения и.</span><span class="sxs-lookup"><span data-stu-id="be42b-142">Instead, authorization metadata is added using the `RequireAuthorization` and `AllowAnonymous` extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="be42b-143">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="be42b-143">Dependency injection</span></span>

<span data-ttu-id="be42b-144">[Внедрение зависимостей (DI)](xref:fundamentals/dependency-injection) с помощью конструктора не поддерживается в Route-to-code .</span><span class="sxs-lookup"><span data-stu-id="be42b-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="be42b-145">Веб-API создает контроллер для вас с помощью служб, внедренных в конструктор.</span><span class="sxs-lookup"><span data-stu-id="be42b-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="be42b-146">Тип не создается при выполнении конечной точки, поэтому службы должны быть разрешены вручную.</span><span class="sxs-lookup"><span data-stu-id="be42b-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="be42b-147">API-интерфейсы на основе маршрутов могут использоваться <xref:System.IServiceProvider> для разрешения служб:</span><span class="sxs-lookup"><span data-stu-id="be42b-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="be42b-148">Временные и ограниченные службы времени существования, такие как `DbContext` , должны быть разрешены из [HttpContext. рекуестсервицес](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) внутри делегата запроса конечной точки.</span><span class="sxs-lookup"><span data-stu-id="be42b-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="be42b-149">Службы времени жизни Singleton, такие как `ILogger` , могут быть разрешены из [Иендпоинтраутебуилдер. serviceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span><span class="sxs-lookup"><span data-stu-id="be42b-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="be42b-150">Службы могут разрешаться вне делегатов запросов и совместно использоваться конечными точками.</span><span class="sxs-lookup"><span data-stu-id="be42b-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="be42b-151">API-интерфейсы, использующие DI, должны использовать тип приложения ASP.NET Core, поддерживающий DI.</span><span class="sxs-lookup"><span data-stu-id="be42b-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="be42b-152">Например, ASP.NET Core веб-API.</span><span class="sxs-lookup"><span data-stu-id="be42b-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="be42b-153">Внедрение службы с помощью конструктора контроллера проще, чем ручное разрешение служб.</span><span class="sxs-lookup"><span data-stu-id="be42b-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="be42b-154">Структура проекта API</span><span class="sxs-lookup"><span data-stu-id="be42b-154">API project structure</span></span>

<span data-ttu-id="be42b-155">Интерфейсы API на основе маршрутов не должны размещаться в *Startup.CS*.</span><span class="sxs-lookup"><span data-stu-id="be42b-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="be42b-156">API-интерфейсы могут размещаться в других файлах и сопоставляться при запуске с `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="be42b-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="be42b-157">Такой подход сокращает размер файла конфигурации запуска.</span><span class="sxs-lookup"><span data-stu-id="be42b-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="be42b-158">Рассмотрим следующий статический `UserApi` класс, определяющий `Map` метод.</span><span class="sxs-lookup"><span data-stu-id="be42b-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="be42b-159">Метод сопоставляет API на основе маршрутов.</span><span class="sxs-lookup"><span data-stu-id="be42b-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="be42b-160">В `Startup.Configure` методе `Map` метод и статические методы другого класса вызываются в `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="be42b-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a><span data-ttu-id="be42b-161">Важные отсутствующие компоненты по сравнению с веб-API</span><span class="sxs-lookup"><span data-stu-id="be42b-161">Notable missing features compared to Web API</span></span>

<span data-ttu-id="be42b-162">Route-to-code предназначен для базовых API-интерфейсов JSON.</span><span class="sxs-lookup"><span data-stu-id="be42b-162">Route-to-code is designed for basic JSON APIs.</span></span> <span data-ttu-id="be42b-163">Она не поддерживает многие дополнительные возможности, предоставляемые ASP.NET Core веб-API.</span><span class="sxs-lookup"><span data-stu-id="be42b-163">It doesn't have support for many of the advanced features provided by ASP.NET Core Web API.</span></span>

<span data-ttu-id="be42b-164">Функции, не предоставляемые, Route-to-code включают:</span><span class="sxs-lookup"><span data-stu-id="be42b-164">Features not provided by Route-to-code include:</span></span>

* <span data-ttu-id="be42b-165">Привязка модели</span><span class="sxs-lookup"><span data-stu-id="be42b-165">Model binding</span></span>
* <span data-ttu-id="be42b-166">Проверка модели</span><span class="sxs-lookup"><span data-stu-id="be42b-166">Model validation</span></span>
* <span data-ttu-id="be42b-167">OpenAPI/Swagger</span><span class="sxs-lookup"><span data-stu-id="be42b-167">OpenAPI/Swagger</span></span>
* <span data-ttu-id="be42b-168">Согласование содержимого</span><span class="sxs-lookup"><span data-stu-id="be42b-168">Content negotiation</span></span>
* <span data-ttu-id="be42b-169">Внедрение зависимостей конструктора</span><span class="sxs-lookup"><span data-stu-id="be42b-169">Constructor dependency injection</span></span>
* <span data-ttu-id="be42b-170">`ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))</span><span class="sxs-lookup"><span data-stu-id="be42b-170">`ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))</span></span>

<span data-ttu-id="be42b-171">Рекомендуется использовать [веб-api ASP.NET Core](xref:web-api/index) для создания API, если для него требуются некоторые функции из предыдущего списка.</span><span class="sxs-lookup"><span data-stu-id="be42b-171">Consider using [ASP.NET Core web API](xref:web-api/index) to create an API if it requires some of the features in the preceding list.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be42b-172">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="be42b-172">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
