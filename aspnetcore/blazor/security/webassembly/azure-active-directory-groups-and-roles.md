---
title: ASP.NET Core [Blazor WebAssembly с группами и ролями Azure Active Directory
author: guardrex
description: Узнайте, как настроить [Blazor WebAssembly для использования групп и ролей Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 6e27b062d7b5a1b72804fe5d4ea31ec65358ce45
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402160"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="dcbd5-103">Группы Azure AD, административные роли и определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="dcbd5-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="dcbd5-104">Авторы: [Люк Латэм](https://github.com/javiercn) (Luke Latham) и [Хавьер Кальварро Нельсон](https://github.com/guardrex) (Javier Calvarro Nelson)</span><span class="sxs-lookup"><span data-stu-id="dcbd5-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="dcbd5-105">Azure Active Directory (AAD) предоставляет несколько подходов к авторизации, которые можно сочетать с ASP.NET Core [Identity:</span><span class="sxs-lookup"><span data-stu-id="dcbd5-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core [Identity:</span></span>

* <span data-ttu-id="dcbd5-106">Определяемые пользователем группы</span><span class="sxs-lookup"><span data-stu-id="dcbd5-106">User-defined groups</span></span>
  * <span data-ttu-id="dcbd5-107">Безопасность</span><span class="sxs-lookup"><span data-stu-id="dcbd5-107">Security</span></span>
  * <span data-ttu-id="dcbd5-108">O365</span><span class="sxs-lookup"><span data-stu-id="dcbd5-108">O365</span></span>
  * <span data-ttu-id="dcbd5-109">Распределение</span><span class="sxs-lookup"><span data-stu-id="dcbd5-109">Distribution</span></span>
* <span data-ttu-id="dcbd5-110">Роли</span><span class="sxs-lookup"><span data-stu-id="dcbd5-110">Roles</span></span>
  * <span data-ttu-id="dcbd5-111">Встроенные административные роли</span><span class="sxs-lookup"><span data-stu-id="dcbd5-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="dcbd5-112">Определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="dcbd5-112">User-defined roles</span></span>

<span data-ttu-id="dcbd5-113">Рекомендации в этой статье относятся к сценариям развертывания [Blazor WebAssembly AAD, описанным в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="dcbd5-113">The guidance in this article applies to the [Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="dcbd5-114">Автономное развертывание с помощью учетных записей Майкрософт</span><span class="sxs-lookup"><span data-stu-id="dcbd5-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="dcbd5-115">Автономное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="dcbd5-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="dcbd5-116">Размещенное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="dcbd5-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="dcbd5-117">Определяемые пользователем группы и встроенные административные роли</span><span class="sxs-lookup"><span data-stu-id="dcbd5-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="dcbd5-118">Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `groups`, см. в следующих статьях Azure.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="dcbd5-119">Назначение пользователей для определяемых пользователем групп AAD и встроенных административных ролей.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="dcbd5-120">Роли, использующие группы безопасности Azure AD</span><span class="sxs-lookup"><span data-stu-id="dcbd5-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="dcbd5-121">Атрибут `groupMembershipClaims`</span><span class="sxs-lookup"><span data-stu-id="dcbd5-121">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="dcbd5-122">В следующих примерах предполагается, что пользователю назначена встроенная роль *Администратор выставления счетов*.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="dcbd5-123">Одно утверждение `groups`, отправленное AAD, представляет группы и роли пользователя в виде идентификаторов объектов (GUID) в массиве JSON.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="dcbd5-124">Приложение должно преобразовать массив JSON групп и ролей в отдельные утверждения `group`, для которых приложение может создавать [политики](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="dcbd5-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="dcbd5-125">Расширьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, чтобы включить свойства массива для групп и ролей.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="dcbd5-126">`CustomUserAccount.cs`.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-126">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="dcbd5-127">создайте настраиваемую фабрику пользователей в изолированном приложении или клиентском приложении размещенного решения.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="dcbd5-128">Следующая фабрика также настроена для обработки массивов утверждений `roles`, которые рассматриваются в разделе [Определяемые пользователем роли](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="dcbd5-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

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

        if (initialUser.[Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.[Identity;

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

<span data-ttu-id="dcbd5-129">Нет необходимости предоставлять код для удаления исходного утверждения `groups`, поскольку оно автоматически удаляется платформой.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="dcbd5-130">Зарегистрируйте фабрику в `Program.Main` (`Program.cs`) изолированного приложения или клиентского приложения размещенного решения:</span><span class="sxs-lookup"><span data-stu-id="dcbd5-130">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or Client app of a Hosted solution:</span></span>

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

<span data-ttu-id="dcbd5-131">Создайте [политики](xref:security/authorization/policies) для каждой группы или роли в `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="dcbd5-132">В следующем примере создается политика для встроенной роли *Администратор выставления счетов*:</span><span class="sxs-lookup"><span data-stu-id="dcbd5-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="dcbd5-133">Полный список идентификаторов объектов ролей AAD см. в разделе [Идентификаторы группы административных ролей AAD](#aad-adminstrative-role-group-ids).</span><span class="sxs-lookup"><span data-stu-id="dcbd5-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="dcbd5-134">В следующих примерах приложение использует предыдущую политику для авторизации пользователя.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="dcbd5-135">С политикой работает [компонент`AuthorizeView`](xref:blazor/security/index#authorizeview-component):</span><span class="sxs-lookup"><span data-stu-id="dcbd5-135">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="dcbd5-136">Доступ ко всему компоненту может основываться на политике, использующей [директиву атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span><span class="sxs-lookup"><span data-stu-id="dcbd5-136">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="dcbd5-137">Если пользователь не вошел в систему, он перенаправляется на страницу входа AAD, а затем после входа обратно в компонент.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="dcbd5-138">Проверку политики можно также [выполнять в коде с помощью процедурной логики](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="dcbd5-138">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

### <a name="user-defined-roles"></a><span data-ttu-id="dcbd5-139">Определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="dcbd5-139">User-defined roles</span></span>

<span data-ttu-id="dcbd5-140">Приложение, зарегистрированное в AAD, можно также настроить для использования определяемых пользователем ролей.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="dcbd5-141">Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `roles`, см. в разделе [Практическое руководство. Добавление ролей приложения в приложение, зарегистрированное в Azure Active Directory, и их получение в токене](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) документации Azure.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="dcbd5-142">В следующем примере предполагается, что приложение настроено с двумя ролями:</span><span class="sxs-lookup"><span data-stu-id="dcbd5-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="dcbd5-143">Несмотря на то, что вы не можете назначать роли группам безопасности без учетной записи Azure AD Premium, вы можете назначить роли пользователям и получить утверждение `roles` для пользователей с помощью стандартной учетной записи Azure.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="dcbd5-144">В рекомендациях в данном разделе не требуется учетная запись Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="dcbd5-145">На портале Azure назначается несколько ролей путем **_повторного добавления пользователей_** для каждого дополнительного назначения роли.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="dcbd5-146">Одно утверждение `roles`, отправленное AAD, представляет определяемые пользователем роли как `appRoles` `value` в массиве JSON.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="dcbd5-147">Приложение должно преобразовать массив JSON из ролей в индивидуальные утверждения `role`.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="dcbd5-148">`CustomUserFactory`, показанные в разделе [Определяемые пользователем группы и административные роли AAD](#user-defined-groups-and-built-in-administrative-roles), настроены для работы с утверждением `roles` со значением массива JSON.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="dcbd5-149">Добавьте и зарегистрируйте `CustomUserFactory` в изолированном приложении или клиентском приложении размещенного решения, как показано в разделе [Определяемые пользователем группы и встроенные административные роли AAD](#user-defined-groups-and-built-in-administrative-roles).</span><span class="sxs-lookup"><span data-stu-id="dcbd5-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="dcbd5-150">Нет необходимости предоставлять код для удаления исходного утверждения `roles`, поскольку оно автоматически удаляется платформой.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="dcbd5-151">В `Program.Main` автономного приложения или клиентского приложения размещенного решения укажите утверждение с именем "`role`" в качестве утверждения роли:</span><span class="sxs-lookup"><span data-stu-id="dcbd5-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="dcbd5-152">На этом этапе можно применять подходы с авторизацией компонентов.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="dcbd5-153">Любой из механизмов авторизации в компонентах может использовать роль `admin` для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="dcbd5-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="dcbd5-154">[Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="dcbd5-154">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="dcbd5-155">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="dcbd5-155">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="dcbd5-156">[Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="dcbd5-156">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="dcbd5-157">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="dcbd5-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="dcbd5-158">Идентификаторы группы административных ролей AAD</span><span class="sxs-lookup"><span data-stu-id="dcbd5-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="dcbd5-159">Идентификаторы объектов, приведенные в следующей таблице, используются для создания [политик](xref:security/authorization/policies) для утверждений `group`.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="dcbd5-160">Политики позволяют приложению авторизовать пользователей для различных действий в приложении.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="dcbd5-161">Дополнительные сведения см. в разделе [Определяемые пользователем группы и встроенные административные роли AAD](#user-defined-groups-and-built-in-administrative-roles).</span><span class="sxs-lookup"><span data-stu-id="dcbd5-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="dcbd5-162">Административная роль AAD</span><span class="sxs-lookup"><span data-stu-id="dcbd5-162">AAD Administrative Role</span></span> | <span data-ttu-id="dcbd5-163">Идентификатор объекта.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-163">Object ID</span></span>
--- | ---
<span data-ttu-id="dcbd5-164">администратор приложений;</span><span class="sxs-lookup"><span data-stu-id="dcbd5-164">Application administrator</span></span> | <span data-ttu-id="dcbd5-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="dcbd5-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="dcbd5-166">Разработчик приложений</span><span class="sxs-lookup"><span data-stu-id="dcbd5-166">Application developer</span></span> | <span data-ttu-id="dcbd5-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="dcbd5-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="dcbd5-168">Администратор проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dcbd5-168">Authentication administrator</span></span> | <span data-ttu-id="dcbd5-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="dcbd5-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="dcbd5-170">Администратор Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="dcbd5-170">Azure DevOps administrator</span></span> | <span data-ttu-id="dcbd5-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="dcbd5-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="dcbd5-172">Администратор Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="dcbd5-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="dcbd5-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="dcbd5-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="dcbd5-174">Администратор наборов ключей IEF B2C</span><span class="sxs-lookup"><span data-stu-id="dcbd5-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="dcbd5-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="dcbd5-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="dcbd5-176">Администратор политик IEF B2C</span><span class="sxs-lookup"><span data-stu-id="dcbd5-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="dcbd5-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="dcbd5-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="dcbd5-178">Администратор потоков пользователей B2C</span><span class="sxs-lookup"><span data-stu-id="dcbd5-178">B2C user flow administrator</span></span> | <span data-ttu-id="dcbd5-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="dcbd5-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="dcbd5-180">Администратор атрибутов потоков пользователей B2C</span><span class="sxs-lookup"><span data-stu-id="dcbd5-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="dcbd5-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="dcbd5-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="dcbd5-182">Администратор выставления счетов</span><span class="sxs-lookup"><span data-stu-id="dcbd5-182">Billing administrator</span></span> | <span data-ttu-id="dcbd5-183">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="dcbd5-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="dcbd5-184">Администратор облачных приложений</span><span class="sxs-lookup"><span data-stu-id="dcbd5-184">Cloud application administrator</span></span> | <span data-ttu-id="dcbd5-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="dcbd5-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="dcbd5-186">Администратор облачного устройства</span><span class="sxs-lookup"><span data-stu-id="dcbd5-186">Cloud device administrator</span></span> | <span data-ttu-id="dcbd5-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="dcbd5-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="dcbd5-188">администратор соответствия требованиям.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-188">Compliance administrator</span></span> | <span data-ttu-id="dcbd5-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="dcbd5-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="dcbd5-190">Администратор данных соответствия</span><span class="sxs-lookup"><span data-stu-id="dcbd5-190">Compliance data administrator</span></span> | <span data-ttu-id="dcbd5-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="dcbd5-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="dcbd5-192">Администратор условного доступа</span><span class="sxs-lookup"><span data-stu-id="dcbd5-192">Conditional Access administrator</span></span> | <span data-ttu-id="dcbd5-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="dcbd5-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="dcbd5-194">лицо, утверждающее доступ клиентов к LockBox;</span><span class="sxs-lookup"><span data-stu-id="dcbd5-194">Customer LockBox access approver</span></span> | <span data-ttu-id="dcbd5-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="dcbd5-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="dcbd5-196">Администратор аналитики классических приложений</span><span class="sxs-lookup"><span data-stu-id="dcbd5-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="dcbd5-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="dcbd5-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="dcbd5-198">Читатели каталога</span><span class="sxs-lookup"><span data-stu-id="dcbd5-198">Directory readers</span></span> | <span data-ttu-id="dcbd5-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="dcbd5-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="dcbd5-200">Администратор Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="dcbd5-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="dcbd5-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="dcbd5-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="dcbd5-202">администратор Exchange;</span><span class="sxs-lookup"><span data-stu-id="dcbd5-202">Exchange administrator</span></span> | <span data-ttu-id="dcbd5-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="dcbd5-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="dcbd5-204">Администратор внешнего поставщика [Identity</span><span class="sxs-lookup"><span data-stu-id="dcbd5-204">External [Identity Provider administrator</span></span> | <span data-ttu-id="dcbd5-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="dcbd5-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="dcbd5-206">Глобальный администратор.</span><span class="sxs-lookup"><span data-stu-id="dcbd5-206">Global administrator</span></span> | <span data-ttu-id="dcbd5-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="dcbd5-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="dcbd5-208">Глобальный читатель</span><span class="sxs-lookup"><span data-stu-id="dcbd5-208">Global reader</span></span> | <span data-ttu-id="dcbd5-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="dcbd5-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="dcbd5-210">Администратор групп</span><span class="sxs-lookup"><span data-stu-id="dcbd5-210">Groups administrator</span></span> | <span data-ttu-id="dcbd5-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="dcbd5-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="dcbd5-212">Приглашающий гостей</span><span class="sxs-lookup"><span data-stu-id="dcbd5-212">Guest inviter</span></span> | <span data-ttu-id="dcbd5-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="dcbd5-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="dcbd5-214">администратор службы технической поддержки;</span><span class="sxs-lookup"><span data-stu-id="dcbd5-214">Helpdesk administrator</span></span> | <span data-ttu-id="dcbd5-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="dcbd5-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="dcbd5-216">Администратор Intune</span><span class="sxs-lookup"><span data-stu-id="dcbd5-216">Intune administrator</span></span> | <span data-ttu-id="dcbd5-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="dcbd5-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="dcbd5-218">Администратор Kaizala</span><span class="sxs-lookup"><span data-stu-id="dcbd5-218">Kaizala administrator</span></span> | <span data-ttu-id="dcbd5-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="dcbd5-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="dcbd5-220">Администратор лицензий</span><span class="sxs-lookup"><span data-stu-id="dcbd5-220">License administrator</span></span> | <span data-ttu-id="dcbd5-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="dcbd5-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="dcbd5-222">Читатель конфиденциальности данных Центра сообщений</span><span class="sxs-lookup"><span data-stu-id="dcbd5-222">Message center privacy reader</span></span> | <span data-ttu-id="dcbd5-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="dcbd5-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="dcbd5-224">Читатель центра сообщений</span><span class="sxs-lookup"><span data-stu-id="dcbd5-224">Message center reader</span></span> | <span data-ttu-id="dcbd5-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="dcbd5-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="dcbd5-226">Администратор приложений Office</span><span class="sxs-lookup"><span data-stu-id="dcbd5-226">Office apps administrator</span></span> | <span data-ttu-id="dcbd5-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="dcbd5-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="dcbd5-228">Администратор паролей</span><span class="sxs-lookup"><span data-stu-id="dcbd5-228">Password administrator</span></span> | <span data-ttu-id="dcbd5-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="dcbd5-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="dcbd5-230">Администратор Power BI</span><span class="sxs-lookup"><span data-stu-id="dcbd5-230">Power BI administrator</span></span> | <span data-ttu-id="dcbd5-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="dcbd5-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="dcbd5-232">Администратор Power Platform</span><span class="sxs-lookup"><span data-stu-id="dcbd5-232">Power platform administrator</span></span> | <span data-ttu-id="dcbd5-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="dcbd5-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="dcbd5-234">Привилегированный администратор проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dcbd5-234">Privileged authentication administrator</span></span> | <span data-ttu-id="dcbd5-235">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="dcbd5-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="dcbd5-236">администратор привилегированных ролей;</span><span class="sxs-lookup"><span data-stu-id="dcbd5-236">Privileged role administrator</span></span> | <span data-ttu-id="dcbd5-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="dcbd5-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="dcbd5-238">Читатель отчетов</span><span class="sxs-lookup"><span data-stu-id="dcbd5-238">Reports reader</span></span> | <span data-ttu-id="dcbd5-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="dcbd5-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="dcbd5-240">Администратор поиска</span><span class="sxs-lookup"><span data-stu-id="dcbd5-240">Search administrator</span></span> | <span data-ttu-id="dcbd5-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="dcbd5-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="dcbd5-242">Редактор поиска</span><span class="sxs-lookup"><span data-stu-id="dcbd5-242">Search editor</span></span> | <span data-ttu-id="dcbd5-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="dcbd5-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="dcbd5-244">администратор безопасности;</span><span class="sxs-lookup"><span data-stu-id="dcbd5-244">Security administrator</span></span> | <span data-ttu-id="dcbd5-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="dcbd5-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="dcbd5-246">Оператор безопасности</span><span class="sxs-lookup"><span data-stu-id="dcbd5-246">Security operator</span></span> | <span data-ttu-id="dcbd5-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="dcbd5-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="dcbd5-248">Читатель сведений о безопасности</span><span class="sxs-lookup"><span data-stu-id="dcbd5-248">Security reader</span></span> | <span data-ttu-id="dcbd5-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="dcbd5-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="dcbd5-250">администратор службы поддержки;</span><span class="sxs-lookup"><span data-stu-id="dcbd5-250">Service support administrator</span></span> | <span data-ttu-id="dcbd5-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="dcbd5-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="dcbd5-252">администратор SharePoint;</span><span class="sxs-lookup"><span data-stu-id="dcbd5-252">SharePoint administrator</span></span> | <span data-ttu-id="dcbd5-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="dcbd5-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="dcbd5-254">администратор Skype для бизнеса;</span><span class="sxs-lookup"><span data-stu-id="dcbd5-254">Skype for Business administrator</span></span> | <span data-ttu-id="dcbd5-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="dcbd5-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="dcbd5-256">Администратор связи Teams</span><span class="sxs-lookup"><span data-stu-id="dcbd5-256">Teams Communications Administrator</span></span> | <span data-ttu-id="dcbd5-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="dcbd5-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="dcbd5-258">Инженер службы поддержки связи Teams</span><span class="sxs-lookup"><span data-stu-id="dcbd5-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="dcbd5-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="dcbd5-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="dcbd5-260">Специалист службы поддержки связи Teams</span><span class="sxs-lookup"><span data-stu-id="dcbd5-260">Teams Communications Specialist</span></span> | <span data-ttu-id="dcbd5-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="dcbd5-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="dcbd5-262">Администратор службы Teams</span><span class="sxs-lookup"><span data-stu-id="dcbd5-262">Teams Service Administrator</span></span> | <span data-ttu-id="dcbd5-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="dcbd5-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="dcbd5-264">Администратор пользователей</span><span class="sxs-lookup"><span data-stu-id="dcbd5-264">User administrator</span></span> | <span data-ttu-id="dcbd5-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="dcbd5-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dcbd5-266">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="dcbd5-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
