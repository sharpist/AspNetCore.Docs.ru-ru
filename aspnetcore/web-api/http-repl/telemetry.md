---
title: Хттпрепл телеметрии
author: scottaddie
description: Сведения о телеметрии, собираемых Хттпрепл.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550612"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="8b92c-103">Хттпрепл телеметрии</span><span class="sxs-lookup"><span data-stu-id="8b92c-103">HttpRepl telemetry</span></span>

<span data-ttu-id="8b92c-104">[Хттпрепл](xref:web-api/http-repl) включает функцию телеметрии, которая собирает данные об использовании.</span><span class="sxs-lookup"><span data-stu-id="8b92c-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="8b92c-105">Важно, чтобы команда Хттпрепл могла улучшить работу средства.</span><span class="sxs-lookup"><span data-stu-id="8b92c-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="8b92c-106">Как отключить функцию</span><span class="sxs-lookup"><span data-stu-id="8b92c-106">How to opt out</span></span>

<span data-ttu-id="8b92c-107">Функция телеметрии Хттпрепл включена по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="8b92c-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="8b92c-108">Чтобы отключить ее, присвойте переменной среды `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` значение `1` или `true`.</span><span class="sxs-lookup"><span data-stu-id="8b92c-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="8b92c-109">Раскрытие информации</span><span class="sxs-lookup"><span data-stu-id="8b92c-109">Disclosure</span></span>

<span data-ttu-id="8b92c-110">При первом запуске средства Хттпрепл отображает текст, аналогичный приведенному ниже.</span><span class="sxs-lookup"><span data-stu-id="8b92c-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="8b92c-111">Текст может немного отличаться в зависимости от используемой версии инструмента.</span><span class="sxs-lookup"><span data-stu-id="8b92c-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="8b92c-112">Именно таким образом корпорация Майкрософт уведомляет вас о сборе данных.</span><span class="sxs-lookup"><span data-stu-id="8b92c-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="8b92c-113">Чтобы отключить текст "первый запуск", задайте `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` для переменной среды значение `1` или `true` .</span><span class="sxs-lookup"><span data-stu-id="8b92c-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="8b92c-114">Точки данных</span><span class="sxs-lookup"><span data-stu-id="8b92c-114">Data points</span></span>

<span data-ttu-id="8b92c-115">Функция телеметрии не:</span><span class="sxs-lookup"><span data-stu-id="8b92c-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="8b92c-116">Собирайте персональные данные, такие как имена пользователей, адреса электронной почты или URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="8b92c-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="8b92c-117">Проверка HTTP-запросов или ответов.</span><span class="sxs-lookup"><span data-stu-id="8b92c-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="8b92c-118">Данные безопасно передаются на серверы Майкрософт и удерживаются в ограниченном доступе.</span><span class="sxs-lookup"><span data-stu-id="8b92c-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="8b92c-119">Мы заботимся о вашей конфиденциальности.</span><span class="sxs-lookup"><span data-stu-id="8b92c-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="8b92c-120">Если вы подозреваете, что функция телеметрии собирает конфиденциальные данные, или что данные не являются безопасными или несоответствующим образом обрабатываются, выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="8b92c-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="8b92c-121">Заполните ошибку в репозитории [DotNet/хттпрепл](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="8b92c-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="8b92c-122">Отправьте сообщение по электронной почте [dotnet@microsoft.com](mailto:dotnet@microsoft.com) для изучения.</span><span class="sxs-lookup"><span data-stu-id="8b92c-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="8b92c-123">Функция телеметрии собирает следующие данные.</span><span class="sxs-lookup"><span data-stu-id="8b92c-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="8b92c-124">Версии пакета SDK для .NET</span><span class="sxs-lookup"><span data-stu-id="8b92c-124">.NET SDK versions</span></span> | <span data-ttu-id="8b92c-125">Данные</span><span class="sxs-lookup"><span data-stu-id="8b92c-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="8b92c-126">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-126">>=5.0</span></span>        | <span data-ttu-id="8b92c-127">Метка времени вызова.</span><span class="sxs-lookup"><span data-stu-id="8b92c-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="8b92c-128">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-128">>=5.0</span></span>        | <span data-ttu-id="8b92c-129">IP-адрес из трех октетов, используемый для определения географического расположения.</span><span class="sxs-lookup"><span data-stu-id="8b92c-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="8b92c-130">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-130">>=5.0</span></span>        | <span data-ttu-id="8b92c-131">Операционная система и ее версия.</span><span class="sxs-lookup"><span data-stu-id="8b92c-131">Operating system and version.</span></span> |
| <span data-ttu-id="8b92c-132">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-132">>=5.0</span></span>        | <span data-ttu-id="8b92c-133">Идентификатор среды выполнения (RID), в которой работает средство.</span><span class="sxs-lookup"><span data-stu-id="8b92c-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="8b92c-134">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-134">>=5.0</span></span>        | <span data-ttu-id="8b92c-135">Выполняется ли средство в контейнере.</span><span class="sxs-lookup"><span data-stu-id="8b92c-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="8b92c-136">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-136">>=5.0</span></span>        | <span data-ttu-id="8b92c-137">Хэшированный контроль доступа к носителю (MAC): криптографический (SHA256) хэшированный и уникальный идентификатор для компьютера.</span><span class="sxs-lookup"><span data-stu-id="8b92c-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="8b92c-138">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-138">>=5.0</span></span>        | <span data-ttu-id="8b92c-139">Версия ядра.</span><span class="sxs-lookup"><span data-stu-id="8b92c-139">Kernel version.</span></span> |
| <span data-ttu-id="8b92c-140">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-140">>=5.0</span></span>        | <span data-ttu-id="8b92c-141">Версия Хттпрепл.</span><span class="sxs-lookup"><span data-stu-id="8b92c-141">HttpRepl version.</span></span> |
| <span data-ttu-id="8b92c-142">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-142">>=5.0</span></span>        | <span data-ttu-id="8b92c-143">Было ли средство запущено с `help` `run` `connect` аргументами, или.</span><span class="sxs-lookup"><span data-stu-id="8b92c-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="8b92c-144">Фактические значения аргументов не собираются.</span><span class="sxs-lookup"><span data-stu-id="8b92c-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="8b92c-145">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-145">>=5.0</span></span>        | <span data-ttu-id="8b92c-146">Вызванная команда (например, `get` ) и ее успешность.</span><span class="sxs-lookup"><span data-stu-id="8b92c-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="8b92c-147">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-147">>=5.0</span></span>        | <span data-ttu-id="8b92c-148">Для `connect` команды, указывает, `root` были ли `base` `openapi` предоставлены аргументы, или.</span><span class="sxs-lookup"><span data-stu-id="8b92c-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="8b92c-149">Фактические значения аргументов не собираются.</span><span class="sxs-lookup"><span data-stu-id="8b92c-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="8b92c-150">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-150">>=5.0</span></span>        | <span data-ttu-id="8b92c-151">Для `pref` команды — независимо от того, была ли выполнена команда `get` или `set` и к какой предпочтению осуществлялся доступ.</span><span class="sxs-lookup"><span data-stu-id="8b92c-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="8b92c-152">Если нет хорошо известных предпочтений, имя хэшируется.</span><span class="sxs-lookup"><span data-stu-id="8b92c-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="8b92c-153">Значение не собираются.</span><span class="sxs-lookup"><span data-stu-id="8b92c-153">The value isn't collected.</span></span> |
| <span data-ttu-id="8b92c-154">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-154">>=5.0</span></span>        | <span data-ttu-id="8b92c-155">Для `set header` команды задается имя заголовка.</span><span class="sxs-lookup"><span data-stu-id="8b92c-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="8b92c-156">Если не является хорошо известным заголовком, имя хэшируется.</span><span class="sxs-lookup"><span data-stu-id="8b92c-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="8b92c-157">Значение не собираются.</span><span class="sxs-lookup"><span data-stu-id="8b92c-157">The value isn't collected.</span></span> |
| <span data-ttu-id="8b92c-158">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-158">>=5.0</span></span>        | <span data-ttu-id="8b92c-159">Для `connect` команды — используется ли особый случай для `dotnet new webapi` , и, является ли он обходится с помощью предпочтений.</span><span class="sxs-lookup"><span data-stu-id="8b92c-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="8b92c-160">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8b92c-160">>=5.0</span></span>        | <span data-ttu-id="8b92c-161">Для всех команд HTTP (например, GET, POST, WHERE) указывается, задан ли каждый из параметров.</span><span class="sxs-lookup"><span data-stu-id="8b92c-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="8b92c-162">Значения параметров не собираются.</span><span class="sxs-lookup"><span data-stu-id="8b92c-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="8b92c-163">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="8b92c-163">Additional resources</span></span>

* [<span data-ttu-id="8b92c-164">Телеметрия пакета SDK для .NET Core</span><span class="sxs-lookup"><span data-stu-id="8b92c-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="8b92c-165">.NET Core CLI данных телеметрии</span><span class="sxs-lookup"><span data-stu-id="8b92c-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
