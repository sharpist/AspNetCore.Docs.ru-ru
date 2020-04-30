---
title: Безопасные приложения ASP.NET Core Blazor Server
author: guardrex
description: Сведения о защите приложений Blazor Server как приложений ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 0021911b731e57bc6eabf857c27a13462e7400ae
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206332"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="27354-103">Безопасные приложения ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="27354-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="27354-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="27354-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="27354-105">Шаблон проекта Blazor Server</span><span class="sxs-lookup"><span data-stu-id="27354-105">Blazor Server project template</span></span>

<span data-ttu-id="27354-106">Шаблон Blazor Server позволяет автоматически настроить аутентификацию при создании проекта.</span><span class="sxs-lookup"><span data-stu-id="27354-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27354-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27354-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="27354-108">Следуйте указаниям по работе с Visual Studio (<xref:blazor/get-started>), чтобы создать проект Blazor на стороне сервера с механизмом аутентификации.</span><span class="sxs-lookup"><span data-stu-id="27354-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="27354-109">Выбрав шаблон **Серверное приложение Blazor** в диалоговом окне **Создать веб-приложение ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="27354-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="27354-110">Откроется диалоговое окно с тем же набором механизмов аутентификации, которые доступны для других проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="27354-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="27354-111">**Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="27354-111">**No Authentication**</span></span>
* <span data-ttu-id="27354-112">**Учетные записи отдельных пользователей**, которые можно хранить следующим образом:</span><span class="sxs-lookup"><span data-stu-id="27354-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="27354-113">в приложении с помощью системы [удостоверений](xref:security/authentication/identity) ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="27354-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="27354-114">в [Azure AD B2C](xref:security/authentication/azure-ad-b2c);</span><span class="sxs-lookup"><span data-stu-id="27354-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="27354-115">**рабочие или учебные учетные записи**.</span><span class="sxs-lookup"><span data-stu-id="27354-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="27354-116">**Проверка подлинности Windows**</span><span class="sxs-lookup"><span data-stu-id="27354-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="27354-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27354-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="27354-118">Следуйте указаниям по работе с Visual Studio Code (<xref:blazor/get-started>), чтобы создать проект Blazor на стороне сервера с механизмом аутентификации.</span><span class="sxs-lookup"><span data-stu-id="27354-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="27354-119">Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="27354-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="27354-120">Механизм аутентификации</span><span class="sxs-lookup"><span data-stu-id="27354-120">Authentication mechanism</span></span> | <span data-ttu-id="27354-121">Описание</span><span class="sxs-lookup"><span data-stu-id="27354-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="27354-122">`None` (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="27354-122">`None` (default)</span></span>         | <span data-ttu-id="27354-123">без аутентификации;</span><span class="sxs-lookup"><span data-stu-id="27354-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="27354-124">Пользователи, сохраненные в приложении с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="27354-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="27354-125">Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="27354-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="27354-126">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="27354-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="27354-127">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="27354-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="27354-128">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="27354-128">Windows Authentication</span></span> |

<span data-ttu-id="27354-129">С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="27354-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="27354-130">Создайте папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="27354-130">Create a folder for the project.</span></span>
* <span data-ttu-id="27354-131">Дайте проекту имя.</span><span class="sxs-lookup"><span data-stu-id="27354-131">Name the project.</span></span>

<span data-ttu-id="27354-132">Подробнее см. статью о команде [dotnet new](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="27354-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="27354-133">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="27354-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="27354-134">Следуйте инструкциям по работе с Visual Studio для Mac (<xref:blazor/get-started>).</span><span class="sxs-lookup"><span data-stu-id="27354-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="27354-135">На шаге **Настройка нового приложения Blazor Server** выберите **Индивидуальная аутентификация (в приложении)** в раскрывающемся списке **Аутентификация**.</span><span class="sxs-lookup"><span data-stu-id="27354-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="27354-136">Приложение создается для отдельных пользователей, сохраненных в приложении с удостоверением ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="27354-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="27354-137">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="27354-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="27354-138">Следуйте инструкциям по работе с .NET Core CLI (<xref:blazor/get-started>), чтобы создать проект Blazor Server с механизмом аутентификации.</span><span class="sxs-lookup"><span data-stu-id="27354-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="27354-139">Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="27354-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="27354-140">Механизм аутентификации</span><span class="sxs-lookup"><span data-stu-id="27354-140">Authentication mechanism</span></span> | <span data-ttu-id="27354-141">Описание</span><span class="sxs-lookup"><span data-stu-id="27354-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="27354-142">`None` (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="27354-142">`None` (default)</span></span>         | <span data-ttu-id="27354-143">без аутентификации;</span><span class="sxs-lookup"><span data-stu-id="27354-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="27354-144">Пользователи, сохраненные в приложении с помощью ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="27354-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="27354-145">Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="27354-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="27354-146">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="27354-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="27354-147">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="27354-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="27354-148">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="27354-148">Windows Authentication</span></span> |

<span data-ttu-id="27354-149">С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="27354-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="27354-150">Создайте папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="27354-150">Create a folder for the project.</span></span>
* <span data-ttu-id="27354-151">Дайте проекту имя.</span><span class="sxs-lookup"><span data-stu-id="27354-151">Name the project.</span></span>

<span data-ttu-id="27354-152">Подробнее см. статью о команде [dotnet new](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="27354-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---
