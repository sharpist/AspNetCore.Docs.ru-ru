---
title: Следующие шаги — DevOps с ASP.NET Core и Azure
author: CamSoper
description: Дополнительные схемы обучения для DevOps с ASP.NET Core и Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/next-steps
ms.openlocfilehash: a9a6b0a588b7813f9d6e2b215630a1b9100be890
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130435"
---
# <a name="next-steps"></a>Следующие шаги

В этом руководстве вы создали конвейер DevOps для примера приложения ASP.NET Core. Поздравляем! Мы надеемся, что вы остались довольны обучением публикации веб-приложений ASP.NET Core в службе приложений Azure и автоматизации непрерывной интеграции изменений.

Помимо веб-хостинга и DevOps, Azure имеет широкий спектр служб платформы как услуги (PaaS), полезных для разработчиков ASP.NET Core. В этом разделе приводится краткий обзор некоторых наиболее часто используемых служб.

## <a name="storage-and-databases"></a>Хранилище и базы данных

[Redis Cache](/azure/redis-cache/) — это кэширование данных с высокой пропускной способностью и низкой задержкой, доступное как услуга. Она может использоваться для кэширования выходных данных страницы, уменьшения количества запросов к базе данных и предоставления состояния сеанса ASP.NET Core нескольким экземплярам приложения.

[Служба хранилища Azure](/azure/storage/) — это высокомасштабируемое облачное хранилище Azure. Разработчики могут воспользоваться преимуществами [хранилища очередей](/azure/storage/queues/storage-queues-introduction) для получения надежной очереди сообщений и [хранилища таблиц](/azure/storage/tables/table-storage-overview) — NoSQL хранилища "ключ-значение", предназначенное для быстрой разработки с использованием больших, частично структурированных наборов данных.

[База данных SQL Azure](/azure/sql-database/) предоставляет как услугу привычные функциональные возможности реляционной базы данных на основе ядра Microsoft SQL Server.

[Cosmos DB](/azure/cosmos-db/) — это NoSQL многомодельная глобально распределенная служба баз данных. Доступны несколько интерфейсов API, в том числе API SQL (прежнее название — DocumentDB), Cassandra и MongoDB.

## Identity

[Azure Active Directory](/azure/active-directory/) и [Azure Active Directory B2C](/azure/active-directory-b2c/) являются службами идентификации. Azure Active Directory предназначена для корпоративных сценариев и обеспечивает совместную работу Azure AD B2B ("бизнес-бизнес"), а Azure Active Directory B2C предназначена для сценариев "бизнес-клиент", включая вход через социальные сети.

## <a name="mobile"></a>Мобильный

[Центры уведомлений](/azure/notification-hubs/) — это многоплатформенная масштабируемая подсистема push-уведомлений, предназначенная для быстрой отправки миллионов сообщений в приложения, работающие на устройствах различных типов.

## <a name="web-infrastructure"></a>Веб-инфраструктура

[Служба контейнеров Azure](/azure/aks/) управляет размещенной средой Kubernetes, позволяя быстро и легко развертывать контейнерные приложения и управлять ими, даже если вы никогда не оркестрировали контейнеры.

[Поиск Azure](/azure/search/) используется для создания корпоративного решения для поиска по частному разнородному контенту.

[Service Fabric](/azure/service-fabric/) — это платформа распределенных систем, которая дает возможность не только легко упаковывать и развертывать масштабируемые надежные микрослужбы и контейнеры, но и управлять ими.
