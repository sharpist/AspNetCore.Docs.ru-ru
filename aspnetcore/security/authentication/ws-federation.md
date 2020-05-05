---
title: Проверка подлинности пользователей с помощью WS-Federation в ASP.NET Core
author: chlowell
description: В этом руководстве показано, как использовать WS-Federation в приложении ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: ce0c484e84bc2ddb4a1d287246c63663f3875924
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768433"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="488ec-103">Проверка подлинности пользователей с помощью WS-Federation в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="488ec-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="488ec-104">В этом руководстве показано, как разрешить пользователям выполнять вход с помощью поставщика проверки подлинности WS-Federation, например службы федерации Active Directory (AD FS) (ADFS) или [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="488ec-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="488ec-105">В нем используется пример приложения ASP.NET Core 2,0, описанный в статье [Аутентификация Facebook, Google и внешнего поставщика](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="488ec-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="488ec-106">Для приложений ASP.NET Core 2,0 поддержка WS-Federation обеспечивается [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="488ec-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="488ec-107">Этот компонент переносится из [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) и разделяет многие механики этого компонента.</span><span class="sxs-lookup"><span data-stu-id="488ec-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="488ec-108">Однако эти компоненты отличаются друг от друга несколькими важными способами.</span><span class="sxs-lookup"><span data-stu-id="488ec-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="488ec-109">По умолчанию новое по промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="488ec-109">By default, the new middleware:</span></span>

* <span data-ttu-id="488ec-110">Не разрешает незапрошенные имена входа.</span><span class="sxs-lookup"><span data-stu-id="488ec-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="488ec-111">Эта функция протокола WS-Federation уязвима для атак XSRF.</span><span class="sxs-lookup"><span data-stu-id="488ec-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="488ec-112">Однако его можно включить с помощью `AllowUnsolicitedLogins` параметра.</span><span class="sxs-lookup"><span data-stu-id="488ec-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="488ec-113">Не проверяет каждую форму POST для сообщений входа.</span><span class="sxs-lookup"><span data-stu-id="488ec-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="488ec-114">Только запросы к компоненту проверяются `CallbackPath` на наличие входов. `CallbackPath` значение `/signin-wsfed` по умолчанию —, но его можно изменить с помощью унаследованного свойства [ремотеаусентикатионоптионс. каллбаккпас](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [всфедератионоптионс](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) .</span><span class="sxs-lookup"><span data-stu-id="488ec-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="488ec-115">Этот путь можно использовать совместно с другими поставщиками проверки подлинности, включив параметр [скипунрекогнизедрекуестс](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .</span><span class="sxs-lookup"><span data-stu-id="488ec-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="488ec-116">Регистрация приложения в Active Directory</span><span class="sxs-lookup"><span data-stu-id="488ec-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="488ec-117">Службы федерации Active Directory (AD FS)</span><span class="sxs-lookup"><span data-stu-id="488ec-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="488ec-118">Откройте **Мастер добавления отношения доверия с проверяющей стороной** на сервере из консоли управления ADFS:</span><span class="sxs-lookup"><span data-stu-id="488ec-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Мастер добавления отношения доверия с проверяющей стороной: Добро пожаловать](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="488ec-120">Выберите Ввод данных вручную:</span><span class="sxs-lookup"><span data-stu-id="488ec-120">Choose to enter data manually:</span></span>

![Мастер добавления отношения доверия с проверяющей стороной: Выбор источника данных](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="488ec-122">Введите отображаемое имя проверяющей стороны.</span><span class="sxs-lookup"><span data-stu-id="488ec-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="488ec-123">Это имя не имеет значения для приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="488ec-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="488ec-124">В [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) отсутствует поддержка шифрования маркеров, поэтому не настраивайте сертификат шифрования маркеров:</span><span class="sxs-lookup"><span data-stu-id="488ec-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Мастер добавления отношения доверия с проверяющей стороной: Настройка сертификата](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="488ec-126">Включите поддержку пассивного протокола WS-Federation с помощью URL-адреса приложения.</span><span class="sxs-lookup"><span data-stu-id="488ec-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="488ec-127">Проверьте правильность порта для приложения:</span><span class="sxs-lookup"><span data-stu-id="488ec-127">Verify the port is correct for the app:</span></span>

![Мастер добавления отношения доверия с проверяющей стороной: Настройка URL-адреса](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="488ec-129">Это должен быть URL-адрес HTTPS.</span><span class="sxs-lookup"><span data-stu-id="488ec-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="488ec-130">IIS Express может предоставить самозаверяющий сертификат при размещении приложения во время разработки.</span><span class="sxs-lookup"><span data-stu-id="488ec-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="488ec-131">Для Kestrel требуется ручная настройка сертификата.</span><span class="sxs-lookup"><span data-stu-id="488ec-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="488ec-132">Дополнительные сведения см. в [документации по Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="488ec-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="488ec-133">Нажмите кнопку **Далее** в мастере и **закройте** в конце.</span><span class="sxs-lookup"><span data-stu-id="488ec-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="488ec-134">ASP.NET Core Identity требуется утверждение **идентификатора имени** .</span><span class="sxs-lookup"><span data-stu-id="488ec-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="488ec-135">Добавьте его в диалоговом окне **изменение правил утверждений** :</span><span class="sxs-lookup"><span data-stu-id="488ec-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Изменение правил утверждения](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="488ec-137">В **мастере добавления правила преобразования утверждений**Оставьте выбранный по умолчанию шаблон **отправки атрибутов LDAP в качестве шаблона утверждений** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="488ec-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="488ec-138">Добавьте правило, сопоставленное с атрибутом **SAM-Account-Name** LDAP к исходящему утверждению **ID** :</span><span class="sxs-lookup"><span data-stu-id="488ec-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Мастер добавления правила преобразования утверждений: Настройка правила для утверждений](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="488ec-140">В окне **изменение правил утверждений** нажмите кнопку **Готово** > **ОК** .</span><span class="sxs-lookup"><span data-stu-id="488ec-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="488ec-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="488ec-141">Azure Active Directory</span></span>

* <span data-ttu-id="488ec-142">Перейдите в колонку регистрации приложений клиента AAD.</span><span class="sxs-lookup"><span data-stu-id="488ec-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="488ec-143">Щелкните **Регистрация нового приложения**:</span><span class="sxs-lookup"><span data-stu-id="488ec-143">Click **New application registration**:</span></span>

![Azure Active Directory: Регистрация приложений](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="488ec-145">Введите имя для регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="488ec-145">Enter a name for the app registration.</span></span> <span data-ttu-id="488ec-146">Это не имеет значения для ASP.NET Core приложения.</span><span class="sxs-lookup"><span data-stu-id="488ec-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="488ec-147">Введите URL-адрес, который прослушивает приложение в качестве **URL-адреса входа**:</span><span class="sxs-lookup"><span data-stu-id="488ec-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: создание регистрации приложения](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="488ec-149">Щелкните **конечные точки** и запишите URL-адрес **документа метаданных федерации** .</span><span class="sxs-lookup"><span data-stu-id="488ec-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="488ec-150">Это по промежуточного слоя WS-Federation `MetadataAddress`:</span><span class="sxs-lookup"><span data-stu-id="488ec-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: конечные точки](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="488ec-152">Перейдите к новой регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="488ec-152">Navigate to the new app registration.</span></span> <span data-ttu-id="488ec-153">Щелкните **Параметры** > **Свойства** и запишите **URI идентификатора приложения**.</span><span class="sxs-lookup"><span data-stu-id="488ec-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="488ec-154">Это по промежуточного слоя WS-Federation `Wtrealm`:</span><span class="sxs-lookup"><span data-stu-id="488ec-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: свойства регистрации приложения](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="488ec-156">Использование WS-Federation без ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="488ec-156">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="488ec-157">По промежуточного слоя WS-Federation можно использовать Identityбез.</span><span class="sxs-lookup"><span data-stu-id="488ec-157">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="488ec-158">Например:</span><span class="sxs-lookup"><span data-stu-id="488ec-158">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="488ec-159">Добавление WS-Federation в качестве внешнего поставщика входа для ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="488ec-159">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="488ec-160">Добавьте зависимость от [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) в проект.</span><span class="sxs-lookup"><span data-stu-id="488ec-160">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="488ec-161">Добавить WS-Federation в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="488ec-161">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="488ec-162">Вход с помощью WS-Federation</span><span class="sxs-lookup"><span data-stu-id="488ec-162">Log in with WS-Federation</span></span>

<span data-ttu-id="488ec-163">Перейдите к приложению и щелкните ссылку **Вход** в заголовке навигации.</span><span class="sxs-lookup"><span data-stu-id="488ec-163">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="488ec-164">Есть возможность войти с помощью WsFederation: Log On Page ![.](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="488ec-164">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="488ec-165">При использовании ADFS в качестве поставщика кнопка перенаправляется на страницу входа ADFS: ![страница входа ADFS](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="488ec-165">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="488ec-166">При Azure Active Directory в качестве поставщика кнопка перенаправляется на страницу входа AAD: страница входа AAD. ![](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="488ec-166">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="488ec-167">При успешном входе нового пользователя выполняется перенаправление на страницу регистрации пользователя приложения: ![регистрация страницы](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="488ec-167">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>