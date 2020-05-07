---
title: Использование шаблона проекта React и Redux с ASP.NET Core
author: SteveSandersonMS
description: Сведения о начале работы с шаблоном проекта одностраничного приложения (SPA) ASP.NET Core для React с Redux и create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/react-with-redux
ms.openlocfilehash: eab71349464255c9e333976caeba0e05a52909f0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773713"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a>Использование шаблона проекта React и Redux с ASP.NET Core

Обновленный шаблон проекта React и Redux служит удобной отправной точкой для создания приложений ASP.NET Core на основе конвенций React, Redux и [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA), позволяющих реализовать полноценный пользовательский интерфейс на стороне клиента.

За исключением команд создания проекта, все сведения о шаблоне React и Redux совпадают с данными о шаблоне React. Чтобы создать проект такого типа, выполните `dotnet new reactredux` вместо `dotnet new react`. Дополнительные сведения о функциональности обоих шаблонов на основе React вы найдете в [документации по шаблону React](xref:spa/react).

Сведения о настройке дочернего приложения React и Redux в IIS см. в статье [ReactRedux Template 2.1. Не удалось использовать SPA в службах IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).
