---
title: ASP.NET Core Blazor WebAssembly с группами и ролями Azure Active Directory
author: guardrex
description: Узнайте, как настроить Blazor WebAssembly для использования групп и ролей Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 7a0c606d82dd625c179ec89e22b9313dfa5d18b4
ms.sourcegitcommit: c026bf76a0e14a5ee68983519a63574c674e9ff7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91636781"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="526fd-103">Группы, роли администратора и определяемые пользователем роли в Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="526fd-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="526fd-104">Авторы: [Люк Латэм](https://github.com/javiercn) (Luke Latham) и [Хавьер Кальварро Нельсон](https://github.com/guardrex) (Javier Calvarro Nelson)</span><span class="sxs-lookup"><span data-stu-id="526fd-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="526fd-105">Azure Active Directory (AAD) предоставляет несколько подходов к авторизации, которые можно сочетать с ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="526fd-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="526fd-106">Определяемые пользователем группы</span><span class="sxs-lookup"><span data-stu-id="526fd-106">User-defined groups</span></span>
  * <span data-ttu-id="526fd-107">Безопасность</span><span class="sxs-lookup"><span data-stu-id="526fd-107">Security</span></span>
  * <span data-ttu-id="526fd-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="526fd-108">Microsoft 365</span></span>
  * <span data-ttu-id="526fd-109">Распределение</span><span class="sxs-lookup"><span data-stu-id="526fd-109">Distribution</span></span>
* <span data-ttu-id="526fd-110">Роли</span><span class="sxs-lookup"><span data-stu-id="526fd-110">Roles</span></span>
  * <span data-ttu-id="526fd-111">Роли администратора в AAD</span><span class="sxs-lookup"><span data-stu-id="526fd-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="526fd-112">Определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="526fd-112">User-defined roles</span></span>

<span data-ttu-id="526fd-113">Рекомендации в этой статье относятся к сценариям развертывания Blazor WebAssembly AAD, описанным в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="526fd-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="526fd-114">Автономное развертывание с помощью учетных записей Майкрософт</span><span class="sxs-lookup"><span data-stu-id="526fd-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="526fd-115">Автономное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="526fd-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="526fd-116">Размещенное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="526fd-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="526fd-117">Разрешения API Microsoft Graph</span><span class="sxs-lookup"><span data-stu-id="526fd-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="526fd-118">Для любого пользователя приложения с более чем пятью ролями администратора AAD и членством в группах безопасности требуется вызов [API Microsoft Graph](/graph/use-the-api).</span><span class="sxs-lookup"><span data-stu-id="526fd-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="526fd-119">Чтобы разрешить API Graph вызовы, предоставьте автономному или клиентскому приложению размещенного решения Blazor любое из следующих [разрешений API Graph](/graph/permissions-reference) на портале Azure:</span><span class="sxs-lookup"><span data-stu-id="526fd-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="526fd-120">`Directory.Read.All` является разрешением с наименьшими привилегиями, используемым для примера, описанного в этой статье.</span><span class="sxs-lookup"><span data-stu-id="526fd-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="526fd-121">Определяемые пользователем группы и роли администратора</span><span class="sxs-lookup"><span data-stu-id="526fd-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="526fd-122">Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `groups`, см. в следующих статьях Azure.</span><span class="sxs-lookup"><span data-stu-id="526fd-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="526fd-123">Включите пользователей в определяемые пользователем группы AAD и роли администратора AAD.</span><span class="sxs-lookup"><span data-stu-id="526fd-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="526fd-124">Роли, использующие группы безопасности Azure AD</span><span class="sxs-lookup"><span data-stu-id="526fd-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="526fd-125">Атрибут `groupMembershipClaims`</span><span class="sxs-lookup"><span data-stu-id="526fd-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="526fd-126">В следующих примерах предполагается, что пользователю назначена роль *Администратор выставления счетов*.</span><span class="sxs-lookup"><span data-stu-id="526fd-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="526fd-127">Одно утверждение `groups`, отправленное AAD, представляет группы и роли пользователя в виде идентификаторов объектов (GUID) в массиве JSON.</span><span class="sxs-lookup"><span data-stu-id="526fd-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="526fd-128">Приложение должно преобразовать массив JSON групп и ролей в отдельные утверждения `group`, для которых приложение может создавать [политики](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="526fd-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="526fd-129">Когда число назначенных ролей администратора AAD и определяемых пользователем групп превысит пять, AAD отправит утверждение `hasgroups` со значением `true` вместо отправки утверждения `groups`.</span><span class="sxs-lookup"><span data-stu-id="526fd-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="526fd-130">Любое приложение, которое может иметь более пяти ролей и групп, назначенных пользователям, должно выполнять отдельный вызов API Graph для получения таких пользовательских ролей и групп.</span><span class="sxs-lookup"><span data-stu-id="526fd-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="526fd-131">Пример реализации, приведенный в этой статье, посвящен этому сценарию.</span><span class="sxs-lookup"><span data-stu-id="526fd-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="526fd-132">Дополнительные сведения см. в сведениях об утверждениях `groups` и `hasgroups` см. в статье [Маркеры доступа платформы идентификации Майкрософт. Утверждение полезных данных](/azure/active-directory/develop/access-tokens#payload-claims).</span><span class="sxs-lookup"><span data-stu-id="526fd-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="526fd-133">Расширьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, чтобы включить свойства массива для групп и ролей.</span><span class="sxs-lookup"><span data-stu-id="526fd-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="526fd-134">Присвойте каждому свойству пустой массив, чтобы проверка `null` не требовалась, если эти свойства используются в циклах `foreach` позже.</span><span class="sxs-lookup"><span data-stu-id="526fd-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="526fd-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="526fd-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="526fd-136">В автономном приложении или клиентском приложении размещенного решения Blazor создайте пользовательский класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>.</span><span class="sxs-lookup"><span data-stu-id="526fd-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="526fd-137">Используйте правильную область (разрешение) для вызовов API Graph, получающих сведения о ролях и группах.</span><span class="sxs-lookup"><span data-stu-id="526fd-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="526fd-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="526fd-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="526fd-139">В `Program.Main` (`Program.cs`) добавьте службу реализации <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> и добавьте именованный <xref:System.Net.Http.HttpClient> для выполнения запросов API Graph.</span><span class="sxs-lookup"><span data-stu-id="526fd-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="526fd-140">В следующем примере создается клиент с именем `GraphAPI`.</span><span class="sxs-lookup"><span data-stu-id="526fd-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="526fd-141">Создайте классы объектов каталога AAD для получения ролей и групп OData из вызова API Graph.</span><span class="sxs-lookup"><span data-stu-id="526fd-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="526fd-142">Данные OData передаются в формате JSON, а вызов <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> заполняет экземпляр класса `DirectoryObjects`.</span><span class="sxs-lookup"><span data-stu-id="526fd-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="526fd-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="526fd-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="526fd-144">Создайте настраиваемую фабрику пользователей для обработки утверждений ролей и групп.</span><span class="sxs-lookup"><span data-stu-id="526fd-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="526fd-145">В следующем примере реализации также обрабатывается массив утверждений `roles`, который рассматривается в разделе [Определяемые пользователем роли](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="526fd-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="526fd-146">Если имеется утверждение `hasgroups`, то именованный <xref:System.Net.Http.HttpClient> используется для создания разрешенного запроса API Graph на получение ролей и групп пользователей.</span><span class="sxs-lookup"><span data-stu-id="526fd-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="526fd-147">В этой реализации используется конечная точка `https://graph.microsoft.com/v1.0/me/memberOf` Microsoft Identity Platform версии 1.0 ([документация по API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="526fd-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="526fd-148">Рекомендации в этом разделе будут обновлены для версии 2.0 Identity при обновлении пакетов MSAL для версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="526fd-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="526fd-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="526fd-149">`CustomAccountFactory.cs`:</span></span>

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
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
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
                    var client = clientFactory.CreateClient("GraphAPI");

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
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
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

<span data-ttu-id="526fd-150">Нет необходимости предоставлять код для удаления исходного утверждения `groups`, если оно есть, поскольку оно автоматически удаляется платформой.</span><span class="sxs-lookup"><span data-stu-id="526fd-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="526fd-151">В этом примере:</span><span class="sxs-lookup"><span data-stu-id="526fd-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="526fd-152">добавляется пользовательский класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> для присоединения маркеров доступа к исходящим запросам;</span><span class="sxs-lookup"><span data-stu-id="526fd-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="526fd-153">добавляется именованный <xref:System.Net.Http.HttpClient> для выполнения запросов веб-API к безопасной внешней конечной точке веб-API;</span><span class="sxs-lookup"><span data-stu-id="526fd-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="526fd-154">используется именованный <xref:System.Net.Http.HttpClient> для выполнения разрешенных запросов.</span><span class="sxs-lookup"><span data-stu-id="526fd-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="526fd-155">Более подробно этот подход рассматривается в статье <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.</span><span class="sxs-lookup"><span data-stu-id="526fd-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="526fd-156">Зарегистрируйте фабрику в `Program.Main` (`Program.cs`) изолированного приложения или клиентского приложения размещенного решения Blazor:</span><span class="sxs-lookup"><span data-stu-id="526fd-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="526fd-157">Согласие на область разрешений `Directory.Read.All` в качестве дополнительной области для приложения:</span><span class="sxs-lookup"><span data-stu-id="526fd-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

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

<span data-ttu-id="526fd-158">Создайте [политики](xref:security/authorization/policies) для каждой группы или роли в `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="526fd-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="526fd-159">В следующем примере создается политика для роли AAD *Администратор выставления счетов*:</span><span class="sxs-lookup"><span data-stu-id="526fd-159">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="526fd-160">Полный список идентификаторов объектов ролей AAD см. в разделе [Идентификаторы объектов ролей администратора в AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="526fd-160">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="526fd-161">В следующих примерах приложение использует предыдущую политику для авторизации пользователя.</span><span class="sxs-lookup"><span data-stu-id="526fd-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="526fd-162">С политикой работает [компонент`AuthorizeView`](xref:blazor/security/index#authorizeview-component):</span><span class="sxs-lookup"><span data-stu-id="526fd-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
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

<span data-ttu-id="526fd-163">Доступ ко всему компоненту может основываться на политике, использующей [директиву атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span><span class="sxs-lookup"><span data-stu-id="526fd-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="526fd-164">Если пользователь не вошел в систему, он перенаправляется на страницу входа AAD, а затем после входа обратно в компонент.</span><span class="sxs-lookup"><span data-stu-id="526fd-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="526fd-165">Проверку политики можно также [выполнять в коде с помощью процедурной логики](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="526fd-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="526fd-166">Авторизация доступа к API сервера для определяемых пользователем групп и ролей администратора</span><span class="sxs-lookup"><span data-stu-id="526fd-166">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="526fd-167">Кроме авторизации пользователей в приложении WebAssembly на стороне клиента для доступа к страницам и ресурсам, API сервера может выполнять авторизацию пользователей для доступа к защищенным конечным точкам API.</span><span class="sxs-lookup"><span data-stu-id="526fd-167">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="526fd-168">После того как приложение *Server* проверит маркер доступа пользователя:</span><span class="sxs-lookup"><span data-stu-id="526fd-168">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="526fd-169">Приложение использует неизменяемое [утверждение идентификатора объекта (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) пользователя из JWT (`id_token`) для получения маркера доступа для API Graph.</span><span class="sxs-lookup"><span data-stu-id="526fd-169">The app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from the JWT (`id_token`) to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="526fd-170">Вызов API Graph получает определяемую пользователем Azure группу безопасности пользователя и членство для ролей администратора.</span><span class="sxs-lookup"><span data-stu-id="526fd-170">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships.</span></span>
* <span data-ttu-id="526fd-171">Членство используется для реализации утверждений `group`.</span><span class="sxs-lookup"><span data-stu-id="526fd-171">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="526fd-172">[Политики авторизации](xref:security/authorization/policies) позволяют ограничить доступ пользователей к конечным точкам API сервера.</span><span class="sxs-lookup"><span data-stu-id="526fd-172">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints.</span></span>

> [!NOTE]
> <span data-ttu-id="526fd-173">Сейчас это руководство не предусматривает авторизацию пользователей на основе [определяемых пользователем ролей AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="526fd-173">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

### <a name="packages"></a><span data-ttu-id="526fd-174">Пакеты</span><span class="sxs-lookup"><span data-stu-id="526fd-174">Packages</span></span>

<span data-ttu-id="526fd-175">Добавьте в приложение *Server* ссылки на следующие пакеты:</span><span class="sxs-lookup"><span data-stu-id="526fd-175">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="526fd-176">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="526fd-176">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="526fd-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="526fd-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="526fd-178">Конфигурация Azure</span><span class="sxs-lookup"><span data-stu-id="526fd-178">Azure configuration</span></span>

* <span data-ttu-id="526fd-179">Убедитесь, что регистрации приложения *Server* предоставлен доступ к API Graph с уровнем `Directory.Read.All`, который является уровнем доступа с минимальным набором прав для групп безопасности.</span><span class="sxs-lookup"><span data-stu-id="526fd-179">Confirm that the *Server* app registration is given API access to the Graph API permission for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="526fd-180">Убедитесь, что согласие администратора применяется к разрешению после создания назначения разрешений.</span><span class="sxs-lookup"><span data-stu-id="526fd-180">Confirm that admin consent is applied to the permission after making the permission assignment.</span></span>
* <span data-ttu-id="526fd-181">Назначьте приложению *Server* новый секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="526fd-181">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="526fd-182">Запишите секрет для конфигурации приложения в разделе [Параметры приложения](#app-settings).</span><span class="sxs-lookup"><span data-stu-id="526fd-182">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="526fd-183">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="526fd-183">App settings</span></span>

<span data-ttu-id="526fd-184">В файле параметров приложения (`appsettings.json` или `appsettings.Production.json`) создайте запись `ClientSecret` с секретом приложения *Server* с портала Azure:</span><span class="sxs-lookup"><span data-stu-id="526fd-184">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="526fd-185">Пример:</span><span class="sxs-lookup"><span data-stu-id="526fd-185">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a><span data-ttu-id="526fd-186">Политики авторизации,</span><span class="sxs-lookup"><span data-stu-id="526fd-186">Authorization policies</span></span>

<span data-ttu-id="526fd-187">Создайте [политики авторизации](xref:security/authorization/policies) для групп безопасности AAD и ролей администратора AAD в методе `Startup.ConfigureServices` приложения *Server* (`Startup.cs`) на основе идентификаторов объектов групп и [идентификаторов объектов ролей администратора AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="526fd-187">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="526fd-188">Например, политика роли администратора выставления счетов в Azure имеет следующую конфигурацию:</span><span class="sxs-lookup"><span data-stu-id="526fd-188">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="526fd-189">Для получения дополнительной информации см. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="526fd-189">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="526fd-190">Доступ к контроллеру</span><span class="sxs-lookup"><span data-stu-id="526fd-190">Controller access</span></span>

<span data-ttu-id="526fd-191">Задайте обязательные политики на контроллерах приложения *Server*.</span><span class="sxs-lookup"><span data-stu-id="526fd-191">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="526fd-192">Следующий пример ограничивает доступ к данным выставления счетов с `BillingDataController` для администраторов выставления счетов Azure с использованием имени политики `BillingAdmin`, которое задано в разделе [Политики авторизации](#authorization-policies):</span><span class="sxs-lookup"><span data-stu-id="526fd-192">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a><span data-ttu-id="526fd-193">Конфигурация службы</span><span class="sxs-lookup"><span data-stu-id="526fd-193">Service configuration</span></span>

<span data-ttu-id="526fd-194">Добавьте логику в метод `Startup.ConfigureServices` приложения *Server*, чтобы API Graph вызывал и реализовывал утверждения `group` для групп безопасности и ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="526fd-194">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="526fd-195">В примере кода в этом разделе используется библиотека проверки подлинности Active Directory (ADAL), которая основана на платформе Microsoft Identity Platform версии 1.0.</span><span class="sxs-lookup"><span data-stu-id="526fd-195">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span> <span data-ttu-id="526fd-196">Этот раздел будет обновлен для Identity версии 2.0 для .NET 5.</span><span class="sxs-lookup"><span data-stu-id="526fd-196">This topic will be updated for Identity v2.0 for .NET 5.</span></span> <span data-ttu-id="526fd-197">Отслеживайте ход этого процесса с помощью платформы [[RC1] Microsoft Identity Platform 2.0 для Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span><span class="sxs-lookup"><span data-stu-id="526fd-197">Track progress on this work by monitoring [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span></span>

<span data-ttu-id="526fd-198">Код в классе `Startup` приложения *Server* требует дополнительные пространства имен.</span><span class="sxs-lookup"><span data-stu-id="526fd-198">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="526fd-199">Следующий набор операторов `using` включает необходимые пространства имен для кода, который приведен в этом разделе:</span><span class="sxs-lookup"><span data-stu-id="526fd-199">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="526fd-200">При настройке <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span><span class="sxs-lookup"><span data-stu-id="526fd-200">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="526fd-201">При необходимости включите обработку для <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="526fd-201">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="526fd-202">Например, приложение может регистрировать неудачные попытки аутентификации.</span><span class="sxs-lookup"><span data-stu-id="526fd-202">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="526fd-203">В <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> создайте вызов API Graph для получения групп и ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="526fd-203">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="526fd-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> предоставляет персональные данные в записях сообщений в журнале.</span><span class="sxs-lookup"><span data-stu-id="526fd-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="526fd-205">Активируйте персональные данные только для отладки в тестовых учетных записях пользователей.</span><span class="sxs-lookup"><span data-stu-id="526fd-205">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="526fd-206">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="526fd-206">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="526fd-207">В предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="526fd-207">In the preceding example:</span></span>

* <span data-ttu-id="526fd-208">Автоматическое получение маркера (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) выполняется первым, так как маркер доступа может быть уже сохранен в кэше маркеров ADAL.</span><span class="sxs-lookup"><span data-stu-id="526fd-208">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="526fd-209">Получить маркер из кэша быстрее, чем запросить новый маркер.</span><span class="sxs-lookup"><span data-stu-id="526fd-209">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="526fd-210">Если маркер доступа не получен из кэша (возникает исключение <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> или <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType>), операция утверждения пользователя (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) выполняется с учетными данными клиента (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) для получения маркера от имени пользователя (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span><span class="sxs-lookup"><span data-stu-id="526fd-210">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="526fd-211">Затем `Microsoft.Graph.GraphServiceClient` может выполнить вызов API Graph с помощью маркера.</span><span class="sxs-lookup"><span data-stu-id="526fd-211">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="526fd-212">Маркер помещается в кэш маркеров ADAL.</span><span class="sxs-lookup"><span data-stu-id="526fd-212">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="526fd-213">При будущих вызовах API Graph к этому же пользователю маркер будет автоматически получен из кэша с помощью <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="526fd-213">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

<span data-ttu-id="526fd-214">Код в <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> не получает сведения о временном членстве.</span><span class="sxs-lookup"><span data-stu-id="526fd-214">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="526fd-215">Чтобы изменить код для получения сведений о непосредственном и временном членстве, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="526fd-215">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="526fd-216">Для строки кода:</span><span class="sxs-lookup"><span data-stu-id="526fd-216">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="526fd-217">Замените предыдущую строку следующим:</span><span class="sxs-lookup"><span data-stu-id="526fd-217">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="526fd-218">Для строки кода:</span><span class="sxs-lookup"><span data-stu-id="526fd-218">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="526fd-219">Замените предыдущую строку следующим:</span><span class="sxs-lookup"><span data-stu-id="526fd-219">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="526fd-220">Код в <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> не различает группы безопасности AAD и роли администратора AAD при создании утверждений.</span><span class="sxs-lookup"><span data-stu-id="526fd-220">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="526fd-221">Чтобы приложение различало группы и роли, проверьте `entry.ODataType` при итерации по группам и ролям.</span><span class="sxs-lookup"><span data-stu-id="526fd-221">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="526fd-222">Чтобы создать отдельные утверждения для групп безопасности и ролей, используйте код, аналогичный приведенному ниже:</span><span class="sxs-lookup"><span data-stu-id="526fd-222">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="526fd-223">Определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="526fd-223">User-defined roles</span></span>

<span data-ttu-id="526fd-224">Приложение, зарегистрированное в AAD, можно также настроить для использования определяемых пользователем ролей.</span><span class="sxs-lookup"><span data-stu-id="526fd-224">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="526fd-225">Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `roles`, см. в разделе [Практическое руководство. Добавление ролей приложения в приложение, зарегистрированное в Azure Active Directory, и их получение в токене](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) документации Azure.</span><span class="sxs-lookup"><span data-stu-id="526fd-225">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="526fd-226">В следующем примере предполагается, что приложение настроено с двумя ролями:</span><span class="sxs-lookup"><span data-stu-id="526fd-226">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="526fd-227">Несмотря на то, что вы не можете назначать роли группам безопасности без учетной записи Azure AD Premium, вы можете назначить роли пользователям и получить утверждение `roles` для пользователей с помощью стандартной учетной записи Azure.</span><span class="sxs-lookup"><span data-stu-id="526fd-227">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="526fd-228">В рекомендациях в данном разделе не требуется учетная запись Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="526fd-228">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="526fd-229">На портале Azure назначается несколько ролей путем **_повторного добавления пользователей_** для каждого дополнительного назначения роли.</span><span class="sxs-lookup"><span data-stu-id="526fd-229">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="526fd-230">Одно утверждение `roles`, отправленное AAD, представляет определяемые пользователем роли как `appRoles` `value` в массиве JSON.</span><span class="sxs-lookup"><span data-stu-id="526fd-230">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="526fd-231">Приложение должно преобразовать массив JSON из ролей в индивидуальные утверждения `role`.</span><span class="sxs-lookup"><span data-stu-id="526fd-231">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="526fd-232">Класс `CustomUserFactory`, приведенный в разделе [Определяемые пользователем группы и роли администратора AAD](#user-defined-groups-and-administrator-roles), настроен на выполнение действий с утверждением `roles` с использованием значения массива JSON.</span><span class="sxs-lookup"><span data-stu-id="526fd-232">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="526fd-233">Добавьте и зарегистрируйте `CustomUserFactory` в изолированном приложении или клиентском приложении размещенного решения Blazor, как показано в разделе [Определяемые пользователем группы и роли администратора AAD](#user-defined-groups-and-administrator-roles).</span><span class="sxs-lookup"><span data-stu-id="526fd-233">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="526fd-234">Нет необходимости предоставлять код для удаления исходного утверждения `roles`, поскольку оно автоматически удаляется платформой.</span><span class="sxs-lookup"><span data-stu-id="526fd-234">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="526fd-235">В `Program.Main` автономного приложения или клиентского приложения размещенного решения Blazor укажите утверждение с именем "`role`" в качестве утверждения роли:</span><span class="sxs-lookup"><span data-stu-id="526fd-235">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="526fd-236">На этом этапе можно применять подходы с авторизацией компонентов.</span><span class="sxs-lookup"><span data-stu-id="526fd-236">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="526fd-237">Любой из механизмов авторизации в компонентах может использовать роль `admin` для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="526fd-237">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="526fd-238">[Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="526fd-238">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="526fd-239">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="526fd-239">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="526fd-240">[Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="526fd-240">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="526fd-241">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="526fd-241">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="526fd-242">Идентификаторы объектов ролей администратора AAD</span><span class="sxs-lookup"><span data-stu-id="526fd-242">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="526fd-243">Идентификаторы объектов, приведенные в следующей таблице, используются для создания [политик](xref:security/authorization/policies) для утверждений `group`.</span><span class="sxs-lookup"><span data-stu-id="526fd-243">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="526fd-244">Политики позволяют приложению авторизовать пользователей для различных действий в приложении.</span><span class="sxs-lookup"><span data-stu-id="526fd-244">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="526fd-245">Дополнительные сведения см. в разделе [Определяемые пользователем группы и роли администратора AAD](#user-defined-groups-and-administrator-roles).</span><span class="sxs-lookup"><span data-stu-id="526fd-245">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="526fd-246">Роли администратора в AAD</span><span class="sxs-lookup"><span data-stu-id="526fd-246">AAD Administrator Role</span></span> | <span data-ttu-id="526fd-247">Идентификатор объекта.</span><span class="sxs-lookup"><span data-stu-id="526fd-247">Object ID</span></span>
--- | ---
<span data-ttu-id="526fd-248">администратор приложений;</span><span class="sxs-lookup"><span data-stu-id="526fd-248">Application administrator</span></span> | <span data-ttu-id="526fd-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="526fd-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="526fd-250">Разработчик приложений</span><span class="sxs-lookup"><span data-stu-id="526fd-250">Application developer</span></span> | <span data-ttu-id="526fd-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="526fd-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="526fd-252">Администратор проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="526fd-252">Authentication administrator</span></span> | <span data-ttu-id="526fd-253">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="526fd-253">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="526fd-254">Администратор Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="526fd-254">Azure DevOps administrator</span></span> | <span data-ttu-id="526fd-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="526fd-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="526fd-256">Администратор Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="526fd-256">Azure Information Protection administrator</span></span> | <span data-ttu-id="526fd-257">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="526fd-257">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="526fd-258">Администратор наборов ключей IEF B2C</span><span class="sxs-lookup"><span data-stu-id="526fd-258">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="526fd-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="526fd-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="526fd-260">Администратор политик IEF B2C</span><span class="sxs-lookup"><span data-stu-id="526fd-260">B2C IEF Policy administrator</span></span> | <span data-ttu-id="526fd-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="526fd-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="526fd-262">Администратор потоков пользователей B2C</span><span class="sxs-lookup"><span data-stu-id="526fd-262">B2C user flow administrator</span></span> | <span data-ttu-id="526fd-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="526fd-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="526fd-264">Администратор атрибутов потоков пользователей B2C</span><span class="sxs-lookup"><span data-stu-id="526fd-264">B2C user flow attribute administrator</span></span> | <span data-ttu-id="526fd-265">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="526fd-265">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="526fd-266">Администратор выставления счетов</span><span class="sxs-lookup"><span data-stu-id="526fd-266">Billing administrator</span></span> | <span data-ttu-id="526fd-267">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="526fd-267">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="526fd-268">Администратор облачных приложений</span><span class="sxs-lookup"><span data-stu-id="526fd-268">Cloud application administrator</span></span> | <span data-ttu-id="526fd-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="526fd-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="526fd-270">Администратор облачного устройства</span><span class="sxs-lookup"><span data-stu-id="526fd-270">Cloud device administrator</span></span> | <span data-ttu-id="526fd-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="526fd-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="526fd-272">администратор соответствия требованиям.</span><span class="sxs-lookup"><span data-stu-id="526fd-272">Compliance administrator</span></span> | <span data-ttu-id="526fd-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="526fd-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="526fd-274">Администратор данных соответствия</span><span class="sxs-lookup"><span data-stu-id="526fd-274">Compliance data administrator</span></span> | <span data-ttu-id="526fd-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="526fd-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="526fd-276">Администратор условного доступа</span><span class="sxs-lookup"><span data-stu-id="526fd-276">Conditional Access administrator</span></span> | <span data-ttu-id="526fd-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="526fd-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="526fd-278">лицо, утверждающее доступ клиентов к LockBox;</span><span class="sxs-lookup"><span data-stu-id="526fd-278">Customer LockBox access approver</span></span> | <span data-ttu-id="526fd-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="526fd-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="526fd-280">Администратор аналитики классических приложений</span><span class="sxs-lookup"><span data-stu-id="526fd-280">Desktop Analytics administrator</span></span> | <span data-ttu-id="526fd-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="526fd-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="526fd-282">Читатели каталога</span><span class="sxs-lookup"><span data-stu-id="526fd-282">Directory readers</span></span> | <span data-ttu-id="526fd-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="526fd-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="526fd-284">Администратор Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="526fd-284">Dynamics 365 administrator</span></span> | <span data-ttu-id="526fd-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="526fd-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="526fd-286">администратор Exchange;</span><span class="sxs-lookup"><span data-stu-id="526fd-286">Exchange administrator</span></span> | <span data-ttu-id="526fd-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="526fd-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="526fd-288">Администратор внешнего поставщика Identity</span><span class="sxs-lookup"><span data-stu-id="526fd-288">External Identity Provider administrator</span></span> | <span data-ttu-id="526fd-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="526fd-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="526fd-290">Глобальный администратор.</span><span class="sxs-lookup"><span data-stu-id="526fd-290">Global administrator</span></span> | <span data-ttu-id="526fd-291">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="526fd-291">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="526fd-292">Глобальный читатель</span><span class="sxs-lookup"><span data-stu-id="526fd-292">Global reader</span></span> | <span data-ttu-id="526fd-293">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="526fd-293">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="526fd-294">Администратор групп</span><span class="sxs-lookup"><span data-stu-id="526fd-294">Groups administrator</span></span> | <span data-ttu-id="526fd-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="526fd-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="526fd-296">Приглашающий гостей</span><span class="sxs-lookup"><span data-stu-id="526fd-296">Guest inviter</span></span> | <span data-ttu-id="526fd-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="526fd-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="526fd-298">администратор службы технической поддержки;</span><span class="sxs-lookup"><span data-stu-id="526fd-298">Helpdesk administrator</span></span> | <span data-ttu-id="526fd-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="526fd-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="526fd-300">Администратор Intune</span><span class="sxs-lookup"><span data-stu-id="526fd-300">Intune administrator</span></span> | <span data-ttu-id="526fd-301">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="526fd-301">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="526fd-302">Администратор Kaizala</span><span class="sxs-lookup"><span data-stu-id="526fd-302">Kaizala administrator</span></span> | <span data-ttu-id="526fd-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="526fd-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="526fd-304">Администратор лицензий</span><span class="sxs-lookup"><span data-stu-id="526fd-304">License administrator</span></span> | <span data-ttu-id="526fd-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="526fd-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="526fd-306">Читатель конфиденциальности данных Центра сообщений</span><span class="sxs-lookup"><span data-stu-id="526fd-306">Message center privacy reader</span></span> | <span data-ttu-id="526fd-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="526fd-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="526fd-308">Читатель центра сообщений</span><span class="sxs-lookup"><span data-stu-id="526fd-308">Message center reader</span></span> | <span data-ttu-id="526fd-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="526fd-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="526fd-310">Администратор приложений Office</span><span class="sxs-lookup"><span data-stu-id="526fd-310">Office apps administrator</span></span> | <span data-ttu-id="526fd-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="526fd-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="526fd-312">Администратор паролей</span><span class="sxs-lookup"><span data-stu-id="526fd-312">Password administrator</span></span> | <span data-ttu-id="526fd-313">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="526fd-313">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="526fd-314">Администратор Power BI</span><span class="sxs-lookup"><span data-stu-id="526fd-314">Power BI administrator</span></span> | <span data-ttu-id="526fd-315">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="526fd-315">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="526fd-316">Администратор Power Platform</span><span class="sxs-lookup"><span data-stu-id="526fd-316">Power platform administrator</span></span> | <span data-ttu-id="526fd-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="526fd-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="526fd-318">Привилегированный администратор проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="526fd-318">Privileged authentication administrator</span></span> | <span data-ttu-id="526fd-319">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="526fd-319">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="526fd-320">администратор привилегированных ролей;</span><span class="sxs-lookup"><span data-stu-id="526fd-320">Privileged role administrator</span></span> | <span data-ttu-id="526fd-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="526fd-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="526fd-322">Читатель отчетов</span><span class="sxs-lookup"><span data-stu-id="526fd-322">Reports reader</span></span> | <span data-ttu-id="526fd-323">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="526fd-323">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="526fd-324">Администратор поиска</span><span class="sxs-lookup"><span data-stu-id="526fd-324">Search administrator</span></span> | <span data-ttu-id="526fd-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="526fd-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="526fd-326">Редактор поиска</span><span class="sxs-lookup"><span data-stu-id="526fd-326">Search editor</span></span> | <span data-ttu-id="526fd-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="526fd-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="526fd-328">администратор безопасности;</span><span class="sxs-lookup"><span data-stu-id="526fd-328">Security administrator</span></span> | <span data-ttu-id="526fd-329">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="526fd-329">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="526fd-330">Оператор безопасности</span><span class="sxs-lookup"><span data-stu-id="526fd-330">Security operator</span></span> | <span data-ttu-id="526fd-331">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="526fd-331">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="526fd-332">Читатель сведений о безопасности</span><span class="sxs-lookup"><span data-stu-id="526fd-332">Security reader</span></span> | <span data-ttu-id="526fd-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="526fd-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="526fd-334">администратор службы поддержки;</span><span class="sxs-lookup"><span data-stu-id="526fd-334">Service support administrator</span></span> | <span data-ttu-id="526fd-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="526fd-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="526fd-336">администратор SharePoint;</span><span class="sxs-lookup"><span data-stu-id="526fd-336">SharePoint administrator</span></span> | <span data-ttu-id="526fd-337">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="526fd-337">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="526fd-338">администратор Skype для бизнеса;</span><span class="sxs-lookup"><span data-stu-id="526fd-338">Skype for Business administrator</span></span> | <span data-ttu-id="526fd-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="526fd-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="526fd-340">Администратор связи Teams</span><span class="sxs-lookup"><span data-stu-id="526fd-340">Teams Communications Administrator</span></span> | <span data-ttu-id="526fd-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="526fd-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="526fd-342">Инженер службы поддержки связи Teams</span><span class="sxs-lookup"><span data-stu-id="526fd-342">Teams Communications Support Engineer</span></span> | <span data-ttu-id="526fd-343">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="526fd-343">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="526fd-344">Специалист службы поддержки связи Teams</span><span class="sxs-lookup"><span data-stu-id="526fd-344">Teams Communications Specialist</span></span> | <span data-ttu-id="526fd-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="526fd-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="526fd-346">Администратор службы Teams</span><span class="sxs-lookup"><span data-stu-id="526fd-346">Teams Service Administrator</span></span> | <span data-ttu-id="526fd-347">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="526fd-347">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="526fd-348">Администратор пользователей</span><span class="sxs-lookup"><span data-stu-id="526fd-348">User administrator</span></span> | <span data-ttu-id="526fd-349">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="526fd-349">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="526fd-350">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="526fd-350">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
