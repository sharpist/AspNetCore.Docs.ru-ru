---
title: Управление состоянием ASP.NET Core [Blazor
author: guardrex
description: Сведения о том, как сохранить состояние в приложениях [Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: blazor/state-management
ms.openlocfilehash: a6c646425145855538f408ec6cafdb151cd24b86
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401952"
---
# <a name="aspnet-core-blazor-state-management"></a><span data-ttu-id="0646f-103">Управление состоянием ASP.NET Core [Blazor</span><span class="sxs-lookup"><span data-stu-id="0646f-103">ASP.NET Core [Blazor state management</span></span>

<span data-ttu-id="0646f-104">Автор: [Стив Сандерсон](https://github.com/SteveSandersonMS) (Steve Sanderson)</span><span class="sxs-lookup"><span data-stu-id="0646f-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="0646f-105">[Blazor Server — это платформа приложений с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="0646f-105">[Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="0646f-106">В большинстве случаев приложение поддерживает постоянное подключение к серверу.</span><span class="sxs-lookup"><span data-stu-id="0646f-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="0646f-107">Состояние пользователя хранится в памяти сервера в *канале*.</span><span class="sxs-lookup"><span data-stu-id="0646f-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="0646f-108">Ниже приведены примеры состояния, удерживаемого для канала пользователя.</span><span class="sxs-lookup"><span data-stu-id="0646f-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="0646f-109">Отображаемый пользовательский интерфейс. Иерархия экземпляров компонента и их последних выходных данных отрисовки.</span><span class="sxs-lookup"><span data-stu-id="0646f-109">The rendered UI: The hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="0646f-110">Значения всех полей и свойств в экземплярах компонента.</span><span class="sxs-lookup"><span data-stu-id="0646f-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="0646f-111">Данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал.</span><span class="sxs-lookup"><span data-stu-id="0646f-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="0646f-112">В этой статье рассматривается сохранение состояния в приложениях [Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="0646f-112">This article addresses state persistence in [Blazor Server apps.</span></span> <span data-ttu-id="0646f-113">В приложениях [Blazor WebAssembly можно использовать [сохранение состояния на стороне клиента в браузере](#client-side-in-the-browser), но для этого требуются пользовательские решения или сторонние пакеты, что выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="0646f-113">[Blazor WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="blazor-circuits"></a><span data-ttu-id="0646f-114">Каналы [Blazor</span><span class="sxs-lookup"><span data-stu-id="0646f-114">[Blazor circuits</span></span>

<span data-ttu-id="0646f-115">Если пользователь испытывает временный сбой сетевого подключения, [Blazor пытается повторно подключить пользователя к исходному каналу, чтобы он мог продолжить использовать приложение.</span><span class="sxs-lookup"><span data-stu-id="0646f-115">If a user experiences a temporary network connection loss, [Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="0646f-116">Однако повторное подключение пользователя к исходному каналу в памяти сервера не всегда возможно.</span><span class="sxs-lookup"><span data-stu-id="0646f-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="0646f-117">Сервер не может постоянно хранить отключенную цепь.</span><span class="sxs-lookup"><span data-stu-id="0646f-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="0646f-118">Сервер должен освободить отключенную цепь после истечения времени ожидания или при нехватке памяти на сервере.</span><span class="sxs-lookup"><span data-stu-id="0646f-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="0646f-119">В многосерверных средах развертывания с балансировкой нагрузки любые запросы на обработку сервера могут стать недоступными в любой конкретный момент времени.</span><span class="sxs-lookup"><span data-stu-id="0646f-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="0646f-120">Отдельные серверы могут выйти из строя или быть автоматически удалены, если они больше не требуются для обработки общего объема запросов.</span><span class="sxs-lookup"><span data-stu-id="0646f-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="0646f-121">Когда пользователь попытается подключиться повторно, исходный сервер может стать недоступным.</span><span class="sxs-lookup"><span data-stu-id="0646f-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="0646f-122">Пользователь может закрыть и снова открыть браузер или перезагрузить страницу, что приведет к удалению всех состояний, хранящихся в памяти браузера.</span><span class="sxs-lookup"><span data-stu-id="0646f-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="0646f-123">Например, теряются значения, заданные через вызовы взаимодействия JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0646f-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="0646f-124">Если пользователь не может повторно подключиться к исходному каналу, он получает новый канал с пустым состоянием.</span><span class="sxs-lookup"><span data-stu-id="0646f-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="0646f-125">Это эквивалентно закрытию и повторному открытию классического приложения.</span><span class="sxs-lookup"><span data-stu-id="0646f-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="0646f-126">Сохранение состояния при смене каналов</span><span class="sxs-lookup"><span data-stu-id="0646f-126">Preserve state across circuits</span></span>

<span data-ttu-id="0646f-127">В некоторых сценариях желательно сохранять состояние при переходе от одного канала к другому.</span><span class="sxs-lookup"><span data-stu-id="0646f-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="0646f-128">Приложение может хранить важные данные для пользователя в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="0646f-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="0646f-129">веб-сервер становится недоступным;</span><span class="sxs-lookup"><span data-stu-id="0646f-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="0646f-130">браузер пользователя вынужден начать новый канал с новым веб-сервером.</span><span class="sxs-lookup"><span data-stu-id="0646f-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="0646f-131">Как правило, поддержание состояния между каналами применяется к сценариям, где пользователи активно могут создавать данные, а не просто считывать уже существующие данные.</span><span class="sxs-lookup"><span data-stu-id="0646f-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="0646f-132">Чтобы сохранить состояние за пределами одного канала, *недостаточно просто сохранить данные в памяти сервера*.</span><span class="sxs-lookup"><span data-stu-id="0646f-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="0646f-133">Приложение должно сохранять данные в другое место хранения.</span><span class="sxs-lookup"><span data-stu-id="0646f-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="0646f-134">Сохраняемость состояния не обеспечивается автоматически.</span><span class="sxs-lookup"><span data-stu-id="0646f-134">State persistence isn't automatic.</span></span> <span data-ttu-id="0646f-135">При разработке приложения необходимо выполнить определенные действия для реализации сохраняемости данных с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="0646f-135">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="0646f-136">Сохраняемость данных, как правило, требуется только для состояния высокой ценности, на создание которого пользователь затратил значительные усилия.</span><span class="sxs-lookup"><span data-stu-id="0646f-136">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="0646f-137">В следующих примерах сохранение состояния экономит время или средства в коммерческих действиях.</span><span class="sxs-lookup"><span data-stu-id="0646f-137">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="0646f-138">Многошаговая веб-форма. Пользователю потребуется много времени для повторного ввода данных для нескольких завершенных шагов многоэтапного процесса, если их данные о состоянии будут утеряны.</span><span class="sxs-lookup"><span data-stu-id="0646f-138">Multistep webform: It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="0646f-139">В этом сценарии пользователь потеряет состояние, если он выйдет из многошаговой формы и снова вернется в нее.</span><span class="sxs-lookup"><span data-stu-id="0646f-139">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="0646f-140">Корзина для покупок. Любой коммерчески важный компонент приложения, который представляет потенциальную прибыль.</span><span class="sxs-lookup"><span data-stu-id="0646f-140">Shopping cart: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="0646f-141">Пользователь, который теряет свое состояние, и, следовательно, свою корзину с покупками, может приобрести меньше товаров или услуг, когда вернется на сайт позже.</span><span class="sxs-lookup"><span data-stu-id="0646f-141">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="0646f-142">Обычно нет необходимости сохранять легко воссозданное состояние, например имя пользователя, введенное в диалоговое окно входа, которое еще не было отправлено.</span><span class="sxs-lookup"><span data-stu-id="0646f-142">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0646f-143">Приложение может сохранять только *состояния приложения*.</span><span class="sxs-lookup"><span data-stu-id="0646f-143">An app can only persist *app state*.</span></span> <span data-ttu-id="0646f-144">Пользовательские интерфейсы не могут быть сохранены, например экземпляры компонентов и их деревья отрисовки.</span><span class="sxs-lookup"><span data-stu-id="0646f-144">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="0646f-145">Компоненты и деревья отрисовки обычно не являются сериализуемыми.</span><span class="sxs-lookup"><span data-stu-id="0646f-145">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="0646f-146">Чтобы сохранить нечто похожее на состояние пользовательского интерфейса, например развернутые узлы элементов TreeView, приложение должно иметь пользовательский код для моделирования поведения как сериализуемого состояния приложения.</span><span class="sxs-lookup"><span data-stu-id="0646f-146">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="0646f-147">Место сохранения состояния</span><span class="sxs-lookup"><span data-stu-id="0646f-147">Where to persist state</span></span>

<span data-ttu-id="0646f-148">Существует три общих расположения для сохранения состояния в приложении [Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="0646f-148">Three common locations exist for persisting state in a [Blazor Server app.</span></span> <span data-ttu-id="0646f-149">Каждый подход лучше всего подходит для определенных сценариев и имеет свои подводные камни:</span><span class="sxs-lookup"><span data-stu-id="0646f-149">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="0646f-150">На стороне сервера в базе данных</span><span class="sxs-lookup"><span data-stu-id="0646f-150">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="0646f-151">URL</span><span class="sxs-lookup"><span data-stu-id="0646f-151">URL</span></span>](#url)
* [<span data-ttu-id="0646f-152">На стороне клиента в браузере</span><span class="sxs-lookup"><span data-stu-id="0646f-152">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="0646f-153">На стороне сервера в базе данных</span><span class="sxs-lookup"><span data-stu-id="0646f-153">Server-side in a database</span></span>

<span data-ttu-id="0646f-154">Для постоянного сохранения данных или для данных, которые должны охватывать несколько пользователей или устройств, независимая база данных на стороне сервера почти наверняка является лучшим выбором.</span><span class="sxs-lookup"><span data-stu-id="0646f-154">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="0646f-155">Возможны следующие значения.</span><span class="sxs-lookup"><span data-stu-id="0646f-155">Options include:</span></span>

* <span data-ttu-id="0646f-156">Реляционная база данных SQL</span><span class="sxs-lookup"><span data-stu-id="0646f-156">Relational SQL database</span></span>
* <span data-ttu-id="0646f-157">Хранилище ключ-значение</span><span class="sxs-lookup"><span data-stu-id="0646f-157">Key-value store</span></span>
* <span data-ttu-id="0646f-158">Хранилище больших двоичных объектов</span><span class="sxs-lookup"><span data-stu-id="0646f-158">Blob store</span></span>
* <span data-ttu-id="0646f-159">Хранилище таблиц</span><span class="sxs-lookup"><span data-stu-id="0646f-159">Table store</span></span>

<span data-ttu-id="0646f-160">После сохранения данных в базе данных пользователь может запустить новый канал в любое время.</span><span class="sxs-lookup"><span data-stu-id="0646f-160">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="0646f-161">Данные пользователя сохранены и доступны в любом новом канале.</span><span class="sxs-lookup"><span data-stu-id="0646f-161">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="0646f-162">Дополнительные сведения о вариантах хранения данных Azure см. в [документации по службе хранилища Azure](/azure/storage/) и [документации по базам данных Azure](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="0646f-162">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="0646f-163">URL-адрес</span><span class="sxs-lookup"><span data-stu-id="0646f-163">URL</span></span>

<span data-ttu-id="0646f-164">Для временных данных, представляющих состояние навигации, моделируют данные как часть URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="0646f-164">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="0646f-165">Ниже приведены примеры состояний, которые моделируются в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="0646f-165">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="0646f-166">Идентификатор просматриваемой сущности.</span><span class="sxs-lookup"><span data-stu-id="0646f-166">The ID of a viewed entity.</span></span>
* <span data-ttu-id="0646f-167">Номер текущей страницы в постраничной сетке.</span><span class="sxs-lookup"><span data-stu-id="0646f-167">The current page number in a paged grid.</span></span>

<span data-ttu-id="0646f-168">Содержимое адресной строки браузера будет сохраняться в следующих случаях.</span><span class="sxs-lookup"><span data-stu-id="0646f-168">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="0646f-169">Если пользователь вручную обновляет страницу.</span><span class="sxs-lookup"><span data-stu-id="0646f-169">If the user manually reloads the page.</span></span>
* <span data-ttu-id="0646f-170">Если веб-сервер становится недоступным и пользователь вынужден перезагрузить страницу, чтобы подключиться к другому серверу.</span><span class="sxs-lookup"><span data-stu-id="0646f-170">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="0646f-171">Сведения об определении шаблонов URL-адресов с помощью директивы `@page` см. в разделе <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="0646f-171">For information on defining URL patterns with the `@page` directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="0646f-172">На стороне клиента в браузере</span><span class="sxs-lookup"><span data-stu-id="0646f-172">Client-side in the browser</span></span>

<span data-ttu-id="0646f-173">Для временных данных, создаваемых пользователем, общим резервным хранилищем являются коллекции браузера `localStorage` и `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="0646f-173">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="0646f-174">Приложению не требуется управлять сохраненным состоянием или очищать его, если канал прерван, что является преимуществом по сравнению с хранилищем на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="0646f-174">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="0646f-175">"На стороне клиента" в этом разделе относится к сценариям на стороне клиента в браузере, а не к [модели размещения [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="0646f-175">"Client-side" in this section refers to client-side scenarios in the browser, not the [[Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="0646f-176">`localStorage` и `sessionStorage` можно использовать в приложениях [Blazor WebAssembly, но только путем написания пользовательского кода или использования стороннего пакета.</span><span class="sxs-lookup"><span data-stu-id="0646f-176">`localStorage` and `sessionStorage` can be used in [Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="0646f-177">`localStorage` и `sessionStorage` различаются следующим образом.</span><span class="sxs-lookup"><span data-stu-id="0646f-177">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="0646f-178">`localStorage` входит в область браузера пользователя.</span><span class="sxs-lookup"><span data-stu-id="0646f-178">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="0646f-179">Если пользователь перезагружает страницу или закрывает и снова открывает браузер, состояние сохраняется.</span><span class="sxs-lookup"><span data-stu-id="0646f-179">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="0646f-180">Если пользователь открывает несколько вкладок браузера, это состояние совместно используется на нескольких вкладках.</span><span class="sxs-lookup"><span data-stu-id="0646f-180">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="0646f-181">Данные сохраняются в `localStorage` до тех пор, пока они не будут явно очищены.</span><span class="sxs-lookup"><span data-stu-id="0646f-181">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="0646f-182">`sessionStorage` входит в область вкладки браузера пользователя. Если пользователь перезагружает вкладку, состояние сохраняется.</span><span class="sxs-lookup"><span data-stu-id="0646f-182">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="0646f-183">Если пользователь закрывает вкладку или браузер, состояние теряется.</span><span class="sxs-lookup"><span data-stu-id="0646f-183">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="0646f-184">Если пользователь открывает несколько вкладок браузера, каждая вкладка имеет собственную независимую версию данных.</span><span class="sxs-lookup"><span data-stu-id="0646f-184">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="0646f-185">Как правило, `sessionStorage` более безопасно для использования.</span><span class="sxs-lookup"><span data-stu-id="0646f-185">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="0646f-186">`sessionStorage` позволяет избежать риска, когда пользователь открывает несколько вкладок и сталкивается со следующими проблемами.</span><span class="sxs-lookup"><span data-stu-id="0646f-186">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="0646f-187">Ошибки в хранилище состояний на разных вкладках.</span><span class="sxs-lookup"><span data-stu-id="0646f-187">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="0646f-188">Путаница в работе, когда одна вкладка перезаписывает состояние других.</span><span class="sxs-lookup"><span data-stu-id="0646f-188">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="0646f-189">`localStorage` является лучшим выбором, если приложение должно сохранять состояние в случае закрытия и повторного открытия браузера.</span><span class="sxs-lookup"><span data-stu-id="0646f-189">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="0646f-190">Предостережения при использовании хранилища браузера.</span><span class="sxs-lookup"><span data-stu-id="0646f-190">Caveats for using browser storage:</span></span>

* <span data-ttu-id="0646f-191">Аналогично использованию базы данных на стороне сервера, загрузка и сохранение данных выполняются асинхронно.</span><span class="sxs-lookup"><span data-stu-id="0646f-191">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="0646f-192">В отличие от базы данных на стороне сервера, хранилище недоступно во время предварительной отрисовки, так как запрошенная страница не существует в браузере во время выполнения этой стадии.</span><span class="sxs-lookup"><span data-stu-id="0646f-192">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="0646f-193">Для приложений [Blazor Server имеет смысл хранить данные в пределах нескольких килобайт.</span><span class="sxs-lookup"><span data-stu-id="0646f-193">Storage of a few kilobytes of data is reasonable to persist for [Blazor Server apps.</span></span> <span data-ttu-id="0646f-194">При превышении этого порога необходимо учитывать последствия производительности, поскольку данные загружаются и сохраняются по сети.</span><span class="sxs-lookup"><span data-stu-id="0646f-194">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="0646f-195">Пользователи могут просматривать и изменять данные.</span><span class="sxs-lookup"><span data-stu-id="0646f-195">Users may view or tamper with the data.</span></span> <span data-ttu-id="0646f-196">[Защита данных](xref:security/data-protection/introduction) ASP.NET Core может снизить этот риск.</span><span class="sxs-lookup"><span data-stu-id="0646f-196">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="0646f-197">Сторонние решения для хранения в браузере</span><span class="sxs-lookup"><span data-stu-id="0646f-197">Third-party browser storage solutions</span></span>

<span data-ttu-id="0646f-198">Сторонние пакеты NuGet предоставляют интерфейсы API для работы с `localStorage` и `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="0646f-198">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="0646f-199">Стоит рассмотреть выбор пакета, который прозрачно использует [защиту данных](xref:security/data-protection/introduction) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0646f-199">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="0646f-200">Защита данных ASP.NET Core шифрует хранимые данные и уменьшает потенциальный риск несанкционированного изменения хранимых данных.</span><span class="sxs-lookup"><span data-stu-id="0646f-200">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="0646f-201">Если сериализованные данные JSON хранятся в виде обычного текста, пользователи могут просматривать данные с помощью средств разработчика браузера, а также изменять сохраненные данные.</span><span class="sxs-lookup"><span data-stu-id="0646f-201">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="0646f-202">Защита данных не всегда является проблемой, так как данные могут быть тривиальными по своей природе.</span><span class="sxs-lookup"><span data-stu-id="0646f-202">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="0646f-203">Например, чтение или изменение сохраненного цвета элемента пользовательского интерфейса не является серьезной угрозой безопасности для пользователя или организации.</span><span class="sxs-lookup"><span data-stu-id="0646f-203">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="0646f-204">Не разрешайте пользователям проверять или изменять *конфиденциальные данные*.</span><span class="sxs-lookup"><span data-stu-id="0646f-204">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="0646f-205">Экспериментальный пакет Protected Browser Storage</span><span class="sxs-lookup"><span data-stu-id="0646f-205">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="0646f-206">Примером пакета NuGet, который предоставляет [защиту данных](xref:security/data-protection/introduction) для `localStorage` и `sessionStorage`, является [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="0646f-206">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="0646f-207">`Microsoft.AspNetCore.ProtectedBrowserStorage` является неподдерживаемым экспериментальным пакетом, который в настоящее время не подходит для использования в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="0646f-207">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="0646f-208">Установка</span><span class="sxs-lookup"><span data-stu-id="0646f-208">Installation</span></span>

<span data-ttu-id="0646f-209">Чтобы установить пакет `Microsoft.AspNetCore.ProtectedBrowserStorage`, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="0646f-209">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="0646f-210">В проекте приложения [Blazor Server добавьте ссылку на пакет [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="0646f-210">In the [Blazor Server app project, add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="0646f-211">В HTML верхнего уровня (например, в файле `Pages/_Host.cshtml` в шаблоне проекта по умолчанию) добавьте следующий тег `<script>`:</span><span class="sxs-lookup"><span data-stu-id="0646f-211">In the top-level HTML (for example, in the `Pages/_Host.cshtml` file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="0646f-212">В методе `Startup.ConfigureServices` вызовите `AddProtectedBrowserStorage`, чтобы добавить службы `localStorage` и `sessionStorage` в коллекцию служб.</span><span class="sxs-lookup"><span data-stu-id="0646f-212">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="0646f-213">Сохранение и загрузка данных в компоненте</span><span class="sxs-lookup"><span data-stu-id="0646f-213">Save and load data within a component</span></span>

<span data-ttu-id="0646f-214">В любом компоненте, требующем загрузки или сохранения данных в хранилище браузера, используйте [`@inject`](xref:mvc/views/razor#inject) для вставки экземпляра одного из следующих компонентов.</span><span class="sxs-lookup"><span data-stu-id="0646f-214">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:mvc/views/razor#inject) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="0646f-215">Выбор зависит от того, какое резервное хранилище вы хотите использовать.</span><span class="sxs-lookup"><span data-stu-id="0646f-215">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="0646f-216">В следующем примере используется `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="0646f-216">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="0646f-217">Инструкцию `@using` можно поместить в файл `_Imports.razor`, а не в компонент.</span><span class="sxs-lookup"><span data-stu-id="0646f-217">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="0646f-218">Использование файла `_Imports.razor` делает пространство имен доступным для больших сегментов приложения или всего приложения.</span><span class="sxs-lookup"><span data-stu-id="0646f-218">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="0646f-219">Чтобы сохранить значение `currentCount` в компонент шаблона проекта `Counter`, измените метод `IncrementCount` для использования `ProtectedSessionStore.SetAsync`.</span><span class="sxs-lookup"><span data-stu-id="0646f-219">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="0646f-220">В больших и более реалистичных приложениях хранение отдельных полей является маловероятной ситуацией.</span><span class="sxs-lookup"><span data-stu-id="0646f-220">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="0646f-221">Приложения, скорее всего, будут хранить все объекты модели, включающие сложное состояние.</span><span class="sxs-lookup"><span data-stu-id="0646f-221">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="0646f-222">`ProtectedSessionStore` автоматически сериализует и десериализует данные JSON.</span><span class="sxs-lookup"><span data-stu-id="0646f-222">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="0646f-223">В предыдущем примере кода данные `currentCount` хранятся в виде `sessionStorage['count']` в браузере пользователя.</span><span class="sxs-lookup"><span data-stu-id="0646f-223">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="0646f-224">Данные не хранятся в виде обычного текста, а защищаются с помощью [защиты данных](xref:security/data-protection/introduction) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0646f-224">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="0646f-225">Зашифрованные данные можно увидеть, если `sessionStorage['count']` оценивается в консоли разработчика браузера.</span><span class="sxs-lookup"><span data-stu-id="0646f-225">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="0646f-226">Чтобы восстановить данные `currentCount`, когда пользователь снова возвращается в компонент `Counter` (в том числе если он находится в совершенно новом канале), используйте `ProtectedSessionStore.GetAsync`.</span><span class="sxs-lookup"><span data-stu-id="0646f-226">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="0646f-227">Если параметры компонента включают состояние навигации, вызовите `ProtectedSessionStore.GetAsync` и назначьте результат в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, а не в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="0646f-227">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="0646f-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> вызывается только один раз при первом создании экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="0646f-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="0646f-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> не вызывается позже, если пользователь переходит на другой URL-адрес, оставаясь на той же странице.</span><span class="sxs-lookup"><span data-stu-id="0646f-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="0646f-230">Для получения дополнительной информации см. <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="0646f-230">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="0646f-231">Примеры в этом разделе работают только в том случае, если на сервере не включена предварительная отрисовка.</span><span class="sxs-lookup"><span data-stu-id="0646f-231">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="0646f-232">При включенной предварительной отрисовке возникает ошибка следующего вида.</span><span class="sxs-lookup"><span data-stu-id="0646f-232">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="0646f-233">В настоящее время вызовы взаимодействия JavaScript не могут быть выполнены.</span><span class="sxs-lookup"><span data-stu-id="0646f-233">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="0646f-234">Это связано с тем, что компонент предварительно отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="0646f-234">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="0646f-235">Отключите предварительную отрисовку или добавьте дополнительный код для работы с предварительной отрисовкой.</span><span class="sxs-lookup"><span data-stu-id="0646f-235">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="0646f-236">Дополнительные сведения о написании кода, который работает с предварительной отрисовкой, см. в разделе [Обработка предварительной отрисовки](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="0646f-236">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="0646f-237">Обработка состояния загрузки</span><span class="sxs-lookup"><span data-stu-id="0646f-237">Handle the loading state</span></span>

<span data-ttu-id="0646f-238">Так как хранилище браузера является асинхронным (доступным через сетевое подключение), всегда есть период времени, прежде чем данные будут загружены и доступны для использования компонентом.</span><span class="sxs-lookup"><span data-stu-id="0646f-238">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="0646f-239">Для достижения лучших результатов перед отображением пустых данных или данных по умолчанию выводится сообщение о состоянии загрузки.</span><span class="sxs-lookup"><span data-stu-id="0646f-239">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="0646f-240">Один из подходов состоит в том, чтобы определить, является ли значение данных равным `null` (все еще загружаются) или нет.</span><span class="sxs-lookup"><span data-stu-id="0646f-240">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="0646f-241">В компоненте `Counter` по умолчанию количество хранится в `int`.</span><span class="sxs-lookup"><span data-stu-id="0646f-241">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="0646f-242">Сделайте `currentCount` допускающим значение NULL, добавив вопросительный знак (`?`) к типу (`int`).</span><span class="sxs-lookup"><span data-stu-id="0646f-242">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="0646f-243">Вместо безусловного отображения количества и кнопки **`Increment`** настройте отображение этих элементов только в том случае, если данные загружены.</span><span class="sxs-lookup"><span data-stu-id="0646f-243">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="0646f-244">Обработка предварительной отрисовки</span><span class="sxs-lookup"><span data-stu-id="0646f-244">Handle prerendering</span></span>

<span data-ttu-id="0646f-245">Во время предварительной отрисовки происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="0646f-245">During prerendering:</span></span>

* <span data-ttu-id="0646f-246">интерактивное подключение к браузеру пользователя не существует;</span><span class="sxs-lookup"><span data-stu-id="0646f-246">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="0646f-247">в браузере еще нет страницы, на которой можно запустить код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0646f-247">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="0646f-248">`localStorage` или `sessionStorage` недоступны во время предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="0646f-248">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="0646f-249">Если компонент пытается взаимодействовать с хранилищем, возникает ошибка следующего вида.</span><span class="sxs-lookup"><span data-stu-id="0646f-249">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="0646f-250">В настоящее время вызовы взаимодействия JavaScript не могут быть выполнены.</span><span class="sxs-lookup"><span data-stu-id="0646f-250">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="0646f-251">Это связано с тем, что компонент предварительно отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="0646f-251">This is because the component is being prerendered.</span></span>

<span data-ttu-id="0646f-252">Одним из способов устранения этой ошибки является отключение предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="0646f-252">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="0646f-253">Обычно это наилучший вариант, если приложение активно использует хранилище в браузере.</span><span class="sxs-lookup"><span data-stu-id="0646f-253">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="0646f-254">Предварительная отрисовка увеличивает сложность и не дает приложению никаких преимуществ, так как приложение не может выдать какое-либо полезное содержимое, пока не станут доступны `localStorage` или `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="0646f-254">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="0646f-255">Чтобы отключить предварительную отрисовку, откройте файл `Pages/_Host.cshtml` и измените `render-mode` [вспомогательной функции тега компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) на <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span><span class="sxs-lookup"><span data-stu-id="0646f-255">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="0646f-256">Предварительная отрисовка может быть полезной для других страниц, которые не используют `localStorage` или `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="0646f-256">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="0646f-257">Чтобы включить предварительную отрисовку, отложите операцию загрузки до тех пор, пока браузер не подключится к каналу.</span><span class="sxs-lookup"><span data-stu-id="0646f-257">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="0646f-258">Ниже приведен пример хранения значения счетчика.</span><span class="sxs-lookup"><span data-stu-id="0646f-258">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="0646f-259">Перенос сохранения состояния в общее расположение</span><span class="sxs-lookup"><span data-stu-id="0646f-259">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="0646f-260">Если многие компоненты используют хранилище на основе браузера, повторная реализация кода поставщика состояний много раз создает дублирование кода.</span><span class="sxs-lookup"><span data-stu-id="0646f-260">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="0646f-261">Одним из вариантов предотвращения дублирования кода является создание *родительского компонента поставщика состояний*, который инкапсулирует логику поставщика состояний.</span><span class="sxs-lookup"><span data-stu-id="0646f-261">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="0646f-262">Дочерние компоненты могут работать с сохраненными данными без учета механизма сохранения состояния.</span><span class="sxs-lookup"><span data-stu-id="0646f-262">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="0646f-263">В следующем примере компонента `CounterStateProvider` данные счетчика сохраняются.</span><span class="sxs-lookup"><span data-stu-id="0646f-263">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
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
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="0646f-264">Компонент `CounterStateProvider` обрабатывает этап загрузки, не выполняя отрисовку его дочернего содержимого до завершения загрузки.</span><span class="sxs-lookup"><span data-stu-id="0646f-264">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="0646f-265">Чтобы использовать компонент `CounterStateProvider`, оберните экземпляр компонента вокруг любого другого компонента, которому требуется доступ к состоянию счетчика.</span><span class="sxs-lookup"><span data-stu-id="0646f-265">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="0646f-266">Чтобы сделать состояние доступным для всех компонентов в приложении, оберните `CounterStateProvider` компонент вокруг <xref:Microsoft.AspNetCore.Components.Routing.Router> в компоненте `App` (`App.razor`).</span><span class="sxs-lookup"><span data-stu-id="0646f-266">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="0646f-267">Упакованные компоненты получают и могут изменять состояние сохраненного счетчика.</span><span class="sxs-lookup"><span data-stu-id="0646f-267">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="0646f-268">Следующий компонент `Counter` реализует этот шаблон.</span><span class="sxs-lookup"><span data-stu-id="0646f-268">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="0646f-269">Предыдущий компонент не требуется для взаимодействия с `ProtectedBrowserStorage` и не имеет отношения к этапу "загрузки".</span><span class="sxs-lookup"><span data-stu-id="0646f-269">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="0646f-270">Для работы с предварительной отрисовкой, как описано выше, `CounterStateProvider` можно сделать так, чтобы все компоненты, использующие данные счетчика, автоматически работали с предварительной отрисовкой.</span><span class="sxs-lookup"><span data-stu-id="0646f-270">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="0646f-271">Дополнительные сведения см. в разделе [Обработка предварительной отрисовки](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="0646f-271">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="0646f-272">В целом рекомендуется использовать шаблон *родительского компонента поставщика состояний* в следующих случаях.</span><span class="sxs-lookup"><span data-stu-id="0646f-272">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="0646f-273">Для использования состояния во многих других компонентах.</span><span class="sxs-lookup"><span data-stu-id="0646f-273">To consume state in many other components.</span></span>
* <span data-ttu-id="0646f-274">Если имеется только один объект состояния верхнего уровня для сохранения.</span><span class="sxs-lookup"><span data-stu-id="0646f-274">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="0646f-275">Чтобы сохранить множество различных объектов состояния и использовать разные подмножества объектов в разных местах, лучше избегать глобальной обработки загрузки и сохранения состояния.</span><span class="sxs-lookup"><span data-stu-id="0646f-275">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
