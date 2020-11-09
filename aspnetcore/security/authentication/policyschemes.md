---
title: Схемы политики в ASP.NET Core
author: rick-anderson
description: Схемы политики аутентификации упрощают создание одной логической схемы проверки подлинности
ms.author: riande
ms.date: 12/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/policyschemes
ms.openlocfilehash: 63d931c926c9660f5d68d5a2ce292bf57efdb49c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053232"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="c465a-103">Схемы политики в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c465a-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="c465a-104">Схемы политики аутентификации упрощают создание одной логической схемы проверки подлинности, потенциально использующей несколько подходов.</span><span class="sxs-lookup"><span data-stu-id="c465a-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="c465a-105">Например, схема политики может использовать проверку подлинности Google для решения проблем и cookie проверку подлинности для всех остальных.</span><span class="sxs-lookup"><span data-stu-id="c465a-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="c465a-106">Схемы политики проверки подлинности делают следующее:</span><span class="sxs-lookup"><span data-stu-id="c465a-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="c465a-107">Простое перенаправление любого действия проверки подлинности в другую схему.</span><span class="sxs-lookup"><span data-stu-id="c465a-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="c465a-108">Пересылать динамически на основе запроса.</span><span class="sxs-lookup"><span data-stu-id="c465a-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="c465a-109">Все схемы проверки подлинности, использующие производные <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> и связанные [ \<TOptions> аусентикатионхандлер](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="c465a-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="c465a-110">Являются автоматическими схемами политик в ASP.NET Core 2,1 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="c465a-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="c465a-111">Можно включить с помощью настройки параметров схемы.</span><span class="sxs-lookup"><span data-stu-id="c465a-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="c465a-112">Примеры</span><span class="sxs-lookup"><span data-stu-id="c465a-112">Examples</span></span>

<span data-ttu-id="c465a-113">В следующем примере показана схема более высокого уровня, которая сочетает схемы более низкого уровня.</span><span class="sxs-lookup"><span data-stu-id="c465a-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="c465a-114">Проверка подлинности Google используется для решения проблем, и cookie для всех остальных операций используется проверка подлинности.</span><span class="sxs-lookup"><span data-stu-id="c465a-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="c465a-115">В следующем примере включается динамическое выделение схем на основе каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="c465a-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="c465a-116">То есть как смешивать cookie s и аутентификацию API:</span><span class="sxs-lookup"><span data-stu-id="c465a-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
