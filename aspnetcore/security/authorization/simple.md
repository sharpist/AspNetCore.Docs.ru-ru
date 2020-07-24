---
title: Простая Авторизация в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать атрибут авторизации для ограничения доступа к ASP.NET Core контроллерам и действиям.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/simple
ms.openlocfilehash: 09514032349d489b73d5bb785f11e44ca18b169c
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160246"
---
# <a name="simple-authorization-in-aspnet-core"></a>Простая Авторизация в ASP.NET Core

<a name="security-authorization-simple"></a>

Авторизация в ASP.NET Core контролируется с помощью <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> и различных параметров. В простейшей форме применение `[Authorize]` атрибута к контроллеру, действию или :::no-loc(Razor)::: странице ограничивает доступ к этому компоненту для любого пользователя, прошедшего проверку подлинности.

Например, следующий код ограничивает доступ к `AccountController` любому пользователю, прошедшему проверку подлинности.

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

Теперь доступ к функции могут получить только пользователи, прошедшие проверку подлинности `Logout` .

Можно также использовать атрибут, `AllowAnonymous` чтобы разрешить пользователям без проверки подлинности выполнять отдельные действия. Пример:

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

Это позволит использовать только пользователей, прошедших проверку подлинности `AccountController` , за исключением `Login` действия, доступного для всех, независимо от их состояния с проверкой подлинности или без проверки подлинности или анонимного пользователя.

> [!WARNING]
> `[AllowAnonymous]`обходит все инструкции авторизации. Если объединить `[AllowAnonymous]` и любой `[Authorize]` атрибут, `[Authorize]` атрибуты игнорируются. Например, при применении `[AllowAnonymous]` на уровне контроллера все `[Authorize]` атрибуты на том же контроллере (или в любом действии в нем) игнорируются.

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-no-locrazor-pages"></a>Атрибут authorize и :::no-loc(Razor)::: Pages

<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ***Не*** может применяться к :::no-loc(Razor)::: обработчикам страниц. Например, `[Authorize]` нельзя применить к `OnGet` , или к `OnPost` любому другому обработчику страницы. Рекомендуется использовать ASP.NET Core контроллер MVC для страниц с различными требованиями к авторизации для разных обработчиков.

Для применения авторизации к :::no-loc(Razor)::: методам обработчика страниц можно использовать следующие два подхода:

* Используйте отдельные страницы для обработчиков страниц, для которых необходима другая авторизация. Общее содержимое перемещено в одно или несколько [частичных представлений](xref:mvc/views/partial). По возможности рекомендуется использовать этот подход.
* Для содержимого, которое должно совместно использовать общую страницу, напишите фильтр, выполняющий авторизацию как часть [иасинкпажефилтер. онпажехандлерселектионасинк](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A). Этот подход демонстрируется в проекте [пажехандлераус](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub:
  * [Аусоризеиндекспажехандлерфилтер](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) реализует фильтр авторизации:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]

  * Атрибут [[аусоризепажехандлер]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) применяется к `OnGet` обработчику страницы:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]

> [!WARNING]
> Пример подхода [пажехандлераус](https://github.com/pranavkm/PageHandlerAuth) ***не:***
> * Составьте атрибуты авторизации, применяемые к странице, модели страницы или глобально. Составление атрибутов авторизации приводит к многократному выполнению проверки подлинности и авторизации, когда `AuthorizeAttribute` `AuthorizeFilter` на страницу также применяются еще один экземпляр или экземпляры.
> * Работа в сочетании с остальными ASP.NET Core системы проверки подлинности и авторизации. Необходимо убедиться, что использование этого подхода правильно работает для вашего приложения.

Поддержка `AuthorizeAttribute` обработчиков страниц в не планируется :::no-loc(Razor)::: . 
