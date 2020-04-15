---
title: Образцы аутентификации для ASP.NET ядра
author: rick-anderson
description: Предоставляет ссылки на образцы аутентификации в репозитории ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308219"
---
# <a name="authentication-samples-for-aspnet-core"></a>Образцы аутентификации для ASP.NET ядра

Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[Репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore) содержит следующие образцы аутентификации в папке *AspNetCore/src/Security/samples:*

* [Преобразование утверждений](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Проверка подлинности файлов cookie](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Поставщик пользовательских полисов - IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Динамические схемы проверки подлинности и варианты](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Внешние претензии](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [Выбор между файлами cookie и другой схемой проверки подлинности на основе запроса](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Ограничение доступа к статическим файлам](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Запуск шаблонов

* Выберите [ветку.](https://github.com/dotnet/AspNetCore) Например: `release/3.1`
* Клон или скачать [репозиторий core ASP.NET.](https://github.com/dotnet/AspNetCore)
* Убедитесь, что вы установили версию [.NET Core SDK,](https://dotnet.microsoft.com/download/dotnet-core) соответствующую клону ASP.NET репозитория Core.
* Перейдите к образцу в *AspNetCore/src/Security/samples* и запустите образец с `dotnet run`помощью .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore) содержит следующие образцы аутентификации в папке *AspNetCore/src/Security/samples:*

* [Преобразование утверждений](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Проверка подлинности файлов cookie](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Поставщик пользовательских полисов - IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Динамические схемы проверки подлинности и варианты](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Внешние претензии](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Выбор между файлами cookie и другой схемой проверки подлинности на основе запроса](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Ограничение доступа к статическим файлам](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Запуск шаблонов

* Выберите [ветку.](https://github.com/dotnet/AspNetCore) Например: `release/2.2`
* Клон или скачать [репозиторий core ASP.NET.](https://github.com/dotnet/AspNetCore)
* Убедитесь, что вы установили версию [.NET Core SDK,](https://dotnet.microsoft.com/download/dotnet-core) соответствующую клону ASP.NET репозитория Core.
* Перейдите к образцу в *AspNetCore/src/Security/samples* и запустите образец с `dotnet run`помощью .

::: moniker-end
