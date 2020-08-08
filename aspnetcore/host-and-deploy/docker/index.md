---
title: Размещение ASP.NET Core в контейнерах Docker
author: rick-anderson
description: Ссылки на ресурсы для изучения способов размещения приложений ASP.NET Core в контейнерах Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
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
uid: host-and-deploy/docker/index
ms.openlocfilehash: 35837eb2bd1ed2321663e1ae334eb20b18e5587a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015781"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Размещение ASP.NET Core в контейнерах Docker

В следующих статьях содержатся сведения о размещении приложений ASP.NET Core в Docker.

[Общие сведения о контейнерах и Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
Узнайте о том, что контейнеризация — это подход к разработке программного обеспечения, при котором приложение или служба, их зависимости и конфигурация упаковываются вместе в образ контейнера. Образ можно протестировать и затем развернуть на узле.

[Что такое Docker?](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
Узнайте, о том, что Docker — это проект с открытым исходным кодом для автоматизации развертывания приложений в виде переносимых автономных контейнеров, выполняемых в облаке или локальной среде.

[Терминология Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
Изучите термины и определения для технологии Docker.

[Контейнеры, образы и реестры Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
Узнайте о хранении образов контейнеров Docker в реестре образов для согласованного развертывания в средах.

<xref:host-and-deploy/docker/building-net-docker-images> Ознакомьтесь со сведениями о том, как создать приложение ASP.NET Core и преобразовать его для Docker. Изучите образы Docker, поддерживаемые корпорацией Майкрософт, и ознакомьтесь с вариантами использования.

[Средства Visual Studio для контейнеров](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Узнайте, как Visual Studio поддерживает создание, отладку и запуск приложений ASP.NET Core, предназначенных для .NET Framework или .NET Core, в Docker для Windows. Поддерживаются контейнеры Windows и Linux.

[Публикация в Реестре контейнеров Azure](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
Узнайте, как использовать расширение средств Visual Studio для контейнеров для развертывания приложения ASP.NET Core на узле Docker в Azure с помощью PowerShell.

[Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer)  
Для приложений, размещенных за прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка. При передаче запросов через прокси-сервер сведения об исходном запросе, например схема и IP-адрес клиента, часто бывают скрыты. Иногда необходимо вручную переслать некоторые сведения о запросе в приложение.

[Сборка мусора с использованием Docker и небольших контейнеров](xref:performance/memory#sc) Обсуждается выбор сборки мусора с использованием небольших контейнеров.