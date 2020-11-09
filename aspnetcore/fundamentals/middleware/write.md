---
title: Написание пользовательского ПО промежуточного слоя ASP.NET Core
author: rick-anderson
description: Узнайте, как написать пользовательское ПО промежуточного слоя ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/18/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/middleware/write
ms.openlocfilehash: 5f33691cbcc00f407fff907ca62547fd80f2aa3c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057470"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="87af6-103">Написание пользовательского ПО промежуточного слоя ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87af6-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="87af6-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="87af6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="87af6-105">ПО промежуточного слоя — это программное обеспечение, выстраиваемое в виде конвейера приложения для обработки запросов и откликов.</span><span class="sxs-lookup"><span data-stu-id="87af6-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="87af6-106">ASP.NET Core предоставляет широкий набор встроенных компонентов ПО промежуточного слоя, но в некоторых случаях может потребоваться написать пользовательское ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="87af6-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="87af6-107">В этом разделе показано, как разработать ПО промежуточного слоя *convention-based*.</span><span class="sxs-lookup"><span data-stu-id="87af6-107">This topic describes how to write *convention-based* middleware.</span></span> <span data-ttu-id="87af6-108">Подход, использующий строгую типизацию и активацию по запросу, описан здесь: <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="87af6-108">For an approach that uses strong typing and per-request activation, see <xref:fundamentals/middleware/extensibility>.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="87af6-109">Класс ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="87af6-109">Middleware class</span></span>

<span data-ttu-id="87af6-110">ПО промежуточного слоя обычно инкапсулируется в класс и предоставляется с помощью метода расширения.</span><span class="sxs-lookup"><span data-stu-id="87af6-110">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="87af6-111">Рассмотрим следующее ПО промежуточного слоя, которое задает язык и региональные параметры для текущего запроса из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="87af6-111">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="87af6-112">Приведенный выше пример кода демонстрирует создание компонента промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="87af6-112">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="87af6-113">Дополнительные сведения о встроенной поддержке локализации в ASP.NET Core см. в разделе <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="87af6-113">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="87af6-114">Протестируйте ПО промежуточного слоя, передав язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="87af6-114">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="87af6-115">Например, выполните запрос `https://localhost:5001/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="87af6-115">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="87af6-116">Следующий код перемещает делегат ПО промежуточного слоя в класс.</span><span class="sxs-lookup"><span data-stu-id="87af6-116">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="87af6-117">Класс ПО промежуточного слоя должен включать следующее:</span><span class="sxs-lookup"><span data-stu-id="87af6-117">The middleware class must include:</span></span>

* <span data-ttu-id="87af6-118">Открытый конструктор с параметром типа <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="87af6-118">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="87af6-119">Открытый метод с именем `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="87af6-119">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="87af6-120">Этот метод должен:</span><span class="sxs-lookup"><span data-stu-id="87af6-120">This method must:</span></span>
  * <span data-ttu-id="87af6-121">вернуть `Task`;</span><span class="sxs-lookup"><span data-stu-id="87af6-121">Return a `Task`.</span></span>
  * <span data-ttu-id="87af6-122">принять первый параметр типа <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="87af6-122">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="87af6-123">Дополнительные параметры для конструктора и `Invoke`/`InvokeAsync` заполняются с помощью [внедрения зависимости](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="87af6-123">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="87af6-124">Зависимости ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="87af6-124">Middleware dependencies</span></span>

<span data-ttu-id="87af6-125">ПО промежуточного слоя должно соответствовать [принципу явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies), предоставляя свои зависимости в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="87af6-125">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="87af6-126">ПО промежуточного слоя создается один раз за *время существования приложения*.</span><span class="sxs-lookup"><span data-stu-id="87af6-126">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="87af6-127">В разделе [Зависимости отдельных запросов](#per-request-middleware-dependencies) приведены сведения о том, как использовать службы совместно с ПО промежуточного слоя внутри запроса.</span><span class="sxs-lookup"><span data-stu-id="87af6-127">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="87af6-128">Компоненты промежуточного слоя могут разрешать свои зависимости, возникшие в результате [внедрения зависимостей](xref:fundamentals/dependency-injection), за счет параметров конструктора.</span><span class="sxs-lookup"><span data-stu-id="87af6-128">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="87af6-129">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) также может принимать дополнительные параметры напрямую.</span><span class="sxs-lookup"><span data-stu-id="87af6-129">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="87af6-130">Зависимости ПО промежуточного слоя для отдельных запросов</span><span class="sxs-lookup"><span data-stu-id="87af6-130">Per-request middleware dependencies</span></span>

<span data-ttu-id="87af6-131">Так как ПО промежуточного слоя создается при запуске приложения, а не для отдельных запросов, службы времени существования *scoped* , используемые конструкторами ПО промежуточного слоя, не являются общими с другими типами, возникшими в результате внедрения зависимостей, в каждом из запросов.</span><span class="sxs-lookup"><span data-stu-id="87af6-131">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="87af6-132">Если необходимо предоставить службу *scoped* для совместного использования ПО промежуточного слоя и другими типами, добавьте ее в сигнатуру метода `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="87af6-132">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="87af6-133">Метод `Invoke` может принимать дополнительные параметры, заполняемые при внедрении зависимостей.</span><span class="sxs-lookup"><span data-stu-id="87af6-133">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

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

<span data-ttu-id="87af6-134">[Параметры времени существования и регистрации](xref:fundamentals/dependency-injection#lifetime-and-registration-options) — раздел содержит полный пример ПО промежуточного слоя со службами, имеющими время существования *scoped* (с заданной областью).</span><span class="sxs-lookup"><span data-stu-id="87af6-134">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped* lifetime services.</span></span>

## <a name="middleware-extension-method"></a><span data-ttu-id="87af6-135">Метод расширения ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="87af6-135">Middleware extension method</span></span>

<span data-ttu-id="87af6-136">Следующий метод расширения предоставляет ПО промежуточного слоя посредством <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span><span class="sxs-lookup"><span data-stu-id="87af6-136">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="87af6-137">Следующий код вызывает ПО промежуточного слоя из `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="87af6-137">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="87af6-138">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="87af6-138">Additional resources</span></span>

* <span data-ttu-id="87af6-139">[Параметры времени существования и регистрации](xref:fundamentals/dependency-injection#lifetime-and-registration-options) — раздел содержит полный пример ПО промежуточного слоя со службами, имеющими время существования *scoped* (с заданной областью), *transient* (временное) и *singleton* (отдельное).</span><span class="sxs-lookup"><span data-stu-id="87af6-139">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped* , *transient* , and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
