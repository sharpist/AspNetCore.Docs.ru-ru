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
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid: blazor/call-web-api Вызов веб-API из ASP.NET Core Blazor

Авторы: [Люк Латэм (Luke Latham)](https://github.com/guardrex), [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Хуан Де ла Круз (Juan De la Cruz)](https://github.com/juandelacruz23) [Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) вызывают веб-API с помощью предварительно настроенной службы <xref:System.Net.Http.HttpClient>. Составляйте запросы, которые могут включать параметры JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API), используя вспомогательные методы JSON Blazor или с помощью <xref:System.Net.Http.HttpRequestMessage>.

Служба <xref:System.Net.Http.HttpClient> в приложениях Blazor WebAssembly направляет запросы обратно к серверу-источнику.

Рекомендации в этом разделе относятся только к приложениям Blazor WebAssembly.

* [Приложения Blazor Server](xref:blazor/hosting-models#blazor-server) вызывают веб-API с помощью экземпляров <xref:System.Net.Http.HttpClient>, обычно созданных с помощью <xref:System.Net.Http.IHttpClientFactory>.
* Рекомендации в этом разделе не относятся к приложениям Blazor Server.

## <a name="packages"></a>При разработке приложений Blazor Server следуйте указаниям в <xref:fundamentals/http-requests>.

[Просмотрите или скачайте пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([описание процедуры скачивания](xref:index#how-to-download-a-sample)). Выберите приложение *BlazorWebAssemblySample*.

## <a name="add-the-httpclient-service"></a>См. следующие компоненты в примере приложения *BlazorWebAssemblySample*:

Вызов веб-API (*Pages/CallWebAPI.razor*)

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Тестер HTTP-запросов (*Components/HTTPRequestTester.razor*)

Пакеты

Сошлитесь на пакет NuGet [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) в файле проекта. Добавление службы HttpClient

В `Program.Main` добавьте службу <xref:System.Net.Http.HttpClient>, если она еще не существует: HttpClient и вспомогательные методы JSON

В приложении Blazor WebAssembly [HttpClient](xref:fundamentals/http-requests) доступен в качестве предварительно настроенной службы для отправки запросов обратно к серверу-источнику. Приложение Blazor Server не включает службу <xref:System.Net.Http.HttpClient> по умолчанию.

```razor
@using System.Net.Http
@inject HttpClient Http
```

Предоставьте <xref:System.Net.Http.HttpClient> для приложения с помощью [инфраструктуры фабрики HttpClient](xref:fundamentals/http-requests). <xref:System.Net.Http.HttpClient> и вспомогательные методы JSON также используются для вызова сторонних конечных точек веб-API.

* <xref:System.Net.Http.HttpClient> реализуется с помощью [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) браузера и зависит от его ограничений, включая принудительное применение той же политики источника.
* Базовый адрес клиента устанавливается как адрес сервера-источника.
* Внедрите экземпляр <xref:System.Net.Http.HttpClient> с помощью директивы [`@inject`](xref:mvc/views/razor#inject):

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

В следующих примерах веб-API Todo обрабатывает операции создания, чтения, обновления и удаления (CRUD).

* Примеры основаны на классе `TodoItem`, в котором хранится:

  Идентификатор (`Id`, `long`): уникальный идентификатор элемента. Имя (`Name`, `string`). Имя элемента. Состояние (`IsComplete`, `bool`): указание того, завершен ли элемент Todo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* Вспомогательные методы JSON отправляют запросы к URI (веб-API в следующих примерах) и обрабатывают ответ:

  <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>. Отправляет запрос HTTP GET и анализирует текст ответа JSON для создания объекта. В следующем коде `todoItems` отображаются компонентом. Метод `GetTodoItems` срабатывает после завершения подготовки компонента к просмотру ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).

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
  
  Полный пример см. в примере приложения. <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>. Отправляет запрос HTTP POST, включая содержимое в кодировке JSON, и анализирует текст ответа JSON для создания объекта.
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* В следующем коде `newItemName` предоставляется связанным элементом компонента.

  Метод `AddItem` активируется путем выбора элемента `<button>`. Полный пример см. в примере приложения. Вызовы к <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> возвращают <xref:System.Net.Http.HttpResponseMessage>. Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения `ReadFromJsonAsync<T>`:

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
  
  <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>. Отправляет запрос HTTP PUT, включая содержимое в кодировке JSON. В следующем коде значения `editItem` для `Name` и `IsCompleted` предоставляются связанными элементами компонента.
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

Элемент `Id` задается, когда элемент выбирается в другой части пользовательского интерфейса и вызывается `EditItem`. Метод `SaveItem` активируется путем выбора элемента `<button>` "Save".

Полный пример см. в примере приложения. Вызовы к <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> возвращают <xref:System.Net.Http.HttpResponseMessage>. Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>:

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><xref:System.Net.Http> включает дополнительные методы расширения для отправки HTTP-запросов и получения HTTP-ответов.

<xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> используется для отправки запроса HTTP DELETE в веб-интерфейс API.

В следующем коде элемент `<button>` "Delete" вызывает метод `DeleteItem`.

Связанный элемент `<input>` предоставляет `id` удаляемого элемента.

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Полный пример см. в примере приложения.

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

## <a name="typed-httpclient"></a>Именованный класс HttpClient с IHttpClientFactory

Поддерживаются службы <xref:System.Net.Http.IHttpClientFactory> и конфигурация именованного класса <xref:System.Net.Http.HttpClient>.

Используйте ссылку на пакет NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) в файле проекта.

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

`Program.Main` (*Program.cs*):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Компонент `FetchData` (*Pages/FetchData.razor*):

Типизированный класс HttpClient

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

## <a name="handle-errors"></a>Для возврата данных из одной или нескольких конечных точек веб-API типизированный класс <xref:System.Net.Http.HttpClient> использует один или несколько экземпляров класса <xref:System.Net.Http.HttpClient> приложения (заданных по умолчанию или именованных).

*WeatherForecastClient.cs*: `Program.Main` (*Program.cs*): Компоненты внедряют типизированный класс <xref:System.Net.Http.HttpClient> для вызова веб-API.

Компонент `FetchData` (*Pages/FetchData.razor*): Обработка ошибок

Ошибки, возникающие во время взаимодействия с веб-API, могут обрабатываться кодом разработчика. Например, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> ожидает ответа JSON от API сервера с заголовком `Content-Type` типа `application/json`.

Если формат ответа отличается от JSON, при проверке содержимого возникает исключение <xref:System.NotSupportedException>. В следующем примере показана неправильно написанная конечная точка URI для запроса данных прогноза погоды.

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
> URI должен иметь вид `WeatherForecast`, но отображается в вызове как `WeatherForcast` (без "e"). Вызов <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> ожидает возврата JSON, но сервер возвращает HTML для необработанного исключения на сервере с заголовком `Content-Type` типа `text/html`.

Необработанное исключение возникает на сервере, так как путь не найден, а ПО промежуточного слоя не может обслуживать страницу или представление для запроса.

## <a name="cross-origin-resource-sharing-cors"></a>Если выясняется, что содержимое ответа не является содержимым формата JSON, в методе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> на клиенте возникает исключение <xref:System.NotSupportedException>.

Исключение перехватывается в блоке `catch`, где пользовательская логика может зарегистрировать ошибку или вывести понятное сообщение об ошибке для пользователя: Предыдущий пример приведен только в качестве демонстрации. Серверное приложение веб-API можно настроить для возврата JSON, даже если не существует конечная точка или на сервере возникает необработанное исключение. Для получения дополнительной информации см. <xref:blazor/handle-errors>.

Общий доступ к ресурсам независимо от источника (CORS)

Безопасность в браузере предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу.

## <a name="additional-resources"></a>Это ограничение называется *политика одного источника*.

* Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* Для выполнения запросов из браузера к конечной точке с другим источником *конечная точка* должна включать [общий доступ к ресурсам независимо от источника (CORS)](https://www.w3.org/TR/cors/).
* В [примере приложения Blazor WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) показано использование CORS в компоненте вызова веб-API (*Pages/CallWebAPI.razor*).
