---
title: Доступ к HttpContext в ASP.NET Core
author: coderandhiker
description: Сведения о получении доступа к HttpContext в ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/httpcontext
ms.openlocfilehash: 2b8ac1d6c6cdeee14b74c5b14206bff51982c711
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017250"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="37eb7-103">Доступ к HttpContext в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37eb7-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="37eb7-104">Приложения ASP.NET Core получают доступ к `HttpContext` через интерфейс <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> и его реализацию по умолчанию — <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span><span class="sxs-lookup"><span data-stu-id="37eb7-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="37eb7-105">`IHttpContextAccessor` требуется использовать только при необходимости доступа к `HttpContext` внутри службы.</span><span class="sxs-lookup"><span data-stu-id="37eb7-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-no-locrazor-pages"></a><span data-ttu-id="37eb7-106">Использование HttpContext через Razor Pages</span><span class="sxs-lookup"><span data-stu-id="37eb7-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="37eb7-107">Класс Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> предоставляет свойство <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="37eb7-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-no-locrazor-view"></a><span data-ttu-id="37eb7-108">Использование HttpContext через представление Razor</span><span class="sxs-lookup"><span data-stu-id="37eb7-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="37eb7-109">Представления Razor предоставляют `HttpContext` непосредственно через свойство [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context).</span><span class="sxs-lookup"><span data-stu-id="37eb7-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="37eb7-110">В следующем примере имя текущего пользователя в приложении интрасети извлекается с использованием проверки подлинности Windows:</span><span class="sxs-lookup"><span data-stu-id="37eb7-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="37eb7-111">Использование HttpContext через контроллер</span><span class="sxs-lookup"><span data-stu-id="37eb7-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="37eb7-112">Контроллеры предоставляют свойство [ControllerBase.HttpContex](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext):</span><span class="sxs-lookup"><span data-stu-id="37eb7-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="37eb7-113">Использование HttpContext через ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="37eb7-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="37eb7-114">При работе с компонентами пользовательского ПО промежуточного слоя свойство `HttpContext` передается в метод `Invoke` или `InvokeAsync` и доступ к нему может осуществляться при настройке ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="37eb7-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="37eb7-115">Использование HttpContext через пользовательские компоненты</span><span class="sxs-lookup"><span data-stu-id="37eb7-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="37eb7-116">Для других компонентов платформы и пользовательских компонентов, которым требуется доступ к `HttpContext`, рекомендуется зарегистрировать зависимость с помощью встроенного контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="37eb7-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="37eb7-117">Контейнер внедрения зависимостей предоставляет свойство `IHttpContextAccessor` для всех классов, которые объявляют его как зависимость в своих конструкторах:</span><span class="sxs-lookup"><span data-stu-id="37eb7-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

<span data-ttu-id="37eb7-118">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="37eb7-118">In the following example:</span></span>

* <span data-ttu-id="37eb7-119">`UserRepository` объявляет зависимость от `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="37eb7-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="37eb7-120">Зависимость предоставляется, если внедрение зависимостей разрешает цепочку зависимостей и создает экземпляр класса `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="37eb7-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="37eb7-121">Доступ к HttpContext из фонового потока</span><span class="sxs-lookup"><span data-stu-id="37eb7-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="37eb7-122">`HttpContext` не является потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="37eb7-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="37eb7-123">Чтение или запись свойств `HttpContext` за пределами обработки запроса может привести к <xref:System.NullReferenceException>.</span><span class="sxs-lookup"><span data-stu-id="37eb7-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="37eb7-124">Если приложение генерирует случайные ошибки `NullReferenceException`, просмотрите части кода, которые запускают фоновую обработку или продолжают обработку после выполнения запроса.</span><span class="sxs-lookup"><span data-stu-id="37eb7-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="37eb7-125">Ищите ошибки, например, как определение метода контроллера в качестве `async void`.</span><span class="sxs-lookup"><span data-stu-id="37eb7-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="37eb7-126">Для безопасного выполнения фоновой работы с данными `HttpContext` сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="37eb7-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="37eb7-127">Скопируйте необходимые данные во время обработки запроса.</span><span class="sxs-lookup"><span data-stu-id="37eb7-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="37eb7-128">Передайте скопированные данные в фоновую задачу.</span><span class="sxs-lookup"><span data-stu-id="37eb7-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="37eb7-129">Чтобы избежать небезопасного кода, никогда не передавайте `HttpContext` в метод, который выполняет фоновую работу.</span><span class="sxs-lookup"><span data-stu-id="37eb7-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="37eb7-130">Вместо этого передайте нужные данные.</span><span class="sxs-lookup"><span data-stu-id="37eb7-130">Pass the required data instead.</span></span> <span data-ttu-id="37eb7-131">В приведенном ниже примере метод `SendEmailCore` вызывается для запуска отправки сообщения электронной почты.</span><span class="sxs-lookup"><span data-stu-id="37eb7-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="37eb7-132">`correlationId` передается в `SendEmailCore`, а не в `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="37eb7-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="37eb7-133">Код не ждет завершения выполнения метода `SendEmailCore`:</span><span class="sxs-lookup"><span data-stu-id="37eb7-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
```

## <a name="no-locblazor-and-shared-state"></a><span data-ttu-id="37eb7-134">Blazor и общее состояние</span><span class="sxs-lookup"><span data-stu-id="37eb7-134">Blazor and shared state</span></span>

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]
