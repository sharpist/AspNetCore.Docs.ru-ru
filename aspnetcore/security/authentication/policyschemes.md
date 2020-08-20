---
title: Схемы политики в ASP.NET Core
author: rick-anderson
description: Схемы политики аутентификации упрощают создание одной логической схемы проверки подлинности
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 60ac9914ef811a705c61ab3b2bec61643acc6ec0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634986"
---
# <a name="policy-schemes-in-aspnet-core"></a>Схемы политики в ASP.NET Core

Схемы политики аутентификации упрощают создание одной логической схемы проверки подлинности, потенциально использующей несколько подходов. Например, схема политики может использовать проверку подлинности Google для решения проблем и cookie проверку подлинности для всех остальных. Схемы политики проверки подлинности делают следующее:

* Простое перенаправление любого действия проверки подлинности в другую схему.
* Пересылать динамически на основе запроса.

Все схемы проверки подлинности, использующие производные <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> и связанные [ \<TOptions> аусентикатионхандлер](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):

* Являются автоматическими схемами политик в ASP.NET Core 2,1 и более поздних версий.
* Можно включить с помощью настройки параметров схемы.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Примеры

В следующем примере показана схема более высокого уровня, которая сочетает схемы более низкого уровня. Проверка подлинности Google используется для решения проблем, и cookie для всех остальных операций используется проверка подлинности.

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

В следующем примере включается динамическое выделение схем на основе каждого запроса. То есть как смешивать cookie s и аутентификацию API:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
