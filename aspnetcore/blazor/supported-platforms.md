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
ms.openlocfilehash: 99f14486711c2dd2a634bc51b27a8e3891deee1a
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243230"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>Поддерживаемые платформы ASP.NET Core Blazor

Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)

## <a name="browser-requirements"></a>Требования к браузерам

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| Браузер                          | Версия               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Текущий               |
| Mozilla Firefox                  | Текущий               |
| Google Chrome, включая Android | Текущий               |
| Safari, включая iOS            | Текущий               |
| Microsoft Internet Explorer      | Не поддерживается&dagger; |

&dagger;Microsoft Internet Explorer не поддерживает [WebAssembly](https://webassembly.org).

### <a name="blazor-server"></a>Сервер Blazor

| Браузер                          | Версия    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Текущий    |
| Mozilla Firefox                  | Текущий    |
| Google Chrome, включая Android | Текущий    |
| Safari, включая iOS            | Текущий    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Требуются дополнительные заполнения (например, обещания можно добавить с помощью пакета [`Polyfill.io`](https://polyfill.io/v3/)).

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/hosting-models>
