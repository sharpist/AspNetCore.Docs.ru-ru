---
Title: простая Авторизация в ASP.NET Core Автор: Рик-Андерсон (Description: сведения об использовании атрибута авторизации для ограничения доступа к контроллерам и действиям ASP.NET Core.
MS. author: рианде MS. Дата: 10/14/2016 No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' UID: Безопасность/авторизация/простой

---
# <a name="simple-authorization-in-aspnet-core"></a>Простая Авторизация в ASP.NET Core

<a name="security-authorization-simple"></a>

Авторизация в ASP.NET Core контролируется с помощью <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> и различных параметров. В простейшей форме применение `[Authorize]` атрибута к контроллеру, действию или Razor странице ограничивает доступ к этому компоненту для любого пользователя, прошедшего проверку подлинности.

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
