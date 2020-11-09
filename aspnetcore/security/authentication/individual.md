---
title: Статьи на основе проектов ASP.NET Core, созданных с учетными записями отдельных пользователей
author: rick-anderson
description: Ознакомьтесь с статьями, основанными на ASP.NET Core проектах, созданных с учетными записями отдельных пользователей.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/individual
ms.openlocfilehash: 656006396de120b7feae6f2e08b5dad3b5a170b5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053349"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="9a935-103">Статьи на основе проектов ASP.NET Core, созданных с учетными записями отдельных пользователей</span><span class="sxs-lookup"><span data-stu-id="9a935-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="9a935-104">ASP.NET Core Identity включается в шаблоны проектов Visual Studio с параметром "индивидуальные учетные записи пользователей".</span><span class="sxs-lookup"><span data-stu-id="9a935-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="9a935-105">Шаблоны проверки подлинности доступны в .NET Core CLI с помощью `-au Individual` :</span><span class="sxs-lookup"><span data-stu-id="9a935-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="9a935-106">См. [эту ошибку GitHub](https://github.com/dotnet/AspNetCore/issues/5833) для проверки подлинности веб-API.</span><span class="sxs-lookup"><span data-stu-id="9a935-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="9a935-107">Без аутентификации</span><span class="sxs-lookup"><span data-stu-id="9a935-107">No Authentication</span></span>

<span data-ttu-id="9a935-108">Проверка подлинности указывается в .NET Core CLI с `-au` параметром.</span><span class="sxs-lookup"><span data-stu-id="9a935-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="9a935-109">В Visual Studio диалоговое окно **Изменение проверки подлинности** доступно для новых веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="9a935-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="9a935-110">По умолчанию для новых веб-приложений в Visual Studio **Проверка подлинности не** выполняется.</span><span class="sxs-lookup"><span data-stu-id="9a935-110">The default for new web apps in Visual Studio is **No Authentication** .</span></span>

<span data-ttu-id="9a935-111">Проекты, созданные без проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="9a935-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="9a935-112">Не следует включать веб-страницы и пользовательский интерфейс для входа и выхода.</span><span class="sxs-lookup"><span data-stu-id="9a935-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="9a935-113">Не содержать код проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9a935-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="9a935-114">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="9a935-114">Windows Authentication</span></span>

<span data-ttu-id="9a935-115">Проверка подлинности Windows указана для новых веб-приложений в .NET Core CLI с `-au Windows` параметром.</span><span class="sxs-lookup"><span data-stu-id="9a935-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="9a935-116">В Visual Studio диалоговое окно **Изменение проверки подлинности** предоставляет параметры **проверки подлинности Windows** .</span><span class="sxs-lookup"><span data-stu-id="9a935-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="9a935-117">Если выбрана проверка подлинности Windows, приложение настроено для использования [модуля IIS проверки подлинности Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="9a935-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="9a935-118">Проверка подлинности Windows предназначена для веб-сайтов интрасети.</span><span class="sxs-lookup"><span data-stu-id="9a935-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="9a935-119">Параметры проверки подлинности DotNet New webapp</span><span class="sxs-lookup"><span data-stu-id="9a935-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="9a935-120">В следующей таблице приведены параметры проверки подлинности, доступные для новых веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="9a935-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="9a935-121">Параметр</span><span class="sxs-lookup"><span data-stu-id="9a935-121">Option</span></span> | <span data-ttu-id="9a935-122">Тип проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="9a935-122">Type of authentication</span></span> | <span data-ttu-id="9a935-123">Ссылка на дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="9a935-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="9a935-124">Нет</span><span class="sxs-lookup"><span data-stu-id="9a935-124">None</span></span>            |  <span data-ttu-id="9a935-125">Без проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="9a935-125">No authentication</span></span> | | 
| <span data-ttu-id="9a935-126">Индивидуальное лицо</span><span class="sxs-lookup"><span data-stu-id="9a935-126">Individual</span></span>      |  <span data-ttu-id="9a935-127">Индивидуальная проверка подлинности</span><span class="sxs-lookup"><span data-stu-id="9a935-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="9a935-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="9a935-128">IndividualB2C</span></span>   |  <span data-ttu-id="9a935-129">Индивидуальная проверка подлинности, размещенная в облаке, с Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="9a935-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="9a935-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="9a935-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="9a935-131">синглеорг</span><span class="sxs-lookup"><span data-stu-id="9a935-131">SingleOrg</span></span>       |  <span data-ttu-id="9a935-132">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="9a935-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="9a935-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="9a935-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="9a935-134">мултиорг</span><span class="sxs-lookup"><span data-stu-id="9a935-134">MultiOrg</span></span>        |  <span data-ttu-id="9a935-135">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="9a935-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="9a935-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="9a935-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="9a935-137">Windows</span><span class="sxs-lookup"><span data-stu-id="9a935-137">Windows</span></span>         |  <span data-ttu-id="9a935-138">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="9a935-138">Windows authentication</span></span> | [<span data-ttu-id="9a935-139">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="9a935-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="9a935-140">Параметры проверки подлинности New webapp в Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a935-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="9a935-141">В следующей таблице показаны параметры проверки подлинности, доступные при создании нового веб-приложения с помощью Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9a935-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="9a935-142">Параметр</span><span class="sxs-lookup"><span data-stu-id="9a935-142">Option</span></span> | <span data-ttu-id="9a935-143">Тип проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="9a935-143">Type of authentication</span></span> | <span data-ttu-id="9a935-144">Ссылка на дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="9a935-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="9a935-145">Нет</span><span class="sxs-lookup"><span data-stu-id="9a935-145">None</span></span>            |  <span data-ttu-id="9a935-146">Без проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="9a935-146">No authentication</span></span> | | 
| <span data-ttu-id="9a935-147">Индивидуальные учетные записи пользователей и хранение учетных записей пользователей в приложении</span><span class="sxs-lookup"><span data-stu-id="9a935-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="9a935-148">Индивидуальная проверка подлинности</span><span class="sxs-lookup"><span data-stu-id="9a935-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="9a935-149">Учетные записи отдельных пользователей и подключение к существующему хранилищу пользователей в облаке</span><span class="sxs-lookup"><span data-stu-id="9a935-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="9a935-150">Индивидуальная проверка подлинности, размещенная в облаке, с Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="9a935-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="9a935-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="9a935-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="9a935-152">Рабочее или учебное облако/Единая Организация</span><span class="sxs-lookup"><span data-stu-id="9a935-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="9a935-153">Корпоративная аутентификация для отдельного клиента</span><span class="sxs-lookup"><span data-stu-id="9a935-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="9a935-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="9a935-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="9a935-155">Рабочее или учебное облако/несколько Организации</span><span class="sxs-lookup"><span data-stu-id="9a935-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="9a935-156">Корпоративная аутентификация для нескольких клиентов</span><span class="sxs-lookup"><span data-stu-id="9a935-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="9a935-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="9a935-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="9a935-158">Windows</span><span class="sxs-lookup"><span data-stu-id="9a935-158">Windows</span></span>         |  <span data-ttu-id="9a935-159">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="9a935-159">Windows authentication</span></span> | [<span data-ttu-id="9a935-160">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="9a935-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="9a935-161">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9a935-161">Additional resources</span></span>

<span data-ttu-id="9a935-162">В следующих статьях показано, как использовать код, созданный в шаблонах ASP.NET Core, использующих отдельные учетные записи пользователей.</span><span class="sxs-lookup"><span data-stu-id="9a935-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="9a935-163">Подтверждение учетной записи и восстановление пароля в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a935-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="9a935-164">Создание приложения ASP.NET Core с данными пользователя, защищенными с помощью авторизации</span><span class="sxs-lookup"><span data-stu-id="9a935-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
