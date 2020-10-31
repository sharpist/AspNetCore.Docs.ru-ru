---
title: Общие сведения о безопасности ASP.NET Core
author: rick-anderson
description: Основные сведения о проверки подлинности, авторизации и безопасности в ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/index
ms.openlocfilehash: 3c86c66bebe8a5ce1c195ebf931193e7e2a73fef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051633"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="7808f-103">Общие сведения о безопасности ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7808f-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="7808f-104">ASP.NET Core позволяет разработчикам легко настраивать параметры безопасности для приложений и управлять ими.</span><span class="sxs-lookup"><span data-stu-id="7808f-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="7808f-105">ASP.NET Core содержит функции для управления аутентификацией, авторизацией, защитой данных, применением HTTPS, секретами приложений, защитой от атак XSRF/CSRF, а также для управления CORS.</span><span class="sxs-lookup"><span data-stu-id="7808f-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, XSRF/CSRF prevention, and CORS management.</span></span> <span data-ttu-id="7808f-106">Эти функции обеспечения безопасности позволяют создавать надежные и защищенные приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7808f-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="7808f-107">Функции безопасности в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7808f-107">ASP.NET Core security features</span></span>

<span data-ttu-id="7808f-108">ASP.NET Core предоставляет множество средств и библиотек для защиты приложений, включая встроенные поставщики удостоверений, однако помимо них можно использовать сторонние службы удостоверений, такие как Facebook, LinkedIn или Twitter.</span><span class="sxs-lookup"><span data-stu-id="7808f-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="7808f-109">В ASP.NET Core можно легко управлять секретами приложений, которые позволяют хранить и использовать конфиденциальные данные, не предоставляя их в коде.</span><span class="sxs-lookup"><span data-stu-id="7808f-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="7808f-110">Проверка подлинности и Авторизация</span><span class="sxs-lookup"><span data-stu-id="7808f-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="7808f-111">Проверка подлинности — это процесс, когда пользователь вводит учетные данные, которые затем сравниваются с данными, хранящимися в операционной системе, базе данных, приложении или ресурсе.</span><span class="sxs-lookup"><span data-stu-id="7808f-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="7808f-112">Если они совпадают, пользователи успешно проходят аутентификацию и во время авторизации могут выполнять разрешенные действия.</span><span class="sxs-lookup"><span data-stu-id="7808f-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="7808f-113">Авторизация представляет собой процесс, определяющий, какие действия может выполнять пользователь.</span><span class="sxs-lookup"><span data-stu-id="7808f-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="7808f-114">Если взглянуть на проверку подлинности с другой стороны, ее можно считать способом входа в определенную область, например на сервер, в базу данных, приложение или ресурс, тогда как авторизация определяет, какие действия с какими объектами может выполнять пользователь в этой области (на сервере, в базе данных или приложении).</span><span class="sxs-lookup"><span data-stu-id="7808f-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="7808f-115">Распространенные уязвимости в программном обеспечении</span><span class="sxs-lookup"><span data-stu-id="7808f-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="7808f-116">ASP.NET Core и EF содержат средства, помогающие защитить приложения и предотвратить возникновение нарушений безопасности.</span><span class="sxs-lookup"><span data-stu-id="7808f-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="7808f-117">Далее приводится список ссылок на документацию с описанием методов, позволяющих устранять наиболее распространенные уязвимости в веб-приложениях:</span><span class="sxs-lookup"><span data-stu-id="7808f-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="7808f-118">Атаки с выполнением межсайтовых сценариев (XSS)</span><span class="sxs-lookup"><span data-stu-id="7808f-118">Cross-Site Scripting (XSS) attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="7808f-119">Атаки путем внедрения кода SQL</span><span class="sxs-lookup"><span data-stu-id="7808f-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="7808f-120">Атаки с межсайтовой подделкой запросов (CSRF)</span><span class="sxs-lookup"><span data-stu-id="7808f-120">Cross-Site Request Forgery (XSRF/CSRF) attacks</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="7808f-121">Атаки с открытой переадресацией</span><span class="sxs-lookup"><span data-stu-id="7808f-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="7808f-122">Существует еще целый ряд уязвимостей, о которых следует знать.</span><span class="sxs-lookup"><span data-stu-id="7808f-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="7808f-123">Дополнительные сведения см. в других статьях раздела **Безопасность и :::no-loc(Identity):::** .</span><span class="sxs-lookup"><span data-stu-id="7808f-123">For more information, see the other articles in the **Security and :::no-loc(Identity):::** section of the table of contents.</span></span>
