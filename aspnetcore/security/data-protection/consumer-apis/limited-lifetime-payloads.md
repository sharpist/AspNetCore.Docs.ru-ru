---
title: Ограничить время существования защищенных полезных данных в ASP.NET Core
author: rick-anderson
description: Узнайте, как ограничить время существования защищенных полезных данных с помощью ASP.NET Core API-интерфейсов защиты данных.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: d8c83ca46b1993af1f5e7985571ff012d90b1e01
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408374"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="398e8-103">Ограничить время существования защищенных полезных данных в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="398e8-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="398e8-104">Существуют сценарии, в которых разработчик приложения хочет создать защищенные полезные данные, срок действия которых истекает через заданный период времени.</span><span class="sxs-lookup"><span data-stu-id="398e8-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="398e8-105">Например, защищенные полезные данные могут представлять маркер сброса пароля, который должен быть допустимым только в течение одного часа.</span><span class="sxs-lookup"><span data-stu-id="398e8-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="398e8-106">Разработчику наверняка можно создать собственный формат полезных данных, который содержит внедренную дату истечения срока действия, и опытные разработчики могут сделать это все в любом случае, но для большинства разработчиков, занимающихся обработкой этих сроков, может расти утомительно.</span><span class="sxs-lookup"><span data-stu-id="398e8-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="398e8-107">Чтобы упростить эту задачу для нашей аудитории разработчика, пакет [Microsoft. AspNetCore. данные защиты. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) содержит служебные API-интерфейсы для создания полезных данных, срок действия которых истекает автоматически по истечении заданного периода времени.</span><span class="sxs-lookup"><span data-stu-id="398e8-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="398e8-108">Эти API зависает от `ITimeLimitedDataProtector` типа.</span><span class="sxs-lookup"><span data-stu-id="398e8-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="398e8-109">Использование API</span><span class="sxs-lookup"><span data-stu-id="398e8-109">API usage</span></span>

<span data-ttu-id="398e8-110">`ITimeLimitedDataProtector`Интерфейс — это основной интерфейс для защиты и снятия защиты полезных данных с ограниченным временем или с самостоятельным истечением срока действия.</span><span class="sxs-lookup"><span data-stu-id="398e8-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="398e8-111">Чтобы создать экземпляр класса `ITimeLimitedDataProtector` , сначала требуется экземпляр регулярного [идатапротектор](xref:security/data-protection/consumer-apis/overview) , созданный с определенной целью.</span><span class="sxs-lookup"><span data-stu-id="398e8-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="398e8-112">После того как `IDataProtector` экземпляр доступен, вызовите `IDataProtector.ToTimeLimitedDataProtector` метод расширения для возврата предохранителя с помощью встроенных возможностей истечения срока действия.</span><span class="sxs-lookup"><span data-stu-id="398e8-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="398e8-113">`ITimeLimitedDataProtector`предоставляет следующие интерфейсы API и интерфейсов расширения:</span><span class="sxs-lookup"><span data-stu-id="398e8-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="398e8-114">Креатепротектор (Назначение строки): Итимелимитеддатапротектор — этот API аналогичен существующему `IDataProtectionProvider.CreateProtector` в том, что его можно использовать для создания [цепочек целей](xref:security/data-protection/consumer-apis/purpose-strings) из корневого средства защиты с ограниченным сроком действия.</span><span class="sxs-lookup"><span data-stu-id="398e8-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="398e8-115">Защита (незашифрованный текст в байтах, срок действия DateTimeOffset): Byte []</span><span class="sxs-lookup"><span data-stu-id="398e8-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="398e8-116">Защитить (байт [] в формате UTC, время существования TimeSpan): Byte []</span><span class="sxs-lookup"><span data-stu-id="398e8-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="398e8-117">Защитить (байт [] в формате UTC): Byte []</span><span class="sxs-lookup"><span data-stu-id="398e8-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="398e8-118">Защита (обычный текст строки, срок действия DateTimeOffset): строка</span><span class="sxs-lookup"><span data-stu-id="398e8-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="398e8-119">Защита (обычный текст в виде строки, время существования TimeSpan): строка</span><span class="sxs-lookup"><span data-stu-id="398e8-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="398e8-120">Защита (текст в виде строки): строка</span><span class="sxs-lookup"><span data-stu-id="398e8-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="398e8-121">В дополнение к основным `Protect` методам, которые принимают только открытый текст, существуют новые перегрузки, которые позволяют указать дату истечения срока действия полезных данных.</span><span class="sxs-lookup"><span data-stu-id="398e8-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="398e8-122">Дата истечения срока действия может быть задана как абсолютная дата (через `DateTimeOffset` ) или как относительное время (от текущего системного времени через `TimeSpan` ).</span><span class="sxs-lookup"><span data-stu-id="398e8-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="398e8-123">Если вызывается перегрузка, которая не принимает истечение срока действия, то предполагается, что полезная нагрузка никогда не истечет.</span><span class="sxs-lookup"><span data-stu-id="398e8-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="398e8-124">Снять защиту (Byte [] protectedData, исходящий срок действия DateTimeOffset): Byte []</span><span class="sxs-lookup"><span data-stu-id="398e8-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="398e8-125">Снять защиту (байт [] protectedData): Byte []</span><span class="sxs-lookup"><span data-stu-id="398e8-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="398e8-126">Снять защиту (строковый protectedData, исходящий срок действия DateTimeOffset): строка</span><span class="sxs-lookup"><span data-stu-id="398e8-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="398e8-127">Снять защиту (строка protectedData): строка</span><span class="sxs-lookup"><span data-stu-id="398e8-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="398e8-128">`Unprotect`Методы возвращают исходные незащищенные данные.</span><span class="sxs-lookup"><span data-stu-id="398e8-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="398e8-129">Если еще не истек срок действия полезных данных, абсолютный срок действия возвращается как необязательный параметр out вместе с исходными незащищенными данными.</span><span class="sxs-lookup"><span data-stu-id="398e8-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="398e8-130">Если срок действия полезных данных истек, все перегрузки метода Unprotect будут вызывать CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="398e8-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="398e8-131">Не рекомендуется использовать эти API для защиты полезных данных, требующих долгосрочного или неопределенного сохраняемости.</span><span class="sxs-lookup"><span data-stu-id="398e8-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="398e8-132">«Могу ли я позволить необратимому восстановлению защищенных полезных данных через месяц?»</span><span class="sxs-lookup"><span data-stu-id="398e8-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="398e8-133">может служить хорошим правилом для Thumb. Если ответ — нет, разработчики должны рассмотреть альтернативные API.</span><span class="sxs-lookup"><span data-stu-id="398e8-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="398e8-134">В приведенном ниже примере используются [пути кода без di](xref:security/data-protection/configuration/non-di-scenarios) для создания экземпляра системы защиты данных.</span><span class="sxs-lookup"><span data-stu-id="398e8-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="398e8-135">Чтобы запустить этот пример, убедитесь, что предварительно добавлены ссылки на пакет Microsoft. AspNetCore. MDAC. Extensions.</span><span class="sxs-lookup"><span data-stu-id="398e8-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
