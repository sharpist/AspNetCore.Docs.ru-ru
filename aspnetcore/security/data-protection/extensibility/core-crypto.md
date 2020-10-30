---
title: Расширяемость ядра криптографии в ASP.NET Core
author: rick-anderson
description: Сведения о Иаусентикатеденкриптор, Иаусентикатеденкриптордескриптор, Иаусентикатеденкриптордескриптордесериализер и фабрике верхнего уровня.
ms.author: riande
ms.date: 08/11/2017
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 99ce283d56a6010ddd846f21e0ca9a9a324d55fc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060785"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="036fe-103">Расширяемость ядра криптографии в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="036fe-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="036fe-104">Типы, реализующие любой из следующих интерфейсов, должны быть потокобезопасными для нескольких вызывающих объектов.</span><span class="sxs-lookup"><span data-stu-id="036fe-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="036fe-105">иаусентикатеденкриптор</span><span class="sxs-lookup"><span data-stu-id="036fe-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="036fe-106">Интерфейс **иаусентикатеденкриптор** является основным стандартным блоком криптографической подсистемы.</span><span class="sxs-lookup"><span data-stu-id="036fe-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="036fe-107">Обычно по одному Иаусентикатеденкриптор на каждый ключ, а экземпляр Иаусентикатеденкриптор упаковывает все данные криптографического ключа и алгоритмы, необходимые для выполнения криптографических операций.</span><span class="sxs-lookup"><span data-stu-id="036fe-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="036fe-108">Как видно из названия, тип отвечает за предоставление служб шифрования и расшифровки с проверкой подлинности.</span><span class="sxs-lookup"><span data-stu-id="036fe-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="036fe-109">Он предоставляет следующие два API-интерфейса.</span><span class="sxs-lookup"><span data-stu-id="036fe-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="036fe-110">Метод Encrypt возвращает большой двоичный объект, который содержит открытый текст зашифрованные и тег проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="036fe-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="036fe-111">Тег проверки подлинности должен охватывать дополнительные данные, прошедшие проверку подлинности (AAD), хотя сам AAD не должен быть восстановлен из окончательных полезных данных.</span><span class="sxs-lookup"><span data-stu-id="036fe-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="036fe-112">Метод дешифровки проверяет тег проверки подлинности и возвращает расшифрованные полезные данные.</span><span class="sxs-lookup"><span data-stu-id="036fe-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="036fe-113">Все ошибки (кроме ArgumentNullException и аналогичные) должны быть хоможенизеды в CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="036fe-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="036fe-114">Сам экземпляр Иаусентикатеденкриптор не должен содержать ключевой материал.</span><span class="sxs-lookup"><span data-stu-id="036fe-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="036fe-115">Например, реализация может делегировать модуль HSM для всех операций.</span><span class="sxs-lookup"><span data-stu-id="036fe-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="036fe-116">Создание Иаусентикатеденкриптор</span><span class="sxs-lookup"><span data-stu-id="036fe-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="036fe-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="036fe-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="036fe-118">Интерфейс **иаусентикатеденкрипторфактори** представляет тип, который знает, как создать экземпляр [иаусентикатеденкриптор](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="036fe-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="036fe-119">Его API выглядит следующим образом.</span><span class="sxs-lookup"><span data-stu-id="036fe-119">Its API is as follows.</span></span>

* <span data-ttu-id="036fe-120">Креатинкрипторинстанце (ключ IKey): Иаусентикатеденкриптор</span><span class="sxs-lookup"><span data-stu-id="036fe-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="036fe-121">Для любого заданного экземпляра IKey все прошедшие проверку подлинности средства шифрования, созданные его методом Креатинкрипторинстанце, должны считаться эквивалентными, как показано в примере кода ниже.</span><span class="sxs-lookup"><span data-stu-id="036fe-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

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

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="036fe-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="036fe-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="036fe-123">Интерфейс **иаусентикатеденкриптордескриптор** представляет тип, который знает, как создать экземпляр [иаусентикатеденкриптор](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="036fe-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="036fe-124">Его API выглядит следующим образом.</span><span class="sxs-lookup"><span data-stu-id="036fe-124">Its API is as follows.</span></span>

* <span data-ttu-id="036fe-125">Креатинкрипторинстанце (): Иаусентикатеденкриптор</span><span class="sxs-lookup"><span data-stu-id="036fe-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="036fe-126">Експорттоксмл (): Ксмлсериализеддескрипторинфо</span><span class="sxs-lookup"><span data-stu-id="036fe-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="036fe-127">Как и Иаусентикатеденкриптор, предполагается, что экземпляр Иаусентикатеденкриптордескриптор является оболочкой для одного конкретного ключа.</span><span class="sxs-lookup"><span data-stu-id="036fe-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="036fe-128">Это означает, что для любого заданного экземпляра Иаусентикатеденкриптордескриптор все прошедшие проверку подлинности средства шифрования, созданные его методом Креатинкрипторинстанце, должны считаться эквивалентными, как показано в примере кода ниже.</span><span class="sxs-lookup"><span data-stu-id="036fe-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="036fe-129">Иаусентикатеденкриптордескриптор (только ASP.NET Core 2. x)</span><span class="sxs-lookup"><span data-stu-id="036fe-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="036fe-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="036fe-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="036fe-131">Интерфейс **иаусентикатеденкриптордескриптор** представляет тип, который знает, как экспортировать себя в XML.</span><span class="sxs-lookup"><span data-stu-id="036fe-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="036fe-132">Его API выглядит следующим образом.</span><span class="sxs-lookup"><span data-stu-id="036fe-132">Its API is as follows.</span></span>

* <span data-ttu-id="036fe-133">Експорттоксмл (): Ксмлсериализеддескрипторинфо</span><span class="sxs-lookup"><span data-stu-id="036fe-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="036fe-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="036fe-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="036fe-135">XML-сериализация</span><span class="sxs-lookup"><span data-stu-id="036fe-135">XML Serialization</span></span>

<span data-ttu-id="036fe-136">Основное различие между Иаусентикатеденкриптор и Иаусентикатеденкриптордескриптор заключается в том, что дескриптор знает, как создать шифратор и предоставить его допустимым аргументам.</span><span class="sxs-lookup"><span data-stu-id="036fe-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="036fe-137">Рассмотрим Иаусентикатеденкриптор, реализация которого основывается на SymmetricAlgorithm и KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="036fe-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="036fe-138">Задание шифратора заключается в использовании этих типов, но не обязательно знает, откуда поступили эти типы, поэтому не может действительно записать правильное описание способа повторного создания, если приложение перезапускается.</span><span class="sxs-lookup"><span data-stu-id="036fe-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="036fe-139">Дескриптор действует как более высокий уровень поверх этого.</span><span class="sxs-lookup"><span data-stu-id="036fe-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="036fe-140">Поскольку дескриптору известно, как создать экземпляр шифратора (например, он знает, как создать необходимые алгоритмы), он может сериализовать этот набор знаний в XML-форме, чтобы экземпляр шифратора можно было повторно создать после сброса приложения.</span><span class="sxs-lookup"><span data-stu-id="036fe-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="036fe-141">Дескриптор можно сериализовать с помощью своей подпрограммы Експорттоксмл.</span><span class="sxs-lookup"><span data-stu-id="036fe-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="036fe-142">Эта подпрограммы возвращает Ксмлсериализеддескрипторинфо, который содержит два свойства: представление XElement дескриптора и тип, который представляет [иаусентикатеденкриптордескриптордесериализер](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) , который можно использовать для восвыполнения этого дескриптора, учитывая соответствующий элемент XElement.</span><span class="sxs-lookup"><span data-stu-id="036fe-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="036fe-143">Сериализованный дескриптор может содержать конфиденциальные сведения, такие как материал криптографического ключа.</span><span class="sxs-lookup"><span data-stu-id="036fe-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="036fe-144">Система защиты данных имеет встроенную поддержку шифрования данных перед их сохранением в хранилище.</span><span class="sxs-lookup"><span data-stu-id="036fe-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="036fe-145">Чтобы воспользоваться этим преимуществом, дескриптор должен пометить элемент, содержащий конфиденциальную информацию, именем атрибута «Рекуиресенкриптион» (xmlns « <http://schemas.asp.net/2015/03/dataProtection> »), значением «true».</span><span class="sxs-lookup"><span data-stu-id="036fe-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="036fe-146">Для установки этого атрибута существует вспомогательный API.</span><span class="sxs-lookup"><span data-stu-id="036fe-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="036fe-147">Вызовите метод расширения XElement. Маркасрекуиресенкриптион (), расположенный в пространстве имен Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigУратионмодел.</span><span class="sxs-lookup"><span data-stu-id="036fe-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="036fe-148">Возможны также случаи, когда сериализованный дескриптор не содержит конфиденциальных сведений.</span><span class="sxs-lookup"><span data-stu-id="036fe-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="036fe-149">Снова рассмотрите возможность использования криптографического ключа, хранящегося в HSM.</span><span class="sxs-lookup"><span data-stu-id="036fe-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="036fe-150">Дескриптор не может записать ключевой материал при сериализации, так как HSM не будет предоставлять материал в виде открытого текста.</span><span class="sxs-lookup"><span data-stu-id="036fe-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="036fe-151">Вместо этого дескриптор может записать версию ключа, упакованную в ключ (если HSM поддерживает экспорт), или собственный уникальный идентификатор HSM для ключа.</span><span class="sxs-lookup"><span data-stu-id="036fe-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="036fe-152">иаусентикатеденкриптордескриптордесериализер</span><span class="sxs-lookup"><span data-stu-id="036fe-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="036fe-153">Интерфейс **иаусентикатеденкриптордескриптордесериализер** представляет тип, который знает, как десериализовать экземпляр Иаусентикатеденкриптордескриптор из XElement.</span><span class="sxs-lookup"><span data-stu-id="036fe-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="036fe-154">Он предоставляет один метод:</span><span class="sxs-lookup"><span data-stu-id="036fe-154">It exposes a single method:</span></span>

* <span data-ttu-id="036fe-155">Импортфромксмл (элемент XElement): Иаусентикатеденкриптордескриптор</span><span class="sxs-lookup"><span data-stu-id="036fe-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="036fe-156">Метод Импортфромксмл принимает элемент XElement, возвращенный [иаусентикатеденкриптордескриптор. експорттоксмл](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) , и создает эквивалент исходного иаусентикатеденкриптордескриптор.</span><span class="sxs-lookup"><span data-stu-id="036fe-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="036fe-157">Типы, реализующие Иаусентикатеденкриптордескриптордесериализер, должны иметь один из следующих двух открытых конструкторов:</span><span class="sxs-lookup"><span data-stu-id="036fe-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="036fe-158">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="036fe-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="036fe-159">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="036fe-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="036fe-160">Объект IServiceProvider, переданный в конструктор, может иметь значение null.</span><span class="sxs-lookup"><span data-stu-id="036fe-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="036fe-161">Фабрика верхнего уровня</span><span class="sxs-lookup"><span data-stu-id="036fe-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="036fe-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="036fe-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="036fe-163">Класс **алгорисмконфигуратион** представляет тип, который знает, как создавать экземпляры [иаусентикатеденкриптордескриптор](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="036fe-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="036fe-164">Он предоставляет один API.</span><span class="sxs-lookup"><span data-stu-id="036fe-164">It exposes a single API.</span></span>

* <span data-ttu-id="036fe-165">Креатеневдескриптор (): Иаусентикатеденкриптордескриптор</span><span class="sxs-lookup"><span data-stu-id="036fe-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="036fe-166">Алгорисмконфигуратион в качестве фабрики верхнего уровня.</span><span class="sxs-lookup"><span data-stu-id="036fe-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="036fe-167">Конфигурация служит шаблоном.</span><span class="sxs-lookup"><span data-stu-id="036fe-167">The configuration serves as a template.</span></span> <span data-ttu-id="036fe-168">Он создает оболочку для алгоритмной информации (например, эта конфигурация создает дескрипторы с помощью главного ключа AES-128-GCM), но еще не связана с конкретным ключом.</span><span class="sxs-lookup"><span data-stu-id="036fe-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="036fe-169">При вызове Креатеневдескриптор новый материал ключа создается исключительно для этого вызова, и создается новая Иаусентикатеденкриптордескриптор, которая упаковывает этот материал ключа и алгоритмную информацию, необходимую для использования материала.</span><span class="sxs-lookup"><span data-stu-id="036fe-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="036fe-170">Материал ключа может быть создан в программном обеспечении (и храниться в памяти), его можно создать и удерживать в АППАРАТном модуле безопасности и т. д.</span><span class="sxs-lookup"><span data-stu-id="036fe-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="036fe-171">Важным моментом является то, что любые два вызова Креатеневдескриптор никогда не должны создавать эквивалентные экземпляры Иаусентикатеденкриптордескриптор.</span><span class="sxs-lookup"><span data-stu-id="036fe-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="036fe-172">Тип Алгорисмконфигуратион выступает в качестве точки входа для подпрограмм создания ключей, таких как [Автоматическое пошаговое выполнение ключей](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="036fe-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="036fe-173">Чтобы изменить реализацию для всех последующих ключей, установите свойство Аусентикатеденкрипторконфигуратион в Кэйманажементоптионс.</span><span class="sxs-lookup"><span data-stu-id="036fe-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="036fe-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="036fe-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="036fe-175">Интерфейс **иаусентикатеденкрипторконфигуратион** представляет тип, который знает, как создавать экземпляры [иаусентикатеденкриптордескриптор](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="036fe-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="036fe-176">Он предоставляет один API.</span><span class="sxs-lookup"><span data-stu-id="036fe-176">It exposes a single API.</span></span>

* <span data-ttu-id="036fe-177">Креатеневдескриптор (): Иаусентикатеденкриптордескриптор</span><span class="sxs-lookup"><span data-stu-id="036fe-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="036fe-178">Иаусентикатеденкрипторконфигуратион в качестве фабрики верхнего уровня.</span><span class="sxs-lookup"><span data-stu-id="036fe-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="036fe-179">Конфигурация служит шаблоном.</span><span class="sxs-lookup"><span data-stu-id="036fe-179">The configuration serves as a template.</span></span> <span data-ttu-id="036fe-180">Он создает оболочку для алгоритмной информации (например, эта конфигурация создает дескрипторы с помощью главного ключа AES-128-GCM), но еще не связана с конкретным ключом.</span><span class="sxs-lookup"><span data-stu-id="036fe-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="036fe-181">При вызове Креатеневдескриптор новый материал ключа создается исключительно для этого вызова, и создается новая Иаусентикатеденкриптордескриптор, которая упаковывает этот материал ключа и алгоритмную информацию, необходимую для использования материала.</span><span class="sxs-lookup"><span data-stu-id="036fe-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="036fe-182">Материал ключа может быть создан в программном обеспечении (и храниться в памяти), его можно создать и удерживать в АППАРАТном модуле безопасности и т. д.</span><span class="sxs-lookup"><span data-stu-id="036fe-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="036fe-183">Важным моментом является то, что любые два вызова Креатеневдескриптор никогда не должны создавать эквивалентные экземпляры Иаусентикатеденкриптордескриптор.</span><span class="sxs-lookup"><span data-stu-id="036fe-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="036fe-184">Тип Иаусентикатеденкрипторконфигуратион выступает в качестве точки входа для подпрограмм создания ключей, таких как [Автоматическое пошаговое выполнение ключей](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="036fe-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="036fe-185">Чтобы изменить реализацию для всех последующих ключей, зарегистрируйте одноэлементную Иаусентикатеденкрипторконфигуратион в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="036fe-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
