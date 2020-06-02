---
<span data-ttu-id="7d018-101">Title: простая Авторизация в ASP.NET Core Автор: Рик-Андерсон (Description: сведения об использовании атрибута авторизации для ограничения доступа к контроллерам и действиям ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7d018-101">title: Simple authorization in ASP.NET Core author: rick-anderson description: Learn how to use the Authorize attribute to restrict access to ASP.NET Core controllers and actions.</span></span>
<span data-ttu-id="7d018-102">MS. author: рианде MS. Дата: 10/14/2016 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7d018-102">ms.author: riande ms.date: 10/14/2016 no-loc:</span></span>
- <span data-ttu-id="7d018-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7d018-103">'Blazor'</span></span>
- <span data-ttu-id="7d018-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7d018-104">'Identity'</span></span>
- <span data-ttu-id="7d018-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7d018-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="7d018-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7d018-106">'Razor'</span></span>
- <span data-ttu-id="7d018-107">' SignalR ' UID: Безопасность/авторизация/простой</span><span class="sxs-lookup"><span data-stu-id="7d018-107">'SignalR' uid: security/authorization/simple</span></span>

---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="7d018-108">Простая Авторизация в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d018-108">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="7d018-109">Авторизация в ASP.NET Core контролируется с помощью <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> и различных параметров.</span><span class="sxs-lookup"><span data-stu-id="7d018-109">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="7d018-110">В простейшей форме применение `[Authorize]` атрибута к контроллеру, действию или Razor странице ограничивает доступ к этому компоненту для любого пользователя, прошедшего проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="7d018-110">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="7d018-111">Например, следующий код ограничивает доступ к `AccountController` любому пользователю, прошедшему проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="7d018-111">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="7d018-112">Если вы хотите применить авторизацию к действию, а не к контроллеру, примените `AuthorizeAttribute` атрибут к самому действию:</span><span class="sxs-lookup"><span data-stu-id="7d018-112">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="7d018-113">Теперь доступ к функции могут получить только пользователи, прошедшие проверку подлинности `Logout` .</span><span class="sxs-lookup"><span data-stu-id="7d018-113">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="7d018-114">Можно также использовать атрибут, `AllowAnonymous` чтобы разрешить пользователям без проверки подлинности выполнять отдельные действия.</span><span class="sxs-lookup"><span data-stu-id="7d018-114">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="7d018-115">Пример:</span><span class="sxs-lookup"><span data-stu-id="7d018-115">For example:</span></span>

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

<span data-ttu-id="7d018-116">Это позволит использовать только пользователей, прошедших проверку подлинности `AccountController` , за исключением `Login` действия, доступного для всех, независимо от их состояния с проверкой подлинности или без проверки подлинности или анонимного пользователя.</span><span class="sxs-lookup"><span data-stu-id="7d018-116">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="7d018-117">`[AllowAnonymous]`обходит все инструкции авторизации.</span><span class="sxs-lookup"><span data-stu-id="7d018-117">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="7d018-118">Если объединить `[AllowAnonymous]` и любой `[Authorize]` атрибут, `[Authorize]` атрибуты игнорируются.</span><span class="sxs-lookup"><span data-stu-id="7d018-118">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="7d018-119">Например, при применении `[AllowAnonymous]` на уровне контроллера все `[Authorize]` атрибуты на том же контроллере (или в любом действии в нем) игнорируются.</span><span class="sxs-lookup"><span data-stu-id="7d018-119">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
