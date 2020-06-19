---
title: Авторизация на основе политик в ASP.NET Core
author: rick-anderson
description: Узнайте, как создавать и использовать обработчики политик авторизации для применения требований к авторизации в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 533bddc9c4499dad99cfdb3089045ea10aed4548
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074159"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="191a2-103">Авторизация на основе политик в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="191a2-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="191a2-104">В процессе [авторизации на основе ролей](xref:security/authorization/roles) и [авторизации на основе утверждений](xref:security/authorization/claims) используются требования, обработчик требований и предварительно настроенная политика.</span><span class="sxs-lookup"><span data-stu-id="191a2-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="191a2-105">Эти стандартные блоки поддерживают выражение оценки авторизации в коде.</span><span class="sxs-lookup"><span data-stu-id="191a2-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="191a2-106">Результатом является расширенная, повторно используемая, тестируемая структура авторизации.</span><span class="sxs-lookup"><span data-stu-id="191a2-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="191a2-107">Политика авторизации состоит из одного или нескольких требований.</span><span class="sxs-lookup"><span data-stu-id="191a2-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="191a2-108">Он регистрируется как часть конфигурации службы авторизации в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="191a2-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="191a2-109">В предыдущем примере создается политика "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="191a2-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="191a2-110">Он имеет одно требование &mdash; с минимальным возрастом, которое предоставляется в качестве параметра для требования.</span><span class="sxs-lookup"><span data-stu-id="191a2-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="191a2-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="191a2-111">IAuthorizationService</span></span> 

<span data-ttu-id="191a2-112">Основная служба, которая определяет, успешно ли выполнена авторизация <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="191a2-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="191a2-113">В приведенном выше коде показаны два метода [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="191a2-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="191a2-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>— это служба маркеров без методов, а также механизм отслеживания успешности авторизации.</span><span class="sxs-lookup"><span data-stu-id="191a2-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="191a2-115">Каждый из них <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> отвечает за проверку соблюдения требований:</span><span class="sxs-lookup"><span data-stu-id="191a2-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="191a2-116">Этот <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> класс используется обработчиком для обозначения того, выполнены ли требования.</span><span class="sxs-lookup"><span data-stu-id="191a2-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="191a2-117">В следующем коде показаны упрощенная реализация службы авторизации по умолчанию (и заметки с комментариями):</span><span class="sxs-lookup"><span data-stu-id="191a2-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="191a2-118">В следующем коде показана типичная `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="191a2-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="191a2-119">Используйте <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> или `[Authorize(Policy = "Something")]` для авторизации.</span><span class="sxs-lookup"><span data-stu-id="191a2-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="191a2-120">Применение политик к контроллерам MVC</span><span class="sxs-lookup"><span data-stu-id="191a2-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="191a2-121">Если вы используете Razor страницы, см. раздел [применение политик к Razor страницам](#apply-policies-to-razor-pages) этого документа.</span><span class="sxs-lookup"><span data-stu-id="191a2-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="191a2-122">Политики применяются к контроллерам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="191a2-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="191a2-123">Пример:</span><span class="sxs-lookup"><span data-stu-id="191a2-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="191a2-124">Применение политик к Razor страницам</span><span class="sxs-lookup"><span data-stu-id="191a2-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="191a2-125">Политики применяются к Razor страницам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="191a2-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="191a2-126">Пример:</span><span class="sxs-lookup"><span data-stu-id="191a2-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="191a2-127">Политики также можно применять к Razor страницам с помощью [соглашения об авторизации](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="191a2-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="191a2-128">Требования</span><span class="sxs-lookup"><span data-stu-id="191a2-128">Requirements</span></span>

<span data-ttu-id="191a2-129">Требование авторизации — это коллекция параметров данных, которую политика может использовать для вычисления текущего субъекта-пользователя.</span><span class="sxs-lookup"><span data-stu-id="191a2-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="191a2-130">В нашей политике "AtLeast21" требование — это единственный параметр &mdash; с минимальным возрастом.</span><span class="sxs-lookup"><span data-stu-id="191a2-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="191a2-131">Требование реализует [иаусоризатионрекуиремент](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), который является пустым интерфейсом маркера.</span><span class="sxs-lookup"><span data-stu-id="191a2-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="191a2-132">Требование к параметризованному минимальному возрасту можно реализовать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="191a2-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="191a2-133">Если политика авторизации содержит несколько требований к авторизации, все требования должны пройти, чтобы оценка политики прошла успешно.</span><span class="sxs-lookup"><span data-stu-id="191a2-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="191a2-134">Иными словами, несколько требований к авторизации, добавленных в одну политику авторизации, обрабатываются **и** на основе.</span><span class="sxs-lookup"><span data-stu-id="191a2-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="191a2-135">Требование не обязательно должно иметь данные или свойства.</span><span class="sxs-lookup"><span data-stu-id="191a2-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="191a2-136">Обработчики авторизации</span><span class="sxs-lookup"><span data-stu-id="191a2-136">Authorization handlers</span></span>

<span data-ttu-id="191a2-137">Обработчик авторизации отвечает за оценку свойств требования.</span><span class="sxs-lookup"><span data-stu-id="191a2-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="191a2-138">Обработчик авторизации оценивает требования к предоставленному [аусоризатионхандлерконтекст](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) , чтобы определить, разрешен ли доступ.</span><span class="sxs-lookup"><span data-stu-id="191a2-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="191a2-139">Требование может иметь [несколько обработчиков](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="191a2-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="191a2-140">Обработчик может наследовать [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), где `TRequirement` является требованием для обработки.</span><span class="sxs-lookup"><span data-stu-id="191a2-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="191a2-141">Кроме того, обработчик может реализовать [иаусоризатионхандлер](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) для обработки более чем одного типа требований.</span><span class="sxs-lookup"><span data-stu-id="191a2-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="191a2-142">Использование обработчика для одного требования</span><span class="sxs-lookup"><span data-stu-id="191a2-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="191a2-143">Ниже приведен пример связи «один к одному», в которой обработчик минимального возраста использует одно требование:</span><span class="sxs-lookup"><span data-stu-id="191a2-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="191a2-144">Приведенный выше код определяет, имеет ли текущий участник пользователя дату утверждения о рождении, выданного известным и надежным издателем.</span><span class="sxs-lookup"><span data-stu-id="191a2-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="191a2-145">Авторизация не может произойти, если утверждение отсутствует, в этом случае возвращается завершенная задача.</span><span class="sxs-lookup"><span data-stu-id="191a2-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="191a2-146">При наличии утверждения возраст пользователя вычисляется.</span><span class="sxs-lookup"><span data-stu-id="191a2-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="191a2-147">Если пользователь соответствует минимальному возрасту, заданному требованием, авторизация считается успешной.</span><span class="sxs-lookup"><span data-stu-id="191a2-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="191a2-148">Когда авторизация прошла успешно, `context.Succeed` вызывается с удовлетворенным требованием в качестве единственного параметра.</span><span class="sxs-lookup"><span data-stu-id="191a2-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="191a2-149">Использование обработчика для нескольких требований</span><span class="sxs-lookup"><span data-stu-id="191a2-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="191a2-150">Ниже приведен пример связи «один ко многим», в которой обработчик разрешений может управлять тремя различными типами требований:</span><span class="sxs-lookup"><span data-stu-id="191a2-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="191a2-151">Предыдущий код проходит по [пендингрекуирементс](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; свойству, содержащему требования, не помеченные как неудачные.</span><span class="sxs-lookup"><span data-stu-id="191a2-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="191a2-152">Для `ReadPermission` получения требования пользователь должен быть владельцем или спонсором для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="191a2-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="191a2-153">В случае `EditPermission` `DeletePermission` требования или ему необходимо быть владельцем для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="191a2-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="191a2-154">Регистрация обработчика</span><span class="sxs-lookup"><span data-stu-id="191a2-154">Handler registration</span></span>

<span data-ttu-id="191a2-155">Обработчики регистрируются в коллекции служб во время настройки.</span><span class="sxs-lookup"><span data-stu-id="191a2-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="191a2-156">Пример:</span><span class="sxs-lookup"><span data-stu-id="191a2-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="191a2-157">Предыдущий код регистрируется `MinimumAgeHandler` как одноэлементный, вызывая `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="191a2-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="191a2-158">Обработчики можно регистрировать с помощью любого встроенного [времени существования службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="191a2-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="191a2-159">Что должен возвращать обработчик?</span><span class="sxs-lookup"><span data-stu-id="191a2-159">What should a handler return?</span></span>

<span data-ttu-id="191a2-160">Обратите внимание, что `Handle` метод в [примере обработчика](#security-authorization-handler-example) не возвращает значения.</span><span class="sxs-lookup"><span data-stu-id="191a2-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="191a2-161">Как отображается состояние «успешно» или «сбой»?</span><span class="sxs-lookup"><span data-stu-id="191a2-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="191a2-162">Обработчик указывает на успешное выполнение путем вызова `context.Succeed(IAuthorizationRequirement requirement)` , передавая требование, которое прошло проверку.</span><span class="sxs-lookup"><span data-stu-id="191a2-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="191a2-163">Обработчику обычно не требуется обработка ошибок, так как другие обработчики для такого же требования могут быть выполнены удачно.</span><span class="sxs-lookup"><span data-stu-id="191a2-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="191a2-164">Чтобы гарантировать сбой, даже если другие обработчики требований выполняются успешно, вызовите `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="191a2-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="191a2-165">Если обработчик вызывает `context.Succeed` или `context.Fail` , все остальные обработчики все еще вызываются.</span><span class="sxs-lookup"><span data-stu-id="191a2-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="191a2-166">Это позволяет создавать побочные эффекты, например ведение журнала, которое выполняется, даже если другой обработчик прошел проверку или не прошел требование.</span><span class="sxs-lookup"><span data-stu-id="191a2-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="191a2-167">Если задано значение `false` , свойство [инвокехандлерсафтерфаилуре](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (доступно в ASP.NET Core 1,1 и более поздних версиях) сокращено при `context.Fail` вызове обработчиков.</span><span class="sxs-lookup"><span data-stu-id="191a2-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="191a2-168">`InvokeHandlersAfterFailure`по умолчанию имеет значение `true` , в этом случае вызываются все обработчики.</span><span class="sxs-lookup"><span data-stu-id="191a2-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="191a2-169">Обработчики авторизации вызываются даже в случае сбоя проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="191a2-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="191a2-170">Зачем требуется несколько обработчиков для требования?</span><span class="sxs-lookup"><span data-stu-id="191a2-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="191a2-171">В случаях, когда требуется, чтобы оценка была в **или** на основе, реализуйте несколько обработчиков для одного требования.</span><span class="sxs-lookup"><span data-stu-id="191a2-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="191a2-172">Например, в корпорации Майкрософт есть дверцы, которые открываются только с карточками ключей.</span><span class="sxs-lookup"><span data-stu-id="191a2-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="191a2-173">Если вы оставите свой ключ дома, секретарь выводит временную наклейку и открывает дверцу.</span><span class="sxs-lookup"><span data-stu-id="191a2-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="191a2-174">В этом сценарии у вас будет одно требование, *буилдинжентри*, но несколько обработчиков, каждый из которых проанализировать одно требование.</span><span class="sxs-lookup"><span data-stu-id="191a2-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="191a2-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="191a2-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="191a2-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="191a2-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="191a2-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="191a2-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="191a2-178">Убедитесь, что оба обработчика [зарегистрированы](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="191a2-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="191a2-179">Если любой из обработчиков будет выполнен, когда политика оценивает `BuildingEntryRequirement` , то Вычисление политики будет завершено.</span><span class="sxs-lookup"><span data-stu-id="191a2-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="191a2-180">Использование Func для выполнения политики</span><span class="sxs-lookup"><span data-stu-id="191a2-180">Use a func to fulfill a policy</span></span>

<span data-ttu-id="191a2-181">Могут возникнуть ситуации, в которых выполнение политики может быть простым для выражения в коде.</span><span class="sxs-lookup"><span data-stu-id="191a2-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="191a2-182">`Func<AuthorizationHandlerContext, bool>`При настройке политики с помощью `RequireAssertion` построителя политик можно указать.</span><span class="sxs-lookup"><span data-stu-id="191a2-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="191a2-183">Например, Предыдущая возможность `BadgeEntryHandler` может быть переписана следующим образом:</span><span class="sxs-lookup"><span data-stu-id="191a2-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="191a2-184">Доступ к контексту запроса MVC в обработчиках</span><span class="sxs-lookup"><span data-stu-id="191a2-184">Access MVC request context in handlers</span></span>

<span data-ttu-id="191a2-185">`HandleRequirementAsync`Метод, реализуемый в обработчике авторизации, имеет два параметра: и обрабатываемый объект `AuthorizationHandlerContext` `TRequirement` .</span><span class="sxs-lookup"><span data-stu-id="191a2-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="191a2-186">Платформы, такие как MVC или SignalR , могут добавлять любой объект в свойство в `Resource` `AuthorizationHandlerContext` для передачи дополнительной информации.</span><span class="sxs-lookup"><span data-stu-id="191a2-186">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="191a2-187">При использовании маршрутизации конечных точек авторизация обычно обрабатывается по промежуточного слоя авторизации.</span><span class="sxs-lookup"><span data-stu-id="191a2-187">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="191a2-188">В этом случае `Resource` свойство является экземпляром <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="191a2-188">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="191a2-189">Конечную точку можно использовать для проверки базового ресурса, с которым выполняется маршрутизация.</span><span class="sxs-lookup"><span data-stu-id="191a2-189">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="191a2-190">Пример:</span><span class="sxs-lookup"><span data-stu-id="191a2-190">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="191a2-191">Конечная точка не предоставляет доступ к текущей `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="191a2-191">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="191a2-192">При использовании маршрутизации конечных точек используйте `IHttpContextAcessor` для доступа `HttpContext` внутри обработчика авторизации.</span><span class="sxs-lookup"><span data-stu-id="191a2-192">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="191a2-193">Дополнительные сведения см. в разделе [Использование HttpContext из пользовательских компонентов](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="191a2-193">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="191a2-194">Если используется традиционная маршрутизация или когда Авторизация происходит как часть фильтра авторизации MVC, значение `Resource` является <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> экземпляром.</span><span class="sxs-lookup"><span data-stu-id="191a2-194">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="191a2-195">Это свойство предоставляет доступ к `HttpContext` , `RouteData` и всем остальным, предоставляемым MVC и Razor страницами.</span><span class="sxs-lookup"><span data-stu-id="191a2-195">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="191a2-196">Использование `Resource` свойства зависит от платформы.</span><span class="sxs-lookup"><span data-stu-id="191a2-196">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="191a2-197">Использование сведений в `Resource` свойстве ограничивает политики авторизации определенными платформами.</span><span class="sxs-lookup"><span data-stu-id="191a2-197">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="191a2-198">Необходимо привести `Resource` свойство с помощью `is` ключевого слова, а затем подтвердить успешное приведение, чтобы гарантировать, что код не будет завершаться с ошибкой `InvalidCastException` при выполнении в других платформах:</span><span class="sxs-lookup"><span data-stu-id="191a2-198">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="191a2-199">В процессе [авторизации на основе ролей](xref:security/authorization/roles) и [авторизации на основе утверждений](xref:security/authorization/claims) используются требования, обработчик требований и предварительно настроенная политика.</span><span class="sxs-lookup"><span data-stu-id="191a2-199">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="191a2-200">Эти стандартные блоки поддерживают выражение оценки авторизации в коде.</span><span class="sxs-lookup"><span data-stu-id="191a2-200">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="191a2-201">Результатом является расширенная, повторно используемая, тестируемая структура авторизации.</span><span class="sxs-lookup"><span data-stu-id="191a2-201">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="191a2-202">Политика авторизации состоит из одного или нескольких требований.</span><span class="sxs-lookup"><span data-stu-id="191a2-202">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="191a2-203">Он регистрируется как часть конфигурации службы авторизации в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="191a2-203">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="191a2-204">В предыдущем примере создается политика "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="191a2-204">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="191a2-205">Он имеет одно требование &mdash; с минимальным возрастом, которое предоставляется в качестве параметра для требования.</span><span class="sxs-lookup"><span data-stu-id="191a2-205">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="191a2-206">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="191a2-206">IAuthorizationService</span></span> 

<span data-ttu-id="191a2-207">Основная служба, которая определяет, успешно ли выполнена авторизация <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="191a2-207">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="191a2-208">В приведенном выше коде показаны два метода [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="191a2-208">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="191a2-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>— это служба маркеров без методов, а также механизм отслеживания успешности авторизации.</span><span class="sxs-lookup"><span data-stu-id="191a2-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="191a2-210">Каждый из них <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> отвечает за проверку соблюдения требований:</span><span class="sxs-lookup"><span data-stu-id="191a2-210">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="191a2-211">Этот <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> класс используется обработчиком для обозначения того, выполнены ли требования.</span><span class="sxs-lookup"><span data-stu-id="191a2-211">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="191a2-212">В следующем коде показаны упрощенная реализация службы авторизации по умолчанию (и заметки с комментариями):</span><span class="sxs-lookup"><span data-stu-id="191a2-212">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="191a2-213">В следующем коде показана типичная `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="191a2-213">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="191a2-214">Используйте <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> или `[Authorize(Policy = "Something")]` для авторизации.</span><span class="sxs-lookup"><span data-stu-id="191a2-214">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="191a2-215">Применение политик к контроллерам MVC</span><span class="sxs-lookup"><span data-stu-id="191a2-215">Apply policies to MVC controllers</span></span>

<span data-ttu-id="191a2-216">Если вы используете Razor страницы, см. раздел [применение политик к Razor страницам](#apply-policies-to-razor-pages) этого документа.</span><span class="sxs-lookup"><span data-stu-id="191a2-216">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="191a2-217">Политики применяются к контроллерам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="191a2-217">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="191a2-218">Пример:</span><span class="sxs-lookup"><span data-stu-id="191a2-218">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="191a2-219">Применение политик к Razor страницам</span><span class="sxs-lookup"><span data-stu-id="191a2-219">Apply policies to Razor Pages</span></span>

<span data-ttu-id="191a2-220">Политики применяются к Razor страницам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="191a2-220">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="191a2-221">Пример:</span><span class="sxs-lookup"><span data-stu-id="191a2-221">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="191a2-222">Политики также можно применять к Razor страницам с помощью [соглашения об авторизации](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="191a2-222">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="191a2-223">Требования</span><span class="sxs-lookup"><span data-stu-id="191a2-223">Requirements</span></span>

<span data-ttu-id="191a2-224">Требование авторизации — это коллекция параметров данных, которую политика может использовать для вычисления текущего субъекта-пользователя.</span><span class="sxs-lookup"><span data-stu-id="191a2-224">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="191a2-225">В нашей политике "AtLeast21" требование — это единственный параметр &mdash; с минимальным возрастом.</span><span class="sxs-lookup"><span data-stu-id="191a2-225">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="191a2-226">Требование реализует [иаусоризатионрекуиремент](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), который является пустым интерфейсом маркера.</span><span class="sxs-lookup"><span data-stu-id="191a2-226">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="191a2-227">Требование к параметризованному минимальному возрасту можно реализовать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="191a2-227">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="191a2-228">Если политика авторизации содержит несколько требований к авторизации, все требования должны пройти, чтобы оценка политики прошла успешно.</span><span class="sxs-lookup"><span data-stu-id="191a2-228">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="191a2-229">Иными словами, несколько требований к авторизации, добавленных в одну политику авторизации, обрабатываются **и** на основе.</span><span class="sxs-lookup"><span data-stu-id="191a2-229">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="191a2-230">Требование не обязательно должно иметь данные или свойства.</span><span class="sxs-lookup"><span data-stu-id="191a2-230">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="191a2-231">Обработчики авторизации</span><span class="sxs-lookup"><span data-stu-id="191a2-231">Authorization handlers</span></span>

<span data-ttu-id="191a2-232">Обработчик авторизации отвечает за оценку свойств требования.</span><span class="sxs-lookup"><span data-stu-id="191a2-232">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="191a2-233">Обработчик авторизации оценивает требования к предоставленному [аусоризатионхандлерконтекст](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) , чтобы определить, разрешен ли доступ.</span><span class="sxs-lookup"><span data-stu-id="191a2-233">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="191a2-234">Требование может иметь [несколько обработчиков](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="191a2-234">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="191a2-235">Обработчик может наследовать [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), где `TRequirement` является требованием для обработки.</span><span class="sxs-lookup"><span data-stu-id="191a2-235">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="191a2-236">Кроме того, обработчик может реализовать [иаусоризатионхандлер](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) для обработки более чем одного типа требований.</span><span class="sxs-lookup"><span data-stu-id="191a2-236">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="191a2-237">Использование обработчика для одного требования</span><span class="sxs-lookup"><span data-stu-id="191a2-237">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="191a2-238">Ниже приведен пример связи «один к одному», в которой обработчик минимального возраста использует одно требование:</span><span class="sxs-lookup"><span data-stu-id="191a2-238">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="191a2-239">Приведенный выше код определяет, имеет ли текущий участник пользователя дату утверждения о рождении, выданного известным и надежным издателем.</span><span class="sxs-lookup"><span data-stu-id="191a2-239">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="191a2-240">Авторизация не может произойти, если утверждение отсутствует, в этом случае возвращается завершенная задача.</span><span class="sxs-lookup"><span data-stu-id="191a2-240">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="191a2-241">При наличии утверждения возраст пользователя вычисляется.</span><span class="sxs-lookup"><span data-stu-id="191a2-241">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="191a2-242">Если пользователь соответствует минимальному возрасту, заданному требованием, авторизация считается успешной.</span><span class="sxs-lookup"><span data-stu-id="191a2-242">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="191a2-243">Когда авторизация прошла успешно, `context.Succeed` вызывается с удовлетворенным требованием в качестве единственного параметра.</span><span class="sxs-lookup"><span data-stu-id="191a2-243">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="191a2-244">Использование обработчика для нескольких требований</span><span class="sxs-lookup"><span data-stu-id="191a2-244">Use a handler for multiple requirements</span></span>

<span data-ttu-id="191a2-245">Ниже приведен пример связи «один ко многим», в которой обработчик разрешений может управлять тремя различными типами требований:</span><span class="sxs-lookup"><span data-stu-id="191a2-245">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="191a2-246">Предыдущий код проходит по [пендингрекуирементс](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; свойству, содержащему требования, не помеченные как неудачные.</span><span class="sxs-lookup"><span data-stu-id="191a2-246">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="191a2-247">Для `ReadPermission` получения требования пользователь должен быть владельцем или спонсором для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="191a2-247">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="191a2-248">В случае `EditPermission` `DeletePermission` требования или ему необходимо быть владельцем для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="191a2-248">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="191a2-249">Регистрация обработчика</span><span class="sxs-lookup"><span data-stu-id="191a2-249">Handler registration</span></span>

<span data-ttu-id="191a2-250">Обработчики регистрируются в коллекции служб во время настройки.</span><span class="sxs-lookup"><span data-stu-id="191a2-250">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="191a2-251">Пример:</span><span class="sxs-lookup"><span data-stu-id="191a2-251">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="191a2-252">Предыдущий код регистрируется `MinimumAgeHandler` как одноэлементный, вызывая `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="191a2-252">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="191a2-253">Обработчики можно регистрировать с помощью любого встроенного [времени существования службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="191a2-253">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="191a2-254">Что должен возвращать обработчик?</span><span class="sxs-lookup"><span data-stu-id="191a2-254">What should a handler return?</span></span>

<span data-ttu-id="191a2-255">Обратите внимание, что `Handle` метод в [примере обработчика](#security-authorization-handler-example) не возвращает значения.</span><span class="sxs-lookup"><span data-stu-id="191a2-255">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="191a2-256">Как отображается состояние «успешно» или «сбой»?</span><span class="sxs-lookup"><span data-stu-id="191a2-256">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="191a2-257">Обработчик указывает на успешное выполнение путем вызова `context.Succeed(IAuthorizationRequirement requirement)` , передавая требование, которое прошло проверку.</span><span class="sxs-lookup"><span data-stu-id="191a2-257">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="191a2-258">Обработчику обычно не требуется обработка ошибок, так как другие обработчики для такого же требования могут быть выполнены удачно.</span><span class="sxs-lookup"><span data-stu-id="191a2-258">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="191a2-259">Чтобы гарантировать сбой, даже если другие обработчики требований выполняются успешно, вызовите `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="191a2-259">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="191a2-260">Если обработчик вызывает `context.Succeed` или `context.Fail` , все остальные обработчики все еще вызываются.</span><span class="sxs-lookup"><span data-stu-id="191a2-260">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="191a2-261">Это позволяет создавать побочные эффекты, например ведение журнала, которое выполняется, даже если другой обработчик прошел проверку или не прошел требование.</span><span class="sxs-lookup"><span data-stu-id="191a2-261">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="191a2-262">Если задано значение `false` , свойство [инвокехандлерсафтерфаилуре](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (доступно в ASP.NET Core 1,1 и более поздних версиях) сокращено при `context.Fail` вызове обработчиков.</span><span class="sxs-lookup"><span data-stu-id="191a2-262">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="191a2-263">`InvokeHandlersAfterFailure`по умолчанию имеет значение `true` , в этом случае вызываются все обработчики.</span><span class="sxs-lookup"><span data-stu-id="191a2-263">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="191a2-264">Обработчики авторизации вызываются даже в случае сбоя проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="191a2-264">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="191a2-265">Зачем требуется несколько обработчиков для требования?</span><span class="sxs-lookup"><span data-stu-id="191a2-265">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="191a2-266">В случаях, когда требуется, чтобы оценка была в **или** на основе, реализуйте несколько обработчиков для одного требования.</span><span class="sxs-lookup"><span data-stu-id="191a2-266">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="191a2-267">Например, в корпорации Майкрософт есть дверцы, которые открываются только с карточками ключей.</span><span class="sxs-lookup"><span data-stu-id="191a2-267">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="191a2-268">Если вы оставите свой ключ дома, секретарь выводит временную наклейку и открывает дверцу.</span><span class="sxs-lookup"><span data-stu-id="191a2-268">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="191a2-269">В этом сценарии у вас будет одно требование, *буилдинжентри*, но несколько обработчиков, каждый из которых проанализировать одно требование.</span><span class="sxs-lookup"><span data-stu-id="191a2-269">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="191a2-270">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="191a2-270">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="191a2-271">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="191a2-271">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="191a2-272">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="191a2-272">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="191a2-273">Убедитесь, что оба обработчика [зарегистрированы](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="191a2-273">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="191a2-274">Если любой из обработчиков будет выполнен, когда политика оценивает `BuildingEntryRequirement` , то Вычисление политики будет завершено.</span><span class="sxs-lookup"><span data-stu-id="191a2-274">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="191a2-275">Использование Func для выполнения политики</span><span class="sxs-lookup"><span data-stu-id="191a2-275">Use a func to fulfill a policy</span></span>

<span data-ttu-id="191a2-276">Могут возникнуть ситуации, в которых выполнение политики может быть простым для выражения в коде.</span><span class="sxs-lookup"><span data-stu-id="191a2-276">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="191a2-277">`Func<AuthorizationHandlerContext, bool>`При настройке политики с помощью `RequireAssertion` построителя политик можно указать.</span><span class="sxs-lookup"><span data-stu-id="191a2-277">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="191a2-278">Например, Предыдущая возможность `BadgeEntryHandler` может быть переписана следующим образом:</span><span class="sxs-lookup"><span data-stu-id="191a2-278">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="191a2-279">Доступ к контексту запроса MVC в обработчиках</span><span class="sxs-lookup"><span data-stu-id="191a2-279">Access MVC request context in handlers</span></span>

<span data-ttu-id="191a2-280">`HandleRequirementAsync`Метод, реализуемый в обработчике авторизации, имеет два параметра: и обрабатываемый объект `AuthorizationHandlerContext` `TRequirement` .</span><span class="sxs-lookup"><span data-stu-id="191a2-280">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="191a2-281">Платформы, такие как MVC или SignalR , могут добавлять любой объект в свойство в `Resource` `AuthorizationHandlerContext` для передачи дополнительной информации.</span><span class="sxs-lookup"><span data-stu-id="191a2-281">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="191a2-282">Например, MVC передает экземпляр [аусоризатионфилтерконтекст](/dotnet/api/?term=AuthorizationFilterContext) в `Resource` свойство.</span><span class="sxs-lookup"><span data-stu-id="191a2-282">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="191a2-283">Это свойство предоставляет доступ к `HttpContext` , `RouteData` и всем остальным, предоставляемым MVC и Razor страницами.</span><span class="sxs-lookup"><span data-stu-id="191a2-283">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="191a2-284">Использование `Resource` свойства зависит от платформы.</span><span class="sxs-lookup"><span data-stu-id="191a2-284">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="191a2-285">Использование сведений в `Resource` свойстве ограничивает политики авторизации определенными платформами.</span><span class="sxs-lookup"><span data-stu-id="191a2-285">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="191a2-286">Необходимо привести `Resource` свойство с помощью `is` ключевого слова, а затем подтвердить успешное приведение, чтобы гарантировать, что код не будет завершаться с ошибкой `InvalidCastException` при выполнении в других платформах:</span><span class="sxs-lookup"><span data-stu-id="191a2-286">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
