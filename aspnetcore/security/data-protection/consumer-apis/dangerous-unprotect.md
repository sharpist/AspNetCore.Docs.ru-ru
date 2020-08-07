---
title: Снять защиту с полезных данных, ключи которых были отозваны в ASP.NET Core
author: rick-anderson
description: Узнайте, как снять защиту данных, защищенных с помощью ключей, которые были отозваны в ASP.NET Core приложении.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 55a7ec4052b3ab47d5ff41bbce3fc3f9662f609c
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913854"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="1bf72-103">Снять защиту с полезных данных, ключи которых были отозваны в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bf72-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="1bf72-104">ASP.NET Core API-интерфейсы защиты данных в основном не предназначены для неопределенного сохранения конфиденциальных полезных данных.</span><span class="sxs-lookup"><span data-stu-id="1bf72-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="1bf72-105">Другие технологии, такие как [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) и [Azure Rights Management](/rights-management/) , более подходят для сценария неопределенного хранилища и имеют соответствующие возможности управления ключами.</span><span class="sxs-lookup"><span data-stu-id="1bf72-105">Other technologies like [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="1bf72-106">С другой стороны, разработчик не запрещает использовать ASP.NET Core API-интерфейсы защиты данных для долгосрочной защиты конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="1bf72-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="1bf72-107">Ключи никогда не удаляются из кольца ключей, поэтому `IDataProtector.Unprotect` всегда могут восстанавливать существующие полезные данные, если ключи доступны и действительны.</span><span class="sxs-lookup"><span data-stu-id="1bf72-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="1bf72-108">Однако проблема возникает, когда разработчик пытается снять защиту с данных, защищенных с помощью отозванного ключа, так как `IDataProtector.Unprotect` в этом случае будет выдано исключение.</span><span class="sxs-lookup"><span data-stu-id="1bf72-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="1bf72-109">Это может быть удобно для кратковременных или временных полезных данных (например, маркеров проверки подлинности), так как эти типы полезных данных могут легко воссоздаться системой, и в худшем случае посетителям сайта может потребоваться снова войти в систему.</span><span class="sxs-lookup"><span data-stu-id="1bf72-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="1bf72-110">Но для сохраненных полезных данных использование `Unprotect` throw может привести к неприемлемой потери данных.</span><span class="sxs-lookup"><span data-stu-id="1bf72-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="1bf72-111">иперсистеддатапротектор</span><span class="sxs-lookup"><span data-stu-id="1bf72-111">IPersistedDataProtector</span></span>

<span data-ttu-id="1bf72-112">Для поддержки сценария, позволяющего снять защиту полезных данных даже в случае отозванных ключей, система защиты данных содержит `IPersistedDataProtector` тип.</span><span class="sxs-lookup"><span data-stu-id="1bf72-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="1bf72-113">Чтобы получить экземпляр `IPersistedDataProtector` , просто получите экземпляр `IDataProtector` обычным образом и попробуйте приведение `IDataProtector` к `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="1bf72-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="1bf72-114">Не все `IDataProtector` экземпляры могут быть приведены к `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="1bf72-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="1bf72-115">Разработчики должны использовать оператор C# AS или аналогичный, чтобы избежать исключений среды выполнения, вызванных недопустимыми приведениями, и должны быть готовы к обработке случайного сбоя.</span><span class="sxs-lookup"><span data-stu-id="1bf72-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="1bf72-116">`IPersistedDataProtector`предоставляет следующую поверхность API:</span><span class="sxs-lookup"><span data-stu-id="1bf72-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="1bf72-117">Этот API принимает защищенные полезные данные (в виде массива байтов) и возвращает незащищенные полезные данные.</span><span class="sxs-lookup"><span data-stu-id="1bf72-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="1bf72-118">Перегрузка на основе строк отсутствует.</span><span class="sxs-lookup"><span data-stu-id="1bf72-118">There's no string-based overload.</span></span> <span data-ttu-id="1bf72-119">Ниже приведены два выходных параметра.</span><span class="sxs-lookup"><span data-stu-id="1bf72-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="1bf72-120">`requiresMigration`: будет иметь значение true, если ключ, используемый для защиты этих полезных данных, больше не является активным ключом по умолчанию, например, ключ, используемый для защиты этой полезной нагрузки, устарел и операция с предыдущим ключом уже выполнена.</span><span class="sxs-lookup"><span data-stu-id="1bf72-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="1bf72-121">Вызывающий объект может попытаться повторно защитить полезные данные в зависимости от бизнес-потребностей.</span><span class="sxs-lookup"><span data-stu-id="1bf72-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="1bf72-122">`wasRevoked`: будет иметь значение true, если ключ, используемый для защиты полезной нагрузки, был отозван.</span><span class="sxs-lookup"><span data-stu-id="1bf72-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="1bf72-123">При передаче `ignoreRevocationErrors: true` в метод Будьте предельно осторожны `DangerousUnprotect` .</span><span class="sxs-lookup"><span data-stu-id="1bf72-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="1bf72-124">Если после вызова этого метода `wasRevoked` значение равно true, то ключ, используемый для защиты этих полезных данных, был отозван, а подлинность полезной нагрузки должна рассматриваться как подозрительная.</span><span class="sxs-lookup"><span data-stu-id="1bf72-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="1bf72-125">В этом случае продолжайте работать только с незащищенными полезными данными, только если у вас есть отдельная гарантия, которую она подлинна, например, она поступает из защищенной базы данных, а не отправляется недоверенным веб-клиентом.</span><span class="sxs-lookup"><span data-stu-id="1bf72-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
