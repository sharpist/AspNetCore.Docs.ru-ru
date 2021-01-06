---
title: Создание приложения ASP.NET Core с данными пользователя, защищенными с помощью авторизации
author: rick-anderson
description: Узнайте, как создать ASP.NET Core веб-приложение с данными пользователя, защищенными с помощью авторизации. Включает протокол HTTPS, проверку подлинности, безопасность, ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: dc70cfe7cb0c0f044f5f1e7ee68a293b3ea7507f
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854656"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="a0d7d-104">Создание ASP.NET Core веб-приложения с данными пользователя, защищенными с помощью авторизации</span><span class="sxs-lookup"><span data-stu-id="a0d7d-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="a0d7d-105">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Джо Одетт](https://twitter.com/joeaudette) (Joe Audette)</span><span class="sxs-lookup"><span data-stu-id="a0d7d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="a0d7d-106">Просмотреть [этот PDF-файл](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="a0d7d-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a0d7d-107">В этом руководстве показано, как создать веб-приложение ASP.NET Core с данными пользователя, защищенными с помощью авторизации.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="a0d7d-108">Отображается список контактов, которые были созданы пользователями, прошедшими проверку подлинности (зарегистрированные).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="a0d7d-109">Существует три группы безопасности:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-109">There are three security groups:</span></span>

* <span data-ttu-id="a0d7d-110">**Зарегистрированные пользователи** могут просматривать все утвержденные данные, а также изменять и удалять свои данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="a0d7d-111">**Руководители** могут утверждать или отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="a0d7d-112">Пользователям видны только утвержденные контакты.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="a0d7d-113">**Администраторы** могут утверждать, отклонять и изменять и удалять любые данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="a0d7d-114">Изображения в этом документе не полностью соответствуют последним шаблонам.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="a0d7d-115">На следующем рисунке пользователь Рик ( `rick@example.com` ) вошел в.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="a0d7d-116">Рик может просматривать только утвержденные контакты и **редактировать** / **Удалить** / **создать новые** ссылки для своих контактов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="a0d7d-117">Ссылки **Edit** и **Delete** отображаются только в последней записи, созданной Рик.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="a0d7d-118">Другие пользователи не увидят последнюю запись, пока руководитель или администратор не изменит состояние на "утверждено".</span><span class="sxs-lookup"><span data-stu-id="a0d7d-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Снимок экрана, показывающий Рик, выполнивший вход](secure-data/_static/rick.png)

<span data-ttu-id="a0d7d-120">На следующем рисунке `manager@contoso.com` Вход выполнен и в роли руководителя:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Снимок экрана, показывающий manager@contoso.com Вход](secure-data/_static/manager1.png)

<span data-ttu-id="a0d7d-122">На следующем рисунке показано представление сведений об менеджерах для контакта:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-122">The following image shows the managers details view of a contact:</span></span>

![Представление контакта руководителя](secure-data/_static/manager.png)

<span data-ttu-id="a0d7d-124">Кнопки **утвердить** и **отклонить** отображаются только для руководителей и администраторов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="a0d7d-125">На следующем рисунке `admin@contoso.com` Вход выполнен и в роли администратора:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Снимок экрана, показывающий admin@contoso.com Вход](secure-data/_static/admin.png)

<span data-ttu-id="a0d7d-127">Администратор имеет все привилегии.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-127">The administrator has all privileges.</span></span> <span data-ttu-id="a0d7d-128">Она может читать, изменять и удалять контакты, а также изменять состояние контактов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="a0d7d-129">Приложение было создано с помощью [формирования шаблонов](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) следующей `Contact` модели:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="a0d7d-130">Этот пример содержит следующие обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="a0d7d-131">`ContactIsOwnerAuthorizationHandler`: Гарантирует, что пользователь может изменять только свои данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="a0d7d-132">`ContactManagerAuthorizationHandler`: Позволяет руководителям утверждать или отклонять контакты.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="a0d7d-133">`ContactAdministratorsAuthorizationHandler`: Позволяет администраторам утверждать или отклонять контакты, а также изменять и удалять контакты.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a0d7d-134">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="a0d7d-134">Prerequisites</span></span>

<span data-ttu-id="a0d7d-135">Этот учебник расширен.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-135">This tutorial is advanced.</span></span> <span data-ttu-id="a0d7d-136">Вы должны быть знакомы с:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-136">You should be familiar with:</span></span>

* [<span data-ttu-id="a0d7d-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0d7d-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="a0d7d-138">Аутентификация</span><span class="sxs-lookup"><span data-stu-id="a0d7d-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="a0d7d-139">Подтверждение учетной записи и восстановление пароля</span><span class="sxs-lookup"><span data-stu-id="a0d7d-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="a0d7d-140">Авторизация</span><span class="sxs-lookup"><span data-stu-id="a0d7d-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="a0d7d-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a0d7d-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="a0d7d-142">Начальное и завершенное приложение</span><span class="sxs-lookup"><span data-stu-id="a0d7d-142">The starter and completed app</span></span>

<span data-ttu-id="a0d7d-143">[Скачайте](xref:index#how-to-download-a-sample) [готовое](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) приложение.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="a0d7d-144">[Протестируйте](#test-the-completed-app) готовое приложение, чтобы ознакомиться с его функциями безопасности.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="a0d7d-145">Начальное приложение</span><span class="sxs-lookup"><span data-stu-id="a0d7d-145">The starter app</span></span>

<span data-ttu-id="a0d7d-146">[Скачайте](xref:index#how-to-download-a-sample) [Начальное](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) приложение.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="a0d7d-147">Запустите приложение, коснитесь ссылки **ContactManager** и убедитесь, что вы можете создать, изменить и удалить контакт.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="a0d7d-148">Сведения о создании начального приложения см. в разделе [Создание начального приложения](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="a0d7d-149">Защита пользовательских данных</span><span class="sxs-lookup"><span data-stu-id="a0d7d-149">Secure user data</span></span>

<span data-ttu-id="a0d7d-150">Следующие разделы содержат все основные шаги по созданию безопасного приложения для данных пользователей.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="a0d7d-151">Может оказаться полезным ссылаться на завершенный проект.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="a0d7d-152">Связать контактные данные с пользователем</span><span class="sxs-lookup"><span data-stu-id="a0d7d-152">Tie the contact data to the user</span></span>

<span data-ttu-id="a0d7d-153">Используйте [Identity](xref:security/authentication/identity) идентификатор пользователя ASP.NET, чтобы убедиться, что пользователи могут изменять данные, но не данные других пользователей.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="a0d7d-154">Добавьте `OwnerID` и `ContactStatus` в `Contact` модель:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="a0d7d-155">`OwnerID` Идентификатор пользователя из `AspNetUser` таблицы в [Identity](xref:security/authentication/identity) базе данных.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="a0d7d-156">`Status`Поле определяет, можно ли просматривать контакт обычными пользователями.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="a0d7d-157">Создайте новую миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="a0d7d-158">Добавление служб ролей в Identity</span><span class="sxs-lookup"><span data-stu-id="a0d7d-158">Add Role services to Identity</span></span>

<span data-ttu-id="a0d7d-159">Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="a0d7d-160">Требовать прошедших проверку пользователей</span><span class="sxs-lookup"><span data-stu-id="a0d7d-160">Require authenticated users</span></span>

<span data-ttu-id="a0d7d-161">Настройте политику резервной проверки подлинности, чтобы требовать проверку подлинности пользователей:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="a0d7d-162">Выделенный выше код задает [политику резервной проверки подлинности](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="a0d7d-163">Для применения политики резервной проверки подлинности требуется \**_ALL_* _ Users, за исключением Razor страниц, контроллеров или методов действий с атрибутом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="a0d7d-164">Например, Razor страницы, контроллеры или методы действий с `[AllowAnonymous]` или `[Authorize(PolicyName="MyPolicy")]` используют примененный атрибут проверки подлинности, а не политику резервной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="a0d7d-165">Резервная политика проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-165">The fallback authentication policy:</span></span>

<span data-ttu-id="a0d7d-166">_ Применяется ко всем запросам, которые не указывают политику проверки подлинности явным образом.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-166">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="a0d7d-167">Для запросов, обслуживаемых маршрутизацией конечных точек, сюда входят все конечные точки, не указывающие атрибут авторизации.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-167">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="a0d7d-168">Для запросов, обслуживаемых другим по промежуточного слоя, после по промежуточного слоя авторизации, например [статических файлов](xref:fundamentals/static-files), эта политика будет применена ко всем запросам.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-168">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="a0d7d-169">Настройка политики резервной проверки подлинности на требование проверки подлинности пользователей защищает вновь добавленные Razor страницы и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-169">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="a0d7d-170">Необходимость проверки подлинности по умолчанию более безопасна, чем использование новых контроллеров и Razor страниц для включения `[Authorize]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-170">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="a0d7d-171"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Класс также содержит <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-171">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a0d7d-172">`DefaultPolicy`— Это политика, используемая с `[Authorize]` атрибутом, если не указана политика.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-172">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="a0d7d-173">`[Authorize]` не содержит именованную политику, в отличие от `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-173">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="a0d7d-174">Дополнительные сведения о политиках см. в разделе <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-174">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="a0d7d-175">Альтернативный способ для контроллеров и Razor страниц MVC, требующих проверки подлинности всех пользователей, — Добавление фильтра авторизации:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-175">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="a0d7d-176">В приведенном выше коде используется фильтр авторизации, установка политики резервирования использует маршрутизацию конечных точек.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-176">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="a0d7d-177">Настройка политики резервирования является предпочтительным способом требовать проверку подлинности всех пользователей.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-177">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="a0d7d-178">Добавьте [allowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) на `Index` страницы и, `Privacy` чтобы анонимные пользователи могли получить сведения о сайте перед регистрацией:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-178">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="a0d7d-179">Настройка тестовой учетной записи</span><span class="sxs-lookup"><span data-stu-id="a0d7d-179">Configure the test account</span></span>

<span data-ttu-id="a0d7d-180">`SeedData`Класс создает две учетные записи: Administrator и Manager.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-180">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="a0d7d-181">Используйте [средство диспетчера секретов](xref:security/app-secrets) , чтобы задать пароль для этих учетных записей.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-181">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="a0d7d-182">Задайте пароль из каталога проекта (каталога, содержащего *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="a0d7d-182">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="a0d7d-183">Если надежный пароль не указан, при вызове вызывается исключение `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-183">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="a0d7d-184">Обновите `Main` , чтобы использовать тестовый пароль:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-184">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="a0d7d-185">Создание тестовых учетных записей и обновление контактов</span><span class="sxs-lookup"><span data-stu-id="a0d7d-185">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="a0d7d-186">Обновите `Initialize` метод в `SeedData` классе, чтобы создать тестовые учетные записи:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-186">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="a0d7d-187">Добавьте идентификатор пользователя администратора и `ContactStatus` в список контактов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-187">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="a0d7d-188">Сделайте одно из контактов "Отправлено" и одно "Отклонено".</span><span class="sxs-lookup"><span data-stu-id="a0d7d-188">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="a0d7d-189">Добавьте идентификатор пользователя и состояние во все контакты.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-189">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="a0d7d-190">Отображается только один контакт:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-190">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="a0d7d-191">Создание обработчиков авторизации владельца, руководителя и администратора</span><span class="sxs-lookup"><span data-stu-id="a0d7d-191">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="a0d7d-192">Создайте `ContactIsOwnerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-192">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a0d7d-193">`ContactIsOwnerAuthorizationHandler`Проверяет, принадлежит ли ресурс пользователю, работающему с ресурсом.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-193">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="a0d7d-194">`ContactIsOwnerAuthorizationHandler`Контекст вызовов [. Считается успешной](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , если текущий пользователь, прошедший проверку подлинности, является владельцем контакта.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-194">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="a0d7d-195">Обычно обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-195">Authorization handlers generally:</span></span>

* <span data-ttu-id="a0d7d-196">Возврат `context.Succeed` при соблюдении требований.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-196">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="a0d7d-197">Возврат `Task.CompletedTask` , если требования не выполнены.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-197">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="a0d7d-198">`Task.CompletedTask` не является успешным или неудачным, &mdash; что позволяет запускать другие обработчики авторизации.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-198">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="a0d7d-199">Если необходимо явное завершение, возвращаемый [контекст. Не пройдено](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-199">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="a0d7d-200">Приложение позволяет владельцам контактов изменять, удалять и создавать собственные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-200">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="a0d7d-201">`ContactIsOwnerAuthorizationHandler` не требуется проверять операцию, переданную в параметре требования.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-201">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="a0d7d-202">Создание обработчика авторизации диспетчера</span><span class="sxs-lookup"><span data-stu-id="a0d7d-202">Create a manager authorization handler</span></span>

<span data-ttu-id="a0d7d-203">Создайте `ContactManagerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-203">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a0d7d-204">`ContactManagerAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, диспетчером.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-204">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="a0d7d-205">Только руководители могут утверждать или отклонять изменения содержимого (новые или измененные).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-205">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="a0d7d-206">Создание обработчика авторизации администратора</span><span class="sxs-lookup"><span data-stu-id="a0d7d-206">Create an administrator authorization handler</span></span>

<span data-ttu-id="a0d7d-207">Создайте `ContactAdministratorsAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-207">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a0d7d-208">`ContactAdministratorsAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, администратором.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-208">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="a0d7d-209">Администратор может выполнять все операции.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-209">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="a0d7d-210">Регистрация обработчиков авторизации</span><span class="sxs-lookup"><span data-stu-id="a0d7d-210">Register the authorization handlers</span></span>

<span data-ttu-id="a0d7d-211">Службы, использующие Entity Framework Core, должны быть зарегистрированы для [внедрения зависимостей](xref:fundamentals/dependency-injection) с помощью [аддскопед](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-211">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="a0d7d-212">`ContactIsOwnerAuthorizationHandler`Использует ASP.NET Core [Identity](xref:security/authentication/identity) , который построен на Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-212">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="a0d7d-213">Зарегистрируйте обработчики в коллекции служб, чтобы они были доступны `ContactsController` через [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-213">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a0d7d-214">Добавьте следующий код в конец `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a0d7d-214">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="a0d7d-215">`ContactAdministratorsAuthorizationHandler` и `ContactManagerAuthorizationHandler` добавляются в виде Singleton.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-215">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="a0d7d-216">Они являются Singleton-классом, поскольку не используют EF, а вся необходимая информация находится в `Context` параметре `HandleRequirementAsync` метода.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-216">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="a0d7d-217">Поддержка авторизации</span><span class="sxs-lookup"><span data-stu-id="a0d7d-217">Support authorization</span></span>

<span data-ttu-id="a0d7d-218">В этом разделе вы обновите Razor страницы и добавите класс требований к операциям.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-218">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="a0d7d-219">Ознакомьтесь с классом требований к операциям</span><span class="sxs-lookup"><span data-stu-id="a0d7d-219">Review the contact operations requirements class</span></span>

<span data-ttu-id="a0d7d-220">Проверьте `ContactOperations` класс.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-220">Review the `ContactOperations` class.</span></span> <span data-ttu-id="a0d7d-221">Этот класс содержит требования, которые поддерживает приложение:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-221">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="a0d7d-222">Создание базового класса для Razor страниц контактов</span><span class="sxs-lookup"><span data-stu-id="a0d7d-222">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="a0d7d-223">Создайте базовый класс, содержащий службы, используемые на Razor страницах контактов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-223">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="a0d7d-224">Базовый класс помещает код инициализации в одно расположение:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-224">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="a0d7d-225">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-225">The preceding code:</span></span>

* <span data-ttu-id="a0d7d-226">Добавляет `IAuthorizationService` службу для доступа к обработчикам авторизации.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-226">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="a0d7d-227">Добавляет Identity `UserManager` службу.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-227">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="a0d7d-228">Добавьте `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-228">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="a0d7d-229">Обновление Креатемодел</span><span class="sxs-lookup"><span data-stu-id="a0d7d-229">Update the CreateModel</span></span>

<span data-ttu-id="a0d7d-230">Обновите конструктор модели страницы Create, чтобы использовать `DI_BasePageModel` базовый класс:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-230">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="a0d7d-231">Обновите `CreateModel.OnPostAsync` метод, чтобы:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-231">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="a0d7d-232">Добавьте в модель идентификатор пользователя `Contact` .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-232">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="a0d7d-233">Вызовите обработчик авторизации, чтобы убедиться, что пользователь имеет разрешение на создание контактов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-233">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="a0d7d-234">Обновление Индексмодел</span><span class="sxs-lookup"><span data-stu-id="a0d7d-234">Update the IndexModel</span></span>

<span data-ttu-id="a0d7d-235">Обновите `OnGetAsync` метод, чтобы только утвержденные контакты отображались для обычных пользователей:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-235">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="a0d7d-236">Обновление Едитмодел</span><span class="sxs-lookup"><span data-stu-id="a0d7d-236">Update the EditModel</span></span>

<span data-ttu-id="a0d7d-237">Добавьте обработчик авторизации, чтобы убедиться, что пользователь владеет контактом.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-237">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="a0d7d-238">Так как авторизация ресурса проверяется, `[Authorize]` атрибут недостаточно.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-238">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="a0d7d-239">Приложение не имеет доступа к ресурсу при оценке атрибутов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-239">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="a0d7d-240">Авторизация на основе ресурсов должна быть принудительной.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-240">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="a0d7d-241">Проверки должны выполняться после того, как приложение будет иметь доступ к ресурсу, загрузив его в модель страницы или загрузив в сам обработчик.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-241">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="a0d7d-242">Вы часто обращаетесь к ресурсу, передавая ключ ресурса.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-242">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="a0d7d-243">Обновление Делетемодел</span><span class="sxs-lookup"><span data-stu-id="a0d7d-243">Update the DeleteModel</span></span>

<span data-ttu-id="a0d7d-244">Обновите модель страницы удаления, чтобы использовать обработчик авторизации для проверки наличия у пользователя разрешения на удаление контакта.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-244">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="a0d7d-245">Внедрение службы авторизации в представления</span><span class="sxs-lookup"><span data-stu-id="a0d7d-245">Inject the authorization service into the views</span></span>

<span data-ttu-id="a0d7d-246">В настоящее время в пользовательском интерфейсе отображаются ссылки Edit и DELETE для контактов, которые пользователь не может изменить.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-246">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="a0d7d-247">Вставьте службу авторизации в файл *pages/_ViewImports. cshtml* , чтобы он был доступен для всех представлений:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-247">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="a0d7d-248">Предыдущая разметка добавляет несколько `using` инструкций.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-248">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="a0d7d-249">Обновите ссылки **Edit** и **Delete** в *pages/contacts/index. cshtml* , чтобы они отображались только для пользователей с соответствующими разрешениями:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-249">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="a0d7d-250">Скрытие ссылок от пользователей, не имеющих разрешений на изменение данных, не защищает приложение.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-250">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="a0d7d-251">Скрытие ссылок делает приложение более удобным для пользователей, отображая только допустимые ссылки.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-251">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="a0d7d-252">Пользователи могут обращаться к созданным URL-адресам для вызова операций правки и удаления данных, которыми они не владеют.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-252">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="a0d7d-253">RazorСтраница или контроллер должны применять проверки доступа для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-253">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="a0d7d-254">Сведения об обновлении</span><span class="sxs-lookup"><span data-stu-id="a0d7d-254">Update Details</span></span>

<span data-ttu-id="a0d7d-255">Обновите представление сведений, чтобы руководители могли утверждать или отклонять контакты:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-255">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="a0d7d-256">Обновите модель страницы сведений:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-256">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="a0d7d-257">Добавление или удаление пользователя для роли</span><span class="sxs-lookup"><span data-stu-id="a0d7d-257">Add or remove a user to a role</span></span>

<span data-ttu-id="a0d7d-258">Сведения об [этой ошибке](https://github.com/dotnet/AspNetCore.Docs/issues/8502) см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-258">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="a0d7d-259">Удаление привилегий у пользователя.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-259">Removing privileges from a user.</span></span> <span data-ttu-id="a0d7d-260">Например, отзвука пользователя в приложении разговора.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-260">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="a0d7d-261">Добавление привилегий для пользователя.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-261">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="a0d7d-262">Различия между запросом и запретом</span><span class="sxs-lookup"><span data-stu-id="a0d7d-262">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="a0d7d-263">Это приложение задает политику по умолчанию, [требующую проверки подлинности пользователей](#rau).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-263">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="a0d7d-264">Следующий код позволяет анонимным пользователям.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-264">The following code allows anonymous users.</span></span> <span data-ttu-id="a0d7d-265">Анонимным пользователям разрешено показывать различия между запросом и запретом.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-265">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="a0d7d-266">В предыдущем коде:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-266">In the preceding code:</span></span>

* <span data-ttu-id="a0d7d-267">Если пользователь **не** прошел проверку подлинности, `ChallengeResult` возвращается значение.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-267">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="a0d7d-268">Когда `ChallengeResult` возвращается, пользователь перенаправляется на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-268">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="a0d7d-269">Если пользователь прошел проверку подлинности, но не авторизован, `ForbidResult` возвращается значение.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-269">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="a0d7d-270">Когда `ForbidResult` возвращается значение, пользователь перенаправляется на страницу отказ в доступе.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-270">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="a0d7d-271">Тестирование завершенного приложения</span><span class="sxs-lookup"><span data-stu-id="a0d7d-271">Test the completed app</span></span>

<span data-ttu-id="a0d7d-272">Если вы еще не установили пароль для заполненных учетных записей пользователей, используйте [средство диспетчера секретов](xref:security/app-secrets#secret-manager) , чтобы задать пароль:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-272">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="a0d7d-273">Выберите надежный пароль: используйте восемь или более символов и по крайней мере одну прописную букву, цифру и символ.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-273">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="a0d7d-274">Например, `Passw0rd!` соответствует требованиям к надежному паролю.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-274">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="a0d7d-275">Выполните следующую команду из папки проекта, где `<PW>` — пароль:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-275">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="a0d7d-276">Если у приложения есть контакты:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-276">If the app has contacts:</span></span>

* <span data-ttu-id="a0d7d-277">Удалите все записи в `Contact` таблице.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-277">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="a0d7d-278">Перезапустите приложение, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-278">Restart the app to seed the database.</span></span>

<span data-ttu-id="a0d7d-279">Простой способ тестирования завершенного приложения — запуск трех различных браузеров (или режиме инкогнито и нечастных сеансов).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-279">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="a0d7d-280">В одном браузере Зарегистрируйте нового пользователя (например, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-280">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="a0d7d-281">Войдите в каждый браузер с другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-281">Sign in to each browser with a different user.</span></span> <span data-ttu-id="a0d7d-282">Проверьте следующие операции:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-282">Verify the following operations:</span></span>

* <span data-ttu-id="a0d7d-283">Зарегистрированные пользователи могут просматривать все утвержденные контактные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-283">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="a0d7d-284">Зарегистрированные пользователи могут изменять и удалять собственные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-284">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="a0d7d-285">Руководители могут утверждать и отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-285">Managers can approve/reject contact data.</span></span> <span data-ttu-id="a0d7d-286">В `Details` представлении отображаются кнопки **утвердить** и **отклонить** .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-286">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="a0d7d-287">Администраторы могут утверждать, отклонять и изменять и удалять все данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-287">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="a0d7d-288">User (Пользователь)</span><span class="sxs-lookup"><span data-stu-id="a0d7d-288">User</span></span>                | <span data-ttu-id="a0d7d-289">Заполнено приложением</span><span class="sxs-lookup"><span data-stu-id="a0d7d-289">Seeded by the app</span></span> | <span data-ttu-id="a0d7d-290">Варианты</span><span class="sxs-lookup"><span data-stu-id="a0d7d-290">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="a0d7d-291">Нет</span><span class="sxs-lookup"><span data-stu-id="a0d7d-291">No</span></span>                | <span data-ttu-id="a0d7d-292">Изменение или удаление собственных данных.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-292">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="a0d7d-293">Да</span><span class="sxs-lookup"><span data-stu-id="a0d7d-293">Yes</span></span>               | <span data-ttu-id="a0d7d-294">Утвердите, отклоните и измените или удалите собственные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-294">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="a0d7d-295">Да</span><span class="sxs-lookup"><span data-stu-id="a0d7d-295">Yes</span></span>               | <span data-ttu-id="a0d7d-296">Утвердите или отклоните и измените или удалите все данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-296">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="a0d7d-297">Создайте контакт в браузере администратора.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-297">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="a0d7d-298">Скопируйте URL-адрес для DELETE и Edit из контакта администратора.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-298">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="a0d7d-299">Вставьте эти ссылки в браузер тестового пользователя, чтобы убедиться, что тестовая пользователь не может выполнить эти операции.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-299">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="a0d7d-300">Создание начального приложения</span><span class="sxs-lookup"><span data-stu-id="a0d7d-300">Create the starter app</span></span>

* <span data-ttu-id="a0d7d-301">Создание Razor страницы приложения с именем "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="a0d7d-301">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="a0d7d-302">Создайте приложение с **учетными записями отдельных пользователей**.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-302">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="a0d7d-303">Назовите его "ContactManager", чтобы пространство имен совпадало с пространством имен, используемым в примере.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-303">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="a0d7d-304">`-uld` Указывает LocalDB вместо SQLite</span><span class="sxs-lookup"><span data-stu-id="a0d7d-304">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="a0d7d-305">Добавление *моделей/Contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-305">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="a0d7d-306">Формирование шаблонов `Contact` модели.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-306">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="a0d7d-307">Создайте начальную миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-307">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="a0d7d-308">Если при выполнении команды возникла ошибка `dotnet aspnet-codegenerator razorpage` , см. [эту ошибку в GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-308">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="a0d7d-309">Обновите привязку **ContactManager** в файле *pages/shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a0d7d-309">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="a0d7d-310">Тестирование приложения путем создания, изменения и удаления контакта</span><span class="sxs-lookup"><span data-stu-id="a0d7d-310">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="a0d7d-311">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="a0d7d-311">Seed the database</span></span>

<span data-ttu-id="a0d7d-312">Добавьте класс [сиддата](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) в папку *Data* :</span><span class="sxs-lookup"><span data-stu-id="a0d7d-312">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="a0d7d-313">Вызов `SeedData.Initialize` из `Main` :</span><span class="sxs-lookup"><span data-stu-id="a0d7d-313">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="a0d7d-314">Проверьте, что приложение заполнено базой данных.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-314">Test that the app seeded the database.</span></span> <span data-ttu-id="a0d7d-315">Если в базе данных Contact есть какие бы то ни было строки, метод SEED не выполняется.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-315">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="a0d7d-316">В этом руководстве показано, как создать веб-приложение ASP.NET Core с данными пользователя, защищенными с помощью авторизации.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-316">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="a0d7d-317">Отображается список контактов, которые были созданы пользователями, прошедшими проверку подлинности (зарегистрированные).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-317">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="a0d7d-318">Существует три группы безопасности:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-318">There are three security groups:</span></span>

* <span data-ttu-id="a0d7d-319">**Зарегистрированные пользователи** могут просматривать все утвержденные данные, а также изменять и удалять свои данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-319">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="a0d7d-320">**Руководители** могут утверждать или отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-320">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="a0d7d-321">Пользователям видны только утвержденные контакты.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-321">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="a0d7d-322">**Администраторы** могут утверждать, отклонять и изменять и удалять любые данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-322">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="a0d7d-323">На следующем рисунке пользователь Рик ( `rick@example.com` ) вошел в.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-323">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="a0d7d-324">Рик может просматривать только утвержденные контакты и **редактировать** / **Удалить** / **создать новые** ссылки для своих контактов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-324">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="a0d7d-325">Ссылки **Edit** и **Delete** отображаются только в последней записи, созданной Рик.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-325">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="a0d7d-326">Другие пользователи не увидят последнюю запись, пока руководитель или администратор не изменит состояние на "утверждено".</span><span class="sxs-lookup"><span data-stu-id="a0d7d-326">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Снимок экрана, показывающий Рик, выполнивший вход](secure-data/_static/rick.png)

<span data-ttu-id="a0d7d-328">На следующем рисунке `manager@contoso.com` Вход выполнен и в роли руководителя:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-328">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Снимок экрана, показывающий manager@contoso.com Вход](secure-data/_static/manager1.png)

<span data-ttu-id="a0d7d-330">На следующем рисунке показано представление сведений об менеджерах для контакта:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-330">The following image shows the managers details view of a contact:</span></span>

![Представление контакта руководителя](secure-data/_static/manager.png)

<span data-ttu-id="a0d7d-332">Кнопки **утвердить** и **отклонить** отображаются только для руководителей и администраторов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-332">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="a0d7d-333">На следующем рисунке `admin@contoso.com` Вход выполнен и в роли администратора:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-333">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Снимок экрана, показывающий admin@contoso.com Вход](secure-data/_static/admin.png)

<span data-ttu-id="a0d7d-335">Администратор имеет все привилегии.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-335">The administrator has all privileges.</span></span> <span data-ttu-id="a0d7d-336">Она может читать, изменять и удалять контакты, а также изменять состояние контактов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-336">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="a0d7d-337">Приложение было создано с помощью [формирования шаблонов](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) следующей `Contact` модели:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-337">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="a0d7d-338">Этот пример содержит следующие обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-338">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="a0d7d-339">`ContactIsOwnerAuthorizationHandler`: Гарантирует, что пользователь может изменять только свои данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-339">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="a0d7d-340">`ContactManagerAuthorizationHandler`: Позволяет руководителям утверждать или отклонять контакты.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-340">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="a0d7d-341">`ContactAdministratorsAuthorizationHandler`: Позволяет администраторам утверждать или отклонять контакты, а также изменять и удалять контакты.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-341">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a0d7d-342">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="a0d7d-342">Prerequisites</span></span>

<span data-ttu-id="a0d7d-343">Этот учебник расширен.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-343">This tutorial is advanced.</span></span> <span data-ttu-id="a0d7d-344">Вы должны быть знакомы с:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-344">You should be familiar with:</span></span>

* [<span data-ttu-id="a0d7d-345">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0d7d-345">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="a0d7d-346">Аутентификация</span><span class="sxs-lookup"><span data-stu-id="a0d7d-346">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="a0d7d-347">Подтверждение учетной записи и восстановление пароля</span><span class="sxs-lookup"><span data-stu-id="a0d7d-347">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="a0d7d-348">Авторизация</span><span class="sxs-lookup"><span data-stu-id="a0d7d-348">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="a0d7d-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a0d7d-349">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="a0d7d-350">Начальное и завершенное приложение</span><span class="sxs-lookup"><span data-stu-id="a0d7d-350">The starter and completed app</span></span>

<span data-ttu-id="a0d7d-351">[Скачайте](xref:index#how-to-download-a-sample) [готовое](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) приложение.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-351">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="a0d7d-352">[Протестируйте](#test-the-completed-app) готовое приложение, чтобы ознакомиться с его функциями безопасности.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-352">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="a0d7d-353">Начальное приложение</span><span class="sxs-lookup"><span data-stu-id="a0d7d-353">The starter app</span></span>

<span data-ttu-id="a0d7d-354">[Скачайте](xref:index#how-to-download-a-sample) [Начальное](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) приложение.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-354">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="a0d7d-355">Запустите приложение, коснитесь ссылки **ContactManager** и убедитесь, что вы можете создать, изменить и удалить контакт.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-355">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="a0d7d-356">Защита пользовательских данных</span><span class="sxs-lookup"><span data-stu-id="a0d7d-356">Secure user data</span></span>

<span data-ttu-id="a0d7d-357">Следующие разделы содержат все основные шаги по созданию безопасного приложения для данных пользователей.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-357">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="a0d7d-358">Может оказаться полезным ссылаться на завершенный проект.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-358">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="a0d7d-359">Связать контактные данные с пользователем</span><span class="sxs-lookup"><span data-stu-id="a0d7d-359">Tie the contact data to the user</span></span>

<span data-ttu-id="a0d7d-360">Используйте [Identity](xref:security/authentication/identity) идентификатор пользователя ASP.NET, чтобы убедиться, что пользователи могут изменять данные, но не данные других пользователей.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-360">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="a0d7d-361">Добавьте `OwnerID` и `ContactStatus` в `Contact` модель:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-361">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="a0d7d-362">`OwnerID` Идентификатор пользователя из `AspNetUser` таблицы в [Identity](xref:security/authentication/identity) базе данных.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-362">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="a0d7d-363">`Status`Поле определяет, можно ли просматривать контакт обычными пользователями.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-363">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="a0d7d-364">Создайте новую миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-364">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="a0d7d-365">Добавление служб ролей в Identity</span><span class="sxs-lookup"><span data-stu-id="a0d7d-365">Add Role services to Identity</span></span>

<span data-ttu-id="a0d7d-366">Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-366">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="a0d7d-367">Требовать прошедших проверку пользователей</span><span class="sxs-lookup"><span data-stu-id="a0d7d-367">Require authenticated users</span></span>

<span data-ttu-id="a0d7d-368">Задайте политику проверки подлинности по умолчанию, чтобы требовать проверку подлинности пользователей:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-368">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="a0d7d-369">Можно отказаться от проверки подлинности на Razor уровне страницы, контроллера или метода действия с помощью `[AllowAnonymous]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-369">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="a0d7d-370">Настройка политики проверки подлинности по умолчанию для обязательной проверки подлинности пользователей защищает вновь добавленные Razor страницы и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-370">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="a0d7d-371">Необходимость проверки подлинности по умолчанию более безопасна, чем использование новых контроллеров и Razor страниц для включения `[Authorize]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-371">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="a0d7d-372">Добавьте [allowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) в индекс, About и Contact Pages, чтобы анонимные пользователи могли получить сведения о сайте перед регистрацией.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-372">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="a0d7d-373">Настройка тестовой учетной записи</span><span class="sxs-lookup"><span data-stu-id="a0d7d-373">Configure the test account</span></span>

<span data-ttu-id="a0d7d-374">`SeedData`Класс создает две учетные записи: Administrator и Manager.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-374">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="a0d7d-375">Используйте [средство диспетчера секретов](xref:security/app-secrets) , чтобы задать пароль для этих учетных записей.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-375">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="a0d7d-376">Задайте пароль из каталога проекта (каталога, содержащего *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="a0d7d-376">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="a0d7d-377">Если надежный пароль не указан, при вызове вызывается исключение `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-377">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="a0d7d-378">Обновите `Main` , чтобы использовать тестовый пароль:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-378">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="a0d7d-379">Создание тестовых учетных записей и обновление контактов</span><span class="sxs-lookup"><span data-stu-id="a0d7d-379">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="a0d7d-380">Обновите `Initialize` метод в `SeedData` классе, чтобы создать тестовые учетные записи:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-380">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="a0d7d-381">Добавьте идентификатор пользователя администратора и `ContactStatus` в список контактов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-381">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="a0d7d-382">Сделайте одно из контактов "Отправлено" и одно "Отклонено".</span><span class="sxs-lookup"><span data-stu-id="a0d7d-382">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="a0d7d-383">Добавьте идентификатор пользователя и состояние во все контакты.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-383">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="a0d7d-384">Отображается только один контакт:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-384">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="a0d7d-385">Создание обработчиков авторизации владельца, руководителя и администратора</span><span class="sxs-lookup"><span data-stu-id="a0d7d-385">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="a0d7d-386">Создайте папку *авторизации* и создайте `ContactIsOwnerAuthorizationHandler` в ней класс.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-386">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="a0d7d-387">`ContactIsOwnerAuthorizationHandler`Проверяет, принадлежит ли ресурс пользователю, работающему с ресурсом.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-387">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="a0d7d-388">`ContactIsOwnerAuthorizationHandler`Контекст вызовов [. Считается успешной](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , если текущий пользователь, прошедший проверку подлинности, является владельцем контакта.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-388">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="a0d7d-389">Обычно обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-389">Authorization handlers generally:</span></span>

* <span data-ttu-id="a0d7d-390">Возврат `context.Succeed` при соблюдении требований.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-390">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="a0d7d-391">Возврат `Task.CompletedTask` , если требования не выполнены.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-391">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="a0d7d-392">`Task.CompletedTask` не является успешным или неудачным, &mdash; что позволяет запускать другие обработчики авторизации.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-392">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="a0d7d-393">Если необходимо явное завершение, возвращаемый [контекст. Не пройдено](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-393">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="a0d7d-394">Приложение позволяет владельцам контактов изменять, удалять и создавать собственные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-394">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="a0d7d-395">`ContactIsOwnerAuthorizationHandler` не требуется проверять операцию, переданную в параметре требования.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-395">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="a0d7d-396">Создание обработчика авторизации диспетчера</span><span class="sxs-lookup"><span data-stu-id="a0d7d-396">Create a manager authorization handler</span></span>

<span data-ttu-id="a0d7d-397">Создайте `ContactManagerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-397">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a0d7d-398">`ContactManagerAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, диспетчером.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-398">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="a0d7d-399">Только руководители могут утверждать или отклонять изменения содержимого (новые или измененные).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-399">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="a0d7d-400">Создание обработчика авторизации администратора</span><span class="sxs-lookup"><span data-stu-id="a0d7d-400">Create an administrator authorization handler</span></span>

<span data-ttu-id="a0d7d-401">Создайте `ContactAdministratorsAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-401">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a0d7d-402">`ContactAdministratorsAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, администратором.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-402">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="a0d7d-403">Администратор может выполнять все операции.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-403">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="a0d7d-404">Регистрация обработчиков авторизации</span><span class="sxs-lookup"><span data-stu-id="a0d7d-404">Register the authorization handlers</span></span>

<span data-ttu-id="a0d7d-405">Службы, использующие Entity Framework Core, должны быть зарегистрированы для [внедрения зависимостей](xref:fundamentals/dependency-injection) с помощью [аддскопед](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-405">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="a0d7d-406">`ContactIsOwnerAuthorizationHandler`Использует ASP.NET Core [Identity](xref:security/authentication/identity) , который построен на Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-406">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="a0d7d-407">Зарегистрируйте обработчики в коллекции служб, чтобы они были доступны `ContactsController` через [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-407">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a0d7d-408">Добавьте следующий код в конец `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a0d7d-408">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="a0d7d-409">`ContactAdministratorsAuthorizationHandler` и `ContactManagerAuthorizationHandler` добавляются в виде Singleton.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-409">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="a0d7d-410">Они являются Singleton-классом, поскольку не используют EF, а вся необходимая информация находится в `Context` параметре `HandleRequirementAsync` метода.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-410">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="a0d7d-411">Поддержка авторизации</span><span class="sxs-lookup"><span data-stu-id="a0d7d-411">Support authorization</span></span>

<span data-ttu-id="a0d7d-412">В этом разделе вы обновите Razor страницы и добавите класс требований к операциям.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-412">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="a0d7d-413">Ознакомьтесь с классом требований к операциям</span><span class="sxs-lookup"><span data-stu-id="a0d7d-413">Review the contact operations requirements class</span></span>

<span data-ttu-id="a0d7d-414">Проверьте `ContactOperations` класс.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-414">Review the `ContactOperations` class.</span></span> <span data-ttu-id="a0d7d-415">Этот класс содержит требования, которые поддерживает приложение:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-415">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="a0d7d-416">Создание базового класса для Razor страниц контактов</span><span class="sxs-lookup"><span data-stu-id="a0d7d-416">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="a0d7d-417">Создайте базовый класс, содержащий службы, используемые на Razor страницах контактов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-417">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="a0d7d-418">Базовый класс помещает код инициализации в одно расположение:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-418">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="a0d7d-419">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-419">The preceding code:</span></span>

* <span data-ttu-id="a0d7d-420">Добавляет `IAuthorizationService` службу для доступа к обработчикам авторизации.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-420">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="a0d7d-421">Добавляет Identity `UserManager` службу.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-421">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="a0d7d-422">Добавьте `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-422">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="a0d7d-423">Обновление Креатемодел</span><span class="sxs-lookup"><span data-stu-id="a0d7d-423">Update the CreateModel</span></span>

<span data-ttu-id="a0d7d-424">Обновите конструктор модели страницы Create, чтобы использовать `DI_BasePageModel` базовый класс:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-424">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="a0d7d-425">Обновите `CreateModel.OnPostAsync` метод, чтобы:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-425">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="a0d7d-426">Добавьте в модель идентификатор пользователя `Contact` .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-426">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="a0d7d-427">Вызовите обработчик авторизации, чтобы убедиться, что пользователь имеет разрешение на создание контактов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-427">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="a0d7d-428">Обновление Индексмодел</span><span class="sxs-lookup"><span data-stu-id="a0d7d-428">Update the IndexModel</span></span>

<span data-ttu-id="a0d7d-429">Обновите `OnGetAsync` метод, чтобы только утвержденные контакты отображались для обычных пользователей:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-429">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="a0d7d-430">Обновление Едитмодел</span><span class="sxs-lookup"><span data-stu-id="a0d7d-430">Update the EditModel</span></span>

<span data-ttu-id="a0d7d-431">Добавьте обработчик авторизации, чтобы убедиться, что пользователь владеет контактом.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-431">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="a0d7d-432">Так как авторизация ресурса проверяется, `[Authorize]` атрибут недостаточно.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-432">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="a0d7d-433">Приложение не имеет доступа к ресурсу при оценке атрибутов.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-433">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="a0d7d-434">Авторизация на основе ресурсов должна быть принудительной.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-434">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="a0d7d-435">Проверки должны выполняться после того, как приложение будет иметь доступ к ресурсу, загрузив его в модель страницы или загрузив в сам обработчик.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-435">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="a0d7d-436">Вы часто обращаетесь к ресурсу, передавая ключ ресурса.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-436">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="a0d7d-437">Обновление Делетемодел</span><span class="sxs-lookup"><span data-stu-id="a0d7d-437">Update the DeleteModel</span></span>

<span data-ttu-id="a0d7d-438">Обновите модель страницы удаления, чтобы использовать обработчик авторизации для проверки наличия у пользователя разрешения на удаление контакта.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-438">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="a0d7d-439">Внедрение службы авторизации в представления</span><span class="sxs-lookup"><span data-stu-id="a0d7d-439">Inject the authorization service into the views</span></span>

<span data-ttu-id="a0d7d-440">В настоящее время в пользовательском интерфейсе отображаются ссылки Edit и DELETE для контактов, которые пользователь не может изменить.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-440">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="a0d7d-441">Вставьте службу авторизации в файл *views/_ViewImports. cshtml* , чтобы она была доступна для всех представлений:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-441">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="a0d7d-442">Предыдущая разметка добавляет несколько `using` инструкций.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-442">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="a0d7d-443">Обновите ссылки **Edit** и **Delete** в *pages/contacts/index. cshtml* , чтобы они отображались только для пользователей с соответствующими разрешениями:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-443">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="a0d7d-444">Скрытие ссылок от пользователей, не имеющих разрешений на изменение данных, не защищает приложение.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-444">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="a0d7d-445">Скрытие ссылок делает приложение более удобным для пользователей, отображая только допустимые ссылки.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-445">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="a0d7d-446">Пользователи могут обращаться к созданным URL-адресам для вызова операций правки и удаления данных, которыми они не владеют.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-446">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="a0d7d-447">RazorСтраница или контроллер должны применять проверки доступа для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-447">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="a0d7d-448">Сведения об обновлении</span><span class="sxs-lookup"><span data-stu-id="a0d7d-448">Update Details</span></span>

<span data-ttu-id="a0d7d-449">Обновите представление сведений, чтобы руководители могли утверждать или отклонять контакты:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-449">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="a0d7d-450">Обновите модель страницы сведений:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-450">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="a0d7d-451">Добавление или удаление пользователя для роли</span><span class="sxs-lookup"><span data-stu-id="a0d7d-451">Add or remove a user to a role</span></span>

<span data-ttu-id="a0d7d-452">Сведения об [этой ошибке](https://github.com/dotnet/AspNetCore.Docs/issues/8502) см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-452">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="a0d7d-453">Удаление привилегий у пользователя.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-453">Removing privileges from a user.</span></span> <span data-ttu-id="a0d7d-454">Например, отзвука пользователя в приложении разговора.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-454">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="a0d7d-455">Добавление привилегий для пользователя.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-455">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="a0d7d-456">Тестирование завершенного приложения</span><span class="sxs-lookup"><span data-stu-id="a0d7d-456">Test the completed app</span></span>

<span data-ttu-id="a0d7d-457">Если вы еще не установили пароль для заполненных учетных записей пользователей, используйте [средство диспетчера секретов](xref:security/app-secrets#secret-manager) , чтобы задать пароль:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-457">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="a0d7d-458">Выберите надежный пароль: используйте восемь или более символов и по крайней мере одну прописную букву, цифру и символ.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-458">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="a0d7d-459">Например, `Passw0rd!` соответствует требованиям к надежному паролю.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-459">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="a0d7d-460">Выполните следующую команду из папки проекта, где `<PW>` — пароль:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-460">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="a0d7d-461">Удаление и обновление базы данных</span><span class="sxs-lookup"><span data-stu-id="a0d7d-461">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="a0d7d-462">Перезапустите приложение, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-462">Restart the app to seed the database.</span></span>

<span data-ttu-id="a0d7d-463">Простой способ тестирования завершенного приложения — запуск трех различных браузеров (или режиме инкогнито и нечастных сеансов).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-463">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="a0d7d-464">В одном браузере Зарегистрируйте нового пользователя (например, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-464">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="a0d7d-465">Войдите в каждый браузер с другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-465">Sign in to each browser with a different user.</span></span> <span data-ttu-id="a0d7d-466">Проверьте следующие операции:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-466">Verify the following operations:</span></span>

* <span data-ttu-id="a0d7d-467">Зарегистрированные пользователи могут просматривать все утвержденные контактные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-467">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="a0d7d-468">Зарегистрированные пользователи могут изменять и удалять собственные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-468">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="a0d7d-469">Руководители могут утверждать и отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-469">Managers can approve/reject contact data.</span></span> <span data-ttu-id="a0d7d-470">В `Details` представлении отображаются кнопки **утвердить** и **отклонить** .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-470">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="a0d7d-471">Администраторы могут утверждать, отклонять и изменять и удалять все данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-471">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="a0d7d-472">User (Пользователь)</span><span class="sxs-lookup"><span data-stu-id="a0d7d-472">User</span></span>                | <span data-ttu-id="a0d7d-473">Заполнено приложением</span><span class="sxs-lookup"><span data-stu-id="a0d7d-473">Seeded by the app</span></span> | <span data-ttu-id="a0d7d-474">Варианты</span><span class="sxs-lookup"><span data-stu-id="a0d7d-474">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="a0d7d-475">Нет</span><span class="sxs-lookup"><span data-stu-id="a0d7d-475">No</span></span>                | <span data-ttu-id="a0d7d-476">Изменение или удаление собственных данных.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-476">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="a0d7d-477">Да</span><span class="sxs-lookup"><span data-stu-id="a0d7d-477">Yes</span></span>               | <span data-ttu-id="a0d7d-478">Утвердите, отклоните и измените или удалите собственные данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-478">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="a0d7d-479">Да</span><span class="sxs-lookup"><span data-stu-id="a0d7d-479">Yes</span></span>               | <span data-ttu-id="a0d7d-480">Утвердите или отклоните и измените или удалите все данные.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-480">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="a0d7d-481">Создайте контакт в браузере администратора.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-481">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="a0d7d-482">Скопируйте URL-адрес для DELETE и Edit из контакта администратора.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-482">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="a0d7d-483">Вставьте эти ссылки в браузер тестового пользователя, чтобы убедиться, что тестовая пользователь не может выполнить эти операции.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-483">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="a0d7d-484">Создание начального приложения</span><span class="sxs-lookup"><span data-stu-id="a0d7d-484">Create the starter app</span></span>

* <span data-ttu-id="a0d7d-485">Создание Razor страницы приложения с именем "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="a0d7d-485">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="a0d7d-486">Создайте приложение с **учетными записями отдельных пользователей**.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-486">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="a0d7d-487">Назовите его "ContactManager", чтобы пространство имен совпадало с пространством имен, используемым в примере.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-487">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="a0d7d-488">`-uld` Указывает LocalDB вместо SQLite</span><span class="sxs-lookup"><span data-stu-id="a0d7d-488">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="a0d7d-489">Добавление *моделей/Contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-489">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="a0d7d-490">Формирование шаблонов `Contact` модели.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-490">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="a0d7d-491">Создайте начальную миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="a0d7d-491">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="a0d7d-492">Обновите привязку **ContactManager** в файле *pages/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a0d7d-492">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="a0d7d-493">Тестирование приложения путем создания, изменения и удаления контакта</span><span class="sxs-lookup"><span data-stu-id="a0d7d-493">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="a0d7d-494">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="a0d7d-494">Seed the database</span></span>

<span data-ttu-id="a0d7d-495">Добавьте класс [сиддата](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) в папку *Data* .</span><span class="sxs-lookup"><span data-stu-id="a0d7d-495">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="a0d7d-496">Вызов `SeedData.Initialize` из `Main` :</span><span class="sxs-lookup"><span data-stu-id="a0d7d-496">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="a0d7d-497">Проверьте, что приложение заполнено базой данных.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-497">Test that the app seeded the database.</span></span> <span data-ttu-id="a0d7d-498">Если в базе данных Contact есть какие бы то ни было строки, метод SEED не выполняется.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-498">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="a0d7d-499">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a0d7d-499">Additional resources</span></span>

* [<span data-ttu-id="a0d7d-500">Сборка веб-приложения .NET Core и базы данных SQL в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="a0d7d-500">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="a0d7d-501">[ASP.NET Core лаборатории авторизации](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="a0d7d-501">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="a0d7d-502">В этом лабораторном занятии подробно рассматриваются функции безопасности, представленные в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="a0d7d-502">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="a0d7d-503">Настраиваемая авторизация на основе политик</span><span class="sxs-lookup"><span data-stu-id="a0d7d-503">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
