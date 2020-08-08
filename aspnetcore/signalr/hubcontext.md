---
title: SignalRхубконтекст
author: bradygaster
description: Узнайте, как использовать службу ASP.NET Core SignalR хубконтекст для отправки уведомлений клиентам за пределами концентратора.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hubcontext
ms.openlocfilehash: b9adc54c1928d6ec11f707b2bd5e1e297973f1ae
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021930"
---
# <a name="send-messages-from-outside-a-hub"></a>Отправка сообщений из-за пределов концентратора

Авторы: [Микаэль Менгисту](https://twitter.com/MikaelM_12) (Mikael Mengistu)

SignalRЦентр является основной абстракцией для отправки сообщений клиентам, подключенным к SignalR серверу. Кроме того, можно отправить сообщения из других мест в приложении с помощью `IHubContext` службы. В этой статье объясняется, как получить доступ к, SignalR `IHubContext` чтобы отправлять уведомления клиентам извне концентратора.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) ([описание загрузки](xref:index#how-to-download-a-sample))

## <a name="get-an-instance-of-ihubcontext"></a>Получение экземпляра Ихубконтекст

В ASP.NET Core SignalR можно получить доступ к экземпляру `IHubContext` через внедрение зависимостей. Экземпляр можно внедрить `IHubContext` в контроллер, по промежуточного слоя или в другую службу di. Используйте экземпляр для отправки сообщений клиентам.

> [!NOTE]
> Это отличается от ASP.NET 4. x SignalR , который использовал GlobalHost для предоставления доступа к `IHubContext` . ASP.NET Core имеет платформу внедрения зависимостей, которая устраняет необходимость в этом глобальном одноэлементном наборе.

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>Внедрение экземпляра Ихубконтекст в контроллер

Вы можете внедрить экземпляр `IHubContext` в контроллер, добавив его в конструктор:

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

Теперь, используя доступ к экземпляру `IHubContext` , можно вызывать методы концентратора, как если бы вы были в самом концентраторе.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>Получение экземпляра Ихубконтекст по промежуточного слоя

Получите доступ к в `IHubContext` конвейере по промежуточного слоя следующим образом:

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> Когда методы концентратора вызываются извне `Hub` класса, не существует вызывающего объекта, связанного с этим вызовом. Поэтому нет доступа к `ConnectionId` `Caller` `Others` свойствам, и.

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a>Получение экземпляра Ихубконтекст из Ихост

Доступ к `IHubContext` из веб-узла полезен для интеграции с областями за пределами ASP.NET Core, например с помощью сторонних платформ внедрения зависимостей:

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a>Внедрить строго типизированный Хубконтекст

Чтобы внедрить строго типизированный Хубконтекст, убедитесь, что ваш центр наследует от `Hub<T>` . Вставьте его, используя `IHubContext<THub, T>` интерфейс, а не `IHubContext<THub>` .

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a>Связанные ресурсы

* [Начало работы](xref:tutorials/signalr)
* [Концентраторы](xref:signalr/hubs)
* [Публикация в Azure](xref:signalr/publish-to-azure-web-app)
