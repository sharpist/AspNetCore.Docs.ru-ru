---
title: Многофакторная идентификация в ASP.NET Core
author: damienbod
description: Узнайте, как настроить многофакторную проверку подлинности (MFA) в ASP.NET Core приложении.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
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
uid: security/authentication/mfa
ms.openlocfilehash: e224f947335ea8ea6ed8887dfadb52202bfd7866
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009509"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a>Многофакторная идентификация в ASP.NET Core

По [(Damien Бауден](https://github.com/damienbod)

Многофакторная идентификация (MFA) — это процесс, в ходе которого пользователь запрашивается во время события входа, чтобы получить дополнительные формы идентификации. Этот запрос может быть использован для ввода кода из целлфоне, использования ключа FIDO2 или для обеспечения сканирования отпечатков пальцев. Если требуется Вторая форма проверки подлинности, Безопасность улучшена. Злоумышленник не может легко получить или продублировать дополнительный фактор.

В этой статье рассматриваются следующие вопросы:

* Что такое MFA и какие потоки MFA рекомендуются
* Настройка MFA для страниц администрирования с помощью ASP.NET Core Identity
* Отправка требования к входу MFA в OpenID Connect Connect Server
* Принудительное ASP.NET Core OpenID Connect Connect Client для требования MFA

## <a name="mfa-2fa"></a>MFA, 2FA

MFA требует по крайней мере два типа цветопробы для удостоверения, такого как известное, что вы владеете или биометрической проверки подлинности пользователя.

Двухфакторная проверка подлинности (2FA) похожа на подмножество MFA, но в этом случае в MFA может потребоваться два или более фактора для подтверждения личности.

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a>MFA TOTP (алгоритм одноразового пароля на основе времени)

MFA с использованием TOTP является поддерживаемой реализацией с помощью ASP.NET Core Identity . Это можно использовать вместе со всеми совместимыми приложениями проверки подлинности, в том числе:

* Приложение Microsoft Authenticator
* Приложение Google Authenticator

Сведения о реализации см. по следующей ссылке:

[Включение создания QR-кода для приложений TOTP Authenticator в ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a>FIDO2 MFA или безпарольный

FIDO2 в настоящее время:

* Самый безопасный способ достижения MFA.
* Единственный поток MFA, защищающий от фишинговых атак.

В настоящее время ASP.NET Core не поддерживает FIDO2 напрямую. FIDO2 можно использовать для MFA или беспарольных потоков.

Azure Active Directory обеспечивает поддержку для FIDO2 и безпарольных потоков. Дополнительные сведения см. в разделе [Параметры проверки подлинности](/azure/active-directory/authentication/concept-authentication-passwordless), не имеющие пароля, для Azure Active Directory.

### <a name="mfa-sms"></a>SMS ДЛЯ MFA

MFA с SMS значительно повышает безопасность с помощью проверки пароля (один фактор). Однако использовать SMS в качестве второго фактора больше не рекомендуется. Для этого типа реализации существует слишком много известных векторов атак.

[Рекомендации NIST](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a>Настройка MFA для страниц администрирования с помощью ASP.NET Core Identity

MFA можно принудительно заставить пользователей получать доступ к конфиденциальным страницам в ASP.NET Core Identity приложении. Это может быть полезно для приложений, где существуют разные уровни доступа для разных удостоверений. Например, пользователи могут просматривать данные профиля с помощью имени входа с паролем, но для доступа к административным страницам администратору потребуется использовать MFA.

### <a name="extend-the-login-with-an-mfa-claim"></a>Расширение имени входа с помощью утверждения MFA

Демонстрационный код — это программа установки, использующая ASP.NET Core со Identity Razor страницами и. `AddIdentity`Метод используется вместо `AddDefaultIdentity` одного, поэтому `IUserClaimsPrincipalFactory` реализация может использоваться для добавления утверждений в удостоверение после успешного входа.

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

`AdditionalUserClaimsPrincipalFactory`Класс добавляет `amr` утверждение в утверждения пользователя только после успешного входа. Значение утверждения считывается из базы данных. Утверждение добавляется здесь, так как пользователь должен получить доступ к более защищенному представлению, если удостоверение было зарегистрировано с помощью MFA. Если представление базы данных считывается непосредственно из базы данных, а не с помощью утверждения, можно получить доступ к представлению без MFA непосредственно после активации MFA.

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

Поскольку Identity программа установки службы изменилась в `Startup` классе, необходимо обновить макеты Identity . Формирование шаблонов Identity страниц в приложении. Определите макет в файле * Identity /аккаунт/манаже/_layout. cshtml* .

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

Также назначайте макет для всех страниц управления со Identity страниц:

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a>Проверка требования MFA на странице "Администрирование"

На Razor странице администрирования проверяется, что пользователь выполнил вход с помощью mfa. В `OnGet` методе удостоверение используется для доступа к утверждениям пользователя. `amr`Утверждение проверяется на наличие значения `mfa` . Если в удостоверении отсутствует это утверждение или оно имеет значение `false` , то страница переправляется на страницу включение mfa. Это возможно, так как пользователь уже вошел в систему, но без MFA.

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

### <a name="ui-logic-to-toggle-user-login-information"></a>Логика пользовательского интерфейса для переключения сведений для входа пользователя

Политика авторизации была добавлена при запуске. Политика требует `amr` утверждения со значением `mfa` .

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

Затем эту политику можно использовать в представлении, `_Layout` чтобы показать или скрыть меню **администрирования** с предупреждением:

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

Если удостоверение было зарегистрировано с помощью MFA, меню **Admin** отображается без предупреждения о подсказке. Когда пользователь вошел в систему без MFA, меню **Администратор (не включено)** отображается вместе с подсказкой, которая информирует пользователя (объяснение предупреждения).

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

Если пользователь входит в систему без MFA, отображается предупреждение:

![Проверка подлинности администратора MFA](mfa/_static/identitystandalonemfa_01.png)

Пользователь перенаправляется в представление включения MFA при щелчке ссылки **администратора** :

![Администратор активирует аутентификацию MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a>Отправка требования к входу MFA в OpenID Connect Connect Server 

`acr_values`Параметр можно использовать для передачи `mfa` требуемого значения с клиента на сервер в запросе проверки подлинности.

> [!NOTE]
> Чтобы `acr_values` это работало, параметр должен быть обработан на сервере OpenID Connect Connect.

### <a name="openid-connect-aspnet-core-client"></a>OpenID Connect подключение клиента ASP.NET Core

RazorКлиентское приложение ASP.NET Core Pages OpenID Connect Connect использует `AddOpenIdConnect` метод для входа на сервер OpenID Connect Connect. `acr_values`Параметр задается со `mfa` значением и отправляется с запросом проверки подлинности. `OpenIdConnectEvents`Используется для добавления этого.

Рекомендуемые `acr_values` значения параметров см. в разделе [Ссылочные значения метода проверки подлинности](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).

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

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a>Пример OpenID Connect Connect Identity Server 4 Server с ASP.NET Core Identity

На сервере OpenID Connect Connect, который реализуется с помощью ASP.NET Core Identity с представлениями MVC, создается новое представление с именем *ErrorEnable2FA. cshtml* . Представление:

* Показывает, Identity поступает ли объект из приложения, для которого требуется MFA, но пользователь не активировал его в Identity .
* Информирует пользователя и добавляет ссылку для активации.

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

В `Login` методе `IIdentityServerInteractionService` Реализация интерфейса `_interaction` используется для доступа к параметрам запроса OpenID Connect Connect. `acr_values`Доступ к параметру осуществляется с помощью `AcrValues` Свойства. После отправки клиентом `mfa` значения SET его можно проверить.

Если требуется MFA и для пользователя в ASP.NET Core Identity включено MFA, то вход будет продолжен. Если для пользователя не включено MFA, пользователь перенаправляется к пользовательскому представлению *ErrorEnable2FA. cshtml*. Затем ASP.NET Core Identity подписывает пользователя в.

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

`ExternalLoginCallback`Метод работает как локальное Identity имя входа. `AcrValues`Свойство проверяется на наличие `mfa` значения. Если указано `mfa` значение, MFA выполняется перед завершением имени входа (например, перенаправлено в `ErrorEnable2FA` представление).

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

Если пользователь уже вошел в систему, клиентское приложение:

* По-прежнему проверяет `amr` утверждение.
* Может настроить MFA со ссылкой на ASP.NET Core Identity представление.

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a>Принудительное ASP.NET Core OpenID Connect Connect Client для требования MFA

В этом примере показано, как Razor приложение ASP.NET Core страниц, использующее OpenID Connect Connect для входа, может потребовать, чтобы пользователи прошли проверку подлинности с помощью mfa.

Для проверки требования MFA `IAuthorizationRequirement` создается требование. Это будет добавлено на страницы с помощью политики, требующей MFA.

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

`AuthorizationHandler`Реализуется, который будет использовать `amr` утверждение и проверить значение `mfa` . Объект `amr` возвращается в `id_token` случае успешной проверки подлинности и может иметь множество различных значений, как определено в спецификации [ссылочных значений метода проверки подлинности](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .

Возвращаемое значение зависит от того, как удостоверение прошло проверку подлинности и в реализации сервера OpenID Connect Connect.

`AuthorizationHandler`Компонент использует `RequireMfa` требование и проверяет `amr` утверждение. Сервер OpenID Connect Connect можно реализовать с помощью Identity Server4 с ASP.NET Core Identity . Когда пользователь входит в систему с помощью TOTP, `amr` утверждение возвращается со ЗНАЧЕНИЕМ mfa. Если используется другая реализация сервера OpenID Connect Connect или другой тип MFA, `amr` утверждение будет иметь или может иметь другое значение. Код необходимо расширить, чтобы принять его.

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

В `Startup.ConfigureServices` методе `AddOpenIdConnect` метод используется в качестве схемы запроса по умолчанию. Обработчик авторизации, который используется для проверки `amr` утверждения, добавляется в инверсию контейнера управления. Затем создается политика, которая добавляет `RequireMfa` требование.

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

Затем эта политика используется на Razor странице при необходимости. Кроме того, политику можно добавить глобально для всего приложения.

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

Если пользователь проходит проверку подлинности без MFA, то, `amr` вероятно, у утверждения будет `pwd` значение. Запрос не будет иметь разрешения на доступ к странице. При использовании значений по умолчанию пользователь будет перенаправлен на страницу *Account/AccessDenied* . Это поведение можно изменить, или можно реализовать собственную пользовательскую логику здесь. В этом примере добавляется ссылка, чтобы допустимый пользователь мог настроить MFA для своей учетной записи.

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

Теперь доступ к странице или веб-сайту можно получить только пользователям, которые проходят проверку подлинности с помощью MFA. Если используются разные типы MFA или 2FA, то `amr` утверждение будет иметь разные значения и должно обрабатываться правильно. Различные серверы OpenID Connect Connect также возвращают разные значения для этого утверждения и могут не следовать спецификации [ссылочных значений метода проверки подлинности](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .

При входе без MFA (например, при использовании только пароля):

* `amr`Имеет `pwd` значение:

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* Отказано в доступе:

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

Также можно войти в систему с использованием OTP с помощью Identity :

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Включение создания QR-кода для приложений TOTP Authenticator в ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Параметры проверки подлинности с паролем для Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless)
* [Библиотека FIDO2 .NET для FIDO2/WebAuthn аттестации и утверждения с помощью .NET](https://github.com/abergs/fido2-net-lib)
* [WebAuthn Awesome](https://github.com/herrjemand/awesome-webauthn)
