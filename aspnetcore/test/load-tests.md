---
title: Нагрузочное тестирование в ASP.NET Core
author: Jeremy-Meng
description: Узнайте о нескольких важных средствах и подходах к нагрузочному тестированию приложений ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
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
uid: test/loadtests
ms.openlocfilehash: 8e3ca41312922cbf44361601c38e455b342e9fe1
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "88632815"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="5b0d4-103">Нагрузочное тестирование в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5b0d4-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="5b0d4-104">Нагрузочное тестирование и нагрузочные испытания важны для обеспечения эффективности и масштабируемости веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="5b0d4-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="5b0d4-105">Их цели различаются, несмотря на то, что они часто используют аналогичные тесты.</span><span class="sxs-lookup"><span data-stu-id="5b0d4-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="5b0d4-106">**Нагрузочное тестирование.** Проверка того, может ли приложение управлять заданной нагрузкой пользователей для определенного сценария в соответствии с задачей ответа.</span><span class="sxs-lookup"><span data-stu-id="5b0d4-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="5b0d4-107">Приложение запускается в нормальных условиях.</span><span class="sxs-lookup"><span data-stu-id="5b0d4-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="5b0d4-108">**Нагрузочные тесты.** Проверка стабильности приложений при выполнении в экстремальных условиях, часто в течение длительного периода времени.</span><span class="sxs-lookup"><span data-stu-id="5b0d4-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="5b0d4-109">В тестах создается большая нагрузка на пользователей, пиковая или постепенно увеличивающаяся нагрузка на приложение или ограничиваются вычислительные ресурсы приложения.</span><span class="sxs-lookup"><span data-stu-id="5b0d4-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="5b0d4-110">Нагрузочные испытания определяют, может ли приложение под нагрузкой восстанавливаться после сбоя и корректно возвращаться к ожидаемому поведению.</span><span class="sxs-lookup"><span data-stu-id="5b0d4-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="5b0d4-111">В условиях нагрузки приложение не запускается в нормальных условиях.</span><span class="sxs-lookup"><span data-stu-id="5b0d4-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="5b0d4-112">Visual Studio 2019 объявил о планах [объявить устаревшим нагрузочное тестирование](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="5b0d4-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="5b0d4-113">Работа соответствующей облачной службы нагрузочного тестирования Azure DevOps прекращена.</span><span class="sxs-lookup"><span data-stu-id="5b0d4-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="5b0d4-114">Сторонние средства</span><span class="sxs-lookup"><span data-stu-id="5b0d4-114">Third-party tools</span></span>

<span data-ttu-id="5b0d4-115">В списке ниже представлены сторонние средства тестирования веб-производительности с различными наборами функций:</span><span class="sxs-lookup"><span data-stu-id="5b0d4-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="5b0d4-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="5b0d4-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="5b0d4-117">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="5b0d4-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="5b0d4-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="5b0d4-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="5b0d4-119">k6</span><span class="sxs-lookup"><span data-stu-id="5b0d4-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="5b0d4-120">Locust</span><span class="sxs-lookup"><span data-stu-id="5b0d4-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="5b0d4-121">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="5b0d4-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="5b0d4-122">Netling</span><span class="sxs-lookup"><span data-stu-id="5b0d4-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="5b0d4-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="5b0d4-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)