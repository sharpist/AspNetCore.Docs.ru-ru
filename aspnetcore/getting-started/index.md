---
title: Начало работы с ASP.NET Core
author: rick-anderson
description: Краткий учебник, в котором с помощью ASP.NET Core создается и запускается простое приложение Hello World.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: getting-started
ms.openlocfilehash: 565cde233d6fc5f085ae1709e167e73980d5c3c2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776237"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Учебник. Начало работы с ASP.NET Core

В этом руководстве показано, как с помощью .NET Core CLI создать и запустить веб-приложение ASP.NET Core.

Вы научитесь:

> [!div class="checklist"]
> * создавать проект веб-приложения;
> * устанавливать доверие к сертификату разработки;
> * Запустите приложение.
> * редактировать страницу Razor.

В итоге вы получите рабочее веб-приложение на локальном компьютере.

![Домашняя страница веб-приложения](_static/home-page.png)

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a>Создание проекта веб-приложения

Откройте окно командной оболочки и введите следующую команду:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

Предыдущая команда позволяет:

* создать веб-сайт;  
* с помощью параметра `-o aspnetcoreapp` создать каталог *aspnetcoreapp* с исходными файлами приложения.

### <a name="trust-the-development-certificate"></a>Установка доверия к сертификату разработки

Установите доверие к сертификату разработки HTTPS.

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

Приведенная выше команда отображает следующее диалоговое окно.

![Диалоговое окно "Предупреждение о безопасности"](~/getting-started/_static/cert.png)

Выберите **Да**, если согласны доверять сертификату разработки.

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

Приведенная выше команда отображает следующее сообщение.

*Запрошено доверие к сертификату разработки HTTPS. Если сертификат не является доверенным, выполните следующую команду:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

Эта команда может запросить пароль для установки сертификата в системной цепочке ключей. Введите пароль, если согласны доверять сертификату разработки.

# <a name="linux"></a>[Linux](#tab/linux)

Просмотрите документацию по дистрибутиву Linux, чтобы узнать, как установить отношение доверия к сертификату разработки HTTPS.

---

Дополнительные сведения см. в разделе [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) (Настройка доверия к сертификату разработки HTTPS ASP.NET Core).

## <a name="run-the-app"></a>Запуск приложения

Выполните следующие команды:

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

Когда в командной оболочке будет показано, что приложение запущено, откройте страницу `https://localhost:5001`.

## <a name="edit-a-razor-page"></a>Изменение страницы Razor

Откройте *Pages/Index.cshtml*, а затем измените и сохраните страницу, добавив выделенное исправление:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Перейдите на страницу `https://localhost:5001`, обновите ее и проверьте, отобразились ли изменения.

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы узнали, как:

> [!div class="checklist"]
> * создавать проект веб-приложения;
> * устанавливать доверие к сертификату разработки;
> * Запустите проект.
> * вносить изменения.

Дополнительные сведения об ASP.NET Core см. в разделе рекомендуемой схемы обучения в вводной статье:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
