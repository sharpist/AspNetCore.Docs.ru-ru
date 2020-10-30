---
title: Расширяемость ядра криптографии в ASP.NET Core
author: rick-anderson
description: Сведения о Иаусентикатеденкриптор, Иаусентикатеденкриптордескриптор, Иаусентикатеденкриптордескриптордесериализер и фабрике верхнего уровня.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 99ce283d56a6010ddd846f21e0ca9a9a324d55fc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060785"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>Расширяемость ядра криптографии в ASP.NET Core

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Типы, реализующие любой из следующих интерфейсов, должны быть потокобезопасными для нескольких вызывающих объектов.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>иаусентикатеденкриптор

Интерфейс **иаусентикатеденкриптор** является основным стандартным блоком криптографической подсистемы. Обычно по одному Иаусентикатеденкриптор на каждый ключ, а экземпляр Иаусентикатеденкриптор упаковывает все данные криптографического ключа и алгоритмы, необходимые для выполнения криптографических операций.

Как видно из названия, тип отвечает за предоставление служб шифрования и расшифровки с проверкой подлинности. Он предоставляет следующие два API-интерфейса.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Метод Encrypt возвращает большой двоичный объект, который содержит открытый текст зашифрованные и тег проверки подлинности. Тег проверки подлинности должен охватывать дополнительные данные, прошедшие проверку подлинности (AAD), хотя сам AAD не должен быть восстановлен из окончательных полезных данных. Метод дешифровки проверяет тег проверки подлинности и возвращает расшифрованные полезные данные. Все ошибки (кроме ArgumentNullException и аналогичные) должны быть хоможенизеды в CryptographicException.

> [!NOTE]
> Сам экземпляр Иаусентикатеденкриптор не должен содержать ключевой материал. Например, реализация может делегировать модуль HSM для всех операций.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Создание Иаусентикатеденкриптор

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Интерфейс **иаусентикатеденкрипторфактори** представляет тип, который знает, как создать экземпляр [иаусентикатеденкриптор](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Его API выглядит следующим образом.

* Креатинкрипторинстанце (ключ IKey): Иаусентикатеденкриптор

Для любого заданного экземпляра IKey все прошедшие проверку подлинности средства шифрования, созданные его методом Креатинкрипторинстанце, должны считаться эквивалентными, как показано в примере кода ниже.

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Интерфейс **иаусентикатеденкриптордескриптор** представляет тип, который знает, как создать экземпляр [иаусентикатеденкриптор](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Его API выглядит следующим образом.

* Креатинкрипторинстанце (): Иаусентикатеденкриптор

* Експорттоксмл (): Ксмлсериализеддескрипторинфо

Как и Иаусентикатеденкриптор, предполагается, что экземпляр Иаусентикатеденкриптордескриптор является оболочкой для одного конкретного ключа. Это означает, что для любого заданного экземпляра Иаусентикатеденкриптордескриптор все прошедшие проверку подлинности средства шифрования, созданные его методом Креатинкрипторинстанце, должны считаться эквивалентными, как показано в примере кода ниже.

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>Иаусентикатеденкриптордескриптор (только ASP.NET Core 2. x)

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Интерфейс **иаусентикатеденкриптордескриптор** представляет тип, который знает, как экспортировать себя в XML. Его API выглядит следующим образом.

* Експорттоксмл (): Ксмлсериализеддескрипторинфо

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>XML-сериализация

Основное различие между Иаусентикатеденкриптор и Иаусентикатеденкриптордескриптор заключается в том, что дескриптор знает, как создать шифратор и предоставить его допустимым аргументам. Рассмотрим Иаусентикатеденкриптор, реализация которого основывается на SymmetricAlgorithm и KeyedHashAlgorithm. Задание шифратора заключается в использовании этих типов, но не обязательно знает, откуда поступили эти типы, поэтому не может действительно записать правильное описание способа повторного создания, если приложение перезапускается. Дескриптор действует как более высокий уровень поверх этого. Поскольку дескриптору известно, как создать экземпляр шифратора (например, он знает, как создать необходимые алгоритмы), он может сериализовать этот набор знаний в XML-форме, чтобы экземпляр шифратора можно было повторно создать после сброса приложения.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

Дескриптор можно сериализовать с помощью своей подпрограммы Експорттоксмл. Эта подпрограммы возвращает Ксмлсериализеддескрипторинфо, который содержит два свойства: представление XElement дескриптора и тип, который представляет [иаусентикатеденкриптордескриптордесериализер](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) , который можно использовать для восвыполнения этого дескриптора, учитывая соответствующий элемент XElement.

Сериализованный дескриптор может содержать конфиденциальные сведения, такие как материал криптографического ключа. Система защиты данных имеет встроенную поддержку шифрования данных перед их сохранением в хранилище. Чтобы воспользоваться этим преимуществом, дескриптор должен пометить элемент, содержащий конфиденциальную информацию, именем атрибута «Рекуиресенкриптион» (xmlns « <http://schemas.asp.net/2015/03/dataProtection> »), значением «true».

>[!TIP]
> Для установки этого атрибута существует вспомогательный API. Вызовите метод расширения XElement. Маркасрекуиресенкриптион (), расположенный в пространстве имен Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigУратионмодел.

Возможны также случаи, когда сериализованный дескриптор не содержит конфиденциальных сведений. Снова рассмотрите возможность использования криптографического ключа, хранящегося в HSM. Дескриптор не может записать ключевой материал при сериализации, так как HSM не будет предоставлять материал в виде открытого текста. Вместо этого дескриптор может записать версию ключа, упакованную в ключ (если HSM поддерживает экспорт), или собственный уникальный идентификатор HSM для ключа.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>иаусентикатеденкриптордескриптордесериализер

Интерфейс **иаусентикатеденкриптордескриптордесериализер** представляет тип, который знает, как десериализовать экземпляр Иаусентикатеденкриптордескриптор из XElement. Он предоставляет один метод:

* Импортфромксмл (элемент XElement): Иаусентикатеденкриптордескриптор

Метод Импортфромксмл принимает элемент XElement, возвращенный [иаусентикатеденкриптордескриптор. експорттоксмл](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) , и создает эквивалент исходного иаусентикатеденкриптордескриптор.

Типы, реализующие Иаусентикатеденкриптордескриптордесериализер, должны иметь один из следующих двух открытых конструкторов:

* . ctor (IServiceProvider)

* . ctor ()

> [!NOTE]
> Объект IServiceProvider, переданный в конструктор, может иметь значение null.

## <a name="the-top-level-factory"></a>Фабрика верхнего уровня

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Класс **алгорисмконфигуратион** представляет тип, который знает, как создавать экземпляры [иаусентикатеденкриптордескриптор](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Он предоставляет один API.

* Креатеневдескриптор (): Иаусентикатеденкриптордескриптор

Алгорисмконфигуратион в качестве фабрики верхнего уровня. Конфигурация служит шаблоном. Он создает оболочку для алгоритмной информации (например, эта конфигурация создает дескрипторы с помощью главного ключа AES-128-GCM), но еще не связана с конкретным ключом.

При вызове Креатеневдескриптор новый материал ключа создается исключительно для этого вызова, и создается новая Иаусентикатеденкриптордескриптор, которая упаковывает этот материал ключа и алгоритмную информацию, необходимую для использования материала. Материал ключа может быть создан в программном обеспечении (и храниться в памяти), его можно создать и удерживать в АППАРАТном модуле безопасности и т. д. Важным моментом является то, что любые два вызова Креатеневдескриптор никогда не должны создавать эквивалентные экземпляры Иаусентикатеденкриптордескриптор.

Тип Алгорисмконфигуратион выступает в качестве точки входа для подпрограмм создания ключей, таких как [Автоматическое пошаговое выполнение ключей](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Чтобы изменить реализацию для всех последующих ключей, установите свойство Аусентикатеденкрипторконфигуратион в Кэйманажементоптионс.

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Интерфейс **иаусентикатеденкрипторконфигуратион** представляет тип, который знает, как создавать экземпляры [иаусентикатеденкриптордескриптор](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Он предоставляет один API.

* Креатеневдескриптор (): Иаусентикатеденкриптордескриптор

Иаусентикатеденкрипторконфигуратион в качестве фабрики верхнего уровня. Конфигурация служит шаблоном. Он создает оболочку для алгоритмной информации (например, эта конфигурация создает дескрипторы с помощью главного ключа AES-128-GCM), но еще не связана с конкретным ключом.

При вызове Креатеневдескриптор новый материал ключа создается исключительно для этого вызова, и создается новая Иаусентикатеденкриптордескриптор, которая упаковывает этот материал ключа и алгоритмную информацию, необходимую для использования материала. Материал ключа может быть создан в программном обеспечении (и храниться в памяти), его можно создать и удерживать в АППАРАТном модуле безопасности и т. д. Важным моментом является то, что любые два вызова Креатеневдескриптор никогда не должны создавать эквивалентные экземпляры Иаусентикатеденкриптордескриптор.

Тип Иаусентикатеденкрипторконфигуратион выступает в качестве точки входа для подпрограмм создания ключей, таких как [Автоматическое пошаговое выполнение ключей](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Чтобы изменить реализацию для всех последующих ключей, зарегистрируйте одноэлементную Иаусентикатеденкрипторконфигуратион в контейнере службы.

---
