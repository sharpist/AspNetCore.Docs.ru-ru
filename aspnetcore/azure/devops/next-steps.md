---
title: Следующие шаги — DevOps с ASP.NET Core и Azure
author: CamSoper
description: Дополнительные схемы обучения для DevOps с ASP.NET Core и Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/next-steps
ms.openlocfilehash: 92401d45d36dd3b93d175e08a8fa8697217ca7c7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766528"
---
# <a name="next-steps"></a><span data-ttu-id="5eef4-103">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="5eef4-103">Next steps</span></span>

<span data-ttu-id="5eef4-104">В этом руководстве вы создали конвейер DevOps для примера приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5eef4-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="5eef4-105">Поздравляем!</span><span class="sxs-lookup"><span data-stu-id="5eef4-105">Congratulations!</span></span> <span data-ttu-id="5eef4-106">Мы надеемся, что вы остались довольны обучением публикации веб-приложений ASP.NET Core в службе приложений Azure и автоматизации непрерывной интеграции изменений.</span><span class="sxs-lookup"><span data-stu-id="5eef4-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="5eef4-107">Помимо веб-хостинга и DevOps, Azure имеет широкий спектр служб платформы как услуги (PaaS), полезных для разработчиков ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5eef4-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="5eef4-108">В этом разделе приводится краткий обзор некоторых наиболее часто используемых служб.</span><span class="sxs-lookup"><span data-stu-id="5eef4-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="5eef4-109">Хранилище и базы данных</span><span class="sxs-lookup"><span data-stu-id="5eef4-109">Storage and databases</span></span>

<span data-ttu-id="5eef4-110">[Redis Cache](/azure/redis-cache/) — это кэширование данных с высокой пропускной способностью и низкой задержкой, доступное как услуга.</span><span class="sxs-lookup"><span data-stu-id="5eef4-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="5eef4-111">Она может использоваться для кэширования выходных данных страницы, уменьшения количества запросов к базе данных и предоставления состояния сеанса ASP.NET Core нескольким экземплярам приложения.</span><span class="sxs-lookup"><span data-stu-id="5eef4-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="5eef4-112">[Служба хранилища Azure](/azure/storage/) — это высокомасштабируемое облачное хранилище Azure.</span><span class="sxs-lookup"><span data-stu-id="5eef4-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="5eef4-113">Разработчики могут воспользоваться преимуществами [хранилища очередей](/azure/storage/queues/storage-queues-introduction) для получения надежной очереди сообщений и [хранилища таблиц](/azure/storage/tables/table-storage-overview) — NoSQL хранилища "ключ-значение", предназначенное для быстрой разработки с использованием больших, частично структурированных наборов данных.</span><span class="sxs-lookup"><span data-stu-id="5eef4-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="5eef4-114">[База данных SQL Azure](/azure/sql-database/) предоставляет как услугу привычные функциональные возможности реляционной базы данных на основе ядра Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5eef4-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="5eef4-115">[Cosmos DB](/azure/cosmos-db/) — это NoSQL многомодельная глобально распределенная служба баз данных.</span><span class="sxs-lookup"><span data-stu-id="5eef4-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="5eef4-116">Доступны несколько интерфейсов API, в том числе API SQL (прежнее название — DocumentDB), Cassandra и MongoDB.</span><span class="sxs-lookup"><span data-stu-id="5eef4-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## Identity

<span data-ttu-id="5eef4-117">[Azure Active Directory](/azure/active-directory/) и [Azure Active Directory B2C](/azure/active-directory-b2c/) являются службами идентификации.</span><span class="sxs-lookup"><span data-stu-id="5eef4-117">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="5eef4-118">Azure Active Directory предназначена для корпоративных сценариев и обеспечивает совместную работу Azure AD B2B ("бизнес-бизнес"), а Azure Active Directory B2C предназначена для сценариев "бизнес-клиент", включая вход через социальные сети.</span><span class="sxs-lookup"><span data-stu-id="5eef4-118">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="5eef4-119">Мобильный</span><span class="sxs-lookup"><span data-stu-id="5eef4-119">Mobile</span></span>

<span data-ttu-id="5eef4-120">[Центры уведомлений](/azure/notification-hubs/) — это многоплатформенная масштабируемая подсистема push-уведомлений, предназначенная для быстрой отправки миллионов сообщений в приложения, работающие на устройствах различных типов.</span><span class="sxs-lookup"><span data-stu-id="5eef4-120">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="5eef4-121">Веб-инфраструктура</span><span class="sxs-lookup"><span data-stu-id="5eef4-121">Web infrastructure</span></span>

<span data-ttu-id="5eef4-122">[Служба контейнеров Azure](/azure/aks/) управляет размещенной средой Kubernetes, позволяя быстро и легко развертывать контейнерные приложения и управлять ими, даже если вы никогда не оркестрировали контейнеры.</span><span class="sxs-lookup"><span data-stu-id="5eef4-122">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="5eef4-123">[Поиск Azure](/azure/search/) используется для создания корпоративного решения для поиска по частному разнородному контенту.</span><span class="sxs-lookup"><span data-stu-id="5eef4-123">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="5eef4-124">[Service Fabric](/azure/service-fabric/) — это платформа распределенных систем, которая дает возможность не только легко упаковывать и развертывать масштабируемые надежные микрослужбы и контейнеры, но и управлять ими.</span><span class="sxs-lookup"><span data-stu-id="5eef4-124">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
