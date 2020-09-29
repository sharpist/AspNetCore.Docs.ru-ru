---
title: Авторизация на основе политик в ASP.NET Core
author: rick-anderson
description: Узнайте, как создавать и использовать обработчики политик авторизации для применения требований к авторизации в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
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
uid: security/authorization/policies
ms.openlocfilehash: af282ec1f82b2ac31fd0b46b2406110e24e9211b
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424247"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="dd32a-103">Авторизация на основе политик в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd32a-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dd32a-104">В процессе [авторизации на основе ролей](xref:security/authorization/roles) и [авторизации на основе утверждений](xref:security/authorization/claims) используются требования, обработчик требований и предварительно настроенная политика.</span><span class="sxs-lookup"><span data-stu-id="dd32a-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="dd32a-105">Эти стандартные блоки поддерживают выражение оценки авторизации в коде.</span><span class="sxs-lookup"><span data-stu-id="dd32a-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="dd32a-106">Результатом является расширенная, повторно используемая, тестируемая структура авторизации.</span><span class="sxs-lookup"><span data-stu-id="dd32a-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="dd32a-107">Политика авторизации состоит из одного или нескольких требований.</span><span class="sxs-lookup"><span data-stu-id="dd32a-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="dd32a-108">Он регистрируется как часть конфигурации службы авторизации в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="dd32a-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="dd32a-109">В предыдущем примере создается политика "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="dd32a-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="dd32a-110">Он имеет одно требование &mdash; с минимальным возрастом, которое предоставляется в качестве параметра для требования.</span><span class="sxs-lookup"><span data-stu-id="dd32a-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="dd32a-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="dd32a-111">IAuthorizationService</span></span> 

<span data-ttu-id="dd32a-112">Основная служба, которая определяет, успешно ли выполнена авторизация <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="dd32a-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="dd32a-113">В приведенном выше коде показаны два метода [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="dd32a-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="dd32a-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> — это служба маркеров без методов, а также механизм отслеживания успешности авторизации.</span><span class="sxs-lookup"><span data-stu-id="dd32a-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="dd32a-115">Каждый из них <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> отвечает за проверку соблюдения требований:</span><span class="sxs-lookup"><span data-stu-id="dd32a-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="dd32a-116">Этот <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> класс используется обработчиком для обозначения того, выполнены ли требования.</span><span class="sxs-lookup"><span data-stu-id="dd32a-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="dd32a-117">В следующем коде показаны упрощенная реализация службы авторизации по умолчанию (и заметки с комментариями):</span><span class="sxs-lookup"><span data-stu-id="dd32a-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="dd32a-118">В следующем коде показана типичная `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dd32a-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="dd32a-119">Используйте <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> или `[Authorize(Policy = "Something")]` для авторизации.</span><span class="sxs-lookup"><span data-stu-id="dd32a-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="dd32a-120">Применение политик к контроллерам MVC</span><span class="sxs-lookup"><span data-stu-id="dd32a-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="dd32a-121">Если вы используете Razor страницы, см. раздел [применение политик к Razor страницам](#apply-policies-to-razor-pages) этого документа.</span><span class="sxs-lookup"><span data-stu-id="dd32a-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="dd32a-122">Политики применяются к контроллерам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="dd32a-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="dd32a-123">Пример:</span><span class="sxs-lookup"><span data-stu-id="dd32a-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="dd32a-124">Применение политик к Razor страницам</span><span class="sxs-lookup"><span data-stu-id="dd32a-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="dd32a-125">Политики применяются к Razor страницам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="dd32a-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="dd32a-126">Пример:</span><span class="sxs-lookup"><span data-stu-id="dd32a-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="dd32a-127">Политики ***не*** могут применяться на Razor уровне обработчика страницы, они должны быть применены к странице.</span><span class="sxs-lookup"><span data-stu-id="dd32a-127">Policies can ***not*** be applied at the Razor Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="dd32a-128">Политики можно применять к Razor страницам с помощью [соглашения об авторизации](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="dd32a-128">Policies can be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="dd32a-129">Требования</span><span class="sxs-lookup"><span data-stu-id="dd32a-129">Requirements</span></span>

<span data-ttu-id="dd32a-130">Требование авторизации — это коллекция параметров данных, которую политика может использовать для вычисления текущего субъекта-пользователя.</span><span class="sxs-lookup"><span data-stu-id="dd32a-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="dd32a-131">В нашей политике "AtLeast21" требование — это единственный параметр &mdash; с минимальным возрастом.</span><span class="sxs-lookup"><span data-stu-id="dd32a-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="dd32a-132">Требование реализует [иаусоризатионрекуиремент](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), который является пустым интерфейсом маркера.</span><span class="sxs-lookup"><span data-stu-id="dd32a-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="dd32a-133">Требование к параметризованному минимальному возрасту можно реализовать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="dd32a-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="dd32a-134">Если политика авторизации содержит несколько требований к авторизации, все требования должны пройти, чтобы оценка политики прошла успешно.</span><span class="sxs-lookup"><span data-stu-id="dd32a-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="dd32a-135">Иными словами, несколько требований к авторизации, добавленных в одну политику авторизации, обрабатываются **и** на основе.</span><span class="sxs-lookup"><span data-stu-id="dd32a-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="dd32a-136">Требование не обязательно должно иметь данные или свойства.</span><span class="sxs-lookup"><span data-stu-id="dd32a-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="dd32a-137">Обработчики авторизации</span><span class="sxs-lookup"><span data-stu-id="dd32a-137">Authorization handlers</span></span>

<span data-ttu-id="dd32a-138">Обработчик авторизации отвечает за оценку свойств требования.</span><span class="sxs-lookup"><span data-stu-id="dd32a-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="dd32a-139">Обработчик авторизации оценивает требования к предоставленному [аусоризатионхандлерконтекст](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) , чтобы определить, разрешен ли доступ.</span><span class="sxs-lookup"><span data-stu-id="dd32a-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="dd32a-140">Требование может иметь [несколько обработчиков](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="dd32a-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="dd32a-141">Обработчик может наследовать [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), где `TRequirement` является требованием для обработки.</span><span class="sxs-lookup"><span data-stu-id="dd32a-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="dd32a-142">Кроме того, обработчик может реализовать [иаусоризатионхандлер](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) для обработки более чем одного типа требований.</span><span class="sxs-lookup"><span data-stu-id="dd32a-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="dd32a-143">Использование обработчика для одного требования</span><span class="sxs-lookup"><span data-stu-id="dd32a-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="dd32a-144">Ниже приведен пример связи «один к одному», в которой обработчик минимального возраста использует одно требование:</span><span class="sxs-lookup"><span data-stu-id="dd32a-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="dd32a-145">Приведенный выше код определяет, имеет ли текущий участник пользователя дату утверждения о рождении, выданного известным и надежным издателем.</span><span class="sxs-lookup"><span data-stu-id="dd32a-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="dd32a-146">Авторизация не может произойти, если утверждение отсутствует, в этом случае возвращается завершенная задача.</span><span class="sxs-lookup"><span data-stu-id="dd32a-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="dd32a-147">При наличии утверждения возраст пользователя вычисляется.</span><span class="sxs-lookup"><span data-stu-id="dd32a-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="dd32a-148">Если пользователь соответствует минимальному возрасту, заданному требованием, авторизация считается успешной.</span><span class="sxs-lookup"><span data-stu-id="dd32a-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="dd32a-149">Когда авторизация прошла успешно, `context.Succeed` вызывается с удовлетворенным требованием в качестве единственного параметра.</span><span class="sxs-lookup"><span data-stu-id="dd32a-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="dd32a-150">Использование обработчика для нескольких требований</span><span class="sxs-lookup"><span data-stu-id="dd32a-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="dd32a-151">Ниже приведен пример связи «один ко многим», в которой обработчик разрешений может управлять тремя различными типами требований:</span><span class="sxs-lookup"><span data-stu-id="dd32a-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="dd32a-152">Предыдущий код проходит по [пендингрекуирементс](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; свойству, содержащему требования, не помеченные как неудачные.</span><span class="sxs-lookup"><span data-stu-id="dd32a-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="dd32a-153">Для `ReadPermission` получения требования пользователь должен быть владельцем или спонсором для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="dd32a-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="dd32a-154">В случае `EditPermission` `DeletePermission` требования или ему необходимо быть владельцем для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="dd32a-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="dd32a-155">Регистрация обработчика</span><span class="sxs-lookup"><span data-stu-id="dd32a-155">Handler registration</span></span>

<span data-ttu-id="dd32a-156">Обработчики регистрируются в коллекции служб во время настройки.</span><span class="sxs-lookup"><span data-stu-id="dd32a-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="dd32a-157">Пример:</span><span class="sxs-lookup"><span data-stu-id="dd32a-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="dd32a-158">Предыдущий код регистрируется `MinimumAgeHandler` как одноэлементный, вызывая `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="dd32a-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="dd32a-159">Обработчики можно регистрировать с помощью любого встроенного [времени существования службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="dd32a-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="dd32a-160">Что должен возвращать обработчик?</span><span class="sxs-lookup"><span data-stu-id="dd32a-160">What should a handler return?</span></span>

<span data-ttu-id="dd32a-161">Обратите внимание, что `Handle` метод в [примере обработчика](#security-authorization-handler-example) не возвращает значения.</span><span class="sxs-lookup"><span data-stu-id="dd32a-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="dd32a-162">Как отображается состояние «успешно» или «сбой»?</span><span class="sxs-lookup"><span data-stu-id="dd32a-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="dd32a-163">Обработчик указывает на успешное выполнение путем вызова `context.Succeed(IAuthorizationRequirement requirement)` , передавая требование, которое прошло проверку.</span><span class="sxs-lookup"><span data-stu-id="dd32a-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="dd32a-164">Обработчику обычно не требуется обработка ошибок, так как другие обработчики для такого же требования могут быть выполнены удачно.</span><span class="sxs-lookup"><span data-stu-id="dd32a-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="dd32a-165">Чтобы гарантировать сбой, даже если другие обработчики требований выполняются успешно, вызовите `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="dd32a-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="dd32a-166">Если обработчик вызывает `context.Succeed` или `context.Fail` , все остальные обработчики все еще вызываются.</span><span class="sxs-lookup"><span data-stu-id="dd32a-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="dd32a-167">Это позволяет создавать побочные эффекты, например ведение журнала, которое выполняется, даже если другой обработчик прошел проверку или не прошел требование.</span><span class="sxs-lookup"><span data-stu-id="dd32a-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="dd32a-168">Если задано значение `false` , свойство [инвокехандлерсафтерфаилуре](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) сокращенно вызывает выполнение обработчиков при `context.Fail` вызове метода.</span><span class="sxs-lookup"><span data-stu-id="dd32a-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="dd32a-169">`InvokeHandlersAfterFailure` по умолчанию имеет значение `true` , в этом случае вызываются все обработчики.</span><span class="sxs-lookup"><span data-stu-id="dd32a-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="dd32a-170">Обработчики авторизации вызываются даже в случае сбоя проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dd32a-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="dd32a-171">Зачем требуется несколько обработчиков для требования?</span><span class="sxs-lookup"><span data-stu-id="dd32a-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="dd32a-172">В случаях, когда требуется, чтобы оценка была в **или** на основе, реализуйте несколько обработчиков для одного требования.</span><span class="sxs-lookup"><span data-stu-id="dd32a-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="dd32a-173">Например, в корпорации Майкрософт есть дверцы, которые открываются только с карточками ключей.</span><span class="sxs-lookup"><span data-stu-id="dd32a-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="dd32a-174">Если вы оставите свой ключ дома, секретарь выводит временную наклейку и открывает дверцу.</span><span class="sxs-lookup"><span data-stu-id="dd32a-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="dd32a-175">В этом сценарии у вас будет одно требование, *буилдинжентри*, но несколько обработчиков, каждый из которых проанализировать одно требование.</span><span class="sxs-lookup"><span data-stu-id="dd32a-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="dd32a-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="dd32a-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="dd32a-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="dd32a-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="dd32a-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="dd32a-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="dd32a-179">Убедитесь, что оба обработчика [зарегистрированы](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="dd32a-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="dd32a-180">Если любой из обработчиков будет выполнен, когда политика оценивает `BuildingEntryRequirement` , то Вычисление политики будет завершено.</span><span class="sxs-lookup"><span data-stu-id="dd32a-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="dd32a-181">Использование Func для выполнения политики</span><span class="sxs-lookup"><span data-stu-id="dd32a-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="dd32a-182">Могут возникнуть ситуации, в которых выполнение политики может быть простым для выражения в коде.</span><span class="sxs-lookup"><span data-stu-id="dd32a-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="dd32a-183">`Func<AuthorizationHandlerContext, bool>`При настройке политики с помощью `RequireAssertion` построителя политик можно указать.</span><span class="sxs-lookup"><span data-stu-id="dd32a-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="dd32a-184">Например, Предыдущая возможность `BadgeEntryHandler` может быть переписана следующим образом:</span><span class="sxs-lookup"><span data-stu-id="dd32a-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="dd32a-185">Доступ к контексту запроса MVC в обработчиках</span><span class="sxs-lookup"><span data-stu-id="dd32a-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="dd32a-186">`HandleRequirementAsync`Метод, реализуемый в обработчике авторизации, имеет два параметра: и обрабатываемый объект `AuthorizationHandlerContext` `TRequirement` .</span><span class="sxs-lookup"><span data-stu-id="dd32a-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="dd32a-187">Платформы, такие как MVC или SignalR , могут добавлять любой объект в свойство в `Resource` `AuthorizationHandlerContext` для передачи дополнительной информации.</span><span class="sxs-lookup"><span data-stu-id="dd32a-187">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="dd32a-188">При использовании маршрутизации конечных точек авторизация обычно обрабатывается по промежуточного слоя авторизации.</span><span class="sxs-lookup"><span data-stu-id="dd32a-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="dd32a-189">В этом случае `Resource` свойство является экземпляром <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="dd32a-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="dd32a-190">Конечную точку можно использовать для проверки базового ресурса, с которым выполняется маршрутизация.</span><span class="sxs-lookup"><span data-stu-id="dd32a-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="dd32a-191">Пример:</span><span class="sxs-lookup"><span data-stu-id="dd32a-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="dd32a-192">Конечная точка не предоставляет доступ к текущей `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="dd32a-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="dd32a-193">При использовании маршрутизации конечных точек используйте `IHttpContextAcessor` для доступа `HttpContext` внутри обработчика авторизации.</span><span class="sxs-lookup"><span data-stu-id="dd32a-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="dd32a-194">Дополнительные сведения см. в разделе [Использование HttpContext из пользовательских компонентов](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="dd32a-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="dd32a-195">Если используется традиционная маршрутизация или когда Авторизация происходит как часть фильтра авторизации MVC, значение `Resource` является <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> экземпляром.</span><span class="sxs-lookup"><span data-stu-id="dd32a-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="dd32a-196">Это свойство предоставляет доступ к `HttpContext` , `RouteData` и всем остальным, предоставляемым MVC и Razor страницами.</span><span class="sxs-lookup"><span data-stu-id="dd32a-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="dd32a-197">Использование `Resource` свойства зависит от платформы.</span><span class="sxs-lookup"><span data-stu-id="dd32a-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="dd32a-198">Использование сведений в `Resource` свойстве ограничивает политики авторизации определенными платформами.</span><span class="sxs-lookup"><span data-stu-id="dd32a-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="dd32a-199">Необходимо привести `Resource` свойство с помощью `is` ключевого слова, а затем подтвердить успешное приведение, чтобы гарантировать, что код не будет завершаться с ошибкой `InvalidCastException` при выполнении в других платформах:</span><span class="sxs-lookup"><span data-stu-id="dd32a-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="dd32a-200">Глобально требовать проверку подлинности всех пользователей</span><span class="sxs-lookup"><span data-stu-id="dd32a-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dd32a-201">В процессе [авторизации на основе ролей](xref:security/authorization/roles) и [авторизации на основе утверждений](xref:security/authorization/claims) используются требования, обработчик требований и предварительно настроенная политика.</span><span class="sxs-lookup"><span data-stu-id="dd32a-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="dd32a-202">Эти стандартные блоки поддерживают выражение оценки авторизации в коде.</span><span class="sxs-lookup"><span data-stu-id="dd32a-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="dd32a-203">Результатом является расширенная, повторно используемая, тестируемая структура авторизации.</span><span class="sxs-lookup"><span data-stu-id="dd32a-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="dd32a-204">Политика авторизации состоит из одного или нескольких требований.</span><span class="sxs-lookup"><span data-stu-id="dd32a-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="dd32a-205">Он регистрируется как часть конфигурации службы авторизации в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="dd32a-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="dd32a-206">В предыдущем примере создается политика "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="dd32a-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="dd32a-207">Он имеет одно требование &mdash; с минимальным возрастом, которое предоставляется в качестве параметра для требования.</span><span class="sxs-lookup"><span data-stu-id="dd32a-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="dd32a-208">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="dd32a-208">IAuthorizationService</span></span> 

<span data-ttu-id="dd32a-209">Основная служба, которая определяет, успешно ли выполнена авторизация <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="dd32a-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="dd32a-210">В приведенном выше коде показаны два метода [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="dd32a-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="dd32a-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> — это служба маркеров без методов, а также механизм отслеживания успешности авторизации.</span><span class="sxs-lookup"><span data-stu-id="dd32a-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="dd32a-212">Каждый из них <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> отвечает за проверку соблюдения требований:</span><span class="sxs-lookup"><span data-stu-id="dd32a-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="dd32a-213">Этот <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> класс используется обработчиком для обозначения того, выполнены ли требования.</span><span class="sxs-lookup"><span data-stu-id="dd32a-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="dd32a-214">В следующем коде показаны упрощенная реализация службы авторизации по умолчанию (и заметки с комментариями):</span><span class="sxs-lookup"><span data-stu-id="dd32a-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="dd32a-215">В следующем коде показана типичная `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dd32a-215">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="dd32a-216">Используйте <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> или `[Authorize(Policy = "Something")]` для авторизации.</span><span class="sxs-lookup"><span data-stu-id="dd32a-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="dd32a-217">Применение политик к контроллерам MVC</span><span class="sxs-lookup"><span data-stu-id="dd32a-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="dd32a-218">Если вы используете Razor страницы, см. раздел [применение политик к Razor страницам](#apply-policies-to-razor-pages) этого документа.</span><span class="sxs-lookup"><span data-stu-id="dd32a-218">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="dd32a-219">Политики применяются к контроллерам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="dd32a-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="dd32a-220">Пример:</span><span class="sxs-lookup"><span data-stu-id="dd32a-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="dd32a-221">Применение политик к Razor страницам</span><span class="sxs-lookup"><span data-stu-id="dd32a-221">Apply policies to Razor Pages</span></span>

<span data-ttu-id="dd32a-222">Политики применяются к Razor страницам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="dd32a-222">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="dd32a-223">Пример:</span><span class="sxs-lookup"><span data-stu-id="dd32a-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="dd32a-224">Политики также можно применять к Razor страницам с помощью [соглашения об авторизации](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="dd32a-224">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="dd32a-225">Требования</span><span class="sxs-lookup"><span data-stu-id="dd32a-225">Requirements</span></span>

<span data-ttu-id="dd32a-226">Требование авторизации — это коллекция параметров данных, которую политика может использовать для вычисления текущего субъекта-пользователя.</span><span class="sxs-lookup"><span data-stu-id="dd32a-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="dd32a-227">В нашей политике "AtLeast21" требование — это единственный параметр &mdash; с минимальным возрастом.</span><span class="sxs-lookup"><span data-stu-id="dd32a-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="dd32a-228">Требование реализует [иаусоризатионрекуиремент](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), который является пустым интерфейсом маркера.</span><span class="sxs-lookup"><span data-stu-id="dd32a-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="dd32a-229">Требование к параметризованному минимальному возрасту можно реализовать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="dd32a-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="dd32a-230">Если политика авторизации содержит несколько требований к авторизации, все требования должны пройти, чтобы оценка политики прошла успешно.</span><span class="sxs-lookup"><span data-stu-id="dd32a-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="dd32a-231">Иными словами, несколько требований к авторизации, добавленных в одну политику авторизации, обрабатываются **и** на основе.</span><span class="sxs-lookup"><span data-stu-id="dd32a-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="dd32a-232">Требование не обязательно должно иметь данные или свойства.</span><span class="sxs-lookup"><span data-stu-id="dd32a-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="dd32a-233">Обработчики авторизации</span><span class="sxs-lookup"><span data-stu-id="dd32a-233">Authorization handlers</span></span>

<span data-ttu-id="dd32a-234">Обработчик авторизации отвечает за оценку свойств требования.</span><span class="sxs-lookup"><span data-stu-id="dd32a-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="dd32a-235">Обработчик авторизации оценивает требования к предоставленному [аусоризатионхандлерконтекст](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) , чтобы определить, разрешен ли доступ.</span><span class="sxs-lookup"><span data-stu-id="dd32a-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="dd32a-236">Требование может иметь [несколько обработчиков](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="dd32a-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="dd32a-237">Обработчик может наследовать [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), где `TRequirement` является требованием для обработки.</span><span class="sxs-lookup"><span data-stu-id="dd32a-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="dd32a-238">Кроме того, обработчик может реализовать [иаусоризатионхандлер](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) для обработки более чем одного типа требований.</span><span class="sxs-lookup"><span data-stu-id="dd32a-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="dd32a-239">Использование обработчика для одного требования</span><span class="sxs-lookup"><span data-stu-id="dd32a-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="dd32a-240">Ниже приведен пример связи «один к одному», в которой обработчик минимального возраста использует одно требование:</span><span class="sxs-lookup"><span data-stu-id="dd32a-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="dd32a-241">Приведенный выше код определяет, имеет ли текущий участник пользователя дату утверждения о рождении, выданного известным и надежным издателем.</span><span class="sxs-lookup"><span data-stu-id="dd32a-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="dd32a-242">Авторизация не может произойти, если утверждение отсутствует, в этом случае возвращается завершенная задача.</span><span class="sxs-lookup"><span data-stu-id="dd32a-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="dd32a-243">При наличии утверждения возраст пользователя вычисляется.</span><span class="sxs-lookup"><span data-stu-id="dd32a-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="dd32a-244">Если пользователь соответствует минимальному возрасту, заданному требованием, авторизация считается успешной.</span><span class="sxs-lookup"><span data-stu-id="dd32a-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="dd32a-245">Когда авторизация прошла успешно, `context.Succeed` вызывается с удовлетворенным требованием в качестве единственного параметра.</span><span class="sxs-lookup"><span data-stu-id="dd32a-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="dd32a-246">Использование обработчика для нескольких требований</span><span class="sxs-lookup"><span data-stu-id="dd32a-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="dd32a-247">Ниже приведен пример связи «один ко многим», в которой обработчик разрешений может управлять тремя различными типами требований:</span><span class="sxs-lookup"><span data-stu-id="dd32a-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="dd32a-248">Предыдущий код проходит по [пендингрекуирементс](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; свойству, содержащему требования, не помеченные как неудачные.</span><span class="sxs-lookup"><span data-stu-id="dd32a-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="dd32a-249">Для `ReadPermission` получения требования пользователь должен быть владельцем или спонсором для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="dd32a-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="dd32a-250">В случае `EditPermission` `DeletePermission` требования или ему необходимо быть владельцем для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="dd32a-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="dd32a-251">Регистрация обработчика</span><span class="sxs-lookup"><span data-stu-id="dd32a-251">Handler registration</span></span>

<span data-ttu-id="dd32a-252">Обработчики регистрируются в коллекции служб во время настройки.</span><span class="sxs-lookup"><span data-stu-id="dd32a-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="dd32a-253">Пример:</span><span class="sxs-lookup"><span data-stu-id="dd32a-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="dd32a-254">Предыдущий код регистрируется `MinimumAgeHandler` как одноэлементный, вызывая `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="dd32a-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="dd32a-255">Обработчики можно регистрировать с помощью любого встроенного [времени существования службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="dd32a-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="dd32a-256">Что должен возвращать обработчик?</span><span class="sxs-lookup"><span data-stu-id="dd32a-256">What should a handler return?</span></span>

<span data-ttu-id="dd32a-257">Обратите внимание, что `Handle` метод в [примере обработчика](#security-authorization-handler-example) не возвращает значения.</span><span class="sxs-lookup"><span data-stu-id="dd32a-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="dd32a-258">Как отображается состояние «успешно» или «сбой»?</span><span class="sxs-lookup"><span data-stu-id="dd32a-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="dd32a-259">Обработчик указывает на успешное выполнение путем вызова `context.Succeed(IAuthorizationRequirement requirement)` , передавая требование, которое прошло проверку.</span><span class="sxs-lookup"><span data-stu-id="dd32a-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="dd32a-260">Обработчику обычно не требуется обработка ошибок, так как другие обработчики для такого же требования могут быть выполнены удачно.</span><span class="sxs-lookup"><span data-stu-id="dd32a-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="dd32a-261">Чтобы гарантировать сбой, даже если другие обработчики требований выполняются успешно, вызовите `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="dd32a-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="dd32a-262">Если обработчик вызывает `context.Succeed` или `context.Fail` , все остальные обработчики все еще вызываются.</span><span class="sxs-lookup"><span data-stu-id="dd32a-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="dd32a-263">Это позволяет создавать побочные эффекты, например ведение журнала, которое выполняется, даже если другой обработчик прошел проверку или не прошел требование.</span><span class="sxs-lookup"><span data-stu-id="dd32a-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="dd32a-264">Если задано значение `false` , свойство [инвокехандлерсафтерфаилуре](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) сокращенно вызывает выполнение обработчиков при `context.Fail` вызове метода.</span><span class="sxs-lookup"><span data-stu-id="dd32a-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="dd32a-265">`InvokeHandlersAfterFailure` по умолчанию имеет значение `true` , в этом случае вызываются все обработчики.</span><span class="sxs-lookup"><span data-stu-id="dd32a-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="dd32a-266">Обработчики авторизации вызываются даже в случае сбоя проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dd32a-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="dd32a-267">Зачем требуется несколько обработчиков для требования?</span><span class="sxs-lookup"><span data-stu-id="dd32a-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="dd32a-268">В случаях, когда требуется, чтобы оценка была в **или** на основе, реализуйте несколько обработчиков для одного требования.</span><span class="sxs-lookup"><span data-stu-id="dd32a-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="dd32a-269">Например, в корпорации Майкрософт есть дверцы, которые открываются только с карточками ключей.</span><span class="sxs-lookup"><span data-stu-id="dd32a-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="dd32a-270">Если вы оставите свой ключ дома, секретарь выводит временную наклейку и открывает дверцу.</span><span class="sxs-lookup"><span data-stu-id="dd32a-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="dd32a-271">В этом сценарии у вас будет одно требование, *буилдинжентри*, но несколько обработчиков, каждый из которых проанализировать одно требование.</span><span class="sxs-lookup"><span data-stu-id="dd32a-271">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="dd32a-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="dd32a-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="dd32a-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="dd32a-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="dd32a-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="dd32a-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="dd32a-275">Убедитесь, что оба обработчика [зарегистрированы](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="dd32a-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="dd32a-276">Если любой из обработчиков будет выполнен, когда политика оценивает `BuildingEntryRequirement` , то Вычисление политики будет завершено.</span><span class="sxs-lookup"><span data-stu-id="dd32a-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="dd32a-277">Использование Func для выполнения политики</span><span class="sxs-lookup"><span data-stu-id="dd32a-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="dd32a-278">Могут возникнуть ситуации, в которых выполнение политики может быть простым для выражения в коде.</span><span class="sxs-lookup"><span data-stu-id="dd32a-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="dd32a-279">`Func<AuthorizationHandlerContext, bool>`При настройке политики с помощью `RequireAssertion` построителя политик можно указать.</span><span class="sxs-lookup"><span data-stu-id="dd32a-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="dd32a-280">Например, Предыдущая возможность `BadgeEntryHandler` может быть переписана следующим образом:</span><span class="sxs-lookup"><span data-stu-id="dd32a-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="dd32a-281">Доступ к контексту запроса MVC в обработчиках</span><span class="sxs-lookup"><span data-stu-id="dd32a-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="dd32a-282">`HandleRequirementAsync`Метод, реализуемый в обработчике авторизации, имеет два параметра: и обрабатываемый объект `AuthorizationHandlerContext` `TRequirement` .</span><span class="sxs-lookup"><span data-stu-id="dd32a-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="dd32a-283">Платформы, такие как MVC или SignalR , могут добавлять любой объект в свойство в `Resource` `AuthorizationHandlerContext` для передачи дополнительной информации.</span><span class="sxs-lookup"><span data-stu-id="dd32a-283">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="dd32a-284">Например, MVC передает экземпляр [аусоризатионфилтерконтекст](/dotnet/api/?term=AuthorizationFilterContext) в `Resource` свойство.</span><span class="sxs-lookup"><span data-stu-id="dd32a-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="dd32a-285">Это свойство предоставляет доступ к `HttpContext` , `RouteData` и всем остальным, предоставляемым MVC и Razor страницами.</span><span class="sxs-lookup"><span data-stu-id="dd32a-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="dd32a-286">Использование `Resource` свойства зависит от платформы.</span><span class="sxs-lookup"><span data-stu-id="dd32a-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="dd32a-287">Использование сведений в `Resource` свойстве ограничивает политики авторизации определенными платформами.</span><span class="sxs-lookup"><span data-stu-id="dd32a-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="dd32a-288">Необходимо привести `Resource` свойство с помощью `is` ключевого слова, а затем подтвердить успешное приведение, чтобы гарантировать, что код не будет завершаться с ошибкой `InvalidCastException` при выполнении в других платформах:</span><span class="sxs-lookup"><span data-stu-id="dd32a-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
