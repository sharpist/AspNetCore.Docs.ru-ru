---
title: Авторизация с помощью определенной схемы в ASP.NET Core
author: rick-anderson
description: В этой статье объясняется, как ограничить идентификацию определенной схемой при работе с несколькими методами проверки подлинности.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 69b6412f249355573faa785743b124a67ecb8b9e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777518"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="9cbef-103">Авторизация с помощью определенной схемы в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9cbef-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="9cbef-104">В некоторых сценариях, таких как одностраничные приложения (одностраничные приложения), обычно используется несколько методов проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9cbef-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="9cbef-105">Например, приложение может использовать проверку подлинности на основе файлов cookie для входа и аутентификации JWT Bearer для запросов JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9cbef-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="9cbef-106">В некоторых случаях приложение может иметь несколько экземпляров обработчика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9cbef-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="9cbef-107">Например, два обработчика файлов cookie, где один из них содержит базовое удостоверение и создается при активации многофакторной проверки подлинности (MFA).</span><span class="sxs-lookup"><span data-stu-id="9cbef-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="9cbef-108">MFA может активироваться, так как пользователь запросил операцию, требующую дополнительной защиты.</span><span class="sxs-lookup"><span data-stu-id="9cbef-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="9cbef-109">Дополнительные сведения о применении MFA, когда пользователь запрашивает ресурс, требующий MFA, см. в разделе "Защита от проблем GitHub" [с MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span><span class="sxs-lookup"><span data-stu-id="9cbef-109">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="9cbef-110">Схема проверки подлинности называется, когда служба проверки подлинности настроена во время проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9cbef-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="9cbef-111">Например:</span><span class="sxs-lookup"><span data-stu-id="9cbef-111">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="9cbef-112">В приведенном выше коде были добавлены два обработчика проверки подлинности: один для файлов cookie и один для носителя.</span><span class="sxs-lookup"><span data-stu-id="9cbef-112">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="9cbef-113">При указании схемы по умолчанию `HttpContext.User` задается свойство, которому присваивается это удостоверение.</span><span class="sxs-lookup"><span data-stu-id="9cbef-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="9cbef-114">Если такое поведение не требуется, отключите его, вызвав форму без параметров `AddAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="9cbef-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="9cbef-115">Выбор схемы с помощью атрибута авторизации</span><span class="sxs-lookup"><span data-stu-id="9cbef-115">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="9cbef-116">В точке авторизации приложение указывает, какой обработчик следует использовать.</span><span class="sxs-lookup"><span data-stu-id="9cbef-116">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="9cbef-117">Выберите обработчик, с помощью которого приложение будет выполнять авторизацию, передав список схем проверки подлинности с разделителями-запятыми в `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="9cbef-117">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="9cbef-118">`[Authorize]` Атрибут указывает схему или схемы проверки подлинности, которые будут использоваться независимо от того, настроено ли значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="9cbef-118">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="9cbef-119">Например:</span><span class="sxs-lookup"><span data-stu-id="9cbef-119">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="9cbef-120">В предыдущем примере выполняются обработчики файлов cookie и носителя, а также возможность создания и добавления удостоверения для текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="9cbef-120">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="9cbef-121">Если указать только одну схему, выполняется соответствующий обработчик.</span><span class="sxs-lookup"><span data-stu-id="9cbef-121">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="9cbef-122">В приведенном выше коде выполняется только обработчик со схемой "Bearer".</span><span class="sxs-lookup"><span data-stu-id="9cbef-122">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="9cbef-123">Все удостоверения на основе файлов cookie игнорируются.</span><span class="sxs-lookup"><span data-stu-id="9cbef-123">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="9cbef-124">Выбор схемы с политиками</span><span class="sxs-lookup"><span data-stu-id="9cbef-124">Selecting the scheme with policies</span></span>

<span data-ttu-id="9cbef-125">Если вы предпочитаете указывать нужные схемы в [политике](xref:security/authorization/policies), вы можете задать `AuthenticationSchemes` коллекцию при добавлении политики:</span><span class="sxs-lookup"><span data-stu-id="9cbef-125">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="9cbef-126">В предыдущем примере политика "Over18" выполняется только для удостоверения, созданного обработчиком "Bearer".</span><span class="sxs-lookup"><span data-stu-id="9cbef-126">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="9cbef-127">Используйте политику, задав `[Authorize]` `Policy` свойство атрибута:</span><span class="sxs-lookup"><span data-stu-id="9cbef-127">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="9cbef-128">Использование нескольких схем проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="9cbef-128">Use multiple authentication schemes</span></span>

<span data-ttu-id="9cbef-129">Некоторым приложениям может потребоваться поддержка нескольких типов проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9cbef-129">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="9cbef-130">Например, приложение может выполнять проверку подлинности пользователей из Azure Active Directory и из базы данных пользователей.</span><span class="sxs-lookup"><span data-stu-id="9cbef-130">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="9cbef-131">Другим примером является приложение, которое выполняет проверку подлинности пользователей как с службы федерации Active Directory (AD FS), так Azure Active Directory B2C.</span><span class="sxs-lookup"><span data-stu-id="9cbef-131">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="9cbef-132">В этом случае приложение должно принять токен носителя JWT из нескольких издателей.</span><span class="sxs-lookup"><span data-stu-id="9cbef-132">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="9cbef-133">Добавьте все схемы проверки подлинности, которые вы хотите принять.</span><span class="sxs-lookup"><span data-stu-id="9cbef-133">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="9cbef-134">Например, следующий код `Startup.ConfigureServices` добавляет две схемы проверки подлинности JWT Bearer с разными издателями:</span><span class="sxs-lookup"><span data-stu-id="9cbef-134">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="9cbef-135">В схеме `JwtBearerDefaults.AuthenticationScheme`проверки подлинности по умолчанию регистрируется только одна проверка подлинности носителя JWT.</span><span class="sxs-lookup"><span data-stu-id="9cbef-135">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="9cbef-136">Дополнительную проверку подлинности необходимо зарегистрировать с помощью уникальной схемы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9cbef-136">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="9cbef-137">Следующим шагом является обновление политики авторизации по умолчанию для принятия обеих схем проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9cbef-137">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="9cbef-138">Например:</span><span class="sxs-lookup"><span data-stu-id="9cbef-138">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="9cbef-139">По мере переопределения политики авторизации по умолчанию можно использовать `[Authorize]` атрибут в контроллерах.</span><span class="sxs-lookup"><span data-stu-id="9cbef-139">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="9cbef-140">Затем контроллер принимает запросы с маркером JWT, выданным первым или вторым издателем.</span><span class="sxs-lookup"><span data-stu-id="9cbef-140">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end
