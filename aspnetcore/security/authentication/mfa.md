---
title: Многофакторная идентификация в ASP.NET Core
author: damienbod
description: Узнайте, как настроить многофакторную проверку подлинности (MFA) в ASP.NET Core приложении.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/mfa
ms.openlocfilehash: 873f7d113df84c931ad7fbf2c72aa292e4e87c48
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060395"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="2613d-103">Многофакторная идентификация в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2613d-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="2613d-104">По [(Damien Бауден](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="2613d-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

[<span data-ttu-id="2613d-105">Просмотреть или скачать пример кода (дамиенбод/Аспнеткорехибридфловвисапи репозиторий GitHub)</span><span class="sxs-lookup"><span data-stu-id="2613d-105">View or download sample code (damienbod/AspNetCoreHybridFlowWithApi GitHub repository)</span></span>](https://github.com/damienbod/AspNetCoreHybridFlowWithApi)

<span data-ttu-id="2613d-106">Многофакторная идентификация (MFA) — это процесс, в ходе которого пользователь запрашивается во время события входа, чтобы получить дополнительные формы идентификации.</span><span class="sxs-lookup"><span data-stu-id="2613d-106">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="2613d-107">Этот запрос может быть использован для ввода кода из целлфоне, использования ключа FIDO2 или для обеспечения сканирования отпечатков пальцев.</span><span class="sxs-lookup"><span data-stu-id="2613d-107">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="2613d-108">Если требуется Вторая форма проверки подлинности, Безопасность улучшена.</span><span class="sxs-lookup"><span data-stu-id="2613d-108">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="2613d-109">Злоумышленник не может легко получить или продублировать дополнительный фактор.</span><span class="sxs-lookup"><span data-stu-id="2613d-109">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="2613d-110">В этой статье рассматриваются следующие вопросы:</span><span class="sxs-lookup"><span data-stu-id="2613d-110">This article covers the following areas:</span></span>

* <span data-ttu-id="2613d-111">Что такое MFA и какие потоки MFA рекомендуются</span><span class="sxs-lookup"><span data-stu-id="2613d-111">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="2613d-112">Настройка MFA для страниц администрирования с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2613d-112">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="2613d-113">Отправка требования к входу MFA в OpenID Connect Connect Server</span><span class="sxs-lookup"><span data-stu-id="2613d-113">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="2613d-114">Принудительное ASP.NET Core OpenID Connect Connect Client для требования MFA</span><span class="sxs-lookup"><span data-stu-id="2613d-114">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="2613d-115">MFA, 2FA</span><span class="sxs-lookup"><span data-stu-id="2613d-115">MFA, 2FA</span></span>

<span data-ttu-id="2613d-116">MFA требует по крайней мере два типа цветопробы для удостоверения, такого как известное, что вы владеете или биометрической проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="2613d-116">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="2613d-117">Двухфакторная проверка подлинности (2FA) похожа на подмножество MFA, но в этом случае в MFA может потребоваться два или более фактора для подтверждения личности.</span><span class="sxs-lookup"><span data-stu-id="2613d-117">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="2613d-118">MFA TOTP (алгоритм одноразового пароля на основе времени)</span><span class="sxs-lookup"><span data-stu-id="2613d-118">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="2613d-119">MFA с использованием TOTP является поддерживаемой реализацией с помощью ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="2613d-119">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="2613d-120">Это можно использовать вместе со всеми совместимыми приложениями проверки подлинности, в том числе:</span><span class="sxs-lookup"><span data-stu-id="2613d-120">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="2613d-121">Приложение Microsoft Authenticator</span><span class="sxs-lookup"><span data-stu-id="2613d-121">Microsoft Authenticator App</span></span>
* <span data-ttu-id="2613d-122">Приложение Google Authenticator</span><span class="sxs-lookup"><span data-stu-id="2613d-122">Google Authenticator App</span></span>

<span data-ttu-id="2613d-123">Сведения о реализации см. по следующей ссылке:</span><span class="sxs-lookup"><span data-stu-id="2613d-123">See the following link for implementation details:</span></span>

[<span data-ttu-id="2613d-124">Включение создания QR-кода для приложений TOTP Authenticator в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2613d-124">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="2613d-125">FIDO2 MFA или безпарольный</span><span class="sxs-lookup"><span data-stu-id="2613d-125">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="2613d-126">FIDO2 в настоящее время:</span><span class="sxs-lookup"><span data-stu-id="2613d-126">FIDO2 is currently:</span></span>

* <span data-ttu-id="2613d-127">Самый безопасный способ достижения MFA.</span><span class="sxs-lookup"><span data-stu-id="2613d-127">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="2613d-128">Единственный поток MFA, защищающий от фишинговых атак.</span><span class="sxs-lookup"><span data-stu-id="2613d-128">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="2613d-129">В настоящее время ASP.NET Core не поддерживает FIDO2 напрямую.</span><span class="sxs-lookup"><span data-stu-id="2613d-129">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="2613d-130">FIDO2 можно использовать для MFA или беспарольных потоков.</span><span class="sxs-lookup"><span data-stu-id="2613d-130">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="2613d-131">Azure Active Directory обеспечивает поддержку для FIDO2 и безпарольных потоков.</span><span class="sxs-lookup"><span data-stu-id="2613d-131">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="2613d-132">Дополнительные сведения см. в разделе [Параметры проверки подлинности](/azure/active-directory/authentication/concept-authentication-passwordless), не имеющие пароля, для Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="2613d-132">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="2613d-133">SMS ДЛЯ MFA</span><span class="sxs-lookup"><span data-stu-id="2613d-133">MFA SMS</span></span>

<span data-ttu-id="2613d-134">MFA с SMS значительно повышает безопасность с помощью проверки пароля (один фактор).</span><span class="sxs-lookup"><span data-stu-id="2613d-134">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="2613d-135">Однако использовать SMS в качестве второго фактора больше не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="2613d-135">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="2613d-136">Для этого типа реализации существует слишком много известных векторов атак.</span><span class="sxs-lookup"><span data-stu-id="2613d-136">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="2613d-137">Рекомендации NIST</span><span class="sxs-lookup"><span data-stu-id="2613d-137">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a><span data-ttu-id="2613d-138">Настройка MFA для страниц администрирования с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2613d-138">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="2613d-139">MFA можно принудительно заставить пользователей получать доступ к конфиденциальным страницам в ASP.NET Core Identity приложении.</span><span class="sxs-lookup"><span data-stu-id="2613d-139">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="2613d-140">Это может быть полезно для приложений, где существуют разные уровни доступа для разных удостоверений.</span><span class="sxs-lookup"><span data-stu-id="2613d-140">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="2613d-141">Например, пользователи могут просматривать данные профиля с помощью имени входа с паролем, но для доступа к административным страницам администратору потребуется использовать MFA.</span><span class="sxs-lookup"><span data-stu-id="2613d-141">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="2613d-142">Расширение имени входа с помощью утверждения MFA</span><span class="sxs-lookup"><span data-stu-id="2613d-142">Extend the login with an MFA claim</span></span>

<span data-ttu-id="2613d-143">Демонстрационный код — это программа установки, использующая ASP.NET Core со Identity Razor страницами и.</span><span class="sxs-lookup"><span data-stu-id="2613d-143">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="2613d-144">`AddIdentity`Метод используется вместо `AddDefaultIdentity` одного, поэтому `IUserClaimsPrincipalFactory` реализация может использоваться для добавления утверждений в удостоверение после успешного входа.</span><span class="sxs-lookup"><span data-stu-id="2613d-144">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

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

<span data-ttu-id="2613d-145">`AdditionalUserClaimsPrincipalFactory`Класс добавляет `amr` утверждение в утверждения пользователя только после успешного входа.</span><span class="sxs-lookup"><span data-stu-id="2613d-145">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="2613d-146">Значение утверждения считывается из базы данных.</span><span class="sxs-lookup"><span data-stu-id="2613d-146">The claim's value is read from the database.</span></span> <span data-ttu-id="2613d-147">Утверждение добавляется здесь, так как пользователь должен получить доступ к более защищенному представлению, если удостоверение было зарегистрировано с помощью MFA.</span><span class="sxs-lookup"><span data-stu-id="2613d-147">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="2613d-148">Если представление базы данных считывается непосредственно из базы данных, а не с помощью утверждения, можно получить доступ к представлению без MFA непосредственно после активации MFA.</span><span class="sxs-lookup"><span data-stu-id="2613d-148">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

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

<span data-ttu-id="2613d-149">Поскольку Identity программа установки службы изменилась в `Startup` классе, необходимо обновить макеты Identity .</span><span class="sxs-lookup"><span data-stu-id="2613d-149">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="2613d-150">Формирование шаблонов Identity страниц в приложении.</span><span class="sxs-lookup"><span data-stu-id="2613d-150">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="2613d-151">Определите макет в файле *Identity /аккаунт/манаже/_layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2613d-151">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="2613d-152">Также назначайте макет для всех страниц управления со Identity страниц:</span><span class="sxs-lookup"><span data-stu-id="2613d-152">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="2613d-153">Проверка требования MFA на странице "Администрирование"</span><span class="sxs-lookup"><span data-stu-id="2613d-153">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="2613d-154">На Razor странице администрирования проверяется, что пользователь выполнил вход с помощью mfa.</span><span class="sxs-lookup"><span data-stu-id="2613d-154">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="2613d-155">В `OnGet` методе удостоверение используется для доступа к утверждениям пользователя.</span><span class="sxs-lookup"><span data-stu-id="2613d-155">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="2613d-156">`amr`Утверждение проверяется на наличие значения `mfa` .</span><span class="sxs-lookup"><span data-stu-id="2613d-156">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="2613d-157">Если в удостоверении отсутствует это утверждение или оно имеет значение `false` , то страница переправляется на страницу включение mfa.</span><span class="sxs-lookup"><span data-stu-id="2613d-157">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="2613d-158">Это возможно, так как пользователь уже вошел в систему, но без MFA.</span><span class="sxs-lookup"><span data-stu-id="2613d-158">This is possible because the user has logged in already, but without MFA.</span></span>

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

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="2613d-159">Логика пользовательского интерфейса для переключения сведений для входа пользователя</span><span class="sxs-lookup"><span data-stu-id="2613d-159">UI logic to toggle user login information</span></span>

<span data-ttu-id="2613d-160">Политика авторизации была добавлена при запуске.</span><span class="sxs-lookup"><span data-stu-id="2613d-160">An authorization policy was added at startup.</span></span> <span data-ttu-id="2613d-161">Политика требует `amr` утверждения со значением `mfa` .</span><span class="sxs-lookup"><span data-stu-id="2613d-161">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="2613d-162">Затем эту политику можно использовать в представлении, `_Layout` чтобы показать или скрыть меню **администрирования** с предупреждением:</span><span class="sxs-lookup"><span data-stu-id="2613d-162">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="2613d-163">Если удостоверение было зарегистрировано с помощью MFA, меню **Admin** отображается без предупреждения о подсказке.</span><span class="sxs-lookup"><span data-stu-id="2613d-163">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="2613d-164">Когда пользователь вошел в систему без MFA, меню **Администратор (не включено)** отображается вместе с подсказкой, которая информирует пользователя (объяснение предупреждения).</span><span class="sxs-lookup"><span data-stu-id="2613d-164">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

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

<span data-ttu-id="2613d-165">Если пользователь входит в систему без MFA, отображается предупреждение:</span><span class="sxs-lookup"><span data-stu-id="2613d-165">If the user logs in without MFA, the warning is displayed:</span></span>

![Проверка подлинности администратора MFA](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="2613d-167">Пользователь перенаправляется в представление включения MFA при щелчке ссылки **администратора** :</span><span class="sxs-lookup"><span data-stu-id="2613d-167">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![Администратор активирует аутентификацию MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="2613d-169">Отправка требования к входу MFA в OpenID Connect Connect Server</span><span class="sxs-lookup"><span data-stu-id="2613d-169">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="2613d-170">`acr_values`Параметр можно использовать для передачи `mfa` требуемого значения с клиента на сервер в запросе проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2613d-170">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="2613d-171">Чтобы `acr_values` это работало, параметр должен быть обработан на сервере OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="2613d-171">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="2613d-172">OpenID Connect подключение клиента ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2613d-172">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="2613d-173">RazorКлиентское приложение ASP.NET Core Pages OpenID Connect Connect использует `AddOpenIdConnect` метод для входа на сервер OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="2613d-173">The ASP.NET Core Razor Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="2613d-174">`acr_values`Параметр задается со `mfa` значением и отправляется с запросом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2613d-174">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="2613d-175">`OpenIdConnectEvents`Используется для добавления этого.</span><span class="sxs-lookup"><span data-stu-id="2613d-175">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="2613d-176">Рекомендуемые `acr_values` значения параметров см. в разделе [Ссылочные значения метода проверки подлинности](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span><span class="sxs-lookup"><span data-stu-id="2613d-176">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

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

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a><span data-ttu-id="2613d-177">Пример OpenID Connect Connect Identity Server 4 Server с ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2613d-177">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="2613d-178">На сервере OpenID Connect Connect, который реализуется с помощью ASP.NET Core Identity с представлениями MVC, создается новое представление с именем *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2613d-178">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="2613d-179">Представление:</span><span class="sxs-lookup"><span data-stu-id="2613d-179">The view:</span></span>

* <span data-ttu-id="2613d-180">Показывает, Identity поступает ли объект из приложения, для которого требуется MFA, но пользователь не активировал его в Identity .</span><span class="sxs-lookup"><span data-stu-id="2613d-180">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="2613d-181">Информирует пользователя и добавляет ссылку для активации.</span><span class="sxs-lookup"><span data-stu-id="2613d-181">Informs the user and adds a link to activate this.</span></span>

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

<span data-ttu-id="2613d-182">В `Login` методе `IIdentityServerInteractionService` Реализация интерфейса `_interaction` используется для доступа к параметрам запроса OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="2613d-182">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="2613d-183">`acr_values`Доступ к параметру осуществляется с помощью `AcrValues` Свойства.</span><span class="sxs-lookup"><span data-stu-id="2613d-183">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="2613d-184">После отправки клиентом `mfa` значения SET его можно проверить.</span><span class="sxs-lookup"><span data-stu-id="2613d-184">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="2613d-185">Если требуется MFA и для пользователя в ASP.NET Core Identity включено MFA, то вход будет продолжен.</span><span class="sxs-lookup"><span data-stu-id="2613d-185">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="2613d-186">Если для пользователя не включено MFA, пользователь перенаправляется к пользовательскому представлению *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2613d-186">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml* .</span></span> <span data-ttu-id="2613d-187">Затем ASP.NET Core Identity подписывает пользователя в.</span><span class="sxs-lookup"><span data-stu-id="2613d-187">Then ASP.NET Core Identity signs the user in.</span></span>

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

<span data-ttu-id="2613d-188">`ExternalLoginCallback`Метод работает как локальное Identity имя входа.</span><span class="sxs-lookup"><span data-stu-id="2613d-188">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="2613d-189">`AcrValues`Свойство проверяется на наличие `mfa` значения.</span><span class="sxs-lookup"><span data-stu-id="2613d-189">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="2613d-190">Если указано `mfa` значение, MFA выполняется перед завершением имени входа (например, перенаправлено в `ErrorEnable2FA` представление).</span><span class="sxs-lookup"><span data-stu-id="2613d-190">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

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

<span data-ttu-id="2613d-191">Если пользователь уже вошел в систему, клиентское приложение:</span><span class="sxs-lookup"><span data-stu-id="2613d-191">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="2613d-192">По-прежнему проверяет `amr` утверждение.</span><span class="sxs-lookup"><span data-stu-id="2613d-192">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="2613d-193">Может настроить MFA со ссылкой на ASP.NET Core Identity представление.</span><span class="sxs-lookup"><span data-stu-id="2613d-193">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="2613d-195">Принудительное ASP.NET Core OpenID Connect Connect Client для требования MFA</span><span class="sxs-lookup"><span data-stu-id="2613d-195">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="2613d-196">В этом примере показано, как Razor приложение ASP.NET Core страниц, использующее OpenID Connect Connect для входа, может потребовать, чтобы пользователи прошли проверку подлинности с помощью mfa.</span><span class="sxs-lookup"><span data-stu-id="2613d-196">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="2613d-197">Для проверки требования MFA `IAuthorizationRequirement` создается требование.</span><span class="sxs-lookup"><span data-stu-id="2613d-197">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="2613d-198">Это будет добавлено на страницы с помощью политики, требующей MFA.</span><span class="sxs-lookup"><span data-stu-id="2613d-198">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="2613d-199">`AuthorizationHandler`Реализуется, который будет использовать `amr` утверждение и проверить значение `mfa` .</span><span class="sxs-lookup"><span data-stu-id="2613d-199">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="2613d-200">Объект `amr` возвращается в `id_token` случае успешной проверки подлинности и может иметь множество различных значений, как определено в спецификации [ссылочных значений метода проверки подлинности](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="2613d-200">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="2613d-201">Возвращаемое значение зависит от того, как удостоверение прошло проверку подлинности и в реализации сервера OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="2613d-201">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="2613d-202">`AuthorizationHandler`Компонент использует `RequireMfa` требование и проверяет `amr` утверждение.</span><span class="sxs-lookup"><span data-stu-id="2613d-202">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="2613d-203">Сервер OpenID Connect Connect можно реализовать с помощью Identity Server4 с ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="2613d-203">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="2613d-204">Когда пользователь входит в систему с помощью TOTP, `amr` утверждение возвращается со ЗНАЧЕНИЕМ mfa.</span><span class="sxs-lookup"><span data-stu-id="2613d-204">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="2613d-205">Если используется другая реализация сервера OpenID Connect Connect или другой тип MFA, `amr` утверждение будет иметь или может иметь другое значение.</span><span class="sxs-lookup"><span data-stu-id="2613d-205">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="2613d-206">Код необходимо расширить, чтобы принять его.</span><span class="sxs-lookup"><span data-stu-id="2613d-206">The code must be extended to accept this as well.</span></span>

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

<span data-ttu-id="2613d-207">В `Startup.ConfigureServices` методе `AddOpenIdConnect` метод используется в качестве схемы запроса по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2613d-207">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="2613d-208">Обработчик авторизации, который используется для проверки `amr` утверждения, добавляется в инверсию контейнера управления.</span><span class="sxs-lookup"><span data-stu-id="2613d-208">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="2613d-209">Затем создается политика, которая добавляет `RequireMfa` требование.</span><span class="sxs-lookup"><span data-stu-id="2613d-209">A policy is then created which adds the `RequireMfa` requirement.</span></span>

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

<span data-ttu-id="2613d-210">Затем эта политика используется на Razor странице при необходимости.</span><span class="sxs-lookup"><span data-stu-id="2613d-210">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="2613d-211">Кроме того, политику можно добавить глобально для всего приложения.</span><span class="sxs-lookup"><span data-stu-id="2613d-211">The policy could be added globally for the entire app as well.</span></span>

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

<span data-ttu-id="2613d-212">Если пользователь проходит проверку подлинности без MFA, то, `amr` вероятно, у утверждения будет `pwd` значение.</span><span class="sxs-lookup"><span data-stu-id="2613d-212">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="2613d-213">Запрос не будет иметь разрешения на доступ к странице.</span><span class="sxs-lookup"><span data-stu-id="2613d-213">The request won't be authorized to access the page.</span></span> <span data-ttu-id="2613d-214">При использовании значений по умолчанию пользователь будет перенаправлен на страницу *Account/AccessDenied* .</span><span class="sxs-lookup"><span data-stu-id="2613d-214">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="2613d-215">Это поведение можно изменить, или можно реализовать собственную пользовательскую логику здесь.</span><span class="sxs-lookup"><span data-stu-id="2613d-215">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="2613d-216">В этом примере добавляется ссылка, чтобы допустимый пользователь мог настроить MFA для своей учетной записи.</span><span class="sxs-lookup"><span data-stu-id="2613d-216">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

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

<span data-ttu-id="2613d-217">Теперь доступ к странице или веб-сайту можно получить только пользователям, которые проходят проверку подлинности с помощью MFA.</span><span class="sxs-lookup"><span data-stu-id="2613d-217">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="2613d-218">Если используются разные типы MFA или 2FA, то `amr` утверждение будет иметь разные значения и должно обрабатываться правильно.</span><span class="sxs-lookup"><span data-stu-id="2613d-218">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="2613d-219">Различные серверы OpenID Connect Connect также возвращают разные значения для этого утверждения и могут не следовать спецификации [ссылочных значений метода проверки подлинности](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="2613d-219">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="2613d-220">При входе без MFA (например, при использовании только пароля):</span><span class="sxs-lookup"><span data-stu-id="2613d-220">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="2613d-221">`amr`Имеет `pwd` значение:</span><span class="sxs-lookup"><span data-stu-id="2613d-221">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="2613d-223">Отказано в доступе:</span><span class="sxs-lookup"><span data-stu-id="2613d-223">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="2613d-225">Также можно войти в систему с использованием OTP с помощью Identity :</span><span class="sxs-lookup"><span data-stu-id="2613d-225">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="2613d-227">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2613d-227">Additional resources</span></span>

* [<span data-ttu-id="2613d-228">Включение создания QR-кода для приложений TOTP Authenticator в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2613d-228">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="2613d-229">Параметры проверки подлинности с паролем для Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="2613d-229">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="2613d-230">Библиотека FIDO2 .NET для FIDO2/WebAuthn аттестации и утверждения с помощью .NET</span><span class="sxs-lookup"><span data-stu-id="2613d-230">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="2613d-231">WebAuthn Awesome</span><span class="sxs-lookup"><span data-stu-id="2613d-231">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
