---
title: Начало работы с ASP.NET Core
author: rick-anderson
description: Краткий учебник, в котором с помощью ASP.NET Core создается и запускается простое приложение Hello World.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: getting-started
ms.openlocfilehash: 565cde233d6fc5f085ae1709e167e73980d5c3c2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776237"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="9da64-103">Учебник. Начало работы с ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9da64-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="9da64-104">В этом руководстве показано, как с помощью .NET Core CLI создать и запустить веб-приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9da64-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="9da64-105">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="9da64-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9da64-106">создавать проект веб-приложения;</span><span class="sxs-lookup"><span data-stu-id="9da64-106">Create a web app project.</span></span>
> * <span data-ttu-id="9da64-107">устанавливать доверие к сертификату разработки;</span><span class="sxs-lookup"><span data-stu-id="9da64-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="9da64-108">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="9da64-108">Run the app.</span></span>
> * <span data-ttu-id="9da64-109">редактировать страницу Razor.</span><span class="sxs-lookup"><span data-stu-id="9da64-109">Edit a Razor page.</span></span>

<span data-ttu-id="9da64-110">В итоге вы получите рабочее веб-приложение на локальном компьютере.</span><span class="sxs-lookup"><span data-stu-id="9da64-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Домашняя страница веб-приложения](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="9da64-112">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="9da64-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="9da64-113">Создание проекта веб-приложения</span><span class="sxs-lookup"><span data-stu-id="9da64-113">Create a web app project</span></span>

<span data-ttu-id="9da64-114">Откройте окно командной оболочки и введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="9da64-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="9da64-115">Предыдущая команда позволяет:</span><span class="sxs-lookup"><span data-stu-id="9da64-115">The preceding command:</span></span>

* <span data-ttu-id="9da64-116">создать веб-сайт;</span><span class="sxs-lookup"><span data-stu-id="9da64-116">Creates a new web app.</span></span>  
* <span data-ttu-id="9da64-117">с помощью параметра `-o aspnetcoreapp` создать каталог *aspnetcoreapp* с исходными файлами приложения.</span><span class="sxs-lookup"><span data-stu-id="9da64-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="9da64-118">Установка доверия к сертификату разработки</span><span class="sxs-lookup"><span data-stu-id="9da64-118">Trust the development certificate</span></span>

<span data-ttu-id="9da64-119">Установите доверие к сертификату разработки HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9da64-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="9da64-120">Windows</span><span class="sxs-lookup"><span data-stu-id="9da64-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="9da64-121">Приведенная выше команда отображает следующее диалоговое окно.</span><span class="sxs-lookup"><span data-stu-id="9da64-121">The preceding command displays the following dialog:</span></span>

![Диалоговое окно "Предупреждение о безопасности"](~/getting-started/_static/cert.png)

<span data-ttu-id="9da64-123">Выберите **Да**, если согласны доверять сертификату разработки.</span><span class="sxs-lookup"><span data-stu-id="9da64-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="9da64-124">macOS</span><span class="sxs-lookup"><span data-stu-id="9da64-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="9da64-125">Приведенная выше команда отображает следующее сообщение.</span><span class="sxs-lookup"><span data-stu-id="9da64-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="9da64-126">*Запрошено доверие к сертификату разработки HTTPS. Если сертификат не является доверенным, выполните следующую команду:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="9da64-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="9da64-127">Эта команда может запросить пароль для установки сертификата в системной цепочке ключей.</span><span class="sxs-lookup"><span data-stu-id="9da64-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="9da64-128">Введите пароль, если согласны доверять сертификату разработки.</span><span class="sxs-lookup"><span data-stu-id="9da64-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="9da64-129">Linux</span><span class="sxs-lookup"><span data-stu-id="9da64-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="9da64-130">Просмотрите документацию по дистрибутиву Linux, чтобы узнать, как установить отношение доверия к сертификату разработки HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9da64-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="9da64-131">Дополнительные сведения см. в разделе [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) (Настройка доверия к сертификату разработки HTTPS ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="9da64-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="9da64-132">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="9da64-132">Run the app</span></span>

<span data-ttu-id="9da64-133">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="9da64-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="9da64-134">Когда в командной оболочке будет показано, что приложение запущено, откройте страницу `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9da64-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="9da64-135">Изменение страницы Razor</span><span class="sxs-lookup"><span data-stu-id="9da64-135">Edit a Razor page</span></span>

<span data-ttu-id="9da64-136">Откройте *Pages/Index.cshtml*, а затем измените и сохраните страницу, добавив выделенное исправление:</span><span class="sxs-lookup"><span data-stu-id="9da64-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="9da64-137">Перейдите на страницу `https://localhost:5001`, обновите ее и проверьте, отобразились ли изменения.</span><span class="sxs-lookup"><span data-stu-id="9da64-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9da64-138">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="9da64-138">Next steps</span></span>

<span data-ttu-id="9da64-139">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="9da64-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9da64-140">создавать проект веб-приложения;</span><span class="sxs-lookup"><span data-stu-id="9da64-140">Create a web app project.</span></span>
> * <span data-ttu-id="9da64-141">устанавливать доверие к сертификату разработки;</span><span class="sxs-lookup"><span data-stu-id="9da64-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="9da64-142">Запустите проект.</span><span class="sxs-lookup"><span data-stu-id="9da64-142">Run the project.</span></span>
> * <span data-ttu-id="9da64-143">вносить изменения.</span><span class="sxs-lookup"><span data-stu-id="9da64-143">Make a change.</span></span>

<span data-ttu-id="9da64-144">Дополнительные сведения об ASP.NET Core см. в разделе рекомендуемой схемы обучения в вводной статье:</span><span class="sxs-lookup"><span data-stu-id="9da64-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
