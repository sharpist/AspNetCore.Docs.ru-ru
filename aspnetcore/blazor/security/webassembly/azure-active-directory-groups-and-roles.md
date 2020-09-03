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
ms.openlocfilehash: adc16989e5b4e2e639896e5fe9562e42cb8ceeb4
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102748"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Группы Azure AD, административные роли и определяемые пользователем роли

Авторы: [Люк Латэм](https://github.com/javiercn) (Luke Latham) и [Хавьер Кальварро Нельсон](https://github.com/guardrex) (Javier Calvarro Nelson)

Azure Active Directory (AAD) предоставляет несколько подходов к авторизации, которые можно сочетать с ASP.NET Core Identity.

* Определяемые пользователем группы
  * Безопасность
  * Microsoft 365
  * Распределение
* Роли
  * Встроенные административные роли
  * Определяемые пользователем роли

Рекомендации в этой статье относятся к сценариям развертывания Blazor WebAssembly AAD, описанным в следующих разделах:

* [Автономное развертывание с помощью учетных записей Майкрософт](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Автономное развертывание с помощью AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Размещенное развертывание с помощью AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a>Разрешения API Microsoft Graph

Для любого пользователя приложения с более чем пятью встроенными ролями администратора AAD и членством в группах безопасности требуется вызов [API Microsoft Graph](/graph/use-the-api).

Чтобы разрешить API Graph вызовы, предоставьте автономному или клиентскому приложению размещенного решения Blazor любое из следующих [разрешений API Graph](/graph/permissions-reference) на портале Azure:

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` является разрешением с наименьшими привилегиями, используемым для примера, описанного в этой статье.

## <a name="user-defined-groups-and-built-in-administrative-roles"></a>Определяемые пользователем группы и встроенные административные роли

Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `groups`, см. в следующих статьях Azure. Назначение пользователей для определяемых пользователем групп AAD и встроенных административных ролей.

* [Роли, использующие группы безопасности Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [Атрибут `groupMembershipClaims`](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

В следующих примерах предполагается, что пользователю назначена встроенная роль *Администратор выставления счетов*.

Одно утверждение `groups`, отправленное AAD, представляет группы и роли пользователя в виде идентификаторов объектов (GUID) в массиве JSON. Приложение должно преобразовать массив JSON групп и ролей в отдельные утверждения `group`, для которых приложение может создавать [политики](xref:security/authorization/policies).

Когда число назначенных встроенных административных ролей Azure и определяемых пользователем групп превышает пять, AAD отправляет `hasgroups` утверждение со значением `true` вместо отправки утверждения `groups`. Любое приложение, которое может иметь более пяти ролей и групп, назначенных пользователям, должно выполнять отдельный вызов API Graph для получения таких пользовательских ролей и групп. Пример реализации, приведенный в этой статье, посвящен этому сценарию. Дополнительные сведения см. в сведениях об утверждениях `groups` и `hasgroups` см. в статье [Маркеры доступа платформы идентификации Майкрософт. Утверждение полезных данных](/azure/active-directory/develop/access-tokens#payload-claims).

Расширьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, чтобы включить свойства массива для групп и ролей. Присвойте каждому свойству пустой массив, чтобы проверка `null` не требовалась, если эти свойства используются в циклах `foreach` позже.

`CustomUserAccount.cs`:

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

В автономном приложении или клиентском приложении размещенного решения Blazor создайте пользовательский класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>. Используйте правильную область (разрешение) для вызовов API Graph, получающих сведения о ролях и группах.

`GraphAPIAuthorizationMessageHandler.cs`:

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

В `Program.Main` (`Program.cs`) добавьте службу реализации <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> и добавьте именованный <xref:System.Net.Http.HttpClient> для выполнения запросов API Graph. В следующем примере создается клиент с именем `GraphAPI`.

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Создайте классы объектов каталога AAD для получения ролей и групп OData из вызова API Graph. Данные OData передаются в формате JSON, а вызов <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> заполняет экземпляр класса `DirectoryObjects`.

`DirectoryObjects.cs`:

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

Создайте настраиваемую фабрику пользователей для обработки утверждений ролей и групп. В следующем примере реализации также обрабатывается массив утверждений `roles`, который рассматривается в разделе [Определяемые пользователем роли](#user-defined-roles). Если имеется утверждение `hasgroups`, то именованный <xref:System.Net.Http.HttpClient> используется для создания разрешенного запроса API Graph на получение ролей и групп пользователей. В этой реализации используется конечная точка `https://graph.microsoft.com/v1.0/me/memberOf` Microsoft Identity Platform версии 1.0 ([документация по API](/graph/api/user-list-memberof)). Рекомендации в этом разделе будут обновлены для версии 2.0 Identity при обновлении пакетов MSAL для версии 2.0.

`CustomAccountFactory.cs`:

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

Нет необходимости предоставлять код для удаления исходного утверждения `groups`, если оно есть, поскольку оно автоматически удаляется платформой.

> [!NOTE]
> В этом примере:
>
> * добавляется пользовательский класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> для присоединения маркеров доступа к исходящим запросам;
> * добавляется именованный <xref:System.Net.Http.HttpClient> для выполнения запросов веб-API к безопасной внешней конечной точке веб-API;
> * используется именованный <xref:System.Net.Http.HttpClient> для выполнения разрешенных запросов.
>
> Более подробно этот подход рассматривается в статье <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.

Зарегистрируйте фабрику в `Program.Main` (`Program.cs`) изолированного приложения или клиентского приложения размещенного решения Blazor: Согласие на область разрешений `Directory.Read.All` в качестве дополнительной области для приложения:

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

Создайте [политики](xref:security/authorization/policies) для каждой группы или роли в `Program.Main`. В следующем примере создается политика для встроенной роли *Администратор выставления счетов*:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Полный список идентификаторов объектов ролей AAD см. в разделе [Идентификаторы группы административных ролей AAD](#aad-adminstrative-role-group-ids).

В следующих примерах приложение использует предыдущую политику для авторизации пользователя.

С политикой работает [компонент`AuthorizeView`](xref:blazor/security/index#authorizeview-component):

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

Доступ ко всему компоненту может основываться на политике, использующей [директиву атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Если пользователь не вошел в систему, он перенаправляется на страницу входа AAD, а затем после входа обратно в компонент.

Проверку политики можно также [выполнять в коде с помощью процедурной логики](xref:blazor/security/index#procedural-logic).

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

## <a name="user-defined-roles"></a>Определяемые пользователем роли

Приложение, зарегистрированное в AAD, можно также настроить для использования определяемых пользователем ролей.

Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `roles`, см. в разделе [Практическое руководство. Добавление ролей приложения в приложение, зарегистрированное в Azure Active Directory, и их получение в токене](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) документации Azure.

В следующем примере предполагается, что приложение настроено с двумя ролями:

* `admin`
* `developer`

> [!NOTE]
> Несмотря на то, что вы не можете назначать роли группам безопасности без учетной записи Azure AD Premium, вы можете назначить роли пользователям и получить утверждение `roles` для пользователей с помощью стандартной учетной записи Azure. В рекомендациях в данном разделе не требуется учетная запись Azure AD Premium.
>
> На портале Azure назначается несколько ролей путем **_повторного добавления пользователей_** для каждого дополнительного назначения роли.

Одно утверждение `roles`, отправленное AAD, представляет определяемые пользователем роли как `appRoles` `value` в массиве JSON. Приложение должно преобразовать массив JSON из ролей в индивидуальные утверждения `role`.

`CustomUserFactory`, показанные в разделе [Определяемые пользователем группы и административные роли AAD](#user-defined-groups-and-built-in-administrative-roles), настроены для работы с утверждением `roles` со значением массива JSON. Добавьте и зарегистрируйте `CustomUserFactory` в изолированном приложении или клиентском приложении размещенного решения Blazor, как показано в разделе [Определяемые пользователем группы и встроенные административные роли AAD](#user-defined-groups-and-built-in-administrative-roles). Нет необходимости предоставлять код для удаления исходного утверждения `roles`, поскольку оно автоматически удаляется платформой.

В `Program.Main` автономного приложения или клиентского приложения размещенного решения Blazor укажите утверждение с именем "`role`" в качестве утверждения роли:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

На этом этапе можно применять подходы с авторизацией компонентов. Любой из механизмов авторизации в компонентах может использовать роль `admin` для авторизации пользователя:

* [Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)
* [Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)
* [Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)

  Поддерживается тестирование с использованием нескольких ролей:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>Идентификаторы группы административных ролей AAD

Идентификаторы объектов, приведенные в следующей таблице, используются для создания [политик](xref:security/authorization/policies) для утверждений `group`. Политики позволяют приложению авторизовать пользователей для различных действий в приложении. Дополнительные сведения см. в разделе [Определяемые пользователем группы и встроенные административные роли AAD](#user-defined-groups-and-built-in-administrative-roles).

Административная роль AAD | Идентификатор объекта.
--- | ---
администратор приложений; | fa11557b-4f15-4ddd-85d5-313c7cd74047
Разработчик приложений | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Администратор проверки подлинности | 02d110a1-96b1-419e-af87-746461b60ed7
Администратор Azure DevOps | a5311ace-ca41-44cd-b833-8d22caa0b34f
Администратор Azure Information Protection | 18632dce-f9b5-4f01-abb5-37051f06860e
Администратор наборов ключей IEF B2C | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Администратор политик IEF B2C | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Администратор потоков пользователей B2C | baa531b7-8cf0-44ad-8f98-eded88dae827
Администратор атрибутов потоков пользователей B2C | dd0baca0-a535-48c1-b871-8431abe16452
Администратор выставления счетов | 69ff516a-b57d-4697-a429-9de4af7b5609
Администратор облачных приложений | 250b5fe3-b553-458d-9a53-b782c13c34bf
Администратор облачного устройства | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
администратор соответствия требованиям. | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Администратор данных соответствия | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Администратор условного доступа | 8f71a611-137d-49af-87ad-e97f1fd5da76
лицо, утверждающее доступ клиентов к LockBox; | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Администратор аналитики классических приложений | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Читатели каталога | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Администратор Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
администратор Exchange; | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
Администратор внешнего поставщика Identity | febfaeb4-e478-407a-b4b3-f4d9716618a2
Глобальный администратор. | a45ba61b-44db-462c-924b-3b2719152588
Глобальный читатель | f6903b21-6aba-4124-b44c-76671796b9d5
Администратор групп | 158b3e5a-d89d-460b-92b5-3b34985f0197
Приглашающий гостей | 4c730a1d-cc22-44af-8f9f-4eec635c7502
администратор службы технической поддержки; | 108678c8-6628-44e1-8d01-caf598a6a5f5
Администратор Intune | 79950741-23fa-4189-b2cb-46640601c497
Администратор Kaizala | d6322af2-48e7-42e0-8c68-0bbe31af3412
Администратор лицензий | 3355458a-e423-44bf-8b98-4ac5e572cea5
Читатель конфиденциальности данных Центра сообщений | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Читатель центра сообщений | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Администратор приложений Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Администратор паролей | 466e48b7-5d66-4ae5-8911-1a118de74941
Администратор Power BI | 984e83b8-8337-4255-91a1-acb663175ab4
Администратор Power Platform | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Привилегированный администратор проверки подлинности | 0829f731-b46d-419f-9742-aeb122367d11
администратор привилегированных ролей; | f20a725a-d1c8-4107-83ea-1171c97d00c7
Читатель отчетов | 54635450-e8ed-4f2d-9632-07db2517b4de
Администратор поиска | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Редактор поиска | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
администратор безопасности; | 20fa50e3-6531-44d8-bd39-b251420568ad
Оператор безопасности | 43aae017-8e51-4188-91ab-e6debd572800
Читатель сведений о безопасности | 45035cd3-fd97-4250-8197-3a53d3562d9b
администратор службы поддержки; | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
администратор SharePoint; | e1c32229-875e-461d-ae24-3cb99116e86c
администратор Skype для бизнеса; | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Администратор связи Teams | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Инженер службы поддержки связи Teams | 802dd94e-d717-46f6-af98-b9167071e9fc
Специалист службы поддержки связи Teams | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Администратор службы Teams | 8846a0be-197b-443a-b13c-11192691fa24
Администратор пользователей | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
