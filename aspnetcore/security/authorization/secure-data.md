---
title: Создание приложения ASP.NET Core с данными пользователя, защищенными с помощью авторизации
author: rick-anderson
description: Узнайте, как создать ASP.NET Core веб-приложение с данными пользователя, защищенными с помощью авторизации. Включает протокол HTTPS, проверку подлинности, Безопасность ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
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
ms.openlocfilehash: 44777369693f9eb29d78c3ba638db2e692f430ae
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021193"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="93802-104">Создание ASP.NET Core веб-приложения с данными пользователя, защищенными с помощью авторизации</span><span class="sxs-lookup"><span data-stu-id="93802-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="93802-105">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Джо Одетт](https://twitter.com/joeaudette) (Joe Audette)</span><span class="sxs-lookup"><span data-stu-id="93802-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="93802-106">Просмотреть [этот PDF-файл](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="93802-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="93802-107">В этом руководстве показано, как создать веб-приложение ASP.NET Core с данными пользователя, защищенными с помощью авторизации.</span><span class="sxs-lookup"><span data-stu-id="93802-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="93802-108">Отображается список контактов, которые были созданы пользователями, прошедшими проверку подлинности (зарегистрированные).</span><span class="sxs-lookup"><span data-stu-id="93802-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="93802-109">Существует три группы безопасности:</span><span class="sxs-lookup"><span data-stu-id="93802-109">There are three security groups:</span></span>

* <span data-ttu-id="93802-110">**Зарегистрированные пользователи** могут просматривать все утвержденные данные, а также изменять и удалять свои данные.</span><span class="sxs-lookup"><span data-stu-id="93802-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="93802-111">**Руководители** могут утверждать или отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="93802-112">Пользователям видны только утвержденные контакты.</span><span class="sxs-lookup"><span data-stu-id="93802-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="93802-113">**Администраторы** могут утверждать, отклонять и изменять и удалять любые данные.</span><span class="sxs-lookup"><span data-stu-id="93802-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="93802-114">Изображения в этом документе не полностью соответствуют последним шаблонам.</span><span class="sxs-lookup"><span data-stu-id="93802-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="93802-115">На следующем рисунке пользователь Рик ( `rick@example.com` ) вошел в.</span><span class="sxs-lookup"><span data-stu-id="93802-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="93802-116">Рик может просматривать только утвержденные контакты и **редактировать** / **Удалить** / **создать новые** ссылки для своих контактов.</span><span class="sxs-lookup"><span data-stu-id="93802-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="93802-117">Ссылки **Edit** и **Delete** отображаются только в последней записи, созданной Рик.</span><span class="sxs-lookup"><span data-stu-id="93802-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="93802-118">Другие пользователи не увидят последнюю запись, пока руководитель или администратор не изменит состояние на "утверждено".</span><span class="sxs-lookup"><span data-stu-id="93802-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Снимок экрана, показывающий Рик, выполнивший вход](secure-data/_static/rick.png)

<span data-ttu-id="93802-120">На следующем рисунке `manager@contoso.com` Вход выполнен и в роли руководителя:</span><span class="sxs-lookup"><span data-stu-id="93802-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Снимок экрана, показывающий manager@contoso.com Вход](secure-data/_static/manager1.png)

<span data-ttu-id="93802-122">На следующем рисунке показано представление сведений об менеджерах для контакта:</span><span class="sxs-lookup"><span data-stu-id="93802-122">The following image shows the managers details view of a contact:</span></span>

![Представление контакта руководителя](secure-data/_static/manager.png)

<span data-ttu-id="93802-124">Кнопки **утвердить** и **отклонить** отображаются только для руководителей и администраторов.</span><span class="sxs-lookup"><span data-stu-id="93802-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="93802-125">На следующем рисунке `admin@contoso.com` Вход выполнен и в роли администратора:</span><span class="sxs-lookup"><span data-stu-id="93802-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Снимок экрана, показывающий admin@contoso.com Вход](secure-data/_static/admin.png)

<span data-ttu-id="93802-127">Администратор имеет все привилегии.</span><span class="sxs-lookup"><span data-stu-id="93802-127">The administrator has all privileges.</span></span> <span data-ttu-id="93802-128">Она может читать, изменять и удалять контакты, а также изменять состояние контактов.</span><span class="sxs-lookup"><span data-stu-id="93802-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="93802-129">Приложение было создано с помощью [формирования шаблонов](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) следующей `Contact` модели:</span><span class="sxs-lookup"><span data-stu-id="93802-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="93802-130">Этот пример содержит следующие обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="93802-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="93802-131">`ContactIsOwnerAuthorizationHandler`: Гарантирует, что пользователь может изменять только свои данные.</span><span class="sxs-lookup"><span data-stu-id="93802-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="93802-132">`ContactManagerAuthorizationHandler`: Позволяет руководителям утверждать или отклонять контакты.</span><span class="sxs-lookup"><span data-stu-id="93802-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="93802-133">`ContactAdministratorsAuthorizationHandler`: Позволяет администраторам утверждать или отклонять контакты, а также изменять и удалять контакты.</span><span class="sxs-lookup"><span data-stu-id="93802-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="93802-134">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="93802-134">Prerequisites</span></span>

<span data-ttu-id="93802-135">Этот учебник расширен.</span><span class="sxs-lookup"><span data-stu-id="93802-135">This tutorial is advanced.</span></span> <span data-ttu-id="93802-136">Вы должны быть знакомы с:</span><span class="sxs-lookup"><span data-stu-id="93802-136">You should be familiar with:</span></span>

* [<span data-ttu-id="93802-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="93802-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="93802-138">Аутентификация</span><span class="sxs-lookup"><span data-stu-id="93802-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="93802-139">Подтверждение учетной записи и восстановление пароля</span><span class="sxs-lookup"><span data-stu-id="93802-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="93802-140">Авторизация</span><span class="sxs-lookup"><span data-stu-id="93802-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="93802-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="93802-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="93802-142">Начальное и завершенное приложение</span><span class="sxs-lookup"><span data-stu-id="93802-142">The starter and completed app</span></span>

<span data-ttu-id="93802-143">[Скачайте](xref:index#how-to-download-a-sample) [готовое](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) приложение.</span><span class="sxs-lookup"><span data-stu-id="93802-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="93802-144">[Протестируйте](#test-the-completed-app) готовое приложение, чтобы ознакомиться с его функциями безопасности.</span><span class="sxs-lookup"><span data-stu-id="93802-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="93802-145">Начальное приложение</span><span class="sxs-lookup"><span data-stu-id="93802-145">The starter app</span></span>

<span data-ttu-id="93802-146">[Скачайте](xref:index#how-to-download-a-sample) [Начальное](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) приложение.</span><span class="sxs-lookup"><span data-stu-id="93802-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="93802-147">Запустите приложение, коснитесь ссылки **ContactManager** и убедитесь, что вы можете создать, изменить и удалить контакт.</span><span class="sxs-lookup"><span data-stu-id="93802-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="93802-148">Защита пользовательских данных</span><span class="sxs-lookup"><span data-stu-id="93802-148">Secure user data</span></span>

<span data-ttu-id="93802-149">Следующие разделы содержат все основные шаги по созданию безопасного приложения для данных пользователей.</span><span class="sxs-lookup"><span data-stu-id="93802-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="93802-150">Может оказаться полезным ссылаться на завершенный проект.</span><span class="sxs-lookup"><span data-stu-id="93802-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="93802-151">Связать контактные данные с пользователем</span><span class="sxs-lookup"><span data-stu-id="93802-151">Tie the contact data to the user</span></span>

<span data-ttu-id="93802-152">Используйте [Identity](xref:security/authentication/identity) идентификатор пользователя ASP.NET, чтобы убедиться, что пользователи могут изменять данные, но не данные других пользователей.</span><span class="sxs-lookup"><span data-stu-id="93802-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="93802-153">Добавьте `OwnerID` и `ContactStatus` в `Contact` модель:</span><span class="sxs-lookup"><span data-stu-id="93802-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="93802-154">`OwnerID`Идентификатор пользователя из `AspNetUser` таблицы в [Identity](xref:security/authentication/identity) базе данных.</span><span class="sxs-lookup"><span data-stu-id="93802-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="93802-155">`Status`Поле определяет, можно ли просматривать контакт обычными пользователями.</span><span class="sxs-lookup"><span data-stu-id="93802-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="93802-156">Создайте новую миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="93802-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="93802-157">Добавление служб ролей вIdentity</span><span class="sxs-lookup"><span data-stu-id="93802-157">Add Role services to Identity</span></span>

<span data-ttu-id="93802-158">Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:</span><span class="sxs-lookup"><span data-stu-id="93802-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="93802-159">Требовать прошедших проверку пользователей</span><span class="sxs-lookup"><span data-stu-id="93802-159">Require authenticated users</span></span>

<span data-ttu-id="93802-160">Настройте политику резервной проверки подлинности, чтобы требовать проверку подлинности пользователей:</span><span class="sxs-lookup"><span data-stu-id="93802-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="93802-161">Выделенный выше код задает [политику резервной проверки подлинности](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="93802-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="93802-162">Политика резервной проверки подлинности требует, чтобы ***все*** пользователи прошли проверку подлинности, за исключением Razor страниц, контроллеров или методов действий с атрибутом проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="93802-162">The fallback authentication policy requires ***all*** users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="93802-163">Например, Razor страницы, контроллеры или методы действий с `[AllowAnonymous]` или `[Authorize(PolicyName="MyPolicy")]` используют примененный атрибут проверки подлинности, а не политику резервной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="93802-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="93802-164">Резервная политика проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="93802-164">The fallback authentication policy:</span></span>

* <span data-ttu-id="93802-165">Применяется ко всем запросам, которые не указывают политику проверки подлинности явным образом.</span><span class="sxs-lookup"><span data-stu-id="93802-165">Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="93802-166">Для запросов, обслуживаемых маршрутизацией конечных точек, сюда входят все конечные точки, не указывающие атрибут авторизации.</span><span class="sxs-lookup"><span data-stu-id="93802-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="93802-167">Для запросов, обслуживаемых другим по промежуточного слоя, после по промежуточного слоя авторизации, например [статических файлов](xref:fundamentals/static-files), эта политика будет применена ко всем запросам.</span><span class="sxs-lookup"><span data-stu-id="93802-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="93802-168">Настройка политики резервной проверки подлинности на требование проверки подлинности пользователей защищает вновь добавленные Razor страницы и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="93802-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="93802-169">Необходимость проверки подлинности по умолчанию более безопасна, чем использование новых контроллеров и Razor страниц для включения `[Authorize]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="93802-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="93802-170"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Класс также содержит <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="93802-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="93802-171">`DefaultPolicy`— Это политика, используемая с `[Authorize]` атрибутом, если не указана политика.</span><span class="sxs-lookup"><span data-stu-id="93802-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="93802-172">`[Authorize]`не содержит именованную политику, в отличие от `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="93802-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="93802-173">Дополнительные сведения о политиках см. в разделе <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="93802-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="93802-174">Альтернативный способ для контроллеров и Razor страниц MVC, требующих проверки подлинности всех пользователей, — Добавление фильтра авторизации:</span><span class="sxs-lookup"><span data-stu-id="93802-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="93802-175">В приведенном выше коде используется фильтр авторизации, установка политики резервирования использует маршрутизацию конечных точек.</span><span class="sxs-lookup"><span data-stu-id="93802-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="93802-176">Настройка политики резервирования является предпочтительным способом требовать проверку подлинности всех пользователей.</span><span class="sxs-lookup"><span data-stu-id="93802-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="93802-177">Добавьте [allowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) на `Index` страницы и, `Privacy` чтобы анонимные пользователи могли получить сведения о сайте перед регистрацией:</span><span class="sxs-lookup"><span data-stu-id="93802-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="93802-178">Настройка тестовой учетной записи</span><span class="sxs-lookup"><span data-stu-id="93802-178">Configure the test account</span></span>

<span data-ttu-id="93802-179">`SeedData`Класс создает две учетные записи: Administrator и Manager.</span><span class="sxs-lookup"><span data-stu-id="93802-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="93802-180">Используйте [средство диспетчера секретов](xref:security/app-secrets) , чтобы задать пароль для этих учетных записей.</span><span class="sxs-lookup"><span data-stu-id="93802-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="93802-181">Задайте пароль из каталога проекта (каталога, содержащего *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="93802-181">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="93802-182">Если надежный пароль не указан, при вызове вызывается исключение `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="93802-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="93802-183">Обновите `Main` , чтобы использовать тестовый пароль:</span><span class="sxs-lookup"><span data-stu-id="93802-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="93802-184">Создание тестовых учетных записей и обновление контактов</span><span class="sxs-lookup"><span data-stu-id="93802-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="93802-185">Обновите `Initialize` метод в `SeedData` классе, чтобы создать тестовые учетные записи:</span><span class="sxs-lookup"><span data-stu-id="93802-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="93802-186">Добавьте идентификатор пользователя администратора и `ContactStatus` в список контактов.</span><span class="sxs-lookup"><span data-stu-id="93802-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="93802-187">Сделайте одно из контактов "Отправлено" и одно "Отклонено".</span><span class="sxs-lookup"><span data-stu-id="93802-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="93802-188">Добавьте идентификатор пользователя и состояние во все контакты.</span><span class="sxs-lookup"><span data-stu-id="93802-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="93802-189">Отображается только один контакт:</span><span class="sxs-lookup"><span data-stu-id="93802-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="93802-190">Создание обработчиков авторизации владельца, руководителя и администратора</span><span class="sxs-lookup"><span data-stu-id="93802-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="93802-191">Создайте `ContactIsOwnerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="93802-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="93802-192">`ContactIsOwnerAuthorizationHandler`Проверяет, принадлежит ли ресурс пользователю, работающему с ресурсом.</span><span class="sxs-lookup"><span data-stu-id="93802-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="93802-193">`ContactIsOwnerAuthorizationHandler`Контекст вызовов [. Считается успешной](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , если текущий пользователь, прошедший проверку подлинности, является владельцем контакта.</span><span class="sxs-lookup"><span data-stu-id="93802-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="93802-194">Обычно обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="93802-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="93802-195">Возврат `context.Succeed` при соблюдении требований.</span><span class="sxs-lookup"><span data-stu-id="93802-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="93802-196">Возврат `Task.CompletedTask` , если требования не выполнены.</span><span class="sxs-lookup"><span data-stu-id="93802-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="93802-197">`Task.CompletedTask`не является успешным или неудачным, &mdash; что позволяет запускать другие обработчики авторизации.</span><span class="sxs-lookup"><span data-stu-id="93802-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="93802-198">Если необходимо явное завершение, возвращаемый [контекст. Не пройдено](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="93802-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="93802-199">Приложение позволяет владельцам контактов изменять, удалять и создавать собственные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="93802-200">`ContactIsOwnerAuthorizationHandler`не требуется проверять операцию, переданную в параметре требования.</span><span class="sxs-lookup"><span data-stu-id="93802-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="93802-201">Создание обработчика авторизации диспетчера</span><span class="sxs-lookup"><span data-stu-id="93802-201">Create a manager authorization handler</span></span>

<span data-ttu-id="93802-202">Создайте `ContactManagerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="93802-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="93802-203">`ContactManagerAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, диспетчером.</span><span class="sxs-lookup"><span data-stu-id="93802-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="93802-204">Только руководители могут утверждать или отклонять изменения содержимого (новые или измененные).</span><span class="sxs-lookup"><span data-stu-id="93802-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="93802-205">Создание обработчика авторизации администратора</span><span class="sxs-lookup"><span data-stu-id="93802-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="93802-206">Создайте `ContactAdministratorsAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="93802-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="93802-207">`ContactAdministratorsAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, администратором.</span><span class="sxs-lookup"><span data-stu-id="93802-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="93802-208">Администратор может выполнять все операции.</span><span class="sxs-lookup"><span data-stu-id="93802-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="93802-209">Регистрация обработчиков авторизации</span><span class="sxs-lookup"><span data-stu-id="93802-209">Register the authorization handlers</span></span>

<span data-ttu-id="93802-210">Службы, использующие Entity Framework Core, должны быть зарегистрированы для [внедрения зависимостей](xref:fundamentals/dependency-injection) с помощью [аддскопед](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="93802-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="93802-211">`ContactIsOwnerAuthorizationHandler`Использует ASP.NET Core [Identity](xref:security/authentication/identity) , который построен на Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="93802-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="93802-212">Зарегистрируйте обработчики в коллекции служб, чтобы они были доступны `ContactsController` через [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="93802-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="93802-213">Добавьте следующий код в конец `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="93802-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="93802-214">`ContactAdministratorsAuthorizationHandler`и `ContactManagerAuthorizationHandler` добавляются в виде Singleton.</span><span class="sxs-lookup"><span data-stu-id="93802-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="93802-215">Они являются Singleton-классом, поскольку не используют EF, а вся необходимая информация находится в `Context` параметре `HandleRequirementAsync` метода.</span><span class="sxs-lookup"><span data-stu-id="93802-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="93802-216">Поддержка авторизации</span><span class="sxs-lookup"><span data-stu-id="93802-216">Support authorization</span></span>

<span data-ttu-id="93802-217">В этом разделе вы обновите Razor страницы и добавите класс требований к операциям.</span><span class="sxs-lookup"><span data-stu-id="93802-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="93802-218">Ознакомьтесь с классом требований к операциям</span><span class="sxs-lookup"><span data-stu-id="93802-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="93802-219">Проверьте `ContactOperations` класс.</span><span class="sxs-lookup"><span data-stu-id="93802-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="93802-220">Этот класс содержит требования, которые поддерживает приложение:</span><span class="sxs-lookup"><span data-stu-id="93802-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="93802-221">Создание базового класса для Razor страниц контактов</span><span class="sxs-lookup"><span data-stu-id="93802-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="93802-222">Создайте базовый класс, содержащий службы, используемые на Razor страницах контактов.</span><span class="sxs-lookup"><span data-stu-id="93802-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="93802-223">Базовый класс помещает код инициализации в одно расположение:</span><span class="sxs-lookup"><span data-stu-id="93802-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="93802-224">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="93802-224">The preceding code:</span></span>

* <span data-ttu-id="93802-225">Добавляет `IAuthorizationService` службу для доступа к обработчикам авторизации.</span><span class="sxs-lookup"><span data-stu-id="93802-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="93802-226">Добавляет Identity `UserManager` службу.</span><span class="sxs-lookup"><span data-stu-id="93802-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="93802-227">Добавьте `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="93802-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="93802-228">Обновление Креатемодел</span><span class="sxs-lookup"><span data-stu-id="93802-228">Update the CreateModel</span></span>

<span data-ttu-id="93802-229">Обновите конструктор модели страницы Create, чтобы использовать `DI_BasePageModel` базовый класс:</span><span class="sxs-lookup"><span data-stu-id="93802-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="93802-230">Обновите `CreateModel.OnPostAsync` метод, чтобы:</span><span class="sxs-lookup"><span data-stu-id="93802-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="93802-231">Добавьте в модель идентификатор пользователя `Contact` .</span><span class="sxs-lookup"><span data-stu-id="93802-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="93802-232">Вызовите обработчик авторизации, чтобы убедиться, что пользователь имеет разрешение на создание контактов.</span><span class="sxs-lookup"><span data-stu-id="93802-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="93802-233">Обновление Индексмодел</span><span class="sxs-lookup"><span data-stu-id="93802-233">Update the IndexModel</span></span>

<span data-ttu-id="93802-234">Обновите `OnGetAsync` метод, чтобы только утвержденные контакты отображались для обычных пользователей:</span><span class="sxs-lookup"><span data-stu-id="93802-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="93802-235">Обновление Едитмодел</span><span class="sxs-lookup"><span data-stu-id="93802-235">Update the EditModel</span></span>

<span data-ttu-id="93802-236">Добавьте обработчик авторизации, чтобы убедиться, что пользователь владеет контактом.</span><span class="sxs-lookup"><span data-stu-id="93802-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="93802-237">Так как авторизация ресурса проверяется, `[Authorize]` атрибут недостаточно.</span><span class="sxs-lookup"><span data-stu-id="93802-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="93802-238">Приложение не имеет доступа к ресурсу при оценке атрибутов.</span><span class="sxs-lookup"><span data-stu-id="93802-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="93802-239">Авторизация на основе ресурсов должна быть принудительной.</span><span class="sxs-lookup"><span data-stu-id="93802-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="93802-240">Проверки должны выполняться после того, как приложение будет иметь доступ к ресурсу, загрузив его в модель страницы или загрузив в сам обработчик.</span><span class="sxs-lookup"><span data-stu-id="93802-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="93802-241">Вы часто обращаетесь к ресурсу, передавая ключ ресурса.</span><span class="sxs-lookup"><span data-stu-id="93802-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="93802-242">Обновление Делетемодел</span><span class="sxs-lookup"><span data-stu-id="93802-242">Update the DeleteModel</span></span>

<span data-ttu-id="93802-243">Обновите модель страницы удаления, чтобы использовать обработчик авторизации для проверки наличия у пользователя разрешения на удаление контакта.</span><span class="sxs-lookup"><span data-stu-id="93802-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="93802-244">Внедрение службы авторизации в представления</span><span class="sxs-lookup"><span data-stu-id="93802-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="93802-245">В настоящее время в пользовательском интерфейсе отображаются ссылки Edit и DELETE для контактов, которые пользователь не может изменить.</span><span class="sxs-lookup"><span data-stu-id="93802-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="93802-246">Вставьте службу авторизации в файл *pages/_ViewImports. cshtml* , чтобы он был доступен для всех представлений:</span><span class="sxs-lookup"><span data-stu-id="93802-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="93802-247">Предыдущая разметка добавляет несколько `using` инструкций.</span><span class="sxs-lookup"><span data-stu-id="93802-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="93802-248">Обновите ссылки **Edit** и **Delete** в *pages/contacts/index. cshtml* , чтобы они отображались только для пользователей с соответствующими разрешениями:</span><span class="sxs-lookup"><span data-stu-id="93802-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="93802-249">Скрытие ссылок от пользователей, не имеющих разрешений на изменение данных, не защищает приложение.</span><span class="sxs-lookup"><span data-stu-id="93802-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="93802-250">Скрытие ссылок делает приложение более удобным для пользователей, отображая только допустимые ссылки.</span><span class="sxs-lookup"><span data-stu-id="93802-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="93802-251">Пользователи могут обращаться к созданным URL-адресам для вызова операций правки и удаления данных, которыми они не владеют.</span><span class="sxs-lookup"><span data-stu-id="93802-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="93802-252">RazorСтраница или контроллер должны применять проверки доступа для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="93802-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="93802-253">Сведения об обновлении</span><span class="sxs-lookup"><span data-stu-id="93802-253">Update Details</span></span>

<span data-ttu-id="93802-254">Обновите представление сведений, чтобы руководители могли утверждать или отклонять контакты:</span><span class="sxs-lookup"><span data-stu-id="93802-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="93802-255">Обновите модель страницы сведений:</span><span class="sxs-lookup"><span data-stu-id="93802-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="93802-256">Добавление или удаление пользователя для роли</span><span class="sxs-lookup"><span data-stu-id="93802-256">Add or remove a user to a role</span></span>

<span data-ttu-id="93802-257">Сведения об [этой ошибке](https://github.com/dotnet/AspNetCore.Docs/issues/8502) см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="93802-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="93802-258">Удаление привилегий у пользователя.</span><span class="sxs-lookup"><span data-stu-id="93802-258">Removing privileges from a user.</span></span> <span data-ttu-id="93802-259">Например, отзвука пользователя в приложении разговора.</span><span class="sxs-lookup"><span data-stu-id="93802-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="93802-260">Добавление привилегий для пользователя.</span><span class="sxs-lookup"><span data-stu-id="93802-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="93802-261">Различия между запросом и запретом</span><span class="sxs-lookup"><span data-stu-id="93802-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="93802-262">Это приложение задает политику по умолчанию, [требующую проверки подлинности пользователей](#rau).</span><span class="sxs-lookup"><span data-stu-id="93802-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="93802-263">Следующий код позволяет анонимным пользователям.</span><span class="sxs-lookup"><span data-stu-id="93802-263">The following code allows anonymous users.</span></span> <span data-ttu-id="93802-264">Анонимным пользователям разрешено показывать различия между запросом и запретом.</span><span class="sxs-lookup"><span data-stu-id="93802-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="93802-265">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="93802-265">In the preceding code:</span></span>

* <span data-ttu-id="93802-266">Если пользователь **не** прошел проверку подлинности, `ChallengeResult` возвращается значение.</span><span class="sxs-lookup"><span data-stu-id="93802-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="93802-267">Когда `ChallengeResult` возвращается, пользователь перенаправляется на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="93802-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="93802-268">Если пользователь прошел проверку подлинности, но не авторизован, `ForbidResult` возвращается значение.</span><span class="sxs-lookup"><span data-stu-id="93802-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="93802-269">Когда `ForbidResult` возвращается значение, пользователь перенаправляется на страницу отказ в доступе.</span><span class="sxs-lookup"><span data-stu-id="93802-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="93802-270">Тестирование завершенного приложения</span><span class="sxs-lookup"><span data-stu-id="93802-270">Test the completed app</span></span>

<span data-ttu-id="93802-271">Если вы еще не установили пароль для заполненных учетных записей пользователей, используйте [средство диспетчера секретов](xref:security/app-secrets#secret-manager) , чтобы задать пароль:</span><span class="sxs-lookup"><span data-stu-id="93802-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="93802-272">Выберите надежный пароль: используйте восемь или более символов и по крайней мере одну прописную букву, цифру и символ.</span><span class="sxs-lookup"><span data-stu-id="93802-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="93802-273">Например, `Passw0rd!` соответствует требованиям к надежному паролю.</span><span class="sxs-lookup"><span data-stu-id="93802-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="93802-274">Выполните следующую команду из папки проекта, где `<PW>` — пароль:</span><span class="sxs-lookup"><span data-stu-id="93802-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="93802-275">Если у приложения есть контакты:</span><span class="sxs-lookup"><span data-stu-id="93802-275">If the app has contacts:</span></span>

* <span data-ttu-id="93802-276">Удалите все записи в `Contact` таблице.</span><span class="sxs-lookup"><span data-stu-id="93802-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="93802-277">Перезапустите приложение, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="93802-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="93802-278">Простой способ тестирования завершенного приложения — запуск трех различных браузеров (или режиме инкогнито и нечастных сеансов).</span><span class="sxs-lookup"><span data-stu-id="93802-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="93802-279">В одном браузере Зарегистрируйте нового пользователя (например, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="93802-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="93802-280">Войдите в каждый браузер с другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="93802-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="93802-281">Проверьте следующие операции:</span><span class="sxs-lookup"><span data-stu-id="93802-281">Verify the following operations:</span></span>

* <span data-ttu-id="93802-282">Зарегистрированные пользователи могут просматривать все утвержденные контактные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="93802-283">Зарегистрированные пользователи могут изменять и удалять собственные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="93802-284">Руководители могут утверждать и отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="93802-285">В `Details` представлении отображаются кнопки **утвердить** и **отклонить** .</span><span class="sxs-lookup"><span data-stu-id="93802-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="93802-286">Администраторы могут утверждать, отклонять и изменять и удалять все данные.</span><span class="sxs-lookup"><span data-stu-id="93802-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="93802-287">Пользователь</span><span class="sxs-lookup"><span data-stu-id="93802-287">User</span></span>                | <span data-ttu-id="93802-288">Заполнено приложением</span><span class="sxs-lookup"><span data-stu-id="93802-288">Seeded by the app</span></span> | <span data-ttu-id="93802-289">Параметры</span><span class="sxs-lookup"><span data-stu-id="93802-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="93802-290">Нет</span><span class="sxs-lookup"><span data-stu-id="93802-290">No</span></span>                | <span data-ttu-id="93802-291">Изменение или удаление собственных данных.</span><span class="sxs-lookup"><span data-stu-id="93802-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="93802-292">да</span><span class="sxs-lookup"><span data-stu-id="93802-292">Yes</span></span>               | <span data-ttu-id="93802-293">Утвердите, отклоните и измените или удалите собственные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="93802-294">да</span><span class="sxs-lookup"><span data-stu-id="93802-294">Yes</span></span>               | <span data-ttu-id="93802-295">Утвердите или отклоните и измените или удалите все данные.</span><span class="sxs-lookup"><span data-stu-id="93802-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="93802-296">Создайте контакт в браузере администратора.</span><span class="sxs-lookup"><span data-stu-id="93802-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="93802-297">Скопируйте URL-адрес для DELETE и Edit из контакта администратора.</span><span class="sxs-lookup"><span data-stu-id="93802-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="93802-298">Вставьте эти ссылки в браузер тестового пользователя, чтобы убедиться, что тестовая пользователь не может выполнить эти операции.</span><span class="sxs-lookup"><span data-stu-id="93802-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="93802-299">Создание начального приложения</span><span class="sxs-lookup"><span data-stu-id="93802-299">Create the starter app</span></span>

* <span data-ttu-id="93802-300">Создание Razor страницы приложения с именем "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="93802-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="93802-301">Создайте приложение с **учетными записями отдельных пользователей**.</span><span class="sxs-lookup"><span data-stu-id="93802-301">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="93802-302">Назовите его "ContactManager", чтобы пространство имен совпадало с пространством имен, используемым в примере.</span><span class="sxs-lookup"><span data-stu-id="93802-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="93802-303">`-uld`Указывает LocalDB вместо SQLite</span><span class="sxs-lookup"><span data-stu-id="93802-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="93802-304">Добавление *моделей/Contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="93802-304">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="93802-305">Формирование шаблонов `Contact` модели.</span><span class="sxs-lookup"><span data-stu-id="93802-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="93802-306">Создайте начальную миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="93802-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="93802-307">Если при выполнении команды возникла ошибка `dotnet aspnet-codegenerator razorpage` , см. [эту ошибку в GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="93802-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="93802-308">Обновите привязку **ContactManager** в файле *pages/shared/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="93802-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="93802-309">Тестирование приложения путем создания, изменения и удаления контакта</span><span class="sxs-lookup"><span data-stu-id="93802-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="93802-310">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="93802-310">Seed the database</span></span>

<span data-ttu-id="93802-311">Добавьте класс [сиддата](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) в папку *Data* :</span><span class="sxs-lookup"><span data-stu-id="93802-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="93802-312">Вызов `SeedData.Initialize` из `Main` :</span><span class="sxs-lookup"><span data-stu-id="93802-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="93802-313">Проверьте, что приложение заполнено базой данных.</span><span class="sxs-lookup"><span data-stu-id="93802-313">Test that the app seeded the database.</span></span> <span data-ttu-id="93802-314">Если в базе данных Contact есть какие бы то ни было строки, метод SEED не выполняется.</span><span class="sxs-lookup"><span data-stu-id="93802-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="93802-315">В этом руководстве показано, как создать веб-приложение ASP.NET Core с данными пользователя, защищенными с помощью авторизации.</span><span class="sxs-lookup"><span data-stu-id="93802-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="93802-316">Отображается список контактов, которые были созданы пользователями, прошедшими проверку подлинности (зарегистрированные).</span><span class="sxs-lookup"><span data-stu-id="93802-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="93802-317">Существует три группы безопасности:</span><span class="sxs-lookup"><span data-stu-id="93802-317">There are three security groups:</span></span>

* <span data-ttu-id="93802-318">**Зарегистрированные пользователи** могут просматривать все утвержденные данные, а также изменять и удалять свои данные.</span><span class="sxs-lookup"><span data-stu-id="93802-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="93802-319">**Руководители** могут утверждать или отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="93802-320">Пользователям видны только утвержденные контакты.</span><span class="sxs-lookup"><span data-stu-id="93802-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="93802-321">**Администраторы** могут утверждать, отклонять и изменять и удалять любые данные.</span><span class="sxs-lookup"><span data-stu-id="93802-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="93802-322">На следующем рисунке пользователь Рик ( `rick@example.com` ) вошел в.</span><span class="sxs-lookup"><span data-stu-id="93802-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="93802-323">Рик может просматривать только утвержденные контакты и **редактировать** / **Удалить** / **создать новые** ссылки для своих контактов.</span><span class="sxs-lookup"><span data-stu-id="93802-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="93802-324">Ссылки **Edit** и **Delete** отображаются только в последней записи, созданной Рик.</span><span class="sxs-lookup"><span data-stu-id="93802-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="93802-325">Другие пользователи не увидят последнюю запись, пока руководитель или администратор не изменит состояние на "утверждено".</span><span class="sxs-lookup"><span data-stu-id="93802-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Снимок экрана, показывающий Рик, выполнивший вход](secure-data/_static/rick.png)

<span data-ttu-id="93802-327">На следующем рисунке `manager@contoso.com` Вход выполнен и в роли руководителя:</span><span class="sxs-lookup"><span data-stu-id="93802-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Снимок экрана, показывающий manager@contoso.com Вход](secure-data/_static/manager1.png)

<span data-ttu-id="93802-329">На следующем рисунке показано представление сведений об менеджерах для контакта:</span><span class="sxs-lookup"><span data-stu-id="93802-329">The following image shows the managers details view of a contact:</span></span>

![Представление контакта руководителя](secure-data/_static/manager.png)

<span data-ttu-id="93802-331">Кнопки **утвердить** и **отклонить** отображаются только для руководителей и администраторов.</span><span class="sxs-lookup"><span data-stu-id="93802-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="93802-332">На следующем рисунке `admin@contoso.com` Вход выполнен и в роли администратора:</span><span class="sxs-lookup"><span data-stu-id="93802-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Снимок экрана, показывающий admin@contoso.com Вход](secure-data/_static/admin.png)

<span data-ttu-id="93802-334">Администратор имеет все привилегии.</span><span class="sxs-lookup"><span data-stu-id="93802-334">The administrator has all privileges.</span></span> <span data-ttu-id="93802-335">Она может читать, изменять и удалять контакты, а также изменять состояние контактов.</span><span class="sxs-lookup"><span data-stu-id="93802-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="93802-336">Приложение было создано с помощью [формирования шаблонов](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) следующей `Contact` модели:</span><span class="sxs-lookup"><span data-stu-id="93802-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="93802-337">Этот пример содержит следующие обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="93802-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="93802-338">`ContactIsOwnerAuthorizationHandler`: Гарантирует, что пользователь может изменять только свои данные.</span><span class="sxs-lookup"><span data-stu-id="93802-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="93802-339">`ContactManagerAuthorizationHandler`: Позволяет руководителям утверждать или отклонять контакты.</span><span class="sxs-lookup"><span data-stu-id="93802-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="93802-340">`ContactAdministratorsAuthorizationHandler`: Позволяет администраторам утверждать или отклонять контакты, а также изменять и удалять контакты.</span><span class="sxs-lookup"><span data-stu-id="93802-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="93802-341">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="93802-341">Prerequisites</span></span>

<span data-ttu-id="93802-342">Этот учебник расширен.</span><span class="sxs-lookup"><span data-stu-id="93802-342">This tutorial is advanced.</span></span> <span data-ttu-id="93802-343">Вы должны быть знакомы с:</span><span class="sxs-lookup"><span data-stu-id="93802-343">You should be familiar with:</span></span>

* [<span data-ttu-id="93802-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="93802-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="93802-345">Аутентификация</span><span class="sxs-lookup"><span data-stu-id="93802-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="93802-346">Подтверждение учетной записи и восстановление пароля</span><span class="sxs-lookup"><span data-stu-id="93802-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="93802-347">Авторизация</span><span class="sxs-lookup"><span data-stu-id="93802-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="93802-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="93802-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="93802-349">Начальное и завершенное приложение</span><span class="sxs-lookup"><span data-stu-id="93802-349">The starter and completed app</span></span>

<span data-ttu-id="93802-350">[Скачайте](xref:index#how-to-download-a-sample) [готовое](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) приложение.</span><span class="sxs-lookup"><span data-stu-id="93802-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="93802-351">[Протестируйте](#test-the-completed-app) готовое приложение, чтобы ознакомиться с его функциями безопасности.</span><span class="sxs-lookup"><span data-stu-id="93802-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="93802-352">Начальное приложение</span><span class="sxs-lookup"><span data-stu-id="93802-352">The starter app</span></span>

<span data-ttu-id="93802-353">[Скачайте](xref:index#how-to-download-a-sample) [Начальное](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) приложение.</span><span class="sxs-lookup"><span data-stu-id="93802-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="93802-354">Запустите приложение, коснитесь ссылки **ContactManager** и убедитесь, что вы можете создать, изменить и удалить контакт.</span><span class="sxs-lookup"><span data-stu-id="93802-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="93802-355">Защита пользовательских данных</span><span class="sxs-lookup"><span data-stu-id="93802-355">Secure user data</span></span>

<span data-ttu-id="93802-356">Следующие разделы содержат все основные шаги по созданию безопасного приложения для данных пользователей.</span><span class="sxs-lookup"><span data-stu-id="93802-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="93802-357">Может оказаться полезным ссылаться на завершенный проект.</span><span class="sxs-lookup"><span data-stu-id="93802-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="93802-358">Связать контактные данные с пользователем</span><span class="sxs-lookup"><span data-stu-id="93802-358">Tie the contact data to the user</span></span>

<span data-ttu-id="93802-359">Используйте [Identity](xref:security/authentication/identity) идентификатор пользователя ASP.NET, чтобы убедиться, что пользователи могут изменять данные, но не данные других пользователей.</span><span class="sxs-lookup"><span data-stu-id="93802-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="93802-360">Добавьте `OwnerID` и `ContactStatus` в `Contact` модель:</span><span class="sxs-lookup"><span data-stu-id="93802-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="93802-361">`OwnerID`Идентификатор пользователя из `AspNetUser` таблицы в [Identity](xref:security/authentication/identity) базе данных.</span><span class="sxs-lookup"><span data-stu-id="93802-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="93802-362">`Status`Поле определяет, можно ли просматривать контакт обычными пользователями.</span><span class="sxs-lookup"><span data-stu-id="93802-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="93802-363">Создайте новую миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="93802-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="93802-364">Добавление служб ролей вIdentity</span><span class="sxs-lookup"><span data-stu-id="93802-364">Add Role services to Identity</span></span>

<span data-ttu-id="93802-365">Добавление [аддролес](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:</span><span class="sxs-lookup"><span data-stu-id="93802-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="93802-366">Требовать прошедших проверку пользователей</span><span class="sxs-lookup"><span data-stu-id="93802-366">Require authenticated users</span></span>

<span data-ttu-id="93802-367">Задайте политику проверки подлинности по умолчанию, чтобы требовать проверку подлинности пользователей:</span><span class="sxs-lookup"><span data-stu-id="93802-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="93802-368">Можно отказаться от проверки подлинности на Razor уровне страницы, контроллера или метода действия с помощью `[AllowAnonymous]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="93802-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="93802-369">Настройка политики проверки подлинности по умолчанию для обязательной проверки подлинности пользователей защищает вновь добавленные Razor страницы и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="93802-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="93802-370">Необходимость проверки подлинности по умолчанию более безопасна, чем использование новых контроллеров и Razor страниц для включения `[Authorize]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="93802-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="93802-371">Добавьте [allowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) в индекс, About и Contact Pages, чтобы анонимные пользователи могли получить сведения о сайте перед регистрацией.</span><span class="sxs-lookup"><span data-stu-id="93802-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="93802-372">Настройка тестовой учетной записи</span><span class="sxs-lookup"><span data-stu-id="93802-372">Configure the test account</span></span>

<span data-ttu-id="93802-373">`SeedData`Класс создает две учетные записи: Administrator и Manager.</span><span class="sxs-lookup"><span data-stu-id="93802-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="93802-374">Используйте [средство диспетчера секретов](xref:security/app-secrets) , чтобы задать пароль для этих учетных записей.</span><span class="sxs-lookup"><span data-stu-id="93802-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="93802-375">Задайте пароль из каталога проекта (каталога, содержащего *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="93802-375">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="93802-376">Если надежный пароль не указан, при вызове вызывается исключение `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="93802-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="93802-377">Обновите `Main` , чтобы использовать тестовый пароль:</span><span class="sxs-lookup"><span data-stu-id="93802-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="93802-378">Создание тестовых учетных записей и обновление контактов</span><span class="sxs-lookup"><span data-stu-id="93802-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="93802-379">Обновите `Initialize` метод в `SeedData` классе, чтобы создать тестовые учетные записи:</span><span class="sxs-lookup"><span data-stu-id="93802-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="93802-380">Добавьте идентификатор пользователя администратора и `ContactStatus` в список контактов.</span><span class="sxs-lookup"><span data-stu-id="93802-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="93802-381">Сделайте одно из контактов "Отправлено" и одно "Отклонено".</span><span class="sxs-lookup"><span data-stu-id="93802-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="93802-382">Добавьте идентификатор пользователя и состояние во все контакты.</span><span class="sxs-lookup"><span data-stu-id="93802-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="93802-383">Отображается только один контакт:</span><span class="sxs-lookup"><span data-stu-id="93802-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="93802-384">Создание обработчиков авторизации владельца, руководителя и администратора</span><span class="sxs-lookup"><span data-stu-id="93802-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="93802-385">Создайте папку *авторизации* и создайте `ContactIsOwnerAuthorizationHandler` в ней класс.</span><span class="sxs-lookup"><span data-stu-id="93802-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="93802-386">`ContactIsOwnerAuthorizationHandler`Проверяет, принадлежит ли ресурс пользователю, работающему с ресурсом.</span><span class="sxs-lookup"><span data-stu-id="93802-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="93802-387">`ContactIsOwnerAuthorizationHandler`Контекст вызовов [. Считается успешной](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , если текущий пользователь, прошедший проверку подлинности, является владельцем контакта.</span><span class="sxs-lookup"><span data-stu-id="93802-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="93802-388">Обычно обработчики авторизации:</span><span class="sxs-lookup"><span data-stu-id="93802-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="93802-389">Возврат `context.Succeed` при соблюдении требований.</span><span class="sxs-lookup"><span data-stu-id="93802-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="93802-390">Возврат `Task.CompletedTask` , если требования не выполнены.</span><span class="sxs-lookup"><span data-stu-id="93802-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="93802-391">`Task.CompletedTask`не является успешным или неудачным, &mdash; что позволяет запускать другие обработчики авторизации.</span><span class="sxs-lookup"><span data-stu-id="93802-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="93802-392">Если необходимо явное завершение, возвращаемый [контекст. Не пройдено](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="93802-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="93802-393">Приложение позволяет владельцам контактов изменять, удалять и создавать собственные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="93802-394">`ContactIsOwnerAuthorizationHandler`не требуется проверять операцию, переданную в параметре требования.</span><span class="sxs-lookup"><span data-stu-id="93802-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="93802-395">Создание обработчика авторизации диспетчера</span><span class="sxs-lookup"><span data-stu-id="93802-395">Create a manager authorization handler</span></span>

<span data-ttu-id="93802-396">Создайте `ContactManagerAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="93802-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="93802-397">`ContactManagerAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, диспетчером.</span><span class="sxs-lookup"><span data-stu-id="93802-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="93802-398">Только руководители могут утверждать или отклонять изменения содержимого (новые или измененные).</span><span class="sxs-lookup"><span data-stu-id="93802-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="93802-399">Создание обработчика авторизации администратора</span><span class="sxs-lookup"><span data-stu-id="93802-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="93802-400">Создайте `ContactAdministratorsAuthorizationHandler` класс в папке *authorization* .</span><span class="sxs-lookup"><span data-stu-id="93802-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="93802-401">`ContactAdministratorsAuthorizationHandler`Проверяет, является ли пользователь, действующий для ресурса, администратором.</span><span class="sxs-lookup"><span data-stu-id="93802-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="93802-402">Администратор может выполнять все операции.</span><span class="sxs-lookup"><span data-stu-id="93802-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="93802-403">Регистрация обработчиков авторизации</span><span class="sxs-lookup"><span data-stu-id="93802-403">Register the authorization handlers</span></span>

<span data-ttu-id="93802-404">Службы, использующие Entity Framework Core, должны быть зарегистрированы для [внедрения зависимостей](xref:fundamentals/dependency-injection) с помощью [аддскопед](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="93802-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="93802-405">`ContactIsOwnerAuthorizationHandler`Использует ASP.NET Core [Identity](xref:security/authentication/identity) , который построен на Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="93802-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="93802-406">Зарегистрируйте обработчики в коллекции служб, чтобы они были доступны `ContactsController` через [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="93802-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="93802-407">Добавьте следующий код в конец `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="93802-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="93802-408">`ContactAdministratorsAuthorizationHandler`и `ContactManagerAuthorizationHandler` добавляются в виде Singleton.</span><span class="sxs-lookup"><span data-stu-id="93802-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="93802-409">Они являются Singleton-классом, поскольку не используют EF, а вся необходимая информация находится в `Context` параметре `HandleRequirementAsync` метода.</span><span class="sxs-lookup"><span data-stu-id="93802-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="93802-410">Поддержка авторизации</span><span class="sxs-lookup"><span data-stu-id="93802-410">Support authorization</span></span>

<span data-ttu-id="93802-411">В этом разделе вы обновите Razor страницы и добавите класс требований к операциям.</span><span class="sxs-lookup"><span data-stu-id="93802-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="93802-412">Ознакомьтесь с классом требований к операциям</span><span class="sxs-lookup"><span data-stu-id="93802-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="93802-413">Проверьте `ContactOperations` класс.</span><span class="sxs-lookup"><span data-stu-id="93802-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="93802-414">Этот класс содержит требования, которые поддерживает приложение:</span><span class="sxs-lookup"><span data-stu-id="93802-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="93802-415">Создание базового класса для Razor страниц контактов</span><span class="sxs-lookup"><span data-stu-id="93802-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="93802-416">Создайте базовый класс, содержащий службы, используемые на Razor страницах контактов.</span><span class="sxs-lookup"><span data-stu-id="93802-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="93802-417">Базовый класс помещает код инициализации в одно расположение:</span><span class="sxs-lookup"><span data-stu-id="93802-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="93802-418">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="93802-418">The preceding code:</span></span>

* <span data-ttu-id="93802-419">Добавляет `IAuthorizationService` службу для доступа к обработчикам авторизации.</span><span class="sxs-lookup"><span data-stu-id="93802-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="93802-420">Добавляет Identity `UserManager` службу.</span><span class="sxs-lookup"><span data-stu-id="93802-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="93802-421">Добавьте `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="93802-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="93802-422">Обновление Креатемодел</span><span class="sxs-lookup"><span data-stu-id="93802-422">Update the CreateModel</span></span>

<span data-ttu-id="93802-423">Обновите конструктор модели страницы Create, чтобы использовать `DI_BasePageModel` базовый класс:</span><span class="sxs-lookup"><span data-stu-id="93802-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="93802-424">Обновите `CreateModel.OnPostAsync` метод, чтобы:</span><span class="sxs-lookup"><span data-stu-id="93802-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="93802-425">Добавьте в модель идентификатор пользователя `Contact` .</span><span class="sxs-lookup"><span data-stu-id="93802-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="93802-426">Вызовите обработчик авторизации, чтобы убедиться, что пользователь имеет разрешение на создание контактов.</span><span class="sxs-lookup"><span data-stu-id="93802-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="93802-427">Обновление Индексмодел</span><span class="sxs-lookup"><span data-stu-id="93802-427">Update the IndexModel</span></span>

<span data-ttu-id="93802-428">Обновите `OnGetAsync` метод, чтобы только утвержденные контакты отображались для обычных пользователей:</span><span class="sxs-lookup"><span data-stu-id="93802-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="93802-429">Обновление Едитмодел</span><span class="sxs-lookup"><span data-stu-id="93802-429">Update the EditModel</span></span>

<span data-ttu-id="93802-430">Добавьте обработчик авторизации, чтобы убедиться, что пользователь владеет контактом.</span><span class="sxs-lookup"><span data-stu-id="93802-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="93802-431">Так как авторизация ресурса проверяется, `[Authorize]` атрибут недостаточно.</span><span class="sxs-lookup"><span data-stu-id="93802-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="93802-432">Приложение не имеет доступа к ресурсу при оценке атрибутов.</span><span class="sxs-lookup"><span data-stu-id="93802-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="93802-433">Авторизация на основе ресурсов должна быть принудительной.</span><span class="sxs-lookup"><span data-stu-id="93802-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="93802-434">Проверки должны выполняться после того, как приложение будет иметь доступ к ресурсу, загрузив его в модель страницы или загрузив в сам обработчик.</span><span class="sxs-lookup"><span data-stu-id="93802-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="93802-435">Вы часто обращаетесь к ресурсу, передавая ключ ресурса.</span><span class="sxs-lookup"><span data-stu-id="93802-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="93802-436">Обновление Делетемодел</span><span class="sxs-lookup"><span data-stu-id="93802-436">Update the DeleteModel</span></span>

<span data-ttu-id="93802-437">Обновите модель страницы удаления, чтобы использовать обработчик авторизации для проверки наличия у пользователя разрешения на удаление контакта.</span><span class="sxs-lookup"><span data-stu-id="93802-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="93802-438">Внедрение службы авторизации в представления</span><span class="sxs-lookup"><span data-stu-id="93802-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="93802-439">В настоящее время в пользовательском интерфейсе отображаются ссылки Edit и DELETE для контактов, которые пользователь не может изменить.</span><span class="sxs-lookup"><span data-stu-id="93802-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="93802-440">Вставьте службу авторизации в файл *views/_ViewImports. cshtml* , чтобы она была доступна для всех представлений:</span><span class="sxs-lookup"><span data-stu-id="93802-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="93802-441">Предыдущая разметка добавляет несколько `using` инструкций.</span><span class="sxs-lookup"><span data-stu-id="93802-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="93802-442">Обновите ссылки **Edit** и **Delete** в *pages/contacts/index. cshtml* , чтобы они отображались только для пользователей с соответствующими разрешениями:</span><span class="sxs-lookup"><span data-stu-id="93802-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="93802-443">Скрытие ссылок от пользователей, не имеющих разрешений на изменение данных, не защищает приложение.</span><span class="sxs-lookup"><span data-stu-id="93802-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="93802-444">Скрытие ссылок делает приложение более удобным для пользователей, отображая только допустимые ссылки.</span><span class="sxs-lookup"><span data-stu-id="93802-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="93802-445">Пользователи могут обращаться к созданным URL-адресам для вызова операций правки и удаления данных, которыми они не владеют.</span><span class="sxs-lookup"><span data-stu-id="93802-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="93802-446">RazorСтраница или контроллер должны применять проверки доступа для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="93802-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="93802-447">Сведения об обновлении</span><span class="sxs-lookup"><span data-stu-id="93802-447">Update Details</span></span>

<span data-ttu-id="93802-448">Обновите представление сведений, чтобы руководители могли утверждать или отклонять контакты:</span><span class="sxs-lookup"><span data-stu-id="93802-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="93802-449">Обновите модель страницы сведений:</span><span class="sxs-lookup"><span data-stu-id="93802-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="93802-450">Добавление или удаление пользователя для роли</span><span class="sxs-lookup"><span data-stu-id="93802-450">Add or remove a user to a role</span></span>

<span data-ttu-id="93802-451">Сведения об [этой ошибке](https://github.com/dotnet/AspNetCore.Docs/issues/8502) см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="93802-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="93802-452">Удаление привилегий у пользователя.</span><span class="sxs-lookup"><span data-stu-id="93802-452">Removing privileges from a user.</span></span> <span data-ttu-id="93802-453">Например, отзвука пользователя в приложении разговора.</span><span class="sxs-lookup"><span data-stu-id="93802-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="93802-454">Добавление привилегий для пользователя.</span><span class="sxs-lookup"><span data-stu-id="93802-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="93802-455">Тестирование завершенного приложения</span><span class="sxs-lookup"><span data-stu-id="93802-455">Test the completed app</span></span>

<span data-ttu-id="93802-456">Если вы еще не установили пароль для заполненных учетных записей пользователей, используйте [средство диспетчера секретов](xref:security/app-secrets#secret-manager) , чтобы задать пароль:</span><span class="sxs-lookup"><span data-stu-id="93802-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="93802-457">Выберите надежный пароль: используйте восемь или более символов и по крайней мере одну прописную букву, цифру и символ.</span><span class="sxs-lookup"><span data-stu-id="93802-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="93802-458">Например, `Passw0rd!` соответствует требованиям к надежному паролю.</span><span class="sxs-lookup"><span data-stu-id="93802-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="93802-459">Выполните следующую команду из папки проекта, где `<PW>` — пароль:</span><span class="sxs-lookup"><span data-stu-id="93802-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="93802-460">Удаление и обновление базы данных</span><span class="sxs-lookup"><span data-stu-id="93802-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="93802-461">Перезапустите приложение, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="93802-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="93802-462">Простой способ тестирования завершенного приложения — запуск трех различных браузеров (или режиме инкогнито и нечастных сеансов).</span><span class="sxs-lookup"><span data-stu-id="93802-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="93802-463">В одном браузере Зарегистрируйте нового пользователя (например, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="93802-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="93802-464">Войдите в каждый браузер с другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="93802-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="93802-465">Проверьте следующие операции:</span><span class="sxs-lookup"><span data-stu-id="93802-465">Verify the following operations:</span></span>

* <span data-ttu-id="93802-466">Зарегистрированные пользователи могут просматривать все утвержденные контактные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="93802-467">Зарегистрированные пользователи могут изменять и удалять собственные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="93802-468">Руководители могут утверждать и отклонять контактные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="93802-469">В `Details` представлении отображаются кнопки **утвердить** и **отклонить** .</span><span class="sxs-lookup"><span data-stu-id="93802-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="93802-470">Администраторы могут утверждать, отклонять и изменять и удалять все данные.</span><span class="sxs-lookup"><span data-stu-id="93802-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="93802-471">Пользователь</span><span class="sxs-lookup"><span data-stu-id="93802-471">User</span></span>                | <span data-ttu-id="93802-472">Заполнено приложением</span><span class="sxs-lookup"><span data-stu-id="93802-472">Seeded by the app</span></span> | <span data-ttu-id="93802-473">Параметры</span><span class="sxs-lookup"><span data-stu-id="93802-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="93802-474">Нет</span><span class="sxs-lookup"><span data-stu-id="93802-474">No</span></span>                | <span data-ttu-id="93802-475">Изменение или удаление собственных данных.</span><span class="sxs-lookup"><span data-stu-id="93802-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="93802-476">да</span><span class="sxs-lookup"><span data-stu-id="93802-476">Yes</span></span>               | <span data-ttu-id="93802-477">Утвердите, отклоните и измените или удалите собственные данные.</span><span class="sxs-lookup"><span data-stu-id="93802-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="93802-478">да</span><span class="sxs-lookup"><span data-stu-id="93802-478">Yes</span></span>               | <span data-ttu-id="93802-479">Утвердите или отклоните и измените или удалите все данные.</span><span class="sxs-lookup"><span data-stu-id="93802-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="93802-480">Создайте контакт в браузере администратора.</span><span class="sxs-lookup"><span data-stu-id="93802-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="93802-481">Скопируйте URL-адрес для DELETE и Edit из контакта администратора.</span><span class="sxs-lookup"><span data-stu-id="93802-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="93802-482">Вставьте эти ссылки в браузер тестового пользователя, чтобы убедиться, что тестовая пользователь не может выполнить эти операции.</span><span class="sxs-lookup"><span data-stu-id="93802-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="93802-483">Создание начального приложения</span><span class="sxs-lookup"><span data-stu-id="93802-483">Create the starter app</span></span>

* <span data-ttu-id="93802-484">Создание Razor страницы приложения с именем "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="93802-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="93802-485">Создайте приложение с **учетными записями отдельных пользователей**.</span><span class="sxs-lookup"><span data-stu-id="93802-485">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="93802-486">Назовите его "ContactManager", чтобы пространство имен совпадало с пространством имен, используемым в примере.</span><span class="sxs-lookup"><span data-stu-id="93802-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="93802-487">`-uld`Указывает LocalDB вместо SQLite</span><span class="sxs-lookup"><span data-stu-id="93802-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="93802-488">Добавление *моделей/Contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="93802-488">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="93802-489">Формирование шаблонов `Contact` модели.</span><span class="sxs-lookup"><span data-stu-id="93802-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="93802-490">Создайте начальную миграцию и обновите базу данных:</span><span class="sxs-lookup"><span data-stu-id="93802-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="93802-491">Обновите привязку **ContactManager** в файле *pages/_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="93802-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="93802-492">Тестирование приложения путем создания, изменения и удаления контакта</span><span class="sxs-lookup"><span data-stu-id="93802-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="93802-493">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="93802-493">Seed the database</span></span>

<span data-ttu-id="93802-494">Добавьте класс [сиддата](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) в папку *Data* .</span><span class="sxs-lookup"><span data-stu-id="93802-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="93802-495">Вызов `SeedData.Initialize` из `Main` :</span><span class="sxs-lookup"><span data-stu-id="93802-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="93802-496">Проверьте, что приложение заполнено базой данных.</span><span class="sxs-lookup"><span data-stu-id="93802-496">Test that the app seeded the database.</span></span> <span data-ttu-id="93802-497">Если в базе данных Contact есть какие бы то ни было строки, метод SEED не выполняется.</span><span class="sxs-lookup"><span data-stu-id="93802-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="93802-498">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="93802-498">Additional resources</span></span>

* [<span data-ttu-id="93802-499">Сборка веб-приложения .NET Core и базы данных SQL в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="93802-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="93802-500">[ASP.NET Core лаборатории авторизации](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="93802-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="93802-501">В этом лабораторном занятии подробно рассматриваются функции безопасности, представленные в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="93802-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="93802-502">Настраиваемая авторизация на основе политик</span><span class="sxs-lookup"><span data-stu-id="93802-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
