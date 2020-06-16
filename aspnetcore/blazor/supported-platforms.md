---
title: Поддерживаемые платформы ASP.NET Core Blazor
author: guardrex
description: Сведения о поддерживаемых платформах для ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: c10132c87c93346af89c548363e786967609f3da
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2020
ms.locfileid: "83607983"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>Поддерживаемые платформы ASP.NET Core Blazor

Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)

## <a name="browser-requirements"></a>Требования к браузеру

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| Браузер                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Текущие               |
| Mozilla Firefox                  | Текущие               |
| Google Chrome, включая Android | Текущие               |
| Safari, включая iOS            | Текущие               |
| Microsoft Internet Explorer      | Не поддерживается&dagger; |

&dagger;Microsoft Internet Explorer не поддерживает [WebAssembly](https://webassembly.org).

### <a name="blazor-server"></a>Сервер Blazor

| Браузер                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Текущие    |
| Mozilla Firefox                  | Текущие    |
| Google Chrome, включая Android | Текущие    |
| Safari, включая iOS            | Текущие    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Требуются дополнительные заполнения (например, обещания можно добавить с помощью пакета [Polyfill.io](https://polyfill.io/v3/)).

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/hosting-models>
