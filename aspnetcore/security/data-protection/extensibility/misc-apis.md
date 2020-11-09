---
title: Прочие ASP.NET Core API-интерфейсы защиты данных
author: rick-anderson
description: Сведения об интерфейсе Исекрет защиты данных ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: bd772b11300cca8896ed512da1cd12c49e6f104b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060759"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="fe541-103">Прочие ASP.NET Core API-интерфейсы защиты данных</span><span class="sxs-lookup"><span data-stu-id="fe541-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="fe541-104">Типы, реализующие любой из следующих интерфейсов, должны быть потокобезопасными для нескольких вызывающих объектов.</span><span class="sxs-lookup"><span data-stu-id="fe541-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="fe541-105">исекрет</span><span class="sxs-lookup"><span data-stu-id="fe541-105">ISecret</span></span>

<span data-ttu-id="fe541-106">`ISecret`Интерфейс представляет секретное значение, например материал криптографического ключа.</span><span class="sxs-lookup"><span data-stu-id="fe541-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="fe541-107">Он содержит следующую поверхность API:</span><span class="sxs-lookup"><span data-stu-id="fe541-107">It contains the following API surface:</span></span>

* <span data-ttu-id="fe541-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="fe541-108">`Length`: `int`</span></span>

* <span data-ttu-id="fe541-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="fe541-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="fe541-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="fe541-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="fe541-111">`WriteSecretIntoBuffer`Метод заполняет указанный буфер необработанным значением секрета.</span><span class="sxs-lookup"><span data-stu-id="fe541-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="fe541-112">Причина, по которой этот API принимает буфер в качестве параметра, вместо `byte[]` прямого возврата напрямую заключается в том, что это дает вызывающей стороне возможность закрепить объект буфера, ограничивая секретный код управляемому сборщику мусора.</span><span class="sxs-lookup"><span data-stu-id="fe541-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="fe541-113">`Secret`Тип является конкретной реализацией, `ISecret` где значение секрета хранится в памяти внутри процесса.</span><span class="sxs-lookup"><span data-stu-id="fe541-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="fe541-114">На платформах Windows значение секрета шифруется через [криптпротектмемори](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span><span class="sxs-lookup"><span data-stu-id="fe541-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
