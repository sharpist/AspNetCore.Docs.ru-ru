---
title: Неизменность ключа и ключевые параметры в ASP.NET Core
author: rick-anderson
description: Сведения о реализации интерфейсов API неизменности ключа защиты данных ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 5bbd1fb28fc0330ccfe4e829ab0b2f86226c7166
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776920"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Неизменность ключа и ключевые параметры в ASP.NET Core

После сохранения объекта в резервном хранилище его представление постоянно фиксируется. Новые данные могут быть добавлены в резервное хранилище, но существующие данные не могут изменяться. Основное назначение этого поведения заключается в предотвращении повреждения данных.

Одним из следствием такого поведения является то, что после того, как ключ записывается в резервное хранилище, он становится неизменяемым. Даты создания, активации и истечения срока действия не могут быть изменены, хотя могут быть отозваны с `IKeyManager`помощью. Кроме того, его базовые сведения о алгоритме, основном материале для ключа и шифровании неактивных данных также являются неизменяемыми.

Если разработчик изменяет любой параметр, влияющий на сохраняемость ключей, эти изменения не вступят в силу до следующего создания ключа, либо посредством явного вызова или посредством `IKeyManager.CreateNewKey` [автоматического создания ключа](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) в системе защиты данных. Ниже приведены параметры, влияющие на сохраняемость ключей.

* [Время жизни ключа по умолчанию](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [Механизм неактивных ключей шифрования](xref:security/data-protection/implementation/key-encryption-at-rest)

* [Алгоритмическая информация, содержащаяся в ключе](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Если необходимо, чтобы эти параметры запускались раньше, чем при следующем автоматическом создании ключа, рассмотрите возможность явного вызова `IKeyManager.CreateNewKey` для принудительного создания нового ключа. Не забудьте предоставить явную дату активации ({Now + 2 дня} — хорошее правило, чтобы разрешить распространение изменений) и дату истечения срока действия в вызове.

>[!TIP]
> Все приложения, затрагивающие репозиторий, должны указывать те же `IDataProtectionBuilder` параметры с помощью методов расширения. В противном случае свойства постоянного ключа будут зависеть от конкретного приложения, которое вызвало подпрограммы создания ключей.
