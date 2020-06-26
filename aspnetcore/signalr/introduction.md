---
title: Введение в ASP.NET Core SignalR
author: bradygaster
description: Узнайте, как SignalR библиотека ASP.NET Core упрощает добавление в приложения функций в режиме реального времени.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 816ecfc5d23e8e1d2901a8c35c657cc968fa95df
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404955"
---
# <a name="introduction-to-aspnet-core-signalr"></a><span data-ttu-id="bbe8d-103">Введение в ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="bbe8d-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-signalr"></a><span data-ttu-id="bbe8d-104">Что такое SignalR?</span><span class="sxs-lookup"><span data-stu-id="bbe8d-104">What is SignalR?</span></span>

<span data-ttu-id="bbe8d-105">ASP.NET Core SignalR — это библиотека с открытым исходным кодом, которая упрощает добавление веб-функций в режиме реального времени в приложения.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="bbe8d-106">Веб-функции в режиме реального времени позволяют коду на стороне сервера мгновенно отправлять содержимое на клиенты.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="bbe8d-107">Хорошие кандидаты для SignalR :</span><span class="sxs-lookup"><span data-stu-id="bbe8d-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="bbe8d-108">Приложения, требующие частых обновлений с сервера.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="bbe8d-109">Примерами являются игры, социальные сети, платформы для голосования, аукционы, карты и приложения GPS.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="bbe8d-110">Панели и приложения мониторинга.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="bbe8d-111">Примеры включают в себя панели мониторинга компании, мгновенные обновления продаж или оповещения о поездках.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="bbe8d-112">Приложения для совместной работы.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-112">Collaborative apps.</span></span> <span data-ttu-id="bbe8d-113">Примерами таких приложений служат демонстрационные приложения и программное обеспечение для конференций .</span><span class="sxs-lookup"><span data-stu-id="bbe8d-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="bbe8d-114">Приложения, которым требуются уведомления.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-114">Apps that require notifications.</span></span> <span data-ttu-id="bbe8d-115">Уведомления используют социальные сети, электронная почта, чат, игры, оповещения о поездках и многие другие приложения.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

SignalR<span data-ttu-id="bbe8d-116">предоставляет API для создания [удаленных вызовов процедур (RPC) "](https://wikipedia.org/wiki/Remote_procedure_call)сервер-клиент".</span><span class="sxs-lookup"><span data-stu-id="bbe8d-116"> provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="bbe8d-117">Вызов RPC вызывает функции JavaScript на клиентах из кода .NET Core на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="bbe8d-118">Ниже приведены некоторые функции SignalR для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="bbe8d-119">Управляет автоматическим управлением соединениями.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="bbe8d-120">Отправляет сообщения всем подключенным клиентам одновременно.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="bbe8d-121">Например, комната чатов.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-121">For example, a chat room.</span></span>
* <span data-ttu-id="bbe8d-122">Отправляет сообщения конкретным клиентам или группам клиентов.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="bbe8d-123">Масштабируется для управления увеличением трафика.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="bbe8d-124">Источник размещается в [ SignalR репозитории на сайте GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span><span class="sxs-lookup"><span data-stu-id="bbe8d-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="bbe8d-125">Транспорты</span><span class="sxs-lookup"><span data-stu-id="bbe8d-125">Transports</span></span>

SignalR<span data-ttu-id="bbe8d-126">поддерживает следующие методы обработки обмена данными в режиме реального времени (в порядке правильного резерва):</span><span class="sxs-lookup"><span data-stu-id="bbe8d-126"> supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="bbe8d-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="bbe8d-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="bbe8d-128">События, отправленные сервером</span><span class="sxs-lookup"><span data-stu-id="bbe8d-128">Server-Sent Events</span></span>
* <span data-ttu-id="bbe8d-129">Длительный опрос</span><span class="sxs-lookup"><span data-stu-id="bbe8d-129">Long Polling</span></span>

SignalR<span data-ttu-id="bbe8d-130">автоматически выбирает лучший транспортный метод, который находится в пределах возможностей сервера и клиента.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-130"> automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="bbe8d-131">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="bbe8d-131">Hubs</span></span>

SignalR<span data-ttu-id="bbe8d-132">использует *концентраторы* для взаимодействия между клиентами и серверами.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-132"> uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="bbe8d-133">Концентратор — это высокоуровневый конвейер, который позволяет клиенту и серверу вызывать методы друг друга.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> SignalR<span data-ttu-id="bbe8d-134">автоматически обрабатывает диспетчеризации между компьютерами, позволяя клиентам вызывать методы на сервере и наоборот.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-134"> handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="bbe8d-135">Можно передать строго типизированные параметры методам, которые обеспечивают привязку модели.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> SignalR<span data-ttu-id="bbe8d-136">предоставляет два встроенных протокола концентратора: текстовый протокол на основе JSON и двоичный протокол на основе [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="bbe8d-136"> provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="bbe8d-137">MessagePack обычно создает меньшие сообщения по сравнению с JSON.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="bbe8d-138">Более старые браузеры должны поддерживать [XHR уровня 2](https://caniuse.com/#feat=xhr2) , чтобы обеспечить поддержку протокола MessagePack.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="bbe8d-139">Концентраторы вызывают код на стороне клиента, отправляя сообщения, содержащие имя и параметры клиентского метода.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="bbe8d-140">Объекты, отправляемые как параметры метода, десериализованы с помощью настроенного протокола.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="bbe8d-141">Клиент пытается сопоставить имя с методом в коде на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="bbe8d-142">Когда клиент находит совпадение, он вызывает метод и передает ему данные десериализованного параметра.</span><span class="sxs-lookup"><span data-stu-id="bbe8d-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bbe8d-143">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="bbe8d-143">Additional resources</span></span>

* <span data-ttu-id="bbe8d-144">[Начало работы с SignalR для ASP.NET Core](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="bbe8d-144">[Get started with SignalR for ASP.NET Core](xref:tutorials/signalr)</span></span>
* [<span data-ttu-id="bbe8d-145">Поддерживаемые платформы</span><span class="sxs-lookup"><span data-stu-id="bbe8d-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="bbe8d-146">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="bbe8d-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="bbe8d-147">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="bbe8d-147">JavaScript client</span></span>](xref:signalr/javascript-client)
