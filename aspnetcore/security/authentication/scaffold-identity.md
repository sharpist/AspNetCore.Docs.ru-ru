---
title: 'Формирование шаблонов :::no-loc(Identity)::: в ASP.NET Core проектах'
author: rick-anderson
description: 'Сведения о формировании шаблонов :::no-loc(Identity)::: в проекте ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/scaffold-identity
ms.openlocfilehash: c79dfc64d4311088c3f9ea03aad7570189000e2a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053323"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="b75e3-103">Формирование шаблонов :::no-loc(Identity)::: в ASP.NET Core проектах</span><span class="sxs-lookup"><span data-stu-id="b75e3-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="b75e3-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="b75e3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b75e3-105">ASP.NET Core предоставляется [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) в виде [ :::no-loc(Razor)::: библиотеки классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="b75e3-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b75e3-106">Приложения, которые включают в себя, :::no-loc(Identity)::: могут применять механизм формирования шаблонов для выборочного добавления исходного кода, содержащегося в :::no-loc(Identity)::: :::no-loc(Razor)::: библиотеке классов (РКЛ).</span><span class="sxs-lookup"><span data-stu-id="b75e3-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="b75e3-107">Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение.</span><span class="sxs-lookup"><span data-stu-id="b75e3-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="b75e3-108">Например, вы можете указать шаблону создать код, используемый при регистрации.</span><span class="sxs-lookup"><span data-stu-id="b75e3-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="b75e3-109">Созданный код имеет приоритет над тем же кодом в RCL для :::no-loc(Identity):::.</span><span class="sxs-lookup"><span data-stu-id="b75e3-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="b75e3-110">Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного :::no-loc(Identity)::: источника пользовательского интерфейса](#full).</span><span class="sxs-lookup"><span data-stu-id="b75e3-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="b75e3-111">Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов для добавления :::no-loc(Identity)::: пакета РКЛ.</span><span class="sxs-lookup"><span data-stu-id="b75e3-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="b75e3-112">Вы можете выбрать, какой код :::no-loc(Identity)::: будет создан.</span><span class="sxs-lookup"><span data-stu-id="b75e3-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="b75e3-113">Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс.</span><span class="sxs-lookup"><span data-stu-id="b75e3-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="b75e3-114">В этом документе объясняются шаги, необходимые для завершения :::no-loc(Identity)::: обновления формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="b75e3-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="b75e3-115">Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения.</span><span class="sxs-lookup"><span data-stu-id="b75e3-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="b75e3-116">Проверьте изменения после запуска :::no-loc(Identity)::: механизма формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="b75e3-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="b75e3-117">Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также других функций безопасности с :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b75e3-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="b75e3-118">Службы или заглушки служб не создаются при формировании шаблонов :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b75e3-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="b75e3-119">Службы для включения этих функций необходимо добавить вручную.</span><span class="sxs-lookup"><span data-stu-id="b75e3-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="b75e3-120">Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="b75e3-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="b75e3-121">При формировании шаблона :::no-loc(Identity)::: с новым контекстом данных в проекте с существующими индивидуальными учетными записями:</span><span class="sxs-lookup"><span data-stu-id="b75e3-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="b75e3-122">В `Startup.ConfigureServices` удалите вызовы:</span><span class="sxs-lookup"><span data-stu-id="b75e3-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="b75e3-123">Например, `AddDbContext` и `AddDefault:::no-loc(Identity):::` комментарии в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="b75e3-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="b75e3-124">Предшествующий код записывает в комментарий код, который дублируется в *области или :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS*</span><span class="sxs-lookup"><span data-stu-id="b75e3-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="b75e3-125">Как правило, приложения, созданные с отдельными учетными записями, должны \* **не** _, создать новый контекст данных.</span><span class="sxs-lookup"><span data-stu-id="b75e3-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="b75e3-126">Формирование шаблона :::no-loc(Identity)::: в пустой проект</span><span class="sxs-lookup"><span data-stu-id="b75e3-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b75e3-127">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="b75e3-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="b75e3-128">Формирование шаблонов :::no-loc(Identity)::: в :::no-loc(Razor)::: проекте без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="b75e3-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add Create:::no-loc(Identity):::Schema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b75e3-129">:::no-loc(Identity):::настраивается в _Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs \*.</span><span class="sxs-lookup"><span data-stu-id="b75e3-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="b75e3-130">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b75e3-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="b75e3-131">Миграция, Усеаусентикатион и макет</span><span class="sxs-lookup"><span data-stu-id="b75e3-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="b75e3-132">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="b75e3-132">Enable authentication</span></span>

<span data-ttu-id="b75e3-133">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="b75e3-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="b75e3-134">Изменения макета</span><span class="sxs-lookup"><span data-stu-id="b75e3-134">Layout changes</span></span>

<span data-ttu-id="b75e3-135">Необязательно: Добавьте в файл макета имя для входа partial ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="b75e3-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="b75e3-136">Формирование шаблонов :::no-loc(Identity)::: в :::no-loc(Razor)::: проекте с авторизацией</span><span class="sxs-lookup"><span data-stu-id="b75e3-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="b75e3-137">Некоторые :::no-loc(Identity)::: параметры настраиваются в *области или :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="b75e3-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b75e3-138">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b75e3-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="b75e3-139">Формирование шаблонов :::no-loc(Identity)::: в проекте MVC без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="b75e3-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b75e3-140">Необязательно: Добавьте часть имени для входа partial ( `_LoginPartial` ) в файл *views/Shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b75e3-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="b75e3-141">Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="b75e3-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="b75e3-142">:::no-loc(Identity):::настраивается в *области или :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="b75e3-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b75e3-143">Дополнительные сведения см. в разделе IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="b75e3-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="b75e3-144">Обновите `Startup` класс с помощью кода, аналогичного следующему:</span><span class="sxs-lookup"><span data-stu-id="b75e3-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="b75e3-145">Формирование шаблонов :::no-loc(Identity)::: в проекте MVC с авторизацией</span><span class="sxs-lookup"><span data-stu-id="b75e3-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="b75e3-146">Формирование шаблонов :::no-loc(Identity)::: в :::no-loc(Blazor Server)::: проекте без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="b75e3-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b75e3-147">:::no-loc(Identity):::настраивается в *области или :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="b75e3-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b75e3-148">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b75e3-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="b75e3-149">Миграции</span><span class="sxs-lookup"><span data-stu-id="b75e3-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="b75e3-150">Передача токена XSRF в приложение</span><span class="sxs-lookup"><span data-stu-id="b75e3-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="b75e3-151">Маркеры можно передавать в компоненты:</span><span class="sxs-lookup"><span data-stu-id="b75e3-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="b75e3-152">Когда маркеры проверки подлинности подготавливаются и сохраняются в процессе проверки подлинности :::no-loc(cookie)::: , они могут передаваться в компоненты.</span><span class="sxs-lookup"><span data-stu-id="b75e3-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="b75e3-153">:::no-loc(Razor)::: компоненты не могут использовать `HttpContext` напрямую, поэтому не существует способа получить [маркер подделки для защиты от запроса (XSRF)](xref:security/anti-request-forgery) для размещения :::no-loc(Identity)::: конечной точки выхода в `/:::no-loc(Identity):::/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="b75e3-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="b75e3-154">Токен XSRF может быть передан компонентам.</span><span class="sxs-lookup"><span data-stu-id="b75e3-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="b75e3-155">Для получения дополнительной информации см. <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="b75e3-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="b75e3-156">В файле *pages/_Host. cshtml* Установите маркер после его добавления в `InitialApplicationState` `TokenProvider` классы и:</span><span class="sxs-lookup"><span data-stu-id="b75e3-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="b75e3-157">Обновите `App` компонент ( *app. Razor* ), чтобы назначить `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="b75e3-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="b75e3-158">`TokenProvider`Служба, показанная в разделе, используется в `LoginDisplay` компоненте в следующем разделе [Макет и изменения потока проверки подлинности](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="b75e3-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="b75e3-159">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="b75e3-159">Enable authentication</span></span>

<span data-ttu-id="b75e3-160">В `Startup` классе:</span><span class="sxs-lookup"><span data-stu-id="b75e3-160">In the `Startup` class:</span></span>

* <span data-ttu-id="b75e3-161">Убедитесь, что :::no-loc(Razor)::: службы страниц добавлены в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b75e3-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="b75e3-162">При использовании [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)Зарегистрируйте службу.</span><span class="sxs-lookup"><span data-stu-id="b75e3-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="b75e3-163">Вызовите в `UseDatabaseErrorPage` построителе приложений в `Startup.Configure` среде разработки.</span><span class="sxs-lookup"><span data-stu-id="b75e3-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="b75e3-164">Вызовите `UseAuthentication` и `UseAuthorization` после `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="b75e3-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="b75e3-165">Добавление конечной точки для :::no-loc(Razor)::: страниц.</span><span class="sxs-lookup"><span data-stu-id="b75e3-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="b75e3-166">Изменения в потоке макета и проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b75e3-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="b75e3-167">Добавьте `RedirectToLogin` компонент ( *редиректтологин. Razor* ) в *общую* папку приложения в корневом каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="b75e3-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(":::no-loc(Identity):::/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Добавьте `LoginDisplay` компонент ( *логиндисплай. Razor* ) в *общую* папку приложения. <span data-ttu-id="b75e3-169">[Служба TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) предоставляет маркер XSRF для HTML-формы, которая отправляет в :::no-loc(Identity)::: конечную точку выхода.</span><span class="sxs-lookup"><span data-stu-id="b75e3-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href=":::no-loc(Identity):::/Account/Manage/Index">
            Hello, @context.User.:::no-loc(Identity):::.Name!
        </a>
        <form action="/:::no-loc(Identity):::/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href=":::no-loc(Identity):::/Account/Register">Register</a>
        <a href=":::no-loc(Identity):::/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="b75e3-170">В `MainLayout` компоненте ( *Shared/маинлайаут. Razor* ) добавьте `LoginDisplay` компонент в `<div>` содержимое элемента верхней строки:</span><span class="sxs-lookup"><span data-stu-id="b75e3-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="b75e3-171">Конечные точки проверки подлинности стиля</span><span class="sxs-lookup"><span data-stu-id="b75e3-171">Style authentication endpoints</span></span>

<span data-ttu-id="b75e3-172">Поскольку :::no-loc(Blazor Server)::: использует :::no-loc(Razor)::: :::no-loc(Identity)::: страницы страниц, стиль пользовательского интерфейса изменяется при переходе посетителя между :::no-loc(Identity)::: страницами и компонентами.</span><span class="sxs-lookup"><span data-stu-id="b75e3-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="b75e3-173">Существует два варианта для адресации стилей инконгруаус:</span><span class="sxs-lookup"><span data-stu-id="b75e3-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="b75e3-174">:::no-loc(Identity):::Компоненты сборки</span><span class="sxs-lookup"><span data-stu-id="b75e3-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="b75e3-175">Подход к использованию компонентов :::no-loc(Identity)::: вместо страниц заключается в создании :::no-loc(Identity)::: компонентов.</span><span class="sxs-lookup"><span data-stu-id="b75e3-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="b75e3-176">Поскольку `SignInManager` и `UserManager` не поддерживаются в :::no-loc(Razor)::: компонентах, используйте конечные точки API в :::no-loc(Blazor Server)::: приложении для обработки действий с учетной записью пользователя.</span><span class="sxs-lookup"><span data-stu-id="b75e3-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="b75e3-177">Использование пользовательского макета с :::no-loc(Blazor)::: стилями приложения</span><span class="sxs-lookup"><span data-stu-id="b75e3-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="b75e3-178">:::no-loc(Identity):::Макет и стили страниц можно изменить для создания страниц, использующих тему по умолчанию :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="b75e3-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="b75e3-179">Пример в этом разделе является просто начальной точкой для настройки.</span><span class="sxs-lookup"><span data-stu-id="b75e3-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="b75e3-180">Для лучшего взаимодействия с пользователем, скорее всего, потребуется дополнительная работа.</span><span class="sxs-lookup"><span data-stu-id="b75e3-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="b75e3-181">Создайте новый `NavMenu_:::no-loc(Identity):::Layout` компонент ( *Shared/NavMenu_ :::no-loc(Identity)::: Layout. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="b75e3-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="b75e3-182">Для разметки и кода компонента используйте то же содержимое `NavMenu` компонента приложения ( *Shared/навмену. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="b75e3-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="b75e3-183">Вывлекайте все `NavLink` компоненты, которые не могут быть анонимно подключены, так как автоматическое перенаправление в `RedirectToLogin` компоненте не выполняется для компонентов, нуждающихся в проверке подлинности или авторизации.</span><span class="sxs-lookup"><span data-stu-id="b75e3-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="b75e3-184">В файле *pages/Shared/Layout. cshtml* внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="b75e3-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="b75e3-185">Добавьте :::no-loc(Razor)::: директивы в начало файла, чтобы использовать вспомогательные функции тегов и компоненты приложения в *общей* папке:</span><span class="sxs-lookup"><span data-stu-id="b75e3-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="b75e3-186">Замените `{APPLICATION ASSEMBLY}` на имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="b75e3-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="b75e3-187">Добавьте `<base>` тег и :::no-loc(Blazor)::: таблицу стилей `<link>` к `<head>` содержимому:</span><span class="sxs-lookup"><span data-stu-id="b75e3-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="b75e3-188">Измените содержимое `<body>` тега следующим образом:</span><span class="sxs-lookup"><span data-stu-id="b75e3-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_:::no-loc(Identity):::Layout)" 
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
              throw new InvalidOperationException("The default :::no-loc(Identity)::: UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/:::no-loc(Identity):::/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/:::no-loc(Identity):::/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/:::no-loc(Identity):::/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="b75e3-189">Формирование шаблонов :::no-loc(Identity)::: в :::no-loc(Blazor Server)::: проекте с авторизацией</span><span class="sxs-lookup"><span data-stu-id="b75e3-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b75e3-190">Некоторые :::no-loc(Identity)::: параметры настраиваются в *области или :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="b75e3-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b75e3-191">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b75e3-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="b75e3-192">Создание полного :::no-loc(Identity)::: источника пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="b75e3-192">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="b75e3-193">Чтобы обеспечить полный контроль над :::no-loc(Identity)::: пользовательским интерфейсом, запустите шаблон :::no-loc(Identity)::: и выберите **переопределить все файлы** .</span><span class="sxs-lookup"><span data-stu-id="b75e3-193">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="b75e3-194">В следующем выделенном коде показаны изменения для замены пользовательского интерфейса по умолчанию :::no-loc(Identity)::: :::no-loc(Identity)::: в веб-приложении ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="b75e3-194">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="b75e3-195">Это может потребоваться для полного контроля над :::no-loc(Identity)::: пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="b75e3-195">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="b75e3-196">Значение по умолчанию :::no-loc(Identity)::: заменяется в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="b75e3-196">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="b75e3-197">Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="b75e3-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="b75e3-198">Зарегистрируйте `IEmailSender` реализацию, например:</span><span class="sxs-lookup"><span data-stu-id="b75e3-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="b75e3-199">Настройка пароля</span><span class="sxs-lookup"><span data-stu-id="b75e3-199">Password configuration</span></span>

<span data-ttu-id="b75e3-200">Если <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> настроен в `Startup.ConfigureServices` , для свойства в шаблонных страницах может потребоваться настройка [ `[StringLength]` атрибутов](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b75e3-200">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="b75e3-201">`InputModel``Password`Свойства находятся в следующих файлах:</span><span class="sxs-lookup"><span data-stu-id="b75e3-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="b75e3-202">Отключение страницы</span><span class="sxs-lookup"><span data-stu-id="b75e3-202">Disable a page</span></span>

<span data-ttu-id="b75e3-203">В этом разделе показано, как отключить страницу Register, но подход можно использовать для отключения любой страницы.</span><span class="sxs-lookup"><span data-stu-id="b75e3-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="b75e3-204">Чтобы отключить регистрацию пользователей, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="b75e3-204">To disable user registration:</span></span>

* <span data-ttu-id="b75e3-205">Шаблон :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b75e3-205">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="b75e3-206">Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион.</span><span class="sxs-lookup"><span data-stu-id="b75e3-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="b75e3-207">Пример:</span><span class="sxs-lookup"><span data-stu-id="b75e3-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="b75e3-208">Обновите *области или :::no-loc(Identity)::: /Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:</span><span class="sxs-lookup"><span data-stu-id="b75e3-208">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="b75e3-209">Обновите *области/ :::no-loc(Identity)::: /Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:</span><span class="sxs-lookup"><span data-stu-id="b75e3-209">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="b75e3-210">Закомментируйте или удалите регистрационную ссылку из *областей/ :::no-loc(Identity)::: /Пажес/аккаунт/логин.кштмл*</span><span class="sxs-lookup"><span data-stu-id="b75e3-210">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="b75e3-211">Обновите страницу *области или :::no-loc(Identity)::: /Пажес/аккаунт/регистерконфирматион* .</span><span class="sxs-lookup"><span data-stu-id="b75e3-211">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="b75e3-212">Удалите код и ссылки из файла CSHTML.</span><span class="sxs-lookup"><span data-stu-id="b75e3-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="b75e3-213">Удалите код подтверждения из `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="b75e3-213">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="b75e3-214">Использование другого приложения для добавления пользователей</span><span class="sxs-lookup"><span data-stu-id="b75e3-214">Use another app to add users</span></span>

<span data-ttu-id="b75e3-215">Предоставьте механизм для добавления пользователей за пределами веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="b75e3-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="b75e3-216">Ниже перечислены параметры для добавления пользователей.</span><span class="sxs-lookup"><span data-stu-id="b75e3-216">Options to add users include:</span></span>

* <span data-ttu-id="b75e3-217">Выделенное административное веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="b75e3-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="b75e3-218">Консольное приложение.</span><span class="sxs-lookup"><span data-stu-id="b75e3-218">A console app.</span></span>

<span data-ttu-id="b75e3-219">В следующем примере кода один из подходов к добавлению пользователей:</span><span class="sxs-lookup"><span data-stu-id="b75e3-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="b75e3-220">Список пользователей считывается в память.</span><span class="sxs-lookup"><span data-stu-id="b75e3-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="b75e3-221">Для каждого пользователя создается надежный уникальный пароль.</span><span class="sxs-lookup"><span data-stu-id="b75e3-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="b75e3-222">Пользователь будет добавлен в :::no-loc(Identity)::: базу данных.</span><span class="sxs-lookup"><span data-stu-id="b75e3-222">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="b75e3-223">Пользователь получает уведомления и сообщил об изменении пароля.</span><span class="sxs-lookup"><span data-stu-id="b75e3-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="b75e3-224">В следующем коде показано, как добавить пользователя:</span><span class="sxs-lookup"><span data-stu-id="b75e3-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="b75e3-225">Подобный подход можно выполнить для рабочих сценариев.</span><span class="sxs-lookup"><span data-stu-id="b75e3-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="b75e3-226">Запретить публикацию статических :::no-loc(Identity)::: ресурсов</span><span class="sxs-lookup"><span data-stu-id="b75e3-226">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="b75e3-227">Чтобы предотвратить публикацию статических :::no-loc(Identity)::: ресурсов в веб-корне, см <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> . раздел.</span><span class="sxs-lookup"><span data-stu-id="b75e3-227">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b75e3-228">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b75e3-228">Additional resources</span></span>

* [<span data-ttu-id="b75e3-229">Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="b75e3-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b75e3-230">ASP.NET Core 2,1 и более поздних версий предоставляет [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) [ :::no-loc(Razor)::: библиотеку классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="b75e3-230">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b75e3-231">Приложения, которые включают в себя, :::no-loc(Identity)::: могут применять механизм формирования шаблонов для выборочного добавления исходного кода, содержащегося в :::no-loc(Identity)::: :::no-loc(Razor)::: библиотеке классов (РКЛ).</span><span class="sxs-lookup"><span data-stu-id="b75e3-231">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="b75e3-232">Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение.</span><span class="sxs-lookup"><span data-stu-id="b75e3-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="b75e3-233">Например, вы можете указать шаблону создать код, используемый при регистрации.</span><span class="sxs-lookup"><span data-stu-id="b75e3-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="b75e3-234">Созданный код имеет приоритет над тем же кодом в RCL для :::no-loc(Identity):::.</span><span class="sxs-lookup"><span data-stu-id="b75e3-234">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="b75e3-235">Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного идентификатора пользовательского интерфейса идентификации](#full).</span><span class="sxs-lookup"><span data-stu-id="b75e3-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="b75e3-236">Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов для добавления :::no-loc(Identity)::: пакета РКЛ.</span><span class="sxs-lookup"><span data-stu-id="b75e3-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="b75e3-237">Вы можете выбрать, какой код :::no-loc(Identity)::: будет создан.</span><span class="sxs-lookup"><span data-stu-id="b75e3-237">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="b75e3-238">Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс.</span><span class="sxs-lookup"><span data-stu-id="b75e3-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="b75e3-239">В этом документе объясняются шаги, необходимые для завершения :::no-loc(Identity)::: обновления формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="b75e3-239">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="b75e3-240">При :::no-loc(Identity)::: запуске шаблона создается файл *ScaffoldingReadme.txt* в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="b75e3-240">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="b75e3-241">Файл *ScaffoldingReadme.txt* содержит общие инструкции о том, что необходимо для завершения :::no-loc(Identity)::: обновления формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="b75e3-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="b75e3-242">Этот документ содержит более полные инструкции, чем файл *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="b75e3-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="b75e3-243">Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения.</span><span class="sxs-lookup"><span data-stu-id="b75e3-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="b75e3-244">Проверьте изменения после запуска :::no-loc(Identity)::: механизма формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="b75e3-244">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="b75e3-245">Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также других функций безопасности с :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b75e3-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="b75e3-246">Службы или заглушки служб не создаются при формировании шаблонов :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b75e3-246">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="b75e3-247">Службы для включения этих функций необходимо добавить вручную.</span><span class="sxs-lookup"><span data-stu-id="b75e3-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="b75e3-248">Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="b75e3-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="b75e3-249">Формирование шаблона :::no-loc(Identity)::: в пустой проект</span><span class="sxs-lookup"><span data-stu-id="b75e3-249">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b75e3-250">Добавьте следующие выделенные вызовы в `Startup` класс:</span><span class="sxs-lookup"><span data-stu-id="b75e3-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="b75e3-251">Формирование шаблонов :::no-loc(Identity)::: в :::no-loc(Razor)::: проекте без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="b75e3-251">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b75e3-252">:::no-loc(Identity):::настраивается в *области или :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="b75e3-252">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b75e3-253">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b75e3-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="b75e3-254">Миграция, Усеаусентикатион и макет</span><span class="sxs-lookup"><span data-stu-id="b75e3-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="b75e3-255">Включить проверку подлинности</span><span class="sxs-lookup"><span data-stu-id="b75e3-255">Enable authentication</span></span>

<span data-ttu-id="b75e3-256">В `Configure` методе `Startup` класса вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="b75e3-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="b75e3-257">Изменения макета</span><span class="sxs-lookup"><span data-stu-id="b75e3-257">Layout changes</span></span>

<span data-ttu-id="b75e3-258">Необязательно: Добавьте в файл макета имя для входа partial ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="b75e3-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="b75e3-259">Формирование шаблонов :::no-loc(Identity)::: в :::no-loc(Razor)::: проекте с авторизацией</span><span class="sxs-lookup"><span data-stu-id="b75e3-259">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="b75e3-260">Некоторые :::no-loc(Identity)::: параметры настраиваются в *области или :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="b75e3-260">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b75e3-261">Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b75e3-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="b75e3-262">Формирование шаблонов :::no-loc(Identity)::: в проекте MVC без существующей авторизации</span><span class="sxs-lookup"><span data-stu-id="b75e3-262">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b75e3-263">Необязательно: Добавьте часть имени для входа partial ( `_LoginPartial` ) в файл *views/Shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b75e3-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="b75e3-264">Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="b75e3-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="b75e3-265">:::no-loc(Identity):::настраивается в *области или :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="b75e3-265">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b75e3-266">Дополнительные сведения см. в разделе IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="b75e3-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="b75e3-267">Вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="b75e3-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="b75e3-268">Формирование шаблонов :::no-loc(Identity)::: в проекте MVC с авторизацией</span><span class="sxs-lookup"><span data-stu-id="b75e3-268">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b75e3-269">Удалите папки *pages/Shared* и файлы в этой папке.</span><span class="sxs-lookup"><span data-stu-id="b75e3-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="b75e3-270">Создание полного :::no-loc(Identity)::: источника пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="b75e3-270">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="b75e3-271">Чтобы обеспечить полный контроль над :::no-loc(Identity)::: пользовательским интерфейсом, запустите шаблон :::no-loc(Identity)::: и выберите **переопределить все файлы** .</span><span class="sxs-lookup"><span data-stu-id="b75e3-271">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="b75e3-272">В следующем выделенном коде показаны изменения для замены пользовательского интерфейса по умолчанию :::no-loc(Identity)::: :::no-loc(Identity)::: в веб-приложении ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="b75e3-272">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="b75e3-273">Это может потребоваться для полного контроля над :::no-loc(Identity)::: пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="b75e3-273">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="b75e3-274">Значение по умолчанию :::no-loc(Identity)::: заменяется в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="b75e3-274">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="b75e3-275">Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="b75e3-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="b75e3-276">Зарегистрируйте `IEmailSender` реализацию, например:</span><span class="sxs-lookup"><span data-stu-id="b75e3-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="b75e3-277">Настройка пароля</span><span class="sxs-lookup"><span data-stu-id="b75e3-277">Password configuration</span></span>

<span data-ttu-id="b75e3-278">Если <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> настроен в `Startup.ConfigureServices` , для свойства в шаблонных страницах может потребоваться настройка [ `[StringLength]` атрибутов](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b75e3-278">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="b75e3-279">`InputModel``Password`Свойства находятся в следующих файлах:</span><span class="sxs-lookup"><span data-stu-id="b75e3-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="b75e3-280">Отключить страницу регистрации</span><span class="sxs-lookup"><span data-stu-id="b75e3-280">Disable register page</span></span>

<span data-ttu-id="b75e3-281">Чтобы отключить регистрацию пользователей, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="b75e3-281">To disable user registration:</span></span>

* <span data-ttu-id="b75e3-282">Шаблон :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b75e3-282">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="b75e3-283">Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион.</span><span class="sxs-lookup"><span data-stu-id="b75e3-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="b75e3-284">Пример:</span><span class="sxs-lookup"><span data-stu-id="b75e3-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="b75e3-285">Обновите *области или :::no-loc(Identity)::: /Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:</span><span class="sxs-lookup"><span data-stu-id="b75e3-285">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="b75e3-286">Обновите *области/ :::no-loc(Identity)::: /Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:</span><span class="sxs-lookup"><span data-stu-id="b75e3-286">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="b75e3-287">Закомментируйте или удалите регистрационную ссылку из *областей/ :::no-loc(Identity)::: /Пажес/аккаунт/логин.кштмл*</span><span class="sxs-lookup"><span data-stu-id="b75e3-287">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="b75e3-288">Обновите страницу *области или :::no-loc(Identity)::: /Пажес/аккаунт/регистерконфирматион* .</span><span class="sxs-lookup"><span data-stu-id="b75e3-288">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="b75e3-289">Удалите код и ссылки из файла CSHTML.</span><span class="sxs-lookup"><span data-stu-id="b75e3-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="b75e3-290">Удалите код подтверждения из `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="b75e3-290">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="b75e3-291">Использование другого приложения для добавления пользователей</span><span class="sxs-lookup"><span data-stu-id="b75e3-291">Use another app to add users</span></span>

<span data-ttu-id="b75e3-292">Предоставьте механизм для добавления пользователей за пределами веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="b75e3-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="b75e3-293">Ниже перечислены параметры для добавления пользователей.</span><span class="sxs-lookup"><span data-stu-id="b75e3-293">Options to add users include:</span></span>

* <span data-ttu-id="b75e3-294">Выделенное административное веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="b75e3-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="b75e3-295">Консольное приложение.</span><span class="sxs-lookup"><span data-stu-id="b75e3-295">A console app.</span></span>

<span data-ttu-id="b75e3-296">В следующем примере кода один из подходов к добавлению пользователей:</span><span class="sxs-lookup"><span data-stu-id="b75e3-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="b75e3-297">Список пользователей считывается в память.</span><span class="sxs-lookup"><span data-stu-id="b75e3-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="b75e3-298">Для каждого пользователя создается надежный уникальный пароль.</span><span class="sxs-lookup"><span data-stu-id="b75e3-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="b75e3-299">Пользователь будет добавлен в :::no-loc(Identity)::: базу данных.</span><span class="sxs-lookup"><span data-stu-id="b75e3-299">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="b75e3-300">Пользователь получает уведомления и сообщил об изменении пароля.</span><span class="sxs-lookup"><span data-stu-id="b75e3-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="b75e3-301">В следующем коде показано, как добавить пользователя:</span><span class="sxs-lookup"><span data-stu-id="b75e3-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="b75e3-302">Подобный подход можно выполнить для рабочих сценариев.</span><span class="sxs-lookup"><span data-stu-id="b75e3-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b75e3-303">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b75e3-303">Additional resources</span></span>

* [<span data-ttu-id="b75e3-304">Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="b75e3-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
