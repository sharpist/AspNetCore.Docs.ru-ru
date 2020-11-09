---
title: Инструменты и файлы для скачивания — DevOps с ASP.NET Core и Azure
author: CamSoper
description: Инструменты и файлы для скачивания, необходимые для DevOps с ASP.NET Core и Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 8c7f10a6b6f8504efaba6054533aba034982820c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056573"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="4d0c5-103">Инструменты и файлы для скачивания</span><span class="sxs-lookup"><span data-stu-id="4d0c5-103">Tools and downloads</span></span>

<span data-ttu-id="4d0c5-104">В Azure имеется несколько интерфейсов для подготовки ресурсов и управления ими, например [портал Azure](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), а также Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="4d0c5-105">В данном руководстве применяется минимальный подход и по возможности используется Azure Cloud Shell, чтобы сократить количество необходимых действий.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="4d0c5-106">Однако для некоторых действий требуется использовать портал Azure.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4d0c5-107">предварительные требования</span><span class="sxs-lookup"><span data-stu-id="4d0c5-107">Prerequisites</span></span>

<span data-ttu-id="4d0c5-108">Требуются следующие подписки:</span><span class="sxs-lookup"><span data-stu-id="4d0c5-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="4d0c5-109">Azure &mdash; если у вас нет учетной записи, [зарегистрируйтесь для использования бесплатной пробной версии](https://azure.microsoft.com/free/dotnet/).</span><span class="sxs-lookup"><span data-stu-id="4d0c5-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/dotnet/).</span></span>
* <span data-ttu-id="4d0c5-110">Azure DevOps Services &mdash; порядок создания подписки Azure DevOps и организации описан в Главе 4.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="4d0c5-111">GitHub &mdash; если у вас нет учетной записи, [зарегистрируйтесь бесплатно](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="4d0c5-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="4d0c5-112">Требуются следующие средства:</span><span class="sxs-lookup"><span data-stu-id="4d0c5-112">The following tools are required:</span></span>

* <span data-ttu-id="4d0c5-113">[Git](https://git-scm.com/downloads) &mdash; для использования данного руководства рекомендуется понимание основных принципов Git.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="4d0c5-114">Изучите [документацию по Git](https://git-scm.com/doc), в частности [git remote](https://git-scm.com/docs/git-remote) и [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="4d0c5-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="4d0c5-115">[Пакет SDK для .NET Core](https://dotnet.microsoft.com/download/) &mdash; для создания и запуска примера приложения требуется версия 2.1.300 или более поздняя.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="4d0c5-116">Если Visual Studio был установлен вместе с рабочей нагрузкой **.NET Core для разработки кроссплатформенных приложений** , то пакет SDK для .NET Core уже установлен.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="4d0c5-117">Проверьте установку пакета SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="4d0c5-118">Откройте окно командной оболочки и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="4d0c5-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="4d0c5-119">Рекомендуемые средства (только для Windows)</span><span class="sxs-lookup"><span data-stu-id="4d0c5-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="4d0c5-120">Надежные инструменты Azure в составе [Visual Studio](https://visualstudio.microsoft.com) предоставляют графический пользовательский интерфейс для большинства функций, описанных в настоящем руководстве.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="4d0c5-121">Подойдет любой выпуск Visual Studio, включая бесплатный выпуск Visual Studio Community.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="4d0c5-122">Руководства созданы с целью продемонстрировать разработку, развертывание и DevOps как с Visual Studio, так и без него.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="4d0c5-123">Убедитесь, что в Visual Studio установлены следующие [рабочие нагрузки](/visualstudio/install/modify-visual-studio):</span><span class="sxs-lookup"><span data-stu-id="4d0c5-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="4d0c5-124">ASP.NET и веб-разработка.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="4d0c5-125">разработка Azure;</span><span class="sxs-lookup"><span data-stu-id="4d0c5-125">Azure development</span></span>
  * <span data-ttu-id="4d0c5-126">Кроссплатформенная разработка .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d0c5-126">.NET Core cross-platform development</span></span>
