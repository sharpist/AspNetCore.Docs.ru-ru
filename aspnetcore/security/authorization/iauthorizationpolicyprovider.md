---
title: Поставщики политики пользовательских авторизов в ASP.NET Core
author: mjrousos
description: Узнайте, как использовать пользовательский IAuthorizationPolicyProvider в ASP.NET базовом приложении для динамического создания политик авторизации.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2a8b189cc9f17529a962a1f9642c7bb199d5781b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440926"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="9b05b-103">Поставщики политики пользовательских авторизаций, использующие IAuthorizationPolicyProvider в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b05b-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="9b05b-104">Автор: [Майк Роусос (Mike Rousos)](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="9b05b-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="9b05b-105">Обычно при использовании [авторизации на основе политики](xref:security/authorization/policies)политики регистрируются путем вызова `AuthorizationOptions.AddPolicy` как части конфигурации службы авторизации.</span><span class="sxs-lookup"><span data-stu-id="9b05b-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="9b05b-106">В некоторых сценариях может оказаться невозможным (или желательным) зарегистрировать все политики авторизации таким образом.</span><span class="sxs-lookup"><span data-stu-id="9b05b-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="9b05b-107">В этих случаях можно использовать `IAuthorizationPolicyProvider` обычай контролировать способы управления политиками авторизации.</span><span class="sxs-lookup"><span data-stu-id="9b05b-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="9b05b-108">Примеры сценариев, где пользовательский [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) может быть полезным, включают:</span><span class="sxs-lookup"><span data-stu-id="9b05b-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="9b05b-109">Использование внешней службы для оценки политики.</span><span class="sxs-lookup"><span data-stu-id="9b05b-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="9b05b-110">Использование широкого спектра политик (например, для различных номеров или возрастов), поэтому нет смысла `AuthorizationOptions.AddPolicy` добавлять каждую индивидуальную политику авторизации с вызовом.</span><span class="sxs-lookup"><span data-stu-id="9b05b-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="9b05b-111">Создание политик в режиме выполнения на основе информации во внешнем источнике данных (например, в базе данных) или динамические определения требований к авторизации с помощью другого механизма.</span><span class="sxs-lookup"><span data-stu-id="9b05b-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="9b05b-112">[Просмотр или загрузка примера кода](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) из [репозитория AspNetCore GitHub.](https://github.com/dotnet/AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="9b05b-112">[View or download sample code](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="9b05b-113">Загрузите файл репозивора dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="9b05b-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="9b05b-114">Отстегните файл.</span><span class="sxs-lookup"><span data-stu-id="9b05b-114">Unzip the file.</span></span> <span data-ttu-id="9b05b-115">Перейдите к папке проекта *src/Security/samples/CustomPolicyProvider.*</span><span class="sxs-lookup"><span data-stu-id="9b05b-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="9b05b-116">Настройка поиска политики</span><span class="sxs-lookup"><span data-stu-id="9b05b-116">Customize policy retrieval</span></span>

<span data-ttu-id="9b05b-117">ASP.NET основные приложения используют `IAuthorizationPolicyProvider` реализацию интерфейса для получения политикавторизации.</span><span class="sxs-lookup"><span data-stu-id="9b05b-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="9b05b-118">По умолчанию, [по умолчанию, По умолчанию, по умолчанию, по умолчанию,](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) зарегистрирован и используется.</span><span class="sxs-lookup"><span data-stu-id="9b05b-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="9b05b-119">`DefaultAuthorizationPolicyProvider`возвращает политики `AuthorizationOptions` из предоставленных в вызове. `IServiceCollection.AddAuthorization`</span><span class="sxs-lookup"><span data-stu-id="9b05b-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="9b05b-120">Настройте это поведение, `IAuthorizationPolicyProvider` зарегистрировав другую реализацию в контейнере [для впрыска зависимости](xref:fundamentals/dependency-injection) приложения.</span><span class="sxs-lookup"><span data-stu-id="9b05b-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="9b05b-121">Интерфейс `IAuthorizationPolicyProvider` содержит три AIS:</span><span class="sxs-lookup"><span data-stu-id="9b05b-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="9b05b-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) возвращает политику авторизации для данного имени.</span><span class="sxs-lookup"><span data-stu-id="9b05b-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="9b05b-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) возвращает политику авторизации по `[Authorize]` умолчанию (политика, используемая для атрибутов без указанной политики).</span><span class="sxs-lookup"><span data-stu-id="9b05b-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="9b05b-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) возвращает политику авторизации резервного копирования (политика, используемая программой авторизации Middleware, когда политика не указана).</span><span class="sxs-lookup"><span data-stu-id="9b05b-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="9b05b-125">Внедряя эти AAP, можно настроить способы предоставления политик авторизации.</span><span class="sxs-lookup"><span data-stu-id="9b05b-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="9b05b-126">Параметризованный авторизованный пример атрибута</span><span class="sxs-lookup"><span data-stu-id="9b05b-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="9b05b-127">Одним из `IAuthorizationPolicyProvider` сценариев, `[Authorize]` где это полезно, является включение пользовательских атрибутов, требования которых зависят от параметра.</span><span class="sxs-lookup"><span data-stu-id="9b05b-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="9b05b-128">Например, в [документации, основанной на разрешении](xref:security/authorization/policies) политики, в качестве выборки использовалась возрастная политика (AtLeast21).</span><span class="sxs-lookup"><span data-stu-id="9b05b-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="9b05b-129">Если различные действия контроллера в приложении должны быть доступны пользователям *разных* возрастов, было бы полезно иметь много различных возрастных политик.</span><span class="sxs-lookup"><span data-stu-id="9b05b-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="9b05b-130">Вместо регистрации всех различных возрастных политик, в `AuthorizationOptions`которые будет необходимо приложение, можно динамически генерировать политики с помощью пользовательского. `IAuthorizationPolicyProvider`</span><span class="sxs-lookup"><span data-stu-id="9b05b-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="9b05b-131">Чтобы упростить использование политик, можно аннотировать действия с `[MinimumAgeAuthorize(20)]`помощью пользовательского атрибута авторизации.</span><span class="sxs-lookup"><span data-stu-id="9b05b-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="9b05b-132">Атрибуты пользовательского авторизации</span><span class="sxs-lookup"><span data-stu-id="9b05b-132">Custom Authorization attributes</span></span>

<span data-ttu-id="9b05b-133">Политики авторизации определяются по их именам.</span><span class="sxs-lookup"><span data-stu-id="9b05b-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="9b05b-134">Описанный `MinimumAgeAuthorizeAttribute` ранее обычай должен составить аргументы в строку, которая может быть использована для получения соответствующей политики авторизации.</span><span class="sxs-lookup"><span data-stu-id="9b05b-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="9b05b-135">Вы можете сделать это, `AuthorizeAttribute` произвольно и сделать свойство `Age` обернуть собственности. `AuthorizeAttribute.Policy`</span><span class="sxs-lookup"><span data-stu-id="9b05b-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="9b05b-136">Этот тип атрибута имеет строку, основанную `Policy` `"MinimumAge"`на жесткой приставке () и рядом передается через конструктор.</span><span class="sxs-lookup"><span data-stu-id="9b05b-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="9b05b-137">Вы можете применить его к действиям `Authorize` так же, как и другие атрибуты, за исключением того, что он принимает целый ряд в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="9b05b-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="9b05b-138">Пользовательские IАвторизациюProvider</span><span class="sxs-lookup"><span data-stu-id="9b05b-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="9b05b-139">Обычай `MinimumAgeAuthorizeAttribute` упрощает запрос политикавторов для любого желаемого возраста.</span><span class="sxs-lookup"><span data-stu-id="9b05b-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="9b05b-140">Следующая проблема заключается в обеспечении доступности политик авторизации для всех этих возрастов.</span><span class="sxs-lookup"><span data-stu-id="9b05b-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="9b05b-141">Это где `IAuthorizationPolicyProvider` полезно.</span><span class="sxs-lookup"><span data-stu-id="9b05b-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="9b05b-142">При `MinimumAgeAuthorizationAttribute`использовании имен политики авторизации будут следовать шаблону, `"MinimumAge" + Age`поэтому пользовательский режим `IAuthorizationPolicyProvider` должен генерировать политики авторизации:</span><span class="sxs-lookup"><span data-stu-id="9b05b-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="9b05b-143">Разбор возраста от имени политики.</span><span class="sxs-lookup"><span data-stu-id="9b05b-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="9b05b-144">Использование `AuthorizationPolicyBuilder` для создания нового`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="9b05b-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="9b05b-145">В этом и следующих примерах предполагается, что пользователь проходит аутентификации с помощью файла cookie.</span><span class="sxs-lookup"><span data-stu-id="9b05b-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="9b05b-146">Конструкция `AuthorizationPolicyBuilder` должна быть построена с по крайней мере одним названием схемы авторизации или всегда успешно.</span><span class="sxs-lookup"><span data-stu-id="9b05b-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="9b05b-147">В противном случае нет информации о том, как предоставить вызов пользователю и исключение будет брошено.</span><span class="sxs-lookup"><span data-stu-id="9b05b-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="9b05b-148">Добавление требований к политике в `AuthorizationPolicyBuilder.AddRequirements`зависимости от возраста с .</span><span class="sxs-lookup"><span data-stu-id="9b05b-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="9b05b-149">В других сценариях `RequireClaim`можно `RequireRole`использовать `RequireUserName` , или вместо этого.</span><span class="sxs-lookup"><span data-stu-id="9b05b-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="9b05b-150">Несколько поставщиков политик авторизации</span><span class="sxs-lookup"><span data-stu-id="9b05b-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="9b05b-151">При использовании пользовательских `IAuthorizationPolicyProvider` реализаций имейте в виду, что ASP.NET Core использует только один `IAuthorizationPolicyProvider`экземпляр.</span><span class="sxs-lookup"><span data-stu-id="9b05b-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="9b05b-152">Если пользовательская поставщик не в состоянии предоставить политики авторизации для всех имен политик, которые будут использоваться, он должен отложить на резервного поставщика.</span><span class="sxs-lookup"><span data-stu-id="9b05b-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="9b05b-153">Например, рассмотрим приложение, которое нуждается как в пользовательских возрастных политиках, так и в более традиционных ролевых политиках поиска.</span><span class="sxs-lookup"><span data-stu-id="9b05b-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="9b05b-154">Такое приложение может использовать поставщика пользовательских политик авторизации, который:</span><span class="sxs-lookup"><span data-stu-id="9b05b-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="9b05b-155">Попытки разобрать имена политик.</span><span class="sxs-lookup"><span data-stu-id="9b05b-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="9b05b-156">Вызовы в другой поставщик `DefaultAuthorizationPolicyProvider`политик (например), если имя политики не содержит возраста.</span><span class="sxs-lookup"><span data-stu-id="9b05b-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="9b05b-157">Приведенная `IAuthorizationPolicyProvider` выше реализация примера может `DefaultAuthorizationPolicyProvider` быть обновлена для использования поставщика политики резервного копирования в своем конструкторе (для использования в случае, если имя политики не соответствует ожидаемому шаблону 'MinimumAge' и возраст).</span><span class="sxs-lookup"><span data-stu-id="9b05b-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="9b05b-158">Затем `GetPolicyAsync` метод можно обновить, чтобы `BackupPolicyProvider` использовать вместо возврата нулевых:</span><span class="sxs-lookup"><span data-stu-id="9b05b-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="9b05b-159">Политика по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9b05b-159">Default policy</span></span>

<span data-ttu-id="9b05b-160">Помимо предоставления именных политик `IAuthorizationPolicyProvider` авторизации, `GetDefaultPolicyAsync` пользовательский способ `[Authorize]` должен реализовать для предоставления политики авторизации для атрибутов без указанного имени политики.</span><span class="sxs-lookup"><span data-stu-id="9b05b-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="9b05b-161">Во многих случаях этот атрибут авторизации требует только аутентифицированного пользователя, `RequireAuthenticatedUser`поэтому вы можете сделать необходимую политику с вызовом:</span><span class="sxs-lookup"><span data-stu-id="9b05b-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="9b05b-162">Как и все аспекты обычая, `IAuthorizationPolicyProvider`вы можете настроить это, по мере необходимости.</span><span class="sxs-lookup"><span data-stu-id="9b05b-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="9b05b-163">В некоторых случаях может быть желательно получить политику по `IAuthorizationPolicyProvider`умолчанию из запасного варианта.</span><span class="sxs-lookup"><span data-stu-id="9b05b-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="9b05b-164">Политика возврата</span><span class="sxs-lookup"><span data-stu-id="9b05b-164">Fallback policy</span></span>

<span data-ttu-id="9b05b-165">Пользовательский `IAuthorizationPolicyProvider` обычай `GetFallbackPolicyAsync` может дополнительно реализовать для предоставления политики, используемой при [объединении политик](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) и когда не указаны политики.</span><span class="sxs-lookup"><span data-stu-id="9b05b-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="9b05b-166">При `GetFallbackPolicyAsync` возврате политики без нулевых возврат используется системой авторизации, когда для запроса не указаны политики.</span><span class="sxs-lookup"><span data-stu-id="9b05b-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="9b05b-167">Если не требуется политика резервного `null` копирования, поставщик может вернуться или отложить резервному поставщику:</span><span class="sxs-lookup"><span data-stu-id="9b05b-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="9b05b-168">Используйте пользовательский IАвторитиадтиВИ-провайдер</span><span class="sxs-lookup"><span data-stu-id="9b05b-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="9b05b-169">Для использования пользовательских `IAuthorizationPolicyProvider`политик из, вы должны:</span><span class="sxs-lookup"><span data-stu-id="9b05b-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="9b05b-170">Зарегистрируйте `AuthorizationHandler` соответствующие типы с инъекцией зависимостей (описанными в [авторизации на основе политики),](xref:security/authorization/policies#authorization-handlers)как и во всех сценариях авторизации, основанных на политике.</span><span class="sxs-lookup"><span data-stu-id="9b05b-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="9b05b-171">Заменить `IAuthorizationPolicyProvider` поставщика политики по умолчанию, зарегистрируйте пользовательский тип в коллекции служб инъекций зависимостей приложения.ru. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="9b05b-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="9b05b-172">Полный `IAuthorizationPolicyProvider` пользовательский образец доступен в [репозитории dotnet/aspnetcore GitHub.](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider)</span><span class="sxs-lookup"><span data-stu-id="9b05b-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).</span></span>
