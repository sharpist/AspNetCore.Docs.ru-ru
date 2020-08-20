---
title: Инструменты и файлы для скачивания — DevOps с ASP.NET Core и Azure
author: CamSoper
description: Инструменты и файлы для скачивания, необходимые для DevOps с ASP.NET Core и Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 3242031f07f3fa344422db4591ea1a244b6723dd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625249"
---
# <a name="tools-and-downloads"></a>Инструменты и файлы для скачивания

В Azure имеется несколько интерфейсов для подготовки ресурсов и управления ими, например [портал Azure](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), а также Visual Studio. В данном руководстве применяется минимальный подход и по возможности используется Azure Cloud Shell, чтобы сократить количество необходимых действий. Однако для некоторых действий требуется использовать портал Azure.

## <a name="prerequisites"></a>предварительные требования

Требуются следующие подписки:

* Azure &mdash; если у вас нет учетной записи, [зарегистрируйтесь для использования бесплатной пробной версии](https://azure.microsoft.com/free/dotnet/).
* Azure DevOps Services &mdash; порядок создания подписки Azure DevOps и организации описан в Главе 4.
* GitHub &mdash; если у вас нет учетной записи, [зарегистрируйтесь бесплатно](https://github.com/join).

Требуются следующие средства:

* [Git](https://git-scm.com/downloads) &mdash; для использования данного руководства рекомендуется понимание основных принципов Git. Изучите [документацию по Git](https://git-scm.com/doc), в частности [git remote](https://git-scm.com/docs/git-remote) и [git push](https://git-scm.com/docs/git-push).
* [Пакет SDK для .NET Core](https://dotnet.microsoft.com/download/) &mdash; для создания и запуска примера приложения требуется версия 2.1.300 или более поздняя. Если Visual Studio был установлен вместе с рабочей нагрузкой **.NET Core для разработки кроссплатформенных приложений**, то пакет SDK для .NET Core уже установлен.

    Проверьте установку пакета SDK для .NET Core. Откройте окно командной оболочки и выполните следующую команду:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Рекомендуемые средства (только для Windows)

* Надежные инструменты Azure в составе [Visual Studio](https://visualstudio.microsoft.com) предоставляют графический пользовательский интерфейс для большинства функций, описанных в настоящем руководстве. Подойдет любой выпуск Visual Studio, включая бесплатный выпуск Visual Studio Community. Руководства созданы с целью продемонстрировать разработку, развертывание и DevOps как с Visual Studio, так и без него.

  Убедитесь, что в Visual Studio установлены следующие [рабочие нагрузки](/visualstudio/install/modify-visual-studio):

  * ASP.NET и веб-разработка.
  * разработка Azure;
  * Кроссплатформенная разработка .NET Core.
