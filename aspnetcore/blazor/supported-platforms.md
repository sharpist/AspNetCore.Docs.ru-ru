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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401939"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>Поддерживаемые платформы ASP.NET Core Blazor

Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)

## <a name="browser-requirements"></a>Требования к браузеру

### Blazor WebAssembly

| Браузер                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Текущие               |
| Mozilla Firefox                  | Текущие               |
| Google Chrome, включая Android | Текущие               |
| Safari, включая iOS            | Текущие               |
| Microsoft Internet Explorer      | Не поддерживается&dagger; |

&dagger;Microsoft Internet Explorer не поддерживает [WebAssembly](https://webassembly.org).

### Blazor Server

| Браузер                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Текущие    |
| Mozilla Firefox                  | Текущие    |
| Google Chrome, включая Android | Текущие    |
| Safari, включая iOS            | Текущие    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Требуются дополнительные заполнения (например, обещания можно добавить с помощью пакета [`Polyfill.io`](https://polyfill.io/v3/)).

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/hosting-models>
