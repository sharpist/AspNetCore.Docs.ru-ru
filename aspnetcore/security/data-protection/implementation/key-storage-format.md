---
title: Формат хранилища ключей в ASP.NET Core
author: rick-anderson
description: Сведения о реализации формата хранилища ключей ASP.NET Core Data Protection.
ms.author: riande
ms.date: 04/08/2020
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: daf86d3e3357d42ddad74d5e2f06e00e0e24db07
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631996"
---
# <a name="key-storage-format-in-aspnet-core"></a>Формат хранилища ключей в ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Объекты хранятся в XML-представлении в неактивных объектах. Каталогом по умолчанию для хранилища ключей является:

* Windows: *%Локалаппдата%\асп.нет\датапротектион-Кэйс\*
* macOS/Linux: *$Home/.АСПнет/датапротектион-Кэйс*

## <a name="the-key-element"></a>элемент \<key>;

Ключи существуют как объекты верхнего уровня в репозитории ключей. По ключам соглашения имеет **раздел filename-{GUID}. XML**, где {GUID} — это идентификатор ключа. Каждый такой файл содержит один ключ. Файл имеет следующий формат.

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

\<key>Элемент содержит следующие атрибуты и дочерние элементы:

* Идентификатор ключа. Это значение считается полномочным; имя файла — это просто ницети для удобочитаемости человека.

* Версия \<key> элемента, в настоящее время фиксированная в 1.

* Дата создания, активации и окончания срока действия ключа.

* \<descriptor>Элемент, содержащий сведения о реализации шифрования, прошедшего проверку подлинности, которая содержится в этом ключе.

В приведенном выше примере идентификатором ключа является {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, он был создан и активирован 19 марта 2015 и имеет время существования 90 дней. (Иногда Дата активации может быть немного раньше даты создания, как в этом примере. Это обусловлено nрекомендуетсяом работы API и небезопасным на практике.)

## <a name="the-descriptor-element"></a>элемент \<descriptor>;

Внешний \<descriptor> элемент содержит атрибут десериализертипе, который является именем с указанием сборки типа, реализующего иаусентикатеденкриптордескриптордесериализер. Этот тип отвечает за чтение внутреннего \<descriptor> элемента и анализ сведений, содержащихся в.

Конкретный формат \<descriptor> элемента зависит от реализации, прошедшей проверку подлинности, которая инкапсулируется ключом, и каждый тип десериализатора ожидает для этого несколько других форматов. Однако в общем случае этот элемент будет содержать алгоритмические сведения (имена, типы, OID и т. д.) и материал секретного ключа. В приведенном выше примере дескриптор указывает, что этот ключ заключает в оболочку шифрование AES-256-CBC и проверку HMACSHA256.

## <a name="the-encryptedsecret-element"></a>элемент \<encryptedSecret>;

Элемент ** &lt; енкриптедсекрет &gt; ** , содержащий зашифрованную форму секретного ключа, может присутствовать в случае [, если включено шифрование секретов при](xref:security/data-protection/implementation/key-encryption-at-rest)хранении. Атрибут `decryptorType` является именем с указанием сборки типа, который реализует [иксмлдекриптор](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Этот тип отвечает за чтение внутреннего элемента ** &lt; EncryptedKey &gt; ** и его расшифровку для восстановления исходного открытого текста.

Как и в `<descriptor>` случае с, конкретный формат `<encryptedSecret>` элемента зависит от используемого механизма шифрования неактивных данных. В приведенном выше примере главный ключ шифруется с помощью Windows DPAPI в соответствии с комментарием.

## <a name="the-revocation-element"></a>элемент \<revocation>;

Отзыва существуют как объекты верхнего уровня в репозитории ключей. При отзыве соглашения используется отзыв имени файла **: {timestamp}. XML** (для отмены всех ключей до определенной даты) или **отзыв: {GUID}. XML** (для отзыва определенного ключа). Каждый файл содержит один \<revocation> элемент.

Для отзыва отдельных ключей содержимое файла будет следующим.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

В этом случае отменяется только указанный ключ. Если идентификатор ключа — "*", то, как показано в следующем примере, будут отозваны все ключи, Дата создания которых предшествует указанной дате отзыва.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

\<reason>Элемент никогда не считывается системой. Это просто удобное место для того, чтобы сохранить понятную пользователю причину отзыва.
