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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Прочие ASP.NET Core API-интерфейсы защиты данных

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Типы, реализующие любой из следующих интерфейсов, должны быть потокобезопасными для нескольких вызывающих объектов.

## <a name="isecret"></a>исекрет

`ISecret` Интерфейс представляет секретное значение, например материал криптографического ключа. Он содержит следующую поверхность API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer` Метод заполняет указанный буфер необработанным значением секрета. Причина, по которой этот API принимает буфер в качестве параметра, вместо `byte[]` прямого возврата напрямую заключается в том, что это дает вызывающей стороне возможность закрепить объект буфера, ограничивая секретный код управляемому сборщику мусора.

`Secret` Тип является конкретной реализацией, `ISecret` где значение секрета хранится в памяти внутри процесса. На платформах Windows значение секрета шифруется через [криптпротектмемори](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).
