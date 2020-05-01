---
title: Удостоверение шаблона в ASP.NET Core проектах
author: rick-anderson
description: Узнайте, как сформировать удостоверение в проекте ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ac95035b114274ddaa6ccb0b5b6e3da98885e39e
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604731"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="21a1b-103">Удостоверение шаблона в ASP.NET Core проектах</span><span class="sxs-lookup"><span data-stu-id="21a1b-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="21a1b-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="21a1b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="21a1b-105">ASP.NET Core предоставляет [ASP.NET Core удостоверение](xref:security/authentication/identity) как [библиотеку классов Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="21a1b-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="21a1b-106">Приложения, включающие удостоверение, могут применять шаблон для выборочного добавления исходного кода, содержащегося в библиотеке классов Razor класса Identity (РКЛ).</span><span class="sxs-lookup"><span data-stu-id="21a1b-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="21a1b-107">Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение.</span><span class="sxs-lookup"><span data-stu-id="21a1b-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="21a1b-108">Например, вы можете указать шаблону создать код, используемый при регистрации.</span><span class="sxs-lookup"><span data-stu-id="21a1b-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="21a1b-109">Созданный код имеет приоритет над тем же кодом в RCL для идентификаторов.</span><span class="sxs-lookup"><span data-stu-id="21a1b-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="21a1b-110">Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного идентификатора пользовательского интерфейса идентификации](#full).</span><span class="sxs-lookup"><span data-stu-id="21a1b-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="21a1b-111">Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов для добавления пакета РКЛ Identity.</span><span class="sxs-lookup"><span data-stu-id="21a1b-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="21a1b-112">Вы можете выбрать, какой код идентификатора будет создан.</span><span class="sxs-lookup"><span data-stu-id="21a1b-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="21a1b-113">Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс.</span><span class="sxs-lookup"><span data-stu-id="21a1b-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="21a1b-114">В этом документе объясняются шаги, необходимые для завершения обновления формирования шаблонов удостоверений.</span><span class="sxs-lookup"><span data-stu-id="21a1b-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="21a1b-115">Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения.</span><span class="sxs-lookup"><span data-stu-id="21a1b-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="21a1b-116">Проверьте изменения после запуска шаблона удостоверений.</span><span class="sxs-lookup"><span data-stu-id="21a1b-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="21a1b-117">Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также других функций безопасности с удостоверениями.</span><span class="sxs-lookup"><span data-stu-id="21a1b-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="21a1b-118">Службы или заглушки служб не создаются при формировании удостоверений формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="21a1b-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="21a1b-119">Службы для включения этих функций необходимо добавить вручную.</span><span class="sxs-lookup"><span data-stu-id="21a1b-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="21a1b-120">Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="21a1b-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="21a1b-121">При формировании удостоверения с новым контекстом данных в проекте с существующими индивидуальными учетными записями:</span><span class="sxs-lookup"><span data-stu-id="21a1b-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="21a1b-122">В `Startup.ConfigureServices`удалите вызовы:</span><span class="sxs-lookup"><span data-stu-id="21a1b-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="21a1b-123">Например, `AddDbContext` и `AddDefaultIdentity` комментарии в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="21a1b-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="21a1b-124">Предшествующий код записывает в комментарий код, который дублируется в *области/Identity/идентитихостингстартуп. cs.*</span><span class="sxs-lookup"><span data-stu-id="21a1b-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="21a1b-125">Как правило, приложения, созданные с помощью отдельных учетных записей, ***не*** должны создавать новый контекст данных.</span><span class="sxs-lookup"><span data-stu-id="21a1b-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="21a1b-126">Удостоверение шаблона в пустой проект</span><span class="sxs-lookup"><span data-stu-id="21a1b-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="21a1b-127">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="21a1b-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="21a1b-128">Удостоверение шаблона в проекте Razor без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="21a1b-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="21a1b-129">Удостоверение настраивается в *области/Identity/идентитихостингстартуп. CS*.</span><span class="sxs-lookup"><span data-stu-id="21a1b-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21a1b-130">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="21a1b-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="21a1b-131">Миграция, Усеаусентикатион и макет</span><span class="sxs-lookup"><span data-stu-id="21a1b-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="21a1b-132">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="21a1b-132">Enable authentication</span></span>

<span data-ttu-id="21a1b-133">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="21a1b-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="21a1b-134">Изменения макета</span><span class="sxs-lookup"><span data-stu-id="21a1b-134">Layout changes</span></span>

<span data-ttu-id="21a1b-135">Необязательно: Добавьте в файл макета`_LoginPartial`имя для входа partial ():</span><span class="sxs-lookup"><span data-stu-id="21a1b-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="21a1b-136">Удостоверение шаблона в проекте Razor с авторизацией</span><span class="sxs-lookup"><span data-stu-id="21a1b-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="21a1b-137">Некоторые параметры удостоверения настраиваются в *области/Identity/идентитихостингстартуп. CS*.</span><span class="sxs-lookup"><span data-stu-id="21a1b-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21a1b-138">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="21a1b-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="21a1b-139">Удостоверение шаблона в проекте MVC без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="21a1b-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="21a1b-140">Необязательно: Добавьте часть имени для`_LoginPartial`входа partial () в файл *views/shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21a1b-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="21a1b-141">Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21a1b-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="21a1b-142">Удостоверение настраивается в *области/Identity/идентитихостингстартуп. CS*.</span><span class="sxs-lookup"><span data-stu-id="21a1b-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21a1b-143">Дополнительные сведения см. в разделе IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="21a1b-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="21a1b-144">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="21a1b-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="21a1b-145">Удостоверение шаблона в проекте MVC с авторизацией</span><span class="sxs-lookup"><span data-stu-id="21a1b-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="21a1b-146">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="21a1b-146">Create full identity UI source</span></span>

<span data-ttu-id="21a1b-147">Чтобы обеспечить полный контроль над пользовательским интерфейсом удостоверений, запустите механизм формирования удостоверений и выберите **переопределить все файлы**.</span><span class="sxs-lookup"><span data-stu-id="21a1b-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="21a1b-148">В следующем выделенном коде показаны изменения, которые заменяют пользовательский интерфейс удостоверения по умолчанию удостоверением в веб-приложении ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="21a1b-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="21a1b-149">Это может потребоваться для полного управления пользовательским интерфейсом идентификации.</span><span class="sxs-lookup"><span data-stu-id="21a1b-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="21a1b-150">Удостоверение по умолчанию заменяется в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="21a1b-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="21a1b-151">Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="21a1b-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="21a1b-152">Зарегистрируйте `IEmailSender` реализацию, например:</span><span class="sxs-lookup"><span data-stu-id="21a1b-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="21a1b-153">Отключить страницу регистрации</span><span class="sxs-lookup"><span data-stu-id="21a1b-153">Disable register page</span></span>

<span data-ttu-id="21a1b-154">Чтобы отключить регистрацию пользователей, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="21a1b-154">To disable user registration:</span></span>

* <span data-ttu-id="21a1b-155">Удостоверение шаблона.</span><span class="sxs-lookup"><span data-stu-id="21a1b-155">Scaffold Identity.</span></span> <span data-ttu-id="21a1b-156">Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион.</span><span class="sxs-lookup"><span data-stu-id="21a1b-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="21a1b-157">Пример:</span><span class="sxs-lookup"><span data-stu-id="21a1b-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="21a1b-158">Обновите *области/идентификаторы/страницы/учетная запись/Register. cshtml. CS* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:</span><span class="sxs-lookup"><span data-stu-id="21a1b-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="21a1b-159">Обновите *области, идентификаторы, страницы, учетную запись или Register. cshtml* , чтобы они были совместимы с предыдущими изменениями:</span><span class="sxs-lookup"><span data-stu-id="21a1b-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="21a1b-160">Закомментируйте или удалите ссылку регистрации из *областей, удостоверений, страниц, учетной записи или имени входа. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21a1b-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="21a1b-161">Обновите страницу *области, удостоверение, страницы/учетная запись или регистерконфирматион* .</span><span class="sxs-lookup"><span data-stu-id="21a1b-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="21a1b-162">Удалите код и ссылки из файла CSHTML.</span><span class="sxs-lookup"><span data-stu-id="21a1b-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="21a1b-163">Удалите код подтверждения из `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="21a1b-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="21a1b-164">Использование другого приложения для добавления пользователей</span><span class="sxs-lookup"><span data-stu-id="21a1b-164">Use another app to add users</span></span>

<span data-ttu-id="21a1b-165">Предоставьте механизм для добавления пользователей за пределами веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="21a1b-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="21a1b-166">Ниже перечислены параметры для добавления пользователей.</span><span class="sxs-lookup"><span data-stu-id="21a1b-166">Options to add users include:</span></span>

* <span data-ttu-id="21a1b-167">Выделенное административное веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="21a1b-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="21a1b-168">Консольное приложение.</span><span class="sxs-lookup"><span data-stu-id="21a1b-168">A console app.</span></span>

<span data-ttu-id="21a1b-169">В следующем примере кода один из подходов к добавлению пользователей:</span><span class="sxs-lookup"><span data-stu-id="21a1b-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="21a1b-170">Список пользователей считывается в память.</span><span class="sxs-lookup"><span data-stu-id="21a1b-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="21a1b-171">Для каждого пользователя создается надежный уникальный пароль.</span><span class="sxs-lookup"><span data-stu-id="21a1b-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="21a1b-172">Пользователь будет добавлен в базу данных удостоверений.</span><span class="sxs-lookup"><span data-stu-id="21a1b-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="21a1b-173">Пользователь получает уведомления и сообщил об изменении пароля.</span><span class="sxs-lookup"><span data-stu-id="21a1b-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="21a1b-174">В следующем коде показано, как добавить пользователя:</span><span class="sxs-lookup"><span data-stu-id="21a1b-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="21a1b-175">Подобный подход можно выполнить для рабочих сценариев.</span><span class="sxs-lookup"><span data-stu-id="21a1b-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="21a1b-176">Запретить публикацию ресурсов статических удостоверений</span><span class="sxs-lookup"><span data-stu-id="21a1b-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="21a1b-177">Сведения о запрете публикации ресурсов статических удостоверений в корневом веб-каталоге см. в разделе <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span><span class="sxs-lookup"><span data-stu-id="21a1b-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21a1b-178">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="21a1b-178">Additional resources</span></span>

* [<span data-ttu-id="21a1b-179">Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="21a1b-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="21a1b-180">ASP.NET Core 2,1 и более поздних версий предоставляет [ASP.NET Core удостоверение](xref:security/authentication/identity) как [библиотеку классов Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="21a1b-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="21a1b-181">Приложения, включающие удостоверение, могут применять шаблон для выборочного добавления исходного кода, содержащегося в библиотеке классов Razor класса Identity (РКЛ).</span><span class="sxs-lookup"><span data-stu-id="21a1b-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="21a1b-182">Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение.</span><span class="sxs-lookup"><span data-stu-id="21a1b-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="21a1b-183">Например, вы можете указать шаблону создать код, используемый при регистрации.</span><span class="sxs-lookup"><span data-stu-id="21a1b-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="21a1b-184">Созданный код имеет приоритет над тем же кодом в RCL для идентификаторов.</span><span class="sxs-lookup"><span data-stu-id="21a1b-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="21a1b-185">Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного идентификатора пользовательского интерфейса идентификации](#full).</span><span class="sxs-lookup"><span data-stu-id="21a1b-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="21a1b-186">Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов для добавления пакета РКЛ Identity.</span><span class="sxs-lookup"><span data-stu-id="21a1b-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="21a1b-187">Вы можете выбрать, какой код идентификатора будет создан.</span><span class="sxs-lookup"><span data-stu-id="21a1b-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="21a1b-188">Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс.</span><span class="sxs-lookup"><span data-stu-id="21a1b-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="21a1b-189">В этом документе объясняются шаги, необходимые для завершения обновления формирования шаблонов удостоверений.</span><span class="sxs-lookup"><span data-stu-id="21a1b-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="21a1b-190">При запуске механизма формирования идентификаторов в каталоге проекта создается файл *скаффолдингреадме. txt* .</span><span class="sxs-lookup"><span data-stu-id="21a1b-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="21a1b-191">Файл *скаффолдингреадме. txt* содержит общие инструкции о том, что необходимо для завершения обновления формирования шаблонов удостоверений.</span><span class="sxs-lookup"><span data-stu-id="21a1b-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="21a1b-192">Этот документ содержит более полные инструкции, чем файл *скаффолдингреадме. txt* .</span><span class="sxs-lookup"><span data-stu-id="21a1b-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="21a1b-193">Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения.</span><span class="sxs-lookup"><span data-stu-id="21a1b-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="21a1b-194">Проверьте изменения после запуска шаблона удостоверений.</span><span class="sxs-lookup"><span data-stu-id="21a1b-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="21a1b-195">Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также других функций безопасности с удостоверениями.</span><span class="sxs-lookup"><span data-stu-id="21a1b-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="21a1b-196">Службы или заглушки служб не создаются при формировании удостоверений формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="21a1b-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="21a1b-197">Службы для включения этих функций необходимо добавить вручную.</span><span class="sxs-lookup"><span data-stu-id="21a1b-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="21a1b-198">Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="21a1b-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="21a1b-199">Удостоверение шаблона в пустой проект</span><span class="sxs-lookup"><span data-stu-id="21a1b-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="21a1b-200">Добавьте следующие выделенные вызовы в `Startup` класс:</span><span class="sxs-lookup"><span data-stu-id="21a1b-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="21a1b-201">Удостоверение шаблона в проекте Razor без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="21a1b-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="21a1b-202">Удостоверение настраивается в *области/Identity/идентитихостингстартуп. CS*.</span><span class="sxs-lookup"><span data-stu-id="21a1b-202">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21a1b-203">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="21a1b-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="21a1b-204">Миграция, Усеаусентикатион и макет</span><span class="sxs-lookup"><span data-stu-id="21a1b-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="21a1b-205">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="21a1b-205">Enable authentication</span></span>

<span data-ttu-id="21a1b-206">В `Configure` `Startup` методе класса вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="21a1b-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="21a1b-207">Изменения макета</span><span class="sxs-lookup"><span data-stu-id="21a1b-207">Layout changes</span></span>

<span data-ttu-id="21a1b-208">Необязательно: Добавьте в файл макета`_LoginPartial`имя для входа partial ():</span><span class="sxs-lookup"><span data-stu-id="21a1b-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="21a1b-209">Удостоверение шаблона в проекте Razor с авторизацией</span><span class="sxs-lookup"><span data-stu-id="21a1b-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="21a1b-210">Некоторые параметры удостоверения настраиваются в *области/Identity/идентитихостингстартуп. CS*.</span><span class="sxs-lookup"><span data-stu-id="21a1b-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21a1b-211">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="21a1b-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="21a1b-212">Удостоверение шаблона в проекте MVC без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="21a1b-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="21a1b-213">Необязательно: Добавьте часть имени для`_LoginPartial`входа partial () в файл *views/shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21a1b-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="21a1b-214">Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21a1b-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="21a1b-215">Удостоверение настраивается в *области/Identity/идентитихостингстартуп. CS*.</span><span class="sxs-lookup"><span data-stu-id="21a1b-215">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21a1b-216">Дополнительные сведения см. в разделе IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="21a1b-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="21a1b-217">Вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="21a1b-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="21a1b-218">Удостоверение шаблона в проекте MVC с авторизацией</span><span class="sxs-lookup"><span data-stu-id="21a1b-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="21a1b-219">Удалите папки *pages/Shared* и файлы в этой папке.</span><span class="sxs-lookup"><span data-stu-id="21a1b-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="21a1b-220">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="21a1b-220">Create full identity UI source</span></span>

<span data-ttu-id="21a1b-221">Чтобы обеспечить полный контроль над пользовательским интерфейсом удостоверений, запустите механизм формирования удостоверений и выберите **переопределить все файлы**.</span><span class="sxs-lookup"><span data-stu-id="21a1b-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="21a1b-222">В следующем выделенном коде показаны изменения, которые заменяют пользовательский интерфейс удостоверения по умолчанию удостоверением в веб-приложении ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="21a1b-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="21a1b-223">Это может потребоваться для полного управления пользовательским интерфейсом идентификации.</span><span class="sxs-lookup"><span data-stu-id="21a1b-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="21a1b-224">Удостоверение по умолчанию заменяется в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="21a1b-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="21a1b-225">Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="21a1b-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="21a1b-226">Зарегистрируйте `IEmailSender` реализацию, например:</span><span class="sxs-lookup"><span data-stu-id="21a1b-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="21a1b-227">Отключить страницу регистрации</span><span class="sxs-lookup"><span data-stu-id="21a1b-227">Disable register page</span></span>

<span data-ttu-id="21a1b-228">Чтобы отключить регистрацию пользователей, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="21a1b-228">To disable user registration:</span></span>

* <span data-ttu-id="21a1b-229">Удостоверение шаблона.</span><span class="sxs-lookup"><span data-stu-id="21a1b-229">Scaffold Identity.</span></span> <span data-ttu-id="21a1b-230">Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион.</span><span class="sxs-lookup"><span data-stu-id="21a1b-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="21a1b-231">Пример:</span><span class="sxs-lookup"><span data-stu-id="21a1b-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="21a1b-232">Обновите *области/идентификаторы/страницы/учетная запись/Register. cshtml. CS* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:</span><span class="sxs-lookup"><span data-stu-id="21a1b-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="21a1b-233">Обновите *области, идентификаторы, страницы, учетную запись или Register. cshtml* , чтобы они были совместимы с предыдущими изменениями:</span><span class="sxs-lookup"><span data-stu-id="21a1b-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="21a1b-234">Закомментируйте или удалите ссылку регистрации из *областей, удостоверений, страниц, учетной записи или имени входа. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21a1b-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="21a1b-235">Обновите страницу *области, удостоверение, страницы/учетная запись или регистерконфирматион* .</span><span class="sxs-lookup"><span data-stu-id="21a1b-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="21a1b-236">Удалите код и ссылки из файла CSHTML.</span><span class="sxs-lookup"><span data-stu-id="21a1b-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="21a1b-237">Удалите код подтверждения из `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="21a1b-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="21a1b-238">Использование другого приложения для добавления пользователей</span><span class="sxs-lookup"><span data-stu-id="21a1b-238">Use another app to add users</span></span>

<span data-ttu-id="21a1b-239">Предоставьте механизм для добавления пользователей за пределами веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="21a1b-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="21a1b-240">Ниже перечислены параметры для добавления пользователей.</span><span class="sxs-lookup"><span data-stu-id="21a1b-240">Options to add users include:</span></span>

* <span data-ttu-id="21a1b-241">Выделенное административное веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="21a1b-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="21a1b-242">Консольное приложение.</span><span class="sxs-lookup"><span data-stu-id="21a1b-242">A console app.</span></span>

<span data-ttu-id="21a1b-243">В следующем примере кода один из подходов к добавлению пользователей:</span><span class="sxs-lookup"><span data-stu-id="21a1b-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="21a1b-244">Список пользователей считывается в память.</span><span class="sxs-lookup"><span data-stu-id="21a1b-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="21a1b-245">Для каждого пользователя создается надежный уникальный пароль.</span><span class="sxs-lookup"><span data-stu-id="21a1b-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="21a1b-246">Пользователь будет добавлен в базу данных удостоверений.</span><span class="sxs-lookup"><span data-stu-id="21a1b-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="21a1b-247">Пользователь получает уведомления и сообщил об изменении пароля.</span><span class="sxs-lookup"><span data-stu-id="21a1b-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="21a1b-248">В следующем коде показано, как добавить пользователя:</span><span class="sxs-lookup"><span data-stu-id="21a1b-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="21a1b-249">Подобный подход можно выполнить для рабочих сценариев.</span><span class="sxs-lookup"><span data-stu-id="21a1b-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21a1b-250">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="21a1b-250">Additional resources</span></span>

* [<span data-ttu-id="21a1b-251">Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="21a1b-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end