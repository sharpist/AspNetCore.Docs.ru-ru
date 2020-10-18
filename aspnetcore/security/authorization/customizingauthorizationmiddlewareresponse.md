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
# <a name="customize-the-behavior-of-authorizationmiddleware"></a>Настройка поведения Аусоризатионмиддлеваре

Приложения могут зарегистрировать, `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` чтобы настроить способ обработки результатов авторизации по промежуточного слоя. Приложения могут использовать настроенное по промежуточного слоя для:

* Возврат настраиваемых ответов.
* Улучшайте запросы по умолчанию или запрещайте ответы.

В следующем коде показан пример обработчика авторизации, который возвращает пользовательский ответ для определенных видов сбоев авторизации:

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

Зарегистрируйтесь `MyAuthorizationMiddlewareResultHandler` в `Startup.ConfigureServices` :

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->