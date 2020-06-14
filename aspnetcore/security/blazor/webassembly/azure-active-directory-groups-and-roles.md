---
title: ASP.NET Core Blazor сборки с Azure Active Directory группами и ролями
author: guardrex
description: Узнайте, как настроить Blazor сборку для использования Azure Active Directory групп и ролей.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/aad-groups-roles
ms.openlocfilehash: 87cdf02a6f6babc869d90658e6a7cd54db73bb68
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756032"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Группы Azure AD, роли администрирования и определяемые пользователем роли

[Люк ЛаСаМ](https://github.com/guardrex) и [Хавьер калварро Воронков](https://github.com/javiercn)

Azure Active Directory (AAD) предоставляет несколько подходов к авторизации, которые можно сочетать с ASP.NET Core Identity :

* Определяемые пользователем группы
  * Безопасность
  * O365
  * Распределение
* Роли
  * Встроенные административные роли
  * Определяемые пользователем роли

Рекомендации в этой статье относятся к Blazor сценариям развертывания AAD в сборке, описанным в следующих разделах:

* [Автономное развертывание с помощью учетных записей Майкрософт](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [Автономное развертывание с помощью AAD](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [Размещенное развертывание с помощью AAD](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>Определяемые пользователем группы и встроенные административные роли

Чтобы настроить приложение в портал Azure для предоставления `groups` утверждения о членстве, см. следующие статьи Azure. Назначение пользователей для пользовательских групп AAD и встроенных административных ролей.

* [Роли, использующие группы безопасности Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [Атрибут groupMembershipClaims](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

В следующих примерах предполагается, что пользователь назначен встроенной роли *администратора выставления счетов* AAD.

Единое `groups` утверждение, отправленное AAD, представляет группы и роли пользователя в виде идентификаторов объектов (GUID) в массиве JSON. Приложение должно преобразовать массив JSON групп и ролей в индивидуальные `group` утверждения, для которых приложение может создавать [политики](xref:security/authorization/policies) .

Расширение <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> позволяет включать свойства массива для групп и ролей.

*CustomUserAccount.CS*:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

Создайте настраиваемую фабрику пользователей в отдельном приложении или клиентском приложении размещенного решения. Следующая фабрика также настроена для обработки `roles` массивов утверждений, которые рассматриваются в разделе [определяемые пользователем роли](#user-defined-roles) .

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
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

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

Нет необходимости предоставлять код для удаления исходного `groups` утверждения, так как он автоматически удаляется платформой.

Зарегистрируйте фабрику в `Program.Main` (*Program.CS*) автономного приложения или клиентского приложения размещенного решения:

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

Создайте [политику](xref:security/authorization/policies) для каждой группы или роли в `Program.Main` . В следующем примере создается политика для встроенной роли *администратора выставления счетов* AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Полный список идентификаторов объектов ролей AAD см. в разделе [идентификаторы групп ролей AAD администрирования](#aad-adminstrative-role-group-ids) .

В следующих примерах приложение использует предыдущую политику для авторизации пользователя.

[Компонент аусоризевиев](xref:security/blazor/index#authorizeview-component) работает с политикой:

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

Доступ ко всему компоненту может основываться на политике, использующей [ `[Authorize]` директиву Attribute](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Если пользователь не вошел в систему, он перенаправляется на страницу входа AAD, а затем обратно в компонент после входа.

Проверку политики также можно [выполнить в коде с помощью процедурной логики](xref:security/blazor/index#procedural-logic):

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

### <a name="user-defined-roles"></a>Определяемые пользователем роли

Приложение, зарегистрированное в AAD, можно также настроить для использования пользовательских ролей.

Чтобы настроить приложение в портал Azure для предоставления `roles` утверждения о членстве, см. статью [как добавить роли приложения в приложение и получить их в токене](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) в документации Azure.

В следующем примере предполагается, что приложение настроено с двумя ролями:

* `admin`
* `developer`

> [!NOTE]
> Хотя вы не можете назначать роли группам безопасности без учетной записи Azure AD Premium, вы можете назначить пользователей ролям и получить `roles` утверждение для пользователей с помощью стандартной учетной записи Azure. В рекомендациях этого раздела не требуется учетная запись Azure AD Premium.
>
> В портал Azure назначается несколько ролей путем **_повторного добавления пользователя_** для каждого дополнительного назначения роли.

Одно `roles` утверждение, отправленное AAD, представляет определяемые пользователем роли в качестве `appRoles` `value` s в массиве JSON. Приложение должно преобразовать массив JSON из ролей в индивидуальные `role` утверждения.

, `CustomUserFactory` Показанный в разделе [определяемые пользователем группы и встроенные административные роли AAD](#user-defined-groups-and-built-in-administrative-roles) , настроен для работы с `roles` утверждением со значением массива JSON. Добавьте и зарегистрируйте в `CustomUserFactory` отдельном приложении или клиентском приложении размещенного решения, как показано в разделе [определяемые пользователем группы и встроенные административные роли AAD](#user-defined-groups-and-built-in-administrative-roles) . Нет необходимости предоставлять код для удаления исходного `roles` утверждения, так как он автоматически удаляется платформой.

В `Program.Main` изолированном приложении или клиентском приложении размещенного решения укажите утверждение с именем "" в `role` качестве утверждения роли:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

На этом этапе работают подходы к авторизации компонентов. Любой из механизмов авторизации в компонентах может использовать эту `admin` роль для авторизации пользователя:

* [Компонент аусоризевиев](xref:security/blazor/index#authorizeview-component) (пример: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` директива Attribute](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (пример: `@attribute [Authorize(Roles = "admin")]` )
* [Процедурная логика](xref:security/blazor/index#procedural-logic) (пример: `if (user.IsInRole("admin")) { ... }` )

  Поддерживаются несколько тестов ролей:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>Идентификаторы группы ролей AAD администрирования

Идентификаторы объектов, приведенные в следующей таблице, используются для создания [политик](xref:security/authorization/policies) для `group` утверждений. Политики позволяют приложению авторизовать пользователей для различных действий в приложении. Дополнительные сведения см. в разделе [определяемые пользователем группы и встроенные административные роли AAD](#user-defined-groups-and-built-in-administrative-roles) .

Административная роль AAD | Идентификатор объекта.
--- | ---
администратор приложений; | fa11557b-4f15-4ddd-85d5-313c7cd74047
Разработчик приложений | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
администратор проверки подлинности. | 02d110a1-96b1-419e-af87-746461b60ed7
Администратор DevOps Azure | a5311ace-ca41-44cd-b833-8d22caa0b34f
Администратор Azure Information Protection | 18632dce-f9b5-4f01-abb5-37051f06860e
Администратор набора ключей B2C инфраструктура процедур идентификации | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Администратор политики B2C инфраструктура процедур идентификации | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Администратор потока пользователей B2C | baa531b7-8cf0-44ad-8f98-eded88dae827
Администратор атрибута потока пользователя B2C | dd0baca0-a535-48c1-b871-8431abe16452
Администратор выставления счетов | 69ff516a-b57d-4697-A429-9de4af7b5609
Администратор облачных приложений | 250b5fe3-b553-458d-9a53-b782c13c34bf
Администратор облачного устройства | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
администратор соответствия требованиям. | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Администратор данных соответствия требованиям | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Администратор условного доступа | 8f71a611-137d-49af-87ad-e97f1fd5da76
лицо, утверждающее доступ клиентов к LockBox; | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Администратор Desktop Analytics | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Читатели каталога | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Администратор Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
администратор Exchange; | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
IdentityАдминистратор внешнего поставщика | febfaeb4-e478-407a-b4b3-f4d9716618a2
Глобальный администратор. | a45ba61b-44db-462c-924b-3b2719152588
глобальный читатель; | f6903b21-6aba-4124-b44c-76671796b9d5
Администратор групп | 158b3e5a-d89d-460b-92b5-3b34985f0197
Приглашающий гостей | 4c730a1d-cc22-44af-8f9f-4eec635c7502
администратор службы технической поддержки; | 108678c8-6628-44e1-8d01-caf598a6a5f5
Администратор Intune | 79950741-23fa-4189-b2cb-46640601c497
Администратор Kaizala | d6322af2-48e7-42e0-8c68-0bbe31af3412
Администратор лицензий | 3355458a-e423-44bf-8b98-4ac5e572cea5
Читатель конфиденциальности в центре сообщений | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Средство чтения центра сообщений | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Администратор приложений Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Администратор паролей | 466e48b7-5d66-4ae5-8911-1a118de74941
Администратор Power BI | 984e83b8-8337-4255-91a1-acb663175ab4
Администратор платформа Power | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Администратор привилегированной проверки подлинности | 0829f731-b46d-419f-9742-aeb122367d11
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
Специалист по коммуникациям команд | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Администратор службы Teams | 8846a0be-197b-443a-b13c-11192691fa24
Администратор пользователей | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
