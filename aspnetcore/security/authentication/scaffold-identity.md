---
title: Формирование шаблонов Identity в ASP.NET Core проектах
author: rick-anderson
description: Сведения о формировании шаблонов Identity в проекте ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 155bdfbeea06022d35bbb551d5b2d0ee5a51a093
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400821"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="3011e-103">Формирование шаблонов Identity в ASP.NET Core проектах</span><span class="sxs-lookup"><span data-stu-id="3011e-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="3011e-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="3011e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3011e-105">ASP.NET Core предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="3011e-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="3011e-106">Приложения, которые включают в себя, Identity могут применять механизм формирования шаблонов для выборочного добавления исходного кода, содержащегося в Identity Razor библиотеке классов (РКЛ).</span><span class="sxs-lookup"><span data-stu-id="3011e-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="3011e-107">Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение.</span><span class="sxs-lookup"><span data-stu-id="3011e-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="3011e-108">Например, вы можете указать шаблону создать код, используемый при регистрации.</span><span class="sxs-lookup"><span data-stu-id="3011e-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="3011e-109">Созданный код имеет приоритет над тем же кодом в RCL для Identity.</span><span class="sxs-lookup"><span data-stu-id="3011e-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="3011e-110">Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного Identity источника пользовательского интерфейса](#full).</span><span class="sxs-lookup"><span data-stu-id="3011e-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="3011e-111">Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов для добавления Identity пакета РКЛ.</span><span class="sxs-lookup"><span data-stu-id="3011e-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="3011e-112">Вы можете выбрать, какой код Identity будет создан.</span><span class="sxs-lookup"><span data-stu-id="3011e-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="3011e-113">Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс.</span><span class="sxs-lookup"><span data-stu-id="3011e-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="3011e-114">В этом документе объясняются шаги, необходимые для завершения Identity обновления формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="3011e-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="3011e-115">Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения.</span><span class="sxs-lookup"><span data-stu-id="3011e-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="3011e-116">Проверьте изменения после запуска Identity механизма формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="3011e-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="3011e-117">Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также других функций безопасности с Identity .</span><span class="sxs-lookup"><span data-stu-id="3011e-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="3011e-118">Службы или заглушки служб не создаются при формировании шаблонов Identity .</span><span class="sxs-lookup"><span data-stu-id="3011e-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="3011e-119">Службы для включения этих функций необходимо добавить вручную.</span><span class="sxs-lookup"><span data-stu-id="3011e-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="3011e-120">Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="3011e-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="3011e-121">При формировании шаблона Identity с новым контекстом данных в проекте с существующими индивидуальными учетными записями:</span><span class="sxs-lookup"><span data-stu-id="3011e-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="3011e-122">В `Startup.ConfigureServices` удалите вызовы:</span><span class="sxs-lookup"><span data-stu-id="3011e-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="3011e-123">Например, `AddDbContext` и `AddDefaultIdentity` комментарии в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="3011e-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="3011e-124">Предшествующий код записывает в комментарий код, который дублируется в *области/ Identity /IdentityHostingStartup.CS*</span><span class="sxs-lookup"><span data-stu-id="3011e-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="3011e-125">Как правило, приложения, созданные с помощью отдельных учетных записей, ***не*** должны создавать новый контекст данных.</span><span class="sxs-lookup"><span data-stu-id="3011e-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="3011e-126">Формирование шаблона Identity в пустой проект</span><span class="sxs-lookup"><span data-stu-id="3011e-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="3011e-127">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="3011e-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="3011e-128">Формирование шаблонов Identity в Razor проекте без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="3011e-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="3011e-129">настраивается в *области/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3011e-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3011e-130">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3011e-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="3011e-131">Миграция, Усеаусентикатион и макет</span><span class="sxs-lookup"><span data-stu-id="3011e-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="3011e-132">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="3011e-132">Enable authentication</span></span>

<span data-ttu-id="3011e-133">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="3011e-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="3011e-134">Изменения макета</span><span class="sxs-lookup"><span data-stu-id="3011e-134">Layout changes</span></span>

<span data-ttu-id="3011e-135">Необязательно: Добавьте в файл макета имя для входа partial ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="3011e-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="3011e-136">Формирование шаблонов Identity в Razor проекте с авторизацией</span><span class="sxs-lookup"><span data-stu-id="3011e-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="3011e-137">Некоторые Identity параметры настраиваются в *области/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3011e-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3011e-138">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3011e-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="3011e-139">Формирование шаблонов Identity в проекте MVC без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="3011e-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="3011e-140">Необязательно: Добавьте часть имени для входа partial ( `_LoginPartial` ) в файл *views/Shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3011e-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="3011e-141">Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3011e-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="3011e-142">настраивается в *области/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3011e-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3011e-143">Дополнительные сведения см. в разделе IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="3011e-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="3011e-144">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="3011e-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="3011e-145">Формирование шаблонов Identity в проекте MVC с авторизацией</span><span class="sxs-lookup"><span data-stu-id="3011e-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="3011e-146">Формирование шаблонов Identity в Blazor Server проекте без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="3011e-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="3011e-147">настраивается в *области/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3011e-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3011e-148">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3011e-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="3011e-149">Миграции</span><span class="sxs-lookup"><span data-stu-id="3011e-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="3011e-150">Передача токена XSRF в приложение</span><span class="sxs-lookup"><span data-stu-id="3011e-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="3011e-151">Маркеры можно передавать в компоненты:</span><span class="sxs-lookup"><span data-stu-id="3011e-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="3011e-152">Когда маркеры проверки подлинности подготавливаются и сохраняются в файле cookie проверки подлинности, они могут передаваться компонентам.</span><span class="sxs-lookup"><span data-stu-id="3011e-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="3011e-153">компоненты не могут использовать `HttpContext` напрямую, поэтому не существует способа получить [маркер подделки для защиты от запроса (XSRF)](xref:security/anti-request-forgery) для размещения Identity конечной точки выхода в `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="3011e-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="3011e-154">Токен XSRF может быть передан компонентам.</span><span class="sxs-lookup"><span data-stu-id="3011e-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="3011e-155">Для получения дополнительной информации см. <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="3011e-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="3011e-156">В файле *pages/_Host. cshtml* Установите маркер после его добавления в `InitialApplicationState` `TokenProvider` классы и:</span><span class="sxs-lookup"><span data-stu-id="3011e-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="3011e-157">Обновите `App` компонент (*app. Razor*), чтобы назначить `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="3011e-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="3011e-158">`TokenProvider`Служба, показанная в разделе, используется в `LoginDisplay` компоненте в следующем разделе [Макет и изменения потока проверки подлинности](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="3011e-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="3011e-159">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="3011e-159">Enable authentication</span></span>

<span data-ttu-id="3011e-160">В `Startup` классе:</span><span class="sxs-lookup"><span data-stu-id="3011e-160">In the `Startup` class:</span></span>

* <span data-ttu-id="3011e-161">Убедитесь, что Razor службы страниц добавлены в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3011e-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="3011e-162">При использовании [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)Зарегистрируйте службу.</span><span class="sxs-lookup"><span data-stu-id="3011e-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="3011e-163">Вызовите в `UseDatabaseErrorPage` построителе приложений в `Startup.Configure` среде разработки.</span><span class="sxs-lookup"><span data-stu-id="3011e-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="3011e-164">Вызовите `UseAuthentication` и `UseAuthorization` после `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="3011e-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="3011e-165">Добавление конечной точки для Razor страниц.</span><span class="sxs-lookup"><span data-stu-id="3011e-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="3011e-166">Изменения в потоке макета и проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="3011e-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="3011e-167">Добавьте `RedirectToLogin` компонент (*редиректтологин. Razor*) в *общую* папку приложения в корневом каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="3011e-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Добавьте `LoginDisplay` компонент (*логиндисплай. Razor*) в *общую* папку приложения. <span data-ttu-id="3011e-169">[Служба TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) предоставляет маркер XSRF для HTML-формы, которая отправляет в Identity конечную точку выхода.</span><span class="sxs-lookup"><span data-stu-id="3011e-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="3011e-170">В `MainLayout` компоненте (*Shared/маинлайаут. Razor*) добавьте `LoginDisplay` компонент в `<div>` содержимое элемента верхней строки:</span><span class="sxs-lookup"><span data-stu-id="3011e-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="3011e-171">Конечные точки проверки подлинности стиля</span><span class="sxs-lookup"><span data-stu-id="3011e-171">Style authentication endpoints</span></span>

<span data-ttu-id="3011e-172">Поскольку Blazor Server использует Razor Identity страницы страниц, стиль пользовательского интерфейса изменяется при переходе посетителя между Identity страницами и компонентами.</span><span class="sxs-lookup"><span data-stu-id="3011e-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="3011e-173">Существует два варианта для адресации стилей инконгруаус:</span><span class="sxs-lookup"><span data-stu-id="3011e-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="3011e-174">IdentityКомпоненты сборки</span><span class="sxs-lookup"><span data-stu-id="3011e-174">Build Identity components</span></span>

<span data-ttu-id="3011e-175">Подход к использованию компонентов Identity вместо страниц заключается в создании Identity компонентов.</span><span class="sxs-lookup"><span data-stu-id="3011e-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="3011e-176">Поскольку `SignInManager` и `UserManager` не поддерживаются в Razor компонентах, используйте конечные точки API в Blazor Server приложении для обработки действий с учетной записью пользователя.</span><span class="sxs-lookup"><span data-stu-id="3011e-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="3011e-177">Использование пользовательского макета с Blazor стилями приложения</span><span class="sxs-lookup"><span data-stu-id="3011e-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="3011e-178">IdentityМакет и стили страниц можно изменить для создания страниц, использующих тему по умолчанию Blazor .</span><span class="sxs-lookup"><span data-stu-id="3011e-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="3011e-179">Пример в этом разделе является просто начальной точкой для настройки.</span><span class="sxs-lookup"><span data-stu-id="3011e-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="3011e-180">Для лучшего взаимодействия с пользователем, скорее всего, потребуется дополнительная работа.</span><span class="sxs-lookup"><span data-stu-id="3011e-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="3011e-181">Создайте новый `NavMenu_IdentityLayout` компонент (*Shared/NavMenu_IdentityLayout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="3011e-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="3011e-182">Для разметки и кода компонента используйте то же содержимое `NavMenu` компонента приложения (*Shared/навмену. Razor*).</span><span class="sxs-lookup"><span data-stu-id="3011e-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="3011e-183">Вывлекайте все `NavLink` компоненты, которые не могут быть анонимно подключены, так как автоматическое перенаправление в `RedirectToLogin` компоненте не выполняется для компонентов, нуждающихся в проверке подлинности или авторизации.</span><span class="sxs-lookup"><span data-stu-id="3011e-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="3011e-184">В файле *pages/Shared/Layout. cshtml* внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="3011e-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="3011e-185">Добавьте Razor директивы в начало файла, чтобы использовать вспомогательные функции тегов и компоненты приложения в *общей* папке:</span><span class="sxs-lookup"><span data-stu-id="3011e-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="3011e-186">Замените `{APPLICATION ASSEMBLY}` на имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="3011e-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="3011e-187">Добавьте `<base>` тег и Blazor таблицу стилей `<link>` к `<head>` содержимому:</span><span class="sxs-lookup"><span data-stu-id="3011e-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="3011e-188">Измените содержимое `<body>` тега следующим образом:</span><span class="sxs-lookup"><span data-stu-id="3011e-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="3011e-189">Формирование шаблонов Identity в Blazor Server проекте с авторизацией</span><span class="sxs-lookup"><span data-stu-id="3011e-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="3011e-190">Некоторые Identity параметры настраиваются в *области/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3011e-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3011e-191">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3011e-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="3011e-192">Создание полного Identity источника пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="3011e-192">Create full Identity UI source</span></span>

<span data-ttu-id="3011e-193">Чтобы обеспечить полный контроль над Identity пользовательским интерфейсом, запустите шаблон Identity и выберите **переопределить все файлы**.</span><span class="sxs-lookup"><span data-stu-id="3011e-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="3011e-194">В следующем выделенном коде показаны изменения для замены пользовательского интерфейса по умолчанию Identity Identity в веб-приложении ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="3011e-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="3011e-195">Это может потребоваться для полного контроля над Identity пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="3011e-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="3011e-196">Значение по умолчанию Identity заменяется в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="3011e-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="3011e-197">Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="3011e-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="3011e-198">Зарегистрируйте `IEmailSender` реализацию, например:</span><span class="sxs-lookup"><span data-stu-id="3011e-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-a-page"></a><span data-ttu-id="3011e-199">Отключение страницы</span><span class="sxs-lookup"><span data-stu-id="3011e-199">Disable a page</span></span>

<span data-ttu-id="3011e-200">В этом разделе показано, как отключить страницу Register, но подход можно использовать для отключения любой страницы.</span><span class="sxs-lookup"><span data-stu-id="3011e-200">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="3011e-201">Чтобы отключить регистрацию пользователей, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="3011e-201">To disable user registration:</span></span>

* <span data-ttu-id="3011e-202">Шаблон Identity .</span><span class="sxs-lookup"><span data-stu-id="3011e-202">Scaffold Identity.</span></span> <span data-ttu-id="3011e-203">Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион.</span><span class="sxs-lookup"><span data-stu-id="3011e-203">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="3011e-204">Пример.</span><span class="sxs-lookup"><span data-stu-id="3011e-204">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="3011e-205">Обновите *области или Identity /Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:</span><span class="sxs-lookup"><span data-stu-id="3011e-205">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="3011e-206">Обновите *области/ Identity /Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:</span><span class="sxs-lookup"><span data-stu-id="3011e-206">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="3011e-207">Закомментируйте или удалите регистрационную ссылку из *областей/ Identity /Пажес/аккаунт/логин.кштмл*</span><span class="sxs-lookup"><span data-stu-id="3011e-207">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="3011e-208">Обновите страницу *области или Identity /Пажес/аккаунт/регистерконфирматион* .</span><span class="sxs-lookup"><span data-stu-id="3011e-208">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="3011e-209">Удалите код и ссылки из файла CSHTML.</span><span class="sxs-lookup"><span data-stu-id="3011e-209">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="3011e-210">Удалите код подтверждения из `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="3011e-210">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="3011e-211">Использование другого приложения для добавления пользователей</span><span class="sxs-lookup"><span data-stu-id="3011e-211">Use another app to add users</span></span>

<span data-ttu-id="3011e-212">Предоставьте механизм для добавления пользователей за пределами веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="3011e-212">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="3011e-213">Ниже перечислены параметры для добавления пользователей.</span><span class="sxs-lookup"><span data-stu-id="3011e-213">Options to add users include:</span></span>

* <span data-ttu-id="3011e-214">Выделенное административное веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="3011e-214">A dedicated admin web app.</span></span>
* <span data-ttu-id="3011e-215">Консольное приложение.</span><span class="sxs-lookup"><span data-stu-id="3011e-215">A console app.</span></span>

<span data-ttu-id="3011e-216">В следующем примере кода один из подходов к добавлению пользователей:</span><span class="sxs-lookup"><span data-stu-id="3011e-216">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="3011e-217">Список пользователей считывается в память.</span><span class="sxs-lookup"><span data-stu-id="3011e-217">A list of users is read into memory.</span></span>
* <span data-ttu-id="3011e-218">Для каждого пользователя создается надежный уникальный пароль.</span><span class="sxs-lookup"><span data-stu-id="3011e-218">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="3011e-219">Пользователь будет добавлен в Identity базу данных.</span><span class="sxs-lookup"><span data-stu-id="3011e-219">The user is added to the Identity database.</span></span>
* <span data-ttu-id="3011e-220">Пользователь получает уведомления и сообщил об изменении пароля.</span><span class="sxs-lookup"><span data-stu-id="3011e-220">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="3011e-221">В следующем коде показано, как добавить пользователя:</span><span class="sxs-lookup"><span data-stu-id="3011e-221">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="3011e-222">Подобный подход можно выполнить для рабочих сценариев.</span><span class="sxs-lookup"><span data-stu-id="3011e-222">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="3011e-223">Запретить публикацию статических Identity ресурсов</span><span class="sxs-lookup"><span data-stu-id="3011e-223">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="3011e-224">Чтобы предотвратить публикацию статических Identity ресурсов в веб-корне, см <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> . раздел.</span><span class="sxs-lookup"><span data-stu-id="3011e-224">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3011e-225">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3011e-225">Additional resources</span></span>

* [<span data-ttu-id="3011e-226">Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="3011e-226">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3011e-227">ASP.NET Core 2,1 и более поздних версий предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="3011e-227">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="3011e-228">Приложения, которые включают в себя, Identity могут применять механизм формирования шаблонов для выборочного добавления исходного кода, содержащегося в Identity Razor библиотеке классов (РКЛ).</span><span class="sxs-lookup"><span data-stu-id="3011e-228">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="3011e-229">Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение.</span><span class="sxs-lookup"><span data-stu-id="3011e-229">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="3011e-230">Например, вы можете указать шаблону создать код, используемый при регистрации.</span><span class="sxs-lookup"><span data-stu-id="3011e-230">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="3011e-231">Созданный код имеет приоритет над тем же кодом в RCL для Identity.</span><span class="sxs-lookup"><span data-stu-id="3011e-231">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="3011e-232">Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного идентификатора пользовательского интерфейса идентификации](#full).</span><span class="sxs-lookup"><span data-stu-id="3011e-232">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="3011e-233">Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов для добавления Identity пакета РКЛ.</span><span class="sxs-lookup"><span data-stu-id="3011e-233">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="3011e-234">Вы можете выбрать, какой код Identity будет создан.</span><span class="sxs-lookup"><span data-stu-id="3011e-234">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="3011e-235">Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс.</span><span class="sxs-lookup"><span data-stu-id="3011e-235">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="3011e-236">В этом документе объясняются шаги, необходимые для завершения Identity обновления формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="3011e-236">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="3011e-237">При Identity запуске шаблона создается файл *ScaffoldingReadme.txt* в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="3011e-237">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="3011e-238">Файл *ScaffoldingReadme.txt* содержит общие инструкции о том, что необходимо для завершения Identity обновления формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="3011e-238">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="3011e-239">Этот документ содержит более полные инструкции, чем файл *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="3011e-239">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="3011e-240">Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения.</span><span class="sxs-lookup"><span data-stu-id="3011e-240">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="3011e-241">Проверьте изменения после запуска Identity механизма формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="3011e-241">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="3011e-242">Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также других функций безопасности с Identity .</span><span class="sxs-lookup"><span data-stu-id="3011e-242">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="3011e-243">Службы или заглушки служб не создаются при формировании шаблонов Identity .</span><span class="sxs-lookup"><span data-stu-id="3011e-243">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="3011e-244">Службы для включения этих функций необходимо добавить вручную.</span><span class="sxs-lookup"><span data-stu-id="3011e-244">Services to enable these features must be added manually.</span></span> <span data-ttu-id="3011e-245">Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="3011e-245">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="3011e-246">Формирование шаблона Identity в пустой проект</span><span class="sxs-lookup"><span data-stu-id="3011e-246">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="3011e-247">Добавьте следующие выделенные вызовы в `Startup` класс:</span><span class="sxs-lookup"><span data-stu-id="3011e-247">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="3011e-248">Формирование шаблонов Identity в Razor проекте без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="3011e-248">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="3011e-249">настраивается в *области/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3011e-249"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3011e-250">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3011e-250">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="3011e-251">Миграция, Усеаусентикатион и макет</span><span class="sxs-lookup"><span data-stu-id="3011e-251">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="3011e-252">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="3011e-252">Enable authentication</span></span>

<span data-ttu-id="3011e-253">В `Configure` методе `Startup` класса вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="3011e-253">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="3011e-254">Изменения макета</span><span class="sxs-lookup"><span data-stu-id="3011e-254">Layout changes</span></span>

<span data-ttu-id="3011e-255">Необязательно: Добавьте в файл макета имя для входа partial ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="3011e-255">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="3011e-256">Формирование шаблонов Identity в Razor проекте с авторизацией</span><span class="sxs-lookup"><span data-stu-id="3011e-256">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="3011e-257">Некоторые Identity параметры настраиваются в *области/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3011e-257">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3011e-258">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3011e-258">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="3011e-259">Формирование шаблонов Identity в проекте MVC без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="3011e-259">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="3011e-260">Необязательно: Добавьте часть имени для входа partial ( `_LoginPartial` ) в файл *views/Shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3011e-260">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="3011e-261">Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3011e-261">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="3011e-262">настраивается в *области/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3011e-262"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3011e-263">Дополнительные сведения см. в разделе IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="3011e-263">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="3011e-264">Вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="3011e-264">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="3011e-265">Формирование шаблонов Identity в проекте MVC с авторизацией</span><span class="sxs-lookup"><span data-stu-id="3011e-265">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="3011e-266">Удалите папки *pages/Shared* и файлы в этой папке.</span><span class="sxs-lookup"><span data-stu-id="3011e-266">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="3011e-267">Создание полного Identity источника пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="3011e-267">Create full Identity UI source</span></span>

<span data-ttu-id="3011e-268">Чтобы обеспечить полный контроль над Identity пользовательским интерфейсом, запустите шаблон Identity и выберите **переопределить все файлы**.</span><span class="sxs-lookup"><span data-stu-id="3011e-268">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="3011e-269">В следующем выделенном коде показаны изменения для замены пользовательского интерфейса по умолчанию Identity Identity в веб-приложении ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="3011e-269">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="3011e-270">Это может потребоваться для полного контроля над Identity пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="3011e-270">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="3011e-271">Значение по умолчанию Identity заменяется в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="3011e-271">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="3011e-272">Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="3011e-272">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="3011e-273">Зарегистрируйте `IEmailSender` реализацию, например:</span><span class="sxs-lookup"><span data-stu-id="3011e-273">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="3011e-274">Отключить страницу регистрации</span><span class="sxs-lookup"><span data-stu-id="3011e-274">Disable register page</span></span>

<span data-ttu-id="3011e-275">Чтобы отключить регистрацию пользователей, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="3011e-275">To disable user registration:</span></span>

* <span data-ttu-id="3011e-276">Шаблон Identity .</span><span class="sxs-lookup"><span data-stu-id="3011e-276">Scaffold Identity.</span></span> <span data-ttu-id="3011e-277">Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион.</span><span class="sxs-lookup"><span data-stu-id="3011e-277">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="3011e-278">Пример.</span><span class="sxs-lookup"><span data-stu-id="3011e-278">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="3011e-279">Обновите *области или Identity /Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:</span><span class="sxs-lookup"><span data-stu-id="3011e-279">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="3011e-280">Обновите *области/ Identity /Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:</span><span class="sxs-lookup"><span data-stu-id="3011e-280">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="3011e-281">Закомментируйте или удалите регистрационную ссылку из *областей/ Identity /Пажес/аккаунт/логин.кштмл*</span><span class="sxs-lookup"><span data-stu-id="3011e-281">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="3011e-282">Обновите страницу *области или Identity /Пажес/аккаунт/регистерконфирматион* .</span><span class="sxs-lookup"><span data-stu-id="3011e-282">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="3011e-283">Удалите код и ссылки из файла CSHTML.</span><span class="sxs-lookup"><span data-stu-id="3011e-283">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="3011e-284">Удалите код подтверждения из `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="3011e-284">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="3011e-285">Использование другого приложения для добавления пользователей</span><span class="sxs-lookup"><span data-stu-id="3011e-285">Use another app to add users</span></span>

<span data-ttu-id="3011e-286">Предоставьте механизм для добавления пользователей за пределами веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="3011e-286">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="3011e-287">Ниже перечислены параметры для добавления пользователей.</span><span class="sxs-lookup"><span data-stu-id="3011e-287">Options to add users include:</span></span>

* <span data-ttu-id="3011e-288">Выделенное административное веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="3011e-288">A dedicated admin web app.</span></span>
* <span data-ttu-id="3011e-289">Консольное приложение.</span><span class="sxs-lookup"><span data-stu-id="3011e-289">A console app.</span></span>

<span data-ttu-id="3011e-290">В следующем примере кода один из подходов к добавлению пользователей:</span><span class="sxs-lookup"><span data-stu-id="3011e-290">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="3011e-291">Список пользователей считывается в память.</span><span class="sxs-lookup"><span data-stu-id="3011e-291">A list of users is read into memory.</span></span>
* <span data-ttu-id="3011e-292">Для каждого пользователя создается надежный уникальный пароль.</span><span class="sxs-lookup"><span data-stu-id="3011e-292">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="3011e-293">Пользователь будет добавлен в Identity базу данных.</span><span class="sxs-lookup"><span data-stu-id="3011e-293">The user is added to the Identity database.</span></span>
* <span data-ttu-id="3011e-294">Пользователь получает уведомления и сообщил об изменении пароля.</span><span class="sxs-lookup"><span data-stu-id="3011e-294">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="3011e-295">В следующем коде показано, как добавить пользователя:</span><span class="sxs-lookup"><span data-stu-id="3011e-295">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="3011e-296">Подобный подход можно выполнить для рабочих сценариев.</span><span class="sxs-lookup"><span data-stu-id="3011e-296">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3011e-297">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3011e-297">Additional resources</span></span>

* [<span data-ttu-id="3011e-298">Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="3011e-298">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
