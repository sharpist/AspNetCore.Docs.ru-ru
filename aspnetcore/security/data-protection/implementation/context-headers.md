---
title: Заголовки контекста в ASP.NET Core
author: rick-anderson
description: Сведения о реализации ASP.NET Core заголовков контекста защиты данных.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051724"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="65e35-103">Заголовки контекста в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65e35-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="65e35-104">Фон и теория</span><span class="sxs-lookup"><span data-stu-id="65e35-104">Background and theory</span></span>

<span data-ttu-id="65e35-105">В системе защиты данных "ключ" означает объект, который может предоставлять службы шифрования, прошедшие проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="65e35-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="65e35-106">Каждый ключ идентифицируется с помощью уникального идентификатора (GUID) и включает в себя ИТ-алгоритмную информацию и энтропию.</span><span class="sxs-lookup"><span data-stu-id="65e35-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="65e35-107">Предполагается, что каждый ключ имеет уникальную энтропию, но система не может их применить, и нам также нужно учитывать, что для разработчиков, которые могут изменить ключевое кольцо вручную, путем изменения алгоритма данных существующего ключа в кольце ключа.</span><span class="sxs-lookup"><span data-stu-id="65e35-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="65e35-108">Для достижения требований безопасности в этих случаях система защиты данных имеет концепцию [криптографической гибкости](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), которая позволяет безопасно использовать одно значение энтропии в нескольких алгоритмах шифрования.</span><span class="sxs-lookup"><span data-stu-id="65e35-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="65e35-109">Большинство систем, поддерживающих гибкость криптографии, делают это, включая некоторые идентифицирующие сведения о алгоритме в полезных данных.</span><span class="sxs-lookup"><span data-stu-id="65e35-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="65e35-110">Как правило, этот идентификатор является хорошим кандидатом для этого.</span><span class="sxs-lookup"><span data-stu-id="65e35-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="65e35-111">Однако одна из проблем, с которыми мы столкнулись, заключается в том, что существует несколько способов указать один и тот же алгоритм: "AES" (CNG), а управляемые классы AES, AesManaged, AesCryptoServiceProvider, AesCng и RijndaelManaged (заданные конкретные параметры) фактически одинаковы, и нам пришлось бы поддерживать сопоставление всех этих классов с правильным идентификатором OID.</span><span class="sxs-lookup"><span data-stu-id="65e35-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="65e35-112">Если разработчик хочет предоставить пользовательский алгоритм (или даже другую реализацию AES!), ему пришлось бы сообщить нам свой идентификатор OID.</span><span class="sxs-lookup"><span data-stu-id="65e35-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="65e35-113">Этот дополнительный шаг регистрации делает конфигурацию системы особенно ненужной.</span><span class="sxs-lookup"><span data-stu-id="65e35-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="65e35-114">При пошаговом выполнении мы решили, что мы разрешили проблему в неправильном направлении.</span><span class="sxs-lookup"><span data-stu-id="65e35-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="65e35-115">OID сообщает, что представляет собой алгоритм, но на самом деле это не важно.</span><span class="sxs-lookup"><span data-stu-id="65e35-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="65e35-116">Если необходимо безопасно использовать одно значение энтропии в двух разных алгоритмах, нет необходимости знать, что именно представляют собой алгоритмы.</span><span class="sxs-lookup"><span data-stu-id="65e35-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="65e35-117">В действительности мы будем заниматься тем, как они ведут себя.</span><span class="sxs-lookup"><span data-stu-id="65e35-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="65e35-118">Любой неназначенный симметричный алгоритм блочного шифра также является строго псевдослучайноеой перестановки (репликации паролей): исправьте входные данные (режим цепочки, последовательного режима, вектор инициализации, обычный текст) и вывод зашифрованного текста с перегруженной вероятностью будет отличаться от любого другого алгоритма симметричного блочного шифра,</span><span class="sxs-lookup"><span data-stu-id="65e35-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="65e35-119">Аналогичным образом любая функция хэширования с любыми ключевыми ключами также является строгой функцией псевдослучайное (PRF), и при указании фиксированного входного набора его выходные данные будут совершенно отличаться от любых других хэш-функций с ключом.</span><span class="sxs-lookup"><span data-stu-id="65e35-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="65e35-120">Мы используем эту концепцию strong PRP и Прфс для создания заголовка контекста.</span><span class="sxs-lookup"><span data-stu-id="65e35-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="65e35-121">Этот заголовок контекста, по сути, действует как стабильный отпечаток по сравнению с алгоритмами, используемыми для любой конкретной операции, и обеспечивает гибкость шифрования, необходимую системе защиты данных.</span><span class="sxs-lookup"><span data-stu-id="65e35-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="65e35-122">Этот заголовок воспроизводится воспроизводимым образом и используется позже как часть [процесса наследования подраздела](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="65e35-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="65e35-123">Существует два разных способа построения заголовка контекста в зависимости от режимов работы базовых алгоритмов.</span><span class="sxs-lookup"><span data-stu-id="65e35-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="65e35-124">Шифрование в режиме CBC + проверка подлинности HMAC</span><span class="sxs-lookup"><span data-stu-id="65e35-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="65e35-125">Заголовок контекста состоит из следующих компонентов:</span><span class="sxs-lookup"><span data-stu-id="65e35-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="65e35-126">[16 бит] Значение 00 00, которое является маркером, означающее "шифрование CBC + HMAC Authentication".</span><span class="sxs-lookup"><span data-stu-id="65e35-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="65e35-127">[32 бит] Длина ключа (в байтах с обратным порядком байтов) для алгоритма симметричного блочного шифра.</span><span class="sxs-lookup"><span data-stu-id="65e35-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="65e35-128">[32 бит] Размер блока (в байтах с обратным порядком байтов) для алгоритма симметричного блочного шифра.</span><span class="sxs-lookup"><span data-stu-id="65e35-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="65e35-129">[32 бит] Длина ключа (в байтах с обратным порядком байтов) для алгоритма HMAC.</span><span class="sxs-lookup"><span data-stu-id="65e35-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="65e35-130">(В настоящее время размер ключа всегда совпадает с размером дайджеста.)</span><span class="sxs-lookup"><span data-stu-id="65e35-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="65e35-131">[32 бит] Размер дайджеста (в байтах с обратным порядком байтов) для алгоритма HMAC.</span><span class="sxs-lookup"><span data-stu-id="65e35-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="65e35-132">`EncCBC(K_E, IV, "")`, который является выходным значением алгоритма симметричного блочного шифра, в котором используется входная пустая строка и где IV является нулевым вектором.</span><span class="sxs-lookup"><span data-stu-id="65e35-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="65e35-133">Конструкция `K_E` описана ниже.</span><span class="sxs-lookup"><span data-stu-id="65e35-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="65e35-134">`MAC(K_H, "")`, который является выходным результатом алгоритма HMAC по заданному входу пустой строки.</span><span class="sxs-lookup"><span data-stu-id="65e35-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="65e35-135">Конструкция `K_H` описана ниже.</span><span class="sxs-lookup"><span data-stu-id="65e35-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="65e35-136">В идеале мы можем передать все нулевые векторы для `K_E` и `K_H` .</span><span class="sxs-lookup"><span data-stu-id="65e35-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="65e35-137">Однако мы хотим избежать ситуации, когда базовый алгоритм проверяет наличие слабых ключей перед выполнением любых операций (особенно DES и 3DES), которые не могут использовать простой или повторяемый шаблон, такой как «все-ноль».</span><span class="sxs-lookup"><span data-stu-id="65e35-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="65e35-138">Вместо этого мы используем директиву NIST SP800-108 ПОДПРОГРАММ в режиме счетчика (см. директивы [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) с нулевой длиной ключа, метки и контекста и HMACSHA512 в качестве базового PRF.</span><span class="sxs-lookup"><span data-stu-id="65e35-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="65e35-139">Мы получаем `| K_E | + | K_H |` байты выходных данных, а затем разбивают результат на `K_E` и `K_H` сами.</span><span class="sxs-lookup"><span data-stu-id="65e35-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="65e35-140">В математическом виде это представлено следующим образом.</span><span class="sxs-lookup"><span data-stu-id="65e35-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="65e35-141">Пример: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="65e35-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="65e35-142">В качестве примера рассмотрим случай, когда алгоритм симметричного блочного шифра — AES-192-CBC, а алгоритм проверки — HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="65e35-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="65e35-143">Система создаст заголовок контекста, выполнив следующие действия.</span><span class="sxs-lookup"><span data-stu-id="65e35-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="65e35-144">Сначала позвольте `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , где и в соответствии `| K_E | = 192 bits` `| K_H | = 256 bits` с указанными алгоритмами.</span><span class="sxs-lookup"><span data-stu-id="65e35-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="65e35-145">Это приводит к `K_E = 5BB6..21DD` и `K_H = A04A..00A9` в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="65e35-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="65e35-146">Затем вычислите `Enc_CBC (K_E, IV, "")` данные для AES-192-CBC `IV = 0*` и `K_E` как описано выше.</span><span class="sxs-lookup"><span data-stu-id="65e35-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="65e35-147">Далее, вычисление `MAC(K_H, "")` для HMACSHA256 было задано `K_H` как выше.</span><span class="sxs-lookup"><span data-stu-id="65e35-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="65e35-148">В результате будет получен заголовок полного контекста ниже:</span><span class="sxs-lookup"><span data-stu-id="65e35-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="65e35-149">Этот заголовок контекста представляет собой отпечаток пары алгоритмов шифрования с проверкой подлинности (AES-192-CBC Encryption + HMACSHA256).</span><span class="sxs-lookup"><span data-stu-id="65e35-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="65e35-150">Ниже перечислены компоненты, описанные [выше](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) .</span><span class="sxs-lookup"><span data-stu-id="65e35-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="65e35-151">маркер `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="65e35-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="65e35-152">Длина ключа блочного шифра `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="65e35-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="65e35-153">Размер блока блочного шифра `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="65e35-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="65e35-154">Длина ключа HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="65e35-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="65e35-155">Размер дайджеста HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="65e35-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="65e35-156">выходные данные блочного шифра репликации `(F4 74 - DB 6F)` и</span><span class="sxs-lookup"><span data-stu-id="65e35-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="65e35-157">выходные данные HMAC "PRF" `(D4 79 - end)` .</span><span class="sxs-lookup"><span data-stu-id="65e35-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="65e35-158">Заголовок контекста проверки подлинности шифрования и HMAC в режиме CBC создается одинаково независимо от того, предоставляются ли реализации алгоритмов Windows CNG или управляемыми типами SymmetricAlgorithm и KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="65e35-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="65e35-159">Это позволяет приложениям, работающим в разных операционных системах, надежно создавать один и тот же заголовок контекста, даже несмотря на то, что реализации алгоритмов отличаются в разных ОС.</span><span class="sxs-lookup"><span data-stu-id="65e35-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="65e35-160">(На практике KeyedHashAlgorithm не обязательно должен быть правильным HMAC.</span><span class="sxs-lookup"><span data-stu-id="65e35-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="65e35-161">Это может быть любой тип хэш-алгоритма с ключом.)</span><span class="sxs-lookup"><span data-stu-id="65e35-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="65e35-162">Пример: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="65e35-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="65e35-163">Сначала позвольте `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , где и в соответствии `| K_E | = 192 bits` `| K_H | = 160 bits` с указанными алгоритмами.</span><span class="sxs-lookup"><span data-stu-id="65e35-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="65e35-164">Это приводит к `K_E = A219..E2BB` и `K_H = DC4A..B464` в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="65e35-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="65e35-165">Затем необходимо выполнить вычисление `Enc_CBC (K_E, IV, "")` для 3DES-192-CBC `IV = 0*` и `K_E` , как и выше.</span><span class="sxs-lookup"><span data-stu-id="65e35-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="65e35-166">Далее, вычисление `MAC(K_H, "")` для HMACSHA1 было задано `K_H` как выше.</span><span class="sxs-lookup"><span data-stu-id="65e35-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="65e35-167">При этом создается полный заголовок контекста, который является отпечатком пары алгоритмов шифрования с проверкой подлинности (3DES-192-CBC + HMACSHA1 Validation), как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="65e35-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="65e35-168">Компоненты разбивается на следующие:</span><span class="sxs-lookup"><span data-stu-id="65e35-168">The components break down as follows:</span></span>

* <span data-ttu-id="65e35-169">маркер `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="65e35-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="65e35-170">Длина ключа блочного шифра `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="65e35-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="65e35-171">Размер блока блочного шифра `(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="65e35-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="65e35-172">Длина ключа HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="65e35-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="65e35-173">Размер дайджеста HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="65e35-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="65e35-174">выходные данные блочного шифра репликации `(AB B1 - E1 0E)` и</span><span class="sxs-lookup"><span data-stu-id="65e35-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="65e35-175">выходные данные HMAC "PRF" `(76 EB - end)` .</span><span class="sxs-lookup"><span data-stu-id="65e35-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="65e35-176">Галоис/шифрование в режиме счетчика + аутентификация</span><span class="sxs-lookup"><span data-stu-id="65e35-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="65e35-177">Заголовок контекста состоит из следующих компонентов:</span><span class="sxs-lookup"><span data-stu-id="65e35-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="65e35-178">[16 бит] Значение 00 01, которое является маркером "шифрование GCM + проверка подлинности".</span><span class="sxs-lookup"><span data-stu-id="65e35-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="65e35-179">[32 бит] Длина ключа (в байтах с обратным порядком байтов) для алгоритма симметричного блочного шифра.</span><span class="sxs-lookup"><span data-stu-id="65e35-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="65e35-180">[32 бит] Размер nonce (в байтах с обратным порядком байтов), используемый во время операций шифрования, прошедших проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="65e35-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="65e35-181">(Для нашей системы это исправлено на nonce size = 96 бит.)</span><span class="sxs-lookup"><span data-stu-id="65e35-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="65e35-182">[32 бит] Размер блока (в байтах с обратным порядком байтов) для алгоритма симметричного блочного шифра.</span><span class="sxs-lookup"><span data-stu-id="65e35-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="65e35-183">(Для GCM это фиксировано в блоке размером = 128 бит.)</span><span class="sxs-lookup"><span data-stu-id="65e35-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="65e35-184">[32 бит] Размер тега проверки подлинности (в байтах с обратным порядком байтов), созданный функцией шифрования с проверкой подлинности.</span><span class="sxs-lookup"><span data-stu-id="65e35-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="65e35-185">(Для нашей системы это фиксировано по размеру тега = 128 бит.)</span><span class="sxs-lookup"><span data-stu-id="65e35-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="65e35-186">[128 бит] Тег `Enc_GCM (K_E, nonce, "")` , который является выходным значением алгоритма симметричного блочного шифра, учитывая входную пустую строку и где nonce — это 96-разрядный вектор с нулевым значением.</span><span class="sxs-lookup"><span data-stu-id="65e35-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="65e35-187">`K_E` является производным, используя тот же механизм, что и в сценарии проверки подлинности шифрования CBC + HMAC.</span><span class="sxs-lookup"><span data-stu-id="65e35-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="65e35-188">Тем не менее, так как здесь нет, то, по `K_H` сути `| K_H | = 0` , есть, и алгоритм сворачивается в приведенную ниже форму.</span><span class="sxs-lookup"><span data-stu-id="65e35-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="65e35-189">Пример: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="65e35-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="65e35-190">Сначала позвольте `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , где `| K_E | = 256 bits` .</span><span class="sxs-lookup"><span data-stu-id="65e35-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="65e35-191">Затем вычислите тег проверки подлинности `Enc_GCM (K_E, nonce, "")` для AES-256-gcm в заданном `nonce = 096` и `K_E` приведенном выше виде.</span><span class="sxs-lookup"><span data-stu-id="65e35-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="65e35-192">В результате будет получен заголовок полного контекста ниже:</span><span class="sxs-lookup"><span data-stu-id="65e35-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="65e35-193">Компоненты разбивается на следующие:</span><span class="sxs-lookup"><span data-stu-id="65e35-193">The components break down as follows:</span></span>

* <span data-ttu-id="65e35-194">маркер `(00 01)`</span><span class="sxs-lookup"><span data-stu-id="65e35-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="65e35-195">Длина ключа блочного шифра `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="65e35-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="65e35-196">Размер nonce `(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="65e35-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="65e35-197">Размер блока блочного шифра `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="65e35-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="65e35-198">Размер тега проверки подлинности `(00 00 00 10)` и</span><span class="sxs-lookup"><span data-stu-id="65e35-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="65e35-199">Тег проверки подлинности для запуска блочного шифра `(E7 DC - end)` .</span><span class="sxs-lookup"><span data-stu-id="65e35-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
