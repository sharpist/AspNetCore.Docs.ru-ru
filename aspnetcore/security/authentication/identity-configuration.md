---
title: Настройка ASP.NET CoreIdentity
author: AdrienTorris
description: Изучите ASP.NET Core Identity значения по умолчанию и Identity Узнайте, как настроить свойства для использования пользовательских значений.
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: b88f2627eabc536f2d3b8e677020a67bfd1a40ba
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775652"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="21ba1-103">Настройка удостоверения ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21ba1-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="21ba1-104">ASP.NET Core удостоверение использует значения по умолчанию для таких параметров, как политика паролей, блокировка и конфигурация файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="21ba1-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="21ba1-105">Эти параметры можно переопределить в `Startup` классе.</span><span class="sxs-lookup"><span data-stu-id="21ba1-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a><span data-ttu-id="21ba1-106">Параметры идентификации</span><span class="sxs-lookup"><span data-stu-id="21ba1-106">Identity options</span></span>

<span data-ttu-id="21ba1-107">Класс [идентитйоптионс](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) представляет параметры, которые можно использовать для настройки системы удостоверений.</span><span class="sxs-lookup"><span data-stu-id="21ba1-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="21ba1-108">`IdentityOptions`необходимо задать **после** вызова метода `AddIdentity` или `AddDefaultIdentity`.</span><span class="sxs-lookup"><span data-stu-id="21ba1-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="21ba1-109">Удостоверение утверждений</span><span class="sxs-lookup"><span data-stu-id="21ba1-109">Claims Identity</span></span>

<span data-ttu-id="21ba1-110">[Идентитйоптионс. ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) указывает [клаимсидентитйоптионс](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) со свойствами, показанными в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="21ba1-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="21ba1-111">Свойство.</span><span class="sxs-lookup"><span data-stu-id="21ba1-111">Property</span></span> | <span data-ttu-id="21ba1-112">Описание</span><span class="sxs-lookup"><span data-stu-id="21ba1-112">Description</span></span> | <span data-ttu-id="21ba1-113">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="21ba1-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="21ba1-114">ролеклаимтипе</span><span class="sxs-lookup"><span data-stu-id="21ba1-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="21ba1-115">Возвращает или задает тип утверждения, используемого для утверждения роли.</span><span class="sxs-lookup"><span data-stu-id="21ba1-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="21ba1-116">ClaimTypes. Role</span><span class="sxs-lookup"><span data-stu-id="21ba1-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="21ba1-117">секуритистампклаимтипе</span><span class="sxs-lookup"><span data-stu-id="21ba1-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="21ba1-118">Возвращает или задает тип утверждения, используемого для утверждения метки безопасности.</span><span class="sxs-lookup"><span data-stu-id="21ba1-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="21ba1-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="21ba1-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="21ba1-120">Возвращает или задает тип утверждения, используемого для утверждения идентификатора пользователя.</span><span class="sxs-lookup"><span data-stu-id="21ba1-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="21ba1-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="21ba1-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="21ba1-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="21ba1-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="21ba1-123">Возвращает или задает тип утверждения, используемого для утверждения имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="21ba1-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="21ba1-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="21ba1-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="21ba1-125">Блокирование</span><span class="sxs-lookup"><span data-stu-id="21ba1-125">Lockout</span></span>

<span data-ttu-id="21ba1-126">Блокировка задается в методе [пассвордсигнинасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :</span><span class="sxs-lookup"><span data-stu-id="21ba1-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="21ba1-127">Приведенный выше код основан на шаблоне `Login` Identity.</span><span class="sxs-lookup"><span data-stu-id="21ba1-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="21ba1-128">Параметры блокировки задаются `StartUp.ConfigureServices`в:</span><span class="sxs-lookup"><span data-stu-id="21ba1-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="21ba1-129">Приведенный выше код задает [идентитйоптионс](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [локкаутоптионс](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) со значениями по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="21ba1-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="21ba1-130">Успешная проверка подлинности сбрасывает количество неудачных попыток доступа и сбрасывает часы.</span><span class="sxs-lookup"><span data-stu-id="21ba1-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="21ba1-131">[Идентитйоптионс. Блокировка](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) задает [локкаутоптионс](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) со свойствами, показанными в таблице.</span><span class="sxs-lookup"><span data-stu-id="21ba1-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="21ba1-132">Свойство.</span><span class="sxs-lookup"><span data-stu-id="21ba1-132">Property</span></span> | <span data-ttu-id="21ba1-133">Описание</span><span class="sxs-lookup"><span data-stu-id="21ba1-133">Description</span></span> | <span data-ttu-id="21ba1-134">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="21ba1-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="21ba1-135">алловедфорневусерс</span><span class="sxs-lookup"><span data-stu-id="21ba1-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="21ba1-136">Определяет, можно ли заблокировать нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="21ba1-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="21ba1-137">дефаултлоккауттимеспан</span><span class="sxs-lookup"><span data-stu-id="21ba1-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="21ba1-138">Количество времени, в течение которого пользователь блокируется в случае блокировки.</span><span class="sxs-lookup"><span data-stu-id="21ba1-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="21ba1-139">5 мин</span><span class="sxs-lookup"><span data-stu-id="21ba1-139">5 minutes</span></span> |
| [<span data-ttu-id="21ba1-140">максфаиледакцессаттемптс</span><span class="sxs-lookup"><span data-stu-id="21ba1-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="21ba1-141">Число неудачных попыток доступа, пока пользователь не будет заблокирован, если блокировка включена.</span><span class="sxs-lookup"><span data-stu-id="21ba1-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="21ba1-142">5</span><span class="sxs-lookup"><span data-stu-id="21ba1-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="21ba1-143">Пароль</span><span class="sxs-lookup"><span data-stu-id="21ba1-143">Password</span></span>

<span data-ttu-id="21ba1-144">По умолчанию для удостоверения требуется, чтобы пароли содержали символы в верхнем регистре, символы нижнего регистра, цифры и символы, отличные от буквенно-цифровых.</span><span class="sxs-lookup"><span data-stu-id="21ba1-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="21ba1-145">Пароли должны иметь длину не менее шести символов.</span><span class="sxs-lookup"><span data-stu-id="21ba1-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="21ba1-146">[Пассвордоптионс](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) можно задать в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="21ba1-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="21ba1-147">[Идентитйоптионс. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) указывает [пассвордоптионс](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) с свойствами, показанными в таблице.</span><span class="sxs-lookup"><span data-stu-id="21ba1-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="21ba1-148">Свойство.</span><span class="sxs-lookup"><span data-stu-id="21ba1-148">Property</span></span> | <span data-ttu-id="21ba1-149">Описание</span><span class="sxs-lookup"><span data-stu-id="21ba1-149">Description</span></span> | <span data-ttu-id="21ba1-150">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="21ba1-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="21ba1-151">рекуиредигит</span><span class="sxs-lookup"><span data-stu-id="21ba1-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="21ba1-152">Требуется число от 0-9 в пароле.</span><span class="sxs-lookup"><span data-stu-id="21ba1-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="21ba1-153">рекуиредленгс</span><span class="sxs-lookup"><span data-stu-id="21ba1-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="21ba1-154">Минимальная длина пароля.</span><span class="sxs-lookup"><span data-stu-id="21ba1-154">The minimum length of the password.</span></span> | <span data-ttu-id="21ba1-155">6</span><span class="sxs-lookup"><span data-stu-id="21ba1-155">6</span></span> |
| [<span data-ttu-id="21ba1-156">рекуиреловеркасе</span><span class="sxs-lookup"><span data-stu-id="21ba1-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="21ba1-157">В пароле требуется символ нижнего регистра.</span><span class="sxs-lookup"><span data-stu-id="21ba1-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="21ba1-158">рекуиреноналфанумерик</span><span class="sxs-lookup"><span data-stu-id="21ba1-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="21ba1-159">В пароле требуется символ, отличный от буквенно-цифрового.</span><span class="sxs-lookup"><span data-stu-id="21ba1-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="21ba1-160">рекуиредуникуечарс</span><span class="sxs-lookup"><span data-stu-id="21ba1-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="21ba1-161">Применяется только к ASP.NET Core 2,0 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="21ba1-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="21ba1-162">Требуется число уникальных символов в пароле.</span><span class="sxs-lookup"><span data-stu-id="21ba1-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="21ba1-163">1</span><span class="sxs-lookup"><span data-stu-id="21ba1-163">1</span></span> |
| [<span data-ttu-id="21ba1-164">рекуиреупперкасе</span><span class="sxs-lookup"><span data-stu-id="21ba1-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="21ba1-165">В пароле требуется символ верхнего регистра.</span><span class="sxs-lookup"><span data-stu-id="21ba1-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="21ba1-166">Свойство.</span><span class="sxs-lookup"><span data-stu-id="21ba1-166">Property</span></span> | <span data-ttu-id="21ba1-167">Описание</span><span class="sxs-lookup"><span data-stu-id="21ba1-167">Description</span></span> | <span data-ttu-id="21ba1-168">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="21ba1-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="21ba1-169">рекуиредигит</span><span class="sxs-lookup"><span data-stu-id="21ba1-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="21ba1-170">Требуется число от 0-9 в пароле.</span><span class="sxs-lookup"><span data-stu-id="21ba1-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="21ba1-171">рекуиредленгс</span><span class="sxs-lookup"><span data-stu-id="21ba1-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="21ba1-172">Минимальная длина пароля.</span><span class="sxs-lookup"><span data-stu-id="21ba1-172">The minimum length of the password.</span></span> | <span data-ttu-id="21ba1-173">6</span><span class="sxs-lookup"><span data-stu-id="21ba1-173">6</span></span> |
| [<span data-ttu-id="21ba1-174">рекуиреловеркасе</span><span class="sxs-lookup"><span data-stu-id="21ba1-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="21ba1-175">В пароле требуется символ нижнего регистра.</span><span class="sxs-lookup"><span data-stu-id="21ba1-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="21ba1-176">рекуиреноналфанумерик</span><span class="sxs-lookup"><span data-stu-id="21ba1-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="21ba1-177">В пароле требуется символ, отличный от буквенно-цифрового.</span><span class="sxs-lookup"><span data-stu-id="21ba1-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="21ba1-178">рекуиреупперкасе</span><span class="sxs-lookup"><span data-stu-id="21ba1-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="21ba1-179">В пароле требуется символ верхнего регистра.</span><span class="sxs-lookup"><span data-stu-id="21ba1-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="21ba1-180">Вход</span><span class="sxs-lookup"><span data-stu-id="21ba1-180">Sign-in</span></span>

<span data-ttu-id="21ba1-181">Следующий код задает `SignIn` параметры (значения по умолчанию):</span><span class="sxs-lookup"><span data-stu-id="21ba1-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="21ba1-182">[Идентитйоптионс. Signing](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) указывает [сигниноптионс](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) со свойствами, показанными в таблице.</span><span class="sxs-lookup"><span data-stu-id="21ba1-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="21ba1-183">Свойство.</span><span class="sxs-lookup"><span data-stu-id="21ba1-183">Property</span></span> | <span data-ttu-id="21ba1-184">Описание</span><span class="sxs-lookup"><span data-stu-id="21ba1-184">Description</span></span> | <span data-ttu-id="21ba1-185">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="21ba1-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="21ba1-186">рекуиреконфирмедемаил</span><span class="sxs-lookup"><span data-stu-id="21ba1-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="21ba1-187">Для входа требуется подтвержденное электронное письмо.</span><span class="sxs-lookup"><span data-stu-id="21ba1-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="21ba1-188">рекуиреконфирмедфоненумбер</span><span class="sxs-lookup"><span data-stu-id="21ba1-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="21ba1-189">Для входа требуется подтвержденный номер телефона.</span><span class="sxs-lookup"><span data-stu-id="21ba1-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="21ba1-190">Токены</span><span class="sxs-lookup"><span data-stu-id="21ba1-190">Tokens</span></span>

<span data-ttu-id="21ba1-191">[Идентитйоптионс. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) указывает [токеноптионс](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) с помощью свойств, показанных в таблице.</span><span class="sxs-lookup"><span data-stu-id="21ba1-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="21ba1-192">Свойство.</span><span class="sxs-lookup"><span data-stu-id="21ba1-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="21ba1-193">Описание</span><span class="sxs-lookup"><span data-stu-id="21ba1-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="21ba1-194">аусентикатортокенпровидер</span><span class="sxs-lookup"><span data-stu-id="21ba1-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="21ba1-195">Возвращает или задает объект `AuthenticatorTokenProvider` , используемый для проверки двухфакторной регистрации с помощью средства проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="21ba1-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="21ba1-196">чанжеемаилтокенпровидер</span><span class="sxs-lookup"><span data-stu-id="21ba1-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="21ba1-197">Возвращает или задает объект `ChangeEmailTokenProvider` , используемый для создания токенов, используемых в сообщениях электронной почты с подтверждением изменений.</span><span class="sxs-lookup"><span data-stu-id="21ba1-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="21ba1-198">чанжефоненумбертокенпровидер</span><span class="sxs-lookup"><span data-stu-id="21ba1-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="21ba1-199">Возвращает или задает объект `ChangePhoneNumberTokenProvider` , используемый для создания маркеров, используемых при изменении номеров телефонов.</span><span class="sxs-lookup"><span data-stu-id="21ba1-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="21ba1-200">емаилконфирматионтокенпровидер</span><span class="sxs-lookup"><span data-stu-id="21ba1-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="21ba1-201">Возвращает или задает поставщик токенов, используемый для создания токенов, используемых в сообщениях электронной почты с подтверждением учетной записи.</span><span class="sxs-lookup"><span data-stu-id="21ba1-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="21ba1-202">пассвордресеттокенпровидер</span><span class="sxs-lookup"><span data-stu-id="21ba1-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="21ba1-203">Возвращает или задает [>Тусер\<иусертвофактортокенпровидер](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) , используемый для создания токенов, используемых в сообщениях электронной почты для сброса пароля.</span><span class="sxs-lookup"><span data-stu-id="21ba1-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="21ba1-204">провидермап</span><span class="sxs-lookup"><span data-stu-id="21ba1-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="21ba1-205">Используется для создания [поставщика пользовательского токена](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) с ключом, используемым в качестве имени поставщика.</span><span class="sxs-lookup"><span data-stu-id="21ba1-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="21ba1-206">Пользователь</span><span class="sxs-lookup"><span data-stu-id="21ba1-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="21ba1-207">[Идентитйоптионс. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) указывает [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) с свойствами, показанными в таблице.</span><span class="sxs-lookup"><span data-stu-id="21ba1-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="21ba1-208">Свойство.</span><span class="sxs-lookup"><span data-stu-id="21ba1-208">Property</span></span> | <span data-ttu-id="21ba1-209">Описание</span><span class="sxs-lookup"><span data-stu-id="21ba1-209">Description</span></span> | <span data-ttu-id="21ba1-210">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="21ba1-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="21ba1-211">алловедусернамечарактерс</span><span class="sxs-lookup"><span data-stu-id="21ba1-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="21ba1-212">Допустимые символы в имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="21ba1-212">Allowed characters in the username.</span></span> | <span data-ttu-id="21ba1-213">абкдефгхижклмнопкрстуввксиз</span><span class="sxs-lookup"><span data-stu-id="21ba1-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="21ba1-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="21ba1-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="21ba1-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="21ba1-215">0123456789</span></span><br><span data-ttu-id="21ba1-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="21ba1-216">-.\_@+</span></span> |
| [<span data-ttu-id="21ba1-217">рекуиреуникуимаил</span><span class="sxs-lookup"><span data-stu-id="21ba1-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="21ba1-218">Требует, чтобы каждый пользователь имел уникальный адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="21ba1-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="21ba1-219">Параметры файлов cookie</span><span class="sxs-lookup"><span data-stu-id="21ba1-219">Cookie settings</span></span>

<span data-ttu-id="21ba1-220">Настройте файл cookie приложения в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="21ba1-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="21ba1-221">[Конфигуреаппликатионкукие](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) должен вызываться **после** вызова `AddIdentity` метода `AddDefaultIdentity`или.</span><span class="sxs-lookup"><span data-stu-id="21ba1-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="21ba1-222">Дополнительные сведения см. в разделе [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span><span class="sxs-lookup"><span data-stu-id="21ba1-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="21ba1-223">Параметры хэша пароля</span><span class="sxs-lookup"><span data-stu-id="21ba1-223">Password Hasher options</span></span>

<span data-ttu-id="21ba1-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Возвращает и задает параметры хэширования паролей.</span><span class="sxs-lookup"><span data-stu-id="21ba1-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="21ba1-225">Параметр</span><span class="sxs-lookup"><span data-stu-id="21ba1-225">Option</span></span> | <span data-ttu-id="21ba1-226">Описание:</span><span class="sxs-lookup"><span data-stu-id="21ba1-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="21ba1-227">Режим совместимости, используемый при хэшировании новых паролей.</span><span class="sxs-lookup"><span data-stu-id="21ba1-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="21ba1-228">По умолчанию имеет значение <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span><span class="sxs-lookup"><span data-stu-id="21ba1-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="21ba1-229">Первый байт хэшированного пароля, называемый *маркером формата*, указывает версию алгоритма хэширования, используемого для хэширования пароля.</span><span class="sxs-lookup"><span data-stu-id="21ba1-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="21ba1-230">При проверке пароля по хэшу <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> метод выбирает правильный алгоритм на основе первого байта.</span><span class="sxs-lookup"><span data-stu-id="21ba1-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="21ba1-231">Клиент может проходить проверку подлинности независимо от того, какая версия алгоритма использовалась для хэширования пароля.</span><span class="sxs-lookup"><span data-stu-id="21ba1-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="21ba1-232">Установка режима совместимости влияет на хэширование *новых паролей*.</span><span class="sxs-lookup"><span data-stu-id="21ba1-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="21ba1-233">Число итераций, используемых при хэшировании паролей с помощью PBKDF2.</span><span class="sxs-lookup"><span data-stu-id="21ba1-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="21ba1-234">Это значение используется только в том случае <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> , если свойству присвоено значение <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span><span class="sxs-lookup"><span data-stu-id="21ba1-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="21ba1-235">Значение должно быть положительным целым числом, а по `10000`умолчанию —.</span><span class="sxs-lookup"><span data-stu-id="21ba1-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="21ba1-236">В следующем примере <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> для `12000` задано значение в: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="21ba1-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
