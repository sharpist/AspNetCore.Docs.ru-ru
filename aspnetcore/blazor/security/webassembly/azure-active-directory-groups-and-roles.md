---
title: ASP.NET Core Blazor WebAssembly с группами и ролями Azure Active Directory
author: guardrex
description: Узнайте, как настроить Blazor WebAssembly для использования групп и ролей Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: bd0a7c5a905ae4888ea6ad326be5b16cbfaa10fc
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130409"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="8ca3c-103">Группы Azure AD, административные роли и определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="8ca3c-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="8ca3c-104">Авторы: [Люк Латэм](https://github.com/javiercn) (Luke Latham) и [Хавьер Кальварро Нельсон](https://github.com/guardrex) (Javier Calvarro Nelson)</span><span class="sxs-lookup"><span data-stu-id="8ca3c-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="8ca3c-105">Azure Active Directory (AAD) предоставляет несколько подходов к авторизации, которые можно сочетать с ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="8ca3c-106">Определяемые пользователем группы</span><span class="sxs-lookup"><span data-stu-id="8ca3c-106">User-defined groups</span></span>
  * <span data-ttu-id="8ca3c-107">Безопасность</span><span class="sxs-lookup"><span data-stu-id="8ca3c-107">Security</span></span>
  * <span data-ttu-id="8ca3c-108">O365</span><span class="sxs-lookup"><span data-stu-id="8ca3c-108">O365</span></span>
  * <span data-ttu-id="8ca3c-109">Распределение</span><span class="sxs-lookup"><span data-stu-id="8ca3c-109">Distribution</span></span>
* <span data-ttu-id="8ca3c-110">Роли</span><span class="sxs-lookup"><span data-stu-id="8ca3c-110">Roles</span></span>
  * <span data-ttu-id="8ca3c-111">Встроенные административные роли</span><span class="sxs-lookup"><span data-stu-id="8ca3c-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="8ca3c-112">Определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="8ca3c-112">User-defined roles</span></span>

<span data-ttu-id="8ca3c-113">Рекомендации в этой статье относятся к сценариям развертывания Blazor WebAssembly AAD, описанным в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="8ca3c-114">Автономное развертывание с помощью учетных записей Майкрософт</span><span class="sxs-lookup"><span data-stu-id="8ca3c-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="8ca3c-115">Автономное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="8ca3c-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="8ca3c-116">Размещенное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="8ca3c-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="8ca3c-117">Разрешения API Microsoft Graph</span><span class="sxs-lookup"><span data-stu-id="8ca3c-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="8ca3c-118">Для любого пользователя приложения с более чем пятью встроенными ролями администратора AAD и членством в группах безопасности требуется вызов [API Microsoft Graph](/graph/use-the-api).</span><span class="sxs-lookup"><span data-stu-id="8ca3c-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five built-in AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="8ca3c-119">Чтобы разрешить API Graph вызовы, предоставьте автономному или клиентскому приложению размещенного решения Blazor любое из следующих [разрешений API Graph](/graph/permissions-reference) на портале Azure:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="8ca3c-120">`Directory.Read.All` является разрешением с наименьшими привилегиями, используемым для примера, описанного в этой статье.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="8ca3c-121">Определяемые пользователем группы и встроенные административные роли</span><span class="sxs-lookup"><span data-stu-id="8ca3c-121">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="8ca3c-122">Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `groups`, см. в следующих статьях Azure.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="8ca3c-123">Назначение пользователей для определяемых пользователем групп AAD и встроенных административных ролей.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-123">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="8ca3c-124">Роли, использующие группы безопасности Azure AD</span><span class="sxs-lookup"><span data-stu-id="8ca3c-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="8ca3c-125">Атрибут `groupMembershipClaims`</span><span class="sxs-lookup"><span data-stu-id="8ca3c-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="8ca3c-126">В следующих примерах предполагается, что пользователю назначена встроенная роль *Администратор выставления счетов*.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-126">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="8ca3c-127">Одно утверждение `groups`, отправленное AAD, представляет группы и роли пользователя в виде идентификаторов объектов (GUID) в массиве JSON.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="8ca3c-128">Приложение должно преобразовать массив JSON групп и ролей в отдельные утверждения `group`, для которых приложение может создавать [политики](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="8ca3c-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="8ca3c-129">Когда число назначенных встроенных административных ролей Azure и определяемых пользователем групп превышает пять, AAD отправляет `hasgroups` утверждение со значением `true` вместо отправки утверждения `groups`.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-129">When the number of assigned built-in Azure Administrative Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="8ca3c-130">Любое приложение, которое может иметь более пяти ролей и групп, назначенных пользователям, должно выполнять отдельный вызов API Graph для получения таких пользовательских ролей и групп.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="8ca3c-131">Пример реализации, приведенный в этой статье, посвящен этому сценарию.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="8ca3c-132">Дополнительные сведения см. в сведениях об утверждениях `groups` и `hasgroups` см. в статье [Маркеры доступа платформы идентификации Майкрософт. Утверждение полезных данных](/azure/active-directory/develop/access-tokens#payload-claims).</span><span class="sxs-lookup"><span data-stu-id="8ca3c-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="8ca3c-133">Расширьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, чтобы включить свойства массива для групп и ролей.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="8ca3c-134">Присвойте каждому свойству пустой массив, чтобы проверка `null` не требовалась, если эти свойства используются в циклах `foreach` позже.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="8ca3c-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-135">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

<span data-ttu-id="8ca3c-136">В автономном приложении или клиентском приложении размещенного решения Blazor создайте пользовательский класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="8ca3c-137">Используйте правильную область (разрешение) для вызовов API Graph, получающих сведения о ролях и группах.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="8ca3c-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

<span data-ttu-id="8ca3c-139">В `Program.Main` (`Program.cs`) добавьте службу реализации <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> и добавьте именованный <xref:System.Net.Http.HttpClient> для выполнения запросов API Graph.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="8ca3c-140">В следующем примере создается клиент с именем `GraphAPI`.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="8ca3c-141">Создайте классы объектов каталога AAD для получения ролей и групп OData из вызова API Graph.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="8ca3c-142">Данные OData передаются в формате JSON, а вызов <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> заполняет экземпляр класса `DirectoryObjects`.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="8ca3c-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-143">`DirectoryObjects.cs`:</span></span>

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

<span data-ttu-id="8ca3c-144">Создайте настраиваемую фабрику пользователей для обработки утверждений ролей и групп.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="8ca3c-145">В следующем примере реализации также обрабатывается массив утверждений `roles`, который рассматривается в разделе [Определяемые пользователем роли](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="8ca3c-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="8ca3c-146">Если имеется утверждение `hasgroups`, то именованный <xref:System.Net.Http.HttpClient> используется для создания разрешенного запроса API Graph на получение ролей и групп пользователей.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="8ca3c-147">В этой реализации используется конечная точка `https://graph.microsoft.com/v1.0/me/memberOf` Microsoft Identity Platform версии 1.0 ([документация по API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="8ca3c-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="8ca3c-148">Рекомендации в этом разделе будут обновлены для версии 2.0 Identity при обновлении пакетов MSAL для версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="8ca3c-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-149">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> _logger;
    private readonly IHttpClientFactory _clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        _clientFactory = clientFactory;
        _logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = _clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        _logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    _logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="8ca3c-150">Нет необходимости предоставлять код для удаления исходного утверждения `groups`, если оно есть, поскольку оно автоматически удаляется платформой.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="8ca3c-151">В этом примере:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="8ca3c-152">добавляется пользовательский класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> для присоединения маркеров доступа к исходящим запросам;</span><span class="sxs-lookup"><span data-stu-id="8ca3c-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="8ca3c-153">добавляется именованный <xref:System.Net.Http.HttpClient> для выполнения запросов веб-API к безопасной внешней конечной точке веб-API;</span><span class="sxs-lookup"><span data-stu-id="8ca3c-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="8ca3c-154">используется именованный <xref:System.Net.Http.HttpClient> для выполнения разрешенных запросов.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="8ca3c-155">Более подробно этот подход рассматривается в статье <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="8ca3c-156">Зарегистрируйте фабрику в `Program.Main` (`Program.cs`) изолированного приложения или клиентского приложения размещенного решения Blazor:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="8ca3c-157">Согласие на область разрешений `Directory.Read.All` в качестве дополнительной области для приложения:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="8ca3c-158">Создайте [политики](xref:security/authorization/policies) для каждой группы или роли в `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="8ca3c-159">В следующем примере создается политика для встроенной роли *Администратор выставления счетов*:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-159">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="8ca3c-160">Полный список идентификаторов объектов ролей AAD см. в разделе [Идентификаторы группы административных ролей AAD](#aad-adminstrative-role-group-ids).</span><span class="sxs-lookup"><span data-stu-id="8ca3c-160">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="8ca3c-161">В следующих примерах приложение использует предыдущую политику для авторизации пользователя.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="8ca3c-162">С политикой работает [компонент`AuthorizeView`](xref:blazor/security/index#authorizeview-component):</span><span class="sxs-lookup"><span data-stu-id="8ca3c-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="8ca3c-163">Доступ ко всему компоненту может основываться на политике, использующей [директиву атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span><span class="sxs-lookup"><span data-stu-id="8ca3c-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="8ca3c-164">Если пользователь не вошел в систему, он перенаправляется на страницу входа AAD, а затем после входа обратно в компонент.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="8ca3c-165">Проверку политики можно также [выполнять в коде с помощью процедурной логики](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="8ca3c-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="8ca3c-166">Определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="8ca3c-166">User-defined roles</span></span>

<span data-ttu-id="8ca3c-167">Приложение, зарегистрированное в AAD, можно также настроить для использования определяемых пользователем ролей.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-167">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="8ca3c-168">Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `roles`, см. в разделе [Практическое руководство. Добавление ролей приложения в приложение, зарегистрированное в Azure Active Directory, и их получение в токене](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) документации Azure.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-168">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="8ca3c-169">В следующем примере предполагается, что приложение настроено с двумя ролями:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-169">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="8ca3c-170">Несмотря на то, что вы не можете назначать роли группам безопасности без учетной записи Azure AD Premium, вы можете назначить роли пользователям и получить утверждение `roles` для пользователей с помощью стандартной учетной записи Azure.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-170">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="8ca3c-171">В рекомендациях в данном разделе не требуется учетная запись Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-171">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="8ca3c-172">На портале Azure назначается несколько ролей путем **_повторного добавления пользователей_** для каждого дополнительного назначения роли.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-172">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="8ca3c-173">Одно утверждение `roles`, отправленное AAD, представляет определяемые пользователем роли как `appRoles` `value` в массиве JSON.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-173">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="8ca3c-174">Приложение должно преобразовать массив JSON из ролей в индивидуальные утверждения `role`.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-174">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="8ca3c-175">`CustomUserFactory`, показанные в разделе [Определяемые пользователем группы и административные роли AAD](#user-defined-groups-and-built-in-administrative-roles), настроены для работы с утверждением `roles` со значением массива JSON.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-175">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="8ca3c-176">Добавьте и зарегистрируйте `CustomUserFactory` в изолированном приложении или клиентском приложении размещенного решения Blazor, как показано в разделе [Определяемые пользователем группы и встроенные административные роли AAD](#user-defined-groups-and-built-in-administrative-roles).</span><span class="sxs-lookup"><span data-stu-id="8ca3c-176">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="8ca3c-177">Нет необходимости предоставлять код для удаления исходного утверждения `roles`, поскольку оно автоматически удаляется платформой.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-177">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="8ca3c-178">В `Program.Main` автономного приложения или клиентского приложения размещенного решения Blazor укажите утверждение с именем "`role`" в качестве утверждения роли:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-178">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="8ca3c-179">На этом этапе можно применять подходы с авторизацией компонентов.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-179">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="8ca3c-180">Любой из механизмов авторизации в компонентах может использовать роль `admin` для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-180">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="8ca3c-181">[Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="8ca3c-181">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="8ca3c-182">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="8ca3c-182">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="8ca3c-183">[Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="8ca3c-183">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="8ca3c-184">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="8ca3c-184">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="8ca3c-185">Идентификаторы группы административных ролей AAD</span><span class="sxs-lookup"><span data-stu-id="8ca3c-185">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="8ca3c-186">Идентификаторы объектов, приведенные в следующей таблице, используются для создания [политик](xref:security/authorization/policies) для утверждений `group`.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-186">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="8ca3c-187">Политики позволяют приложению авторизовать пользователей для различных действий в приложении.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-187">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="8ca3c-188">Дополнительные сведения см. в разделе [Определяемые пользователем группы и встроенные административные роли AAD](#user-defined-groups-and-built-in-administrative-roles).</span><span class="sxs-lookup"><span data-stu-id="8ca3c-188">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="8ca3c-189">Административная роль AAD</span><span class="sxs-lookup"><span data-stu-id="8ca3c-189">AAD Administrative Role</span></span> | <span data-ttu-id="8ca3c-190">Идентификатор объекта.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-190">Object ID</span></span>
--- | ---
<span data-ttu-id="8ca3c-191">администратор приложений;</span><span class="sxs-lookup"><span data-stu-id="8ca3c-191">Application administrator</span></span> | <span data-ttu-id="8ca3c-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="8ca3c-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="8ca3c-193">Разработчик приложений</span><span class="sxs-lookup"><span data-stu-id="8ca3c-193">Application developer</span></span> | <span data-ttu-id="8ca3c-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="8ca3c-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="8ca3c-195">Администратор проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="8ca3c-195">Authentication administrator</span></span> | <span data-ttu-id="8ca3c-196">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="8ca3c-196">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="8ca3c-197">Администратор Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="8ca3c-197">Azure DevOps administrator</span></span> | <span data-ttu-id="8ca3c-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="8ca3c-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="8ca3c-199">Администратор Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="8ca3c-199">Azure Information Protection administrator</span></span> | <span data-ttu-id="8ca3c-200">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="8ca3c-200">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="8ca3c-201">Администратор наборов ключей IEF B2C</span><span class="sxs-lookup"><span data-stu-id="8ca3c-201">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="8ca3c-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="8ca3c-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="8ca3c-203">Администратор политик IEF B2C</span><span class="sxs-lookup"><span data-stu-id="8ca3c-203">B2C IEF Policy administrator</span></span> | <span data-ttu-id="8ca3c-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="8ca3c-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="8ca3c-205">Администратор потоков пользователей B2C</span><span class="sxs-lookup"><span data-stu-id="8ca3c-205">B2C user flow administrator</span></span> | <span data-ttu-id="8ca3c-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="8ca3c-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="8ca3c-207">Администратор атрибутов потоков пользователей B2C</span><span class="sxs-lookup"><span data-stu-id="8ca3c-207">B2C user flow attribute administrator</span></span> | <span data-ttu-id="8ca3c-208">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="8ca3c-208">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="8ca3c-209">Администратор выставления счетов</span><span class="sxs-lookup"><span data-stu-id="8ca3c-209">Billing administrator</span></span> | <span data-ttu-id="8ca3c-210">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="8ca3c-210">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="8ca3c-211">Администратор облачных приложений</span><span class="sxs-lookup"><span data-stu-id="8ca3c-211">Cloud application administrator</span></span> | <span data-ttu-id="8ca3c-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="8ca3c-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="8ca3c-213">Администратор облачного устройства</span><span class="sxs-lookup"><span data-stu-id="8ca3c-213">Cloud device administrator</span></span> | <span data-ttu-id="8ca3c-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="8ca3c-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="8ca3c-215">администратор соответствия требованиям.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-215">Compliance administrator</span></span> | <span data-ttu-id="8ca3c-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="8ca3c-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="8ca3c-217">Администратор данных соответствия</span><span class="sxs-lookup"><span data-stu-id="8ca3c-217">Compliance data administrator</span></span> | <span data-ttu-id="8ca3c-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="8ca3c-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="8ca3c-219">Администратор условного доступа</span><span class="sxs-lookup"><span data-stu-id="8ca3c-219">Conditional Access administrator</span></span> | <span data-ttu-id="8ca3c-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="8ca3c-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="8ca3c-221">лицо, утверждающее доступ клиентов к LockBox;</span><span class="sxs-lookup"><span data-stu-id="8ca3c-221">Customer LockBox access approver</span></span> | <span data-ttu-id="8ca3c-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="8ca3c-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="8ca3c-223">Администратор аналитики классических приложений</span><span class="sxs-lookup"><span data-stu-id="8ca3c-223">Desktop Analytics administrator</span></span> | <span data-ttu-id="8ca3c-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="8ca3c-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="8ca3c-225">Читатели каталога</span><span class="sxs-lookup"><span data-stu-id="8ca3c-225">Directory readers</span></span> | <span data-ttu-id="8ca3c-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="8ca3c-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="8ca3c-227">Администратор Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="8ca3c-227">Dynamics 365 administrator</span></span> | <span data-ttu-id="8ca3c-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="8ca3c-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="8ca3c-229">администратор Exchange;</span><span class="sxs-lookup"><span data-stu-id="8ca3c-229">Exchange administrator</span></span> | <span data-ttu-id="8ca3c-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="8ca3c-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="8ca3c-231">Администратор внешнего поставщика Identity</span><span class="sxs-lookup"><span data-stu-id="8ca3c-231">External Identity Provider administrator</span></span> | <span data-ttu-id="8ca3c-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="8ca3c-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="8ca3c-233">Глобальный администратор.</span><span class="sxs-lookup"><span data-stu-id="8ca3c-233">Global administrator</span></span> | <span data-ttu-id="8ca3c-234">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="8ca3c-234">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="8ca3c-235">Глобальный читатель</span><span class="sxs-lookup"><span data-stu-id="8ca3c-235">Global reader</span></span> | <span data-ttu-id="8ca3c-236">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="8ca3c-236">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="8ca3c-237">Администратор групп</span><span class="sxs-lookup"><span data-stu-id="8ca3c-237">Groups administrator</span></span> | <span data-ttu-id="8ca3c-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="8ca3c-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="8ca3c-239">Приглашающий гостей</span><span class="sxs-lookup"><span data-stu-id="8ca3c-239">Guest inviter</span></span> | <span data-ttu-id="8ca3c-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="8ca3c-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="8ca3c-241">администратор службы технической поддержки;</span><span class="sxs-lookup"><span data-stu-id="8ca3c-241">Helpdesk administrator</span></span> | <span data-ttu-id="8ca3c-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="8ca3c-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="8ca3c-243">Администратор Intune</span><span class="sxs-lookup"><span data-stu-id="8ca3c-243">Intune administrator</span></span> | <span data-ttu-id="8ca3c-244">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="8ca3c-244">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="8ca3c-245">Администратор Kaizala</span><span class="sxs-lookup"><span data-stu-id="8ca3c-245">Kaizala administrator</span></span> | <span data-ttu-id="8ca3c-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="8ca3c-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="8ca3c-247">Администратор лицензий</span><span class="sxs-lookup"><span data-stu-id="8ca3c-247">License administrator</span></span> | <span data-ttu-id="8ca3c-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="8ca3c-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="8ca3c-249">Читатель конфиденциальности данных Центра сообщений</span><span class="sxs-lookup"><span data-stu-id="8ca3c-249">Message center privacy reader</span></span> | <span data-ttu-id="8ca3c-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="8ca3c-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="8ca3c-251">Читатель центра сообщений</span><span class="sxs-lookup"><span data-stu-id="8ca3c-251">Message center reader</span></span> | <span data-ttu-id="8ca3c-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="8ca3c-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="8ca3c-253">Администратор приложений Office</span><span class="sxs-lookup"><span data-stu-id="8ca3c-253">Office apps administrator</span></span> | <span data-ttu-id="8ca3c-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="8ca3c-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="8ca3c-255">Администратор паролей</span><span class="sxs-lookup"><span data-stu-id="8ca3c-255">Password administrator</span></span> | <span data-ttu-id="8ca3c-256">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="8ca3c-256">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="8ca3c-257">Администратор Power BI</span><span class="sxs-lookup"><span data-stu-id="8ca3c-257">Power BI administrator</span></span> | <span data-ttu-id="8ca3c-258">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="8ca3c-258">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="8ca3c-259">Администратор Power Platform</span><span class="sxs-lookup"><span data-stu-id="8ca3c-259">Power platform administrator</span></span> | <span data-ttu-id="8ca3c-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="8ca3c-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="8ca3c-261">Привилегированный администратор проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="8ca3c-261">Privileged authentication administrator</span></span> | <span data-ttu-id="8ca3c-262">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="8ca3c-262">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="8ca3c-263">администратор привилегированных ролей;</span><span class="sxs-lookup"><span data-stu-id="8ca3c-263">Privileged role administrator</span></span> | <span data-ttu-id="8ca3c-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="8ca3c-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="8ca3c-265">Читатель отчетов</span><span class="sxs-lookup"><span data-stu-id="8ca3c-265">Reports reader</span></span> | <span data-ttu-id="8ca3c-266">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="8ca3c-266">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="8ca3c-267">Администратор поиска</span><span class="sxs-lookup"><span data-stu-id="8ca3c-267">Search administrator</span></span> | <span data-ttu-id="8ca3c-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="8ca3c-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="8ca3c-269">Редактор поиска</span><span class="sxs-lookup"><span data-stu-id="8ca3c-269">Search editor</span></span> | <span data-ttu-id="8ca3c-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="8ca3c-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="8ca3c-271">администратор безопасности;</span><span class="sxs-lookup"><span data-stu-id="8ca3c-271">Security administrator</span></span> | <span data-ttu-id="8ca3c-272">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="8ca3c-272">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="8ca3c-273">Оператор безопасности</span><span class="sxs-lookup"><span data-stu-id="8ca3c-273">Security operator</span></span> | <span data-ttu-id="8ca3c-274">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="8ca3c-274">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="8ca3c-275">Читатель сведений о безопасности</span><span class="sxs-lookup"><span data-stu-id="8ca3c-275">Security reader</span></span> | <span data-ttu-id="8ca3c-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="8ca3c-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="8ca3c-277">администратор службы поддержки;</span><span class="sxs-lookup"><span data-stu-id="8ca3c-277">Service support administrator</span></span> | <span data-ttu-id="8ca3c-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="8ca3c-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="8ca3c-279">администратор SharePoint;</span><span class="sxs-lookup"><span data-stu-id="8ca3c-279">SharePoint administrator</span></span> | <span data-ttu-id="8ca3c-280">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="8ca3c-280">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="8ca3c-281">администратор Skype для бизнеса;</span><span class="sxs-lookup"><span data-stu-id="8ca3c-281">Skype for Business administrator</span></span> | <span data-ttu-id="8ca3c-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="8ca3c-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="8ca3c-283">Администратор связи Teams</span><span class="sxs-lookup"><span data-stu-id="8ca3c-283">Teams Communications Administrator</span></span> | <span data-ttu-id="8ca3c-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="8ca3c-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="8ca3c-285">Инженер службы поддержки связи Teams</span><span class="sxs-lookup"><span data-stu-id="8ca3c-285">Teams Communications Support Engineer</span></span> | <span data-ttu-id="8ca3c-286">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="8ca3c-286">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="8ca3c-287">Специалист службы поддержки связи Teams</span><span class="sxs-lookup"><span data-stu-id="8ca3c-287">Teams Communications Specialist</span></span> | <span data-ttu-id="8ca3c-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="8ca3c-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="8ca3c-289">Администратор службы Teams</span><span class="sxs-lookup"><span data-stu-id="8ca3c-289">Teams Service Administrator</span></span> | <span data-ttu-id="8ca3c-290">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="8ca3c-290">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="8ca3c-291">Администратор пользователей</span><span class="sxs-lookup"><span data-stu-id="8ca3c-291">User administrator</span></span> | <span data-ttu-id="8ca3c-292">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="8ca3c-292">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8ca3c-293">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="8ca3c-293">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
