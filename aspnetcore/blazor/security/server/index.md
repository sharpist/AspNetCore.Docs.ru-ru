---
title: Безопасные приложения ASP.NET Core Blazor Server
author: guardrex
description: Сведения о защите приложений Blazor Server как приложений ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: a8604ca6ea60386bb3c54c950205ee695d37c689
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103141"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Безопасные приложения ASP.NET Core Blazor Server

Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)

## <a name="blazor-server-project-template"></a>Шаблон проекта "Сервер Blazor"

Шаблон проекта "Сервер Blazor" можно настроить для проверки подлинности при создании проекта.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Следуйте указаниям по работе с Visual Studio (<xref:blazor/get-started>), чтобы создать проект Blazor Server с механизмом проверки подлинности.

Выбрав шаблон **Серверное приложение Blazor** в диалоговом окне **Создать веб-приложение ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.

Откроется диалоговое окно с тем же набором механизмов аутентификации, которые доступны для других проектов ASP.NET Core.

* **Без аутентификации**.
* **Учетные записи отдельных пользователей**. Учетные записи пользователей могут храниться:
  * Внутри приложения с помощью системы [Identity](xref:security/authentication/identity) в ASP.NET Core.
  * в [Azure AD B2C](xref:security/authentication/azure-ad-b2c);
* **рабочие или учебные учетные записи**.
* **Проверка подлинности Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Следуйте указаниям по работе с Visual Studio Code (<xref:blazor/get-started>), чтобы создать проект Blazor Server с механизмом проверки подлинности:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.

| Механизм аутентификации | Описание |
| ------------------------ | ----------- |
| `None` (по умолчанию)         | без аутентификации; |
| `Individual`             | Хранение пользователей в приложении с помощью ASP.NET Core Identity |
| `IndividualB2C`          | Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c). |
| `SingleOrg`              | Корпоративная аутентификация для отдельного клиента |
| `MultiOrg`               | Корпоративная аутентификация для нескольких клиентов |
| `Windows`                | Проверка подлинности Windows |

С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:

* Создайте папку для проекта.
* Дайте проекту имя.

Подробнее см. статью о команде [dotnet new](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. Следуйте инструкциям по работе с Visual Studio для Mac (<xref:blazor/get-started>).

1. На шаге **Настройка нового серверного приложения Blazor** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.

1. Приложение будет создано для отдельных пользователей, хранимых в приложении с помощью ASP.NET Core Identity.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

Выполните указания для .NET Core CLI из статьи <xref:blazor/get-started>, чтобы создать новый проект "Сервер Blazor", используя следующий механизм проверки подлинности:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.

| Механизм аутентификации | Описание |
| ------------------------ | ----------- |
| `None` (по умолчанию)         | без аутентификации; |
| `Individual`             | Хранение пользователей в приложении с помощью ASP.NET Core Identity |
| `IndividualB2C`          | Пользователи, сохраненные в [Azure AD B2C](xref:security/authentication/azure-ad-b2c). |
| `SingleOrg`              | Корпоративная аутентификация для отдельного клиента |
| `MultiOrg`               | Корпоративная аутентификация для нескольких клиентов |
| `Windows`                | Проверка подлинности Windows |

С помощью параметра `-o|--output` команда использует значение, указанное для заполнителя `{APP NAME}`, чтобы:

* Создайте папку для проекта.
* Дайте проекту имя.

Подробнее см. статью о команде [dotnet new](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.

---

## <a name="secure-an-existing-app"></a>Защита существующего приложения

Blazor Серверные приложения настраиваются для обеспечения безопасности так же, как приложения ASP.NET Core. Дополнительные сведения см. в статьях в документе <xref:security/index>.

## <a name="scaffold-identity"></a>Формирование шаблонов Identity

Сформируйте шаблоны Identity для проекта "Сервер Blazor":

* [Без существующей авторизации](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [С авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).
