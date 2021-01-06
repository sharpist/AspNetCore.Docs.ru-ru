---
title: Надежные службы gRPC с крайними сроками и отменой
author: jamesnk
description: Узнайте, как создавать надежные службы gRPC с крайними сроками и отменой в .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059927"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="eda54-103">Надежные службы gRPC с крайними сроками и отменой</span><span class="sxs-lookup"><span data-stu-id="eda54-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="eda54-104">Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="eda54-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="eda54-105">Крайние сроки и отмена — это функции, используемые клиентами gRPC для прерывания выполняющихся вызовов.</span><span class="sxs-lookup"><span data-stu-id="eda54-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="eda54-106">В этой статье описывается, в чем заключается важность крайних сроков и отмены и как использовать их в приложениях .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="eda54-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="eda54-107">Крайние сроки</span><span class="sxs-lookup"><span data-stu-id="eda54-107">Deadlines</span></span>

<span data-ttu-id="eda54-108">Крайний срок позволяет клиенту gRPC указать, как долго он будет ожидать завершения вызова.</span><span class="sxs-lookup"><span data-stu-id="eda54-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="eda54-109">При наступлении крайнего срока вызов отменяется.</span><span class="sxs-lookup"><span data-stu-id="eda54-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="eda54-110">Задавать крайний срок важно по той причине, что он ограничивает длительность выполнения вызова.</span><span class="sxs-lookup"><span data-stu-id="eda54-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="eda54-111">Это позволяет предотвратить бесконечное выполнение служб и исчерпание ресурсов сервера.</span><span class="sxs-lookup"><span data-stu-id="eda54-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="eda54-112">Крайние сроки — это полезное средство для повышения надежности приложений, поэтому ими не следует пренебрегать.</span><span class="sxs-lookup"><span data-stu-id="eda54-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="eda54-113">Настройка крайнего срока:</span><span class="sxs-lookup"><span data-stu-id="eda54-113">Deadline configuration:</span></span>

* <span data-ttu-id="eda54-114">Крайний срок настраивается с помощью `CallOptions.Deadline` при выполнении вызова.</span><span class="sxs-lookup"><span data-stu-id="eda54-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="eda54-115">Значение по умолчанию отсутствует.</span><span class="sxs-lookup"><span data-stu-id="eda54-115">There is no default deadline value.</span></span> <span data-ttu-id="eda54-116">Если крайний срок не указан, время выполнения вызовов gRPC не ограничено.</span><span class="sxs-lookup"><span data-stu-id="eda54-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="eda54-117">Крайний срок — это время в формате UTC.</span><span class="sxs-lookup"><span data-stu-id="eda54-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="eda54-118">Например, `DateTime.UtcNow.AddSeconds(5)` означает, что крайний срок наступает через 5 секунд от текущего момента.</span><span class="sxs-lookup"><span data-stu-id="eda54-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="eda54-119">Если указано время в прошлом или текущий момент, крайний срок наступает немедленно.</span><span class="sxs-lookup"><span data-stu-id="eda54-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="eda54-120">Крайний срок передается службе вместе с вызовом gRPC и отслеживается независимо клиентом и службой.</span><span class="sxs-lookup"><span data-stu-id="eda54-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="eda54-121">Возможна ситуация, когда выполнение вызова gRPC завершается на одном компьютере, но к тому времени, когда ответ возвращается клиенту, крайний срок уже истекает.</span><span class="sxs-lookup"><span data-stu-id="eda54-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="eda54-122">При наступлении крайнего срока клиент и служба ведут себя по-разному.</span><span class="sxs-lookup"><span data-stu-id="eda54-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="eda54-123">Клиент немедленно прерывает базовый HTTP-запрос и выдает ошибку `DeadlineExceeded`.</span><span class="sxs-lookup"><span data-stu-id="eda54-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="eda54-124">Клиентское приложение может перехватить ошибку и вывести сообщение об истечении времени ожидания пользователю.</span><span class="sxs-lookup"><span data-stu-id="eda54-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="eda54-125">На сервере выполнение HTTP-запроса прерывается, и вызывается [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken).</span><span class="sxs-lookup"><span data-stu-id="eda54-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="eda54-126">Несмотря на то, что HTTP-запрос прерывается, вызов gRPC продолжает выполняться на сервере до тех пор, пока метод не завершит выполнение.</span><span class="sxs-lookup"><span data-stu-id="eda54-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="eda54-127">Важно передавать токен отмены асинхронным методам, чтобы они отменялись вместе с вызовом.</span><span class="sxs-lookup"><span data-stu-id="eda54-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="eda54-128">Например, необходимо передавать токен отмены в асинхронные запросы к базе данных и HTTP-запросы.</span><span class="sxs-lookup"><span data-stu-id="eda54-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="eda54-129">Передача токена отмены позволяет быстро завершить отмененный вызов на сервере и высвободить ресурсы для других вызовов.</span><span class="sxs-lookup"><span data-stu-id="eda54-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="eda54-130">Чтобы задать крайний срок для вызова gRPC, настройте `CallOptions.Deadline`:</span><span class="sxs-lookup"><span data-stu-id="eda54-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="eda54-131">Используйте `ServerCallContext.CancellationToken` в службе gRPC:</span><span class="sxs-lookup"><span data-stu-id="eda54-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="eda54-132">Распространение крайних сроков</span><span class="sxs-lookup"><span data-stu-id="eda54-132">Propagating deadlines</span></span>

<span data-ttu-id="eda54-133">При выполнении вызова gRPC из работающей службы gRPC необходимо распространить крайний срок.</span><span class="sxs-lookup"><span data-stu-id="eda54-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="eda54-134">Пример:</span><span class="sxs-lookup"><span data-stu-id="eda54-134">For example:</span></span>

1. <span data-ttu-id="eda54-135">Клиентское приложение вызывает `FrontendService.GetUser` с крайним сроком.</span><span class="sxs-lookup"><span data-stu-id="eda54-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="eda54-136">`FrontendService` вызывает `UserService.GetUser`.</span><span class="sxs-lookup"><span data-stu-id="eda54-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="eda54-137">Крайний срок, заданный клиентом, должен быть указан с новым вызовом gRPC.</span><span class="sxs-lookup"><span data-stu-id="eda54-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="eda54-138">`UserService.GetUser` получает крайний срок.</span><span class="sxs-lookup"><span data-stu-id="eda54-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="eda54-139">При наступлении крайнего срока время ожидания в клиентском приложении корректно истекает.</span><span class="sxs-lookup"><span data-stu-id="eda54-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="eda54-140">В контексте вызова крайний срок указывается с помощью `ServerCallContext.Deadline`:</span><span class="sxs-lookup"><span data-stu-id="eda54-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="eda54-141">Ручное распространение крайних сроков может быть обременительным.</span><span class="sxs-lookup"><span data-stu-id="eda54-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="eda54-142">Крайний срок должен быть передан каждому вызову, и легко что-то упустить.</span><span class="sxs-lookup"><span data-stu-id="eda54-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="eda54-143">Фабрика клиента gRPC позволяет автоматизировать этот процесс.</span><span class="sxs-lookup"><span data-stu-id="eda54-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="eda54-144">Указание `EnableCallContextPropagation`:</span><span class="sxs-lookup"><span data-stu-id="eda54-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="eda54-145">автоматически распространяет крайний срок и токен отмены на дочерние вызовы;</span><span class="sxs-lookup"><span data-stu-id="eda54-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="eda54-146">является отличным способом обеспечить распространение крайнего срока и отмены в случае со сложными вложенными вызовами gRPC.</span><span class="sxs-lookup"><span data-stu-id="eda54-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="eda54-147">Для получения дополнительной информации см. <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span><span class="sxs-lookup"><span data-stu-id="eda54-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="eda54-148">Отмена</span><span class="sxs-lookup"><span data-stu-id="eda54-148">Cancellation</span></span>

<span data-ttu-id="eda54-149">Отмена позволяет клиенту gRPC отменять долго выполняющиеся вызовы, которые больше не нужны.</span><span class="sxs-lookup"><span data-stu-id="eda54-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="eda54-150">Например, когда пользователь посещает страницу на веб-сайте, выполняется вызов gRPC для потоковой передачи обновлений в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="eda54-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="eda54-151">Потоковая передача должна быть отменена, когда пользователь покидает страницу.</span><span class="sxs-lookup"><span data-stu-id="eda54-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="eda54-152">Вызов gRPC можно отменить в клиенте путем передачи токена отмены с помощью [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) или вызова `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="eda54-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="eda54-153">Отменяемые службы gRPC должны отвечать указанным ниже требованиям.</span><span class="sxs-lookup"><span data-stu-id="eda54-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="eda54-154">В асинхронные методы должен передаваться `ServerCallContext.CancellationToken`.</span><span class="sxs-lookup"><span data-stu-id="eda54-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="eda54-155">Отмена асинхронных методов позволяет быстро завершить вызов на сервере.</span><span class="sxs-lookup"><span data-stu-id="eda54-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="eda54-156">Токен отмены должен распространяться на дочерние вызовы.</span><span class="sxs-lookup"><span data-stu-id="eda54-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="eda54-157">Распространение токена отмены гарантирует, что дочерние вызовы будут отменены вместе с родительскими.</span><span class="sxs-lookup"><span data-stu-id="eda54-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="eda54-158">[Фабрика клиента gRPC](xref:grpc/clientfactory) и `EnableCallContextPropagation()` автоматически распространяют токен отмены.</span><span class="sxs-lookup"><span data-stu-id="eda54-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eda54-159">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="eda54-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
