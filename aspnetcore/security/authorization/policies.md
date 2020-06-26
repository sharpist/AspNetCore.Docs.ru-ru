---
title: Авторизация на основе политик в ASP.NET Core
author: rick-anderson
description: Узнайте, как создавать и использовать обработчики политик авторизации для применения требований к авторизации в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 8c68f2a15d07909d4576a2426d92f9beaa91fbb7
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408075"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="b43af-103">Авторизация на основе политик в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b43af-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b43af-104">В процессе [авторизации на основе ролей](xref:security/authorization/roles) и [авторизации на основе утверждений](xref:security/authorization/claims) используются требования, обработчик требований и предварительно настроенная политика.</span><span class="sxs-lookup"><span data-stu-id="b43af-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="b43af-105">Эти стандартные блоки поддерживают выражение оценки авторизации в коде.</span><span class="sxs-lookup"><span data-stu-id="b43af-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="b43af-106">Результатом является расширенная, повторно используемая, тестируемая структура авторизации.</span><span class="sxs-lookup"><span data-stu-id="b43af-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="b43af-107">Политика авторизации состоит из одного или нескольких требований.</span><span class="sxs-lookup"><span data-stu-id="b43af-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="b43af-108">Он регистрируется как часть конфигурации службы авторизации в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="b43af-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="b43af-109">В предыдущем примере создается политика "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="b43af-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="b43af-110">Он имеет одно требование &mdash; с минимальным возрастом, которое предоставляется в качестве параметра для требования.</span><span class="sxs-lookup"><span data-stu-id="b43af-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="b43af-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="b43af-111">IAuthorizationService</span></span> 

<span data-ttu-id="b43af-112">Основная служба, которая определяет, успешно ли выполнена авторизация <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="b43af-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="b43af-113">В приведенном выше коде показаны два метода [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="b43af-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="b43af-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>— это служба маркеров без методов, а также механизм отслеживания успешности авторизации.</span><span class="sxs-lookup"><span data-stu-id="b43af-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="b43af-115">Каждый из них <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> отвечает за проверку соблюдения требований:</span><span class="sxs-lookup"><span data-stu-id="b43af-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="b43af-116">Этот <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> класс используется обработчиком для обозначения того, выполнены ли требования.</span><span class="sxs-lookup"><span data-stu-id="b43af-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="b43af-117">В следующем коде показаны упрощенная реализация службы авторизации по умолчанию (и заметки с комментариями):</span><span class="sxs-lookup"><span data-stu-id="b43af-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="b43af-118">В следующем коде показана типичная `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b43af-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="b43af-119">Используйте <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> или `[Authorize(Policy = "Something")]` для авторизации.</span><span class="sxs-lookup"><span data-stu-id="b43af-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="b43af-120">Применение политик к контроллерам MVC</span><span class="sxs-lookup"><span data-stu-id="b43af-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="b43af-121">Если вы используете Razor страницы, см. раздел [применение политик к Razor страницам](#apply-policies-to-razor-pages) этого документа.</span><span class="sxs-lookup"><span data-stu-id="b43af-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="b43af-122">Политики применяются к контроллерам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="b43af-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b43af-123">Пример.</span><span class="sxs-lookup"><span data-stu-id="b43af-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="b43af-124">Применение политик к Razor страницам</span><span class="sxs-lookup"><span data-stu-id="b43af-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="b43af-125">Политики применяются к Razor страницам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="b43af-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b43af-126">Пример.</span><span class="sxs-lookup"><span data-stu-id="b43af-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="b43af-127">Политики ***не*** могут применяться на Razor уровне обработчика страницы, они должны быть применены к странице.</span><span class="sxs-lookup"><span data-stu-id="b43af-127">Policies can ***not*** be applied at the Razor Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="b43af-128">Политики можно применять к Razor страницам с помощью [соглашения об авторизации](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="b43af-128">Policies can be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="b43af-129">Требования</span><span class="sxs-lookup"><span data-stu-id="b43af-129">Requirements</span></span>

<span data-ttu-id="b43af-130">Требование авторизации — это коллекция параметров данных, которую политика может использовать для вычисления текущего субъекта-пользователя.</span><span class="sxs-lookup"><span data-stu-id="b43af-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="b43af-131">В нашей политике "AtLeast21" требование — это единственный параметр &mdash; с минимальным возрастом.</span><span class="sxs-lookup"><span data-stu-id="b43af-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="b43af-132">Требование реализует [иаусоризатионрекуиремент](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), который является пустым интерфейсом маркера.</span><span class="sxs-lookup"><span data-stu-id="b43af-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="b43af-133">Требование к параметризованному минимальному возрасту можно реализовать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="b43af-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="b43af-134">Если политика авторизации содержит несколько требований к авторизации, все требования должны пройти, чтобы оценка политики прошла успешно.</span><span class="sxs-lookup"><span data-stu-id="b43af-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="b43af-135">Иными словами, несколько требований к авторизации, добавленных в одну политику авторизации, обрабатываются **и** на основе.</span><span class="sxs-lookup"><span data-stu-id="b43af-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="b43af-136">Требование не обязательно должно иметь данные или свойства.</span><span class="sxs-lookup"><span data-stu-id="b43af-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="b43af-137">Обработчики авторизации</span><span class="sxs-lookup"><span data-stu-id="b43af-137">Authorization handlers</span></span>

<span data-ttu-id="b43af-138">Обработчик авторизации отвечает за оценку свойств требования.</span><span class="sxs-lookup"><span data-stu-id="b43af-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="b43af-139">Обработчик авторизации оценивает требования к предоставленному [аусоризатионхандлерконтекст](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) , чтобы определить, разрешен ли доступ.</span><span class="sxs-lookup"><span data-stu-id="b43af-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="b43af-140">Требование может иметь [несколько обработчиков](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="b43af-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="b43af-141">Обработчик может наследовать [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), где `TRequirement` является требованием для обработки.</span><span class="sxs-lookup"><span data-stu-id="b43af-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="b43af-142">Кроме того, обработчик может реализовать [иаусоризатионхандлер](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) для обработки более чем одного типа требований.</span><span class="sxs-lookup"><span data-stu-id="b43af-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="b43af-143">Использование обработчика для одного требования</span><span class="sxs-lookup"><span data-stu-id="b43af-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="b43af-144">Ниже приведен пример связи «один к одному», в которой обработчик минимального возраста использует одно требование:</span><span class="sxs-lookup"><span data-stu-id="b43af-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="b43af-145">Приведенный выше код определяет, имеет ли текущий участник пользователя дату утверждения о рождении, выданного известным и надежным издателем.</span><span class="sxs-lookup"><span data-stu-id="b43af-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="b43af-146">Авторизация не может произойти, если утверждение отсутствует, в этом случае возвращается завершенная задача.</span><span class="sxs-lookup"><span data-stu-id="b43af-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="b43af-147">При наличии утверждения возраст пользователя вычисляется.</span><span class="sxs-lookup"><span data-stu-id="b43af-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="b43af-148">Если пользователь соответствует минимальному возрасту, заданному требованием, авторизация считается успешной.</span><span class="sxs-lookup"><span data-stu-id="b43af-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="b43af-149">Когда авторизация прошла успешно, `context.Succeed` вызывается с удовлетворенным требованием в качестве единственного параметра.</span><span class="sxs-lookup"><span data-stu-id="b43af-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="b43af-150">Использование обработчика для нескольких требований</span><span class="sxs-lookup"><span data-stu-id="b43af-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="b43af-151">Ниже приведен пример связи «один ко многим», в которой обработчик разрешений может управлять тремя различными типами требований:</span><span class="sxs-lookup"><span data-stu-id="b43af-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="b43af-152">Предыдущий код проходит по [пендингрекуирементс](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; свойству, содержащему требования, не помеченные как неудачные.</span><span class="sxs-lookup"><span data-stu-id="b43af-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="b43af-153">Для `ReadPermission` получения требования пользователь должен быть владельцем или спонсором для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="b43af-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="b43af-154">В случае `EditPermission` `DeletePermission` требования или ему необходимо быть владельцем для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="b43af-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="b43af-155">Регистрация обработчика</span><span class="sxs-lookup"><span data-stu-id="b43af-155">Handler registration</span></span>

<span data-ttu-id="b43af-156">Обработчики регистрируются в коллекции служб во время настройки.</span><span class="sxs-lookup"><span data-stu-id="b43af-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="b43af-157">Пример.</span><span class="sxs-lookup"><span data-stu-id="b43af-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="b43af-158">Предыдущий код регистрируется `MinimumAgeHandler` как одноэлементный, вызывая `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="b43af-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="b43af-159">Обработчики можно регистрировать с помощью любого встроенного [времени существования службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="b43af-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="b43af-160">Что должен возвращать обработчик?</span><span class="sxs-lookup"><span data-stu-id="b43af-160">What should a handler return?</span></span>

<span data-ttu-id="b43af-161">Обратите внимание, что `Handle` метод в [примере обработчика](#security-authorization-handler-example) не возвращает значения.</span><span class="sxs-lookup"><span data-stu-id="b43af-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="b43af-162">Как отображается состояние «успешно» или «сбой»?</span><span class="sxs-lookup"><span data-stu-id="b43af-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="b43af-163">Обработчик указывает на успешное выполнение путем вызова `context.Succeed(IAuthorizationRequirement requirement)` , передавая требование, которое прошло проверку.</span><span class="sxs-lookup"><span data-stu-id="b43af-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="b43af-164">Обработчику обычно не требуется обработка ошибок, так как другие обработчики для такого же требования могут быть выполнены удачно.</span><span class="sxs-lookup"><span data-stu-id="b43af-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="b43af-165">Чтобы гарантировать сбой, даже если другие обработчики требований выполняются успешно, вызовите `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="b43af-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="b43af-166">Если обработчик вызывает `context.Succeed` или `context.Fail` , все остальные обработчики все еще вызываются.</span><span class="sxs-lookup"><span data-stu-id="b43af-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="b43af-167">Это позволяет создавать побочные эффекты, например ведение журнала, которое выполняется, даже если другой обработчик прошел проверку или не прошел требование.</span><span class="sxs-lookup"><span data-stu-id="b43af-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="b43af-168">Если задано значение `false` , свойство [инвокехандлерсафтерфаилуре](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (доступно в ASP.NET Core 1,1 и более поздних версиях) сокращено при `context.Fail` вызове обработчиков.</span><span class="sxs-lookup"><span data-stu-id="b43af-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="b43af-169">`InvokeHandlersAfterFailure`по умолчанию имеет значение `true` , в этом случае вызываются все обработчики.</span><span class="sxs-lookup"><span data-stu-id="b43af-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="b43af-170">Обработчики авторизации вызываются даже в случае сбоя проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="b43af-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="b43af-171">Зачем требуется несколько обработчиков для требования?</span><span class="sxs-lookup"><span data-stu-id="b43af-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="b43af-172">В случаях, когда требуется, чтобы оценка была в **или** на основе, реализуйте несколько обработчиков для одного требования.</span><span class="sxs-lookup"><span data-stu-id="b43af-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="b43af-173">Например, в корпорации Майкрософт есть дверцы, которые открываются только с карточками ключей.</span><span class="sxs-lookup"><span data-stu-id="b43af-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="b43af-174">Если вы оставите свой ключ дома, секретарь выводит временную наклейку и открывает дверцу.</span><span class="sxs-lookup"><span data-stu-id="b43af-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="b43af-175">В этом сценарии у вас будет одно требование, *буилдинжентри*, но несколько обработчиков, каждый из которых проанализировать одно требование.</span><span class="sxs-lookup"><span data-stu-id="b43af-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="b43af-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="b43af-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="b43af-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b43af-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="b43af-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b43af-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="b43af-179">Убедитесь, что оба обработчика [зарегистрированы](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="b43af-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="b43af-180">Если любой из обработчиков будет выполнен, когда политика оценивает `BuildingEntryRequirement` , то Вычисление политики будет завершено.</span><span class="sxs-lookup"><span data-stu-id="b43af-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="b43af-181">Использование Func для выполнения политики</span><span class="sxs-lookup"><span data-stu-id="b43af-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="b43af-182">Могут возникнуть ситуации, в которых выполнение политики может быть простым для выражения в коде.</span><span class="sxs-lookup"><span data-stu-id="b43af-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="b43af-183">`Func<AuthorizationHandlerContext, bool>`При настройке политики с помощью `RequireAssertion` построителя политик можно указать.</span><span class="sxs-lookup"><span data-stu-id="b43af-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="b43af-184">Например, Предыдущая возможность `BadgeEntryHandler` может быть переписана следующим образом:</span><span class="sxs-lookup"><span data-stu-id="b43af-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="b43af-185">Доступ к контексту запроса MVC в обработчиках</span><span class="sxs-lookup"><span data-stu-id="b43af-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="b43af-186">`HandleRequirementAsync`Метод, реализуемый в обработчике авторизации, имеет два параметра: и обрабатываемый объект `AuthorizationHandlerContext` `TRequirement` .</span><span class="sxs-lookup"><span data-stu-id="b43af-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="b43af-187">Платформы, такие как MVC или SignalR , могут добавлять любой объект в свойство в `Resource` `AuthorizationHandlerContext` для передачи дополнительной информации.</span><span class="sxs-lookup"><span data-stu-id="b43af-187">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="b43af-188">При использовании маршрутизации конечных точек авторизация обычно обрабатывается по промежуточного слоя авторизации.</span><span class="sxs-lookup"><span data-stu-id="b43af-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="b43af-189">В этом случае `Resource` свойство является экземпляром <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="b43af-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="b43af-190">Конечную точку можно использовать для проверки базового ресурса, с которым выполняется маршрутизация.</span><span class="sxs-lookup"><span data-stu-id="b43af-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="b43af-191">Пример.</span><span class="sxs-lookup"><span data-stu-id="b43af-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="b43af-192">Конечная точка не предоставляет доступ к текущей `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="b43af-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="b43af-193">При использовании маршрутизации конечных точек используйте `IHttpContextAcessor` для доступа `HttpContext` внутри обработчика авторизации.</span><span class="sxs-lookup"><span data-stu-id="b43af-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="b43af-194">Дополнительные сведения см. в разделе [Использование HttpContext из пользовательских компонентов](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="b43af-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="b43af-195">Если используется традиционная маршрутизация или когда Авторизация происходит как часть фильтра авторизации MVC, значение `Resource` является <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> экземпляром.</span><span class="sxs-lookup"><span data-stu-id="b43af-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="b43af-196">Это свойство предоставляет доступ к `HttpContext` , `RouteData` и всем остальным, предоставляемым MVC и Razor страницами.</span><span class="sxs-lookup"><span data-stu-id="b43af-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="b43af-197">Использование `Resource` свойства зависит от платформы.</span><span class="sxs-lookup"><span data-stu-id="b43af-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="b43af-198">Использование сведений в `Resource` свойстве ограничивает политики авторизации определенными платформами.</span><span class="sxs-lookup"><span data-stu-id="b43af-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="b43af-199">Необходимо привести `Resource` свойство с помощью `is` ключевого слова, а затем подтвердить успешное приведение, чтобы гарантировать, что код не будет завершаться с ошибкой `InvalidCastException` при выполнении в других платформах:</span><span class="sxs-lookup"><span data-stu-id="b43af-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="b43af-200">В процессе [авторизации на основе ролей](xref:security/authorization/roles) и [авторизации на основе утверждений](xref:security/authorization/claims) используются требования, обработчик требований и предварительно настроенная политика.</span><span class="sxs-lookup"><span data-stu-id="b43af-200">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="b43af-201">Эти стандартные блоки поддерживают выражение оценки авторизации в коде.</span><span class="sxs-lookup"><span data-stu-id="b43af-201">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="b43af-202">Результатом является расширенная, повторно используемая, тестируемая структура авторизации.</span><span class="sxs-lookup"><span data-stu-id="b43af-202">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="b43af-203">Политика авторизации состоит из одного или нескольких требований.</span><span class="sxs-lookup"><span data-stu-id="b43af-203">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="b43af-204">Он регистрируется как часть конфигурации службы авторизации в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="b43af-204">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="b43af-205">В предыдущем примере создается политика "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="b43af-205">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="b43af-206">Он имеет одно требование &mdash; с минимальным возрастом, которое предоставляется в качестве параметра для требования.</span><span class="sxs-lookup"><span data-stu-id="b43af-206">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="b43af-207">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="b43af-207">IAuthorizationService</span></span> 

<span data-ttu-id="b43af-208">Основная служба, которая определяет, успешно ли выполнена авторизация <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="b43af-208">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="b43af-209">В приведенном выше коде показаны два метода [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="b43af-209">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="b43af-210"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>— это служба маркеров без методов, а также механизм отслеживания успешности авторизации.</span><span class="sxs-lookup"><span data-stu-id="b43af-210"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="b43af-211">Каждый из них <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> отвечает за проверку соблюдения требований:</span><span class="sxs-lookup"><span data-stu-id="b43af-211">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="b43af-212">Этот <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> класс используется обработчиком для обозначения того, выполнены ли требования.</span><span class="sxs-lookup"><span data-stu-id="b43af-212">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="b43af-213">В следующем коде показаны упрощенная реализация службы авторизации по умолчанию (и заметки с комментариями):</span><span class="sxs-lookup"><span data-stu-id="b43af-213">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="b43af-214">В следующем коде показана типичная `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b43af-214">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="b43af-215">Используйте <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> или `[Authorize(Policy = "Something")]` для авторизации.</span><span class="sxs-lookup"><span data-stu-id="b43af-215">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="b43af-216">Применение политик к контроллерам MVC</span><span class="sxs-lookup"><span data-stu-id="b43af-216">Apply policies to MVC controllers</span></span>

<span data-ttu-id="b43af-217">Если вы используете Razor страницы, см. раздел [применение политик к Razor страницам](#apply-policies-to-razor-pages) этого документа.</span><span class="sxs-lookup"><span data-stu-id="b43af-217">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="b43af-218">Политики применяются к контроллерам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="b43af-218">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b43af-219">Пример.</span><span class="sxs-lookup"><span data-stu-id="b43af-219">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="b43af-220">Применение политик к Razor страницам</span><span class="sxs-lookup"><span data-stu-id="b43af-220">Apply policies to Razor Pages</span></span>

<span data-ttu-id="b43af-221">Политики применяются к Razor страницам с помощью `[Authorize]` атрибута с именем политики.</span><span class="sxs-lookup"><span data-stu-id="b43af-221">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b43af-222">Пример.</span><span class="sxs-lookup"><span data-stu-id="b43af-222">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="b43af-223">Политики также можно применять к Razor страницам с помощью [соглашения об авторизации](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="b43af-223">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="b43af-224">Требования</span><span class="sxs-lookup"><span data-stu-id="b43af-224">Requirements</span></span>

<span data-ttu-id="b43af-225">Требование авторизации — это коллекция параметров данных, которую политика может использовать для вычисления текущего субъекта-пользователя.</span><span class="sxs-lookup"><span data-stu-id="b43af-225">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="b43af-226">В нашей политике "AtLeast21" требование — это единственный параметр &mdash; с минимальным возрастом.</span><span class="sxs-lookup"><span data-stu-id="b43af-226">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="b43af-227">Требование реализует [иаусоризатионрекуиремент](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), который является пустым интерфейсом маркера.</span><span class="sxs-lookup"><span data-stu-id="b43af-227">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="b43af-228">Требование к параметризованному минимальному возрасту можно реализовать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="b43af-228">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="b43af-229">Если политика авторизации содержит несколько требований к авторизации, все требования должны пройти, чтобы оценка политики прошла успешно.</span><span class="sxs-lookup"><span data-stu-id="b43af-229">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="b43af-230">Иными словами, несколько требований к авторизации, добавленных в одну политику авторизации, обрабатываются **и** на основе.</span><span class="sxs-lookup"><span data-stu-id="b43af-230">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="b43af-231">Требование не обязательно должно иметь данные или свойства.</span><span class="sxs-lookup"><span data-stu-id="b43af-231">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="b43af-232">Обработчики авторизации</span><span class="sxs-lookup"><span data-stu-id="b43af-232">Authorization handlers</span></span>

<span data-ttu-id="b43af-233">Обработчик авторизации отвечает за оценку свойств требования.</span><span class="sxs-lookup"><span data-stu-id="b43af-233">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="b43af-234">Обработчик авторизации оценивает требования к предоставленному [аусоризатионхандлерконтекст](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) , чтобы определить, разрешен ли доступ.</span><span class="sxs-lookup"><span data-stu-id="b43af-234">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="b43af-235">Требование может иметь [несколько обработчиков](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="b43af-235">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="b43af-236">Обработчик может наследовать [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), где `TRequirement` является требованием для обработки.</span><span class="sxs-lookup"><span data-stu-id="b43af-236">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="b43af-237">Кроме того, обработчик может реализовать [иаусоризатионхандлер](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) для обработки более чем одного типа требований.</span><span class="sxs-lookup"><span data-stu-id="b43af-237">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="b43af-238">Использование обработчика для одного требования</span><span class="sxs-lookup"><span data-stu-id="b43af-238">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="b43af-239">Ниже приведен пример связи «один к одному», в которой обработчик минимального возраста использует одно требование:</span><span class="sxs-lookup"><span data-stu-id="b43af-239">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="b43af-240">Приведенный выше код определяет, имеет ли текущий участник пользователя дату утверждения о рождении, выданного известным и надежным издателем.</span><span class="sxs-lookup"><span data-stu-id="b43af-240">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="b43af-241">Авторизация не может произойти, если утверждение отсутствует, в этом случае возвращается завершенная задача.</span><span class="sxs-lookup"><span data-stu-id="b43af-241">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="b43af-242">При наличии утверждения возраст пользователя вычисляется.</span><span class="sxs-lookup"><span data-stu-id="b43af-242">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="b43af-243">Если пользователь соответствует минимальному возрасту, заданному требованием, авторизация считается успешной.</span><span class="sxs-lookup"><span data-stu-id="b43af-243">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="b43af-244">Когда авторизация прошла успешно, `context.Succeed` вызывается с удовлетворенным требованием в качестве единственного параметра.</span><span class="sxs-lookup"><span data-stu-id="b43af-244">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="b43af-245">Использование обработчика для нескольких требований</span><span class="sxs-lookup"><span data-stu-id="b43af-245">Use a handler for multiple requirements</span></span>

<span data-ttu-id="b43af-246">Ниже приведен пример связи «один ко многим», в которой обработчик разрешений может управлять тремя различными типами требований:</span><span class="sxs-lookup"><span data-stu-id="b43af-246">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="b43af-247">Предыдущий код проходит по [пендингрекуирементс](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; свойству, содержащему требования, не помеченные как неудачные.</span><span class="sxs-lookup"><span data-stu-id="b43af-247">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="b43af-248">Для `ReadPermission` получения требования пользователь должен быть владельцем или спонсором для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="b43af-248">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="b43af-249">В случае `EditPermission` `DeletePermission` требования или ему необходимо быть владельцем для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="b43af-249">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="b43af-250">Регистрация обработчика</span><span class="sxs-lookup"><span data-stu-id="b43af-250">Handler registration</span></span>

<span data-ttu-id="b43af-251">Обработчики регистрируются в коллекции служб во время настройки.</span><span class="sxs-lookup"><span data-stu-id="b43af-251">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="b43af-252">Пример.</span><span class="sxs-lookup"><span data-stu-id="b43af-252">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="b43af-253">Предыдущий код регистрируется `MinimumAgeHandler` как одноэлементный, вызывая `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="b43af-253">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="b43af-254">Обработчики можно регистрировать с помощью любого встроенного [времени существования службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="b43af-254">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="b43af-255">Что должен возвращать обработчик?</span><span class="sxs-lookup"><span data-stu-id="b43af-255">What should a handler return?</span></span>

<span data-ttu-id="b43af-256">Обратите внимание, что `Handle` метод в [примере обработчика](#security-authorization-handler-example) не возвращает значения.</span><span class="sxs-lookup"><span data-stu-id="b43af-256">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="b43af-257">Как отображается состояние «успешно» или «сбой»?</span><span class="sxs-lookup"><span data-stu-id="b43af-257">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="b43af-258">Обработчик указывает на успешное выполнение путем вызова `context.Succeed(IAuthorizationRequirement requirement)` , передавая требование, которое прошло проверку.</span><span class="sxs-lookup"><span data-stu-id="b43af-258">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="b43af-259">Обработчику обычно не требуется обработка ошибок, так как другие обработчики для такого же требования могут быть выполнены удачно.</span><span class="sxs-lookup"><span data-stu-id="b43af-259">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="b43af-260">Чтобы гарантировать сбой, даже если другие обработчики требований выполняются успешно, вызовите `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="b43af-260">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="b43af-261">Если обработчик вызывает `context.Succeed` или `context.Fail` , все остальные обработчики все еще вызываются.</span><span class="sxs-lookup"><span data-stu-id="b43af-261">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="b43af-262">Это позволяет создавать побочные эффекты, например ведение журнала, которое выполняется, даже если другой обработчик прошел проверку или не прошел требование.</span><span class="sxs-lookup"><span data-stu-id="b43af-262">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="b43af-263">Если задано значение `false` , свойство [инвокехандлерсафтерфаилуре](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (доступно в ASP.NET Core 1,1 и более поздних версиях) сокращено при `context.Fail` вызове обработчиков.</span><span class="sxs-lookup"><span data-stu-id="b43af-263">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="b43af-264">`InvokeHandlersAfterFailure`по умолчанию имеет значение `true` , в этом случае вызываются все обработчики.</span><span class="sxs-lookup"><span data-stu-id="b43af-264">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="b43af-265">Обработчики авторизации вызываются даже в случае сбоя проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="b43af-265">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="b43af-266">Зачем требуется несколько обработчиков для требования?</span><span class="sxs-lookup"><span data-stu-id="b43af-266">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="b43af-267">В случаях, когда требуется, чтобы оценка была в **или** на основе, реализуйте несколько обработчиков для одного требования.</span><span class="sxs-lookup"><span data-stu-id="b43af-267">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="b43af-268">Например, в корпорации Майкрософт есть дверцы, которые открываются только с карточками ключей.</span><span class="sxs-lookup"><span data-stu-id="b43af-268">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="b43af-269">Если вы оставите свой ключ дома, секретарь выводит временную наклейку и открывает дверцу.</span><span class="sxs-lookup"><span data-stu-id="b43af-269">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="b43af-270">В этом сценарии у вас будет одно требование, *буилдинжентри*, но несколько обработчиков, каждый из которых проанализировать одно требование.</span><span class="sxs-lookup"><span data-stu-id="b43af-270">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="b43af-271">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="b43af-271">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="b43af-272">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b43af-272">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="b43af-273">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b43af-273">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="b43af-274">Убедитесь, что оба обработчика [зарегистрированы](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="b43af-274">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="b43af-275">Если любой из обработчиков будет выполнен, когда политика оценивает `BuildingEntryRequirement` , то Вычисление политики будет завершено.</span><span class="sxs-lookup"><span data-stu-id="b43af-275">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="b43af-276">Использование Func для выполнения политики</span><span class="sxs-lookup"><span data-stu-id="b43af-276">Use a func to fulfill a policy</span></span>

<span data-ttu-id="b43af-277">Могут возникнуть ситуации, в которых выполнение политики может быть простым для выражения в коде.</span><span class="sxs-lookup"><span data-stu-id="b43af-277">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="b43af-278">`Func<AuthorizationHandlerContext, bool>`При настройке политики с помощью `RequireAssertion` построителя политик можно указать.</span><span class="sxs-lookup"><span data-stu-id="b43af-278">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="b43af-279">Например, Предыдущая возможность `BadgeEntryHandler` может быть переписана следующим образом:</span><span class="sxs-lookup"><span data-stu-id="b43af-279">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="b43af-280">Доступ к контексту запроса MVC в обработчиках</span><span class="sxs-lookup"><span data-stu-id="b43af-280">Access MVC request context in handlers</span></span>

<span data-ttu-id="b43af-281">`HandleRequirementAsync`Метод, реализуемый в обработчике авторизации, имеет два параметра: и обрабатываемый объект `AuthorizationHandlerContext` `TRequirement` .</span><span class="sxs-lookup"><span data-stu-id="b43af-281">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="b43af-282">Платформы, такие как MVC или SignalR , могут добавлять любой объект в свойство в `Resource` `AuthorizationHandlerContext` для передачи дополнительной информации.</span><span class="sxs-lookup"><span data-stu-id="b43af-282">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="b43af-283">Например, MVC передает экземпляр [аусоризатионфилтерконтекст](/dotnet/api/?term=AuthorizationFilterContext) в `Resource` свойство.</span><span class="sxs-lookup"><span data-stu-id="b43af-283">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="b43af-284">Это свойство предоставляет доступ к `HttpContext` , `RouteData` и всем остальным, предоставляемым MVC и Razor страницами.</span><span class="sxs-lookup"><span data-stu-id="b43af-284">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="b43af-285">Использование `Resource` свойства зависит от платформы.</span><span class="sxs-lookup"><span data-stu-id="b43af-285">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="b43af-286">Использование сведений в `Resource` свойстве ограничивает политики авторизации определенными платформами.</span><span class="sxs-lookup"><span data-stu-id="b43af-286">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="b43af-287">Необходимо привести `Resource` свойство с помощью `is` ключевого слова, а затем подтвердить успешное приведение, чтобы гарантировать, что код не будет завершаться с ошибкой `InvalidCastException` при выполнении в других платформах:</span><span class="sxs-lookup"><span data-stu-id="b43af-287">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
