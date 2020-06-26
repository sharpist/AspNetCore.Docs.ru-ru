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
# <a name="signalr-api-design-considerations"></a>SignalRРекомендации по проектированию API

[Эндрю Стантон-медперсонала](https://twitter.com/anurse)

В этой статье приводятся рекомендации по созданию SignalR API-интерфейсов на основе.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Использование пользовательских параметров объекта для обеспечения обратной совместимости

Добавление параметров в SignalR метод концентратора (как на клиенте, так и на сервере) является *критическим изменением*. Это означает, что старые клиенты и серверы будут получать ошибки при попытке вызвать метод без соответствующего количества параметров. Однако добавление свойств в параметр пользовательского объекта **не** является критическим изменением. Это можно использовать для проектирования совместимых интерфейсов API, устойчивых к изменениям на клиенте или сервере.

Например, рассмотрим API на стороне сервера, как показано ниже:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

Клиент JavaScript вызывает этот метод, используя `invoke` следующий код:

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Если позднее добавить второй параметр в метод сервера, более старые клиенты не будут предоставлять это значение параметра. Пример.

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Когда старый клиент пытается вызвать этот метод, он получит ошибку следующего вида:

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

На сервере появится сообщение журнала следующего вида:

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

Старый клиент отправил только один параметр, но новый API сервера должен иметь два параметра. Использование пользовательских объектов в качестве параметров обеспечивает большую гибкость. Давайте перейдем к исходному API для использования пользовательского объекта:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

Теперь клиент использует объект для вызова метода:

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

Вместо добавления параметра добавьте в `TotalLengthRequest` объект свойство:

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Когда старый клиент отправляет один параметр, дополнительный `Param2` свойство будет оставлено `null` . Чтобы обнаружить сообщение, отправленное более старым клиентом, установите флажок `Param2` для `null` и примените значение по умолчанию. Новый клиент может отправить оба параметра.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

Тот же метод работает для методов, определенных на клиенте. Вы можете отправить пользовательский объект со стороны сервера:

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

На стороне клиента вы получите доступ к `Message` свойству, а не по параметру:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Если позже вы решите добавить отправителя сообщения в полезные данные, добавьте свойство в объект:

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

Более старые клиенты не будут ожидать `Sender` значение, поэтому они будут проигнорированы. Новый клиент может принять его, обновив для чтения нового свойства:

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

В этом случае новый клиент также является отказоустойчивым для старого сервера, который не предоставляет это `Sender` значение. Так как старый сервер не предоставит это `Sender` значение, клиент проверяет, существует ли он перед доступом к нему.
