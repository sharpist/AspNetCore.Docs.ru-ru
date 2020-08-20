---
title: Метапакет Microsoft.AspNetCore.All для ASP.NET Core 2.0
author: Rick-Anderson
description: Метапакет Microsoft.AspNetCore.All не рекомендуется использовать для ASP.NET Core 2.1 и более поздних версий.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
no-loc:
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
uid: fundamentals/metapackage
ms.openlocfilehash: 133eee71fcd85a95ac53684c3c6e475a54592967
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634570"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="83f68-103">Метапакет Microsoft.AspNetCore.All для ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="83f68-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83f68-104">Метапакет `Microsoft.AspNetCore.All` не входит в состав ASP.NET Core 3.0 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="83f68-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="83f68-105">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/aspnet/Announcements/issues/314).</span><span class="sxs-lookup"><span data-stu-id="83f68-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="83f68-106">Для приложений, предназначенных для ASP.NET Core 2.1 и более поздних версий, вместо этого пакета рекомендуется использовать метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="83f68-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="83f68-107">См. раздел [Переход от Microsoft.AspNetCore.All к Microsoft.AspNetCore.App](#migrate) в этой статье.</span><span class="sxs-lookup"><span data-stu-id="83f68-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="83f68-108">Для этой функции нужен ASP.NET Core 2.x, нацеленный на .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="83f68-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="83f68-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) является метапакетом, который ссылается на общую платформу.</span><span class="sxs-lookup"><span data-stu-id="83f68-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="83f68-110">*Общая платформа* — это набор сборок (*DLL*-файлов), которые не находятся в папках приложения.</span><span class="sxs-lookup"><span data-stu-id="83f68-110">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="83f68-111">Чтобы запустить приложение, необходимо установить на компьютере общую платформу.</span><span class="sxs-lookup"><span data-stu-id="83f68-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="83f68-112">Дополнительную информацию см. в этой публикации об [общей платформе](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="83f68-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="83f68-113">Общая платформа, на которую ссылается `Microsoft.AspNetCore.All`, включает в себя следующее:</span><span class="sxs-lookup"><span data-stu-id="83f68-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="83f68-114">все пакеты, поддерживаемые командой ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="83f68-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="83f68-115">все пакеты, поддерживаемые Entity Framework Core;</span><span class="sxs-lookup"><span data-stu-id="83f68-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="83f68-116">внутренние и сторонние зависимости, используемые ASP.NET Core и Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="83f68-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="83f68-117">В пакет `Microsoft.AspNetCore.All` входят все компоненты ASP.NET Core 2.x и Entity Framework Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="83f68-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="83f68-118">Этот пакет по умолчанию используется для шаблонов проектов, предназначенных для ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="83f68-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="83f68-119">Номер версии метапакета `Microsoft.AspNetCore.All` соответствует минимальной версии ASP.NET Core и версии Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="83f68-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="83f68-120">Следующий файл *CSPROJ* ссылается на метапакет `Microsoft.AspNetCore.All` для ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="83f68-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="83f68-121">Неявное указание версий</span><span class="sxs-lookup"><span data-stu-id="83f68-121">Implicit versioning</span></span>

<span data-ttu-id="83f68-122">В ASP.NET Core 2.1 или более поздних версиях можно указывать ссылку на пакет `Microsoft.AspNetCore.All` без версии.</span><span class="sxs-lookup"><span data-stu-id="83f68-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="83f68-123">Если версия не указана, она задается неявно пакетом SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="83f68-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="83f68-124">Рекомендуется использовать неявное указание версии через пакет SDK, а не задавать номер версии явно в ссылке на пакет.</span><span class="sxs-lookup"><span data-stu-id="83f68-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="83f68-125">Если у вас возникли вопросы по этому подходу, оставьте комментарий на GitHub в [обсуждении неявного указания версий Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="83f68-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="83f68-126">Для переносимых приложений при неявном указании версии устанавливается значение `major.minor.0`.</span><span class="sxs-lookup"><span data-stu-id="83f68-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="83f68-127">Механизм выбора последней общей платформы запускает приложение на последней совместимой версии среди установленных общих платформ.</span><span class="sxs-lookup"><span data-stu-id="83f68-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="83f68-128">Чтобы гарантировать, что используется одна и та же версия при разработке, тестировании и эксплуатации, убедитесь, что установлена одинаковая версия общей платформы во всех средах.</span><span class="sxs-lookup"><span data-stu-id="83f68-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="83f68-129">Для автономных приложений неявный номер версии общей платформы, включенной в установленный пакет SDK, устанавливается в значение `major.minor.patch`.</span><span class="sxs-lookup"><span data-stu-id="83f68-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="83f68-130">Указание номера версии в ссылке на пакет `Microsoft.AspNetCore.All`**не** гарантирует, что выбирается эта версия общей платформы.</span><span class="sxs-lookup"><span data-stu-id="83f68-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="83f68-131">Например, пусть указана версия "2.1.1", но установлена версия "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="83f68-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="83f68-132">В этом случае приложение будет использовать версию "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="83f68-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="83f68-133">Хотя это не рекомендуется, можно отключить функцию выбора последней версии (для исправлений и (или) вспомогательных версий).</span><span class="sxs-lookup"><span data-stu-id="83f68-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="83f68-134">Дополнительную информацию см. в статье о [выборе последней версии на узле .NET](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="83f68-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="83f68-135">Чтобы использовать неявную версию `Microsoft.AspNetCore.All`, для пакета SDK проекта следует указать `Microsoft.NET.Sdk.Web` в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="83f68-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="83f68-136">Если указан пакет SDK `Microsoft.NET.Sdk` (`<Project Sdk="Microsoft.NET.Sdk">` в верхней части файла проекта), выводится следующее предупреждение:</span><span class="sxs-lookup"><span data-stu-id="83f68-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="83f68-137">*Предупреждение NU1604. Зависимость проекта Microsoft.AspNetCore.All не содержит включенную нижнюю границу. Включите нижнюю границу в версию зависимости, чтобы гарантировать согласованные результаты восстановления.*</span><span class="sxs-lookup"><span data-stu-id="83f68-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="83f68-138">Это известная проблема с пакетом SDK для .NET Core 2.1. Она будет устранена в пакете SDK для .NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="83f68-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="83f68-139">Переход от Microsoft.AspNetCore.All к Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="83f68-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="83f68-140">Следующие пакеты включены в пакет `Microsoft.AspNetCore.All`, но не выключены в пакет `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="83f68-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

<span data-ttu-id="83f68-141">При переходе от `Microsoft.AspNetCore.All` к `Microsoft.AspNetCore.App`, если приложение использует API из пакетов выше или включенных в них пакетов, необходимо добавить в проект ссылки на эти пакеты.</span><span class="sxs-lookup"><span data-stu-id="83f68-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="83f68-142">Любые зависимости пакетов выше, которые не являются зависимостями пакета `Microsoft.AspNetCore.App`, не добавляются автоматически.</span><span class="sxs-lookup"><span data-stu-id="83f68-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="83f68-143">Пример:</span><span class="sxs-lookup"><span data-stu-id="83f68-143">For example:</span></span>

* <span data-ttu-id="83f68-144">`StackExchange.Redis` как зависимость `Microsoft.Extensions.Caching.Redis`</span><span class="sxs-lookup"><span data-stu-id="83f68-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="83f68-145">`Microsoft.ApplicationInsights` как зависимость `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span><span class="sxs-lookup"><span data-stu-id="83f68-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="83f68-146">Обновление до версии ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="83f68-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="83f68-147">Мы рекомендуем перейти к использованию метапакета `Microsoft.AspNetCore.App` для 2.1 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="83f68-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="83f68-148">Чтобы продолжить использование метапакета `Microsoft.AspNetCore.All` и обеспечить развертывание версии с последними исправлениями, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="83f68-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="83f68-149">На компьютерах разработки и серверах сборки выполните следующее: Установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download) последней версии.</span><span class="sxs-lookup"><span data-stu-id="83f68-149">On development machines and build servers: Install the latest [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="83f68-150">На серверах развертывания выполните следующее: Установите [среду выполнения .NET Core](https://dotnet.microsoft.com/download) последней версии.</span><span class="sxs-lookup"><span data-stu-id="83f68-150">On deployment servers: Install the latest [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>
 <span data-ttu-id="83f68-151">Ваше приложение обновится до последней установленной версии при перезапуске.</span><span class="sxs-lookup"><span data-stu-id="83f68-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
