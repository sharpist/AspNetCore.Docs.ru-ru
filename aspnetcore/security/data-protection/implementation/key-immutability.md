---
title: Неизменность ключа и ключевые параметры в ASP.NET Core
author: rick-anderson
description: Сведения о реализации интерфейсов API неизменности ключа защиты данных ASP.NET Core.
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 32925a3ef6d52a7be3a852cefe670c15d545b490
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627628"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Неизменность ключа и ключевые параметры в ASP.NET Core

После сохранения объекта в резервном хранилище его представление постоянно фиксируется. Новые данные могут быть добавлены в резервное хранилище, но существующие данные не могут изменяться. Основное назначение этого поведения заключается в предотвращении повреждения данных.

Одним из следствием такого поведения является то, что после того, как ключ записывается в резервное хранилище, он становится неизменяемым. Даты создания, активации и истечения срока действия не могут быть изменены, хотя могут быть отозваны с помощью `IKeyManager` . Кроме того, его базовые сведения о алгоритме, основном материале для ключа и шифровании неактивных данных также являются неизменяемыми.

Если разработчик изменяет любой параметр, влияющий на сохраняемость ключей, эти изменения не вступят в силу до следующего создания ключа, либо посредством явного вызова `IKeyManager.CreateNewKey` или посредством [автоматического создания ключа](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) в системе защиты данных. Ниже приведены параметры, влияющие на сохраняемость ключей.

* [Время жизни ключа по умолчанию](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [Механизм неактивных ключей шифрования](xref:security/data-protection/implementation/key-encryption-at-rest)

* [Алгоритмическая информация, содержащаяся в ключе](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Если необходимо, чтобы эти параметры запускались раньше, чем при следующем автоматическом создании ключа, рассмотрите возможность явного вызова для `IKeyManager.CreateNewKey` принудительного создания нового ключа. Не забудьте предоставить явную дату активации ({Now + 2 дня} — хорошее правило, чтобы разрешить распространение изменений) и дату истечения срока действия в вызове.

>[!TIP]
> Все приложения, затрагивающие репозиторий, должны указывать те же параметры с помощью `IDataProtectionBuilder` методов расширения. В противном случае свойства постоянного ключа будут зависеть от конкретного приложения, которое вызвало подпрограммы создания ключей.
