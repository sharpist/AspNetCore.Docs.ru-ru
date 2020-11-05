---
title: Использование шаблона проекта React и Redux с ASP.NET Core
author: SteveSandersonMS
description: Сведения о начале работы с шаблоном проекта одностраничного приложения (SPA) ASP.NET Core для React с Redux и create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: spa/react-with-redux
ms.openlocfilehash: 9ae96b14f3f50d4079933bbd893ff95fa677d273
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054506"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="19f2a-103">Использование шаблона проекта React и Redux с ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19f2a-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="19f2a-104">Обновленный шаблон проекта React и Redux служит удобной отправной точкой для создания приложений ASP.NET Core на основе конвенций React, Redux и [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA), позволяющих реализовать полноценный пользовательский интерфейс на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="19f2a-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="19f2a-105">За исключением команд создания проекта, все сведения о шаблоне React и Redux совпадают с данными о шаблоне React.</span><span class="sxs-lookup"><span data-stu-id="19f2a-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="19f2a-106">Чтобы создать проект такого типа, выполните `dotnet new reactredux` вместо `dotnet new react`.</span><span class="sxs-lookup"><span data-stu-id="19f2a-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="19f2a-107">Дополнительные сведения о функциональности обоих шаблонов на основе React вы найдете в [документации по шаблону React](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="19f2a-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="19f2a-108">Сведения о настройке дочернего приложения React и Redux в IIS см. в статье [ReactRedux Template 2.1. Не удалось использовать SPA в службах IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="19f2a-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
