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
# <a name="policy-based-authorization-in-aspnet-core"></a>Авторизация на основе политики в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Под крышками [авторизация на основе ролей](xref:security/authorization/roles) и [авторизация на основе утверждений](xref:security/authorization/claims) используют требование, обработчик требований и предварительно настроенную политику. Эти строительные блоки поддерживают выражение оценок авторизации в коде. Результатом является более богатая, многоразовая, проверяемая структура авторизации.

Политика авторизации состоит из одного или нескольких требований. Он зарегистрирован как часть конфигурации службы `Startup.ConfigureServices` авторизации, в методе:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

В предыдущем примере создается политика "AtLeast21". Он имеет одно&mdash;требование минимального возраста, которое поставляется в качестве параметра к требованию.

## <a name="iauthorizationservice"></a>IАвторизацияСервис 

Основная служба, определяющая успешное <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>авторизацию:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

В предыдущем коде освещаются два метода [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>является службой маркеров без методов и механизмом отслеживания успешной авторизации.

Каждый из них <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> отвечает за проверку соответствия требованиям:
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

Класс <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> — это то, что обработчик использует для обозначения того, были ли выполнены требования:

```csharp
 context.Succeed(requirement)
```

Следующий код показывает упрощенную (и аннотированную комментариями) выполнение службы авторизации по умолчанию:

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

Следующий код показывает `ConfigureServices`типичный:

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

Использовать <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` или для авторизации.

## <a name="applying-policies-to-mvc-controllers"></a>Применение политик к контроллерам MVC

Если вы используете страницы Razor, в этом документе смотрите [применяющие политики к страницам Razor.](#applying-policies-to-razor-pages)

Политики применяются к контроллерам с помощью атрибута `[Authorize]` с именем политики. Пример:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Применение политик на страницах Razor

Политики применяются к страницам `[Authorize]` Razor с помощью атрибута с именем политики. Пример:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Политики также могут быть применены к Razor Pages с помощью [конвенции авторизации.](xref:security/authorization/razor-pages-authorization)

## <a name="requirements"></a>Требования

Требование авторизации — это набор параметров данных, которые политика может использовать для оценки текущего принципала пользователя. В нашей политике "AtLeast21" требование представляет&mdash;собой единый параметр минимального возраста. Требование реализует [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), который является пустым интерфейсом маркера. Параметризованное требование минимального возраста может быть выполнено следующим образом:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Если политика авторизации содержит несколько требований к авторизации, все требования должны пройти для того, чтобы оценка политики увенчалась успехом. Другими словами, несколько **требований** к авторизации, добавленные к единой политике авторизации, обрабатываются на идо.

> [!NOTE]
> Требование не должно иметь данные или свойства.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Обработчики авторизации

Обработчик авторизации отвечает за оценку свойств требования. Обработчик авторизации оценивает требования к предоставленному [авторизационномуконтексту,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) чтобы определить, разрешен ли доступ.

Требование может иметь [несколько обработчиков.](#security-authorization-policies-based-multiple-handlers) Обработчик может унаследовать [AuthorizationHandler\<TRequirement>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)где `TRequirement` необходимо обработать требование. Кроме того, обработчик может реализовать [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) для обработки нескольких типов требований.

### <a name="use-a-handler-for-one-requirement"></a>Используйте обработчик для одного требования

<a name="security-authorization-handler-example"></a>

Ниже приводится пример отношений один к одному, в которых обработчик минимального возраста использует одно требование:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Предыдущий код определяет, имеет ли текущий директор пользователя дату рождения, которая была выдана известным и доверенным эмитентом. Авторизация не может произойти, когда претензия отсутствует, и в этом случае завершенная задача возвращается. При предъявлении претензии рассчитывается возраст пользователя. Если пользователь соответствует минимальному возрасту, определенному требованием, авторизация считается успешной. Когда авторизация `context.Succeed` успешно, ссылается с удовлетворенным требованием в качестве единственного параметра.

### <a name="use-a-handler-for-multiple-requirements"></a>Используйте обработчик для нескольких требований

Ниже приводится пример отношения «один к много», в котором обработчик разрешений может обрабатывать три различных типа требований:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Предыдущий код пересекает [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;свойство, содержащее требования, не отмеченные как успешные. Для `ReadPermission` требования пользователь должен быть либо владельцем, либо спонсором для доступа к запрашиваемому ресурсу. В случае требования `EditPermission` `DeletePermission` или требования он или она должны быть владельцем, чтобы получить доступ к запрашиваемому ресурсу.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Регистрация обработчиков

Обработчики регистрируются в коллекции служб во время конфигурации. Пример:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Предыдущий код `MinimumAgeHandler` регистрируется как синглтон, `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`ссылаясь на . Обработчики могут быть зарегистрированы с помощью любого из встроенных [сроков службы.](xref:fundamentals/dependency-injection#service-lifetimes)

## <a name="what-should-a-handler-return"></a>Что должен вернуть обработчик?

Обратите внимание, что метод в `Handle` [примере обработчика](#security-authorization-handler-example) не возвращает значения. Как указывается статус успеха или неудачи?

* Обработчик указывает `context.Succeed(IAuthorizationRequirement requirement)`на успех, вызывая, передавая требование, которое было успешно проверено.

* Обработчику не нужно обрабатывать сбои в целом, так как другие обработчики для того же требования могут преуспеть.

* Чтобы гарантировать сбой, даже если другие обработчики требований преуспевают, позвоните. `context.Fail`

Если обработчик вызывает `context.Succeed` или `context.Fail`все остальные обработчики по-прежнему вызываются. Это позволяет требованиям создавать побочные эффекты, такие как журналирование, что происходит, даже если другой обработчик успешно проверил или не выполнил требование. При наборе `false`в , [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) собственности (доступно в ASP.NET Core 1.1 и `context.Fail` более поздней) короткого замыкания исполнения обработчиков, когда называется. `InvokeHandlersAfterFailure`по умолчанию, `true`и в этом случае все обработчики называются.

> [!NOTE]
> Обработчики авторизации вызываются даже при сбой аутентификации.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Зачем мне нужны несколько обработчиков для требования?

В тех случаях, когда вы хотите, чтобы оценка была на **или** основе, реализуем несколько обработчиков для одного требования. Например, у корпорации Майкрософт есть двери, которые открываются только с помощью ключевых карт. Если вы оставите ключ-карту дома, администратор напечатывает временную наклейку и откроет дверь для вас. В этом сценарии у вас будет одно требование, *BuildingEntry,* но несколько обработчиков, каждый из которых изучает одно требование.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Убедитесь, что оба обработчика [зарегистрированы.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) Если какой-либо обработчик `BuildingEntryRequirement`преуспевает, когда политика оценивает, оценка политики удается.

## <a name="using-a-func-to-fulfill-a-policy"></a>Использование func для выполнения политики

Возможны ситуации, в которых выполнение политики просто выразить в коде. При настройке политики `Func<AuthorizationHandlerContext, bool>` с программой-строителем `RequireAssertion` можно предоставить его.

Например, предыдущий `BadgeEntryHandler` можно было бы переписать следующим образом:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Доступ к контексту запроса MVC в обработчиках

Метод, `HandleRequirementAsync` который вы реализуете в обработчике авторизации, имеет два параметра: an `AuthorizationHandlerContext` и вы обрабатываете. `TRequirement` Такие рамки, как MVC или Jabbr, могут `Resource` добавлять `AuthorizationHandlerContext` любой объект в свойство для передачи дополнительной информации.

Например, MVC передает экземпляр [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) в свойстве. `Resource` Это свойство `HttpContext`предоставляет `RouteData`доступ к , и все остальное, предоставляемые MVC и Razor Pages.

Использование `Resource` свойства является рамочной. Использование информации `Resource` в свойстве ограничивает ваши политики авторизации определенными инфраструктурами. Вы должны `Resource` бросить свойство с помощью ключевого `is` слова, а затем подтвердить, `InvalidCastException` что литые удалось обеспечить ваш код не сбой с при запуске на других средах:

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

Под крышками [авторизация на основе ролей](xref:security/authorization/roles) и [авторизация на основе утверждений](xref:security/authorization/claims) используют требование, обработчик требований и предварительно настроенную политику. Эти строительные блоки поддерживают выражение оценок авторизации в коде. Результатом является более богатая, многоразовая, проверяемая структура авторизации.

Политика авторизации состоит из одного или нескольких требований. Он зарегистрирован как часть конфигурации службы `Startup.ConfigureServices` авторизации, в методе:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

В предыдущем примере создается политика "AtLeast21". Он имеет одно&mdash;требование минимального возраста, которое поставляется в качестве параметра к требованию.

## <a name="iauthorizationservice"></a>IАвторизацияСервис 

Основная служба, определяющая успешное <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>авторизацию:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

В предыдущем коде освещаются два метода [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>является службой маркеров без методов и механизмом отслеживания успешной авторизации.

Каждый из них <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> отвечает за проверку соответствия требованиям:
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

Класс <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> — это то, что обработчик использует для обозначения того, были ли выполнены требования:

```csharp
 context.Succeed(requirement)
```

Следующий код показывает упрощенную (и аннотированную комментариями) выполнение службы авторизации по умолчанию:

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

Следующий код показывает `ConfigureServices`типичный:

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

Использовать <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` или для авторизации.

## <a name="applying-policies-to-mvc-controllers"></a>Применение политик к контроллерам MVC

Если вы используете страницы Razor, в этом документе смотрите [применяющие политики к страницам Razor.](#applying-policies-to-razor-pages)

Политики применяются к контроллерам с помощью атрибута `[Authorize]` с именем политики. Пример:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Применение политик на страницах Razor

Политики применяются к страницам `[Authorize]` Razor с помощью атрибута с именем политики. Пример:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Политики также могут быть применены к Razor Pages с помощью [конвенции авторизации.](xref:security/authorization/razor-pages-authorization)

## <a name="requirements"></a>Требования

Требование авторизации — это набор параметров данных, которые политика может использовать для оценки текущего принципала пользователя. В нашей политике "AtLeast21" требование представляет&mdash;собой единый параметр минимального возраста. Требование реализует [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), который является пустым интерфейсом маркера. Параметризованное требование минимального возраста может быть выполнено следующим образом:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Если политика авторизации содержит несколько требований к авторизации, все требования должны пройти для того, чтобы оценка политики увенчалась успехом. Другими словами, несколько **требований** к авторизации, добавленные к единой политике авторизации, обрабатываются на идо.

> [!NOTE]
> Требование не должно иметь данные или свойства.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Обработчики авторизации

Обработчик авторизации отвечает за оценку свойств требования. Обработчик авторизации оценивает требования к предоставленному [авторизационномуконтексту,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) чтобы определить, разрешен ли доступ.

Требование может иметь [несколько обработчиков.](#security-authorization-policies-based-multiple-handlers) Обработчик может унаследовать [AuthorizationHandler\<TRequirement>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)где `TRequirement` необходимо обработать требование. Кроме того, обработчик может реализовать [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) для обработки нескольких типов требований.

### <a name="use-a-handler-for-one-requirement"></a>Используйте обработчик для одного требования

<a name="security-authorization-handler-example"></a>

Ниже приводится пример отношений один к одному, в которых обработчик минимального возраста использует одно требование:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Предыдущий код определяет, имеет ли текущий директор пользователя дату рождения, которая была выдана известным и доверенным эмитентом. Авторизация не может произойти, когда претензия отсутствует, и в этом случае завершенная задача возвращается. При предъявлении претензии рассчитывается возраст пользователя. Если пользователь соответствует минимальному возрасту, определенному требованием, авторизация считается успешной. Когда авторизация `context.Succeed` успешно, ссылается с удовлетворенным требованием в качестве единственного параметра.

### <a name="use-a-handler-for-multiple-requirements"></a>Используйте обработчик для нескольких требований

Ниже приводится пример отношения «один к много», в котором обработчик разрешений может обрабатывать три различных типа требований:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Предыдущий код пересекает [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;свойство, содержащее требования, не отмеченные как успешные. Для `ReadPermission` требования пользователь должен быть либо владельцем, либо спонсором для доступа к запрашиваемому ресурсу. В случае требования `EditPermission` `DeletePermission` или требования он или она должны быть владельцем, чтобы получить доступ к запрашиваемому ресурсу.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Регистрация обработчиков

Обработчики регистрируются в коллекции служб во время конфигурации. Пример:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Предыдущий код `MinimumAgeHandler` регистрируется как синглтон, `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`ссылаясь на . Обработчики могут быть зарегистрированы с помощью любого из встроенных [сроков службы.](xref:fundamentals/dependency-injection#service-lifetimes)

## <a name="what-should-a-handler-return"></a>Что должен вернуть обработчик?

Обратите внимание, что метод в `Handle` [примере обработчика](#security-authorization-handler-example) не возвращает значения. Как указывается статус успеха или неудачи?

* Обработчик указывает `context.Succeed(IAuthorizationRequirement requirement)`на успех, вызывая, передавая требование, которое было успешно проверено.

* Обработчику не нужно обрабатывать сбои в целом, так как другие обработчики для того же требования могут преуспеть.

* Чтобы гарантировать сбой, даже если другие обработчики требований преуспевают, позвоните. `context.Fail`

Если обработчик вызывает `context.Succeed` или `context.Fail`все остальные обработчики по-прежнему вызываются. Это позволяет требованиям создавать побочные эффекты, такие как журналирование, что происходит, даже если другой обработчик успешно проверил или не выполнил требование. При наборе `false`в , [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) собственности (доступно в ASP.NET Core 1.1 и `context.Fail` более поздней) короткого замыкания исполнения обработчиков, когда называется. `InvokeHandlersAfterFailure`по умолчанию, `true`и в этом случае все обработчики называются.

> [!NOTE]
> Обработчики авторизации вызываются даже при сбой аутентификации.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Зачем мне нужны несколько обработчиков для требования?

В тех случаях, когда вы хотите, чтобы оценка была на **или** основе, реализуем несколько обработчиков для одного требования. Например, у корпорации Майкрософт есть двери, которые открываются только с помощью ключевых карт. Если вы оставите ключ-карту дома, администратор напечатывает временную наклейку и откроет дверь для вас. В этом сценарии у вас будет одно требование, *BuildingEntry,* но несколько обработчиков, каждый из которых изучает одно требование.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Убедитесь, что оба обработчика [зарегистрированы.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) Если какой-либо обработчик `BuildingEntryRequirement`преуспевает, когда политика оценивает, оценка политики удается.

## <a name="using-a-func-to-fulfill-a-policy"></a>Использование func для выполнения политики

Возможны ситуации, в которых выполнение политики просто выразить в коде. При настройке политики `Func<AuthorizationHandlerContext, bool>` с программой-строителем `RequireAssertion` можно предоставить его.

Например, предыдущий `BadgeEntryHandler` можно было бы переписать следующим образом:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Доступ к контексту запроса MVC в обработчиках

Метод, `HandleRequirementAsync` который вы реализуете в обработчике авторизации, имеет два параметра: an `AuthorizationHandlerContext` и вы обрабатываете. `TRequirement` Такие рамки, как MVC или SignalR, могут `Resource` добавлять `AuthorizationHandlerContext` любой объект в свойство для передачи дополнительной информации.

При использовании конечных точек авторизация обычно обрабатывается программой Authorization Middleware. В этом случае `Resource` свойство является <xref:Microsoft.AspNetCore.Http.Endpoint>экземпляром . Конечная точка может быть использована для зондирования базового ресурса, к которому вы перенаправляетесь. Пример:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

При традиционной реутриации или при разрешении в качестве части фильтра авторизации MVC значение `Resource` является экземпляром. <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> Это свойство `HttpContext`предоставляет `RouteData`доступ к , и все остальное, предоставляемые MVC и Razor Pages.

Использование `Resource` свойства является рамочной. Использование информации `Resource` в свойстве ограничивает ваши политики авторизации определенными инфраструктурами. Вы должны `Resource` бросить свойство с помощью ключевого `is` слова, а затем подтвердить, `InvalidCastException` что литые удалось обеспечить ваш код не сбой с при запуске на других средах:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
