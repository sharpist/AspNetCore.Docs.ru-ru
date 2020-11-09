---
title: Сведения о шифровании с проверкой подлинности в ASP.NET Core
author: rick-anderson
description: Сведения о реализации шифрования с проверкой подлинности ASP.NET Core Data Protection.
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 7978725534cdd3a5b425851f61b1c7ae3ada88df
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051841"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="926ae-103">Сведения о шифровании с проверкой подлинности в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="926ae-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="926ae-104">Вызовы Идатапротектор. Protect — это операции шифрования, прошедшие проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="926ae-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="926ae-105">Метод Protect обеспечивает конфиденциальность и подлинность, а также привязан к цепочке назначений, которая использовалась для получения этого конкретного экземпляра Идатапротектор из его корневого Идатапротектионпровидер.</span><span class="sxs-lookup"><span data-stu-id="926ae-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="926ae-106">Идатапротектор. Protect принимает параметр Byte [] с открытым текстом и создает защищенные полезные данные в байтах [], формат которых описан ниже.</span><span class="sxs-lookup"><span data-stu-id="926ae-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="926ae-107">(Существует также перегрузка метода расширения, принимающая параметр строки с открытым текстом и возвращающая защищенную строковую полезную нагрузку.</span><span class="sxs-lookup"><span data-stu-id="926ae-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="926ae-108">Если этот API используется, формат защищенных полезных данных по-прежнему будет иметь следующую структуру, но он будет [base64url в кодировке](https://tools.ietf.org/html/rfc4648#section-5).)</span><span class="sxs-lookup"><span data-stu-id="926ae-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="926ae-109">Формат защищенных полезных данных</span><span class="sxs-lookup"><span data-stu-id="926ae-109">Protected payload format</span></span>

<span data-ttu-id="926ae-110">Защищенный формат полезных данных состоит из трех основных компонентов:</span><span class="sxs-lookup"><span data-stu-id="926ae-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="926ae-111">32-разрядный заголовок Magic, определяющий версию системы защиты данных.</span><span class="sxs-lookup"><span data-stu-id="926ae-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="926ae-112">Идентификатор 128-битного ключа, определяющий ключ, используемый для защиты этих полезных данных.</span><span class="sxs-lookup"><span data-stu-id="926ae-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="926ae-113">Оставшаяся часть защищенных полезных данных зависит [от шифратора, инкапсулированного этим ключом](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="926ae-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="926ae-114">В приведенном ниже примере ключ представляет собой шифратор AES-256-CBC + HMACSHA256, а полезные данные дополнительно делятся следующим образом:</span><span class="sxs-lookup"><span data-stu-id="926ae-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="926ae-115">Модификатор 128-разрядного ключа.</span><span class="sxs-lookup"><span data-stu-id="926ae-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="926ae-116">128-разрядный вектор инициализации.</span><span class="sxs-lookup"><span data-stu-id="926ae-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="926ae-117">48 байта для выходных данных AES-256-CBC.</span><span class="sxs-lookup"><span data-stu-id="926ae-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="926ae-118">Тег проверки подлинности HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="926ae-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="926ae-119">Пример защищенных полезных данных показан ниже.</span><span class="sxs-lookup"><span data-stu-id="926ae-119">A sample protected payload is illustrated below.</span></span>

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

<span data-ttu-id="926ae-120">В формате полезных данных, превышающем первые 32 бит, или 4 байта — это заголовок Magic, определяющий версию (09 F0 C9 F0).</span><span class="sxs-lookup"><span data-stu-id="926ae-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="926ae-121">Следующий 128 бит или 16 байт является идентификатором ключа (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57).</span><span class="sxs-lookup"><span data-stu-id="926ae-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="926ae-122">Остаток содержит полезные данные и зависит от используемого формата.</span><span class="sxs-lookup"><span data-stu-id="926ae-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="926ae-123">Все полезные данные, защищенные заданным ключом, будут начинаться с того же 20-байтового заголовка (магическое значение, идентификатор ключа).</span><span class="sxs-lookup"><span data-stu-id="926ae-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="926ae-124">Администраторы могут использовать этот факт в целях диагностики, чтобы приблизительно оценить время создания полезных данных.</span><span class="sxs-lookup"><span data-stu-id="926ae-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="926ae-125">Например, приведенные выше полезные данные соответствуют ключу {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="926ae-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="926ae-126">Если после проверки репозитория ключей вы обнаружите, что Дата активации конкретного ключа была 2015-01-01, а Дата окончания срока действия была 2015-03-01, то разумно предположить, что полезные данные (если они не были изменены) были созданы в этом окне, присвойте или Возьмите небольшой параметр фактор с любой стороны.</span><span class="sxs-lookup"><span data-stu-id="926ae-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
