---
title: Общие сведения об авторизации в ASP.NET Core
author: rick-anderson
description: Ознакомьтесь с основами авторизации и принципами работы авторизации в ASP.NET Core приложениях.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- appsettings.json
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
uid: security/authorization/introduction
ms.openlocfilehash: 8afee7d8bc6b7ad71154916f4a41a2b417b24f9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060252"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Общие сведения об авторизации в ASP.NET Core

<a name="security-authorization-introduction"></a>

Авторизация относится к процессу, который определяет, что может сделать пользователь. Например, пользователю с правами администратора разрешается создавать библиотеки документов, добавлять документы, редактировать документы и удалять их. Пользователь без прав администратора, работающий с библиотекой, имеет разрешение только на чтение документов.

Авторизация является ортогональной и независимой от проверки подлинности. Однако Авторизация требует использования механизма проверки подлинности. Проверка подлинности — это процесс проверки пользователя. При использовании аутентификации возможно создание одного или нескольких удостоверений для текущего пользователя.

Дополнительные сведения о проверке подлинности в ASP.NET Core см. в разделе <xref:security/authentication/index> .

## <a name="authorization-types"></a>Типы авторизации

ASP.NET Coreная авторизация предоставляет простую, декларативную [роль](xref:security/authorization/roles) и многофункциональную модель [на основе политик](xref:security/authorization/policies) . Авторизация выражается в требованиях, и обработчики оценивают утверждения пользователя по требованиям. Императивные проверки могут основываться на простых политиках или политиках, которые оценивают удостоверение пользователя и свойства ресурса, к которому пользователь пытается получить доступ.

## <a name="namespaces"></a>Пространства имен

Компоненты авторизации, включая `AuthorizeAttribute` атрибуты и `AllowAnonymousAttribute` , находятся в `Microsoft.AspNetCore.Authorization` пространстве имен.

Обратитесь к документации по [простой авторизации](xref:security/authorization/simple).
