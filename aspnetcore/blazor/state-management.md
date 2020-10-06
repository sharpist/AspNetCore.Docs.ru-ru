---
title: Управление состоянием ASP.NET Core Blazor
author: guardrex
description: Сведения о том, как сохранить состояние в приложениях Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: a74f056447839c4cf057948f26a9ece9b5799656
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606704"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="702f7-103">Управление состоянием ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="702f7-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="702f7-104">Авторы: [Стив Сандерсон (Steve Sanderson)](https://github.com/SteveSandersonMS) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="702f7-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="702f7-105">Данные о состоянии пользователя, создаваемые в приложении Blazor WebAssembly, хранятся в памяти браузера.</span><span class="sxs-lookup"><span data-stu-id="702f7-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="702f7-106">Ниже приведены примеры данных о состоянии пользователя, хранящиеся в памяти браузера.</span><span class="sxs-lookup"><span data-stu-id="702f7-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="702f7-107">Иерархия экземпляров компонента и их последних выходных данных отрисовки в преобразованном для просмотра пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="702f7-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="702f7-108">Значения полей и свойств в экземплярах компонента.</span><span class="sxs-lookup"><span data-stu-id="702f7-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="702f7-109">Данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы.</span><span class="sxs-lookup"><span data-stu-id="702f7-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="702f7-110">Значения, заданные через вызовы [взаимодействия с JavaScript](xref:blazor/call-javascript-from-dotnet).</span><span class="sxs-lookup"><span data-stu-id="702f7-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="702f7-111">Когда пользователь закрывает и повторно открывает браузер или перезагружает страницу, данные о состоянии пользователя, содержащиеся в памяти браузера, утрачиваются.</span><span class="sxs-lookup"><span data-stu-id="702f7-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="702f7-112">Сохранение состояния в сеансах браузера</span><span class="sxs-lookup"><span data-stu-id="702f7-112">Persist state across browser sessions</span></span>

<span data-ttu-id="702f7-113">Как правило, состояния поддерживаются между сеансами браузера, где пользователи активно могут создавать данные, а не просто считывать уже существующие данные.</span><span class="sxs-lookup"><span data-stu-id="702f7-113">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="702f7-114">Чтобы сохранять состояние между сеансами браузера, приложение должно хранить данные не в памяти браузера, а в другом месте.</span><span class="sxs-lookup"><span data-stu-id="702f7-114">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="702f7-115">Сохраняемость состояния не обеспечивается автоматически.</span><span class="sxs-lookup"><span data-stu-id="702f7-115">State persistence isn't automatic.</span></span> <span data-ttu-id="702f7-116">При разработке приложения необходимо выполнить определенные действия для реализации сохраняемости данных с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="702f7-116">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="702f7-117">Сохраняемость данных, как правило, требуется только для состояний высокой ценности, на создание которых пользователь затратил значительные усилия.</span><span class="sxs-lookup"><span data-stu-id="702f7-117">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="702f7-118">В следующих примерах сохранение состояния экономит время или средства в коммерческих действиях.</span><span class="sxs-lookup"><span data-stu-id="702f7-118">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="702f7-119">Веб-формы с многоэтапным заполнением. Пользователю потребуется много времени для повторного ввода данных на нескольких завершенных этапах заполнения таких форм, если данные об их состоянии будут утеряны.</span><span class="sxs-lookup"><span data-stu-id="702f7-119">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="702f7-120">В этом сценарии пользователь потеряет данные о своем состоянии, если он выйдет из формы и вернется в нее позже.</span><span class="sxs-lookup"><span data-stu-id="702f7-120">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="702f7-121">Корзины для покупок. Любой коммерчески важный компонент приложения, который представляет потенциальную прибыль.</span><span class="sxs-lookup"><span data-stu-id="702f7-121">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="702f7-122">Пользователь, который теряет свое состояние, и, следовательно, свою корзину с покупками, может приобрести меньше товаров или услуг, когда вернется на сайт позже.</span><span class="sxs-lookup"><span data-stu-id="702f7-122">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="702f7-123">Приложение может сохранять только *состояния приложения*.</span><span class="sxs-lookup"><span data-stu-id="702f7-123">An app can only persist *app state*.</span></span> <span data-ttu-id="702f7-124">Пользовательские интерфейсы не могут быть сохранены, например экземпляры компонентов и их деревья отрисовки.</span><span class="sxs-lookup"><span data-stu-id="702f7-124">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="702f7-125">Компоненты и деревья отрисовки обычно не являются сериализуемыми.</span><span class="sxs-lookup"><span data-stu-id="702f7-125">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="702f7-126">Чтобы сохранить состояние пользовательского интерфейса, например развернутые узлы элементов управления иерархического представления, в приложении должен быть пользовательский код для моделирования поведения этого интерфейса как сериализуемого состояния приложения.</span><span class="sxs-lookup"><span data-stu-id="702f7-126">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="702f7-127">Место сохранения состояния</span><span class="sxs-lookup"><span data-stu-id="702f7-127">Where to persist state</span></span>

<span data-ttu-id="702f7-128">Данные о состоянии могут храниться в общих расположениях:</span><span class="sxs-lookup"><span data-stu-id="702f7-128">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="702f7-129">Хранилище на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="702f7-129">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="702f7-130">URL-адрес</span><span class="sxs-lookup"><span data-stu-id="702f7-130">URL</span></span>](#url)
* [<span data-ttu-id="702f7-131">Хранилище браузера</span><span class="sxs-lookup"><span data-stu-id="702f7-131">Browser storage</span></span>](#browser-storage)
* [<span data-ttu-id="702f7-132">Служба контейнеров состояния в памяти</span><span class="sxs-lookup"><span data-stu-id="702f7-132">In-memory state container service</span></span>](#in-memory-state-container-service)

### <a name="server-side-storage"></a><span data-ttu-id="702f7-133">Хранилище на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="702f7-133">Server-side storage</span></span>

<span data-ttu-id="702f7-134">Для постоянного хранения данных для нескольких пользователей и устройств приложение может использовать независимое хранилище на стороне сервера, доступ к которому осуществляется через веб-API.</span><span class="sxs-lookup"><span data-stu-id="702f7-134">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="702f7-135">Доступны следующие варианты:</span><span class="sxs-lookup"><span data-stu-id="702f7-135">Options include:</span></span>

* <span data-ttu-id="702f7-136">Хранилище BLOB-объектов</span><span class="sxs-lookup"><span data-stu-id="702f7-136">Blob storage</span></span>
* <span data-ttu-id="702f7-137">Хранилище значений ключей</span><span class="sxs-lookup"><span data-stu-id="702f7-137">Key-value storage</span></span>
* <span data-ttu-id="702f7-138">Реляционная база данных</span><span class="sxs-lookup"><span data-stu-id="702f7-138">Relational database</span></span>
* <span data-ttu-id="702f7-139">Хранилище таблиц</span><span class="sxs-lookup"><span data-stu-id="702f7-139">Table storage</span></span>

<span data-ttu-id="702f7-140">После сохранения данных состояние пользователя сохраняется и становится доступным во всех новых сеансах браузера.</span><span class="sxs-lookup"><span data-stu-id="702f7-140">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="702f7-141">Поскольку приложения Blazor WebAssembly полностью выполняются в браузере пользователя, им требуются дополнительные меры для доступа к защищенным внешним системам, например службам хранилища и базам данных.</span><span class="sxs-lookup"><span data-stu-id="702f7-141">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="702f7-142">Защита приложений Blazor WebAssembly обеспечивается аналогично защите одностраничных приложений (SPA).</span><span class="sxs-lookup"><span data-stu-id="702f7-142">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="702f7-143">Как правило, приложение выполняет проверку подлинности пользователя с помощью [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/), а затем взаимодействует со службами хранилища и базами данных, отправляя вызовы веб-API в серверное приложение.</span><span class="sxs-lookup"><span data-stu-id="702f7-143">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="702f7-144">Приложение на стороне сервера обеспечивает перенос данных между приложением Blazor WebAssembly и службой хранилища или базой данных.</span><span class="sxs-lookup"><span data-stu-id="702f7-144">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="702f7-145">Приложение Blazor WebAssembly поддерживает временное подключение к приложению на стороне сервера, в то время как приложение на стороне сервера подключено к хранилищу постоянно.</span><span class="sxs-lookup"><span data-stu-id="702f7-145">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="702f7-146">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="702f7-146">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="702f7-147">Статьи по *безопасности и Identity* Blazor</span><span class="sxs-lookup"><span data-stu-id="702f7-147">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="702f7-148">Дополнительные сведения о вариантах хранения данных в Azure см. здесь:</span><span class="sxs-lookup"><span data-stu-id="702f7-148">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="702f7-149">Базы данных Azure</span><span class="sxs-lookup"><span data-stu-id="702f7-149">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="702f7-150">Документация по хранилищу Azure</span><span class="sxs-lookup"><span data-stu-id="702f7-150">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="702f7-151">URL-адрес</span><span class="sxs-lookup"><span data-stu-id="702f7-151">URL</span></span>

<span data-ttu-id="702f7-152">Для временных данных, представляющих состояние навигации, моделируют данные как часть URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="702f7-152">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="702f7-153">Ниже приведены примеры данных о состоянии пользователя, которые моделируются в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="702f7-153">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="702f7-154">Идентификатор просматриваемой сущности.</span><span class="sxs-lookup"><span data-stu-id="702f7-154">The ID of a viewed entity.</span></span>
* <span data-ttu-id="702f7-155">Номер текущей страницы в постраничной сетке.</span><span class="sxs-lookup"><span data-stu-id="702f7-155">The current page number in a paged grid.</span></span>

<span data-ttu-id="702f7-156">Если пользователь вручную перезагружает страницу, содержимое адресной строки браузера сохраняется.</span><span class="sxs-lookup"><span data-stu-id="702f7-156">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="702f7-157">Сведения об определении шаблонов URL-адресов с помощью директивы [`@page`](xref:mvc/views/razor#page) см. в статье <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="702f7-157">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="702f7-158">Хранилище браузера</span><span class="sxs-lookup"><span data-stu-id="702f7-158">Browser storage</span></span>

<span data-ttu-id="702f7-159">Для хранения временных данных, создаваемых пользователем, обычно используются коллекции браузера [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) и [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span><span class="sxs-lookup"><span data-stu-id="702f7-159">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="702f7-160">`localStorage` ограничивается окном браузера.</span><span class="sxs-lookup"><span data-stu-id="702f7-160">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="702f7-161">Если пользователь перезагружает страницу или закрывает и снова открывает браузер, состояние сохраняется.</span><span class="sxs-lookup"><span data-stu-id="702f7-161">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="702f7-162">Если пользователь открывает несколько вкладок браузера, это состояние совместно используется на нескольких вкладках.</span><span class="sxs-lookup"><span data-stu-id="702f7-162">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="702f7-163">Данные сохраняются в `localStorage` до тех пор, пока они не будут явно очищены.</span><span class="sxs-lookup"><span data-stu-id="702f7-163">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="702f7-164">`sessionStorage` ограничивается вкладкой браузера. Если пользователь перезагружает вкладку, состояние сохраняется.</span><span class="sxs-lookup"><span data-stu-id="702f7-164">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="702f7-165">Если пользователь закрывает вкладку или браузер, состояние теряется.</span><span class="sxs-lookup"><span data-stu-id="702f7-165">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="702f7-166">Если пользователь открывает несколько вкладок браузера, каждая вкладка имеет собственную независимую версию данных.</span><span class="sxs-lookup"><span data-stu-id="702f7-166">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="702f7-167">`localStorage` и `sessionStorage` можно использовать в приложениях Blazor WebAssembly, но только путем написания пользовательского кода или использования стороннего пакета.</span><span class="sxs-lookup"><span data-stu-id="702f7-167">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="702f7-168">Как правило, `sessionStorage` более безопасно для использования.</span><span class="sxs-lookup"><span data-stu-id="702f7-168">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="702f7-169">`sessionStorage` позволяет избежать риска, когда пользователь открывает несколько вкладок и сталкивается со следующими проблемами.</span><span class="sxs-lookup"><span data-stu-id="702f7-169">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="702f7-170">Ошибки в хранилище состояний на разных вкладках.</span><span class="sxs-lookup"><span data-stu-id="702f7-170">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="702f7-171">Путаница в работе, когда одна вкладка перезаписывает состояние других.</span><span class="sxs-lookup"><span data-stu-id="702f7-171">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="702f7-172">`localStorage` является лучшим выбором, если приложение должно сохранять состояние в случае закрытия и повторного открытия браузера.</span><span class="sxs-lookup"><span data-stu-id="702f7-172">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="702f7-173">Пользователи могут просматривать и изменять данные, хранимые в `localStorage` и `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="702f7-173">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

## <a name="in-memory-state-container-service"></a><span data-ttu-id="702f7-174">Служба контейнеров состояния в памяти</span><span class="sxs-lookup"><span data-stu-id="702f7-174">In-memory state container service</span></span>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="702f7-175">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="702f7-175">Additional resources</span></span>

* [<span data-ttu-id="702f7-176">Сохранение состояния приложения перед операцией проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="702f7-176">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="702f7-177">Blazor Server — это платформа приложений с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="702f7-177">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="702f7-178">В большинстве случаев приложение поддерживает подключение к серверу.</span><span class="sxs-lookup"><span data-stu-id="702f7-178">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="702f7-179">Состояние пользователя хранится в памяти сервера в *канале*.</span><span class="sxs-lookup"><span data-stu-id="702f7-179">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="702f7-180">Ниже приведены примеры данных о состоянии пользователя, хранящиеся в канале.</span><span class="sxs-lookup"><span data-stu-id="702f7-180">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="702f7-181">Иерархия экземпляров компонента и их последних выходных данных отрисовки в преобразованном для просмотра пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="702f7-181">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="702f7-182">Значения полей и свойств в экземплярах компонента.</span><span class="sxs-lookup"><span data-stu-id="702f7-182">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="702f7-183">Данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал.</span><span class="sxs-lookup"><span data-stu-id="702f7-183">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="702f7-184">Данные о состоянии пользователя также можно найти в переменных JavaScript в памяти браузера, заданной с помощью вызовов [взаимодействия с JavaScript](xref:blazor/call-javascript-from-dotnet).</span><span class="sxs-lookup"><span data-stu-id="702f7-184">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="702f7-185">Если пользователь испытывает временный сбой сетевого подключения, Blazor пытается повторно подключить пользователя к исходному каналу с сохранением исходного состояния.</span><span class="sxs-lookup"><span data-stu-id="702f7-185">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="702f7-186">Однако повторное подключение пользователя к исходному каналу в памяти сервера не всегда возможно.</span><span class="sxs-lookup"><span data-stu-id="702f7-186">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="702f7-187">Сервер не может постоянно хранить отключенную цепь.</span><span class="sxs-lookup"><span data-stu-id="702f7-187">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="702f7-188">Сервер должен освободить отключенную цепь после истечения времени ожидания или при нехватке памяти на сервере.</span><span class="sxs-lookup"><span data-stu-id="702f7-188">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="702f7-189">В средах развертывания с несколькими серверами и балансировкой нагрузки отдельные серверы могут выйти из строя или быть автоматически удалены, если они больше не требуются для обработки общего объема запросов.</span><span class="sxs-lookup"><span data-stu-id="702f7-189">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="702f7-190">В этом случае исходные запросы на обработку на сервере могут стать недоступными для пользователя, когда он попытается подключиться повторно.</span><span class="sxs-lookup"><span data-stu-id="702f7-190">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="702f7-191">Пользователь может закрыть и снова открыть браузер или перезагрузить страницу, что приведет к удалению всех состояний, хранящихся в памяти браузера.</span><span class="sxs-lookup"><span data-stu-id="702f7-191">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="702f7-192">Например, теряются значения переменных JavaScript, заданные через вызовы взаимодействия с JavaScript.</span><span class="sxs-lookup"><span data-stu-id="702f7-192">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="702f7-193">Если пользователь не может повторно подключиться к исходному каналу, он получает новый канал с пустым состоянием.</span><span class="sxs-lookup"><span data-stu-id="702f7-193">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="702f7-194">Это эквивалентно закрытию и повторному открытию классического приложения.</span><span class="sxs-lookup"><span data-stu-id="702f7-194">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="702f7-195">Сохранение состояния при смене каналов</span><span class="sxs-lookup"><span data-stu-id="702f7-195">Persist state across circuits</span></span>

<span data-ttu-id="702f7-196">Как правило, состояния поддерживается при смене каналов, где пользователи активно могут создавать данные, а не просто считывать уже существующие данные.</span><span class="sxs-lookup"><span data-stu-id="702f7-196">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="702f7-197">Чтобы сохранять состояние при смене каналов, приложение должно хранить данные не в памяти сервера, а в другом месте.</span><span class="sxs-lookup"><span data-stu-id="702f7-197">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="702f7-198">Сохраняемость состояния не обеспечивается автоматически.</span><span class="sxs-lookup"><span data-stu-id="702f7-198">State persistence isn't automatic.</span></span> <span data-ttu-id="702f7-199">При разработке приложения необходимо выполнить определенные действия для реализации сохраняемости данных с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="702f7-199">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="702f7-200">Сохраняемость данных, как правило, требуется только для состояний высокой ценности, на создание которых пользователь затратил значительные усилия.</span><span class="sxs-lookup"><span data-stu-id="702f7-200">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="702f7-201">В следующих примерах сохранение состояния экономит время или средства в коммерческих действиях.</span><span class="sxs-lookup"><span data-stu-id="702f7-201">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="702f7-202">Веб-формы с многоэтапным заполнением. Пользователю потребуется много времени для повторного ввода данных на нескольких завершенных этапах заполнения таких форм, если данные об их состоянии будут утеряны.</span><span class="sxs-lookup"><span data-stu-id="702f7-202">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="702f7-203">В этом сценарии пользователь потеряет данные о своем состоянии, если он выйдет из формы и вернется в нее позже.</span><span class="sxs-lookup"><span data-stu-id="702f7-203">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="702f7-204">Корзины для покупок. Любой коммерчески важный компонент приложения, который представляет потенциальную прибыль.</span><span class="sxs-lookup"><span data-stu-id="702f7-204">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="702f7-205">Пользователь, который теряет свое состояние, и, следовательно, свою корзину с покупками, может приобрести меньше товаров или услуг, когда вернется на сайт позже.</span><span class="sxs-lookup"><span data-stu-id="702f7-205">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="702f7-206">Приложение может сохранять только *состояния приложения*.</span><span class="sxs-lookup"><span data-stu-id="702f7-206">An app can only persist *app state*.</span></span> <span data-ttu-id="702f7-207">Пользовательские интерфейсы не могут быть сохранены, например экземпляры компонентов и их деревья отрисовки.</span><span class="sxs-lookup"><span data-stu-id="702f7-207">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="702f7-208">Компоненты и деревья отрисовки обычно не являются сериализуемыми.</span><span class="sxs-lookup"><span data-stu-id="702f7-208">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="702f7-209">Чтобы сохранить состояние пользовательского интерфейса, например развернутые узлы элементов управления иерархического представления, в приложении должен быть пользовательский код для моделирования поведения этого интерфейса как сериализуемого состояния приложения.</span><span class="sxs-lookup"><span data-stu-id="702f7-209">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="702f7-210">Место сохранения состояния</span><span class="sxs-lookup"><span data-stu-id="702f7-210">Where to persist state</span></span>

<span data-ttu-id="702f7-211">Данные о состоянии могут храниться в общих расположениях:</span><span class="sxs-lookup"><span data-stu-id="702f7-211">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="702f7-212">Хранилище на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="702f7-212">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="702f7-213">URL-адрес</span><span class="sxs-lookup"><span data-stu-id="702f7-213">URL</span></span>](#url)
* [<span data-ttu-id="702f7-214">Хранилище браузера</span><span class="sxs-lookup"><span data-stu-id="702f7-214">Browser storage</span></span>](#browser-storage)
* [<span data-ttu-id="702f7-215">Служба контейнеров состояния в памяти</span><span class="sxs-lookup"><span data-stu-id="702f7-215">In-memory state container service</span></span>](#in-memory-state-container-service)

### <a name="server-side-storage"></a><span data-ttu-id="702f7-216">Хранилище на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="702f7-216">Server-side storage</span></span>

<span data-ttu-id="702f7-217">Для постоянного хранения данных для нескольких пользователей и устройств приложение может использовать хранилище на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="702f7-217">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="702f7-218">Доступны следующие варианты:</span><span class="sxs-lookup"><span data-stu-id="702f7-218">Options include:</span></span>

* <span data-ttu-id="702f7-219">Хранилище BLOB-объектов</span><span class="sxs-lookup"><span data-stu-id="702f7-219">Blob storage</span></span>
* <span data-ttu-id="702f7-220">Хранилище значений ключей</span><span class="sxs-lookup"><span data-stu-id="702f7-220">Key-value storage</span></span>
* <span data-ttu-id="702f7-221">Реляционная база данных</span><span class="sxs-lookup"><span data-stu-id="702f7-221">Relational database</span></span>
* <span data-ttu-id="702f7-222">Хранилище таблиц</span><span class="sxs-lookup"><span data-stu-id="702f7-222">Table storage</span></span>

<span data-ttu-id="702f7-223">После сохранения данных состояние пользователя сохраняется и становится доступным во всех новых каналах.</span><span class="sxs-lookup"><span data-stu-id="702f7-223">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="702f7-224">Дополнительные сведения о вариантах хранения данных в Azure см. здесь:</span><span class="sxs-lookup"><span data-stu-id="702f7-224">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="702f7-225">Базы данных Azure</span><span class="sxs-lookup"><span data-stu-id="702f7-225">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="702f7-226">Документация по хранилищу Azure</span><span class="sxs-lookup"><span data-stu-id="702f7-226">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="702f7-227">URL-адрес</span><span class="sxs-lookup"><span data-stu-id="702f7-227">URL</span></span>

<span data-ttu-id="702f7-228">Для временных данных, представляющих состояние навигации, моделируют данные как часть URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="702f7-228">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="702f7-229">Ниже приведены примеры данных о состоянии пользователя, которые моделируются в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="702f7-229">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="702f7-230">Идентификатор просматриваемой сущности.</span><span class="sxs-lookup"><span data-stu-id="702f7-230">The ID of a viewed entity.</span></span>
* <span data-ttu-id="702f7-231">Номер текущей страницы в постраничной сетке.</span><span class="sxs-lookup"><span data-stu-id="702f7-231">The current page number in a paged grid.</span></span>

<span data-ttu-id="702f7-232">Содержимое адресной строки браузера будет сохраняться в следующих случаях.</span><span class="sxs-lookup"><span data-stu-id="702f7-232">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="702f7-233">Если пользователь вручную обновляет страницу.</span><span class="sxs-lookup"><span data-stu-id="702f7-233">If the user manually reloads the page.</span></span>
* <span data-ttu-id="702f7-234">Если веб-сервер становится недоступным и пользователь вынужден перезагрузить страницу, чтобы подключиться к другому серверу.</span><span class="sxs-lookup"><span data-stu-id="702f7-234">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="702f7-235">Сведения об определении шаблонов URL-адресов с помощью директивы [`@page`](xref:mvc/views/razor#page) см. в статье <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="702f7-235">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="702f7-236">Хранилище браузера</span><span class="sxs-lookup"><span data-stu-id="702f7-236">Browser storage</span></span>

<span data-ttu-id="702f7-237">Для хранения временных данных, создаваемых пользователем, обычно используются коллекции браузера [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) и [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span><span class="sxs-lookup"><span data-stu-id="702f7-237">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="702f7-238">`localStorage` ограничивается окном браузера.</span><span class="sxs-lookup"><span data-stu-id="702f7-238">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="702f7-239">Если пользователь перезагружает страницу или закрывает и снова открывает браузер, состояние сохраняется.</span><span class="sxs-lookup"><span data-stu-id="702f7-239">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="702f7-240">Если пользователь открывает несколько вкладок браузера, это состояние совместно используется на нескольких вкладках.</span><span class="sxs-lookup"><span data-stu-id="702f7-240">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="702f7-241">Данные сохраняются в `localStorage` до тех пор, пока они не будут явно очищены.</span><span class="sxs-lookup"><span data-stu-id="702f7-241">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="702f7-242">`sessionStorage` ограничивается вкладкой браузера. Если пользователь перезагружает вкладку, состояние сохраняется.</span><span class="sxs-lookup"><span data-stu-id="702f7-242">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="702f7-243">Если пользователь закрывает вкладку или браузер, состояние теряется.</span><span class="sxs-lookup"><span data-stu-id="702f7-243">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="702f7-244">Если пользователь открывает несколько вкладок браузера, каждая вкладка имеет собственную независимую версию данных.</span><span class="sxs-lookup"><span data-stu-id="702f7-244">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="702f7-245">Как правило, `sessionStorage` более безопасно для использования.</span><span class="sxs-lookup"><span data-stu-id="702f7-245">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="702f7-246">`sessionStorage` позволяет избежать риска, когда пользователь открывает несколько вкладок и сталкивается со следующими проблемами.</span><span class="sxs-lookup"><span data-stu-id="702f7-246">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="702f7-247">Ошибки в хранилище состояний на разных вкладках.</span><span class="sxs-lookup"><span data-stu-id="702f7-247">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="702f7-248">Путаница в работе, когда одна вкладка перезаписывает состояние других.</span><span class="sxs-lookup"><span data-stu-id="702f7-248">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="702f7-249">`localStorage` является лучшим выбором, если приложение должно сохранять состояние в случае закрытия и повторного открытия браузера.</span><span class="sxs-lookup"><span data-stu-id="702f7-249">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="702f7-250">Предостережения при использовании хранилища браузера.</span><span class="sxs-lookup"><span data-stu-id="702f7-250">Caveats for using browser storage:</span></span>

* <span data-ttu-id="702f7-251">Аналогично использованию базы данных на стороне сервера, загрузка и сохранение данных выполняются асинхронно.</span><span class="sxs-lookup"><span data-stu-id="702f7-251">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="702f7-252">В отличие от базы данных на стороне сервера, хранилище недоступно во время предварительной отрисовки, так как запрошенная страница не существует в браузере во время выполнения этой стадии.</span><span class="sxs-lookup"><span data-stu-id="702f7-252">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="702f7-253">Для приложений Blazor Server имеет смысл хранить данные в пределах нескольких килобайт.</span><span class="sxs-lookup"><span data-stu-id="702f7-253">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="702f7-254">При превышении этого порога необходимо учитывать последствия производительности, поскольку данные загружаются и сохраняются по сети.</span><span class="sxs-lookup"><span data-stu-id="702f7-254">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="702f7-255">Пользователи могут просматривать и изменять данные.</span><span class="sxs-lookup"><span data-stu-id="702f7-255">Users may view or tamper with the data.</span></span> <span data-ttu-id="702f7-256">[Защита данных ASP.NET Core](xref:security/data-protection/introduction) может снизить этот риск.</span><span class="sxs-lookup"><span data-stu-id="702f7-256">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="702f7-257">Например, [защищенное хранилище браузера ASP.NET Core](#aspnet-core-protected-browser-storage) использует защиту данных ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="702f7-257">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="702f7-258">Сторонние пакеты NuGet предоставляют интерфейсы API для работы с `localStorage` и `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="702f7-258">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="702f7-259">Рекомендуется выбрать пакет, который прозрачно использует [защиту данных ASP.NET Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="702f7-259">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="702f7-260">Функция защиты данных шифрует хранимые данные и уменьшает потенциальный риск несанкционированного изменения хранимых данных.</span><span class="sxs-lookup"><span data-stu-id="702f7-260">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="702f7-261">Если сериализованные данные JSON хранятся в виде обычного текста, пользователи могут просматривать данные с помощью средств разработчика браузера, а также изменять сохраненные данные.</span><span class="sxs-lookup"><span data-stu-id="702f7-261">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="702f7-262">Защита данных не всегда является проблемой, так как данные могут быть тривиальными по своей природе.</span><span class="sxs-lookup"><span data-stu-id="702f7-262">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="702f7-263">Например, чтение или изменение сохраненного цвета элемента пользовательского интерфейса не является серьезной угрозой безопасности для пользователя или организации.</span><span class="sxs-lookup"><span data-stu-id="702f7-263">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="702f7-264">Не разрешайте пользователям проверять или изменять *конфиденциальные данные*.</span><span class="sxs-lookup"><span data-stu-id="702f7-264">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="702f7-265">Защищенное хранилище браузера ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="702f7-265">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="702f7-266">Защищенное хранилище браузера ASP.NET Core использует [защиту данных ASP.NET Core](xref:security/data-protection/introduction) для [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) и [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span><span class="sxs-lookup"><span data-stu-id="702f7-266">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="702f7-267">Защищенное хранилище браузера использует защиту данных ASP.NET Core и поддерживается только для приложений Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="702f7-267">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="702f7-268">Конфигурация</span><span class="sxs-lookup"><span data-stu-id="702f7-268">Configuration</span></span>

1. <span data-ttu-id="702f7-269">Добавьте ссылку на пакет для [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions).</span><span class="sxs-lookup"><span data-stu-id="702f7-269">Add a package reference to [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions).</span></span>
1. <span data-ttu-id="702f7-270">В `Startup.ConfigureServices` вызовите `AddProtectedBrowserStorage`, чтобы добавить службы `localStorage` и `sessionStorage` в коллекцию служб.</span><span class="sxs-lookup"><span data-stu-id="702f7-270">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="702f7-271">Сохранение и загрузка данных в компоненте</span><span class="sxs-lookup"><span data-stu-id="702f7-271">Save and load data within a component</span></span>

<span data-ttu-id="702f7-272">В любом компоненте, требующем загрузки или сохранения данных в хранилище браузера, используйте директиву [`@inject`](xref:mvc/views/razor#inject) для вставки экземпляра одного из следующих компонентов.</span><span class="sxs-lookup"><span data-stu-id="702f7-272">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="702f7-273">Выбор зависит от расположения хранилища браузера, которое требуется использовать.</span><span class="sxs-lookup"><span data-stu-id="702f7-273">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="702f7-274">В следующем примере используется `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="702f7-274">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="702f7-275">Директиву `@using` можно поместить в файл приложения `_Imports.razor`, а не в компонент.</span><span class="sxs-lookup"><span data-stu-id="702f7-275">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="702f7-276">Использование файла `_Imports.razor` делает пространство имен доступным для больших сегментов приложения или всего приложения.</span><span class="sxs-lookup"><span data-stu-id="702f7-276">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="702f7-277">Чтобы сохранить значение `currentCount` в компоненте `Counter` приложения на основе шаблона проекта Blazor Server, измените метод `IncrementCount`, чтобы использовать в нем `ProtectedSessionStore.SetAsync`.</span><span class="sxs-lookup"><span data-stu-id="702f7-277">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="702f7-278">В больших и более реалистичных приложениях хранение отдельных полей является маловероятной ситуацией.</span><span class="sxs-lookup"><span data-stu-id="702f7-278">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="702f7-279">Приложения, скорее всего, будут хранить все объекты модели, включающие сложное состояние.</span><span class="sxs-lookup"><span data-stu-id="702f7-279">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="702f7-280">`ProtectedSessionStore` автоматически сериализует и десериализует данные JSON для хранения объектов со сложными состояниями.</span><span class="sxs-lookup"><span data-stu-id="702f7-280">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="702f7-281">В предыдущем примере кода данные `currentCount` хранятся в виде `sessionStorage['count']` в браузере пользователя.</span><span class="sxs-lookup"><span data-stu-id="702f7-281">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="702f7-282">Данные не хранятся в виде обычного текста, а защищаются с помощью функции защиты данных ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="702f7-282">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="702f7-283">Зашифрованные данные можно проверить, если `sessionStorage['count']` вычисляется в консоли разработчика браузера.</span><span class="sxs-lookup"><span data-stu-id="702f7-283">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="702f7-284">Чтобы данные `currentCount` восстанавливались, когда пользователь снова возвращается в компонент `Counter` (в том числе в новом канале), используйте `ProtectedSessionStore.GetAsync`.</span><span class="sxs-lookup"><span data-stu-id="702f7-284">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="702f7-285">Если параметры компонента включают состояние навигации, вызовите `ProtectedSessionStore.GetAsync` и назначьте отличный от `null` результат в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, а не в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="702f7-285">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="702f7-286"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> вызывается только один раз при первом создании экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="702f7-286"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="702f7-287"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> не вызывается позже, если пользователь переходит на другой URL-адрес, оставаясь на той же странице.</span><span class="sxs-lookup"><span data-stu-id="702f7-287"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="702f7-288">Для получения дополнительной информации см. <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="702f7-288">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="702f7-289">Примеры в этом разделе работают только в том случае, если на сервере не включена предварительная отрисовка.</span><span class="sxs-lookup"><span data-stu-id="702f7-289">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="702f7-290">При включенной предварительной отрисовке выводится сообщение об ошибке, объясняющее, что вызовы взаимодействия с JavaScript осуществить невозможно, поскольку выполняется предварительная отрисовка компонента.</span><span class="sxs-lookup"><span data-stu-id="702f7-290">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="702f7-291">Отключите предварительную отрисовку или добавьте дополнительный код для работы с предварительной отрисовкой.</span><span class="sxs-lookup"><span data-stu-id="702f7-291">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="702f7-292">Дополнительные сведения о написании кода, который работает с предварительной отрисовкой, см. в разделе [Обработка предварительной отрисовки](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="702f7-292">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="702f7-293">Обработка состояния загрузки</span><span class="sxs-lookup"><span data-stu-id="702f7-293">Handle the loading state</span></span>

<span data-ttu-id="702f7-294">Так как доступ к хранилищу браузера осуществляется асинхронно через сетевое подключение, всегда есть период времени, прежде чем данные будут загружены и станут доступны для компонента.</span><span class="sxs-lookup"><span data-stu-id="702f7-294">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="702f7-295">Для достижения лучших результатов перед отображением пустых данных или данных по умолчанию выводится сообщение о состоянии загрузки.</span><span class="sxs-lookup"><span data-stu-id="702f7-295">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="702f7-296">Один из подходов состоит в том, чтобы определить, равно ли значение данных `null`. Если это так, данные еще загружаются.</span><span class="sxs-lookup"><span data-stu-id="702f7-296">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="702f7-297">В компоненте `Counter` по умолчанию количество хранится в `int`.</span><span class="sxs-lookup"><span data-stu-id="702f7-297">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="702f7-298">[Сделайте `currentCount` допускающим значение NULL](/dotnet/csharp/language-reference/builtin-types/nullable-value-types), добавив вопросительный знак (`?`) к типу (`int`).</span><span class="sxs-lookup"><span data-stu-id="702f7-298">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="702f7-299">Вместо безусловного отображения количества и кнопки **`Increment`** отображайте эти элементы только в том случае, если данные загружены, указав <xref:System.Nullable%601.HasValue%2A>.</span><span class="sxs-lookup"><span data-stu-id="702f7-299">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="702f7-300">Обработка предварительной отрисовки</span><span class="sxs-lookup"><span data-stu-id="702f7-300">Handle prerendering</span></span>

<span data-ttu-id="702f7-301">Во время предварительной отрисовки происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="702f7-301">During prerendering:</span></span>

* <span data-ttu-id="702f7-302">интерактивное подключение к браузеру пользователя не существует;</span><span class="sxs-lookup"><span data-stu-id="702f7-302">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="702f7-303">в браузере еще нет страницы, на которой можно запустить код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="702f7-303">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="702f7-304">`localStorage` или `sessionStorage` недоступны во время предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="702f7-304">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="702f7-305">Если компонент пытается взаимодействовать с хранилищем, выводится сообщение об ошибке, объясняющее, что вызовы взаимодействия с JavaScript осуществить невозможно, поскольку выполняется предварительная отрисовка компонента.</span><span class="sxs-lookup"><span data-stu-id="702f7-305">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="702f7-306">Одним из способов устранения этой ошибки является отключение предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="702f7-306">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="702f7-307">Обычно это наилучший вариант, если приложение активно использует хранилище в браузере.</span><span class="sxs-lookup"><span data-stu-id="702f7-307">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="702f7-308">Предварительная отрисовка увеличивает сложность и не дает приложению никаких преимуществ, так как приложение не может выдать какое-либо полезное содержимое, пока не станут доступны `localStorage` или `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="702f7-308">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="702f7-309">Чтобы отключить предварительную отрисовку, откройте файл `Pages/_Host.cshtml` и измените атрибут `render-mode` [вспомогательной функции тега компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) на <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span><span class="sxs-lookup"><span data-stu-id="702f7-309">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="702f7-310">Предварительная отрисовка может быть полезной для других страниц, которые не используют `localStorage` или `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="702f7-310">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="702f7-311">Чтобы сохранить предварительную отрисовку, отложите операцию загрузки до тех пор, пока браузер не подключится к каналу.</span><span class="sxs-lookup"><span data-stu-id="702f7-311">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="702f7-312">Ниже приведен пример хранения значения счетчика.</span><span class="sxs-lookup"><span data-stu-id="702f7-312">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="702f7-313">Перенос сохранения состояния в общее расположение</span><span class="sxs-lookup"><span data-stu-id="702f7-313">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="702f7-314">Если многие компоненты используют хранилище на основе браузера, повторная реализация кода поставщика состояний много раз создает дублирование кода.</span><span class="sxs-lookup"><span data-stu-id="702f7-314">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="702f7-315">Одним из вариантов предотвращения дублирования кода является создание *родительского компонента поставщика состояний*, который инкапсулирует логику поставщика состояний.</span><span class="sxs-lookup"><span data-stu-id="702f7-315">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="702f7-316">Дочерние компоненты могут работать с сохраненными данными без учета механизма сохранения состояния.</span><span class="sxs-lookup"><span data-stu-id="702f7-316">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="702f7-317">В следующем примере компонента `CounterStateProvider` данные счетчика сохраняются в `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="702f7-317">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="702f7-318">Компонент `CounterStateProvider` обрабатывает этап загрузки, не выполняя отрисовку его дочернего содержимого до завершения загрузки.</span><span class="sxs-lookup"><span data-stu-id="702f7-318">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="702f7-319">Чтобы использовать компонент `CounterStateProvider`, оберните экземпляр компонента вокруг любого другого компонента, которому требуется доступ к состоянию счетчика.</span><span class="sxs-lookup"><span data-stu-id="702f7-319">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="702f7-320">Чтобы сделать состояние доступным для всех компонентов в приложении, оберните `CounterStateProvider` компонент вокруг <xref:Microsoft.AspNetCore.Components.Routing.Router> в компоненте `App` (`App.razor`).</span><span class="sxs-lookup"><span data-stu-id="702f7-320">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="702f7-321">Упакованные компоненты получают и могут изменять состояние сохраненного счетчика.</span><span class="sxs-lookup"><span data-stu-id="702f7-321">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="702f7-322">Следующий компонент `Counter` реализует этот шаблон.</span><span class="sxs-lookup"><span data-stu-id="702f7-322">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="702f7-323">Предыдущий компонент не требуется для взаимодействия с `ProtectedBrowserStorage` и не имеет отношения к этапу "загрузки".</span><span class="sxs-lookup"><span data-stu-id="702f7-323">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="702f7-324">Для работы с предварительной отрисовкой, как описано выше, `CounterStateProvider` можно сделать так, чтобы все компоненты, использующие данные счетчика, автоматически работали с предварительной отрисовкой.</span><span class="sxs-lookup"><span data-stu-id="702f7-324">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="702f7-325">Дополнительные сведения см. в разделе [Обработка предварительной отрисовки](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="702f7-325">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="702f7-326">В целом шаблон *родительского компонента поставщика состояний* рекомендуется использовать в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="702f7-326">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="702f7-327">Для использования состояния во множестве компонентов.</span><span class="sxs-lookup"><span data-stu-id="702f7-327">To consume state across many components.</span></span>
* <span data-ttu-id="702f7-328">Если имеется только один объект состояния верхнего уровня для сохранения.</span><span class="sxs-lookup"><span data-stu-id="702f7-328">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="702f7-329">Чтобы сохранить множество различных объектов состояния и использовать разные подмножества объектов в разных местах, лучше избегать глобального сохранения состояния.</span><span class="sxs-lookup"><span data-stu-id="702f7-329">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="702f7-330">Экспериментальный пакет NuGet для защищенного хранилища браузера</span><span class="sxs-lookup"><span data-stu-id="702f7-330">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="702f7-331">Защищенное хранилище браузера ASP.NET Core использует [защиту данных ASP.NET Core](xref:security/data-protection/introduction) для [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) и [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span><span class="sxs-lookup"><span data-stu-id="702f7-331">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="702f7-332">`Microsoft.AspNetCore.ProtectedBrowserStorage` является неподдерживаемым экспериментальным пакетом, который не подходит для использования в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="702f7-332">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="702f7-333">Пакет можно использовать только в приложениях Blazor Server для ASP.NET Core 3.1.</span><span class="sxs-lookup"><span data-stu-id="702f7-333">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="702f7-334">Конфигурация</span><span class="sxs-lookup"><span data-stu-id="702f7-334">Configuration</span></span>

1. <span data-ttu-id="702f7-335">Добавьте ссылку на пакет для [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="702f7-335">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="702f7-336">Добавьте следующий скрипт в файл `Pages/_Host.cshtml` до закрывающего тега `</body>`.</span><span class="sxs-lookup"><span data-stu-id="702f7-336">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="702f7-337">В `Startup.ConfigureServices` вызовите `AddProtectedBrowserStorage`, чтобы добавить службы `localStorage` и `sessionStorage` в коллекцию служб.</span><span class="sxs-lookup"><span data-stu-id="702f7-337">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="702f7-338">Сохранение и загрузка данных в компоненте</span><span class="sxs-lookup"><span data-stu-id="702f7-338">Save and load data within a component</span></span>

<span data-ttu-id="702f7-339">В любом компоненте, требующем загрузки или сохранения данных в хранилище браузера, используйте директиву [`@inject`](xref:mvc/views/razor#inject) для вставки экземпляра одного из следующих компонентов.</span><span class="sxs-lookup"><span data-stu-id="702f7-339">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="702f7-340">Выбор зависит от расположения хранилища браузера, которое требуется использовать.</span><span class="sxs-lookup"><span data-stu-id="702f7-340">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="702f7-341">В следующем примере используется `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="702f7-341">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="702f7-342">Инструкцию `@using` можно поместить в файл `_Imports.razor`, а не в компонент.</span><span class="sxs-lookup"><span data-stu-id="702f7-342">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="702f7-343">Использование файла `_Imports.razor` делает пространство имен доступным для больших сегментов приложения или всего приложения.</span><span class="sxs-lookup"><span data-stu-id="702f7-343">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="702f7-344">Чтобы сохранить значение `currentCount` в компоненте `Counter` приложения на основе шаблона проекта Blazor Server, измените метод `IncrementCount`, чтобы использовать в нем `ProtectedSessionStore.SetAsync`.</span><span class="sxs-lookup"><span data-stu-id="702f7-344">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="702f7-345">В больших и более реалистичных приложениях хранение отдельных полей является маловероятной ситуацией.</span><span class="sxs-lookup"><span data-stu-id="702f7-345">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="702f7-346">Приложения, скорее всего, будут хранить все объекты модели, включающие сложное состояние.</span><span class="sxs-lookup"><span data-stu-id="702f7-346">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="702f7-347">`ProtectedSessionStore` автоматически сериализует и десериализует данные JSON.</span><span class="sxs-lookup"><span data-stu-id="702f7-347">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="702f7-348">В предыдущем примере кода данные `currentCount` хранятся в виде `sessionStorage['count']` в браузере пользователя.</span><span class="sxs-lookup"><span data-stu-id="702f7-348">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="702f7-349">Данные не хранятся в виде обычного текста, а защищаются с помощью функции защиты данных ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="702f7-349">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="702f7-350">Зашифрованные данные можно проверить, если `sessionStorage['count']` вычисляется в консоли разработчика браузера.</span><span class="sxs-lookup"><span data-stu-id="702f7-350">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="702f7-351">Чтобы восстановить данные `currentCount`, когда пользователь снова возвращается в компонент `Counter`, в том числе в совершенно новом канале, используйте `ProtectedSessionStore.GetAsync`.</span><span class="sxs-lookup"><span data-stu-id="702f7-351">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="702f7-352">Если параметры компонента включают состояние навигации, вызовите `ProtectedSessionStore.GetAsync` и назначьте результат в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, а не в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="702f7-352">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="702f7-353"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> вызывается только один раз при первом создании экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="702f7-353"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="702f7-354"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> не вызывается позже, если пользователь переходит на другой URL-адрес, оставаясь на той же странице.</span><span class="sxs-lookup"><span data-stu-id="702f7-354"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="702f7-355">Для получения дополнительной информации см. <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="702f7-355">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="702f7-356">Примеры в этом разделе работают только в том случае, если на сервере не включена предварительная отрисовка.</span><span class="sxs-lookup"><span data-stu-id="702f7-356">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="702f7-357">При включенной предварительной отрисовке выводится сообщение об ошибке, объясняющее, что вызовы взаимодействия с JavaScript осуществить невозможно, поскольку выполняется предварительная отрисовка компонента.</span><span class="sxs-lookup"><span data-stu-id="702f7-357">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="702f7-358">Отключите предварительную отрисовку или добавьте дополнительный код для работы с предварительной отрисовкой.</span><span class="sxs-lookup"><span data-stu-id="702f7-358">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="702f7-359">Дополнительные сведения о написании кода, который работает с предварительной отрисовкой, см. в разделе [Обработка предварительной отрисовки](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="702f7-359">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="702f7-360">Обработка состояния загрузки</span><span class="sxs-lookup"><span data-stu-id="702f7-360">Handle the loading state</span></span>

<span data-ttu-id="702f7-361">Так как доступ к хранилищу браузера осуществляется асинхронно через сетевое подключение, всегда есть период времени, прежде чем данные будут загружены и станут доступны для компонента.</span><span class="sxs-lookup"><span data-stu-id="702f7-361">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="702f7-362">Для достижения лучших результатов перед отображением пустых данных или данных по умолчанию выводится сообщение о состоянии загрузки.</span><span class="sxs-lookup"><span data-stu-id="702f7-362">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="702f7-363">Один из подходов состоит в том, чтобы определить, равно ли значение данных `null`. Если это так, данные еще загружаются.</span><span class="sxs-lookup"><span data-stu-id="702f7-363">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="702f7-364">В компоненте `Counter` по умолчанию количество хранится в `int`.</span><span class="sxs-lookup"><span data-stu-id="702f7-364">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="702f7-365">[Сделайте `currentCount` допускающим значение NULL](/dotnet/csharp/language-reference/builtin-types/nullable-value-types), добавив вопросительный знак (`?`) к типу (`int`).</span><span class="sxs-lookup"><span data-stu-id="702f7-365">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="702f7-366">Вместо безусловного отображения количества и кнопки **`Increment`** настройте отображение этих элементов только в том случае, если данные загружены.</span><span class="sxs-lookup"><span data-stu-id="702f7-366">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="702f7-367">Обработка предварительной отрисовки</span><span class="sxs-lookup"><span data-stu-id="702f7-367">Handle prerendering</span></span>

<span data-ttu-id="702f7-368">Во время предварительной отрисовки происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="702f7-368">During prerendering:</span></span>

* <span data-ttu-id="702f7-369">интерактивное подключение к браузеру пользователя не существует;</span><span class="sxs-lookup"><span data-stu-id="702f7-369">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="702f7-370">в браузере еще нет страницы, на которой можно запустить код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="702f7-370">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="702f7-371">`localStorage` или `sessionStorage` недоступны во время предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="702f7-371">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="702f7-372">Если компонент пытается взаимодействовать с хранилищем, выводится сообщение об ошибке, объясняющее, что вызовы взаимодействия с JavaScript осуществить невозможно, поскольку выполняется предварительная отрисовка компонента.</span><span class="sxs-lookup"><span data-stu-id="702f7-372">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="702f7-373">Одним из способов устранения этой ошибки является отключение предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="702f7-373">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="702f7-374">Обычно это наилучший вариант, если приложение активно использует хранилище в браузере.</span><span class="sxs-lookup"><span data-stu-id="702f7-374">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="702f7-375">Предварительная отрисовка увеличивает сложность и не дает приложению никаких преимуществ, так как приложение не может выдать какое-либо полезное содержимое, пока не станут доступны `localStorage` или `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="702f7-375">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="702f7-376">Чтобы отключить предварительную отрисовку, откройте файл `Pages/_Host.cshtml` и измените атрибут `render-mode` [вспомогательной функции тега компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) на <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span><span class="sxs-lookup"><span data-stu-id="702f7-376">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="702f7-377">Предварительная отрисовка может быть полезной для других страниц, которые не используют `localStorage` или `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="702f7-377">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="702f7-378">Чтобы сохранить предварительную отрисовку, отложите операцию загрузки до тех пор, пока браузер не подключится к каналу.</span><span class="sxs-lookup"><span data-stu-id="702f7-378">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="702f7-379">Ниже приведен пример хранения значения счетчика.</span><span class="sxs-lookup"><span data-stu-id="702f7-379">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="702f7-380">Перенос сохранения состояния в общее расположение</span><span class="sxs-lookup"><span data-stu-id="702f7-380">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="702f7-381">Если многие компоненты используют хранилище на основе браузера, повторная реализация кода поставщика состояний много раз создает дублирование кода.</span><span class="sxs-lookup"><span data-stu-id="702f7-381">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="702f7-382">Одним из вариантов предотвращения дублирования кода является создание *родительского компонента поставщика состояний*, который инкапсулирует логику поставщика состояний.</span><span class="sxs-lookup"><span data-stu-id="702f7-382">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="702f7-383">Дочерние компоненты могут работать с сохраненными данными без учета механизма сохранения состояния.</span><span class="sxs-lookup"><span data-stu-id="702f7-383">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="702f7-384">В следующем примере компонента `CounterStateProvider` данные счетчика сохраняются в `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="702f7-384">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="702f7-385">Компонент `CounterStateProvider` обрабатывает этап загрузки, не выполняя отрисовку его дочернего содержимого до завершения загрузки.</span><span class="sxs-lookup"><span data-stu-id="702f7-385">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="702f7-386">Чтобы использовать компонент `CounterStateProvider`, оберните экземпляр компонента вокруг любого другого компонента, которому требуется доступ к состоянию счетчика.</span><span class="sxs-lookup"><span data-stu-id="702f7-386">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="702f7-387">Чтобы сделать состояние доступным для всех компонентов в приложении, оберните `CounterStateProvider` компонент вокруг <xref:Microsoft.AspNetCore.Components.Routing.Router> в компоненте `App` (`App.razor`).</span><span class="sxs-lookup"><span data-stu-id="702f7-387">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="702f7-388">Упакованные компоненты получают и могут изменять состояние сохраненного счетчика.</span><span class="sxs-lookup"><span data-stu-id="702f7-388">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="702f7-389">Следующий компонент `Counter` реализует этот шаблон.</span><span class="sxs-lookup"><span data-stu-id="702f7-389">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="702f7-390">Предыдущий компонент не требуется для взаимодействия с `ProtectedBrowserStorage` и не имеет отношения к этапу "загрузки".</span><span class="sxs-lookup"><span data-stu-id="702f7-390">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="702f7-391">Для работы с предварительной отрисовкой, как описано выше, `CounterStateProvider` можно сделать так, чтобы все компоненты, использующие данные счетчика, автоматически работали с предварительной отрисовкой.</span><span class="sxs-lookup"><span data-stu-id="702f7-391">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="702f7-392">Дополнительные сведения см. в разделе [Обработка предварительной отрисовки](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="702f7-392">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="702f7-393">В целом рекомендуется использовать шаблон *родительского компонента поставщика состояний* в следующих случаях.</span><span class="sxs-lookup"><span data-stu-id="702f7-393">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="702f7-394">Для использования состояния во множестве компонентов.</span><span class="sxs-lookup"><span data-stu-id="702f7-394">To consume state across many components.</span></span>
* <span data-ttu-id="702f7-395">Если имеется только один объект состояния верхнего уровня для сохранения.</span><span class="sxs-lookup"><span data-stu-id="702f7-395">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="702f7-396">Чтобы сохранить множество различных объектов состояния и использовать разные подмножества объектов в разных местах, лучше избегать глобального сохранения состояния.</span><span class="sxs-lookup"><span data-stu-id="702f7-396">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

## <a name="in-memory-state-container-service"></a><span data-ttu-id="702f7-397">Служба контейнеров состояния в памяти</span><span class="sxs-lookup"><span data-stu-id="702f7-397">In-memory state container service</span></span>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

::: zone-end
