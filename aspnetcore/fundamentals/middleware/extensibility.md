---
title: Активация ПО промежуточного слоя на основе фабрики в ASP.NET Core
author: rick-anderson
description: В этой статье приводятся сведения о том, как использовать строго типизированное ПО промежуточного слоя с реализацией активации на основе фабрики в ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 893dce119328acaa4ffd3087b94328017f5915ca
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399911"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="dcb32-103">Активация ПО промежуточного слоя на основе фабрики в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dcb32-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dcb32-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> — это точка расширяемости для активации [ПО промежуточного слоя](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="dcb32-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="dcb32-105">Методы расширения <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> проверяют, реализует ли зарегистрированный тип ПО промежуточного слоя <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="dcb32-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="dcb32-106">Если да, то экземпляр <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>, зарегистрированный в контейнере, используется для разрешения реализации <xref:Microsoft.AspNetCore.Http.IMiddleware> вместо стандартной логики активации ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="dcb32-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="dcb32-107">ПО промежуточного слоя регистрируется как [ограниченная (scoped) или временная (temporary) служба](xref:fundamentals/dependency-injection#service-lifetimes) в контейнере служб приложения.</span><span class="sxs-lookup"><span data-stu-id="dcb32-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="dcb32-108">Преимущества:</span><span class="sxs-lookup"><span data-stu-id="dcb32-108">Benefits:</span></span>

* <span data-ttu-id="dcb32-109">Активация по клиентскому запросу (внедрение ограниченных служб)</span><span class="sxs-lookup"><span data-stu-id="dcb32-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="dcb32-110">Строгая типизация ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="dcb32-110">Strong typing of middleware</span></span>

<span data-ttu-id="dcb32-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> активируется по клиентскому запросу (подключению), благодаря чему ограниченные (scoped) службы могут внедряться в конструктор ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="dcb32-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="dcb32-112">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dcb32-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="dcb32-113">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="dcb32-113">IMiddleware</span></span>

<span data-ttu-id="dcb32-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> определяет ПО промежуточного слоя для конвейера запросов приложения.</span><span class="sxs-lookup"><span data-stu-id="dcb32-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="dcb32-115">Метод [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) обрабатывает запрос и возвращает объект <xref:System.Threading.Tasks.Task>, который представляет выполнение ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="dcb32-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="dcb32-116">Стандартная активация ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="dcb32-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="dcb32-117">Активация ПО промежуточного слоя с помощью <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span><span class="sxs-lookup"><span data-stu-id="dcb32-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="dcb32-118">Расширения, создаваемые для ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="dcb32-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="dcb32-119">Невозможна передача объектов в ПО промежуточного слоя, активируемое на основе фабрики с помощью <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span><span class="sxs-lookup"><span data-stu-id="dcb32-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="dcb32-120">Активируемое на основе фабрики ПО промежуточного слоя добавляется во встроенный контейнер в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dcb32-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="dcb32-121">Оба ПО промежуточного слоя регистрируются в конвейере обработки запросов в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="dcb32-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="dcb32-122">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="dcb32-122">IMiddlewareFactory</span></span>

<span data-ttu-id="dcb32-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> предоставляет методы для создания ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="dcb32-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="dcb32-124">Реализация ПО промежуточного слоя на основе фабрики регистрируется в контейнере в виде ограниченной (scoped) службы.</span><span class="sxs-lookup"><span data-stu-id="dcb32-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="dcb32-125">Реализация <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> по умолчанию, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, находится в пакете [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="dcb32-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dcb32-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> — это точка расширяемости для активации [ПО промежуточного слоя](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="dcb32-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="dcb32-127">Методы расширения <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> проверяют, реализует ли зарегистрированный тип ПО промежуточного слоя <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="dcb32-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="dcb32-128">Если да, то экземпляр <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>, зарегистрированный в контейнере, используется для разрешения реализации <xref:Microsoft.AspNetCore.Http.IMiddleware> вместо стандартной логики активации ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="dcb32-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="dcb32-129">ПО промежуточного слоя регистрируется как [ограниченная (scoped) или временная (temporary) служба](xref:fundamentals/dependency-injection#service-lifetimes) в контейнере служб приложения.</span><span class="sxs-lookup"><span data-stu-id="dcb32-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="dcb32-130">Преимущества:</span><span class="sxs-lookup"><span data-stu-id="dcb32-130">Benefits:</span></span>

* <span data-ttu-id="dcb32-131">Активация по клиентскому запросу (внедрение ограниченных служб)</span><span class="sxs-lookup"><span data-stu-id="dcb32-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="dcb32-132">Строгая типизация ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="dcb32-132">Strong typing of middleware</span></span>

<span data-ttu-id="dcb32-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> активируется по клиентскому запросу (подключению), благодаря чему ограниченные (scoped) службы могут внедряться в конструктор ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="dcb32-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="dcb32-134">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dcb32-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="dcb32-135">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="dcb32-135">IMiddleware</span></span>

<span data-ttu-id="dcb32-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> определяет ПО промежуточного слоя для конвейера запросов приложения.</span><span class="sxs-lookup"><span data-stu-id="dcb32-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="dcb32-137">Метод [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) обрабатывает запрос и возвращает объект <xref:System.Threading.Tasks.Task>, который представляет выполнение ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="dcb32-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="dcb32-138">Стандартная активация ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="dcb32-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="dcb32-139">Активация ПО промежуточного слоя с помощью <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span><span class="sxs-lookup"><span data-stu-id="dcb32-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="dcb32-140">Расширения, создаваемые для ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="dcb32-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="dcb32-141">Невозможна передача объектов в ПО промежуточного слоя, активируемое на основе фабрики с помощью <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span><span class="sxs-lookup"><span data-stu-id="dcb32-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="dcb32-142">Активируемое на основе фабрики ПО промежуточного слоя добавляется во встроенный контейнер в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dcb32-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="dcb32-143">Оба ПО промежуточного слоя регистрируются в конвейере обработки запросов в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="dcb32-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="dcb32-144">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="dcb32-144">IMiddlewareFactory</span></span>

<span data-ttu-id="dcb32-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> предоставляет методы для создания ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="dcb32-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="dcb32-146">Реализация ПО промежуточного слоя на основе фабрики регистрируется в контейнере в виде ограниченной (scoped) службы.</span><span class="sxs-lookup"><span data-stu-id="dcb32-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="dcb32-147">Реализация <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> по умолчанию, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, находится в пакете [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="dcb32-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="dcb32-148">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="dcb32-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
