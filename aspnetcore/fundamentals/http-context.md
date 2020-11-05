---
title: Доступ к HttpContext в ASP.NET Core
author: coderandhiker
description: Сведения о получении доступа к HttpContext в ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
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
uid: fundamentals/httpcontext
ms.openlocfilehash: f51814d25d4e87d166c7b587306da6c77dbf047e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059979"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Доступ к HttpContext в ASP.NET Core

Приложения ASP.NET Core получают доступ к `HttpContext` через интерфейс <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> и его реализацию по умолчанию — <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>. `IHttpContextAccessor` требуется использовать только при необходимости доступа к `HttpContext` внутри службы.

## <a name="use-httpcontext-from-no-locrazor-pages"></a>Использование HttpContext через Razor Pages

Класс Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> предоставляет свойство <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>.

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

## <a name="use-httpcontext-from-a-no-locrazor-view"></a>Использование HttpContext через представление Razor

Представления Razor предоставляют `HttpContext` непосредственно через свойство [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context). В следующем примере имя текущего пользователя в приложении интрасети извлекается с использованием проверки подлинности Windows:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Использование HttpContext через контроллер

Контроллеры предоставляют свойство [ControllerBase.HttpContex](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext):

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

## <a name="use-httpcontext-from-middleware"></a>Использование HttpContext через ПО промежуточного слоя

При работе с компонентами пользовательского ПО промежуточного слоя свойство `HttpContext` передается в метод `Invoke` или `InvokeAsync` и доступ к нему может осуществляться при настройке ПО промежуточного слоя:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Использование HttpContext через пользовательские компоненты

Для других компонентов платформы и пользовательских компонентов, которым требуется доступ к `HttpContext`, рекомендуется зарегистрировать зависимость с помощью встроенного контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection). Контейнер внедрения зависимостей предоставляет свойство `IHttpContextAccessor` для всех классов, которые объявляют его как зависимость в своих конструкторах:

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

В следующем примере:

* `UserRepository` объявляет зависимость от `IHttpContextAccessor`.
* Зависимость предоставляется, если внедрение зависимостей разрешает цепочку зависимостей и создает экземпляр класса `UserRepository`.

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

## <a name="httpcontext-access-from-a-background-thread"></a>Доступ к HttpContext из фонового потока

`HttpContext` не является потокобезопасным. Чтение или запись свойств `HttpContext` за пределами обработки запроса может привести к <xref:System.NullReferenceException>.

> [!NOTE]
> Если приложение генерирует случайные ошибки `NullReferenceException`, просмотрите части кода, которые запускают фоновую обработку или продолжают обработку после выполнения запроса. Ищите ошибки, например, как определение метода контроллера в качестве `async void`.

Для безопасного выполнения фоновой работы с данными `HttpContext` сделайте следующее.

* Скопируйте необходимые данные во время обработки запроса.
* Передайте скопированные данные в фоновую задачу.

Чтобы избежать небезопасного кода, никогда не передавайте `HttpContext` в метод, который выполняет фоновую работу. Вместо этого передайте нужные данные. В приведенном ниже примере метод `SendEmailCore` вызывается для запуска отправки сообщения электронной почты. `correlationId` передается в `SendEmailCore`, а не в `HttpContext`. Код не ждет завершения выполнения метода `SendEmailCore`:

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

## <a name="no-locblazor-and-shared-state"></a>Blazor и общее состояние

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]
