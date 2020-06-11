---
title: Нагрузочное тестирование в ASP.NET Core
author: Jeremy-Meng
description: Узнайте о нескольких важных средствах и подходах к нагрузочному тестированию приложений ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: f0c930a7689c7288a58cfc3ca630d3cd324bf2cd
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106810"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="bcf25-103">Нагрузочное тестирование в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bcf25-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="bcf25-104">Нагрузочное тестирование и нагрузочные испытания важны для обеспечения эффективности и масштабируемости веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="bcf25-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="bcf25-105">Их цели различаются, несмотря на то, что они часто используют аналогичные тесты.</span><span class="sxs-lookup"><span data-stu-id="bcf25-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="bcf25-106">**Нагрузочное тестирование.** Проверка того, может ли приложение управлять заданной нагрузкой пользователей для определенного сценария в соответствии с задачей ответа.</span><span class="sxs-lookup"><span data-stu-id="bcf25-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="bcf25-107">Приложение запускается в нормальных условиях.</span><span class="sxs-lookup"><span data-stu-id="bcf25-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="bcf25-108">**Нагрузочные тесты.** Проверка стабильности приложений при выполнении в экстремальных условиях, часто в течение длительного периода времени.</span><span class="sxs-lookup"><span data-stu-id="bcf25-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="bcf25-109">В тестах создается большая нагрузка на пользователей, пиковая или постепенно увеличивающаяся нагрузка на приложение или ограничиваются вычислительные ресурсы приложения.</span><span class="sxs-lookup"><span data-stu-id="bcf25-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="bcf25-110">Нагрузочные испытания определяют, может ли приложение под нагрузкой восстанавливаться после сбоя и корректно возвращаться к ожидаемому поведению.</span><span class="sxs-lookup"><span data-stu-id="bcf25-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="bcf25-111">В условиях нагрузки приложение не запускается в нормальных условиях.</span><span class="sxs-lookup"><span data-stu-id="bcf25-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="bcf25-112">Visual Studio 2019 — это последняя версия Visual Studio, включающая средства нагрузочного тестирования.</span><span class="sxs-lookup"><span data-stu-id="bcf25-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="bcf25-113">Клиентам, которым в будущем потребуются средства нагрузочного тестирования, мы рекомендуем использовать альтернативные средства нагрузочного тестирования, такие как Apache JMeter, Akamai CloudTest или BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="bcf25-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="bcf25-114">Дополнительные сведения см. в [заметках о выпуске Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="bcf25-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="bcf25-115">Инструменты Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bcf25-115">Visual Studio tools</span></span>

<span data-ttu-id="bcf25-116">Visual Studio позволяет пользователям создавать, разрабатывать и отлаживать тесты веб-производительности и нагрузочные тесты.</span><span class="sxs-lookup"><span data-stu-id="bcf25-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="bcf25-117">Имеется параметр для создания тестов путем записи действий в веб-браузере.</span><span class="sxs-lookup"><span data-stu-id="bcf25-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="bcf25-118">Сведения о создании, настройке и запуске проектов нагрузочных тестов с помощью Visual Studio 2017 см. в разделе [Быстрое начало. Создание проекта нагрузочного тестирования](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="bcf25-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="bcf25-119">Нагрузочные тесты можно настроить для локального запуска или запуска в облаке с помощью Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="bcf25-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="bcf25-120">Сторонние средства</span><span class="sxs-lookup"><span data-stu-id="bcf25-120">Third-party tools</span></span>

<span data-ttu-id="bcf25-121">В списке ниже представлены сторонние средства тестирования веб-производительности с различными наборами функций:</span><span class="sxs-lookup"><span data-stu-id="bcf25-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="bcf25-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="bcf25-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="bcf25-123">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="bcf25-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="bcf25-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="bcf25-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="bcf25-125">k6</span><span class="sxs-lookup"><span data-stu-id="bcf25-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="bcf25-126">Locust</span><span class="sxs-lookup"><span data-stu-id="bcf25-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="bcf25-127">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="bcf25-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="bcf25-128">Netling</span><span class="sxs-lookup"><span data-stu-id="bcf25-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="bcf25-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="bcf25-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

