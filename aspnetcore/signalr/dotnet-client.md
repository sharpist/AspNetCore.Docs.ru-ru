---
title: ASP.NET Core SignalR клиент .NET
author: bradygaster
description: Сведения о клиенте SignalR ASP.NET Core .NET
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: 77d7eb81abc4ec7a6f4f15bbe5d96cedc64cb330
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767217"
---
# <a name="aspnet-core-signalr-net-client"></a>Клиентский ASP.NET Core SignalR .NET

Клиентская библиотека ASP.NET Core SignalR .NET позволяет взаимодействовать с концентраторами SignalR из приложений .NET.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([как скачивать](xref:index#how-to-download-a-sample))

Пример кода в этой статье — это приложение WPF, которое использует клиентский ASP.NET Core SignalR .NET.

## <a name="install-the-signalr-net-client-package"></a>Установка пакета клиента SignalR .NET

Пакет [Microsoft. AspNetCore. SignalR. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) необходим для подключения клиентов .NET к концентраторам SignalR.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Чтобы установить клиентскую библиотеку, в окне **консоли диспетчера пакетов** выполните следующую команду:

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Чтобы установить клиентскую библиотеку, выполните в командной оболочке следующую команду:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Подключение к концентратору

Чтобы установить соединение, создайте вызов `HubConnectionBuilder` `Build`и. При создании соединения можно настроить URL-адрес концентратора, протокол, тип транспорта, уровень ведения журнала, заголовки и другие параметры. Настройте все необходимые параметры, вставив любой из `HubConnectionBuilder` методов в. `Build` Запустите соединение с `StartAsync`.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Обработано потерянное подключение

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Автоматическое повторное подключение

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> Можно настроить для автоматического повторного подключения с помощью `WithAutomaticReconnect` метода в <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>. По умолчанию оно не будет автоматически восстановлено.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

Без каких бы то `WithAutomaticReconnect()` ни было параметров, настраивает клиент на ожидание 0, 2, 10 и 30 секунд соответственно, прежде чем пытаться выполнить каждую попытку повторного подключения, останавливая после четырех неудачных попыток.

Перед запуском всех попыток `HubConnection` повторного подключения компонент переходит в `HubConnectionState.Reconnecting` состояние и запускает `Reconnecting` событие.  Это дает возможность предупредить пользователей о потере подключения и отключении элементов пользовательского интерфейса. Неинтерактивные приложения могут запускать постановку в очередь или удалять сообщения.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Если клиент успешно повторно подключится в течение первых четырех попыток, компонент `HubConnection` вернется в `Connected` состояние и начнет `Reconnected` событие. Это дает возможность информировать пользователей о том, что подключение было восстановлено, и выводит сообщения из очереди.

Так как соединение выглядит совершенно только для сервера, для обработчиков `ConnectionId` `Reconnected` событий будет предоставлен новый.

> [!WARNING]
> Параметр обработчика `Reconnected` событий будет иметь значение null, `HubConnection` если был настроен на [пропуск согласования.](xref:signalr/configuration#configure-client-options) `connectionId`

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()`не настраивается `HubConnection` для повтора начальных сбоев запуска, поэтому ошибки запуска должны быть обработаны вручную:

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

Если клиент не `HubConnection` будет успешно подключаться в течение первых четырех попыток, компонент перейдет `Disconnected` в состояние и запустит <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> событие. Это дает возможность попытаться вручную перезапустить подключение или сообщить пользователям, что подключение было безвозвратно утрачено.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Чтобы настроить настраиваемое число попыток повторного подключения перед отключением или изменением времени повторного подключения, принимает `WithAutomaticReconnect` массив чисел, представляющих задержку в миллисекундах, прежде чем начинать каждую попытку повторного подключения.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

В предыдущем примере настраивается `HubConnection` для запуска попытки повторного подключения сразу после потери соединения. Это также справедливо для конфигурации по умолчанию.

Если первая попытка повторного подключения завершается неудачно, вторая попытка повторного подключения также начнется немедленно, а не подождать 2 секунды, как в конфигурации по умолчанию.

Если вторая попытка повторного подключения завершается неудачно, третья попытка повторного подключения начнется через 10 секунд, что опять же, как и конфигурация по умолчанию.

Затем пользовательское поведение снова рассходится от поведения по умолчанию, останавливаясь после третьего сбоя попытки повторного подключения. В конфигурации по умолчанию будет предпринята еще одна повторная попытки подключения через 30 секунд.

Если требуется даже больший контроль над временем и числом попыток автоматического повторного подключения, `WithAutomaticReconnect` принимает объект, реализующий `IRetryPolicy` интерфейс, который имеет единственный метод с именем `NextRetryDelay`.

`NextRetryDelay`принимает один аргумент с типом `RetryContext`. `RetryContext` Имеет `PreviousRetryCount`три свойства:, `ElapsedTime` и `RetryReason`, которые являются `long`, а `TimeSpan` и `Exception` соответственно. Перед первой попыткой повторного подключения обе `PreviousRetryCount` и `ElapsedTime` будут равны нулю, и `RetryReason` будет исключено, что привело к потере соединения. После каждой неудачной попытки повтора значение `PreviousRetryCount` будет увеличено на единицу, `ElapsedTime` будет обновлено с учетом времени, затраченного на повторное подключение, и `RetryReason` будет исключено, что привело к сбою последней попытки повторного подключения.

`NextRetryDelay`должен возвращать значение TimeSpan, представляющее время ожидания перед следующей попыткой повторного подключения `null` или `HubConnection` отмене повторного подключения.

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

Кроме того, можно написать код, который будет повторно подключаться к клиенту вручную, как показано в [подсоединении вручную](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Повторное подключение вручную

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> До 3,0 клиент .NET для SignalR не выполняет автоматическое повторное подключение. Необходимо написать код, который будет повторно подключаться к клиенту вручную.

::: moniker-end

Используйте <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> событие для реагирования на потерянное подключение. Например, может потребоваться автоматизировать повторное подключение.

Для `Closed` этого события требуется делегат, возвращающий `Task`, что позволяет асинхронному коду выполняться без `async void`использования. Чтобы удовлетворить сигнатуру делегата в `Closed` обработчике событий, который выполняется синхронно `Task.CompletedTask`, возвращается:

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Основная причина поддержки асинхронности заключается в том, что вы можете перезапустить подключение. Запуск соединения является асинхронным действием.

В `Closed` обработчике, который перезапускает подключение, рассмотрите возможность ожидания некоторой случайной задержки, чтобы предотвратить перегрузку сервера, как показано в следующем примере:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Методы концентратора вызовов от клиента

`InvokeAsync`вызывает методы в концентраторе. Передайте имя метода концентратора и все аргументы, определенные в методе `InvokeAsync`концентратора, в. SignalRявляется асинхронным, поэтому `async` используйте `await` и при выполнении вызовов.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

`InvokeAsync` Метод возвращает, `Task` который завершается, когда метод сервера возвращает значение. Возвращаемое значение, если таковое имеется, предоставляется в качестве результата `Task`. Все исключения, вызванные методом на сервере, вызывают сбой `Task`. Используйте `await` синтаксис, чтобы дождаться завершения метода сервера и `try...catch` синтаксиса для обработки ошибок.

`SendAsync` Метод возвращает, `Task` который завершается, когда сообщение было отправлено на сервер. Возвращаемое значение не предоставляется, так `Task` как это не ждет завершения метода сервера. Все исключения, возникающие на клиенте при отправке сообщения, вызывают `Task`сбой. Использование `await` синтаксиса и `try...catch` для управления ошибками отправки.

> [!NOTE]
> Если вы используете службу Azure SignalR в *бессерверном режиме*, вы не можете вызывать методы концентратора из клиента. Дополнительные сведения см. в [ SignalR документации по службе](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Вызов методов клиента из концентратора

Определите методы, которые концентратор вызывает `connection.On` с помощью после сборки, но перед запуском соединения.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Приведенный выше код `connection.On` выполняется, когда код на стороне сервера вызывает его с `SendAsync` помощью метода.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Обработка ошибок и ведение журнала

Обрабатывайте ошибки с помощью оператора try-catch. Проверьте `Exception` объект, чтобы определить необходимые действия после возникновения ошибки.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Концентраторы](xref:signalr/hubs)
* [Клиент JavaScript](xref:signalr/javascript-client)
* [Публикация в Azure](xref:signalr/publish-to-azure-web-app)
* [Документация SignalR , посвященная бессерверной службе Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
