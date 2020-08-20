---
title: Авторизация на основе ролей в ASP.NET Core
author: rick-anderson
description: Узнайте, как ограничить доступ к ASP.NET Core контроллеру и действиям, передав роли атрибуту авторизации.
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
uid: security/authorization/roles
ms.openlocfilehash: 7673bb006c344e6f9baaa3cd99c4bdb4a6fc2862
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635181"
---
# <a name="role-based-authorization-in-aspnet-core"></a>Авторизация на основе ролей в ASP.NET Core

<a name="security-authorization-role-based"></a>

Созданное удостоверение может принадлежать одной или нескольким ролям. Например, Траци может принадлежать администратору и ролям пользователей, пока Скотт может принадлежать только к роли пользователя. Создание и управление этими ролями зависит от резервного хранилища процесса авторизации. Роли предоставляются разработчику через метод [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) в классе [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .

## <a name="adding-role-checks"></a>Добавление проверок ролей

Проверки авторизации на основе ролей являются декларативными. &mdash; разработчик внедряет их в код для контроллера или действия в пределах контроллера, указывая роли, членом которых должен являться текущий пользователь для доступа к запрошенному ресурсу.

Например, следующий код ограничивает доступ к любым действиям `AdministrationController` для пользователей, являющихся членами `Administrator` роли:

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Можно указать несколько ролей в виде списка с разделителями-запятыми:

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Этот контроллер будет доступен только пользователям, которые являются членами `HRManager` роли или `Finance` роли.

При применении нескольких атрибутов пользователь, обращающийся к, должен быть членом всех указанных ролей. в следующем примере необходимо, чтобы пользователь был членом `PowerUser` `ControlPanelUser` роли и.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Можно дополнительно ограничить доступ, применив дополнительные атрибуты авторизации роли на уровне действия.

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

В предыдущем фрагменте кода члены `Administrator` роли или `PowerUser` роли могут получить доступ к контроллеру и `SetTime` действию, но только члены `Administrator` роли могут получить доступ к `ShutDown` действию.

Можно также заблокировать контроллер, но разрешить доступ к отдельным действиям с анонимным доступом без проверки подлинности.

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

Для Razor страниц `AuthorizeAttribute` можно применить один из следующих:

* С помощью [соглашения](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)или
* Применение `AuthorizeAttribute` к `PageModel` экземпляру:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> Атрибуты фильтра, включая `AuthorizeAttribute` , могут применяться только к PageModel и не могут применяться к конкретным методам обработчика страницы.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Проверки ролей на основе политик

Требования к ролям также можно выразить с помощью нового синтаксиса политики, при котором разработчик регистрирует политику при запуске как часть конфигурации службы авторизации. Обычно это происходит в `ConfigureServices()` файле *Startup.CS* .

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

Политики применяются с помощью `Policy` свойства `AuthorizeAttribute` атрибута:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Если необходимо указать несколько допустимых ролей в требовании, их можно указать в качестве параметров `RequireRole` метода:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Этот пример разрешает пользователям, принадлежащим к `Administrator` ролям, `PowerUser` или `BackupAdministrator` .

### <a name="add-role-services-to-no-locidentity"></a>Добавление служб ролей в Identity

Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

