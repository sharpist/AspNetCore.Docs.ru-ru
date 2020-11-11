---
title: Данные телеметрии HTTP REPL
author: scottaddie
description: Сведения о телеметрии, собранных HTTP REPL.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/10/2020
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
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502008"
---
# <a name="http-repl-telemetry"></a><span data-ttu-id="8ca21-103">Данные телеметрии HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="8ca21-103">HTTP REPL telemetry</span></span>

<span data-ttu-id="8ca21-104">[Цикл чтения и оценки-вывода (REPL) в HTTP](xref:web-api/http-repl) включает функцию телеметрии, собирающую данные об использовании.</span><span class="sxs-lookup"><span data-stu-id="8ca21-104">The [HTTP Read-Eval-Print Loop (REPL)](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="8ca21-105">Важно, чтобы команда HTTP REPL понимает, как используется средство, чтобы его можно было улучшить.</span><span class="sxs-lookup"><span data-stu-id="8ca21-105">It's important that the HTTP REPL team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="8ca21-106">Как отключить функцию</span><span class="sxs-lookup"><span data-stu-id="8ca21-106">How to opt out</span></span>

<span data-ttu-id="8ca21-107">Функция телеметрии HTTP REPL включена по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="8ca21-107">The HTTP REPL telemetry feature is enabled by default.</span></span> <span data-ttu-id="8ca21-108">Чтобы отключить ее, присвойте переменной среды `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` значение `1` или `true`.</span><span class="sxs-lookup"><span data-stu-id="8ca21-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="8ca21-109">Раскрытие информации</span><span class="sxs-lookup"><span data-stu-id="8ca21-109">Disclosure</span></span>

<span data-ttu-id="8ca21-110">При первом запуске средства Хттпрепл отображает текст, аналогичный приведенному ниже.</span><span class="sxs-lookup"><span data-stu-id="8ca21-110">HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="8ca21-111">Текст может немного отличаться в зависимости от используемой версии инструмента.</span><span class="sxs-lookup"><span data-stu-id="8ca21-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="8ca21-112">Именно таким образом корпорация Майкрософт уведомляет вас о сборе данных.</span><span class="sxs-lookup"><span data-stu-id="8ca21-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a><span data-ttu-id="8ca21-113">Точки данных</span><span class="sxs-lookup"><span data-stu-id="8ca21-113">Data points</span></span>

<span data-ttu-id="8ca21-114">Функция телеметрии не:</span><span class="sxs-lookup"><span data-stu-id="8ca21-114">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="8ca21-115">Собирайте персональные данные, такие как имена пользователей, адреса электронной почты или URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="8ca21-115">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="8ca21-116">Проверка HTTP-запросов или ответов.</span><span class="sxs-lookup"><span data-stu-id="8ca21-116">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="8ca21-117">Данные безопасно передаются на серверы Майкрософт и удерживаются в ограниченном доступе.</span><span class="sxs-lookup"><span data-stu-id="8ca21-117">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="8ca21-118">Мы заботимся о вашей конфиденциальности.</span><span class="sxs-lookup"><span data-stu-id="8ca21-118">Protecting your privacy is important to us.</span></span> <span data-ttu-id="8ca21-119">Если вы подозреваете, что функция телеметрии собирает конфиденциальные данные, или что данные не являются безопасными или несоответствующим образом обрабатываются, выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="8ca21-119">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="8ca21-120">Заполните ошибку в репозитории [DotNet/хттпрепл](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="8ca21-120">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="8ca21-121">Отправьте сообщение по электронной почте [dotnet@microsoft.com](mailto:dotnet@microsoft.com) для изучения.</span><span class="sxs-lookup"><span data-stu-id="8ca21-121">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="8ca21-122">Функция телеметрии собирает следующие данные.</span><span class="sxs-lookup"><span data-stu-id="8ca21-122">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="8ca21-123">Версии пакета SDK для .NET</span><span class="sxs-lookup"><span data-stu-id="8ca21-123">.NET SDK versions</span></span> | <span data-ttu-id="8ca21-124">Данные</span><span class="sxs-lookup"><span data-stu-id="8ca21-124">Data</span></span> |
|--------------|------|
| <span data-ttu-id="8ca21-125">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-125">>=5.0</span></span>        | <span data-ttu-id="8ca21-126">Метка времени вызова.</span><span class="sxs-lookup"><span data-stu-id="8ca21-126">Timestamp of invocation.</span></span> |
| <span data-ttu-id="8ca21-127">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-127">>=5.0</span></span>        | <span data-ttu-id="8ca21-128">IP-адрес из трех октетов, используемый для определения географического расположения.</span><span class="sxs-lookup"><span data-stu-id="8ca21-128">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="8ca21-129">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-129">>=5.0</span></span>        | <span data-ttu-id="8ca21-130">Операционная система и ее версия.</span><span class="sxs-lookup"><span data-stu-id="8ca21-130">Operating system and version.</span></span> |
| <span data-ttu-id="8ca21-131">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-131">>=5.0</span></span>        | <span data-ttu-id="8ca21-132">Идентификатор среды выполнения (RID), в которой работает средство.</span><span class="sxs-lookup"><span data-stu-id="8ca21-132">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="8ca21-133">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-133">>=5.0</span></span>        | <span data-ttu-id="8ca21-134">Выполняется ли средство в контейнере.</span><span class="sxs-lookup"><span data-stu-id="8ca21-134">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="8ca21-135">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-135">>=5.0</span></span>        | <span data-ttu-id="8ca21-136">Хэшированный контроль доступа к носителю (MAC): криптографический (SHA256) хэшированный и уникальный идентификатор для компьютера.</span><span class="sxs-lookup"><span data-stu-id="8ca21-136">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="8ca21-137">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-137">>=5.0</span></span>        | <span data-ttu-id="8ca21-138">Версия ядра.</span><span class="sxs-lookup"><span data-stu-id="8ca21-138">Kernel version.</span></span> |
| <span data-ttu-id="8ca21-139">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-139">>=5.0</span></span>        | <span data-ttu-id="8ca21-140">Версия HTTP REPL.</span><span class="sxs-lookup"><span data-stu-id="8ca21-140">HTTP REPL version.</span></span> |
| <span data-ttu-id="8ca21-141">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-141">>=5.0</span></span>        | <span data-ttu-id="8ca21-142">Было ли средство запущено с `help` `run` `connect` аргументами, или.</span><span class="sxs-lookup"><span data-stu-id="8ca21-142">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="8ca21-143">Фактические значения аргументов не собираются.</span><span class="sxs-lookup"><span data-stu-id="8ca21-143">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="8ca21-144">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-144">>=5.0</span></span>        | <span data-ttu-id="8ca21-145">Вызванная команда (например, `get` ) и ее успешность.</span><span class="sxs-lookup"><span data-stu-id="8ca21-145">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="8ca21-146">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-146">>=5.0</span></span>        | <span data-ttu-id="8ca21-147">Для `connect` команды, указывает, `root` были ли `base` `openapi` предоставлены аргументы, или.</span><span class="sxs-lookup"><span data-stu-id="8ca21-147">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="8ca21-148">Фактические значения аргументов не собираются.</span><span class="sxs-lookup"><span data-stu-id="8ca21-148">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="8ca21-149">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-149">>=5.0</span></span>        | <span data-ttu-id="8ca21-150">Для `pref` команды — независимо от того, была ли выполнена команда `get` или `set` и к какой предпочтению осуществлялся доступ.</span><span class="sxs-lookup"><span data-stu-id="8ca21-150">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="8ca21-151">Если нет хорошо известных предпочтений, имя хэшируется.</span><span class="sxs-lookup"><span data-stu-id="8ca21-151">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="8ca21-152">Значение не собираются.</span><span class="sxs-lookup"><span data-stu-id="8ca21-152">The value isn't collected.</span></span> |
| <span data-ttu-id="8ca21-153">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-153">>=5.0</span></span>        | <span data-ttu-id="8ca21-154">Для `set header` команды задается имя заголовка.</span><span class="sxs-lookup"><span data-stu-id="8ca21-154">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="8ca21-155">Если не является хорошо известным заголовком, имя хэшируется.</span><span class="sxs-lookup"><span data-stu-id="8ca21-155">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="8ca21-156">Значение не собираются.</span><span class="sxs-lookup"><span data-stu-id="8ca21-156">The value isn't collected.</span></span> |
| <span data-ttu-id="8ca21-157">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-157">>=5.0</span></span>        | <span data-ttu-id="8ca21-158">Для `connect` команды — используется ли особый случай для `dotnet new webapi` , и, является ли он обходится с помощью предпочтений.</span><span class="sxs-lookup"><span data-stu-id="8ca21-158">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="8ca21-159">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="8ca21-159">>=5.0</span></span>        | <span data-ttu-id="8ca21-160">Для всех команд HTTP (например, GET, POST, WHERE) указывается, задан ли каждый из параметров.</span><span class="sxs-lookup"><span data-stu-id="8ca21-160">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="8ca21-161">Значения параметров не собираются.</span><span class="sxs-lookup"><span data-stu-id="8ca21-161">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="8ca21-162">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="8ca21-162">Additional resources</span></span>

* [<span data-ttu-id="8ca21-163">Телеметрия пакета SDK для .NET Core</span><span class="sxs-lookup"><span data-stu-id="8ca21-163">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="8ca21-164">.NET Core CLI данных телеметрии</span><span class="sxs-lookup"><span data-stu-id="8ca21-164">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
