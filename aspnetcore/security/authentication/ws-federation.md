---
title: Проверка подлинности пользователей с помощью WS-Federation в ASP.NET Core
author: chlowell
description: В этом руководстве показано, как использовать WS-Federation в приложении ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: e303679190a7d7f42d8525541cec031ba090fd7a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022307"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="68ab4-103">Проверка подлинности пользователей с помощью WS-Federation в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68ab4-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="68ab4-104">В этом руководстве показано, как разрешить пользователям выполнять вход с помощью поставщика проверки подлинности WS-Federation, например службы федерации Active Directory (AD FS) (ADFS) или [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="68ab4-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="68ab4-105">В нем используется ASP.NET Core пример приложения, описанный в статье [Аутентификация Facebook, Google и внешнего поставщика](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="68ab4-105">It uses the ASP.NET Core sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="68ab4-106">Для ASP.NET Core приложений Поддержка WS-Federation предоставляется службой [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="68ab4-106">For ASP.NET Core apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="68ab4-107">Этот компонент переносится из [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) и разделяет многие механики этого компонента.</span><span class="sxs-lookup"><span data-stu-id="68ab4-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="68ab4-108">Однако эти компоненты отличаются друг от друга несколькими важными способами.</span><span class="sxs-lookup"><span data-stu-id="68ab4-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="68ab4-109">По умолчанию новое по промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="68ab4-109">By default, the new middleware:</span></span>

* <span data-ttu-id="68ab4-110">Не разрешает незапрошенные имена входа.</span><span class="sxs-lookup"><span data-stu-id="68ab4-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="68ab4-111">Эта функция протокола WS-Federation уязвима для атак XSRF.</span><span class="sxs-lookup"><span data-stu-id="68ab4-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="68ab4-112">Однако его можно включить с помощью `AllowUnsolicitedLogins` параметра.</span><span class="sxs-lookup"><span data-stu-id="68ab4-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="68ab4-113">Не проверяет каждую форму POST для сообщений входа.</span><span class="sxs-lookup"><span data-stu-id="68ab4-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="68ab4-114">Только запросы к `CallbackPath` компоненту проверяются на наличие входов. `CallbackPath` значение по умолчанию —, `/signin-wsfed` но его можно изменить с помощью унаследованного свойства [Ремотеаусентикатионоптионс. каллбаккпас](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [всфедератионоптионс](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) .</span><span class="sxs-lookup"><span data-stu-id="68ab4-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="68ab4-115">Этот путь можно использовать совместно с другими поставщиками проверки подлинности, включив параметр [скипунрекогнизедрекуестс](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .</span><span class="sxs-lookup"><span data-stu-id="68ab4-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="68ab4-116">Регистрация приложения в Active Directory</span><span class="sxs-lookup"><span data-stu-id="68ab4-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="68ab4-117">службы федерации Active Directory;</span><span class="sxs-lookup"><span data-stu-id="68ab4-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="68ab4-118">Откройте **Мастер добавления отношения доверия с проверяющей стороной** на сервере из консоли управления ADFS:</span><span class="sxs-lookup"><span data-stu-id="68ab4-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Мастер добавления отношения доверия с проверяющей стороной: Добро пожаловать](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="68ab4-120">Выберите Ввод данных вручную:</span><span class="sxs-lookup"><span data-stu-id="68ab4-120">Choose to enter data manually:</span></span>

![Мастер добавления отношения доверия с проверяющей стороной: Выбор источника данных](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="68ab4-122">Введите отображаемое имя проверяющей стороны.</span><span class="sxs-lookup"><span data-stu-id="68ab4-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="68ab4-123">Это имя не имеет значения для приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68ab4-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="68ab4-124">В [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) отсутствует поддержка шифрования маркеров, поэтому не настраивайте сертификат шифрования маркеров:</span><span class="sxs-lookup"><span data-stu-id="68ab4-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Мастер добавления отношения доверия с проверяющей стороной: Настройка сертификата](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="68ab4-126">Включите поддержку пассивного протокола WS-Federation с помощью URL-адреса приложения.</span><span class="sxs-lookup"><span data-stu-id="68ab4-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="68ab4-127">Проверьте правильность порта для приложения:</span><span class="sxs-lookup"><span data-stu-id="68ab4-127">Verify the port is correct for the app:</span></span>

![Мастер добавления отношения доверия с проверяющей стороной: Настройка URL-адреса](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="68ab4-129">Это должен быть URL-адрес HTTPS.</span><span class="sxs-lookup"><span data-stu-id="68ab4-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="68ab4-130">IIS Express может предоставить самозаверяющий сертификат при размещении приложения во время разработки.</span><span class="sxs-lookup"><span data-stu-id="68ab4-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="68ab4-131">Для Kestrel требуется ручная настройка сертификата.</span><span class="sxs-lookup"><span data-stu-id="68ab4-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="68ab4-132">Дополнительные сведения см. в [документации по Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="68ab4-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="68ab4-133">Нажмите кнопку **Далее** в мастере и **закройте** в конце.</span><span class="sxs-lookup"><span data-stu-id="68ab4-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="68ab4-134">ASP.NET Core Identity требуется утверждение **идентификатора имени** .</span><span class="sxs-lookup"><span data-stu-id="68ab4-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="68ab4-135">Добавьте его в диалоговом окне **изменение правил утверждений** :</span><span class="sxs-lookup"><span data-stu-id="68ab4-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Изменение правил утверждения](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="68ab4-137">В **мастере добавления правила преобразования утверждений**Оставьте выбранный по умолчанию шаблон **отправки атрибутов LDAP в качестве шаблона утверждений** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="68ab4-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="68ab4-138">Добавьте правило, сопоставленное с атрибутом **SAM-Account-Name** LDAP к исходящему утверждению **ID** :</span><span class="sxs-lookup"><span data-stu-id="68ab4-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Мастер добавления правила преобразования утверждений: Настройка правила для утверждений](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="68ab4-140">**Finish**  >  В окне **изменение правил утверждений** нажмите кнопку Готово**ОК** .</span><span class="sxs-lookup"><span data-stu-id="68ab4-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="68ab4-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="68ab4-141">Azure Active Directory</span></span>

* <span data-ttu-id="68ab4-142">Перейдите в колонку регистрации приложений клиента AAD.</span><span class="sxs-lookup"><span data-stu-id="68ab4-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="68ab4-143">Щелкните **Регистрация нового приложения**:</span><span class="sxs-lookup"><span data-stu-id="68ab4-143">Click **New application registration**:</span></span>

![Azure Active Directory: Регистрация приложений](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="68ab4-145">Введите имя для регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="68ab4-145">Enter a name for the app registration.</span></span> <span data-ttu-id="68ab4-146">Это не имеет значения для ASP.NET Core приложения.</span><span class="sxs-lookup"><span data-stu-id="68ab4-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="68ab4-147">Введите URL-адрес, который прослушивает приложение в качестве **URL-адреса входа**:</span><span class="sxs-lookup"><span data-stu-id="68ab4-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: создание регистрации приложения](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="68ab4-149">Щелкните **конечные точки** и запишите URL-адрес **документа метаданных федерации** .</span><span class="sxs-lookup"><span data-stu-id="68ab4-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="68ab4-150">Это по промежуточного слоя WS-Federation `MetadataAddress` :</span><span class="sxs-lookup"><span data-stu-id="68ab4-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: конечные точки](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="68ab4-152">Перейдите к новой регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="68ab4-152">Navigate to the new app registration.</span></span> <span data-ttu-id="68ab4-153">Щелкните **предоставить API**.</span><span class="sxs-lookup"><span data-stu-id="68ab4-153">Click **Expose an API**.</span></span> <span data-ttu-id="68ab4-154">Щелкните идентификатор приложения URI **Set**  >  **Save (сохранить**).</span><span class="sxs-lookup"><span data-stu-id="68ab4-154">Click Application ID URI **Set** > **Save**.</span></span> <span data-ttu-id="68ab4-155">Запишите **URI идентификатора приложения**.</span><span class="sxs-lookup"><span data-stu-id="68ab4-155">Make note of the  **Application ID URI**.</span></span> <span data-ttu-id="68ab4-156">Это по промежуточного слоя WS-Federation `Wtrealm` :</span><span class="sxs-lookup"><span data-stu-id="68ab4-156">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: свойства регистрации приложения](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-no-locidentity"></a><span data-ttu-id="68ab4-158">Использование WS-Federation без ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="68ab4-158">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="68ab4-159">По промежуточного слоя WS-Federation можно использовать без Identity .</span><span class="sxs-lookup"><span data-stu-id="68ab4-159">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="68ab4-160">Например:</span><span class="sxs-lookup"><span data-stu-id="68ab4-160">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-no-locidentity"></a><span data-ttu-id="68ab4-161">Добавление WS-Federation в качестве внешнего поставщика входа для ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="68ab4-161">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="68ab4-162">Добавьте зависимость от [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) в проект.</span><span class="sxs-lookup"><span data-stu-id="68ab4-162">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="68ab4-163">Добавить WS-Federation в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="68ab4-163">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="68ab4-164">Вход с помощью WS-Federation</span><span class="sxs-lookup"><span data-stu-id="68ab4-164">Log in with WS-Federation</span></span>

<span data-ttu-id="68ab4-165">Перейдите к приложению и щелкните ссылку **Вход** в заголовке навигации.</span><span class="sxs-lookup"><span data-stu-id="68ab4-165">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="68ab4-166">Есть возможность войти с помощью WsFederation: ![ Log On Page.](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="68ab4-166">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="68ab4-167">При использовании ADFS в качестве поставщика кнопка перенаправляется на страницу входа ADFS: ![ страница входа ADFS](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="68ab4-167">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="68ab4-168">При Azure Active Directory в качестве поставщика кнопка перенаправляется на страницу входа AAD: ![ страница входа AAD.](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="68ab4-168">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="68ab4-169">При успешном входе нового пользователя выполняется перенаправление на страницу регистрации пользователя приложения: ![ Регистрация страницы](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="68ab4-169">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>
