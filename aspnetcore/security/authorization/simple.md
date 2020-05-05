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
# <a name="simple-authorization-in-aspnet-core"></a>Простая Авторизация в ASP.NET Core

<a name="security-authorization-simple"></a>

Авторизация в MVC контролируется с `AuthorizeAttribute` помощью атрибута и его различных параметров. В самом простом случае применение `AuthorizeAttribute` атрибута к контроллеру или действию ограничивает доступ к контроллеру или действию для любого пользователя, прошедшего проверку подлинности.

Например, следующий код ограничивает доступ к любому пользователю, `AccountController` прошедшему проверку подлинности.

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

Если вы хотите применить авторизацию к действию, а не к контроллеру, примените `AuthorizeAttribute` атрибут к самому действию:

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

Теперь доступ к `Logout` функции могут получить только пользователи, прошедшие проверку подлинности.

Можно также использовать `AllowAnonymous` атрибут, чтобы разрешить пользователям без проверки подлинности выполнять отдельные действия. Например:

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

Это позволит использовать только пользователей, прошедших проверку `AccountController`подлинности, `Login` за исключением действия, доступного для всех, независимо от их состояния с проверкой подлинности или без проверки подлинности или анонимного пользователя.

> [!WARNING]
> `[AllowAnonymous]`обходит все инструкции авторизации. Если объединить `[AllowAnonymous]` и любой `[Authorize]` атрибут, `[Authorize]` атрибуты игнорируются. Например, при применении `[AllowAnonymous]` на уровне контроллера все `[Authorize]` атрибуты на том же контроллере (или в любом действии в нем) игнорируются.
