---
title: Формирование шаблонов Identity в ASP.NET Core проектах
author: rick-anderson
description: Сведения о формировании шаблонов Identity в проекте ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 4404a5513d9dc989e50c904f3e7863de59991939
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022333"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="3cc21-103">Формирование шаблонов Identity в ASP.NET Core проектах</span><span class="sxs-lookup"><span data-stu-id="3cc21-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="3cc21-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="3cc21-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3cc21-105">ASP.NET Core предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="3cc21-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="3cc21-106">Приложения, которые включают в себя, Identity могут применять механизм формирования шаблонов для выборочного добавления исходного кода, содержащегося в Identity Razor библиотеке классов (РКЛ).</span><span class="sxs-lookup"><span data-stu-id="3cc21-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="3cc21-107">Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение.</span><span class="sxs-lookup"><span data-stu-id="3cc21-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="3cc21-108">Например, вы можете указать шаблону создать код, используемый при регистрации.</span><span class="sxs-lookup"><span data-stu-id="3cc21-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="3cc21-109">Созданный код имеет приоритет над тем же кодом в RCL для Identity.</span><span class="sxs-lookup"><span data-stu-id="3cc21-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="3cc21-110">Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного Identity источника пользовательского интерфейса](#full).</span><span class="sxs-lookup"><span data-stu-id="3cc21-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="3cc21-111">Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов для добавления Identity пакета РКЛ.</span><span class="sxs-lookup"><span data-stu-id="3cc21-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="3cc21-112">Вы можете выбрать, какой код Identity будет создан.</span><span class="sxs-lookup"><span data-stu-id="3cc21-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="3cc21-113">Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс.</span><span class="sxs-lookup"><span data-stu-id="3cc21-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="3cc21-114">В этом документе объясняются шаги, необходимые для завершения Identity обновления формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="3cc21-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="3cc21-115">Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения.</span><span class="sxs-lookup"><span data-stu-id="3cc21-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="3cc21-116">Проверьте изменения после запуска Identity механизма формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="3cc21-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="3cc21-117">Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также других функций безопасности с Identity .</span><span class="sxs-lookup"><span data-stu-id="3cc21-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="3cc21-118">Службы или заглушки служб не создаются при формировании шаблонов Identity .</span><span class="sxs-lookup"><span data-stu-id="3cc21-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="3cc21-119">Службы для включения этих функций необходимо добавить вручную.</span><span class="sxs-lookup"><span data-stu-id="3cc21-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="3cc21-120">Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="3cc21-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="3cc21-121">При формировании шаблона Identity с новым контекстом данных в проекте с существующими индивидуальными учетными записями:</span><span class="sxs-lookup"><span data-stu-id="3cc21-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="3cc21-122">В `Startup.ConfigureServices` удалите вызовы:</span><span class="sxs-lookup"><span data-stu-id="3cc21-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="3cc21-123">Например, `AddDbContext` и `AddDefaultIdentity` комментарии в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="3cc21-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="3cc21-124">Предшествующий код записывает в комментарий код, который дублируется в *области или Identity / Identity HostingStartup.CS*</span><span class="sxs-lookup"><span data-stu-id="3cc21-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="3cc21-125">Как правило, приложения, созданные с помощью отдельных учетных записей, ***не*** должны создавать новый контекст данных.</span><span class="sxs-lookup"><span data-stu-id="3cc21-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="3cc21-126">Формирование шаблона Identity в пустой проект</span><span class="sxs-lookup"><span data-stu-id="3cc21-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="3cc21-127">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="3cc21-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="3cc21-128">Формирование шаблонов Identity в Razor проекте без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="3cc21-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="3cc21-129">Identityнастраивается в *области или Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3cc21-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3cc21-130">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3cc21-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="3cc21-131">Миграция, Усеаусентикатион и макет</span><span class="sxs-lookup"><span data-stu-id="3cc21-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="3cc21-132">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="3cc21-132">Enable authentication</span></span>

<span data-ttu-id="3cc21-133">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="3cc21-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="3cc21-134">Изменения макета</span><span class="sxs-lookup"><span data-stu-id="3cc21-134">Layout changes</span></span>

<span data-ttu-id="3cc21-135">Необязательно: Добавьте в файл макета имя для входа partial ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="3cc21-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="3cc21-136">Формирование шаблонов Identity в Razor проекте с авторизацией</span><span class="sxs-lookup"><span data-stu-id="3cc21-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="3cc21-137">Некоторые Identity параметры настраиваются в *области или Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3cc21-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3cc21-138">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3cc21-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="3cc21-139">Формирование шаблонов Identity в проекте MVC без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="3cc21-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="3cc21-140">Необязательно: Добавьте часть имени для входа partial ( `_LoginPartial` ) в файл *views/Shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3cc21-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="3cc21-141">Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3cc21-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="3cc21-142">Identityнастраивается в *области или Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3cc21-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3cc21-143">Дополнительные сведения см. в разделе IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="3cc21-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="3cc21-144">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="3cc21-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="3cc21-145">Формирование шаблонов Identity в проекте MVC с авторизацией</span><span class="sxs-lookup"><span data-stu-id="3cc21-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="3cc21-146">Формирование шаблонов Identity в Blazor Server проекте без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="3cc21-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="3cc21-147">Identityнастраивается в *области или Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3cc21-147">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3cc21-148">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3cc21-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="3cc21-149">Миграции</span><span class="sxs-lookup"><span data-stu-id="3cc21-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="3cc21-150">Передача токена XSRF в приложение</span><span class="sxs-lookup"><span data-stu-id="3cc21-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="3cc21-151">Маркеры можно передавать в компоненты:</span><span class="sxs-lookup"><span data-stu-id="3cc21-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="3cc21-152">Когда маркеры проверки подлинности подготавливаются и сохраняются в процессе проверки подлинности cookie , они могут передаваться в компоненты.</span><span class="sxs-lookup"><span data-stu-id="3cc21-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* <span data-ttu-id="3cc21-153">Razorкомпоненты не могут использовать `HttpContext` напрямую, поэтому не существует способа получить [маркер подделки для защиты от запроса (XSRF)](xref:security/anti-request-forgery) для размещения Identity конечной точки выхода в `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="3cc21-153">Razor components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="3cc21-154">Токен XSRF может быть передан компонентам.</span><span class="sxs-lookup"><span data-stu-id="3cc21-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="3cc21-155">Для получения дополнительной информации см. <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="3cc21-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="3cc21-156">В файле *pages/_Host. cshtml* Установите маркер после его добавления в `InitialApplicationState` `TokenProvider` классы и:</span><span class="sxs-lookup"><span data-stu-id="3cc21-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="3cc21-157">Обновите `App` компонент (*app. Razor*), чтобы назначить `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="3cc21-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="3cc21-158">`TokenProvider`Служба, показанная в разделе, используется в `LoginDisplay` компоненте в следующем разделе [Макет и изменения потока проверки подлинности](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="3cc21-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="3cc21-159">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="3cc21-159">Enable authentication</span></span>

<span data-ttu-id="3cc21-160">В `Startup` классе:</span><span class="sxs-lookup"><span data-stu-id="3cc21-160">In the `Startup` class:</span></span>

* <span data-ttu-id="3cc21-161">Убедитесь, что Razor службы страниц добавлены в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3cc21-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="3cc21-162">При использовании [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)Зарегистрируйте службу.</span><span class="sxs-lookup"><span data-stu-id="3cc21-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="3cc21-163">Вызовите в `UseDatabaseErrorPage` построителе приложений в `Startup.Configure` среде разработки.</span><span class="sxs-lookup"><span data-stu-id="3cc21-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="3cc21-164">Вызовите `UseAuthentication` и `UseAuthorization` после `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="3cc21-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="3cc21-165">Добавление конечной точки для Razor страниц.</span><span class="sxs-lookup"><span data-stu-id="3cc21-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="3cc21-166">Изменения в потоке макета и проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="3cc21-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="3cc21-167">Добавьте `RedirectToLogin` компонент (*редиректтологин. Razor*) в *общую* папку приложения в корневом каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="3cc21-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

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

Добавьте `LoginDisplay` компонент (*логиндисплай. Razor*) в *общую* папку приложения. <span data-ttu-id="3cc21-169">[Служба TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) предоставляет маркер XSRF для HTML-формы, которая отправляет в Identity конечную точку выхода.</span><span class="sxs-lookup"><span data-stu-id="3cc21-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="3cc21-170">В `MainLayout` компоненте (*Shared/маинлайаут. Razor*) добавьте `LoginDisplay` компонент в `<div>` содержимое элемента верхней строки:</span><span class="sxs-lookup"><span data-stu-id="3cc21-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="3cc21-171">Конечные точки проверки подлинности стиля</span><span class="sxs-lookup"><span data-stu-id="3cc21-171">Style authentication endpoints</span></span>

<span data-ttu-id="3cc21-172">Поскольку Blazor Server использует Razor Identity страницы страниц, стиль пользовательского интерфейса изменяется при переходе посетителя между Identity страницами и компонентами.</span><span class="sxs-lookup"><span data-stu-id="3cc21-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="3cc21-173">Существует два варианта для адресации стилей инконгруаус:</span><span class="sxs-lookup"><span data-stu-id="3cc21-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="3cc21-174">IdentityКомпоненты сборки</span><span class="sxs-lookup"><span data-stu-id="3cc21-174">Build Identity components</span></span>

<span data-ttu-id="3cc21-175">Подход к использованию компонентов Identity вместо страниц заключается в создании Identity компонентов.</span><span class="sxs-lookup"><span data-stu-id="3cc21-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="3cc21-176">Поскольку `SignInManager` и `UserManager` не поддерживаются в Razor компонентах, используйте конечные точки API в Blazor Server приложении для обработки действий с учетной записью пользователя.</span><span class="sxs-lookup"><span data-stu-id="3cc21-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="3cc21-177">Использование пользовательского макета с Blazor стилями приложения</span><span class="sxs-lookup"><span data-stu-id="3cc21-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="3cc21-178">IdentityМакет и стили страниц можно изменить для создания страниц, использующих тему по умолчанию Blazor .</span><span class="sxs-lookup"><span data-stu-id="3cc21-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="3cc21-179">Пример в этом разделе является просто начальной точкой для настройки.</span><span class="sxs-lookup"><span data-stu-id="3cc21-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="3cc21-180">Для лучшего взаимодействия с пользователем, скорее всего, потребуется дополнительная работа.</span><span class="sxs-lookup"><span data-stu-id="3cc21-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="3cc21-181">Создайте новый `NavMenu_IdentityLayout` компонент (*Shared/NavMenu_ Identity Layout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="3cc21-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="3cc21-182">Для разметки и кода компонента используйте то же содержимое `NavMenu` компонента приложения (*Shared/навмену. Razor*).</span><span class="sxs-lookup"><span data-stu-id="3cc21-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="3cc21-183">Вывлекайте все `NavLink` компоненты, которые не могут быть анонимно подключены, так как автоматическое перенаправление в `RedirectToLogin` компоненте не выполняется для компонентов, нуждающихся в проверке подлинности или авторизации.</span><span class="sxs-lookup"><span data-stu-id="3cc21-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="3cc21-184">В файле *pages/Shared/Layout. cshtml* внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="3cc21-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="3cc21-185">Добавьте Razor директивы в начало файла, чтобы использовать вспомогательные функции тегов и компоненты приложения в *общей* папке:</span><span class="sxs-lookup"><span data-stu-id="3cc21-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="3cc21-186">Замените `{APPLICATION ASSEMBLY}` на имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="3cc21-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="3cc21-187">Добавьте `<base>` тег и Blazor таблицу стилей `<link>` к `<head>` содержимому:</span><span class="sxs-lookup"><span data-stu-id="3cc21-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="3cc21-188">Измените содержимое `<body>` тега следующим образом:</span><span class="sxs-lookup"><span data-stu-id="3cc21-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="3cc21-189">Формирование шаблонов Identity в Blazor Server проекте с авторизацией</span><span class="sxs-lookup"><span data-stu-id="3cc21-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="3cc21-190">Некоторые Identity параметры настраиваются в *области или Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3cc21-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3cc21-191">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3cc21-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="3cc21-192">Создание полного Identity источника пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="3cc21-192">Create full Identity UI source</span></span>

<span data-ttu-id="3cc21-193">Чтобы обеспечить полный контроль над Identity пользовательским интерфейсом, запустите шаблон Identity и выберите **переопределить все файлы**.</span><span class="sxs-lookup"><span data-stu-id="3cc21-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="3cc21-194">В следующем выделенном коде показаны изменения для замены пользовательского интерфейса по умолчанию Identity Identity в веб-приложении ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="3cc21-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="3cc21-195">Это может потребоваться для полного контроля над Identity пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="3cc21-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="3cc21-196">Значение по умолчанию Identity заменяется в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="3cc21-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="3cc21-197">Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="3cc21-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="3cc21-198">Зарегистрируйте `IEmailSender` реализацию, например:</span><span class="sxs-lookup"><span data-stu-id="3cc21-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="3cc21-199">Настройка пароля</span><span class="sxs-lookup"><span data-stu-id="3cc21-199">Password configuration</span></span>

<span data-ttu-id="3cc21-200">Если <xref:Microsoft.AspNetCore.Identity.PasswordOptions> настроен в `Startup.ConfigureServices` , для свойства в шаблонных страницах может потребоваться настройка [ `[StringLength]` атрибутов](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Identity .</span><span class="sxs-lookup"><span data-stu-id="3cc21-200">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="3cc21-201">`InputModel``Password`Свойства находятся в следующих файлах:</span><span class="sxs-lookup"><span data-stu-id="3cc21-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="3cc21-202">Отключение страницы</span><span class="sxs-lookup"><span data-stu-id="3cc21-202">Disable a page</span></span>

<span data-ttu-id="3cc21-203">В этом разделе показано, как отключить страницу Register, но подход можно использовать для отключения любой страницы.</span><span class="sxs-lookup"><span data-stu-id="3cc21-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="3cc21-204">Чтобы отключить регистрацию пользователей, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="3cc21-204">To disable user registration:</span></span>

* <span data-ttu-id="3cc21-205">Шаблон Identity .</span><span class="sxs-lookup"><span data-stu-id="3cc21-205">Scaffold Identity.</span></span> <span data-ttu-id="3cc21-206">Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион.</span><span class="sxs-lookup"><span data-stu-id="3cc21-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="3cc21-207">Например:</span><span class="sxs-lookup"><span data-stu-id="3cc21-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="3cc21-208">Обновите *области или Identity /Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:</span><span class="sxs-lookup"><span data-stu-id="3cc21-208">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="3cc21-209">Обновите *области/ Identity /Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:</span><span class="sxs-lookup"><span data-stu-id="3cc21-209">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="3cc21-210">Закомментируйте или удалите регистрационную ссылку из *областей/ Identity /Пажес/аккаунт/логин.кштмл*</span><span class="sxs-lookup"><span data-stu-id="3cc21-210">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="3cc21-211">Обновите страницу *области или Identity /Пажес/аккаунт/регистерконфирматион* .</span><span class="sxs-lookup"><span data-stu-id="3cc21-211">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="3cc21-212">Удалите код и ссылки из файла CSHTML.</span><span class="sxs-lookup"><span data-stu-id="3cc21-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="3cc21-213">Удалите код подтверждения из `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="3cc21-213">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="3cc21-214">Использование другого приложения для добавления пользователей</span><span class="sxs-lookup"><span data-stu-id="3cc21-214">Use another app to add users</span></span>

<span data-ttu-id="3cc21-215">Предоставьте механизм для добавления пользователей за пределами веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="3cc21-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="3cc21-216">Ниже перечислены параметры для добавления пользователей.</span><span class="sxs-lookup"><span data-stu-id="3cc21-216">Options to add users include:</span></span>

* <span data-ttu-id="3cc21-217">Выделенное административное веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="3cc21-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="3cc21-218">Консольное приложение.</span><span class="sxs-lookup"><span data-stu-id="3cc21-218">A console app.</span></span>

<span data-ttu-id="3cc21-219">В следующем примере кода один из подходов к добавлению пользователей:</span><span class="sxs-lookup"><span data-stu-id="3cc21-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="3cc21-220">Список пользователей считывается в память.</span><span class="sxs-lookup"><span data-stu-id="3cc21-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="3cc21-221">Для каждого пользователя создается надежный уникальный пароль.</span><span class="sxs-lookup"><span data-stu-id="3cc21-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="3cc21-222">Пользователь будет добавлен в Identity базу данных.</span><span class="sxs-lookup"><span data-stu-id="3cc21-222">The user is added to the Identity database.</span></span>
* <span data-ttu-id="3cc21-223">Пользователь получает уведомления и сообщил об изменении пароля.</span><span class="sxs-lookup"><span data-stu-id="3cc21-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="3cc21-224">В следующем коде показано, как добавить пользователя:</span><span class="sxs-lookup"><span data-stu-id="3cc21-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="3cc21-225">Подобный подход можно выполнить для рабочих сценариев.</span><span class="sxs-lookup"><span data-stu-id="3cc21-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="3cc21-226">Запретить публикацию статических Identity ресурсов</span><span class="sxs-lookup"><span data-stu-id="3cc21-226">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="3cc21-227">Чтобы предотвратить публикацию статических Identity ресурсов в веб-корне, см <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> . раздел.</span><span class="sxs-lookup"><span data-stu-id="3cc21-227">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3cc21-228">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3cc21-228">Additional resources</span></span>

* [<span data-ttu-id="3cc21-229">Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="3cc21-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3cc21-230">ASP.NET Core 2,1 и более поздних версий предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="3cc21-230">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="3cc21-231">Приложения, которые включают в себя, Identity могут применять механизм формирования шаблонов для выборочного добавления исходного кода, содержащегося в Identity Razor библиотеке классов (РКЛ).</span><span class="sxs-lookup"><span data-stu-id="3cc21-231">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="3cc21-232">Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение.</span><span class="sxs-lookup"><span data-stu-id="3cc21-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="3cc21-233">Например, вы можете указать шаблону создать код, используемый при регистрации.</span><span class="sxs-lookup"><span data-stu-id="3cc21-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="3cc21-234">Созданный код имеет приоритет над тем же кодом в RCL для Identity.</span><span class="sxs-lookup"><span data-stu-id="3cc21-234">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="3cc21-235">Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного идентификатора пользовательского интерфейса идентификации](#full).</span><span class="sxs-lookup"><span data-stu-id="3cc21-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="3cc21-236">Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов для добавления Identity пакета РКЛ.</span><span class="sxs-lookup"><span data-stu-id="3cc21-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="3cc21-237">Вы можете выбрать, какой код Identity будет создан.</span><span class="sxs-lookup"><span data-stu-id="3cc21-237">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="3cc21-238">Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс.</span><span class="sxs-lookup"><span data-stu-id="3cc21-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="3cc21-239">В этом документе объясняются шаги, необходимые для завершения Identity обновления формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="3cc21-239">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="3cc21-240">При Identity запуске шаблона создается файл *ScaffoldingReadme.txt* в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="3cc21-240">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="3cc21-241">Файл *ScaffoldingReadme.txt* содержит общие инструкции о том, что необходимо для завершения Identity обновления формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="3cc21-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="3cc21-242">Этот документ содержит более полные инструкции, чем файл *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="3cc21-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="3cc21-243">Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения.</span><span class="sxs-lookup"><span data-stu-id="3cc21-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="3cc21-244">Проверьте изменения после запуска Identity механизма формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="3cc21-244">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="3cc21-245">Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также других функций безопасности с Identity .</span><span class="sxs-lookup"><span data-stu-id="3cc21-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="3cc21-246">Службы или заглушки служб не создаются при формировании шаблонов Identity .</span><span class="sxs-lookup"><span data-stu-id="3cc21-246">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="3cc21-247">Службы для включения этих функций необходимо добавить вручную.</span><span class="sxs-lookup"><span data-stu-id="3cc21-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="3cc21-248">Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="3cc21-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="3cc21-249">Формирование шаблона Identity в пустой проект</span><span class="sxs-lookup"><span data-stu-id="3cc21-249">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="3cc21-250">Добавьте следующие выделенные вызовы в `Startup` класс:</span><span class="sxs-lookup"><span data-stu-id="3cc21-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="3cc21-251">Формирование шаблонов Identity в Razor проекте без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="3cc21-251">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="3cc21-252">Identityнастраивается в *области или Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3cc21-252">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3cc21-253">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3cc21-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="3cc21-254">Миграция, Усеаусентикатион и макет</span><span class="sxs-lookup"><span data-stu-id="3cc21-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="3cc21-255">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="3cc21-255">Enable authentication</span></span>

<span data-ttu-id="3cc21-256">В `Configure` методе `Startup` класса вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="3cc21-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="3cc21-257">Изменения макета</span><span class="sxs-lookup"><span data-stu-id="3cc21-257">Layout changes</span></span>

<span data-ttu-id="3cc21-258">Необязательно: Добавьте в файл макета имя для входа partial ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="3cc21-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="3cc21-259">Формирование шаблонов Identity в Razor проекте с авторизацией</span><span class="sxs-lookup"><span data-stu-id="3cc21-259">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="3cc21-260">Некоторые Identity параметры настраиваются в *области или Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3cc21-260">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3cc21-261">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3cc21-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="3cc21-262">Формирование шаблонов Identity в проекте MVC без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="3cc21-262">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="3cc21-263">Необязательно: Добавьте часть имени для входа partial ( `_LoginPartial` ) в файл *views/Shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3cc21-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="3cc21-264">Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3cc21-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="3cc21-265">Identityнастраивается в *области или Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="3cc21-265">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3cc21-266">Дополнительные сведения см. в разделе IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="3cc21-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="3cc21-267">Вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="3cc21-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="3cc21-268">Формирование шаблонов Identity в проекте MVC с авторизацией</span><span class="sxs-lookup"><span data-stu-id="3cc21-268">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="3cc21-269">Удалите папки *pages/Shared* и файлы в этой папке.</span><span class="sxs-lookup"><span data-stu-id="3cc21-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="3cc21-270">Создание полного Identity источника пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="3cc21-270">Create full Identity UI source</span></span>

<span data-ttu-id="3cc21-271">Чтобы обеспечить полный контроль над Identity пользовательским интерфейсом, запустите шаблон Identity и выберите **переопределить все файлы**.</span><span class="sxs-lookup"><span data-stu-id="3cc21-271">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="3cc21-272">В следующем выделенном коде показаны изменения для замены пользовательского интерфейса по умолчанию Identity Identity в веб-приложении ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="3cc21-272">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="3cc21-273">Это может потребоваться для полного контроля над Identity пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="3cc21-273">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="3cc21-274">Значение по умолчанию Identity заменяется в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="3cc21-274">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="3cc21-275">Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="3cc21-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="3cc21-276">Зарегистрируйте `IEmailSender` реализацию, например:</span><span class="sxs-lookup"><span data-stu-id="3cc21-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="3cc21-277">Настройка пароля</span><span class="sxs-lookup"><span data-stu-id="3cc21-277">Password configuration</span></span>

<span data-ttu-id="3cc21-278">Если <xref:Microsoft.AspNetCore.Identity.PasswordOptions> настроен в `Startup.ConfigureServices` , для свойства в шаблонных страницах может потребоваться настройка [ `[StringLength]` атрибутов](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Identity .</span><span class="sxs-lookup"><span data-stu-id="3cc21-278">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="3cc21-279">`InputModel``Password`Свойства находятся в следующих файлах:</span><span class="sxs-lookup"><span data-stu-id="3cc21-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="3cc21-280">Отключить страницу регистрации</span><span class="sxs-lookup"><span data-stu-id="3cc21-280">Disable register page</span></span>

<span data-ttu-id="3cc21-281">Чтобы отключить регистрацию пользователей, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="3cc21-281">To disable user registration:</span></span>

* <span data-ttu-id="3cc21-282">Шаблон Identity .</span><span class="sxs-lookup"><span data-stu-id="3cc21-282">Scaffold Identity.</span></span> <span data-ttu-id="3cc21-283">Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион.</span><span class="sxs-lookup"><span data-stu-id="3cc21-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="3cc21-284">Например:</span><span class="sxs-lookup"><span data-stu-id="3cc21-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="3cc21-285">Обновите *области или Identity /Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:</span><span class="sxs-lookup"><span data-stu-id="3cc21-285">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="3cc21-286">Обновите *области/ Identity /Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:</span><span class="sxs-lookup"><span data-stu-id="3cc21-286">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="3cc21-287">Закомментируйте или удалите регистрационную ссылку из *областей/ Identity /Пажес/аккаунт/логин.кштмл*</span><span class="sxs-lookup"><span data-stu-id="3cc21-287">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="3cc21-288">Обновите страницу *области или Identity /Пажес/аккаунт/регистерконфирматион* .</span><span class="sxs-lookup"><span data-stu-id="3cc21-288">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="3cc21-289">Удалите код и ссылки из файла CSHTML.</span><span class="sxs-lookup"><span data-stu-id="3cc21-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="3cc21-290">Удалите код подтверждения из `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="3cc21-290">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="3cc21-291">Использование другого приложения для добавления пользователей</span><span class="sxs-lookup"><span data-stu-id="3cc21-291">Use another app to add users</span></span>

<span data-ttu-id="3cc21-292">Предоставьте механизм для добавления пользователей за пределами веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="3cc21-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="3cc21-293">Ниже перечислены параметры для добавления пользователей.</span><span class="sxs-lookup"><span data-stu-id="3cc21-293">Options to add users include:</span></span>

* <span data-ttu-id="3cc21-294">Выделенное административное веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="3cc21-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="3cc21-295">Консольное приложение.</span><span class="sxs-lookup"><span data-stu-id="3cc21-295">A console app.</span></span>

<span data-ttu-id="3cc21-296">В следующем примере кода один из подходов к добавлению пользователей:</span><span class="sxs-lookup"><span data-stu-id="3cc21-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="3cc21-297">Список пользователей считывается в память.</span><span class="sxs-lookup"><span data-stu-id="3cc21-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="3cc21-298">Для каждого пользователя создается надежный уникальный пароль.</span><span class="sxs-lookup"><span data-stu-id="3cc21-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="3cc21-299">Пользователь будет добавлен в Identity базу данных.</span><span class="sxs-lookup"><span data-stu-id="3cc21-299">The user is added to the Identity database.</span></span>
* <span data-ttu-id="3cc21-300">Пользователь получает уведомления и сообщил об изменении пароля.</span><span class="sxs-lookup"><span data-stu-id="3cc21-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="3cc21-301">В следующем коде показано, как добавить пользователя:</span><span class="sxs-lookup"><span data-stu-id="3cc21-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="3cc21-302">Подобный подход можно выполнить для рабочих сценариев.</span><span class="sxs-lookup"><span data-stu-id="3cc21-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3cc21-303">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3cc21-303">Additional resources</span></span>

* [<span data-ttu-id="3cc21-304">Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="3cc21-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
