---
title: Настройка проверки подлинности Windows в ASP.NET Core
author: scottaddie
description: Узнайте, как настроить проверку подлинности Windows в ASP.NET Core для IIS и HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330692"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="88589-103">Настройка проверки подлинности Windows в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="88589-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="88589-104">Автор: [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="88589-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88589-105">Проверка подлинности Windows (также известная как согласование, Kerberos или проверка подлинности NTLM) может быть настроена для ASP.NET Core приложений, размещенных в [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)или [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="88589-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="88589-106">Проверка подлинности Windows (также известная как Negotiate, Kerberos или NTLM) может быть настроена для ASP.NET Core приложений, размещенных в [IIS](xref:host-and-deploy/iis/index) или [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="88589-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="88589-107">Проверка подлинности Windows полагается на операционную систему для проверки подлинности пользователей ASP.NET Core приложений.</span><span class="sxs-lookup"><span data-stu-id="88589-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="88589-108">Проверку подлинности Windows можно использовать, когда сервер работает в корпоративной сети, используя удостоверения домена Active Directory или учетные записи Windows для идентификации пользователей.</span><span class="sxs-lookup"><span data-stu-id="88589-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="88589-109">Проверка подлинности Windows лучше всего подходит для сред интрасети, где пользователи, клиентские приложения и веб-серверы принадлежат одному и тому же домену Windows.</span><span class="sxs-lookup"><span data-stu-id="88589-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="88589-110">Проверка подлинности Windows не поддерживается HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="88589-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="88589-111">Проблемы проверки подлинности могут отправляться в ответах HTTP/2, но перед проверкой подлинности клиент должен перейти на HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="88589-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="88589-112">Сценарии прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="88589-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="88589-113">Проверка подлинности Windows — это сценарий с отслеживанием состояния, который в основном используется в интрасети, где прокси-сервер или балансировщик нагрузки обычно не обрабатывал трафик между клиентами и серверами.</span><span class="sxs-lookup"><span data-stu-id="88589-113">Windows Authentication is a stateful scenario primarily used in an intranet, where a proxy or load balancer doesn't usually handle traffic between clients and servers.</span></span> <span data-ttu-id="88589-114">Если используется прокси-сервер или балансировщик нагрузки, проверка подлинности Windows работает только в том случае, если прокси-сервер или балансировщик нагрузки:</span><span class="sxs-lookup"><span data-stu-id="88589-114">If a proxy or load balancer is used, Windows Authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="88589-115">Обрабатывает проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="88589-115">Handles the authentication.</span></span>
* <span data-ttu-id="88589-116">Передает сведения о проверке подлинности пользователя в приложение (например, в заголовке запроса), которое действует на данные проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="88589-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="88589-117">Альтернативой проверки подлинности Windows в средах, где используются прокси-серверы и подсистемы балансировки нагрузки, — Active Directory Федеративные службы (ADFS) с OpenID Connect Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="88589-117">An alternative to Windows Authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="88589-118">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="88589-118">IIS/IIS Express</span></span>

<span data-ttu-id="88589-119">Добавьте службы проверки подлинности, вызвав <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> пространство имен) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="88589-119">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="88589-120">Параметры запуска (отладчик)</span><span class="sxs-lookup"><span data-stu-id="88589-120">Launch settings (debugger)</span></span>

<span data-ttu-id="88589-121">Настройка параметров запуска влияет только на *Свойства и launchSettings.jsв* файле для IIS Express и не НАСТРАИВАЕТ службы IIS для проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="88589-121">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="88589-122">Конфигурация сервера описывается в разделе [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="88589-122">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="88589-123">Шаблон **веб-приложения** , доступный через Visual Studio или .NET Core CLI можно настроить для поддержки проверки подлинности Windows, который автоматически обновляет *Свойства и launchSettings.jsв* файле.</span><span class="sxs-lookup"><span data-stu-id="88589-123">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="88589-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88589-124">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="88589-125">**Новый проект**</span><span class="sxs-lookup"><span data-stu-id="88589-125">**New project**</span></span>

1. <span data-ttu-id="88589-126">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="88589-126">Create a new project.</span></span>
1. <span data-ttu-id="88589-127">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="88589-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="88589-128">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="88589-128">Select **Next**.</span></span>
1. <span data-ttu-id="88589-129">Введите имя в поле **имя проекта** .</span><span class="sxs-lookup"><span data-stu-id="88589-129">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="88589-130">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="88589-130">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="88589-131">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="88589-131">Select **Create**.</span></span>
1. <span data-ttu-id="88589-132">В разделе **Проверка подлинности**выберите **изменить** .</span><span class="sxs-lookup"><span data-stu-id="88589-132">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="88589-133">В окне **Изменение проверки подлинности** выберите **Проверка подлинности Windows**.</span><span class="sxs-lookup"><span data-stu-id="88589-133">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="88589-134">Щелкните **ОК**.</span><span class="sxs-lookup"><span data-stu-id="88589-134">Select **OK**.</span></span>
1. <span data-ttu-id="88589-135">Выберите **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="88589-135">Select **Web Application**.</span></span>
1. <span data-ttu-id="88589-136">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="88589-136">Select **Create**.</span></span>

<span data-ttu-id="88589-137">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="88589-137">Run the app.</span></span> <span data-ttu-id="88589-138">Имя пользователя отображается в пользовательском интерфейсе отображаемого приложения.</span><span class="sxs-lookup"><span data-stu-id="88589-138">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="88589-139">**Существующий проект**</span><span class="sxs-lookup"><span data-stu-id="88589-139">**Existing project**</span></span>

<span data-ttu-id="88589-140">Свойства проекта включают проверку подлинности Windows и отключение анонимной проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="88589-140">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="88589-141">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="88589-141">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="88589-142">Выберите вкладку **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="88589-142">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="88589-143">Снимите флажок **включить анонимную проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="88589-143">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="88589-144">Установите флажок **включить проверку подлинности Windows**.</span><span class="sxs-lookup"><span data-stu-id="88589-144">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="88589-145">Сохраните и закройте страницу свойств.</span><span class="sxs-lookup"><span data-stu-id="88589-145">Save and close the property page.</span></span>

<span data-ttu-id="88589-146">Кроме того, свойства можно настроить в `iisSettings` узле *launchSettings.jsв* файле:</span><span class="sxs-lookup"><span data-stu-id="88589-146">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[<span data-ttu-id="88589-147">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="88589-147">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="88589-148">**Новый проект**</span><span class="sxs-lookup"><span data-stu-id="88589-148">**New project**</span></span>

<span data-ttu-id="88589-149">Выполните команду [DotNet New](/dotnet/core/tools/dotnet-new) с `webapp` аргументом (ASP.NET Core веб-приложение) и `--auth Windows` параметром:</span><span class="sxs-lookup"><span data-stu-id="88589-149">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```dotnetcli
dotnet new webapp --auth Windows
```

<span data-ttu-id="88589-150">**Существующий проект**</span><span class="sxs-lookup"><span data-stu-id="88589-150">**Existing project**</span></span>

<span data-ttu-id="88589-151">Обновите `iisSettings` узел *launchSettings.jsв* файле:</span><span class="sxs-lookup"><span data-stu-id="88589-151">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="88589-152">При изменении существующего проекта убедитесь, что файл проекта содержит ссылку на пакет для пакета NuGet [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) **или** [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="88589-152">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="88589-153">IIS</span><span class="sxs-lookup"><span data-stu-id="88589-153">IIS</span></span>

<span data-ttu-id="88589-154">Службы IIS используют [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module) для размещения ASP.NET Core приложений.</span><span class="sxs-lookup"><span data-stu-id="88589-154">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="88589-155">Проверка подлинности Windows настраивается для служб IIS с помощью файла *web.config* .</span><span class="sxs-lookup"><span data-stu-id="88589-155">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="88589-156">В следующих разделах показано, как выполнить следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="88589-156">The following sections show how to:</span></span>

* <span data-ttu-id="88589-157">Укажите локальный файл *web.config* , который активирует проверку подлинности Windows на сервере при развертывании приложения.</span><span class="sxs-lookup"><span data-stu-id="88589-157">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="88589-158">Используйте диспетчер служб IIS для настройки файла *web.config* ASP.NET Core приложения, которое уже развернуто на сервере.</span><span class="sxs-lookup"><span data-stu-id="88589-158">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="88589-159">Если вы еще не сделали этого, включите IIS для размещения ASP.NET Core приложений.</span><span class="sxs-lookup"><span data-stu-id="88589-159">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="88589-160">Для получения дополнительной информации см. <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="88589-160">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="88589-161">Включите службу роли IIS для проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="88589-161">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="88589-162">Дополнительные сведения см. [в разделе Включение проверки подлинности Windows в службах РОЛЕЙ IIS (см. шаг 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="88589-162">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="88589-163">По умолчанию по [промежуточного слоя интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) настроено на автоматическую проверку подлинности запросов.</span><span class="sxs-lookup"><span data-stu-id="88589-163">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="88589-164">Дополнительные сведения см. [в разделе Host ASP.NET Core в Windows с IIS: параметры IIS (аутоматикаусентикатион)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="88589-164">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="88589-165">Модуль ASP.NET Core настроен на пересылку маркера проверки подлинности Windows в приложение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="88589-165">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="88589-166">Дополнительные сведения см. в разделе [Справочник по конфигурации модуля ASP.NET Core: атрибуты элемента aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="88589-166">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="88589-167">Используйте **один** из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="88589-167">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="88589-168">**Перед публикацией и развертыванием проекта** добавьте следующий файл *web.config* в корневой каталог проекта:</span><span class="sxs-lookup"><span data-stu-id="88589-168">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="88589-169">Когда проект публикуется пакет SDK для .NET Core (без `<IsTransformWebConfigDisabled>` свойства, установленного `true` в файле проекта), опубликованный файл *web.config* включает `<location><system.webServer><security><authentication>` раздел.</span><span class="sxs-lookup"><span data-stu-id="88589-169">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="88589-170">Дополнительные сведения о `<IsTransformWebConfigDisabled>` свойстве см. в разделе <xref:host-and-deploy/iis/index#webconfig-file> .</span><span class="sxs-lookup"><span data-stu-id="88589-170">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="88589-171">**После публикации и развертывания проекта** выполните настройку на стороне сервера с помощью диспетчера IIS.</span><span class="sxs-lookup"><span data-stu-id="88589-171">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="88589-172">В диспетчере служб IIS выберите сайт IIS в узле **сайты** на боковой панели **подключения** .</span><span class="sxs-lookup"><span data-stu-id="88589-172">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="88589-173">Дважды щелкните **Проверка подлинности** в области **IIS** .</span><span class="sxs-lookup"><span data-stu-id="88589-173">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="88589-174">Выберите **Анонимная проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="88589-174">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="88589-175">На боковой панели **действия** выберите **Отключить** .</span><span class="sxs-lookup"><span data-stu-id="88589-175">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="88589-176">Выберите параметр **Проверка подлинности Windows**.</span><span class="sxs-lookup"><span data-stu-id="88589-176">Select **Windows Authentication**.</span></span> <span data-ttu-id="88589-177">Выберите **включить** на боковой панели **действия** .</span><span class="sxs-lookup"><span data-stu-id="88589-177">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="88589-178">Когда выполняются эти действия, Диспетчер IIS изменяет файл *web.config* приложения.</span><span class="sxs-lookup"><span data-stu-id="88589-178">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="88589-179">`<system.webServer><security><authentication>`Узел добавляется с обновленными параметрами для `anonymousAuthentication` и `windowsAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="88589-179">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="88589-180">`<system.webServer>`Раздел, добавленный в файл *web.config* диспетчером IIS, находится за пределами раздела приложения, `<location>` добавленного пакет SDK для .NET Core при публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="88589-180">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="88589-181">Так как раздел добавляется за пределы `<location>` узла, параметры наследуются любыми [вложенными приложениями](xref:host-and-deploy/iis/index#sub-applications) для текущего приложения.</span><span class="sxs-lookup"><span data-stu-id="88589-181">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="88589-182">Чтобы предотвратить наследование, переместите добавленный `<security>` раздел в `<location><system.webServer>` раздел, указанный пакет SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="88589-182">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="88589-183">Когда диспетчер IIS используется для добавления конфигурации IIS, он влияет только на файл *web.config* приложения на сервере.</span><span class="sxs-lookup"><span data-stu-id="88589-183">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="88589-184">Последующее развертывание приложения может перезаписать параметры на сервере, если копия *web.config* сервера заменяется *web.configным* файлом проекта.</span><span class="sxs-lookup"><span data-stu-id="88589-184">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="88589-185">Для управления параметрами используйте **один** из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="88589-185">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="88589-186">Используйте диспетчер IIS для сброса параметров в файле *web.config* после того, как файл будет перезаписан при развертывании.</span><span class="sxs-lookup"><span data-stu-id="88589-186">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="88589-187">Добавьте *файлweb.config* в приложение локально с параметрами.</span><span class="sxs-lookup"><span data-stu-id="88589-187">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="88589-188">Kestrel</span><span class="sxs-lookup"><span data-stu-id="88589-188">Kestrel</span></span>

<span data-ttu-id="88589-189">Пакет NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) можно использовать с [Kestrel](xref:fundamentals/servers/kestrel) для поддержки проверки подлинности Windows с помощью Negotiate и Kerberos в Windows, Linux и macOS.</span><span class="sxs-lookup"><span data-stu-id="88589-189">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate and Kerberos on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="88589-190">Учетные данные могут быть сохранены в запросах к соединению.</span><span class="sxs-lookup"><span data-stu-id="88589-190">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="88589-191">*Проверка подлинности Negotiate не должна использоваться с прокси, если только прокси-сервер не поддерживает сопоставление соединения 1:1 (постоянное подключение) с Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="88589-191">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="88589-192">Обработчик согласования определяет, поддерживает ли базовый сервер встроенную проверку подлинности Windows и включен ли он.</span><span class="sxs-lookup"><span data-stu-id="88589-192">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="88589-193">Если сервер поддерживает проверку подлинности Windows, но отключен, выдается сообщение об ошибке, предлагающее включить серверную реализацию.</span><span class="sxs-lookup"><span data-stu-id="88589-193">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="88589-194">Если на сервере включена проверка подлинности Windows, обработчик согласования прозрачно пересылает его.</span><span class="sxs-lookup"><span data-stu-id="88589-194">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

<span data-ttu-id="88589-195">Добавьте службы проверки подлинности, вызвав <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> и <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="88589-195">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices`:</span></span>

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="88589-196">Добавьте по промежуточного слоя проверки подлинности, вызвав <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> в `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="88589-196">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();
```

<span data-ttu-id="88589-197">Дополнительные сведения о по промежуточного слоя см. в разделе <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="88589-197">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="88589-198">Анонимные запросы разрешены.</span><span class="sxs-lookup"><span data-stu-id="88589-198">Anonymous requests are allowed.</span></span> <span data-ttu-id="88589-199">Используйте [ASP.NET Coreную авторизацию](xref:security/authorization/introduction) для запроса анонимных запросов на проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="88589-199">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="88589-200">Конфигурация среды Windows</span><span class="sxs-lookup"><span data-stu-id="88589-200">Windows environment configuration</span></span>

<span data-ttu-id="88589-201">Компонент [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) выполняет проверку подлинности пользователя в пользовательском режиме.</span><span class="sxs-lookup"><span data-stu-id="88589-201">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="88589-202">Имена участников-служб (SPN) должны быть добавлены в учетную запись пользователя, запускающего службу, а не в учетную запись компьютера.</span><span class="sxs-lookup"><span data-stu-id="88589-202">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="88589-203">Выполните `setspn -S HTTP/myservername.mydomain.com myuser` команду в административной командной консоли.</span><span class="sxs-lookup"><span data-stu-id="88589-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="88589-204">Конфигурация среды Linux и macOS</span><span class="sxs-lookup"><span data-stu-id="88589-204">Linux and macOS environment configuration</span></span>

<span data-ttu-id="88589-205">Инструкции по присоединению компьютера Linux или macOS к домену Windows доступны в статье [подключение Azure Data Studio к SQL Server с помощью проверки подлинности Windows — Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) .</span><span class="sxs-lookup"><span data-stu-id="88589-205">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="88589-206">Эти инструкции создают учетную запись компьютера для компьютера Linux в домене.</span><span class="sxs-lookup"><span data-stu-id="88589-206">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="88589-207">Имена участников-служб должны быть добавлены в эту учетную запись компьютера.</span><span class="sxs-lookup"><span data-stu-id="88589-207">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="88589-208">При выполнении рекомендаций, приведенных в разделе [подключение Azure Data Studio к SQL Server с помощью проверки подлинности Windows — Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , замените `python-software-properties` на `python3-software-properties` при необходимости.</span><span class="sxs-lookup"><span data-stu-id="88589-208">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="88589-209">После присоединения компьютера Linux или macOS к домену необходимо выполнить дополнительные действия, чтобы предоставить keytab- [файл](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) с именами участников-служб:</span><span class="sxs-lookup"><span data-stu-id="88589-209">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="88589-210">На контроллере домена добавьте новые имена SPN веб-службы в учетную запись компьютера:</span><span class="sxs-lookup"><span data-stu-id="88589-210">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="88589-211">Используйте [ктпасс](/windows-server/administration/windows-commands/ktpass) для создания файла keytab:</span><span class="sxs-lookup"><span data-stu-id="88589-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="88589-212">Некоторые поля должны быть указаны в верхнем регистре, как указано.</span><span class="sxs-lookup"><span data-stu-id="88589-212">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="88589-213">Скопируйте файл keytab на компьютер Linux или macOS.</span><span class="sxs-lookup"><span data-stu-id="88589-213">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="88589-214">Выберите файл keytab с помощью переменной среды:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="88589-214">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="88589-215">Вызовите `klist` , чтобы отобразить имена участников-служб, доступные в настоящее время для использования.</span><span class="sxs-lookup"><span data-stu-id="88589-215">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="88589-216">Файл keytab содержит учетные данные доступа к домену и должен быть защищен соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="88589-216">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="88589-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="88589-217">HTTP.sys</span></span>

<span data-ttu-id="88589-218">[HTTP.sys](xref:fundamentals/servers/httpsys) поддерживает проверку подлинности Windows в режиме ядра с помощью Negotiate, NTLM или обычной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="88589-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="88589-219">Добавьте службы проверки подлинности, вызвав <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> пространство имен) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="88589-219">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="88589-220">Настройте веб-узел приложения для использования HTTP.sys с проверкой подлинности Windows (*Program.CS*).</span><span class="sxs-lookup"><span data-stu-id="88589-220">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="88589-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> находится в пространстве имен <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="88589-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="88589-222">HTTP.sys делегирует задачи в проверку подлинности в режиме ядра с помощью протокола проверки подлинности Kerberos.</span><span class="sxs-lookup"><span data-stu-id="88589-222">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="88589-223">Проверка подлинности в режиме пользователя не поддерживается с Kerberos и HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="88589-223">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="88589-224">Необходимо использовать учетную запись компьютера для расшифровки маркера/билета Kerberos, полученного из Active Directory и переадресованного клиентом на сервер для проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="88589-224">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="88589-225">Зарегистрируйте имя субъекта-службы (SPN) для узла, а не пользователя приложения.</span><span class="sxs-lookup"><span data-stu-id="88589-225">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="88589-226">HTTP.sys не поддерживается на сервере Nano Server версии 1709 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="88589-226">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="88589-227">Чтобы использовать проверку подлинности Windows и HTTP.sys с Nano Server, используйте [контейнер Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="88589-227">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="88589-228">Дополнительные сведения о Server Core см. в разделе [что такое вариант установки Server Core в Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="88589-228">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="88589-229">Авторизация пользователей</span><span class="sxs-lookup"><span data-stu-id="88589-229">Authorize users</span></span>

<span data-ttu-id="88589-230">Состояние конфигурации анонимного доступа определяет способ `[Authorize]` `[AllowAnonymous]` использования атрибутов и в приложении.</span><span class="sxs-lookup"><span data-stu-id="88589-230">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="88589-231">В следующих двух разделах объясняется, как управлять состояниями запрещенных и разрешенных конфигураций анонимного доступа.</span><span class="sxs-lookup"><span data-stu-id="88589-231">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="88589-232">Запретить анонимный доступ</span><span class="sxs-lookup"><span data-stu-id="88589-232">Disallow anonymous access</span></span>

<span data-ttu-id="88589-233">Если включена проверка подлинности Windows, а анонимный доступ отключен, `[Authorize]` `[AllowAnonymous]` атрибуты и не действуют.</span><span class="sxs-lookup"><span data-stu-id="88589-233">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="88589-234">Если сайт IIS настроен для запрета анонимного доступа, запрос никогда не достигнет приложения.</span><span class="sxs-lookup"><span data-stu-id="88589-234">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="88589-235">По этой причине `[AllowAnonymous]` атрибут неприменим.</span><span class="sxs-lookup"><span data-stu-id="88589-235">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="88589-236">Разрешить анонимный доступ</span><span class="sxs-lookup"><span data-stu-id="88589-236">Allow anonymous access</span></span>

<span data-ttu-id="88589-237">Если включена проверка подлинности Windows и анонимный доступ, `[Authorize]` Используйте `[AllowAnonymous]` атрибуты и.</span><span class="sxs-lookup"><span data-stu-id="88589-237">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="88589-238">`[Authorize]`Атрибут позволяет защитить конечные точки приложения, требующие проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="88589-238">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="88589-239">`[AllowAnonymous]`Атрибут переопределяет `[Authorize]` атрибут в приложениях, разрешающих анонимный доступ.</span><span class="sxs-lookup"><span data-stu-id="88589-239">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="88589-240">Сведения об использовании атрибутов см. в разделе <xref:security/authorization/simple> .</span><span class="sxs-lookup"><span data-stu-id="88589-240">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="88589-241">По умолчанию пользователям, у которых отсутствует авторизация на доступ к странице, предоставляется пустой ответ HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="88589-241">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="88589-242">По [промежуточного слоя статускодепажес](xref:fundamentals/error-handling#usestatuscodepages) можно настроить для предоставления пользователям более качественного интерфейса "отказ в доступе".</span><span class="sxs-lookup"><span data-stu-id="88589-242">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="88589-243">Олицетворение</span><span class="sxs-lookup"><span data-stu-id="88589-243">Impersonation</span></span>

<span data-ttu-id="88589-244">ASP.NET Core не реализует олицетворение.</span><span class="sxs-lookup"><span data-stu-id="88589-244">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="88589-245">Приложения запускаются с удостоверением приложения для всех запросов с использованием пула приложений или удостоверения процесса.</span><span class="sxs-lookup"><span data-stu-id="88589-245">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="88589-246">Если приложение должно выполнить действие от имени пользователя, используйте [WindowsIdentity. рунимперсонатед](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) во встроенном по [промежуточного слоя терминала](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="88589-246">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="88589-247">Выполните одно действие в этом контексте и закройте контекст.</span><span class="sxs-lookup"><span data-stu-id="88589-247">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="88589-248">`RunImpersonated`не поддерживает асинхронные операции и не следует использовать для сложных сценариев.</span><span class="sxs-lookup"><span data-stu-id="88589-248">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="88589-249">Например, не поддерживается или не рекомендуется заключать в оболочку все запросы или цепочки промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="88589-249">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88589-250">Хотя пакет [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) включает проверку подлинности в Windows, Linux и macOS, олицетворение поддерживается только в Windows.</span><span class="sxs-lookup"><span data-stu-id="88589-250">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="88589-251">Преобразования утверждений</span><span class="sxs-lookup"><span data-stu-id="88589-251">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88589-252">При размещении с IIS <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутренне для инициализации пользователя.</span><span class="sxs-lookup"><span data-stu-id="88589-252">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="88589-253">Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="88589-253">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="88589-254">Дополнительные сведения и пример кода, который активирует преобразования утверждений, см. в разделе <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="88589-254">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="88589-255">При размещении в режиме обработки IIS <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутренним образом для инициализации пользователя.</span><span class="sxs-lookup"><span data-stu-id="88589-255">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="88589-256">Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="88589-256">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="88589-257">Дополнительные сведения и пример кода, который активирует преобразования утверждений при размещении внутри процесса, см. в разделе <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="88589-257">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="88589-258">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="88589-258">Additional resources</span></span>

* [<span data-ttu-id="88589-259">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="88589-259">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
