---
title: Авторизация на основе политики в ASP.NET Core
author: rick-anderson
description: Узнайте, как создавать и использовать обработчики политики авторизации для обеспечения выполнения требований авторизации в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488765"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="21097-103">Авторизация на основе политики в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21097-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="21097-104">Под крышками [авторизация на основе ролей](xref:security/authorization/roles) и [авторизация на основе утверждений](xref:security/authorization/claims) используют требование, обработчик требований и предварительно настроенную политику.</span><span class="sxs-lookup"><span data-stu-id="21097-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="21097-105">Эти строительные блоки поддерживают выражение оценок авторизации в коде.</span><span class="sxs-lookup"><span data-stu-id="21097-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="21097-106">Результатом является более богатая, многоразовая, проверяемая структура авторизации.</span><span class="sxs-lookup"><span data-stu-id="21097-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="21097-107">Политика авторизации состоит из одного или нескольких требований.</span><span class="sxs-lookup"><span data-stu-id="21097-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="21097-108">Он зарегистрирован как часть конфигурации службы `Startup.ConfigureServices` авторизации, в методе:</span><span class="sxs-lookup"><span data-stu-id="21097-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="21097-109">В предыдущем примере создается политика "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="21097-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="21097-110">Он имеет одно&mdash;требование минимального возраста, которое поставляется в качестве параметра к требованию.</span><span class="sxs-lookup"><span data-stu-id="21097-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="21097-111">IАвторизацияСервис</span><span class="sxs-lookup"><span data-stu-id="21097-111">IAuthorizationService</span></span> 

<span data-ttu-id="21097-112">Основная служба, определяющая успешное <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>авторизацию:</span><span class="sxs-lookup"><span data-stu-id="21097-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="21097-113">В предыдущем коде освещаются два метода [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="21097-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="21097-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>является службой маркеров без методов и механизмом отслеживания успешной авторизации.</span><span class="sxs-lookup"><span data-stu-id="21097-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="21097-115">Каждый из них <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> отвечает за проверку соответствия требованиям:</span><span class="sxs-lookup"><span data-stu-id="21097-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="21097-116">Класс <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> — это то, что обработчик использует для обозначения того, были ли выполнены требования:</span><span class="sxs-lookup"><span data-stu-id="21097-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="21097-117">Следующий код показывает упрощенную (и аннотированную комментариями) выполнение службы авторизации по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="21097-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="21097-118">Следующий код показывает `ConfigureServices`типичный:</span><span class="sxs-lookup"><span data-stu-id="21097-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="21097-119">Использовать <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` или для авторизации.</span><span class="sxs-lookup"><span data-stu-id="21097-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="21097-120">Применение политик к контроллерам MVC</span><span class="sxs-lookup"><span data-stu-id="21097-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="21097-121">Если вы используете страницы Razor, в этом документе смотрите [применяющие политики к страницам Razor.](#applying-policies-to-razor-pages)</span><span class="sxs-lookup"><span data-stu-id="21097-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="21097-122">Политики применяются к контроллерам с помощью атрибута `[Authorize]` с именем политики.</span><span class="sxs-lookup"><span data-stu-id="21097-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="21097-123">Пример:</span><span class="sxs-lookup"><span data-stu-id="21097-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="21097-124">Применение политик на страницах Razor</span><span class="sxs-lookup"><span data-stu-id="21097-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="21097-125">Политики применяются к страницам `[Authorize]` Razor с помощью атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="21097-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="21097-126">Пример:</span><span class="sxs-lookup"><span data-stu-id="21097-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="21097-127">Политики также могут быть применены к Razor Pages с помощью [конвенции авторизации.](xref:security/authorization/razor-pages-authorization)</span><span class="sxs-lookup"><span data-stu-id="21097-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="21097-128">Требования</span><span class="sxs-lookup"><span data-stu-id="21097-128">Requirements</span></span>

<span data-ttu-id="21097-129">Требование авторизации — это набор параметров данных, которые политика может использовать для оценки текущего принципала пользователя.</span><span class="sxs-lookup"><span data-stu-id="21097-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="21097-130">В нашей политике "AtLeast21" требование представляет&mdash;собой единый параметр минимального возраста.</span><span class="sxs-lookup"><span data-stu-id="21097-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="21097-131">Требование реализует [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), который является пустым интерфейсом маркера.</span><span class="sxs-lookup"><span data-stu-id="21097-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="21097-132">Параметризованное требование минимального возраста может быть выполнено следующим образом:</span><span class="sxs-lookup"><span data-stu-id="21097-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="21097-133">Если политика авторизации содержит несколько требований к авторизации, все требования должны пройти для того, чтобы оценка политики увенчалась успехом.</span><span class="sxs-lookup"><span data-stu-id="21097-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="21097-134">Другими словами, несколько **требований** к авторизации, добавленные к единой политике авторизации, обрабатываются на идо.</span><span class="sxs-lookup"><span data-stu-id="21097-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="21097-135">Требование не должно иметь данные или свойства.</span><span class="sxs-lookup"><span data-stu-id="21097-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="21097-136">Обработчики авторизации</span><span class="sxs-lookup"><span data-stu-id="21097-136">Authorization handlers</span></span>

<span data-ttu-id="21097-137">Обработчик авторизации отвечает за оценку свойств требования.</span><span class="sxs-lookup"><span data-stu-id="21097-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="21097-138">Обработчик авторизации оценивает требования к предоставленному [авторизационномуконтексту,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) чтобы определить, разрешен ли доступ.</span><span class="sxs-lookup"><span data-stu-id="21097-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="21097-139">Требование может иметь [несколько обработчиков.](#security-authorization-policies-based-multiple-handlers)</span><span class="sxs-lookup"><span data-stu-id="21097-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="21097-140">Обработчик может унаследовать [AuthorizationHandler\<TRequirement>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)где `TRequirement` необходимо обработать требование.</span><span class="sxs-lookup"><span data-stu-id="21097-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="21097-141">Кроме того, обработчик может реализовать [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) для обработки нескольких типов требований.</span><span class="sxs-lookup"><span data-stu-id="21097-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="21097-142">Используйте обработчик для одного требования</span><span class="sxs-lookup"><span data-stu-id="21097-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="21097-143">Ниже приводится пример отношений один к одному, в которых обработчик минимального возраста использует одно требование:</span><span class="sxs-lookup"><span data-stu-id="21097-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="21097-144">Предыдущий код определяет, имеет ли текущий директор пользователя дату рождения, которая была выдана известным и доверенным эмитентом.</span><span class="sxs-lookup"><span data-stu-id="21097-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="21097-145">Авторизация не может произойти, когда претензия отсутствует, и в этом случае завершенная задача возвращается.</span><span class="sxs-lookup"><span data-stu-id="21097-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="21097-146">При предъявлении претензии рассчитывается возраст пользователя.</span><span class="sxs-lookup"><span data-stu-id="21097-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="21097-147">Если пользователь соответствует минимальному возрасту, определенному требованием, авторизация считается успешной.</span><span class="sxs-lookup"><span data-stu-id="21097-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="21097-148">Когда авторизация `context.Succeed` успешно, ссылается с удовлетворенным требованием в качестве единственного параметра.</span><span class="sxs-lookup"><span data-stu-id="21097-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="21097-149">Используйте обработчик для нескольких требований</span><span class="sxs-lookup"><span data-stu-id="21097-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="21097-150">Ниже приводится пример отношения «один к много», в котором обработчик разрешений может обрабатывать три различных типа требований:</span><span class="sxs-lookup"><span data-stu-id="21097-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="21097-151">Предыдущий код пересекает [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;свойство, содержащее требования, не отмеченные как успешные.</span><span class="sxs-lookup"><span data-stu-id="21097-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="21097-152">Для `ReadPermission` требования пользователь должен быть либо владельцем, либо спонсором для доступа к запрашиваемому ресурсу.</span><span class="sxs-lookup"><span data-stu-id="21097-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="21097-153">В случае требования `EditPermission` `DeletePermission` или требования он или она должны быть владельцем, чтобы получить доступ к запрашиваемому ресурсу.</span><span class="sxs-lookup"><span data-stu-id="21097-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="21097-154">Регистрация обработчиков</span><span class="sxs-lookup"><span data-stu-id="21097-154">Handler registration</span></span>

<span data-ttu-id="21097-155">Обработчики регистрируются в коллекции служб во время конфигурации.</span><span class="sxs-lookup"><span data-stu-id="21097-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="21097-156">Пример:</span><span class="sxs-lookup"><span data-stu-id="21097-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="21097-157">Предыдущий код `MinimumAgeHandler` регистрируется как синглтон, `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`ссылаясь на .</span><span class="sxs-lookup"><span data-stu-id="21097-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="21097-158">Обработчики могут быть зарегистрированы с помощью любого из встроенных [сроков службы.](xref:fundamentals/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="21097-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="21097-159">Что должен вернуть обработчик?</span><span class="sxs-lookup"><span data-stu-id="21097-159">What should a handler return?</span></span>

<span data-ttu-id="21097-160">Обратите внимание, что метод в `Handle` [примере обработчика](#security-authorization-handler-example) не возвращает значения.</span><span class="sxs-lookup"><span data-stu-id="21097-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="21097-161">Как указывается статус успеха или неудачи?</span><span class="sxs-lookup"><span data-stu-id="21097-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="21097-162">Обработчик указывает `context.Succeed(IAuthorizationRequirement requirement)`на успех, вызывая, передавая требование, которое было успешно проверено.</span><span class="sxs-lookup"><span data-stu-id="21097-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="21097-163">Обработчику не нужно обрабатывать сбои в целом, так как другие обработчики для того же требования могут преуспеть.</span><span class="sxs-lookup"><span data-stu-id="21097-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="21097-164">Чтобы гарантировать сбой, даже если другие обработчики требований преуспевают, позвоните. `context.Fail`</span><span class="sxs-lookup"><span data-stu-id="21097-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="21097-165">Если обработчик вызывает `context.Succeed` или `context.Fail`все остальные обработчики по-прежнему вызываются.</span><span class="sxs-lookup"><span data-stu-id="21097-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="21097-166">Это позволяет требованиям создавать побочные эффекты, такие как журналирование, что происходит, даже если другой обработчик успешно проверил или не выполнил требование.</span><span class="sxs-lookup"><span data-stu-id="21097-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="21097-167">При наборе `false`в , [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) собственности (доступно в ASP.NET Core 1.1 и `context.Fail` более поздней) короткого замыкания исполнения обработчиков, когда называется.</span><span class="sxs-lookup"><span data-stu-id="21097-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="21097-168">`InvokeHandlersAfterFailure`по умолчанию, `true`и в этом случае все обработчики называются.</span><span class="sxs-lookup"><span data-stu-id="21097-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="21097-169">Обработчики авторизации вызываются даже при сбой аутентификации.</span><span class="sxs-lookup"><span data-stu-id="21097-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="21097-170">Зачем мне нужны несколько обработчиков для требования?</span><span class="sxs-lookup"><span data-stu-id="21097-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="21097-171">В тех случаях, когда вы хотите, чтобы оценка была на **или** основе, реализуем несколько обработчиков для одного требования.</span><span class="sxs-lookup"><span data-stu-id="21097-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="21097-172">Например, у корпорации Майкрософт есть двери, которые открываются только с помощью ключевых карт.</span><span class="sxs-lookup"><span data-stu-id="21097-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="21097-173">Если вы оставите ключ-карту дома, администратор напечатывает временную наклейку и откроет дверь для вас.</span><span class="sxs-lookup"><span data-stu-id="21097-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="21097-174">В этом сценарии у вас будет одно требование, *BuildingEntry,* но несколько обработчиков, каждый из которых изучает одно требование.</span><span class="sxs-lookup"><span data-stu-id="21097-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="21097-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="21097-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="21097-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="21097-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="21097-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="21097-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="21097-178">Убедитесь, что оба обработчика [зарегистрированы.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)</span><span class="sxs-lookup"><span data-stu-id="21097-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="21097-179">Если какой-либо обработчик `BuildingEntryRequirement`преуспевает, когда политика оценивает, оценка политики удается.</span><span class="sxs-lookup"><span data-stu-id="21097-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="21097-180">Использование func для выполнения политики</span><span class="sxs-lookup"><span data-stu-id="21097-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="21097-181">Возможны ситуации, в которых выполнение политики просто выразить в коде.</span><span class="sxs-lookup"><span data-stu-id="21097-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="21097-182">При настройке политики `Func<AuthorizationHandlerContext, bool>` с программой-строителем `RequireAssertion` можно предоставить его.</span><span class="sxs-lookup"><span data-stu-id="21097-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="21097-183">Например, предыдущий `BadgeEntryHandler` можно было бы переписать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="21097-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="21097-184">Доступ к контексту запроса MVC в обработчиках</span><span class="sxs-lookup"><span data-stu-id="21097-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="21097-185">Метод, `HandleRequirementAsync` который вы реализуете в обработчике авторизации, имеет два параметра: an `AuthorizationHandlerContext` и вы обрабатываете. `TRequirement`</span><span class="sxs-lookup"><span data-stu-id="21097-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="21097-186">Такие рамки, как MVC или Jabbr, могут `Resource` добавлять `AuthorizationHandlerContext` любой объект в свойство для передачи дополнительной информации.</span><span class="sxs-lookup"><span data-stu-id="21097-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="21097-187">Например, MVC передает экземпляр [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) в свойстве. `Resource`</span><span class="sxs-lookup"><span data-stu-id="21097-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="21097-188">Это свойство `HttpContext`предоставляет `RouteData`доступ к , и все остальное, предоставляемые MVC и Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="21097-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="21097-189">Использование `Resource` свойства является рамочной.</span><span class="sxs-lookup"><span data-stu-id="21097-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="21097-190">Использование информации `Resource` в свойстве ограничивает ваши политики авторизации определенными инфраструктурами.</span><span class="sxs-lookup"><span data-stu-id="21097-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="21097-191">Вы должны `Resource` бросить свойство с помощью ключевого `is` слова, а затем подтвердить, `InvalidCastException` что литые удалось обеспечить ваш код не сбой с при запуске на других средах:</span><span class="sxs-lookup"><span data-stu-id="21097-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="21097-192">Под крышками [авторизация на основе ролей](xref:security/authorization/roles) и [авторизация на основе утверждений](xref:security/authorization/claims) используют требование, обработчик требований и предварительно настроенную политику.</span><span class="sxs-lookup"><span data-stu-id="21097-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="21097-193">Эти строительные блоки поддерживают выражение оценок авторизации в коде.</span><span class="sxs-lookup"><span data-stu-id="21097-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="21097-194">Результатом является более богатая, многоразовая, проверяемая структура авторизации.</span><span class="sxs-lookup"><span data-stu-id="21097-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="21097-195">Политика авторизации состоит из одного или нескольких требований.</span><span class="sxs-lookup"><span data-stu-id="21097-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="21097-196">Он зарегистрирован как часть конфигурации службы `Startup.ConfigureServices` авторизации, в методе:</span><span class="sxs-lookup"><span data-stu-id="21097-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="21097-197">В предыдущем примере создается политика "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="21097-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="21097-198">Он имеет одно&mdash;требование минимального возраста, которое поставляется в качестве параметра к требованию.</span><span class="sxs-lookup"><span data-stu-id="21097-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="21097-199">IАвторизацияСервис</span><span class="sxs-lookup"><span data-stu-id="21097-199">IAuthorizationService</span></span> 

<span data-ttu-id="21097-200">Основная служба, определяющая успешное <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>авторизацию:</span><span class="sxs-lookup"><span data-stu-id="21097-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="21097-201">В предыдущем коде освещаются два метода [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="21097-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="21097-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>является службой маркеров без методов и механизмом отслеживания успешной авторизации.</span><span class="sxs-lookup"><span data-stu-id="21097-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="21097-203">Каждый из них <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> отвечает за проверку соответствия требованиям:</span><span class="sxs-lookup"><span data-stu-id="21097-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="21097-204">Класс <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> — это то, что обработчик использует для обозначения того, были ли выполнены требования:</span><span class="sxs-lookup"><span data-stu-id="21097-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="21097-205">Следующий код показывает упрощенную (и аннотированную комментариями) выполнение службы авторизации по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="21097-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="21097-206">Следующий код показывает `ConfigureServices`типичный:</span><span class="sxs-lookup"><span data-stu-id="21097-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="21097-207">Использовать <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` или для авторизации.</span><span class="sxs-lookup"><span data-stu-id="21097-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="21097-208">Применение политик к контроллерам MVC</span><span class="sxs-lookup"><span data-stu-id="21097-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="21097-209">Если вы используете страницы Razor, в этом документе смотрите [применяющие политики к страницам Razor.](#applying-policies-to-razor-pages)</span><span class="sxs-lookup"><span data-stu-id="21097-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="21097-210">Политики применяются к контроллерам с помощью атрибута `[Authorize]` с именем политики.</span><span class="sxs-lookup"><span data-stu-id="21097-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="21097-211">Пример:</span><span class="sxs-lookup"><span data-stu-id="21097-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="21097-212">Применение политик на страницах Razor</span><span class="sxs-lookup"><span data-stu-id="21097-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="21097-213">Политики применяются к страницам `[Authorize]` Razor с помощью атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="21097-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="21097-214">Пример:</span><span class="sxs-lookup"><span data-stu-id="21097-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="21097-215">Политики также могут быть применены к Razor Pages с помощью [конвенции авторизации.](xref:security/authorization/razor-pages-authorization)</span><span class="sxs-lookup"><span data-stu-id="21097-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="21097-216">Требования</span><span class="sxs-lookup"><span data-stu-id="21097-216">Requirements</span></span>

<span data-ttu-id="21097-217">Требование авторизации — это набор параметров данных, которые политика может использовать для оценки текущего принципала пользователя.</span><span class="sxs-lookup"><span data-stu-id="21097-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="21097-218">В нашей политике "AtLeast21" требование представляет&mdash;собой единый параметр минимального возраста.</span><span class="sxs-lookup"><span data-stu-id="21097-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="21097-219">Требование реализует [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), который является пустым интерфейсом маркера.</span><span class="sxs-lookup"><span data-stu-id="21097-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="21097-220">Параметризованное требование минимального возраста может быть выполнено следующим образом:</span><span class="sxs-lookup"><span data-stu-id="21097-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="21097-221">Если политика авторизации содержит несколько требований к авторизации, все требования должны пройти для того, чтобы оценка политики увенчалась успехом.</span><span class="sxs-lookup"><span data-stu-id="21097-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="21097-222">Другими словами, несколько **требований** к авторизации, добавленные к единой политике авторизации, обрабатываются на идо.</span><span class="sxs-lookup"><span data-stu-id="21097-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="21097-223">Требование не должно иметь данные или свойства.</span><span class="sxs-lookup"><span data-stu-id="21097-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="21097-224">Обработчики авторизации</span><span class="sxs-lookup"><span data-stu-id="21097-224">Authorization handlers</span></span>

<span data-ttu-id="21097-225">Обработчик авторизации отвечает за оценку свойств требования.</span><span class="sxs-lookup"><span data-stu-id="21097-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="21097-226">Обработчик авторизации оценивает требования к предоставленному [авторизационномуконтексту,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) чтобы определить, разрешен ли доступ.</span><span class="sxs-lookup"><span data-stu-id="21097-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="21097-227">Требование может иметь [несколько обработчиков.](#security-authorization-policies-based-multiple-handlers)</span><span class="sxs-lookup"><span data-stu-id="21097-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="21097-228">Обработчик может унаследовать [AuthorizationHandler\<TRequirement>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)где `TRequirement` необходимо обработать требование.</span><span class="sxs-lookup"><span data-stu-id="21097-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="21097-229">Кроме того, обработчик может реализовать [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) для обработки нескольких типов требований.</span><span class="sxs-lookup"><span data-stu-id="21097-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="21097-230">Используйте обработчик для одного требования</span><span class="sxs-lookup"><span data-stu-id="21097-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="21097-231">Ниже приводится пример отношений один к одному, в которых обработчик минимального возраста использует одно требование:</span><span class="sxs-lookup"><span data-stu-id="21097-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="21097-232">Предыдущий код определяет, имеет ли текущий директор пользователя дату рождения, которая была выдана известным и доверенным эмитентом.</span><span class="sxs-lookup"><span data-stu-id="21097-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="21097-233">Авторизация не может произойти, когда претензия отсутствует, и в этом случае завершенная задача возвращается.</span><span class="sxs-lookup"><span data-stu-id="21097-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="21097-234">При предъявлении претензии рассчитывается возраст пользователя.</span><span class="sxs-lookup"><span data-stu-id="21097-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="21097-235">Если пользователь соответствует минимальному возрасту, определенному требованием, авторизация считается успешной.</span><span class="sxs-lookup"><span data-stu-id="21097-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="21097-236">Когда авторизация `context.Succeed` успешно, ссылается с удовлетворенным требованием в качестве единственного параметра.</span><span class="sxs-lookup"><span data-stu-id="21097-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="21097-237">Используйте обработчик для нескольких требований</span><span class="sxs-lookup"><span data-stu-id="21097-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="21097-238">Ниже приводится пример отношения «один к много», в котором обработчик разрешений может обрабатывать три различных типа требований:</span><span class="sxs-lookup"><span data-stu-id="21097-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="21097-239">Предыдущий код пересекает [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;свойство, содержащее требования, не отмеченные как успешные.</span><span class="sxs-lookup"><span data-stu-id="21097-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="21097-240">Для `ReadPermission` требования пользователь должен быть либо владельцем, либо спонсором для доступа к запрашиваемому ресурсу.</span><span class="sxs-lookup"><span data-stu-id="21097-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="21097-241">В случае требования `EditPermission` `DeletePermission` или требования он или она должны быть владельцем, чтобы получить доступ к запрашиваемому ресурсу.</span><span class="sxs-lookup"><span data-stu-id="21097-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="21097-242">Регистрация обработчиков</span><span class="sxs-lookup"><span data-stu-id="21097-242">Handler registration</span></span>

<span data-ttu-id="21097-243">Обработчики регистрируются в коллекции служб во время конфигурации.</span><span class="sxs-lookup"><span data-stu-id="21097-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="21097-244">Пример:</span><span class="sxs-lookup"><span data-stu-id="21097-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="21097-245">Предыдущий код `MinimumAgeHandler` регистрируется как синглтон, `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`ссылаясь на .</span><span class="sxs-lookup"><span data-stu-id="21097-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="21097-246">Обработчики могут быть зарегистрированы с помощью любого из встроенных [сроков службы.](xref:fundamentals/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="21097-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="21097-247">Что должен вернуть обработчик?</span><span class="sxs-lookup"><span data-stu-id="21097-247">What should a handler return?</span></span>

<span data-ttu-id="21097-248">Обратите внимание, что метод в `Handle` [примере обработчика](#security-authorization-handler-example) не возвращает значения.</span><span class="sxs-lookup"><span data-stu-id="21097-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="21097-249">Как указывается статус успеха или неудачи?</span><span class="sxs-lookup"><span data-stu-id="21097-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="21097-250">Обработчик указывает `context.Succeed(IAuthorizationRequirement requirement)`на успех, вызывая, передавая требование, которое было успешно проверено.</span><span class="sxs-lookup"><span data-stu-id="21097-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="21097-251">Обработчику не нужно обрабатывать сбои в целом, так как другие обработчики для того же требования могут преуспеть.</span><span class="sxs-lookup"><span data-stu-id="21097-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="21097-252">Чтобы гарантировать сбой, даже если другие обработчики требований преуспевают, позвоните. `context.Fail`</span><span class="sxs-lookup"><span data-stu-id="21097-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="21097-253">Если обработчик вызывает `context.Succeed` или `context.Fail`все остальные обработчики по-прежнему вызываются.</span><span class="sxs-lookup"><span data-stu-id="21097-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="21097-254">Это позволяет требованиям создавать побочные эффекты, такие как журналирование, что происходит, даже если другой обработчик успешно проверил или не выполнил требование.</span><span class="sxs-lookup"><span data-stu-id="21097-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="21097-255">При наборе `false`в , [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) собственности (доступно в ASP.NET Core 1.1 и `context.Fail` более поздней) короткого замыкания исполнения обработчиков, когда называется.</span><span class="sxs-lookup"><span data-stu-id="21097-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="21097-256">`InvokeHandlersAfterFailure`по умолчанию, `true`и в этом случае все обработчики называются.</span><span class="sxs-lookup"><span data-stu-id="21097-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="21097-257">Обработчики авторизации вызываются даже при сбой аутентификации.</span><span class="sxs-lookup"><span data-stu-id="21097-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="21097-258">Зачем мне нужны несколько обработчиков для требования?</span><span class="sxs-lookup"><span data-stu-id="21097-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="21097-259">В тех случаях, когда вы хотите, чтобы оценка была на **или** основе, реализуем несколько обработчиков для одного требования.</span><span class="sxs-lookup"><span data-stu-id="21097-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="21097-260">Например, у корпорации Майкрософт есть двери, которые открываются только с помощью ключевых карт.</span><span class="sxs-lookup"><span data-stu-id="21097-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="21097-261">Если вы оставите ключ-карту дома, администратор напечатывает временную наклейку и откроет дверь для вас.</span><span class="sxs-lookup"><span data-stu-id="21097-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="21097-262">В этом сценарии у вас будет одно требование, *BuildingEntry,* но несколько обработчиков, каждый из которых изучает одно требование.</span><span class="sxs-lookup"><span data-stu-id="21097-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="21097-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="21097-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="21097-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="21097-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="21097-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="21097-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="21097-266">Убедитесь, что оба обработчика [зарегистрированы.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)</span><span class="sxs-lookup"><span data-stu-id="21097-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="21097-267">Если какой-либо обработчик `BuildingEntryRequirement`преуспевает, когда политика оценивает, оценка политики удается.</span><span class="sxs-lookup"><span data-stu-id="21097-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="21097-268">Использование func для выполнения политики</span><span class="sxs-lookup"><span data-stu-id="21097-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="21097-269">Возможны ситуации, в которых выполнение политики просто выразить в коде.</span><span class="sxs-lookup"><span data-stu-id="21097-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="21097-270">При настройке политики `Func<AuthorizationHandlerContext, bool>` с программой-строителем `RequireAssertion` можно предоставить его.</span><span class="sxs-lookup"><span data-stu-id="21097-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="21097-271">Например, предыдущий `BadgeEntryHandler` можно было бы переписать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="21097-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="21097-272">Доступ к контексту запроса MVC в обработчиках</span><span class="sxs-lookup"><span data-stu-id="21097-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="21097-273">Метод, `HandleRequirementAsync` который вы реализуете в обработчике авторизации, имеет два параметра: an `AuthorizationHandlerContext` и вы обрабатываете. `TRequirement`</span><span class="sxs-lookup"><span data-stu-id="21097-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="21097-274">Такие рамки, как MVC или SignalR, могут `Resource` добавлять `AuthorizationHandlerContext` любой объект в свойство для передачи дополнительной информации.</span><span class="sxs-lookup"><span data-stu-id="21097-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="21097-275">При использовании конечных точек авторизация обычно обрабатывается программой Authorization Middleware.</span><span class="sxs-lookup"><span data-stu-id="21097-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="21097-276">В этом случае `Resource` свойство является <xref:Microsoft.AspNetCore.Http.Endpoint>экземпляром .</span><span class="sxs-lookup"><span data-stu-id="21097-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="21097-277">Конечная точка может быть использована для зондирования базового ресурса, к которому вы перенаправляетесь.</span><span class="sxs-lookup"><span data-stu-id="21097-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="21097-278">Пример:</span><span class="sxs-lookup"><span data-stu-id="21097-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="21097-279">При традиционной реутриации или при разрешении в качестве части фильтра авторизации MVC значение `Resource` является экземпляром. <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext></span><span class="sxs-lookup"><span data-stu-id="21097-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="21097-280">Это свойство `HttpContext`предоставляет `RouteData`доступ к , и все остальное, предоставляемые MVC и Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="21097-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="21097-281">Использование `Resource` свойства является рамочной.</span><span class="sxs-lookup"><span data-stu-id="21097-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="21097-282">Использование информации `Resource` в свойстве ограничивает ваши политики авторизации определенными инфраструктурами.</span><span class="sxs-lookup"><span data-stu-id="21097-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="21097-283">Вы должны `Resource` бросить свойство с помощью ключевого `is` слова, а затем подтвердить, `InvalidCastException` что литые удалось обеспечить ваш код не сбой с при запуске на других средах:</span><span class="sxs-lookup"><span data-stu-id="21097-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
