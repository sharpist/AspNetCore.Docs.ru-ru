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
ms.openlocfilehash: 25070e90050db9dca8b003ae782662811096526a
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160304"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="73637-104">Введение в Identity ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73637-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73637-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="73637-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="73637-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="73637-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="73637-107">— Это API, поддерживающий функцию входа в пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="73637-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="73637-108">Управляет пользователями, паролями, данными профилирования, ролями, утверждениями, маркерами, подтверждением электронной почты и т. д.</span><span class="sxs-lookup"><span data-stu-id="73637-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="73637-109">Пользователи могут создать учетную запись с данными входа, хранящимися в, Identity или использовать внешний поставщик входа.</span><span class="sxs-lookup"><span data-stu-id="73637-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="73637-110">Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="73637-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="73637-111">[ Identity Исходный код](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) доступен на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="73637-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="73637-112">[Формирование Identity шаблонов](xref:security/authentication/scaffold-identity) и просмотрите созданные файлы, чтобы проверить взаимодействие с шаблоном Identity .</span><span class="sxs-lookup"><span data-stu-id="73637-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="73637-113">Identityобычно настраивается с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей.</span><span class="sxs-lookup"><span data-stu-id="73637-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="73637-114">Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.</span><span class="sxs-lookup"><span data-stu-id="73637-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="73637-115">В этом разделе вы узнаете, как использовать Identity для регистрации, входа и выхода пользователя.</span><span class="sxs-lookup"><span data-stu-id="73637-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="73637-116">Примечание. шаблоны считают имя пользователя и адрес электронной почты одинаковыми для пользователей.</span><span class="sxs-lookup"><span data-stu-id="73637-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="73637-117">Более подробные инструкции по созданию приложений, использующих Identity , см. в разделе [дальнейшие действия](#next).</span><span class="sxs-lookup"><span data-stu-id="73637-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="73637-118">[Платформа Microsoft Identity Platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="73637-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="73637-119">Развитие платформы разработки Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="73637-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="73637-120">Не связано с ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="73637-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="73637-121">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Загрузка)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="73637-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="73637-122">Создание веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="73637-122">Create a Web app with authentication</span></span>

<span data-ttu-id="73637-123">Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.</span><span class="sxs-lookup"><span data-stu-id="73637-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73637-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73637-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="73637-125">Выберите **файл** > **создать** > **проект**.</span><span class="sxs-lookup"><span data-stu-id="73637-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="73637-126">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="73637-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="73637-127">Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект.</span><span class="sxs-lookup"><span data-stu-id="73637-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="73637-128">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="73637-128">Click **OK**.</span></span>
* <span data-ttu-id="73637-129">Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="73637-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="73637-130">Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="73637-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="73637-131">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="73637-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="73637-132">Предыдущая команда создает Razor веб-приложение с помощью SQLite.</span><span class="sxs-lookup"><span data-stu-id="73637-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="73637-133">Чтобы создать веб-приложение с помощью LocalDB, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="73637-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="73637-134">Созданный проект предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="73637-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="73637-135">Identity Razor Библиотека классов предоставляет конечные точки с `Identity` областью.</span><span class="sxs-lookup"><span data-stu-id="73637-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="73637-136">Пример:</span><span class="sxs-lookup"><span data-stu-id="73637-136">For example:</span></span>

* <span data-ttu-id="73637-137">/Identity/аккаунт/логин</span><span class="sxs-lookup"><span data-stu-id="73637-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="73637-138">/Identity/аккаунт/логаут</span><span class="sxs-lookup"><span data-stu-id="73637-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="73637-139">/Identity/аккаунт/манаже</span><span class="sxs-lookup"><span data-stu-id="73637-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="73637-140">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="73637-140">Apply migrations</span></span>

<span data-ttu-id="73637-141">Примените миграции, чтобы инициализировать базу данных.</span><span class="sxs-lookup"><span data-stu-id="73637-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73637-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73637-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73637-143">Выполните следующую команду в консоли диспетчера пакетов (PMC):</span><span class="sxs-lookup"><span data-stu-id="73637-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="73637-144">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="73637-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="73637-145">Миграция не требуется на этом этапе при использовании SQLite.</span><span class="sxs-lookup"><span data-stu-id="73637-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="73637-146">Для LocalDB выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="73637-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="73637-147">Проверка регистра и вход в систему</span><span class="sxs-lookup"><span data-stu-id="73637-147">Test Register and Login</span></span>

<span data-ttu-id="73637-148">Запустите приложение и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="73637-148">Run the app and register a user.</span></span> <span data-ttu-id="73637-149">В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .</span><span class="sxs-lookup"><span data-stu-id="73637-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="73637-150">Настройка Identity служб</span><span class="sxs-lookup"><span data-stu-id="73637-150">Configure Identity services</span></span>

<span data-ttu-id="73637-151">Службы добавляются в `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="73637-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="73637-152">По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="73637-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="73637-153">Выделенный выше код настраивается Identity со значениями параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="73637-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="73637-154">Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="73637-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="73637-155">Identityвключается путем вызова метода <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="73637-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="73637-156">`UseAuthentication`добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="73637-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="73637-157">Созданное шаблоном приложение не использует [авторизацию](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="73637-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="73637-158">`app.UseAuthorization`включается, чтобы обеспечить его добавление в правильном порядке, если приложение добавляет авторизацию.</span><span class="sxs-lookup"><span data-stu-id="73637-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="73637-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` и `UseEndpoints` должны вызываться в порядке, показанном в приведенном выше коде.</span><span class="sxs-lookup"><span data-stu-id="73637-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="73637-160">Дополнительные сведения о `IdentityOptions` и см `Startup` . в разделе <xref:Microsoft.AspNetCore.Identity.IdentityOptions> и [запуске приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="73637-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="73637-161">Регистр шаблонов, вход, выход и Регистерконфирматион</span><span class="sxs-lookup"><span data-stu-id="73637-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73637-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73637-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73637-163">Добавьте `Register` файлы, `Login` , `LogOut` и `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="73637-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="73637-164">Соблюдайте [идентификатор шаблона в Razor проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="73637-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="73637-165">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="73637-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="73637-166">Если вы создали проект с именем " **APP1**", выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="73637-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="73637-167">В противном случае используйте правильное пространство имен для `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="73637-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="73637-168">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="73637-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="73637-169">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="73637-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="73637-170">Дополнительные сведения о формировании шаблонов Identity см. в статье [удостоверение шаблона в Razor проекте с авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="73637-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="73637-171">Проверка регистра</span><span class="sxs-lookup"><span data-stu-id="73637-171">Examine Register</span></span>

<span data-ttu-id="73637-172">Когда пользователь нажимает кнопку **Register** на `Register` странице, `RegisterModel.OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="73637-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="73637-173">Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:</span><span class="sxs-lookup"><span data-stu-id="73637-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="73637-174">Вход в систему</span><span class="sxs-lookup"><span data-stu-id="73637-174">Log in</span></span>

<span data-ttu-id="73637-175">Форма входа отображается в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="73637-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="73637-176">Выбрана ссылка для **входа** .</span><span class="sxs-lookup"><span data-stu-id="73637-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="73637-177">Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="73637-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="73637-178">При отправке формы на странице входа `OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="73637-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="73637-179">`PasswordSignInAsync`метод вызывается для `_signInManager` объекта.</span><span class="sxs-lookup"><span data-stu-id="73637-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="73637-180">Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="73637-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="73637-181">выход из системы;</span><span class="sxs-lookup"><span data-stu-id="73637-181">Log out</span></span>

<span data-ttu-id="73637-182">Ссылка **выхода** вызывает `LogoutModel.OnPost` действие.</span><span class="sxs-lookup"><span data-stu-id="73637-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="73637-183">В приведенном выше коде код `return RedirectToPage();` должен быть перенаправлением, чтобы браузер выполнял новый запрос и удостоверение для пользователя обновляется.</span><span class="sxs-lookup"><span data-stu-id="73637-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="73637-184">[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="73637-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="73637-185">Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="73637-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="73637-186">ПроверьтеIdentity</span><span class="sxs-lookup"><span data-stu-id="73637-186">Test Identity</span></span>

<span data-ttu-id="73637-187">Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам.</span><span class="sxs-lookup"><span data-stu-id="73637-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="73637-188">Чтобы протестировать Identity , добавьте [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="73637-188">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="73637-189">Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** .</span><span class="sxs-lookup"><span data-stu-id="73637-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="73637-190">Вы перейдете на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="73637-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="73637-191">ПросматриваемIdentity</span><span class="sxs-lookup"><span data-stu-id="73637-191">Explore Identity</span></span>

<span data-ttu-id="73637-192">IdentityБолее подробное изучение:</span><span class="sxs-lookup"><span data-stu-id="73637-192">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="73637-193">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="73637-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="73637-194">Изучите источник каждой страницы и пошаговое выполнение отладчика.</span><span class="sxs-lookup"><span data-stu-id="73637-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="73637-195">IdentityКомпонента</span><span class="sxs-lookup"><span data-stu-id="73637-195">Identity Components</span></span>

<span data-ttu-id="73637-196">Все Identity зависимые пакеты NuGet включены в [ASP.NET Core общую платформу](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="73637-196">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="73637-197">Основным пакетом для Identity является [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="73637-197">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="73637-198">Этот пакет содержит основной набор интерфейсов для ASP.NET Core и входит в Identity состав `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="73637-198">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="73637-199">Переход на ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="73637-199">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="73637-200">Дополнительные сведения и рекомендации по переносу существующего Identity хранилища см. в статье [Миграция Identity проверки подлинности и ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="73637-200">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="73637-201">Установка силы пароля</span><span class="sxs-lookup"><span data-stu-id="73637-201">Setting password strength</span></span>

<span data-ttu-id="73637-202">См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.</span><span class="sxs-lookup"><span data-stu-id="73637-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="73637-203">Адддефаулт Identity и добавитьIdentity</span><span class="sxs-lookup"><span data-stu-id="73637-203">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="73637-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>было введено в ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="73637-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="73637-205">Вызов `AddDefaultIdentity` аналогичен вызову следующего:</span><span class="sxs-lookup"><span data-stu-id="73637-205">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="73637-206">Дополнительные сведения см. в разделе [адддефаулт Identity Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="73637-206">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="73637-207">Запретить публикацию статических Identity ресурсов</span><span class="sxs-lookup"><span data-stu-id="73637-207">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="73637-208">Чтобы предотвратить публикацию статических Identity ресурсов (таблиц стилей и файлов JavaScript для Identity пользовательского интерфейса) в корневом каталоге веб-сайта, добавьте следующее `ResolveStaticWebAssetsInputsDependsOn` свойство и `RemoveIdentityAssets` целевой объект в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="73637-208">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="73637-209">Дальнейшие шаги</span><span class="sxs-lookup"><span data-stu-id="73637-209">Next Steps</span></span>

* <span data-ttu-id="73637-210">[IdentityИсходный код ASP.NET Core](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="73637-210">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="73637-211">Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="73637-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="73637-212">Настройка Identity</span><span class="sxs-lookup"><span data-stu-id="73637-212">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="73637-213">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="73637-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="73637-214">ASP.NET Core Identity — это система членства, которая добавляет функции входа в ASP.NET Core приложения.</span><span class="sxs-lookup"><span data-stu-id="73637-214">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="73637-215">Пользователи могут создать учетную запись с данными входа, хранящимися в, Identity или использовать внешний поставщик входа.</span><span class="sxs-lookup"><span data-stu-id="73637-215">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="73637-216">Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="73637-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="73637-217">Identityможно настроить с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей.</span><span class="sxs-lookup"><span data-stu-id="73637-217">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="73637-218">Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.</span><span class="sxs-lookup"><span data-stu-id="73637-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="73637-219">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Загрузка)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="73637-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="73637-220">В этом разделе вы узнаете, как использовать Identity для регистрации, входа и выхода пользователя.</span><span class="sxs-lookup"><span data-stu-id="73637-220">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="73637-221">Более подробные инструкции по созданию приложений, использующих Identity , см. в разделе дальнейшие действия в конце этой статьи.</span><span class="sxs-lookup"><span data-stu-id="73637-221">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="73637-222">Адддефаулт Identity и добавитьIdentity</span><span class="sxs-lookup"><span data-stu-id="73637-222">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="73637-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>было введено в ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="73637-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="73637-224">Вызов `AddDefaultIdentity` аналогичен вызову следующего:</span><span class="sxs-lookup"><span data-stu-id="73637-224">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="73637-225">Дополнительные сведения см. в разделе [адддефаулт Identity Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="73637-225">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="73637-226">Создание веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="73637-226">Create a Web app with authentication</span></span>

<span data-ttu-id="73637-227">Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.</span><span class="sxs-lookup"><span data-stu-id="73637-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73637-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73637-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="73637-229">Выберите **файл** > **создать** > **проект**.</span><span class="sxs-lookup"><span data-stu-id="73637-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="73637-230">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="73637-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="73637-231">Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект.</span><span class="sxs-lookup"><span data-stu-id="73637-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="73637-232">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="73637-232">Click **OK**.</span></span>
* <span data-ttu-id="73637-233">Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="73637-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="73637-234">Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="73637-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="73637-235">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="73637-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="73637-236">Созданный проект предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="73637-236">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="73637-237">Identity Razor Библиотека классов предоставляет конечные точки с `Identity` областью.</span><span class="sxs-lookup"><span data-stu-id="73637-237">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="73637-238">Пример:</span><span class="sxs-lookup"><span data-stu-id="73637-238">For example:</span></span>

* <span data-ttu-id="73637-239">/Identity/аккаунт/логин</span><span class="sxs-lookup"><span data-stu-id="73637-239">/Identity/Account/Login</span></span>
* <span data-ttu-id="73637-240">/Identity/аккаунт/логаут</span><span class="sxs-lookup"><span data-stu-id="73637-240">/Identity/Account/Logout</span></span>
* <span data-ttu-id="73637-241">/Identity/аккаунт/манаже</span><span class="sxs-lookup"><span data-stu-id="73637-241">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="73637-242">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="73637-242">Apply migrations</span></span>

<span data-ttu-id="73637-243">Примените миграции, чтобы инициализировать базу данных.</span><span class="sxs-lookup"><span data-stu-id="73637-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73637-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73637-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73637-245">Выполните следующую команду в консоли диспетчера пакетов (PMC):</span><span class="sxs-lookup"><span data-stu-id="73637-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="73637-246">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="73637-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="73637-247">Проверка регистра и вход в систему</span><span class="sxs-lookup"><span data-stu-id="73637-247">Test Register and Login</span></span>

<span data-ttu-id="73637-248">Запустите приложение и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="73637-248">Run the app and register a user.</span></span> <span data-ttu-id="73637-249">В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .</span><span class="sxs-lookup"><span data-stu-id="73637-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="73637-250">Настройка Identity служб</span><span class="sxs-lookup"><span data-stu-id="73637-250">Configure Identity services</span></span>

<span data-ttu-id="73637-251">Службы добавляются в `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="73637-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="73637-252">По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="73637-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="73637-253">Приведенный выше код настраивается Identity со значениями параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="73637-253">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="73637-254">Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="73637-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="73637-255">Identityвключается путем вызова [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="73637-255">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="73637-256">`UseAuthentication`добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="73637-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="73637-257">Дополнительные сведения см. в статье [ Identity класс Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) и [Запуск приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="73637-257">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="73637-258">Регистрация, вход и выход из шаблона формирования шаблонов</span><span class="sxs-lookup"><span data-stu-id="73637-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="73637-259">Соблюдайте [идентификатор шаблона в Razor проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="73637-259">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73637-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73637-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73637-261">Добавьте файлы Register, login и LogOut.</span><span class="sxs-lookup"><span data-stu-id="73637-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="73637-262">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="73637-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="73637-263">Если вы создали проект с именем " **APP1**", выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="73637-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="73637-264">В противном случае используйте правильное пространство имен для `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="73637-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="73637-265">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="73637-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="73637-266">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="73637-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="73637-267">Проверка регистра</span><span class="sxs-lookup"><span data-stu-id="73637-267">Examine Register</span></span>

<span data-ttu-id="73637-268">Когда пользователь щелкает ссылку **Register** , `RegisterModel.OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="73637-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="73637-269">Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:</span><span class="sxs-lookup"><span data-stu-id="73637-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="73637-270">Если пользователь успешно создан, пользователь входит в систему с помощью вызова `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="73637-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="73637-271">**Примечание.** Инструкции по предотвращению немедленного входа при регистрации см. в статье [Подтверждение учетной записи](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="73637-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="73637-272">Вход в систему</span><span class="sxs-lookup"><span data-stu-id="73637-272">Log in</span></span>

<span data-ttu-id="73637-273">Форма входа отображается в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="73637-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="73637-274">Выбрана ссылка для **входа** .</span><span class="sxs-lookup"><span data-stu-id="73637-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="73637-275">Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="73637-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="73637-276">При отправке формы на странице входа `OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="73637-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="73637-277">`PasswordSignInAsync`метод вызывается для `_signInManager` объекта.</span><span class="sxs-lookup"><span data-stu-id="73637-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="73637-278">Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="73637-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="73637-279">выход из системы;</span><span class="sxs-lookup"><span data-stu-id="73637-279">Log out</span></span>

<span data-ttu-id="73637-280">Ссылка **выхода** вызывает `LogoutModel.OnPost` действие.</span><span class="sxs-lookup"><span data-stu-id="73637-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="73637-281">[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="73637-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="73637-282">Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="73637-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="73637-283">ПроверьтеIdentity</span><span class="sxs-lookup"><span data-stu-id="73637-283">Test Identity</span></span>

<span data-ttu-id="73637-284">Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам.</span><span class="sxs-lookup"><span data-stu-id="73637-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="73637-285">Чтобы протестировать Identity , добавьте [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) на страницу конфиденциальность.</span><span class="sxs-lookup"><span data-stu-id="73637-285">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="73637-286">Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** .</span><span class="sxs-lookup"><span data-stu-id="73637-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="73637-287">Вы перейдете на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="73637-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="73637-288">ПросматриваемIdentity</span><span class="sxs-lookup"><span data-stu-id="73637-288">Explore Identity</span></span>

<span data-ttu-id="73637-289">IdentityБолее подробное изучение:</span><span class="sxs-lookup"><span data-stu-id="73637-289">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="73637-290">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="73637-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="73637-291">Изучите источник каждой страницы и пошаговое выполнение отладчика.</span><span class="sxs-lookup"><span data-stu-id="73637-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="73637-292">IdentityКомпонента</span><span class="sxs-lookup"><span data-stu-id="73637-292">Identity Components</span></span>

<span data-ttu-id="73637-293">Все Identity зависимые пакеты NuGet включены в пакет [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="73637-293">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="73637-294">Основным пакетом для Identity является [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="73637-294">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="73637-295">Этот пакет содержит основной набор интерфейсов для ASP.NET Core и входит в Identity состав `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="73637-295">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="73637-296">Переход на ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="73637-296">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="73637-297">Дополнительные сведения и рекомендации по переносу существующего Identity хранилища см. в статье [Миграция Identity проверки подлинности и ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="73637-297">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="73637-298">Установка силы пароля</span><span class="sxs-lookup"><span data-stu-id="73637-298">Setting password strength</span></span>

<span data-ttu-id="73637-299">См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.</span><span class="sxs-lookup"><span data-stu-id="73637-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="73637-300">Дальнейшие шаги</span><span class="sxs-lookup"><span data-stu-id="73637-300">Next Steps</span></span>

* <span data-ttu-id="73637-301">Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="73637-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="73637-302">Настройка Identity</span><span class="sxs-lookup"><span data-stu-id="73637-302">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
