---
title: Поддерживаемые платформы ASP.NET Core Blazor
author: guardrex
description: Сведения о поддерживаемых платформах для ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625951"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>Поддерживаемые платформы ASP.NET Core Blazor

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
