---
<span data-ttu-id="36bff-101">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="36bff-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36bff-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36bff-102">'Blazor'</span></span>
- <span data-ttu-id="36bff-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36bff-103">'Identity'</span></span>
- <span data-ttu-id="36bff-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36bff-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="36bff-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36bff-105">'Razor'</span></span>
- <span data-ttu-id="36bff-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="36bff-106">'SignalR' uid:</span></span> 

---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="36bff-107">Передача файлов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36bff-107">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="36bff-108">По [Стив Смит](https://ardalis.com/) и [рутжер](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="36bff-108">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36bff-109">Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-109">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="36bff-110">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36bff-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="36bff-111">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="36bff-111">Security considerations</span></span>

<span data-ttu-id="36bff-112">Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер.</span><span class="sxs-lookup"><span data-stu-id="36bff-112">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="36bff-113">Злоумышленники могут попытаться:</span><span class="sxs-lookup"><span data-stu-id="36bff-113">Attackers may attempt to:</span></span>

* <span data-ttu-id="36bff-114">Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="36bff-114">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="36bff-115">Передать вирусы и вредоносные программы.</span><span class="sxs-lookup"><span data-stu-id="36bff-115">Upload viruses or malware.</span></span>
* <span data-ttu-id="36bff-116">Нарушить безопасность сетей и серверов другими способами.</span><span class="sxs-lookup"><span data-stu-id="36bff-116">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="36bff-117">Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.</span><span class="sxs-lookup"><span data-stu-id="36bff-117">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="36bff-118">Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск.</span><span class="sxs-lookup"><span data-stu-id="36bff-118">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="36bff-119">Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам.</span><span class="sxs-lookup"><span data-stu-id="36bff-119">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="36bff-120">Отключите разрешения на выполнение для расположения отправки файла.&dagger;</span><span class="sxs-lookup"><span data-stu-id="36bff-120">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="36bff-121">**Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;</span><span class="sxs-lookup"><span data-stu-id="36bff-121">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="36bff-122">Используйте безопасное имя файла, определяемое приложением.</span><span class="sxs-lookup"><span data-stu-id="36bff-122">Use a safe file name determined by the app.</span></span> <span data-ttu-id="36bff-123">Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении.</span><span class="sxs-lookup"><span data-stu-id="36bff-123">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="36bff-124">Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).</span><span class="sxs-lookup"><span data-stu-id="36bff-124">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="36bff-125">Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger;</span><span class="sxs-lookup"><span data-stu-id="36bff-125">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="36bff-126">Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.</span><span class="sxs-lookup"><span data-stu-id="36bff-126">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="36bff-127">Проверьте размер отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-127">Check the size of an uploaded file.</span></span> <span data-ttu-id="36bff-128">Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;</span><span class="sxs-lookup"><span data-stu-id="36bff-128">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="36bff-129">Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.</span><span class="sxs-lookup"><span data-stu-id="36bff-129">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="36bff-130">**Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**</span><span class="sxs-lookup"><span data-stu-id="36bff-130">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="36bff-131">&dagger;Пример приложения демонстрирует подход, который соответствует критериям.</span><span class="sxs-lookup"><span data-stu-id="36bff-131">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="36bff-132">Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:</span><span class="sxs-lookup"><span data-stu-id="36bff-132">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="36bff-133">полностью получить контроль над системой;</span><span class="sxs-lookup"><span data-stu-id="36bff-133">Completely gain control of a system.</span></span>
> * <span data-ttu-id="36bff-134">перезагрузить систему так, что она окажется в неработоспособном состоянии;</span><span class="sxs-lookup"><span data-stu-id="36bff-134">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="36bff-135">скомпрометировать пользовательские или системные данные;</span><span class="sxs-lookup"><span data-stu-id="36bff-135">Compromise user or system data.</span></span>
> * <span data-ttu-id="36bff-136">применить граффити к открытому интерфейсу.</span><span class="sxs-lookup"><span data-stu-id="36bff-136">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="36bff-137">Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="36bff-137">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="36bff-138">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="36bff-138">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="36bff-139">Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей</span><span class="sxs-lookup"><span data-stu-id="36bff-139">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="36bff-140">Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).</span><span class="sxs-lookup"><span data-stu-id="36bff-140">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="36bff-141">Сценарии использования хранилища</span><span class="sxs-lookup"><span data-stu-id="36bff-141">Storage scenarios</span></span>

<span data-ttu-id="36bff-142">К общим вариантам хранилища файлов относятся следующие:</span><span class="sxs-lookup"><span data-stu-id="36bff-142">Common storage options for files include:</span></span>

* <span data-ttu-id="36bff-143">База данных</span><span class="sxs-lookup"><span data-stu-id="36bff-143">Database</span></span>

  * <span data-ttu-id="36bff-144">В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="36bff-144">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="36bff-145">База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).</span><span class="sxs-lookup"><span data-stu-id="36bff-145">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="36bff-146">Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="36bff-146">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="36bff-147">Физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="36bff-147">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="36bff-148">Обработка передачи больших файлов:</span><span class="sxs-lookup"><span data-stu-id="36bff-148">For large file uploads:</span></span>
    * <span data-ttu-id="36bff-149">Ограничения базы данных могут ограничивать размер передачи.</span><span class="sxs-lookup"><span data-stu-id="36bff-149">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="36bff-150">Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.</span><span class="sxs-lookup"><span data-stu-id="36bff-150">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="36bff-151">Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="36bff-151">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="36bff-152">Процесс приложения должен иметь разрешения на чтение и запись для места хранения.</span><span class="sxs-lookup"><span data-stu-id="36bff-152">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="36bff-153">**Никогда не предоставляйте разрешение на выполнение.**</span><span class="sxs-lookup"><span data-stu-id="36bff-153">**Never grant execute permission.**</span></span>

* <span data-ttu-id="36bff-154">Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).</span><span class="sxs-lookup"><span data-stu-id="36bff-154">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="36bff-155">Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.</span><span class="sxs-lookup"><span data-stu-id="36bff-155">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="36bff-156">Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.</span><span class="sxs-lookup"><span data-stu-id="36bff-156">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="36bff-157">Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="36bff-157">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="36bff-158">Сценарии передачи файлов</span><span class="sxs-lookup"><span data-stu-id="36bff-158">File upload scenarios</span></span>

<span data-ttu-id="36bff-159">Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.</span><span class="sxs-lookup"><span data-stu-id="36bff-159">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="36bff-160">**ответов**</span><span class="sxs-lookup"><span data-stu-id="36bff-160">**Buffering**</span></span>

<span data-ttu-id="36bff-161">Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).</span><span class="sxs-lookup"><span data-stu-id="36bff-161">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="36bff-162">Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-162">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="36bff-163">При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске.</span><span class="sxs-lookup"><span data-stu-id="36bff-163">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="36bff-164">Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="36bff-164">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="36bff-165">Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.</span><span class="sxs-lookup"><span data-stu-id="36bff-165">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="36bff-166">Буферизация небольших файлов описана в следующих разделах этой статьи:</span><span class="sxs-lookup"><span data-stu-id="36bff-166">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="36bff-167">Физическое хранилище</span><span class="sxs-lookup"><span data-stu-id="36bff-167">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="36bff-168">База данных</span><span class="sxs-lookup"><span data-stu-id="36bff-168">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="36bff-169">**Потоковая передача**</span><span class="sxs-lookup"><span data-stu-id="36bff-169">**Streaming**</span></span>

<span data-ttu-id="36bff-170">Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую.</span><span class="sxs-lookup"><span data-stu-id="36bff-170">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="36bff-171">Потоковая передача повышает производительность не значительно.</span><span class="sxs-lookup"><span data-stu-id="36bff-171">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="36bff-172">При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.</span><span class="sxs-lookup"><span data-stu-id="36bff-172">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="36bff-173">Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="36bff-173">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="36bff-174">Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу</span><span class="sxs-lookup"><span data-stu-id="36bff-174">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="36bff-175">Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.</span><span class="sxs-lookup"><span data-stu-id="36bff-175">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="36bff-176">В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):</span><span class="sxs-lookup"><span data-stu-id="36bff-176">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="36bff-177">Следующий пример аналогичен предыдущему примеру, за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="36bff-177">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="36bff-178">JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.</span><span class="sxs-lookup"><span data-stu-id="36bff-178">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="36bff-179">Проверка не выполняется.</span><span class="sxs-lookup"><span data-stu-id="36bff-179">There's no validation.</span></span>

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

<span data-ttu-id="36bff-180">Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="36bff-180">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="36bff-181">Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="36bff-181">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="36bff-182">Используйте `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="36bff-182">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="36bff-183">Пример:</span><span class="sxs-lookup"><span data-stu-id="36bff-183">For example:</span></span>

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

<span data-ttu-id="36bff-184">Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="36bff-184">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="36bff-185">Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:</span><span class="sxs-lookup"><span data-stu-id="36bff-185">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="36bff-186">Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="36bff-186">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="36bff-187">В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.</span><span class="sxs-lookup"><span data-stu-id="36bff-187">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="36bff-188">**Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="36bff-188">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="36bff-189">При отображении или ведении журнала кодируйте имя файла в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="36bff-189">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="36bff-190">Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути.</span><span class="sxs-lookup"><span data-stu-id="36bff-190">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="36bff-191">Приложения должны:</span><span class="sxs-lookup"><span data-stu-id="36bff-191">Applications should:</span></span>
>
> * <span data-ttu-id="36bff-192">удалить путь из имени файла, указываемого пользователем;</span><span class="sxs-lookup"><span data-stu-id="36bff-192">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="36bff-193">сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="36bff-193">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="36bff-194">создать случайное имя файла для хранения.</span><span class="sxs-lookup"><span data-stu-id="36bff-194">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="36bff-195">Следующий код удаляет путь из имени файла:</span><span class="sxs-lookup"><span data-stu-id="36bff-195">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="36bff-196">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="36bff-196">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="36bff-197">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="36bff-197">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="36bff-198">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="36bff-198">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="36bff-199">Проверка</span><span class="sxs-lookup"><span data-stu-id="36bff-199">Validation</span></span>](#validation)

<span data-ttu-id="36bff-200">При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="36bff-200">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="36bff-201">Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="36bff-201">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="36bff-202">Любая из следующих коллекций, представляющих несколько файлов:</span><span class="sxs-lookup"><span data-stu-id="36bff-202">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="36bff-203">[Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="36bff-203">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="36bff-204">Привязка сопоставляет файлы форм по имени.</span><span class="sxs-lookup"><span data-stu-id="36bff-204">Binding matches form files by name.</span></span> <span data-ttu-id="36bff-205">Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="36bff-205">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="36bff-206">Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="36bff-206">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="36bff-207">Следующий пример:</span><span class="sxs-lookup"><span data-stu-id="36bff-207">The following example:</span></span>

* <span data-ttu-id="36bff-208">Циклично отправляет один или несколько передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-208">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="36bff-209">Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя.</span><span class="sxs-lookup"><span data-stu-id="36bff-209">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="36bff-210">Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.</span><span class="sxs-lookup"><span data-stu-id="36bff-210">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="36bff-211">Возвращает общее число и размер отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-211">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="36bff-212">Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="36bff-212">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="36bff-213">В следующем примере путь получен из конфигурации:</span><span class="sxs-lookup"><span data-stu-id="36bff-213">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="36bff-214">Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-214">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="36bff-215">Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="36bff-215">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="36bff-216">Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой.</span><span class="sxs-lookup"><span data-stu-id="36bff-216">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="36bff-217">Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="36bff-217">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="36bff-218">Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="36bff-218">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="36bff-219">Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="36bff-219">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="36bff-220">Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-220">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="36bff-221">Ограничение в 65 535 файлов предусмотрено для каждого сервера.</span><span class="sxs-lookup"><span data-stu-id="36bff-221">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="36bff-222">Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="36bff-222">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * <span data-ttu-id="36bff-223">[Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);</span><span class="sxs-lookup"><span data-stu-id="36bff-223">[GetTempFileNameA function](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)</span></span>
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="36bff-224">Передача небольших файлов с привязкой буферизованной модели к базе данных</span><span class="sxs-lookup"><span data-stu-id="36bff-224">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="36bff-225">Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="36bff-225">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="36bff-226">Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="36bff-226">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="36bff-227"><xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="36bff-227"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="36bff-228">В предыдущем примере используется свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="36bff-228">The prior example uses a bound model property.</span></span>

<span data-ttu-id="36bff-229">`FileUpload`Используется в Razor форме страниц:</span><span class="sxs-lookup"><span data-stu-id="36bff-229">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="36bff-230">При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов.</span><span class="sxs-lookup"><span data-stu-id="36bff-230">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="36bff-231">В следующем примере `_dbContext` сохраняет контекст базы данных приложения:</span><span class="sxs-lookup"><span data-stu-id="36bff-231">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="36bff-232">Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:</span><span class="sxs-lookup"><span data-stu-id="36bff-232">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="36bff-233">*Pages/BufferedSingleFileUploadDb.cshtml*;</span><span class="sxs-lookup"><span data-stu-id="36bff-233">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="36bff-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="36bff-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="36bff-235">При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.</span><span class="sxs-lookup"><span data-stu-id="36bff-235">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="36bff-236">Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки.</span><span class="sxs-lookup"><span data-stu-id="36bff-236">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="36bff-237">Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.</span><span class="sxs-lookup"><span data-stu-id="36bff-237">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="36bff-238">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="36bff-238">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="36bff-239">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="36bff-239">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="36bff-240">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="36bff-240">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="36bff-241">Проверка</span><span class="sxs-lookup"><span data-stu-id="36bff-241">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="36bff-242">Передача больших файлов с помощью потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="36bff-242">Upload large files with streaming</span></span>

<span data-ttu-id="36bff-243">В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера.</span><span class="sxs-lookup"><span data-stu-id="36bff-243">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="36bff-244">Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса.</span><span class="sxs-lookup"><span data-stu-id="36bff-244">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="36bff-245">Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром.</span><span class="sxs-lookup"><span data-stu-id="36bff-245">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="36bff-246">Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое.</span><span class="sxs-lookup"><span data-stu-id="36bff-246">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="36bff-247">После считывания составных разделов действие выполняет собственную привязку модели.</span><span class="sxs-lookup"><span data-stu-id="36bff-247">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="36bff-248">Вначале страница загружает форму и сохраняет токен против подделки в файле cookie (с помощью атрибута `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="36bff-248">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="36bff-249">Атрибут использует встроенную поддержку [защиты от подделки](xref:security/anti-request-forgery) в ASP.NET Core, чтобы задать файл cookie с токеном запроса.</span><span class="sxs-lookup"><span data-stu-id="36bff-249">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="36bff-250">Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:</span><span class="sxs-lookup"><span data-stu-id="36bff-250">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="36bff-251">В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).</span><span class="sxs-lookup"><span data-stu-id="36bff-251">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="36bff-252">Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать).</span><span class="sxs-lookup"><span data-stu-id="36bff-252">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="36bff-253">Метод действия работает напрямую со свойством `Request`.</span><span class="sxs-lookup"><span data-stu-id="36bff-253">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="36bff-254">Для считывания каждого раздела служит объект `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="36bff-254">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="36bff-255">Данные "ключ — значение" хранятся в `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="36bff-255">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="36bff-256">После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.</span><span class="sxs-lookup"><span data-stu-id="36bff-256">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="36bff-257">Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:</span><span class="sxs-lookup"><span data-stu-id="36bff-257">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="36bff-258">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bff-258">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="36bff-259">Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:</span><span class="sxs-lookup"><span data-stu-id="36bff-259">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="36bff-260">В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="36bff-260">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="36bff-261">Проверка</span><span class="sxs-lookup"><span data-stu-id="36bff-261">Validation</span></span>

<span data-ttu-id="36bff-262">В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-262">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="36bff-263">Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*.</span><span class="sxs-lookup"><span data-stu-id="36bff-263">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="36bff-264">Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.</span><span class="sxs-lookup"><span data-stu-id="36bff-264">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="36bff-265">Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-265">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="36bff-266">В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.</span><span class="sxs-lookup"><span data-stu-id="36bff-266">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="36bff-267">Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:</span><span class="sxs-lookup"><span data-stu-id="36bff-267">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="36bff-268">Вы полностью разбираетесь в реализации.</span><span class="sxs-lookup"><span data-stu-id="36bff-268">Fully understand the implementation.</span></span>
> * <span data-ttu-id="36bff-269">Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.</span><span class="sxs-lookup"><span data-stu-id="36bff-269">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="36bff-270">**Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**</span><span class="sxs-lookup"><span data-stu-id="36bff-270">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="36bff-271">Проверка содержимого</span><span class="sxs-lookup"><span data-stu-id="36bff-271">Content validation</span></span>

<span data-ttu-id="36bff-272">**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**</span><span class="sxs-lookup"><span data-stu-id="36bff-272">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="36bff-273">Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных.</span><span class="sxs-lookup"><span data-stu-id="36bff-273">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="36bff-274">Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения.</span><span class="sxs-lookup"><span data-stu-id="36bff-274">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="36bff-275">Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов.</span><span class="sxs-lookup"><span data-stu-id="36bff-275">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="36bff-276">При передаче файл перемещается в нормальное место хранения файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-276">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="36bff-277">Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-277">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="36bff-278">Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.</span><span class="sxs-lookup"><span data-stu-id="36bff-278">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="36bff-279">Проверка расширения файла</span><span class="sxs-lookup"><span data-stu-id="36bff-279">File extension validation</span></span>

<span data-ttu-id="36bff-280">Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений.</span><span class="sxs-lookup"><span data-stu-id="36bff-280">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="36bff-281">Пример:</span><span class="sxs-lookup"><span data-stu-id="36bff-281">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="36bff-282">Проверка подписи файла</span><span class="sxs-lookup"><span data-stu-id="36bff-282">File signature validation</span></span>

<span data-ttu-id="36bff-283">Подпись файла определяется по первым нескольким байтам в начале файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-283">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="36bff-284">Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-284">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="36bff-285">Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-285">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="36bff-286">В следующем примере проверяется подпись файла для изображения JPEG:</span><span class="sxs-lookup"><span data-stu-id="36bff-286">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="36bff-287">Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-287">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="36bff-288">Безопасность имени файла</span><span class="sxs-lookup"><span data-stu-id="36bff-288">File name security</span></span>

<span data-ttu-id="36bff-289">Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом.</span><span class="sxs-lookup"><span data-stu-id="36bff-289">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="36bff-290">Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.</span><span class="sxs-lookup"><span data-stu-id="36bff-290">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="36bff-291">автоматически кодирует значения свойств для вывода.</span><span class="sxs-lookup"><span data-stu-id="36bff-291"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="36bff-292">Ниже приведен безопасный код, который можно использовать.</span><span class="sxs-lookup"><span data-stu-id="36bff-292">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="36bff-293">За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="36bff-293">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="36bff-294">Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем.</span><span class="sxs-lookup"><span data-stu-id="36bff-294">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="36bff-295">Предоставьте дополнительную логику для соответствия спецификациям приложения.</span><span class="sxs-lookup"><span data-stu-id="36bff-295">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="36bff-296">Проверка размера</span><span class="sxs-lookup"><span data-stu-id="36bff-296">Size validation</span></span>

<span data-ttu-id="36bff-297">Ограничьте размер передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-297">Limit the size of uploaded files.</span></span>

<span data-ttu-id="36bff-298">В примере приложения размер файла ограничен 2 МБ (указывается в байтах).</span><span class="sxs-lookup"><span data-stu-id="36bff-298">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="36bff-299">Ограничение предоставляется с помощью [конфигурации](xref:fundamentals/configuration/index) из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="36bff-299">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="36bff-300">В классы `PageModel` внедряется `FileSizeLimit`.</span><span class="sxs-lookup"><span data-stu-id="36bff-300">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="36bff-301">Если размер файла превышает ограничение, файл отклоняется:</span><span class="sxs-lookup"><span data-stu-id="36bff-301">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="36bff-302">Сопоставление значения атрибута имени и имени параметра метода POST</span><span class="sxs-lookup"><span data-stu-id="36bff-302">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="36bff-303">В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="36bff-303">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="36bff-304">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="36bff-304">In the following example:</span></span>

* <span data-ttu-id="36bff-305">При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="36bff-305">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="36bff-306">При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="36bff-306">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="36bff-307">Используйте соответствующее имя для параметра метода C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="36bff-307">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="36bff-308">Для Razor метода обработчика страницы страниц с именем `Upload` :</span><span class="sxs-lookup"><span data-stu-id="36bff-308">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="36bff-309">Для метода действия контроллера POST приложения MVC:</span><span class="sxs-lookup"><span data-stu-id="36bff-309">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="36bff-310">Конфигурация сервера и приложения</span><span class="sxs-lookup"><span data-stu-id="36bff-310">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="36bff-311">Ограничение длины составного текста</span><span class="sxs-lookup"><span data-stu-id="36bff-311">Multipart body length limit</span></span>

<span data-ttu-id="36bff-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста.</span><span class="sxs-lookup"><span data-stu-id="36bff-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="36bff-313">Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе.</span><span class="sxs-lookup"><span data-stu-id="36bff-313">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="36bff-314">Значение по умолчанию — 134 217 728 байт (128 МБ).</span><span class="sxs-lookup"><span data-stu-id="36bff-314">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="36bff-315">Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36bff-315">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="36bff-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="36bff-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="36bff-317">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="36bff-317">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="36bff-318">В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="36bff-318">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="36bff-319">Максимальный размер текста запроса Kestrel</span><span class="sxs-lookup"><span data-stu-id="36bff-319">Kestrel maximum request body size</span></span>

<span data-ttu-id="36bff-320">Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="36bff-320">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="36bff-321">Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size):</span><span class="sxs-lookup"><span data-stu-id="36bff-321">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="36bff-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="36bff-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="36bff-323">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="36bff-323">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="36bff-324">В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="36bff-324">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="36bff-325">`RequestSizeLimitAttribute`Можно также применить с помощью [`@attribute`](xref:mvc/views/razor#attribute) Razor директивы:</span><span class="sxs-lookup"><span data-stu-id="36bff-325">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="36bff-326">Другие ограничения Kestrel</span><span class="sxs-lookup"><span data-stu-id="36bff-326">Other Kestrel limits</span></span>

<span data-ttu-id="36bff-327">Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:</span><span class="sxs-lookup"><span data-stu-id="36bff-327">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="36bff-328">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="36bff-328">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="36bff-329">Скорость передачи данных запросов и ответов</span><span class="sxs-lookup"><span data-stu-id="36bff-329">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="36bff-330">Ограничение длины содержимого IIS</span><span class="sxs-lookup"><span data-stu-id="36bff-330">IIS content length limit</span></span>

<span data-ttu-id="36bff-331">По умолчанию ограничение запроса (`maxAllowedContentLength`) составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="36bff-331">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="36bff-332">Ограничение можно настроить в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="36bff-332">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="36bff-333">Этот параметр применим только к службам IIS.</span><span class="sxs-lookup"><span data-stu-id="36bff-333">This setting only applies to IIS.</span></span> <span data-ttu-id="36bff-334">При размещении в Kestrel такой проблемы возникать не должно.</span><span class="sxs-lookup"><span data-stu-id="36bff-334">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="36bff-335">Дополнительные сведения см. в статье [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/) (Ограничения запроса <requestLimits>).</span><span class="sxs-lookup"><span data-stu-id="36bff-335">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="36bff-336">Ограничения в модуле ASP.NET Core или наличие модуля фильтрации запросов IIS могут ограничить передачу до 2 или 4 ГБ.</span><span class="sxs-lookup"><span data-stu-id="36bff-336">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="36bff-337">Дополнительные сведения см. в статье форума (dotnet/AspNetCore #2711) [Unable to upload file greater than 2GB in size](https://github.com/dotnet/AspNetCore/issues/2711) (Не удалось отправить файл размером более 2 ГБ).</span><span class="sxs-lookup"><span data-stu-id="36bff-337">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="36bff-338">Диагностика</span><span class="sxs-lookup"><span data-stu-id="36bff-338">Troubleshoot</span></span>

<span data-ttu-id="36bff-339">Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.</span><span class="sxs-lookup"><span data-stu-id="36bff-339">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="36bff-340">Ошибка "Не найдено" при развертывании на сервере IIS</span><span class="sxs-lookup"><span data-stu-id="36bff-340">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="36bff-341">Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:</span><span class="sxs-lookup"><span data-stu-id="36bff-341">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="36bff-342">Дополнительные сведения об увеличении предела см. в разделе [Ограничение длины содержимого IIS](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="36bff-342">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="36bff-343">Сбой подключения</span><span class="sxs-lookup"><span data-stu-id="36bff-343">Connection failure</span></span>

<span data-ttu-id="36bff-344">Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel.</span><span class="sxs-lookup"><span data-stu-id="36bff-344">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="36bff-345">Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="36bff-345">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="36bff-346">Может также потребоваться настроить ограничения подключений клиентов Kestrel.</span><span class="sxs-lookup"><span data-stu-id="36bff-346">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="36bff-347">Исключение, связанное с пустой ссылкой в IFormFile</span><span class="sxs-lookup"><span data-stu-id="36bff-347">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="36bff-348">Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="36bff-348">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="36bff-349">Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`.</span><span class="sxs-lookup"><span data-stu-id="36bff-349">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="36bff-350">Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="36bff-350">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="36bff-351">Поток слишком длинный</span><span class="sxs-lookup"><span data-stu-id="36bff-351">Stream was too long</span></span>

<span data-ttu-id="36bff-352">В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-352">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="36bff-353">Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="36bff-353">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="36bff-354">Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.</span><span class="sxs-lookup"><span data-stu-id="36bff-354">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36bff-355">Действия ASP.NET Core поддерживают передачу одного или нескольких файлов с помощью привязки модели с буферизацией для небольших файлов или потоковой передачи без буферизации для более крупных файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-355">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="36bff-356">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36bff-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="36bff-357">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="36bff-357">Security considerations</span></span>

<span data-ttu-id="36bff-358">Необходимо соблюдать осторожность при предоставлении пользователям возможности отправки файлов на сервер.</span><span class="sxs-lookup"><span data-stu-id="36bff-358">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="36bff-359">Злоумышленники могут попытаться:</span><span class="sxs-lookup"><span data-stu-id="36bff-359">Attackers may attempt to:</span></span>

* <span data-ttu-id="36bff-360">Выполнить атаку типа [отказ в обслуживании](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="36bff-360">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="36bff-361">Передать вирусы и вредоносные программы.</span><span class="sxs-lookup"><span data-stu-id="36bff-361">Upload viruses or malware.</span></span>
* <span data-ttu-id="36bff-362">Нарушить безопасность сетей и серверов другими способами.</span><span class="sxs-lookup"><span data-stu-id="36bff-362">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="36bff-363">Ниже приведены некоторые действия по обеспечению безопасности, которые снижают вероятность успешных атак.</span><span class="sxs-lookup"><span data-stu-id="36bff-363">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="36bff-364">Передавайте файлы в выделенную область для отправки файлов, желательно не на системный диск.</span><span class="sxs-lookup"><span data-stu-id="36bff-364">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="36bff-365">Использование выделенного расположения упрощает применение мер безопасности к отправленным файлам.</span><span class="sxs-lookup"><span data-stu-id="36bff-365">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="36bff-366">Отключите разрешения на выполнение для расположения отправки файла.&dagger;</span><span class="sxs-lookup"><span data-stu-id="36bff-366">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="36bff-367">**Не** сохраняйте переданные файлы в дереве каталогов, где находится приложение.&dagger;</span><span class="sxs-lookup"><span data-stu-id="36bff-367">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="36bff-368">Используйте безопасное имя файла, определяемое приложением.</span><span class="sxs-lookup"><span data-stu-id="36bff-368">Use a safe file name determined by the app.</span></span> <span data-ttu-id="36bff-369">Не используйте имя файла, предоставленное пользователем, или имя ненадежного файла отправленного файла. &dagger; HTML кодирует ненадежное имя файла при его отображении.</span><span class="sxs-lookup"><span data-stu-id="36bff-369">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="36bff-370">Например, запись имени файла или отображение в пользовательском интерфейсе ( Razor автоматически кодируется в формате HTML).</span><span class="sxs-lookup"><span data-stu-id="36bff-370">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="36bff-371">Разрешите только утвержденные расширения файлов для спецификации на проектирование приложения.&dagger;</span><span class="sxs-lookup"><span data-stu-id="36bff-371">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="36bff-372">Убедитесь, что на сервере выполняются проверки на стороне клиента. &dagger; Проверки на стороне клиента легко обойти.</span><span class="sxs-lookup"><span data-stu-id="36bff-372">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="36bff-373">Проверьте размер отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-373">Check the size of an uploaded file.</span></span> <span data-ttu-id="36bff-374">Установите максимальный предельный размер, чтобы предотвратить передачу больших объемов данных.&dagger;</span><span class="sxs-lookup"><span data-stu-id="36bff-374">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="36bff-375">Если файлы не должны перезаписываться переданным файлом с тем же именем, перед отправкой файла проверьте его имя в базе данных или физическом хранилище.</span><span class="sxs-lookup"><span data-stu-id="36bff-375">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="36bff-376">**Запустите сканер для проверки отправляемого содержимого на наличие вирусов и вредоносных программ, прежде чем сохранять файл.**</span><span class="sxs-lookup"><span data-stu-id="36bff-376">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="36bff-377">&dagger;Пример приложения демонстрирует подход, который соответствует критериям.</span><span class="sxs-lookup"><span data-stu-id="36bff-377">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="36bff-378">Отправка в систему вредоносного кода часто является первым шагом перед выполнением кода, который может:</span><span class="sxs-lookup"><span data-stu-id="36bff-378">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="36bff-379">полностью получить контроль над системой;</span><span class="sxs-lookup"><span data-stu-id="36bff-379">Completely gain control of a system.</span></span>
> * <span data-ttu-id="36bff-380">перезагрузить систему так, что она окажется в неработоспособном состоянии;</span><span class="sxs-lookup"><span data-stu-id="36bff-380">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="36bff-381">скомпрометировать пользовательские или системные данные;</span><span class="sxs-lookup"><span data-stu-id="36bff-381">Compromise user or system data.</span></span>
> * <span data-ttu-id="36bff-382">применить граффити к открытому интерфейсу.</span><span class="sxs-lookup"><span data-stu-id="36bff-382">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="36bff-383">Сведения об уменьшении контактной зоны атаки во время приема файлов от пользователей см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="36bff-383">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="36bff-384">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="36bff-384">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="36bff-385">Безопасность Azure: убедитесь в наличии надлежащих мер контроля при получении файлов от пользователей</span><span class="sxs-lookup"><span data-stu-id="36bff-385">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="36bff-386">Дополнительные сведения о реализации мер безопасности, включая примеры из примера приложения, см. в статье [Передача файлов в ASP.NET Core](#validation).</span><span class="sxs-lookup"><span data-stu-id="36bff-386">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="36bff-387">Сценарии использования хранилища</span><span class="sxs-lookup"><span data-stu-id="36bff-387">Storage scenarios</span></span>

<span data-ttu-id="36bff-388">К общим вариантам хранилища файлов относятся следующие:</span><span class="sxs-lookup"><span data-stu-id="36bff-388">Common storage options for files include:</span></span>

* <span data-ttu-id="36bff-389">База данных</span><span class="sxs-lookup"><span data-stu-id="36bff-389">Database</span></span>

  * <span data-ttu-id="36bff-390">В случае отправки небольших файлов база данных часто работает быстрее, чем физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="36bff-390">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="36bff-391">База данных часто более удобна по сравнению с вариантами физического хранилища, так как получение записи из базы пользовательских данных может одновременно предоставить содержимое файла (например, изображение аватара).</span><span class="sxs-lookup"><span data-stu-id="36bff-391">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="36bff-392">Эксплуатация базы данных потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="36bff-392">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="36bff-393">Физическое хранилище (файловая система или сетевая папка).</span><span class="sxs-lookup"><span data-stu-id="36bff-393">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="36bff-394">Обработка передачи больших файлов:</span><span class="sxs-lookup"><span data-stu-id="36bff-394">For large file uploads:</span></span>
    * <span data-ttu-id="36bff-395">Ограничения базы данных могут ограничивать размер передачи.</span><span class="sxs-lookup"><span data-stu-id="36bff-395">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="36bff-396">Физическое хранилище часто менее экономически выгодно, чем хранилище в базе данных.</span><span class="sxs-lookup"><span data-stu-id="36bff-396">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="36bff-397">Эксплуатация физического хранилища потенциально дешевле, чем использование службы хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="36bff-397">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="36bff-398">Процесс приложения должен иметь разрешения на чтение и запись для места хранения.</span><span class="sxs-lookup"><span data-stu-id="36bff-398">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="36bff-399">**Никогда не предоставляйте разрешение на выполнение.**</span><span class="sxs-lookup"><span data-stu-id="36bff-399">**Never grant execute permission.**</span></span>

* <span data-ttu-id="36bff-400">Служба хранилища данных (например, хранилище [BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)).</span><span class="sxs-lookup"><span data-stu-id="36bff-400">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="36bff-401">Обычно службы обеспечивают улучшенную масштабируемость и устойчивость по сравнению с локальными решениями, которые обычно подвержены единым точкам отказа.</span><span class="sxs-lookup"><span data-stu-id="36bff-401">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="36bff-402">Затраты на использование служб обычно ниже в сценариях с крупномасштабной инфраструктурой хранения.</span><span class="sxs-lookup"><span data-stu-id="36bff-402">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="36bff-403">Дополнительные сведения см. в разделе [Краткое руководство. Создание большого двоичного объекта в хранилище объектов с помощью .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="36bff-403">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="36bff-404">В этом разделе показан метод <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, но метод <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> можно использовать для сохранения <xref:System.IO.FileStream> в хранилище BLOB-объектов при работе с <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="36bff-404">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="36bff-405">Сценарии передачи файлов</span><span class="sxs-lookup"><span data-stu-id="36bff-405">File upload scenarios</span></span>

<span data-ttu-id="36bff-406">Есть два распространенных подхода к передаче файлов — буферизация и потоковая передача.</span><span class="sxs-lookup"><span data-stu-id="36bff-406">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="36bff-407">**ответов**</span><span class="sxs-lookup"><span data-stu-id="36bff-407">**Buffering**</span></span>

<span data-ttu-id="36bff-408">Весь файл считывается в <xref:Microsoft.AspNetCore.Http.IFormFile> (представление файла на C#, используемого для обработки или сохранения файла).</span><span class="sxs-lookup"><span data-stu-id="36bff-408">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="36bff-409">Потребление ресурсов (диска, памяти) при передаче файлов зависит от количества и размера одновременно передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-409">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="36bff-410">При попытке приложения поместить в буфер слишком много файлов может произойти аварийное завершение работы сайта из-за нехватки памяти или места на диске.</span><span class="sxs-lookup"><span data-stu-id="36bff-410">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="36bff-411">Если размер или частота отправки файлов исчерпывают ресурсы приложения, используйте потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="36bff-411">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="36bff-412">Один буферизованный файл размером свыше 64 КБ перемещается из памяти во временный файл на диске.</span><span class="sxs-lookup"><span data-stu-id="36bff-412">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="36bff-413">Буферизация небольших файлов описана в следующих разделах этой статьи:</span><span class="sxs-lookup"><span data-stu-id="36bff-413">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="36bff-414">Физическое хранилище</span><span class="sxs-lookup"><span data-stu-id="36bff-414">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="36bff-415">База данных</span><span class="sxs-lookup"><span data-stu-id="36bff-415">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="36bff-416">**Потоковая передача**</span><span class="sxs-lookup"><span data-stu-id="36bff-416">**Streaming**</span></span>

<span data-ttu-id="36bff-417">Файл можно получить с помощью составного запроса. Затем он обрабатывается или сохраняется приложением напрямую.</span><span class="sxs-lookup"><span data-stu-id="36bff-417">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="36bff-418">Потоковая передача повышает производительность не значительно.</span><span class="sxs-lookup"><span data-stu-id="36bff-418">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="36bff-419">При отправке файлов потоковая передача снижает нагрузку на память или на место на диске.</span><span class="sxs-lookup"><span data-stu-id="36bff-419">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="36bff-420">Потоковая передача больших файлов рассматривается в разделе [Передача больших файлов с помощью потоковой передачи](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="36bff-420">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="36bff-421">Передача небольших файлов с привязкой буферизованной модели к физическому хранилищу</span><span class="sxs-lookup"><span data-stu-id="36bff-421">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="36bff-422">Для передачи небольших файлов можно применить составную форму или сформировать запрос POST на языке JavaScript.</span><span class="sxs-lookup"><span data-stu-id="36bff-422">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="36bff-423">В следующем примере показано использование Razor формы страниц для передачи одного файла (*pages/буффередсинглефилеуплоадфисикал. cshtml* в примере приложения):</span><span class="sxs-lookup"><span data-stu-id="36bff-423">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="36bff-424">Следующий пример аналогичен предыдущему примеру, за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="36bff-424">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="36bff-425">JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) используется для отправки данных формы.</span><span class="sxs-lookup"><span data-stu-id="36bff-425">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="36bff-426">Проверка не выполняется.</span><span class="sxs-lookup"><span data-stu-id="36bff-426">There's no validation.</span></span>

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

<span data-ttu-id="36bff-427">Для выполнения отправки формы в JavaScript для клиентов, которые [не поддерживают Fetch API](https://caniuse.com/#feat=fetch), используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="36bff-427">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="36bff-428">Используйте функцию Fetch Polyfill (например, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="36bff-428">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="36bff-429">Используйте `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="36bff-429">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="36bff-430">Пример:</span><span class="sxs-lookup"><span data-stu-id="36bff-430">For example:</span></span>

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

<span data-ttu-id="36bff-431">Для поддержки передачи файлов в HTML-формах должен указываться тип кодировки `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="36bff-431">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="36bff-432">Для элемента ввода `files`, поддерживающего отправку нескольких файлов, в элементе `<input>` необходимо указать атрибут `multiple`:</span><span class="sxs-lookup"><span data-stu-id="36bff-432">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="36bff-433">Доступ к отдельным файлам, переданным на сервер, можно получать посредством [привязки модели](xref:mvc/models/model-binding) с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="36bff-433">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="36bff-434">В примере приложения показано несколько отправок буферизованных файлов для баз данных и физических хранилищ.</span><span class="sxs-lookup"><span data-stu-id="36bff-434">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="36bff-435">**Не** используйте свойство `FileName` объекта <xref:Microsoft.AspNetCore.Http.IFormFile>, кроме как для отображения и ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="36bff-435">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="36bff-436">При отображении или ведении журнала кодируйте имя файла в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="36bff-436">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="36bff-437">Злоумышленник может предоставить имя вредоносного файла, включая полные или относительные пути.</span><span class="sxs-lookup"><span data-stu-id="36bff-437">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="36bff-438">Приложения должны:</span><span class="sxs-lookup"><span data-stu-id="36bff-438">Applications should:</span></span>
>
> * <span data-ttu-id="36bff-439">удалить путь из имени файла, указываемого пользователем;</span><span class="sxs-lookup"><span data-stu-id="36bff-439">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="36bff-440">сохранить имя файла, закодированное в формате HTML, откуда был удален путь, для пользовательского интерфейса или ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="36bff-440">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="36bff-441">создать случайное имя файла для хранения.</span><span class="sxs-lookup"><span data-stu-id="36bff-441">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="36bff-442">Следующий код удаляет путь из имени файла:</span><span class="sxs-lookup"><span data-stu-id="36bff-442">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="36bff-443">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="36bff-443">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="36bff-444">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="36bff-444">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="36bff-445">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="36bff-445">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="36bff-446">Проверка</span><span class="sxs-lookup"><span data-stu-id="36bff-446">Validation</span></span>](#validation)

<span data-ttu-id="36bff-447">При отправке файлов с помощью привязки модели и <xref:Microsoft.AspNetCore.Http.IFormFile> метод действия может принимать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="36bff-447">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="36bff-448">Один файл <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="36bff-448">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="36bff-449">Любая из следующих коллекций, представляющих несколько файлов:</span><span class="sxs-lookup"><span data-stu-id="36bff-449">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="36bff-450">[Числ](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="36bff-450">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="36bff-451">Привязка сопоставляет файлы форм по имени.</span><span class="sxs-lookup"><span data-stu-id="36bff-451">Binding matches form files by name.</span></span> <span data-ttu-id="36bff-452">Например, значение HTML `name` в `<input type="file" name="formFile">` должно соответствовать привязанному к C# параметру или свойству (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="36bff-452">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="36bff-453">Дополнительные сведения см. в разделе [Сопоставление значения атрибута имени и имени параметра метода POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="36bff-453">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="36bff-454">Следующий пример:</span><span class="sxs-lookup"><span data-stu-id="36bff-454">The following example:</span></span>

* <span data-ttu-id="36bff-455">Циклично отправляет один или несколько передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-455">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="36bff-456">Использует метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы вернуть полный путь к файлу, включая его имя.</span><span class="sxs-lookup"><span data-stu-id="36bff-456">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="36bff-457">Сохраняет файлы в локальную файловую систему, используя имя файла, созданное приложением.</span><span class="sxs-lookup"><span data-stu-id="36bff-457">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="36bff-458">Возвращает общее число и размер отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-458">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="36bff-459">Чтобы создать имя файла без пути, используйте `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="36bff-459">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="36bff-460">В следующем примере путь получен из конфигурации:</span><span class="sxs-lookup"><span data-stu-id="36bff-460">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="36bff-461">Передаваемый в  путь <xref:System.IO.FileStream> *должен* содержать имя файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-461">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="36bff-462">Если имя файла не указано, в среде выполнения возникает исключение <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="36bff-462">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="36bff-463">Файлы, передаваемые с помощью интерфейса <xref:Microsoft.AspNetCore.Http.IFormFile>, буферизуются в памяти или на диске на сервере перед обработкой.</span><span class="sxs-lookup"><span data-stu-id="36bff-463">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="36bff-464">Внутри метода действия содержимое <xref:Microsoft.AspNetCore.Http.IFormFile> доступно в виде <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="36bff-464">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="36bff-465">Помимо локальной файловой системы, файлы можно сохранять в сетевой папке или в службе хранилища файлов, например в хранилище [BLOB-объектов Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="36bff-465">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="36bff-466">Еще один пример, который перебирает несколько файлов для отправки и использует надежные имена файлов, см. в файле *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="36bff-466">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="36bff-467">Метод [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) вызывает исключение <xref:System.IO.IOException> в случае создания более чем 65 535 файлов без удаления предыдущих временных файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-467">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="36bff-468">Ограничение в 65 535 файлов предусмотрено для каждого сервера.</span><span class="sxs-lookup"><span data-stu-id="36bff-468">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="36bff-469">Дополнительные сведения об этом ограничении в ОС Windows см. в примечаниях в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="36bff-469">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * <span data-ttu-id="36bff-470">[Функция GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks);</span><span class="sxs-lookup"><span data-stu-id="36bff-470">[GetTempFileNameA function](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)</span></span>
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="36bff-471">Передача небольших файлов с привязкой буферизованной модели к базе данных</span><span class="sxs-lookup"><span data-stu-id="36bff-471">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="36bff-472">Для сохранения данных двоичных файлов в базе данных с помощью [Entity Framework](/ef/core/index) определите для сущности свойство массива <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="36bff-472">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="36bff-473">Укажите свойство модели страницы для класса, который содержит <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="36bff-473">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="36bff-474"><xref:Microsoft.AspNetCore.Http.IFormFile> можно использовать непосредственно как параметр метода действия или свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="36bff-474"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="36bff-475">В предыдущем примере используется свойство модели привязки.</span><span class="sxs-lookup"><span data-stu-id="36bff-475">The prior example uses a bound model property.</span></span>

<span data-ttu-id="36bff-476">`FileUpload`Используется в Razor форме страниц:</span><span class="sxs-lookup"><span data-stu-id="36bff-476">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="36bff-477">При публикации формы на сервере скопируйте <xref:Microsoft.AspNetCore.Http.IFormFile> в поток и сохраните его в базе данных в виде массива байтов.</span><span class="sxs-lookup"><span data-stu-id="36bff-477">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="36bff-478">В следующем примере `_dbContext` сохраняет контекст базы данных приложения:</span><span class="sxs-lookup"><span data-stu-id="36bff-478">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="36bff-479">Предыдущий пример похож на сценарий, продемонстрированный в примере приложения:</span><span class="sxs-lookup"><span data-stu-id="36bff-479">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="36bff-480">*Pages/BufferedSingleFileUploadDb.cshtml*;</span><span class="sxs-lookup"><span data-stu-id="36bff-480">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="36bff-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="36bff-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="36bff-482">При сохранении двоичных данных в реляционных базах данных следует соблюдать осторожность, так как это может отрицательно сказаться на производительности.</span><span class="sxs-lookup"><span data-stu-id="36bff-482">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="36bff-483">Свойство `FileName` параметра <xref:Microsoft.AspNetCore.Http.IFormFile> требует обязательной проверки.</span><span class="sxs-lookup"><span data-stu-id="36bff-483">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="36bff-484">Свойство `FileName` следует использовать только в целях вывода и только после HTML-кодирования.</span><span class="sxs-lookup"><span data-stu-id="36bff-484">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="36bff-485">В приведенных выше примерах не учитываются вопросы безопасности.</span><span class="sxs-lookup"><span data-stu-id="36bff-485">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="36bff-486">Дополнительные сведения приведены в следующих разделах и в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="36bff-486">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="36bff-487">Вопросы безопасности</span><span class="sxs-lookup"><span data-stu-id="36bff-487">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="36bff-488">Проверка</span><span class="sxs-lookup"><span data-stu-id="36bff-488">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="36bff-489">Передача больших файлов с помощью потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="36bff-489">Upload large files with streaming</span></span>

<span data-ttu-id="36bff-490">В приведенном ниже примере демонстрируется использование JavaScript для потоковой передачи файла в действие контроллера.</span><span class="sxs-lookup"><span data-stu-id="36bff-490">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="36bff-491">Токен против подделки файла создается с помощью пользовательского атрибута фильтра и передается в заголовках HTTP клиента, а не в теле запроса.</span><span class="sxs-lookup"><span data-stu-id="36bff-491">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="36bff-492">Так как метод действия обрабатывает передаваемые данные напрямую, привязка модели формы отключается другим пользовательским фильтром.</span><span class="sxs-lookup"><span data-stu-id="36bff-492">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="36bff-493">Внутри действия содержимое формы считывается с помощью объекта `MultipartReader`, который считывает каждый объект `MultipartSection` по отдельности, обрабатывая файл или сохраняя содержимое.</span><span class="sxs-lookup"><span data-stu-id="36bff-493">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="36bff-494">После считывания составных разделов действие выполняет собственную привязку модели.</span><span class="sxs-lookup"><span data-stu-id="36bff-494">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="36bff-495">Вначале страница загружает форму и сохраняет токен против подделки в файле cookie (с помощью атрибута `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="36bff-495">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="36bff-496">Атрибут использует встроенную поддержку [защиты от подделки](xref:security/anti-request-forgery) в ASP.NET Core, чтобы задать файл cookie с токеном запроса.</span><span class="sxs-lookup"><span data-stu-id="36bff-496">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="36bff-497">Для отключения привязки модели используется `DisableFormValueModelBindingAttribute`:</span><span class="sxs-lookup"><span data-stu-id="36bff-497">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="36bff-498">В примере приложения `GenerateAntiforgeryTokenCookieAttribute` и применяются в `DisableFormValueModelBindingAttribute` качестве фильтров к моделям приложений страницы `/StreamedSingleFileUploadDb` и `/StreamedSingleFileUploadPhysical` в `Startup.ConfigureServices` с использованием [ Razor соглашений о страницах](xref:razor-pages/razor-pages-conventions).</span><span class="sxs-lookup"><span data-stu-id="36bff-498">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="36bff-499">Так как привязка модели не считывает форму, параметры, привязанные из формы, не привязываются (запросы, маршруты и заголовки продолжают работать).</span><span class="sxs-lookup"><span data-stu-id="36bff-499">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="36bff-500">Метод действия работает напрямую со свойством `Request`.</span><span class="sxs-lookup"><span data-stu-id="36bff-500">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="36bff-501">Для считывания каждого раздела служит объект `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="36bff-501">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="36bff-502">Данные "ключ — значение" хранятся в `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="36bff-502">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="36bff-503">После считывания составных разделов содержимое `KeyValueAccumulator` используется для привязки данных формы к типу модели.</span><span class="sxs-lookup"><span data-stu-id="36bff-503">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="36bff-504">Полный метод `StreamingController.UploadDatabase` для потоковой передачи в базу данных с EF Core:</span><span class="sxs-lookup"><span data-stu-id="36bff-504">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="36bff-505">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bff-505">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="36bff-506">Полный метод `StreamingController.UploadPhysical` для потоковой передачи в физическое расположение:</span><span class="sxs-lookup"><span data-stu-id="36bff-506">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="36bff-507">В примере приложения проверки обрабатываются с помощью `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="36bff-507">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="36bff-508">Проверка</span><span class="sxs-lookup"><span data-stu-id="36bff-508">Validation</span></span>

<span data-ttu-id="36bff-509">В классе `FileHelpers` примера приложения демонстрируется несколько проверок буферизованного файла <xref:Microsoft.AspNetCore.Http.IFormFile> и потоковой передачи файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-509">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="36bff-510">Сведения об обработке буферизованных файлов <xref:Microsoft.AspNetCore.Http.IFormFile> в примере приложения см. в описании метода `ProcessFormFile` в файле *Utilities/FileHelpers.cs*.</span><span class="sxs-lookup"><span data-stu-id="36bff-510">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="36bff-511">Сведения об обработке потоковых файлов см. в описании метода `ProcessStreamedFile` в том же файле.</span><span class="sxs-lookup"><span data-stu-id="36bff-511">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="36bff-512">Методы обработки проверки, показанные в примере приложения, не проверяют содержимое отправленных файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-512">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="36bff-513">В большинстве рабочих сценариев в файле применяется API сканирования на наличие вирусов и вредоносных программ, прежде чем сделать файл доступным для пользователей или других систем.</span><span class="sxs-lookup"><span data-stu-id="36bff-513">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="36bff-514">Хотя пример в разделе содержит рабочий пример методов проверки, не следует реализовывать класс `FileHelpers` в рабочем приложении, кроме таких случаев:</span><span class="sxs-lookup"><span data-stu-id="36bff-514">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="36bff-515">Вы полностью разбираетесь в реализации.</span><span class="sxs-lookup"><span data-stu-id="36bff-515">Fully understand the implementation.</span></span>
> * <span data-ttu-id="36bff-516">Вы изменяете реализацию соответствующим образом для среды и спецификаций приложения.</span><span class="sxs-lookup"><span data-stu-id="36bff-516">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="36bff-517">**Никогда не реализуйте код безопасности в приложении, не выполнив эти требования.**</span><span class="sxs-lookup"><span data-stu-id="36bff-517">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="36bff-518">Проверка содержимого</span><span class="sxs-lookup"><span data-stu-id="36bff-518">Content validation</span></span>

<span data-ttu-id="36bff-519">**Используйте сторонний API сканирования на наличие вирусов и вредоносных программ для отправленного содержимого.**</span><span class="sxs-lookup"><span data-stu-id="36bff-519">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="36bff-520">Сканирование файлов требует использования ресурсов сервера в сценариях с большими объемами данных.</span><span class="sxs-lookup"><span data-stu-id="36bff-520">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="36bff-521">Если производительность обработки запросов снижается из-за сканирования файлов, рассмотрите возможность разгрузки сканирования путем использования [фоновой службы](xref:fundamentals/host/hosted-services), возможно, службы, которая работает на сервере, отличном от сервера приложения.</span><span class="sxs-lookup"><span data-stu-id="36bff-521">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="36bff-522">Как правило, передаваемые файлы хранятся в карантинной области до тех пор, пока фоновый сканер не проверит их на наличие вирусов.</span><span class="sxs-lookup"><span data-stu-id="36bff-522">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="36bff-523">При передаче файл перемещается в нормальное место хранения файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-523">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="36bff-524">Эти действия обычно выполняются вместе с записью базы данных, которая указывает состояние сканирования файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-524">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="36bff-525">Благодаря такому подходу приложение и сервер приложений остаются в режиме реагирования на запросы.</span><span class="sxs-lookup"><span data-stu-id="36bff-525">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="36bff-526">Проверка расширения файла</span><span class="sxs-lookup"><span data-stu-id="36bff-526">File extension validation</span></span>

<span data-ttu-id="36bff-527">Расширение переданного файла должно проверяться в соответствии со списком разрешенных расширений.</span><span class="sxs-lookup"><span data-stu-id="36bff-527">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="36bff-528">Пример:</span><span class="sxs-lookup"><span data-stu-id="36bff-528">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="36bff-529">Проверка подписи файла</span><span class="sxs-lookup"><span data-stu-id="36bff-529">File signature validation</span></span>

<span data-ttu-id="36bff-530">Подпись файла определяется по первым нескольким байтам в начале файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-530">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="36bff-531">Эти байты можно использовать, чтобы указать, совпадает ли расширение с содержимым файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-531">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="36bff-532">Пример приложения проверяет подписи файлов на соответствие нескольким распространенным типам файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-532">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="36bff-533">В следующем примере проверяется подпись файла для изображения JPEG:</span><span class="sxs-lookup"><span data-stu-id="36bff-533">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="36bff-534">Дополнительные сведения о получении дополнительных подписей файлов см. [по этой ссылке](https://www.filesignatures.net/) и в официальных спецификациях файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-534">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="36bff-535">Безопасность имени файла</span><span class="sxs-lookup"><span data-stu-id="36bff-535">File name security</span></span>

<span data-ttu-id="36bff-536">Никогда не используйте для хранения файла в физическом хранилище имя, предоставляемое клиентом.</span><span class="sxs-lookup"><span data-stu-id="36bff-536">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="36bff-537">Создайте надежное имя файла с помощью [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) или [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), чтобы получить полный путь (включая имя файла) для временного хранилища.</span><span class="sxs-lookup"><span data-stu-id="36bff-537">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="36bff-538">автоматически кодирует значения свойств для вывода.</span><span class="sxs-lookup"><span data-stu-id="36bff-538"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="36bff-539">Ниже приведен безопасный код, который можно использовать.</span><span class="sxs-lookup"><span data-stu-id="36bff-539">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="36bff-540">За пределами Razor , всегда следует <xref:System.Net.WebUtility.HtmlEncode*> содержимое имени файла из запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="36bff-540">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="36bff-541">Во многих реализациях следует включать проверку существования файла. В противном случае файл перезаписывается файлом с тем же именем.</span><span class="sxs-lookup"><span data-stu-id="36bff-541">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="36bff-542">Предоставьте дополнительную логику для соответствия спецификациям приложения.</span><span class="sxs-lookup"><span data-stu-id="36bff-542">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="36bff-543">Проверка размера</span><span class="sxs-lookup"><span data-stu-id="36bff-543">Size validation</span></span>

<span data-ttu-id="36bff-544">Ограничьте размер передаваемых файлов.</span><span class="sxs-lookup"><span data-stu-id="36bff-544">Limit the size of uploaded files.</span></span>

<span data-ttu-id="36bff-545">В примере приложения размер файла ограничен 2 МБ (указывается в байтах).</span><span class="sxs-lookup"><span data-stu-id="36bff-545">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="36bff-546">Ограничение предоставляется с помощью [конфигурации](xref:fundamentals/configuration/index) из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="36bff-546">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="36bff-547">В классы `PageModel` внедряется `FileSizeLimit`.</span><span class="sxs-lookup"><span data-stu-id="36bff-547">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="36bff-548">Если размер файла превышает ограничение, файл отклоняется:</span><span class="sxs-lookup"><span data-stu-id="36bff-548">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="36bff-549">Сопоставление значения атрибута имени и имени параметра метода POST</span><span class="sxs-lookup"><span data-stu-id="36bff-549">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="36bff-550">В других Razor формах, которые передают данные формы или используют JavaScript `FormData` непосредственно, имя, указанное в элементе формы, или `FormData` должно совпадать с именем параметра в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="36bff-550">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="36bff-551">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="36bff-551">In the following example:</span></span>

* <span data-ttu-id="36bff-552">При использовании элемента `<input>` атрибуту `name` присваивается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="36bff-552">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="36bff-553">При использовании `FormData` в JavaScript для имени задается значение `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="36bff-553">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="36bff-554">Используйте соответствующее имя для параметра метода C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="36bff-554">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="36bff-555">Для Razor метода обработчика страницы страниц с именем `Upload` :</span><span class="sxs-lookup"><span data-stu-id="36bff-555">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="36bff-556">Для метода действия контроллера POST приложения MVC:</span><span class="sxs-lookup"><span data-stu-id="36bff-556">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="36bff-557">Конфигурация сервера и приложения</span><span class="sxs-lookup"><span data-stu-id="36bff-557">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="36bff-558">Ограничение длины составного текста</span><span class="sxs-lookup"><span data-stu-id="36bff-558">Multipart body length limit</span></span>

<span data-ttu-id="36bff-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> устанавливает ограничение длины каждого составного текста.</span><span class="sxs-lookup"><span data-stu-id="36bff-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="36bff-560">Разделы формы, превышающие это ограничение, вызовут <xref:System.IO.InvalidDataException> при синтаксическом анализе.</span><span class="sxs-lookup"><span data-stu-id="36bff-560">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="36bff-561">Значение по умолчанию — 134 217 728 байт (128 МБ).</span><span class="sxs-lookup"><span data-stu-id="36bff-561">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="36bff-562">Настройте ограничение с помощью параметра <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36bff-562">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="36bff-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> используется для настройки <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="36bff-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="36bff-564">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="36bff-564">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="36bff-565">В Razor приложении "страницы" или в приложении MVC примените фильтр к модели страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="36bff-565">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="36bff-566">Максимальный размер текста запроса Kestrel</span><span class="sxs-lookup"><span data-stu-id="36bff-566">Kestrel maximum request body size</span></span>

<span data-ttu-id="36bff-567">Для приложений, размещенных в Kestrel, по умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="36bff-567">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="36bff-568">Настройте ограничение с помощью параметра сервера Kestrel [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size):</span><span class="sxs-lookup"><span data-stu-id="36bff-568">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="36bff-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> используется для настройки [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) для одной страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="36bff-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="36bff-570">В Razor приложении "страницы" примените фильтр с [соглашением](xref:razor-pages/razor-pages-conventions) в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="36bff-570">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="36bff-571">В Razor приложении Pages или MVC примените фильтр к классу обработчика страницы или методу действия:</span><span class="sxs-lookup"><span data-stu-id="36bff-571">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="36bff-572">Другие ограничения Kestrel</span><span class="sxs-lookup"><span data-stu-id="36bff-572">Other Kestrel limits</span></span>

<span data-ttu-id="36bff-573">Другие ограничения Kestrel могут применяться для приложений, размещенных в Kestrel:</span><span class="sxs-lookup"><span data-stu-id="36bff-573">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="36bff-574">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="36bff-574">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="36bff-575">Скорость передачи данных запросов и ответов</span><span class="sxs-lookup"><span data-stu-id="36bff-575">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="36bff-576">Ограничение длины содержимого IIS</span><span class="sxs-lookup"><span data-stu-id="36bff-576">IIS content length limit</span></span>

<span data-ttu-id="36bff-577">По умолчанию ограничение запроса (`maxAllowedContentLength`) составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="36bff-577">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="36bff-578">Ограничение можно настроить в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="36bff-578">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="36bff-579">Этот параметр применим только к службам IIS.</span><span class="sxs-lookup"><span data-stu-id="36bff-579">This setting only applies to IIS.</span></span> <span data-ttu-id="36bff-580">При размещении в Kestrel такой проблемы возникать не должно.</span><span class="sxs-lookup"><span data-stu-id="36bff-580">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="36bff-581">Дополнительные сведения см. в статье [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/) (Ограничения запроса <requestLimits>).</span><span class="sxs-lookup"><span data-stu-id="36bff-581">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="36bff-582">Ограничения в модуле ASP.NET Core или наличие модуля фильтрации запросов IIS могут ограничить передачу до 2 или 4 ГБ.</span><span class="sxs-lookup"><span data-stu-id="36bff-582">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="36bff-583">Дополнительные сведения см. в статье форума (dotnet/AspNetCore #2711) [Unable to upload file greater than 2GB in size](https://github.com/dotnet/AspNetCore/issues/2711) (Не удалось отправить файл размером более 2 ГБ).</span><span class="sxs-lookup"><span data-stu-id="36bff-583">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="36bff-584">Диагностика</span><span class="sxs-lookup"><span data-stu-id="36bff-584">Troubleshoot</span></span>

<span data-ttu-id="36bff-585">Ниже описываются некоторые распространенные проблемы, которые возникают при передаче файлов, и возможные способы их решения.</span><span class="sxs-lookup"><span data-stu-id="36bff-585">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="36bff-586">Ошибка "Не найдено" при развертывании на сервере IIS</span><span class="sxs-lookup"><span data-stu-id="36bff-586">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="36bff-587">Следующая ошибка свидетельствует о том, что размер передаваемых файлов превышает настроенное на сервере ограничение длины содержимого:</span><span class="sxs-lookup"><span data-stu-id="36bff-587">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="36bff-588">Дополнительные сведения об увеличении предела см. в разделе [Ограничение длины содержимого IIS](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="36bff-588">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="36bff-589">Сбой подключения</span><span class="sxs-lookup"><span data-stu-id="36bff-589">Connection failure</span></span>

<span data-ttu-id="36bff-590">Ошибка соединения и сброс соединения с сервером, скорее всего, означают, что размер отправленного файла превышает максимальную длину текста запроса Kestrel.</span><span class="sxs-lookup"><span data-stu-id="36bff-590">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="36bff-591">Дополнительные сведения см. в разделе [Максимальный размер текста запроса Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="36bff-591">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="36bff-592">Может также потребоваться настроить ограничения подключений клиентов Kestrel.</span><span class="sxs-lookup"><span data-stu-id="36bff-592">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="36bff-593">Исключение, связанное с пустой ссылкой в IFormFile</span><span class="sxs-lookup"><span data-stu-id="36bff-593">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="36bff-594">Если контроллер принимает передаваемые файлы с помощью <xref:Microsoft.AspNetCore.Http.IFormFile>, но значение равно `null`, проверьте, указан ли в HTML-форме атрибут `enctype` со значением `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="36bff-594">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="36bff-595">Если этот атрибут не задан для элемента `<form>`, передача файлов происходить не будет и все связанные аргументы <xref:Microsoft.AspNetCore.Http.IFormFile> будут иметь значение `null`.</span><span class="sxs-lookup"><span data-stu-id="36bff-595">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="36bff-596">Убедитесь также, что [именование передачи в данных формы совпадает с именованием приложения](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="36bff-596">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="36bff-597">Поток слишком длинный</span><span class="sxs-lookup"><span data-stu-id="36bff-597">Stream was too long</span></span>

<span data-ttu-id="36bff-598">В примерах в этом разделе <xref:System.IO.MemoryStream> используется для хранения содержимого отправленного файла.</span><span class="sxs-lookup"><span data-stu-id="36bff-598">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="36bff-599">Максимальный размер `MemoryStream` ограничен значением `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="36bff-599">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="36bff-600">Если в сценарии передачи файлов в приложении требуется хранить содержимое, размер которого больше 50 МБ, используйте другой подход, не зависящий от одного только `MemoryStream`.</span><span class="sxs-lookup"><span data-stu-id="36bff-600">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="36bff-601">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="36bff-601">Additional resources</span></span>

* [<span data-ttu-id="36bff-602">Сток запросов HTTP-подключения</span><span class="sxs-lookup"><span data-stu-id="36bff-602">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="36bff-603">Неограниченная отправка файлов</span><span class="sxs-lookup"><span data-stu-id="36bff-603">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="36bff-604">Безопасность Azure: кадр безопасности: Проверка входных данных | Способы их устранения</span><span class="sxs-lookup"><span data-stu-id="36bff-604">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="36bff-605">Шаблоны разработки в облаке Azure: шаблон камердинера Key</span><span class="sxs-lookup"><span data-stu-id="36bff-605">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
