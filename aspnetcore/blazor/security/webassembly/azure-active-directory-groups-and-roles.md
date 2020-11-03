---
title: 'ASP.NET Core :::no-loc(Blazor WebAssembly)::: с группами и ролями Azure Active Directory'
author: guardrex
description: 'Узнайте, как настроить :::no-loc(Blazor WebAssembly)::: для использования групп и ролей Azure Active Directory.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 7a56f03f2c3acd08b009673ef7533186bf703604
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690472"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="89a7a-103">Группы, роли администратора и определяемые пользователем роли в Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="89a7a-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="89a7a-104">Авторы: [Люк Латэм](https://github.com/javiercn) (Luke Latham) и [Хавьер Кальварро Нельсон](https://github.com/guardrex) (Javier Calvarro Nelson)</span><span class="sxs-lookup"><span data-stu-id="89a7a-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="89a7a-105">Azure Active Directory (AAD) предоставляет несколько подходов к авторизации, которые можно сочетать с :::no-loc(ASP.NET Core Identity):::.</span><span class="sxs-lookup"><span data-stu-id="89a7a-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with :::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="89a7a-106">Определяемые пользователем группы</span><span class="sxs-lookup"><span data-stu-id="89a7a-106">User-defined groups</span></span>
  * <span data-ttu-id="89a7a-107">Безопасность</span><span class="sxs-lookup"><span data-stu-id="89a7a-107">Security</span></span>
  * <span data-ttu-id="89a7a-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="89a7a-108">Microsoft 365</span></span>
  * <span data-ttu-id="89a7a-109">Распределение</span><span class="sxs-lookup"><span data-stu-id="89a7a-109">Distribution</span></span>
* <span data-ttu-id="89a7a-110">Роли</span><span class="sxs-lookup"><span data-stu-id="89a7a-110">Roles</span></span>
  * <span data-ttu-id="89a7a-111">Роли администратора в AAD</span><span class="sxs-lookup"><span data-stu-id="89a7a-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="89a7a-112">Определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="89a7a-112">User-defined roles</span></span>

<span data-ttu-id="89a7a-113">Рекомендации в этой статье относятся к сценариям развертывания :::no-loc(Blazor WebAssembly)::: AAD, описанным в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="89a7a-113">The guidance in this article applies to the :::no-loc(Blazor WebAssembly)::: AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="89a7a-114">Автономное развертывание с помощью учетных записей Майкрософт</span><span class="sxs-lookup"><span data-stu-id="89a7a-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="89a7a-115">Автономное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="89a7a-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="89a7a-116">Размещенное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="89a7a-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="89a7a-117">Области действия</span><span class="sxs-lookup"><span data-stu-id="89a7a-117">Scopes</span></span>

<span data-ttu-id="89a7a-118">Для любого пользователя приложения с более чем пятью ролями администратора AAD и членством в группах безопасности требуется вызов [API Microsoft Graph](/graph/use-the-api).</span><span class="sxs-lookup"><span data-stu-id="89a7a-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="89a7a-119">Чтобы разрешить API Graph вызовы, предоставьте автономному приложению или приложению *`Client`* размещенного решения :::no-loc(Blazor)::: любое из следующих [разрешений (областей) API Graph](/graph/permissions-reference) на портале Azure:</span><span class="sxs-lookup"><span data-stu-id="89a7a-119">To permit Graph API calls, give the standalone or *`Client`* app of a hosted :::no-loc(Blazor)::: solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="89a7a-120">`Directory.Read.All` является областью с наименьшими привилегиями и используется в примере, описываемом в этой статье.</span><span class="sxs-lookup"><span data-stu-id="89a7a-120">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="89a7a-121">Определяемые пользователем группы и роли администратора</span><span class="sxs-lookup"><span data-stu-id="89a7a-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="89a7a-122">Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `groups`, см. в следующих статьях Azure.</span><span class="sxs-lookup"><span data-stu-id="89a7a-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="89a7a-123">Включите пользователей в определяемые пользователем группы AAD и роли администратора AAD.</span><span class="sxs-lookup"><span data-stu-id="89a7a-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="89a7a-124">Роли, использующие группы безопасности Azure AD</span><span class="sxs-lookup"><span data-stu-id="89a7a-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="89a7a-125">Атрибут `groupMembershipClaims`</span><span class="sxs-lookup"><span data-stu-id="89a7a-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="89a7a-126">В следующих примерах предполагается, что пользователю назначена роль *Администратор выставления счетов*.</span><span class="sxs-lookup"><span data-stu-id="89a7a-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="89a7a-127">Одно утверждение `groups`, отправленное AAD, представляет группы и роли пользователя в виде идентификаторов объектов (GUID) в массиве JSON.</span><span class="sxs-lookup"><span data-stu-id="89a7a-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="89a7a-128">Приложение должно преобразовать массив JSON групп и ролей в отдельные утверждения `group`, для которых приложение может создавать [политики](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="89a7a-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="89a7a-129">Когда число назначенных ролей администратора AAD и определяемых пользователем групп превысит пять, AAD отправит утверждение `hasgroups` со значением `true` вместо отправки утверждения `groups`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="89a7a-130">Любое приложение, которое может иметь более пяти ролей и групп, назначенных пользователям, должно выполнять отдельный вызов API Graph для получения таких пользовательских ролей и групп.</span><span class="sxs-lookup"><span data-stu-id="89a7a-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="89a7a-131">Пример реализации, приведенный в этой статье, посвящен этому сценарию.</span><span class="sxs-lookup"><span data-stu-id="89a7a-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="89a7a-132">Дополнительные сведения см. в сведениях об утверждениях `groups` и `hasgroups` см. в статье [Маркеры доступа платформы идентификации Майкрософт. Утверждение полезных данных](/azure/active-directory/develop/access-tokens#payload-claims).</span><span class="sxs-lookup"><span data-stu-id="89a7a-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="89a7a-133">Расширьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, чтобы включить свойства массива для групп и ролей.</span><span class="sxs-lookup"><span data-stu-id="89a7a-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="89a7a-134">Присвойте каждому свойству пустой массив, чтобы проверка `null` не требовалась, если эти свойства используются в циклах `foreach` позже.</span><span class="sxs-lookup"><span data-stu-id="89a7a-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="89a7a-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="89a7a-135">`CustomUserAccount.cs`:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="89a7a-136">Чтобы создать утверждения для групп и ролей AAD, используйте **любой** из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="89a7a-136">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="89a7a-137">Использование пакета SDK для Graph</span><span class="sxs-lookup"><span data-stu-id="89a7a-137">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="89a7a-138">Использование именованного элемента `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="89a7a-138">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="89a7a-139">Использование пакета SDK для Graph</span><span class="sxs-lookup"><span data-stu-id="89a7a-139">Use the Graph SDK</span></span>

<span data-ttu-id="89a7a-140">Добавьте ссылку на пакет в автономное приложение или приложение *`Client`* размещенного решения :::no-loc(Blazor)::: для [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span><span class="sxs-lookup"><span data-stu-id="89a7a-140">Add a package reference to the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="89a7a-141">Добавьте служебные классы и конфигурацию Graph SDK, описанные в разделе *Пакет SDK для Graph* статьи <xref:blazor/security/webassembly/graph-api#graph-sdk>.</span><span class="sxs-lookup"><span data-stu-id="89a7a-141">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="89a7a-142">Добавьте следующую настраиваемую фабрику учетных записей пользователей в изолированное приложение или приложение *`Client`* размещенного решения :::no-loc(Blazor)::: (`CustomAccountFactory.cs`).</span><span class="sxs-lookup"><span data-stu-id="89a7a-142">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted :::no-loc(Blazor)::: solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="89a7a-143">Настраиваемая фабрика пользователей используется для обработки утверждений ролей и групп.</span><span class="sxs-lookup"><span data-stu-id="89a7a-143">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="89a7a-144">Массив утверждений `roles` рассматривается в разделе [Определяемые пользователем роли](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="89a7a-144">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="89a7a-145">Если имеется утверждение `hasgroups`, то пакет SDK для Graph используется для создания разрешенного запроса API Graph на получение ролей и групп пользователей:</span><span class="sxs-lookup"><span data-stu-id="89a7a-145">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            foreach (var role in account.Roles)
            {
                user:::no-loc(Identity):::.AddClaim(new Claim("role", role));
            }

            if (user:::no-loc(Identity):::.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = user:::no-loc(Identity):::.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = user:::no-loc(Identity):::.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                user:::no-loc(Identity):::.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    user:::no-loc(Identity):::.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="89a7a-146">Приведенный выше код не включает в себя транзитивные членства.</span><span class="sxs-lookup"><span data-stu-id="89a7a-146">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="89a7a-147">Если приложению требуются прямые и транзитивные утверждения на членство в группах:</span><span class="sxs-lookup"><span data-stu-id="89a7a-147">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="89a7a-148">Измените тип `IUserMemberOfCollectionWithReferencesPage` с `groupsAndAzureRoles` на `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-148">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="89a7a-149">При запросе групп и ролей пользователя замените свойство `MemberOf` на `TransitiveMemberOf`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-149">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="89a7a-150">В `Program.Main` (`Program.cs`) настройте проверку подлинности MSAL для использования настраиваемой фабрики учетных записей пользователей: Если приложение использует настраиваемый класс учетной записи пользователя, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, замените <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> на этот класс в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="89a7a-150">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

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

### <a name="use-a-named-httpclient"></a><span data-ttu-id="89a7a-151">Использование именованного элемента `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="89a7a-151">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="89a7a-152">В автономном приложении или приложении *`Client`* размещенного решения :::no-loc(Blazor)::: создайте пользовательский класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>.</span><span class="sxs-lookup"><span data-stu-id="89a7a-152">In the standalone app or the *`Client`* app of a hosted :::no-loc(Blazor)::: solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="89a7a-153">Используйте правильную область для вызовов API Graph, получающих сведения о ролях и группах.</span><span class="sxs-lookup"><span data-stu-id="89a7a-153">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="89a7a-154">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="89a7a-154">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="89a7a-155">В `Program.Main` (`Program.cs`) добавьте службу реализации <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> и добавьте именованный <xref:System.Net.Http.HttpClient> для выполнения запросов API Graph.</span><span class="sxs-lookup"><span data-stu-id="89a7a-155">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="89a7a-156">В следующем примере создается клиент с именем `GraphAPI`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-156">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="89a7a-157">Создайте классы объектов каталога AAD для получения ролей и групп OData из вызова API Graph.</span><span class="sxs-lookup"><span data-stu-id="89a7a-157">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="89a7a-158">Данные OData передаются в формате JSON, а вызов <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> заполняет экземпляр класса `DirectoryObjects`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-158">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="89a7a-159">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="89a7a-159">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="89a7a-160">Создайте настраиваемую фабрику пользователей для обработки утверждений ролей и групп.</span><span class="sxs-lookup"><span data-stu-id="89a7a-160">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="89a7a-161">В следующем примере реализации также обрабатывается массив утверждений `roles`, который рассматривается в разделе [Определяемые пользователем роли](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="89a7a-161">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="89a7a-162">Если имеется утверждение `hasgroups`, то именованный <xref:System.Net.Http.HttpClient> используется для создания разрешенного запроса API Graph на получение ролей и групп пользователей.</span><span class="sxs-lookup"><span data-stu-id="89a7a-162">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="89a7a-163">В этой реализации используется конечная точка `https://graph.microsoft.com/v1.0/me/memberOf` Microsoft :::no-loc(Identity)::: Platform версии 1.0 ([документация по API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="89a7a-163">This implementation uses the Microsoft :::no-loc(Identity)::: Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="89a7a-164">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="89a7a-164">`CustomAccountFactory.cs`:</span></span>

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

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            foreach (var role in account.Roles)
            {
                user:::no-loc(Identity):::.AddClaim(new Claim("role", role));
            }

            if (user:::no-loc(Identity):::.HasClaim(c => c.Type == "hasgroups"))
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
                            user:::no-loc(Identity):::.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = user:::no-loc(Identity):::.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        user:::no-loc(Identity):::.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {Message}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    user:::no-loc(Identity):::.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="89a7a-165">Нет необходимости предоставлять код для удаления исходного утверждения `groups`, если оно есть, поскольку оно автоматически удаляется платформой.</span><span class="sxs-lookup"><span data-stu-id="89a7a-165">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="89a7a-166">В этом примере:</span><span class="sxs-lookup"><span data-stu-id="89a7a-166">The approach in this example:</span></span>
>
> * <span data-ttu-id="89a7a-167">добавляется пользовательский класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> для присоединения маркеров доступа к исходящим запросам;</span><span class="sxs-lookup"><span data-stu-id="89a7a-167">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="89a7a-168">добавляется именованный <xref:System.Net.Http.HttpClient> для выполнения запросов веб-API к безопасной внешней конечной точке веб-API;</span><span class="sxs-lookup"><span data-stu-id="89a7a-168">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="89a7a-169">используется именованный <xref:System.Net.Http.HttpClient> для выполнения разрешенных запросов.</span><span class="sxs-lookup"><span data-stu-id="89a7a-169">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="89a7a-170">Более подробно этот подход рассматривается в статье <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.</span><span class="sxs-lookup"><span data-stu-id="89a7a-170">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="89a7a-171">Зарегистрируйте фабрику в `Program.Main` (`Program.cs`) изолированного приложения или приложения *`Client`* размещенного решения :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="89a7a-171">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution.</span></span> <span data-ttu-id="89a7a-172">Согласие на область `Directory.Read.All` в качестве дополнительной области для приложения:</span><span class="sxs-lookup"><span data-stu-id="89a7a-172">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

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

## <a name="authorization-configuration"></a><span data-ttu-id="89a7a-173">Настройка авторизации</span><span class="sxs-lookup"><span data-stu-id="89a7a-173">Authorization configuration</span></span>

<span data-ttu-id="89a7a-174">Создайте [политики](xref:security/authorization/policies) для каждой группы или роли в `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-174">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="89a7a-175">В следующем примере создается политика для роли AAD *Администратор выставления счетов* :</span><span class="sxs-lookup"><span data-stu-id="89a7a-175">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="89a7a-176">Полный список идентификаторов объектов ролей AAD см. в разделе [Идентификаторы объектов ролей администратора в AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="89a7a-176">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="89a7a-177">В следующих примерах приложение использует предыдущую политику для авторизации пользователя.</span><span class="sxs-lookup"><span data-stu-id="89a7a-177">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="89a7a-178">С политикой работает [компонент`AuthorizeView`](xref:blazor/security/index#authorizeview-component):</span><span class="sxs-lookup"><span data-stu-id="89a7a-178">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="89a7a-179">Доступ ко всему компоненту может основываться на политике, использующей [директиву атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span><span class="sxs-lookup"><span data-stu-id="89a7a-179">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="89a7a-180">Если пользователь не вошел в систему, он перенаправляется на страницу входа AAD, а затем после входа обратно в компонент.</span><span class="sxs-lookup"><span data-stu-id="89a7a-180">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="89a7a-181">Проверку политики можно также [выполнять в коде с помощью процедурной логики](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="89a7a-181">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="89a7a-182">Авторизация доступа к API сервера для определяемых пользователем групп и ролей администратора</span><span class="sxs-lookup"><span data-stu-id="89a7a-182">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="89a7a-183">Кроме авторизации пользователей в приложении WebAssembly на стороне клиента для доступа к страницам и ресурсам, API сервера может выполнять авторизацию пользователей для доступа к защищенным конечным точкам API.</span><span class="sxs-lookup"><span data-stu-id="89a7a-183">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="89a7a-184">После того как приложение *Server* проверит маркер доступа пользователя:</span><span class="sxs-lookup"><span data-stu-id="89a7a-184">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="89a7a-185">Приложение API сервера использует неизменяемое [утверждение идентификатора объекта (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) пользователя из маркера доступа для получения маркера доступа для API Graph.</span><span class="sxs-lookup"><span data-stu-id="89a7a-185">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="89a7a-186">Вызов API Graph получает определяемую пользователем Azure группу безопасности пользователя и членство для ролей администратора с помощью вызова метода пользователя [`memberOf`](/graph/api/user-list-memberof).</span><span class="sxs-lookup"><span data-stu-id="89a7a-186">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="89a7a-187">Членство используется для реализации утверждений `group`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-187">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="89a7a-188">[Политики авторизации](xref:security/authorization/policies) позволяют ограничить доступ пользователей к конечным точкам API сервера во всем приложении.</span><span class="sxs-lookup"><span data-stu-id="89a7a-188">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="89a7a-189">Сейчас это руководство не предусматривает авторизацию пользователей на основе [определяемых пользователем ролей AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="89a7a-189">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="89a7a-190">Рекомендации в этом разделе описывают настройку приложения API сервера в качестве [*управляющей программы*](/azure/active-directory/develop/scenario-daemon-overview) для вызова API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="89a7a-190">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="89a7a-191">В этом подходе **не** :</span><span class="sxs-lookup"><span data-stu-id="89a7a-191">This approach does **not** :</span></span>

* <span data-ttu-id="89a7a-192">требуется область `access_as_user`;</span><span class="sxs-lookup"><span data-stu-id="89a7a-192">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="89a7a-193">выполняется доступ API Graph от имени пользователя или клиента, выполняющего запрос API.</span><span class="sxs-lookup"><span data-stu-id="89a7a-193">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="89a7a-194">Для вызова API Graph приложением API сервера требуется только предоставление области API Graph **Приложение** уровня `Directory.Read.All` на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="89a7a-194">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="89a7a-195">Такой подход полностью запрещает клиентскому приложению доступ к данным каталога, которые API сервера не разрешает явно.</span><span class="sxs-lookup"><span data-stu-id="89a7a-195">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="89a7a-196">Клиентское приложение может получить доступ только к конечным точкам контроллера приложения API сервера.</span><span class="sxs-lookup"><span data-stu-id="89a7a-196">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="89a7a-197">Конфигурация Azure</span><span class="sxs-lookup"><span data-stu-id="89a7a-197">Azure configuration</span></span>

* <span data-ttu-id="89a7a-198">Убедитесь, что регистрации приложения *Server* предоставлена область API Graph **Приложение** (не **Делегат** ) с уровнем `Directory.Read.All`, которая является уровнем доступа с минимальным набором прав для групп безопасности.</span><span class="sxs-lookup"><span data-stu-id="89a7a-198">Confirm that the *Server* app registration is given **Application** (not **Delegated** ) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="89a7a-199">Убедитесь, что согласие администратора применяется к области после создания назначения области.</span><span class="sxs-lookup"><span data-stu-id="89a7a-199">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="89a7a-200">Назначьте приложению *Server* новый секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="89a7a-200">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="89a7a-201">Запишите секрет для конфигурации приложения в разделе [Параметры приложения](#app-settings).</span><span class="sxs-lookup"><span data-stu-id="89a7a-201">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="89a7a-202">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="89a7a-202">App settings</span></span>

<span data-ttu-id="89a7a-203">В файле параметров приложения (`appsettings.json` или `appsettings.Production.json`) создайте запись `ClientSecret` с секретом приложения *Server* с портала Azure:</span><span class="sxs-lookup"><span data-stu-id="89a7a-203">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="89a7a-204">Пример:</span><span class="sxs-lookup"><span data-stu-id="89a7a-204">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="89a7a-205">Если домен издателя клиента не проверен, в области API сервера для доступа пользователя или клиента используется универсальный код ресурса (URI) на основе `https://`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-205">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="89a7a-206">В этом сценарии приложению API сервера требуется конфигурация `Audience` в файле `appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-206">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="89a7a-207">В следующей конфигурации конец значения `Audience` **не** включает область по умолчанию `/{DEFAULT SCOPE}`, где заполнитель `{DEFAULT SCOPE}` является областью по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="89a7a-207">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> <span data-ttu-id="89a7a-208">В приведенном выше примере конфигурации:</span><span class="sxs-lookup"><span data-stu-id="89a7a-208">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="89a7a-209">домен клиента `contoso.onmicrosoft.com`;</span><span class="sxs-lookup"><span data-stu-id="89a7a-209">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="89a7a-210">`ClientId` приложения API сервера `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-210">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="89a7a-211">Настройка `Audience` явным образом обычно **не** требуется для приложения с проверенным доменом издателя с областью API на основе `api://`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-211">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="89a7a-212">Для получения дополнительной информации см. <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="89a7a-212">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="89a7a-213">Политики авторизации,</span><span class="sxs-lookup"><span data-stu-id="89a7a-213">Authorization policies</span></span>

<span data-ttu-id="89a7a-214">Создайте [политики авторизации](xref:security/authorization/policies) для групп безопасности AAD и ролей администратора AAD в методе `Startup.ConfigureServices` приложения *Server* (`Startup.cs`) на основе идентификаторов объектов групп и [идентификаторов объектов ролей администратора AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="89a7a-214">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="89a7a-215">Например, политика роли администратора выставления счетов в Azure имеет следующую конфигурацию:</span><span class="sxs-lookup"><span data-stu-id="89a7a-215">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="89a7a-216">Для получения дополнительной информации см. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="89a7a-216">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="89a7a-217">Доступ к контроллеру</span><span class="sxs-lookup"><span data-stu-id="89a7a-217">Controller access</span></span>

<span data-ttu-id="89a7a-218">Задайте обязательные политики на контроллерах приложения *Server*.</span><span class="sxs-lookup"><span data-stu-id="89a7a-218">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="89a7a-219">Следующий пример ограничивает доступ к данным выставления счетов с `BillingDataController` для администраторов выставления счетов Azure с использованием имени политики `BillingAdmin`, которое задано в разделе [Политики авторизации](#authorization-policies):</span><span class="sxs-lookup"><span data-stu-id="89a7a-219">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="89a7a-220">Пакеты</span><span class="sxs-lookup"><span data-stu-id="89a7a-220">Packages</span></span>

<span data-ttu-id="89a7a-221">Добавьте в приложение *Server* ссылки на следующие пакеты:</span><span class="sxs-lookup"><span data-stu-id="89a7a-221">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="89a7a-222">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="89a7a-222">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="89a7a-223">[Microsoft.:::no-loc(Identity):::.Client](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Client)</span><span class="sxs-lookup"><span data-stu-id="89a7a-223">[Microsoft.:::no-loc(Identity):::.Client](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Client)</span></span>

### <a name="services"></a><span data-ttu-id="89a7a-224">Службы</span><span class="sxs-lookup"><span data-stu-id="89a7a-224">Services</span></span>

<span data-ttu-id="89a7a-225">Код в методе `Startup.ConfigureServices` класса `Startup` приложения *Server* требует дополнительные пространства имен.</span><span class="sxs-lookup"><span data-stu-id="89a7a-225">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="89a7a-226">Добавьте следующие пространства имен в `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="89a7a-226">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.:::no-loc(Identity):::.Client;
using Microsoft.:::no-loc(Identity):::Model.Logging;
```

<span data-ttu-id="89a7a-227">При настройке <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span><span class="sxs-lookup"><span data-stu-id="89a7a-227">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="89a7a-228">При необходимости включите обработку для <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="89a7a-228">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="89a7a-229">Например, приложение может регистрировать неудачные попытки аутентификации.</span><span class="sxs-lookup"><span data-stu-id="89a7a-229">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="89a7a-230">В <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> создайте вызов API Graph для получения групп и ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="89a7a-230">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="89a7a-231"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> предоставляет персональные данные в записях сообщений в журнале.</span><span class="sxs-lookup"><span data-stu-id="89a7a-231"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="89a7a-232">Активируйте персональные данные только для отладки в тестовых учетных записях пользователей.</span><span class="sxs-lookup"><span data-stu-id="89a7a-232">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="89a7a-233">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="89a7a-233">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
:::no-loc(Identity):::ModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoft:::no-loc(Identity):::WebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)context.Principal.:::no-loc(Identity):::;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

<span data-ttu-id="89a7a-234">В приведенном выше коде обработка следующих ошибок токена является необязательной:</span><span class="sxs-lookup"><span data-stu-id="89a7a-234">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="89a7a-235">`MsalUiRequiredException`: приложение не имеет достаточных разрешений (областей).</span><span class="sxs-lookup"><span data-stu-id="89a7a-235">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="89a7a-236">Убедитесь, что области приложения API сервера на портале Azure включают разрешение **Приложение** для `Directory.Read.All`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-236">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="89a7a-237">Убедитесь, что администратор клиента предоставил разрешения для приложения.</span><span class="sxs-lookup"><span data-stu-id="89a7a-237">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="89a7a-238">`MsalServiceException` (`AADSTS70011`): Убедитесь, что областью является `https://graph.microsoft.com/.default`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-238">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="89a7a-239">Пакеты</span><span class="sxs-lookup"><span data-stu-id="89a7a-239">Packages</span></span>

<span data-ttu-id="89a7a-240">Добавьте в приложение *Server* ссылки на следующие пакеты:</span><span class="sxs-lookup"><span data-stu-id="89a7a-240">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="89a7a-241">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="89a7a-241">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="89a7a-242">[Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="89a7a-242">[Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="89a7a-243">Конфигурация службы</span><span class="sxs-lookup"><span data-stu-id="89a7a-243">Service configuration</span></span>

<span data-ttu-id="89a7a-244">Добавьте логику в метод `Startup.ConfigureServices` приложения *Server* , чтобы API Graph вызывал и реализовывал утверждения `group` для групп безопасности и ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="89a7a-244">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="89a7a-245">В примере кода в этом разделе используется библиотека проверки подлинности Active Directory (ADAL), которая основана на платформе Microsoft :::no-loc(Identity)::: Platform версии 1.0.</span><span class="sxs-lookup"><span data-stu-id="89a7a-245">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft :::no-loc(Identity)::: Platform v1.0.</span></span>

<span data-ttu-id="89a7a-246">Код в классе `Startup` приложения *Server* требует дополнительные пространства имен.</span><span class="sxs-lookup"><span data-stu-id="89a7a-246">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="89a7a-247">Следующий набор операторов `using` включает необходимые пространства имен для кода, который приведен в этом разделе:</span><span class="sxs-lookup"><span data-stu-id="89a7a-247">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
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
using Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory;
using Microsoft.:::no-loc(Identity):::Model.Logging;
```

<span data-ttu-id="89a7a-248">При настройке <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span><span class="sxs-lookup"><span data-stu-id="89a7a-248">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="89a7a-249">При необходимости включите обработку для <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="89a7a-249">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="89a7a-250">Например, приложение может регистрировать неудачные попытки аутентификации.</span><span class="sxs-lookup"><span data-stu-id="89a7a-250">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="89a7a-251">В <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> создайте вызов API Graph для получения групп и ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="89a7a-251">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="89a7a-252"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> предоставляет персональные данные в записях сообщений в журнале.</span><span class="sxs-lookup"><span data-stu-id="89a7a-252"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="89a7a-253">Активируйте персональные данные только для отладки в тестовых учетных записях пользователей.</span><span class="sxs-lookup"><span data-stu-id="89a7a-253">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="89a7a-254">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="89a7a-254">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
:::no-loc(Identity):::ModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
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

                var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)context.Principal.:::no-loc(Identity):::;

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
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="89a7a-255">В предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="89a7a-255">In the preceding example:</span></span>

* <span data-ttu-id="89a7a-256">Автоматическое получение маркера (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) выполняется первым, так как маркер доступа может быть уже сохранен в кэше маркеров ADAL.</span><span class="sxs-lookup"><span data-stu-id="89a7a-256">Silent token acquisition (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="89a7a-257">Получить маркер из кэша быстрее, чем запросить новый маркер.</span><span class="sxs-lookup"><span data-stu-id="89a7a-257">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="89a7a-258">Если маркер доступа не получен из кэша (возникает исключение <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> или <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType>), операция утверждения пользователя (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.UserAssertion>) выполняется с учетными данными клиента (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.ClientCredential>) для получения маркера от имени пользователя (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span><span class="sxs-lookup"><span data-stu-id="89a7a-258">If the access token isn't acquired from cache (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="89a7a-259">Затем `Microsoft.Graph.GraphServiceClient` может выполнить вызов API Graph с помощью маркера.</span><span class="sxs-lookup"><span data-stu-id="89a7a-259">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="89a7a-260">Маркер помещается в кэш маркеров ADAL.</span><span class="sxs-lookup"><span data-stu-id="89a7a-260">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="89a7a-261">При будущих вызовах API Graph к этому же пользователю маркер будет автоматически получен из кэша с помощью <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="89a7a-261">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="89a7a-262">Код в <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> не получает сведения о временном членстве.</span><span class="sxs-lookup"><span data-stu-id="89a7a-262">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="89a7a-263">Чтобы изменить код для получения сведений о непосредственном и временном членстве, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="89a7a-263">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="89a7a-264">Для строки кода:</span><span class="sxs-lookup"><span data-stu-id="89a7a-264">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="89a7a-265">Замените предыдущую строку следующим:</span><span class="sxs-lookup"><span data-stu-id="89a7a-265">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="89a7a-266">Для строки кода:</span><span class="sxs-lookup"><span data-stu-id="89a7a-266">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="89a7a-267">Замените предыдущую строку следующим:</span><span class="sxs-lookup"><span data-stu-id="89a7a-267">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="89a7a-268">Код в <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> не различает группы безопасности AAD и роли администратора AAD при создании утверждений.</span><span class="sxs-lookup"><span data-stu-id="89a7a-268">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="89a7a-269">Чтобы приложение различало группы и роли, проверьте `entry.ODataType` при итерации по группам и ролям.</span><span class="sxs-lookup"><span data-stu-id="89a7a-269">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="89a7a-270">Чтобы создать отдельные утверждения для групп безопасности и ролей, используйте код, аналогичный приведенному ниже:</span><span class="sxs-lookup"><span data-stu-id="89a7a-270">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        user:::no-loc(Identity):::.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="89a7a-271">Определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="89a7a-271">User-defined roles</span></span>

<span data-ttu-id="89a7a-272">Приложение, зарегистрированное в AAD, можно также настроить для использования определяемых пользователем ролей.</span><span class="sxs-lookup"><span data-stu-id="89a7a-272">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="89a7a-273">Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `roles`, см. в разделе [Практическое руководство. Добавление ролей приложения в приложение, зарегистрированное в Azure Active Directory, и их получение в токене](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) документации Azure.</span><span class="sxs-lookup"><span data-stu-id="89a7a-273">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="89a7a-274">В следующем примере предполагается, что приложение настроено с двумя ролями:</span><span class="sxs-lookup"><span data-stu-id="89a7a-274">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="89a7a-275">Несмотря на то, что вы не можете назначать роли группам безопасности без учетной записи Azure AD Premium, вы можете назначить роли пользователям и получить утверждение `roles` для пользователей с помощью стандартной учетной записи Azure.</span><span class="sxs-lookup"><span data-stu-id="89a7a-275">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="89a7a-276">В рекомендациях в данном разделе не требуется учетная запись Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="89a7a-276">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="89a7a-277">На портале Azure назначается несколько ролей путем **_повторного добавления пользователей_** для каждого дополнительного назначения роли.</span><span class="sxs-lookup"><span data-stu-id="89a7a-277">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="89a7a-278">Одно утверждение `roles`, отправленное AAD, представляет определяемые пользователем роли как `appRoles` `value` в массиве JSON.</span><span class="sxs-lookup"><span data-stu-id="89a7a-278">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="89a7a-279">Приложение должно преобразовать массив JSON из ролей в индивидуальные утверждения `role`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-279">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="89a7a-280">Класс `CustomUserFactory`, приведенный в разделе [Определяемые пользователем группы и роли администратора AAD](#user-defined-groups-and-administrator-roles), настроен на выполнение действий с утверждением `roles` с использованием значения массива JSON.</span><span class="sxs-lookup"><span data-stu-id="89a7a-280">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="89a7a-281">Выполните добавление и регистрацию `CustomUserFactory` в изолированном приложении или приложении *`Client`* размещенного решения :::no-loc(Blazor):::, как показано в разделе [Определяемые пользователями группы и роли администратора AAD](#user-defined-groups-and-administrator-roles).</span><span class="sxs-lookup"><span data-stu-id="89a7a-281">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="89a7a-282">Нет необходимости предоставлять код для удаления исходного утверждения `roles`, поскольку оно автоматически удаляется платформой.</span><span class="sxs-lookup"><span data-stu-id="89a7a-282">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="89a7a-283">В `Program.Main` изолированного приложения или приложения *`Client`* размещенного решения :::no-loc(Blazor)::: укажите утверждение с именем `role` в качестве утверждения роли:</span><span class="sxs-lookup"><span data-stu-id="89a7a-283">In `Program.Main` of the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="89a7a-284">На этом этапе можно применять подходы с авторизацией компонентов.</span><span class="sxs-lookup"><span data-stu-id="89a7a-284">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="89a7a-285">Любой из механизмов авторизации в компонентах может использовать роль `admin` для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="89a7a-285">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="89a7a-286">[Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="89a7a-286">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="89a7a-287">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="89a7a-287">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="89a7a-288">[Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="89a7a-288">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="89a7a-289">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="89a7a-289">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="89a7a-290">Идентификаторы объектов ролей администратора AAD</span><span class="sxs-lookup"><span data-stu-id="89a7a-290">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="89a7a-291">Идентификаторы объектов, приведенные в следующей таблице, используются для создания [политик](xref:security/authorization/policies) для утверждений `group`.</span><span class="sxs-lookup"><span data-stu-id="89a7a-291">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="89a7a-292">Политики позволяют приложению авторизовать пользователей для различных действий в приложении.</span><span class="sxs-lookup"><span data-stu-id="89a7a-292">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="89a7a-293">Дополнительные сведения см. в разделе [Определяемые пользователем группы и роли администратора AAD](#user-defined-groups-and-administrator-roles).</span><span class="sxs-lookup"><span data-stu-id="89a7a-293">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="89a7a-294">Роли администратора в AAD</span><span class="sxs-lookup"><span data-stu-id="89a7a-294">AAD Administrator Role</span></span> | <span data-ttu-id="89a7a-295">Идентификатор объекта.</span><span class="sxs-lookup"><span data-stu-id="89a7a-295">Object ID</span></span>
--- | ---
<span data-ttu-id="89a7a-296">администратор приложений;</span><span class="sxs-lookup"><span data-stu-id="89a7a-296">Application administrator</span></span> | <span data-ttu-id="89a7a-297">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="89a7a-297">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="89a7a-298">Разработчик приложений</span><span class="sxs-lookup"><span data-stu-id="89a7a-298">Application developer</span></span> | <span data-ttu-id="89a7a-299">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="89a7a-299">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="89a7a-300">Администратор проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="89a7a-300">Authentication administrator</span></span> | <span data-ttu-id="89a7a-301">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="89a7a-301">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="89a7a-302">Администратор Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="89a7a-302">Azure DevOps administrator</span></span> | <span data-ttu-id="89a7a-303">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="89a7a-303">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="89a7a-304">Администратор Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="89a7a-304">Azure Information Protection administrator</span></span> | <span data-ttu-id="89a7a-305">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="89a7a-305">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="89a7a-306">Администратор наборов ключей IEF B2C</span><span class="sxs-lookup"><span data-stu-id="89a7a-306">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="89a7a-307">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="89a7a-307">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="89a7a-308">Администратор политик IEF B2C</span><span class="sxs-lookup"><span data-stu-id="89a7a-308">B2C IEF Policy administrator</span></span> | <span data-ttu-id="89a7a-309">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="89a7a-309">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="89a7a-310">Администратор потоков пользователей B2C</span><span class="sxs-lookup"><span data-stu-id="89a7a-310">B2C user flow administrator</span></span> | <span data-ttu-id="89a7a-311">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="89a7a-311">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="89a7a-312">Администратор атрибутов потоков пользователей B2C</span><span class="sxs-lookup"><span data-stu-id="89a7a-312">B2C user flow attribute administrator</span></span> | <span data-ttu-id="89a7a-313">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="89a7a-313">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="89a7a-314">Администратор выставления счетов</span><span class="sxs-lookup"><span data-stu-id="89a7a-314">Billing administrator</span></span> | <span data-ttu-id="89a7a-315">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="89a7a-315">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="89a7a-316">Администратор облачных приложений</span><span class="sxs-lookup"><span data-stu-id="89a7a-316">Cloud application administrator</span></span> | <span data-ttu-id="89a7a-317">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="89a7a-317">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="89a7a-318">Администратор облачного устройства</span><span class="sxs-lookup"><span data-stu-id="89a7a-318">Cloud device administrator</span></span> | <span data-ttu-id="89a7a-319">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="89a7a-319">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="89a7a-320">администратор соответствия требованиям.</span><span class="sxs-lookup"><span data-stu-id="89a7a-320">Compliance administrator</span></span> | <span data-ttu-id="89a7a-321">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="89a7a-321">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="89a7a-322">Администратор данных соответствия</span><span class="sxs-lookup"><span data-stu-id="89a7a-322">Compliance data administrator</span></span> | <span data-ttu-id="89a7a-323">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="89a7a-323">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="89a7a-324">Администратор условного доступа</span><span class="sxs-lookup"><span data-stu-id="89a7a-324">Conditional Access administrator</span></span> | <span data-ttu-id="89a7a-325">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="89a7a-325">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="89a7a-326">лицо, утверждающее доступ клиентов к LockBox;</span><span class="sxs-lookup"><span data-stu-id="89a7a-326">Customer LockBox access approver</span></span> | <span data-ttu-id="89a7a-327">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="89a7a-327">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="89a7a-328">Администратор аналитики классических приложений</span><span class="sxs-lookup"><span data-stu-id="89a7a-328">Desktop Analytics administrator</span></span> | <span data-ttu-id="89a7a-329">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="89a7a-329">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="89a7a-330">Читатели каталога</span><span class="sxs-lookup"><span data-stu-id="89a7a-330">Directory readers</span></span> | <span data-ttu-id="89a7a-331">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="89a7a-331">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="89a7a-332">Администратор Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="89a7a-332">Dynamics 365 administrator</span></span> | <span data-ttu-id="89a7a-333">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="89a7a-333">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="89a7a-334">администратор Exchange;</span><span class="sxs-lookup"><span data-stu-id="89a7a-334">Exchange administrator</span></span> | <span data-ttu-id="89a7a-335">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="89a7a-335">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="89a7a-336">Администратор внешнего поставщика :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="89a7a-336">External :::no-loc(Identity)::: Provider administrator</span></span> | <span data-ttu-id="89a7a-337">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="89a7a-337">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="89a7a-338">Глобальный администратор.</span><span class="sxs-lookup"><span data-stu-id="89a7a-338">Global administrator</span></span> | <span data-ttu-id="89a7a-339">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="89a7a-339">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="89a7a-340">Глобальный читатель</span><span class="sxs-lookup"><span data-stu-id="89a7a-340">Global reader</span></span> | <span data-ttu-id="89a7a-341">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="89a7a-341">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="89a7a-342">Администратор групп</span><span class="sxs-lookup"><span data-stu-id="89a7a-342">Groups administrator</span></span> | <span data-ttu-id="89a7a-343">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="89a7a-343">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="89a7a-344">Приглашающий гостей</span><span class="sxs-lookup"><span data-stu-id="89a7a-344">Guest inviter</span></span> | <span data-ttu-id="89a7a-345">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="89a7a-345">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="89a7a-346">администратор службы технической поддержки;</span><span class="sxs-lookup"><span data-stu-id="89a7a-346">Helpdesk administrator</span></span> | <span data-ttu-id="89a7a-347">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="89a7a-347">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="89a7a-348">Администратор Intune</span><span class="sxs-lookup"><span data-stu-id="89a7a-348">Intune administrator</span></span> | <span data-ttu-id="89a7a-349">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="89a7a-349">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="89a7a-350">Администратор Kaizala</span><span class="sxs-lookup"><span data-stu-id="89a7a-350">Kaizala administrator</span></span> | <span data-ttu-id="89a7a-351">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="89a7a-351">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="89a7a-352">Администратор лицензий</span><span class="sxs-lookup"><span data-stu-id="89a7a-352">License administrator</span></span> | <span data-ttu-id="89a7a-353">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="89a7a-353">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="89a7a-354">Читатель конфиденциальности данных Центра сообщений</span><span class="sxs-lookup"><span data-stu-id="89a7a-354">Message center privacy reader</span></span> | <span data-ttu-id="89a7a-355">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="89a7a-355">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="89a7a-356">Читатель центра сообщений</span><span class="sxs-lookup"><span data-stu-id="89a7a-356">Message center reader</span></span> | <span data-ttu-id="89a7a-357">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="89a7a-357">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="89a7a-358">Администратор приложений Office</span><span class="sxs-lookup"><span data-stu-id="89a7a-358">Office apps administrator</span></span> | <span data-ttu-id="89a7a-359">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="89a7a-359">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="89a7a-360">Администратор паролей</span><span class="sxs-lookup"><span data-stu-id="89a7a-360">Password administrator</span></span> | <span data-ttu-id="89a7a-361">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="89a7a-361">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="89a7a-362">Администратор Power BI</span><span class="sxs-lookup"><span data-stu-id="89a7a-362">Power BI administrator</span></span> | <span data-ttu-id="89a7a-363">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="89a7a-363">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="89a7a-364">Администратор Power Platform</span><span class="sxs-lookup"><span data-stu-id="89a7a-364">Power platform administrator</span></span> | <span data-ttu-id="89a7a-365">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="89a7a-365">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="89a7a-366">Привилегированный администратор проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="89a7a-366">Privileged authentication administrator</span></span> | <span data-ttu-id="89a7a-367">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="89a7a-367">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="89a7a-368">администратор привилегированных ролей;</span><span class="sxs-lookup"><span data-stu-id="89a7a-368">Privileged role administrator</span></span> | <span data-ttu-id="89a7a-369">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="89a7a-369">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="89a7a-370">Читатель отчетов</span><span class="sxs-lookup"><span data-stu-id="89a7a-370">Reports reader</span></span> | <span data-ttu-id="89a7a-371">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="89a7a-371">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="89a7a-372">Администратор поиска</span><span class="sxs-lookup"><span data-stu-id="89a7a-372">Search administrator</span></span> | <span data-ttu-id="89a7a-373">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="89a7a-373">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="89a7a-374">Редактор поиска</span><span class="sxs-lookup"><span data-stu-id="89a7a-374">Search editor</span></span> | <span data-ttu-id="89a7a-375">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="89a7a-375">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="89a7a-376">администратор безопасности;</span><span class="sxs-lookup"><span data-stu-id="89a7a-376">Security administrator</span></span> | <span data-ttu-id="89a7a-377">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="89a7a-377">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="89a7a-378">Оператор безопасности</span><span class="sxs-lookup"><span data-stu-id="89a7a-378">Security operator</span></span> | <span data-ttu-id="89a7a-379">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="89a7a-379">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="89a7a-380">Читатель сведений о безопасности</span><span class="sxs-lookup"><span data-stu-id="89a7a-380">Security reader</span></span> | <span data-ttu-id="89a7a-381">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="89a7a-381">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="89a7a-382">администратор службы поддержки;</span><span class="sxs-lookup"><span data-stu-id="89a7a-382">Service support administrator</span></span> | <span data-ttu-id="89a7a-383">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="89a7a-383">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="89a7a-384">администратор SharePoint;</span><span class="sxs-lookup"><span data-stu-id="89a7a-384">SharePoint administrator</span></span> | <span data-ttu-id="89a7a-385">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="89a7a-385">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="89a7a-386">администратор Skype для бизнеса;</span><span class="sxs-lookup"><span data-stu-id="89a7a-386">Skype for Business administrator</span></span> | <span data-ttu-id="89a7a-387">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="89a7a-387">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="89a7a-388">Администратор связи Teams</span><span class="sxs-lookup"><span data-stu-id="89a7a-388">Teams Communications Administrator</span></span> | <span data-ttu-id="89a7a-389">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="89a7a-389">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="89a7a-390">Инженер службы поддержки связи Teams</span><span class="sxs-lookup"><span data-stu-id="89a7a-390">Teams Communications Support Engineer</span></span> | <span data-ttu-id="89a7a-391">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="89a7a-391">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="89a7a-392">Специалист службы поддержки связи Teams</span><span class="sxs-lookup"><span data-stu-id="89a7a-392">Teams Communications Specialist</span></span> | <span data-ttu-id="89a7a-393">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="89a7a-393">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="89a7a-394">Администратор службы Teams</span><span class="sxs-lookup"><span data-stu-id="89a7a-394">Teams Service Administrator</span></span> | <span data-ttu-id="89a7a-395">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="89a7a-395">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="89a7a-396">Администратор пользователей</span><span class="sxs-lookup"><span data-stu-id="89a7a-396">User administrator</span></span> | <span data-ttu-id="89a7a-397">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="89a7a-397">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89a7a-398">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="89a7a-398">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
