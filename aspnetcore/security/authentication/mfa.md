---
title: Многофакторная идентификация в ASP.NET Core
author: damienbod
description: Узнайте, как настроить многофакторную проверку подлинности (MFA) в ASP.NET Core приложении.
monikerRange: '>= aspnetcore-3.1'
ms.author: rick-anderson
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/mfa
ms.openlocfilehash: e2f34a72515a700223ce83ce6ec8b55020599ab0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767425"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="33e9a-103">Многофакторная идентификация в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33e9a-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="33e9a-104">По [(Damien Бауден](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="33e9a-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

<span data-ttu-id="33e9a-105">Многофакторная идентификация (MFA) — это процесс, в ходе которого пользователь запрашивается во время события входа, чтобы получить дополнительные формы идентификации.</span><span class="sxs-lookup"><span data-stu-id="33e9a-105">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="33e9a-106">Этот запрос может быть использован для ввода кода из целлфоне, использования ключа FIDO2 или для обеспечения сканирования отпечатков пальцев.</span><span class="sxs-lookup"><span data-stu-id="33e9a-106">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="33e9a-107">Если требуется Вторая форма проверки подлинности, Безопасность улучшена.</span><span class="sxs-lookup"><span data-stu-id="33e9a-107">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="33e9a-108">Злоумышленник не может легко получить или продублировать дополнительный фактор.</span><span class="sxs-lookup"><span data-stu-id="33e9a-108">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="33e9a-109">В этой статье рассматриваются следующие вопросы:</span><span class="sxs-lookup"><span data-stu-id="33e9a-109">This article covers the following areas:</span></span>

* <span data-ttu-id="33e9a-110">Что такое MFA и какие потоки MFA рекомендуются</span><span class="sxs-lookup"><span data-stu-id="33e9a-110">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="33e9a-111">Настройка MFA для страниц администрирования с помощью ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="33e9a-111">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="33e9a-112">Отправка требования к входу MFA в OpenID Connect Connect Server</span><span class="sxs-lookup"><span data-stu-id="33e9a-112">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="33e9a-113">Принудительное ASP.NET Core OpenID Connect Connect Client для требования MFA</span><span class="sxs-lookup"><span data-stu-id="33e9a-113">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="33e9a-114">MFA, 2FA</span><span class="sxs-lookup"><span data-stu-id="33e9a-114">MFA, 2FA</span></span>

<span data-ttu-id="33e9a-115">MFA требует по крайней мере два типа цветопробы для удостоверения, такого как известное, что вы владеете или биометрической проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="33e9a-115">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="33e9a-116">Двухфакторная проверка подлинности (2FA) похожа на подмножество MFA, но в этом случае в MFA может потребоваться два или более фактора для подтверждения личности.</span><span class="sxs-lookup"><span data-stu-id="33e9a-116">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="33e9a-117">MFA TOTP (алгоритм одноразового пароля на основе времени)</span><span class="sxs-lookup"><span data-stu-id="33e9a-117">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="33e9a-118">MFA с использованием TOTP является поддерживаемой реализацией Identityс помощью ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="33e9a-118">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="33e9a-119">Это можно использовать вместе со всеми совместимыми приложениями проверки подлинности, в том числе:</span><span class="sxs-lookup"><span data-stu-id="33e9a-119">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="33e9a-120">Приложение Microsoft Authenticator</span><span class="sxs-lookup"><span data-stu-id="33e9a-120">Microsoft Authenticator App</span></span>
* <span data-ttu-id="33e9a-121">Приложение Google Authenticator</span><span class="sxs-lookup"><span data-stu-id="33e9a-121">Google Authenticator App</span></span>

<span data-ttu-id="33e9a-122">Сведения о реализации см. по следующей ссылке:</span><span class="sxs-lookup"><span data-stu-id="33e9a-122">See the following link for implementation details:</span></span>

[<span data-ttu-id="33e9a-123">Включение создания QR-кода для приложений TOTP Authenticator в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33e9a-123">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="33e9a-124">FIDO2 MFA или безпарольный</span><span class="sxs-lookup"><span data-stu-id="33e9a-124">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="33e9a-125">FIDO2 в настоящее время:</span><span class="sxs-lookup"><span data-stu-id="33e9a-125">FIDO2 is currently:</span></span>

* <span data-ttu-id="33e9a-126">Самый безопасный способ достижения MFA.</span><span class="sxs-lookup"><span data-stu-id="33e9a-126">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="33e9a-127">Единственный поток MFA, защищающий от фишинговых атак.</span><span class="sxs-lookup"><span data-stu-id="33e9a-127">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="33e9a-128">В настоящее время ASP.NET Core не поддерживает FIDO2 напрямую.</span><span class="sxs-lookup"><span data-stu-id="33e9a-128">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="33e9a-129">FIDO2 можно использовать для MFA или беспарольных потоков.</span><span class="sxs-lookup"><span data-stu-id="33e9a-129">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="33e9a-130">Azure Active Directory обеспечивает поддержку для FIDO2 и безпарольных потоков.</span><span class="sxs-lookup"><span data-stu-id="33e9a-130">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="33e9a-131">Дополнительные сведения см. в разделе [Параметры проверки подлинности](/azure/active-directory/authentication/concept-authentication-passwordless), не имеющие пароля, для Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="33e9a-131">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="33e9a-132">SMS ДЛЯ MFA</span><span class="sxs-lookup"><span data-stu-id="33e9a-132">MFA SMS</span></span>

<span data-ttu-id="33e9a-133">MFA с SMS значительно повышает безопасность с помощью проверки пароля (один фактор).</span><span class="sxs-lookup"><span data-stu-id="33e9a-133">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="33e9a-134">Однако использовать SMS в качестве второго фактора больше не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="33e9a-134">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="33e9a-135">Для этого типа реализации существует слишком много известных векторов атак.</span><span class="sxs-lookup"><span data-stu-id="33e9a-135">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="33e9a-136">Рекомендации NIST</span><span class="sxs-lookup"><span data-stu-id="33e9a-136">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-aspnet-core-identity"></a><span data-ttu-id="33e9a-137">Настройка MFA для страниц администрирования с помощью ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="33e9a-137">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="33e9a-138">MFA можно принудительно заставить пользователей получать доступ к конфиденциальным страницам Identity в ASP.NET Core приложении.</span><span class="sxs-lookup"><span data-stu-id="33e9a-138">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="33e9a-139">Это может быть полезно для приложений, где существуют разные уровни доступа для разных удостоверений.</span><span class="sxs-lookup"><span data-stu-id="33e9a-139">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="33e9a-140">Например, пользователи могут просматривать данные профиля с помощью имени входа с паролем, но для доступа к административным страницам администратору потребуется использовать MFA.</span><span class="sxs-lookup"><span data-stu-id="33e9a-140">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="33e9a-141">Расширение имени входа с помощью утверждения MFA</span><span class="sxs-lookup"><span data-stu-id="33e9a-141">Extend the login with an MFA claim</span></span>

<span data-ttu-id="33e9a-142">Демонстрационный код — это программа установки, Identity использующая ASP.NET Core со страницами и Razor .</span><span class="sxs-lookup"><span data-stu-id="33e9a-142">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="33e9a-143">`AddIdentity` Метод используется вместо `AddDefaultIdentity` одного, поэтому `IUserClaimsPrincipalFactory` реализация может использоваться для добавления утверждений в удостоверение после успешного входа.</span><span class="sxs-lookup"><span data-stu-id="33e9a-143">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

<span data-ttu-id="33e9a-144">`AdditionalUserClaimsPrincipalFactory` Класс добавляет `amr` утверждение в утверждения пользователя только после успешного входа.</span><span class="sxs-lookup"><span data-stu-id="33e9a-144">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="33e9a-145">Значение утверждения считывается из базы данных.</span><span class="sxs-lookup"><span data-stu-id="33e9a-145">The claim's value is read from the database.</span></span> <span data-ttu-id="33e9a-146">Утверждение добавляется здесь, так как пользователь должен получить доступ к более защищенному представлению, если удостоверение было зарегистрировано с помощью MFA.</span><span class="sxs-lookup"><span data-stu-id="33e9a-146">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="33e9a-147">Если представление базы данных считывается непосредственно из базы данных, а не с помощью утверждения, можно получить доступ к представлению без MFA непосредственно после активации MFA.</span><span class="sxs-lookup"><span data-stu-id="33e9a-147">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

<span data-ttu-id="33e9a-148">Поскольку программа Identity установки службы изменилась в `Startup` классе, необходимо обновить макеты Identity .</span><span class="sxs-lookup"><span data-stu-id="33e9a-148">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="33e9a-149">Формирование шаблонов Identity страниц в приложении.</span><span class="sxs-lookup"><span data-stu-id="33e9a-149">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="33e9a-150">Определите макет в файле \* Identity/аккаунт/манаже/_layout. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="33e9a-150">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="33e9a-151">Также назначайте макет для всех страниц управления со Identity страниц:</span><span class="sxs-lookup"><span data-stu-id="33e9a-151">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="33e9a-152">Проверка требования MFA на странице "Администрирование"</span><span class="sxs-lookup"><span data-stu-id="33e9a-152">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="33e9a-153">На странице Razor администрирования проверяется, что пользователь выполнил вход с помощью mfa.</span><span class="sxs-lookup"><span data-stu-id="33e9a-153">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="33e9a-154">В `OnGet` методе удостоверение используется для доступа к утверждениям пользователя.</span><span class="sxs-lookup"><span data-stu-id="33e9a-154">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="33e9a-155">`amr` Утверждение проверяется на наличие значения `mfa`.</span><span class="sxs-lookup"><span data-stu-id="33e9a-155">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="33e9a-156">Если в удостоверении отсутствует это утверждение или оно `false`имеет значение, то страница переправляется на страницу включение mfa.</span><span class="sxs-lookup"><span data-stu-id="33e9a-156">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="33e9a-157">Это возможно, так как пользователь уже вошел в систему, но без MFA.</span><span class="sxs-lookup"><span data-stu-id="33e9a-157">This is possible because the user has logged in already, but without MFA.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="33e9a-158">Логика пользовательского интерфейса для переключения сведений для входа пользователя</span><span class="sxs-lookup"><span data-stu-id="33e9a-158">UI logic to toggle user login information</span></span>

<span data-ttu-id="33e9a-159">Политика авторизации была добавлена при запуске.</span><span class="sxs-lookup"><span data-stu-id="33e9a-159">An authorization policy was added at startup.</span></span> <span data-ttu-id="33e9a-160">Политика требует `amr` утверждения со значением `mfa`.</span><span class="sxs-lookup"><span data-stu-id="33e9a-160">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="33e9a-161">Затем эту политику можно использовать в `_Layout` представлении, чтобы показать или скрыть меню **администрирования** с предупреждением:</span><span class="sxs-lookup"><span data-stu-id="33e9a-161">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="33e9a-162">Если удостоверение было зарегистрировано с помощью MFA, меню **Admin** отображается без предупреждения о подсказке.</span><span class="sxs-lookup"><span data-stu-id="33e9a-162">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="33e9a-163">Когда пользователь вошел в систему без MFA, меню **Администратор (не включено)** отображается вместе с подсказкой, которая информирует пользователя (объяснение предупреждения).</span><span class="sxs-lookup"><span data-stu-id="33e9a-163">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

<span data-ttu-id="33e9a-164">Если пользователь входит в систему без MFA, отображается предупреждение:</span><span class="sxs-lookup"><span data-stu-id="33e9a-164">If the user logs in without MFA, the warning is displayed:</span></span>

![Проверка подлинности администратора MFA](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="33e9a-166">Пользователь перенаправляется в представление включения MFA при щелчке ссылки **администратора** :</span><span class="sxs-lookup"><span data-stu-id="33e9a-166">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![Администратор активирует аутентификацию MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="33e9a-168">Отправка требования к входу MFA в OpenID Connect Connect Server</span><span class="sxs-lookup"><span data-stu-id="33e9a-168">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="33e9a-169">`acr_values` Параметр можно использовать для передачи `mfa` требуемого значения с клиента на сервер в запросе проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="33e9a-169">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="33e9a-170">Чтобы `acr_values` это работало, параметр должен быть обработан на сервере Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="33e9a-170">The `acr_values` parameter needs to be handled on the Open ID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="33e9a-171">OpenID Connect подключение клиента ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33e9a-171">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="33e9a-172">Клиентское Razor приложение с открытым кодом для ASP.NET Core страниц использует `AddOpenIdConnect` метод для входа на сервер Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="33e9a-172">The ASP.NET Core Razor Pages Open ID Connect client app uses the `AddOpenIdConnect` method to login to the Open ID Connect server.</span></span> <span data-ttu-id="33e9a-173">`acr_values` Параметр задается со `mfa` значением и отправляется с запросом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="33e9a-173">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="33e9a-174">`OpenIdConnectEvents` Используется для добавления этого.</span><span class="sxs-lookup"><span data-stu-id="33e9a-174">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="33e9a-175">Рекомендуемые `acr_values` значения параметров см. в разделе [Ссылочные значения метода проверки подлинности](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span><span class="sxs-lookup"><span data-stu-id="33e9a-175">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-identityserver-4-server-with-aspnet-core-identity"></a><span data-ttu-id="33e9a-176">Пример OpenID Connect Connect IdentityServer 4 Server with ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="33e9a-176">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="33e9a-177">На сервере OpenID Connect Connect, который реализуется с помощью ASP.NET Core Identity с представлениями MVC, создается новое представление с именем *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="33e9a-177">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="33e9a-178">Представление:</span><span class="sxs-lookup"><span data-stu-id="33e9a-178">The view:</span></span>

* <span data-ttu-id="33e9a-179">Показывает, поступает ли объект Identity из приложения, для которого требуется MFA, но пользователь не Identityактивировал его в.</span><span class="sxs-lookup"><span data-stu-id="33e9a-179">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="33e9a-180">Информирует пользователя и добавляет ссылку для активации.</span><span class="sxs-lookup"><span data-stu-id="33e9a-180">Informs the user and adds a link to activate this.</span></span>

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="33e9a-181">В `Login` методе реализация `IIdentityServerInteractionService` `_interaction` интерфейса используется для доступа к параметрам запроса Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="33e9a-181">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the Open ID Connect request parameters.</span></span> <span data-ttu-id="33e9a-182">Доступ `acr_values` к параметру осуществляется с `AcrValues` помощью свойства.</span><span class="sxs-lookup"><span data-stu-id="33e9a-182">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="33e9a-183">После отправки клиентом `mfa` значения SET его можно проверить.</span><span class="sxs-lookup"><span data-stu-id="33e9a-183">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="33e9a-184">Если требуется MFA и пользователь в ASP.NET Core Identity включил MFA, то вход будет продолжен.</span><span class="sxs-lookup"><span data-stu-id="33e9a-184">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="33e9a-185">Если для пользователя не включено MFA, пользователь перенаправляется к пользовательскому представлению *ErrorEnable2FA. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="33e9a-185">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml*.</span></span> <span data-ttu-id="33e9a-186">Затем ASP.NET Core Identity подписывает пользователя в.</span><span class="sxs-lookup"><span data-stu-id="33e9a-186">Then ASP.NET Core Identity signs the user in.</span></span>

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

<span data-ttu-id="33e9a-187">`ExternalLoginCallback` Метод работает как локальное Identity имя входа.</span><span class="sxs-lookup"><span data-stu-id="33e9a-187">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="33e9a-188">`AcrValues` Свойство проверяется на наличие `mfa` значения.</span><span class="sxs-lookup"><span data-stu-id="33e9a-188">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="33e9a-189">Если указано `mfa` значение, MFA выполняется перед завершением имени входа (например, перенаправлено в `ErrorEnable2FA` представление).</span><span class="sxs-lookup"><span data-stu-id="33e9a-189">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

<span data-ttu-id="33e9a-190">Если пользователь уже вошел в систему, клиентское приложение:</span><span class="sxs-lookup"><span data-stu-id="33e9a-190">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="33e9a-191">По-прежнему проверяет `amr` утверждение.</span><span class="sxs-lookup"><span data-stu-id="33e9a-191">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="33e9a-192">Может настроить MFA со ссылкой на представление ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="33e9a-192">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="33e9a-194">Принудительное ASP.NET Core OpenID Connect Connect Client для требования MFA</span><span class="sxs-lookup"><span data-stu-id="33e9a-194">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="33e9a-195">В этом примере показано, как Razor приложение ASP.NET Core страниц, использующее OpenID Connect Connect для входа, может потребовать, чтобы пользователи прошли проверку подлинности с помощью mfa.</span><span class="sxs-lookup"><span data-stu-id="33e9a-195">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="33e9a-196">Для проверки требования MFA создается `IAuthorizationRequirement` требование.</span><span class="sxs-lookup"><span data-stu-id="33e9a-196">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="33e9a-197">Это будет добавлено на страницы с помощью политики, требующей MFA.</span><span class="sxs-lookup"><span data-stu-id="33e9a-197">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="33e9a-198">`AuthorizationHandler` Реализуется, который будет использовать `amr` утверждение и проверить значение `mfa`.</span><span class="sxs-lookup"><span data-stu-id="33e9a-198">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="33e9a-199">`amr` Объект возвращается в `id_token` случае успешной проверки подлинности и может иметь множество различных значений, как определено в спецификации [ссылочных значений метода проверки подлинности](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="33e9a-199">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="33e9a-200">Возвращаемое значение зависит от того, как удостоверение прошло проверку подлинности и в реализации сервера Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="33e9a-200">The returned value depends on how the identity authenticated and on the Open ID Connect server implementation.</span></span>

<span data-ttu-id="33e9a-201">`AuthorizationHandler` Компонент использует `RequireMfa` требование и проверяет `amr` утверждение.</span><span class="sxs-lookup"><span data-stu-id="33e9a-201">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="33e9a-202">Сервер OpenID Connect Connect можно реализовать с помощью IdentityServer4 с ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="33e9a-202">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="33e9a-203">Когда пользователь входит в систему с помощью TOTP, `amr` утверждение возвращается со значением mfa.</span><span class="sxs-lookup"><span data-stu-id="33e9a-203">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="33e9a-204">Если используется другая реализация сервера OpenID Connect Connect или другой тип MFA, `amr` утверждение будет иметь или может иметь другое значение.</span><span class="sxs-lookup"><span data-stu-id="33e9a-204">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="33e9a-205">Код необходимо расширить, чтобы принять его.</span><span class="sxs-lookup"><span data-stu-id="33e9a-205">The code must be extended to accept this as well.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

<span data-ttu-id="33e9a-206">В `Startup.ConfigureServices` методе `AddOpenIdConnect` метод используется в качестве схемы запроса по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="33e9a-206">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="33e9a-207">Обработчик авторизации, который используется для проверки `amr` утверждения, добавляется в инверсию контейнера управления.</span><span class="sxs-lookup"><span data-stu-id="33e9a-207">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="33e9a-208">Затем создается политика, которая добавляет `RequireMfa` требование.</span><span class="sxs-lookup"><span data-stu-id="33e9a-208">A policy is then created which adds the `RequireMfa` requirement.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

<span data-ttu-id="33e9a-209">Затем эта политика используется на Razor странице при необходимости.</span><span class="sxs-lookup"><span data-stu-id="33e9a-209">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="33e9a-210">Кроме того, политику можно добавить глобально для всего приложения.</span><span class="sxs-lookup"><span data-stu-id="33e9a-210">The policy could be added globally for the entire app as well.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

<span data-ttu-id="33e9a-211">Если пользователь проходит проверку подлинности без MFA `amr` , то, вероятно, `pwd` у утверждения будет значение.</span><span class="sxs-lookup"><span data-stu-id="33e9a-211">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="33e9a-212">Запрос не будет иметь разрешения на доступ к странице.</span><span class="sxs-lookup"><span data-stu-id="33e9a-212">The request won't be authorized to access the page.</span></span> <span data-ttu-id="33e9a-213">При использовании значений по умолчанию пользователь будет перенаправлен на страницу *Account/AccessDenied* .</span><span class="sxs-lookup"><span data-stu-id="33e9a-213">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="33e9a-214">Это поведение можно изменить, или можно реализовать собственную пользовательскую логику здесь.</span><span class="sxs-lookup"><span data-stu-id="33e9a-214">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="33e9a-215">В этом примере добавляется ссылка, чтобы допустимый пользователь мог настроить MFA для своей учетной записи.</span><span class="sxs-lookup"><span data-stu-id="33e9a-215">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="33e9a-216">Теперь доступ к странице или веб-сайту можно получить только пользователям, которые проходят проверку подлинности с помощью MFA.</span><span class="sxs-lookup"><span data-stu-id="33e9a-216">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="33e9a-217">Если используются разные типы MFA или 2FA, то `amr` утверждение будет иметь разные значения и должно обрабатываться правильно.</span><span class="sxs-lookup"><span data-stu-id="33e9a-217">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="33e9a-218">Другие серверы подключения с открытым кодом также возвращают разные значения для этого утверждения и могут не следовать спецификации [ссылочных значений метода проверки подлинности](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="33e9a-218">Different Open ID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="33e9a-219">При входе без MFA (например, при использовании только пароля):</span><span class="sxs-lookup"><span data-stu-id="33e9a-219">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="33e9a-220">`amr` Имеет `pwd` значение:</span><span class="sxs-lookup"><span data-stu-id="33e9a-220">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02. png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="33e9a-222">Отказано в доступе:</span><span class="sxs-lookup"><span data-stu-id="33e9a-222">Access is denied:</span></span>

    ![require_mfa_oidc_03. png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="33e9a-224">Также можно войти в систему с использованием IdentityOTP с помощью:</span><span class="sxs-lookup"><span data-stu-id="33e9a-224">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01. png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="33e9a-226">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="33e9a-226">Additional resources</span></span>

* [<span data-ttu-id="33e9a-227">Включение создания QR-кода для приложений TOTP Authenticator в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33e9a-227">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="33e9a-228">Параметры проверки подлинности с паролем для Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="33e9a-228">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="33e9a-229">Библиотека FIDO2 .NET для FIDO2/WebAuthn аттестации и утверждения с помощью .NET</span><span class="sxs-lookup"><span data-stu-id="33e9a-229">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="33e9a-230">WebAuthn Awesome</span><span class="sxs-lookup"><span data-stu-id="33e9a-230">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
