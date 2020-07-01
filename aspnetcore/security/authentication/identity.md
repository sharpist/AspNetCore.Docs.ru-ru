---
title: Введение в Identity ASP.NET Core
author: rick-anderson
description: Используйте Identity с приложением ASP.NET Core. Узнайте, как устанавливать требования к паролю (Рекуиредигит, Рекуиредленгс, Рекуиредуникуечарс и др.).
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 31970bd2b52ad83c116067d258aa9dca2d9b3b66
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793570"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="deaa8-104">Введение в Identity ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="deaa8-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="deaa8-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="deaa8-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="deaa8-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="deaa8-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="deaa8-107">— Это API, поддерживающий функцию входа в пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="deaa8-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="deaa8-108">Управляет пользователями, паролями, данными профилирования, ролями, утверждениями, маркерами, подтверждением электронной почты и т. д.</span><span class="sxs-lookup"><span data-stu-id="deaa8-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="deaa8-109">Пользователи могут создать учетную запись с данными входа, хранящимися в, Identity или использовать внешний поставщик входа.</span><span class="sxs-lookup"><span data-stu-id="deaa8-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="deaa8-110">Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="deaa8-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="deaa8-111">[ Identity Исходный код](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) доступен на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="deaa8-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="deaa8-112">[Формирование Identity шаблонов](xref:security/authentication/scaffold-identity) и просмотрите созданные файлы, чтобы проверить взаимодействие с шаблоном Identity .</span><span class="sxs-lookup"><span data-stu-id="deaa8-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="deaa8-113">обычно настраивается с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей.</span><span class="sxs-lookup"><span data-stu-id="deaa8-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="deaa8-114">Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.</span><span class="sxs-lookup"><span data-stu-id="deaa8-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="deaa8-115">В этом разделе вы узнаете, как использовать Identity для регистрации, входа и выхода пользователя.</span><span class="sxs-lookup"><span data-stu-id="deaa8-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="deaa8-116">Примечание. шаблоны считают имя пользователя и адрес электронной почты одинаковыми для пользователей.</span><span class="sxs-lookup"><span data-stu-id="deaa8-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="deaa8-117">Более подробные инструкции по созданию приложений, использующих Identity , см. в разделе дальнейшие действия в конце этой статьи.</span><span class="sxs-lookup"><span data-stu-id="deaa8-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="deaa8-118">[Платформа Microsoft Identity Platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="deaa8-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="deaa8-119">Развитие платформы разработки Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="deaa8-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="deaa8-120">Не связано с ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="deaa8-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="deaa8-121">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Загрузка)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="deaa8-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="deaa8-122">Создание веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="deaa8-122">Create a Web app with authentication</span></span>

<span data-ttu-id="deaa8-123">Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.</span><span class="sxs-lookup"><span data-stu-id="deaa8-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="deaa8-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="deaa8-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="deaa8-125">Выберите **файл** > **создать** > **проект**.</span><span class="sxs-lookup"><span data-stu-id="deaa8-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="deaa8-126">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="deaa8-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="deaa8-127">Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект.</span><span class="sxs-lookup"><span data-stu-id="deaa8-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="deaa8-128">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="deaa8-128">Click **OK**.</span></span>
* <span data-ttu-id="deaa8-129">Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="deaa8-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="deaa8-130">Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="deaa8-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="deaa8-131">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="deaa8-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="deaa8-132">Предыдущая команда создает Razor веб-приложение с помощью SQLite.</span><span class="sxs-lookup"><span data-stu-id="deaa8-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="deaa8-133">Чтобы создать веб-приложение с помощью LocalDB, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="deaa8-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="deaa8-134">Созданный проект предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="deaa8-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="deaa8-135">Identity Razor Библиотека классов предоставляет конечные точки с `Identity` областью.</span><span class="sxs-lookup"><span data-stu-id="deaa8-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="deaa8-136">Пример:</span><span class="sxs-lookup"><span data-stu-id="deaa8-136">For example:</span></span>

* <span data-ttu-id="deaa8-137">/Identity/аккаунт/логин</span><span class="sxs-lookup"><span data-stu-id="deaa8-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="deaa8-138">/Identity/аккаунт/логаут</span><span class="sxs-lookup"><span data-stu-id="deaa8-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="deaa8-139">/Identity/аккаунт/манаже</span><span class="sxs-lookup"><span data-stu-id="deaa8-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="deaa8-140">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="deaa8-140">Apply migrations</span></span>

<span data-ttu-id="deaa8-141">Примените миграции, чтобы инициализировать базу данных.</span><span class="sxs-lookup"><span data-stu-id="deaa8-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="deaa8-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="deaa8-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="deaa8-143">Выполните следующую команду в консоли диспетчера пакетов (PMC):</span><span class="sxs-lookup"><span data-stu-id="deaa8-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="deaa8-144">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="deaa8-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="deaa8-145">Миграция не требуется на этом этапе при использовании SQLite.</span><span class="sxs-lookup"><span data-stu-id="deaa8-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="deaa8-146">Для LocalDB выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="deaa8-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="deaa8-147">Проверка регистра и вход в систему</span><span class="sxs-lookup"><span data-stu-id="deaa8-147">Test Register and Login</span></span>

<span data-ttu-id="deaa8-148">Запустите приложение и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="deaa8-148">Run the app and register a user.</span></span> <span data-ttu-id="deaa8-149">В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .</span><span class="sxs-lookup"><span data-stu-id="deaa8-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="deaa8-150">Настройка Identity служб</span><span class="sxs-lookup"><span data-stu-id="deaa8-150">Configure Identity services</span></span>

<span data-ttu-id="deaa8-151">Службы добавляются в `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="deaa8-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="deaa8-152">По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="deaa8-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="deaa8-153">Выделенный выше код настраивается Identity со значениями параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="deaa8-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="deaa8-154">Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="deaa8-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="deaa8-155">включается путем вызова метода <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="deaa8-155"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="deaa8-156">`UseAuthentication`добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="deaa8-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="deaa8-157">Созданное шаблоном приложение не использует [авторизацию](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="deaa8-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="deaa8-158">`app.UseAuthorization`включается, чтобы обеспечить его добавление в правильном порядке, если приложение добавляет авторизацию.</span><span class="sxs-lookup"><span data-stu-id="deaa8-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="deaa8-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` и `UseEndpoints` должны вызываться в порядке, показанном в приведенном выше коде.</span><span class="sxs-lookup"><span data-stu-id="deaa8-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="deaa8-160">Дополнительные сведения о `IdentityOptions` и см `Startup` . в разделе <xref:Microsoft.AspNetCore.Identity.IdentityOptions> и [запуске приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="deaa8-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="deaa8-161">Регистрация, вход и выход из шаблона формирования шаблонов</span><span class="sxs-lookup"><span data-stu-id="deaa8-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="deaa8-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="deaa8-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="deaa8-163">Добавьте файлы Register, login и LogOut.</span><span class="sxs-lookup"><span data-stu-id="deaa8-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="deaa8-164">Соблюдайте [идентификатор шаблона в Razor проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="deaa8-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="deaa8-165">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="deaa8-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="deaa8-166">Если вы создали проект с именем " **APP1**", выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="deaa8-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="deaa8-167">В противном случае используйте правильное пространство имен для `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="deaa8-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="deaa8-168">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="deaa8-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="deaa8-169">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="deaa8-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="deaa8-170">Дополнительные сведения о формировании шаблонов Identity см. в статье [удостоверение шаблона в Razor проекте с авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="deaa8-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="deaa8-171">Проверка регистра</span><span class="sxs-lookup"><span data-stu-id="deaa8-171">Examine Register</span></span>

<span data-ttu-id="deaa8-172">Когда пользователь щелкает ссылку **Register** , `RegisterModel.OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="deaa8-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="deaa8-173">Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:</span><span class="sxs-lookup"><span data-stu-id="deaa8-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="deaa8-174">Если пользователь успешно создан, пользователь входит в систему с помощью вызова `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="deaa8-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="deaa8-175">Инструкции по предотвращению немедленного входа при регистрации см. в статье [Подтверждение учетной записи](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="deaa8-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="deaa8-176">Вход в систему</span><span class="sxs-lookup"><span data-stu-id="deaa8-176">Log in</span></span>

<span data-ttu-id="deaa8-177">Форма входа отображается в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="deaa8-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="deaa8-178">Выбрана ссылка для **входа** .</span><span class="sxs-lookup"><span data-stu-id="deaa8-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="deaa8-179">Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="deaa8-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="deaa8-180">При отправке формы на странице входа `OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="deaa8-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="deaa8-181">`PasswordSignInAsync`метод вызывается для `_signInManager` объекта.</span><span class="sxs-lookup"><span data-stu-id="deaa8-181">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="deaa8-182">Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="deaa8-182">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="deaa8-183">выход из системы;</span><span class="sxs-lookup"><span data-stu-id="deaa8-183">Log out</span></span>

<span data-ttu-id="deaa8-184">Ссылка **выхода** вызывает `LogoutModel.OnPost` действие.</span><span class="sxs-lookup"><span data-stu-id="deaa8-184">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="deaa8-185">В приведенном выше коде код `return RedirectToPage();` должен быть перенаправлением, чтобы браузер выполнял новый запрос и удостоверение для пользователя обновляется.</span><span class="sxs-lookup"><span data-stu-id="deaa8-185">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="deaa8-186">[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="deaa8-186">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="deaa8-187">Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="deaa8-187">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="deaa8-188">ПроверьтеIdentity</span><span class="sxs-lookup"><span data-stu-id="deaa8-188">Test Identity</span></span>

<span data-ttu-id="deaa8-189">Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам.</span><span class="sxs-lookup"><span data-stu-id="deaa8-189">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="deaa8-190">Чтобы протестировать Identity , добавьте [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="deaa8-190">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="deaa8-191">Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** .</span><span class="sxs-lookup"><span data-stu-id="deaa8-191">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="deaa8-192">Вы перейдете на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="deaa8-192">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="deaa8-193">ПросматриваемIdentity</span><span class="sxs-lookup"><span data-stu-id="deaa8-193">Explore Identity</span></span>

<span data-ttu-id="deaa8-194">IdentityБолее подробное изучение:</span><span class="sxs-lookup"><span data-stu-id="deaa8-194">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="deaa8-195">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="deaa8-195">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="deaa8-196">Изучите источник каждой страницы и пошаговое выполнение отладчика.</span><span class="sxs-lookup"><span data-stu-id="deaa8-196">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="deaa8-197">Компонента</span><span class="sxs-lookup"><span data-stu-id="deaa8-197"> Components</span></span>

<span data-ttu-id="deaa8-198">Все Identity зависимые пакеты NuGet включены в [ASP.NET Core общую платформу](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="deaa8-198">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="deaa8-199">Основным пакетом для Identity является [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="deaa8-199">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="deaa8-200">Этот пакет содержит основной набор интерфейсов для ASP.NET Core и входит в Identity состав `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="deaa8-200">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="deaa8-201">Переход на ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="deaa8-201">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="deaa8-202">Дополнительные сведения и рекомендации по переносу существующего Identity хранилища см. в статье [Миграция Identity проверки подлинности и ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="deaa8-202">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="deaa8-203">Установка силы пароля</span><span class="sxs-lookup"><span data-stu-id="deaa8-203">Setting password strength</span></span>

<span data-ttu-id="deaa8-204">См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.</span><span class="sxs-lookup"><span data-stu-id="deaa8-204">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="deaa8-205">Адддефаултидентити и Аддидентити</span><span class="sxs-lookup"><span data-stu-id="deaa8-205">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="deaa8-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>было введено в ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="deaa8-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="deaa8-207">Вызов `AddDefaultIdentity` аналогичен вызову следующего:</span><span class="sxs-lookup"><span data-stu-id="deaa8-207">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="deaa8-208">Дополнительные сведения см. в разделе [адддефаултидентити Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="deaa8-208">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="deaa8-209">Запретить публикацию статических Identity ресурсов</span><span class="sxs-lookup"><span data-stu-id="deaa8-209">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="deaa8-210">Чтобы предотвратить публикацию статических Identity ресурсов (таблиц стилей и файлов JavaScript для Identity пользовательского интерфейса) в корневом каталоге веб-сайта, добавьте следующее `ResolveStaticWebAssetsInputsDependsOn` свойство и `RemoveIdentityAssets` целевой объект в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="deaa8-210">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a><span data-ttu-id="deaa8-211">Next Steps</span><span class="sxs-lookup"><span data-stu-id="deaa8-211">Next Steps</span></span>

* <span data-ttu-id="deaa8-212">[IdentityИсходный код ASP.NET Core](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="deaa8-212">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="deaa8-213">Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="deaa8-213">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="deaa8-214">[Настройка Identity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="deaa8-214">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="deaa8-215">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="deaa8-215">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="deaa8-216">ASP.NET Core Identity — это система членства, которая добавляет функции входа в ASP.NET Core приложения.</span><span class="sxs-lookup"><span data-stu-id="deaa8-216">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="deaa8-217">Пользователи могут создать учетную запись с данными входа, хранящимися в, Identity или использовать внешний поставщик входа.</span><span class="sxs-lookup"><span data-stu-id="deaa8-217">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="deaa8-218">Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="deaa8-218">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="deaa8-219">можно настроить с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей.</span><span class="sxs-lookup"><span data-stu-id="deaa8-219"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="deaa8-220">Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.</span><span class="sxs-lookup"><span data-stu-id="deaa8-220">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="deaa8-221">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Загрузка)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="deaa8-221">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="deaa8-222">В этом разделе вы узнаете, как использовать Identity для регистрации, входа и выхода пользователя.</span><span class="sxs-lookup"><span data-stu-id="deaa8-222">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="deaa8-223">Более подробные инструкции по созданию приложений, использующих Identity , см. в разделе дальнейшие действия в конце этой статьи.</span><span class="sxs-lookup"><span data-stu-id="deaa8-223">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="deaa8-224">Адддефаултидентити и Аддидентити</span><span class="sxs-lookup"><span data-stu-id="deaa8-224">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="deaa8-225"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>было введено в ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="deaa8-225"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="deaa8-226">Вызов `AddDefaultIdentity` аналогичен вызову следующего:</span><span class="sxs-lookup"><span data-stu-id="deaa8-226">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="deaa8-227">Дополнительные сведения см. в разделе [адддефаултидентити Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="deaa8-227">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="deaa8-228">Создание веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="deaa8-228">Create a Web app with authentication</span></span>

<span data-ttu-id="deaa8-229">Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.</span><span class="sxs-lookup"><span data-stu-id="deaa8-229">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="deaa8-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="deaa8-230">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="deaa8-231">Выберите **файл** > **создать** > **проект**.</span><span class="sxs-lookup"><span data-stu-id="deaa8-231">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="deaa8-232">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="deaa8-232">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="deaa8-233">Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект.</span><span class="sxs-lookup"><span data-stu-id="deaa8-233">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="deaa8-234">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="deaa8-234">Click **OK**.</span></span>
* <span data-ttu-id="deaa8-235">Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="deaa8-235">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="deaa8-236">Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="deaa8-236">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="deaa8-237">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="deaa8-237">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="deaa8-238">Созданный проект предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="deaa8-238">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="deaa8-239">Identity Razor Библиотека классов предоставляет конечные точки с `Identity` областью.</span><span class="sxs-lookup"><span data-stu-id="deaa8-239">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="deaa8-240">Пример:</span><span class="sxs-lookup"><span data-stu-id="deaa8-240">For example:</span></span>

* <span data-ttu-id="deaa8-241">/Identity/аккаунт/логин</span><span class="sxs-lookup"><span data-stu-id="deaa8-241">/Identity/Account/Login</span></span>
* <span data-ttu-id="deaa8-242">/Identity/аккаунт/логаут</span><span class="sxs-lookup"><span data-stu-id="deaa8-242">/Identity/Account/Logout</span></span>
* <span data-ttu-id="deaa8-243">/Identity/аккаунт/манаже</span><span class="sxs-lookup"><span data-stu-id="deaa8-243">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="deaa8-244">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="deaa8-244">Apply migrations</span></span>

<span data-ttu-id="deaa8-245">Примените миграции, чтобы инициализировать базу данных.</span><span class="sxs-lookup"><span data-stu-id="deaa8-245">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="deaa8-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="deaa8-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="deaa8-247">Выполните следующую команду в консоли диспетчера пакетов (PMC):</span><span class="sxs-lookup"><span data-stu-id="deaa8-247">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="deaa8-248">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="deaa8-248">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="deaa8-249">Проверка регистра и вход в систему</span><span class="sxs-lookup"><span data-stu-id="deaa8-249">Test Register and Login</span></span>

<span data-ttu-id="deaa8-250">Запустите приложение и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="deaa8-250">Run the app and register a user.</span></span> <span data-ttu-id="deaa8-251">В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .</span><span class="sxs-lookup"><span data-stu-id="deaa8-251">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="deaa8-252">Настройка Identity служб</span><span class="sxs-lookup"><span data-stu-id="deaa8-252">Configure Identity services</span></span>

<span data-ttu-id="deaa8-253">Службы добавляются в `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="deaa8-253">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="deaa8-254">По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="deaa8-254">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="deaa8-255">Приведенный выше код настраивается Identity со значениями параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="deaa8-255">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="deaa8-256">Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="deaa8-256">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="deaa8-257">включается путем вызова [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="deaa8-257"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="deaa8-258">`UseAuthentication`добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="deaa8-258">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="deaa8-259">Дополнительные сведения см. в разделе [класс идентитйоптионс](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) и [Запуск приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="deaa8-259">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="deaa8-260">Регистрация, вход и выход из шаблона формирования шаблонов</span><span class="sxs-lookup"><span data-stu-id="deaa8-260">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="deaa8-261">Соблюдайте [идентификатор шаблона в Razor проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="deaa8-261">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="deaa8-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="deaa8-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="deaa8-263">Добавьте файлы Register, login и LogOut.</span><span class="sxs-lookup"><span data-stu-id="deaa8-263">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="deaa8-264">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="deaa8-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="deaa8-265">Если вы создали проект с именем " **APP1**", выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="deaa8-265">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="deaa8-266">В противном случае используйте правильное пространство имен для `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="deaa8-266">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="deaa8-267">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="deaa8-267">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="deaa8-268">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="deaa8-268">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="deaa8-269">Проверка регистра</span><span class="sxs-lookup"><span data-stu-id="deaa8-269">Examine Register</span></span>

<span data-ttu-id="deaa8-270">Когда пользователь щелкает ссылку **Register** , `RegisterModel.OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="deaa8-270">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="deaa8-271">Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:</span><span class="sxs-lookup"><span data-stu-id="deaa8-271">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="deaa8-272">Если пользователь успешно создан, пользователь входит в систему с помощью вызова `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="deaa8-272">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="deaa8-273">**Примечание.** Инструкции по предотвращению немедленного входа при регистрации см. в статье [Подтверждение учетной записи](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="deaa8-273">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="deaa8-274">Вход в систему</span><span class="sxs-lookup"><span data-stu-id="deaa8-274">Log in</span></span>

<span data-ttu-id="deaa8-275">Форма входа отображается в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="deaa8-275">The Login form is displayed when:</span></span>

* <span data-ttu-id="deaa8-276">Выбрана ссылка для **входа** .</span><span class="sxs-lookup"><span data-stu-id="deaa8-276">The **Log in** link is selected.</span></span>
* <span data-ttu-id="deaa8-277">Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="deaa8-277">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="deaa8-278">При отправке формы на странице входа `OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="deaa8-278">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="deaa8-279">`PasswordSignInAsync`метод вызывается для `_signInManager` объекта.</span><span class="sxs-lookup"><span data-stu-id="deaa8-279">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="deaa8-280">Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="deaa8-280">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="deaa8-281">выход из системы;</span><span class="sxs-lookup"><span data-stu-id="deaa8-281">Log out</span></span>

<span data-ttu-id="deaa8-282">Ссылка **выхода** вызывает `LogoutModel.OnPost` действие.</span><span class="sxs-lookup"><span data-stu-id="deaa8-282">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="deaa8-283">[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="deaa8-283">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="deaa8-284">Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="deaa8-284">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="deaa8-285">ПроверьтеIdentity</span><span class="sxs-lookup"><span data-stu-id="deaa8-285">Test Identity</span></span>

<span data-ttu-id="deaa8-286">Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам.</span><span class="sxs-lookup"><span data-stu-id="deaa8-286">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="deaa8-287">Чтобы протестировать Identity , добавьте [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) на страницу конфиденциальность.</span><span class="sxs-lookup"><span data-stu-id="deaa8-287">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="deaa8-288">Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** .</span><span class="sxs-lookup"><span data-stu-id="deaa8-288">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="deaa8-289">Вы перейдете на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="deaa8-289">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="deaa8-290">ПросматриваемIdentity</span><span class="sxs-lookup"><span data-stu-id="deaa8-290">Explore Identity</span></span>

<span data-ttu-id="deaa8-291">IdentityБолее подробное изучение:</span><span class="sxs-lookup"><span data-stu-id="deaa8-291">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="deaa8-292">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="deaa8-292">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="deaa8-293">Изучите источник каждой страницы и пошаговое выполнение отладчика.</span><span class="sxs-lookup"><span data-stu-id="deaa8-293">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="deaa8-294">Компонента</span><span class="sxs-lookup"><span data-stu-id="deaa8-294"> Components</span></span>

<span data-ttu-id="deaa8-295">Все Identity зависимые пакеты NuGet включены в пакет [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="deaa8-295">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="deaa8-296">Основным пакетом для Identity является [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="deaa8-296">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="deaa8-297">Этот пакет содержит основной набор интерфейсов для ASP.NET Core и входит в Identity состав `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="deaa8-297">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="deaa8-298">Переход на ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="deaa8-298">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="deaa8-299">Дополнительные сведения и рекомендации по переносу существующего Identity хранилища см. в статье [Миграция Identity проверки подлинности и ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="deaa8-299">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="deaa8-300">Установка силы пароля</span><span class="sxs-lookup"><span data-stu-id="deaa8-300">Setting password strength</span></span>

<span data-ttu-id="deaa8-301">См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.</span><span class="sxs-lookup"><span data-stu-id="deaa8-301">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="deaa8-302">Next Steps</span><span class="sxs-lookup"><span data-stu-id="deaa8-302">Next Steps</span></span>

* <span data-ttu-id="deaa8-303">Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="deaa8-303">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="deaa8-304">[Настройка Identity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="deaa8-304">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
