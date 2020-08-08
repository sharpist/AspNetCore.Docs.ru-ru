---
title: Защита приложений ASP.NET Core Blazor Server
author: guardrex
description: Сведения о защите приложений Blazor Server как приложений ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: 4dc9040b9410304eb33e5df7c47db2f9a42152d3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014000"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="dbd75-103">Защита приложений ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="dbd75-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="dbd75-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="dbd75-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dbd75-105">Приложения Blazor Server настраиваются для обеспечения безопасности так же, как приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbd75-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="dbd75-106">Дополнительные сведения см. в статьях в документе <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="dbd75-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="dbd75-107">Разделы в этом обзоре посвящены Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="dbd75-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="dbd75-108">Шаблон проекта Blazor Server</span><span class="sxs-lookup"><span data-stu-id="dbd75-108">Blazor Server project template</span></span>

<span data-ttu-id="dbd75-109">Шаблон проекта Blazor Server можно настроить для проверки подлинности при создании проекта.</span><span class="sxs-lookup"><span data-stu-id="dbd75-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dbd75-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbd75-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dbd75-111">Следуйте инструкциям по работе с Visual Studio (<xref:blazor/tooling>), чтобы создать проект Blazor Server с механизмом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dbd75-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="dbd75-112">Выбрав шаблон **Приложение Blazor Server** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="dbd75-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="dbd75-113">Откроется диалоговое окно с тем же набором механизмов аутентификации, которые доступны для других проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbd75-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="dbd75-114">**Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="dbd75-114">**No Authentication**</span></span>
* <span data-ttu-id="dbd75-115">**Учетные записи отдельных пользователей**. Учетные записи пользователей могут храниться:</span><span class="sxs-lookup"><span data-stu-id="dbd75-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="dbd75-116">Внутри приложения с помощью системы [Identity](xref:security/authentication/identity) в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbd75-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="dbd75-117">в [Azure AD B2C](xref:security/authentication/azure-ad-b2c);</span><span class="sxs-lookup"><span data-stu-id="dbd75-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="dbd75-118">**рабочие или учебные учетные записи**.</span><span class="sxs-lookup"><span data-stu-id="dbd75-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="dbd75-119">**Проверка подлинности Windows**</span><span class="sxs-lookup"><span data-stu-id="dbd75-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dbd75-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbd75-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dbd75-121">Следуйте инструкциям по работе с Visual Studio Code (<xref:blazor/tooling>), чтобы создать проект Blazor Server с механизмом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dbd75-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="dbd75-122">Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="dbd75-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="dbd75-123">Механизм аутентификации</span><span class="sxs-lookup"><span data-stu-id="dbd75-123">Authentication mechanism</span></span> | <span data-ttu-id="dbd75-124">Описание</span><span class="sxs-lookup"><span data-stu-id="dbd75-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="dbd75-125">`None` (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="dbd75-125">`None` (default)</span></span>         | <span data-ttu-id="dbd75-126">без аутентификации;</span><span class="sxs-lookup"><span data-stu-id="dbd75-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="dbd75-127">Хранение пользователей в приложении с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="dbd75-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="dbd75-128">Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="dbd75-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="dbd75-129">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="dbd75-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="dbd75-130">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="dbd75-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="dbd75-131">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="dbd75-131">Windows Authentication</span></span> |

<span data-ttu-id="dbd75-132">С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="dbd75-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="dbd75-133">Создайте папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="dbd75-133">Create a folder for the project.</span></span>
* <span data-ttu-id="dbd75-134">Дайте проекту имя.</span><span class="sxs-lookup"><span data-stu-id="dbd75-134">Name the project.</span></span>

<span data-ttu-id="dbd75-135">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbd75-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbd75-136">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="dbd75-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dbd75-137">Следуйте инструкциям по работе с Visual Studio для Mac (<xref:blazor/tooling>).</span><span class="sxs-lookup"><span data-stu-id="dbd75-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="dbd75-138">На шаге **Настройка нового приложения Blazor Server** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="dbd75-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="dbd75-139">Приложение будет создано для отдельных пользователей, хранимых в приложении с помощью ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="dbd75-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="dbd75-140">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="dbd75-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="dbd75-141">Создайте новый проект Blazor Server с механизмом проверки подлинности, используя следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="dbd75-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="dbd75-142">Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="dbd75-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="dbd75-143">Механизм аутентификации</span><span class="sxs-lookup"><span data-stu-id="dbd75-143">Authentication mechanism</span></span> | <span data-ttu-id="dbd75-144">Описание</span><span class="sxs-lookup"><span data-stu-id="dbd75-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="dbd75-145">`None` (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="dbd75-145">`None` (default)</span></span>         | <span data-ttu-id="dbd75-146">без аутентификации;</span><span class="sxs-lookup"><span data-stu-id="dbd75-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="dbd75-147">Хранение пользователей в приложении с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="dbd75-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="dbd75-148">Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="dbd75-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="dbd75-149">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="dbd75-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="dbd75-150">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="dbd75-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="dbd75-151">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="dbd75-151">Windows Authentication</span></span> |

<span data-ttu-id="dbd75-152">С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="dbd75-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="dbd75-153">Создайте папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="dbd75-153">Create a folder for the project.</span></span>
* <span data-ttu-id="dbd75-154">Дайте проекту имя.</span><span class="sxs-lookup"><span data-stu-id="dbd75-154">Name the project.</span></span>

<span data-ttu-id="dbd75-155">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbd75-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="dbd75-156">Формирование шаблонов Identity</span><span class="sxs-lookup"><span data-stu-id="dbd75-156">Scaffold Identity</span></span>

<span data-ttu-id="dbd75-157">Сформируйте шаблоны Identity для проекта Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="dbd75-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="dbd75-158">[Без существующей авторизации](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="dbd75-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="dbd75-159">[С авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="dbd75-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
