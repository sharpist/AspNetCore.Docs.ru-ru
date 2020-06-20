---
title: Безопасные приложения ASP.NET Core Blazor Server
author: guardrex
description: Сведения о защите приложений Blazor Server как приложений ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: a8604ca6ea60386bb3c54c950205ee695d37c689
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103141"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="874b0-103">Безопасные приложения ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="874b0-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="874b0-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="874b0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="874b0-105">Шаблон проекта "Сервер Blazor"</span><span class="sxs-lookup"><span data-stu-id="874b0-105">Blazor Server project template</span></span>

<span data-ttu-id="874b0-106">Шаблон проекта "Сервер Blazor" можно настроить для проверки подлинности при создании проекта.</span><span class="sxs-lookup"><span data-stu-id="874b0-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="874b0-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="874b0-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="874b0-108">Следуйте указаниям по работе с Visual Studio (<xref:blazor/get-started>), чтобы создать проект Blazor Server с механизмом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="874b0-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="874b0-109">Выбрав шаблон **Серверное приложение Blazor** в диалоговом окне **Создать веб-приложение ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="874b0-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="874b0-110">Откроется диалоговое окно с тем же набором механизмов аутентификации, которые доступны для других проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="874b0-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="874b0-111">**Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="874b0-111">**No Authentication**</span></span>
* <span data-ttu-id="874b0-112">**Учетные записи отдельных пользователей**. Учетные записи пользователей могут храниться:</span><span class="sxs-lookup"><span data-stu-id="874b0-112">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="874b0-113">Внутри приложения с помощью системы [Identity](xref:security/authentication/identity) в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="874b0-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="874b0-114">в [Azure AD B2C](xref:security/authentication/azure-ad-b2c);</span><span class="sxs-lookup"><span data-stu-id="874b0-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="874b0-115">**рабочие или учебные учетные записи**.</span><span class="sxs-lookup"><span data-stu-id="874b0-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="874b0-116">**Проверка подлинности Windows**</span><span class="sxs-lookup"><span data-stu-id="874b0-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="874b0-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="874b0-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="874b0-118">Следуйте указаниям по работе с Visual Studio Code (<xref:blazor/get-started>), чтобы создать проект Blazor Server с механизмом проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="874b0-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="874b0-119">Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="874b0-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="874b0-120">Механизм аутентификации</span><span class="sxs-lookup"><span data-stu-id="874b0-120">Authentication mechanism</span></span> | <span data-ttu-id="874b0-121">Описание</span><span class="sxs-lookup"><span data-stu-id="874b0-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="874b0-122">`None` (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="874b0-122">`None` (default)</span></span>         | <span data-ttu-id="874b0-123">без аутентификации;</span><span class="sxs-lookup"><span data-stu-id="874b0-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="874b0-124">Хранение пользователей в приложении с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="874b0-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="874b0-125">Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="874b0-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="874b0-126">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="874b0-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="874b0-127">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="874b0-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="874b0-128">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="874b0-128">Windows Authentication</span></span> |

<span data-ttu-id="874b0-129">С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="874b0-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="874b0-130">Создайте папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="874b0-130">Create a folder for the project.</span></span>
* <span data-ttu-id="874b0-131">Дайте проекту имя.</span><span class="sxs-lookup"><span data-stu-id="874b0-131">Name the project.</span></span>

<span data-ttu-id="874b0-132">Подробнее см. статью о команде [dotnet new](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="874b0-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="874b0-133">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="874b0-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="874b0-134">Следуйте инструкциям по работе с Visual Studio для Mac (<xref:blazor/get-started>).</span><span class="sxs-lookup"><span data-stu-id="874b0-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="874b0-135">На шаге **Настройка нового серверного приложения Blazor** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="874b0-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="874b0-136">Приложение будет создано для отдельных пользователей, хранимых в приложении с помощью ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="874b0-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="874b0-137">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="874b0-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="874b0-138">Выполните указания для .NET Core CLI из статьи <xref:blazor/get-started>, чтобы создать новый проект "Сервер Blazor", используя следующий механизм проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="874b0-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="874b0-139">Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="874b0-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="874b0-140">Механизм аутентификации</span><span class="sxs-lookup"><span data-stu-id="874b0-140">Authentication mechanism</span></span> | <span data-ttu-id="874b0-141">Описание</span><span class="sxs-lookup"><span data-stu-id="874b0-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="874b0-142">`None` (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="874b0-142">`None` (default)</span></span>         | <span data-ttu-id="874b0-143">без аутентификации;</span><span class="sxs-lookup"><span data-stu-id="874b0-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="874b0-144">Хранение пользователей в приложении с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="874b0-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="874b0-145">Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="874b0-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="874b0-146">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="874b0-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="874b0-147">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="874b0-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="874b0-148">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="874b0-148">Windows Authentication</span></span> |

<span data-ttu-id="874b0-149">С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="874b0-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="874b0-150">Создайте папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="874b0-150">Create a folder for the project.</span></span>
* <span data-ttu-id="874b0-151">Дайте проекту имя.</span><span class="sxs-lookup"><span data-stu-id="874b0-151">Name the project.</span></span>

<span data-ttu-id="874b0-152">Подробнее см. статью о команде [dotnet new](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="874b0-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="874b0-153">Защита существующего приложения</span><span class="sxs-lookup"><span data-stu-id="874b0-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="874b0-154"> Серверные приложения настраиваются для обеспечения безопасности так же, как приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="874b0-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="874b0-155">Дополнительные сведения см. в статьях в документе <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="874b0-155">For more information, see the articles under <xref:security/index>.</span></span>

## <a name="scaffold-identity"></a><span data-ttu-id="874b0-156">Формирование шаблонов Identity</span><span class="sxs-lookup"><span data-stu-id="874b0-156">Scaffold Identity</span></span>

<span data-ttu-id="874b0-157">Сформируйте шаблоны Identity для проекта "Сервер Blazor":</span><span class="sxs-lookup"><span data-stu-id="874b0-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="874b0-158">[Без существующей авторизации](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="874b0-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="874b0-159">[С авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="874b0-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
