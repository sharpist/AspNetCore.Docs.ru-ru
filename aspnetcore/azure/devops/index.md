---
title: DevOps с ASP.NET Core и Azure
author: CamSoper
description: Рекомендации по созданию сквозного решения конвейера DevOps для приложения ASP.NET Core, размещенного в Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: af8e130fe63a266eabacd70a9c84aac56ac1c4c0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625366"
---
# <a name="devops-with-aspnet-core-and-azure"></a>DevOps с ASP.NET Core и Azure

[![Изображение обложки](./media/cover-large.png)](https://aka.ms/devopsbook)

Авторы [Кэм Сопер (Cam Soper)](https://twitter.com/camsoper) и [Скотт Эдди (Scott Addie)](https://twitter.com/scottaddie)

Это руководство доступно как [загружаемая электронная книга в формате PDF](https://aka.ms/devopsbook).

## <a name="welcome"></a>Приветствие 

Руководство по жизненному циклу разработки Azure для .NET В этом руководстве предоставляются основные сведения по созданию жизненного цикла разработки в Azure с помощью инструментов и процессов .NET. После его прохождения вы сможете наиболее эффективно использовать цепочку инструментов DevOps.

## <a name="who-this-guide-is-for"></a>Для кого предназначено это руководство

Вы должны быть опытным разработчиком ASP.NET Core (уровень 200–300). Вам не нужно ничего знать об Azure, так как эти сведения есть во введении. Это руководство может также оказаться полезным для инженеров DevOps, которые преимущественно работают с операциями, а не занимаются разработкой.

Это руководство предназначено для разработчиков Windows. Linux и macOS также полностью поддерживаются в .NET Core. Чтобы адаптировать это руководство для Linux или macOS, смотрите сноски, в которых приводятся характерные отличия.

## <a name="what-this-guide-doesnt-cover"></a>Темы, которые выходят за рамки этого руководства

В этом руководстве приводятся рекомендации для разработчиков .NET по сквозному непрерывному развертыванию. Это не исчерпывающее руководство по Azure, и в нем не рассматриваются подробно API .NET для служб Azure. Основное внимание уделяется непрерывной интеграции, развертыванию, мониторингу и отладке. В конце руководства предлагаются рекомендации по дальнейшим действиям. В число предложений входят службы платформы Azure, полезные для разработчиков ASP.NET Core.

## <a name="whats-in-this-guide"></a>Содержание руководства

### <a name="tools-and-downloads"></a>[Инструменты и файлы для скачивания](xref:azure/devops/tools-and-downloads)

Вы узнаете, где получить инструменты, используемые в этом руководстве.

### <a name="deploy-to-app-service"></a>[Развертывание в службу приложений](xref:azure/devops/deploy-to-app-service)

Разные способы развертывания приложения ASP.NET Core в службе приложений Azure.

### <a name="continuous-integration-and-deployment"></a>[Непрерывная интеграция и развертывание](xref:azure/devops/cicd)

Создание решения сквозной непрерывной интеграции и развертывания для приложения ASP.NET Core с помощью GitHub, Azure DevOps Services и Azure.

### <a name="monitor-and-debug"></a>[Мониторинг и отладка](xref:azure/devops/monitor)

Мониторинг, устранение неполадок и настройка приложения с помощью инструментов Azure.

### <a name="next-steps"></a>[Следующие шаги](xref:azure/devops/next-steps)

Другие способы изучения Azure для разработчиков ASP.NET Core.

## <a name="additional-introductory-reading"></a>Дополнительные справочные материалы

Если это ваш первый опыт работы с облачными вычислениями, в этой статье рассматриваются основы.

* [Что такое облачные вычисления?](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [Примеры облачных вычислений](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [Что такое IaaS?](https://azure.microsoft.com/overview/what-is-iaas/)
* [Что такое PaaS?](https://azure.microsoft.com/overview/what-is-paas/)
