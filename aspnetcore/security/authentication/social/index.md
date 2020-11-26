---
title: Проверка подлинности Facebook, Google и внешних поставщиков в ASP.NET Core
author: rick-anderson
description: В этом учебнике демонстрируется создание приложения ASP.NET Core с использованием OAuth 2.0 с внешними поставщиками проверки подлинности.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
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
uid: security/authentication/social/index
ms.openlocfilehash: ca5fd8f746e759d1994dde9a2a0d5b5fd6c88d1a
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870455"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="2bd78-103">Проверка подлинности Facebook, Google и внешних поставщиков в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2bd78-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="2bd78-104">Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="2bd78-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2bd78-105">В этом руководстве показано создание приложения ASP.NET Core, позволяющего пользователям выполнять вход с помощью OAuth 2.0 и учетных данных от внешних поставщиков проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2bd78-105">This tutorial demonstrates how to build an ASP.NET Core app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="2bd78-106">В следующих разделах рассматриваются такие поставщики, как [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) и [Майкрософт](xref:security/authentication/microsoft-logins), использующие созданный в рамках этой статьи начальный проект.</span><span class="sxs-lookup"><span data-stu-id="2bd78-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="2bd78-107">В сторонних пакетах также доступны другие поставщики, такие как [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) и [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="2bd78-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="2bd78-108">Позволяет пользователям входить в систему с имеющимися учетными данными:</span><span class="sxs-lookup"><span data-stu-id="2bd78-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="2bd78-109">Удобно для пользователей.</span><span class="sxs-lookup"><span data-stu-id="2bd78-109">Is convenient for the users.</span></span>
* <span data-ttu-id="2bd78-110">Многие задачи, связанные с управлением процессом входа, передаются сторонним производителям.</span><span class="sxs-lookup"><span data-stu-id="2bd78-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="2bd78-111">Демонстрацию того, как вход с использованием учетных данных социальных сетей помогает повысить трафик и количество конверсий, см. в примерах для [Facebook](https://www.facebook.com/unsupportedbrowser) и [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="2bd78-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="2bd78-112">Создание проекта ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2bd78-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bd78-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bd78-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2bd78-114">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="2bd78-114">Create a new project.</span></span>
* <span data-ttu-id="2bd78-115">Выберите **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2bd78-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="2bd78-116">Укажите **Имя проекта** и подтвердите либо измените **Расположение**.</span><span class="sxs-lookup"><span data-stu-id="2bd78-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="2bd78-117">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2bd78-117">Select **Create**.</span></span>
* <span data-ttu-id="2bd78-118">Выберите последнюю версию ASP.NET Core в раскрывающемся списке (**ASP.NET Core {X.Y}** ), а затем выберите **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="2bd78-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="2bd78-119">В разделе **Проверка подлинности** выберите **Изменить** и в качестве типа проверки подлинности задайте **Индивидуальные учетные записи пользователей**.</span><span class="sxs-lookup"><span data-stu-id="2bd78-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="2bd78-120">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="2bd78-120">Select **OK**.</span></span>
* <span data-ttu-id="2bd78-121">В окне **Создать веб-приложение ASP.NET Core** выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2bd78-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2bd78-122">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2bd78-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2bd78-123">Откройте терминал.</span><span class="sxs-lookup"><span data-stu-id="2bd78-123">Open the terminal.</span></span>  <span data-ttu-id="2bd78-124">Для Visual Studio Code можно открыть [интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2bd78-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="2bd78-125">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="2bd78-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="2bd78-126">Выполните в Windows следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2bd78-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="2bd78-127">Для MacOS и Linux выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2bd78-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="2bd78-128">Команда `dotnet new` создает новый проект Razor Pages в папке *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="2bd78-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="2bd78-129">`-au Individual` создает код для отдельной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2bd78-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="2bd78-130">`-uld` использует LocalDB, упрощенную версию SQL Server Express для Windows.</span><span class="sxs-lookup"><span data-stu-id="2bd78-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="2bd78-131">Не указывайте `-uld`, чтобы использовать SQLite.</span><span class="sxs-lookup"><span data-stu-id="2bd78-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="2bd78-132">Команда `code` открывает папку *WebApp1* в новом экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2bd78-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="2bd78-133">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="2bd78-133">Apply migrations</span></span>

* <span data-ttu-id="2bd78-134">Запустите приложение и щелкните ссылку **Регистрация**.</span><span class="sxs-lookup"><span data-stu-id="2bd78-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="2bd78-135">Введите адрес электронной почты и пароль для новой учетной записи, а затем щелкните **Зарегистрироваться**.</span><span class="sxs-lookup"><span data-stu-id="2bd78-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="2bd78-136">Следуйте инструкциям по применению миграции.</span><span class="sxs-lookup"><span data-stu-id="2bd78-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="2bd78-137">Хранение маркеров безопасности, предоставленных поставщиками входа, с помощью SecretManager</span><span class="sxs-lookup"><span data-stu-id="2bd78-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="2bd78-138">Поставщики входа социальных сетей назначают маркеры **идентификатора приложения** и **секрета приложения** в процессе регистрации.</span><span class="sxs-lookup"><span data-stu-id="2bd78-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="2bd78-139">Точные имена маркеров зависят от поставщика.</span><span class="sxs-lookup"><span data-stu-id="2bd78-139">The exact token names vary by provider.</span></span> <span data-ttu-id="2bd78-140">Эти маркеры соответствуют учетным данным, которые используются приложением для доступа к API.</span><span class="sxs-lookup"><span data-stu-id="2bd78-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="2bd78-141">Маркеры предоставляют "секреты", которые можно подключить к конфигурации приложения с помощью [Secret Manager](xref:security/app-secrets#secret-manager) (Диспетчера секретов).</span><span class="sxs-lookup"><span data-stu-id="2bd78-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="2bd78-142">Диспетчер секретов — более безопасная альтернатива хранению маркеров в файле конфигурации, например в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2bd78-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2bd78-143">Secret Manager предназначен только для разработки.</span><span class="sxs-lookup"><span data-stu-id="2bd78-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="2bd78-144">Для хранения и защиты секретов Azure в ходе тестирования и непосредственной работы используйте [Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="2bd78-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="2bd78-145">В разделе [Безопасное хранение секретов приложений во время разработки в ASP.NET Core](xref:security/app-secrets) описано, как хранить маркеры, назначаемые приведенными ниже поставщиками входа.</span><span class="sxs-lookup"><span data-stu-id="2bd78-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="2bd78-146">Настройка поставщиков входа, используемых приложением</span><span class="sxs-lookup"><span data-stu-id="2bd78-146">Setup login providers required by your application</span></span>

<span data-ttu-id="2bd78-147">В следующих разделах приводятся инструкции по настройке приложения для работы с соответствующими поставщиками:</span><span class="sxs-lookup"><span data-stu-id="2bd78-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="2bd78-148">Инструкции для [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="2bd78-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="2bd78-149">Инструкции для [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="2bd78-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="2bd78-150">Инструкции для [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="2bd78-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="2bd78-151">Инструкции для [Майкрософт](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="2bd78-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="2bd78-152">Инструкции для [других поставщиков](xref:security/authentication/otherlogins)</span><span class="sxs-lookup"><span data-stu-id="2bd78-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="2bd78-153">Необязательная установка пароля</span><span class="sxs-lookup"><span data-stu-id="2bd78-153">Optionally set password</span></span>

<span data-ttu-id="2bd78-154">При регистрации с использованием внешнего поставщика входа у вас нет пароля, зарегистрированного в приложении.</span><span class="sxs-lookup"><span data-stu-id="2bd78-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="2bd78-155">Благодаря этому вам не нужно создавать и запоминать пароль для сайта, однако при этом возникает зависимость от внешнего поставщика входа.</span><span class="sxs-lookup"><span data-stu-id="2bd78-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="2bd78-156">Если внешний поставщик входа недоступен, вы не сможете войти на веб-сайт.</span><span class="sxs-lookup"><span data-stu-id="2bd78-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="2bd78-157">Чтобы создать пароль и войти с использованием адреса электронной почты, который был настроен при входе с использованием внешнего поставщика, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="2bd78-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="2bd78-158">Щелкните ссылку **Здравствуйте, &lt;псевдоним электронной почты&gt;** в правом верхнем углу, чтобы перейти к представлению **Управление**.</span><span class="sxs-lookup"><span data-stu-id="2bd78-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Представление управления веб-приложения](index/_static/pass1a.png)

* <span data-ttu-id="2bd78-160">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2bd78-160">Select **Create**</span></span>

![Настройте страницу пароля](index/_static/pass2a.png)

* <span data-ttu-id="2bd78-162">Введите допустимый пароль, который будет использоваться для входа с применением этого адреса электронной почты.</span><span class="sxs-lookup"><span data-stu-id="2bd78-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2bd78-163">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="2bd78-163">Next steps</span></span>

* <span data-ttu-id="2bd78-164">Дополнительные сведения о настройке кнопок входа см. в описании [этой проблемы](https://github.com/dotnet/AspNetCore.Docs/issues/10563) на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="2bd78-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="2bd78-165">В этой статье описывается внешняя проверка подлинности и приводятся предварительные требования для добавления внешних поставщиков входа в приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2bd78-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="2bd78-166">Инструкции по настройке учетных данных для входа, используемых вашим приложением, см. на соответствующих страницах поставщиков.</span><span class="sxs-lookup"><span data-stu-id="2bd78-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="2bd78-167">Вы можете сохранить дополнительные данные о пользователях и их маркерах доступа и обновления.</span><span class="sxs-lookup"><span data-stu-id="2bd78-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="2bd78-168">Для получения дополнительной информации см. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="2bd78-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
