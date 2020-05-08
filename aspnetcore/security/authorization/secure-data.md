---
title: Создание приложения ASP.NET Core с данными пользователя, защищенными с помощью авторизации
author: rick-anderson
description: Узнайте, как создать приложение Razor страниц с данными пользователя, защищенными с помощью авторизации. Включает протокол HTTPS, проверку подлинности Identity, Безопасность ASP.NET Core.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f52b08786dde54e7dcbd2e00f43badb58879cf79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775756"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="5adb6-104">Создание приложения ASP.NET Core с данными пользователя, защищенными с помощью авторизации</span><span class="sxs-lookup"><span data-stu-id="5adb6-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="5adb6-105">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Джо Одетт](https://twitter.com/joeaudette) (Joe Audette)</span><span class="sxs-lookup"><span data-stu-id="5adb6-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="5adb6-106">См. [этот PDF-файл](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) для ASP.NET Core версии MVC.</span><span class="sxs-lookup"><span data-stu-id="5adb6-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="5adb6-107">Версия ASP.NET Core 1,1 этого учебника находится в [этой](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) папке.</span><span class="sxs-lookup"><span data-stu-id="5adb6-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="5adb6-108">Образец 1,1 ASP.NET Core приведен [в примерах.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)</span><span class="sxs-lookup"><span data-stu-id="5adb6-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="5adb6-109">Просмотреть [этот PDF-файл](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="5adb6-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5adb6-110">В этом руководстве показано, как создать веб-приложение ASP.NET Core с данными пользователя, защищенными с помощью авторизации.</span><span class="sxs-lookup"><span data-stu-id="5adb6-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="5adb6-111">Отображается список контактов, которые были созданы пользователями, прошедшими проверку подлинности (зарегистрированные).</span><span class="sxs-lookup"><span data-stu-id="5adb6-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="5adb6-112">Существует три группы безопасности:</span><span class="sxs-lookup"><span data-stu-id="5adb6-112">There are three security groups:</span></span>

* <span data-ttu-id="5adb6-113">**Зарегистрированные пользователи** могут просматривать все утвержденные данные, а также изменять и удалять свои данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="5adb6-114">**Руководители** могут утверждать или отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="5adb6-115">Пользователям видны только утвержденные контакты.</span><span class="sxs-lookup"><span data-stu-id="5adb6-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="5adb6-116">**Администраторы** могут утверждать, отклонять и изменять и удалять любые данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="5adb6-117">Изображения в этом документе не полностью соответствуют последним шаблонам.</span><span class="sxs-lookup"><span data-stu-id="5adb6-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="5adb6-118">На следующем рисунке пользователь Рик (`rick@example.com`) вошел в.</span><span class="sxs-lookup"><span data-stu-id="5adb6-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="5adb6-119">Рик может просматривать только утвержденные контакты и **редактировать**/**Удалить**/**создать новые** ссылки для своих контактов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="5adb6-120">Ссылки **Edit** и **Delete** отображаются только в последней записи, созданной Рик.</span><span class="sxs-lookup"><span data-stu-id="5adb6-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="5adb6-121">Другие пользователи не увидят последнюю запись, пока руководитель или администратор не изменит состояние на "утверждено".</span><span class="sxs-lookup"><span data-stu-id="5adb6-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Снимок экрана, показывающий Рик, выполнивший вход](secure-data/_static/rick.png)

<span data-ttu-id="5adb6-123">На следующем рисунке `manager@contoso.com` вход выполнен и в роли руководителя:</span><span class="sxs-lookup"><span data-stu-id="5adb6-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Снимок экрана manager@contoso.com , показывающий вход](secure-data/_static/manager1.png)

<span data-ttu-id="5adb6-125">На следующем рисунке показано представление сведений об менеджерах для контакта:</span><span class="sxs-lookup"><span data-stu-id="5adb6-125">The following image shows the managers details view of a contact:</span></span>

![Представление контакта руководителя](secure-data/_static/manager.png)

<span data-ttu-id="5adb6-127">Кнопки **утвердить** и **отклонить** отображаются только для руководителей и администраторов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="5adb6-128">На следующем рисунке `admin@contoso.com` вход выполнен и в роли администратора:</span><span class="sxs-lookup"><span data-stu-id="5adb6-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Снимок экрана admin@contoso.com , показывающий вход](secure-data/_static/admin.png)

<span data-ttu-id="5adb6-130">Администратор имеет все привилегии.</span><span class="sxs-lookup"><span data-stu-id="5adb6-130">The administrator has all privileges.</span></span> <span data-ttu-id="5adb6-131">Она может читать, изменять и удалять контакты, а также изменять состояние контактов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="5adb6-132">Приложение было создано с помощью [формирования шаблонов](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) следующей `Contact` модели:</span><span class="sxs-lookup"><span data-stu-id="5adb6-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="5adb6-133">Этот пример содержит следующие обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="5adb6-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="5adb6-134">`ContactIsOwnerAuthorizationHandler`: Гарантирует, что пользователь может изменять только свои данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="5adb6-135">`ContactManagerAuthorizationHandler`: Позволяет руководителям утверждать или отклонять контакты.</span><span class="sxs-lookup"><span data-stu-id="5adb6-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="5adb6-136">`ContactAdministratorsAuthorizationHandler`: Позволяет администраторам утверждать или отклонять контакты, а также изменять и удалять контакты.</span><span class="sxs-lookup"><span data-stu-id="5adb6-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5adb6-137">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="5adb6-137">Prerequisites</span></span>

<span data-ttu-id="5adb6-138">Этот учебник расширен.</span><span class="sxs-lookup"><span data-stu-id="5adb6-138">This tutorial is advanced.</span></span> <span data-ttu-id="5adb6-139">Вы должны быть знакомы с:</span><span class="sxs-lookup"><span data-stu-id="5adb6-139">You should be familiar with:</span></span>

* [<span data-ttu-id="5adb6-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5adb6-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="5adb6-141">Authentication</span><span class="sxs-lookup"><span data-stu-id="5adb6-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="5adb6-142">Подтверждение учетной записи и восстановление пароля</span><span class="sxs-lookup"><span data-stu-id="5adb6-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="5adb6-143">Авторизация</span><span class="sxs-lookup"><span data-stu-id="5adb6-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="5adb6-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5adb6-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="5adb6-145">Начальное и завершенное приложение</span><span class="sxs-lookup"><span data-stu-id="5adb6-145">The starter and completed app</span></span>

<span data-ttu-id="5adb6-146">[Скачайте](xref:index#how-to-download-a-sample) [готовое](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) приложение.</span><span class="sxs-lookup"><span data-stu-id="5adb6-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="5adb6-147">[Протестируйте](#test-the-completed-app) готовое приложение, чтобы ознакомиться с его функциями безопасности.</span><span class="sxs-lookup"><span data-stu-id="5adb6-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="5adb6-148">Начальное приложение</span><span class="sxs-lookup"><span data-stu-id="5adb6-148">The starter app</span></span>

<span data-ttu-id="5adb6-149">[Скачайте](xref:index#how-to-download-a-sample) [Начальное](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) приложение.</span><span class="sxs-lookup"><span data-stu-id="5adb6-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="5adb6-150">Запустите приложение, коснитесь ссылки **ContactManager** и убедитесь, что вы можете создать, изменить и удалить контакт.</span><span class="sxs-lookup"><span data-stu-id="5adb6-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="5adb6-151">Защита пользовательских данных</span><span class="sxs-lookup"><span data-stu-id="5adb6-151">Secure user data</span></span>

<span data-ttu-id="5adb6-152">Следующие разделы содержат все основные шаги по созданию безопасного приложения для данных пользователей.</span><span class="sxs-lookup"><span data-stu-id="5adb6-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="5adb6-153">Может оказаться полезным ссылаться на завершенный проект.</span><span class="sxs-lookup"><span data-stu-id="5adb6-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="5adb6-154">Связать контактные данные с пользователем</span><span class="sxs-lookup"><span data-stu-id="5adb6-154">Tie the contact data to the user</span></span>

<span data-ttu-id="5adb6-155">Используйте идентификатор пользователя [удостоверения](xref:security/authentication/identity) ASP.NET, чтобы убедиться, что пользователи могут изменять данные, но не данные других пользователей.</span><span class="sxs-lookup"><span data-stu-id="5adb6-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="5adb6-156">Добавьте `OwnerID` и `ContactStatus` в `Contact` модель:</span><span class="sxs-lookup"><span data-stu-id="5adb6-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="5adb6-157">`OwnerID`Идентификатор пользователя из `AspNetUser` таблицы в базе данных [удостоверений](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="5adb6-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="5adb6-158">`Status` Поле определяет, можно ли просматривать контакт обычными пользователями.</span><span class="sxs-lookup"><span data-stu-id="5adb6-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="5adb6-159">Создайте новую миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="5adb6-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="5adb6-160">Добавление служб ролей в удостоверение</span><span class="sxs-lookup"><span data-stu-id="5adb6-160">Add Role services to Identity</span></span>

<span data-ttu-id="5adb6-161">Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:</span><span class="sxs-lookup"><span data-stu-id="5adb6-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="5adb6-162">Требовать прошедших проверку пользователей</span><span class="sxs-lookup"><span data-stu-id="5adb6-162">Require authenticated users</span></span>

<span data-ttu-id="5adb6-163">Задайте политику проверки подлинности по умолчанию, чтобы требовать проверку подлинности пользователей:</span><span class="sxs-lookup"><span data-stu-id="5adb6-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="5adb6-164">Можно отказаться от проверки подлинности на уровне страницы Razor, контроллера или метода действия с `[AllowAnonymous]` помощью атрибута.</span><span class="sxs-lookup"><span data-stu-id="5adb6-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="5adb6-165">Настройка политики проверки подлинности по умолчанию, чтобы требовать проверку подлинности пользователей, защищает вновь добавленные Razor Pages и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="5adb6-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="5adb6-166">Наличие проверки подлинности по умолчанию является более безопасной, чем использование новых контроллеров и Razor Pages `[Authorize]` для включения атрибута.</span><span class="sxs-lookup"><span data-stu-id="5adb6-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="5adb6-167">Добавьте [allowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) на страницы индекса и конфиденциальности, чтобы анонимные пользователи могли получить сведения о сайте перед их регистрацией.</span><span class="sxs-lookup"><span data-stu-id="5adb6-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="5adb6-168">Настройка тестовой учетной записи</span><span class="sxs-lookup"><span data-stu-id="5adb6-168">Configure the test account</span></span>

<span data-ttu-id="5adb6-169">`SeedData` Класс создает две учетные записи: Administrator и Manager.</span><span class="sxs-lookup"><span data-stu-id="5adb6-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="5adb6-170">Используйте [средство диспетчера секретов](xref:security/app-secrets) , чтобы задать пароль для этих учетных записей.</span><span class="sxs-lookup"><span data-stu-id="5adb6-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="5adb6-171">Задайте пароль из каталога проекта (каталога, содержащего *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="5adb6-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="5adb6-172">Если надежный пароль не указан, при `SeedData.Initialize` вызове вызывается исключение.</span><span class="sxs-lookup"><span data-stu-id="5adb6-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="5adb6-173">Обновите `Main` , чтобы использовать тестовый пароль:</span><span class="sxs-lookup"><span data-stu-id="5adb6-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="5adb6-174">Создание тестовых учетных записей и обновление контактов</span><span class="sxs-lookup"><span data-stu-id="5adb6-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="5adb6-175">Обновите `Initialize` метод в `SeedData` классе, чтобы создать тестовые учетные записи:</span><span class="sxs-lookup"><span data-stu-id="5adb6-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="5adb6-176">Добавьте идентификатор пользователя администратора и `ContactStatus` в список контактов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="5adb6-177">Сделайте одно из контактов "Отправлено" и одно "Отклонено".</span><span class="sxs-lookup"><span data-stu-id="5adb6-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="5adb6-178">Добавьте идентификатор пользователя и состояние во все контакты.</span><span class="sxs-lookup"><span data-stu-id="5adb6-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="5adb6-179">Отображается только один контакт:</span><span class="sxs-lookup"><span data-stu-id="5adb6-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="5adb6-180">Создание обработчиков авторизации владельца, руководителя и администратора</span><span class="sxs-lookup"><span data-stu-id="5adb6-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="5adb6-181">Создайте `ContactIsOwnerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="5adb6-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5adb6-182">`ContactIsOwnerAuthorizationHandler` Проверяет, принадлежит ли ресурс пользователю, работающему с ресурсом.</span><span class="sxs-lookup"><span data-stu-id="5adb6-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="5adb6-183">Контекст `ContactIsOwnerAuthorizationHandler` вызовов [. Считается успешной](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , если текущий пользователь, прошедший проверку подлинности, является владельцем контакта.</span><span class="sxs-lookup"><span data-stu-id="5adb6-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="5adb6-184">Обычно обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="5adb6-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="5adb6-185">Возврат `context.Succeed` при соблюдении требований.</span><span class="sxs-lookup"><span data-stu-id="5adb6-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="5adb6-186">Возврат `Task.CompletedTask` , если требования не выполнены.</span><span class="sxs-lookup"><span data-stu-id="5adb6-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="5adb6-187">`Task.CompletedTask`не является успешным или неудачным&mdash;, что позволяет запускать другие обработчики авторизации.</span><span class="sxs-lookup"><span data-stu-id="5adb6-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="5adb6-188">Если необходимо явное завершение, возвращаемый [контекст. Не пройдено](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="5adb6-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="5adb6-189">Приложение позволяет владельцам контактов изменять, удалять и создавать собственные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="5adb6-190">`ContactIsOwnerAuthorizationHandler`не требуется проверять операцию, переданную в параметре требования.</span><span class="sxs-lookup"><span data-stu-id="5adb6-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="5adb6-191">Создание обработчика авторизации диспетчера</span><span class="sxs-lookup"><span data-stu-id="5adb6-191">Create a manager authorization handler</span></span>

<span data-ttu-id="5adb6-192">Создайте `ContactManagerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="5adb6-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5adb6-193">`ContactManagerAuthorizationHandler` Проверяет, является ли пользователь, действующий для ресурса, диспетчером.</span><span class="sxs-lookup"><span data-stu-id="5adb6-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="5adb6-194">Только руководители могут утверждать или отклонять изменения содержимого (новые или измененные).</span><span class="sxs-lookup"><span data-stu-id="5adb6-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="5adb6-195">Создание обработчика авторизации администратора</span><span class="sxs-lookup"><span data-stu-id="5adb6-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="5adb6-196">Создайте `ContactAdministratorsAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="5adb6-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5adb6-197">`ContactAdministratorsAuthorizationHandler` Проверяет, является ли пользователь, действующий для ресурса, администратором.</span><span class="sxs-lookup"><span data-stu-id="5adb6-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="5adb6-198">Администратор может выполнять все операции.</span><span class="sxs-lookup"><span data-stu-id="5adb6-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="5adb6-199">Регистрация обработчиков авторизации</span><span class="sxs-lookup"><span data-stu-id="5adb6-199">Register the authorization handlers</span></span>

<span data-ttu-id="5adb6-200">Службы, использующие Entity Framework Core, должны быть зарегистрированы для [внедрения зависимостей](xref:fundamentals/dependency-injection) с помощью [аддскопед](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="5adb6-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="5adb6-201">`ContactIsOwnerAuthorizationHandler` Использует [удостоверение](xref:security/authentication/identity)ASP.NET Core, созданное на основе Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5adb6-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="5adb6-202">Зарегистрируйте обработчики в коллекции служб, чтобы они были доступны `ContactsController` через [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5adb6-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5adb6-203">Добавьте следующий код в конец `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5adb6-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="5adb6-204">`ContactAdministratorsAuthorizationHandler`и `ContactManagerAuthorizationHandler` добавляются в виде Singleton.</span><span class="sxs-lookup"><span data-stu-id="5adb6-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="5adb6-205">Они являются Singleton-классом, поскольку не используют EF, а вся необходимая информация находится `Context` в параметре `HandleRequirementAsync` метода.</span><span class="sxs-lookup"><span data-stu-id="5adb6-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="5adb6-206">Поддержка авторизации</span><span class="sxs-lookup"><span data-stu-id="5adb6-206">Support authorization</span></span>

<span data-ttu-id="5adb6-207">В этом разделе вы обновите Razor Pages и добавьте класс требований к операциям.</span><span class="sxs-lookup"><span data-stu-id="5adb6-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="5adb6-208">Ознакомьтесь с классом требований к операциям</span><span class="sxs-lookup"><span data-stu-id="5adb6-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="5adb6-209">Проверьте `ContactOperations` класс.</span><span class="sxs-lookup"><span data-stu-id="5adb6-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="5adb6-210">Этот класс содержит требования, которые поддерживает приложение:</span><span class="sxs-lookup"><span data-stu-id="5adb6-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="5adb6-211">Создание базового класса для контактов Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5adb6-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="5adb6-212">Создайте базовый класс, содержащий службы, используемые в Razor Pages Contacts.</span><span class="sxs-lookup"><span data-stu-id="5adb6-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="5adb6-213">Базовый класс помещает код инициализации в одно расположение:</span><span class="sxs-lookup"><span data-stu-id="5adb6-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="5adb6-214">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="5adb6-214">The preceding code:</span></span>

* <span data-ttu-id="5adb6-215">Добавляет `IAuthorizationService` службу для доступа к обработчикам авторизации.</span><span class="sxs-lookup"><span data-stu-id="5adb6-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="5adb6-216">Добавляет службу удостоверений `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="5adb6-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="5adb6-217">Добавьте `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="5adb6-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="5adb6-218">Обновление Креатемодел</span><span class="sxs-lookup"><span data-stu-id="5adb6-218">Update the CreateModel</span></span>

<span data-ttu-id="5adb6-219">Обновите конструктор модели страницы Create, чтобы использовать `DI_BasePageModel` базовый класс:</span><span class="sxs-lookup"><span data-stu-id="5adb6-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="5adb6-220">Обновите `CreateModel.OnPostAsync` метод, чтобы:</span><span class="sxs-lookup"><span data-stu-id="5adb6-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="5adb6-221">Добавьте в `Contact` модель идентификатор пользователя.</span><span class="sxs-lookup"><span data-stu-id="5adb6-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="5adb6-222">Вызовите обработчик авторизации, чтобы убедиться, что пользователь имеет разрешение на создание контактов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="5adb6-223">Обновление Индексмодел</span><span class="sxs-lookup"><span data-stu-id="5adb6-223">Update the IndexModel</span></span>

<span data-ttu-id="5adb6-224">Обновите `OnGetAsync` метод, чтобы только утвержденные контакты отображались для обычных пользователей:</span><span class="sxs-lookup"><span data-stu-id="5adb6-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="5adb6-225">Обновление Едитмодел</span><span class="sxs-lookup"><span data-stu-id="5adb6-225">Update the EditModel</span></span>

<span data-ttu-id="5adb6-226">Добавьте обработчик авторизации, чтобы убедиться, что пользователь владеет контактом.</span><span class="sxs-lookup"><span data-stu-id="5adb6-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="5adb6-227">Так как авторизация ресурса проверяется, `[Authorize]` атрибут недостаточно.</span><span class="sxs-lookup"><span data-stu-id="5adb6-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="5adb6-228">Приложение не имеет доступа к ресурсу при оценке атрибутов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="5adb6-229">Авторизация на основе ресурсов должна быть принудительной.</span><span class="sxs-lookup"><span data-stu-id="5adb6-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="5adb6-230">Проверки должны выполняться после того, как приложение будет иметь доступ к ресурсу, загрузив его в модель страницы или загрузив в сам обработчик.</span><span class="sxs-lookup"><span data-stu-id="5adb6-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="5adb6-231">Вы часто обращаетесь к ресурсу, передавая ключ ресурса.</span><span class="sxs-lookup"><span data-stu-id="5adb6-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="5adb6-232">Обновление Делетемодел</span><span class="sxs-lookup"><span data-stu-id="5adb6-232">Update the DeleteModel</span></span>

<span data-ttu-id="5adb6-233">Обновите модель страницы удаления, чтобы использовать обработчик авторизации для проверки наличия у пользователя разрешения на удаление контакта.</span><span class="sxs-lookup"><span data-stu-id="5adb6-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="5adb6-234">Внедрение службы авторизации в представления</span><span class="sxs-lookup"><span data-stu-id="5adb6-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="5adb6-235">В настоящее время в пользовательском интерфейсе отображаются ссылки Edit и DELETE для контактов, которые пользователь не может изменить.</span><span class="sxs-lookup"><span data-stu-id="5adb6-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="5adb6-236">Вставьте службу авторизации в файл *pages/_ViewImports. cshtml* , чтобы он был доступен для всех представлений:</span><span class="sxs-lookup"><span data-stu-id="5adb6-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="5adb6-237">Предыдущая разметка добавляет `using` несколько инструкций.</span><span class="sxs-lookup"><span data-stu-id="5adb6-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="5adb6-238">Обновите ссылки **Edit** и **Delete** в *pages/contacts/index. cshtml* , чтобы они отображались только для пользователей с соответствующими разрешениями:</span><span class="sxs-lookup"><span data-stu-id="5adb6-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="5adb6-239">Скрытие ссылок от пользователей, не имеющих разрешений на изменение данных, не защищает приложение.</span><span class="sxs-lookup"><span data-stu-id="5adb6-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="5adb6-240">Скрытие ссылок делает приложение более удобным для пользователей, отображая только допустимые ссылки.</span><span class="sxs-lookup"><span data-stu-id="5adb6-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="5adb6-241">Пользователи могут обращаться к созданным URL-адресам для вызова операций правки и удаления данных, которыми они не владеют.</span><span class="sxs-lookup"><span data-stu-id="5adb6-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="5adb6-242">Страница Razor или контроллер должны принудительно применять проверки доступа для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="5adb6-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="5adb6-243">Сведения об обновлении</span><span class="sxs-lookup"><span data-stu-id="5adb6-243">Update Details</span></span>

<span data-ttu-id="5adb6-244">Обновите представление сведений, чтобы руководители могли утверждать или отклонять контакты:</span><span class="sxs-lookup"><span data-stu-id="5adb6-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="5adb6-245">Обновите модель страницы сведений:</span><span class="sxs-lookup"><span data-stu-id="5adb6-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="5adb6-246">Добавление или удаление пользователя для роли</span><span class="sxs-lookup"><span data-stu-id="5adb6-246">Add or remove a user to a role</span></span>

<span data-ttu-id="5adb6-247">Сведения об [этой ошибке](https://github.com/dotnet/AspNetCore.Docs/issues/8502) см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="5adb6-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="5adb6-248">Удаление привилегий у пользователя.</span><span class="sxs-lookup"><span data-stu-id="5adb6-248">Removing privileges from a user.</span></span> <span data-ttu-id="5adb6-249">Например, отзвука пользователя в приложении разговора.</span><span class="sxs-lookup"><span data-stu-id="5adb6-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="5adb6-250">Добавление привилегий для пользователя.</span><span class="sxs-lookup"><span data-stu-id="5adb6-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="5adb6-251">Различия между запросом и запретом</span><span class="sxs-lookup"><span data-stu-id="5adb6-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="5adb6-252">Это приложение задает политику по умолчанию, [требующую проверки подлинности пользователей](#require-authenticated-users).</span><span class="sxs-lookup"><span data-stu-id="5adb6-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="5adb6-253">Следующий код позволяет анонимным пользователям.</span><span class="sxs-lookup"><span data-stu-id="5adb6-253">The following code allows anonymous users.</span></span> <span data-ttu-id="5adb6-254">Анонимным пользователям разрешено показывать различия между запросом и запретом.</span><span class="sxs-lookup"><span data-stu-id="5adb6-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="5adb6-255">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="5adb6-255">In the preceding code:</span></span>

* <span data-ttu-id="5adb6-256">Если пользователь **не** прошел проверку подлинности, `ChallengeResult` возвращается значение.</span><span class="sxs-lookup"><span data-stu-id="5adb6-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="5adb6-257">`ChallengeResult` Когда возвращается, пользователь перенаправляется на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="5adb6-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="5adb6-258">Если пользователь прошел проверку подлинности, но не авторизован, `ForbidResult` возвращается значение.</span><span class="sxs-lookup"><span data-stu-id="5adb6-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="5adb6-259">Когда возвращается `ForbidResult` значение, пользователь перенаправляется на страницу отказ в доступе.</span><span class="sxs-lookup"><span data-stu-id="5adb6-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="5adb6-260">Тестирование завершенного приложения</span><span class="sxs-lookup"><span data-stu-id="5adb6-260">Test the completed app</span></span>

<span data-ttu-id="5adb6-261">Если вы еще не установили пароль для заполненных учетных записей пользователей, используйте [средство диспетчера секретов](xref:security/app-secrets#secret-manager) , чтобы задать пароль:</span><span class="sxs-lookup"><span data-stu-id="5adb6-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="5adb6-262">Выберите надежный пароль: используйте восемь или более символов и по крайней мере одну прописную букву, цифру и символ.</span><span class="sxs-lookup"><span data-stu-id="5adb6-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="5adb6-263">Например, `Passw0rd!` соответствует требованиям к надежному паролю.</span><span class="sxs-lookup"><span data-stu-id="5adb6-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="5adb6-264">Выполните следующую команду из папки проекта, где `<PW>` — пароль:</span><span class="sxs-lookup"><span data-stu-id="5adb6-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="5adb6-265">Если у приложения есть контакты:</span><span class="sxs-lookup"><span data-stu-id="5adb6-265">If the app has contacts:</span></span>

* <span data-ttu-id="5adb6-266">Удалите все записи в `Contact` таблице.</span><span class="sxs-lookup"><span data-stu-id="5adb6-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="5adb6-267">Перезапустите приложение, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="5adb6-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="5adb6-268">Простой способ тестирования завершенного приложения — запуск трех различных браузеров (или режиме инкогнито и нечастных сеансов).</span><span class="sxs-lookup"><span data-stu-id="5adb6-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="5adb6-269">В одном браузере Зарегистрируйте нового пользователя (например, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="5adb6-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="5adb6-270">Войдите в каждый браузер с другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="5adb6-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="5adb6-271">Проверьте следующие операции:</span><span class="sxs-lookup"><span data-stu-id="5adb6-271">Verify the following operations:</span></span>

* <span data-ttu-id="5adb6-272">Зарегистрированные пользователи могут просматривать все утвержденные контактные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="5adb6-273">Зарегистрированные пользователи могут изменять и удалять собственные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="5adb6-274">Руководители могут утверждать и отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="5adb6-275">В `Details` представлении отображаются кнопки **утвердить** и **отклонить** .</span><span class="sxs-lookup"><span data-stu-id="5adb6-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="5adb6-276">Администраторы могут утверждать, отклонять и изменять и удалять все данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="5adb6-277">Пользователь</span><span class="sxs-lookup"><span data-stu-id="5adb6-277">User</span></span>                | <span data-ttu-id="5adb6-278">Заполнено приложением</span><span class="sxs-lookup"><span data-stu-id="5adb6-278">Seeded by the app</span></span> | <span data-ttu-id="5adb6-279">Параметры</span><span class="sxs-lookup"><span data-stu-id="5adb6-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="5adb6-280">нет</span><span class="sxs-lookup"><span data-stu-id="5adb6-280">No</span></span>                | <span data-ttu-id="5adb6-281">Изменение или удаление собственных данных.</span><span class="sxs-lookup"><span data-stu-id="5adb6-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="5adb6-282">Да</span><span class="sxs-lookup"><span data-stu-id="5adb6-282">Yes</span></span>               | <span data-ttu-id="5adb6-283">Утвердите, отклоните и измените или удалите собственные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="5adb6-284">Да</span><span class="sxs-lookup"><span data-stu-id="5adb6-284">Yes</span></span>               | <span data-ttu-id="5adb6-285">Утвердите или отклоните и измените или удалите все данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="5adb6-286">Создайте контакт в браузере администратора.</span><span class="sxs-lookup"><span data-stu-id="5adb6-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="5adb6-287">Скопируйте URL-адрес для DELETE и Edit из контакта администратора.</span><span class="sxs-lookup"><span data-stu-id="5adb6-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="5adb6-288">Вставьте эти ссылки в браузер тестового пользователя, чтобы убедиться, что тестовая пользователь не может выполнить эти операции.</span><span class="sxs-lookup"><span data-stu-id="5adb6-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="5adb6-289">Создание начального приложения</span><span class="sxs-lookup"><span data-stu-id="5adb6-289">Create the starter app</span></span>

* <span data-ttu-id="5adb6-290">Создание Razor Pages приложения с именем "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="5adb6-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="5adb6-291">Создайте приложение с **учетными записями отдельных пользователей**.</span><span class="sxs-lookup"><span data-stu-id="5adb6-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="5adb6-292">Назовите его "ContactManager", чтобы пространство имен совпадало с пространством имен, используемым в примере.</span><span class="sxs-lookup"><span data-stu-id="5adb6-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="5adb6-293">`-uld`Указывает LocalDB вместо SQLite</span><span class="sxs-lookup"><span data-stu-id="5adb6-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="5adb6-294">Добавление *моделей/Contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="5adb6-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="5adb6-295">Формирование шаблонов `Contact` модели.</span><span class="sxs-lookup"><span data-stu-id="5adb6-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="5adb6-296">Создайте начальную миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="5adb6-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="5adb6-297">Если при выполнении `dotnet aspnet-codegenerator razorpage` команды возникла ошибка, см. [эту ошибку в GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="5adb6-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="5adb6-298">Обновите привязку **ContactManager** в файле *pages/shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="5adb6-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="5adb6-299">Тестирование приложения путем создания, изменения и удаления контакта</span><span class="sxs-lookup"><span data-stu-id="5adb6-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="5adb6-300">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="5adb6-300">Seed the database</span></span>

<span data-ttu-id="5adb6-301">Добавьте класс [сиддата](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) в папку *Data* :</span><span class="sxs-lookup"><span data-stu-id="5adb6-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="5adb6-302">Вызов `SeedData.Initialize` из `Main`:</span><span class="sxs-lookup"><span data-stu-id="5adb6-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="5adb6-303">Проверьте, что приложение заполнено базой данных.</span><span class="sxs-lookup"><span data-stu-id="5adb6-303">Test that the app seeded the database.</span></span> <span data-ttu-id="5adb6-304">Если в базе данных Contact есть какие бы то ни было строки, метод SEED не выполняется.</span><span class="sxs-lookup"><span data-stu-id="5adb6-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="5adb6-305">В этом руководстве показано, как создать веб-приложение ASP.NET Core с данными пользователя, защищенными с помощью авторизации.</span><span class="sxs-lookup"><span data-stu-id="5adb6-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="5adb6-306">Отображается список контактов, которые были созданы пользователями, прошедшими проверку подлинности (зарегистрированные).</span><span class="sxs-lookup"><span data-stu-id="5adb6-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="5adb6-307">Существует три группы безопасности:</span><span class="sxs-lookup"><span data-stu-id="5adb6-307">There are three security groups:</span></span>

* <span data-ttu-id="5adb6-308">**Зарегистрированные пользователи** могут просматривать все утвержденные данные, а также изменять и удалять свои данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="5adb6-309">**Руководители** могут утверждать или отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="5adb6-310">Пользователям видны только утвержденные контакты.</span><span class="sxs-lookup"><span data-stu-id="5adb6-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="5adb6-311">**Администраторы** могут утверждать, отклонять и изменять и удалять любые данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="5adb6-312">На следующем рисунке пользователь Рик (`rick@example.com`) вошел в.</span><span class="sxs-lookup"><span data-stu-id="5adb6-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="5adb6-313">Рик может просматривать только утвержденные контакты и **редактировать**/**Удалить**/**создать новые** ссылки для своих контактов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="5adb6-314">Ссылки **Edit** и **Delete** отображаются только в последней записи, созданной Рик.</span><span class="sxs-lookup"><span data-stu-id="5adb6-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="5adb6-315">Другие пользователи не увидят последнюю запись, пока руководитель или администратор не изменит состояние на "утверждено".</span><span class="sxs-lookup"><span data-stu-id="5adb6-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Снимок экрана, показывающий Рик, выполнивший вход](secure-data/_static/rick.png)

<span data-ttu-id="5adb6-317">На следующем рисунке `manager@contoso.com` вход выполнен и в роли руководителя:</span><span class="sxs-lookup"><span data-stu-id="5adb6-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Снимок экрана manager@contoso.com , показывающий вход](secure-data/_static/manager1.png)

<span data-ttu-id="5adb6-319">На следующем рисунке показано представление сведений об менеджерах для контакта:</span><span class="sxs-lookup"><span data-stu-id="5adb6-319">The following image shows the managers details view of a contact:</span></span>

![Представление контакта руководителя](secure-data/_static/manager.png)

<span data-ttu-id="5adb6-321">Кнопки **утвердить** и **отклонить** отображаются только для руководителей и администраторов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="5adb6-322">На следующем рисунке `admin@contoso.com` вход выполнен и в роли администратора:</span><span class="sxs-lookup"><span data-stu-id="5adb6-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Снимок экрана admin@contoso.com , показывающий вход](secure-data/_static/admin.png)

<span data-ttu-id="5adb6-324">Администратор имеет все привилегии.</span><span class="sxs-lookup"><span data-stu-id="5adb6-324">The administrator has all privileges.</span></span> <span data-ttu-id="5adb6-325">Она может читать, изменять и удалять контакты, а также изменять состояние контактов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="5adb6-326">Приложение было создано с помощью [формирования шаблонов](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) следующей `Contact` модели:</span><span class="sxs-lookup"><span data-stu-id="5adb6-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="5adb6-327">Этот пример содержит следующие обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="5adb6-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="5adb6-328">`ContactIsOwnerAuthorizationHandler`: Гарантирует, что пользователь может изменять только свои данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="5adb6-329">`ContactManagerAuthorizationHandler`: Позволяет руководителям утверждать или отклонять контакты.</span><span class="sxs-lookup"><span data-stu-id="5adb6-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="5adb6-330">`ContactAdministratorsAuthorizationHandler`: Позволяет администраторам утверждать или отклонять контакты, а также изменять и удалять контакты.</span><span class="sxs-lookup"><span data-stu-id="5adb6-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5adb6-331">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="5adb6-331">Prerequisites</span></span>

<span data-ttu-id="5adb6-332">Этот учебник расширен.</span><span class="sxs-lookup"><span data-stu-id="5adb6-332">This tutorial is advanced.</span></span> <span data-ttu-id="5adb6-333">Вы должны быть знакомы с:</span><span class="sxs-lookup"><span data-stu-id="5adb6-333">You should be familiar with:</span></span>

* [<span data-ttu-id="5adb6-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5adb6-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="5adb6-335">Authentication</span><span class="sxs-lookup"><span data-stu-id="5adb6-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="5adb6-336">Подтверждение учетной записи и восстановление пароля</span><span class="sxs-lookup"><span data-stu-id="5adb6-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="5adb6-337">Авторизация</span><span class="sxs-lookup"><span data-stu-id="5adb6-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="5adb6-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5adb6-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="5adb6-339">Начальное и завершенное приложение</span><span class="sxs-lookup"><span data-stu-id="5adb6-339">The starter and completed app</span></span>

<span data-ttu-id="5adb6-340">[Скачайте](xref:index#how-to-download-a-sample) [готовое](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) приложение.</span><span class="sxs-lookup"><span data-stu-id="5adb6-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="5adb6-341">[Протестируйте](#test-the-completed-app) готовое приложение, чтобы ознакомиться с его функциями безопасности.</span><span class="sxs-lookup"><span data-stu-id="5adb6-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="5adb6-342">Начальное приложение</span><span class="sxs-lookup"><span data-stu-id="5adb6-342">The starter app</span></span>

<span data-ttu-id="5adb6-343">[Скачайте](xref:index#how-to-download-a-sample) [Начальное](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) приложение.</span><span class="sxs-lookup"><span data-stu-id="5adb6-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="5adb6-344">Запустите приложение, коснитесь ссылки **ContactManager** и убедитесь, что вы можете создать, изменить и удалить контакт.</span><span class="sxs-lookup"><span data-stu-id="5adb6-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="5adb6-345">Защита пользовательских данных</span><span class="sxs-lookup"><span data-stu-id="5adb6-345">Secure user data</span></span>

<span data-ttu-id="5adb6-346">Следующие разделы содержат все основные шаги по созданию безопасного приложения для данных пользователей.</span><span class="sxs-lookup"><span data-stu-id="5adb6-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="5adb6-347">Может оказаться полезным ссылаться на завершенный проект.</span><span class="sxs-lookup"><span data-stu-id="5adb6-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="5adb6-348">Связать контактные данные с пользователем</span><span class="sxs-lookup"><span data-stu-id="5adb6-348">Tie the contact data to the user</span></span>

<span data-ttu-id="5adb6-349">Используйте идентификатор пользователя [удостоверения](xref:security/authentication/identity) ASP.NET, чтобы убедиться, что пользователи могут изменять данные, но не данные других пользователей.</span><span class="sxs-lookup"><span data-stu-id="5adb6-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="5adb6-350">Добавьте `OwnerID` и `ContactStatus` в `Contact` модель:</span><span class="sxs-lookup"><span data-stu-id="5adb6-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="5adb6-351">`OwnerID`Идентификатор пользователя из `AspNetUser` таблицы в базе данных [удостоверений](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="5adb6-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="5adb6-352">`Status` Поле определяет, можно ли просматривать контакт обычными пользователями.</span><span class="sxs-lookup"><span data-stu-id="5adb6-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="5adb6-353">Создайте новую миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="5adb6-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="5adb6-354">Добавление служб ролей в удостоверение</span><span class="sxs-lookup"><span data-stu-id="5adb6-354">Add Role services to Identity</span></span>

<span data-ttu-id="5adb6-355">Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:</span><span class="sxs-lookup"><span data-stu-id="5adb6-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="5adb6-356">Требовать прошедших проверку пользователей</span><span class="sxs-lookup"><span data-stu-id="5adb6-356">Require authenticated users</span></span>

<span data-ttu-id="5adb6-357">Задайте политику проверки подлинности по умолчанию, чтобы требовать проверку подлинности пользователей:</span><span class="sxs-lookup"><span data-stu-id="5adb6-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="5adb6-358">Можно отказаться от проверки подлинности на уровне страницы Razor, контроллера или метода действия с `[AllowAnonymous]` помощью атрибута.</span><span class="sxs-lookup"><span data-stu-id="5adb6-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="5adb6-359">Настройка политики проверки подлинности по умолчанию, чтобы требовать проверку подлинности пользователей, защищает вновь добавленные Razor Pages и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="5adb6-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="5adb6-360">Наличие проверки подлинности по умолчанию является более безопасной, чем использование новых контроллеров и Razor Pages `[Authorize]` для включения атрибута.</span><span class="sxs-lookup"><span data-stu-id="5adb6-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="5adb6-361">Добавьте [allowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) в индекс, About и Contact Pages, чтобы анонимные пользователи могли получить сведения о сайте перед регистрацией.</span><span class="sxs-lookup"><span data-stu-id="5adb6-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="5adb6-362">Настройка тестовой учетной записи</span><span class="sxs-lookup"><span data-stu-id="5adb6-362">Configure the test account</span></span>

<span data-ttu-id="5adb6-363">`SeedData` Класс создает две учетные записи: Administrator и Manager.</span><span class="sxs-lookup"><span data-stu-id="5adb6-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="5adb6-364">Используйте [средство диспетчера секретов](xref:security/app-secrets) , чтобы задать пароль для этих учетных записей.</span><span class="sxs-lookup"><span data-stu-id="5adb6-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="5adb6-365">Задайте пароль из каталога проекта (каталога, содержащего *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="5adb6-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="5adb6-366">Если надежный пароль не указан, при `SeedData.Initialize` вызове вызывается исключение.</span><span class="sxs-lookup"><span data-stu-id="5adb6-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="5adb6-367">Обновите `Main` , чтобы использовать тестовый пароль:</span><span class="sxs-lookup"><span data-stu-id="5adb6-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="5adb6-368">Создание тестовых учетных записей и обновление контактов</span><span class="sxs-lookup"><span data-stu-id="5adb6-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="5adb6-369">Обновите `Initialize` метод в `SeedData` классе, чтобы создать тестовые учетные записи:</span><span class="sxs-lookup"><span data-stu-id="5adb6-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="5adb6-370">Добавьте идентификатор пользователя администратора и `ContactStatus` в список контактов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="5adb6-371">Сделайте одно из контактов "Отправлено" и одно "Отклонено".</span><span class="sxs-lookup"><span data-stu-id="5adb6-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="5adb6-372">Добавьте идентификатор пользователя и состояние во все контакты.</span><span class="sxs-lookup"><span data-stu-id="5adb6-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="5adb6-373">Отображается только один контакт:</span><span class="sxs-lookup"><span data-stu-id="5adb6-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="5adb6-374">Создание обработчиков авторизации владельца, руководителя и администратора</span><span class="sxs-lookup"><span data-stu-id="5adb6-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="5adb6-375">Создайте папку *авторизации* и создайте в ней `ContactIsOwnerAuthorizationHandler` класс.</span><span class="sxs-lookup"><span data-stu-id="5adb6-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="5adb6-376">`ContactIsOwnerAuthorizationHandler` Проверяет, принадлежит ли ресурс пользователю, работающему с ресурсом.</span><span class="sxs-lookup"><span data-stu-id="5adb6-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="5adb6-377">Контекст `ContactIsOwnerAuthorizationHandler` вызовов [. Считается успешной](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , если текущий пользователь, прошедший проверку подлинности, является владельцем контакта.</span><span class="sxs-lookup"><span data-stu-id="5adb6-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="5adb6-378">Обычно обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="5adb6-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="5adb6-379">Возврат `context.Succeed` при соблюдении требований.</span><span class="sxs-lookup"><span data-stu-id="5adb6-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="5adb6-380">Возврат `Task.CompletedTask` , если требования не выполнены.</span><span class="sxs-lookup"><span data-stu-id="5adb6-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="5adb6-381">`Task.CompletedTask`не является успешным или неудачным&mdash;, что позволяет запускать другие обработчики авторизации.</span><span class="sxs-lookup"><span data-stu-id="5adb6-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="5adb6-382">Если необходимо явное завершение, возвращаемый [контекст. Не пройдено](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="5adb6-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="5adb6-383">Приложение позволяет владельцам контактов изменять, удалять и создавать собственные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="5adb6-384">`ContactIsOwnerAuthorizationHandler`не требуется проверять операцию, переданную в параметре требования.</span><span class="sxs-lookup"><span data-stu-id="5adb6-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="5adb6-385">Создание обработчика авторизации диспетчера</span><span class="sxs-lookup"><span data-stu-id="5adb6-385">Create a manager authorization handler</span></span>

<span data-ttu-id="5adb6-386">Создайте `ContactManagerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="5adb6-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5adb6-387">`ContactManagerAuthorizationHandler` Проверяет, является ли пользователь, действующий для ресурса, диспетчером.</span><span class="sxs-lookup"><span data-stu-id="5adb6-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="5adb6-388">Только руководители могут утверждать или отклонять изменения содержимого (новые или измененные).</span><span class="sxs-lookup"><span data-stu-id="5adb6-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="5adb6-389">Создание обработчика авторизации администратора</span><span class="sxs-lookup"><span data-stu-id="5adb6-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="5adb6-390">Создайте `ContactAdministratorsAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="5adb6-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5adb6-391">`ContactAdministratorsAuthorizationHandler` Проверяет, является ли пользователь, действующий для ресурса, администратором.</span><span class="sxs-lookup"><span data-stu-id="5adb6-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="5adb6-392">Администратор может выполнять все операции.</span><span class="sxs-lookup"><span data-stu-id="5adb6-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="5adb6-393">Регистрация обработчиков авторизации</span><span class="sxs-lookup"><span data-stu-id="5adb6-393">Register the authorization handlers</span></span>

<span data-ttu-id="5adb6-394">Службы, использующие Entity Framework Core, должны быть зарегистрированы для [внедрения зависимостей](xref:fundamentals/dependency-injection) с помощью [аддскопед](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="5adb6-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="5adb6-395">`ContactIsOwnerAuthorizationHandler` Использует [удостоверение](xref:security/authentication/identity)ASP.NET Core, созданное на основе Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5adb6-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="5adb6-396">Зарегистрируйте обработчики в коллекции служб, чтобы они были доступны `ContactsController` через [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5adb6-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5adb6-397">Добавьте следующий код в конец `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5adb6-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="5adb6-398">`ContactAdministratorsAuthorizationHandler`и `ContactManagerAuthorizationHandler` добавляются в виде Singleton.</span><span class="sxs-lookup"><span data-stu-id="5adb6-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="5adb6-399">Они являются Singleton-классом, поскольку не используют EF, а вся необходимая информация находится `Context` в параметре `HandleRequirementAsync` метода.</span><span class="sxs-lookup"><span data-stu-id="5adb6-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="5adb6-400">Поддержка авторизации</span><span class="sxs-lookup"><span data-stu-id="5adb6-400">Support authorization</span></span>

<span data-ttu-id="5adb6-401">В этом разделе вы обновите Razor Pages и добавьте класс требований к операциям.</span><span class="sxs-lookup"><span data-stu-id="5adb6-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="5adb6-402">Ознакомьтесь с классом требований к операциям</span><span class="sxs-lookup"><span data-stu-id="5adb6-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="5adb6-403">Проверьте `ContactOperations` класс.</span><span class="sxs-lookup"><span data-stu-id="5adb6-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="5adb6-404">Этот класс содержит требования, которые поддерживает приложение:</span><span class="sxs-lookup"><span data-stu-id="5adb6-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="5adb6-405">Создание базового класса для контактов Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5adb6-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="5adb6-406">Создайте базовый класс, содержащий службы, используемые в Razor Pages Contacts.</span><span class="sxs-lookup"><span data-stu-id="5adb6-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="5adb6-407">Базовый класс помещает код инициализации в одно расположение:</span><span class="sxs-lookup"><span data-stu-id="5adb6-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="5adb6-408">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="5adb6-408">The preceding code:</span></span>

* <span data-ttu-id="5adb6-409">Добавляет `IAuthorizationService` службу для доступа к обработчикам авторизации.</span><span class="sxs-lookup"><span data-stu-id="5adb6-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="5adb6-410">Добавляет службу удостоверений `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="5adb6-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="5adb6-411">Добавьте `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="5adb6-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="5adb6-412">Обновление Креатемодел</span><span class="sxs-lookup"><span data-stu-id="5adb6-412">Update the CreateModel</span></span>

<span data-ttu-id="5adb6-413">Обновите конструктор модели страницы Create, чтобы использовать `DI_BasePageModel` базовый класс:</span><span class="sxs-lookup"><span data-stu-id="5adb6-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="5adb6-414">Обновите `CreateModel.OnPostAsync` метод, чтобы:</span><span class="sxs-lookup"><span data-stu-id="5adb6-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="5adb6-415">Добавьте в `Contact` модель идентификатор пользователя.</span><span class="sxs-lookup"><span data-stu-id="5adb6-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="5adb6-416">Вызовите обработчик авторизации, чтобы убедиться, что пользователь имеет разрешение на создание контактов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="5adb6-417">Обновление Индексмодел</span><span class="sxs-lookup"><span data-stu-id="5adb6-417">Update the IndexModel</span></span>

<span data-ttu-id="5adb6-418">Обновите `OnGetAsync` метод, чтобы только утвержденные контакты отображались для обычных пользователей:</span><span class="sxs-lookup"><span data-stu-id="5adb6-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="5adb6-419">Обновление Едитмодел</span><span class="sxs-lookup"><span data-stu-id="5adb6-419">Update the EditModel</span></span>

<span data-ttu-id="5adb6-420">Добавьте обработчик авторизации, чтобы убедиться, что пользователь владеет контактом.</span><span class="sxs-lookup"><span data-stu-id="5adb6-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="5adb6-421">Так как авторизация ресурса проверяется, `[Authorize]` атрибут недостаточно.</span><span class="sxs-lookup"><span data-stu-id="5adb6-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="5adb6-422">Приложение не имеет доступа к ресурсу при оценке атрибутов.</span><span class="sxs-lookup"><span data-stu-id="5adb6-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="5adb6-423">Авторизация на основе ресурсов должна быть принудительной.</span><span class="sxs-lookup"><span data-stu-id="5adb6-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="5adb6-424">Проверки должны выполняться после того, как приложение будет иметь доступ к ресурсу, загрузив его в модель страницы или загрузив в сам обработчик.</span><span class="sxs-lookup"><span data-stu-id="5adb6-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="5adb6-425">Вы часто обращаетесь к ресурсу, передавая ключ ресурса.</span><span class="sxs-lookup"><span data-stu-id="5adb6-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="5adb6-426">Обновление Делетемодел</span><span class="sxs-lookup"><span data-stu-id="5adb6-426">Update the DeleteModel</span></span>

<span data-ttu-id="5adb6-427">Обновите модель страницы удаления, чтобы использовать обработчик авторизации для проверки наличия у пользователя разрешения на удаление контакта.</span><span class="sxs-lookup"><span data-stu-id="5adb6-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="5adb6-428">Внедрение службы авторизации в представления</span><span class="sxs-lookup"><span data-stu-id="5adb6-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="5adb6-429">В настоящее время в пользовательском интерфейсе отображаются ссылки Edit и DELETE для контактов, которые пользователь не может изменить.</span><span class="sxs-lookup"><span data-stu-id="5adb6-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="5adb6-430">Вставьте службу авторизации в файл *views/_ViewImports. cshtml* , чтобы она была доступна для всех представлений:</span><span class="sxs-lookup"><span data-stu-id="5adb6-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="5adb6-431">Предыдущая разметка добавляет `using` несколько инструкций.</span><span class="sxs-lookup"><span data-stu-id="5adb6-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="5adb6-432">Обновите ссылки **Edit** и **Delete** в *pages/contacts/index. cshtml* , чтобы они отображались только для пользователей с соответствующими разрешениями:</span><span class="sxs-lookup"><span data-stu-id="5adb6-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="5adb6-433">Скрытие ссылок от пользователей, не имеющих разрешений на изменение данных, не защищает приложение.</span><span class="sxs-lookup"><span data-stu-id="5adb6-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="5adb6-434">Скрытие ссылок делает приложение более удобным для пользователей, отображая только допустимые ссылки.</span><span class="sxs-lookup"><span data-stu-id="5adb6-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="5adb6-435">Пользователи могут обращаться к созданным URL-адресам для вызова операций правки и удаления данных, которыми они не владеют.</span><span class="sxs-lookup"><span data-stu-id="5adb6-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="5adb6-436">Страница Razor или контроллер должны принудительно применять проверки доступа для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="5adb6-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="5adb6-437">Сведения об обновлении</span><span class="sxs-lookup"><span data-stu-id="5adb6-437">Update Details</span></span>

<span data-ttu-id="5adb6-438">Обновите представление сведений, чтобы руководители могли утверждать или отклонять контакты:</span><span class="sxs-lookup"><span data-stu-id="5adb6-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="5adb6-439">Обновите модель страницы сведений:</span><span class="sxs-lookup"><span data-stu-id="5adb6-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="5adb6-440">Добавление или удаление пользователя для роли</span><span class="sxs-lookup"><span data-stu-id="5adb6-440">Add or remove a user to a role</span></span>

<span data-ttu-id="5adb6-441">Сведения об [этой ошибке](https://github.com/dotnet/AspNetCore.Docs/issues/8502) см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="5adb6-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="5adb6-442">Удаление привилегий у пользователя.</span><span class="sxs-lookup"><span data-stu-id="5adb6-442">Removing privileges from a user.</span></span> <span data-ttu-id="5adb6-443">Например, отзвука пользователя в приложении разговора.</span><span class="sxs-lookup"><span data-stu-id="5adb6-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="5adb6-444">Добавление привилегий для пользователя.</span><span class="sxs-lookup"><span data-stu-id="5adb6-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="5adb6-445">Тестирование завершенного приложения</span><span class="sxs-lookup"><span data-stu-id="5adb6-445">Test the completed app</span></span>

<span data-ttu-id="5adb6-446">Если вы еще не установили пароль для заполненных учетных записей пользователей, используйте [средство диспетчера секретов](xref:security/app-secrets#secret-manager) , чтобы задать пароль:</span><span class="sxs-lookup"><span data-stu-id="5adb6-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="5adb6-447">Выберите надежный пароль: используйте восемь или более символов и по крайней мере одну прописную букву, цифру и символ.</span><span class="sxs-lookup"><span data-stu-id="5adb6-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="5adb6-448">Например, `Passw0rd!` соответствует требованиям к надежному паролю.</span><span class="sxs-lookup"><span data-stu-id="5adb6-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="5adb6-449">Выполните следующую команду из папки проекта, где `<PW>` — пароль:</span><span class="sxs-lookup"><span data-stu-id="5adb6-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="5adb6-450">Удаление и обновление базы данных</span><span class="sxs-lookup"><span data-stu-id="5adb6-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="5adb6-451">Перезапустите приложение, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="5adb6-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="5adb6-452">Простой способ тестирования завершенного приложения — запуск трех различных браузеров (или режиме инкогнито и нечастных сеансов).</span><span class="sxs-lookup"><span data-stu-id="5adb6-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="5adb6-453">В одном браузере Зарегистрируйте нового пользователя (например, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="5adb6-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="5adb6-454">Войдите в каждый браузер с другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="5adb6-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="5adb6-455">Проверьте следующие операции:</span><span class="sxs-lookup"><span data-stu-id="5adb6-455">Verify the following operations:</span></span>

* <span data-ttu-id="5adb6-456">Зарегистрированные пользователи могут просматривать все утвержденные контактные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="5adb6-457">Зарегистрированные пользователи могут изменять и удалять собственные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="5adb6-458">Руководители могут утверждать и отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="5adb6-459">В `Details` представлении отображаются кнопки **утвердить** и **отклонить** .</span><span class="sxs-lookup"><span data-stu-id="5adb6-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="5adb6-460">Администраторы могут утверждать, отклонять и изменять и удалять все данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="5adb6-461">Пользователь</span><span class="sxs-lookup"><span data-stu-id="5adb6-461">User</span></span>                | <span data-ttu-id="5adb6-462">Заполнено приложением</span><span class="sxs-lookup"><span data-stu-id="5adb6-462">Seeded by the app</span></span> | <span data-ttu-id="5adb6-463">Параметры</span><span class="sxs-lookup"><span data-stu-id="5adb6-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="5adb6-464">нет</span><span class="sxs-lookup"><span data-stu-id="5adb6-464">No</span></span>                | <span data-ttu-id="5adb6-465">Изменение или удаление собственных данных.</span><span class="sxs-lookup"><span data-stu-id="5adb6-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="5adb6-466">Да</span><span class="sxs-lookup"><span data-stu-id="5adb6-466">Yes</span></span>               | <span data-ttu-id="5adb6-467">Утвердите, отклоните и измените или удалите собственные данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="5adb6-468">Да</span><span class="sxs-lookup"><span data-stu-id="5adb6-468">Yes</span></span>               | <span data-ttu-id="5adb6-469">Утвердите или отклоните и измените или удалите все данные.</span><span class="sxs-lookup"><span data-stu-id="5adb6-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="5adb6-470">Создайте контакт в браузере администратора.</span><span class="sxs-lookup"><span data-stu-id="5adb6-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="5adb6-471">Скопируйте URL-адрес для DELETE и Edit из контакта администратора.</span><span class="sxs-lookup"><span data-stu-id="5adb6-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="5adb6-472">Вставьте эти ссылки в браузер тестового пользователя, чтобы убедиться, что тестовая пользователь не может выполнить эти операции.</span><span class="sxs-lookup"><span data-stu-id="5adb6-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="5adb6-473">Создание начального приложения</span><span class="sxs-lookup"><span data-stu-id="5adb6-473">Create the starter app</span></span>

* <span data-ttu-id="5adb6-474">Создание Razor страницы приложения с именем "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="5adb6-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="5adb6-475">Создайте приложение с **учетными записями отдельных пользователей**.</span><span class="sxs-lookup"><span data-stu-id="5adb6-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="5adb6-476">Назовите его "ContactManager", чтобы пространство имен совпадало с пространством имен, используемым в примере.</span><span class="sxs-lookup"><span data-stu-id="5adb6-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="5adb6-477">`-uld`Указывает LocalDB вместо SQLite</span><span class="sxs-lookup"><span data-stu-id="5adb6-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="5adb6-478">Добавление *моделей/Contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="5adb6-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="5adb6-479">Формирование шаблонов `Contact` модели.</span><span class="sxs-lookup"><span data-stu-id="5adb6-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="5adb6-480">Создайте начальную миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="5adb6-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="5adb6-481">Обновите привязку **ContactManager** в файле *pages/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="5adb6-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="5adb6-482">Тестирование приложения путем создания, изменения и удаления контакта</span><span class="sxs-lookup"><span data-stu-id="5adb6-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="5adb6-483">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="5adb6-483">Seed the database</span></span>

<span data-ttu-id="5adb6-484">Добавьте класс [сиддата](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) в папку *Data* .</span><span class="sxs-lookup"><span data-stu-id="5adb6-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="5adb6-485">Вызов `SeedData.Initialize` из `Main`:</span><span class="sxs-lookup"><span data-stu-id="5adb6-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="5adb6-486">Проверьте, что приложение заполнено базой данных.</span><span class="sxs-lookup"><span data-stu-id="5adb6-486">Test that the app seeded the database.</span></span> <span data-ttu-id="5adb6-487">Если в базе данных Contact есть какие бы то ни было строки, метод SEED не выполняется.</span><span class="sxs-lookup"><span data-stu-id="5adb6-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="5adb6-488">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5adb6-488">Additional resources</span></span>

* [<span data-ttu-id="5adb6-489">Создание веб-приложения .NET Core с базой данных SQL в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="5adb6-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="5adb6-490">[ASP.NET Core лаборатории авторизации](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="5adb6-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="5adb6-491">В этом лабораторном занятии подробно рассматриваются функции безопасности, представленные в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="5adb6-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="5adb6-492">Настраиваемая авторизация на основе политик</span><span class="sxs-lookup"><span data-stu-id="5adb6-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
