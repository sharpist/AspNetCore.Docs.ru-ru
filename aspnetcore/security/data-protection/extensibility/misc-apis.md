---
title: Прочие ASP.NET Core API-интерфейсы защиты данных
author: rick-anderson
description: Сведения об интерфейсе Исекрет защиты данных ASP.NET Core.
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: e9de92233468e9e07791df608b1c37ffb3b29949
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408504"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Прочие ASP.NET Core API-интерфейсы защиты данных

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Типы, реализующие любой из следующих интерфейсов, должны быть потокобезопасными для нескольких вызывающих объектов.

## <a name="isecret"></a>исекрет

`ISecret`Интерфейс представляет секретное значение, например материал криптографического ключа. Он содержит следующую поверхность API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer`Метод заполняет указанный буфер необработанным значением секрета. Причина, по которой этот API принимает буфер в качестве параметра, вместо `byte[]` прямого возврата напрямую заключается в том, что это дает вызывающей стороне возможность закрепить объект буфера, ограничивая секретный код управляемому сборщику мусора.

`Secret`Тип является конкретной реализацией, `ISecret` где значение секрета хранится в памяти внутри процесса. На платформах Windows значение секрета шифруется через [криптпротектмемори](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).
