---
title: SignalRРекомендации по проектированию API
author: anurse
description: Узнайте, как проектировать SignalR интерфейсы API для совместимости с различными версиями приложения.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/api-design
ms.openlocfilehash: 9ad8d30da552d3d3084534b8c7ca57386ad111ac
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407802"
---
# <a name="signalr-api-design-considerations"></a>SignalR<span data-ttu-id="adeb2-103">Рекомендации по проектированию API</span><span class="sxs-lookup"><span data-stu-id="adeb2-103"> API design considerations</span></span>

<span data-ttu-id="adeb2-104">[Эндрю Стантон-медперсонала](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="adeb2-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="adeb2-105">В этой статье приводятся рекомендации по созданию SignalR API-интерфейсов на основе.</span><span class="sxs-lookup"><span data-stu-id="adeb2-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="adeb2-106">Использование пользовательских параметров объекта для обеспечения обратной совместимости</span><span class="sxs-lookup"><span data-stu-id="adeb2-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="adeb2-107">Добавление параметров в SignalR метод концентратора (как на клиенте, так и на сервере) является *критическим изменением*.</span><span class="sxs-lookup"><span data-stu-id="adeb2-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="adeb2-108">Это означает, что старые клиенты и серверы будут получать ошибки при попытке вызвать метод без соответствующего количества параметров.</span><span class="sxs-lookup"><span data-stu-id="adeb2-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="adeb2-109">Однако добавление свойств в параметр пользовательского объекта **не** является критическим изменением.</span><span class="sxs-lookup"><span data-stu-id="adeb2-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="adeb2-110">Это можно использовать для проектирования совместимых интерфейсов API, устойчивых к изменениям на клиенте или сервере.</span><span class="sxs-lookup"><span data-stu-id="adeb2-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="adeb2-111">Например, рассмотрим API на стороне сервера, как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="adeb2-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="adeb2-112">Клиент JavaScript вызывает этот метод, используя `invoke` следующий код:</span><span class="sxs-lookup"><span data-stu-id="adeb2-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="adeb2-113">Если позднее добавить второй параметр в метод сервера, более старые клиенты не будут предоставлять это значение параметра.</span><span class="sxs-lookup"><span data-stu-id="adeb2-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="adeb2-114">Пример.</span><span class="sxs-lookup"><span data-stu-id="adeb2-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="adeb2-115">Когда старый клиент пытается вызвать этот метод, он получит ошибку следующего вида:</span><span class="sxs-lookup"><span data-stu-id="adeb2-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="adeb2-116">На сервере появится сообщение журнала следующего вида:</span><span class="sxs-lookup"><span data-stu-id="adeb2-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="adeb2-117">Старый клиент отправил только один параметр, но новый API сервера должен иметь два параметра.</span><span class="sxs-lookup"><span data-stu-id="adeb2-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="adeb2-118">Использование пользовательских объектов в качестве параметров обеспечивает большую гибкость.</span><span class="sxs-lookup"><span data-stu-id="adeb2-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="adeb2-119">Давайте перейдем к исходному API для использования пользовательского объекта:</span><span class="sxs-lookup"><span data-stu-id="adeb2-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="adeb2-120">Теперь клиент использует объект для вызова метода:</span><span class="sxs-lookup"><span data-stu-id="adeb2-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="adeb2-121">Вместо добавления параметра добавьте в `TotalLengthRequest` объект свойство:</span><span class="sxs-lookup"><span data-stu-id="adeb2-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="adeb2-122">Когда старый клиент отправляет один параметр, дополнительный `Param2` свойство будет оставлено `null` .</span><span class="sxs-lookup"><span data-stu-id="adeb2-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="adeb2-123">Чтобы обнаружить сообщение, отправленное более старым клиентом, установите флажок `Param2` для `null` и примените значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="adeb2-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="adeb2-124">Новый клиент может отправить оба параметра.</span><span class="sxs-lookup"><span data-stu-id="adeb2-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="adeb2-125">Тот же метод работает для методов, определенных на клиенте.</span><span class="sxs-lookup"><span data-stu-id="adeb2-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="adeb2-126">Вы можете отправить пользовательский объект со стороны сервера:</span><span class="sxs-lookup"><span data-stu-id="adeb2-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="adeb2-127">На стороне клиента вы получите доступ к `Message` свойству, а не по параметру:</span><span class="sxs-lookup"><span data-stu-id="adeb2-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="adeb2-128">Если позже вы решите добавить отправителя сообщения в полезные данные, добавьте свойство в объект:</span><span class="sxs-lookup"><span data-stu-id="adeb2-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="adeb2-129">Более старые клиенты не будут ожидать `Sender` значение, поэтому они будут проигнорированы.</span><span class="sxs-lookup"><span data-stu-id="adeb2-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="adeb2-130">Новый клиент может принять его, обновив для чтения нового свойства:</span><span class="sxs-lookup"><span data-stu-id="adeb2-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="adeb2-131">В этом случае новый клиент также является отказоустойчивым для старого сервера, который не предоставляет это `Sender` значение.</span><span class="sxs-lookup"><span data-stu-id="adeb2-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="adeb2-132">Так как старый сервер не предоставит это `Sender` значение, клиент проверяет, существует ли он перед доступом к нему.</span><span class="sxs-lookup"><span data-stu-id="adeb2-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
