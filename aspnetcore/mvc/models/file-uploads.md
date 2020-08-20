---
title: Передача файлов в ASP.NET Core
author: rick-anderson
description: Сведения об использовании привязки модели и потоковой передачи для передачи файлов в ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 93ffa3a5313e63a1e9b98fb5bf9788944254213f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635220"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="ca8f2-103">Передача файлов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca8f2-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="ca8f2-104">По [Стив Смит](https://ardalis.com/) и [рутжер](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="ca8f2-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca8f2-105">Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="ca8f2-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca8f2-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ca8f2-107">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ca8f2-107">Security considerations</span></span>

<span data-ttu-id="ca8f2-108">Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="ca8f2-109">Злоумышленники могут попытаться:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="ca8f2-110">Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="ca8f2-111">Передать вирусы и вредоносные программы.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="ca8f2-112">Нарушить безопасность сетей и серверов другими способами.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="ca8f2-113">Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="ca8f2-114">Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="ca8f2-115">Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="ca8f2-116">Отключите разрешения на выполнение для расположения отправки файла.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="ca8f2-117">**Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="ca8f2-118">Используйте безопасное имя файла, определяемое приложением.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="ca8f2-119">Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="ca8f2-120">Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="ca8f2-121">Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="ca8f2-122">Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="ca8f2-123">Проверьте размер отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="ca8f2-124">Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="ca8f2-125">Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="ca8f2-126">**Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="ca8f2-127">&dagger;Пример приложения демонстрирует подход, который соответствует критериям.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="ca8f2-128">Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="ca8f2-129">полностью получить контроль над системой;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="ca8f2-130">перезагрузить систему так, что она окажется в неработоспособном состоянии;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="ca8f2-131">скомпрометировать пользовательские или системные данные;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="ca8f2-132">применить граффити к открытому интерфейсу.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="ca8f2-133">Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="ca8f2-134">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="ca8f2-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="ca8f2-135">Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей</span><span class="sxs-lookup"><span data-stu-id="ca8f2-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="ca8f2-136">Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="ca8f2-137">Сценарии использования хранилища</span><span class="sxs-lookup"><span data-stu-id="ca8f2-137">Storage scenarios</span></span>

<span data-ttu-id="ca8f2-138">К общим вариантам хранилища файлов относятся следующие:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-138">Common storage options for files include:</span></span>

* <span data-ttu-id="ca8f2-139">База данных</span><span class="sxs-lookup"><span data-stu-id="ca8f2-139">Database</span></span>

  * <span data-ttu-id="ca8f2-140">В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="ca8f2-141">База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="ca8f2-142">Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="ca8f2-143">Физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="ca8f2-144">Обработка передачи больших файлов:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-144">For large file uploads:</span></span>
    * <span data-ttu-id="ca8f2-145">Ограничения базы данных могут ограничивать размер передачи.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="ca8f2-146">Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="ca8f2-147">Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="ca8f2-148">Процесс приложения должен иметь разрешения на чтение и запись для места хранения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="ca8f2-149">**Никогда не предоставляйте разрешение на выполнение.**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="ca8f2-150">Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="ca8f2-151">Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="ca8f2-152">Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="ca8f2-153">Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="ca8f2-154">Сценарии передачи файлов</span><span class="sxs-lookup"><span data-stu-id="ca8f2-154">File upload scenarios</span></span>

<span data-ttu-id="ca8f2-155">Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="ca8f2-156">**ответов**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-156">**Buffering**</span></span>

<span data-ttu-id="ca8f2-157">Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="ca8f2-158">Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="ca8f2-159">При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="ca8f2-160">Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="ca8f2-161">Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="ca8f2-162">Буферизация небольших файлов описана в следующих разделах этой статьи:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="ca8f2-163">Физическое хранилище</span><span class="sxs-lookup"><span data-stu-id="ca8f2-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="ca8f2-164">База данных</span><span class="sxs-lookup"><span data-stu-id="ca8f2-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="ca8f2-165">**Потоковая передача**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-165">**Streaming**</span></span>

<span data-ttu-id="ca8f2-166">Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="ca8f2-167">Потоковая передача повышает производительность не значительно.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="ca8f2-168">При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="ca8f2-169">Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="ca8f2-170">Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу</span><span class="sxs-lookup"><span data-stu-id="ca8f2-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="ca8f2-171">Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="ca8f2-172">В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):</span><span class="sxs-lookup"><span data-stu-id="ca8f2-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="ca8f2-173">Следующий пример аналогичен предыдущему примеру, за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="ca8f2-174">JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="ca8f2-175">Проверка не выполняется.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-175">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="ca8f2-176">Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="ca8f2-177">Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="ca8f2-178">Используйте команду `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="ca8f2-179">Пример:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-179">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="ca8f2-180">Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="ca8f2-181">Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="ca8f2-182">Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="ca8f2-183">В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="ca8f2-184">**Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="ca8f2-185">При отображении или ведении журнала кодируйте имя файла в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="ca8f2-186">Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="ca8f2-187">Приложения должны:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-187">Applications should:</span></span>
>
> * <span data-ttu-id="ca8f2-188">удалить путь из имени файла, указываемого пользователем;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="ca8f2-189">сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="ca8f2-190">создать случайное имя файла для хранения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="ca8f2-191">Следующий код удаляет путь из имени файла:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="ca8f2-192">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="ca8f2-193">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ca8f2-194">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ca8f2-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ca8f2-195">Проверка</span><span class="sxs-lookup"><span data-stu-id="ca8f2-195">Validation</span></span>](#validation)

<span data-ttu-id="ca8f2-196">При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="ca8f2-197">Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="ca8f2-198">Любая из следующих коллекций, представляющих несколько файлов:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="ca8f2-199">[Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="ca8f2-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="ca8f2-200">Привязка сопоставляет файлы форм по имени.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-200">Binding matches form files by name.</span></span> <span data-ttu-id="ca8f2-201">Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="ca8f2-202">Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="ca8f2-203">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-203">The following example:</span></span>

* <span data-ttu-id="ca8f2-204">Циклично отправляет один или несколько передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="ca8f2-205">Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="ca8f2-206">Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="ca8f2-207">Возвращает общее число и размер отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-207">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="ca8f2-208">Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="ca8f2-209">В следующем примере путь получен из конфигурации:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-209">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="ca8f2-210">Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="ca8f2-211">Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="ca8f2-212">Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="ca8f2-213">Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="ca8f2-214">Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="ca8f2-215">Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="ca8f2-216">Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="ca8f2-217">Ограничение в 65 535 файлов предусмотрено для каждого сервера.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="ca8f2-218">Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * <span data-ttu-id="ca8f2-219">[Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);</span><span class="sxs-lookup"><span data-stu-id="ca8f2-219">[GetTempFileNameA function](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)</span></span>
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="ca8f2-220">Передача небольших файлов с привязкой буферизованной модели к базе данных</span><span class="sxs-lookup"><span data-stu-id="ca8f2-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="ca8f2-221">Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="ca8f2-222">Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="ca8f2-223"><xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="ca8f2-224">В предыдущем примере используется свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="ca8f2-225">`FileUpload`Используется в Razor форме страниц:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-225">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="ca8f2-226">При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="ca8f2-227">В следующем примере `_dbContext` сохраняет контекст базы данных приложения:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-227">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="ca8f2-228">Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="ca8f2-229">*Pages/BufferedSingleFileUploadDb.cshtml*;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="ca8f2-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="ca8f2-231">При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="ca8f2-232">Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="ca8f2-233">Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="ca8f2-234">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="ca8f2-235">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ca8f2-236">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ca8f2-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ca8f2-237">Проверка</span><span class="sxs-lookup"><span data-stu-id="ca8f2-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="ca8f2-238">Передача больших файлов с помощью потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="ca8f2-238">Upload large files with streaming</span></span>

<span data-ttu-id="ca8f2-239">В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="ca8f2-240">Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="ca8f2-241">Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="ca8f2-242">Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="ca8f2-243">После считывания составных разделов действие выполняет собственную привязку модели.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="ca8f2-244">Начальный ответ страницы загружает форму и сохраняет токен подделки в cookie (с помощью `GenerateAntiforgeryTokenCookieAttribute` атрибута).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="ca8f2-245">Для установки с помощью маркера запроса атрибут использует встроенную поддержку защиты от [подделки](xref:security/anti-request-forgery) ASP.NET Core cookie .</span><span class="sxs-lookup"><span data-stu-id="ca8f2-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="ca8f2-246">Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="ca8f2-247">В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="ca8f2-248">Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="ca8f2-249">Метод действия работает напрямую со свойством `Request`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="ca8f2-250">Для считывания каждого раздела служит объект `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="ca8f2-251">Данные "ключ — значение" хранятся в `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="ca8f2-252">После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="ca8f2-253">Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="ca8f2-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="ca8f2-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="ca8f2-255">Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="ca8f2-256">В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="ca8f2-257">Проверка</span><span class="sxs-lookup"><span data-stu-id="ca8f2-257">Validation</span></span>

<span data-ttu-id="ca8f2-258">В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="ca8f2-259">Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="ca8f2-260">Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="ca8f2-261">Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="ca8f2-262">В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="ca8f2-263">Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="ca8f2-264">Вы полностью разбираетесь в реализации.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="ca8f2-265">Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="ca8f2-266">**Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="ca8f2-267">Проверка содержимого</span><span class="sxs-lookup"><span data-stu-id="ca8f2-267">Content validation</span></span>

<span data-ttu-id="ca8f2-268">**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="ca8f2-269">Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="ca8f2-270">Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="ca8f2-271">Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="ca8f2-272">При передаче файл перемещается в нормальное место хранения файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="ca8f2-273">Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="ca8f2-274">Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="ca8f2-275">Проверка расширения файла</span><span class="sxs-lookup"><span data-stu-id="ca8f2-275">File extension validation</span></span>

<span data-ttu-id="ca8f2-276">Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="ca8f2-277">Пример:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="ca8f2-278">Проверка подписи файла</span><span class="sxs-lookup"><span data-stu-id="ca8f2-278">File signature validation</span></span>

<span data-ttu-id="ca8f2-279">Подпись файла определяется по первым нескольким байтам в начале файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="ca8f2-280">Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="ca8f2-281">Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="ca8f2-282">В следующем примере проверяется подпись файла для изображения JPEG:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="ca8f2-283">Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="ca8f2-284">Безопасность имени файла</span><span class="sxs-lookup"><span data-stu-id="ca8f2-284">File name security</span></span>

<span data-ttu-id="ca8f2-285">Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="ca8f2-286">Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="ca8f2-287">Razor автоматически кодирует значения свойств для вывода.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="ca8f2-288">Ниже приведен безопасный код, который можно использовать.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="ca8f2-289">За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="ca8f2-290">Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="ca8f2-291">Предоставьте дополнительную логику для соответствия спецификациям приложения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="ca8f2-292">Проверка размера</span><span class="sxs-lookup"><span data-stu-id="ca8f2-292">Size validation</span></span>

<span data-ttu-id="ca8f2-293">Ограничьте размер передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="ca8f2-294">В примере приложения размер файла ограничен 2 МБ (указывается в байтах).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="ca8f2-295">Ограничение предоставляется с помощью [конфигурации](xref:fundamentals/configuration/index) из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="ca8f2-296">В классы `PageModel` внедряется `FileSizeLimit`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="ca8f2-297">Если размер файла превышает ограничение, файл отклоняется:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="ca8f2-298">Сопоставление значения атрибута имени и имени параметра метода POST</span><span class="sxs-lookup"><span data-stu-id="ca8f2-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="ca8f2-299">В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="ca8f2-300">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-300">In the following example:</span></span>

* <span data-ttu-id="ca8f2-301">При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="ca8f2-302">При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="ca8f2-303">Используйте соответствующее имя для параметра метода C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="ca8f2-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="ca8f2-304">Для Razor метода обработчика страницы страниц с именем `Upload` :</span><span class="sxs-lookup"><span data-stu-id="ca8f2-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="ca8f2-305">Для метода действия контроллера POST приложения MVC:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="ca8f2-306">Конфигурация сервера и приложения</span><span class="sxs-lookup"><span data-stu-id="ca8f2-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="ca8f2-307">Ограничение длины составного текста</span><span class="sxs-lookup"><span data-stu-id="ca8f2-307">Multipart body length limit</span></span>

<span data-ttu-id="ca8f2-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="ca8f2-309">Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="ca8f2-310">Значение по умолчанию — 134 217 728 байт (128 МБ).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="ca8f2-311">Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="ca8f2-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="ca8f2-313">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ca8f2-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="ca8f2-314">В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="ca8f2-315">Максимальный размер текста запроса Kestrel</span><span class="sxs-lookup"><span data-stu-id="ca8f2-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="ca8f2-316">Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ca8f2-317">Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size):</span><span class="sxs-lookup"><span data-stu-id="ca8f2-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="ca8f2-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="ca8f2-319">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ca8f2-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="ca8f2-320">В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="ca8f2-321">`RequestSizeLimitAttribute`Можно также применить с помощью [`@attribute`](xref:mvc/views/razor#attribute) Razor директивы:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="ca8f2-322">Другие ограничения Kestrel</span><span class="sxs-lookup"><span data-stu-id="ca8f2-322">Other Kestrel limits</span></span>

<span data-ttu-id="ca8f2-323">Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="ca8f2-324">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="ca8f2-325">Скорость передачи данных запросов и ответов</span><span class="sxs-lookup"><span data-stu-id="ca8f2-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="ca8f2-326">Ограничение длины содержимого IIS</span><span class="sxs-lookup"><span data-stu-id="ca8f2-326">IIS content length limit</span></span>

<span data-ttu-id="ca8f2-327">По умолчанию ограничение запроса (`maxAllowedContentLength`) составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="ca8f2-328">Ограничение можно настроить в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-328">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="ca8f2-329">Этот параметр применим только к службам IIS.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-329">This setting only applies to IIS.</span></span> <span data-ttu-id="ca8f2-330">При размещении в Kestrel такой проблемы возникать не должно.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="ca8f2-331">Дополнительные сведения см. в разделе [ограничения \<requestLimits> запросов ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="ca8f2-332">Ограничения в модуле ASP.NET Core или наличие модуля фильтрации запросов IIS могут ограничить передачу до 2 или 4 ГБ.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="ca8f2-333">Дополнительные сведения см. в статье форума (dotnet/AspNetCore #2711) [Unable to upload file greater than 2GB in size](https://github.com/dotnet/AspNetCore/issues/2711) (Не удалось отправить файл размером более 2 ГБ).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ca8f2-334">Диагностика</span><span class="sxs-lookup"><span data-stu-id="ca8f2-334">Troubleshoot</span></span>

<span data-ttu-id="ca8f2-335">Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="ca8f2-336">Ошибка "Не найдено" при развертывании на сервере IIS</span><span class="sxs-lookup"><span data-stu-id="ca8f2-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="ca8f2-337">Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="ca8f2-338">Дополнительные сведения об увеличении предела см. в разделе [Ограничение длины содержимого IIS](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="ca8f2-339">Сбой подключения</span><span class="sxs-lookup"><span data-stu-id="ca8f2-339">Connection failure</span></span>

<span data-ttu-id="ca8f2-340">Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="ca8f2-341">Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="ca8f2-342">Может также потребоваться настроить ограничения подключений клиентов Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="ca8f2-343">Исключение, связанное с пустой ссылкой в IFormFile</span><span class="sxs-lookup"><span data-stu-id="ca8f2-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="ca8f2-344">Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="ca8f2-345">Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="ca8f2-346">Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="ca8f2-347">Поток слишком длинный</span><span class="sxs-lookup"><span data-stu-id="ca8f2-347">Stream was too long</span></span>

<span data-ttu-id="ca8f2-348">В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="ca8f2-349">Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="ca8f2-350">Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca8f2-351">Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="ca8f2-352">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca8f2-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ca8f2-353">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ca8f2-353">Security considerations</span></span>

<span data-ttu-id="ca8f2-354">Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="ca8f2-355">Злоумышленники могут попытаться:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="ca8f2-356">Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="ca8f2-357">Передать вирусы и вредоносные программы.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="ca8f2-358">Нарушить безопасность сетей и серверов другими способами.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="ca8f2-359">Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="ca8f2-360">Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="ca8f2-361">Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="ca8f2-362">Отключите разрешения на выполнение для расположения отправки файла.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="ca8f2-363">**Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="ca8f2-364">Используйте безопасное имя файла, определяемое приложением.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="ca8f2-365">Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="ca8f2-366">Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="ca8f2-367">Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="ca8f2-368">Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="ca8f2-369">Проверьте размер отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="ca8f2-370">Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="ca8f2-371">Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="ca8f2-372">**Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="ca8f2-373">&dagger;Пример приложения демонстрирует подход, который соответствует критериям.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="ca8f2-374">Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="ca8f2-375">полностью получить контроль над системой;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="ca8f2-376">перезагрузить систему так, что она окажется в неработоспособном состоянии;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="ca8f2-377">скомпрометировать пользовательские или системные данные;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="ca8f2-378">применить граффити к открытому интерфейсу.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="ca8f2-379">Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="ca8f2-380">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="ca8f2-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="ca8f2-381">Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей</span><span class="sxs-lookup"><span data-stu-id="ca8f2-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="ca8f2-382">Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="ca8f2-383">Сценарии использования хранилища</span><span class="sxs-lookup"><span data-stu-id="ca8f2-383">Storage scenarios</span></span>

<span data-ttu-id="ca8f2-384">К общим вариантам хранилища файлов относятся следующие:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-384">Common storage options for files include:</span></span>

* <span data-ttu-id="ca8f2-385">База данных</span><span class="sxs-lookup"><span data-stu-id="ca8f2-385">Database</span></span>

  * <span data-ttu-id="ca8f2-386">В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="ca8f2-387">База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="ca8f2-388">Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="ca8f2-389">Физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="ca8f2-390">Обработка передачи больших файлов:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-390">For large file uploads:</span></span>
    * <span data-ttu-id="ca8f2-391">Ограничения базы данных могут ограничивать размер передачи.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="ca8f2-392">Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="ca8f2-393">Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="ca8f2-394">Процесс приложения должен иметь разрешения на чтение и запись для места хранения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="ca8f2-395">**Никогда не предоставляйте разрешение на выполнение.**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="ca8f2-396">Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="ca8f2-397">Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="ca8f2-398">Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="ca8f2-399">Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="ca8f2-400">В этом разделе показан метод <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, но метод <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> можно использовать для сохранения <xref:System.IO.FileStream> в хранилище BLOB-объектов при работе с <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="ca8f2-401">Сценарии передачи файлов</span><span class="sxs-lookup"><span data-stu-id="ca8f2-401">File upload scenarios</span></span>

<span data-ttu-id="ca8f2-402">Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="ca8f2-403">**ответов**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-403">**Buffering**</span></span>

<span data-ttu-id="ca8f2-404">Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="ca8f2-405">Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="ca8f2-406">При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="ca8f2-407">Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="ca8f2-408">Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="ca8f2-409">Буферизация небольших файлов описана в следующих разделах этой статьи:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="ca8f2-410">Физическое хранилище</span><span class="sxs-lookup"><span data-stu-id="ca8f2-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="ca8f2-411">База данных</span><span class="sxs-lookup"><span data-stu-id="ca8f2-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="ca8f2-412">**Потоковая передача**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-412">**Streaming**</span></span>

<span data-ttu-id="ca8f2-413">Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="ca8f2-414">Потоковая передача повышает производительность не значительно.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="ca8f2-415">При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="ca8f2-416">Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="ca8f2-417">Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу</span><span class="sxs-lookup"><span data-stu-id="ca8f2-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="ca8f2-418">Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="ca8f2-419">В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):</span><span class="sxs-lookup"><span data-stu-id="ca8f2-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="ca8f2-420">Следующий пример аналогичен предыдущему примеру, за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="ca8f2-421">JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="ca8f2-422">Проверка не выполняется.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-422">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="ca8f2-423">Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="ca8f2-424">Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="ca8f2-425">Используйте команду `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="ca8f2-426">Пример:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-426">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="ca8f2-427">Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="ca8f2-428">Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="ca8f2-429">Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="ca8f2-430">В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="ca8f2-431">**Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="ca8f2-432">При отображении или ведении журнала кодируйте имя файла в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="ca8f2-433">Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="ca8f2-434">Приложения должны:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-434">Applications should:</span></span>
>
> * <span data-ttu-id="ca8f2-435">удалить путь из имени файла, указываемого пользователем;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="ca8f2-436">сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="ca8f2-437">создать случайное имя файла для хранения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="ca8f2-438">Следующий код удаляет путь из имени файла:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="ca8f2-439">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="ca8f2-440">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ca8f2-441">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ca8f2-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ca8f2-442">Проверка</span><span class="sxs-lookup"><span data-stu-id="ca8f2-442">Validation</span></span>](#validation)

<span data-ttu-id="ca8f2-443">При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="ca8f2-444">Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="ca8f2-445">Любая из следующих коллекций, представляющих несколько файлов:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="ca8f2-446">[Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="ca8f2-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="ca8f2-447">Привязка сопоставляет файлы форм по имени.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-447">Binding matches form files by name.</span></span> <span data-ttu-id="ca8f2-448">Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="ca8f2-449">Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="ca8f2-450">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-450">The following example:</span></span>

* <span data-ttu-id="ca8f2-451">Циклично отправляет один или несколько передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="ca8f2-452">Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="ca8f2-453">Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="ca8f2-454">Возвращает общее число и размер отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-454">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="ca8f2-455">Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="ca8f2-456">В следующем примере путь получен из конфигурации:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-456">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="ca8f2-457">Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="ca8f2-458">Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="ca8f2-459">Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="ca8f2-460">Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="ca8f2-461">Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="ca8f2-462">Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="ca8f2-463">Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="ca8f2-464">Ограничение в 65 535 файлов предусмотрено для каждого сервера.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="ca8f2-465">Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * <span data-ttu-id="ca8f2-466">[Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);</span><span class="sxs-lookup"><span data-stu-id="ca8f2-466">[GetTempFileNameA function](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)</span></span>
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="ca8f2-467">Передача небольших файлов с привязкой буферизованной модели к базе данных</span><span class="sxs-lookup"><span data-stu-id="ca8f2-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="ca8f2-468">Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="ca8f2-469">Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="ca8f2-470"><xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="ca8f2-471">В предыдущем примере используется свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="ca8f2-472">`FileUpload`Используется в Razor форме страниц:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-472">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="ca8f2-473">При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="ca8f2-474">В следующем примере `_dbContext` сохраняет контекст базы данных приложения:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-474">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="ca8f2-475">Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="ca8f2-476">*Pages/BufferedSingleFileUploadDb.cshtml*;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="ca8f2-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="ca8f2-478">При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="ca8f2-479">Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="ca8f2-480">Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="ca8f2-481">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="ca8f2-482">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ca8f2-483">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="ca8f2-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ca8f2-484">Проверка</span><span class="sxs-lookup"><span data-stu-id="ca8f2-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="ca8f2-485">Передача больших файлов с помощью потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="ca8f2-485">Upload large files with streaming</span></span>

<span data-ttu-id="ca8f2-486">В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="ca8f2-487">Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="ca8f2-488">Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="ca8f2-489">Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="ca8f2-490">После считывания составных разделов действие выполняет собственную привязку модели.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="ca8f2-491">Начальный ответ страницы загружает форму и сохраняет токен подделки в cookie (с помощью `GenerateAntiforgeryTokenCookieAttribute` атрибута).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="ca8f2-492">Для установки с помощью маркера запроса атрибут использует встроенную поддержку защиты от [подделки](xref:security/anti-request-forgery) ASP.NET Core cookie .</span><span class="sxs-lookup"><span data-stu-id="ca8f2-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="ca8f2-493">Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="ca8f2-494">В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="ca8f2-495">Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="ca8f2-496">Метод действия работает напрямую со свойством `Request`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="ca8f2-497">Для считывания каждого раздела служит объект `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="ca8f2-498">Данные "ключ — значение" хранятся в `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="ca8f2-499">После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="ca8f2-500">Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="ca8f2-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="ca8f2-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="ca8f2-502">Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="ca8f2-503">В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="ca8f2-504">Проверка</span><span class="sxs-lookup"><span data-stu-id="ca8f2-504">Validation</span></span>

<span data-ttu-id="ca8f2-505">В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="ca8f2-506">Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="ca8f2-507">Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="ca8f2-508">Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="ca8f2-509">В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="ca8f2-510">Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="ca8f2-511">Вы полностью разбираетесь в реализации.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="ca8f2-512">Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="ca8f2-513">**Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="ca8f2-514">Проверка содержимого</span><span class="sxs-lookup"><span data-stu-id="ca8f2-514">Content validation</span></span>

<span data-ttu-id="ca8f2-515">**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**</span><span class="sxs-lookup"><span data-stu-id="ca8f2-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="ca8f2-516">Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="ca8f2-517">Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="ca8f2-518">Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="ca8f2-519">При передаче файл перемещается в нормальное место хранения файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="ca8f2-520">Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="ca8f2-521">Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="ca8f2-522">Проверка расширения файла</span><span class="sxs-lookup"><span data-stu-id="ca8f2-522">File extension validation</span></span>

<span data-ttu-id="ca8f2-523">Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="ca8f2-524">Пример:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="ca8f2-525">Проверка подписи файла</span><span class="sxs-lookup"><span data-stu-id="ca8f2-525">File signature validation</span></span>

<span data-ttu-id="ca8f2-526">Подпись файла определяется по первым нескольким байтам в начале файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="ca8f2-527">Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="ca8f2-528">Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="ca8f2-529">В следующем примере проверяется подпись файла для изображения JPEG:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="ca8f2-530">Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="ca8f2-531">Безопасность имени файла</span><span class="sxs-lookup"><span data-stu-id="ca8f2-531">File name security</span></span>

<span data-ttu-id="ca8f2-532">Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="ca8f2-533">Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="ca8f2-534">Razor автоматически кодирует значения свойств для вывода.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="ca8f2-535">Ниже приведен безопасный код, который можно использовать.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="ca8f2-536">За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="ca8f2-537">Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="ca8f2-538">Предоставьте дополнительную логику для соответствия спецификациям приложения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="ca8f2-539">Проверка размера</span><span class="sxs-lookup"><span data-stu-id="ca8f2-539">Size validation</span></span>

<span data-ttu-id="ca8f2-540">Ограничьте размер передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="ca8f2-541">В примере приложения размер файла ограничен 2 МБ (указывается в байтах).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="ca8f2-542">Ограничение предоставляется с помощью [конфигурации](xref:fundamentals/configuration/index) из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="ca8f2-543">В классы `PageModel` внедряется `FileSizeLimit`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="ca8f2-544">Если размер файла превышает ограничение, файл отклоняется:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="ca8f2-545">Сопоставление значения атрибута имени и имени параметра метода POST</span><span class="sxs-lookup"><span data-stu-id="ca8f2-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="ca8f2-546">В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="ca8f2-547">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-547">In the following example:</span></span>

* <span data-ttu-id="ca8f2-548">При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="ca8f2-549">При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="ca8f2-550">Используйте соответствующее имя для параметра метода C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="ca8f2-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="ca8f2-551">Для Razor метода обработчика страницы страниц с именем `Upload` :</span><span class="sxs-lookup"><span data-stu-id="ca8f2-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="ca8f2-552">Для метода действия контроллера POST приложения MVC:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="ca8f2-553">Конфигурация сервера и приложения</span><span class="sxs-lookup"><span data-stu-id="ca8f2-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="ca8f2-554">Ограничение длины составного текста</span><span class="sxs-lookup"><span data-stu-id="ca8f2-554">Multipart body length limit</span></span>

<span data-ttu-id="ca8f2-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="ca8f2-556">Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="ca8f2-557">Значение по умолчанию — 134 217 728 байт (128 МБ).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="ca8f2-558">Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="ca8f2-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="ca8f2-560">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ca8f2-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="ca8f2-561">В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="ca8f2-562">Максимальный размер текста запроса Kestrel</span><span class="sxs-lookup"><span data-stu-id="ca8f2-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="ca8f2-563">Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ca8f2-564">Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size):</span><span class="sxs-lookup"><span data-stu-id="ca8f2-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="ca8f2-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="ca8f2-566">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ca8f2-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="ca8f2-567">В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="ca8f2-568">Другие ограничения Kestrel</span><span class="sxs-lookup"><span data-stu-id="ca8f2-568">Other Kestrel limits</span></span>

<span data-ttu-id="ca8f2-569">Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="ca8f2-570">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="ca8f2-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="ca8f2-571">Скорость передачи данных запросов и ответов</span><span class="sxs-lookup"><span data-stu-id="ca8f2-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="ca8f2-572">Ограничение длины содержимого IIS</span><span class="sxs-lookup"><span data-stu-id="ca8f2-572">IIS content length limit</span></span>

<span data-ttu-id="ca8f2-573">По умолчанию ограничение запроса (`maxAllowedContentLength`) составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="ca8f2-574">Ограничение можно настроить в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-574">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="ca8f2-575">Этот параметр применим только к службам IIS.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-575">This setting only applies to IIS.</span></span> <span data-ttu-id="ca8f2-576">При размещении в Kestrel такой проблемы возникать не должно.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="ca8f2-577">Дополнительные сведения см. в разделе [ограничения \<requestLimits> запросов ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="ca8f2-578">Ограничения в модуле ASP.NET Core или наличие модуля фильтрации запросов IIS могут ограничить передачу до 2 или 4 ГБ.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="ca8f2-579">Дополнительные сведения см. в статье форума (dotnet/AspNetCore #2711) [Unable to upload file greater than 2GB in size](https://github.com/dotnet/AspNetCore/issues/2711) (Не удалось отправить файл размером более 2 ГБ).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ca8f2-580">Диагностика</span><span class="sxs-lookup"><span data-stu-id="ca8f2-580">Troubleshoot</span></span>

<span data-ttu-id="ca8f2-581">Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="ca8f2-582">Ошибка "Не найдено" при развертывании на сервере IIS</span><span class="sxs-lookup"><span data-stu-id="ca8f2-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="ca8f2-583">Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:</span><span class="sxs-lookup"><span data-stu-id="ca8f2-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="ca8f2-584">Дополнительные сведения об увеличении предела см. в разделе [Ограничение длины содержимого IIS](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="ca8f2-585">Сбой подключения</span><span class="sxs-lookup"><span data-stu-id="ca8f2-585">Connection failure</span></span>

<span data-ttu-id="ca8f2-586">Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="ca8f2-587">Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="ca8f2-588">Может также потребоваться настроить ограничения подключений клиентов Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="ca8f2-589">Исключение, связанное с пустой ссылкой в IFormFile</span><span class="sxs-lookup"><span data-stu-id="ca8f2-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="ca8f2-590">Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="ca8f2-591">Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="ca8f2-592">Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ca8f2-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="ca8f2-593">Поток слишком длинный</span><span class="sxs-lookup"><span data-stu-id="ca8f2-593">Stream was too long</span></span>

<span data-ttu-id="ca8f2-594">В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="ca8f2-595">Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="ca8f2-596">Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.</span><span class="sxs-lookup"><span data-stu-id="ca8f2-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="ca8f2-597">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ca8f2-597">Additional resources</span></span>

* [<span data-ttu-id="ca8f2-598">Сток запросов HTTP-подключения</span><span class="sxs-lookup"><span data-stu-id="ca8f2-598">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="ca8f2-599">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="ca8f2-599">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="ca8f2-600">Безопасность Azure: кадр безопасности: Проверка входных данных | Способы их устранения</span><span class="sxs-lookup"><span data-stu-id="ca8f2-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="ca8f2-601">Шаблоны разработки в облаке Azure: шаблон камердинера Key</span><span class="sxs-lookup"><span data-stu-id="ca8f2-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
