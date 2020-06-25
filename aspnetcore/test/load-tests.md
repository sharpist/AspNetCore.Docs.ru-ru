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
ms.openlocfilehash: 0ec69ad783a4e545ea95ddcb928d03ba6a2e0050
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074375"
---
# <a name="aspnet-core-loadstress-testing"></a>Нагрузочное тестирование в ASP.NET Core

Нагрузочное тестирование и нагрузочные испытания важны для обеспечения эффективности и масштабируемости веб-приложения. Их цели различаются, несмотря на то, что они часто используют аналогичные тесты.

**Нагрузочное тестирование.** Проверка того, может ли приложение управлять заданной нагрузкой пользователей для определенного сценария в соответствии с задачей ответа. Приложение запускается в нормальных условиях.

**Нагрузочные тесты.** Проверка стабильности приложений при выполнении в экстремальных условиях, часто в течение длительного периода времени. В тестах создается большая нагрузка на пользователей, пиковая или постепенно увеличивающаяся нагрузка на приложение или ограничиваются вычислительные ресурсы приложения.

Нагрузочные испытания определяют, может ли приложение под нагрузкой восстанавливаться после сбоя и корректно возвращаться к ожидаемому поведению. В условиях нагрузки приложение не запускается в нормальных условиях.

Visual Studio 2019 объявил о планах [объявить устаревшим нагрузочное тестирование](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/). Работа соответствующей облачной службы нагрузочного тестирования Azure DevOps прекращена.

## <a name="third-party-tools"></a>Сторонние средства

В списке ниже представлены сторонние средства тестирования веб-производительности с различными наборами функций:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [West Wind WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)