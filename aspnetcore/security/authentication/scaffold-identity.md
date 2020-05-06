---
title: Формирование Identity шаблонов в ASP.NET Core проектах
author: rick-anderson
description: Сведения о формировании Identity шаблонов в проекте ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768394"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="2b851-103">Формирование Identity шаблонов в ASP.NET Core проектах</span><span class="sxs-lookup"><span data-stu-id="2b851-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="2b851-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="2b851-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2b851-105">ASP.NET Core предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="2b851-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="2b851-106">Приложения, которые Identity включают в себя, могут применять механизм формирования шаблонов для выборочного добавления исходного кода Identity Razor , содержащегося в библиотеке классов (РКЛ).</span><span class="sxs-lookup"><span data-stu-id="2b851-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="2b851-107">Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение.</span><span class="sxs-lookup"><span data-stu-id="2b851-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="2b851-108">Например, вы можете указать шаблону создать код, используемый при регистрации.</span><span class="sxs-lookup"><span data-stu-id="2b851-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="2b851-109">Созданный код имеет приоритет над тем же кодом в Identity РКЛ.</span><span class="sxs-lookup"><span data-stu-id="2b851-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="2b851-110">Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного идентификатора пользовательского интерфейса идентификации](#full).</span><span class="sxs-lookup"><span data-stu-id="2b851-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="2b851-111">Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов Identity для добавления пакета РКЛ.</span><span class="sxs-lookup"><span data-stu-id="2b851-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="2b851-112">Вы можете выбрать Identity код для создания.</span><span class="sxs-lookup"><span data-stu-id="2b851-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="2b851-113">Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс.</span><span class="sxs-lookup"><span data-stu-id="2b851-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="2b851-114">В этом документе объясняются шаги, необходимые для Identity завершения обновления формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="2b851-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="2b851-115">Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения.</span><span class="sxs-lookup"><span data-stu-id="2b851-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="2b851-116">Проверьте изменения после запуска Identity механизма формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="2b851-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="2b851-117">Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также Identityдругих функций безопасности с.</span><span class="sxs-lookup"><span data-stu-id="2b851-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="2b851-118">Службы или заглушки служб не создаются при формировании шаблонов Identity.</span><span class="sxs-lookup"><span data-stu-id="2b851-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="2b851-119">Службы для включения этих функций необходимо добавить вручную.</span><span class="sxs-lookup"><span data-stu-id="2b851-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="2b851-120">Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="2b851-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="2b851-121">При формировании Identity шаблона с новым контекстом данных в проекте с существующими индивидуальными учетными записями:</span><span class="sxs-lookup"><span data-stu-id="2b851-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="2b851-122">В `Startup.ConfigureServices`удалите вызовы:</span><span class="sxs-lookup"><span data-stu-id="2b851-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="2b851-123">Например, `AddDbContext` и `AddDefaultIdentity` комментарии в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="2b851-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="2b851-124">Предшествующий код записывает в комментарий код, который дублируется в *областиIdentity//IdentityHostingStartup.CS*</span><span class="sxs-lookup"><span data-stu-id="2b851-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="2b851-125">Как правило, приложения, созданные с помощью отдельных учетных записей, ***не*** должны создавать новый контекст данных.</span><span class="sxs-lookup"><span data-stu-id="2b851-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="2b851-126">Удостоверение шаблона в пустой проект</span><span class="sxs-lookup"><span data-stu-id="2b851-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="2b851-127">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="2b851-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="2b851-128">Удостоверение шаблона в Razor проекте без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="2b851-128">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="2b851-129">настраивается в *областиIdentity//IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="2b851-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2b851-130">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="2b851-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="2b851-131">Миграция, Усеаусентикатион и макет</span><span class="sxs-lookup"><span data-stu-id="2b851-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="2b851-132">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="2b851-132">Enable authentication</span></span>

<span data-ttu-id="2b851-133">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="2b851-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="2b851-134">Изменения макета</span><span class="sxs-lookup"><span data-stu-id="2b851-134">Layout changes</span></span>

<span data-ttu-id="2b851-135">Необязательно: Добавьте в файл макета`_LoginPartial`имя для входа partial ():</span><span class="sxs-lookup"><span data-stu-id="2b851-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="2b851-136">Удостоверение шаблона в Razor проекте с авторизацией</span><span class="sxs-lookup"><span data-stu-id="2b851-136">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="2b851-137">Некоторые Identity параметры настраиваются в *области/Identity/IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="2b851-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2b851-138">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="2b851-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="2b851-139">Удостоверение шаблона в проекте MVC без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="2b851-139">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="2b851-140">Необязательно: Добавьте часть имени для`_LoginPartial`входа partial () в файл *views/shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2b851-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="2b851-141">Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="2b851-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="2b851-142">настраивается в *областиIdentity//IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="2b851-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2b851-143">Дополнительные сведения см. в разделе IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="2b851-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="2b851-144">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="2b851-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="2b851-145">Удостоверение шаблона в проекте MVC с авторизацией</span><span class="sxs-lookup"><span data-stu-id="2b851-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="2b851-146">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="2b851-146">Create full identity UI source</span></span>

<span data-ttu-id="2b851-147">Чтобы обеспечить полный контроль над Identity пользовательским интерфейсом, Identity запустите шаблон и выберите **переопределить все файлы**.</span><span class="sxs-lookup"><span data-stu-id="2b851-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="2b851-148">В следующем выделенном коде показаны изменения для замены пользовательского интерфейса Identity по умолчанию Identity в веб-приложении ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="2b851-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="2b851-149">Это может потребоваться для полного контроля над Identity пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="2b851-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="2b851-150">Значение по Identity умолчанию заменяется в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="2b851-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="2b851-151">Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="2b851-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="2b851-152">Зарегистрируйте `IEmailSender` реализацию, например:</span><span class="sxs-lookup"><span data-stu-id="2b851-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="2b851-153">Отключить страницу регистрации</span><span class="sxs-lookup"><span data-stu-id="2b851-153">Disable register page</span></span>

<span data-ttu-id="2b851-154">Чтобы отключить регистрацию пользователей, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="2b851-154">To disable user registration:</span></span>

* <span data-ttu-id="2b851-155">Шаблон Identity.</span><span class="sxs-lookup"><span data-stu-id="2b851-155">Scaffold Identity.</span></span> <span data-ttu-id="2b851-156">Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион.</span><span class="sxs-lookup"><span data-stu-id="2b851-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="2b851-157">Например:</span><span class="sxs-lookup"><span data-stu-id="2b851-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="2b851-158">Обновите *областиIdentityили/Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:</span><span class="sxs-lookup"><span data-stu-id="2b851-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="2b851-159">Обновите *областиIdentity//Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:</span><span class="sxs-lookup"><span data-stu-id="2b851-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="2b851-160">Закомментируйте или удалите регистрационную ссылку из *областейIdentity//Пажес/аккаунт/логин.кштмл*</span><span class="sxs-lookup"><span data-stu-id="2b851-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="2b851-161">Обновите страницу *областиIdentityили/Пажес/аккаунт/регистерконфирматион* .</span><span class="sxs-lookup"><span data-stu-id="2b851-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="2b851-162">Удалите код и ссылки из файла CSHTML.</span><span class="sxs-lookup"><span data-stu-id="2b851-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="2b851-163">Удалите код подтверждения из `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="2b851-163">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="2b851-164">Использование другого приложения для добавления пользователей</span><span class="sxs-lookup"><span data-stu-id="2b851-164">Use another app to add users</span></span>

<span data-ttu-id="2b851-165">Предоставьте механизм для добавления пользователей за пределами веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="2b851-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="2b851-166">Ниже перечислены параметры для добавления пользователей.</span><span class="sxs-lookup"><span data-stu-id="2b851-166">Options to add users include:</span></span>

* <span data-ttu-id="2b851-167">Выделенное административное веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="2b851-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="2b851-168">Консольное приложение.</span><span class="sxs-lookup"><span data-stu-id="2b851-168">A console app.</span></span>

<span data-ttu-id="2b851-169">В следующем примере кода один из подходов к добавлению пользователей:</span><span class="sxs-lookup"><span data-stu-id="2b851-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="2b851-170">Список пользователей считывается в память.</span><span class="sxs-lookup"><span data-stu-id="2b851-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="2b851-171">Для каждого пользователя создается надежный уникальный пароль.</span><span class="sxs-lookup"><span data-stu-id="2b851-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="2b851-172">Пользователь будет добавлен в Identity базу данных.</span><span class="sxs-lookup"><span data-stu-id="2b851-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="2b851-173">Пользователь получает уведомления и сообщил об изменении пароля.</span><span class="sxs-lookup"><span data-stu-id="2b851-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="2b851-174">В следующем коде показано, как добавить пользователя:</span><span class="sxs-lookup"><span data-stu-id="2b851-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="2b851-175">Подобный подход можно выполнить для рабочих сценариев.</span><span class="sxs-lookup"><span data-stu-id="2b851-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="2b851-176">Запретить публикацию статических Identity ресурсов</span><span class="sxs-lookup"><span data-stu-id="2b851-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="2b851-177">Чтобы предотвратить публикацию Identity статических ресурсов в веб-корне <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>, см. раздел.</span><span class="sxs-lookup"><span data-stu-id="2b851-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2b851-178">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2b851-178">Additional resources</span></span>

* [<span data-ttu-id="2b851-179">Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="2b851-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2b851-180">ASP.NET Core 2,1 и более поздних версий предоставляет [ Identity ASP.NET Core](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="2b851-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="2b851-181">Приложения, которые Identity включают в себя, могут применять механизм формирования шаблонов для выборочного добавления исходного кода Identity Razor , содержащегося в библиотеке классов (РКЛ).</span><span class="sxs-lookup"><span data-stu-id="2b851-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="2b851-182">Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение.</span><span class="sxs-lookup"><span data-stu-id="2b851-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="2b851-183">Например, вы можете указать шаблону создать код, используемый при регистрации.</span><span class="sxs-lookup"><span data-stu-id="2b851-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="2b851-184">Созданный код имеет приоритет над тем же кодом в Identity РКЛ.</span><span class="sxs-lookup"><span data-stu-id="2b851-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="2b851-185">Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного идентификатора пользовательского интерфейса идентификации](#full).</span><span class="sxs-lookup"><span data-stu-id="2b851-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="2b851-186">Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов Identity для добавления пакета РКЛ.</span><span class="sxs-lookup"><span data-stu-id="2b851-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="2b851-187">Вы можете выбрать Identity код для создания.</span><span class="sxs-lookup"><span data-stu-id="2b851-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="2b851-188">Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс.</span><span class="sxs-lookup"><span data-stu-id="2b851-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="2b851-189">В этом документе объясняются шаги, необходимые для Identity завершения обновления формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="2b851-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="2b851-190">При запуске Identity шаблона в каталоге проекта создается файл *скаффолдингреадме. txt* .</span><span class="sxs-lookup"><span data-stu-id="2b851-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="2b851-191">Файл *скаффолдингреадме. txt* содержит общие инструкции о том, что необходимо для завершения обновления Identity формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="2b851-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="2b851-192">Этот документ содержит более полные инструкции, чем файл *скаффолдингреадме. txt* .</span><span class="sxs-lookup"><span data-stu-id="2b851-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="2b851-193">Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения.</span><span class="sxs-lookup"><span data-stu-id="2b851-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="2b851-194">Проверьте изменения после запуска Identity механизма формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="2b851-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="2b851-195">Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также Identityдругих функций безопасности с.</span><span class="sxs-lookup"><span data-stu-id="2b851-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="2b851-196">Службы или заглушки служб не создаются при формировании шаблонов Identity.</span><span class="sxs-lookup"><span data-stu-id="2b851-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="2b851-197">Службы для включения этих функций необходимо добавить вручную.</span><span class="sxs-lookup"><span data-stu-id="2b851-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="2b851-198">Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="2b851-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="2b851-199">Удостоверение шаблона в пустой проект</span><span class="sxs-lookup"><span data-stu-id="2b851-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="2b851-200">Добавьте следующие выделенные вызовы в `Startup` класс:</span><span class="sxs-lookup"><span data-stu-id="2b851-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="2b851-201">Удостоверение шаблона в Razor проекте без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="2b851-201">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="2b851-202">настраивается в *областиIdentity//IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="2b851-202"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2b851-203">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="2b851-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="2b851-204">Миграция, Усеаусентикатион и макет</span><span class="sxs-lookup"><span data-stu-id="2b851-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="2b851-205">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="2b851-205">Enable authentication</span></span>

<span data-ttu-id="2b851-206">В `Configure` `Startup` методе класса вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="2b851-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="2b851-207">Изменения макета</span><span class="sxs-lookup"><span data-stu-id="2b851-207">Layout changes</span></span>

<span data-ttu-id="2b851-208">Необязательно: Добавьте в файл макета`_LoginPartial`имя для входа partial ():</span><span class="sxs-lookup"><span data-stu-id="2b851-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="2b851-209">Удостоверение шаблона в Razor проекте с авторизацией</span><span class="sxs-lookup"><span data-stu-id="2b851-209">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="2b851-210">Некоторые Identity параметры настраиваются в *области/Identity/IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="2b851-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2b851-211">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="2b851-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="2b851-212">Удостоверение шаблона в проекте MVC без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="2b851-212">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="2b851-213">Необязательно: Добавьте часть имени для`_LoginPartial`входа partial () в файл *views/shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2b851-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="2b851-214">Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="2b851-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="2b851-215">настраивается в *областиIdentity//IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="2b851-215"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2b851-216">Дополнительные сведения см. в разделе IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="2b851-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="2b851-217">Вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="2b851-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="2b851-218">Удостоверение шаблона в проекте MVC с авторизацией</span><span class="sxs-lookup"><span data-stu-id="2b851-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="2b851-219">Удалите папки *pages/Shared* и файлы в этой папке.</span><span class="sxs-lookup"><span data-stu-id="2b851-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="2b851-220">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="2b851-220">Create full identity UI source</span></span>

<span data-ttu-id="2b851-221">Чтобы обеспечить полный контроль над Identity пользовательским интерфейсом, Identity запустите шаблон и выберите **переопределить все файлы**.</span><span class="sxs-lookup"><span data-stu-id="2b851-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="2b851-222">В следующем выделенном коде показаны изменения для замены пользовательского интерфейса Identity по умолчанию Identity в веб-приложении ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="2b851-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="2b851-223">Это может потребоваться для полного контроля над Identity пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="2b851-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="2b851-224">Значение по Identity умолчанию заменяется в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="2b851-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="2b851-225">Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="2b851-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="2b851-226">Зарегистрируйте `IEmailSender` реализацию, например:</span><span class="sxs-lookup"><span data-stu-id="2b851-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="2b851-227">Отключить страницу регистрации</span><span class="sxs-lookup"><span data-stu-id="2b851-227">Disable register page</span></span>

<span data-ttu-id="2b851-228">Чтобы отключить регистрацию пользователей, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="2b851-228">To disable user registration:</span></span>

* <span data-ttu-id="2b851-229">Шаблон Identity.</span><span class="sxs-lookup"><span data-stu-id="2b851-229">Scaffold Identity.</span></span> <span data-ttu-id="2b851-230">Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион.</span><span class="sxs-lookup"><span data-stu-id="2b851-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="2b851-231">Например:</span><span class="sxs-lookup"><span data-stu-id="2b851-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="2b851-232">Обновите *областиIdentityили/Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:</span><span class="sxs-lookup"><span data-stu-id="2b851-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="2b851-233">Обновите *областиIdentity//Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:</span><span class="sxs-lookup"><span data-stu-id="2b851-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="2b851-234">Закомментируйте или удалите регистрационную ссылку из *областейIdentity//Пажес/аккаунт/логин.кштмл*</span><span class="sxs-lookup"><span data-stu-id="2b851-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="2b851-235">Обновите страницу *областиIdentityили/Пажес/аккаунт/регистерконфирматион* .</span><span class="sxs-lookup"><span data-stu-id="2b851-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="2b851-236">Удалите код и ссылки из файла CSHTML.</span><span class="sxs-lookup"><span data-stu-id="2b851-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="2b851-237">Удалите код подтверждения из `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="2b851-237">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="2b851-238">Использование другого приложения для добавления пользователей</span><span class="sxs-lookup"><span data-stu-id="2b851-238">Use another app to add users</span></span>

<span data-ttu-id="2b851-239">Предоставьте механизм для добавления пользователей за пределами веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="2b851-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="2b851-240">Ниже перечислены параметры для добавления пользователей.</span><span class="sxs-lookup"><span data-stu-id="2b851-240">Options to add users include:</span></span>

* <span data-ttu-id="2b851-241">Выделенное административное веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="2b851-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="2b851-242">Консольное приложение.</span><span class="sxs-lookup"><span data-stu-id="2b851-242">A console app.</span></span>

<span data-ttu-id="2b851-243">В следующем примере кода один из подходов к добавлению пользователей:</span><span class="sxs-lookup"><span data-stu-id="2b851-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="2b851-244">Список пользователей считывается в память.</span><span class="sxs-lookup"><span data-stu-id="2b851-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="2b851-245">Для каждого пользователя создается надежный уникальный пароль.</span><span class="sxs-lookup"><span data-stu-id="2b851-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="2b851-246">Пользователь будет добавлен в Identity базу данных.</span><span class="sxs-lookup"><span data-stu-id="2b851-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="2b851-247">Пользователь получает уведомления и сообщил об изменении пароля.</span><span class="sxs-lookup"><span data-stu-id="2b851-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="2b851-248">В следующем коде показано, как добавить пользователя:</span><span class="sxs-lookup"><span data-stu-id="2b851-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="2b851-249">Подобный подход можно выполнить для рабочих сценариев.</span><span class="sxs-lookup"><span data-stu-id="2b851-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2b851-250">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2b851-250">Additional resources</span></span>

* [<span data-ttu-id="2b851-251">Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="2b851-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end