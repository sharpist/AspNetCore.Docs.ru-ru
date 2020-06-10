---
title: "title: 'Вызов веб-API из ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Узнайте, как вызывать веб-API из приложения Blazor WebAssembly с помощью вспомогательных методов JSON, включая создание запросов на общий доступ к ресурсам независимо от источника (CORS).'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.автор: riande ms.custom: mvc ms.дата: 28.05.2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 4f3fd1fca075098a94756757474b2cad3cc99775
ms.sourcegitcommit: d8c35c37c90f81bd2cd7ae5e1700f2fede1b4b75
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84214777"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="6ba21-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6ba21-103">'Blazor'</span></span>

<span data-ttu-id="6ba21-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6ba21-104">'Identity'</span></span>

<span data-ttu-id="6ba21-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6ba21-105">'Let's Encrypt'</span></span> <span data-ttu-id="6ba21-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6ba21-106">'Razor'</span></span> <span data-ttu-id="6ba21-107">'SignalR' uid: blazor/call-web-api</span><span class="sxs-lookup"><span data-stu-id="6ba21-107">'SignalR' uid: blazor/call-web-api</span></span> <span data-ttu-id="6ba21-108">Вызов веб-API из ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6ba21-108">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="6ba21-109">Авторы: [Люк Латэм (Luke Latham)](https://github.com/guardrex), [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Хуан Де ла Круз (Juan De la Cruz)](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="6ba21-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span> <span data-ttu-id="6ba21-110">[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) вызывают веб-API с помощью предварительно настроенной службы <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="6ba21-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="6ba21-111">Составляйте запросы, которые могут включать параметры JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API), используя вспомогательные методы JSON Blazor или с помощью <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="6ba21-111">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="6ba21-112">Служба <xref:System.Net.Http.HttpClient> в приложениях Blazor WebAssembly направляет запросы обратно к серверу-источнику.</span><span class="sxs-lookup"><span data-stu-id="6ba21-112">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span>

<span data-ttu-id="6ba21-113">Рекомендации в этом разделе относятся только к приложениям Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="6ba21-113">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

* <span data-ttu-id="6ba21-114">[Приложения Blazor Server](xref:blazor/hosting-models#blazor-server) вызывают веб-API с помощью экземпляров <xref:System.Net.Http.HttpClient>, обычно созданных с помощью <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="6ba21-114">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span>
* <span data-ttu-id="6ba21-115">Рекомендации в этом разделе не относятся к приложениям Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="6ba21-115">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span>

## <a name="packages"></a><span data-ttu-id="6ba21-116">При разработке приложений Blazor Server следуйте указаниям в <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="6ba21-116">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="6ba21-117">[Просмотрите или скачайте пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([описание процедуры скачивания](xref:index#how-to-download-a-sample)). Выберите приложение *BlazorWebAssemblySample*.</span><span class="sxs-lookup"><span data-stu-id="6ba21-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the *BlazorWebAssemblySample* app.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="6ba21-118">См. следующие компоненты в примере приложения *BlazorWebAssemblySample*:</span><span class="sxs-lookup"><span data-stu-id="6ba21-118">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

<span data-ttu-id="6ba21-119">Вызов веб-API (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="6ba21-119">Call Web API (*Pages/CallWebAPI.razor*)</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="6ba21-120">Тестер HTTP-запросов (*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="6ba21-120">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

<span data-ttu-id="6ba21-121">Пакеты</span><span class="sxs-lookup"><span data-stu-id="6ba21-121">Packages</span></span>

<span data-ttu-id="6ba21-122">Сошлитесь на пакет NuGet [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="6ba21-122">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span> <span data-ttu-id="6ba21-123">Добавление службы HttpClient</span><span class="sxs-lookup"><span data-stu-id="6ba21-123">Add the HttpClient service</span></span>

<span data-ttu-id="6ba21-124">В `Program.Main` добавьте службу <xref:System.Net.Http.HttpClient>, если она еще не существует:</span><span class="sxs-lookup"><span data-stu-id="6ba21-124">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span> <span data-ttu-id="6ba21-125">HttpClient и вспомогательные методы JSON</span><span class="sxs-lookup"><span data-stu-id="6ba21-125">HttpClient and JSON helpers</span></span>

<span data-ttu-id="6ba21-126">В приложении Blazor WebAssembly [HttpClient](xref:fundamentals/http-requests) доступен в качестве предварительно настроенной службы для отправки запросов обратно к серверу-источнику.</span><span class="sxs-lookup"><span data-stu-id="6ba21-126">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span> <span data-ttu-id="6ba21-127">Приложение Blazor Server не включает службу <xref:System.Net.Http.HttpClient> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6ba21-127">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="6ba21-128">Предоставьте <xref:System.Net.Http.HttpClient> для приложения с помощью [инфраструктуры фабрики HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="6ba21-128">Provide an <xref:System.Net.Http.HttpClient> to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span> <span data-ttu-id="6ba21-129"><xref:System.Net.Http.HttpClient> и вспомогательные методы JSON также используются для вызова сторонних конечных точек веб-API.</span><span class="sxs-lookup"><span data-stu-id="6ba21-129"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span>

* <span data-ttu-id="6ba21-130"><xref:System.Net.Http.HttpClient> реализуется с помощью [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) браузера и зависит от его ограничений, включая принудительное применение той же политики источника.</span><span class="sxs-lookup"><span data-stu-id="6ba21-130"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>
* <span data-ttu-id="6ba21-131">Базовый адрес клиента устанавливается как адрес сервера-источника.</span><span class="sxs-lookup"><span data-stu-id="6ba21-131">The client's base address is set to the originating server's address.</span></span>
* <span data-ttu-id="6ba21-132">Внедрите экземпляр <xref:System.Net.Http.HttpClient> с помощью директивы [`@inject`](xref:mvc/views/razor#inject):</span><span class="sxs-lookup"><span data-stu-id="6ba21-132">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="6ba21-133">В следующих примерах веб-API Todo обрабатывает операции создания, чтения, обновления и удаления (CRUD).</span><span class="sxs-lookup"><span data-stu-id="6ba21-133">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span>

* <span data-ttu-id="6ba21-134">Примеры основаны на классе `TodoItem`, в котором хранится:</span><span class="sxs-lookup"><span data-stu-id="6ba21-134">The examples are based on a `TodoItem` class that stores the:</span></span>

  <span data-ttu-id="6ba21-135">Идентификатор (`Id`, `long`): уникальный идентификатор элемента.</span><span class="sxs-lookup"><span data-stu-id="6ba21-135">ID (`Id`, `long`): Unique ID of the item.</span></span> <span data-ttu-id="6ba21-136">Имя (`Name`, `string`). Имя элемента.</span><span class="sxs-lookup"><span data-stu-id="6ba21-136">Name (`Name`, `string`): Name of the item.</span></span> <span data-ttu-id="6ba21-137">Состояние (`IsComplete`, `bool`): указание того, завершен ли элемент Todo.</span><span class="sxs-lookup"><span data-stu-id="6ba21-137">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="6ba21-138">Вспомогательные методы JSON отправляют запросы к URI (веб-API в следующих примерах) и обрабатывают ответ:</span><span class="sxs-lookup"><span data-stu-id="6ba21-138">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

  <span data-ttu-id="6ba21-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>. Отправляет запрос HTTP GET и анализирует текст ответа JSON для создания объекта.</span><span class="sxs-lookup"><span data-stu-id="6ba21-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span> <span data-ttu-id="6ba21-140">В следующем коде `todoItems` отображаются компонентом.</span><span class="sxs-lookup"><span data-stu-id="6ba21-140">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="6ba21-141">Метод `GetTodoItems` срабатывает после завершения подготовки компонента к просмотру ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="6ba21-141">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="6ba21-142">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="6ba21-142">See the sample app for a complete example.</span></span> <span data-ttu-id="6ba21-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>. Отправляет запрос HTTP POST, включая содержимое в кодировке JSON, и анализирует текст ответа JSON для создания объекта.</span><span class="sxs-lookup"><span data-stu-id="6ba21-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="6ba21-144">В следующем коде `newItemName` предоставляется связанным элементом компонента.</span><span class="sxs-lookup"><span data-stu-id="6ba21-144">In the following code, `newItemName` is provided by a bound element of the component.</span></span>

  <span data-ttu-id="6ba21-145">Метод `AddItem` активируется путем выбора элемента `<button>`.</span><span class="sxs-lookup"><span data-stu-id="6ba21-145">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="6ba21-146">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="6ba21-146">See the sample app for a complete example.</span></span> <span data-ttu-id="6ba21-147">Вызовы к <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> возвращают <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="6ba21-147">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="6ba21-148">Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения `ReadFromJsonAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="6ba21-148">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="6ba21-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>. Отправляет запрос HTTP PUT, включая содержимое в кодировке JSON.</span><span class="sxs-lookup"><span data-stu-id="6ba21-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span> <span data-ttu-id="6ba21-150">В следующем коде значения `editItem` для `Name` и `IsCompleted` предоставляются связанными элементами компонента.</span><span class="sxs-lookup"><span data-stu-id="6ba21-150">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="6ba21-151">Элемент `Id` задается, когда элемент выбирается в другой части пользовательского интерфейса и вызывается `EditItem`.</span><span class="sxs-lookup"><span data-stu-id="6ba21-151">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="6ba21-152">Метод `SaveItem` активируется путем выбора элемента `<button>` "Save".</span><span class="sxs-lookup"><span data-stu-id="6ba21-152">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span>

<span data-ttu-id="6ba21-153">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="6ba21-153">See the sample app for a complete example.</span></span> <span data-ttu-id="6ba21-154">Вызовы к <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> возвращают <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="6ba21-154">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="6ba21-155">Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="6ba21-155">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="6ba21-156"><xref:System.Net.Http> включает дополнительные методы расширения для отправки HTTP-запросов и получения HTTP-ответов.</span><span class="sxs-lookup"><span data-stu-id="6ba21-156"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span>

<span data-ttu-id="6ba21-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> используется для отправки запроса HTTP DELETE в веб-интерфейс API.</span><span class="sxs-lookup"><span data-stu-id="6ba21-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="6ba21-158">В следующем коде элемент `<button>` "Delete" вызывает метод `DeleteItem`.</span><span class="sxs-lookup"><span data-stu-id="6ba21-158">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span>

<span data-ttu-id="6ba21-159">Связанный элемент `<input>` предоставляет `id` удаляемого элемента.</span><span class="sxs-lookup"><span data-stu-id="6ba21-159">The bound `<input>` element supplies the `id` of the item to delete.</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="6ba21-160">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="6ba21-160">See the sample app for a complete example.</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="6ba21-161">Именованный класс HttpClient с IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="6ba21-161">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="6ba21-162">Поддерживаются службы <xref:System.Net.Http.IHttpClientFactory> и конфигурация именованного класса <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="6ba21-162"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="6ba21-163">Используйте ссылку на пакет NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="6ba21-163">Reference the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package in the project file.</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

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
        catch
        {
            ...
        }
    
        return forecasts;
    }
}
```

<span data-ttu-id="6ba21-164">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="6ba21-164">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="6ba21-165">Компонент `FetchData` (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="6ba21-165">`FetchData` component (*Pages/FetchData.razor*):</span></span>

<span data-ttu-id="6ba21-166">Типизированный класс HttpClient</span><span class="sxs-lookup"><span data-stu-id="6ba21-166">Typed HttpClient</span></span>

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="handle-errors"></a><span data-ttu-id="6ba21-167">Для возврата данных из одной или нескольких конечных точек веб-API типизированный класс <xref:System.Net.Http.HttpClient> использует один или несколько экземпляров класса <xref:System.Net.Http.HttpClient> приложения (заданных по умолчанию или именованных).</span><span class="sxs-lookup"><span data-stu-id="6ba21-167">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="6ba21-168">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="6ba21-168">*WeatherForecastClient.cs*:</span></span> <span data-ttu-id="6ba21-169">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="6ba21-169">`Program.Main` (*Program.cs*):</span></span> <span data-ttu-id="6ba21-170">Компоненты внедряют типизированный класс <xref:System.Net.Http.HttpClient> для вызова веб-API.</span><span class="sxs-lookup"><span data-stu-id="6ba21-170">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="6ba21-171">Компонент `FetchData` (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="6ba21-171">`FetchData` component (*Pages/FetchData.razor*):</span></span> <span data-ttu-id="6ba21-172">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="6ba21-172">Handle errors</span></span>

<span data-ttu-id="6ba21-173">Ошибки, возникающие во время взаимодействия с веб-API, могут обрабатываться кодом разработчика.</span><span class="sxs-lookup"><span data-stu-id="6ba21-173">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="6ba21-174">Например, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> ожидает ответа JSON от API сервера с заголовком `Content-Type` типа `application/json`.</span><span class="sxs-lookup"><span data-stu-id="6ba21-174">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span>

<span data-ttu-id="6ba21-175">Если формат ответа отличается от JSON, при проверке содержимого возникает исключение <xref:System.NotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="6ba21-175">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span> <span data-ttu-id="6ba21-176">В следующем примере показана неправильно написанная конечная точка URI для запроса данных прогноза погоды.</span><span class="sxs-lookup"><span data-stu-id="6ba21-176">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="6ba21-177">URI должен иметь вид `WeatherForecast`, но отображается в вызове как `WeatherForcast` (без "e").</span><span class="sxs-lookup"><span data-stu-id="6ba21-177">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span> <span data-ttu-id="6ba21-178">Вызов <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> ожидает возврата JSON, но сервер возвращает HTML для необработанного исключения на сервере с заголовком `Content-Type` типа `text/html`.</span><span class="sxs-lookup"><span data-stu-id="6ba21-178">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span>

<span data-ttu-id="6ba21-179">Необработанное исключение возникает на сервере, так как путь не найден, а ПО промежуточного слоя не может обслуживать страницу или представление для запроса.</span><span class="sxs-lookup"><span data-stu-id="6ba21-179">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="6ba21-180">Если выясняется, что содержимое ответа не является содержимым формата JSON, в методе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> на клиенте возникает исключение <xref:System.NotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="6ba21-180">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span>

<span data-ttu-id="6ba21-181">Исключение перехватывается в блоке `catch`, где пользовательская логика может зарегистрировать ошибку или вывести понятное сообщение об ошибке для пользователя:</span><span class="sxs-lookup"><span data-stu-id="6ba21-181">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span> <span data-ttu-id="6ba21-182">Предыдущий пример приведен только в качестве демонстрации.</span><span class="sxs-lookup"><span data-stu-id="6ba21-182">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="6ba21-183">Серверное приложение веб-API можно настроить для возврата JSON, даже если не существует конечная точка или на сервере возникает необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="6ba21-183">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span> <span data-ttu-id="6ba21-184">Для получения дополнительной информации см. <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="6ba21-184">For more information, see <xref:blazor/handle-errors>.</span></span>

<span data-ttu-id="6ba21-185">Общий доступ к ресурсам независимо от источника (CORS)</span><span class="sxs-lookup"><span data-stu-id="6ba21-185">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="6ba21-186">Безопасность в браузере предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="6ba21-186">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ba21-187">Это ограничение называется *политика одного источника*.</span><span class="sxs-lookup"><span data-stu-id="6ba21-187">This restriction is called the *same-origin policy*.</span></span>

* <span data-ttu-id="6ba21-188">Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта.</span><span class="sxs-lookup"><span data-stu-id="6ba21-188">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* <span data-ttu-id="6ba21-189">Для выполнения запросов из браузера к конечной точке с другим источником *конечная точка* должна включать [общий доступ к ресурсам независимо от источника (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="6ba21-189">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>
* <span data-ttu-id="6ba21-190">В [примере приложения Blazor WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) показано использование CORS в компоненте вызова веб-API (*Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="6ba21-190">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>
