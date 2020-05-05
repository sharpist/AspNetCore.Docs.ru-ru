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
uid: security/blazor/server/index
ms.openlocfilehash: bbd8b6fcd357b8929bf097450854d98fbea2570e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772639"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Безопасные приложения ASP.NET Core Blazor Server

Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)

## <a name="blazor-server-project-template"></a>Шаблон проекта Blazor Server

Шаблон Blazor Server позволяет автоматически настроить аутентификацию при создании проекта.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Следуйте указаниям по работе с Visual Studio (<xref:blazor/get-started>), чтобы создать проект Blazor на стороне сервера с механизмом аутентификации.

Выбрав шаблон **Серверное приложение Blazor** в диалоговом окне **Создать веб-приложение ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.

Откроется диалоговое окно с тем же набором механизмов аутентификации, которые доступны для других проектов ASP.NET Core.

* **Без аутентификации**.
* **Учетные записи отдельных пользователей**, которые можно хранить следующим образом:
  * в приложении с помощью системы [удостоверений](xref:security/authentication/identity) ASP.NET Core;
  * в [Azure AD B2C](xref:security/authentication/azure-ad-b2c);
* **рабочие или учебные учетные записи**.
* **Проверка подлинности Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Следуйте указаниям по работе с Visual Studio Code (<xref:blazor/get-started>), чтобы создать проект Blazor на стороне сервера с механизмом аутентификации.

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.

| Механизм аутентификации | Описание |
| ------------------------ | ----------- |
| `None` (по умолчанию)         | без аутентификации; |
| `Individual`             | Пользователи, сохраненные в приложении с помощью ASP.NET Core Identity |
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

1. На шаге **Настройка нового приложения Blazor Server** выберите **Индивидуальная аутентификация (в приложении)** в раскрывающемся списке **Аутентификация**.

1. Приложение создается для отдельных пользователей, сохраненных в приложении с удостоверением ASP.NET Core.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

Следуйте инструкциям по работе с .NET Core CLI (<xref:blazor/get-started>), чтобы создать проект Blazor Server с механизмом аутентификации.

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Допустимые значения аутентификации (`{AUTHENTICATION}`) перечислены в следующей таблице.

| Механизм аутентификации | Описание |
| ------------------------ | ----------- |
| `None` (по умолчанию)         | без аутентификации; |
| `Individual`             | Пользователи, сохраненные в приложении с помощью ASP.NET Core Identity |
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
