---
title: 'Введение в :::no-loc(Identity)::: ASP.NET Core'
author: rick-anderson
description: 'Используйте :::no-loc(Identity)::: с приложением ASP.NET Core. Узнайте, как устанавливать требования к паролю (Рекуиредигит, Рекуиредленгс, Рекуиредуникуечарс и др.).'
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: dd3296db568700a363c427398f02239846a46ada
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/21/2020
ms.locfileid: "86445439"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="1fc5d-104">Введение в :::no-loc(Identity)::: ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1fc5d-104">Introduction to :::no-loc(Identity)::: on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1fc5d-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="1fc5d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1fc5d-106">ASP.NET Core :::no-loc(Identity)::::</span><span class="sxs-lookup"><span data-stu-id="1fc5d-106">ASP.NET Core :::no-loc(Identity)::::</span></span>

* <span data-ttu-id="1fc5d-107">— Это API, поддерживающий функцию входа в пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="1fc5d-108">Управляет пользователями, паролями, данными профилирования, ролями, утверждениями, маркерами, подтверждением электронной почты и т. д.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="1fc5d-109">Пользователи могут создать учетную запись с данными входа, хранящимися в, :::no-loc(Identity)::: или использовать внешний поставщик входа.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-109">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="1fc5d-110">Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="1fc5d-111">[ :::no-loc(Identity)::: Исходный код](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) доступен на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-111">The [:::no-loc(Identity)::: source code](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) is available on GitHub.</span></span> <span data-ttu-id="1fc5d-112">[Формирование :::no-loc(Identity)::: шаблонов](xref:security/authentication/scaffold-identity) и просмотрите созданные файлы, чтобы проверить взаимодействие с шаблоном :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-112">[Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with :::no-loc(Identity):::.</span></span>

<span data-ttu-id="1fc5d-113">:::no-loc(Identity):::обычно настраивается с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-113">:::no-loc(Identity)::: is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="1fc5d-114">Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="1fc5d-115">В этом разделе вы узнаете, как использовать :::no-loc(Identity)::: для регистрации, входа и выхода пользователя.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-115">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="1fc5d-116">Примечание. шаблоны считают имя пользователя и адрес электронной почты одинаковыми для пользователей.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="1fc5d-117">Более подробные инструкции по созданию приложений, использующих :::no-loc(Identity)::: , см. в разделе [дальнейшие действия](#next).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-117">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see [Next Steps](#next).</span></span>

<span data-ttu-id="1fc5d-118">[Платформа Microsoft Identity Platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="1fc5d-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="1fc5d-119">Развитие платформы разработки Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="1fc5d-120">Не связано с ASP.NET Core :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-120">Unrelated to ASP.NET Core :::no-loc(Identity):::.</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

<span data-ttu-id="1fc5d-121">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Загрузка)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="1fc5d-122">Создание веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="1fc5d-122">Create a Web app with authentication</span></span>

<span data-ttu-id="1fc5d-123">Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1fc5d-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fc5d-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1fc5d-125">Выберите **файл** > **создать** > **проект**.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="1fc5d-126">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="1fc5d-127">Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="1fc5d-128">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-128">Click **OK**.</span></span>
* <span data-ttu-id="1fc5d-129">Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="1fc5d-130">Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1fc5d-131">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fc5d-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="1fc5d-132">Предыдущая команда создает :::no-loc(Razor)::: веб-приложение с помощью SQLite.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-132">The preceding command creates a :::no-loc(Razor)::: web app using SQLite.</span></span> <span data-ttu-id="1fc5d-133">Чтобы создать веб-приложение с помощью LocalDB, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="1fc5d-134">Созданный проект предоставляет [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) в виде [ :::no-loc(Razor)::: библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-134">The generated project provides [ASP.NET Core :::no-loc(Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="1fc5d-135">:::no-loc(Identity)::: :::no-loc(Razor)::: Библиотека классов предоставляет конечные точки с `:::no-loc(Identity):::` областью.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-135">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="1fc5d-136">Пример:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-136">For example:</span></span>

* <span data-ttu-id="1fc5d-137">/:::no-loc(Identity):::/аккаунт/логин</span><span class="sxs-lookup"><span data-stu-id="1fc5d-137">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="1fc5d-138">/:::no-loc(Identity):::/аккаунт/логаут</span><span class="sxs-lookup"><span data-stu-id="1fc5d-138">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="1fc5d-139">/:::no-loc(Identity):::/аккаунт/манаже</span><span class="sxs-lookup"><span data-stu-id="1fc5d-139">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="1fc5d-140">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="1fc5d-140">Apply migrations</span></span>

<span data-ttu-id="1fc5d-141">Примените миграции, чтобы инициализировать базу данных.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1fc5d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fc5d-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1fc5d-143">Выполните следующую команду в консоли диспетчера пакетов (PMC):</span><span class="sxs-lookup"><span data-stu-id="1fc5d-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="1fc5d-144">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fc5d-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1fc5d-145">Миграция не требуется на этом этапе при использовании SQLite.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="1fc5d-146">Для LocalDB выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="1fc5d-147">Проверка регистра и вход в систему</span><span class="sxs-lookup"><span data-stu-id="1fc5d-147">Test Register and Login</span></span>

<span data-ttu-id="1fc5d-148">Запустите приложение и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-148">Run the app and register a user.</span></span> <span data-ttu-id="1fc5d-149">В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="1fc5d-150">Настройка :::no-loc(Identity)::: служб</span><span class="sxs-lookup"><span data-stu-id="1fc5d-150">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="1fc5d-151">Службы добавляются в `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="1fc5d-152">По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="1fc5d-153">Выделенный выше код настраивается :::no-loc(Identity)::: со значениями параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-153">The preceding highlighted code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="1fc5d-154">Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="1fc5d-155">:::no-loc(Identity):::включается путем вызова метода <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-155">:::no-loc(Identity)::: is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="1fc5d-156">`UseAuthentication`добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="1fc5d-157">Созданное шаблоном приложение не использует [авторизацию](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="1fc5d-158">`app.UseAuthorization`включается, чтобы обеспечить его добавление в правильном порядке, если приложение добавляет авторизацию.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="1fc5d-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` и `UseEndpoints` должны вызываться в порядке, показанном в приведенном выше коде.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="1fc5d-160">Дополнительные сведения о `:::no-loc(Identity):::Options` и см `Startup` . в разделе <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> и [запуске приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-160">For more information on `:::no-loc(Identity):::Options` and `Startup`, see <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="1fc5d-161">Регистр шаблонов, вход, выход и Регистерконфирматион</span><span class="sxs-lookup"><span data-stu-id="1fc5d-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1fc5d-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fc5d-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1fc5d-163">Добавьте `Register` файлы, `Login` , `LogOut` и `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="1fc5d-164">Соблюдайте [идентификатор шаблона в :::no-loc(Razor)::: проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-164">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1fc5d-165">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fc5d-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1fc5d-166">Если вы создали проект с именем " **APP1**", выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="1fc5d-167">В противном случае используйте правильное пространство имен для `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="1fc5d-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="1fc5d-168">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="1fc5d-169">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="1fc5d-170">Дополнительные сведения о формировании шаблонов :::no-loc(Identity)::: см. в статье [удостоверение шаблона в :::no-loc(Razor)::: проекте с авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-170">For more information on scaffolding :::no-loc(Identity):::, see [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="1fc5d-171">Проверка регистра</span><span class="sxs-lookup"><span data-stu-id="1fc5d-171">Examine Register</span></span>

<span data-ttu-id="1fc5d-172">Когда пользователь нажимает кнопку **Register** на `Register` странице, `RegisterModel.OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="1fc5d-173">Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="1fc5d-174">Вход в систему</span><span class="sxs-lookup"><span data-stu-id="1fc5d-174">Log in</span></span>

<span data-ttu-id="1fc5d-175">Форма входа отображается в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="1fc5d-176">Выбрана ссылка для **входа** .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="1fc5d-177">Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="1fc5d-178">При отправке формы на странице входа `OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="1fc5d-179">`PasswordSignInAsync`метод вызывается для `_signInManager` объекта.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="1fc5d-180">Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="1fc5d-181">выход из системы;</span><span class="sxs-lookup"><span data-stu-id="1fc5d-181">Log out</span></span>

<span data-ttu-id="1fc5d-182">Ссылка **выхода** вызывает `LogoutModel.OnPost` действие.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="1fc5d-183">В приведенном выше коде код `return RedirectToPage();` должен быть перенаправлением, чтобы браузер выполнял новый запрос и удостоверение для пользователя обновляется.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="1fc5d-184">[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="1fc5d-185">Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="1fc5d-186">Проверьте:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1fc5d-186">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="1fc5d-187">Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="1fc5d-188">Чтобы протестировать :::no-loc(Identity)::: , добавьте [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="1fc5d-188">To test :::no-loc(Identity):::, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="1fc5d-189">Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="1fc5d-190">Вы перейдете на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="1fc5d-191">Просматриваем:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1fc5d-191">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="1fc5d-192">:::no-loc(Identity):::Более подробное изучение:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-192">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="1fc5d-193">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="1fc5d-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="1fc5d-194">Изучите источник каждой страницы и пошаговое выполнение отладчика.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="1fc5d-195">:::no-loc(Identity):::Компонента</span><span class="sxs-lookup"><span data-stu-id="1fc5d-195">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="1fc5d-196">Все :::no-loc(Identity)::: зависимые пакеты NuGet включены в [ASP.NET Core общую платформу](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-196">All the :::no-loc(Identity):::-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="1fc5d-197">Основным пакетом для :::no-loc(Identity)::: является [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)</span><span class="sxs-lookup"><span data-stu-id="1fc5d-197">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="1fc5d-198">Этот пакет содержит основной набор интерфейсов для ASP.NET Core и входит в :::no-loc(Identity)::: состав `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-198">This package contains the core set of interfaces for ASP.NET Core :::no-loc(Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="1fc5d-199">Переход на ASP.NET Core:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1fc5d-199">Migrating to ASP.NET Core :::no-loc(Identity):::</span></span>

<span data-ttu-id="1fc5d-200">Дополнительные сведения и рекомендации по переносу существующего :::no-loc(Identity)::: хранилища см. в статье [Миграция :::no-loc(Identity)::: проверки подлинности и ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-200">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="1fc5d-201">Установка силы пароля</span><span class="sxs-lookup"><span data-stu-id="1fc5d-201">Setting password strength</span></span>

<span data-ttu-id="1fc5d-202">См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="1fc5d-203">Адддефаулт :::no-loc(Identity)::: и добавить:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1fc5d-203">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="1fc5d-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>было введено в ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="1fc5d-205">Вызов `AddDefault:::no-loc(Identity):::` аналогичен вызову следующего:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-205">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="1fc5d-206">Дополнительные сведения см. в разделе [адддефаулт :::no-loc(Identity)::: Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-206">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="1fc5d-207">Запретить публикацию статических :::no-loc(Identity)::: ресурсов</span><span class="sxs-lookup"><span data-stu-id="1fc5d-207">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="1fc5d-208">Чтобы предотвратить публикацию статических :::no-loc(Identity)::: ресурсов (таблиц стилей и файлов JavaScript для :::no-loc(Identity)::: пользовательского интерфейса) в корневом каталоге веб-сайта, добавьте следующее `ResolveStaticWebAssetsInputsDependsOn` свойство и `Remove:::no-loc(Identity):::Assets` целевой объект в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-208">To prevent publishing static :::no-loc(Identity)::: assets (stylesheets and JavaScript files for :::no-loc(Identity)::: UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `Remove:::no-loc(Identity):::Assets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="1fc5d-209">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="1fc5d-209">Next Steps</span></span>

* <span data-ttu-id="1fc5d-210">[:::no-loc(Identity):::Исходный код ASP.NET Core](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span><span class="sxs-lookup"><span data-stu-id="1fc5d-210">[ASP.NET Core :::no-loc(Identity)::: source code](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span></span>
* <span data-ttu-id="1fc5d-211">Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="1fc5d-212">Настройка :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1fc5d-212">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1fc5d-213">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="1fc5d-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1fc5d-214">ASP.NET Core :::no-loc(Identity)::: — это система членства, которая добавляет функции входа в ASP.NET Core приложения.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-214">ASP.NET Core :::no-loc(Identity)::: is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="1fc5d-215">Пользователи могут создать учетную запись с данными входа, хранящимися в, :::no-loc(Identity)::: или использовать внешний поставщик входа.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-215">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="1fc5d-216">Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="1fc5d-217">:::no-loc(Identity):::можно настроить с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-217">:::no-loc(Identity)::: can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="1fc5d-218">Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="1fc5d-219">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([Загрузка)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="1fc5d-220">В этом разделе вы узнаете, как использовать :::no-loc(Identity)::: для регистрации, входа и выхода пользователя.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-220">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="1fc5d-221">Более подробные инструкции по созданию приложений, использующих :::no-loc(Identity)::: , см. в разделе дальнейшие действия в конце этой статьи.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-221">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="1fc5d-222">Адддефаулт :::no-loc(Identity)::: и добавить:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1fc5d-222">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="1fc5d-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>было введено в ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="1fc5d-224">Вызов `AddDefault:::no-loc(Identity):::` аналогичен вызову следующего:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-224">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="1fc5d-225">Дополнительные сведения см. в разделе [адддефаулт :::no-loc(Identity)::: Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-225">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="1fc5d-226">Создание веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="1fc5d-226">Create a Web app with authentication</span></span>

<span data-ttu-id="1fc5d-227">Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1fc5d-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fc5d-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1fc5d-229">Выберите **файл** > **создать** > **проект**.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="1fc5d-230">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="1fc5d-231">Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="1fc5d-232">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-232">Click **OK**.</span></span>
* <span data-ttu-id="1fc5d-233">Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="1fc5d-234">Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1fc5d-235">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fc5d-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="1fc5d-236">Созданный проект предоставляет [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) в виде [ :::no-loc(Razor)::: библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-236">The generated project provides [ASP.NET Core :::no-loc(Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="1fc5d-237">:::no-loc(Identity)::: :::no-loc(Razor)::: Библиотека классов предоставляет конечные точки с `:::no-loc(Identity):::` областью.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-237">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="1fc5d-238">Пример:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-238">For example:</span></span>

* <span data-ttu-id="1fc5d-239">/:::no-loc(Identity):::/аккаунт/логин</span><span class="sxs-lookup"><span data-stu-id="1fc5d-239">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="1fc5d-240">/:::no-loc(Identity):::/аккаунт/логаут</span><span class="sxs-lookup"><span data-stu-id="1fc5d-240">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="1fc5d-241">/:::no-loc(Identity):::/аккаунт/манаже</span><span class="sxs-lookup"><span data-stu-id="1fc5d-241">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="1fc5d-242">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="1fc5d-242">Apply migrations</span></span>

<span data-ttu-id="1fc5d-243">Примените миграции, чтобы инициализировать базу данных.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1fc5d-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fc5d-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1fc5d-245">Выполните следующую команду в консоли диспетчера пакетов (PMC):</span><span class="sxs-lookup"><span data-stu-id="1fc5d-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="1fc5d-246">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fc5d-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="1fc5d-247">Проверка регистра и вход в систему</span><span class="sxs-lookup"><span data-stu-id="1fc5d-247">Test Register and Login</span></span>

<span data-ttu-id="1fc5d-248">Запустите приложение и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-248">Run the app and register a user.</span></span> <span data-ttu-id="1fc5d-249">В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="1fc5d-250">Настройка :::no-loc(Identity)::: служб</span><span class="sxs-lookup"><span data-stu-id="1fc5d-250">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="1fc5d-251">Службы добавляются в `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="1fc5d-252">По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="1fc5d-253">Приведенный выше код настраивается :::no-loc(Identity)::: со значениями параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-253">The preceding code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="1fc5d-254">Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="1fc5d-255">:::no-loc(Identity):::включается путем вызова [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-255">:::no-loc(Identity)::: is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="1fc5d-256">`UseAuthentication`добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="1fc5d-257">Дополнительные сведения см. в статье [ :::no-loc(Identity)::: класс Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) и [Запуск приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-257">For more information, see the [:::no-loc(Identity):::Options Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="1fc5d-258">Регистрация, вход и выход из шаблона формирования шаблонов</span><span class="sxs-lookup"><span data-stu-id="1fc5d-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="1fc5d-259">Соблюдайте [идентификатор шаблона в :::no-loc(Razor)::: проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-259">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1fc5d-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fc5d-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1fc5d-261">Добавьте файлы Register, login и LogOut.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1fc5d-262">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fc5d-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1fc5d-263">Если вы создали проект с именем " **APP1**", выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="1fc5d-264">В противном случае используйте правильное пространство имен для `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="1fc5d-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="1fc5d-265">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="1fc5d-266">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="1fc5d-267">Проверка регистра</span><span class="sxs-lookup"><span data-stu-id="1fc5d-267">Examine Register</span></span>

<span data-ttu-id="1fc5d-268">Когда пользователь щелкает ссылку **Register** , `RegisterModel.OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="1fc5d-269">Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="1fc5d-270">Если пользователь успешно создан, пользователь входит в систему с помощью вызова `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="1fc5d-271">**Примечание.** Инструкции по предотвращению немедленного входа при регистрации см. в статье [Подтверждение учетной записи](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="1fc5d-272">Вход в систему</span><span class="sxs-lookup"><span data-stu-id="1fc5d-272">Log in</span></span>

<span data-ttu-id="1fc5d-273">Форма входа отображается в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="1fc5d-274">Выбрана ссылка для **входа** .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="1fc5d-275">Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="1fc5d-276">При отправке формы на странице входа `OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="1fc5d-277">`PasswordSignInAsync`метод вызывается для `_signInManager` объекта.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="1fc5d-278">Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="1fc5d-279">выход из системы;</span><span class="sxs-lookup"><span data-stu-id="1fc5d-279">Log out</span></span>

<span data-ttu-id="1fc5d-280">Ссылка **выхода** вызывает `LogoutModel.OnPost` действие.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="1fc5d-281">[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="1fc5d-282">Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="1fc5d-283">Проверьте:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1fc5d-283">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="1fc5d-284">Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="1fc5d-285">Чтобы протестировать :::no-loc(Identity)::: , добавьте [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) на страницу конфиденциальность.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-285">To test :::no-loc(Identity):::, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="1fc5d-286">Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="1fc5d-287">Вы перейдете на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="1fc5d-288">Просматриваем:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1fc5d-288">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="1fc5d-289">:::no-loc(Identity):::Более подробное изучение:</span><span class="sxs-lookup"><span data-stu-id="1fc5d-289">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="1fc5d-290">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="1fc5d-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="1fc5d-291">Изучите источник каждой страницы и пошаговое выполнение отладчика.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="1fc5d-292">:::no-loc(Identity):::Компонента</span><span class="sxs-lookup"><span data-stu-id="1fc5d-292">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="1fc5d-293">Все :::no-loc(Identity)::: зависимые пакеты NuGet включены в пакет [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-293">All the :::no-loc(Identity)::: dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="1fc5d-294">Основным пакетом для :::no-loc(Identity)::: является [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)</span><span class="sxs-lookup"><span data-stu-id="1fc5d-294">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="1fc5d-295">Этот пакет содержит основной набор интерфейсов для ASP.NET Core и входит в :::no-loc(Identity)::: состав `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-295">This package contains the core set of interfaces for ASP.NET Core :::no-loc(Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="1fc5d-296">Переход на ASP.NET Core:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1fc5d-296">Migrating to ASP.NET Core :::no-loc(Identity):::</span></span>

<span data-ttu-id="1fc5d-297">Дополнительные сведения и рекомендации по переносу существующего :::no-loc(Identity)::: хранилища см. в статье [Миграция :::no-loc(Identity)::: проверки подлинности и ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="1fc5d-297">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="1fc5d-298">Установка силы пароля</span><span class="sxs-lookup"><span data-stu-id="1fc5d-298">Setting password strength</span></span>

<span data-ttu-id="1fc5d-299">См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.</span><span class="sxs-lookup"><span data-stu-id="1fc5d-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1fc5d-300">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="1fc5d-300">Next Steps</span></span>

* <span data-ttu-id="1fc5d-301">Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="1fc5d-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="1fc5d-302">Настройка :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1fc5d-302">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
