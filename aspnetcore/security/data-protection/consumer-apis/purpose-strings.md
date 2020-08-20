---
title: Строки назначения в ASP.NET Core
author: rick-anderson
description: Узнайте, как строки назначения используются в ASP.NET Core интерфейсах API защиты данных.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 1119c45570338f629a3ab7adbd43361529aa23e7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626926"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="cbf22-103">Строки назначения в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cbf22-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="cbf22-104">Компоненты, которые используют, `IDataProtectionProvider` должны передавать в метод параметр с уникальными *целями* `CreateProtector` .</span><span class="sxs-lookup"><span data-stu-id="cbf22-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="cbf22-105">*Параметр* цели является неотъемлемой частью безопасности системы защиты данных, так как он обеспечивает изоляцию между криптографическими клиентами, даже если корневые криптографические ключи одинаковы.</span><span class="sxs-lookup"><span data-stu-id="cbf22-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="cbf22-106">Когда потребитель указывает назначение, строка назначения используется вместе с корневыми криптографическими ключами для получения криптографических подразделов, уникальных для этого потребителя.</span><span class="sxs-lookup"><span data-stu-id="cbf22-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="cbf22-107">Это изолирует потребителя от всех других криптографических потребителей в приложении: никакие другие компоненты не могут считывать свои полезные данные и не могут считывать полезные данные какого-либо другого компонента.</span><span class="sxs-lookup"><span data-stu-id="cbf22-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="cbf22-108">Эта изоляция также позволяет визуализировать все категории атак на компонент.</span><span class="sxs-lookup"><span data-stu-id="cbf22-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![Пример схемы назначения](purpose-strings/_static/purposes.png)

<span data-ttu-id="cbf22-110">На приведенной выше схеме `IDataProtector` экземпляры A и B **не могут** считывать полезные данные друг друга, а только их собственные.</span><span class="sxs-lookup"><span data-stu-id="cbf22-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="cbf22-111">Строка назначения не обязательно должна быть секретной.</span><span class="sxs-lookup"><span data-stu-id="cbf22-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="cbf22-112">Он должен быть уникальным в том смысле, что ни один из других некорректно настроенных компонентов никогда не предоставит ту же строку назначения.</span><span class="sxs-lookup"><span data-stu-id="cbf22-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="cbf22-113">Использование пространства имен и имени типа компонента, использующего API-интерфейсы защиты данных, является хорошим правилом для Thumb, так как на практике эти сведения никогда не будут конфликтовать.</span><span class="sxs-lookup"><span data-stu-id="cbf22-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="cbf22-114">Компонент, разработанный Contoso, который отвечает за токены носителя минтинг, может использовать contoso. Security. Беарертокен в качестве строки назначения.</span><span class="sxs-lookup"><span data-stu-id="cbf22-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="cbf22-115">Или еще лучше — в качестве строки назначения может использоваться contoso. Security. Беарертокен. v1.</span><span class="sxs-lookup"><span data-stu-id="cbf22-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="cbf22-116">Добавление номера версии позволяет будущей версии использовать contoso. Security. Беарертокен. v2 в качестве цели, и разные версии будут полностью изолированы друг от друга, как только найдутся полезные данные.</span><span class="sxs-lookup"><span data-stu-id="cbf22-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="cbf22-117">Так как параметр целей `CreateProtector` является массивом строк, приведенный выше способ мог бы быть указан как `[ "Contoso.Security.BearerToken", "v1" ]` .</span><span class="sxs-lookup"><span data-stu-id="cbf22-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="cbf22-118">Это позволяет устанавливать иерархию целей и откроет возможность использования многоядерных сценариев с системой защиты данных.</span><span class="sxs-lookup"><span data-stu-id="cbf22-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="cbf22-119">Компоненты не должны разрешать ввод ненадежных пользователей в качестве единственного источника входных данных для цепочки целей.</span><span class="sxs-lookup"><span data-stu-id="cbf22-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="cbf22-120">Например, рассмотрим компонент contoso. Messaging. Секуремессаже, который отвечает за хранение безопасных сообщений.</span><span class="sxs-lookup"><span data-stu-id="cbf22-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="cbf22-121">Если компонент защищенного обмена сообщениями был вызван `CreateProtector([ username ])` , злоумышленник может создать учетную запись с именем "contoso. Security. беарертокен" в попытке получить вызов компонента `CreateProtector([ "Contoso.Security.BearerToken" ])` , что непреднамеренно приводит к тому, что защищенная система обмена сообщениями Mint полезные данные, которые могут восприниматься как маркеры проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="cbf22-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="cbf22-122">Более лучшей цепочкой для компонента обмена сообщениями будет `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , что обеспечивает надлежащую изоляцию.</span><span class="sxs-lookup"><span data-stu-id="cbf22-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="cbf22-123">Изоляция, предоставляемая и поведением `IDataProtectionProvider` , и, заключается в `IDataProtector` следующем.</span><span class="sxs-lookup"><span data-stu-id="cbf22-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="cbf22-124">Для данного `IDataProtectionProvider` объекта `CreateProtector` метод создает `IDataProtector` объект, который однозначно привязан к `IDataProtectionProvider` объекту, создавшему этот объект, и параметру, который был передан в метод.</span><span class="sxs-lookup"><span data-stu-id="cbf22-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="cbf22-125">Параметр цели не может иметь значение null.</span><span class="sxs-lookup"><span data-stu-id="cbf22-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="cbf22-126">(Если в качестве массива указано значение, это означает, что массив не должен иметь нулевую длину, а все элементы массива должны иметь значение, отличное от NULL.) Назначение пустой строки технически разрешено, но не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="cbf22-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="cbf22-127">Аргументы двух целей эквивалентны, только если они содержат одни и те же строки (с использованием сравнения порядков) в том же порядке.</span><span class="sxs-lookup"><span data-stu-id="cbf22-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="cbf22-128">Аргумент с одним целям эквивалентен одному одноэлементному массиву целей.</span><span class="sxs-lookup"><span data-stu-id="cbf22-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="cbf22-129">Два `IDataProtector` объекта эквивалентны, только если они создаются из эквивалентных `IDataProtectionProvider` объектов с помощью параметров эквивалентных целей.</span><span class="sxs-lookup"><span data-stu-id="cbf22-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="cbf22-130">Для данного `IDataProtector` объекта вызов метода возвратит `Unprotect(protectedData)` исходный результат, `unprotectedData` только если `protectedData := Protect(unprotectedData)` для эквивалентного `IDataProtector` объекта.</span><span class="sxs-lookup"><span data-stu-id="cbf22-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="cbf22-131">Мы не будем учитывать случай, когда некоторый компонент намеренно выбрал строку назначения, которая может конфликтовать с другим компонентом.</span><span class="sxs-lookup"><span data-stu-id="cbf22-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="cbf22-132">Такой компонент, по сути, считается вредоносным, и эта система не предназначена для предоставления гарантий безопасности в случае, если вредоносный код уже выполняется в рабочем процессе.</span><span class="sxs-lookup"><span data-stu-id="cbf22-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
