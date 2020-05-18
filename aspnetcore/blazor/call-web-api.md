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
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Вызов веб-API из ASP.NET Core Blazor

Авторы: [Люк Латэм (Luke Latham)](https://github.com/guardrex), [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Хуан Де ла Круз (Juan De la Cruz)](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) вызывают веб-API с помощью предварительно настроенной службы `HttpClient`. Составляйте запросы, которые могут включать параметры JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API), используя вспомогательные методы JSON Blazor или с помощью <xref:System.Net.Http.HttpRequestMessage>. Служба `HttpClient` в приложениях Blazor WebAssembly направляет запросы обратно к серверу-источнику. Рекомендации в этом разделе относятся только к приложениям Blazor WebAssembly.

[Приложения Blazor Server](xref:blazor/hosting-models#blazor-server) вызывают веб-API с помощью экземпляров <xref:System.Net.Http.HttpClient>, обычно созданных с помощью <xref:System.Net.Http.IHttpClientFactory>. Рекомендации в этом разделе не относятся к приложениям Blazor Server. При разработке приложений Blazor Server следуйте указаниям в <xref:fundamentals/http-requests>.

[Просмотрите или загрузите образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([сведения о загрузке](xref:index#how-to-download-a-sample)) &ndash; Выберите приложение *BlazorWebAssemblySample*.

См. следующие компоненты в примере приложения *BlazorWebAssemblySample*:

* Вызов веб-API (*Pages/CallWebAPI.razor*)
* Тестер HTTP-запросов (*Components/HTTPRequestTester.razor*)

## <a name="packages"></a>Пакеты

Сошлитесь на пакет NuGet [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) в файле проекта.

## <a name="add-the-httpclient-service"></a>Добавление службы HttpClient

В `Program.Main` добавьте службу `HttpClient`, если она еще не существует:

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient и вспомогательные методы JSON

В приложении Blazor WebAssembly [HttpClient](xref:fundamentals/http-requests) доступен в качестве предварительно настроенной службы для отправки запросов обратно к серверу-источнику.

Приложение Blazor Server не включает службу `HttpClient` по умолчанию. Предоставьте `HttpClient` для приложения с помощью [инфраструктуры фабрики HttpClient](xref:fundamentals/http-requests).

`HttpClient` и вспомогательные методы JSON также используются для вызова сторонних конечных точек веб-API. `HttpClient` реализуется с помощью [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) браузера и зависит от его ограничений, включая принудительное применение той же политики источника.

Базовый адрес клиента устанавливается как адрес сервера-источника. Внедрите экземпляр `HttpClient` с помощью директивы `@inject`:

```razor
@using System.Net.Http
@inject HttpClient Http
```

В следующих примерах веб-API Todo обрабатывает операции создания, чтения, обновления и удаления (CRUD). Примеры основаны на классе `TodoItem`, в котором хранится:

* Идентификатор (`Id`, `long`) — уникальный идентификатор элемента.
* Имя (`Name`, `string`) — имя элемента.
* Состояние (`IsComplete`, `bool`) — указание того, завершен ли элемент Todo.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Вспомогательные методы JSON отправляют запросы к URI (веб-API в следующих примерах) и обрабатывают ответ:

* `GetFromJsonAsync` &ndash; Отправляет запрос HTTP GET и анализирует текст ответа JSON для создания объекта.

  В следующем коде `todoItems` отображаются компонентом. Метод `GetTodoItems` срабатывает после завершения подготовки компонента к просмотру ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Полный пример см. в примере приложения.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync` &ndash; Отправляет запрос HTTP POST, включая содержимое в кодировке JSON, и анализирует текст ответа JSON для создания объекта.

  В следующем коде `newItemName` предоставляется связанным элементом компонента. Метод `AddItem` активируется путем выбора элемента `<button>`. Полный пример см. в примере приложения.

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
  
  Вызовы к `PostAsJsonAsync` возвращают <xref:System.Net.Http.HttpResponseMessage>. Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения `ReadFromJsonAsync<T>`:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync` &ndash; Отправляет запрос HTTP PUT, включая содержимое в кодировке JSON.

  В следующем коде значения `editItem` для `Name` и `IsCompleted` предоставляются связанными элементами компонента. Элемент `Id` задается, когда элемент выбирается в другой части пользовательского интерфейса и вызывается `EditItem`. Метод `SaveItem` активируется путем выбора элемента `<button>` "Save". Полный пример см. в примере приложения.

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
  
  Вызовы к `PutAsJsonAsync` возвращают <xref:System.Net.Http.HttpResponseMessage>. Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения `ReadFromJsonAsync<T>`:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http> включает дополнительные методы расширения для отправки HTTP-запросов и получения HTTP-ответов. [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) используется для отправки запроса HTTP DELETE в веб-интерфейс API.

В следующем коде элемент `<button>` "Delete" вызывает метод `DeleteItem`. Связанный элемент `<input>` предоставляет `id` удаляемого элемента. Полный пример см. в примере приложения.

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

## <a name="named-httpclient-with-ihttpclientfactory"></a>Именованный класс HttpClient с IHttpClientFactory

Поддерживаются службы <xref:System.Net.Http.IHttpClientFactory> и конфигурация именованного класса <xref:System.Net.Http.HttpClient>.

`Program.Main` (*Program.cs*):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Компонент `FetchData` (*Pages/FetchData.razor*):

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

## <a name="typed-httpclient"></a>Типизированный класс HttpClient

Для возврата данных из одной или нескольких конечных точек веб-API типизированный класс <xref:System.Net.Http.HttpClient> использует один или несколько экземпляров класса <xref:System.Net.Http.HttpClient> приложения (заданных по умолчанию или именованных).

*WeatherForecastClient.cs*:

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

Компоненты внедряют типизированный класс `HttpClient` для вызова веб-API.

Компонент `FetchData` (*Pages/FetchData.razor*):

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

## <a name="handle-errors"></a>Обработка ошибок

Ошибки, возникающие во время взаимодействия с веб-API, могут обрабатываться кодом разработчика. Например, `GetFromJsonAsync` ожидает ответа JSON от API сервера с заголовком `Content-Type` типа `application/json`. Если формат ответа отличается от JSON, при проверке содержимого возникает исключение <xref:System.NotSupportedException>.

В следующем примере показана неправильно написанная конечная точка URI для запроса данных прогноза погоды. URI должен иметь вид `WeatherForecast`, но отображается в вызове как `WeatherForcast` (без "e").

Вызов `GetFromJsonAsync` ожидает возврата JSON, но сервер возвращает HTML для необработанного исключения на сервере с заголовком `Content-Type` типа `text/html`. Необработанное исключение возникает на сервере, так как путь не найден, а ПО промежуточного слоя не может обслуживать страницу или представление для запроса.

Если выясняется, что содержимое ответа не является содержимым формата JSON, в методе `OnInitializedAsync` на клиенте возникает исключение <xref:System.NotSupportedException>. Исключение перехватывается в блоке `catch`, где пользовательская логика может зарегистрировать ошибку или вывести понятное сообщение об ошибке для пользователя:

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
> Предыдущий пример приведен только в качестве демонстрации. Серверное приложение веб-API можно настроить для возврата JSON, даже если не существует конечная точка или на сервере возникает необработанное исключение.

Для получения дополнительной информации см. <xref:blazor/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>Общий доступ к ресурсам независимо от источника (CORS)

Безопасность в браузере предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу. Это ограничение называется *политика одного источника*. Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта. Для выполнения запросов из браузера к конечной точке с другим источником *конечная точка* должна включать [общий доступ к ресурсам независимо от источника (CORS)](https://www.w3.org/TR/cors/).

В [примере приложения Blazor WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) показано использование CORS в компоненте вызова веб-API (*Pages/CallWebAPI.razor*).

Сведения о том, как разрешить другим сайтам выполнять запросы на общий доступ к ресурсам независимо от источника (CORS) к приложению, см. в разделе <xref:security/cors>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/blazor/webassembly/additional-scenarios> &ndash; Сведения об использовании класса `HttpClient` для отправки безопасных запросов веб-API.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Конфигурация конечных точек HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Общий доступ к ресурсам независимо от источника (CORS) в W3C](https://www.w3.org/TR/cors/)
