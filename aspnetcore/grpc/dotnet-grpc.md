---
title: Управление ссылками protobuf с помощью .NET gRPC
author: juntaoluo
description: Сведения о добавлении, обновлении, удалении и перечислении ссылок Protobuf с помощью глобального средства dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: 18d504d7a130aeaa9f8ab6cca6c92dab5febfae6
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633244"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="f42ae-103">Управление ссылками protobuf с помощью .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="f42ae-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="f42ae-104">Автор: [John Luo](https://github.com/juntaoluo) (Джон Луо)</span><span class="sxs-lookup"><span data-stu-id="f42ae-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="f42ae-105">`dotnet-grpc` — это глобальное средство .NET Core для управления ссылками [Protobuf ( *.proto*)](xref:grpc/basics#proto-file) в рамках проекта gRPC .NET.</span><span class="sxs-lookup"><span data-stu-id="f42ae-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="f42ae-106">Это средство можно использовать для добавления, обновления, удаления и перечисления ссылок Protobuf.</span><span class="sxs-lookup"><span data-stu-id="f42ae-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="f42ae-107">Установка</span><span class="sxs-lookup"><span data-stu-id="f42ae-107">Installation</span></span>

<span data-ttu-id="f42ae-108">Чтобы установить [глобальное средство .NET Core](/dotnet/core/tools/global-tools) `dotnet-grpc`, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="f42ae-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="f42ae-109">Добавление ссылок</span><span class="sxs-lookup"><span data-stu-id="f42ae-109">Add references</span></span>

<span data-ttu-id="f42ae-110">`dotnet-grpc` можно использовать для добавления ссылок Protobuf в качестве элементов `<Protobuf />` в файл *CSPROJ*:</span><span class="sxs-lookup"><span data-stu-id="f42ae-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="f42ae-111">Ссылки Protobuf используются для создания клиентских и (или) серверных ресурсов C#.</span><span class="sxs-lookup"><span data-stu-id="f42ae-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="f42ae-112">Средство `dotnet-grpc` предоставляет указанные ниже возможности:</span><span class="sxs-lookup"><span data-stu-id="f42ae-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="f42ae-113">Создание ссылки Protobuf из локальных файлов на диске.</span><span class="sxs-lookup"><span data-stu-id="f42ae-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="f42ae-114">Создание ссылки Protobuf из удаленного файла, указанного с помощью URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="f42ae-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="f42ae-115">Гарантия добавления в проект правильных зависимостей пакета gRPC.</span><span class="sxs-lookup"><span data-stu-id="f42ae-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="f42ae-116">Например, пакет `Grpc.AspNetCore` добавляется в веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="f42ae-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="f42ae-117">`Grpc.AspNetCore` содержит поддержку инструментов и серверных и клиентских библиотек gRPC.</span><span class="sxs-lookup"><span data-stu-id="f42ae-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="f42ae-118">Кроме того, пакеты `Grpc.Net.Client`, `Grpc.Tools` и `Google.Protobuf`, содержащие только поддержку инструментов и серверных и клиентских библиотек gRPC, добавляются в консольное приложение.</span><span class="sxs-lookup"><span data-stu-id="f42ae-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="f42ae-119">Добавление файла</span><span class="sxs-lookup"><span data-stu-id="f42ae-119">Add file</span></span>

<span data-ttu-id="f42ae-120">Команда `add-file` используется для добавления локальных файлов на диске в качестве ссылок Protobuf.</span><span class="sxs-lookup"><span data-stu-id="f42ae-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="f42ae-121">Предоставленные пути к файлам:</span><span class="sxs-lookup"><span data-stu-id="f42ae-121">The file paths provided:</span></span>

* <span data-ttu-id="f42ae-122">могут быть указаны относительно текущего каталога или абсолютных путей;</span><span class="sxs-lookup"><span data-stu-id="f42ae-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="f42ae-123">могут содержать подстановочные знаки для использования [стандартных масок ](https://wikipedia.org/wiki/Glob_(programming)) файлов.</span><span class="sxs-lookup"><span data-stu-id="f42ae-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="f42ae-124">Если какие-либо файлы находятся за пределами каталога проекта, добавляется элемент `Link` для отображения файла в папке `Protos` в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f42ae-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="f42ae-125">Использование</span><span class="sxs-lookup"><span data-stu-id="f42ae-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="f42ae-126">Аргументы</span><span class="sxs-lookup"><span data-stu-id="f42ae-126">Arguments</span></span>

| <span data-ttu-id="f42ae-127">Аргумент</span><span class="sxs-lookup"><span data-stu-id="f42ae-127">Argument</span></span> | <span data-ttu-id="f42ae-128">Описание</span><span class="sxs-lookup"><span data-stu-id="f42ae-128">Description</span></span> |
|-|-|
| <span data-ttu-id="f42ae-129">файлы</span><span class="sxs-lookup"><span data-stu-id="f42ae-129">files</span></span> | <span data-ttu-id="f42ae-130">Ссылки на файлы protobuf.</span><span class="sxs-lookup"><span data-stu-id="f42ae-130">The protobuf file references.</span></span> <span data-ttu-id="f42ae-131">Это может быть путь к стандартной маске для локальных файлов protobuf.</span><span class="sxs-lookup"><span data-stu-id="f42ae-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="f42ae-132">Параметры</span><span class="sxs-lookup"><span data-stu-id="f42ae-132">Options</span></span>

| <span data-ttu-id="f42ae-133">Короткий параметр</span><span class="sxs-lookup"><span data-stu-id="f42ae-133">Short option</span></span> | <span data-ttu-id="f42ae-134">Длинный параметр</span><span class="sxs-lookup"><span data-stu-id="f42ae-134">Long option</span></span> | <span data-ttu-id="f42ae-135">Описание</span><span class="sxs-lookup"><span data-stu-id="f42ae-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="f42ae-136">-p</span><span class="sxs-lookup"><span data-stu-id="f42ae-136">-p</span></span> | <span data-ttu-id="f42ae-137">--project</span><span class="sxs-lookup"><span data-stu-id="f42ae-137">--project</span></span> | <span data-ttu-id="f42ae-138">Путь к целевому файлу проекта.</span><span class="sxs-lookup"><span data-stu-id="f42ae-138">The path to the project file to operate on.</span></span> <span data-ttu-id="f42ae-139">Если файл не указан, команда ищет текущий каталог для него.</span><span class="sxs-lookup"><span data-stu-id="f42ae-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="f42ae-140">-S</span><span class="sxs-lookup"><span data-stu-id="f42ae-140">-s</span></span> | <span data-ttu-id="f42ae-141">--services</span><span class="sxs-lookup"><span data-stu-id="f42ae-141">--services</span></span> | <span data-ttu-id="f42ae-142">Тип служб gRPC, которые нужно создать.</span><span class="sxs-lookup"><span data-stu-id="f42ae-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="f42ae-143">Если указан `Default`, `Both` используется для веб-проектов, а `Client` — для прочих проектов.</span><span class="sxs-lookup"><span data-stu-id="f42ae-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="f42ae-144">Допустимые значения: `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="f42ae-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="f42ae-145">-i</span><span class="sxs-lookup"><span data-stu-id="f42ae-145">-i</span></span> | <span data-ttu-id="f42ae-146">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="f42ae-146">--additional-import-dirs</span></span> | <span data-ttu-id="f42ae-147">Дополнительные каталоги, которые будут использоваться при разрешении импортов для файлов protobuf.</span><span class="sxs-lookup"><span data-stu-id="f42ae-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="f42ae-148">Это список путей, разделенных точкой с запятой.</span><span class="sxs-lookup"><span data-stu-id="f42ae-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="f42ae-149">--access</span><span class="sxs-lookup"><span data-stu-id="f42ae-149">--access</span></span> | <span data-ttu-id="f42ae-150">Модификатор доступа, используемый для создаваемых классов C#.</span><span class="sxs-lookup"><span data-stu-id="f42ae-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="f42ae-151">Значение по умолчанию — `Public`.</span><span class="sxs-lookup"><span data-stu-id="f42ae-151">The default value is `Public`.</span></span> <span data-ttu-id="f42ae-152">Допустимые значения: `Internal` и `Public`.</span><span class="sxs-lookup"><span data-stu-id="f42ae-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="f42ae-153">Добавление URL-адреса</span><span class="sxs-lookup"><span data-stu-id="f42ae-153">Add URL</span></span>

<span data-ttu-id="f42ae-154">Команда `add-url` используется для добавления удаленного файла, заданного исходным URL-адресом, в качестве ссылки Protobuf.</span><span class="sxs-lookup"><span data-stu-id="f42ae-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="f42ae-155">Чтобы указать место для скачивания удаленного файла, необходимо задать путь к файлу.</span><span class="sxs-lookup"><span data-stu-id="f42ae-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="f42ae-156">Этот путь может быть указан относительно текущего каталога или абсолютного пути.</span><span class="sxs-lookup"><span data-stu-id="f42ae-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="f42ae-157">Если этот путь находится за пределами каталога проекта, добавляется элемент `Link` для отображения файла в папке `Protos` в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f42ae-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="f42ae-158">Использование</span><span class="sxs-lookup"><span data-stu-id="f42ae-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="f42ae-159">Аргументы</span><span class="sxs-lookup"><span data-stu-id="f42ae-159">Arguments</span></span>

| <span data-ttu-id="f42ae-160">Аргумент</span><span class="sxs-lookup"><span data-stu-id="f42ae-160">Argument</span></span> | <span data-ttu-id="f42ae-161">Описание</span><span class="sxs-lookup"><span data-stu-id="f42ae-161">Description</span></span> |
|-|-|
| <span data-ttu-id="f42ae-162">url</span><span class="sxs-lookup"><span data-stu-id="f42ae-162">url</span></span> | <span data-ttu-id="f42ae-163">URL-адрес удаленного файла protobuf.</span><span class="sxs-lookup"><span data-stu-id="f42ae-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="f42ae-164">Параметры</span><span class="sxs-lookup"><span data-stu-id="f42ae-164">Options</span></span>

| <span data-ttu-id="f42ae-165">Короткий параметр</span><span class="sxs-lookup"><span data-stu-id="f42ae-165">Short option</span></span> | <span data-ttu-id="f42ae-166">Длинный параметр</span><span class="sxs-lookup"><span data-stu-id="f42ae-166">Long option</span></span> | <span data-ttu-id="f42ae-167">Описание</span><span class="sxs-lookup"><span data-stu-id="f42ae-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="f42ae-168">-o</span><span class="sxs-lookup"><span data-stu-id="f42ae-168">-o</span></span> | <span data-ttu-id="f42ae-169">--output</span><span class="sxs-lookup"><span data-stu-id="f42ae-169">--output</span></span> | <span data-ttu-id="f42ae-170">Указывает путь скачивания для удаленного файла protobuf.</span><span class="sxs-lookup"><span data-stu-id="f42ae-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="f42ae-171">Это обязательный параметр.</span><span class="sxs-lookup"><span data-stu-id="f42ae-171">This is a required option.</span></span>
| <span data-ttu-id="f42ae-172">-p</span><span class="sxs-lookup"><span data-stu-id="f42ae-172">-p</span></span> | <span data-ttu-id="f42ae-173">--project</span><span class="sxs-lookup"><span data-stu-id="f42ae-173">--project</span></span> | <span data-ttu-id="f42ae-174">Путь к целевому файлу проекта.</span><span class="sxs-lookup"><span data-stu-id="f42ae-174">The path to the project file to operate on.</span></span> <span data-ttu-id="f42ae-175">Если файл не указан, команда ищет текущий каталог для него.</span><span class="sxs-lookup"><span data-stu-id="f42ae-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="f42ae-176">-S</span><span class="sxs-lookup"><span data-stu-id="f42ae-176">-s</span></span> | <span data-ttu-id="f42ae-177">--services</span><span class="sxs-lookup"><span data-stu-id="f42ae-177">--services</span></span> | <span data-ttu-id="f42ae-178">Тип служб gRPC, которые нужно создать.</span><span class="sxs-lookup"><span data-stu-id="f42ae-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="f42ae-179">Если указан `Default`, `Both` используется для веб-проектов, а `Client` — для прочих проектов.</span><span class="sxs-lookup"><span data-stu-id="f42ae-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="f42ae-180">Допустимые значения: `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="f42ae-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="f42ae-181">-i</span><span class="sxs-lookup"><span data-stu-id="f42ae-181">-i</span></span> | <span data-ttu-id="f42ae-182">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="f42ae-182">--additional-import-dirs</span></span> | <span data-ttu-id="f42ae-183">Дополнительные каталоги, которые будут использоваться при разрешении импортов для файлов protobuf.</span><span class="sxs-lookup"><span data-stu-id="f42ae-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="f42ae-184">Это список путей, разделенных точкой с запятой.</span><span class="sxs-lookup"><span data-stu-id="f42ae-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="f42ae-185">--access</span><span class="sxs-lookup"><span data-stu-id="f42ae-185">--access</span></span> | <span data-ttu-id="f42ae-186">Модификатор доступа, используемый для создаваемых классов C#.</span><span class="sxs-lookup"><span data-stu-id="f42ae-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="f42ae-187">Значение по умолчанию — `Public`.</span><span class="sxs-lookup"><span data-stu-id="f42ae-187">Default value is `Public`.</span></span> <span data-ttu-id="f42ae-188">Допустимые значения: `Internal` и `Public`.</span><span class="sxs-lookup"><span data-stu-id="f42ae-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="f42ae-189">Удалить</span><span class="sxs-lookup"><span data-stu-id="f42ae-189">Remove</span></span>

<span data-ttu-id="f42ae-190">Команда `remove` используется для удаления ссылок Protobuf из файла *CSPROJ*.</span><span class="sxs-lookup"><span data-stu-id="f42ae-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="f42ae-191">В качестве аргументов эта команда принимает URL-адреса источника и аргументы пути.</span><span class="sxs-lookup"><span data-stu-id="f42ae-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="f42ae-192">Средство:</span><span class="sxs-lookup"><span data-stu-id="f42ae-192">The tool:</span></span>

* <span data-ttu-id="f42ae-193">удаляет только ссылку Protobuf;</span><span class="sxs-lookup"><span data-stu-id="f42ae-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="f42ae-194">не удаляет файл *PROTO*, даже если он был первоначально скачан с удаленного URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="f42ae-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="f42ae-195">Использование</span><span class="sxs-lookup"><span data-stu-id="f42ae-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="f42ae-196">Аргументы</span><span class="sxs-lookup"><span data-stu-id="f42ae-196">Arguments</span></span>

| <span data-ttu-id="f42ae-197">Аргумент</span><span class="sxs-lookup"><span data-stu-id="f42ae-197">Argument</span></span> | <span data-ttu-id="f42ae-198">Описание</span><span class="sxs-lookup"><span data-stu-id="f42ae-198">Description</span></span> |
|-|-|
| <span data-ttu-id="f42ae-199">ссылки</span><span class="sxs-lookup"><span data-stu-id="f42ae-199">references</span></span> | <span data-ttu-id="f42ae-200">URL-адреса или пути к файлам для удаляемых ссылок protobuf.</span><span class="sxs-lookup"><span data-stu-id="f42ae-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="f42ae-201">Параметры</span><span class="sxs-lookup"><span data-stu-id="f42ae-201">Options</span></span>

| <span data-ttu-id="f42ae-202">Короткий параметр</span><span class="sxs-lookup"><span data-stu-id="f42ae-202">Short option</span></span> | <span data-ttu-id="f42ae-203">Длинный параметр</span><span class="sxs-lookup"><span data-stu-id="f42ae-203">Long option</span></span> | <span data-ttu-id="f42ae-204">Описание</span><span class="sxs-lookup"><span data-stu-id="f42ae-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="f42ae-205">-p</span><span class="sxs-lookup"><span data-stu-id="f42ae-205">-p</span></span> | <span data-ttu-id="f42ae-206">--project</span><span class="sxs-lookup"><span data-stu-id="f42ae-206">--project</span></span> | <span data-ttu-id="f42ae-207">Путь к целевому файлу проекта.</span><span class="sxs-lookup"><span data-stu-id="f42ae-207">The path to the project file to operate on.</span></span> <span data-ttu-id="f42ae-208">Если файл не указан, команда ищет текущий каталог для него.</span><span class="sxs-lookup"><span data-stu-id="f42ae-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="f42ae-209">Обновление</span><span class="sxs-lookup"><span data-stu-id="f42ae-209">Refresh</span></span>

<span data-ttu-id="f42ae-210">Команда `refresh` используется для обновления удаленной ссылки последним содержимым из URL-адреса источника.</span><span class="sxs-lookup"><span data-stu-id="f42ae-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="f42ae-211">Для указания обновляемой ссылки можно использовать как путь скачивания файла, так и URL-адрес источника.</span><span class="sxs-lookup"><span data-stu-id="f42ae-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="f42ae-212">Примечание.</span><span class="sxs-lookup"><span data-stu-id="f42ae-212">Note:</span></span>

* <span data-ttu-id="f42ae-213">Хэши содержимого файла сравниваются, чтобы определить, требуется ли обновить локальный файл.</span><span class="sxs-lookup"><span data-stu-id="f42ae-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="f42ae-214">Сведения о метке времени не сравниваются.</span><span class="sxs-lookup"><span data-stu-id="f42ae-214">No timestamp information is compared.</span></span>

<span data-ttu-id="f42ae-215">Средство всегда заменяет локальный файл удаленным, если требуется обновление.</span><span class="sxs-lookup"><span data-stu-id="f42ae-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="f42ae-216">Использование</span><span class="sxs-lookup"><span data-stu-id="f42ae-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="f42ae-217">Аргументы</span><span class="sxs-lookup"><span data-stu-id="f42ae-217">Arguments</span></span>

| <span data-ttu-id="f42ae-218">Аргумент</span><span class="sxs-lookup"><span data-stu-id="f42ae-218">Argument</span></span> | <span data-ttu-id="f42ae-219">Описание</span><span class="sxs-lookup"><span data-stu-id="f42ae-219">Description</span></span> |
|-|-|
| <span data-ttu-id="f42ae-220">ссылки</span><span class="sxs-lookup"><span data-stu-id="f42ae-220">references</span></span> | <span data-ttu-id="f42ae-221">URL-адреса или пути к файлам для удаленных ссылок protobuf, которые нужно обновить.</span><span class="sxs-lookup"><span data-stu-id="f42ae-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="f42ae-222">Оставьте этот аргумент пустым, чтобы обновить все удаленные ссылки.</span><span class="sxs-lookup"><span data-stu-id="f42ae-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="f42ae-223">Параметры</span><span class="sxs-lookup"><span data-stu-id="f42ae-223">Options</span></span>

| <span data-ttu-id="f42ae-224">Короткий параметр</span><span class="sxs-lookup"><span data-stu-id="f42ae-224">Short option</span></span> | <span data-ttu-id="f42ae-225">Длинный параметр</span><span class="sxs-lookup"><span data-stu-id="f42ae-225">Long option</span></span> | <span data-ttu-id="f42ae-226">Описание</span><span class="sxs-lookup"><span data-stu-id="f42ae-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="f42ae-227">-p</span><span class="sxs-lookup"><span data-stu-id="f42ae-227">-p</span></span> | <span data-ttu-id="f42ae-228">--project</span><span class="sxs-lookup"><span data-stu-id="f42ae-228">--project</span></span> | <span data-ttu-id="f42ae-229">Путь к целевому файлу проекта.</span><span class="sxs-lookup"><span data-stu-id="f42ae-229">The path to the project file to operate on.</span></span> <span data-ttu-id="f42ae-230">Если файл не указан, команда ищет текущий каталог для него.</span><span class="sxs-lookup"><span data-stu-id="f42ae-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="f42ae-231">--dry-run</span><span class="sxs-lookup"><span data-stu-id="f42ae-231">--dry-run</span></span> | <span data-ttu-id="f42ae-232">Выводит список файлов, которые будут обновлены без скачивания нового содержимого.</span><span class="sxs-lookup"><span data-stu-id="f42ae-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="f42ae-233">Список</span><span class="sxs-lookup"><span data-stu-id="f42ae-233">List</span></span>

<span data-ttu-id="f42ae-234">Команда `list` используется для вывода всех ссылок на Protobuf в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="f42ae-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="f42ae-235">Если все значения столбца являются значениями по умолчанию, этот столбец можно опустить.</span><span class="sxs-lookup"><span data-stu-id="f42ae-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="f42ae-236">Использование</span><span class="sxs-lookup"><span data-stu-id="f42ae-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="f42ae-237">Параметры</span><span class="sxs-lookup"><span data-stu-id="f42ae-237">Options</span></span>

| <span data-ttu-id="f42ae-238">Короткий параметр</span><span class="sxs-lookup"><span data-stu-id="f42ae-238">Short option</span></span> | <span data-ttu-id="f42ae-239">Длинный параметр</span><span class="sxs-lookup"><span data-stu-id="f42ae-239">Long option</span></span> | <span data-ttu-id="f42ae-240">Описание</span><span class="sxs-lookup"><span data-stu-id="f42ae-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="f42ae-241">-p</span><span class="sxs-lookup"><span data-stu-id="f42ae-241">-p</span></span> | <span data-ttu-id="f42ae-242">--project</span><span class="sxs-lookup"><span data-stu-id="f42ae-242">--project</span></span> | <span data-ttu-id="f42ae-243">Путь к целевому файлу проекта.</span><span class="sxs-lookup"><span data-stu-id="f42ae-243">The path to the project file to operate on.</span></span> <span data-ttu-id="f42ae-244">Если файл не указан, команда ищет текущий каталог для него.</span><span class="sxs-lookup"><span data-stu-id="f42ae-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f42ae-245">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f42ae-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
