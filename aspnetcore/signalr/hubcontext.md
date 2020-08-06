---
title: SignalRхубконтекст
author: bradygaster
description: Узнайте, как использовать службу ASP.NET Core SignalR хубконтекст для отправки уведомлений клиентам за пределами концентратора.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hubcontext
ms.openlocfilehash: d38cb5b3e818879d3ec40a927acfdd69dc85377a
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819254"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="5fb52-103">Отправка сообщений из-за пределов концентратора</span><span class="sxs-lookup"><span data-stu-id="5fb52-103">Send messages from outside a hub</span></span>

<span data-ttu-id="5fb52-104">Авторы: [Микаэль Менгисту](https://twitter.com/MikaelM_12) (Mikael Mengistu)</span><span class="sxs-lookup"><span data-stu-id="5fb52-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="5fb52-105">SignalRЦентр является основной абстракцией для отправки сообщений клиентам, подключенным к SignalR серверу.</span><span class="sxs-lookup"><span data-stu-id="5fb52-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="5fb52-106">Кроме того, можно отправить сообщения из других мест в приложении с помощью `IHubContext` службы.</span><span class="sxs-lookup"><span data-stu-id="5fb52-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="5fb52-107">В этой статье объясняется, как получить доступ к, SignalR `IHubContext` чтобы отправлять уведомления клиентам извне концентратора.</span><span class="sxs-lookup"><span data-stu-id="5fb52-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="5fb52-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) ([описание загрузки](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5fb52-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="5fb52-109">Получение экземпляра Ихубконтекст</span><span class="sxs-lookup"><span data-stu-id="5fb52-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="5fb52-110">В ASP.NET Core SignalR можно получить доступ к экземпляру `IHubContext` через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fb52-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="5fb52-111">Экземпляр можно внедрить `IHubContext` в контроллер, по промежуточного слоя или в другую службу di.</span><span class="sxs-lookup"><span data-stu-id="5fb52-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="5fb52-112">Используйте экземпляр для отправки сообщений клиентам.</span><span class="sxs-lookup"><span data-stu-id="5fb52-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="5fb52-113">Это отличается от ASP.NET 4. x SignalR , который использовал GlobalHost для предоставления доступа к `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="5fb52-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="5fb52-114">ASP.NET Core имеет платформу внедрения зависимостей, которая устраняет необходимость в этом глобальном одноэлементном наборе.</span><span class="sxs-lookup"><span data-stu-id="5fb52-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="5fb52-115">Внедрение экземпляра Ихубконтекст в контроллер</span><span class="sxs-lookup"><span data-stu-id="5fb52-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="5fb52-116">Вы можете внедрить экземпляр `IHubContext` в контроллер, добавив его в конструктор:</span><span class="sxs-lookup"><span data-stu-id="5fb52-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="5fb52-117">Теперь, используя доступ к экземпляру `IHubContext` , можно вызывать методы концентратора, как если бы вы были в самом концентраторе.</span><span class="sxs-lookup"><span data-stu-id="5fb52-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="5fb52-118">Получение экземпляра Ихубконтекст по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="5fb52-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="5fb52-119">Получите доступ к в `IHubContext` конвейере по промежуточного слоя следующим образом:</span><span class="sxs-lookup"><span data-stu-id="5fb52-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

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
> <span data-ttu-id="5fb52-120">Когда методы концентратора вызываются извне `Hub` класса, не существует вызывающего объекта, связанного с этим вызовом.</span><span class="sxs-lookup"><span data-stu-id="5fb52-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="5fb52-121">Поэтому нет доступа к `ConnectionId` `Caller` `Others` свойствам, и.</span><span class="sxs-lookup"><span data-stu-id="5fb52-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="5fb52-122">Получение экземпляра Ихубконтекст из Ихост</span><span class="sxs-lookup"><span data-stu-id="5fb52-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="5fb52-123">Доступ к `IHubContext` из веб-узла полезен для интеграции с областями за пределами ASP.NET Core, например с помощью сторонних платформ внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="5fb52-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

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

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="5fb52-124">Внедрить строго типизированный Хубконтекст</span><span class="sxs-lookup"><span data-stu-id="5fb52-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="5fb52-125">Чтобы внедрить строго типизированный Хубконтекст, убедитесь, что ваш центр наследует от `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="5fb52-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="5fb52-126">Вставьте его, используя `IHubContext<THub, T>` интерфейс, а не `IHubContext<THub>` .</span><span class="sxs-lookup"><span data-stu-id="5fb52-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

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

## <a name="related-resources"></a><span data-ttu-id="5fb52-127">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5fb52-127">Related resources</span></span>

* [<span data-ttu-id="5fb52-128">Начало работы</span><span class="sxs-lookup"><span data-stu-id="5fb52-128">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5fb52-129">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="5fb52-129">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="5fb52-130">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="5fb52-130">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
