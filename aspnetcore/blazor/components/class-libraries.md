---
title: Библиотеки классов компонентов Razor в ASP.NET Core
author: guardrex
description: Сведения о включении компонентов в приложения Blazor из внешней библиотеки компонентов.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: 0c6f1330a5bac8ab37c957c5ed4e81678fe1f27d
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242515"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>Библиотеки классов компонентов Razor в ASP.NET Core

Автор: [Саймон Тиммс](https://github.com/stimms) (Simon Timms)

Компоненты можно совместно использовать в проектах посредством [библиотеки классов Razor](xref:razor-pages/ui-class). *Библиотека классов компонентов Razor* может включаться из следующих источников:

* другого проекта в решении;
* пакета NuGet;
* связанной библиотеки .NET.

Так же как компоненты являются обычными типами .NET, компоненты, предоставляемые RCL, представляют собой обычные сборки .NET.

## <a name="create-an-rcl"></a>Создание библиотеки RCL

Следуйте указаниям из статьи <xref:blazor/get-started>, чтобы настроить среду для Blazor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Создайте новый проект.
1. Выберите **Библиотека классов Razor** . Выберите **Далее**.
1. В диалоговом окне **Создать библиотеку классов Razor** щелкните **Создать**.
1. В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию. В примерах в этой статье используется имя проекта `MyComponentLib1`. Нажмите кнопку **создания**.
1. Добавьте библиотеку RCL в решение.
   1. Щелкните решение правой кнопкой мыши. Выберите **Добавить** > **Существующий проект**.
   1. Перейдите к файлу проекта RCL.
   1. Выберите файл проекта RCL (`.csproj`).
1. Добавьте ссылку на RCL из приложения.
   1. Щелкните проект приложения правой кнопкой мыши. Выберите **Добавить** > **Ссылка**.
   1. Выберите проект RCL. Щелкните **ОК**.

> [!NOTE]
> Если при создании библиотеки RCL на основе шаблона был установлен флажок **Представления и страницы поддержки**, также добавьте в корневой каталог созданного проекта файл `_Imports.razor` со следующим содержимым, чтобы включить разработку компонента Razor:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Вручную добавьте файл в корневой каталог созданного проекта.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

1. Используйте шаблон **Библиотека классов Razor** (`razorclasslib`) с командой [`dotnet new`](/dotnet/core/tools/dotnet-new) в командной оболочке. В приведенном ниже примере создается библиотека RCL с именем `MyComponentLib1`. Папка с `MyComponentLib1` создается автоматически при выполнении команды.

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Если при создании библиотеки RCL на основе шаблона используется параметр `-s|--support-pages-and-views`, также добавьте в корневой каталог созданного проекта файл `_Imports.razor` со следующим содержимым, чтобы включить разработку компонента Razor:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Вручную добавьте файл в корневой каталог созданного проекта.

1. Чтобы добавить библиотеку в существующий проект, используйте команду [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) в командной оболочке. В приведенном ниже примере в приложение добавляется библиотека RCL. Выполните следующую команду из папки проекта приложения, указав путь к библиотеке:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Использование компонента из библиотеки

Чтобы использовать компоненты, определенные в библиотеке в другом проекте, используйте один из описанных ниже подходов.

* Используйте полное имя типа с пространством имен.
* Используйте директиву [`@using`](xref:mvc/views/razor#using) Razor. Отдельные компоненты можно добавлять по имени.

В приведенном ниже примере `MyComponentLib1` — это библиотека компонентов, содержащая компонент `SalesReport`.

На компонент `SalesReport` можно ссылаться, используя полное имя типа с пространством имен:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

На компонент также можно ссылаться, если библиотека перенесена в область действия с помощью директивы `@using`:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Чтобы сделать компоненты библиотеки доступными для всего проекта, включите директиву `@using MyComponentLib1` в файл `_Import.razor` верхнего уровня. Чтобы применить пространство имен к одной странице или набору страниц в папке, добавьте директиву в файл `_Import.razor` на любом уровне.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Создание библиотеки классов компонентов Razor со статическими ресурсами

Библиотека RCL может включать в себя статические ресурсы. Такие ресурсы доступны любому приложению, использующему библиотеку. Дополнительные сведения см. в разделе <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Сборка, упаковка и отправка в NuGet

Так как библиотеки компонентов являются стандартными библиотеками .NET, их упаковка и передача в NuGet не отличается от упаковки и передачи любых других библиотек. Упаковка выполняется с помощью команды [`dotnet pack`](/dotnet/core/tools/dotnet-pack) в командной оболочке:

```dotnetcli
dotnet pack
```

Чтобы отправить пакет в NuGet, используйте команду [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) в командной оболочке.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:razor-pages/ui-class>
* [Добавление файла конфигурации компоновщика XML в библиотеку](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
