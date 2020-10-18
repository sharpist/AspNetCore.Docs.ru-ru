---
title: Настройка поведения Аусоризатионмиддлеваре
author: pranavkm
ms.author: prkrishn
description: В этой статье объясняется, как настроить обработку результатов Аусоризатионмиддлеваре.
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156772"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="9b3dc-103">Настройка поведения Аусоризатионмиддлеваре</span><span class="sxs-lookup"><span data-stu-id="9b3dc-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="9b3dc-104">Приложения могут зарегистрировать, `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` чтобы настроить способ обработки результатов авторизации по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="9b3dc-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="9b3dc-105">Приложения могут использовать настроенное по промежуточного слоя для:</span><span class="sxs-lookup"><span data-stu-id="9b3dc-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="9b3dc-106">Возврат настраиваемых ответов.</span><span class="sxs-lookup"><span data-stu-id="9b3dc-106">Return customized responses.</span></span>
* <span data-ttu-id="9b3dc-107">Улучшайте запросы по умолчанию или запрещайте ответы.</span><span class="sxs-lookup"><span data-stu-id="9b3dc-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="9b3dc-108">В следующем коде показан пример обработчика авторизации, который возвращает пользовательский ответ для определенных видов сбоев авторизации:</span><span class="sxs-lookup"><span data-stu-id="9b3dc-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="9b3dc-109">Зарегистрируйтесь `MyAuthorizationMiddlewareResultHandler` в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9b3dc-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->