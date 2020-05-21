---
<span data-ttu-id="bd282-101">title: "Проверка подлинности и авторизация в ASP.NET Core Blazor" автор: описание: "Сведения о проверке подлинности и авторизации в Blazor".</span><span class="sxs-lookup"><span data-stu-id="bd282-101">title: 'ASP.NET Core Blazor authentication and authorization' author: description: 'Learn about Blazor authentication and authorization scenarios.'</span></span>
<span data-ttu-id="bd282-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bd282-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bd282-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bd282-103">'Blazor'</span></span>
- <span data-ttu-id="bd282-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bd282-104">'Identity'</span></span>
- <span data-ttu-id="bd282-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bd282-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="bd282-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bd282-106">'Razor'</span></span>
- <span data-ttu-id="bd282-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="bd282-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="bd282-108">Проверка подлинности и авторизация в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="bd282-108">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="bd282-109">Авторы: [Стив Сандерсон (Steve Sanderson)](https://github.com/SteveSandersonMS) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bd282-109">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bd282-110">ASP.NET Core поддерживает настройку и администрирование средств безопасности в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="bd282-110">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="bd282-111">Сценарии безопасности для серверных приложений Blazor и приложений Blazor WebAssembly отличаются.</span><span class="sxs-lookup"><span data-stu-id="bd282-111">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="bd282-112">Так как серверные приложения Blazor выполняются на стороне сервера, проверки авторизации могут определить следующее:</span><span class="sxs-lookup"><span data-stu-id="bd282-112">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="bd282-113">параметры пользовательского интерфейса, предоставленные пользователю (например, какие пункты меню доступны пользователю);</span><span class="sxs-lookup"><span data-stu-id="bd282-113">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="bd282-114">правила доступа для приложений и компонентов.</span><span class="sxs-lookup"><span data-stu-id="bd282-114">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="bd282-115">Приложения Blazor WebAssembly выполняются на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="bd282-115">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="bd282-116">В этом случае авторизация используется *только* для определения отображаемых вариантов пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="bd282-116">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="bd282-117">Так как пользователь может изменить или обойти проверки на стороне клиента, приложение Blazor WebAssembly не может применять правила авторизации доступа.</span><span class="sxs-lookup"><span data-stu-id="bd282-117">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="bd282-118">[Соглашения об авторизации Razor Pages](xref:security/authorization/razor-pages-authorization) не применяются к маршрутизируемым компонентам Razor.</span><span class="sxs-lookup"><span data-stu-id="bd282-118">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="bd282-119">Если компонент Razor, не поддерживающий маршрутизацию, [встроен в страницу](xref:blazor/integrate-components#render-components-from-a-page-or-view), соглашения об авторизации страницы оказывают косвенное влияние на компонент Razor, а также на остальную часть содержимого страницы.</span><span class="sxs-lookup"><span data-stu-id="bd282-119">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="bd282-120"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> и <xref:Microsoft.AspNetCore.Identity.UserManager%601> не поддерживаются в компонентах Razor.</span><span class="sxs-lookup"><span data-stu-id="bd282-120"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="bd282-121">Проверка подлинности</span><span class="sxs-lookup"><span data-stu-id="bd282-121">Authentication</span></span>

Blazor<span data-ttu-id="bd282-122"> использует существующие механизмы проверки подлинности ASP.NET Core для установления личности пользователя.</span><span class="sxs-lookup"><span data-stu-id="bd282-122"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="bd282-123">Конкретный механизм зависит от того, как размещается приложение Blazor (приложение Blazor WebAssembly или серверное приложение Blazor).</span><span class="sxs-lookup"><span data-stu-id="bd282-123">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a><span data-ttu-id="bd282-124">Проверка подлинности в Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="bd282-124">Blazor WebAssembly authentication</span></span>

<span data-ttu-id="bd282-125">В приложениях Blazor WebAssembly проверку подлинности можно обойти, так как пользователь может изменять весь код на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="bd282-125">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="bd282-126">Это же справедливо для всех технологий на стороне клиента, включая платформы одностраничного приложения JavaScript или собственных приложений для любой операционной системы.</span><span class="sxs-lookup"><span data-stu-id="bd282-126">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="bd282-127">Добавьте следующий код:</span><span class="sxs-lookup"><span data-stu-id="bd282-127">Add the following:</span></span>

* <span data-ttu-id="bd282-128">Добавьте ссылку на пакет для [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) в файл проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="bd282-128">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="bd282-129">Добавьте пространство имен `Microsoft.AspNetCore.Components.Authorization` в файл *_Imports.razor* приложения.</span><span class="sxs-lookup"><span data-stu-id="bd282-129">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="bd282-130">Вопросы обработки проверки подлинности и реализации встроенной или настраиваемой службы `AuthenticationStateProvider` рассматриваются в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="bd282-130">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="bd282-131">Дополнительные сведения о создании и настройке приложений см. в статье <xref:security/blazor/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="bd282-131">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a><span data-ttu-id="bd282-132">Проверка подлинности Blazor Server</span><span class="sxs-lookup"><span data-stu-id="bd282-132">Blazor Server authentication</span></span>

<span data-ttu-id="bd282-133">Серверные приложения Blazor работают через подключение в реальном времени, созданное с помощью SignalR.</span><span class="sxs-lookup"><span data-stu-id="bd282-133">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="bd282-134">[Проверка подлинности в приложениях на основе SignalR](xref:signalr/authn-and-authz) выполняется при установлении подключения.</span><span class="sxs-lookup"><span data-stu-id="bd282-134">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="bd282-135">Аутентификация может выполняться на основе файлов cookie или других маркеров носителя.</span><span class="sxs-lookup"><span data-stu-id="bd282-135">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="bd282-136">Дополнительные сведения о создании и настройке приложений см. в статье <xref:security/blazor/server/index>.</span><span class="sxs-lookup"><span data-stu-id="bd282-136">For more information on creating apps and configuration, see <xref:security/blazor/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="bd282-137">Служба AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="bd282-137">AuthenticationStateProvider service</span></span>

<span data-ttu-id="bd282-138">Встроенная служба `AuthenticationStateProvider` получает данные о состоянии проверки подлинности из `HttpContext.User` в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bd282-138">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="bd282-139">Так состояние проверки подлинности интегрируется с существующими соответствующими механизмами проверки подлинности ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bd282-139">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="bd282-140">`AuthenticationStateProvider` является базовой службой, которую компоненты `AuthorizeView` и `CascadingAuthenticationState` используют для получения состояния аутентификации.</span><span class="sxs-lookup"><span data-stu-id="bd282-140">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="bd282-141">Обычно вы не используете `AuthenticationStateProvider` напрямую.</span><span class="sxs-lookup"><span data-stu-id="bd282-141">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="bd282-142">Выберите подход на основе [компонента AuthorizeView](#authorizeview-component) или [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter), которые описаны далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="bd282-142">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="bd282-143">Основной недостаток при использовании `AuthenticationStateProvider` напрямую заключается в том, что компонент не получает автоматического уведомления при изменении базовых данных о состоянии аутентификации.</span><span class="sxs-lookup"><span data-stu-id="bd282-143">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="bd282-144">Служба `AuthenticationStateProvider` может предоставить данные <xref:System.Security.Claims.ClaimsPrincipal> о текущем пользователе, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="bd282-144">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="bd282-145">Если `user.Identity.IsAuthenticated` имеет значение `true`, а пользователь является <xref:System.Security.Claims.ClaimsPrincipal>, можно перечислить утверждения и оценить членство в ролях.</span><span class="sxs-lookup"><span data-stu-id="bd282-145">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="bd282-146">Дополнительные сведения о внедрении зависимостей и службах см. в статьях <xref:blazor/dependency-injection> и <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="bd282-146">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="bd282-147">Реализация пользовательского AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="bd282-147">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="bd282-148">Если приложению требуется пользовательский поставщик, реализуйте `AuthenticationStateProvider` и переопределите `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="bd282-148">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="bd282-149">В приложении WebAssembly Blazor служба `CustomAuthStateProvider` регистрируется в `Main` в файле *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bd282-149">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="bd282-150">В серверном приложении Blazor служба `CustomAuthStateProvider` регистрируется в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bd282-150">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="bd282-151">С помощью `CustomAuthStateProvider` в предыдущем примере все пользователи проходят проверку подлинности с использованием имени пользователя `mrfibuli`.</span><span class="sxs-lookup"><span data-stu-id="bd282-151">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="bd282-152">Предоставление состояния аутентификации в качестве каскадного параметра</span><span class="sxs-lookup"><span data-stu-id="bd282-152">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="bd282-153">Если процедурная логика требует данных о состоянии аутентификации, например при выполнении активируемых пользователем действий, для получения таких данных определите каскадный параметр типа `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="bd282-153">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="bd282-154">Если `user.Identity.IsAuthenticated` имеет значение `true`, можно перечислить утверждения и оценить членство в ролях.</span><span class="sxs-lookup"><span data-stu-id="bd282-154">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="bd282-155">Настройте каскадный параметр `Task<AuthenticationState>` с помощью компонентов `AuthorizeRouteView` и `CascadingAuthenticationState` в компоненте `App` (*App.razor*):</span><span class="sxs-lookup"><span data-stu-id="bd282-155">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="bd282-156">В приложении Blazor WebAssembly добавьте службы для параметров и авторизации в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bd282-156">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="bd282-157">В серверном приложении Blazor уже есть службы для параметров и авторизации, поэтому ничего делать не нужно.</span><span class="sxs-lookup"><span data-stu-id="bd282-157">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="bd282-158">Авторизация</span><span class="sxs-lookup"><span data-stu-id="bd282-158">Authorization</span></span>

<span data-ttu-id="bd282-159">После аутентификации пользователя применяются правила *авторизации*, которые определяют доступные этому пользователю действия.</span><span class="sxs-lookup"><span data-stu-id="bd282-159">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="bd282-160">Доступ обычно предоставляется или запрещается в зависимости от следующих аспектов:</span><span class="sxs-lookup"><span data-stu-id="bd282-160">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="bd282-161">выполнена ли аутентификация (выполнен ли вход);</span><span class="sxs-lookup"><span data-stu-id="bd282-161">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="bd282-162">есть ли у пользователя определенная *роль*;</span><span class="sxs-lookup"><span data-stu-id="bd282-162">A user is in a *role*.</span></span>
* <span data-ttu-id="bd282-163">есть ли у пользователя определенное *утверждение*;</span><span class="sxs-lookup"><span data-stu-id="bd282-163">A user has a *claim*.</span></span>
* <span data-ttu-id="bd282-164">выполняются ли требования *политики*.</span><span class="sxs-lookup"><span data-stu-id="bd282-164">A *policy* is satisfied.</span></span>

<span data-ttu-id="bd282-165">Каждый из этих аспектов применяется здесь так же, как в приложениях ASP.NET Core MVC или Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bd282-165">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="bd282-166">Дополнительные сведения о безопасности в ASP.NET Core вы найдете в статьях [о безопасности и Identity в ASP.NET Core](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="bd282-166">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="bd282-167">Компонент AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="bd282-167">AuthorizeView component</span></span>

<span data-ttu-id="bd282-168">Компонент `AuthorizeView` избирательно демонстрирует элементы пользовательского интерфейса в зависимости от того, имеет ли пользователь права на доступ к этим элементам.</span><span class="sxs-lookup"><span data-stu-id="bd282-168">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="bd282-169">Этот подход полезен, если вам нужно просто *отображать* пользователю данные, и не использовать удостоверение пользователя в процедурной логике.</span><span class="sxs-lookup"><span data-stu-id="bd282-169">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="bd282-170">Этот компонент представляет переменную `context` типа `AuthenticationState`, которую можно использовать для доступа к сведениям о пользователе, выполнившем вход:</span><span class="sxs-lookup"><span data-stu-id="bd282-170">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="bd282-171">Также вы можете указать другое содержимое, которое будет отображаться, если пользователь не выполнил аутентификацию:</span><span class="sxs-lookup"><span data-stu-id="bd282-171">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="bd282-172">Компонент `AuthorizeView` можно использовать в компоненте `NavMenu` (*Shared/NavMenu.razor*) в целях отображения элемента списка (`<li>...</li>`) для `NavLink`, однако следует учитывать, что при использовании этого подхода удаляется только элемент списка из отображаемых выходных данных.</span><span class="sxs-lookup"><span data-stu-id="bd282-172">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="bd282-173">Пользователь по-прежнему может переходить к компоненту.</span><span class="sxs-lookup"><span data-stu-id="bd282-173">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="bd282-174">Содержимое тегов `<Authorized>` и `<NotAuthorized>` может включать произвольные элементы, например другие интерактивные компоненты.</span><span class="sxs-lookup"><span data-stu-id="bd282-174">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="bd282-175">Условия авторизации, такие как роли или правила для выбора вариантов пользовательского интерфейса и доступа к ним, описаны в разделе [об авторизации](#authorization).</span><span class="sxs-lookup"><span data-stu-id="bd282-175">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="bd282-176">Если условия авторизации не указаны, `AuthorizeView` использует политику по умолчанию со следующими правилами:</span><span class="sxs-lookup"><span data-stu-id="bd282-176">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="bd282-177">выполнившие аутентификацию (выполнившие вход) пользователи считаются авторизованными;</span><span class="sxs-lookup"><span data-stu-id="bd282-177">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="bd282-178">не выполнившие аутентификацию (не выполнившие вход) пользователи считаются не авторизованными.</span><span class="sxs-lookup"><span data-stu-id="bd282-178">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="bd282-179">Авторизация на основе ролей и политик</span><span class="sxs-lookup"><span data-stu-id="bd282-179">Role-based and policy-based authorization</span></span>

<span data-ttu-id="bd282-180">Компонент `AuthorizeView` поддерживает авторизацию на основе *ролей* или *политик*.</span><span class="sxs-lookup"><span data-stu-id="bd282-180">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="bd282-181">Для авторизации на основе ролей примените параметр `Roles`:</span><span class="sxs-lookup"><span data-stu-id="bd282-181">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="bd282-182">Для получения дополнительной информации см. <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="bd282-182">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="bd282-183">Для авторизации на основе политик примените параметр `Policy`:</span><span class="sxs-lookup"><span data-stu-id="bd282-183">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="bd282-184">Авторизация на основе утверждений считается особым случаем авторизации на основе политик.</span><span class="sxs-lookup"><span data-stu-id="bd282-184">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="bd282-185">Например, вы можете определить политику, которая требует наличия определенного утверждения у пользователя.</span><span class="sxs-lookup"><span data-stu-id="bd282-185">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="bd282-186">Для получения дополнительной информации см. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="bd282-186">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="bd282-187">Эти API могут использоваться в серверных приложениях Blazor и приложениях Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="bd282-187">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="bd282-188">Если не указано ни `Roles`, ни `Policy`, `AuthorizeView` использует политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="bd282-188">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="bd282-189">Содержимое, отображаемое при асинхронной аутентификации</span><span class="sxs-lookup"><span data-stu-id="bd282-189">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="bd282-190"> позволяет *асинхронно* определять состояние проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="bd282-190"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="bd282-191">Основной сценарий для такого подхода — приложение Blazor WebAssembly, которое направляет запрос на проверку подлинности во внешнюю конечную точку.</span><span class="sxs-lookup"><span data-stu-id="bd282-191">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="bd282-192">Пока аутентификация выполняется, `AuthorizeView` по умолчанию не отображает содержимое.</span><span class="sxs-lookup"><span data-stu-id="bd282-192">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="bd282-193">Чтобы содержимое отображалось, используйте элемент `<Authorizing>`:</span><span class="sxs-lookup"><span data-stu-id="bd282-193">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="bd282-194">Такой подход обычно не применим к серверным приложениям Blazor.</span><span class="sxs-lookup"><span data-stu-id="bd282-194">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="bd282-195">Серверные приложения Blazor узнают состояние проверки подлинности, как только оно устанавливается.</span><span class="sxs-lookup"><span data-stu-id="bd282-195">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="bd282-196">Содержимое `Authorizing` можно указать в компоненте `AuthorizeView` для серверного приложения Blazor, но это содержимое никогда не отображается.</span><span class="sxs-lookup"><span data-stu-id="bd282-196">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="bd282-197">Атрибут [Authorize]</span><span class="sxs-lookup"><span data-stu-id="bd282-197">[Authorize] attribute</span></span>

<span data-ttu-id="bd282-198">Атрибут `[Authorize]` можно использовать в компонентах Razor:</span><span class="sxs-lookup"><span data-stu-id="bd282-198">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="bd282-199">Используйте `[Authorize]` только для компонентов `@page`, полученных через маршрутизатор Blazor.</span><span class="sxs-lookup"><span data-stu-id="bd282-199">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="bd282-200">Авторизация выполняется только как аспект маршрутизации и *не* для дочерних компонентов, которые отображаются на странице.</span><span class="sxs-lookup"><span data-stu-id="bd282-200">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="bd282-201">Чтобы разрешить отображение конкретных частей на странице, используйте вместо этого `AuthorizeView`.</span><span class="sxs-lookup"><span data-stu-id="bd282-201">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="bd282-202">Атрибут `[Authorize]` также поддерживает авторизацию на основе ролей или политик.</span><span class="sxs-lookup"><span data-stu-id="bd282-202">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="bd282-203">Для авторизации на основе ролей примените параметр `Roles`:</span><span class="sxs-lookup"><span data-stu-id="bd282-203">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="bd282-204">Для авторизации на основе политик примените параметр `Policy`:</span><span class="sxs-lookup"><span data-stu-id="bd282-204">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="bd282-205">Если не указано ни `Roles`, ни `Policy`, `[Authorize]` использует политику по умолчанию со следующими правилами:</span><span class="sxs-lookup"><span data-stu-id="bd282-205">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="bd282-206">выполнившие аутентификацию (выполнившие вход) пользователи считаются авторизованными;</span><span class="sxs-lookup"><span data-stu-id="bd282-206">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="bd282-207">не выполнившие аутентификацию (не выполнившие вход) пользователи считаются не авторизованными.</span><span class="sxs-lookup"><span data-stu-id="bd282-207">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="bd282-208">Настройка несанкционированного содержимого для компонента маршрутизатора</span><span class="sxs-lookup"><span data-stu-id="bd282-208">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="bd282-209">Компонент `Router` вместе с компонентом `AuthorizeRouteView` позволяет приложению указать пользовательское содержимое для следующих ситуаций:</span><span class="sxs-lookup"><span data-stu-id="bd282-209">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="bd282-210">содержимое не найдено;</span><span class="sxs-lookup"><span data-stu-id="bd282-210">Content isn't found.</span></span>
* <span data-ttu-id="bd282-211">пользователь не удовлетворяет условию `[Authorize]`, которое применено к компоненту</span><span class="sxs-lookup"><span data-stu-id="bd282-211">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="bd282-212">(атрибут `[Authorize]` описан в разделе [Атрибут `[Authorize]`](#authorize-attribute));</span><span class="sxs-lookup"><span data-stu-id="bd282-212">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="bd282-213">выполняется асинхронная аутентификация.</span><span class="sxs-lookup"><span data-stu-id="bd282-213">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="bd282-214">В стандартном шаблоне проекта серверного приложения Blazor есть компонент `App` (*App.razor*) с примером пользовательского содержимого:</span><span class="sxs-lookup"><span data-stu-id="bd282-214">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="bd282-215">Содержимое тегов `<NotFound>`, `<NotAuthorized>` и `<Authorizing>` может включать произвольные элементы, например другие интерактивные компоненты.</span><span class="sxs-lookup"><span data-stu-id="bd282-215">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="bd282-216">Если элемент `<NotAuthorized>` не указан, `AuthorizeRouteView` использует следующее резервное сообщение:</span><span class="sxs-lookup"><span data-stu-id="bd282-216">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="bd282-217">Уведомление об изменении состояния аутентификации</span><span class="sxs-lookup"><span data-stu-id="bd282-217">Notification about authentication state changes</span></span>

<span data-ttu-id="bd282-218">Если приложение определяет, что изменились базовые данные о состоянии проверки подлинности (например, пользователь вышел из системы или другой пользователь изменил роль), [пользовательский AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) может вызвать необязательный метод `NotifyAuthenticationStateChanged` из базового класса `AuthenticationStateProvider`.</span><span class="sxs-lookup"><span data-stu-id="bd282-218">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="bd282-219">Это действие информирует потребителей данных состояния аутентификации (таких, как `AuthorizeView`) о необходимости повторно отображения с учетом новых данных.</span><span class="sxs-lookup"><span data-stu-id="bd282-219">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="bd282-220">Процедурная логика</span><span class="sxs-lookup"><span data-stu-id="bd282-220">Procedural logic</span></span>

<span data-ttu-id="bd282-221">Если приложению нужно проверять правила авторизации в составе процедурной логики, используйте каскадный параметр с типом `Task<AuthenticationState>`, чтобы получить <xref:System.Security.Claims.ClaimsPrincipal> пользователя.</span><span class="sxs-lookup"><span data-stu-id="bd282-221">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="bd282-222">`Task<AuthenticationState>` можно комбинировать для оценки политик с другими службами, например `IAuthorizationService`.</span><span class="sxs-lookup"><span data-stu-id="bd282-222">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="bd282-223">В компоненте приложения Blazor WebAssembly добавьте пространства имен `Microsoft.AspNetCore.Authorization` и `Microsoft.AspNetCore.Components.Authorization`:</span><span class="sxs-lookup"><span data-stu-id="bd282-223">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="bd282-224">Эти пространства имен можно предоставить глобально, добавив их в файл *_Imports.razor* приложения.</span><span class="sxs-lookup"><span data-stu-id="bd282-224">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="bd282-225">Авторизация в приложениях Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="bd282-225">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="bd282-226">В приложениях Blazor WebAssembly авторизацию можно обойти, так как пользователь может изменять весь код на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="bd282-226">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="bd282-227">Это же справедливо для всех технологий на стороне клиента, включая платформы одностраничного приложения JavaScript или собственных приложений для любой операционной системы.</span><span class="sxs-lookup"><span data-stu-id="bd282-227">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="bd282-228">**Всегда выполняйте проверки авторизации на стороне сервера в конечных точках API, к которым обращается клиентское приложение.**</span><span class="sxs-lookup"><span data-stu-id="bd282-228">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="bd282-229">Дополнительные сведения см. в статьях в документе <xref:security/blazor/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="bd282-229">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="bd282-230">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="bd282-230">Troubleshoot errors</span></span>

<span data-ttu-id="bd282-231">Распространенные ошибки</span><span class="sxs-lookup"><span data-stu-id="bd282-231">Common errors:</span></span>

* <span data-ttu-id="bd282-232">**Для авторизации требуется каскадный параметр с типом \<AuthenticationState>. Попробуйте предоставить его с помощью CascadingAuthenticationState.**</span><span class="sxs-lookup"><span data-stu-id="bd282-232">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="bd282-233">**Для `authenticationStateTask` возвращается значение `null`**</span><span class="sxs-lookup"><span data-stu-id="bd282-233">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="bd282-234">Вполне вероятно, что проект не был создан на основе шаблона серверного приложения Blazor с включенной проверкой подлинности.</span><span class="sxs-lookup"><span data-stu-id="bd282-234">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="bd282-235">Примените `<CascadingAuthenticationState>` в качестве оболочки некоторой части дерева пользовательского интерфейса, например в компоненте `App` (*App.razor*), следующим образом:</span><span class="sxs-lookup"><span data-stu-id="bd282-235">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="bd282-236">`CascadingAuthenticationState` предоставляет каскадный параметр `Task<AuthenticationState>`, который он в свою очередь получает из базовой службы внедрения зависимостей `AuthenticationStateProvider`.</span><span class="sxs-lookup"><span data-stu-id="bd282-236">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd282-237">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="bd282-237">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="bd282-238">[Awesome Blazor: проверка подлинности](https://github.com/AdrienTorris/awesome-blazor#authentication) — ссылки на примеры от сообщества</span><span class="sxs-lookup"><span data-stu-id="bd282-238">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
