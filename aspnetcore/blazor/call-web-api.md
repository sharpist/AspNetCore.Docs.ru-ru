---
title: Вызов веб-API из ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как вызывать веб-API из приложения Blazor WebAssembly с помощью вспомогательных методов JSON, включая создание запросов на общий доступ к ресурсам независимо от источника (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 7ed2d51c0d41a50a2e139d739a0a06cd9f392a83
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153502"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="87a1c-103">Вызов веб-API из ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="87a1c-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="87a1c-104">Авторы: [Люк Латэм (Luke Latham)](https://github.com/guardrex), [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Хуан Де ла Круз (Juan De la Cruz)](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="87a1c-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="87a1c-105">[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) вызывают веб-API с помощью предварительно настроенной службы `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="87a1c-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="87a1c-106">Составляйте запросы, которые могут включать параметры JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API), используя вспомогательные методы JSON Blazor или с помощью <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="87a1c-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="87a1c-107">Служба `HttpClient` в приложениях Blazor WebAssembly направляет запросы обратно к серверу-источнику.</span><span class="sxs-lookup"><span data-stu-id="87a1c-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="87a1c-108">Рекомендации в этом разделе относятся только к приложениям Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="87a1c-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="87a1c-109">[Приложения Blazor Server](xref:blazor/hosting-models#blazor-server) вызывают веб-API с помощью экземпляров <xref:System.Net.Http.HttpClient>, обычно созданных с помощью <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="87a1c-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="87a1c-110">Рекомендации в этом разделе не относятся к приложениям Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="87a1c-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="87a1c-111">При разработке приложений Blazor Server следуйте указаниям в <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="87a1c-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="87a1c-112">[Просмотрите или загрузите образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([сведения о загрузке](xref:index#how-to-download-a-sample)) &ndash; Выберите приложение *BlazorWebAssemblySample*.</span><span class="sxs-lookup"><span data-stu-id="87a1c-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="87a1c-113">См. следующие компоненты в примере приложения *BlazorWebAssemblySample*:</span><span class="sxs-lookup"><span data-stu-id="87a1c-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="87a1c-114">Вызов веб-API (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="87a1c-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="87a1c-115">Тестер HTTP-запросов (*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="87a1c-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="87a1c-116">Пакеты</span><span class="sxs-lookup"><span data-stu-id="87a1c-116">Packages</span></span>

<span data-ttu-id="87a1c-117">Сошлитесь на пакет NuGet [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="87a1c-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="87a1c-118">Добавление службы HttpClient</span><span class="sxs-lookup"><span data-stu-id="87a1c-118">Add the HttpClient service</span></span>

<span data-ttu-id="87a1c-119">В `Program.Main` добавьте службу `HttpClient`, если она еще не существует:</span><span class="sxs-lookup"><span data-stu-id="87a1c-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="87a1c-120">HttpClient и вспомогательные методы JSON</span><span class="sxs-lookup"><span data-stu-id="87a1c-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="87a1c-121">В приложении Blazor WebAssembly [HttpClient](xref:fundamentals/http-requests) доступен в качестве предварительно настроенной службы для отправки запросов обратно к серверу-источнику.</span><span class="sxs-lookup"><span data-stu-id="87a1c-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="87a1c-122">Приложение Blazor Server не включает службу `HttpClient` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="87a1c-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="87a1c-123">Предоставьте `HttpClient` для приложения с помощью [инфраструктуры фабрики HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="87a1c-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="87a1c-124">`HttpClient` и вспомогательные методы JSON также используются для вызова сторонних конечных точек веб-API.</span><span class="sxs-lookup"><span data-stu-id="87a1c-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="87a1c-125">`HttpClient` реализуется с помощью [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) браузера и зависит от его ограничений, включая принудительное применение той же политики источника.</span><span class="sxs-lookup"><span data-stu-id="87a1c-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="87a1c-126">Базовый адрес клиента устанавливается как адрес сервера-источника.</span><span class="sxs-lookup"><span data-stu-id="87a1c-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="87a1c-127">Внедрите экземпляр `HttpClient` с помощью директивы `@inject`:</span><span class="sxs-lookup"><span data-stu-id="87a1c-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="87a1c-128">В следующих примерах веб-API Todo обрабатывает операции создания, чтения, обновления и удаления (CRUD).</span><span class="sxs-lookup"><span data-stu-id="87a1c-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="87a1c-129">Примеры основаны на классе `TodoItem`, в котором хранится:</span><span class="sxs-lookup"><span data-stu-id="87a1c-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="87a1c-130">Идентификатор (`Id`, `long`) — уникальный идентификатор элемента.</span><span class="sxs-lookup"><span data-stu-id="87a1c-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="87a1c-131">Имя (`Name`, `string`) — имя элемента.</span><span class="sxs-lookup"><span data-stu-id="87a1c-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="87a1c-132">Состояние (`IsComplete`, `bool`) — указание того, завершен ли элемент Todo.</span><span class="sxs-lookup"><span data-stu-id="87a1c-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="87a1c-133">Вспомогательные методы JSON отправляют запросы к URI (веб-API в следующих примерах) и обрабатывают ответ:</span><span class="sxs-lookup"><span data-stu-id="87a1c-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="87a1c-134">`GetFromJsonAsync` &ndash; Отправляет запрос HTTP GET и анализирует текст ответа JSON для создания объекта.</span><span class="sxs-lookup"><span data-stu-id="87a1c-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="87a1c-135">В следующем коде `todoItems` отображаются компонентом.</span><span class="sxs-lookup"><span data-stu-id="87a1c-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="87a1c-136">Метод `GetTodoItems` срабатывает после завершения подготовки компонента к просмотру ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="87a1c-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="87a1c-137">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="87a1c-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="87a1c-138">`PostAsJsonAsync` &ndash; Отправляет запрос HTTP POST, включая содержимое в кодировке JSON, и анализирует текст ответа JSON для создания объекта.</span><span class="sxs-lookup"><span data-stu-id="87a1c-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="87a1c-139">В следующем коде `newItemName` предоставляется связанным элементом компонента.</span><span class="sxs-lookup"><span data-stu-id="87a1c-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="87a1c-140">Метод `AddItem` активируется путем выбора элемента `<button>`.</span><span class="sxs-lookup"><span data-stu-id="87a1c-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="87a1c-141">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="87a1c-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="87a1c-142">Вызовы к `PostAsJsonAsync` возвращают <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="87a1c-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="87a1c-143">Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения `ReadFromJsonAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="87a1c-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="87a1c-144">`PutAsJsonAsync` &ndash; Отправляет запрос HTTP PUT, включая содержимое в кодировке JSON.</span><span class="sxs-lookup"><span data-stu-id="87a1c-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="87a1c-145">В следующем коде значения `editItem` для `Name` и `IsCompleted` предоставляются связанными элементами компонента.</span><span class="sxs-lookup"><span data-stu-id="87a1c-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="87a1c-146">Элемент `Id` задается, когда элемент выбирается в другой части пользовательского интерфейса и вызывается `EditItem`.</span><span class="sxs-lookup"><span data-stu-id="87a1c-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="87a1c-147">Метод `SaveItem` активируется путем выбора элемента `<button>` "Save".</span><span class="sxs-lookup"><span data-stu-id="87a1c-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="87a1c-148">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="87a1c-148">See the sample app for a complete example.</span></span>

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
          var editItem = todoItems.Single(i => i.Id == id);
          editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="87a1c-149">Вызовы к `PutAsJsonAsync` возвращают <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="87a1c-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="87a1c-150">Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения `ReadFromJsonAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="87a1c-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="87a1c-151"><xref:System.Net.Http> включает дополнительные методы расширения для отправки HTTP-запросов и получения HTTP-ответов.</span><span class="sxs-lookup"><span data-stu-id="87a1c-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="87a1c-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) используется для отправки запроса HTTP DELETE в веб-интерфейс API.</span><span class="sxs-lookup"><span data-stu-id="87a1c-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="87a1c-153">В следующем коде элемент `<button>` "Delete" вызывает метод `DeleteItem`.</span><span class="sxs-lookup"><span data-stu-id="87a1c-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="87a1c-154">Связанный элемент `<input>` предоставляет `id` удаляемого элемента.</span><span class="sxs-lookup"><span data-stu-id="87a1c-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="87a1c-155">Полный пример см. в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="87a1c-155">See the sample app for a complete example.</span></span>

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="87a1c-156">Именованный класс HttpClient с IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="87a1c-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="87a1c-157">Поддерживаются службы <xref:System.Net.Http.IHttpClientFactory> и конфигурация именованного класса <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="87a1c-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="87a1c-158">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="87a1c-158">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="87a1c-159">Компонент `FetchData` (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="87a1c-159">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="87a1c-160">Типизированный класс HttpClient</span><span class="sxs-lookup"><span data-stu-id="87a1c-160">Typed HttpClient</span></span>

<span data-ttu-id="87a1c-161">Для возврата данных из одной или нескольких конечных точек веб-API типизированный класс <xref:System.Net.Http.HttpClient> использует один или несколько экземпляров класса <xref:System.Net.Http.HttpClient> приложения (заданных по умолчанию или именованных).</span><span class="sxs-lookup"><span data-stu-id="87a1c-161">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="87a1c-162">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="87a1c-162">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="87a1c-163">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="87a1c-163">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="87a1c-164">Компоненты внедряют типизированный класс `HttpClient` для вызова веб-API.</span><span class="sxs-lookup"><span data-stu-id="87a1c-164">Components inject the typed `HttpClient` to call the web API.</span></span>

<span data-ttu-id="87a1c-165">Компонент `FetchData` (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="87a1c-165">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="handle-errors"></a><span data-ttu-id="87a1c-166">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="87a1c-166">Handle errors</span></span>

<span data-ttu-id="87a1c-167">Ошибки, возникающие во время взаимодействия с веб-API, могут обрабатываться кодом разработчика.</span><span class="sxs-lookup"><span data-stu-id="87a1c-167">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="87a1c-168">Например, `GetFromJsonAsync` ожидает ответа JSON от API сервера с заголовком `Content-Type` типа `application/json`.</span><span class="sxs-lookup"><span data-stu-id="87a1c-168">For example, `GetFromJsonAsync` expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="87a1c-169">Если формат ответа отличается от JSON, при проверке содержимого возникает исключение <xref:System.NotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="87a1c-169">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="87a1c-170">В следующем примере показана неправильно написанная конечная точка URI для запроса данных прогноза погоды.</span><span class="sxs-lookup"><span data-stu-id="87a1c-170">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="87a1c-171">URI должен иметь вид `WeatherForecast`, но отображается в вызове как `WeatherForcast` (без "e").</span><span class="sxs-lookup"><span data-stu-id="87a1c-171">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="87a1c-172">Вызов `GetFromJsonAsync` ожидает возврата JSON, но сервер возвращает HTML для необработанного исключения на сервере с заголовком `Content-Type` типа `text/html`.</span><span class="sxs-lookup"><span data-stu-id="87a1c-172">The `GetFromJsonAsync` call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="87a1c-173">Необработанное исключение возникает на сервере, так как путь не найден, а ПО промежуточного слоя не может обслуживать страницу или представление для запроса.</span><span class="sxs-lookup"><span data-stu-id="87a1c-173">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="87a1c-174">Если выясняется, что содержимое ответа не является содержимым формата JSON, в методе `OnInitializedAsync` на клиенте возникает исключение <xref:System.NotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="87a1c-174">In `OnInitializedAsync` on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="87a1c-175">Исключение перехватывается в блоке `catch`, где пользовательская логика может зарегистрировать ошибку или вывести понятное сообщение об ошибке для пользователя:</span><span class="sxs-lookup"><span data-stu-id="87a1c-175">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="87a1c-176">Предыдущий пример приведен только в качестве демонстрации.</span><span class="sxs-lookup"><span data-stu-id="87a1c-176">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="87a1c-177">Серверное приложение веб-API можно настроить для возврата JSON, даже если не существует конечная точка или на сервере возникает необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="87a1c-177">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="87a1c-178">Для получения дополнительной информации см. <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="87a1c-178">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="87a1c-179">Общий доступ к ресурсам независимо от источника (CORS)</span><span class="sxs-lookup"><span data-stu-id="87a1c-179">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="87a1c-180">Безопасность в браузере предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="87a1c-180">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="87a1c-181">Это ограничение называется *политика одного источника*.</span><span class="sxs-lookup"><span data-stu-id="87a1c-181">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="87a1c-182">Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта.</span><span class="sxs-lookup"><span data-stu-id="87a1c-182">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="87a1c-183">Для выполнения запросов из браузера к конечной точке с другим источником *конечная точка* должна включать [общий доступ к ресурсам независимо от источника (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="87a1c-183">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="87a1c-184">В [примере приложения Blazor WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) показано использование CORS в компоненте вызова веб-API (*Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="87a1c-184">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="87a1c-185">Сведения о том, как разрешить другим сайтам выполнять запросы на общий доступ к ресурсам независимо от источника (CORS) к приложению, см. в разделе <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="87a1c-185">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87a1c-186">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="87a1c-186">Additional resources</span></span>

* <span data-ttu-id="87a1c-187"><xref:security/blazor/webassembly/additional-scenarios> &ndash; Сведения об использовании класса `HttpClient` для отправки безопасных запросов веб-API.</span><span class="sxs-lookup"><span data-stu-id="87a1c-187"><xref:security/blazor/webassembly/additional-scenarios> &ndash; Includes coverage on using `HttpClient` to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="87a1c-188">Конфигурация конечных точек HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="87a1c-188">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="87a1c-189">Общий доступ к ресурсам независимо от источника (CORS) в W3C</span><span class="sxs-lookup"><span data-stu-id="87a1c-189">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
