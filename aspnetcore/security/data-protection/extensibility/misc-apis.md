---
title: Прочие ASP.NET Core API-интерфейсы защиты данных
author: rick-anderson
description: Сведения об интерфейсе Исекрет защиты данных ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: a07ccc3645a9a8132fd5290e7c43f353f74aca05
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776985"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="e20d6-103">Прочие ASP.NET Core API-интерфейсы защиты данных</span><span class="sxs-lookup"><span data-stu-id="e20d6-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="e20d6-104">Типы, реализующие любой из следующих интерфейсов, должны быть потокобезопасными для нескольких вызывающих объектов.</span><span class="sxs-lookup"><span data-stu-id="e20d6-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="e20d6-105">исекрет</span><span class="sxs-lookup"><span data-stu-id="e20d6-105">ISecret</span></span>

<span data-ttu-id="e20d6-106">`ISecret` Интерфейс представляет секретное значение, например материал криптографического ключа.</span><span class="sxs-lookup"><span data-stu-id="e20d6-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="e20d6-107">Он содержит следующую поверхность API:</span><span class="sxs-lookup"><span data-stu-id="e20d6-107">It contains the following API surface:</span></span>

* <span data-ttu-id="e20d6-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="e20d6-108">`Length`: `int`</span></span>

* <span data-ttu-id="e20d6-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="e20d6-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="e20d6-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="e20d6-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="e20d6-111">`WriteSecretIntoBuffer` Метод заполняет указанный буфер необработанным значением секрета.</span><span class="sxs-lookup"><span data-stu-id="e20d6-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="e20d6-112">Причина, по которой этот API принимает буфер в качестве параметра, вместо `byte[]` прямого возврата напрямую заключается в том, что это дает вызывающей стороне возможность закрепить объект буфера, ограничивая секретный код управляемому сборщику мусора.</span><span class="sxs-lookup"><span data-stu-id="e20d6-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="e20d6-113">`Secret` Тип является конкретной реализацией, `ISecret` где значение секрета хранится в памяти внутри процесса.</span><span class="sxs-lookup"><span data-stu-id="e20d6-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="e20d6-114">На платформах Windows значение секрета шифруется через [криптпротектмемори](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span><span class="sxs-lookup"><span data-stu-id="e20d6-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
