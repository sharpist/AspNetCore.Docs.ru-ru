---
title: Веб-пакет SDK для ASP.NET Core
author: Rick-Anderson
description: Общие сведения о пакете Microsoft.NET.Sdk.Web.
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: 163bc2679deda449f97cb4e50da1093e6b1edda4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634817"
---
# <a name="aspnet-core-web-sdk"></a>Веб-пакет SDK для ASP.NET Core

### <a name="overview"></a>Обзор

`Microsoft.NET.Sdk.Web` — это [пакет SDK проекта MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) для разработки приложений ASP.NET Core. Приложения ASP.NET Core можно создавать и без этого пакета SDK, однако веб-пакет SDK дает следующие преимущества:

* обеспечивает максимальное удобство разработки;
* рекомендуется для большинства пользователей.

Используйте Web.SDK в проекте:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Возможности, обеспечиваемые веб-пакетом SDK:

* Проекты, предназначенные для .NET Core 3.0 или более поздней версии, имеют неявные ссылки на следующие компоненты:

  * [общая платформа ASP.NET Core](xref:fundamentals/metapackage-app);
  * [анализаторы](/visualstudio/extensibility/getting-started-with-roslyn-analyzers), предназначенные для создания приложений ASP.NET Core.
* Веб-пакет SDK импортирует целевые объекты MSBuild, которые позволяют использовать профили публикации и выполнять публикацию с помощью WebDeploy.

### <a name="properties"></a>Свойства

| Свойство. | Описание |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Отключает неявную ссылку на общую платформу `Microsoft.AspNetCore.App`. |
| `DisableImplicitAspNetCoreAnalyzers` | Отключает неявную ссылку на анализаторы ASP.NET Core. |
| `DisableImplicitComponentsAnalyzers` | Отключает неявную ссылку на анализаторы компонентов Razor при сборке приложений Blazor (серверных). |
