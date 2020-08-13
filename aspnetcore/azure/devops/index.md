---
title: DevOps с ASP.NET Core и Azure
author: CamSoper
description: Рекомендации по созданию сквозного решения конвейера DevOps для приложения ASP.NET Core, размещенного в Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: efabfd7fdcd695186207654887292c6b2104342d
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130188"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="98d7f-103">DevOps с ASP.NET Core и Azure</span><span class="sxs-lookup"><span data-stu-id="98d7f-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="98d7f-104">[![Изображение обложки](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="98d7f-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="98d7f-105">Авторы [Кэм Сопер (Cam Soper)](https://twitter.com/camsoper) и [Скотт Эдди (Scott Addie)](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="98d7f-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="98d7f-106">Это руководство доступно как [загружаемая электронная книга в формате PDF](https://aka.ms/devopsbook).</span><span class="sxs-lookup"><span data-stu-id="98d7f-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="98d7f-107">Приветствие</span><span class="sxs-lookup"><span data-stu-id="98d7f-107">Welcome</span></span> 

<span data-ttu-id="98d7f-108">Руководство по жизненному циклу разработки Azure для .NET</span><span class="sxs-lookup"><span data-stu-id="98d7f-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="98d7f-109">В этом руководстве предоставляются основные сведения по созданию жизненного цикла разработки в Azure с помощью инструментов и процессов .NET.</span><span class="sxs-lookup"><span data-stu-id="98d7f-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="98d7f-110">После его прохождения вы сможете наиболее эффективно использовать цепочку инструментов DevOps.</span><span class="sxs-lookup"><span data-stu-id="98d7f-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="98d7f-111">Для кого предназначено это руководство</span><span class="sxs-lookup"><span data-stu-id="98d7f-111">Who this guide is for</span></span>

<span data-ttu-id="98d7f-112">Вы должны быть опытным разработчиком ASP.NET Core (уровень 200–300).</span><span class="sxs-lookup"><span data-stu-id="98d7f-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="98d7f-113">Вам не нужно ничего знать об Azure, так как эти сведения есть во введении.</span><span class="sxs-lookup"><span data-stu-id="98d7f-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="98d7f-114">Это руководство может также оказаться полезным для инженеров DevOps, которые преимущественно работают с операциями, а не занимаются разработкой.</span><span class="sxs-lookup"><span data-stu-id="98d7f-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="98d7f-115">Это руководство предназначено для разработчиков Windows.</span><span class="sxs-lookup"><span data-stu-id="98d7f-115">This guide targets Windows developers.</span></span> <span data-ttu-id="98d7f-116">Linux и macOS также полностью поддерживаются в .NET Core.</span><span class="sxs-lookup"><span data-stu-id="98d7f-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="98d7f-117">Чтобы адаптировать это руководство для Linux или macOS, смотрите сноски, в которых приводятся характерные отличия.</span><span class="sxs-lookup"><span data-stu-id="98d7f-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="98d7f-118">Темы, которые выходят за рамки этого руководства</span><span class="sxs-lookup"><span data-stu-id="98d7f-118">What this guide doesn't cover</span></span>

<span data-ttu-id="98d7f-119">В этом руководстве приводятся рекомендации для разработчиков .NET по сквозному непрерывному развертыванию.</span><span class="sxs-lookup"><span data-stu-id="98d7f-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="98d7f-120">Это не исчерпывающее руководство по Azure, и в нем не рассматриваются подробно API .NET для служб Azure.</span><span class="sxs-lookup"><span data-stu-id="98d7f-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="98d7f-121">Основное внимание уделяется непрерывной интеграции, развертыванию, мониторингу и отладке.</span><span class="sxs-lookup"><span data-stu-id="98d7f-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="98d7f-122">В конце руководства предлагаются рекомендации по дальнейшим действиям.</span><span class="sxs-lookup"><span data-stu-id="98d7f-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="98d7f-123">В число предложений входят службы платформы Azure, полезные для разработчиков ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="98d7f-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="98d7f-124">Содержание руководства</span><span class="sxs-lookup"><span data-stu-id="98d7f-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="98d7f-125">Инструменты и файлы для скачивания</span><span class="sxs-lookup"><span data-stu-id="98d7f-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="98d7f-126">Вы узнаете, где получить инструменты, используемые в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="98d7f-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="98d7f-127">Развертывание в службу приложений</span><span class="sxs-lookup"><span data-stu-id="98d7f-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="98d7f-128">Разные способы развертывания приложения ASP.NET Core в службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="98d7f-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="98d7f-129">Непрерывная интеграция и развертывание</span><span class="sxs-lookup"><span data-stu-id="98d7f-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="98d7f-130">Создание решения сквозной непрерывной интеграции и развертывания для приложения ASP.NET Core с помощью GitHub, Azure DevOps Services и Azure.</span><span class="sxs-lookup"><span data-stu-id="98d7f-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="98d7f-131">Мониторинг и отладка</span><span class="sxs-lookup"><span data-stu-id="98d7f-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="98d7f-132">Мониторинг, устранение неполадок и настройка приложения с помощью инструментов Azure.</span><span class="sxs-lookup"><span data-stu-id="98d7f-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="98d7f-133">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="98d7f-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="98d7f-134">Другие способы изучения Azure для разработчиков ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="98d7f-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="98d7f-135">Дополнительные справочные материалы</span><span class="sxs-lookup"><span data-stu-id="98d7f-135">Additional introductory reading</span></span>

<span data-ttu-id="98d7f-136">Если это ваш первый опыт работы с облачными вычислениями, в этой статье рассматриваются основы.</span><span class="sxs-lookup"><span data-stu-id="98d7f-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="98d7f-137">Что такое облачные вычисления?</span><span class="sxs-lookup"><span data-stu-id="98d7f-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="98d7f-138">Примеры облачных вычислений</span><span class="sxs-lookup"><span data-stu-id="98d7f-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="98d7f-139">Что такое IaaS?</span><span class="sxs-lookup"><span data-stu-id="98d7f-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="98d7f-140">Что такое PaaS?</span><span class="sxs-lookup"><span data-stu-id="98d7f-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
