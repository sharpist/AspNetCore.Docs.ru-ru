---
title: Написание пользовательского ПО промежуточного слоя ASP.NET Core
author: rick-anderson
description: Узнайте, как написать пользовательское ПО промежуточного слоя ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/write
ms.openlocfilehash: 6852c1831d1f71af7f4fad3288fd6f897dfaa65f
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876196"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="c9a9f-103">Написание пользовательского ПО промежуточного слоя ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9a9f-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="c9a9f-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="c9a9f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c9a9f-105">ПО промежуточного слоя — это программное обеспечение, выстраиваемое в виде конвейера приложения для обработки запросов и откликов.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="c9a9f-106">ASP.NET Core предоставляет широкий набор встроенных компонентов ПО промежуточного слоя, но в некоторых случаях может потребоваться написать пользовательское ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="c9a9f-107">Класс ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="c9a9f-107">Middleware class</span></span>

<span data-ttu-id="c9a9f-108">ПО промежуточного слоя обычно инкапсулируется в класс и предоставляется с помощью метода расширения.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="c9a9f-109">Рассмотрим следующее ПО промежуточного слоя, которое задает язык и региональные параметры для текущего запроса из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="c9a9f-110">Приведенный выше пример кода демонстрирует создание компонента промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="c9a9f-111">Дополнительные сведения о встроенной поддержке локализации в ASP.NET Core см. в разделе <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="c9a9f-112">Протестируйте ПО промежуточного слоя, передав язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-112">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="c9a9f-113">Например, выполните запрос `https://localhost:5001/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-113">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="c9a9f-114">Следующий код перемещает делегат ПО промежуточного слоя в класс.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="c9a9f-115">Класс ПО промежуточного слоя должен включать следующее:</span><span class="sxs-lookup"><span data-stu-id="c9a9f-115">The middleware class must include:</span></span>

* <span data-ttu-id="c9a9f-116">Открытый конструктор с параметром типа <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-116">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="c9a9f-117">Открытый метод с именем `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-117">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="c9a9f-118">Этот метод должен:</span><span class="sxs-lookup"><span data-stu-id="c9a9f-118">This method must:</span></span>
  * <span data-ttu-id="c9a9f-119">вернуть `Task`;</span><span class="sxs-lookup"><span data-stu-id="c9a9f-119">Return a `Task`.</span></span>
  * <span data-ttu-id="c9a9f-120">принять первый параметр типа <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-120">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="c9a9f-121">Дополнительные параметры для конструктора и `Invoke`/`InvokeAsync` заполняются с помощью [внедрения зависимости](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c9a9f-121">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="c9a9f-122">Зависимости ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="c9a9f-122">Middleware dependencies</span></span>

<span data-ttu-id="c9a9f-123">ПО промежуточного слоя должно соответствовать [принципу явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies), предоставляя свои зависимости в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-123">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="c9a9f-124">ПО промежуточного слоя создается один раз за *время существования приложения*.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-124">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="c9a9f-125">В разделе [Зависимости отдельных запросов](#per-request-middleware-dependencies) приведены сведения о том, как использовать службы совместно с ПО промежуточного слоя внутри запроса.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-125">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="c9a9f-126">Компоненты промежуточного слоя могут разрешать свои зависимости, возникшие в результате [внедрения зависимостей](xref:fundamentals/dependency-injection), за счет параметров конструктора.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-126">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="c9a9f-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) также может принимать дополнительные параметры напрямую.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="c9a9f-128">Зависимости ПО промежуточного слоя для отдельных запросов</span><span class="sxs-lookup"><span data-stu-id="c9a9f-128">Per-request middleware dependencies</span></span>

<span data-ttu-id="c9a9f-129">Так как ПО промежуточного слоя создается при запуске приложения, а не для отдельных запросов, службы времени существования *scoped*, используемые конструкторами ПО промежуточного слоя, не являются общими с другими типами, возникшими в результате внедрения зависимостей, в каждом из запросов.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-129">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="c9a9f-130">Если необходимо предоставить службу *scoped* для совместного использования ПО промежуточного слоя и другими типами, добавьте ее в сигнатуру метода `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-130">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="c9a9f-131">Метод `Invoke` может принимать дополнительные параметры, заполняемые при внедрении зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-131">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="c9a9f-132">Метод расширения ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="c9a9f-132">Middleware extension method</span></span>

<span data-ttu-id="c9a9f-133">Следующий метод расширения предоставляет ПО промежуточного слоя посредством <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span><span class="sxs-lookup"><span data-stu-id="c9a9f-133">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="c9a9f-134">Следующий код вызывает ПО промежуточного слоя из `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c9a9f-134">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="c9a9f-135">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c9a9f-135">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
