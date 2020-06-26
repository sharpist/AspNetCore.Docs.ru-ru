---
title: Временные поставщики защиты данных в ASP.NET Core
author: rick-anderson
description: Сведения о реализации ASP.NET Core временных поставщиков защиты данных.
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
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: baec19ef0c0b1e2bf5c176bf1b3c2245de0d3dd0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408920"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="c7331-103">Временные поставщики защиты данных в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c7331-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="c7331-104">Существуют сценарии, в которых приложению требуется пустячных `IDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="c7331-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="c7331-105">Например, разработчик может просто поэкспериментировать в односторонних консольном приложении, или само приложение является временным (в скрипте или проекте модульного теста).</span><span class="sxs-lookup"><span data-stu-id="c7331-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="c7331-106">Для поддержки этих сценариев пакет [Microsoft. AspNetCore. Protection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) включает тип `EphemeralDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="c7331-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="c7331-107">Этот тип предоставляет базовую реализацию `IDataProtectionProvider` , репозиторий ключей которого хранится исключительно в памяти и не записывается в резервное хранилище.</span><span class="sxs-lookup"><span data-stu-id="c7331-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="c7331-108">Каждый экземпляр `EphemeralDataProtectionProvider` использует собственный уникальный главный ключ.</span><span class="sxs-lookup"><span data-stu-id="c7331-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="c7331-109">Таким образом, если в `IDataProtector` корне создается `EphemeralDataProtectionProvider` защищенная полезная нагрузка, то эти полезные данные могут быть незащищенными с помощью эквивалентной `IDataProtector` (с той же цепочкой [назначения](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ), имеющей корень в том же `EphemeralDataProtectionProvider` экземпляре.</span><span class="sxs-lookup"><span data-stu-id="c7331-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="c7331-110">В следующем примере демонстрируется создание экземпляра `EphemeralDataProtectionProvider` и его использование для защиты и снятия защиты данных.</span><span class="sxs-lookup"><span data-stu-id="c7331-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
