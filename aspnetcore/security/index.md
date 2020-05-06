---
title: Общие сведения о безопасности ASP.NET Core
author: rick-anderson
description: Основные сведения о проверки подлинности, авторизации и безопасности в ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/index
ms.openlocfilehash: b507832e34ac850d2bd4e80bab3066e73ea2ad95
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776517"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="4b4e6-103">Общие сведения о безопасности ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b4e6-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="4b4e6-104">ASP.NET Core позволяет разработчикам легко настраивать параметры безопасности для приложений и управлять ими.</span><span class="sxs-lookup"><span data-stu-id="4b4e6-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="4b4e6-105">ASP.NET Core содержит функции для управления проверкой подлинности, авторизацией, защитой данных, применением HTTPS, секретами приложений, защитой от подделки запросов, а также для управления CORS.</span><span class="sxs-lookup"><span data-stu-id="4b4e6-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="4b4e6-106">Эти функции обеспечения безопасности позволяют создавать надежные и защищенные приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b4e6-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="4b4e6-107">Функции безопасности в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b4e6-107">ASP.NET Core security features</span></span>

<span data-ttu-id="4b4e6-108">ASP.NET Core предоставляет множество средств и библиотек для защиты приложений, включая встроенные поставщики Identity, однако помимо них можно использовать сторонние службы удостоверений, такие как Facebook, LinkedIn и Twitter.</span><span class="sxs-lookup"><span data-stu-id="4b4e6-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in Identity providers but you can use 3rd party identity services such as Facebook, Twitter, or LinkedIn.</span></span> <span data-ttu-id="4b4e6-109">В ASP.NET Core можно легко управлять секретами приложений, которые позволяют хранить и использовать конфиденциальные данные, не предоставляя их в коде.</span><span class="sxs-lookup"><span data-stu-id="4b4e6-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="4b4e6-110">Проверка подлинности и Авторизация</span><span class="sxs-lookup"><span data-stu-id="4b4e6-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="4b4e6-111">Проверка подлинности — это процесс, когда пользователь вводит учетные данные, которые затем сравниваются с данными, хранящимися в операционной системе, базе данных, приложении или ресурсе.</span><span class="sxs-lookup"><span data-stu-id="4b4e6-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="4b4e6-112">Если они совпадают, пользователи успешно проходят аутентификацию и во время авторизации могут выполнять разрешенные действия.</span><span class="sxs-lookup"><span data-stu-id="4b4e6-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="4b4e6-113">Авторизация представляет собой процесс, определяющий, какие действия может выполнять пользователь.</span><span class="sxs-lookup"><span data-stu-id="4b4e6-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="4b4e6-114">Если взглянуть на проверку подлинности с другой стороны, ее можно считать способом входа в определенную область, например на сервер, в базу данных, приложение или ресурс, тогда как авторизация определяет, какие действия с какими объектами может выполнять пользователь в этой области (на сервере, в базе данных или приложении).</span><span class="sxs-lookup"><span data-stu-id="4b4e6-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="4b4e6-115">Распространенные уязвимости в программном обеспечении</span><span class="sxs-lookup"><span data-stu-id="4b4e6-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="4b4e6-116">ASP.NET Core и EF содержат средства, помогающие защитить приложения и предотвратить возникновение нарушений безопасности.</span><span class="sxs-lookup"><span data-stu-id="4b4e6-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="4b4e6-117">Далее приводится список ссылок на документацию с описанием методов, позволяющих устранять наиболее распространенные уязвимости в веб-приложениях:</span><span class="sxs-lookup"><span data-stu-id="4b4e6-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="4b4e6-118">Атаки с использованием межузловых сценариев</span><span class="sxs-lookup"><span data-stu-id="4b4e6-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="4b4e6-119">Атаки путем внедрения кода SQL</span><span class="sxs-lookup"><span data-stu-id="4b4e6-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="4b4e6-120">Подделки межсайтовых запросов (CSRF)</span><span class="sxs-lookup"><span data-stu-id="4b4e6-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="4b4e6-121">Атаки с открытой переадресацией</span><span class="sxs-lookup"><span data-stu-id="4b4e6-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="4b4e6-122">Существует еще целый ряд уязвимостей, о которых следует знать.</span><span class="sxs-lookup"><span data-stu-id="4b4e6-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="4b4e6-123">Дополнительные сведения см. в других статьях раздела **Безопасность и Identity** .</span><span class="sxs-lookup"><span data-stu-id="4b4e6-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
