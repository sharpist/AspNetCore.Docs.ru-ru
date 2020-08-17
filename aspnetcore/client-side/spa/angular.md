---
title: Использование шаблона проекта Angular с ASP.NET Core
author: SteveSandersonMS
description: Дополнительные сведения о начале работы с шаблоном проекта одностраничного приложения (SPA) ASP.NET Core для Angular и Angular CLI.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
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
uid: spa/angular
ms.openlocfilehash: ce29d891b0fba2581d352637c2ae50ac792b785e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013168"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="67086-103">Использование шаблона проекта Angular с ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67086-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="67086-104">Обновленный шаблон проекта Angular обеспечивает удобную отправную точку для приложений ASP.NET Core с использованием Angular и Angular CLI для реализации богатого пользовательского интерфейса (UI) на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="67086-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="67086-105">Шаблон является эквивалентом созданию проекта ASP.NET Core, выступающего в качестве серверного API, и проекта Angular CLI в качестве пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="67086-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="67086-106">В шаблоне предлагается удобное размещения обоих типов проектов в проекте отдельного приложения.</span><span class="sxs-lookup"><span data-stu-id="67086-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="67086-107">Что в свою очередь позволит создать и опубликовать проект приложения как единое целое.</span><span class="sxs-lookup"><span data-stu-id="67086-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="67086-108">Создание нового приложения</span><span class="sxs-lookup"><span data-stu-id="67086-108">Create a new app</span></span>

<span data-ttu-id="67086-109">Если установлена ASP.NET Core 2.1, не нужно устанавливать проект шаблона Angular.</span><span class="sxs-lookup"><span data-stu-id="67086-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="67086-110">Создайте из командной строки новый проект в пустом каталоге с помощью команды `dotnet new angular`.</span><span class="sxs-lookup"><span data-stu-id="67086-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="67086-111">Например, следующие команды позволяют создать приложение в каталоге *my-new-app* и перейти к этому каталогу:</span><span class="sxs-lookup"><span data-stu-id="67086-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="67086-112">Запустите приложение в Visual Studio или в .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="67086-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67086-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67086-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="67086-114">Откройте созданный файл проекта *.csproj* и запустите в нем приложение в обычном режиме.</span><span class="sxs-lookup"><span data-stu-id="67086-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="67086-115">В процессе сборки при первом запуске восстанавливаются зависимости npm. Это может занять несколько минут.</span><span class="sxs-lookup"><span data-stu-id="67086-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="67086-116">Последующие сборки выполняются гораздо быстрее.</span><span class="sxs-lookup"><span data-stu-id="67086-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="67086-117">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="67086-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="67086-118">Убедитесь, что у вас существует переменная среда `ASPNETCORE_Environment`, которой соответствует значение `Development`.</span><span class="sxs-lookup"><span data-stu-id="67086-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="67086-119">Запустите команду `SET ASPNETCORE_Environment=Development` в ОС Windows (в приглашениях, отличных от PowerShell).</span><span class="sxs-lookup"><span data-stu-id="67086-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="67086-120">А в ОС Linux или macOS запустите команду `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="67086-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="67086-121">Чтобы проверить наличие ошибок при создании приложения, запустите команду [dotnet-build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="67086-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="67086-122">При первом запуске процесса создания будут восстановлены зависимости npm, на что может потребоваться несколько минут.</span><span class="sxs-lookup"><span data-stu-id="67086-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="67086-123">Последующие сборки выполняются гораздо быстрее.</span><span class="sxs-lookup"><span data-stu-id="67086-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="67086-124">Чтобы запустить приложение, выполните команду [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="67086-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="67086-125">Следующее сообщение будет записано в журнал.</span><span class="sxs-lookup"><span data-stu-id="67086-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="67086-126">Перейдите по этому URL-адресу в браузере.</span><span class="sxs-lookup"><span data-stu-id="67086-126">Navigate to this URL in a browser.</span></span>

> [!WARNING]
> <span data-ttu-id="67086-127">Приложение запускает экземпляр сервера Angular CLI в фоновом режиме.</span><span class="sxs-lookup"><span data-stu-id="67086-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="67086-128">Следующее сообщение будет записано в журнал. *NG Live Development Server выполняет прослушивание порта localhost:&lt;otherport&gt;, откройте браузер для порта http://localhost:&lt;otherport&gt;/* .</span><span class="sxs-lookup"><span data-stu-id="67086-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open a browser to http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="67086-129">Это сообщение необходимо проигнорировать&mdash;оно **не** является URL-адресом объединенного приложения ASP.NET Core и Angular CLI.</span><span class="sxs-lookup"><span data-stu-id="67086-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="67086-130">Шаблон проекта создает приложение ASP.NET Core и приложение Angular.</span><span class="sxs-lookup"><span data-stu-id="67086-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="67086-131">Приложение ASP.NET Core предназначено для использования в таких сферах как: получение доступа к данным, авторизация и других проблемных вопросах на стороне сервера.Приложение ASP.NET Core предназначено для доступа к данным, авторизации и других задач на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="67086-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="67086-132">Приложение Angular, размещенное в подкаталоге *ClientApp*, предназначено для всех задач, связанных с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="67086-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="67086-133">Добавление страниц, изображений, стилей, модулей и т. д.</span><span class="sxs-lookup"><span data-stu-id="67086-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="67086-134">Каталог *ClientApp* содержит стандартное приложение Angular CLI.</span><span class="sxs-lookup"><span data-stu-id="67086-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="67086-135">дополнительные сведения в [официальной документации Angular](https://angular.io).</span><span class="sxs-lookup"><span data-stu-id="67086-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="67086-136">Существуют небольшие различия между приложениями Angular, создаваемыми этим шаблоном и создаваемыми непосредственно Angular CLI (командой `ng new`), однако возможности приложений одинаковые.</span><span class="sxs-lookup"><span data-stu-id="67086-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="67086-137">Приложение, созданное с помощью шаблона, содержит основанный на [Bootstrap](https://getbootstrap.com/) макет и простой пример маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67086-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="67086-138">Выполнение команд ng</span><span class="sxs-lookup"><span data-stu-id="67086-138">Run ng commands</span></span>

<span data-ttu-id="67086-139">В командной строке перейдите в подкаталог *ClientApp*:</span><span class="sxs-lookup"><span data-stu-id="67086-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="67086-140">Если средство `ng` установлено глобально, вы можете запустить любую из его команд.</span><span class="sxs-lookup"><span data-stu-id="67086-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="67086-141">Например, можно запустить `ng lint`, `ng test` или любую другую [команду Angular CLI](https://angular.io/cli).</span><span class="sxs-lookup"><span data-stu-id="67086-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="67086-142">Выполнять `ng serve` не требуется, так как приложения ASP.NET Core обслуживают как серверную, так и клиентскую части приложения.</span><span class="sxs-lookup"><span data-stu-id="67086-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="67086-143">На внутреннем уровне они используют `ng serve` в процессе разработки.</span><span class="sxs-lookup"><span data-stu-id="67086-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="67086-144">Если у вас не установлено средство `ng`, запустите вместо него `npm run ng`.</span><span class="sxs-lookup"><span data-stu-id="67086-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="67086-145">Например, можно запустить `npm run ng lint` или `npm run ng test`.</span><span class="sxs-lookup"><span data-stu-id="67086-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="67086-146">Установка пакетов npm</span><span class="sxs-lookup"><span data-stu-id="67086-146">Install npm packages</span></span>

<span data-ttu-id="67086-147">Для установки npm-пакетов сторонних разработчиков используйте в командной строке подкаталог *ClientApp*.</span><span class="sxs-lookup"><span data-stu-id="67086-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="67086-148">Пример:</span><span class="sxs-lookup"><span data-stu-id="67086-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="67086-149">Публикация и развертывание</span><span class="sxs-lookup"><span data-stu-id="67086-149">Publish and deploy</span></span>

<span data-ttu-id="67086-150">В процессе разработки приложение запускается в режиме, оптимизированном для удобства разработчиков.</span><span class="sxs-lookup"><span data-stu-id="67086-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="67086-151">Например, пакеты JavaScript включают сопоставления с исходным кодом (благодаря чему при отладке вы сможете видеть исходный код TypeScript).</span><span class="sxs-lookup"><span data-stu-id="67086-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="67086-152">Приложение следит за изменениями файлов TypeScript, HTML и CSS на диске и при обнаружении изменений автоматически производит перекомпиляцию и перезагрузку страницы.</span><span class="sxs-lookup"><span data-stu-id="67086-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="67086-153">На рабочем этапе используйте версию приложения, оптимизированную для производительности.</span><span class="sxs-lookup"><span data-stu-id="67086-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="67086-154">На нее уже настроено автоматическое переключение.</span><span class="sxs-lookup"><span data-stu-id="67086-154">This is configured to happen automatically.</span></span> <span data-ttu-id="67086-155">При публикации конфигурация сборки выдает уменьшенные, предварительно скомпилированные (AoT) сборки клиентского кода.</span><span class="sxs-lookup"><span data-stu-id="67086-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="67086-156">В отличие от сборки в режиме разработки, установка на сервере Node.js не требуется (если только не используется предварительная отрисовка на стороне сервера [SSR]).</span><span class="sxs-lookup"><span data-stu-id="67086-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="67086-157">Можно использовать стандартные [методы размещения и развертывания ASP.NET Core](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="67086-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="67086-158">Запуск "ng serve" отдельно</span><span class="sxs-lookup"><span data-stu-id="67086-158">Run "ng serve" independently</span></span>

<span data-ttu-id="67086-159">Проект настроен запускать свой собственный экземпляр сервера Angular CLI в фоновом режиме, когда приложение ASP.NET Core запускается в режиме разработки.</span><span class="sxs-lookup"><span data-stu-id="67086-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="67086-160">Это удобно, так как нет необходимости вручную запускать отдельный сервер.</span><span class="sxs-lookup"><span data-stu-id="67086-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="67086-161">У этой настройки по умолчанию есть недостатки.</span><span class="sxs-lookup"><span data-stu-id="67086-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="67086-162">Каждый раз при изменении кода на C# и необходимости перезапустить приложение ASP.NET Core также перезагружается и сервер Angular CLI.</span><span class="sxs-lookup"><span data-stu-id="67086-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="67086-163">Перезапуск может занять около 10 секунд.</span><span class="sxs-lookup"><span data-stu-id="67086-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="67086-164">Если вы часто вносите изменения в код C# и не хотите ждать перезапуска сервера Angular CLI, запустите сервер Angular CLI отдельно, независимо от процесса ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="67086-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="67086-165">Для этого сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="67086-165">To do so:</span></span>

1. <span data-ttu-id="67086-166">В командной строке перейдите в подкаталог *ClientApp* и запустите сервер разработки Angular CLI следующей командой:</span><span class="sxs-lookup"><span data-stu-id="67086-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="67086-167">Используйте `npm start`, а не `ng serve` для запуска сервера разработки Angular CLI, в результате чего будет учитываться конфигурация в файле *package.json*.</span><span class="sxs-lookup"><span data-stu-id="67086-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="67086-168">Чтобы передать серверу Angular CLI дополнительные параметры, добавьте их в *package.json* в нужную строку `scripts`.</span><span class="sxs-lookup"><span data-stu-id="67086-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="67086-169">Измените свои приложения ASP.NET Core так, чтобы они использовали внешний экземпляр сервера Angular CLI, а не запускали собственный.</span><span class="sxs-lookup"><span data-stu-id="67086-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="67086-170">В вашем классе *Startup* замените вызов `spa.UseAngularCliServer` на следующий:</span><span class="sxs-lookup"><span data-stu-id="67086-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="67086-171">При запуске приложения ASP.NET Core экземпляр сервера Angular CLI запускаться не будет.</span><span class="sxs-lookup"><span data-stu-id="67086-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="67086-172">Вместо него будет использоваться экземпляр, запускаемый вручную.</span><span class="sxs-lookup"><span data-stu-id="67086-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="67086-173">Это ускорит запуск и перезагрузку.</span><span class="sxs-lookup"><span data-stu-id="67086-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="67086-174">Теперь не придется каждый раз дожидаться повторной сборки клиентского приложения в Angular CLI.</span><span class="sxs-lookup"><span data-stu-id="67086-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="67086-175">Передача данных от .NET к TypeScript</span><span class="sxs-lookup"><span data-stu-id="67086-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="67086-176">Во время работы SSR может возникнуть необходимость передать данные отдельных запросов из ASP.NET Core в Angular.</span><span class="sxs-lookup"><span data-stu-id="67086-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="67086-177">Например, можно передать сведения из файла cookie или данные, полученные из базы данных.</span><span class="sxs-lookup"><span data-stu-id="67086-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="67086-178">Чтобы это сделать, необходимо внести изменения в класс *Startup*.</span><span class="sxs-lookup"><span data-stu-id="67086-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="67086-179">В обратном вызове `UseSpaPrerendering` для `options.SupplyData` установите указанное ниже значение.</span><span class="sxs-lookup"><span data-stu-id="67086-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="67086-180">С помощью обратного вызова `SupplyData` можно передавать произвольные данные, отдельные запросы, а также JSON-сериализируемые данные (например, строки, логические значения или числа).</span><span class="sxs-lookup"><span data-stu-id="67086-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="67086-181">Для кода в файле *main.server.ts* эти данные будут получены в качестве `params.data`.</span><span class="sxs-lookup"><span data-stu-id="67086-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="67086-182">Например, в предыдущем примере кода логическое значение передается как `params.data.isHttpsRequest` в обратный вызов `createServerRenderer`.</span><span class="sxs-lookup"><span data-stu-id="67086-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="67086-183">Также эти данные можно передавать другим частям приложения любым способом, поддерживаемым Angular.</span><span class="sxs-lookup"><span data-stu-id="67086-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="67086-184">Например, ознакомьтесь с тем, как *main.server.ts* передает значения `BASE_URL` любому компоненту, конструктор которого объявлен получателем.</span><span class="sxs-lookup"><span data-stu-id="67086-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="67086-185">Недостатки SSR</span><span class="sxs-lookup"><span data-stu-id="67086-185">Drawbacks of SSR</span></span>

<span data-ttu-id="67086-186">Не все приложения получают преимущество от использования SSR.</span><span class="sxs-lookup"><span data-stu-id="67086-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="67086-187">Основное преимущество — воспринимаемая производительность.</span><span class="sxs-lookup"><span data-stu-id="67086-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="67086-188">При использовании вашего приложения посетители, у которых низкая скорость сетевого подключения или медленные мобильные устройства, быстро обнаруживают первоначальный пользовательский интерфейс, даже если для получения или анализа пакетов JavaScript требуется некоторое время.</span><span class="sxs-lookup"><span data-stu-id="67086-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="67086-189">Тем не менее в основном многие одностраничные приложения (SPA) используются в быстрых внутренних сетях компании на быстрых компьютерах, где приложение появляется почти мгновенно.</span><span class="sxs-lookup"><span data-stu-id="67086-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="67086-190">В тоже время при использовании SSR существует ряд недостатков.</span><span class="sxs-lookup"><span data-stu-id="67086-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="67086-191">В процесс разработки вносится дополнительная сложность.</span><span class="sxs-lookup"><span data-stu-id="67086-191">It adds complexity to your development process.</span></span> <span data-ttu-id="67086-192">Код должен быть запущен в двух разных средах как на стороне клиента, так и на стороне сервера (в среде Node.js, вызываемой из ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="67086-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="67086-193">Ниже приведены некоторые аспекты, которые необходимо учитывать.</span><span class="sxs-lookup"><span data-stu-id="67086-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="67086-194">Для SSR необходимо, чтобы на рабочих серверах был установлен Node.js.</span><span class="sxs-lookup"><span data-stu-id="67086-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="67086-195">В некоторых сценариях это происходит автоматически (например, службы приложений Azure), а в других — нет (например, Azure Service Fabric).</span><span class="sxs-lookup"><span data-stu-id="67086-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="67086-196">Установка флага сборки `BuildServerSideRenderer` приведет к публикации каталога *node_modules*.</span><span class="sxs-lookup"><span data-stu-id="67086-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="67086-197">В этой папке находятся более 20 000 файлов, что повлечет за собой увеличение времени развертывания.</span><span class="sxs-lookup"><span data-stu-id="67086-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="67086-198">Для запуска кода в среде Node.js нельзя полагаться на такие существующие API JavaScript как `window` или `localStorage`.</span><span class="sxs-lookup"><span data-stu-id="67086-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="67086-199">Если во время выполнения SSR для кода (или некоторых используемых библиотек сторонних разработчиков) будут использованы эти API, выполнение SSR завершится сбоем.</span><span class="sxs-lookup"><span data-stu-id="67086-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="67086-200">Например, не используйте jQuery, так как часто он использует на браузерные API.</span><span class="sxs-lookup"><span data-stu-id="67086-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="67086-201">Чтобы предотвратить возникновение ошибки, необходимо избегать использовать SSR или браузерных API-интерфейсов и библиотек.</span><span class="sxs-lookup"><span data-stu-id="67086-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="67086-202">Любые вызовы могут быть перенесены в проверки API, чтобы гарантировать, что они не будут вызваны во время SSR.</span><span class="sxs-lookup"><span data-stu-id="67086-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="67086-203">Например, в коде JavaScript или TypeScript можно использовать следующую проверку:</span><span class="sxs-lookup"><span data-stu-id="67086-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="67086-204">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="67086-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
