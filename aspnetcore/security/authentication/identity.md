---
title: Введение в Identity ASP.NET Core
author: rick-anderson
description: Используйте Identity с приложением ASP.NET Core. Узнайте, как устанавливать требования к паролю (Рекуиредигит, Рекуиредленгс, Рекуиредуникуечарс и др.).
ms.author: riande
ms.date: 7/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: dd3296db568700a363c427398f02239846a46ada
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445439"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="f3de0-104">Введение в Identity ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3de0-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3de0-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="f3de0-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f3de0-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="f3de0-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f3de0-107">— Это API, поддерживающий функцию входа в пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="f3de0-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="f3de0-108">Управляет пользователями, паролями, данными профилирования, ролями, утверждениями, маркерами, подтверждением электронной почты и т. д.</span><span class="sxs-lookup"><span data-stu-id="f3de0-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="f3de0-109">Пользователи могут создать учетную запись с данными входа, хранящимися в, Identity или использовать внешний поставщик входа.</span><span class="sxs-lookup"><span data-stu-id="f3de0-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f3de0-110">Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f3de0-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="f3de0-111">[ Identity Исходный код](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) доступен на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="f3de0-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="f3de0-112">[Формирование Identity шаблонов](xref:security/authentication/scaffold-identity) и просмотрите созданные файлы, чтобы проверить взаимодействие с шаблоном Identity .</span><span class="sxs-lookup"><span data-stu-id="f3de0-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="f3de0-113">обычно настраивается с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей.</span><span class="sxs-lookup"><span data-stu-id="f3de0-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f3de0-114">Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.</span><span class="sxs-lookup"><span data-stu-id="f3de0-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f3de0-115">В этом разделе вы узнаете, как использовать Identity для регистрации, входа и выхода пользователя.</span><span class="sxs-lookup"><span data-stu-id="f3de0-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f3de0-116">Примечание. шаблоны считают имя пользователя и адрес электронной почты одинаковыми для пользователей.</span><span class="sxs-lookup"><span data-stu-id="f3de0-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="f3de0-117">Более подробные инструкции по созданию приложений, использующих Identity , см. в разделе [дальнейшие действия](#next).</span><span class="sxs-lookup"><span data-stu-id="f3de0-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="f3de0-118">[Платформа Microsoft Identity Platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="f3de0-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="f3de0-119">Развитие платформы разработки Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="f3de0-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="f3de0-120">Не связано с ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="f3de0-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="f3de0-121">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="f3de0-121">Server4.md)]</span></span>

<span data-ttu-id="f3de0-122">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Загрузка)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f3de0-122">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f3de0-123">Создание веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="f3de0-123">Create a Web app with authentication</span></span>

<span data-ttu-id="f3de0-124">Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.</span><span class="sxs-lookup"><span data-stu-id="f3de0-124">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3de0-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3de0-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3de0-126">Выберите **файл** > **создать** > **проект**.</span><span class="sxs-lookup"><span data-stu-id="f3de0-126">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f3de0-127">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f3de0-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f3de0-128">Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект.</span><span class="sxs-lookup"><span data-stu-id="f3de0-128">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f3de0-129">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="f3de0-129">Click **OK**.</span></span>
* <span data-ttu-id="f3de0-130">Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="f3de0-130">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f3de0-131">Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="f3de0-131">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f3de0-132">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="f3de0-132">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="f3de0-133">Предыдущая команда создает Razor веб-приложение с помощью SQLite.</span><span class="sxs-lookup"><span data-stu-id="f3de0-133">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="f3de0-134">Чтобы создать веб-приложение с помощью LocalDB, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="f3de0-134">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="f3de0-135">Созданный проект предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="f3de0-135">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f3de0-136">Identity Razor Библиотека классов предоставляет конечные точки с `Identity` областью.</span><span class="sxs-lookup"><span data-stu-id="f3de0-136">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f3de0-137">Пример:</span><span class="sxs-lookup"><span data-stu-id="f3de0-137">For example:</span></span>

* <span data-ttu-id="f3de0-138">/Identity/аккаунт/логин</span><span class="sxs-lookup"><span data-stu-id="f3de0-138">/Identity/Account/Login</span></span>
* <span data-ttu-id="f3de0-139">/Identity/аккаунт/логаут</span><span class="sxs-lookup"><span data-stu-id="f3de0-139">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f3de0-140">/Identity/аккаунт/манаже</span><span class="sxs-lookup"><span data-stu-id="f3de0-140">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f3de0-141">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="f3de0-141">Apply migrations</span></span>

<span data-ttu-id="f3de0-142">Примените миграции, чтобы инициализировать базу данных.</span><span class="sxs-lookup"><span data-stu-id="f3de0-142">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3de0-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3de0-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3de0-144">Выполните следующую команду в консоли диспетчера пакетов (PMC):</span><span class="sxs-lookup"><span data-stu-id="f3de0-144">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="f3de0-145">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="f3de0-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f3de0-146">Миграция не требуется на этом этапе при использовании SQLite.</span><span class="sxs-lookup"><span data-stu-id="f3de0-146">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="f3de0-147">Для LocalDB выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="f3de0-147">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f3de0-148">Проверка регистра и вход в систему</span><span class="sxs-lookup"><span data-stu-id="f3de0-148">Test Register and Login</span></span>

<span data-ttu-id="f3de0-149">Запустите приложение и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="f3de0-149">Run the app and register a user.</span></span> <span data-ttu-id="f3de0-150">В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .</span><span class="sxs-lookup"><span data-stu-id="f3de0-150">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="f3de0-151">Настройка Identity служб</span><span class="sxs-lookup"><span data-stu-id="f3de0-151">Configure Identity services</span></span>

<span data-ttu-id="f3de0-152">Службы добавляются в `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f3de0-152">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f3de0-153">По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="f3de0-153">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="f3de0-154">Выделенный выше код настраивается Identity со значениями параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f3de0-154">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="f3de0-155">Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f3de0-155">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="f3de0-156">включается путем вызова метода <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="f3de0-156"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="f3de0-157">`UseAuthentication`добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="f3de0-157">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="f3de0-158">Созданное шаблоном приложение не использует [авторизацию](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="f3de0-158">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="f3de0-159">`app.UseAuthorization`включается, чтобы обеспечить его добавление в правильном порядке, если приложение добавляет авторизацию.</span><span class="sxs-lookup"><span data-stu-id="f3de0-159">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="f3de0-160">`UseRouting`, `UseAuthentication` , `UseAuthorization` и `UseEndpoints` должны вызываться в порядке, показанном в приведенном выше коде.</span><span class="sxs-lookup"><span data-stu-id="f3de0-160">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="f3de0-161">Дополнительные сведения о `IdentityOptions` и см `Startup` . в разделе <xref:Microsoft.AspNetCore.Identity.IdentityOptions> и [запуске приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="f3de0-161">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="f3de0-162">Регистр шаблонов, вход, выход и Регистерконфирматион</span><span class="sxs-lookup"><span data-stu-id="f3de0-162">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3de0-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3de0-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3de0-164">Добавьте `Register` файлы, `Login` , `LogOut` и `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="f3de0-164">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="f3de0-165">Соблюдайте [идентификатор шаблона в Razor проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="f3de0-165">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f3de0-166">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="f3de0-166">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f3de0-167">Если вы создали проект с именем " **APP1**", выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="f3de0-167">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f3de0-168">В противном случае используйте правильное пространство имен для `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="f3de0-168">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="f3de0-169">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="f3de0-169">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f3de0-170">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="f3de0-170">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="f3de0-171">Дополнительные сведения о формировании шаблонов Identity см. в статье [удостоверение шаблона в Razor проекте с авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="f3de0-171">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f3de0-172">Проверка регистра</span><span class="sxs-lookup"><span data-stu-id="f3de0-172">Examine Register</span></span>

<span data-ttu-id="f3de0-173">Когда пользователь нажимает кнопку **Register** на `Register` странице, `RegisterModel.OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="f3de0-173">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f3de0-174">Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:</span><span class="sxs-lookup"><span data-stu-id="f3de0-174">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="f3de0-175">Вход в систему</span><span class="sxs-lookup"><span data-stu-id="f3de0-175">Log in</span></span>

<span data-ttu-id="f3de0-176">Форма входа отображается в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="f3de0-176">The Login form is displayed when:</span></span>

* <span data-ttu-id="f3de0-177">Выбрана ссылка для **входа** .</span><span class="sxs-lookup"><span data-stu-id="f3de0-177">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f3de0-178">Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="f3de0-178">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f3de0-179">При отправке формы на странице входа `OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="f3de0-179">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f3de0-180">`PasswordSignInAsync`метод вызывается для `_signInManager` объекта.</span><span class="sxs-lookup"><span data-stu-id="f3de0-180">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f3de0-181">Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="f3de0-181">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f3de0-182">выход из системы;</span><span class="sxs-lookup"><span data-stu-id="f3de0-182">Log out</span></span>

<span data-ttu-id="f3de0-183">Ссылка **выхода** вызывает `LogoutModel.OnPost` действие.</span><span class="sxs-lookup"><span data-stu-id="f3de0-183">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="f3de0-184">В приведенном выше коде код `return RedirectToPage();` должен быть перенаправлением, чтобы браузер выполнял новый запрос и удостоверение для пользователя обновляется.</span><span class="sxs-lookup"><span data-stu-id="f3de0-184">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="f3de0-185">[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="f3de0-185">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f3de0-186">Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f3de0-186">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="f3de0-187">ПроверьтеIdentity</span><span class="sxs-lookup"><span data-stu-id="f3de0-187">Test Identity</span></span>

<span data-ttu-id="f3de0-188">Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам.</span><span class="sxs-lookup"><span data-stu-id="f3de0-188">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f3de0-189">Чтобы протестировать Identity , добавьте [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="f3de0-189">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f3de0-190">Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** .</span><span class="sxs-lookup"><span data-stu-id="f3de0-190">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f3de0-191">Вы перейдете на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="f3de0-191">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="f3de0-192">ПросматриваемIdentity</span><span class="sxs-lookup"><span data-stu-id="f3de0-192">Explore Identity</span></span>

<span data-ttu-id="f3de0-193">IdentityБолее подробное изучение:</span><span class="sxs-lookup"><span data-stu-id="f3de0-193">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f3de0-194">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="f3de0-194">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f3de0-195">Изучите источник каждой страницы и пошаговое выполнение отладчика.</span><span class="sxs-lookup"><span data-stu-id="f3de0-195">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="f3de0-196">Компонента</span><span class="sxs-lookup"><span data-stu-id="f3de0-196"> Components</span></span>

<span data-ttu-id="f3de0-197">Все Identity зависимые пакеты NuGet включены в [ASP.NET Core общую платформу](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="f3de0-197">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="f3de0-198">Основным пакетом для Identity является [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="f3de0-198">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f3de0-199">Этот пакет содержит основной набор интерфейсов для ASP.NET Core и входит в Identity состав `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="f3de0-199">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="f3de0-200">Переход на ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="f3de0-200">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f3de0-201">Дополнительные сведения и рекомендации по переносу существующего Identity хранилища см. в статье [Миграция Identity проверки подлинности и ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="f3de0-201">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f3de0-202">Установка силы пароля</span><span class="sxs-lookup"><span data-stu-id="f3de0-202">Setting password strength</span></span>

<span data-ttu-id="f3de0-203">См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.</span><span class="sxs-lookup"><span data-stu-id="f3de0-203">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="f3de0-204">Адддефаулт Identity и добавитьIdentity</span><span class="sxs-lookup"><span data-stu-id="f3de0-204">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f3de0-205"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>было введено в ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="f3de0-205"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f3de0-206">Вызов `AddDefaultIdentity` аналогичен вызову следующего:</span><span class="sxs-lookup"><span data-stu-id="f3de0-206">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f3de0-207">Дополнительные сведения см. в разделе [адддефаулт Identity Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="f3de0-207">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="f3de0-208">Запретить публикацию статических Identity ресурсов</span><span class="sxs-lookup"><span data-stu-id="f3de0-208">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="f3de0-209">Чтобы предотвратить публикацию статических Identity ресурсов (таблиц стилей и файлов JavaScript для Identity пользовательского интерфейса) в корневом каталоге веб-сайта, добавьте следующее `ResolveStaticWebAssetsInputsDependsOn` свойство и `RemoveIdentityAssets` целевой объект в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="f3de0-209">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="f3de0-210">Next Steps</span><span class="sxs-lookup"><span data-stu-id="f3de0-210">Next Steps</span></span>

* <span data-ttu-id="f3de0-211">[IdentityИсходный код ASP.NET Core](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="f3de0-211">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="f3de0-212">Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="f3de0-212">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="f3de0-213">[Настройка Identity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="f3de0-213">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f3de0-214">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="f3de0-214">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f3de0-215">ASP.NET Core Identity — это система членства, которая добавляет функции входа в ASP.NET Core приложения.</span><span class="sxs-lookup"><span data-stu-id="f3de0-215">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="f3de0-216">Пользователи могут создать учетную запись с данными входа, хранящимися в, Identity или использовать внешний поставщик входа.</span><span class="sxs-lookup"><span data-stu-id="f3de0-216">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f3de0-217">Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f3de0-217">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="f3de0-218">можно настроить с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей.</span><span class="sxs-lookup"><span data-stu-id="f3de0-218"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f3de0-219">Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.</span><span class="sxs-lookup"><span data-stu-id="f3de0-219">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f3de0-220">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Загрузка)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f3de0-220">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f3de0-221">В этом разделе вы узнаете, как использовать Identity для регистрации, входа и выхода пользователя.</span><span class="sxs-lookup"><span data-stu-id="f3de0-221">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f3de0-222">Более подробные инструкции по созданию приложений, использующих Identity , см. в разделе дальнейшие действия в конце этой статьи.</span><span class="sxs-lookup"><span data-stu-id="f3de0-222">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="f3de0-223">Адддефаулт Identity и добавитьIdentity</span><span class="sxs-lookup"><span data-stu-id="f3de0-223">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f3de0-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>было введено в ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="f3de0-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f3de0-225">Вызов `AddDefaultIdentity` аналогичен вызову следующего:</span><span class="sxs-lookup"><span data-stu-id="f3de0-225">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f3de0-226">Дополнительные сведения см. в разделе [адддефаулт Identity Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="f3de0-226">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f3de0-227">Создание веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="f3de0-227">Create a Web app with authentication</span></span>

<span data-ttu-id="f3de0-228">Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.</span><span class="sxs-lookup"><span data-stu-id="f3de0-228">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3de0-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3de0-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3de0-230">Выберите **файл** > **создать** > **проект**.</span><span class="sxs-lookup"><span data-stu-id="f3de0-230">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f3de0-231">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f3de0-231">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f3de0-232">Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект.</span><span class="sxs-lookup"><span data-stu-id="f3de0-232">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f3de0-233">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="f3de0-233">Click **OK**.</span></span>
* <span data-ttu-id="f3de0-234">Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="f3de0-234">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f3de0-235">Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="f3de0-235">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f3de0-236">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="f3de0-236">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="f3de0-237">Созданный проект предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="f3de0-237">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f3de0-238">Identity Razor Библиотека классов предоставляет конечные точки с `Identity` областью.</span><span class="sxs-lookup"><span data-stu-id="f3de0-238">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f3de0-239">Пример:</span><span class="sxs-lookup"><span data-stu-id="f3de0-239">For example:</span></span>

* <span data-ttu-id="f3de0-240">/Identity/аккаунт/логин</span><span class="sxs-lookup"><span data-stu-id="f3de0-240">/Identity/Account/Login</span></span>
* <span data-ttu-id="f3de0-241">/Identity/аккаунт/логаут</span><span class="sxs-lookup"><span data-stu-id="f3de0-241">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f3de0-242">/Identity/аккаунт/манаже</span><span class="sxs-lookup"><span data-stu-id="f3de0-242">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f3de0-243">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="f3de0-243">Apply migrations</span></span>

<span data-ttu-id="f3de0-244">Примените миграции, чтобы инициализировать базу данных.</span><span class="sxs-lookup"><span data-stu-id="f3de0-244">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3de0-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3de0-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3de0-246">Выполните следующую команду в консоли диспетчера пакетов (PMC):</span><span class="sxs-lookup"><span data-stu-id="f3de0-246">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="f3de0-247">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="f3de0-247">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f3de0-248">Проверка регистра и вход в систему</span><span class="sxs-lookup"><span data-stu-id="f3de0-248">Test Register and Login</span></span>

<span data-ttu-id="f3de0-249">Запустите приложение и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="f3de0-249">Run the app and register a user.</span></span> <span data-ttu-id="f3de0-250">В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .</span><span class="sxs-lookup"><span data-stu-id="f3de0-250">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="f3de0-251">Настройка Identity служб</span><span class="sxs-lookup"><span data-stu-id="f3de0-251">Configure Identity services</span></span>

<span data-ttu-id="f3de0-252">Службы добавляются в `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f3de0-252">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f3de0-253">По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="f3de0-253">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="f3de0-254">Приведенный выше код настраивается Identity со значениями параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f3de0-254">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="f3de0-255">Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f3de0-255">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="f3de0-256">включается путем вызова [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="f3de0-256"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="f3de0-257">`UseAuthentication`добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="f3de0-257">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="f3de0-258">Дополнительные сведения см. в статье [ Identity класс Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) и [Запуск приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="f3de0-258">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="f3de0-259">Регистрация, вход и выход из шаблона формирования шаблонов</span><span class="sxs-lookup"><span data-stu-id="f3de0-259">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="f3de0-260">Соблюдайте [идентификатор шаблона в Razor проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="f3de0-260">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3de0-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3de0-261">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3de0-262">Добавьте файлы Register, login и LogOut.</span><span class="sxs-lookup"><span data-stu-id="f3de0-262">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f3de0-263">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="f3de0-263">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f3de0-264">Если вы создали проект с именем " **APP1**", выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="f3de0-264">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f3de0-265">В противном случае используйте правильное пространство имен для `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="f3de0-265">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="f3de0-266">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="f3de0-266">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f3de0-267">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="f3de0-267">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f3de0-268">Проверка регистра</span><span class="sxs-lookup"><span data-stu-id="f3de0-268">Examine Register</span></span>

<span data-ttu-id="f3de0-269">Когда пользователь щелкает ссылку **Register** , `RegisterModel.OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="f3de0-269">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f3de0-270">Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:</span><span class="sxs-lookup"><span data-stu-id="f3de0-270">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="f3de0-271">Если пользователь успешно создан, пользователь входит в систему с помощью вызова `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="f3de0-271">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="f3de0-272">**Примечание.** Инструкции по предотвращению немедленного входа при регистрации см. в статье [Подтверждение учетной записи](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="f3de0-272">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="f3de0-273">Вход в систему</span><span class="sxs-lookup"><span data-stu-id="f3de0-273">Log in</span></span>

<span data-ttu-id="f3de0-274">Форма входа отображается в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="f3de0-274">The Login form is displayed when:</span></span>

* <span data-ttu-id="f3de0-275">Выбрана ссылка для **входа** .</span><span class="sxs-lookup"><span data-stu-id="f3de0-275">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f3de0-276">Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="f3de0-276">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f3de0-277">При отправке формы на странице входа `OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="f3de0-277">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f3de0-278">`PasswordSignInAsync`метод вызывается для `_signInManager` объекта.</span><span class="sxs-lookup"><span data-stu-id="f3de0-278">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f3de0-279">Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="f3de0-279">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f3de0-280">выход из системы;</span><span class="sxs-lookup"><span data-stu-id="f3de0-280">Log out</span></span>

<span data-ttu-id="f3de0-281">Ссылка **выхода** вызывает `LogoutModel.OnPost` действие.</span><span class="sxs-lookup"><span data-stu-id="f3de0-281">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="f3de0-282">[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="f3de0-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f3de0-283">Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f3de0-283">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="f3de0-284">ПроверьтеIdentity</span><span class="sxs-lookup"><span data-stu-id="f3de0-284">Test Identity</span></span>

<span data-ttu-id="f3de0-285">Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам.</span><span class="sxs-lookup"><span data-stu-id="f3de0-285">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f3de0-286">Чтобы протестировать Identity , добавьте [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) на страницу конфиденциальность.</span><span class="sxs-lookup"><span data-stu-id="f3de0-286">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f3de0-287">Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** .</span><span class="sxs-lookup"><span data-stu-id="f3de0-287">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f3de0-288">Вы перейдете на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="f3de0-288">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="f3de0-289">ПросматриваемIdentity</span><span class="sxs-lookup"><span data-stu-id="f3de0-289">Explore Identity</span></span>

<span data-ttu-id="f3de0-290">IdentityБолее подробное изучение:</span><span class="sxs-lookup"><span data-stu-id="f3de0-290">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f3de0-291">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="f3de0-291">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f3de0-292">Изучите источник каждой страницы и пошаговое выполнение отладчика.</span><span class="sxs-lookup"><span data-stu-id="f3de0-292">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="f3de0-293">Компонента</span><span class="sxs-lookup"><span data-stu-id="f3de0-293"> Components</span></span>

<span data-ttu-id="f3de0-294">Все Identity зависимые пакеты NuGet включены в пакет [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f3de0-294">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f3de0-295">Основным пакетом для Identity является [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="f3de0-295">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f3de0-296">Этот пакет содержит основной набор интерфейсов для ASP.NET Core и входит в Identity состав `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="f3de0-296">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="f3de0-297">Переход на ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="f3de0-297">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f3de0-298">Дополнительные сведения и рекомендации по переносу существующего Identity хранилища см. в статье [Миграция Identity проверки подлинности и ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="f3de0-298">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f3de0-299">Установка силы пароля</span><span class="sxs-lookup"><span data-stu-id="f3de0-299">Setting password strength</span></span>

<span data-ttu-id="f3de0-300">См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.</span><span class="sxs-lookup"><span data-stu-id="f3de0-300">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f3de0-301">Next Steps</span><span class="sxs-lookup"><span data-stu-id="f3de0-301">Next Steps</span></span>

* <span data-ttu-id="f3de0-302">Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="f3de0-302">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="f3de0-303">[Настройка Identity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="f3de0-303">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
