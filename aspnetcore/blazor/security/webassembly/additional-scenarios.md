---
title: Сценарии обеспечения дополнительной безопасности Blazor WebAssembly для ASP.NET Core
author: guardrex
description: Узнайте, как настроить Blazor WebAssembly для сценариев обеспечения дополнительной безопасности.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 81ab2bb139dfcbea712d4eb51acfc9d7f6767d46
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818837"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="dca45-103">Сценарии обеспечения дополнительной безопасности Blazor WebAssembly для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dca45-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="dca45-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="dca45-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="dca45-105">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="dca45-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="dca45-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> — это <xref:System.Net.Http.DelegatingHandler>, который используется для присоединения маркеров доступа к исходящим экземплярам <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="dca45-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="dca45-107">Токены получаются с помощью службы <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider>, которая регистрируется платформой.</span><span class="sxs-lookup"><span data-stu-id="dca45-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="dca45-108">Если маркер получить невозможно, создается исключение <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>.</span><span class="sxs-lookup"><span data-stu-id="dca45-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="dca45-109">В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> есть <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> метод, с помощью которого можно направить пользователя к поставщику удостоверений для получения нового маркера.</span><span class="sxs-lookup"><span data-stu-id="dca45-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="dca45-110">Для удобства платформа предоставляет <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> предварительно настроенным с базовым адресом приложения как разрешенным URL-адресом.</span><span class="sxs-lookup"><span data-stu-id="dca45-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="dca45-111">**Маркеры доступа добавляются, только если URI запроса находится в базовом URI приложения.**</span><span class="sxs-lookup"><span data-stu-id="dca45-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="dca45-112">Если URI исходящих запросов не находятся в базовом URI приложения, используйте [настраиваемый класс `AuthorizationMessageHandler` (*рекомендуется*)](#custom-authorizationmessagehandler-class) или [настройте `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span><span class="sxs-lookup"><span data-stu-id="dca45-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class (*recommended*)](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="dca45-113">Помимо настройки клиентского приложения для доступа к API сервера, серверный API должен также разрешить запросы независимо от источника (CORS), если клиент и сервер не находятся по одному и тому же базовому адресу.</span><span class="sxs-lookup"><span data-stu-id="dca45-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="dca45-114">Дополнительные сведения о конфигурации CORS на стороне сервера см. в подразделе [Общий доступ к ресурсам независимо от источника (CORS)](#cross-origin-resource-sharing-cors) далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="dca45-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="dca45-115">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="dca45-115">In the following example:</span></span>

* <span data-ttu-id="dca45-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> добавляет <xref:System.Net.Http.IHttpClientFactory> и связанные службы в коллекцию служб и настраивает именованный <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="dca45-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="dca45-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> — это базовый адрес URI ресурса при отправке запросов.</span><span class="sxs-lookup"><span data-stu-id="dca45-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="dca45-118"><xref:System.Net.Http.IHttpClientFactory> предоставляется пакетом NuGet [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http).</span><span class="sxs-lookup"><span data-stu-id="dca45-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="dca45-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> — это <xref:System.Net.Http.DelegatingHandler>, который используется для присоединения маркеров доступа к исходящим экземплярам <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="dca45-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="dca45-120">Маркеры доступа добавляются, только если URI запроса находится в базовом URI приложения.</span><span class="sxs-lookup"><span data-stu-id="dca45-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="dca45-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> создает и настраивает экземпляр <xref:System.Net.Http.HttpClient> для исходящих запросов с использованием конфигурации, соответствующей именованному <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="dca45-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="dca45-122">Для приложения Blazor, основанного на шаблоне проекта, размещенном в Blazor WebAssembly, по умолчанию URI запроса находятся в базовом URI приложений.</span><span class="sxs-lookup"><span data-stu-id="dca45-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="dca45-123">Таким образом, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> в приложении, созданном из шаблона проекта.</span><span class="sxs-lookup"><span data-stu-id="dca45-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="dca45-124">Настроенный объект <xref:System.Net.Http.HttpClient> используется для выполнения авторизованных запросов с помощью шаблона [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch):</span><span class="sxs-lookup"><span data-stu-id="dca45-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="dca45-125">Пользовательский класс `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="dca45-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="dca45-126">*Руководство в этом разделе рекомендуется для клиентских приложений, которые делают исходящие запросы к URI, которые не находятся в базовом URI приложения.*</span><span class="sxs-lookup"><span data-stu-id="dca45-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="dca45-127">В следующем примере пользовательский класс расширяет <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> для использования в качестве <xref:System.Net.Http.DelegatingHandler> для <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="dca45-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="dca45-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> настраивает этот обработчик для авторизации исходящих HTTP-запросов с помощью маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="dca45-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="dca45-129">Маркер доступа прикрепляется, только если по крайней мере один из разрешенных URL-адресов является базовым для URI запроса (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span><span class="sxs-lookup"><span data-stu-id="dca45-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="dca45-130">В `Program.Main` (`Program.cs`) `CustomAuthorizationMessageHandler` регистрируется как служба с ограниченной областью действия и настраивается как <xref:System.Net.Http.DelegatingHandler> для исходящих экземпляров <xref:System.Net.Http.HttpResponseMessage>, созданных с помощью именованного <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="dca45-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="dca45-131">Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dca45-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="dca45-132">Настроенный объект <xref:System.Net.Http.HttpClient> используется для выполнения авторизованных запросов с помощью шаблона [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="dca45-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="dca45-133">Когда клиент создается с помощью метода <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (из пакета [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)), при выполнении запросов к интерфейсу API сервера объекту <xref:System.Net.Http.HttpClient> передаются экземпляры, содержащие маркеры доступа.</span><span class="sxs-lookup"><span data-stu-id="dca45-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="dca45-134">Если URI запроса является относительным URI, как показано в следующем примере (`ExampleAPIMethod`), он объединяется с <xref:System.Net.Http.HttpClient.BaseAddress>, когда клиентское приложение выполняет запрос.</span><span class="sxs-lookup"><span data-stu-id="dca45-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="dca45-135">Настройка `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="dca45-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="dca45-136">Для <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> можно настроить разрешенные URL-адреса, области и URL-адреса возврата с помощью метода <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="dca45-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="dca45-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> настраивает обработчик для авторизации исходящих HTTP-запросов с помощью маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="dca45-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="dca45-138">Маркер доступа прикрепляется, только если по крайней мере один из разрешенных URL-адресов является базовым для URI запроса (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span><span class="sxs-lookup"><span data-stu-id="dca45-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="dca45-139">Если URI запроса является относительным URI, он объединяется с <xref:System.Net.Http.HttpClient.BaseAddress>.</span><span class="sxs-lookup"><span data-stu-id="dca45-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="dca45-140">В следующем примере обработчик <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> настраивает объект <xref:System.Net.Http.HttpClient> в `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dca45-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="dca45-141">Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> назначается по умолчанию следующим образом.</span><span class="sxs-lookup"><span data-stu-id="dca45-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="dca45-142">свойству <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`);</span><span class="sxs-lookup"><span data-stu-id="dca45-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="dca45-143">URL-адресу массива `authorizedUrls`.</span><span class="sxs-lookup"><span data-stu-id="dca45-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="dca45-144">Типизированный `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="dca45-144">Typed `HttpClient`</span></span>

<span data-ttu-id="dca45-145">Можно определить типизированный клиент, который будет обрабатывать все аспекты, связанные с HTTP и получением маркеров, в пределах одного класса.</span><span class="sxs-lookup"><span data-stu-id="dca45-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="dca45-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="dca45-146">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="dca45-147">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `using static BlazorSample.Data;`).</span><span class="sxs-lookup"><span data-stu-id="dca45-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="dca45-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dca45-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="dca45-149">Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dca45-149">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="dca45-150">Компонент `FetchData` (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="dca45-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="dca45-151">Настройка обработчика `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="dca45-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="dca45-152">Обработчик можно дополнительно настроить с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="dca45-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="dca45-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dca45-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="dca45-154">Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> назначается по умолчанию следующим образом.</span><span class="sxs-lookup"><span data-stu-id="dca45-154">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="dca45-155">свойству <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`);</span><span class="sxs-lookup"><span data-stu-id="dca45-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="dca45-156">URL-адресу массива `authorizedUrls`.</span><span class="sxs-lookup"><span data-stu-id="dca45-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="dca45-157">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="dca45-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="dca45-158">Если приложение Blazor WebAssembly обычно использует защищенный клиент <xref:System.Net.Http.HttpClient> по умолчанию, приложение также может выполнять неавторизованные или неаутентифицированные запросы к веб-API путем настройки именованного клиента <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="dca45-158">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="dca45-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dca45-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="dca45-160">Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dca45-160">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="dca45-161">Приведенная выше регистрация является дополнением к существующей регистрации защищенного клиента <xref:System.Net.Http.HttpClient> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dca45-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="dca45-162">Компонент создает клиент <xref:System.Net.Http.HttpClient> на основе <xref:System.Net.Http.IHttpClientFactory> (из пакета [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)) для выполнения не прошедших проверку или неавторизованных запросов:</span><span class="sxs-lookup"><span data-stu-id="dca45-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="dca45-163">Контроллер в интерфейсе API сервера (`WeatherForecastNoAuthenticationController` в предыдущем примере) не помечен атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).</span><span class="sxs-lookup"><span data-stu-id="dca45-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="dca45-164">Решение о том, следует ли использовать безопасный клиент или небезопасный клиент в качестве экземпляра <xref:System.Net.Http.HttpClient> по умолчанию, принимает разработчик.</span><span class="sxs-lookup"><span data-stu-id="dca45-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="dca45-165">При принятии такого решения рекомендуется учесть соотношение неавторизованных и аутентифицированных конечных точек, к которым обращается приложение.</span><span class="sxs-lookup"><span data-stu-id="dca45-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="dca45-166">Если приложение отправляет большинство запросов к безопасным конечным точкам API, используйте аутентифицированный экземпляр <xref:System.Net.Http.HttpClient> в качестве значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dca45-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="dca45-167">В противном случае в качестве значения по умолчанию зарегистрируйте неавторизованный экземпляр <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="dca45-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="dca45-168">Альтернативный подход к использованию <xref:System.Net.Http.IHttpClientFactory> заключается в создании [типизированного клиента](#typed-httpclient) для неаутентифицированного доступа к анонимным конечным точкам.</span><span class="sxs-lookup"><span data-stu-id="dca45-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="dca45-169">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="dca45-169">Request additional access tokens</span></span>

<span data-ttu-id="dca45-170">Маркеры доступа можно получать вручную, вызывая `IAccessTokenProvider.RequestAccessToken`.</span><span class="sxs-lookup"><span data-stu-id="dca45-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="dca45-171">В приведенном ниже примере для чтения данных пользователей и отправки почты приложениям требуются дополнительные области API Microsoft Graph Azure Active Directory (AAD).</span><span class="sxs-lookup"><span data-stu-id="dca45-171">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="dca45-172">После добавления разрешений API Microsoft Graph на портале Azure AAD дополнительные области настраиваются в клиентском приложении.</span><span class="sxs-lookup"><span data-stu-id="dca45-172">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="dca45-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dca45-173">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="dca45-174">Метод `IAccessTokenProvider.RequestToken` предоставляет перегрузку, которая позволяет приложению подготавливать маркер доступа с указанным набором областей.</span><span class="sxs-lookup"><span data-stu-id="dca45-174">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="dca45-175">В компоненте Razor:</span><span class="sxs-lookup"><span data-stu-id="dca45-175">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="dca45-176"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> возвращает:</span><span class="sxs-lookup"><span data-stu-id="dca45-176"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="dca45-177">`true` с пригодным для использования `token`.</span><span class="sxs-lookup"><span data-stu-id="dca45-177">`true` with the `token` for use.</span></span>
* <span data-ttu-id="dca45-178">`false`, если маркер не получен.</span><span class="sxs-lookup"><span data-stu-id="dca45-178">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="dca45-179">`HttpClient` и `HttpRequestMessage` с параметрами запроса API Fetch</span><span class="sxs-lookup"><span data-stu-id="dca45-179">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="dca45-180">При выполнении в WebAssembly в приложении Blazor WebAssembly для настройки запросов можно использовать [`HttpClient`](xref:fundamentals/http-requests) ([документация по API](xref:System.Net.Http.HttpClient)) и <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="dca45-180">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="dca45-181">Например, можно указать метод HTTP и заголовки запроса.</span><span class="sxs-lookup"><span data-stu-id="dca45-181">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="dca45-182">Следующий компонент выполняет запрос `POST` к конечной точке API списка дел на сервере и отображает текст ответа:</span><span class="sxs-lookup"><span data-stu-id="dca45-182">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="dca45-183">Реализация .NET WebAssembly <xref:System.Net.Http.HttpClient> использует [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="dca45-183">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="dca45-184">Извлечение позволяет настроить несколько [параметров, связанных с запросами](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="dca45-184">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="dca45-185">Параметры запроса HTTP на извлечение можно настроить с помощью методов расширения <xref:System.Net.Http.HttpRequestMessage>, приведенных в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="dca45-185">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="dca45-186">Метод расширения</span><span class="sxs-lookup"><span data-stu-id="dca45-186">Extension method</span></span> | <span data-ttu-id="dca45-187">Свойство запроса на извлечение</span><span class="sxs-lookup"><span data-stu-id="dca45-187">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="dca45-188">Вы можете задать дополнительные параметры с помощью более универсального метода расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>.</span><span class="sxs-lookup"><span data-stu-id="dca45-188">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="dca45-189">HTTP-ответ обычно помещается в буфер в приложении Blazor WebAssembly для выполнения операций синхронизации содержимого ответа.</span><span class="sxs-lookup"><span data-stu-id="dca45-189">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="dca45-190">Для выполнения потоковой передачи ответов используйте метод расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> для запроса.</span><span class="sxs-lookup"><span data-stu-id="dca45-190">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="dca45-191">Чтобы включить учетные данные в запрос независимо от источника, используйте метод расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>.</span><span class="sxs-lookup"><span data-stu-id="dca45-191">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="dca45-192">Дополнительные сведения о возможностях Fetch API см. в разделе [Веб-документы MDN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="dca45-192">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="dca45-193">Общий доступ к ресурсам независимо от источника (CORS)</span><span class="sxs-lookup"><span data-stu-id="dca45-193">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="dca45-194">При отправке учетных данных (файлов cookie или заголовков авторизации) в запросах CORS заголовок `Authorization` должен быть разрешен политикой CORS.</span><span class="sxs-lookup"><span data-stu-id="dca45-194">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="dca45-195">Следующая политика включает в себя настройку для следующих элементов:</span><span class="sxs-lookup"><span data-stu-id="dca45-195">The following policy includes configuration for:</span></span>

* <span data-ttu-id="dca45-196">Источники запроса (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="dca45-196">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="dca45-197">Любой метод (команда).</span><span class="sxs-lookup"><span data-stu-id="dca45-197">Any method (verb).</span></span>
* <span data-ttu-id="dca45-198">Заголовки `Content-Type` и `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="dca45-198">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="dca45-199">Чтобы разрешить пользовательский заголовок (например, `x-custom-header`), выведите список заголовков при вызове <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="dca45-199">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="dca45-200">Учетные данные, установленные клиентским кодом JavaScript (для свойства `credentials` задано значение `include`).</span><span class="sxs-lookup"><span data-stu-id="dca45-200">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="dca45-201">Размещенное решение Blazor на основе размещенного шаблона проекта Blazor, использует тот же базовый адрес для клиентских и серверных приложений.</span><span class="sxs-lookup"><span data-stu-id="dca45-201">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="dca45-202">Для <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> клиентского приложения задан универсальный код ресурса (URI) `builder.HostEnvironment.BaseAddress` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dca45-202">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="dca45-203">Конфигурация CORS **не** требуется в конфигурации по умолчанию размещенного приложения, созданного из размещенного шаблона проекта Blazor.</span><span class="sxs-lookup"><span data-stu-id="dca45-203">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="dca45-204">Дополнительным клиентским приложениям, которые не размещаются в серверном проекте и не используют базовый адрес серверного приложения **требуется** конфигурация CORS в серверном проекте.</span><span class="sxs-lookup"><span data-stu-id="dca45-204">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="dca45-205">Дополнительные сведения см. в разделе <xref:security/cors> и компоненте "Тестер HTTP-запросов" примера приложения (`Components/HTTPRequestTester.razor`).</span><span class="sxs-lookup"><span data-stu-id="dca45-205">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="dca45-206">Обработка ошибок при выполнении запросов маркеров</span><span class="sxs-lookup"><span data-stu-id="dca45-206">Handle token request errors</span></span>

<span data-ttu-id="dca45-207">Когда одностраничное приложение (SPA) выполняет проверку подлинности пользователя с помощью OpenID Connect (OIDC), состояние проверки подлинности сохраняется локально в приложении SPA и в поставщике Identity (IP) в виде файла cookie сеанса, который задается в результате предоставления учетных данных пользователем.</span><span class="sxs-lookup"><span data-stu-id="dca45-207">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="dca45-208">Маркеры, которые IP-адрес выдает для пользователя, обычно действительны недолго (как правило, около часа), поэтому клиентское приложение должно регулярно получать новые маркеры.</span><span class="sxs-lookup"><span data-stu-id="dca45-208">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="dca45-209">В противном случае после истечения срока действия предоставленных маркеров будет выполнен выход пользователя из системы.</span><span class="sxs-lookup"><span data-stu-id="dca45-209">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="dca45-210">В большинстве случаев клиенты OIDC могут подготавливать новые маркеры без повторного прохождения проверки подлинности пользователем благодаря сохранению состояния проверки подлинности или сеанса в поставщике удостоверений.</span><span class="sxs-lookup"><span data-stu-id="dca45-210">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="dca45-211">В некоторых случаях клиент не может получить маркер без участия пользователя, например, если по какой-либо причине пользователь явно вышел из поставщика удостоверений.</span><span class="sxs-lookup"><span data-stu-id="dca45-211">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="dca45-212">Такая ситуация возникает, если пользователь переходит по адресу `https://login.microsoftonline.com` и выполняет выход из системы. В этих случаях приложению не становится известно о выходе пользователя из системы немедленно. Любой маркер, который имеется у клиента, может быть уже недействительным.</span><span class="sxs-lookup"><span data-stu-id="dca45-212">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="dca45-213">Кроме того, клиент не может подготовить новый маркер без участия пользователя после истечения срока действия текущего маркера.</span><span class="sxs-lookup"><span data-stu-id="dca45-213">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="dca45-214">Такие сценарии нехарактерны для проверки подлинности на основе маркеров.</span><span class="sxs-lookup"><span data-stu-id="dca45-214">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="dca45-215">Они проистекают из особенностей одностраничных приложений.</span><span class="sxs-lookup"><span data-stu-id="dca45-215">They are part of the nature of SPAs.</span></span> <span data-ttu-id="dca45-216">Одностраничному приложению, использующему файлы cookie, также не удастся вызвать интерфейс API сервера в случае удаления файла cookie для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dca45-216">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="dca45-217">Когда приложение выполняет вызовы API к защищенным ресурсам, необходимо учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="dca45-217">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="dca45-218">Для подготовки нового маркера доступа с целью вызова API пользователю может потребоваться пройти проверку подлинности повторно.</span><span class="sxs-lookup"><span data-stu-id="dca45-218">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="dca45-219">Даже если у клиента есть маркер, который представляется действительным, вызов сервера может завершиться ошибкой из-за того, что маркер был отозван пользователем.</span><span class="sxs-lookup"><span data-stu-id="dca45-219">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="dca45-220">Когда приложение запрашивает маркер, возможны два результата:</span><span class="sxs-lookup"><span data-stu-id="dca45-220">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="dca45-221">Запрос выполняется успешно, и приложение получает действительный маркер.</span><span class="sxs-lookup"><span data-stu-id="dca45-221">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="dca45-222">Запрос завершается ошибкой, и приложение должно повторно провести проверку подлинности пользователя, чтобы получить новый маркер.</span><span class="sxs-lookup"><span data-stu-id="dca45-222">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="dca45-223">При сбое запроса маркера необходимо решить, следует ли сохранить текущее состояние перед выполнением перенаправления.</span><span class="sxs-lookup"><span data-stu-id="dca45-223">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="dca45-224">Ниже приведены возможные подходы в порядке увеличения сложности.</span><span class="sxs-lookup"><span data-stu-id="dca45-224">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="dca45-225">Сохраните текущее состояние страницы в хранилище сеансов.</span><span class="sxs-lookup"><span data-stu-id="dca45-225">Store the current page state in session storage.</span></span> <span data-ttu-id="dca45-226">Во время [события жизненного цикла `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) проверьте, можно ли восстановить состояние, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="dca45-226">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="dca45-227">Добавьте параметр строки запроса и используйте его, чтобы сообщить приложению о необходимости восстановить ранее сохраненное состояние.</span><span class="sxs-lookup"><span data-stu-id="dca45-227">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="dca45-228">Добавьте параметр строки запроса с уникальным идентификатором для сохранения данных в хранилище сеансов без риска конфликтов с другими элементами.</span><span class="sxs-lookup"><span data-stu-id="dca45-228">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="dca45-229">В приведенном ниже примере показано, как выполнить следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="dca45-229">The following example shows how to:</span></span>

* <span data-ttu-id="dca45-230">сохранить состояние перед перенаправлением на страницу входа;</span><span class="sxs-lookup"><span data-stu-id="dca45-230">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="dca45-231">восстановить предыдущее состояние после проверки подлинности с помощью параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="dca45-231">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="dca45-232">Сохранение состояния приложения перед операцией проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dca45-232">Save app state before an authentication operation</span></span>

<span data-ttu-id="dca45-233">Во время операции проверки подлинности существуют случаи, когда необходимо сохранить состояние приложения, прежде чем в браузере будет выполнено перенаправление в поставщик удостоверений.</span><span class="sxs-lookup"><span data-stu-id="dca45-233">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="dca45-234">Например, такая потребность может возникнуть, когда вы используете контейнер состояния и хотите восстановить состояние после успешной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dca45-234">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="dca45-235">Вы можете использовать пользовательский объект состояния проверки подлинности для сохранения состояния приложения или ссылки на него и восстанавливать это состояние после успешного завершения проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dca45-235">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="dca45-236">Такой подход демонстрируется в приведенном ниже примере.</span><span class="sxs-lookup"><span data-stu-id="dca45-236">The following example demonstrates the approach.</span></span>

<span data-ttu-id="dca45-237">В приложении создается класс контейнера состояния со свойствами для хранения значений состояния приложения.</span><span class="sxs-lookup"><span data-stu-id="dca45-237">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="dca45-238">В приведенном ниже примере контейнер используется для хранения значения счетчика для компонента `Counter` шаблона проекта по умолчанию (`Pages/Counter.razor`).</span><span class="sxs-lookup"><span data-stu-id="dca45-238">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="dca45-239">Методы сериализации и десериализации контейнера основаны на <xref:System.Text.Json>.</span><span class="sxs-lookup"><span data-stu-id="dca45-239">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="dca45-240">Компонент `Counter` использует контейнер состояния для хранения значения `currentCount` за пределами компонента:</span><span class="sxs-lookup"><span data-stu-id="dca45-240">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="dca45-241">Создайте `ApplicationAuthenticationState` на основе <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span><span class="sxs-lookup"><span data-stu-id="dca45-241">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="dca45-242">Укажите свойство `Id`, которое служит идентификатором для локально хранящегося состояния.</span><span class="sxs-lookup"><span data-stu-id="dca45-242">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="dca45-243">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="dca45-243">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="dca45-244">Компонент `Authentication` (`Pages/Authentication.razor`) сохраняет и восстанавливает состояние приложения, используя локальное хранилище сеансов, с помощью методов сериализации и десериализации `StateContainer` (`GetStateForLocalStorage` и `SetStateFromLocalStorage`):</span><span class="sxs-lookup"><span data-stu-id="dca45-244">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="dca45-245">В этом примере для проверки подлинности используется Azure Active Directory (AAD).</span><span class="sxs-lookup"><span data-stu-id="dca45-245">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="dca45-246">В `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dca45-246">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="dca45-247">`ApplicationAuthenticationState` настраивается с типом `RemoteAuthenticationState` из библиотеки проверки подлинности Майкрософт (MSAL).</span><span class="sxs-lookup"><span data-stu-id="dca45-247">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="dca45-248">Контейнер состояния регистрируется в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="dca45-248">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="dca45-249">Настройка маршрутов приложения</span><span class="sxs-lookup"><span data-stu-id="dca45-249">Customize app routes</span></span>

<span data-ttu-id="dca45-250">Для представления различных состояний проверки подлинности библиотека [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) по умолчанию использует маршруты, приведенные в таблице ниже.</span><span class="sxs-lookup"><span data-stu-id="dca45-250">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="dca45-251">Маршрут</span><span class="sxs-lookup"><span data-stu-id="dca45-251">Route</span></span>                            | <span data-ttu-id="dca45-252">Цель</span><span class="sxs-lookup"><span data-stu-id="dca45-252">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="dca45-253">Активирует операцию входа.</span><span class="sxs-lookup"><span data-stu-id="dca45-253">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="dca45-254">Обрабатывает результат любой операции входа.</span><span class="sxs-lookup"><span data-stu-id="dca45-254">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="dca45-255">Отображает сообщения об ошибках при сбое операции входа по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="dca45-255">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="dca45-256">Активирует операцию выхода.</span><span class="sxs-lookup"><span data-stu-id="dca45-256">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="dca45-257">Обрабатывает результат любой операции выхода.</span><span class="sxs-lookup"><span data-stu-id="dca45-257">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="dca45-258">Отображает сообщения об ошибках при сбое операции выхода по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="dca45-258">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="dca45-259">Указывает, что пользователь успешно выполнил выход.</span><span class="sxs-lookup"><span data-stu-id="dca45-259">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="dca45-260">Активирует операцию изменения профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="dca45-260">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="dca45-261">Активирует операцию регистрации нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="dca45-261">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="dca45-262">Маршруты, представленные в предыдущей таблице, можно настраивать с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="dca45-262">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="dca45-263">При настройке параметров для предоставления пользовательских маршрутов убедитесь в том, что у приложения есть маршрут для обработки каждого пути.</span><span class="sxs-lookup"><span data-stu-id="dca45-263">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="dca45-264">В приведенном ниже примере все пути имеют префикс `/security`.</span><span class="sxs-lookup"><span data-stu-id="dca45-264">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="dca45-265">Компонент `Authentication` (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="dca45-265">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="dca45-266">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dca45-266">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="dca45-267">Если требуются совершенно разные пути, задайте маршруты, как описано выше, и обработайте <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> с помощью явного параметра действия:</span><span class="sxs-lookup"><span data-stu-id="dca45-267">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="dca45-268">При необходимости можно разделить пользовательский интерфейс на несколько страниц.</span><span class="sxs-lookup"><span data-stu-id="dca45-268">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="dca45-269">Настройка пользовательского интерфейса проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dca45-269">Customize the authentication user interface</span></span>

<span data-ttu-id="dca45-270"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> содержит набор элементов пользовательского интерфейса по умолчанию для каждого состояния проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dca45-270"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="dca45-271">Каждое состояние можно настроить, передав пользовательский объект <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span><span class="sxs-lookup"><span data-stu-id="dca45-271">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="dca45-272">Чтобы настроить текст, отображаемый во время первоначального входа в систему, можно изменить <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> указанным ниже образом.</span><span class="sxs-lookup"><span data-stu-id="dca45-272">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="dca45-273">Компонент `Authentication` (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="dca45-273">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="dca45-274">В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> есть фрагмент, который можно использовать для каждого маршрута проверки подлинности, как показано в приведенной ниже таблице.</span><span class="sxs-lookup"><span data-stu-id="dca45-274">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="dca45-275">Маршрут</span><span class="sxs-lookup"><span data-stu-id="dca45-275">Route</span></span>                            | <span data-ttu-id="dca45-276">Fragment</span><span class="sxs-lookup"><span data-stu-id="dca45-276">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="dca45-277">Настройка пользователя</span><span class="sxs-lookup"><span data-stu-id="dca45-277">Customize the user</span></span>

<span data-ttu-id="dca45-278">Пользователей, привязанных к приложению, можно настраивать.</span><span class="sxs-lookup"><span data-stu-id="dca45-278">Users bound to the app can be customized.</span></span> <span data-ttu-id="dca45-279">В приведенном ниже примере все пользователи, прошедшие проверку подлинности, получают утверждение `amr` для каждого из методов проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dca45-279">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="dca45-280">Создайте класс, расширяющий класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>:</span><span class="sxs-lookup"><span data-stu-id="dca45-280">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="dca45-281">Создайте фабрику, расширяющую <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span><span class="sxs-lookup"><span data-stu-id="dca45-281">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="dca45-282">Зарегистрируйте `CustomAccountFactory` для используемого поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dca45-282">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="dca45-283">Допустима любая из следующих регистраций:</span><span class="sxs-lookup"><span data-stu-id="dca45-283">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="dca45-284"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="dca45-284"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="dca45-285"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="dca45-285"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="dca45-286"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="dca45-286"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="dca45-287">Поддержка предварительной отрисовки с помощью проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dca45-287">Support prerendering with authentication</span></span>

<span data-ttu-id="dca45-288">Выполнив инструкции в одном из разделов, посвященных размещенным приложениям Blazor WebAssembly, выполните приведенные далее действия, чтобы создать приложение, которое:</span><span class="sxs-lookup"><span data-stu-id="dca45-288">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="dca45-289">предварительно отрисовывает пути, не требующие авторизации;</span><span class="sxs-lookup"><span data-stu-id="dca45-289">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="dca45-290">не выполняет предварительную отрисовку путей, требующих авторизации.</span><span class="sxs-lookup"><span data-stu-id="dca45-290">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="dca45-291">В классе `Program` клиентского приложения (`Program.cs`) включите регистрации общих служб в отдельный метод (например, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="dca45-291">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="dca45-292">В `Startup.ConfigureServices` серверного приложения зарегистрируйте следующие дополнительные службы:</span><span class="sxs-lookup"><span data-stu-id="dca45-292">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="dca45-293">В методе `Startup.Configure` серверного приложения замените [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) на [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="dca45-293">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="dca45-294">В серверном приложении создайте папку `Pages`, если она отсутствует.</span><span class="sxs-lookup"><span data-stu-id="dca45-294">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="dca45-295">В папке серверного приложения `Pages` создайте страницу `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="dca45-295">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="dca45-296">Вставьте содержимое из файла `wwwroot/index.html` клиентского приложения в файл `Pages/_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="dca45-296">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="dca45-297">Обновите содержимое файла:</span><span class="sxs-lookup"><span data-stu-id="dca45-297">Update the file's contents:</span></span>

* <span data-ttu-id="dca45-298">Добавьте `@page "_Host"` в начало файла.</span><span class="sxs-lookup"><span data-stu-id="dca45-298">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="dca45-299">Замените тег `<app>Loading...</app>` следующим:</span><span class="sxs-lookup"><span data-stu-id="dca45-299">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="dca45-300">Варианты для размещенных приложений и сторонних поставщиков входа</span><span class="sxs-lookup"><span data-stu-id="dca45-300">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="dca45-301">При аутентификации и авторизации размещенного приложения Blazor WebAssembly в стороннем поставщике доступно несколько вариантов аутентификации пользователя.</span><span class="sxs-lookup"><span data-stu-id="dca45-301">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="dca45-302">Выбор варианта зависит от вашего сценария.</span><span class="sxs-lookup"><span data-stu-id="dca45-302">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="dca45-303">Для получения дополнительной информации см. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="dca45-303">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="dca45-304">Проверка подлинности пользователей для вызова только защищенных сторонних API</span><span class="sxs-lookup"><span data-stu-id="dca45-304">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="dca45-305">Проверьте подлинность пользователя с помощью потока OAuth на стороне клиента в стороннем поставщике API:</span><span class="sxs-lookup"><span data-stu-id="dca45-305">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="dca45-306">В этом сценарии:</span><span class="sxs-lookup"><span data-stu-id="dca45-306">In this scenario:</span></span>

* <span data-ttu-id="dca45-307">Сервер, на котором размещено приложение, не имеет особого значения.</span><span class="sxs-lookup"><span data-stu-id="dca45-307">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="dca45-308">Невозможно обеспечить защиту API на сервере.</span><span class="sxs-lookup"><span data-stu-id="dca45-308">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="dca45-309">Приложение может вызывать только защищенные сторонние интерфейсы API.</span><span class="sxs-lookup"><span data-stu-id="dca45-309">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="dca45-310">Проверка подлинности пользователей в стороннем поставщике и вызов защищенных API на сервере узла и сторонних API</span><span class="sxs-lookup"><span data-stu-id="dca45-310">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="dca45-311">Настройте Identity с помощью стороннего поставщика входа.</span><span class="sxs-lookup"><span data-stu-id="dca45-311">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="dca45-312">Получите токены, необходимые для доступа к сторонним API, и сохраните их.</span><span class="sxs-lookup"><span data-stu-id="dca45-312">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="dca45-313">При входе пользователя в систему Identity собирает маркеры доступа и обновления в рамках процесса проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dca45-313">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="dca45-314">На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.</span><span class="sxs-lookup"><span data-stu-id="dca45-314">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="dca45-315">Использование токена доступа сервера для получения стороннего токена доступа</span><span class="sxs-lookup"><span data-stu-id="dca45-315">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="dca45-316">С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера.</span><span class="sxs-lookup"><span data-stu-id="dca45-316">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="dca45-317">Затем воспользуйтесь сторонним маркером доступа для вызова ресурсов стороннего API непосредственно из Identity в клиенте.</span><span class="sxs-lookup"><span data-stu-id="dca45-317">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="dca45-318">Этот вариант использовать не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="dca45-318">We don't recommend this approach.</span></span> <span data-ttu-id="dca45-319">Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента.</span><span class="sxs-lookup"><span data-stu-id="dca45-319">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="dca45-320">С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента.</span><span class="sxs-lookup"><span data-stu-id="dca45-320">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="dca45-321">Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.</span><span class="sxs-lookup"><span data-stu-id="dca45-321">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="dca45-322">Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.</span><span class="sxs-lookup"><span data-stu-id="dca45-322">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="dca45-323">Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.</span><span class="sxs-lookup"><span data-stu-id="dca45-323">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="dca45-324">Отправка вызовов API с API клиента на API сервера для вызова сторонних API</span><span class="sxs-lookup"><span data-stu-id="dca45-324">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="dca45-325">Отправьте вызов API с API клиента на API сервера.</span><span class="sxs-lookup"><span data-stu-id="dca45-325">Make an API call from the client to the server API.</span></span> <span data-ttu-id="dca45-326">На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.</span><span class="sxs-lookup"><span data-stu-id="dca45-326">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="dca45-327">Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.</span><span class="sxs-lookup"><span data-stu-id="dca45-327">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="dca45-328">Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.</span><span class="sxs-lookup"><span data-stu-id="dca45-328">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="dca45-329">Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.</span><span class="sxs-lookup"><span data-stu-id="dca45-329">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="dca45-330">Использование конечных точек OpenID Connect (OIDC) версии 2.0</span><span class="sxs-lookup"><span data-stu-id="dca45-330">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="dca45-331">В библиотеке проверки подлинности и шаблонах проекта Blazor используются конечные точки OpenID Connect (OIDC) версии 1.0.</span><span class="sxs-lookup"><span data-stu-id="dca45-331">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="dca45-332">Чтобы использовать конечную точку версии 2.0, настройте параметр <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> маркера носителя JWT.</span><span class="sxs-lookup"><span data-stu-id="dca45-332">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="dca45-333">В следующем примере для AAD настраивается версия 2.0 путем добавления сегмента `v2.0` к свойству <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority>:</span><span class="sxs-lookup"><span data-stu-id="dca45-333">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="dca45-334">Кроме того, параметр можно задать в файле параметров приложения (`appsettings.json`):</span><span class="sxs-lookup"><span data-stu-id="dca45-334">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="dca45-335">Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например в случае с поставщиками, не являющимися владельцами AAD, задайте свойство <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> напрямую.</span><span class="sxs-lookup"><span data-stu-id="dca45-335">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="dca45-336">Задайте свойство либо в <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>, либо в файле параметров приложения (`appsettings.json`) с помощью ключа `Authority`.</span><span class="sxs-lookup"><span data-stu-id="dca45-336">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="dca45-337">Список утверждений в маркере идентификатора отличается для конечных точек версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="dca45-337">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="dca45-338">Дополнительные сведения см. в статье [Зачем выполнять обновление до платформы удостоверений Майкрософт (версия 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="dca45-338">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="dca45-339">Настройка и использование gRPC в компонентах</span><span class="sxs-lookup"><span data-stu-id="dca45-339">Configure and use gRPC in components</span></span>

<span data-ttu-id="dca45-340">Чтобы настроить приложение Blazor WebAssembly для использования [платформы ASP.NET Core gRPC](xref:grpc/index), выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="dca45-340">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="dca45-341">Включите gRPC-Web на сервере.</span><span class="sxs-lookup"><span data-stu-id="dca45-341">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="dca45-342">Для получения дополнительной информации см. <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="dca45-342">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="dca45-343">Зарегистрируйте службы gRPC для обработчика сообщений приложения.</span><span class="sxs-lookup"><span data-stu-id="dca45-343">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="dca45-344">В следующем примере обработчик сообщений авторизации приложения настраивается для использования [службы `GreeterClient` из учебника по gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span><span class="sxs-lookup"><span data-stu-id="dca45-344">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="dca45-345">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="dca45-345">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="dca45-346">Поместите файл `.proto` в проект `Shared` размещенного решения Blazor.</span><span class="sxs-lookup"><span data-stu-id="dca45-346">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="dca45-347">Компонент в клиентском приложении может выполнять вызовы gRPC с помощью клиента gRPC (`Pages/Grpc.razor`):</span><span class="sxs-lookup"><span data-stu-id="dca45-347">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="dca45-348">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="dca45-348">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="dca45-349">Чтобы использовать свойство `Status.DebugException`, используйте [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) версии 2.30.0 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="dca45-349">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="dca45-350">Для получения дополнительной информации см. <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="dca45-350">For more information, see <xref:grpc/browser>.</span></span>
