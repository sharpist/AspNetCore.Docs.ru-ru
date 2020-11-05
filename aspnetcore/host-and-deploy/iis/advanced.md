---
title: Расширенная конфигурация
author: rick-anderson
description: Расширенная настройка с помощью модуля ASP.NET Core и служб IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058620"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="c388b-103">Расширенная настройка модуля ASP.NET Core и служб IIS</span><span class="sxs-lookup"><span data-stu-id="c388b-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="c388b-104">В этой статье рассматриваются параметры и сценарии расширенной настройки модуля ASP.NET Core и служб IIS.</span><span class="sxs-lookup"><span data-stu-id="c388b-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="c388b-105">Изменение размера стека</span><span class="sxs-lookup"><span data-stu-id="c388b-105">Modify the stack size</span></span>

<span data-ttu-id="c388b-106">*Применяется только при использовании модели внутрипроцессного размещения.*</span><span class="sxs-lookup"><span data-stu-id="c388b-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="c388b-107">Настройте размер управляемого стека, задав для параметра `stackSize` значение в байтах в файле `web.config`.</span><span class="sxs-lookup"><span data-stu-id="c388b-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="c388b-108">Размер по умолчанию — 1 048 576 байт (1 МБ).</span><span class="sxs-lookup"><span data-stu-id="c388b-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="c388b-109">В следующем примере размер стека изменяется на 2 МБ (2 097 152 байт).</span><span class="sxs-lookup"><span data-stu-id="c388b-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="c388b-110">Конфигурация прокси-сервера использует протокол HTTP и токен связывания</span><span class="sxs-lookup"><span data-stu-id="c388b-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="c388b-111">*Применяется только к размещению вне процесса.*</span><span class="sxs-lookup"><span data-stu-id="c388b-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="c388b-112">Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP.</span><span class="sxs-lookup"><span data-stu-id="c388b-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="c388b-113">Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="c388b-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="c388b-114">Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника.</span><span class="sxs-lookup"><span data-stu-id="c388b-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="c388b-115">Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="c388b-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="c388b-116">Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="c388b-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="c388b-117">ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды.</span><span class="sxs-lookup"><span data-stu-id="c388b-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="c388b-118">Если значения токена не совпадают, запрос заносится в журнал и отклоняется.</span><span class="sxs-lookup"><span data-stu-id="c388b-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="c388b-119">Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="c388b-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="c388b-120">Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.</span><span class="sxs-lookup"><span data-stu-id="c388b-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="c388b-121">Модуль ASP.NET Core с общей конфигурацией IIS</span><span class="sxs-lookup"><span data-stu-id="c388b-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="c388b-122">Программа установки модуля ASP.NET Core запускается с правами учетной записи `TrustedInstaller`.</span><span class="sxs-lookup"><span data-stu-id="c388b-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="c388b-123">Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле `applicationHost.config` общей папки.</span><span class="sxs-lookup"><span data-stu-id="c388b-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="c388b-124">При использовании общей конфигурации IIS на том же компьютере, где установлены службы IIS, запустите установщик пакета размещения ASP.NET Core с параметром `OPT_NO_SHARED_CONFIG_CHECK` со значением `1`:</span><span class="sxs-lookup"><span data-stu-id="c388b-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="c388b-125">Если путь к общей конфигурации не на том же компьютере, где установлены службы IIS, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="c388b-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="c388b-126">Отключите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="c388b-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="c388b-127">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="c388b-127">Run the installer.</span></span>
1. <span data-ttu-id="c388b-128">Экспортируйте обновленный файл `applicationHost.config` в общую папку.</span><span class="sxs-lookup"><span data-stu-id="c388b-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="c388b-129">Повторно включите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="c388b-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="c388b-130">Защита данных</span><span class="sxs-lookup"><span data-stu-id="c388b-130">Data protection</span></span>

<span data-ttu-id="c388b-131">[Стек защиты данных ASP.NET Core](xref:security/data-protection/introduction) используют несколько [ПО промежуточного слоя](xref:fundamentals/middleware/index) ASP.NET Core, включая ПО, которое применяется для аутентификации.</span><span class="sxs-lookup"><span data-stu-id="c388b-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="c388b-132">Даже если API-интерфейсы защиты данных не вызываются из пользовательского кода, защиту данных следует настроить для создания постоянного [хранилища криптографических ключей](xref:security/data-protection/implementation/key-management). Это можно сделать с помощью скрипта развертывания или в пользовательском коде.</span><span class="sxs-lookup"><span data-stu-id="c388b-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="c388b-133">Если защита данных не настроена, ключи хранятся в памяти и удаляются при перезапуске приложения.</span><span class="sxs-lookup"><span data-stu-id="c388b-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="c388b-134">Если набор ключей для защиты данных хранится в памяти, при перезапуске приложения происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="c388b-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="c388b-135">Все токены аутентификации, использующие файлы :::no-loc(cookie):::, становятся недействительными.</span><span class="sxs-lookup"><span data-stu-id="c388b-135">All :::no-loc(cookie):::-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="c388b-136">При выполнении следующего запроса пользователю требуется выполнить вход снова.</span><span class="sxs-lookup"><span data-stu-id="c388b-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="c388b-137">Все данные, защищенные с помощью набора ключей, больше не могут быть расшифрованы.</span><span class="sxs-lookup"><span data-stu-id="c388b-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="c388b-138">Это могут быть [токены CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) и [файлы :::no-loc(cookie)::: временных данных ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="c388b-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData :::no-loc(cookie):::s](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="c388b-139">Чтобы настроить защиту данных в службах IIS для хранения набора ключей, воспользуйтесь **одним** из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="c388b-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="c388b-140">**Создание разделов реестра для защиты данных**</span><span class="sxs-lookup"><span data-stu-id="c388b-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="c388b-141">Ключи защиты данных, используемые приложениями ASP.NET Core, хранятся во внешнем для приложений реестре.</span><span class="sxs-lookup"><span data-stu-id="c388b-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="c388b-142">Чтобы хранить эти ключи для определенного приложения, нужно создать разделы реестра для пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c388b-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="c388b-143">При автономной установке служб IIS, не поддерживающей веб-ферму, можно выполнить [скрипт PowerShell Provision-AutoGenKeys.ps1 для защиты данных](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) применительно к каждому пулу приложений, в который входит приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c388b-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="c388b-144">Этот скрипт создает раздел в реестре HKLM, который будет доступен только для учетной записи рабочего процесса пула приложений, к которому относится соответствующее приложение.</span><span class="sxs-lookup"><span data-stu-id="c388b-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="c388b-145">Неактивные ключи шифруются с помощью API защиты данных с ключом компьютера.</span><span class="sxs-lookup"><span data-stu-id="c388b-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="c388b-146">В случаях, когда используется веб-ферма, в приложении можно настроить UNC-путь, по которому это приложение будет хранить набор ключей для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="c388b-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="c388b-147">По умолчанию ключи не шифруются.</span><span class="sxs-lookup"><span data-stu-id="c388b-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="c388b-148">Разрешения на доступ к файлам в сетевой папке должны быть предоставлены только учетной записи Windows, с помощью которой выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="c388b-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="c388b-149">Для защиты неактивных ключей можно использовать сертификат X509.</span><span class="sxs-lookup"><span data-stu-id="c388b-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="c388b-150">Рассмотрите возможность реализации механизма, позволяющего пользователям отправлять сертификаты.</span><span class="sxs-lookup"><span data-stu-id="c388b-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="c388b-151">поместите сертификаты в хранилище доверенных сертификатов пользователя и обеспечьте их доступность на всех компьютерах, где выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="c388b-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="c388b-152">Дополнительные сведения см. в разделе <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="c388b-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="c388b-153">**Настройка пула приложений IIS для загрузки профиля пользователя**.</span><span class="sxs-lookup"><span data-stu-id="c388b-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="c388b-154">Этот параметр находится на странице **Дополнительные параметры** пула приложений в разделе **Модель процесса**.</span><span class="sxs-lookup"><span data-stu-id="c388b-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="c388b-155">Задайте для параметра **Загрузить профиль пользователя** значение `True`.</span><span class="sxs-lookup"><span data-stu-id="c388b-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="c388b-156">Если задать значение `True`, ключи будут храниться в каталоге профиля пользователя и защищаться с помощью API защиты данных и ключа на уровне учетной записи пользователя.</span><span class="sxs-lookup"><span data-stu-id="c388b-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="c388b-157">Ключи хранятся в папке `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys`.</span><span class="sxs-lookup"><span data-stu-id="c388b-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="c388b-158">Также необходимо включить [атрибут `setProfileEnvironment`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c388b-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="c388b-159">Значением свойства `setProfileEnvironment` по умолчанию является `true`.</span><span class="sxs-lookup"><span data-stu-id="c388b-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="c388b-160">В некоторых сценариях (например, в ОС Windows) для параметра `setProfileEnvironment` установлено значение `false`.</span><span class="sxs-lookup"><span data-stu-id="c388b-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="c388b-161">Если ключи не хранятся в каталоге профиля пользователя:</span><span class="sxs-lookup"><span data-stu-id="c388b-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="c388b-162">Перейдите в папку `%windir%/system32/inetsrv/config`.</span><span class="sxs-lookup"><span data-stu-id="c388b-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="c388b-163">Откройте файл `applicationHost.config` .</span><span class="sxs-lookup"><span data-stu-id="c388b-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="c388b-164">Найдите элемент `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .</span><span class="sxs-lookup"><span data-stu-id="c388b-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="c388b-165">Убедитесь, что атрибут `setProfileEnvironment` отсутствует и установлено значение по умолчанию `true`, или же явно задайте для атрибута значение `true`.</span><span class="sxs-lookup"><span data-stu-id="c388b-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="c388b-166">**Использование файловой системы в качестве хранилища набора ключей**.</span><span class="sxs-lookup"><span data-stu-id="c388b-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="c388b-167">Измените код приложения так, чтобы [в качестве хранилища набора ключей использовалась файловая система](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="c388b-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="c388b-168">Для защиты набора ключей используйте доверенный сертификат X509.</span><span class="sxs-lookup"><span data-stu-id="c388b-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="c388b-169">Если сертификат — самозаверяющий, поместите его в доверенное корневое хранилище.</span><span class="sxs-lookup"><span data-stu-id="c388b-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="c388b-170">При использовании служб IIS в веб-ферме:</span><span class="sxs-lookup"><span data-stu-id="c388b-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="c388b-171">используйте общую папку, которая доступна всем компьютерам;</span><span class="sxs-lookup"><span data-stu-id="c388b-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="c388b-172">разверните сертификат X509 на каждом компьютере;</span><span class="sxs-lookup"><span data-stu-id="c388b-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="c388b-173">настройте [защиту данных в коде](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="c388b-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="c388b-174">**Настройка политики защиты данных на уровне компьютера**</span><span class="sxs-lookup"><span data-stu-id="c388b-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="c388b-175">Система защиты данных обеспечивает ограниченную поддержку задания [политики по умолчанию на уровне компьютера](xref:security/data-protection/configuration/machine-wide-policy) для всех приложений, использующих интерфейсы API защиты данных.</span><span class="sxs-lookup"><span data-stu-id="c388b-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="c388b-176">Дополнительные сведения см. в разделе <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="c388b-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="c388b-177">Конфигурация IIS</span><span class="sxs-lookup"><span data-stu-id="c388b-177">IIS configuration</span></span>

<span data-ttu-id="c388b-178">**Операционные системы семейства Windows Server**</span><span class="sxs-lookup"><span data-stu-id="c388b-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="c388b-179">Включите роль сервера **Веб-сервер (IIS)** и настройте службы роли.</span><span class="sxs-lookup"><span data-stu-id="c388b-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="c388b-180">В меню **Управление** запустите мастер **Добавить роли и компоненты** или в окне **Диспетчер серверов** щелкните соответствующую ссылку.</span><span class="sxs-lookup"><span data-stu-id="c388b-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="c388b-181">На этапе **Роли сервера** установите флажок **Веб-сервер (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="c388b-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Роль "Веб-сервер (IIS)" выбрана на странице "Выбор ролей сервера".](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="c388b-183">После этапа выбора **компонентов** запускается этап выбора **служб роли** для веб-сервера (IIS).</span><span class="sxs-lookup"><span data-stu-id="c388b-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="c388b-184">Выберите нужные службы роли IIS или оставьте службы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c388b-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Службы роли по умолчанию, выбранные на странице "Выбор служб ролей".](index/_static/role-services-ws2016.png)

   <span data-ttu-id="c388b-186">**Проверка подлинности Windows (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c388b-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c388b-187">Чтобы включить проверку подлинности Windows, разверните такие узлы: **Веб-сервер** > **Безопасность**.</span><span class="sxs-lookup"><span data-stu-id="c388b-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="c388b-188">Выберите компонент **Проверка подлинности Windows**.</span><span class="sxs-lookup"><span data-stu-id="c388b-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c388b-189">Дополнительные сведения см. в статьях [Проверка подлинности Windows `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) и [Настройка проверки подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c388b-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c388b-190">**Протокол WebSocket (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c388b-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c388b-191">Протокол WebSocket поддерживается в ASP.NET Core начиная с версии 1.1.</span><span class="sxs-lookup"><span data-stu-id="c388b-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c388b-192">Чтобы включить протокол WebSocket, разверните такие узлы: **Веб-сервер** > **Разработка приложений**.</span><span class="sxs-lookup"><span data-stu-id="c388b-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="c388b-193">Выберите компонент **Протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="c388b-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c388b-194">Дополнительные сведения см. в разделе [Протокол WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="c388b-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c388b-195">Пройдите этап **Подтверждение** , чтобы установить роль и службы веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="c388b-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="c388b-196">После установки роли **Веб-сервер (IIS)** перезагружать сервер или службы IIS не требуется.</span><span class="sxs-lookup"><span data-stu-id="c388b-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="c388b-197">**Операционные системы Windows для настольных компьютеров**</span><span class="sxs-lookup"><span data-stu-id="c388b-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="c388b-198">Включите компоненты **Консоль управления IIS** и **Службы Интернета**.</span><span class="sxs-lookup"><span data-stu-id="c388b-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c388b-199">Последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).</span><span class="sxs-lookup"><span data-stu-id="c388b-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="c388b-200">Разверните узел **Службы IIS**.</span><span class="sxs-lookup"><span data-stu-id="c388b-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="c388b-201">Разверните узел **Средства управления веб-сайтом**.</span><span class="sxs-lookup"><span data-stu-id="c388b-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="c388b-202">Установите флажок **Консоль управления IIS**.</span><span class="sxs-lookup"><span data-stu-id="c388b-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="c388b-203">Установите флажок **Службы Интернета**.</span><span class="sxs-lookup"><span data-stu-id="c388b-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c388b-204">В группе **Службы Интернета** оставьте компоненты по умолчанию или настройте компоненты IIS.</span><span class="sxs-lookup"><span data-stu-id="c388b-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="c388b-205">**Проверка подлинности Windows (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c388b-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c388b-206">Чтобы включить проверку подлинности Windows, разверните такие узлы: **Службы Интернета** > **Безопасность**.</span><span class="sxs-lookup"><span data-stu-id="c388b-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="c388b-207">Выберите компонент **Проверка подлинности Windows**.</span><span class="sxs-lookup"><span data-stu-id="c388b-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c388b-208">Дополнительные сведения см. в статьях [Проверка подлинности Windows `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) и [Настройка проверки подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c388b-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c388b-209">**Протокол WebSocket (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c388b-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c388b-210">Протокол WebSocket поддерживается в ASP.NET Core начиная с версии 1.1.</span><span class="sxs-lookup"><span data-stu-id="c388b-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c388b-211">Чтобы включить протокол WebSocket, разверните такие узлы: **Службы Интернета** > **Компоненты разработки приложений**.</span><span class="sxs-lookup"><span data-stu-id="c388b-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="c388b-212">Выберите компонент **Протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="c388b-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c388b-213">Дополнительные сведения см. в разделе [Протокол WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="c388b-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c388b-214">Если во время установки IIS потребуется перезагрузка, перезагрузите систему.</span><span class="sxs-lookup"><span data-stu-id="c388b-214">If the IIS installation requires a restart, restart the system.</span></span>

![Группы "Консоль управления IIS" и "Службы Интернета" выбраны в окне "Компоненты Windows".](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="c388b-216">Виртуальные каталоги</span><span class="sxs-lookup"><span data-stu-id="c388b-216">Virtual Directories</span></span>

<span data-ttu-id="c388b-217">[Виртуальные каталоги IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) не поддерживаются в приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c388b-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="c388b-218">Приложение может размещаться как [вложенное](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="c388b-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="c388b-219">Вложенные приложения</span><span class="sxs-lookup"><span data-stu-id="c388b-219">Sub-applications</span></span>

<span data-ttu-id="c388b-220">Приложение ASP.NET Core можно разместить как [вложенное приложение IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="c388b-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="c388b-221">Путь к вложенному приложению становится частью URL-адреса главного приложения.</span><span class="sxs-lookup"><span data-stu-id="c388b-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="c388b-222">В ссылках на статический ресурс во вложенном приложении следует использовать нотацию `~/`.</span><span class="sxs-lookup"><span data-stu-id="c388b-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="c388b-223">Такая нотация активирует [вспомогательную функцию тега](xref:mvc/views/tag-helpers/intro) для добавления свойства pathbase вложенного приложения в начало отображаемой относительной ссылки.</span><span class="sxs-lookup"><span data-stu-id="c388b-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="c388b-224">Для вложенного приложения с путем `/subapp_path` ссылка на изображение `src="~/image.png"` отображается как `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="c388b-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="c388b-225">ПО промежуточного слоя для обработки статических файлов в главном приложении не обрабатывает такой запрос статического файла.</span><span class="sxs-lookup"><span data-stu-id="c388b-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="c388b-226">Запрос обрабатывается соответствующим ПО вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c388b-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="c388b-227">Если атрибуту `src` статического ресурса присваивается абсолютный путь (например, `src="/image.png"`), ссылка отображается без свойства pathbase вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c388b-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="c388b-228">ПО промежуточного слоя для обработки статических файлов в главном приложении пытается найти ресурс в [корневом веб-каталоге](xref:fundamentals/index#web-root) главного приложения, что приводит к ошибке *404 — Not Found* (не найдено), кроме случаев, когда статический ресурс доступен из главного приложения.</span><span class="sxs-lookup"><span data-stu-id="c388b-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="c388b-229">Для размещения приложения ASP.NET Core в качестве приложения, вложенного в другое приложение ASP.NET Core, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="c388b-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="c388b-230">Создайте пул приложений для вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c388b-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="c388b-231">Установите для параметра **Версия среды CLR .NET** значение **Без управляемого кода** , так как для размещения приложения в рабочем процессе запускается CoreCLR для .NET Core, а не среда CRL для настольных ПК (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="c388b-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="c388b-232">Добавьте корневой веб-сайт в диспетчере служб IIS с вложенным приложением в папке внутри корневого веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="c388b-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="c388b-233">Щелкните правой кнопкой мыши папку вложенного приложения в диспетчере служб IIS и выберите **Преобразовать в приложение**.</span><span class="sxs-lookup"><span data-stu-id="c388b-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="c388b-234">В диалоговом окне **Добавление приложения** нажмите кнопку **Выбрать** , чтобы назначить созданный **пул приложений** вложенному приложению.</span><span class="sxs-lookup"><span data-stu-id="c388b-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="c388b-235">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c388b-235">Select **OK**.</span></span>

<span data-ttu-id="c388b-236">Назначение отдельного пула приложений вложенному приложению является обязательным при использовании модели внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="c388b-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="c388b-237">Дополнительные сведения о внутрипроцессной модели размещения и настройке модуля ASP.NET Core см. в статье <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c388b-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="c388b-238">Пулы приложений</span><span class="sxs-lookup"><span data-stu-id="c388b-238">Application Pools</span></span>

<span data-ttu-id="c388b-239">Модель размещения определяет изоляцию пула приложений:</span><span class="sxs-lookup"><span data-stu-id="c388b-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="c388b-240">Внутрипроцессное размещение: Приложения должны выполняться в отдельных пулах.</span><span class="sxs-lookup"><span data-stu-id="c388b-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="c388b-241">Размещение вне процесса: Рекомендуем изолировать приложения друг от друга, запуская каждое из них в собственном пуле.</span><span class="sxs-lookup"><span data-stu-id="c388b-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="c388b-242">В диалоговом окне **Добавление веб-сайта** IIS на каждое приложение по умолчанию задан один пул приложений.</span><span class="sxs-lookup"><span data-stu-id="c388b-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="c388b-243">Если указано **Имя сайта** , оно автоматически переносится в текстовое поле **Пул приложений**.</span><span class="sxs-lookup"><span data-stu-id="c388b-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="c388b-244">При добавлении веб-сайта создается пул приложений с именем сайта.</span><span class="sxs-lookup"><span data-stu-id="c388b-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="c388b-245">:::no-loc(Identity)::: пула приложений</span><span class="sxs-lookup"><span data-stu-id="c388b-245">Application Pool :::no-loc(Identity):::</span></span>

<span data-ttu-id="c388b-246">Учетная запись удостоверения пула приложений позволяет запускать приложение от имени уникальной учетной записи, не создавая домены или локальные учетные записи и не управляя ими.</span><span class="sxs-lookup"><span data-stu-id="c388b-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="c388b-247">В службах IIS начиная с версии 8.0 рабочий процесс администратора IIS (WAS) создает виртуальную учетную запись с именем нового пула приложений и по умолчанию выполняет рабочие процессы пула приложений с помощью этой учетной записи.</span><span class="sxs-lookup"><span data-stu-id="c388b-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="c388b-248">В консоли управления IIS в окне **Дополнительные параметры** пула приложений для **:::no-loc(Identity):::** необходимо выбрать `ApplicationPool:::no-loc(Identity):::`.</span><span class="sxs-lookup"><span data-stu-id="c388b-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **:::no-loc(Identity):::** is set to use `ApplicationPool:::no-loc(Identity):::`:</span></span>

![Диалоговое окно дополнительных параметров для пула приложений](index/_static/apppool-identity.png)

<span data-ttu-id="c388b-250">Процесс управления IIS создает в системе безопасности Windows безопасный идентификатор с именем пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c388b-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="c388b-251">С помощью этого идентификатора можно защищать ресурсы,</span><span class="sxs-lookup"><span data-stu-id="c388b-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="c388b-252">но он не является реальной учетной записью и не отображается в консоли управления пользователями Windows.</span><span class="sxs-lookup"><span data-stu-id="c388b-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="c388b-253">Если рабочему процессу IIS требуется предоставить доступ к приложению с повышенными правами, измените список управления доступом (ACL) для каталога, содержащего приложение.</span><span class="sxs-lookup"><span data-stu-id="c388b-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="c388b-254">Откройте проводник и перейдите к каталогу.</span><span class="sxs-lookup"><span data-stu-id="c388b-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="c388b-255">Щелкните каталог правой кнопкой мыши и выберите пункт **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="c388b-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="c388b-256">На вкладке **Безопасность** нажмите кнопку **Изменить** , а затем — кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c388b-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="c388b-257">Нажмите кнопку **Размещение** и выберите систему.</span><span class="sxs-lookup"><span data-stu-id="c388b-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="c388b-258">В области **Введите имена объектов для выбора** введите значение в формате `IIS AppPool\{APP POOL NAME}`, где заполнитель `{APP POOL NAME}` представляет собой имя пула приложения.</span><span class="sxs-lookup"><span data-stu-id="c388b-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="c388b-259">Нажмите кнопку **Проверить имена**.</span><span class="sxs-lookup"><span data-stu-id="c388b-259">Select the **Check Names** button.</span></span> <span data-ttu-id="c388b-260">В случае с *DefaultAppPool* проверьте имена с помощью `IIS AppPool\DefaultAppPool`.</span><span class="sxs-lookup"><span data-stu-id="c388b-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="c388b-261">После нажатия кнопки **Проверить имена** в области имен объектов отобразится значение `DefaultAppPool`.</span><span class="sxs-lookup"><span data-stu-id="c388b-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="c388b-262">Вручную ввести имя пула приложений в области имен объектов нельзя.</span><span class="sxs-lookup"><span data-stu-id="c388b-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="c388b-263">При проверке имени объекта используйте формат `IIS AppPool\{APP POOL NAME}`, где заполнитель `{APP POOL NAME}` представляет собой имя пула приложения.</span><span class="sxs-lookup"><span data-stu-id="c388b-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![Диалоговое окно "Выбор пользователей или групп" для папки приложения. До нажатия кнопки "Проверить имена" в области имен объектов к строке IIS AppPool\" добавилось имя пула приложений, DefaultAppPool.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="c388b-265">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c388b-265">Select **OK**.</span></span>

   ![Диалоговое окно "Выбор пользователей или групп" для папки приложения. После нажатия кнопки "Проверить имена" в области имен объектов отображается имя пула приложений, DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="c388b-267">Разрешения на чтение и выполнение предоставляются по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c388b-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="c388b-268">При необходимости предоставьте дополнительные разрешения.</span><span class="sxs-lookup"><span data-stu-id="c388b-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="c388b-269">Кроме того, доступ можно предоставить через командную строку, используя средство **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="c388b-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="c388b-270">В случае с *DefaultAppPool* выполняется такая команда:</span><span class="sxs-lookup"><span data-stu-id="c388b-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="c388b-271">Дополнительные сведения об ICACLS см. [здесь](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="c388b-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="c388b-272">Поддержка HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c388b-272">HTTP/2 support</span></span>

<span data-ttu-id="c388b-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) поддерживается в ASP.NET Core для следующих сценариев развертывания IIS:</span><span class="sxs-lookup"><span data-stu-id="c388b-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="c388b-274">Внутрипроцессно</span><span class="sxs-lookup"><span data-stu-id="c388b-274">In-process</span></span>
  * <span data-ttu-id="c388b-275">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c388b-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c388b-276">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="c388b-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="c388b-277">Внепроцессно</span><span class="sxs-lookup"><span data-stu-id="c388b-277">Out-of-process</span></span>
  * <span data-ttu-id="c388b-278">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c388b-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c388b-279">Подключения к пограничным серверам, открытых для общего доступа, выполняются по протоколу HTTP/2, а подключения к [серверу Kestrel](xref:fundamentals/servers/kestrel) через обратный прокси-сервер — по HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c388b-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="c388b-280">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="c388b-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c388b-281">При внутрипроцессном развертывании и установленном подключении HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="c388b-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="c388b-282">При внепроцессном развертывании и установленном подключении HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="c388b-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="c388b-283">Дополнительные сведения о внутрипроцессной и внепроцессной моделях размещения см. в статье <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c388b-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c388b-284">Протокол HTTP/2 включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c388b-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c388b-285">Если не удается установить подключение HTTP/2, применяется резервный вариант HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c388b-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="c388b-286">Дополнительные сведения о настройке HTTP/2 для развертываний IIS см. в статье [об HTTP/2 в IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="c388b-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="c388b-287">Предварительные запросы CORS</span><span class="sxs-lookup"><span data-stu-id="c388b-287">CORS preflight requests</span></span>

<span data-ttu-id="c388b-288">*Этот раздел относится только к приложениям ASP.NET Core, предназначенным для .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="c388b-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="c388b-289">Для приложения ASP.NET Core, предназначенного для .NET Framework, параметры OPTIONS не передаются в приложение по умолчанию в службах IIS.</span><span class="sxs-lookup"><span data-stu-id="c388b-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="c388b-290">Чтобы узнать, как настроить обработчики приложения IIS в файле `web.config` для передачи запросов OPTIONS, см. раздел [Enable cross-origin requests in ASP.NET Web API 2. How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works) (Включение запросов CORS в ASP.NET Web API 2. Принцип работы CORS).</span><span class="sxs-lookup"><span data-stu-id="c388b-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="c388b-291">Модуль инициализации приложений и время ожидания в режиме простоя</span><span class="sxs-lookup"><span data-stu-id="c388b-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="c388b-292">Размещение в IIS с помощью модуля ASP.NET Core версии 2:</span><span class="sxs-lookup"><span data-stu-id="c388b-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="c388b-293">[Модуль инициализации приложений](#application-initialization-module). Приложение, размещенное [в процессе](xref:host-and-deploy/iis/in-process-hosting) или [вне процесса](xref:host-and-deploy/iis/out-of-process-hosting), можно настроить на автоматический запуск при перезапуске рабочего процесса или сервера.</span><span class="sxs-lookup"><span data-stu-id="c388b-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="c388b-294">[Время ожидания в режиме простоя](#idle-timeout). Приложение, размещенное [в процессе](xref:host-and-deploy/iis/in-process-hosting), можно настроить на игнорирование времени ожидания в периоды неактивности.</span><span class="sxs-lookup"><span data-stu-id="c388b-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="c388b-295">Модуль инициализации приложений</span><span class="sxs-lookup"><span data-stu-id="c388b-295">Application Initialization Module</span></span>

<span data-ttu-id="c388b-296">*Применяется к приложениям, размещенным в процессе и вне процесса.*</span><span class="sxs-lookup"><span data-stu-id="c388b-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="c388b-297">Функция [инициализации приложений](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) в IIS отправляет в приложение HTTP-запрос при запуске или перезапуске пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c388b-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="c388b-298">Этот запрос инициирует запуск приложения.</span><span class="sxs-lookup"><span data-stu-id="c388b-298">The request triggers the app to start.</span></span> <span data-ttu-id="c388b-299">По умолчанию IIS отправляет запрос к корневому URL-адресу приложения (`/`) для его инициализации (подробные сведения о конфигурации см. в [дополнительных ресурсах](#application-initialization-module-and-idle-timeout-additional-resources)).</span><span class="sxs-lookup"><span data-stu-id="c388b-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="c388b-300">Убедитесь, что включена роль инициализации приложения IIS.</span><span class="sxs-lookup"><span data-stu-id="c388b-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="c388b-301">На настольных компьютерах с Windows 7 или более поздней версии при локальном использовании IIS:</span><span class="sxs-lookup"><span data-stu-id="c388b-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="c388b-302">Последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).</span><span class="sxs-lookup"><span data-stu-id="c388b-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="c388b-303">Откройте **Службы IIS** > **Службы Интернета** > **Компоненты разработки приложений**.</span><span class="sxs-lookup"><span data-stu-id="c388b-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="c388b-304">Установите флажок **Инициализация приложений**.</span><span class="sxs-lookup"><span data-stu-id="c388b-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="c388b-305">В Windows Server 2008 R2 и более поздней версии:</span><span class="sxs-lookup"><span data-stu-id="c388b-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="c388b-306">Откройте **Мастер добавления ролей и компонентов**.</span><span class="sxs-lookup"><span data-stu-id="c388b-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="c388b-307">На панели **Выбор служб ролей** разверните узел **Разработка приложений**.</span><span class="sxs-lookup"><span data-stu-id="c388b-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="c388b-308">Установите флажок **Инициализация приложений**.</span><span class="sxs-lookup"><span data-stu-id="c388b-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="c388b-309">Чтобы включить модуль инициализации приложений для сайта, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="c388b-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="c388b-310">При использовании диспетчера IIS:</span><span class="sxs-lookup"><span data-stu-id="c388b-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="c388b-311">Выберите **Пулы приложений** на панели **Подключения**.</span><span class="sxs-lookup"><span data-stu-id="c388b-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="c388b-312">Щелкните пул приложений в списке правой кнопкой мыши и выберите **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c388b-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="c388b-313">Для **режима запуска** по умолчанию задано значение `OnDemand`.</span><span class="sxs-lookup"><span data-stu-id="c388b-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="c388b-314">Выберите для параметра **Режим запуска** значение `AlwaysRunning`.</span><span class="sxs-lookup"><span data-stu-id="c388b-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="c388b-315">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c388b-315">Select **OK**.</span></span>
  1. <span data-ttu-id="c388b-316">Откройте узел **Сайты** на панели **Подключения**.</span><span class="sxs-lookup"><span data-stu-id="c388b-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="c388b-317">Щелкните приложение правой кнопкой мыши и выберите **Управление веб-сайтом** > **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c388b-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="c388b-318">По умолчанию для параметра **Предварительная загрузка включена** установлено значение `False`.</span><span class="sxs-lookup"><span data-stu-id="c388b-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="c388b-319">Для параметра **Предварительная загрузка включена** выберите значение `True`.</span><span class="sxs-lookup"><span data-stu-id="c388b-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="c388b-320">Щелкните **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c388b-320">Select **OK**.</span></span>

* <span data-ttu-id="c388b-321">Откройте `web.config` и добавьте элемент `<applicationInitialization>` с параметром `doAppInitAfterRestart`, для которого установлено значение `true`, к элементам `<system.webServer>` в файле `web.config` приложения.</span><span class="sxs-lookup"><span data-stu-id="c388b-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="c388b-322">Время ожидания в режиме простоя</span><span class="sxs-lookup"><span data-stu-id="c388b-322">Idle Timeout</span></span>

<span data-ttu-id="c388b-323">*Применяется только к приложениям, размещенным в процессе.*</span><span class="sxs-lookup"><span data-stu-id="c388b-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="c388b-324">Чтобы предотвратить переход приложения из состояния простоя, задайте для пула приложений время ожидания в режиме простоя с помощью диспетчера IIS:</span><span class="sxs-lookup"><span data-stu-id="c388b-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="c388b-325">Выберите **Пулы приложений** на панели **Подключения**.</span><span class="sxs-lookup"><span data-stu-id="c388b-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="c388b-326">Щелкните пул приложений в списке правой кнопкой мыши и выберите **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c388b-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="c388b-327">По умолчанию для параметра **Тайм-аут простоя (в минутах)** установлено значение `20` минут.</span><span class="sxs-lookup"><span data-stu-id="c388b-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="c388b-328">Задайте для параметра **Тайм-аут простоя (в минутах)** значение `0`.</span><span class="sxs-lookup"><span data-stu-id="c388b-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="c388b-329">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c388b-329">Select **OK**.</span></span>
1. <span data-ttu-id="c388b-330">Перезапустите рабочий процесс.</span><span class="sxs-lookup"><span data-stu-id="c388b-330">Recycle the worker process.</span></span>

<span data-ttu-id="c388b-331">Чтобы не истекло время ожидания в приложениях, размещенных [вне процесса](xref:host-and-deploy/iis/out-of-process-hosting), воспользуйтесь одним из таких методов:</span><span class="sxs-lookup"><span data-stu-id="c388b-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="c388b-332">Проверьте связь с приложением из внешней службы, чтобы оно продолжило работу.</span><span class="sxs-lookup"><span data-stu-id="c388b-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="c388b-333">Если приложение размещает только фоновые службы, избегайте размещения в службах IIS и воспользуйтесь [службой Windows для размещения приложения ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="c388b-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="c388b-334">Дополнительные ресурсы по модулю инициализации приложений и времени ожидания в режиме простоя</span><span class="sxs-lookup"><span data-stu-id="c388b-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="c388b-335">Инициализация приложений в IIS 8.0</span><span class="sxs-lookup"><span data-stu-id="c388b-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="c388b-336">[Инициализация приложений `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="c388b-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="c388b-337">[Параметры модели процессов для пула приложений `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="c388b-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="c388b-338">Модуль, схемы и расположение файлов конфигурации</span><span class="sxs-lookup"><span data-stu-id="c388b-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="c388b-339">Module</span><span class="sxs-lookup"><span data-stu-id="c388b-339">Module</span></span>

<span data-ttu-id="c388b-340">**IIS (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="c388b-340">**IIS (x86/amd64)** :</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="c388b-341">**IIS Express (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="c388b-341">**IIS Express (x86/amd64)** :</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="c388b-342">Схема</span><span class="sxs-lookup"><span data-stu-id="c388b-342">Schema</span></span>

<span data-ttu-id="c388b-343">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="c388b-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="c388b-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="c388b-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="c388b-345">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="c388b-345">Configuration</span></span>

<span data-ttu-id="c388b-346">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="c388b-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="c388b-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="c388b-347">**IIS Express**</span></span>

* <span data-ttu-id="c388b-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="c388b-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="c388b-349">CLI *iisexpress.exe* : `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="c388b-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="c388b-350">Файлы можно найти путем поиска `aspnetcore` в файле `applicationHost.config`.</span><span class="sxs-lookup"><span data-stu-id="c388b-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>
