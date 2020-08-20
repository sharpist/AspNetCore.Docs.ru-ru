---
title: Размещение ASP.NET Core в контейнерах Docker
author: rick-anderson
description: Ссылки на ресурсы для изучения способов размещения приложений ASP.NET Core в контейнерах Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
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
uid: host-and-deploy/docker/index
ms.openlocfilehash: bcb09300ccfd0fa1595bff325da1049d4d185b64
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627355"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="2c987-103">Размещение ASP.NET Core в контейнерах Docker</span><span class="sxs-lookup"><span data-stu-id="2c987-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="2c987-104">В следующих статьях содержатся сведения о размещении приложений ASP.NET Core в Docker.</span><span class="sxs-lookup"><span data-stu-id="2c987-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="2c987-105">Общие сведения о контейнерах и Docker</span><span class="sxs-lookup"><span data-stu-id="2c987-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="2c987-106">Узнайте о том, что контейнеризация — это подход к разработке программного обеспечения, при котором приложение или служба, их зависимости и конфигурация упаковываются вместе в образ контейнера.</span><span class="sxs-lookup"><span data-stu-id="2c987-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="2c987-107">Образ можно протестировать и затем развернуть на узле.</span><span class="sxs-lookup"><span data-stu-id="2c987-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="2c987-108">Что такое Docker?</span><span class="sxs-lookup"><span data-stu-id="2c987-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="2c987-109">Узнайте, о том, что Docker — это проект с открытым исходным кодом для автоматизации развертывания приложений в виде переносимых автономных контейнеров, выполняемых в облаке или локальной среде.</span><span class="sxs-lookup"><span data-stu-id="2c987-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="2c987-110">Терминология Docker</span><span class="sxs-lookup"><span data-stu-id="2c987-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="2c987-111">Изучите термины и определения для технологии Docker.</span><span class="sxs-lookup"><span data-stu-id="2c987-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="2c987-112">Контейнеры, образы и реестры Docker</span><span class="sxs-lookup"><span data-stu-id="2c987-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="2c987-113">Узнайте о хранении образов контейнеров Docker в реестре образов для согласованного развертывания в средах.</span><span class="sxs-lookup"><span data-stu-id="2c987-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

<span data-ttu-id="2c987-114"><xref:host-and-deploy/docker/building-net-docker-images> Ознакомьтесь со сведениями о том, как создать приложение ASP.NET Core и преобразовать его для Docker.</span><span class="sxs-lookup"><span data-stu-id="2c987-114"><xref:host-and-deploy/docker/building-net-docker-images> Learn how to build and dockerize an ASP.NET Core app.</span></span> <span data-ttu-id="2c987-115">Изучите образы Docker, поддерживаемые корпорацией Майкрософт, и ознакомьтесь с вариантами использования.</span><span class="sxs-lookup"><span data-stu-id="2c987-115">Explore Docker images maintained by Microsoft and examine use cases.</span></span>

[<span data-ttu-id="2c987-116">Средства Visual Studio для контейнеров</span><span class="sxs-lookup"><span data-stu-id="2c987-116">Visual Studio Container Tools</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="2c987-117">Узнайте, как Visual Studio поддерживает создание, отладку и запуск приложений ASP.NET Core, предназначенных для .NET Framework или .NET Core, в Docker для Windows.</span><span class="sxs-lookup"><span data-stu-id="2c987-117">Discover how Visual Studio supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="2c987-118">Поддерживаются контейнеры Windows и Linux.</span><span class="sxs-lookup"><span data-stu-id="2c987-118">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="2c987-119">Публикация в Реестре контейнеров Azure</span><span class="sxs-lookup"><span data-stu-id="2c987-119">Publish to Azure Container Registry</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="2c987-120">Узнайте, как использовать расширение средств Visual Studio для контейнеров для развертывания приложения ASP.NET Core на узле Docker в Azure с помощью PowerShell.</span><span class="sxs-lookup"><span data-stu-id="2c987-120">Find out how to use the Visual Studio Container Tools extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="2c987-121">Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="2c987-121">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="2c987-122">Для приложений, размещенных за прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="2c987-122">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="2c987-123">При передаче запросов через прокси-сервер сведения об исходном запросе, например схема и IP-адрес клиента, часто бывают скрыты.</span><span class="sxs-lookup"><span data-stu-id="2c987-123">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="2c987-124">Иногда необходимо вручную переслать некоторые сведения о запросе в приложение.</span><span class="sxs-lookup"><span data-stu-id="2c987-124">It might be necessary to forwarded some information about the request manually to the app.</span></span>

<span data-ttu-id="2c987-125">[Сборка мусора с использованием Docker и небольших контейнеров](xref:performance/memory#sc) Обсуждается выбор сборки мусора с использованием небольших контейнеров.</span><span class="sxs-lookup"><span data-stu-id="2c987-125">[GC using Docker and small containers](xref:performance/memory#sc) Discusses GC selection with small containers.</span></span>