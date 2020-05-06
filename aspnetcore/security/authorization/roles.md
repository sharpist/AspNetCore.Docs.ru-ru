---
title: Авторизация на основе ролей в ASP.NET Core
author: rick-anderson
description: Узнайте, как ограничить доступ к ASP.NET Core контроллеру и действиям, передав роли атрибуту авторизации.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/roles
ms.openlocfilehash: 01d4239377b128f711a110a821e1afea58ca14a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776543"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="a51bc-103">Авторизация на основе ролей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a51bc-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="a51bc-104">Созданное удостоверение может принадлежать одной или нескольким ролям.</span><span class="sxs-lookup"><span data-stu-id="a51bc-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="a51bc-105">Например, Траци может принадлежать администратору и ролям пользователей, пока Скотт может принадлежать только к роли пользователя.</span><span class="sxs-lookup"><span data-stu-id="a51bc-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="a51bc-106">Создание и управление этими ролями зависит от резервного хранилища процесса авторизации.</span><span class="sxs-lookup"><span data-stu-id="a51bc-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="a51bc-107">Роли предоставляются разработчику через метод [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) в классе [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .</span><span class="sxs-lookup"><span data-stu-id="a51bc-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="a51bc-108">Добавление проверок ролей</span><span class="sxs-lookup"><span data-stu-id="a51bc-108">Adding role checks</span></span>

<span data-ttu-id="a51bc-109">Проверки авторизации на основе ролей являются декларативными&mdash;. разработчик внедряет их в код для контроллера или действия в пределах контроллера, указывая роли, членом которых должен являться текущий пользователь для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="a51bc-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="a51bc-110">Например, следующий код ограничивает доступ к любым действиям `AdministrationController` для пользователей, являющихся членами `Administrator` роли:</span><span class="sxs-lookup"><span data-stu-id="a51bc-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="a51bc-111">Можно указать несколько ролей в виде списка с разделителями-запятыми:</span><span class="sxs-lookup"><span data-stu-id="a51bc-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="a51bc-112">Этот контроллер будет доступен только пользователям, которые являются членами `HRManager` роли или `Finance` роли.</span><span class="sxs-lookup"><span data-stu-id="a51bc-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="a51bc-113">При применении нескольких атрибутов пользователь, обращающийся к, должен быть членом всех указанных ролей. в следующем примере необходимо, чтобы пользователь был членом роли `PowerUser` и. `ControlPanelUser`</span><span class="sxs-lookup"><span data-stu-id="a51bc-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="a51bc-114">Можно дополнительно ограничить доступ, применив дополнительные атрибуты авторизации роли на уровне действия.</span><span class="sxs-lookup"><span data-stu-id="a51bc-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

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

<span data-ttu-id="a51bc-115">В `Administrator` предыдущем фрагменте кода члены роли или `PowerUser` роли могут получить доступ к контроллеру и `SetTime` действию, но только члены `Administrator` роли могут получить доступ к `ShutDown` действию.</span><span class="sxs-lookup"><span data-stu-id="a51bc-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="a51bc-116">Можно также заблокировать контроллер, но разрешить доступ к отдельным действиям с анонимным доступом без проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a51bc-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

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

<span data-ttu-id="a51bc-117">Для Razor страниц `AuthorizeAttribute` можно применить один из следующих:</span><span class="sxs-lookup"><span data-stu-id="a51bc-117">For Razor Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="a51bc-118">С помощью [соглашения](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)или</span><span class="sxs-lookup"><span data-stu-id="a51bc-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="a51bc-119">Применение `AuthorizeAttribute` к `PageModel` экземпляру:</span><span class="sxs-lookup"><span data-stu-id="a51bc-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

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
> <span data-ttu-id="a51bc-120">Атрибуты фильтра, включая `AuthorizeAttribute`, могут применяться только к PageModel и не могут применяться к конкретным методам обработчика страницы.</span><span class="sxs-lookup"><span data-stu-id="a51bc-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="a51bc-121">Проверки ролей на основе политик</span><span class="sxs-lookup"><span data-stu-id="a51bc-121">Policy based role checks</span></span>

<span data-ttu-id="a51bc-122">Требования к ролям также можно выразить с помощью нового синтаксиса политики, при котором разработчик регистрирует политику при запуске как часть конфигурации службы авторизации.</span><span class="sxs-lookup"><span data-stu-id="a51bc-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="a51bc-123">Обычно это происходит в `ConfigureServices()` файле *Startup.CS* .</span><span class="sxs-lookup"><span data-stu-id="a51bc-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="a51bc-124">Политики применяются с помощью `Policy` свойства `AuthorizeAttribute` атрибута:</span><span class="sxs-lookup"><span data-stu-id="a51bc-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="a51bc-125">Если необходимо указать несколько допустимых ролей в требовании, их можно указать в качестве параметров `RequireRole` метода:</span><span class="sxs-lookup"><span data-stu-id="a51bc-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="a51bc-126">Этот пример разрешает пользователям `Administrator`, принадлежащим к ролям, `PowerUser` или `BackupAdministrator` .</span><span class="sxs-lookup"><span data-stu-id="a51bc-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-identity"></a><span data-ttu-id="a51bc-127">Добавление служб ролей вIdentity</span><span class="sxs-lookup"><span data-stu-id="a51bc-127">Add Role services to Identity</span></span>

<span data-ttu-id="a51bc-128">Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:</span><span class="sxs-lookup"><span data-stu-id="a51bc-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

