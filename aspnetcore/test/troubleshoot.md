---
title: Устранение неполадок и отладка проектов ASP.NET Core
author: Rick-Anderson
description: Устранение неполадок при возникновении ошибок и предупреждений в проектах ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot
ms.openlocfilehash: bd5e74569717db26496760eaaa2760122d4b49e7
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106498"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="4b0fa-103">Устранение неполадок и отладка проектов ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b0fa-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="4b0fa-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="4b0fa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4b0fa-105">Рекомендации по устранению неполадок приведены по следующим ссылкам:</span><span class="sxs-lookup"><span data-stu-id="4b0fa-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="4b0fa-106">Конференция NDC (Лондон, 2018): диагностика проблем в приложениях ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b0fa-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="4b0fa-107">Блог об ASP.NET. Устранение проблем ASP.NET Core, связанных с производительностью</span><span class="sxs-lookup"><span data-stu-id="4b0fa-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="4b0fa-108">Предупреждение пакета SDK для .NET Core</span><span class="sxs-lookup"><span data-stu-id="4b0fa-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="4b0fa-109">Установлены как 32-разрядная, так и 64-разрядная версии пакета SDK для .NET Core</span><span class="sxs-lookup"><span data-stu-id="4b0fa-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="4b0fa-110">В диалоговом окне **Новый проект** для ASP.NET Core может отобразиться следующее предупреждение.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="4b0fa-111">Установлены как 32-разрядная, так и 64-разрядная версии пакета SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="4b0fa-112">Отображаются только шаблоны 64-разрядных версий, установленных в папке "C:\\Program Files\\dotnet\\sdk\\".</span><span class="sxs-lookup"><span data-stu-id="4b0fa-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="4b0fa-113">Это предупреждение появляется при одновременной установке 32-разрядной (x86) и 64-разрядной (x64) версий [пакета SDK для .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="4b0fa-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="4b0fa-114">Ниже перечислены распространенные причины, по которым могут быть установлены обе версии:</span><span class="sxs-lookup"><span data-stu-id="4b0fa-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="4b0fa-115">вы первоначально загрузили установщик пакета SDK для .NET Core на 32-разрядном компьютере, а затем скопировали его и установили на 64-разрядном компьютере;</span><span class="sxs-lookup"><span data-stu-id="4b0fa-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="4b0fa-116">32-разрядная версия пакета SDK для .NET Core была установлена другим приложением;</span><span class="sxs-lookup"><span data-stu-id="4b0fa-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="4b0fa-117">была загружена и установлена неправильная версия.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="4b0fa-118">Удалите 32-разрядную версию пакета SDK для .NET Core, чтобы устранить это предупреждение.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="4b0fa-119">Для удаления перейдите в **Панель управления** > **Программы и компоненты** > **Удаление или изменение программы**.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="4b0fa-120">Если вы понимаете, почему возникает предупреждение и его последствия, вы можете проигнорировать это предупреждение.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="4b0fa-121">Пакет SDK для .NET Core установлен в нескольких расположениях</span><span class="sxs-lookup"><span data-stu-id="4b0fa-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="4b0fa-122">В диалоговом окне **Новый проект** для ASP.NET Core может отобразиться следующее предупреждение.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="4b0fa-123">Пакет SDK для .NET Core установлен в нескольких расположениях.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="4b0fa-124">Отображаются только шаблоны пакетов SDK, установленных в папке "C:\\Program Files\\dotnet\\sdk\\".</span><span class="sxs-lookup"><span data-stu-id="4b0fa-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="4b0fa-125">Это сообщение появляется при наличии хотя бы одной установки пакета SDK для .NET Core в каталоге за пределами *C:\\Program Files\\dotnet\\sdk\\* .</span><span class="sxs-lookup"><span data-stu-id="4b0fa-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="4b0fa-126">Обычно это происходит, когда пакет SDK для .NET Core развертывается на компьютере с помощью команды копировать/вставить, а не с помощью установщика MSI.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="4b0fa-127">Удалите все 32-разрядные версии пакетов SDK и среды выполнения .NET Core, чтобы устранить это предупреждение.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="4b0fa-128">Для удаления перейдите в **Панель управления** > **Программы и компоненты** > **Удаление или изменение программы**.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="4b0fa-129">Если вы понимаете, почему возникает предупреждение и его последствия, вы можете проигнорировать это предупреждение.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="4b0fa-130">Пакеты SDK для .NET Core не обнаружены</span><span class="sxs-lookup"><span data-stu-id="4b0fa-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="4b0fa-131">В Visual Studio в диалоговом окне **Новый проект** для ASP.NET Core может отобразиться следующее предупреждение.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="4b0fa-132">Пакеты SDK для .NET Core не обнаружены. Они должны быть включены в переменную среды `PATH`.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="4b0fa-133">При выполнении команды `dotnet` предупреждение выглядит следующим образом.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="4b0fa-134">Не удалось найти установленные пакеты SDK dotnet.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="4b0fa-135">Эти предупреждения появляются, когда переменная среды `PATH` не содержит расположение пакетов SDK для .NET Core на компьютере.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="4b0fa-136">Для решения этой проблемы сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="4b0fa-136">To resolve this problem:</span></span>

* <span data-ttu-id="4b0fa-137">Установите пакет SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="4b0fa-138">Получите последнюю версию установщика в [разделе загрузок .NET](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="4b0fa-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="4b0fa-139">Убедитесь, что переменная среды `PATH` содержит расположение, в которое установлен пакет SDK (`C:\Program Files\dotnet\` для 64-разрядной версии — x64, или `C:\Program Files (x86)\dotnet\` для 32-разрядной версии — x86).</span><span class="sxs-lookup"><span data-stu-id="4b0fa-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="4b0fa-140">Установщик пакета SDK обычно устанавливает значение `PATH`.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="4b0fa-141">Всегда устанавливайте на одном компьютере пакеты SDK и среды выполнения одинаковой разрядности.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="4b0fa-142">Отсутствует пакет SDK после установки пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="4b0fa-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="4b0fa-143">Установка [пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) изменяет переменную среды `PATH` при установке среды выполнения .NET Core, чтобы она указывала на 32-разрядную (x86) версию .NET Core (`C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="4b0fa-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="4b0fa-144">Это может привести к отсутствию пакетов SDK при использовании 32-разрядной (x86) команды .NET Core `dotnet` ([Пакеты SDK для .NET Core не обнаружены](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="4b0fa-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="4b0fa-145">Для решения этой проблемы в переменной `PATH` переместите значение `C:\Program Files\dotnet\` в положение перед `C:\Program Files (x86)\dotnet\`.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="4b0fa-146">Получение данных из приложения</span><span class="sxs-lookup"><span data-stu-id="4b0fa-146">Obtain data from an app</span></span>

<span data-ttu-id="4b0fa-147">Если приложение может отвечать на запросы, можно получить следующие данные из приложения с помощью ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="4b0fa-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="4b0fa-148">Запрос Метод, схема, узел, свойство pathbase, путь, строка запроса, заголовки</span><span class="sxs-lookup"><span data-stu-id="4b0fa-148">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="4b0fa-149">Подключение: Удаленный IP-адрес, удаленный порт, локальный IP-адрес, локальный порт, сертификат клиента</span><span class="sxs-lookup"><span data-stu-id="4b0fa-149">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* Identity: Name, display name
* <span data-ttu-id="4b0fa-150">Параметры конфигурации</span><span class="sxs-lookup"><span data-stu-id="4b0fa-150">Configuration settings</span></span>
* <span data-ttu-id="4b0fa-151">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="4b0fa-151">Environment variables</span></span>

<span data-ttu-id="4b0fa-152">Поместите следующий код [ПО промежуточного слоя](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) в начале конвейера обработки запроса метода `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-152">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="4b0fa-153">Среда проверяется перед выполнением ПО промежуточного слоя, чтобы гарантировать выполнение кода только в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-153">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="4b0fa-154">Чтобы получить среду, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-154">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="4b0fa-155">Вставьте `IHostingEnvironment` в метод `Startup.Configure` и проверьте окружение с помощью локальной переменной.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-155">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="4b0fa-156">Этот подход демонстрируется в следующем образце кода.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-156">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="4b0fa-157">Назначьте среду свойству в классе `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-157">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="4b0fa-158">Проверьте среду с помощью свойства (например, `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="4b0fa-158">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="4b0fa-159">Отладка приложений ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b0fa-159">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="4b0fa-160">Следующие ссылки содержат сведения об отладке приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b0fa-160">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="4b0fa-161">Отладка ASP Core в Linux</span><span class="sxs-lookup"><span data-stu-id="4b0fa-161">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="4b0fa-162">Отладка .NET Core в UNIX через SSH</span><span class="sxs-lookup"><span data-stu-id="4b0fa-162">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="4b0fa-163">Краткое руководство. Отладка в ASP.NET с помощью отладчика Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0fa-163">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="4b0fa-164">Дополнительные сведения об отладке см. в [этой статье об ошибке на GitHub ](https://github.com/dotnet/AspNetCore.Docs/issues/2960).</span><span class="sxs-lookup"><span data-stu-id="4b0fa-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
