---
title: Выполнения HTTP-запросов с помощью IHttpClientFactory в ASP.NET Core
author: stevejgordon
description: Сведения об использовании интерфейса IHttpClientFactory для управления логическими экземплярами HttpClient в ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: ae33218d6944c62a08e677592ac0c66f9026b15f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766554"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="ad8a4-103">Выполнения HTTP-запросов с помощью IHttpClientFactory в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ad8a4-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ad8a4-104">Авторы: [Гленн Кондрон (Glenn Condron)](https://github.com/glennc), [Райан Новак (Ryan Nowak)](https://github.com/rynowak), [Стив Гордон (Steve Gordon)](https://github.com/stevejgordon), [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT) и [Кирк Ларкин (Kirk Larkin)](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="ad8a4-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="ad8a4-105"><xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="ad8a4-106">`IHttpClientFactory` предоставляет следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="ad8a4-107">Центральное расположение для именования и настройки логических экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="ad8a4-108">Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="ad8a4-109">Можно зарегистрировать клиент по умолчанию для общего доступа.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="ad8a4-110">Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="ad8a4-111">Предоставление расширений для ПО промежуточного слоя на основе Polly для делегирования обработчиков в `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="ad8a4-112">Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="ad8a4-113">Автоматическое управление позволяет избежать обычных проблем со службой доменных имен (DNS), которые возникают при управлении временем существования `HttpClient` вручную.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="ad8a4-114">Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="ad8a4-115">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ad8a4-116">Пример кода в этой версии раздела использует <xref:System.Text.Json> для десериализации содержимого JSON, возвращаемого в ответах HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="ad8a4-117">Для примеров, использующих `Json.NET` и `ReadAsAsync<T>`, воспользуйтесь средством выбора версии, чтобы выбрать версию 2.x этого раздела.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="ad8a4-118">Шаблоны потребления</span><span class="sxs-lookup"><span data-stu-id="ad8a4-118">Consumption patterns</span></span>

<span data-ttu-id="ad8a4-119">Существует несколько способов использования `IHttpClientFactory` в приложении:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="ad8a4-120">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad8a4-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="ad8a4-121">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="ad8a4-122">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="ad8a4-123">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="ad8a4-124">Оптимальный подход зависит от требований приложения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="ad8a4-125">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad8a4-125">Basic usage</span></span>

<span data-ttu-id="ad8a4-126">`IHttpClientFactory` можно зарегистрировать, вызвав `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="ad8a4-127">`IHttpClientFactory` можно запросить с помощью [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ad8a4-128">Следующий код использует `IHttpClientFactory` для создания экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="ad8a4-129">Подобное использование `IHttpClientFactory` — это хороший способ рефакторинга имеющегося приложения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="ad8a4-130">Он не оказывает влияния на использование `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="ad8a4-131">Там, где в существующем приложении создаются экземпляры `HttpClient`, используйте вызовы к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="ad8a4-132">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-132">Named clients</span></span>

<span data-ttu-id="ad8a4-133">Именованные клиенты являются хорошим выбором в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="ad8a4-134">Приложение требует много отдельных использований `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="ad8a4-135">Многие `HttpClient` имеют другую конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="ad8a4-136">Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="ad8a4-137">В приведенном выше коде клиент регистрируется с:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="ad8a4-138">базовым адресом `https://api.github.com/`;</span><span class="sxs-lookup"><span data-stu-id="ad8a4-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="ad8a4-139">двумя заголовками, необходимыми для работы с API GitHub.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="ad8a4-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="ad8a4-140">CreateClient</span></span>

<span data-ttu-id="ad8a4-141">При каждом вызове <xref:System.Net.Http.IHttpClientFactory.CreateClient*>:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="ad8a4-142">создается новый экземпляр `HttpClient`;</span><span class="sxs-lookup"><span data-stu-id="ad8a4-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="ad8a4-143">вызывается действие настройки.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-143">The configuration action is called.</span></span>

<span data-ttu-id="ad8a4-144">Чтобы создать именованный клиент, передайте его имя в `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="ad8a4-145">В приведенном выше коде в запросе не требуется указывать имя узла.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="ad8a4-146">Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="ad8a4-147">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-147">Typed clients</span></span>

<span data-ttu-id="ad8a4-148">Типизированные клиенты:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-148">Typed clients:</span></span>

* <span data-ttu-id="ad8a4-149">предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="ad8a4-150">Это помогает IntelliSense и компилятору при использовании клиентов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="ad8a4-151">Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="ad8a4-152">Например, один типизированный клиент можно использовать:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="ad8a4-153">для одной серверной конечной точки;</span><span class="sxs-lookup"><span data-stu-id="ad8a4-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="ad8a4-154">для инкапсуляции всей логики, связанной с конечной точкой.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="ad8a4-155">Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="ad8a4-156">Типизированный клиент принимает параметр `HttpClient` в конструкторе:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ad8a4-157">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-157">In the preceding code:</span></span>

* <span data-ttu-id="ad8a4-158">Конфигурация перемещается в типизированный клиент.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="ad8a4-159">Объект `HttpClient` предоставляется в виде открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="ad8a4-160">Можно создать связанные с API методы, которые предоставляют функциональные возможности `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="ad8a4-161">Например, метод `GetAspNetDocsIssues` инкапсулирует код для получения открытых вопросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="ad8a4-162">Следующий код вызывает <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices` для регистрации класса типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="ad8a4-163">Типизированный клиент регистрируется во внедрении зависимостей как временный.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="ad8a4-164">В приведенном выше коде `AddHttpClient` регистрирует `GitHubService` как временную службу.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="ad8a4-165">Эта регистрация использует фабричный метод для следующих задач:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="ad8a4-166">Создайте экземпляр `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="ad8a4-167">Создайте экземпляр `GitHubService`, передав его конструктору экземпляр `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="ad8a4-168">Типизированный клиент можно внедрить и использовать напрямую:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="ad8a4-169">Конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="ad8a4-170">`HttpClient` может быть инкапсулирован в типизированном клиенте.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="ad8a4-171">Вместо предоставления его как свойства определите метод для внутреннего вызова экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="ad8a4-172">В приведенном выше коде `HttpClient` хранится в закрытом поле.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="ad8a4-173">Доступ к `HttpClient` осуществляется с помощью общедоступного метода `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="ad8a4-174">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-174">Generated clients</span></span>

<span data-ttu-id="ad8a4-175">`IHttpClientFactory` можно использовать в сочетании с библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="ad8a4-176">Refit — это библиотека REST для .NET.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="ad8a4-177">Она преобразует REST API в динамические интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="ad8a4-178">Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="ad8a4-179">Для представления внешнего API и его ответа определяются интерфейс и ответ:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="ad8a4-180">Можно добавить типизированный клиент, используя Refit для создания реализации:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="ad8a4-181">При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="ad8a4-182">ПО промежуточного слоя для исходящих запросов</span><span class="sxs-lookup"><span data-stu-id="ad8a4-182">Outgoing request middleware</span></span>

<span data-ttu-id="ad8a4-183">В `HttpClient` существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="ad8a4-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="ad8a4-185">Упрощает определение обработчиков для применения к каждому именованному клиенту.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="ad8a4-186">Поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="ad8a4-187">Каждый из этих обработчиков может выполнять работу до и после исходящего запроса.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="ad8a4-188">Этот шаблон:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-188">This pattern:</span></span>

  * <span data-ttu-id="ad8a4-189">похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="ad8a4-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="ad8a4-190">предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="ad8a4-191">кэширование</span><span class="sxs-lookup"><span data-stu-id="ad8a4-191">caching</span></span>
    * <span data-ttu-id="ad8a4-192">обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="ad8a4-192">error handling</span></span>
    * <span data-ttu-id="ad8a4-193">последовательное упорядочение</span><span class="sxs-lookup"><span data-stu-id="ad8a4-193">serialization</span></span>
    * <span data-ttu-id="ad8a4-194">Ведение журналов</span><span class="sxs-lookup"><span data-stu-id="ad8a4-194">logging</span></span>

<span data-ttu-id="ad8a4-195">Чтобы создать делегированный обработчик, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-195">To create a delegating handler:</span></span>

* <span data-ttu-id="ad8a4-196">Создайте объект, производный от <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="ad8a4-197">Переопределите метод <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="ad8a4-198">Выполните код до передачи запроса следующему обработчику в конвейере:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="ad8a4-199">Предыдущий код проверяет, находится ли заголовок `X-API-KEY` в запросе.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="ad8a4-200">Если `X-API-KEY` отсутствует, возвращается <xref:System.Net.HttpStatusCode.BadRequest>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="ad8a4-201">Можно добавить сразу несколько обработчиков в конфигурацию для `HttpClient` с использованием <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="ad8a4-202">В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="ad8a4-203">`IHttpClientFactory` создает отдельную область внедрения зависимостей для каждого обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="ad8a4-204">Обработчики могут зависеть от служб из любой области.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="ad8a4-205">Службы, которые зависят от обработчиков, удаляются при удалении обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="ad8a4-206">После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="ad8a4-207">Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="ad8a4-208">Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="ad8a4-209">Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="ad8a4-210">Передайте данные в обработчик с помощью [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="ad8a4-211">Используйте <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> для доступа к текущему запросу.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="ad8a4-212">Создайте пользовательский объект хранилища <xref:System.Threading.AsyncLocal`1> для передачи данных.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="ad8a4-213">Использование обработчиков на основе Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-213">Use Polly-based handlers</span></span>

<span data-ttu-id="ad8a4-214">`IHttpClientFactory` интегрируется с библиотекой сторонних разработчиков [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="ad8a4-215">Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="ad8a4-216">Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="ad8a4-217">Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="ad8a4-218">Расширения Polly поддерживают добавление обработчиков на основе Polly клиентам.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="ad8a4-219">Polly нужен пакет NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="ad8a4-220">Обработка временных сбоев</span><span class="sxs-lookup"><span data-stu-id="ad8a4-220">Handle transient faults</span></span>

<span data-ttu-id="ad8a4-221">Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="ad8a4-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> позволяет определить политику для обработки временных ошибок.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="ad8a4-223">Политики, настроенные с помощью `AddTransientHttpErrorPolicy`, обрабатывают следующие ответы:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="ad8a4-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="ad8a4-224">HTTP 5xx</span></span>
* <span data-ttu-id="ad8a4-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="ad8a4-225">HTTP 408</span></span>

<span data-ttu-id="ad8a4-226">`AddTransientHttpErrorPolicy` предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="ad8a4-227">В приведенном выше коде определена политика `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="ad8a4-228">Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="ad8a4-229">Динамический выбор политик</span><span class="sxs-lookup"><span data-stu-id="ad8a4-229">Dynamically select policies</span></span>

<span data-ttu-id="ad8a4-230">Предоставляются методы расширения для добавления обработчиков на основе Polly, например <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="ad8a4-231">Следующая перегрузка `AddPolicyHandler` проверяет запрос для определения применимой политики:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="ad8a4-232">Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="ad8a4-233">Для остальных методов HTTP время ожидания — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="ad8a4-234">Добавление нескольких обработчиков Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="ad8a4-235">Общепринятой практикой является вложение политик Polly:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="ad8a4-236">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-236">In the preceding example:</span></span>

* <span data-ttu-id="ad8a4-237">Добавляются два обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-237">Two handlers are added.</span></span>
* <span data-ttu-id="ad8a4-238">Первый обработчик использует <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>, чтобы добавить политику повтора.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="ad8a4-239">Неудачные запросы выполняются повторно до трех раз.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="ad8a4-240">Второй вызов `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="ad8a4-241">Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="ad8a4-242">Политики размыкателя цепи отслеживают состояние.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="ad8a4-243">Все вызовы через этот клиент имеют одинаковое состояние цепи.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="ad8a4-244">Добавление политик из реестра Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="ad8a4-245">Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="ad8a4-246">В приведенном ниже коде выполняется следующее:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-246">In the following code:</span></span>

* <span data-ttu-id="ad8a4-247">Добавляются политики regular и long.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="ad8a4-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> добавляет политики regular и long из реестра.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="ad8a4-249">Дополнительные сведения о `IHttpClientFactory` и интеграции Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="ad8a4-250">Управление HttpClient и временем существования</span><span class="sxs-lookup"><span data-stu-id="ad8a4-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="ad8a4-251">При каждом вызове `HttpClient` в `CreateClient` возвращается новый экземпляр `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="ad8a4-252"><xref:System.Net.Http.HttpMessageHandler> создается для каждого именованного клиента.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="ad8a4-253">Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="ad8a4-254">`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="ad8a4-255">Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="ad8a4-256">Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="ad8a4-257">Создание лишних обработчиков может привести к задержке подключения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="ad8a4-258">Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения службы доменных имен (DNS).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="ad8a4-259">Время существования обработчика по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="ad8a4-260">Значение по умолчанию можно переопределить для каждого именованного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="ad8a4-261">Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, **не** требующие освобождения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="ad8a4-262">Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="ad8a4-263">`IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="ad8a4-264">До появления `HttpClient` один экземпляр `IHttpClientFactory` часто сохраняли в активном состоянии в течение длительного времени.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="ad8a4-265">После перехода на `IHttpClientFactory` это уже не нужно.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="ad8a4-266">Альтернативы интерфейсу IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="ad8a4-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="ad8a4-267">Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="ad8a4-268">предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;</span><span class="sxs-lookup"><span data-stu-id="ad8a4-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="ad8a4-269">предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="ad8a4-270">Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="ad8a4-271">Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="ad8a4-272">Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="ad8a4-273">По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="ad8a4-274">Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="ad8a4-275">`SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="ad8a4-276">Этот позволяет предотвратить нехватку сокетов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="ad8a4-277">`SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="ad8a4-278">Файлы cookie</span><span class="sxs-lookup"><span data-stu-id="ad8a4-278">Cookies</span></span>

<span data-ttu-id="ad8a4-279">Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="ad8a4-280">Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="ad8a4-281">Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="ad8a4-282">отключите автоматическую обработку файлов cookie;</span><span class="sxs-lookup"><span data-stu-id="ad8a4-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="ad8a4-283">не используйте `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="ad8a4-284">Чтобы отключить автоматическую обработку файлов cookie, вызовите <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="ad8a4-285">Logging</span><span class="sxs-lookup"><span data-stu-id="ad8a4-285">Logging</span></span>

<span data-ttu-id="ad8a4-286">Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="ad8a4-287">Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="ad8a4-288">Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="ad8a4-289">Категория журнала для каждого клиента включает в себя имя клиента.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="ad8a4-290">Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="ad8a4-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="ad8a4-291">Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="ad8a4-292">Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="ad8a4-293">Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="ad8a4-294">Кроме того, журнал ведется в конвейере обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="ad8a4-295">В примере *MyNamedClient* эти сообщения регистрируются с категорией журнала "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="ad8a4-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="ad8a4-296">Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="ad8a4-297">Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="ad8a4-298">Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="ad8a4-299">Сюда входят изменения заголовков запросов или кода состояния ответов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="ad8a4-300">Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="ad8a4-301">Настройка HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ad8a4-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="ad8a4-302">Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="ad8a4-303">При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="ad8a4-304">Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="ad8a4-305">Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="ad8a4-306">Использование IHttpClientFactory в консольном приложении</span><span class="sxs-lookup"><span data-stu-id="ad8a4-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="ad8a4-307">В консольном приложении добавьте в проект следующие ссылки на пакеты:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="ad8a4-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="ad8a4-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="ad8a4-309">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="ad8a4-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="ad8a4-310">Рассмотрим следующий пример:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-310">In the following example:</span></span>

* <span data-ttu-id="ad8a4-311"><xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):</span><span class="sxs-lookup"><span data-stu-id="ad8a4-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="ad8a4-312">`MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="ad8a4-313">`HttpClient` используется для получения веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="ad8a4-314">`Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="ad8a4-315">ПО промежуточного слоя для распространения заголовков</span><span class="sxs-lookup"><span data-stu-id="ad8a4-315">Header propagation middleware</span></span>

<span data-ttu-id="ad8a4-316">Header propagation — это ПО промежуточного слоя ASP.NET Core для распространения HTTP-заголовков из входящего запроса на исходящие запросы HTTP-клиентов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="ad8a4-317">Чтобы использовать распространение заголовков, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-317">To use header propagation:</span></span>

* <span data-ttu-id="ad8a4-318">Укажите ссылку на пакет [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="ad8a4-319">Настройте ПО промежуточного слоя и `HttpClient` в `Startup`:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="ad8a4-320">Клиент включает настроенные заголовки в исходящие запросы:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="ad8a4-321">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ad8a4-321">Additional resources</span></span>

* [<span data-ttu-id="ad8a4-322">Использование HttpClientFactory для реализации устойчивых HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="ad8a4-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="ad8a4-323">Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="ad8a4-324">Реализация шаблона размыкателя цепи</span><span class="sxs-lookup"><span data-stu-id="ad8a4-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="ad8a4-325">Сериализация и десериализация JSON в .NET</span><span class="sxs-lookup"><span data-stu-id="ad8a4-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ad8a4-326">Авторы: [Гленн Кондрон (Glenn Condron)](https://github.com/glennc), [Райан Новак (Ryan Nowak)](https://github.com/rynowak) и [Стив Гордон (Steve Gordon)](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="ad8a4-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="ad8a4-327"><xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="ad8a4-328">Так вы получите следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-328">It offers the following benefits:</span></span>

* <span data-ttu-id="ad8a4-329">Центральное расположение для именования и настройки логических экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="ad8a4-330">Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="ad8a4-331">Можно зарегистрировать клиент по умолчанию для других целей.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="ad8a4-332">Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient` и предоставление расширений для ПО промежуточного слоя на основе Polly для использования этой возможности.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="ad8a4-333">Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler` с целью избежать обычных проблем с DNS, которые возникают при управлении временем существования `HttpClient` вручную.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="ad8a4-334">Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="ad8a4-335">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ad8a4-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="ad8a4-336">Шаблоны потребления</span><span class="sxs-lookup"><span data-stu-id="ad8a4-336">Consumption patterns</span></span>

<span data-ttu-id="ad8a4-337">Существует несколько способов использования `IHttpClientFactory` в приложении:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="ad8a4-338">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad8a4-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="ad8a4-339">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="ad8a4-340">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="ad8a4-341">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="ad8a4-342">Все способы равноценны.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="ad8a4-343">Оптимальный подход зависит от ограничений приложения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="ad8a4-344">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad8a4-344">Basic usage</span></span>

<span data-ttu-id="ad8a4-345">`IHttpClientFactory` можно зарегистрировать путем вызова метода расширения `AddHttpClient` в `IServiceCollection` внутри метода `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="ad8a4-346">После регистрации код может принимать `IHttpClientFactory` в любом месте, куда можно внедрить службу с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ad8a4-347">`IHttpClientFactory` можно использовать для создания экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="ad8a4-348">Подобное использование `IHttpClientFactory` — это отличный способ рефакторинга имеющегося приложения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="ad8a4-349">Он не оказывает влияния на использование `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="ad8a4-350">Там, где в данный момент создаются экземпляры `HttpClient`, используйте вызов к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="ad8a4-351">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-351">Named clients</span></span>

<span data-ttu-id="ad8a4-352">Если для приложения предполагаются разные способы использования `HttpClient`, каждый со своей конфигурацией, можно применять **именованные клиенты**.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="ad8a4-353">Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="ad8a4-354">В приведенном выше коде вызывается клиент `AddHttpClient`, предоставляющий имя *github*.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="ad8a4-355">У клиента есть некоторые настройки по умолчанию &mdash; а именно: базовый адрес и два заголовка, необходимые для работы с API GitHub.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="ad8a4-356">При каждом вызове `CreateClient` создается новый экземпляр `HttpClient` и вызывается действие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="ad8a4-357">Для использования именованного клиента можно передать строковый параметр в `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="ad8a4-358">Укажите имя создаваемого клиента:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="ad8a4-359">В приведенном выше коде в запросе не требуется указывать имя узла.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="ad8a4-360">Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="ad8a4-361">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-361">Typed clients</span></span>

<span data-ttu-id="ad8a4-362">Типизированные клиенты:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-362">Typed clients:</span></span>

* <span data-ttu-id="ad8a4-363">предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="ad8a4-364">Это помогает IntelliSense и компилятору при использовании клиентов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="ad8a4-365">Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="ad8a4-366">Например, для конечной точки серверной части можно использовать один типизированный клиент, который будет содержать всю логику работы с этой конечной точкой.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="ad8a4-367">Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="ad8a4-368">Типизированный клиент принимает параметр `HttpClient` в конструкторе:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ad8a4-369">В приведенном выше коде конфигурация перемещается в типизированный клиент.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="ad8a4-370">Объект `HttpClient` предоставляется в виде открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="ad8a4-371">Можно определить связанные с API методы, которые предоставляют функциональные возможности `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="ad8a4-372">Метод `GetAspNetDocsIssues` инкапсулирует код, необходимый для запроса и анализа последнего открытого выпуска из репозитория GitHub.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="ad8a4-373">Для регистрации типизированного клиента можно использовать универсальный метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices`, указав класс типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="ad8a4-374">Типизированный клиент регистрируется во внедрении зависимостей как временный.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="ad8a4-375">Типизированный клиент можно внедрить и использовать напрямую:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="ad8a4-376">При желании конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="ad8a4-377">Можно полностью инкапсулировать `HttpClient` внутри типизированного клиента.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="ad8a4-378">Вместо предоставления его как свойства можно использовать открытые методы для внутреннего вызова экземпляра `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="ad8a4-379">В приведенном выше коде `HttpClient` хранится как закрытое поле.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="ad8a4-380">Любой доступ для совершения внешних вызовов осуществляется через метод `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="ad8a4-381">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-381">Generated clients</span></span>

<span data-ttu-id="ad8a4-382">`IHttpClientFactory` можно использовать в сочетании с другими библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="ad8a4-383">Refit — это библиотека REST для .NET.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="ad8a4-384">Она преобразует REST API в динамические интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="ad8a4-385">Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="ad8a4-386">Для представления внешнего API и его ответа определяются интерфейс и ответ:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-386">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="ad8a4-387">Можно добавить типизированный клиент, используя Refit для создания реализации:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-387">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="ad8a4-388">При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="ad8a4-389">ПО промежуточного слоя для исходящих запросов</span><span class="sxs-lookup"><span data-stu-id="ad8a4-389">Outgoing request middleware</span></span>

<span data-ttu-id="ad8a4-390">В `HttpClient` уже существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="ad8a4-391">Класс `IHttpClientFactory` упрощает определение обработчиков для применения к каждому именованному клиенту.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="ad8a4-392">Он поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="ad8a4-393">Каждый из этих обработчиков может выполнять работу до и после исходящего запроса.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="ad8a4-394">Этот шаблон похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="ad8a4-395">Шаблон предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая кэширование, обработку ошибок, сериализацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="ad8a4-396">Чтобы создать обработчик, необходимо определить класс, производный от <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="ad8a4-397">Переопределите метод `SendAsync` для выполнения кода до передачи запросов следующему обработчику в конвейере:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="ad8a4-398">В предыдущем коде определяется базовый обработчик.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="ad8a4-399">Он проверяет, включен ли в запрос заголовок `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="ad8a4-400">Если заголовок отсутствует, он может избежать вызовов HTTP и вернуть подходящий ответ.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="ad8a4-401">Во время регистрации можно добавить один или несколько обработчиков в конфигурацию для `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="ad8a4-402">Эта задача выполняется через методы расширения в <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="ad8a4-403">В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="ad8a4-404">`IHttpClientFactory` создает отдельную область внедрения зависимостей для каждого обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="ad8a4-405">Обработчики могут зависеть от служб из любой области.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="ad8a4-406">Службы, которые зависят от обработчиков, удаляются при удалении обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="ad8a4-407">После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="ad8a4-408">Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="ad8a4-409">Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="ad8a4-410">Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="ad8a4-411">Передайте данные в обработчик с помощью `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="ad8a4-412">Используйте `IHttpContextAccessor` для доступа к текущему запросу.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="ad8a4-413">Создайте пользовательский объект хранилища `AsyncLocal` для передачи данных.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="ad8a4-414">Использование обработчиков на основе Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-414">Use Polly-based handlers</span></span>

<span data-ttu-id="ad8a4-415">`IHttpClientFactory` интегрируется с популярной библиотекой сторонних разработчиков под названием [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="ad8a4-416">Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="ad8a4-417">Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="ad8a4-418">Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="ad8a4-419">Расширения Polly:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-419">The Polly extensions:</span></span>

* <span data-ttu-id="ad8a4-420">Поддерживает добавление обработчиков на основе Polly клиентам.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="ad8a4-421">Можно использовать после установки пакета NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="ad8a4-422">Пакет не включен в общую платформу ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="ad8a4-423">Обработка временных сбоев</span><span class="sxs-lookup"><span data-stu-id="ad8a4-423">Handle transient faults</span></span>

<span data-ttu-id="ad8a4-424">Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="ad8a4-425">Используется удобный метод расширения `AddTransientHttpErrorPolicy`, который позволяет определить политику для обработки временных ошибок.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="ad8a4-426">Политики, заданные с помощью этого метода расширения, обрабатывают `HttpRequestException`, ответы HTTP 5xx и ответы HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="ad8a4-427">Расширение `AddTransientHttpErrorPolicy` может быть использовано в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ad8a4-428">Данное расширение предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="ad8a4-429">В приведенном выше коде определена политика `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="ad8a4-430">Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="ad8a4-431">Динамический выбор политик</span><span class="sxs-lookup"><span data-stu-id="ad8a4-431">Dynamically select policies</span></span>

<span data-ttu-id="ad8a4-432">Существуют дополнительные методы расширения, которые можно использовать для добавления обработчиков на основе Polly.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="ad8a4-433">Одним из таких расширений является `AddPolicyHandler` с несколькими перегрузками.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="ad8a4-434">Одна перегрузка разрешает проверку запроса для определения необходимой политики:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="ad8a4-435">Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="ad8a4-436">Для остальных методов HTTP время ожидания — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="ad8a4-437">Добавление нескольких обработчиков Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="ad8a4-438">Общепринятой практикой является вложение политик Polly для предоставления расширенной функциональности:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="ad8a4-439">В приведенном выше примере добавляются два обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="ad8a4-440">Первый использует расширение `AddTransientHttpErrorPolicy`, чтобы добавить политику повтора.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="ad8a4-441">Неудачные запросы выполняются повторно до трех раз.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="ad8a4-442">Второй вызов к `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="ad8a4-443">Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="ad8a4-444">Политики размыкателя цепи отслеживают состояние.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="ad8a4-445">Все вызовы через этот клиент имеют одинаковое состояние цепи.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="ad8a4-446">Добавление политик из реестра Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="ad8a4-447">Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="ad8a4-448">Предоставляется метод расширения, разрешающий добавление обработчика с помощью политики из реестра:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="ad8a4-449">В приведенном выше коде, когда `PolicyRegistry` добавляется в `ServiceCollection`, регистрируются две политики.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="ad8a4-450">Чтобы использовать политику из реестра, применяется метод `AddPolicyHandlerFromRegistry`, который передает имя необходимой политики.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="ad8a4-451">Дополнительные сведения об интеграции `IHttpClientFactory` и Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="ad8a4-452">Управление HttpClient и временем существования</span><span class="sxs-lookup"><span data-stu-id="ad8a4-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="ad8a4-453">При каждом вызове `HttpClient` в `CreateClient` возвращается новый экземпляр `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="ad8a4-454">Для каждого названного клиента существует <xref:System.Net.Http.HttpMessageHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="ad8a4-455">Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="ad8a4-456">`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="ad8a4-457">Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="ad8a4-458">Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="ad8a4-459">Создание лишних обработчиков может привести к задержке подключения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="ad8a4-460">Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения DNS.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="ad8a4-461">Время существования обработчика по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="ad8a4-462">Значение по умолчанию можно переопределить для каждого именованного клиента.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="ad8a4-463">Чтобы переопределить это значение, вызовите <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> в `IHttpClientBuilder`, который возвращается при создании клиента:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="ad8a4-464">Высвобождать клиент не требуется.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="ad8a4-465">Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="ad8a4-466">`IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="ad8a4-467">Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, не требующие высвобождения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="ad8a4-468">До появления `HttpClient` один экземпляр `IHttpClientFactory` часто сохраняли в активном состоянии в течение длительного времени.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="ad8a4-469">После перехода на `IHttpClientFactory` это уже не нужно.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="ad8a4-470">Альтернативы интерфейсу IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="ad8a4-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="ad8a4-471">Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="ad8a4-472">предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;</span><span class="sxs-lookup"><span data-stu-id="ad8a4-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="ad8a4-473">предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="ad8a4-474">Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="ad8a4-475">Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="ad8a4-476">Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="ad8a4-477">По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="ad8a4-478">Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="ad8a4-479">`SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="ad8a4-480">Этот позволяет предотвратить нехватку сокетов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="ad8a4-481">`SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="ad8a4-482">Файлы cookie</span><span class="sxs-lookup"><span data-stu-id="ad8a4-482">Cookies</span></span>

<span data-ttu-id="ad8a4-483">Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="ad8a4-484">Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="ad8a4-485">Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="ad8a4-486">отключите автоматическую обработку файлов cookie;</span><span class="sxs-lookup"><span data-stu-id="ad8a4-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="ad8a4-487">не используйте `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="ad8a4-488">Чтобы отключить автоматическую обработку файлов cookie, вызовите <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="ad8a4-489">Logging</span><span class="sxs-lookup"><span data-stu-id="ad8a4-489">Logging</span></span>

<span data-ttu-id="ad8a4-490">Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="ad8a4-491">Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="ad8a4-492">Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="ad8a4-493">Категория журнала для каждого клиента включает в себя имя клиента.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="ad8a4-494">Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="ad8a4-495">Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="ad8a4-496">Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="ad8a4-497">Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="ad8a4-498">Кроме того, журнал ведется в конвейере обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="ad8a4-499">В примере *MyNamedClient* эти сообщения вносятся в журнал по категории журнала `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="ad8a4-500">Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса по сети.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="ad8a4-501">Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="ad8a4-502">Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="ad8a4-503">Сюда входят, например, изменения заголовков запросов или кода состояния ответов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="ad8a4-504">Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам при необходимости.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="ad8a4-505">Настройка HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ad8a4-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="ad8a4-506">Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="ad8a4-507">При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="ad8a4-508">Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="ad8a4-509">Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="ad8a4-510">Использование IHttpClientFactory в консольном приложении</span><span class="sxs-lookup"><span data-stu-id="ad8a4-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="ad8a4-511">В консольном приложении добавьте в проект следующие ссылки на пакеты:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="ad8a4-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="ad8a4-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="ad8a4-513">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="ad8a4-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="ad8a4-514">Рассмотрим следующий пример:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-514">In the following example:</span></span>

* <span data-ttu-id="ad8a4-515"><xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):</span><span class="sxs-lookup"><span data-stu-id="ad8a4-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="ad8a4-516">`MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="ad8a4-517">`HttpClient` используется для получения веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="ad8a4-518">`Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="ad8a4-519">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ad8a4-519">Additional resources</span></span>

* [<span data-ttu-id="ad8a4-520">Использование HttpClientFactory для реализации устойчивых HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="ad8a4-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="ad8a4-521">Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="ad8a4-522">Реализация шаблона размыкателя цепи</span><span class="sxs-lookup"><span data-stu-id="ad8a4-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="ad8a4-523">Авторы: [Гленн Кондрон (Glenn Condron)](https://github.com/glennc), [Райан Новак (Ryan Nowak)](https://github.com/rynowak) и [Стив Гордон (Steve Gordon)](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="ad8a4-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="ad8a4-524"><xref:System.Net.Http.IHttpClientFactory> можно зарегистрировать и использовать для настройки и создания экземпляров <xref:System.Net.Http.HttpClient> в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="ad8a4-525">Так вы получите следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-525">It offers the following benefits:</span></span>

* <span data-ttu-id="ad8a4-526">Центральное расположение для именования и настройки логических экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="ad8a4-527">Например, можно зарегистрировать и настроить клиент *github* для доступа к [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="ad8a4-528">Можно зарегистрировать клиент по умолчанию для других целей.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="ad8a4-529">Кодификация концепции исходящего ПО промежуточного слоя путем делегирования обработчиков в `HttpClient` и предоставление расширений для ПО промежуточного слоя на основе Polly для использования этой возможности.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="ad8a4-530">Управление созданием пулов и временем существования базовых экземпляров `HttpClientMessageHandler` с целью избежать обычных проблем с DNS, которые возникают при управлении временем существования `HttpClient` вручную.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="ad8a4-531">Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="ad8a4-532">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ad8a4-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ad8a4-533">предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ad8a4-533">Prerequisites</span></span>

<span data-ttu-id="ad8a4-534">Для проектов, предназначенных для .NET Framework, необходимо установить пакет NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="ad8a4-535">Пакет [ уже включен в проекты, предназначенные для .NET Core и ссылающиеся на ](xref:fundamentals/metapackage-app)метапакет Microsoft.AspNetCore.App`Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="ad8a4-536">Шаблоны потребления</span><span class="sxs-lookup"><span data-stu-id="ad8a4-536">Consumption patterns</span></span>

<span data-ttu-id="ad8a4-537">Существует несколько способов использования `IHttpClientFactory` в приложении:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="ad8a4-538">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad8a4-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="ad8a4-539">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="ad8a4-540">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="ad8a4-541">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="ad8a4-542">Все способы равноценны.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="ad8a4-543">Оптимальный подход зависит от ограничений приложения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="ad8a4-544">Основное использование</span><span class="sxs-lookup"><span data-stu-id="ad8a4-544">Basic usage</span></span>

<span data-ttu-id="ad8a4-545">`IHttpClientFactory` можно зарегистрировать путем вызова метода расширения `AddHttpClient` в `IServiceCollection` внутри метода `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="ad8a4-546">После регистрации код может принимать `IHttpClientFactory` в любом месте, куда можно внедрить службу с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ad8a4-547">`IHttpClientFactory` можно использовать для создания экземпляра `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="ad8a4-548">Подобное использование `IHttpClientFactory` — это отличный способ рефакторинга имеющегося приложения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="ad8a4-549">Он не оказывает влияния на использование `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="ad8a4-550">Там, где в данный момент создаются экземпляры `HttpClient`, используйте вызов к <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="ad8a4-551">Именованные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-551">Named clients</span></span>

<span data-ttu-id="ad8a4-552">Если для приложения предполагаются разные способы использования `HttpClient`, каждый со своей конфигурацией, можно применять **именованные клиенты**.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="ad8a4-553">Конфигурацию для именованного клиента `HttpClient` можно указать во время регистрации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="ad8a4-554">В приведенном выше коде вызывается клиент `AddHttpClient`, предоставляющий имя *github*.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="ad8a4-555">У клиента есть некоторые настройки по умолчанию &mdash; а именно: базовый адрес и два заголовка, необходимые для работы с API GitHub.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="ad8a4-556">При каждом вызове `CreateClient` создается новый экземпляр `HttpClient` и вызывается действие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="ad8a4-557">Для использования именованного клиента можно передать строковый параметр в `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="ad8a4-558">Укажите имя создаваемого клиента:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="ad8a4-559">В приведенном выше коде в запросе не требуется указывать имя узла.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="ad8a4-560">Достаточно передать только путь, так как используется базовый адрес, заданный для клиента.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="ad8a4-561">Типизированные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-561">Typed clients</span></span>

<span data-ttu-id="ad8a4-562">Типизированные клиенты:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-562">Typed clients:</span></span>

* <span data-ttu-id="ad8a4-563">предоставляют те же возможности, что и именованные клиенты, без необходимости использовать строки в качестве ключей.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="ad8a4-564">Это помогает IntelliSense и компилятору при использовании клиентов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="ad8a4-565">Они предоставляют единое расположение для настройки и взаимодействия с конкретным клиентом `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="ad8a4-566">Например, для конечной точки серверной части можно использовать один типизированный клиент, который будет содержать всю логику работы с этой конечной точкой.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="ad8a4-567">Поддерживаются работа с внедрением зависимостей и возможность вставки в нужное место в приложении.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="ad8a4-568">Типизированный клиент принимает параметр `HttpClient` в конструкторе:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ad8a4-569">В приведенном выше коде конфигурация перемещается в типизированный клиент.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="ad8a4-570">Объект `HttpClient` предоставляется в виде открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="ad8a4-571">Можно определить связанные с API методы, которые предоставляют функциональные возможности `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="ad8a4-572">Метод `GetAspNetDocsIssues` инкапсулирует код, необходимый для запроса и анализа последнего открытого выпуска из репозитория GitHub.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="ad8a4-573">Для регистрации типизированного клиента можно использовать универсальный метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> в `Startup.ConfigureServices`, указав класс типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="ad8a4-574">Типизированный клиент регистрируется во внедрении зависимостей как временный.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="ad8a4-575">Типизированный клиент можно внедрить и использовать напрямую:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="ad8a4-576">При желании конфигурацию для типизированного клиента можно указать во время регистрации в `Startup.ConfigureServices`, а не в конструкторе типизированного клиента:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="ad8a4-577">Можно полностью инкапсулировать `HttpClient` внутри типизированного клиента.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="ad8a4-578">Вместо предоставления его как свойства можно использовать открытые методы для внутреннего вызова экземпляра `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="ad8a4-579">В приведенном выше коде `HttpClient` хранится как закрытое поле.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="ad8a4-580">Любой доступ для совершения внешних вызовов осуществляется через метод `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="ad8a4-581">Созданные клиенты</span><span class="sxs-lookup"><span data-stu-id="ad8a4-581">Generated clients</span></span>

<span data-ttu-id="ad8a4-582">`IHttpClientFactory` можно использовать в сочетании с другими библиотеками сторонних разработчиков, например [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="ad8a4-583">Refit — это библиотека REST для .NET.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="ad8a4-584">Она преобразует REST API в динамические интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="ad8a4-585">Реализация интерфейса формируется динамически с помощью `RestService` с использованием `HttpClient` для совершения внешних вызовов HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="ad8a4-586">Для представления внешнего API и его ответа определяются интерфейс и ответ:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-586">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="ad8a4-587">Можно добавить типизированный клиент, используя Refit для создания реализации:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-587">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="ad8a4-588">При необходимости можно использовать определенный интерфейс с реализацией, предоставленной внедрением зависимостей и Refit:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="ad8a4-589">ПО промежуточного слоя для исходящих запросов</span><span class="sxs-lookup"><span data-stu-id="ad8a4-589">Outgoing request middleware</span></span>

<span data-ttu-id="ad8a4-590">В `HttpClient` уже существует концепция делегирования обработчиков, которые можно связать друг с другом для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="ad8a4-591">Класс `IHttpClientFactory` упрощает определение обработчиков для применения к каждому именованному клиенту.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="ad8a4-592">Он поддерживает регистрацию и объединение в цепочки нескольких обработчиков для создания конвейера ПО промежуточного слоя для исходящих запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="ad8a4-593">Каждый из этих обработчиков может выполнять работу до и после исходящего запроса.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="ad8a4-594">Этот шаблон похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="ad8a4-595">Шаблон предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая кэширование, обработку ошибок, сериализацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="ad8a4-596">Чтобы создать обработчик, необходимо определить класс, производный от <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="ad8a4-597">Переопределите метод `SendAsync` для выполнения кода до передачи запросов следующему обработчику в конвейере:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="ad8a4-598">В предыдущем коде определяется базовый обработчик.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="ad8a4-599">Он проверяет, включен ли в запрос заголовок `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="ad8a4-600">Если заголовок отсутствует, он может избежать вызовов HTTP и вернуть подходящий ответ.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="ad8a4-601">Во время регистрации можно добавить один или несколько обработчиков в конфигурацию для `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="ad8a4-602">Эта задача выполняется через методы расширения в <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="ad8a4-603">В приведенном выше коде `ValidateHeaderHandler` регистрируется с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="ad8a4-604">Обработчик **должен** регистрироваться во внедрении зависимостей как временная служба, а не ограниченная.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="ad8a4-605">Если обработчик зарегистрирован в качестве службы с областью действия и все службы, от которых зависит этот обработчик, освобождаются:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="ad8a4-606">Службы обработчика могли быть удалены, прежде чем обработчик вышел из области действия.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="ad8a4-607">Освобожденные службы обработчика приводят к его сбою.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="ad8a4-608">После регистрации можно вызвать <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, передав тип обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="ad8a4-609">Можно зарегистрировать несколько обработчиков в порядке, в котором они должны выполняться.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="ad8a4-610">Каждый обработчик содержит следующий обработчик, пока последний `HttpClientHandler` не выполнит запрос:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="ad8a4-611">Используйте один из следующих методов для предоставления общего доступа к состоянию отдельных запросов с помощью обработчиков сообщений:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="ad8a4-612">Передайте данные в обработчик с помощью `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="ad8a4-613">Используйте `IHttpContextAccessor` для доступа к текущему запросу.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="ad8a4-614">Создайте пользовательский объект хранилища `AsyncLocal` для передачи данных.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="ad8a4-615">Использование обработчиков на основе Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-615">Use Polly-based handlers</span></span>

<span data-ttu-id="ad8a4-616">`IHttpClientFactory` интегрируется с популярной библиотекой сторонних разработчиков под названием [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="ad8a4-617">Polly — это комплексная библиотека, обеспечивающая отказоустойчивость и обработку временных сбоев в .NET.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="ad8a4-618">Она позволяет разработчикам выражать политики, например политику повтора, размыкателя цепи, времени ожидания, изоляции отсеков и отката, более эффективным и потокобезопасным образом.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="ad8a4-619">Для использования политик Polly с настроенными экземплярами `HttpClient` предоставляются методы расширения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="ad8a4-620">Расширения Polly:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-620">The Polly extensions:</span></span>

* <span data-ttu-id="ad8a4-621">Поддерживает добавление обработчиков на основе Polly клиентам.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="ad8a4-622">Можно использовать после установки пакета NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="ad8a4-623">Пакет не включен в общую платформу ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="ad8a4-624">Обработка временных сбоев</span><span class="sxs-lookup"><span data-stu-id="ad8a4-624">Handle transient faults</span></span>

<span data-ttu-id="ad8a4-625">Чаще всего ошибки происходят, когда внешние вызовы HTTP являются временными.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="ad8a4-626">Используется удобный метод расширения `AddTransientHttpErrorPolicy`, который позволяет определить политику для обработки временных ошибок.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="ad8a4-627">Политики, заданные с помощью этого метода расширения, обрабатывают `HttpRequestException`, ответы HTTP 5xx и ответы HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="ad8a4-628">Расширение `AddTransientHttpErrorPolicy` может быть использовано в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ad8a4-629">Данное расширение предоставляет доступ к объекту `PolicyBuilder`, настроенному для обработки ошибок, представляющих возможный временный сбой:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="ad8a4-630">В приведенном выше коде определена политика `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="ad8a4-631">Неудачные запросы повторяются до трех раз с задержкой 600 мс между попытками.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="ad8a4-632">Динамический выбор политик</span><span class="sxs-lookup"><span data-stu-id="ad8a4-632">Dynamically select policies</span></span>

<span data-ttu-id="ad8a4-633">Существуют дополнительные методы расширения, которые можно использовать для добавления обработчиков на основе Polly.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="ad8a4-634">Одним из таких расширений является `AddPolicyHandler` с несколькими перегрузками.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="ad8a4-635">Одна перегрузка разрешает проверку запроса для определения необходимой политики:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="ad8a4-636">Если в приведенном выше коде исходящий запрос является запросом HTTP GET, применяется время ожидания 10 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="ad8a4-637">Для остальных методов HTTP время ожидания — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="ad8a4-638">Добавление нескольких обработчиков Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="ad8a4-639">Общепринятой практикой является вложение политик Polly для предоставления расширенной функциональности:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="ad8a4-640">В приведенном выше примере добавляются два обработчика.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="ad8a4-641">Первый использует расширение `AddTransientHttpErrorPolicy`, чтобы добавить политику повтора.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="ad8a4-642">Неудачные запросы выполняются повторно до трех раз.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="ad8a4-643">Второй вызов к `AddTransientHttpErrorPolicy` добавляет политику размыкателя цепи.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="ad8a4-644">Дополнительные внешние запросы блокируются в течение 30 секунд в случае пяти неудачных попыток подряд.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="ad8a4-645">Политики размыкателя цепи отслеживают состояние.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="ad8a4-646">Все вызовы через этот клиент имеют одинаковое состояние цепи.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="ad8a4-647">Добавление политик из реестра Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="ad8a4-648">Подход к управлению регулярно используемыми политиками заключается в их однократном определении и регистрации с помощью `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="ad8a4-649">Предоставляется метод расширения, разрешающий добавление обработчика с помощью политики из реестра:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="ad8a4-650">В приведенном выше коде, когда `PolicyRegistry` добавляется в `ServiceCollection`, регистрируются две политики.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="ad8a4-651">Чтобы использовать политику из реестра, применяется метод `AddPolicyHandlerFromRegistry`, который передает имя необходимой политики.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="ad8a4-652">Дополнительные сведения об интеграции `IHttpClientFactory` и Polly см. на [вики-сайте Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="ad8a4-653">Управление HttpClient и временем существования</span><span class="sxs-lookup"><span data-stu-id="ad8a4-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="ad8a4-654">При каждом вызове `HttpClient` в `CreateClient` возвращается новый экземпляр `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="ad8a4-655">Для каждого названного клиента существует <xref:System.Net.Http.HttpMessageHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="ad8a4-656">Фабрика обеспечивает управление временем существования экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="ad8a4-657">`IHttpClientFactory` объединяет в пул все экземпляры `HttpMessageHandler`, созданные фабрикой, чтобы уменьшить потребление ресурсов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="ad8a4-658">Экземпляр `HttpMessageHandler` можно использовать повторно из пула при создании экземпляра `HttpClient`, если его время существования еще не истекло.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="ad8a4-659">Создавать пулы обработчиков желательно, так как каждый обработчик обычно управляет собственными базовыми HTTP-подключениями.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="ad8a4-660">Создание лишних обработчиков может привести к задержке подключения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="ad8a4-661">Некоторые обработчики поддерживают подключения открытыми в течение неопределенного периода, что может помешать обработчику отреагировать на изменения DNS.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="ad8a4-662">Время существования обработчика по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="ad8a4-663">Значение по умолчанию можно переопределить для каждого именованного клиента.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="ad8a4-664">Чтобы переопределить это значение, вызовите <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> в `IHttpClientBuilder`, который возвращается при создании клиента:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="ad8a4-665">Высвобождать клиент не требуется.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="ad8a4-666">Высвобождение отменяет исходящие запросы и гарантирует, что указанный экземпляр `HttpClient` не может использоваться после вызова <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="ad8a4-667">`IHttpClientFactory` отслеживает и высвобождает ресурсы, используемые экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="ad8a4-668">Экземпляры `HttpClient` обычно можно рассматривать как объекты .NET, не требующие высвобождения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="ad8a4-669">До появления `HttpClient` один экземпляр `IHttpClientFactory` часто сохраняли в активном состоянии в течение длительного времени.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="ad8a4-670">После перехода на `IHttpClientFactory` это уже не нужно.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="ad8a4-671">Альтернативы интерфейсу IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="ad8a4-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="ad8a4-672">Использование `IHttpClientFactory` в приложении с внедрением зависимостей позволяет:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="ad8a4-673">предотвращать проблемы нехватки ресурсов путем объединения экземпляров `HttpMessageHandler` в пулы;</span><span class="sxs-lookup"><span data-stu-id="ad8a4-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="ad8a4-674">предотвращать проблемы устаревания записей DNS путем регулярной утилизации экземпляров `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="ad8a4-675">Существуют альтернативные способы решения указанных выше проблем с помощью долгосрочного экземпляра <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="ad8a4-676">Создайте экземпляр `SocketsHttpHandler` при запуске приложения и используйте его в течение всего жизненного цикла приложения.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="ad8a4-677">Присвойте <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> соответствующее значение в соответствии со временем обновления записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="ad8a4-678">По мере необходимости создавайте экземпляры `HttpClient` с помощью `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="ad8a4-679">Описанные выше подходы решают проблемы, связанные с управлением ресурсами, которые в `IHttpClientFactory` решаются сходным образом.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="ad8a4-680">`SocketsHttpHandler` обеспечивает совместное использование подключений экземплярами `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="ad8a4-681">Этот позволяет предотвратить нехватку сокетов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="ad8a4-682">`SocketsHttpHandler` уничтожает подключения в соответствии со значением `PooledConnectionLifetime`, чтобы предотвратить проблемы устаревания записей DNS.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="ad8a4-683">Файлы cookie</span><span class="sxs-lookup"><span data-stu-id="ad8a4-683">Cookies</span></span>

<span data-ttu-id="ad8a4-684">Объединение экземпляров `HttpMessageHandler` в пул приводит к совместному использованию объектов `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="ad8a4-685">Непредвиденное совместное использование объектов `CookieContainer` часто приводит к ошибкам в коде.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="ad8a4-686">Для приложений, которым требуются файлы cookie, рекомендуется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="ad8a4-687">отключите автоматическую обработку файлов cookie;</span><span class="sxs-lookup"><span data-stu-id="ad8a4-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="ad8a4-688">не используйте `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="ad8a4-689">Чтобы отключить автоматическую обработку файлов cookie, вызовите <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="ad8a4-690">Logging</span><span class="sxs-lookup"><span data-stu-id="ad8a4-690">Logging</span></span>

<span data-ttu-id="ad8a4-691">Клиенты, созданные через `IHttpClientFactory`, записывают сообщения журнала для всех запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="ad8a4-692">Установите соответствующий уровень информации в конфигурации ведения журнала, чтобы просматривать сообщения журнала по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="ad8a4-693">Дополнительное ведение журнала, например запись заголовков запросов, включено только на уровне трассировки.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="ad8a4-694">Категория журнала для каждого клиента включает в себя имя клиента.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="ad8a4-695">Клиент с именем *MyNamedClient*, например, записывает в журнал сообщения с категорией `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="ad8a4-696">Сообщения с суффиксом *LogicalHandler* создаются за пределами конвейера обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="ad8a4-697">Во время запроса сообщения записываются в журнал до обработки запроса другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="ad8a4-698">Во время ответа сообщения записываются в журнал после получения ответа другими обработчиками в конвейере.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="ad8a4-699">Кроме того, журнал ведется в конвейере обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="ad8a4-700">В примере *MyNamedClient* эти сообщения вносятся в журнал по категории журнала `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="ad8a4-701">Во время запроса это происходит после выполнения всех обработчиков и непосредственно перед отправкой запроса по сети.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="ad8a4-702">Во время ответа в журнале записывается состояние ответа перед его передачей обратно по конвейеру обработчиков.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="ad8a4-703">Включив ведение журнала в конвейере и за его пределами, можно выполнять проверку изменений, внесенных другими обработчиками конвейера.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="ad8a4-704">Сюда входят, например, изменения заголовков запросов или кода состояния ответов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="ad8a4-705">Включение имени клиента в категорию журнала позволяет фильтровать журналы по именованным клиентам при необходимости.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="ad8a4-706">Настройка HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ad8a4-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="ad8a4-707">Иногда необходимо контролировать конфигурацию внутреннего обработчика `HttpMessageHandler`, используемого клиентом.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="ad8a4-708">При добавлении именованного или типизированного клиента возвращается `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="ad8a4-709">Для определения делегата можно использовать метод расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="ad8a4-710">Делегат используется для создания и настройки основного обработчика `HttpMessageHandler`, используемого этим клиентом:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="ad8a4-711">Использование IHttpClientFactory в консольном приложении</span><span class="sxs-lookup"><span data-stu-id="ad8a4-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="ad8a4-712">В консольном приложении добавьте в проект следующие ссылки на пакеты:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="ad8a4-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="ad8a4-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="ad8a4-714">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="ad8a4-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="ad8a4-715">Рассмотрим следующий пример:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-715">In the following example:</span></span>

* <span data-ttu-id="ad8a4-716"><xref:System.Net.Http.IHttpClientFactory> регистрируется в контейнере службы [универсального узла](xref:fundamentals/host/generic-host):</span><span class="sxs-lookup"><span data-stu-id="ad8a4-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="ad8a4-717">`MyService` создает экземпляр фабрики клиента из службы, который используется для создания `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="ad8a4-718">`HttpClient` используется для получения веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="ad8a4-719">`Main` создает область для выполнения метода `GetPage` службы и вывода первых 500 символов содержимого веб-страницы на консоль.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="ad8a4-720">ПО промежуточного слоя для распространения заголовков</span><span class="sxs-lookup"><span data-stu-id="ad8a4-720">Header propagation middleware</span></span>

<span data-ttu-id="ad8a4-721">Header propagation — это поддерживаемое сообществом ПО промежуточного слоя для распространения HTTP-заголовков из входящего запроса на исходящие запросы HTTP-клиентов.</span><span class="sxs-lookup"><span data-stu-id="ad8a4-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="ad8a4-722">Чтобы использовать распространение заголовков, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-722">To use header propagation:</span></span>

* <span data-ttu-id="ad8a4-723">Укажите ссылку на поддерживаемый сообществом порт пакета [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="ad8a4-724">ASP.NET Core 3.1 и более поздних версий поддерживает [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="ad8a4-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="ad8a4-725">Настройте ПО промежуточного слоя и `HttpClient` в `Startup`:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="ad8a4-726">Клиент включает настроенные заголовки в исходящие запросы:</span><span class="sxs-lookup"><span data-stu-id="ad8a4-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="ad8a4-727">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ad8a4-727">Additional resources</span></span>

* [<span data-ttu-id="ad8a4-728">Использование HttpClientFactory для реализации устойчивых HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="ad8a4-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="ad8a4-729">Реализация повторных попыток вызова HTTP с экспоненциальной задержкой с помощью HttpClientFactory и политик Polly</span><span class="sxs-lookup"><span data-stu-id="ad8a4-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="ad8a4-730">Реализация шаблона размыкателя цепи</span><span class="sxs-lookup"><span data-stu-id="ad8a4-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
