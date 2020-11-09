---
title: Сохранение дополнительных утверждений и маркеров от внешних поставщиков в ASP.NET Core
author: rick-anderson
description: Узнайте, как устанавливать дополнительные утверждения и токены от внешних поставщиков.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 4503291ff887f79b1ad6eacd4e56943ce23335bc
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141512"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="c2059-103">Сохранение дополнительных утверждений и маркеров от внешних поставщиков в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2059-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2059-104">Приложение ASP.NET Core может устанавливать дополнительные утверждения и маркеры от внешних поставщиков проверки подлинности, таких как Facebook, Google, Microsoft и Twitter.</span><span class="sxs-lookup"><span data-stu-id="c2059-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="c2059-105">Каждый поставщик раскрывает разные сведения о пользователях на своей платформе, но шаблон для получения и преобразования пользовательских данных в дополнительные утверждения одинаковы.</span><span class="sxs-lookup"><span data-stu-id="c2059-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="c2059-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2059-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c2059-107">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="c2059-107">Prerequisites</span></span>

<span data-ttu-id="c2059-108">Решите, какие внешние поставщики проверки подлинности поддерживаются в приложении.</span><span class="sxs-lookup"><span data-stu-id="c2059-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="c2059-109">Для каждого поставщика Зарегистрируйте приложение и получите идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="c2059-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="c2059-110">Для получения дополнительной информации см. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="c2059-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="c2059-111">В примере приложения используется [поставщик проверки подлинности Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="c2059-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="c2059-112">Задание идентификатора клиента и секрета клиента</span><span class="sxs-lookup"><span data-stu-id="c2059-112">Set the client ID and client secret</span></span>

<span data-ttu-id="c2059-113">Поставщик проверки подлинности OAuth устанавливает отношение доверия с приложением, используя идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="c2059-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="c2059-114">Идентификатор клиента и значения секрета клиента создаются для приложения внешним поставщиком проверки подлинности при регистрации приложения в поставщике.</span><span class="sxs-lookup"><span data-stu-id="c2059-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="c2059-115">Каждый внешний поставщик, используемый приложением, должен быть настроен независимо с ИДЕНТИФИКАТОРом клиента и секретом клиента поставщика.</span><span class="sxs-lookup"><span data-stu-id="c2059-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="c2059-116">Дополнительные сведения см. в разделах, посвященных внешнему поставщику проверки подлинности, которые относятся к вашему сценарию.</span><span class="sxs-lookup"><span data-stu-id="c2059-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="c2059-117">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="c2059-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="c2059-118">Проверка подлинности Google</span><span class="sxs-lookup"><span data-stu-id="c2059-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="c2059-119">Проверка подлинности Майкрософт</span><span class="sxs-lookup"><span data-stu-id="c2059-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="c2059-120">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="c2059-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="c2059-121">Другие поставщики проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="c2059-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="c2059-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="c2059-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="c2059-123">Пример приложения настраивает поставщик проверки подлинности Google с ИДЕНТИФИКАТОРом клиента и секретом клиента, предоставленным Google:</span><span class="sxs-lookup"><span data-stu-id="c2059-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="c2059-124">Определение области проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="c2059-124">Establish the authentication scope</span></span>

<span data-ttu-id="c2059-125">Укажите список разрешений для получения от поставщика, указав <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="c2059-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="c2059-126">В следующей таблице приведены области проверки подлинности для общих внешних поставщиков.</span><span class="sxs-lookup"><span data-stu-id="c2059-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="c2059-127">Поставщик</span><span class="sxs-lookup"><span data-stu-id="c2059-127">Provider</span></span>  | <span data-ttu-id="c2059-128">Область</span><span class="sxs-lookup"><span data-stu-id="c2059-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="c2059-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="c2059-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="c2059-130">Google</span><span class="sxs-lookup"><span data-stu-id="c2059-130">Google</span></span>    | <span data-ttu-id="c2059-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="c2059-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="c2059-132">Майкрософт</span><span class="sxs-lookup"><span data-stu-id="c2059-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="c2059-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="c2059-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="c2059-134">В примере приложения `profile` платформа Google, `email` и `openid` области автоматически добавляются платформой при <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> вызове в <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="c2059-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="c2059-135">Если приложению требуются дополнительные области, добавьте их в параметры.</span><span class="sxs-lookup"><span data-stu-id="c2059-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="c2059-136">В следующем примере `https://www.googleapis.com/auth/user.birthday.read` добавлена область Google для получения дня рождения пользователя:</span><span class="sxs-lookup"><span data-stu-id="c2059-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="c2059-137">Сопоставьте ключи данных пользователя и создайте утверждения</span><span class="sxs-lookup"><span data-stu-id="c2059-137">Map user data keys and create claims</span></span>

<span data-ttu-id="c2059-138">В параметрах поставщика укажите <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> или для каждого ключа или подключа <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> в данных пользователя JSON внешнего поставщика, чтобы удостоверение приложения было прочитано при входе.</span><span class="sxs-lookup"><span data-stu-id="c2059-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="c2059-139">Дополнительные сведения о типах утверждений см. в разделе <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="c2059-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="c2059-140">Пример приложения создает утверждения locale ( `urn:google:locale` ) и Picture ( `urn:google:picture` ) из `locale` `picture` ключей и в данных пользователя Google:</span><span class="sxs-lookup"><span data-stu-id="c2059-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="c2059-141">В службах `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) выполняется вход в приложение с помощью <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="c2059-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="c2059-142">Во время входа в систему <xref:Microsoft.AspNetCore.Identity.UserManager%601> можно хранить `ApplicationUser` утверждения для пользовательских данных, доступных в <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="c2059-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="c2059-143">В примере приложения `OnPostConfirmationAsync` ( *Account/екстерналлогин. cshtml. CS* ) устанавливает утверждения языкового стандарта ( `urn:google:locale` ) и изображения ( `urn:google:picture` ) для входа `ApplicationUser` , включая утверждение для <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="c2059-143">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="c2059-144">По умолчанию утверждения пользователя хранятся в проверке подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="c2059-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="c2059-145">Если проверка подлинности cookie слишком велика, это может привести к сбою приложения по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="c2059-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="c2059-146">Браузер обнаруживает, что cookie заголовок слишком длинный.</span><span class="sxs-lookup"><span data-stu-id="c2059-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="c2059-147">Общий размер запроса слишком велик.</span><span class="sxs-lookup"><span data-stu-id="c2059-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="c2059-148">Если для обработки запросов пользователей требуется большой объем пользовательских данных:</span><span class="sxs-lookup"><span data-stu-id="c2059-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="c2059-149">Ограничьте количество и размер утверждений пользователей для обработки запроса только тем, что требуется приложению.</span><span class="sxs-lookup"><span data-stu-id="c2059-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="c2059-150">Используйте пользовательский <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> по Cookie промежуточного слоя для проверки подлинности <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> для хранения удостоверений в запросах.</span><span class="sxs-lookup"><span data-stu-id="c2059-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="c2059-151">Сохраняйте большие объемы информации об удостоверениях на сервере, при этом клиенту отправляется только небольшой ключ идентификатора сеанса.</span><span class="sxs-lookup"><span data-stu-id="c2059-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="c2059-152">Сохранение маркера доступа</span><span class="sxs-lookup"><span data-stu-id="c2059-152">Save the access token</span></span>

<span data-ttu-id="c2059-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Определяет, должны ли маркеры доступа и обновления храниться в <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> после успешной авторизации.</span><span class="sxs-lookup"><span data-stu-id="c2059-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="c2059-154">`SaveTokens` по `false` умолчанию имеет значение, чтобы уменьшить размер окончательной проверки подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="c2059-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="c2059-155">Пример приложения задает для параметра значение `SaveTokens` `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="c2059-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="c2059-156">Когда `OnPostConfirmationAsync` выполняется, сохраните маркер доступа ([Екстерналлогининфо. аусентикатионтокенс](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) из внешнего поставщика в `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="c2059-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="c2059-157">Пример приложения сохраняет маркер доступа в `OnPostConfirmationAsync` (регистрация нового пользователя) и `OnGetCallbackAsync` (ранее зарегистрированный пользователь) в *Account/екстерналлогин. cshtml. CS* :</span><span class="sxs-lookup"><span data-stu-id="c2059-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="c2059-158">Добавление дополнительных настраиваемых токенов</span><span class="sxs-lookup"><span data-stu-id="c2059-158">How to add additional custom tokens</span></span>

<span data-ttu-id="c2059-159">Чтобы продемонстрировать, как добавить пользовательский маркер, который хранится в составе `SaveTokens` , пример приложения добавляет объект <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> с текущим <xref:System.DateTime> для [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) из `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="c2059-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="c2059-160">Создание и добавление утверждений</span><span class="sxs-lookup"><span data-stu-id="c2059-160">Creating and adding claims</span></span>

<span data-ttu-id="c2059-161">Платформа предоставляет общие действия и методы расширения для создания и добавления заявок в коллекцию.</span><span class="sxs-lookup"><span data-stu-id="c2059-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="c2059-162">Дополнительные сведения см. в разделах <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> и <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="c2059-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="c2059-163">Пользователи могут определять пользовательские действия, производя от <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> и реализуя абстрактный <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> метод.</span><span class="sxs-lookup"><span data-stu-id="c2059-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="c2059-164">Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="c2059-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="c2059-165">Удаление действий утверждений и утверждений</span><span class="sxs-lookup"><span data-stu-id="c2059-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="c2059-166">[Клаимактионколлектион. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) удаляет все действия с утверждениями для заданного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> из коллекции.</span><span class="sxs-lookup"><span data-stu-id="c2059-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="c2059-167">[Клаимактионколлектионмапекстенсионс. делетеклаим (клаимактионколлектион, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) удаляет утверждение для данного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> удостоверения.</span><span class="sxs-lookup"><span data-stu-id="c2059-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="c2059-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> в основном используется с [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) для удаления сформированных протоколом утверждений.</span><span class="sxs-lookup"><span data-stu-id="c2059-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="c2059-169">Выходные данные примера приложения</span><span class="sxs-lookup"><span data-stu-id="c2059-169">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c2059-170">Приложение ASP.NET Core может устанавливать дополнительные утверждения и маркеры от внешних поставщиков проверки подлинности, таких как Facebook, Google, Microsoft и Twitter.</span><span class="sxs-lookup"><span data-stu-id="c2059-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="c2059-171">Каждый поставщик раскрывает разные сведения о пользователях на своей платформе, но шаблон для получения и преобразования пользовательских данных в дополнительные утверждения одинаковы.</span><span class="sxs-lookup"><span data-stu-id="c2059-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="c2059-172">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2059-172">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c2059-173">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="c2059-173">Prerequisites</span></span>

<span data-ttu-id="c2059-174">Решите, какие внешние поставщики проверки подлинности поддерживаются в приложении.</span><span class="sxs-lookup"><span data-stu-id="c2059-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="c2059-175">Для каждого поставщика Зарегистрируйте приложение и получите идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="c2059-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="c2059-176">Для получения дополнительной информации см. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="c2059-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="c2059-177">В примере приложения используется [поставщик проверки подлинности Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="c2059-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="c2059-178">Задание идентификатора клиента и секрета клиента</span><span class="sxs-lookup"><span data-stu-id="c2059-178">Set the client ID and client secret</span></span>

<span data-ttu-id="c2059-179">Поставщик проверки подлинности OAuth устанавливает отношение доверия с приложением, используя идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="c2059-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="c2059-180">Идентификатор клиента и значения секрета клиента создаются для приложения внешним поставщиком проверки подлинности при регистрации приложения в поставщике.</span><span class="sxs-lookup"><span data-stu-id="c2059-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="c2059-181">Каждый внешний поставщик, используемый приложением, должен быть настроен независимо с ИДЕНТИФИКАТОРом клиента и секретом клиента поставщика.</span><span class="sxs-lookup"><span data-stu-id="c2059-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="c2059-182">Дополнительные сведения см. в разделах, посвященных внешнему поставщику проверки подлинности, которые относятся к вашему сценарию.</span><span class="sxs-lookup"><span data-stu-id="c2059-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="c2059-183">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="c2059-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="c2059-184">Проверка подлинности Google</span><span class="sxs-lookup"><span data-stu-id="c2059-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="c2059-185">Проверка подлинности Майкрософт</span><span class="sxs-lookup"><span data-stu-id="c2059-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="c2059-186">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="c2059-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="c2059-187">Другие поставщики проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="c2059-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="c2059-188">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="c2059-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="c2059-189">Пример приложения настраивает поставщик проверки подлинности Google с ИДЕНТИФИКАТОРом клиента и секретом клиента, предоставленным Google:</span><span class="sxs-lookup"><span data-stu-id="c2059-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="c2059-190">Определение области проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="c2059-190">Establish the authentication scope</span></span>

<span data-ttu-id="c2059-191">Укажите список разрешений для получения от поставщика, указав <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="c2059-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="c2059-192">В следующей таблице приведены области проверки подлинности для общих внешних поставщиков.</span><span class="sxs-lookup"><span data-stu-id="c2059-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="c2059-193">Поставщик</span><span class="sxs-lookup"><span data-stu-id="c2059-193">Provider</span></span>  | <span data-ttu-id="c2059-194">Область</span><span class="sxs-lookup"><span data-stu-id="c2059-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="c2059-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="c2059-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="c2059-196">Google</span><span class="sxs-lookup"><span data-stu-id="c2059-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="c2059-197">Майкрософт</span><span class="sxs-lookup"><span data-stu-id="c2059-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="c2059-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="c2059-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="c2059-199">В примере приложения `userinfo.profile` область Google автоматически добавляется платформой при <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> вызове в <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="c2059-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="c2059-200">Если приложению требуются дополнительные области, добавьте их в параметры.</span><span class="sxs-lookup"><span data-stu-id="c2059-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="c2059-201">В следующем примере `https://www.googleapis.com/auth/user.birthday.read` область Google добавляется для получения дня рождения пользователя:</span><span class="sxs-lookup"><span data-stu-id="c2059-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="c2059-202">Сопоставьте ключи данных пользователя и создайте утверждения</span><span class="sxs-lookup"><span data-stu-id="c2059-202">Map user data keys and create claims</span></span>

<span data-ttu-id="c2059-203">В параметрах поставщика укажите <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> или для каждого ключа или подключа <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> в данных пользователя JSON внешнего поставщика, чтобы удостоверение приложения было прочитано при входе.</span><span class="sxs-lookup"><span data-stu-id="c2059-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="c2059-204">Дополнительные сведения о типах утверждений см. в разделе <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="c2059-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="c2059-205">Пример приложения создает утверждения locale ( `urn:google:locale` ) и Picture ( `urn:google:picture` ) из `locale` `picture` ключей и в данных пользователя Google:</span><span class="sxs-lookup"><span data-stu-id="c2059-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="c2059-206">В службах `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) выполняется вход в приложение с помощью <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="c2059-206">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="c2059-207">Во время входа в систему <xref:Microsoft.AspNetCore.Identity.UserManager%601> можно хранить `ApplicationUser` утверждения для пользовательских данных, доступных в <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="c2059-207">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="c2059-208">В примере приложения `OnPostConfirmationAsync` ( *Account/екстерналлогин. cshtml. CS* ) устанавливает утверждения языкового стандарта ( `urn:google:locale` ) и изображения ( `urn:google:picture` ) для входа `ApplicationUser` , включая утверждение для <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="c2059-208">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="c2059-209">По умолчанию утверждения пользователя хранятся в проверке подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="c2059-209">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="c2059-210">Если проверка подлинности cookie слишком велика, это может привести к сбою приложения по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="c2059-210">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="c2059-211">Браузер обнаруживает, что cookie заголовок слишком длинный.</span><span class="sxs-lookup"><span data-stu-id="c2059-211">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="c2059-212">Общий размер запроса слишком велик.</span><span class="sxs-lookup"><span data-stu-id="c2059-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="c2059-213">Если для обработки запросов пользователей требуется большой объем пользовательских данных:</span><span class="sxs-lookup"><span data-stu-id="c2059-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="c2059-214">Ограничьте количество и размер утверждений пользователей для обработки запроса только тем, что требуется приложению.</span><span class="sxs-lookup"><span data-stu-id="c2059-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="c2059-215">Используйте пользовательский <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> по Cookie промежуточного слоя для проверки подлинности <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> для хранения удостоверений в запросах.</span><span class="sxs-lookup"><span data-stu-id="c2059-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="c2059-216">Сохраняйте большие объемы информации об удостоверениях на сервере, при этом клиенту отправляется только небольшой ключ идентификатора сеанса.</span><span class="sxs-lookup"><span data-stu-id="c2059-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="c2059-217">Сохранение маркера доступа</span><span class="sxs-lookup"><span data-stu-id="c2059-217">Save the access token</span></span>

<span data-ttu-id="c2059-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Определяет, должны ли маркеры доступа и обновления храниться в <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> после успешной авторизации.</span><span class="sxs-lookup"><span data-stu-id="c2059-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="c2059-219">`SaveTokens` по `false` умолчанию имеет значение, чтобы уменьшить размер окончательной проверки подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="c2059-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="c2059-220">Пример приложения задает для параметра значение `SaveTokens` `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="c2059-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="c2059-221">Когда `OnPostConfirmationAsync` выполняется, сохраните маркер доступа ([Екстерналлогининфо. аусентикатионтокенс](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) из внешнего поставщика в `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="c2059-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="c2059-222">Пример приложения сохраняет маркер доступа в `OnPostConfirmationAsync` (регистрация нового пользователя) и `OnGetCallbackAsync` (ранее зарегистрированный пользователь) в *Account/екстерналлогин. cshtml. CS* :</span><span class="sxs-lookup"><span data-stu-id="c2059-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="c2059-223">Добавление дополнительных настраиваемых токенов</span><span class="sxs-lookup"><span data-stu-id="c2059-223">How to add additional custom tokens</span></span>

<span data-ttu-id="c2059-224">Чтобы продемонстрировать, как добавить пользовательский маркер, который хранится в составе `SaveTokens` , пример приложения добавляет объект <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> с текущим <xref:System.DateTime> для [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) из `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="c2059-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="c2059-225">Создание и добавление утверждений</span><span class="sxs-lookup"><span data-stu-id="c2059-225">Creating and adding claims</span></span>

<span data-ttu-id="c2059-226">Платформа предоставляет общие действия и методы расширения для создания и добавления заявок в коллекцию.</span><span class="sxs-lookup"><span data-stu-id="c2059-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="c2059-227">Дополнительные сведения см. в разделах <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> и <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="c2059-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="c2059-228">Пользователи могут определять пользовательские действия, производя от <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> и реализуя абстрактный <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> метод.</span><span class="sxs-lookup"><span data-stu-id="c2059-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="c2059-229">Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="c2059-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="c2059-230">Удаление действий утверждений и утверждений</span><span class="sxs-lookup"><span data-stu-id="c2059-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="c2059-231">[Клаимактионколлектион. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) удаляет все действия с утверждениями для заданного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> из коллекции.</span><span class="sxs-lookup"><span data-stu-id="c2059-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="c2059-232">[Клаимактионколлектионмапекстенсионс. делетеклаим (клаимактионколлектион, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) удаляет утверждение для данного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> удостоверения.</span><span class="sxs-lookup"><span data-stu-id="c2059-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="c2059-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> в основном используется с [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) для удаления сформированных протоколом утверждений.</span><span class="sxs-lookup"><span data-stu-id="c2059-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="c2059-234">Выходные данные примера приложения</span><span class="sxs-lookup"><span data-stu-id="c2059-234">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c2059-235">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c2059-235">Additional resources</span></span>

* <span data-ttu-id="c2059-236">[соЦиалсампле приложение DotNet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): связанный пример приложения находится в технологической ветви [DotNet/AspNetCore в репозитории GitHub](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="c2059-236">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="c2059-237">`master`Ветвь содержит код в разделе активная разработка для следующего выпуска ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c2059-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="c2059-238">Чтобы просмотреть версию примера приложения для выпущенной версии ASP.NET Core, используйте раскрывающийся список **ветвь** для выбора ветви выпуска (например, `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="c2059-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
