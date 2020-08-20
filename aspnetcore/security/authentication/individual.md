---
title: Статьи на основе проектов ASP.NET Core, созданных с учетными записями отдельных пользователей
author: rick-anderson
description: Ознакомьтесь с статьями, основанными на ASP.NET Core проектах, созданных с учетными записями отдельных пользователей.
ms.author: riande
ms.date: 12/11/2019
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
uid: security/authentication/individual
ms.openlocfilehash: 0cbde7cf0be830dab9dd094df6d102d81f9f0949
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632620"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Статьи на основе проектов ASP.NET Core, созданных с учетными записями отдельных пользователей

ASP.NET Core Identity включается в шаблоны проектов Visual Studio с параметром "индивидуальные учетные записи пользователей".

Шаблоны проверки подлинности доступны в .NET Core CLI с помощью `-au Individual` :

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

См. [эту ошибку GitHub](https://github.com/dotnet/AspNetCore/issues/5833) для проверки подлинности веб-API.

<a name="no"></a>

## <a name="no-authentication"></a>Без аутентификации

Проверка подлинности указывается в .NET Core CLI с `-au` параметром. В Visual Studio диалоговое окно **Изменение проверки подлинности** доступно для новых веб-приложений. По умолчанию для новых веб-приложений в Visual Studio **Проверка подлинности не**выполняется.

Проекты, созданные без проверки подлинности:

* Не следует включать веб-страницы и пользовательский интерфейс для входа и выхода.
* Не содержать код проверки подлинности.

<a name="win"></a>

## <a name="windows-authentication"></a>Проверка подлинности Windows

Проверка подлинности Windows указана для новых веб-приложений в .NET Core CLI с `-au Windows` параметром. В Visual Studio диалоговое окно **Изменение проверки подлинности** предоставляет параметры **проверки подлинности Windows** .

Если выбрана проверка подлинности Windows, приложение настроено для использования [модуля IIS проверки подлинности Windows](xref:host-and-deploy/iis/modules). Проверка подлинности Windows предназначена для веб-сайтов интрасети.

## <a name="dotnet-new-webapp-authentication-options"></a>Параметры проверки подлинности DotNet New webapp

В следующей таблице приведены параметры проверки подлинности, доступные для новых веб-приложений.

| Параметр | Тип проверки подлинности | Ссылка на дополнительные сведения |
 | ----------------- | ------------ | ---------- |
| Нет            |  без аутентификации; | | 
| Индивидуальное лицо      |  Индивидуальная проверка подлинности | <xref:security/authentication/identity>
| IndividualB2C   |  Индивидуальная проверка подлинности, размещенная в облаке, с Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| синглеорг       |  Корпоративная аутентификация для отдельного клиента | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| мултиорг        |  Корпоративная аутентификация для нескольких клиентов | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Проверка подлинности Windows | [Проверка подлинности Windows](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Параметры проверки подлинности New webapp в Visual Studio

В следующей таблице показаны параметры проверки подлинности, доступные при создании нового веб-приложения с помощью Visual Studio.

| Параметр | Тип проверки подлинности | Ссылка на дополнительные сведения |
 | ----------------- | ------------ | ---------- |
| Нет            |  без аутентификации; | | 
| Индивидуальные учетные записи пользователей и хранение учетных записей пользователей в приложении |  Индивидуальная проверка подлинности | <xref:security/authentication/identity> |
| Учетные записи отдельных пользователей и подключение к существующему хранилищу пользователей в облаке |  Индивидуальная проверка подлинности, размещенная в облаке, с Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| Рабочее или учебное облако/Единая Организация  |  Корпоративная аутентификация для отдельного клиента | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Рабочее или учебное облако/несколько Организации |  Корпоративная аутентификация для нескольких клиентов | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Проверка подлинности Windows | [Проверка подлинности Windows](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>Дополнительные ресурсы

В следующих статьях показано, как использовать код, созданный в шаблонах ASP.NET Core, использующих отдельные учетные записи пользователей.

* [Подтверждение учетной записи и восстановление пароля в ASP.NET Core](xref:security/authentication/accconfirm)
* [Создание приложения ASP.NET Core с данными пользователя, защищенными с помощью авторизации](xref:security/authorization/secure-data)
