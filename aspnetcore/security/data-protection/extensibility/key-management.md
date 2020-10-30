---
title: Расширяемость управления ключами в ASP.NET Core
author: rick-anderson
description: Дополнительные сведения о ASP.NET Core расширяемости управления ключами для защиты данных.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- appsettings.json
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 08ec018238d18cd253e793267c5b146df8319485
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060772"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>Расширяемость управления ключами в ASP.NET Core

Ознакомьтесь с разделом [Управление ключами](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) перед чтением этого раздела, так как в нем объясняются основные понятия, лежащие в основе этих API-интерфейсов.

**Предупреждение** : типы, реализующие любой из следующих интерфейсов, должны быть потокобезопасными для нескольких вызывающих объектов.

## <a name="key"></a>Ключ

`IKey`Интерфейс является базовым представлением ключа в криптосистемы. Ключевое слово Key используется в абстрактном смысле, а не в литеральном смысле «материал криптографического ключа». Ключ имеет следующие свойства:

* Даты активации, создания и истечения срока действия

* Состояние отзыва.

* Идентификатор ключа (GUID)

::: moniker range=">= aspnetcore-2.0"

Кроме того, `IKey` предоставляет `CreateEncryptor` метод, который можно использовать для создания экземпляра [иаусентикатеденкриптор](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) , привязанного к этому ключу.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Кроме того, `IKey` предоставляет `CreateEncryptorInstance` метод, который можно использовать для создания экземпляра [иаусентикатеденкриптор](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) , привязанного к этому ключу.

::: moniker-end

> [!NOTE]
> Нет API для извлечения необработанных криптографических материалов из `IKey` экземпляра.

## <a name="ikeymanager"></a>икэйманажер

`IKeyManager`Интерфейс представляет объект, отвечающий за общее хранение ключей, их извлечение и обработку. Он предоставляет три высокоуровневые операции:

* Создайте новый ключ и сохранит его в хранилище.

* Получение всех ключей из хранилища.

* Отозвать один или несколько ключей и сохранить сведения об отзыве в хранилище.

>[!WARNING]
> Написание `IKeyManager` — очень сложная задача, и большинство разработчиков не должны предпринимать попытки. Вместо этого большинству разработчиков следует воспользоваться возможностями, предлагаемыми классом [ксмлкэйманажер](#xmlkeymanager) .

## <a name="xmlkeymanager"></a>ксмлкэйманажер

`XmlKeyManager`Тип является встроенной конкретной реализацией `IKeyManager` . Он предоставляет несколько полезных средств, включая депонирование ключей и шифрование неактивных ключей. Ключи в этой системе представлены в виде XML-элементов (в частности, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` зависит от нескольких других компонентов в процессе выполнения задач.

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, который определяет алгоритмы, используемые новыми ключами.

* `IXmlRepository`, который управляет сохранением ключей в хранилище.

* `IXmlEncryptor` [необязательное], что позволяет шифровать ключи неактивных ключей.

* `IKeyEscrowSink` [необязательно], который предоставляет службы депонирования ключей.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, который управляет сохранением ключей в хранилище.

* `IXmlEncryptor` [необязательное], что позволяет шифровать ключи неактивных ключей.

* `IKeyEscrowSink` [необязательно], который предоставляет службы депонирования ключей.

::: moniker-end

Ниже приведены высокоуровневые схемы, которые показывают, как эти компоненты взаимодействуют в `XmlKeyManager` .

::: moniker range=">= aspnetcore-2.0"

![Создание ключа](key-management/_static/keycreation2.png)

*Создание ключа/Креатеневкэй*

В реализации `CreateNewKey` `AlgorithmConfiguration` компонент используется для создания уникального объекта `IAuthenticatedEncryptorDescriptor` , который затем сериализуется как XML. Если указан депонированный ключ, в приемнике для долгосрочного хранения предоставляется необработанный (незашифрованный) XML-код. После этого незашифрованный XML выполняется с помощью `IXmlEncryptor` (при необходимости) для создания зашифрованного XML-документа. Этот зашифрованный документ сохраняется в долгосрочном хранилище с помощью `IXmlRepository` . (Если не `IXmlEncryptor` настроено, незашифрованный документ сохраняется в `IXmlRepository` .)

![Получение ключа](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Создание ключа](key-management/_static/keycreation1.png)

*Создание ключа/Креатеневкэй*

В реализации `CreateNewKey` `IAuthenticatedEncryptorConfiguration` компонент используется для создания уникального объекта `IAuthenticatedEncryptorDescriptor` , который затем сериализуется как XML. Если указан депонированный ключ, в приемнике для долгосрочного хранения предоставляется необработанный (незашифрованный) XML-код. После этого незашифрованный XML выполняется с помощью `IXmlEncryptor` (при необходимости) для создания зашифрованного XML-документа. Этот зашифрованный документ сохраняется в долгосрочном хранилище с помощью `IXmlRepository` . (Если не `IXmlEncryptor` настроено, незашифрованный документ сохраняется в `IXmlRepository` .)

![Получение ключа](key-management/_static/keyretrieval1.png)

::: moniker-end

*Получение ключа или Жеталлкэйс*

В реализации `GetAllKeys` XML-документы, представляющие ключи и отзыва, считываются из базового объекта `IXmlRepository` . Если эти документы зашифрованы, система будет автоматически расшифровывать их. `XmlKeyManager` создает соответствующие `IAuthenticatedEncryptorDescriptorDeserializer` экземпляры для десериализации документов обратно в `IAuthenticatedEncryptorDescriptor` экземпляры, которые затем упаковываются в отдельные `IKey` экземпляры. Эта коллекция `IKey` экземпляров возвращается вызывающему объекту.

Дополнительные сведения об определенных элементах XML можно найти в [документе формат хранилища ключей](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>иксмлрепоситори

`IXmlRepository`Интерфейс представляет тип, который может сохранять XML и получать XML из резервного хранилища. Он предоставляет два интерфейса API:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Реализациям `IXmlRepository` не нужно анализировать XML-код, передающий их. Они должны рассматривать XML-документы как непрозрачные и позволять более высоким уровням беспокоиться о формировании и анализе документов.

Существует четыре встроенных конкретных типа, которые реализуют `IXmlRepository` :

::: moniker range=">= aspnetcore-2.2"

* [филесистемксмлрепоситори](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [регистриксмлрепоситори](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. Азуреблобксмлрепоситори](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [редисксмлрепоситори](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [филесистемксмлрепоситори](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [регистриксмлрепоситори](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. Азуреблобксмлрепоситори](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [редисксмлрепоситори](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Дополнительные сведения см. в [документе о поставщиках хранилища ключей](xref:security/data-protection/implementation/key-storage-providers) .

Регистрация настраиваемого элемента `IXmlRepository` подходит для использования другого резервного хранилища (например, табличного хранилища Azure).

Чтобы изменить приложение репозитория по умолчанию, зарегистрируйте пользовательский `IXmlRepository` экземпляр:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a>иксмленкриптор

`IXmlEncryptor`Интерфейс представляет тип, который может шифровать XML-элемент с открытым текстом. Он предоставляет один API:

* Encrypt (XElement Плаинтекстелемент): Енкриптедксмлинфо

Если сериализованный объект `IAuthenticatedEncryptorDescriptor` содержит какие-либо элементы, помеченные как "требуется шифрование", то запустит `XmlKeyManager` эти элементы с помощью `IXmlEncryptor` `Encrypt` метода, который будет сохранять элемент зашифрованные, а не элемент открытого текста в `IXmlRepository` . Выходными данными `Encrypt` метода является `EncryptedXmlInfo` объект. Этот объект представляет собой оболочку, которая содержит результирующий зашифрованные `XElement` и тип, который `IXmlDecryptor` может использоваться для расшифровки соответствующего элемента.

Существует четыре встроенных конкретных типа, которые реализуют `IXmlEncryptor` :

* [цертификатексмленкриптор](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [дпапингксмленкриптор](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [дпапиксмленкриптор](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [нуллксмленкриптор](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Дополнительные сведения см. в статье о [шифровании ключа в неактивных документах](xref:security/data-protection/implementation/key-encryption-at-rest) .

Чтобы изменить значение по умолчанию для механизма шифрования по ключу "ключ-неактивных" на уровне приложения, зарегистрируйте пользовательский `IXmlEncryptor` экземпляр:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a>иксмлдекриптор

`IXmlDecryptor`Интерфейс представляет тип, который знает, как расшифровать объект `XElement` , зашифрованные с помощью `IXmlEncryptor` . Он предоставляет один API:

* Расшифровка (XElement Енкриптеделемент): XElement

Метод отменяет `Decrypt` Шифрование, выполненное `IXmlEncryptor.Encrypt` . Как правило, каждая конкретная `IXmlEncryptor` реализация будет иметь соответствующую конкретную `IXmlDecryptor` реализацию.

Типы, реализующие, `IXmlDecryptor` должны иметь один из следующих двух открытых конструкторов:

* . ctor (IServiceProvider)
* . ctor ()

> [!NOTE]
> `IServiceProvider`Переданный конструктору параметр может иметь значение null.

## <a name="ikeyescrowsink"></a>икэйескровсинк

`IKeyEscrowSink`Интерфейс представляет тип, который может выполнять депонирование конфиденциальной информации. Вспомним, что сериализованные дескрипторы могут содержать конфиденциальные сведения (такие как криптографические материалы), и это привело к вводу типа [иксмленкриптор](#ixmlencryptor) в первую очередь. Однако всякое происходит, и при этом могут быть удалены или повреждены кольца ключей.

Депонированный интерфейс предоставляет escape-последовательность аварийного переключения, предоставляя доступ к необработанному сериализованному XML, прежде чем он преобразуется любым настроенным [иксмленкриптор](#ixmlencryptor). Интерфейс предоставляет один API:

* Store (GUID keyId, элемент XElement)

`IKeyEscrowSink`Реализация для обработки указанного элемента обеспечивается безопасным способом, согласованным с бизнес-политикой. Одна из возможных реализаций может быть для того, чтобы депонированный приемник зашифрует XML-элемент с помощью известного корпоративного сертификата X. 509, в котором был указан закрытый ключ сертификата. `CertificateXmlEncryptor` этот тип может помочь в этом. `IKeyEscrowSink`Реализация также отвечает за сохранение указанного элемента соответствующим образом.

По умолчанию не включен механизм депонирования, хотя администраторы сервера могут [настроить глобально](xref:security/data-protection/configuration/machine-wide-policy). Его также можно настроить программно с помощью `IDataProtectionBuilder.AddKeyEscrowSink` метода, как показано в примере ниже. `AddKeyEscrowSink`Перегрузки методов отражают `IServiceCollection.AddSingleton` и `IServiceCollection.AddInstance` перегрузки, так как `IKeyEscrowSink` экземпляры должны быть одноэлементными. Если `IKeyEscrowSink` зарегистрировано несколько экземпляров, каждый из них будет вызываться во время создания ключа, так что ключи могут быть одновременно переданы нескольким механизмам.

Нет API для чтения материала из `IKeyEscrowSink` экземпляра. Это согласуется с теоретическим механизмом депонированного механизма: он предназначен для того, чтобы сделать материал ключа доступным доверенному центру, а так как само приложение не является доверенным центром, оно не должно иметь доступа к его собственному депонированному материалу.

В следующем образце кода показано создание и регистрация `IKeyEscrowSink` ключей WHERE, которые могут быть восстановлены только членами группы "Администраторы контосодомаин".

> [!NOTE]
> Для выполнения этого примера необходимо находиться на присоединенном к домену компьютере Windows 8 или Windows Server 2012, а контроллер домена должен быть Windows Server 2012 или более поздней версии.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
