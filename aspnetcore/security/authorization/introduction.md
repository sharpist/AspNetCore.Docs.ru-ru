---
title: Общие сведения об авторизации в ASP.NET Core
author: rick-anderson
description: Ознакомьтесь с основами авторизации и принципами работы авторизации в ASP.NET Core приложениях.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/introduction
ms.openlocfilehash: b0d6c91adfb5dc273aeb662cb2c249626ef1d3c6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405436"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="4d7c8-103">Общие сведения об авторизации в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d7c8-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="4d7c8-104">Авторизация относится к процессу, который определяет, что может сделать пользователь.</span><span class="sxs-lookup"><span data-stu-id="4d7c8-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="4d7c8-105">Например, пользователю с правами администратора разрешается создавать библиотеки документов, добавлять документы, редактировать документы и удалять их.</span><span class="sxs-lookup"><span data-stu-id="4d7c8-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="4d7c8-106">Пользователь без прав администратора, работающий с библиотекой, имеет разрешение только на чтение документов.</span><span class="sxs-lookup"><span data-stu-id="4d7c8-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="4d7c8-107">Авторизация является ортогональной и независимой от проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="4d7c8-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="4d7c8-108">Однако Авторизация требует использования механизма проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="4d7c8-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="4d7c8-109">Проверка подлинности — это процесс проверки пользователя.</span><span class="sxs-lookup"><span data-stu-id="4d7c8-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="4d7c8-110">При использовании аутентификации возможно создание одного или нескольких удостоверений для текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="4d7c8-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="4d7c8-111">Дополнительные сведения о проверке подлинности в ASP.NET Core см. в разделе <xref:security/authentication/index> .</span><span class="sxs-lookup"><span data-stu-id="4d7c8-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="4d7c8-112">Типы авторизации</span><span class="sxs-lookup"><span data-stu-id="4d7c8-112">Authorization types</span></span>

<span data-ttu-id="4d7c8-113">ASP.NET Coreная авторизация предоставляет простую, декларативную [роль](xref:security/authorization/roles) и многофункциональную модель [на основе политик](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="4d7c8-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="4d7c8-114">Авторизация выражается в требованиях, и обработчики оценивают утверждения пользователя по требованиям.</span><span class="sxs-lookup"><span data-stu-id="4d7c8-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="4d7c8-115">Императивные проверки могут основываться на простых политиках или политиках, которые оценивают удостоверение пользователя и свойства ресурса, к которому пользователь пытается получить доступ.</span><span class="sxs-lookup"><span data-stu-id="4d7c8-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="4d7c8-116">Пространства имен</span><span class="sxs-lookup"><span data-stu-id="4d7c8-116">Namespaces</span></span>

<span data-ttu-id="4d7c8-117">Компоненты авторизации, включая `AuthorizeAttribute` атрибуты и `AllowAnonymousAttribute` , находятся в `Microsoft.AspNetCore.Authorization` пространстве имен.</span><span class="sxs-lookup"><span data-stu-id="4d7c8-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="4d7c8-118">Обратитесь к документации по [простой авторизации](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="4d7c8-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
