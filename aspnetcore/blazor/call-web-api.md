---
title: Вызов веб-API из ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как вызывать веб-API из приложения Blazor WebAssembly с помощью вспомогательных методов JSON, включая создание запросов на общий доступ к ресурсам независимо от источника (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: d823db3688e05f6befefacc9f390e0dcdbf329a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767152"
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

  В следующем коде `_todoItems` отображаются компонентом. Метод `GetTodoItems` срабатывает после завершения подготовки компонента к просмотру ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Полный пример см. в примере приложения.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync` &ndash; Отправляет запрос HTTP POST, включая содержимое в кодировке JSON, и анализирует текст ответа JSON для создания объекта.

  В следующем коде `_newItemName` предоставляется связанным элементом компонента. Метод `AddItem` активируется путем выбора элемента `<button>`. Полный пример см. в примере приложения.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  Вызовы к `PostAsJsonAsync` возвращают <xref:System.Net.Http.HttpResponseMessage>. Чтобы десериализовать содержимое JSON из ответного сообщения, используйте метод расширения `ReadFromJsonAsync<T>`:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync` &ndash; Отправляет запрос HTTP PUT, включая содержимое в кодировке JSON.

  В следующем коде значения `_editItem` для `Name` и `IsCompleted` предоставляются связанными элементами компонента. Элемент `Id` задается, когда элемент выбирается в другой части пользовательского интерфейса и вызывается `EditItem`. Метод `SaveItem` активируется путем выбора элемента `<button>` "Save". Полный пример см. в примере приложения.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
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

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>Общий доступ к ресурсам независимо от источника (CORS)

Безопасность в браузере предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу. Это ограничение называется *политика одного источника*. Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта. Для выполнения запросов из браузера к конечной точке с другим источником *конечная точка* должна включать [общий доступ к ресурсам независимо от источника (CORS)](https://www.w3.org/TR/cors/).

В [примере приложения Blazor WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) показано использование CORS в компоненте вызова веб-API (*Pages/CallWebAPI.razor*).

Сведения о том, как разрешить другим сайтам выполнять запросы на общий доступ к ресурсам независимо от источника (CORS) к приложению, см. в разделе <xref:security/cors>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Конфигурация конечных точек HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Общий доступ к ресурсам независимо от источника (CORS) в W3C](https://www.w3.org/TR/cors/)
