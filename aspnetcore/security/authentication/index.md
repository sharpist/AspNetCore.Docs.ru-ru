---
title: Общие сведения о проверке подлинности в ASP.NET Core
author: mjrousos
description: Узнайте, как работает проверка подлинности в ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: security/authentication/index
ms.openlocfilehash: e9e4ca11d20557666c75b84e56af825d002df0f1
ms.sourcegitcommit: fbd5427293d9ecccc388bd5fd305c2eb8ada7281
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94464007"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="696d8-103">Общие сведения о проверке подлинности в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="696d8-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="696d8-104">Автор: [Майк Роусос (Mike Rousos)](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="696d8-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="696d8-105">Проверка подлинности — это процесс установления личности пользователя.</span><span class="sxs-lookup"><span data-stu-id="696d8-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="696d8-106">[Авторизация](xref:security/authorization/introduction) — это процесс определения, есть ли у пользователя доступ к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="696d8-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="696d8-107">Проверка подлинности в ASP.NET Core выполняется через интерфейс `IAuthenticationService`, применяемый в специальном [ПО промежуточного слоя](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="696d8-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="696d8-108">Служба проверки подлинности использует зарегистрированные обработчики для выполнения связанных с проверкой подлинности действий.</span><span class="sxs-lookup"><span data-stu-id="696d8-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="696d8-109">Примеры таких действий:</span><span class="sxs-lookup"><span data-stu-id="696d8-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="696d8-110">Проверка подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="696d8-110">Authenticating a user.</span></span>
* <span data-ttu-id="696d8-111">Реагирование на ситуацию, когда не прошедший проверку подлинности пользователь пытается обратиться к ресурсу, доступ к которому ограничен.</span><span class="sxs-lookup"><span data-stu-id="696d8-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="696d8-112">Зарегистрированные обработчики проверки подлинности и их параметры конфигурации называются "схемами".</span><span class="sxs-lookup"><span data-stu-id="696d8-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="696d8-113">Схемы проверки подлинности задаются путем регистрации служб проверки подлинности в `Startup.ConfigureServices` следующими способами:</span><span class="sxs-lookup"><span data-stu-id="696d8-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="696d8-114">После вызова `services.AddAuthentication` (например, `AddJwtBearer` или `Add:::no-loc(Cookie):::`) выполняется вызов метода расширения для конкретной схемы.</span><span class="sxs-lookup"><span data-stu-id="696d8-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `Add:::no-loc(Cookie):::`, for example).</span></span> <span data-ttu-id="696d8-115">Эти методы расширения используют [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) для регистрации схем с необходимыми параметрами.</span><span class="sxs-lookup"><span data-stu-id="696d8-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="696d8-116">В более редких случаях выполняется вызов [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) напрямую.</span><span class="sxs-lookup"><span data-stu-id="696d8-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="696d8-117">Например, следующий код позволяет зарегистрировать службы и обработчики для схем проверки подлинности на основе файлов :::no-loc(cookie)::: и носителя JWT:</span><span class="sxs-lookup"><span data-stu-id="696d8-117">For example, the following code registers authentication services and handlers for :::no-loc(cookie)::: and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .Add:::no-loc(Cookie):::(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind(":::no-loc(Cookie):::Settings", options));
```

<span data-ttu-id="696d8-118">В `AddAuthentication` параметр `JwtBearerDefaults.AuthenticationScheme` представляет собой имя схемы, применяемой по умолчанию, когда конкретная схема не запрашивается.</span><span class="sxs-lookup"><span data-stu-id="696d8-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="696d8-119">Если используется множество схем, в политиках (или атрибутах) авторизации можно [указать конкретные схемы](xref:security/authorization/limitingidentitybyscheme), применяемые для проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="696d8-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="696d8-120">В приведенном выше примере можно специально задать использование схемы на основе файлов :::no-loc(cookie):::, указав ее имя (по умолчанию — `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`, однако при вызове `Add:::no-loc(Cookie):::` можно указать другое).</span><span class="sxs-lookup"><span data-stu-id="696d8-120">In the example above, the :::no-loc(cookie)::: authentication scheme could be used by specifying its name (`:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `Add:::no-loc(Cookie):::`).</span></span>

<span data-ttu-id="696d8-121">В некоторых случаях `AddAuthentication` автоматически вызывается другими методами расширения.</span><span class="sxs-lookup"><span data-stu-id="696d8-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="696d8-122">Например, при использовании [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) выполняется внутренний вызов `AddAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="696d8-122">For example, when using [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="696d8-123">ПО промежуточного слоя для проверки подлинности добавляется в `Startup.Configure` путем вызова метода расширения <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> в интерфейсе `IApplicationBuilder` приложения.</span><span class="sxs-lookup"><span data-stu-id="696d8-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="696d8-124">Вызов `UseAuthentication` регистрирует ПО промежуточного слоя, использующее зарегистрированные ранее схемы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="696d8-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="696d8-125">Следует вызывать `UseAuthentication` перед вызовом любого ПО промежуточного слоя, требующего проверки подлинности пользователей.</span><span class="sxs-lookup"><span data-stu-id="696d8-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="696d8-126">При использовании маршрутизации конечных точек метод `UseAuthentication` необходимо вызывать в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="696d8-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="696d8-127">После `UseRouting`, чтобы были доступны сведения о маршрутизации, необходимые для принятия решений о проверке подлинности.</span><span class="sxs-lookup"><span data-stu-id="696d8-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="696d8-128">До `UseEndpoints`, чтобы пользователи проходили проверку подлинности перед доступом к конечным точкам.</span><span class="sxs-lookup"><span data-stu-id="696d8-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="696d8-129">Концепции проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="696d8-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="696d8-130">Схема проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="696d8-130">Authentication scheme</span></span>

<span data-ttu-id="696d8-131">Схема проверки подлинности — это имя, соответствующее следующим элементам:</span><span class="sxs-lookup"><span data-stu-id="696d8-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="696d8-132">обработчик проверки подлинности;</span><span class="sxs-lookup"><span data-stu-id="696d8-132">An authentication handler.</span></span>
* <span data-ttu-id="696d8-133">параметры для настройки конкретного экземпляра обработчика.</span><span class="sxs-lookup"><span data-stu-id="696d8-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="696d8-134">Схемы выполняют роль механизма, определяющего действия проверки подлинности, проверочного запроса и запрета для связанного обработчика.</span><span class="sxs-lookup"><span data-stu-id="696d8-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="696d8-135">Например, политика авторизации может использовать имена схем для указания схемы или схем авторизации, которые должны использоваться для проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="696d8-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="696d8-136">При настройке проверки подлинности обычно задается схема проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="696d8-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="696d8-137">Она применяется, если ресурс не запрашивает конкретную схему.</span><span class="sxs-lookup"><span data-stu-id="696d8-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="696d8-138">Также доступны следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="696d8-138">It's also possible to:</span></span>

* <span data-ttu-id="696d8-139">Можно указать разные схемы по умолчанию для действий проверки подлинности, проверочного запроса и запрета.</span><span class="sxs-lookup"><span data-stu-id="696d8-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="696d8-140">Можно объединить несколько схем в одну, используя [схемы политик](xref:security/authentication/policyschemes).</span><span class="sxs-lookup"><span data-stu-id="696d8-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="696d8-141">Обработчик проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="696d8-141">Authentication handler</span></span>

<span data-ttu-id="696d8-142">Обработчик проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="696d8-142">An authentication handler:</span></span>

* <span data-ttu-id="696d8-143">является типом, который реализует действия схемы;</span><span class="sxs-lookup"><span data-stu-id="696d8-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="696d8-144">является производным от <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> или <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>;</span><span class="sxs-lookup"><span data-stu-id="696d8-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="696d8-145">служит главным образом для проверки подлинности пользователей.</span><span class="sxs-lookup"><span data-stu-id="696d8-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="696d8-146">В зависимости от конфигурации схемы проверки подлинности и контекста входящего запроса обработчики проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="696d8-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="696d8-147">создают объекты <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket>, представляющие удостоверение пользователя, если проверка подлинности прошла успешно;</span><span class="sxs-lookup"><span data-stu-id="696d8-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="696d8-148">возвращают "результат отсутствует" или "сбой", если проверка подлинности не пройдена;</span><span class="sxs-lookup"><span data-stu-id="696d8-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="696d8-149">поддерживают методы для действий проверочного запроса или запрета при попытке пользователей получить доступ к ресурсам в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="696d8-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="696d8-150">отсутствуют права доступа (запрет);</span><span class="sxs-lookup"><span data-stu-id="696d8-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="696d8-151">не пройдена проверка подлинности (проверочный запрос).</span><span class="sxs-lookup"><span data-stu-id="696d8-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="696d8-152">Authenticate</span><span class="sxs-lookup"><span data-stu-id="696d8-152">Authenticate</span></span>

<span data-ttu-id="696d8-153">Действие проверки подлинности в схеме отвечает за получение удостоверения пользователя в зависимости от контекста запроса.</span><span class="sxs-lookup"><span data-stu-id="696d8-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="696d8-154">Оно возвращает объект <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult>, который указывает, выполнена ли проверка подлинности, и, если да, включает удостоверение пользователя в билете проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="696d8-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="696d8-155">См. раздел <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="696d8-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="696d8-156">Примеры проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="696d8-156">Authenticate examples include:</span></span>

* <span data-ttu-id="696d8-157">Схема проверки подлинности на основе :::no-loc(cookie)::: получает удостоверение пользователя из файлов :::no-loc(cookie):::.</span><span class="sxs-lookup"><span data-stu-id="696d8-157">A :::no-loc(cookie)::: authentication scheme constructing the user's identity from :::no-loc(cookie):::s.</span></span>
* <span data-ttu-id="696d8-158">Схема на основе носителя JWT десериализирует и проверяет токен носителя JWT для получения удостоверения пользователя.</span><span class="sxs-lookup"><span data-stu-id="696d8-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="696d8-159">Задача</span><span class="sxs-lookup"><span data-stu-id="696d8-159">Challenge</span></span>

<span data-ttu-id="696d8-160">Проверочный запрос вызывается процессом авторизации, когда пользователь, не прошедший проверку подлинности, запрашивает доступ к конечной точке, требующей проверку.</span><span class="sxs-lookup"><span data-stu-id="696d8-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="696d8-161">Запрос выдается, например, если анонимный пользователь обращается к ресурсу с ограниченным доступом или щелкает какую-то ссылку для входа.</span><span class="sxs-lookup"><span data-stu-id="696d8-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="696d8-162">Процесс авторизации выдает запрос с использованием указанных схем проверки подлинности. Если схемы не заданы, применяется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="696d8-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="696d8-163">См. раздел <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="696d8-163">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="696d8-164">Примеры проверочных запросов:</span><span class="sxs-lookup"><span data-stu-id="696d8-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="696d8-165">Схема проверки подлинности на основе файлов :::no-loc(cookie)::: перенаправляет пользователя на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="696d8-165">A :::no-loc(cookie)::: authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="696d8-166">Схема на основе носителя JWT возвращает результат 401 с заголовком `www-authenticate: bearer`.</span><span class="sxs-lookup"><span data-stu-id="696d8-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="696d8-167">Действие проверочного запроса должно сообщать пользователю, какой механизм проверки подлинности необходим для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="696d8-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="696d8-168">Запрет</span><span class="sxs-lookup"><span data-stu-id="696d8-168">Forbid</span></span>

<span data-ttu-id="696d8-169">Действие запрета в схеме проверки подлинности вызывается процессом авторизации, когда прошедший проверку подлинности пользователь пытается обратиться к ресурсу, к которому у него нет прав доступа.</span><span class="sxs-lookup"><span data-stu-id="696d8-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="696d8-170">См. раздел <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="696d8-170">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="696d8-171">Примеры запрета проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="696d8-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="696d8-172">Схема проверки подлинности на основе файлов :::no-loc(cookie)::: перенаправляет пользователя на страницу с сообщением об отказе в доступе.</span><span class="sxs-lookup"><span data-stu-id="696d8-172">A :::no-loc(cookie)::: authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="696d8-173">Схема на основе носителя JWT возвращает результат 403.</span><span class="sxs-lookup"><span data-stu-id="696d8-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="696d8-174">Настраиваемая схема проверки подлинности перенаправляет пользователя на страницу, где он может запросить доступ к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="696d8-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="696d8-175">Действие запрета может сообщить пользователю следующее:</span><span class="sxs-lookup"><span data-stu-id="696d8-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="696d8-176">Проверка подлинности пройдена.</span><span class="sxs-lookup"><span data-stu-id="696d8-176">They are authenticated.</span></span>
* <span data-ttu-id="696d8-177">Доступ к запрошенному ресурсу не разрешен.</span><span class="sxs-lookup"><span data-stu-id="696d8-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="696d8-178">Сведения о различиях между действиями проверочного запроса и запрета см. по следующим ссылкам:</span><span class="sxs-lookup"><span data-stu-id="696d8-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="696d8-179">[Проверочный запрос и запрет с помощью обработчика ресурсов операций](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span><span class="sxs-lookup"><span data-stu-id="696d8-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="696d8-180">[Различия между проверочным запросом и запретом](xref:security/authorization/secure-data#challenge).</span><span class="sxs-lookup"><span data-stu-id="696d8-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="696d8-181">Поставщики проверки подлинности для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="696d8-181">Authentication providers per tenant</span></span>

<span data-ttu-id="696d8-182">Платформа ASP.NET Core не имеет встроенного решения для проверки подлинности в системе с несколькими клиентами.</span><span class="sxs-lookup"><span data-stu-id="696d8-182">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="696d8-183">Конечно же, клиенты могут сами создать для себя такое решение на основе встроенных функций, но мы рекомендуем использовать для этой цели [Orchard Core](https://www.orchardcore.net/).</span><span class="sxs-lookup"><span data-stu-id="696d8-183">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="696d8-184">Orchard Core — это:</span><span class="sxs-lookup"><span data-stu-id="696d8-184">Orchard Core is:</span></span>

* <span data-ttu-id="696d8-185">модульная платформа с открытым исходным кодом и инфраструктура для приложений с несколькими клиентами, созданная на основе ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="696d8-185">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="696d8-186">система управления содержимым (CMS), созданная на основе этой платформы приложений.</span><span class="sxs-lookup"><span data-stu-id="696d8-186">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="696d8-187">[Этот пример кода для Orchard Core](https://github.com/OrchardCMS/OrchardCore) содержит реализацию поставщиков проверки подлинности отдельно для каждого клиента.</span><span class="sxs-lookup"><span data-stu-id="696d8-187">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="696d8-188">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="696d8-188">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [<span data-ttu-id="696d8-189">Глобальное требование проверки подлинности пользователей</span><span class="sxs-lookup"><span data-stu-id="696d8-189">Globally require authenticated users</span></span>](xref:security/authorization/secure-data#rau)
* [<span data-ttu-id="696d8-190">Описание проблемы, связанной с использованием нескольких схем проверки подлинности, на сайте GitHub</span><span class="sxs-lookup"><span data-stu-id="696d8-190">GitHub issue on using multiple authentication schemes</span></span>](https://github.com/dotnet/aspnetcore/issues/26002)
