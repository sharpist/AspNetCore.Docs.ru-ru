---
title: Простая Авторизация в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать атрибут авторизации для ограничения доступа к ASP.NET Core контроллерам и действиям.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775639"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="612b9-103">Простая Авторизация в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="612b9-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="612b9-104">Авторизация в MVC контролируется с `AuthorizeAttribute` помощью атрибута и его различных параметров.</span><span class="sxs-lookup"><span data-stu-id="612b9-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="612b9-105">В самом простом случае применение `AuthorizeAttribute` атрибута к контроллеру или действию ограничивает доступ к контроллеру или действию для любого пользователя, прошедшего проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="612b9-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="612b9-106">Например, следующий код ограничивает доступ к любому пользователю, `AccountController` прошедшему проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="612b9-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="612b9-107">Если вы хотите применить авторизацию к действию, а не к контроллеру, примените `AuthorizeAttribute` атрибут к самому действию:</span><span class="sxs-lookup"><span data-stu-id="612b9-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="612b9-108">Теперь доступ к `Logout` функции могут получить только пользователи, прошедшие проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="612b9-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="612b9-109">Можно также использовать `AllowAnonymous` атрибут, чтобы разрешить пользователям без проверки подлинности выполнять отдельные действия.</span><span class="sxs-lookup"><span data-stu-id="612b9-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="612b9-110">Например:</span><span class="sxs-lookup"><span data-stu-id="612b9-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="612b9-111">Это позволит использовать только пользователей, прошедших проверку `AccountController`подлинности, `Login` за исключением действия, доступного для всех, независимо от их состояния с проверкой подлинности или без проверки подлинности или анонимного пользователя.</span><span class="sxs-lookup"><span data-stu-id="612b9-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="612b9-112">`[AllowAnonymous]`обходит все инструкции авторизации.</span><span class="sxs-lookup"><span data-stu-id="612b9-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="612b9-113">Если объединить `[AllowAnonymous]` и любой `[Authorize]` атрибут, `[Authorize]` атрибуты игнорируются.</span><span class="sxs-lookup"><span data-stu-id="612b9-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="612b9-114">Например, при применении `[AllowAnonymous]` на уровне контроллера все `[Authorize]` атрибуты на том же контроллере (или в любом действии в нем) игнорируются.</span><span class="sxs-lookup"><span data-stu-id="612b9-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
