---
title: Поддерживаемые платформы ASP.NET Core Blazor
author: guardrex
description: Сведения о поддерживаемых платформах для ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754545"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>Поддерживаемые платформы ASP.NET Core Blazor

Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly и Blazor Server поддерживаются в браузерах, приведенных в таблице ниже.

| Браузер                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari, включая iOS      | Текущий&dagger; |
| Google Chrome, включая Android | Текущий&dagger; |
| Microsoft Edge                   | Текущий&dagger; |
| Mozilla Firefox;                  | Текущий&dagger; |  

&dagger;*Текущий* означает последнюю версию браузера.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| Браузер                          | Версия               |
| -------------------------------- | --------------------- |
| Apple Safari, включая iOS      | Текущий&dagger;       |
| Google Chrome, включая Android | Текущий&dagger;       |
| Microsoft Edge                   | Текущий&dagger;       |
| Microsoft Internet Explorer      | Не поддерживается&Dagger; |
| Mozilla Firefox;                  | Текущий&dagger;       |  

&dagger;*Текущий* означает последнюю версию браузера.  
&Dagger;Microsoft Internet Explorer не поддерживает [WebAssembly](https://webassembly.org).

## Blazor Server

| Браузер                          | Версия         |
| -------------------------------- | --------------- |
| Apple Safari, включая iOS      | Текущий&dagger; |
| Google Chrome, включая Android | Текущий&dagger; |
| Microsoft Edge                   | Текущий&dagger; |
| Microsoft Internet Explorer      | 11&Dagger;      |
| Mozilla Firefox;                  | Текущий&dagger; |

&dagger;*Текущий* означает последнюю версию браузера.  
&Dagger;Требуются дополнительные заполнения. Например, обещания можно добавить с помощью пакета [`Polyfill.io`](https://polyfill.io/v3/).

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
