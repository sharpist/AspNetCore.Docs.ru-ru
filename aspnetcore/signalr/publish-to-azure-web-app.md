---
title: Публикация ASP.NET Core SignalR приложения в службе приложений Azure
author: bradygaster
description: Узнайте, как опубликовать приложение ASP.NET Core SignalR в службе приложений Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/02/2020
no-loc:
- appsettings.json
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 8e6d36fe0b38486f94078b8f9cf12b852da7e0d9
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234518"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a>Публикация ASP.NET Core SignalR приложения в службе приложений Azure

По [Брейди Гастер](https://twitter.com/bradygaster)

[Служба приложений Azure](/azure/app-service/app-service-web-overview) — это служба платформы [облачных вычислений Майкрософт](https://azure.microsoft.com/) для размещения веб-приложений, включая ASP.NET Core.

> [!NOTE]
> В этой статье описывается публикация ASP.NET Core SignalR приложения из Visual Studio. Дополнительные сведения см. в статье [ SignalR служба для Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Публикация приложения

В этой статье описывается публикация с помощью средств в Visual Studio. Visual Studio Code пользователи могут использовать команды [Azure CLI](/cli/azure) для публикации приложений в Azure. Дополнительные сведения см. в статье [публикация ASP.NET Core приложения в Azure с помощью средств командной строки](/azure/app-service/app-service-web-get-started-dotnet).

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Опубликовать** .

1. Убедитесь, что в диалоговом окне **Выбор целевого объекта публикации** выбран параметр **служба приложений** и **создать новые** .

1. Выберите **создать профиль** из раскрывающегося списка кнопка " **опубликовать** ".

   Введите сведения, описанные в следующей таблице в диалоговом окне **Создание службы приложений** , и выберите **создать** .

   | Элемент               | Описание |
   | ------------------ | ----------- |
   | **имя** ;           | Уникальное имя приложения. |
   | **Подписка**   | Подписка Azure, используемая приложением. |
   | **Группа ресурсов** | Группа связанных ресурсов, к которым принадлежит приложение. |
   | **План размещения**   | Тарифный план для веб-приложения. |

1. В разделе **Service Dependencies (зависимости служб** ) выберите **SignalR служба Azure** . Нажмите **+** кнопку:

   ![Область зависимостей, отображающая выбор Azure::: No-Loc (SignalR)::: Service в раскрывающемся списке "Добавить"](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. В диалоговом окне **SignalR службы Azure** выберите **создать новый SignalR экземпляр службы Azure** .

1. Укажите **имя** , **группу ресурсов** и **Расположение** . Вернитесь в диалоговое окно **SignalR службы Azure** и нажмите кнопку **добавить** .

Visual Studio выполняет следующие задачи:

* Создает профиль публикации, содержащий параметры публикации.
* Создает *веб-приложение Azure* с предоставленными сведениями.
* Публикует приложение.
* Запускает браузер, который загружает веб-приложение.

URL-адрес приложения имеет формат `{APP SERVICE NAME}.azurewebsites.net` . Например, приложение с именем `SignalRChatApp` имеет URL-адрес `https://signalrchatapp.azurewebsites.net` .

Если при развертывании приложения, предназначенного для предварительной версии .NET Core, возникает ошибка HTTP *502,2-Bad Gateway* , см. статью [развертывание ASP.NET Core предварительной версии в службе приложений Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) .

## <a name="configure-the-app-in-azure-app-service"></a>Настройка приложения в службе приложений Azure

> [!NOTE]
> *Этот раздел относится только к приложениям, которые не используют SignalR службу Azure.*
>
> Если приложение использует SignalR службу Azure, служба приложений не требует настройки сходства маршрутизации запросов приложений (ARR) и веб-сокетов, описанных в этом разделе. Клиенты соединяют свои веб-сокеты со SignalR службой Azure, а не напрямую с приложением.

Для приложений, размещенных без SignalR службы Azure, включите:

* [Соответствие ARR] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie -(ARR- cookie ) -for-Azure-web-apps.html) для маршрутизации запросов от пользователя к тому же экземпляру службы приложений. Значение по умолчанию — **On** .
* [Веб-сокеты](xref:fundamentals/websockets) для обеспечения функционирования транспорта веб-сокетов. Значение по умолчанию — **Off** .

1. В портал Azure перейдите к веб-приложению в **службах приложений** .
1. Откройте **Configuration**  >  **Общие параметры** конфигурации.
1. Установите для **веб-сокетов** значение **вкл** .
1. Убедитесь, что для параметра **сходство arr** установлено значение **вкл** .

## <a name="app-service-plan-limits"></a>Ограничения плана службы приложений

Веб-сокеты и другие транспорты ограничены в зависимости от выбранного плана службы приложений. Дополнительные сведения см. в разделе *ограничения облачных служб Azure* и *ограничения службы приложений* статьи [Подписка Azure и ограничения службы, квоты и ограничения](/azure/azure-subscription-service-limits#app-service-limits) .

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Что такое служба SignalR Azure?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Публикация приложения ASP.NET Core в Azure с использованием средств командной строки](/azure/app-service/app-service-web-get-started-dotnet)
* [Размещение и развертывание приложений ASP.NET Core Preview в Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
