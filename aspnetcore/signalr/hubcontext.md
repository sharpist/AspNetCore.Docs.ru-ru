---
title: 'SignalR хубконтекст'
author: bradygaster
description: 'Узнайте, как использовать службу ASP.NET Core SignalR хубконтекст для отправки уведомлений клиентам за пределами концентратора.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
- 'IHubContext'
uid: signalr/hubcontext
ms.openlocfilehash: 0b1940dc85634051e8a566c6859f51c130b69269
ms.sourcegitcommit: 1b7f2e1aabf43fa93b920cad36515d7336bfc2df
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93066737"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="87b6b-103">Отправка сообщений из-за пределов концентратора</span><span class="sxs-lookup"><span data-stu-id="87b6b-103">Send messages from outside a hub</span></span>

<span data-ttu-id="87b6b-104">Авторы: [Микаэль Менгисту](https://twitter.com/MikaelM_12) (Mikael Mengistu)</span><span class="sxs-lookup"><span data-stu-id="87b6b-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="87b6b-105">SignalRЦентр является основной абстракцией для отправки сообщений клиентам, подключенным к SignalR серверу.</span><span class="sxs-lookup"><span data-stu-id="87b6b-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="87b6b-106">Кроме того, можно отправить сообщения из других мест в приложении с помощью `IHubContext` службы.</span><span class="sxs-lookup"><span data-stu-id="87b6b-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="87b6b-107">В этой статье объясняется, как получить доступ к, SignalR `IHubContext` чтобы отправлять уведомления клиентам извне концентратора.</span><span class="sxs-lookup"><span data-stu-id="87b6b-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="87b6b-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(описание загрузки)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="87b6b-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-no-locihubcontext"></a><span data-ttu-id="87b6b-109">Получение экземпляра IHubContext</span><span class="sxs-lookup"><span data-stu-id="87b6b-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="87b6b-110">В ASP.NET Core SignalR можно получить доступ к экземпляру `IHubContext` через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="87b6b-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="87b6b-111">Экземпляр можно внедрить `IHubContext` в контроллер, по промежуточного слоя или в другую службу di.</span><span class="sxs-lookup"><span data-stu-id="87b6b-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="87b6b-112">Используйте экземпляр для отправки сообщений клиентам.</span><span class="sxs-lookup"><span data-stu-id="87b6b-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="87b6b-113">Это отличается от ASP.NET 4. x SignalR , который использовал GlobalHost для предоставления доступа к `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="87b6b-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="87b6b-114">ASP.NET Core имеет платформу внедрения зависимостей, которая устраняет необходимость в этом глобальном одноэлементном наборе.</span><span class="sxs-lookup"><span data-stu-id="87b6b-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-no-locihubcontext-in-a-controller"></a><span data-ttu-id="87b6b-115">Внедрение экземпляра IHubContext в контроллер</span><span class="sxs-lookup"><span data-stu-id="87b6b-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="87b6b-116">Вы можете внедрить экземпляр `IHubContext` в контроллер, добавив его в конструктор:</span><span class="sxs-lookup"><span data-stu-id="87b6b-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="87b6b-117">Теперь, используя доступ к экземпляру `IHubContext` , можно вызывать методы концентратора, как если бы вы были в самом концентраторе.</span><span class="sxs-lookup"><span data-stu-id="87b6b-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-no-locihubcontext-in-middleware"></a><span data-ttu-id="87b6b-118">Получить экземпляр IHubContext по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="87b6b-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="87b6b-119">Получите доступ к в `IHubContext` конвейере по промежуточного слоя следующим образом:</span><span class="sxs-lookup"><span data-stu-id="87b6b-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

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
> <span data-ttu-id="87b6b-120">Когда методы концентратора вызываются извне `Hub` класса, не существует вызывающего объекта, связанного с этим вызовом.</span><span class="sxs-lookup"><span data-stu-id="87b6b-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="87b6b-121">Поэтому нет доступа к `ConnectionId` `Caller` `Others` свойствам, и.</span><span class="sxs-lookup"><span data-stu-id="87b6b-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-no-locihubcontext-from-ihost"></a><span data-ttu-id="87b6b-122">Получение экземпляра IHubContext из ихост</span><span class="sxs-lookup"><span data-stu-id="87b6b-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="87b6b-123">Доступ к `IHubContext` из веб-узла полезен для интеграции с областями за пределами ASP.NET Core, например с помощью сторонних платформ внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="87b6b-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

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

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="87b6b-124">Внедрить строго типизированный Хубконтекст</span><span class="sxs-lookup"><span data-stu-id="87b6b-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="87b6b-125">Чтобы внедрить строго типизированный Хубконтекст, убедитесь, что ваш центр наследует от `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="87b6b-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="87b6b-126">Вставьте его, используя `IHubContext<THub, T>` интерфейс, а не `IHubContext<THub>` .</span><span class="sxs-lookup"><span data-stu-id="87b6b-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

<span data-ttu-id="87b6b-127">Дополнительные сведения см. в разделе [строго типизированные концентраторы](xref:signalr/hubs#strongly-typed-hubs) .</span><span class="sxs-lookup"><span data-stu-id="87b6b-127">See [Strongly typed hubs](xref:signalr/hubs#strongly-typed-hubs) for more information.</span></span>

## <a name="related-resources"></a><span data-ttu-id="87b6b-128">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="87b6b-128">Related resources</span></span>

* [<span data-ttu-id="87b6b-129">Начало работы</span><span class="sxs-lookup"><span data-stu-id="87b6b-129">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="87b6b-130">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="87b6b-130">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="87b6b-131">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="87b6b-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
