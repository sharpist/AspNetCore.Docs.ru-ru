---
title: Публикация приложения ASP.NET Core в Azure с помощью Visual Studio Code
author: rick-anderson
description: Сведения о публикации приложения ASP.NET Core в службе приложений Azure с помощью Visual Studio Code
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 43a2c4fed3a8873f64259167d3949d720f2c5dab
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406749"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Публикация приложения ASP.NET Core в Azure с помощью Visual Studio Code

Автор: [Рикардо Серрадас](https://twitter.com/ricardoserradas) (Ricardo Serradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Сведения об устранении проблем развертывания службы приложений см. в статье <xref:test/troubleshoot-azure-iis>.

## <a name="intro"></a>Введение

С помощью этого руководства вы узнаете, как создать приложение MVC ASP.Net Core и развернуть его в Visual Studio Code.

## <a name="set-up"></a>Настройка

- Создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/dotnet/), если у вас ее нет.
- Установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).
- Установка [Visual Studio Code](https://code.visualstudio.com/Download)
  - Установите [расширение C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) для Visual Studio Code.
  - Установите [расширение службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) для Visual Studio Code и настройте его перед продолжением

## <a name="create-an-aspnet-core-mvc-project"></a>Создание проекта MVC ASP.Net Core

Используя терминал, перейдите в папку для создания проекта и используйте следующую команду:

```dotnetcli
dotnet new mvc
```

Вы получите структуру папок, аналогичную следующей:

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a>Открытие проекта с помощью Visual Studio Code

После создания проекта его можно открыть с помощью Visual Studio Code, используя один из следующих способов.

### <a name="through-the-command-line"></a>С помощью командной строки

Используйте следующую команду в папке, где создан проект:

```cmd
> code .
```

Если приведенная ниже команда не работает, убедитесь в правильной настройке установки (перейдите по [этой ссылке](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform)).

### <a name="through-visual-studio-code-interface"></a>С помощью интерфейса Visual Studio Code

- Откройте Visual Studio Code.
- В меню выберите `File > Open Folder`.
- Выберите корень папки, в которой создан проект MVC.

При открытии папки проекта вы получите сообщение о том, что отсутствуют необходимые ресурсы для сборки и отладки. Примите приглашение справки, чтобы добавить их.

![Интерфейс Visual Studio Code с загруженным проектом](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

Папка `.vscode` будет создана в структуре проекта. Она будет содержать следующие файлы:

```cmd
launch.json
tasks.json
```

Это служебные файлы, которые помогут вам создать веб-приложение .NET Core и выполнить его отладку.

## <a name="run-the-app"></a>Запуск приложения

Перед развертыванием приложения в Azure убедитесь, что оно работает правильно на локальном компьютере.

- Нажмите клавишу F5, чтобы запустить проект.

Веб-приложение начнет выполняться на новой вкладке браузера по умолчанию. Сразу после его запуска может появиться предупреждение о конфиденциальности. Это происходит потому, что ваше приложение запускается с помощью HTTP или HTTPS и оно переходит к конечной точке HTTPS по умолчанию.

![Предупреждение о конфиденциальности при локальной отладке приложения](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Чтобы сохранить сеанс отладки, нажмите кнопку `Advanced`, а затем `Continue to localhost (unsafe)`.

## <a name="generate-the-deployment-package-locally"></a>Создание пакета развертывания локально

- Откройте терминал Visual Studio Code.
- Используйте следующую команду, чтобы создать пакет `Release` во вложенной папке с именем `publish`:
  - `dotnet publish -c Release -o ./publish`
- Новая папка `publish` будет создана в структуре проекта.

![Структура папки публикации](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Публикация в службу приложений Azure

Используя расширение службы приложений Azure для Visual Studio Code, выполните следующие действия, чтобы опубликовать веб-сайт непосредственно в службе приложений Azure.

### <a name="if-youre-creating-a-new-web-app"></a>При создании нового веб-приложения

- Щелкните папку `publish` правой кнопкой мыши и выберите `Deploy to Web App...`.
- Выберите подписку, в которой нужно создать веб-приложение.
- Выберите `Create New Web App`.
- Введите имя веб-приложения.

Расширение создаст веб-приложение и автоматически начнет развертывание пакета для приложения. После завершения развертывания щелкните `Browse Website`, чтобы проверить развертывание.

![Сообщение об успешно выполненном развертывании](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

После нажатия кнопки `Browse Website` вы перейдете к веб-сайту с помощью браузера по умолчанию:

![Новое веб-приложение успешно развернуто](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>При развертывании в существующее веб-приложение

- Щелкните папку `publish` правой кнопкой мыши и выберите `Deploy to Web App...`.
- Выберите подписку, в которой находится существующее веб-приложение.
- Выберите веб-приложение в списке.
- Visual Studio Code выдаст запрос на перезапись существующего содержимого. Щелкните `Deploy` для подтверждения.

Расширение выполнит развертывание обновленного содержимого веб-приложения. После этого щелкните `Browse Website`, чтобы проверить развертывание.

![Существующее веб-приложение успешно развернуто](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Следующие шаги

- [Создание первого конвейера DevOps в Azure](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [служба приложений Azure](/azure/app-service/app-service-web-overview);
- [Группа ресурсов Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
