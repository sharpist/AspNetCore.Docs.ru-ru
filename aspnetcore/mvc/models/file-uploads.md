---
title: Передача файлов в ASP.NET Core
author: rick-anderson
description: Сведения об использовании привязки модели и потоковой передачи для передачи файлов в ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/file-uploads
ms.openlocfilehash: 632cc9fafc5daf2923997f0113adee52491acdcc
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "83838322"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="a1feb-103">Передача файлов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1feb-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="a1feb-104">По [Стив Смит](https://ardalis.com/) и [рутжер](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="a1feb-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a1feb-105">Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="a1feb-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a1feb-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="a1feb-107">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="a1feb-107">Security considerations</span></span>

<span data-ttu-id="a1feb-108">Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер.</span><span class="sxs-lookup"><span data-stu-id="a1feb-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="a1feb-109">Злоумышленники могут попытаться:</span><span class="sxs-lookup"><span data-stu-id="a1feb-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="a1feb-110">Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="a1feb-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="a1feb-111">Передать вирусы и вредоносные программы.</span><span class="sxs-lookup"><span data-stu-id="a1feb-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="a1feb-112">Нарушить безопасность сетей и серверов другими способами.</span><span class="sxs-lookup"><span data-stu-id="a1feb-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="a1feb-113">Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.</span><span class="sxs-lookup"><span data-stu-id="a1feb-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="a1feb-114">Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск.</span><span class="sxs-lookup"><span data-stu-id="a1feb-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="a1feb-115">Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам.</span><span class="sxs-lookup"><span data-stu-id="a1feb-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="a1feb-116">Отключите разрешения на выполнение для расположения отправки файла.&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1feb-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="a1feb-117">**Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1feb-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="a1feb-118">Используйте безопасное имя файла, определяемое приложением.</span><span class="sxs-lookup"><span data-stu-id="a1feb-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="a1feb-119">Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении.</span><span class="sxs-lookup"><span data-stu-id="a1feb-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="a1feb-120">Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).</span><span class="sxs-lookup"><span data-stu-id="a1feb-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="a1feb-121">Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1feb-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="a1feb-122">Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.</span><span class="sxs-lookup"><span data-stu-id="a1feb-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="a1feb-123">Проверьте размер отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="a1feb-124">Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1feb-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="a1feb-125">Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.</span><span class="sxs-lookup"><span data-stu-id="a1feb-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="a1feb-126">**Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**</span><span class="sxs-lookup"><span data-stu-id="a1feb-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="a1feb-127">&dagger;Пример приложения демонстрирует подход, который соответствует критериям.</span><span class="sxs-lookup"><span data-stu-id="a1feb-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="a1feb-128">Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:</span><span class="sxs-lookup"><span data-stu-id="a1feb-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="a1feb-129">полностью получить контроль над системой;</span><span class="sxs-lookup"><span data-stu-id="a1feb-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="a1feb-130">перезагрузить систему так, что она окажется в неработоспособном состоянии;</span><span class="sxs-lookup"><span data-stu-id="a1feb-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="a1feb-131">скомпрометировать пользовательские или системные данные;</span><span class="sxs-lookup"><span data-stu-id="a1feb-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="a1feb-132">применить граффити к открытому интерфейсу.</span><span class="sxs-lookup"><span data-stu-id="a1feb-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="a1feb-133">Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="a1feb-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="a1feb-134">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="a1feb-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="a1feb-135">Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей</span><span class="sxs-lookup"><span data-stu-id="a1feb-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="a1feb-136">Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).</span><span class="sxs-lookup"><span data-stu-id="a1feb-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="a1feb-137">Сценарии использования хранилища</span><span class="sxs-lookup"><span data-stu-id="a1feb-137">Storage scenarios</span></span>

<span data-ttu-id="a1feb-138">К общим вариантам хранилища файлов относятся следующие:</span><span class="sxs-lookup"><span data-stu-id="a1feb-138">Common storage options for files include:</span></span>

* <span data-ttu-id="a1feb-139">База данных</span><span class="sxs-lookup"><span data-stu-id="a1feb-139">Database</span></span>

  * <span data-ttu-id="a1feb-140">В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="a1feb-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="a1feb-141">База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).</span><span class="sxs-lookup"><span data-stu-id="a1feb-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="a1feb-142">Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="a1feb-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="a1feb-143">Физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="a1feb-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="a1feb-144">Обработка передачи больших файлов:</span><span class="sxs-lookup"><span data-stu-id="a1feb-144">For large file uploads:</span></span>
    * <span data-ttu-id="a1feb-145">Ограничения базы данных могут ограничивать размер передачи.</span><span class="sxs-lookup"><span data-stu-id="a1feb-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="a1feb-146">Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.</span><span class="sxs-lookup"><span data-stu-id="a1feb-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="a1feb-147">Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="a1feb-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="a1feb-148">Процесс приложения должен иметь разрешения на чтение и запись для места хранения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="a1feb-149">**Никогда не предоставляйте разрешение на выполнение.**</span><span class="sxs-lookup"><span data-stu-id="a1feb-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="a1feb-150">Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).</span><span class="sxs-lookup"><span data-stu-id="a1feb-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="a1feb-151">Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.</span><span class="sxs-lookup"><span data-stu-id="a1feb-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="a1feb-152">Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="a1feb-153">Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="a1feb-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="a1feb-154">Сценарии передачи файлов</span><span class="sxs-lookup"><span data-stu-id="a1feb-154">File upload scenarios</span></span>

<span data-ttu-id="a1feb-155">Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.</span><span class="sxs-lookup"><span data-stu-id="a1feb-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="a1feb-156">**ответов**</span><span class="sxs-lookup"><span data-stu-id="a1feb-156">**Buffering**</span></span>

<span data-ttu-id="a1feb-157">Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).</span><span class="sxs-lookup"><span data-stu-id="a1feb-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="a1feb-158">Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="a1feb-159">При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске.</span><span class="sxs-lookup"><span data-stu-id="a1feb-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="a1feb-160">Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="a1feb-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="a1feb-161">Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.</span><span class="sxs-lookup"><span data-stu-id="a1feb-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="a1feb-162">Буферизация небольших файлов описана в следующих разделах этой статьи:</span><span class="sxs-lookup"><span data-stu-id="a1feb-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="a1feb-163">Физическое хранилище</span><span class="sxs-lookup"><span data-stu-id="a1feb-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="a1feb-164">База данных</span><span class="sxs-lookup"><span data-stu-id="a1feb-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="a1feb-165">**Потоковая передача**</span><span class="sxs-lookup"><span data-stu-id="a1feb-165">**Streaming**</span></span>

<span data-ttu-id="a1feb-166">Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую.</span><span class="sxs-lookup"><span data-stu-id="a1feb-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="a1feb-167">Потоковая передача повышает производительность не значительно.</span><span class="sxs-lookup"><span data-stu-id="a1feb-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="a1feb-168">При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.</span><span class="sxs-lookup"><span data-stu-id="a1feb-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="a1feb-169">Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="a1feb-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="a1feb-170">Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу</span><span class="sxs-lookup"><span data-stu-id="a1feb-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="a1feb-171">Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a1feb-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="a1feb-172">В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):</span><span class="sxs-lookup"><span data-stu-id="a1feb-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="a1feb-173">Следующий пример аналогичен предыдущему примеру, за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="a1feb-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="a1feb-174">JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.</span><span class="sxs-lookup"><span data-stu-id="a1feb-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="a1feb-175">Проверка не выполняется.</span><span class="sxs-lookup"><span data-stu-id="a1feb-175">There's no validation.</span></span>

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

<span data-ttu-id="a1feb-176">Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="a1feb-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="a1feb-177">Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="a1feb-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="a1feb-178">Используйте `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="a1feb-179">Пример.</span><span class="sxs-lookup"><span data-stu-id="a1feb-179">For example:</span></span>

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

<span data-ttu-id="a1feb-180">Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="a1feb-181">Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:</span><span class="sxs-lookup"><span data-stu-id="a1feb-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="a1feb-182">Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="a1feb-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="a1feb-183">В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.</span><span class="sxs-lookup"><span data-stu-id="a1feb-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="a1feb-184">**Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="a1feb-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="a1feb-185">При отображении или ведении журнала кодируйте имя файла в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="a1feb-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="a1feb-186">Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути.</span><span class="sxs-lookup"><span data-stu-id="a1feb-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="a1feb-187">Приложения должны:</span><span class="sxs-lookup"><span data-stu-id="a1feb-187">Applications should:</span></span>
>
> * <span data-ttu-id="a1feb-188">удалить путь из имени файла, указываемого пользователем;</span><span class="sxs-lookup"><span data-stu-id="a1feb-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="a1feb-189">сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="a1feb-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="a1feb-190">создать случайное имя файла для хранения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="a1feb-191">Следующий код удаляет путь из имени файла:</span><span class="sxs-lookup"><span data-stu-id="a1feb-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="a1feb-192">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="a1feb-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="a1feb-193">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="a1feb-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="a1feb-194">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="a1feb-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="a1feb-195">Проверка</span><span class="sxs-lookup"><span data-stu-id="a1feb-195">Validation</span></span>](#validation)

<span data-ttu-id="a1feb-196">При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="a1feb-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="a1feb-197">Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="a1feb-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="a1feb-198">Любая из следующих коллекций, представляющих несколько файлов:</span><span class="sxs-lookup"><span data-stu-id="a1feb-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="a1feb-199">[Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="a1feb-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="a1feb-200">Привязка сопоставляет файлы форм по имени.</span><span class="sxs-lookup"><span data-stu-id="a1feb-200">Binding matches form files by name.</span></span> <span data-ttu-id="a1feb-201">Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="a1feb-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="a1feb-202">Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="a1feb-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="a1feb-203">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="a1feb-203">The following example:</span></span>

* <span data-ttu-id="a1feb-204">Циклично отправляет один или несколько передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="a1feb-205">Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя.</span><span class="sxs-lookup"><span data-stu-id="a1feb-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="a1feb-206">Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.</span><span class="sxs-lookup"><span data-stu-id="a1feb-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="a1feb-207">Возвращает общее число и размер отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="a1feb-208">Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="a1feb-209">В следующем примере путь получен из конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a1feb-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="a1feb-210">Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="a1feb-211">Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="a1feb-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="a1feb-212">Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой.</span><span class="sxs-lookup"><span data-stu-id="a1feb-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="a1feb-213">Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="a1feb-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="a1feb-214">Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="a1feb-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="a1feb-215">Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="a1feb-216">Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="a1feb-217">Ограничение в 65 535 файлов предусмотрено для каждого сервера.</span><span class="sxs-lookup"><span data-stu-id="a1feb-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="a1feb-218">Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="a1feb-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * <span data-ttu-id="a1feb-219">[Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);</span><span class="sxs-lookup"><span data-stu-id="a1feb-219">[GetTempFileNameA function](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)</span></span>
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="a1feb-220">Передача небольших файлов с привязкой буферизованной модели к базе данных</span><span class="sxs-lookup"><span data-stu-id="a1feb-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="a1feb-221">Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="a1feb-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="a1feb-222">Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="a1feb-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="a1feb-223"><xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="a1feb-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="a1feb-224">В предыдущем примере используется свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="a1feb-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="a1feb-225">`FileUpload`Используется в Razor форме страниц:</span><span class="sxs-lookup"><span data-stu-id="a1feb-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="a1feb-226">При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="a1feb-227">В следующем примере `_dbContext` сохраняет контекст базы данных приложения:</span><span class="sxs-lookup"><span data-stu-id="a1feb-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="a1feb-228">Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:</span><span class="sxs-lookup"><span data-stu-id="a1feb-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="a1feb-229">*Pages/BufferedSingleFileUploadDb.cshtml*;</span><span class="sxs-lookup"><span data-stu-id="a1feb-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="a1feb-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="a1feb-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="a1feb-231">При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.</span><span class="sxs-lookup"><span data-stu-id="a1feb-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="a1feb-232">Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки.</span><span class="sxs-lookup"><span data-stu-id="a1feb-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="a1feb-233">Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.</span><span class="sxs-lookup"><span data-stu-id="a1feb-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="a1feb-234">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="a1feb-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="a1feb-235">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="a1feb-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="a1feb-236">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="a1feb-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="a1feb-237">Проверка</span><span class="sxs-lookup"><span data-stu-id="a1feb-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="a1feb-238">Передача больших файлов с помощью потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="a1feb-238">Upload large files with streaming</span></span>

<span data-ttu-id="a1feb-239">В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера.</span><span class="sxs-lookup"><span data-stu-id="a1feb-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="a1feb-240">Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса.</span><span class="sxs-lookup"><span data-stu-id="a1feb-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="a1feb-241">Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром.</span><span class="sxs-lookup"><span data-stu-id="a1feb-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="a1feb-242">Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое.</span><span class="sxs-lookup"><span data-stu-id="a1feb-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="a1feb-243">После считывания составных разделов действие выполняет собственную привязку модели.</span><span class="sxs-lookup"><span data-stu-id="a1feb-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="a1feb-244">Вначале страница загружает форму и сохраняет токен против подделки в файле cookie (с помощью атрибута `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="a1feb-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="a1feb-245">Атрибут использует встроенную поддержку [защиты от подделки](xref:security/anti-request-forgery) в ASP.NET Core, чтобы задать файл cookie с токеном запроса.</span><span class="sxs-lookup"><span data-stu-id="a1feb-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="a1feb-246">Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:</span><span class="sxs-lookup"><span data-stu-id="a1feb-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="a1feb-247">В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).</span><span class="sxs-lookup"><span data-stu-id="a1feb-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="a1feb-248">Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать).</span><span class="sxs-lookup"><span data-stu-id="a1feb-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="a1feb-249">Метод действия работает напрямую со свойством `Request`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="a1feb-250">Для считывания каждого раздела служит объект `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="a1feb-251">Данные "ключ — значение" хранятся в `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="a1feb-252">После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.</span><span class="sxs-lookup"><span data-stu-id="a1feb-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="a1feb-253">Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:</span><span class="sxs-lookup"><span data-stu-id="a1feb-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="a1feb-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="a1feb-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="a1feb-255">Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:</span><span class="sxs-lookup"><span data-stu-id="a1feb-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="a1feb-256">В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="a1feb-257">Проверка</span><span class="sxs-lookup"><span data-stu-id="a1feb-257">Validation</span></span>

<span data-ttu-id="a1feb-258">В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="a1feb-259">Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*.</span><span class="sxs-lookup"><span data-stu-id="a1feb-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="a1feb-260">Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.</span><span class="sxs-lookup"><span data-stu-id="a1feb-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="a1feb-261">Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="a1feb-262">В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.</span><span class="sxs-lookup"><span data-stu-id="a1feb-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="a1feb-263">Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:</span><span class="sxs-lookup"><span data-stu-id="a1feb-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="a1feb-264">Вы полностью разбираетесь в реализации.</span><span class="sxs-lookup"><span data-stu-id="a1feb-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="a1feb-265">Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="a1feb-266">**Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**</span><span class="sxs-lookup"><span data-stu-id="a1feb-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="a1feb-267">Проверка содержимого</span><span class="sxs-lookup"><span data-stu-id="a1feb-267">Content validation</span></span>

<span data-ttu-id="a1feb-268">**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**</span><span class="sxs-lookup"><span data-stu-id="a1feb-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="a1feb-269">Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных.</span><span class="sxs-lookup"><span data-stu-id="a1feb-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="a1feb-270">Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="a1feb-271">Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="a1feb-272">При передаче файл перемещается в нормальное место хранения файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="a1feb-273">Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="a1feb-274">Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.</span><span class="sxs-lookup"><span data-stu-id="a1feb-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="a1feb-275">Проверка расширения файла</span><span class="sxs-lookup"><span data-stu-id="a1feb-275">File extension validation</span></span>

<span data-ttu-id="a1feb-276">Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений.</span><span class="sxs-lookup"><span data-stu-id="a1feb-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="a1feb-277">Пример.</span><span class="sxs-lookup"><span data-stu-id="a1feb-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="a1feb-278">Проверка подписи файла</span><span class="sxs-lookup"><span data-stu-id="a1feb-278">File signature validation</span></span>

<span data-ttu-id="a1feb-279">Подпись файла определяется по первым нескольким байтам в начале файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="a1feb-280">Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="a1feb-281">Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="a1feb-282">В следующем примере проверяется подпись файла для изображения JPEG:</span><span class="sxs-lookup"><span data-stu-id="a1feb-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="a1feb-283">Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="a1feb-284">Безопасность имени файла</span><span class="sxs-lookup"><span data-stu-id="a1feb-284">File name security</span></span>

<span data-ttu-id="a1feb-285">Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом.</span><span class="sxs-lookup"><span data-stu-id="a1feb-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="a1feb-286">Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.</span><span class="sxs-lookup"><span data-stu-id="a1feb-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="a1feb-287">автоматически кодирует значения свойств для вывода.</span><span class="sxs-lookup"><span data-stu-id="a1feb-287"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="a1feb-288">Ниже приведен безопасный код, который можно использовать.</span><span class="sxs-lookup"><span data-stu-id="a1feb-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="a1feb-289">За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="a1feb-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="a1feb-290">Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем.</span><span class="sxs-lookup"><span data-stu-id="a1feb-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="a1feb-291">Предоставьте дополнительную логику для соответствия спецификациям приложения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="a1feb-292">Проверка размера</span><span class="sxs-lookup"><span data-stu-id="a1feb-292">Size validation</span></span>

<span data-ttu-id="a1feb-293">Ограничьте размер передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="a1feb-294">В примере приложения размер файла ограничен 2 МБ (указывается в байтах).</span><span class="sxs-lookup"><span data-stu-id="a1feb-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="a1feb-295">Ограничение предоставляется с помощью [конфигурации](xref:fundamentals/configuration/index) из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a1feb-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="a1feb-296">В классы `PageModel` внедряется `FileSizeLimit`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="a1feb-297">Если размер файла превышает ограничение, файл отклоняется:</span><span class="sxs-lookup"><span data-stu-id="a1feb-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="a1feb-298">Сопоставление значения атрибута имени и имени параметра метода POST</span><span class="sxs-lookup"><span data-stu-id="a1feb-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="a1feb-299">В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="a1feb-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="a1feb-300">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="a1feb-300">In the following example:</span></span>

* <span data-ttu-id="a1feb-301">При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="a1feb-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="a1feb-302">При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="a1feb-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="a1feb-303">Используйте соответствующее имя для параметра метода C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="a1feb-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="a1feb-304">Для Razor метода обработчика страницы страниц с именем `Upload` :</span><span class="sxs-lookup"><span data-stu-id="a1feb-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="a1feb-305">Для метода действия контроллера POST приложения MVC:</span><span class="sxs-lookup"><span data-stu-id="a1feb-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="a1feb-306">Конфигурация сервера и приложения</span><span class="sxs-lookup"><span data-stu-id="a1feb-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="a1feb-307">Ограничение длины составного текста</span><span class="sxs-lookup"><span data-stu-id="a1feb-307">Multipart body length limit</span></span>

<span data-ttu-id="a1feb-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста.</span><span class="sxs-lookup"><span data-stu-id="a1feb-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="a1feb-309">Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе.</span><span class="sxs-lookup"><span data-stu-id="a1feb-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="a1feb-310">Значение по умолчанию — 134 217 728 байт (128 МБ).</span><span class="sxs-lookup"><span data-stu-id="a1feb-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="a1feb-311">Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a1feb-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1feb-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="a1feb-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="a1feb-313">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a1feb-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
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
    });
```

<span data-ttu-id="a1feb-314">В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="a1feb-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="a1feb-315">Максимальный размер текста запроса Kestrel</span><span class="sxs-lookup"><span data-stu-id="a1feb-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="a1feb-316">Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="a1feb-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="a1feb-317">Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size):</span><span class="sxs-lookup"><span data-stu-id="a1feb-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="a1feb-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="a1feb-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="a1feb-319">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a1feb-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
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
    });
```

<span data-ttu-id="a1feb-320">В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="a1feb-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="a1feb-321">`RequestSizeLimitAttribute`Можно также применить с помощью [`@attribute`](xref:mvc/views/razor#attribute) Razor директивы:</span><span class="sxs-lookup"><span data-stu-id="a1feb-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="a1feb-322">Другие ограничения Kestrel</span><span class="sxs-lookup"><span data-stu-id="a1feb-322">Other Kestrel limits</span></span>

<span data-ttu-id="a1feb-323">Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:</span><span class="sxs-lookup"><span data-stu-id="a1feb-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="a1feb-324">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="a1feb-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="a1feb-325">Скорость передачи данных запросов и ответов</span><span class="sxs-lookup"><span data-stu-id="a1feb-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="a1feb-326">Ограничение длины содержимого IIS</span><span class="sxs-lookup"><span data-stu-id="a1feb-326">IIS content length limit</span></span>

<span data-ttu-id="a1feb-327">По умолчанию ограничение запроса (`maxAllowedContentLength`) составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="a1feb-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="a1feb-328">Ограничение можно настроить в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="a1feb-328">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="a1feb-329">Этот параметр применим только к службам IIS.</span><span class="sxs-lookup"><span data-stu-id="a1feb-329">This setting only applies to IIS.</span></span> <span data-ttu-id="a1feb-330">При размещении в Kestrel такой проблемы возникать не должно.</span><span class="sxs-lookup"><span data-stu-id="a1feb-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="a1feb-331">Дополнительные сведения см. в разделе [ограничения \<requestLimits> запросов ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="a1feb-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="a1feb-332">Ограничения в модуле ASP.NET Core или наличие модуля фильтрации запросов IIS могут ограничить передачу до 2 или 4 ГБ.</span><span class="sxs-lookup"><span data-stu-id="a1feb-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="a1feb-333">Дополнительные сведения см. в статье форума (dotnet/AspNetCore #2711) [Unable to upload file greater than 2GB in size](https://github.com/dotnet/AspNetCore/issues/2711) (Не удалось отправить файл размером более 2 ГБ).</span><span class="sxs-lookup"><span data-stu-id="a1feb-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a1feb-334">Диагностика</span><span class="sxs-lookup"><span data-stu-id="a1feb-334">Troubleshoot</span></span>

<span data-ttu-id="a1feb-335">Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="a1feb-336">Ошибка "Не найдено" при развертывании на сервере IIS</span><span class="sxs-lookup"><span data-stu-id="a1feb-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="a1feb-337">Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:</span><span class="sxs-lookup"><span data-stu-id="a1feb-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="a1feb-338">Дополнительные сведения об увеличении предела см. в разделе [Ограничение длины содержимого IIS](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="a1feb-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="a1feb-339">Сбой подключения</span><span class="sxs-lookup"><span data-stu-id="a1feb-339">Connection failure</span></span>

<span data-ttu-id="a1feb-340">Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a1feb-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="a1feb-341">Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="a1feb-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="a1feb-342">Может также потребоваться настроить ограничения подключений клиентов Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a1feb-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="a1feb-343">Исключение, связанное с пустой ссылкой в IFormFile</span><span class="sxs-lookup"><span data-stu-id="a1feb-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="a1feb-344">Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="a1feb-345">Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="a1feb-346">Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="a1feb-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="a1feb-347">Поток слишком длинный</span><span class="sxs-lookup"><span data-stu-id="a1feb-347">Stream was too long</span></span>

<span data-ttu-id="a1feb-348">В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="a1feb-349">Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="a1feb-350">Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a1feb-351">Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="a1feb-352">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a1feb-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="a1feb-353">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="a1feb-353">Security considerations</span></span>

<span data-ttu-id="a1feb-354">Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер.</span><span class="sxs-lookup"><span data-stu-id="a1feb-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="a1feb-355">Злоумышленники могут попытаться:</span><span class="sxs-lookup"><span data-stu-id="a1feb-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="a1feb-356">Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="a1feb-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="a1feb-357">Передать вирусы и вредоносные программы.</span><span class="sxs-lookup"><span data-stu-id="a1feb-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="a1feb-358">Нарушить безопасность сетей и серверов другими способами.</span><span class="sxs-lookup"><span data-stu-id="a1feb-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="a1feb-359">Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.</span><span class="sxs-lookup"><span data-stu-id="a1feb-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="a1feb-360">Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск.</span><span class="sxs-lookup"><span data-stu-id="a1feb-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="a1feb-361">Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам.</span><span class="sxs-lookup"><span data-stu-id="a1feb-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="a1feb-362">Отключите разрешения на выполнение для расположения отправки файла.&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1feb-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="a1feb-363">**Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1feb-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="a1feb-364">Используйте безопасное имя файла, определяемое приложением.</span><span class="sxs-lookup"><span data-stu-id="a1feb-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="a1feb-365">Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении.</span><span class="sxs-lookup"><span data-stu-id="a1feb-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="a1feb-366">Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).</span><span class="sxs-lookup"><span data-stu-id="a1feb-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="a1feb-367">Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1feb-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="a1feb-368">Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.</span><span class="sxs-lookup"><span data-stu-id="a1feb-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="a1feb-369">Проверьте размер отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="a1feb-370">Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1feb-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="a1feb-371">Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.</span><span class="sxs-lookup"><span data-stu-id="a1feb-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="a1feb-372">**Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**</span><span class="sxs-lookup"><span data-stu-id="a1feb-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="a1feb-373">&dagger;Пример приложения демонстрирует подход, который соответствует критериям.</span><span class="sxs-lookup"><span data-stu-id="a1feb-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="a1feb-374">Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:</span><span class="sxs-lookup"><span data-stu-id="a1feb-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="a1feb-375">полностью получить контроль над системой;</span><span class="sxs-lookup"><span data-stu-id="a1feb-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="a1feb-376">перезагрузить систему так, что она окажется в неработоспособном состоянии;</span><span class="sxs-lookup"><span data-stu-id="a1feb-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="a1feb-377">скомпрометировать пользовательские или системные данные;</span><span class="sxs-lookup"><span data-stu-id="a1feb-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="a1feb-378">применить граффити к открытому интерфейсу.</span><span class="sxs-lookup"><span data-stu-id="a1feb-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="a1feb-379">Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="a1feb-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="a1feb-380">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="a1feb-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="a1feb-381">Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей</span><span class="sxs-lookup"><span data-stu-id="a1feb-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="a1feb-382">Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).</span><span class="sxs-lookup"><span data-stu-id="a1feb-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="a1feb-383">Сценарии использования хранилища</span><span class="sxs-lookup"><span data-stu-id="a1feb-383">Storage scenarios</span></span>

<span data-ttu-id="a1feb-384">К общим вариантам хранилища файлов относятся следующие:</span><span class="sxs-lookup"><span data-stu-id="a1feb-384">Common storage options for files include:</span></span>

* <span data-ttu-id="a1feb-385">База данных</span><span class="sxs-lookup"><span data-stu-id="a1feb-385">Database</span></span>

  * <span data-ttu-id="a1feb-386">В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="a1feb-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="a1feb-387">База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).</span><span class="sxs-lookup"><span data-stu-id="a1feb-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="a1feb-388">Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="a1feb-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="a1feb-389">Физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="a1feb-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="a1feb-390">Обработка передачи больших файлов:</span><span class="sxs-lookup"><span data-stu-id="a1feb-390">For large file uploads:</span></span>
    * <span data-ttu-id="a1feb-391">Ограничения базы данных могут ограничивать размер передачи.</span><span class="sxs-lookup"><span data-stu-id="a1feb-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="a1feb-392">Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.</span><span class="sxs-lookup"><span data-stu-id="a1feb-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="a1feb-393">Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="a1feb-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="a1feb-394">Процесс приложения должен иметь разрешения на чтение и запись для места хранения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="a1feb-395">**Никогда не предоставляйте разрешение на выполнение.**</span><span class="sxs-lookup"><span data-stu-id="a1feb-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="a1feb-396">Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).</span><span class="sxs-lookup"><span data-stu-id="a1feb-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="a1feb-397">Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.</span><span class="sxs-lookup"><span data-stu-id="a1feb-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="a1feb-398">Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="a1feb-399">Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="a1feb-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="a1feb-400">В этом разделе показан метод <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, но метод <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> можно использовать для сохранения <xref:System.IO.FileStream> в хранилище BLOB-объектов при работе с <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="a1feb-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="a1feb-401">Сценарии передачи файлов</span><span class="sxs-lookup"><span data-stu-id="a1feb-401">File upload scenarios</span></span>

<span data-ttu-id="a1feb-402">Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.</span><span class="sxs-lookup"><span data-stu-id="a1feb-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="a1feb-403">**ответов**</span><span class="sxs-lookup"><span data-stu-id="a1feb-403">**Buffering**</span></span>

<span data-ttu-id="a1feb-404">Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).</span><span class="sxs-lookup"><span data-stu-id="a1feb-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="a1feb-405">Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="a1feb-406">При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске.</span><span class="sxs-lookup"><span data-stu-id="a1feb-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="a1feb-407">Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="a1feb-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="a1feb-408">Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.</span><span class="sxs-lookup"><span data-stu-id="a1feb-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="a1feb-409">Буферизация небольших файлов описана в следующих разделах этой статьи:</span><span class="sxs-lookup"><span data-stu-id="a1feb-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="a1feb-410">Физическое хранилище</span><span class="sxs-lookup"><span data-stu-id="a1feb-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="a1feb-411">База данных</span><span class="sxs-lookup"><span data-stu-id="a1feb-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="a1feb-412">**Потоковая передача**</span><span class="sxs-lookup"><span data-stu-id="a1feb-412">**Streaming**</span></span>

<span data-ttu-id="a1feb-413">Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую.</span><span class="sxs-lookup"><span data-stu-id="a1feb-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="a1feb-414">Потоковая передача повышает производительность не значительно.</span><span class="sxs-lookup"><span data-stu-id="a1feb-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="a1feb-415">При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.</span><span class="sxs-lookup"><span data-stu-id="a1feb-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="a1feb-416">Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="a1feb-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="a1feb-417">Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу</span><span class="sxs-lookup"><span data-stu-id="a1feb-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="a1feb-418">Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a1feb-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="a1feb-419">В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):</span><span class="sxs-lookup"><span data-stu-id="a1feb-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="a1feb-420">Следующий пример аналогичен предыдущему примеру, за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="a1feb-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="a1feb-421">JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.</span><span class="sxs-lookup"><span data-stu-id="a1feb-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="a1feb-422">Проверка не выполняется.</span><span class="sxs-lookup"><span data-stu-id="a1feb-422">There's no validation.</span></span>

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

<span data-ttu-id="a1feb-423">Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="a1feb-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="a1feb-424">Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="a1feb-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="a1feb-425">Используйте `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="a1feb-426">Пример.</span><span class="sxs-lookup"><span data-stu-id="a1feb-426">For example:</span></span>

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

<span data-ttu-id="a1feb-427">Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="a1feb-428">Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:</span><span class="sxs-lookup"><span data-stu-id="a1feb-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="a1feb-429">Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="a1feb-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="a1feb-430">В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.</span><span class="sxs-lookup"><span data-stu-id="a1feb-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="a1feb-431">**Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="a1feb-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="a1feb-432">При отображении или ведении журнала кодируйте имя файла в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="a1feb-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="a1feb-433">Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути.</span><span class="sxs-lookup"><span data-stu-id="a1feb-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="a1feb-434">Приложения должны:</span><span class="sxs-lookup"><span data-stu-id="a1feb-434">Applications should:</span></span>
>
> * <span data-ttu-id="a1feb-435">удалить путь из имени файла, указываемого пользователем;</span><span class="sxs-lookup"><span data-stu-id="a1feb-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="a1feb-436">сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="a1feb-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="a1feb-437">создать случайное имя файла для хранения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="a1feb-438">Следующий код удаляет путь из имени файла:</span><span class="sxs-lookup"><span data-stu-id="a1feb-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="a1feb-439">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="a1feb-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="a1feb-440">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="a1feb-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="a1feb-441">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="a1feb-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="a1feb-442">Проверка</span><span class="sxs-lookup"><span data-stu-id="a1feb-442">Validation</span></span>](#validation)

<span data-ttu-id="a1feb-443">При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="a1feb-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="a1feb-444">Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="a1feb-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="a1feb-445">Любая из следующих коллекций, представляющих несколько файлов:</span><span class="sxs-lookup"><span data-stu-id="a1feb-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="a1feb-446">[Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="a1feb-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="a1feb-447">Привязка сопоставляет файлы форм по имени.</span><span class="sxs-lookup"><span data-stu-id="a1feb-447">Binding matches form files by name.</span></span> <span data-ttu-id="a1feb-448">Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="a1feb-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="a1feb-449">Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="a1feb-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="a1feb-450">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="a1feb-450">The following example:</span></span>

* <span data-ttu-id="a1feb-451">Циклично отправляет один или несколько передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="a1feb-452">Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя.</span><span class="sxs-lookup"><span data-stu-id="a1feb-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="a1feb-453">Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.</span><span class="sxs-lookup"><span data-stu-id="a1feb-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="a1feb-454">Возвращает общее число и размер отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="a1feb-455">Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="a1feb-456">В следующем примере путь получен из конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a1feb-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="a1feb-457">Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="a1feb-458">Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="a1feb-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="a1feb-459">Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой.</span><span class="sxs-lookup"><span data-stu-id="a1feb-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="a1feb-460">Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="a1feb-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="a1feb-461">Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="a1feb-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="a1feb-462">Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="a1feb-463">Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="a1feb-464">Ограничение в 65 535 файлов предусмотрено для каждого сервера.</span><span class="sxs-lookup"><span data-stu-id="a1feb-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="a1feb-465">Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="a1feb-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * <span data-ttu-id="a1feb-466">[Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);</span><span class="sxs-lookup"><span data-stu-id="a1feb-466">[GetTempFileNameA function](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)</span></span>
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="a1feb-467">Передача небольших файлов с привязкой буферизованной модели к базе данных</span><span class="sxs-lookup"><span data-stu-id="a1feb-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="a1feb-468">Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="a1feb-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="a1feb-469">Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="a1feb-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="a1feb-470"><xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="a1feb-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="a1feb-471">В предыдущем примере используется свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="a1feb-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="a1feb-472">`FileUpload`Используется в Razor форме страниц:</span><span class="sxs-lookup"><span data-stu-id="a1feb-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="a1feb-473">При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="a1feb-474">В следующем примере `_dbContext` сохраняет контекст базы данных приложения:</span><span class="sxs-lookup"><span data-stu-id="a1feb-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="a1feb-475">Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:</span><span class="sxs-lookup"><span data-stu-id="a1feb-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="a1feb-476">*Pages/BufferedSingleFileUploadDb.cshtml*;</span><span class="sxs-lookup"><span data-stu-id="a1feb-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="a1feb-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="a1feb-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="a1feb-478">При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.</span><span class="sxs-lookup"><span data-stu-id="a1feb-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="a1feb-479">Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки.</span><span class="sxs-lookup"><span data-stu-id="a1feb-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="a1feb-480">Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.</span><span class="sxs-lookup"><span data-stu-id="a1feb-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="a1feb-481">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="a1feb-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="a1feb-482">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="a1feb-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="a1feb-483">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="a1feb-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="a1feb-484">Проверка</span><span class="sxs-lookup"><span data-stu-id="a1feb-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="a1feb-485">Передача больших файлов с помощью потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="a1feb-485">Upload large files with streaming</span></span>

<span data-ttu-id="a1feb-486">В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера.</span><span class="sxs-lookup"><span data-stu-id="a1feb-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="a1feb-487">Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса.</span><span class="sxs-lookup"><span data-stu-id="a1feb-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="a1feb-488">Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром.</span><span class="sxs-lookup"><span data-stu-id="a1feb-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="a1feb-489">Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое.</span><span class="sxs-lookup"><span data-stu-id="a1feb-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="a1feb-490">После считывания составных разделов действие выполняет собственную привязку модели.</span><span class="sxs-lookup"><span data-stu-id="a1feb-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="a1feb-491">Вначале страница загружает форму и сохраняет токен против подделки в файле cookie (с помощью атрибута `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="a1feb-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="a1feb-492">Атрибут использует встроенную поддержку [защиты от подделки](xref:security/anti-request-forgery) в ASP.NET Core, чтобы задать файл cookie с токеном запроса.</span><span class="sxs-lookup"><span data-stu-id="a1feb-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="a1feb-493">Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:</span><span class="sxs-lookup"><span data-stu-id="a1feb-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="a1feb-494">В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).</span><span class="sxs-lookup"><span data-stu-id="a1feb-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="a1feb-495">Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать).</span><span class="sxs-lookup"><span data-stu-id="a1feb-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="a1feb-496">Метод действия работает напрямую со свойством `Request`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="a1feb-497">Для считывания каждого раздела служит объект `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="a1feb-498">Данные "ключ — значение" хранятся в `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="a1feb-499">После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.</span><span class="sxs-lookup"><span data-stu-id="a1feb-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="a1feb-500">Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:</span><span class="sxs-lookup"><span data-stu-id="a1feb-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="a1feb-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="a1feb-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="a1feb-502">Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:</span><span class="sxs-lookup"><span data-stu-id="a1feb-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="a1feb-503">В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="a1feb-504">Проверка</span><span class="sxs-lookup"><span data-stu-id="a1feb-504">Validation</span></span>

<span data-ttu-id="a1feb-505">В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="a1feb-506">Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*.</span><span class="sxs-lookup"><span data-stu-id="a1feb-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="a1feb-507">Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.</span><span class="sxs-lookup"><span data-stu-id="a1feb-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="a1feb-508">Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="a1feb-509">В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.</span><span class="sxs-lookup"><span data-stu-id="a1feb-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="a1feb-510">Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:</span><span class="sxs-lookup"><span data-stu-id="a1feb-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="a1feb-511">Вы полностью разбираетесь в реализации.</span><span class="sxs-lookup"><span data-stu-id="a1feb-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="a1feb-512">Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="a1feb-513">**Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**</span><span class="sxs-lookup"><span data-stu-id="a1feb-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="a1feb-514">Проверка содержимого</span><span class="sxs-lookup"><span data-stu-id="a1feb-514">Content validation</span></span>

<span data-ttu-id="a1feb-515">**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**</span><span class="sxs-lookup"><span data-stu-id="a1feb-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="a1feb-516">Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных.</span><span class="sxs-lookup"><span data-stu-id="a1feb-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="a1feb-517">Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="a1feb-518">Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="a1feb-519">При передаче файл перемещается в нормальное место хранения файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="a1feb-520">Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="a1feb-521">Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.</span><span class="sxs-lookup"><span data-stu-id="a1feb-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="a1feb-522">Проверка расширения файла</span><span class="sxs-lookup"><span data-stu-id="a1feb-522">File extension validation</span></span>

<span data-ttu-id="a1feb-523">Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений.</span><span class="sxs-lookup"><span data-stu-id="a1feb-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="a1feb-524">Пример.</span><span class="sxs-lookup"><span data-stu-id="a1feb-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="a1feb-525">Проверка подписи файла</span><span class="sxs-lookup"><span data-stu-id="a1feb-525">File signature validation</span></span>

<span data-ttu-id="a1feb-526">Подпись файла определяется по первым нескольким байтам в начале файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="a1feb-527">Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="a1feb-528">Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="a1feb-529">В следующем примере проверяется подпись файла для изображения JPEG:</span><span class="sxs-lookup"><span data-stu-id="a1feb-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="a1feb-530">Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="a1feb-531">Безопасность имени файла</span><span class="sxs-lookup"><span data-stu-id="a1feb-531">File name security</span></span>

<span data-ttu-id="a1feb-532">Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом.</span><span class="sxs-lookup"><span data-stu-id="a1feb-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="a1feb-533">Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.</span><span class="sxs-lookup"><span data-stu-id="a1feb-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="a1feb-534">автоматически кодирует значения свойств для вывода.</span><span class="sxs-lookup"><span data-stu-id="a1feb-534"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="a1feb-535">Ниже приведен безопасный код, который можно использовать.</span><span class="sxs-lookup"><span data-stu-id="a1feb-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="a1feb-536">За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="a1feb-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="a1feb-537">Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем.</span><span class="sxs-lookup"><span data-stu-id="a1feb-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="a1feb-538">Предоставьте дополнительную логику для соответствия спецификациям приложения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="a1feb-539">Проверка размера</span><span class="sxs-lookup"><span data-stu-id="a1feb-539">Size validation</span></span>

<span data-ttu-id="a1feb-540">Ограничьте размер передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="a1feb-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="a1feb-541">В примере приложения размер файла ограничен 2 МБ (указывается в байтах).</span><span class="sxs-lookup"><span data-stu-id="a1feb-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="a1feb-542">Ограничение предоставляется с помощью [конфигурации](xref:fundamentals/configuration/index) из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a1feb-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="a1feb-543">В классы `PageModel` внедряется `FileSizeLimit`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="a1feb-544">Если размер файла превышает ограничение, файл отклоняется:</span><span class="sxs-lookup"><span data-stu-id="a1feb-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="a1feb-545">Сопоставление значения атрибута имени и имени параметра метода POST</span><span class="sxs-lookup"><span data-stu-id="a1feb-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="a1feb-546">В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="a1feb-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="a1feb-547">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="a1feb-547">In the following example:</span></span>

* <span data-ttu-id="a1feb-548">При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="a1feb-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="a1feb-549">При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="a1feb-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="a1feb-550">Используйте соответствующее имя для параметра метода C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="a1feb-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="a1feb-551">Для Razor метода обработчика страницы страниц с именем `Upload` :</span><span class="sxs-lookup"><span data-stu-id="a1feb-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="a1feb-552">Для метода действия контроллера POST приложения MVC:</span><span class="sxs-lookup"><span data-stu-id="a1feb-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="a1feb-553">Конфигурация сервера и приложения</span><span class="sxs-lookup"><span data-stu-id="a1feb-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="a1feb-554">Ограничение длины составного текста</span><span class="sxs-lookup"><span data-stu-id="a1feb-554">Multipart body length limit</span></span>

<span data-ttu-id="a1feb-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста.</span><span class="sxs-lookup"><span data-stu-id="a1feb-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="a1feb-556">Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе.</span><span class="sxs-lookup"><span data-stu-id="a1feb-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="a1feb-557">Значение по умолчанию — 134 217 728 байт (128 МБ).</span><span class="sxs-lookup"><span data-stu-id="a1feb-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="a1feb-558">Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a1feb-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1feb-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="a1feb-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="a1feb-560">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a1feb-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1feb-561">В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="a1feb-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="a1feb-562">Максимальный размер текста запроса Kestrel</span><span class="sxs-lookup"><span data-stu-id="a1feb-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="a1feb-563">Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="a1feb-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="a1feb-564">Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size):</span><span class="sxs-lookup"><span data-stu-id="a1feb-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="a1feb-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="a1feb-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="a1feb-566">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a1feb-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1feb-567">В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="a1feb-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="a1feb-568">Другие ограничения Kestrel</span><span class="sxs-lookup"><span data-stu-id="a1feb-568">Other Kestrel limits</span></span>

<span data-ttu-id="a1feb-569">Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:</span><span class="sxs-lookup"><span data-stu-id="a1feb-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="a1feb-570">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="a1feb-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="a1feb-571">Скорость передачи данных запросов и ответов</span><span class="sxs-lookup"><span data-stu-id="a1feb-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="a1feb-572">Ограничение длины содержимого IIS</span><span class="sxs-lookup"><span data-stu-id="a1feb-572">IIS content length limit</span></span>

<span data-ttu-id="a1feb-573">По умолчанию ограничение запроса (`maxAllowedContentLength`) составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="a1feb-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="a1feb-574">Ограничение можно настроить в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="a1feb-574">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="a1feb-575">Этот параметр применим только к службам IIS.</span><span class="sxs-lookup"><span data-stu-id="a1feb-575">This setting only applies to IIS.</span></span> <span data-ttu-id="a1feb-576">При размещении в Kestrel такой проблемы возникать не должно.</span><span class="sxs-lookup"><span data-stu-id="a1feb-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="a1feb-577">Дополнительные сведения см. в разделе [ограничения \<requestLimits> запросов ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="a1feb-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="a1feb-578">Ограничения в модуле ASP.NET Core или наличие модуля фильтрации запросов IIS могут ограничить передачу до 2 или 4 ГБ.</span><span class="sxs-lookup"><span data-stu-id="a1feb-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="a1feb-579">Дополнительные сведения см. в статье форума (dotnet/AspNetCore #2711) [Unable to upload file greater than 2GB in size](https://github.com/dotnet/AspNetCore/issues/2711) (Не удалось отправить файл размером более 2 ГБ).</span><span class="sxs-lookup"><span data-stu-id="a1feb-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a1feb-580">Диагностика</span><span class="sxs-lookup"><span data-stu-id="a1feb-580">Troubleshoot</span></span>

<span data-ttu-id="a1feb-581">Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.</span><span class="sxs-lookup"><span data-stu-id="a1feb-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="a1feb-582">Ошибка "Не найдено" при развертывании на сервере IIS</span><span class="sxs-lookup"><span data-stu-id="a1feb-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="a1feb-583">Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:</span><span class="sxs-lookup"><span data-stu-id="a1feb-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="a1feb-584">Дополнительные сведения об увеличении предела см. в разделе [Ограничение длины содержимого IIS](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="a1feb-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="a1feb-585">Сбой подключения</span><span class="sxs-lookup"><span data-stu-id="a1feb-585">Connection failure</span></span>

<span data-ttu-id="a1feb-586">Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a1feb-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="a1feb-587">Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="a1feb-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="a1feb-588">Может также потребоваться настроить ограничения подключений клиентов Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a1feb-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="a1feb-589">Исключение, связанное с пустой ссылкой в IFormFile</span><span class="sxs-lookup"><span data-stu-id="a1feb-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="a1feb-590">Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="a1feb-591">Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="a1feb-592">Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="a1feb-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="a1feb-593">Поток слишком длинный</span><span class="sxs-lookup"><span data-stu-id="a1feb-593">Stream was too long</span></span>

<span data-ttu-id="a1feb-594">В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="a1feb-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="a1feb-595">Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="a1feb-596">Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.</span><span class="sxs-lookup"><span data-stu-id="a1feb-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="a1feb-597">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a1feb-597">Additional resources</span></span>

* [<span data-ttu-id="a1feb-598">Сток запросов HTTP-подключения</span><span class="sxs-lookup"><span data-stu-id="a1feb-598">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="a1feb-599">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="a1feb-599">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="a1feb-600">Безопасность Azure: кадр безопасности: Проверка входных данных | Способы их устранения</span><span class="sxs-lookup"><span data-stu-id="a1feb-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="a1feb-601">Шаблоны разработки в облаке Azure: шаблон камердинера Key</span><span class="sxs-lookup"><span data-stu-id="a1feb-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
