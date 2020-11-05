---
title: Зачем выполнять миграцию WCF на ASP.NET Core gRPC
author: markrendle
description: В этой статье приводятся сводные сведения о достоинствах ASP.NET Core gRPC для разработчиков на Windows Communication Foundation (WCF), которые хотят перейти на современные архитектуры и платформы.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058692"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="fb6ef-103">gRPC для разработчиков на Windows Communication Foundation (WCF)</span><span class="sxs-lookup"><span data-stu-id="fb6ef-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="fb6ef-104">В этой статье приводятся сводные сведения о достоинствах ASP.NET Core gRPC для разработчиков на Windows Communication Foundation (WCF), которые хотят перейти на современные архитектуры и платформы.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="fb6ef-105">Сравнение с WCF</span><span class="sxs-lookup"><span data-stu-id="fb6ef-105">Comparison to WCF</span></span>

<span data-ttu-id="fb6ef-106">Хотя реализация и подход в случае с gRPC отличаются, принципы разработки и использования служб с помощью gRPC должны быть интуитивно понятными для разработчиков на WCF.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="fb6ef-107">WCF и gRPC — это платформы удаленного вызова процедур (RPC), преследующие одинаковые цели:</span><span class="sxs-lookup"><span data-stu-id="fb6ef-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="fb6ef-108">возможность программирования клиента и сервера так, как будто они находятся на одной платформе;</span><span class="sxs-lookup"><span data-stu-id="fb6ef-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="fb6ef-109">предоставление упрощенного переносимого API для сетевого взаимодействия.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="fb6ef-110">Обе платформы требуют объявления и реализации интерфейса, хотя процесс объявления интерфейса отличается.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="fb6ef-111">Многие типы вызовов RPC, которые поддерживает gRPC, сопоставимы с привязками, доступными для служб WCF.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="fb6ef-112">Дополнительные сведения и примеры см. в разделе [Перенос решения WCF в gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span><span class="sxs-lookup"><span data-stu-id="fb6ef-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="fb6ef-113">Преимущества gRPC</span><span class="sxs-lookup"><span data-stu-id="fb6ef-113">Benefits of gRPC</span></span>

<span data-ttu-id="fb6ef-114">gRPC предоставляет собой более эффективную платформу по сравнению с другими подходами по указанным ниже причинам.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="fb6ef-115">Производительность</span><span class="sxs-lookup"><span data-stu-id="fb6ef-115">Performance</span></span>

<span data-ttu-id="fb6ef-116">gRPC использует HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="fb6ef-117">По сравнению с HTTP/1.1 протокол HTTP/2 имеет следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="fb6ef-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="fb6ef-118">представляет собой более компактный и быстрый двоичный протокол;</span><span class="sxs-lookup"><span data-stu-id="fb6ef-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="fb6ef-119">более эффективно анализируется компьютерами;</span><span class="sxs-lookup"><span data-stu-id="fb6ef-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="fb6ef-120">поддерживает мультиплексирование запросов через одно соединение.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="fb6ef-121">Мультиплексирование позволяет отправлять несколько запросов через одно соединение так, что они не блокируют друг друга.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="fb6ef-122">В HTTP/1.1 такое блокирование называется "блокировкой очереди (HOL)".</span><span class="sxs-lookup"><span data-stu-id="fb6ef-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="fb6ef-123">Для сериализации сообщений gRPC использует Protobuf, эффективный двоичный формат.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="fb6ef-124">Сообщения Protobuf:</span><span class="sxs-lookup"><span data-stu-id="fb6ef-124">Protobuf messages are:</span></span>
* <span data-ttu-id="fb6ef-125">быстро сериализуются и десериализуются;</span><span class="sxs-lookup"><span data-stu-id="fb6ef-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="fb6ef-126">используют меньше пропускной способности, чем текстовые форматы.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="fb6ef-127">gRPC — это хорошее решение для мобильных устройств и сетей с ограниченной пропускной способностью.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="fb6ef-128">Совместимость</span><span class="sxs-lookup"><span data-stu-id="fb6ef-128">Interoperability</span></span>

<span data-ttu-id="fb6ef-129">Существуют средства и библиотеки gRPC для всех основных языков программирования и платформ, включая .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby и PHP.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="fb6ef-130">Благодаря двоичному формату подключения Protobuf и эффективному формированию кода для каждой платформы разработчики могут создавать производительные кроссплатформенные приложения.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="fb6ef-131">Удобство использования и производительность</span><span class="sxs-lookup"><span data-stu-id="fb6ef-131">Usability and productivity</span></span>

<span data-ttu-id="fb6ef-132">gRPC — это комплексное решение RPC.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="fb6ef-133">Оно работает согласованно на различных языках и платформах.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="fb6ef-134">Для него также доступен развитый инструментарий, причем большая часть стандартного кода создается автоматически.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="fb6ef-135">Как и WCF, gRPC автоматически создает сообщения и строго типизированный клиент.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="fb6ef-136">Разработчик может сосредоточиться на бизнес-логике.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="fb6ef-137">Потоковые операторы</span><span class="sxs-lookup"><span data-stu-id="fb6ef-137">Streaming</span></span>

<span data-ttu-id="fb6ef-138">gRPC поддерживает двунаправленную потоковую передачу, которая обеспечивает функциональность, аналогичную полнодуплексным службам WCF.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="fb6ef-139">Потоковая передача gRPC может производиться по обычным подключениям через Интернет, посредством подсистем балансировки нагрузки и через одноранговые сети служб.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="fb6ef-140">Крайний срок, время ожидания и отмена</span><span class="sxs-lookup"><span data-stu-id="fb6ef-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="fb6ef-141">gRPC позволяет клиентам указать максимальное время, в течение которого должен быть завершен вызов RPC.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="fb6ef-142">При истечении указанного крайнего срока сервер может отменить операцию независимо от клиента.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="fb6ef-143">Крайний срок и отмена могут распространяться через последующие вызовы gRPC с целью применения ограничений на использование ресурсов.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="fb6ef-144">Клиенты могут прекращать операции при наступлении крайнего срока или ранее при необходимости.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="fb6ef-145">Например, клиент может прервать операцию из-за действия пользователя.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="fb6ef-146">Безопасность</span><span class="sxs-lookup"><span data-stu-id="fb6ef-146">Security</span></span>

<span data-ttu-id="fb6ef-147">gRPC может использовать протоколы TLS и HTTP/2 для обеспечения соединения между клиентом и сервером со сквозным шифрованием.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="fb6ef-148">Поддержка проверки подлинности с помощью сертификата клиента еще более усиливает безопасность и доверие между клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="fb6ef-149">gRPC как путь перехода с WCF на .NET Core и .NET 5</span><span class="sxs-lookup"><span data-stu-id="fb6ef-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="fb6ef-150">.NET Core и .NET 5 знаменуют собой сдвиг в подходе корпорации Майкрософт к предоставлению решений для удаленного взаимодействия разработчикам, которые хотят создавать службы на различных платформах.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="fb6ef-151">.NET Core и .NET 5 поддерживают [вызов служб WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), но не предлагают поддержки для размещения WCF на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="fb6ef-152">Существует два рекомендуемых способа модернизации приложений WCF.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="fb6ef-153">Платформа gRPC основана на современных технологиях и стала наиболее популярным выбором для приложений RPC среди сообщества разработчиков.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="fb6ef-154">Начиная с .NET Core 3.0 современные платформы .NET обладают отличной поддержкой gRPC.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="fb6ef-155">Перенос служб WCF на gRPC помогает обеспечить функциональность RPC, производительность и взаимодействие, необходимые в современных приложениях.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="fb6ef-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) — это проект сообщества, цель которого — обеспечить поддержку размещения служб WCF в .NET Core и .NET 5.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="fb6ef-157">В настоящее время доступен предварительный выпуск, и идет работа над рабочей версией.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="fb6ef-158">CoreWCF поддерживает только часть функций WCF. Для успешной работы приложений .NET Framework, которые переносятся на эту платформу, потребуется внести изменения в код и провести тестирование.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="fb6ef-159">CoreWCF — подходящий вариант, если приложение должно сохранять совместимость с существующими клиентами, вызывающими службы WCF.</span><span class="sxs-lookup"><span data-stu-id="fb6ef-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="fb6ef-160">Начало работы</span><span class="sxs-lookup"><span data-stu-id="fb6ef-160">Get started</span></span>

<span data-ttu-id="fb6ef-161">Подробные рекомендации по созданию служб gRPC в ASP.NET Core для разработчиков на WCF см. в статье [ASP.NET Core gRPC для разработчиков WCF](/dotnet/architecture/grpc-for-wcf-developers).</span><span class="sxs-lookup"><span data-stu-id="fb6ef-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
