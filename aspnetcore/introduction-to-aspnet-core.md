---
title: Введение в ASP.NET Core
author: rick-anderson
description: Вводные сведения об ASP.NET Core, кроссплатформенной высокопроизводительной платформе с открытым исходным кодом для создания современных облачных интернет-приложений.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: index
ms.openlocfilehash: 7f46051193681ecac59428b77ca1e36830c7bb63
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "84466992"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="4ff68-103">Введение в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ff68-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="4ff68-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27), [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT) и [Шон Луттин (Shaun Luttin)](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="4ff68-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4ff68-105">ASP.NET Core является кроссплатформенной, высокопроизводительной средой с [открытым исходным кодом](https://github.com/dotnet/aspnetcore) для создания современных облачных приложений, подключенных к Интернету.</span><span class="sxs-lookup"><span data-stu-id="4ff68-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="4ff68-106">ASP.NET Core позволяет выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="4ff68-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="4ff68-107">Создавать веб-приложения и службы, приложения [Интернета вещей](https://www.microsoft.com/internet-of-things/) (IoT) и серверные части для мобильных приложений.</span><span class="sxs-lookup"><span data-stu-id="4ff68-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="4ff68-108">Использовать избранные средства разработки в Windows, macOS и Linux.</span><span class="sxs-lookup"><span data-stu-id="4ff68-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="4ff68-109">Выполнять развертывания в облаке или локальной среде.</span><span class="sxs-lookup"><span data-stu-id="4ff68-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="4ff68-110">Запускать в [.NET Core](/dotnet/core/introduction).</span><span class="sxs-lookup"><span data-stu-id="4ff68-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="4ff68-111">Преимущества, обеспечиваемые ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ff68-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="4ff68-112">Миллионы разработчиков использовали и продолжают использовать [ASP.NET 4.x](/aspnet/overview) для создания веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="4ff68-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="4ff68-113">ASP.NET Core — это модификация ASP.NET 4.x с архитектурными изменениями, формирующими более рациональную и более модульную платформу.</span><span class="sxs-lookup"><span data-stu-id="4ff68-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="4ff68-114">Создание веб-API и пользовательского веб-интерфейса с помощью ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="4ff68-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="4ff68-115">ASP.NET Core MVC предоставляет функции, которые позволяют создавать [веб-интерфейсы API](xref:tutorials/first-web-api) и [веб-приложения](xref:tutorials/razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="4ff68-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="4ff68-116">[Шаблон Model-View-Controller (MVC)](xref:mvc/overview) помогает сделать веб-API и веб-приложения тестируемыми.</span><span class="sxs-lookup"><span data-stu-id="4ff68-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="4ff68-117">[Razor Pages](xref:razor-pages/index) — это основанная на страницах модель программирования, которая упрощает и повышает эффективность создания пользовательского веб-интерфейса.</span><span class="sxs-lookup"><span data-stu-id="4ff68-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="4ff68-118">[Разметка Razor](xref:mvc/views/razor) предоставляет эффективный синтаксис для [страниц Razor](xref:razor-pages/index) и [представлений MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="4ff68-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="4ff68-119">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="4ff68-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="4ff68-120">Благодаря встроенной поддержке [нескольких форматов данных и согласованию содержимого](xref:web-api/advanced/formatting) веб-API становятся доступными для множества клиентов, включая браузеры и мобильные устройства.</span><span class="sxs-lookup"><span data-stu-id="4ff68-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="4ff68-121">[Привязка модели](xref:mvc/models/model-binding) автоматически сопоставляет данные из HTTP-запросов с параметрами методов действия.</span><span class="sxs-lookup"><span data-stu-id="4ff68-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="4ff68-122">[Проверка модели](xref:mvc/models/validation) автоматически выполняется на стороне сервера и клиента.</span><span class="sxs-lookup"><span data-stu-id="4ff68-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="4ff68-123">Клиентская разработка</span><span class="sxs-lookup"><span data-stu-id="4ff68-123">Client-side development</span></span>

<span data-ttu-id="4ff68-124">ASP.NET Core легко интегрируется с популярными клиентскими платформами и библиотеками, включая [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) и [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="4ff68-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="4ff68-125">Подробнее см. <xref:blazor/index> и связанные материалы о *разработке на стороне клиента*.</span><span class="sxs-lookup"><span data-stu-id="4ff68-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="4ff68-126">Целевые версии платформы ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ff68-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="4ff68-127">ASP.NET Core 3.x и более поздние версии может выполняться только в .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ff68-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="4ff68-128">Как правило, ASP.NET Core состоит из библиотек [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="4ff68-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="4ff68-129">Библиотеки, написанные на .NET Standard 2.0 под управлением любой [платформы .NET с реализацией .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="4ff68-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="4ff68-130">При использовании .NET Core существуют некоторые преимущества, и их число увеличивается с каждым выпуском.</span><span class="sxs-lookup"><span data-stu-id="4ff68-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="4ff68-131">Преимущества .NET Core по сравнению с .NET Framework включают:</span><span class="sxs-lookup"><span data-stu-id="4ff68-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="4ff68-132">Кроссплатформенность.</span><span class="sxs-lookup"><span data-stu-id="4ff68-132">Cross-platform.</span></span> <span data-ttu-id="4ff68-133">Выполняется в Windows, macOS и Linux.</span><span class="sxs-lookup"><span data-stu-id="4ff68-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="4ff68-134">Повышение производительности</span><span class="sxs-lookup"><span data-stu-id="4ff68-134">Improved performance</span></span>
* [<span data-ttu-id="4ff68-135">Управление параллельными версиями</span><span class="sxs-lookup"><span data-stu-id="4ff68-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="4ff68-136">Новые интерфейсы API</span><span class="sxs-lookup"><span data-stu-id="4ff68-136">New APIs</span></span>
* <span data-ttu-id="4ff68-137">Открытый код</span><span class="sxs-lookup"><span data-stu-id="4ff68-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="4ff68-138">Рекомендуемая схема обучения</span><span class="sxs-lookup"><span data-stu-id="4ff68-138">Recommended learning path</span></span>

<span data-ttu-id="4ff68-139">Для знакомства с разработкой приложений ASP.NET Core рекомендуется изучить следующую последовательность учебников.</span><span class="sxs-lookup"><span data-stu-id="4ff68-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="4ff68-140">Пройдите учебник по тому типу приложения, которое вы собираетесь разрабатывать или обслуживать.</span><span class="sxs-lookup"><span data-stu-id="4ff68-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="4ff68-141">Тип приложения</span><span class="sxs-lookup"><span data-stu-id="4ff68-141">App type</span></span>  |<span data-ttu-id="4ff68-142">Сценарий</span><span class="sxs-lookup"><span data-stu-id="4ff68-142">Scenario</span></span>  |<span data-ttu-id="4ff68-143">Учебник</span><span class="sxs-lookup"><span data-stu-id="4ff68-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="4ff68-144">Веб-приложение</span><span class="sxs-lookup"><span data-stu-id="4ff68-144">Web app</span></span>                   | <span data-ttu-id="4ff68-145">Разработка нового веб-интерфейса на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="4ff68-145">New server-side web UI development</span></span> |[<span data-ttu-id="4ff68-146">Начало работы с Razor Pages</span><span class="sxs-lookup"><span data-stu-id="4ff68-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="4ff68-147">Веб-приложение</span><span class="sxs-lookup"><span data-stu-id="4ff68-147">Web app</span></span>                   | <span data-ttu-id="4ff68-148">Обслуживание приложения MVC</span><span class="sxs-lookup"><span data-stu-id="4ff68-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="4ff68-149">Начало работы с MVC</span><span class="sxs-lookup"><span data-stu-id="4ff68-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="4ff68-150">Веб-приложение</span><span class="sxs-lookup"><span data-stu-id="4ff68-150">Web app</span></span>                   | <span data-ttu-id="4ff68-151">Разработка веб-интерфейса на стороне клиента</span><span class="sxs-lookup"><span data-stu-id="4ff68-151">Client-side web UI development</span></span> |[<span data-ttu-id="4ff68-152">Начало работы с Blazor</span><span class="sxs-lookup"><span data-stu-id="4ff68-152">Get started with Blazor</span></span>](xref:tutorials/first-blazor-app) |
   |<span data-ttu-id="4ff68-153">Веб-интерфейс API</span><span class="sxs-lookup"><span data-stu-id="4ff68-153">Web API</span></span>                   | <span data-ttu-id="4ff68-154">Службы HTTP RESTFUL</span><span class="sxs-lookup"><span data-stu-id="4ff68-154">RESTful HTTP services</span></span> |<span data-ttu-id="4ff68-155">[Создание веб-API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="4ff68-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="4ff68-156">Приложение удаленного вызова процедур</span><span class="sxs-lookup"><span data-stu-id="4ff68-156">Remote Procedure Call app</span></span> | <span data-ttu-id="4ff68-157">Разработка в соответствии с парадигмой "Сначала контракт" с использованием Protocol Buffers</span><span class="sxs-lookup"><span data-stu-id="4ff68-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="4ff68-158">Начало работы со службой gRPC</span><span class="sxs-lookup"><span data-stu-id="4ff68-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="4ff68-159">Приложение режима реального времени</span><span class="sxs-lookup"><span data-stu-id="4ff68-159">Real-time app</span></span>             | <span data-ttu-id="4ff68-160">Двунаправленный обмен данными между сервером и подключенными к нему клиентами</span><span class="sxs-lookup"><span data-stu-id="4ff68-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="4ff68-161">Начало работы с SignalR</span><span class="sxs-lookup"><span data-stu-id="4ff68-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="4ff68-162">Пройдите учебник, посвященный основам доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="4ff68-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="4ff68-163">Сценарий</span><span class="sxs-lookup"><span data-stu-id="4ff68-163">Scenario</span></span>  |<span data-ttu-id="4ff68-164">Учебник</span><span class="sxs-lookup"><span data-stu-id="4ff68-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="4ff68-165">Разработка нового приложения</span><span class="sxs-lookup"><span data-stu-id="4ff68-165">New development</span></span>        |[<span data-ttu-id="4ff68-166">Razor Pages с Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4ff68-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="4ff68-167">Обслуживание приложения MVC</span><span class="sxs-lookup"><span data-stu-id="4ff68-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="4ff68-168">MVC с Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4ff68-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="4ff68-169">Ознакомьтесь с обзором [основ](xref:fundamentals/index) ASP.NET Core, относящихся ко всем типам приложений.</span><span class="sxs-lookup"><span data-stu-id="4ff68-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="4ff68-170">Просмотрите содержание, чтобы найти другие интересующие вас темы.</span><span class="sxs-lookup"><span data-stu-id="4ff68-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="4ff68-171">&dagger;Доступен [интерактивный учебник по веб-API](/learn/modules/build-web-api-net-core).</span><span class="sxs-lookup"><span data-stu-id="4ff68-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="4ff68-172">Локальная установка средств разработки не требуется.</span><span class="sxs-lookup"><span data-stu-id="4ff68-172">No local installation of development tools is required.</span></span> <span data-ttu-id="4ff68-173">Код выполняется в [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) в браузере, а для тестирования используется [curl](https://curl.haxx.se/).</span><span class="sxs-lookup"><span data-stu-id="4ff68-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="4ff68-174">Миграция с .NET Framework</span><span class="sxs-lookup"><span data-stu-id="4ff68-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="4ff68-175">Справочное руководство по миграции приложений ASP.NET 4.x на ASP.NET Core см. в статье <xref:migration/proper-to-2x/index>.</span><span class="sxs-lookup"><span data-stu-id="4ff68-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4ff68-176">ASP.NET Core является кроссплатформенной, высокопроизводительной средой с [открытым исходным кодом](https://github.com/dotnet/aspnetcore) для создания современных облачных приложений, подключенных к Интернету.</span><span class="sxs-lookup"><span data-stu-id="4ff68-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="4ff68-177">ASP.NET Core позволяет выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="4ff68-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="4ff68-178">Создавать веб-приложения и службы, приложения [Интернета вещей](https://www.microsoft.com/internet-of-things/) (IoT) и серверные части для мобильных приложений.</span><span class="sxs-lookup"><span data-stu-id="4ff68-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="4ff68-179">Использовать избранные средства разработки в Windows, macOS и Linux.</span><span class="sxs-lookup"><span data-stu-id="4ff68-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="4ff68-180">Выполнять развертывания в облаке или локальной среде.</span><span class="sxs-lookup"><span data-stu-id="4ff68-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="4ff68-181">Работать в [.NET Core или .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="4ff68-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="4ff68-182">Преимущества, обеспечиваемые ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ff68-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="4ff68-183">Миллионы разработчиков использовали и продолжают использовать [ASP.NET 4.x](/aspnet/overview) для создания веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="4ff68-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="4ff68-184">ASP.NET Core — это модификация ASP.NET 4.x с архитектурными изменениями, формирующими более рациональную и более модульную платформу.</span><span class="sxs-lookup"><span data-stu-id="4ff68-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="4ff68-185">Создание веб-API и пользовательского веб-интерфейса с помощью ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="4ff68-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="4ff68-186">ASP.NET Core MVC предоставляет функции, которые позволяют создавать [веб-интерфейсы API](xref:tutorials/first-web-api) и [веб-приложения](xref:tutorials/razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="4ff68-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="4ff68-187">[Шаблон Model-View-Controller (MVC)](xref:mvc/overview) помогает сделать веб-API и веб-приложения тестируемыми.</span><span class="sxs-lookup"><span data-stu-id="4ff68-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="4ff68-188">[Razor Pages](xref:razor-pages/index) — это основанная на страницах модель программирования, которая упрощает и повышает эффективность создания пользовательского веб-интерфейса.</span><span class="sxs-lookup"><span data-stu-id="4ff68-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="4ff68-189">[Разметка Razor](xref:mvc/views/razor) предоставляет эффективный синтаксис для [страниц Razor](xref:razor-pages/index) и [представлений MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="4ff68-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="4ff68-190">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="4ff68-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="4ff68-191">Благодаря встроенной поддержке [нескольких форматов данных и согласованию содержимого](xref:web-api/advanced/formatting) веб-API становятся доступными для множества клиентов, включая браузеры и мобильные устройства.</span><span class="sxs-lookup"><span data-stu-id="4ff68-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="4ff68-192">[Привязка модели](xref:mvc/models/model-binding) автоматически сопоставляет данные из HTTP-запросов с параметрами методов действия.</span><span class="sxs-lookup"><span data-stu-id="4ff68-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="4ff68-193">[Проверка модели](xref:mvc/models/validation) автоматически выполняется на стороне сервера и клиента.</span><span class="sxs-lookup"><span data-stu-id="4ff68-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="4ff68-194">Клиентская разработка</span><span class="sxs-lookup"><span data-stu-id="4ff68-194">Client-side development</span></span>

<span data-ttu-id="4ff68-195">ASP.NET Core легко интегрируется с популярными клиентскими платформами и библиотеками, включая [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) и [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="4ff68-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="4ff68-196">Подробнее см. <xref:blazor/index> и связанные материалы о *разработке на стороне клиента*.</span><span class="sxs-lookup"><span data-stu-id="4ff68-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="4ff68-197">ASP.NET Core для платформы .NET Framework</span><span class="sxs-lookup"><span data-stu-id="4ff68-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="4ff68-198">Приложения ASP.NET Core 2.x могут выполняться в .NET Core или .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="4ff68-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="4ff68-199">Приложения ASP.NET Core, предназначенные для .NET Framework, не являются кроссплатформенными &mdash; они выполняются только в Windows.</span><span class="sxs-lookup"><span data-stu-id="4ff68-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="4ff68-200">Как правило, ASP.NET Core 2.x состоит из библиотек [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="4ff68-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="4ff68-201">Библиотеки, написанные на .NET Standard 2.0 под управлением любой [платформы .NET с реализацией .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="4ff68-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="4ff68-202">ASP.NET Core 2.x поддерживается в версиях .NET Framework с реализацией .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="4ff68-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="4ff68-203">Рекомендуется использовать последнюю версию .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="4ff68-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="4ff68-204">.NET Framework 4.6.1 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="4ff68-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="4ff68-205">ASP.NET Core версии 3.0 и более поздних будут выполняться только в .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ff68-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="4ff68-206">Дополнительные сведения об этом изменении см. в разделе [Первое знакомство с предстоящими изменениями в ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="4ff68-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="4ff68-207">При использовании .NET Core существуют некоторые преимущества, и их число увеличивается с каждым выпуском.</span><span class="sxs-lookup"><span data-stu-id="4ff68-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="4ff68-208">Преимущества .NET Core по сравнению с .NET Framework включают:</span><span class="sxs-lookup"><span data-stu-id="4ff68-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="4ff68-209">Кроссплатформенность.</span><span class="sxs-lookup"><span data-stu-id="4ff68-209">Cross-platform.</span></span> <span data-ttu-id="4ff68-210">Выполняется на macOS, Linux и Windows.</span><span class="sxs-lookup"><span data-stu-id="4ff68-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="4ff68-211">Повышение производительности</span><span class="sxs-lookup"><span data-stu-id="4ff68-211">Improved performance</span></span>
* [<span data-ttu-id="4ff68-212">Управление параллельными версиями</span><span class="sxs-lookup"><span data-stu-id="4ff68-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="4ff68-213">Новые интерфейсы API</span><span class="sxs-lookup"><span data-stu-id="4ff68-213">New APIs</span></span>
* <span data-ttu-id="4ff68-214">Открытый код</span><span class="sxs-lookup"><span data-stu-id="4ff68-214">Open source</span></span>

<span data-ttu-id="4ff68-215">Благодаря [пакету обеспечения совместимости Windows](/dotnet/core/porting/windows-compat-pack) в .NET Core доступны тысячи API-интерфейсов, созданных только для Windows, что позволяет расширить диапазон API при переходе с .NET Framework на .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ff68-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="4ff68-216">Эти API-интерфейсы не были доступны в .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="4ff68-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="4ff68-217">Рекомендуемая схема обучения</span><span class="sxs-lookup"><span data-stu-id="4ff68-217">Recommended learning path</span></span>

<span data-ttu-id="4ff68-218">Для знакомства с разработкой приложений ASP.NET Core рекомендуется изучить следующую последовательность учебников и статей.</span><span class="sxs-lookup"><span data-stu-id="4ff68-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="4ff68-219">Пройдите учебник по тому типу приложения, которое вы собираетесь разрабатывать или обслуживать.</span><span class="sxs-lookup"><span data-stu-id="4ff68-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="4ff68-220">Тип приложения</span><span class="sxs-lookup"><span data-stu-id="4ff68-220">App type</span></span>  |<span data-ttu-id="4ff68-221">Сценарий</span><span class="sxs-lookup"><span data-stu-id="4ff68-221">Scenario</span></span>  |<span data-ttu-id="4ff68-222">Учебник</span><span class="sxs-lookup"><span data-stu-id="4ff68-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="4ff68-223">Веб-приложение</span><span class="sxs-lookup"><span data-stu-id="4ff68-223">Web app</span></span>                   | <span data-ttu-id="4ff68-224">Разработка нового приложения</span><span class="sxs-lookup"><span data-stu-id="4ff68-224">For new development</span></span>        |[<span data-ttu-id="4ff68-225">Начало работы с Razor Pages</span><span class="sxs-lookup"><span data-stu-id="4ff68-225">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="4ff68-226">Веб-приложение</span><span class="sxs-lookup"><span data-stu-id="4ff68-226">Web app</span></span>                   | <span data-ttu-id="4ff68-227">Обслуживание приложения MVC</span><span class="sxs-lookup"><span data-stu-id="4ff68-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="4ff68-228">Начало работы с MVC</span><span class="sxs-lookup"><span data-stu-id="4ff68-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="4ff68-229">Веб-интерфейс API</span><span class="sxs-lookup"><span data-stu-id="4ff68-229">Web API</span></span>                   |                            |<span data-ttu-id="4ff68-230">[Создание веб-API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="4ff68-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="4ff68-231">Приложение режима реального времени</span><span class="sxs-lookup"><span data-stu-id="4ff68-231">Real-time app</span></span>             |                            |[<span data-ttu-id="4ff68-232">Начало работы с SignalR</span><span class="sxs-lookup"><span data-stu-id="4ff68-232">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="4ff68-233">Пройдите учебник, посвященный основам доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="4ff68-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="4ff68-234">Сценарий</span><span class="sxs-lookup"><span data-stu-id="4ff68-234">Scenario</span></span>  |<span data-ttu-id="4ff68-235">Учебник</span><span class="sxs-lookup"><span data-stu-id="4ff68-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="4ff68-236">Разработка нового приложения</span><span class="sxs-lookup"><span data-stu-id="4ff68-236">For new development</span></span>        |<span data-ttu-id="4ff68-237">[Razor Pages с Entity Framework Core](xref:data/ef-rp/intro)</span><span class="sxs-lookup"><span data-stu-id="4ff68-237">[Razor Pages with Entity Framework Core](xref:data/ef-rp/intro)</span></span> |
   | <span data-ttu-id="4ff68-238">Обслуживание приложения MVC</span><span class="sxs-lookup"><span data-stu-id="4ff68-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="4ff68-239">MVC с Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4ff68-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="4ff68-240">Ознакомьтесь с обзором [основ](xref:fundamentals/index) ASP.NET Core, относящихся ко всем типам приложений.</span><span class="sxs-lookup"><span data-stu-id="4ff68-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="4ff68-241">Просмотрите содержание, чтобы найти другие интересующие вас темы.</span><span class="sxs-lookup"><span data-stu-id="4ff68-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="4ff68-242">&dagger; Доступен новый [учебник по веб-API с прохождением в браузере](/learn/modules/build-web-api-net-core), не требующий установки локальной интегрированной среды разработки.</span><span class="sxs-lookup"><span data-stu-id="4ff68-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="4ff68-243">Код выполняется в [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), а для тестирования используется [curl](https://curl.haxx.se/).</span><span class="sxs-lookup"><span data-stu-id="4ff68-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="4ff68-244">Миграция с .NET Framework</span><span class="sxs-lookup"><span data-stu-id="4ff68-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="4ff68-245">Справочное руководство по миграции приложений ASP.NET на ASP.NET Core см. в статье <xref:migration/proper-to-2x/index>.</span><span class="sxs-lookup"><span data-stu-id="4ff68-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="4ff68-246">Загрузка примера</span><span class="sxs-lookup"><span data-stu-id="4ff68-246">How to download a sample</span></span>

<span data-ttu-id="4ff68-247">Многие статьи и учебники содержат ссылки на примеры кода.</span><span class="sxs-lookup"><span data-stu-id="4ff68-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="4ff68-248">[Загрузите ZIP-файл репозитория ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="4ff68-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="4ff68-249">Распакуйте файл *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="4ff68-249">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="4ff68-250">Перейдите в папку примера по URL-адресу, указанному в примере.</span><span class="sxs-lookup"><span data-stu-id="4ff68-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="4ff68-251">Директивы препроцессора в примере кода</span><span class="sxs-lookup"><span data-stu-id="4ff68-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="4ff68-252">Для демонстрации нескольких сценариев в примерах приложений используются директивы препроцессора `#define` и `#if-#else/#elif-#endif`, выборочно компилирующие и запускающие разные фрагменты примеров кода.</span><span class="sxs-lookup"><span data-stu-id="4ff68-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="4ff68-253">В примерах, где применяется этот подход, задайте в начале файлов C# директиву `#define` для определения символа, связанного со сценарием, который нужно запустить.</span><span class="sxs-lookup"><span data-stu-id="4ff68-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="4ff68-254">Для запуска сценария в некоторых примерах потребуется определить символ в начале нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="4ff68-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="4ff68-255">Например, в следующем списке символов `#define` видно, что доступно четыре сценария (один сценарий на символ).</span><span class="sxs-lookup"><span data-stu-id="4ff68-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="4ff68-256">В текущем примере конфигурации запускается сценарий `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="4ff68-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="4ff68-257">Чтобы запустить в примере сценарий `ExpandDefault`, задайте символ `ExpandDefault` и оставьте остальные символы раскомментированными:</span><span class="sxs-lookup"><span data-stu-id="4ff68-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="4ff68-258">Дополнительные сведения об использовании [директив препроцессора C#](/dotnet/csharp/language-reference/preprocessor-directives/) для выборочной компиляции фрагментов кода см. в разделах [#define (Справочник по C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) и [#if (Справочник по C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="4ff68-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="4ff68-259">Регионы в примере кода</span><span class="sxs-lookup"><span data-stu-id="4ff68-259">Regions in sample code</span></span>

<span data-ttu-id="4ff68-260">Некоторые примеры приложений содержат фрагменты кода внутри директив C# [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) и [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion).</span><span class="sxs-lookup"><span data-stu-id="4ff68-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="4ff68-261">Система сборки документации вставляет эти регионы в обработанные разделы документации.</span><span class="sxs-lookup"><span data-stu-id="4ff68-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="4ff68-262">Названия регионов обычно содержат слово "фрагмент".</span><span class="sxs-lookup"><span data-stu-id="4ff68-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="4ff68-263">В следующем примере показан регион с именем `snippet_WebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="4ff68-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="4ff68-264">На предыдущий фрагмент кода C# указывает ссылка в следующей строке в файле Markdown раздела:</span><span class="sxs-lookup"><span data-stu-id="4ff68-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="4ff68-265">Вы можете спокойно проигнорировать или удалить директивы `#region` и `#endregion` вокруг кода.</span><span class="sxs-lookup"><span data-stu-id="4ff68-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="4ff68-266">Не изменяйте код внутри этих директив, если планируете запустить примеры сценариев, описанные в разделе.</span><span class="sxs-lookup"><span data-stu-id="4ff68-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="4ff68-267">Вы можете изменить код, экспериментируя с другими сценариями.</span><span class="sxs-lookup"><span data-stu-id="4ff68-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="4ff68-268">Дополнительные сведения см. в разделе[Участие в написании документации ASP.NET: Фрагменты кода](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="4ff68-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="4ff68-269">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="4ff68-269">Next steps</span></span>

<span data-ttu-id="4ff68-270">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="4ff68-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="4ff68-271">Основы ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ff68-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="4ff68-272">[В еженедельном выпуске ASP.NET Community Standup](https://live.asp.net/) рассматривается ход работы и планы команды.</span><span class="sxs-lookup"><span data-stu-id="4ff68-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="4ff68-273">Помимо этого, публикуются новые блоги и стороннее программное обеспечение.</span><span class="sxs-lookup"><span data-stu-id="4ff68-273">It features new blogs and third-party software.</span></span>
