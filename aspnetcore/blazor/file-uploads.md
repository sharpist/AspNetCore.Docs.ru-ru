---
title: Отправка файлов в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как отправлять файлы в Blazor с помощью компонента InputFile.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
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
uid: blazor/file-uploads
ms.openlocfilehash: 06d1464cb731a8008362fc911f463e4ff8a37b6b
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606659"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="c95e7-103">Отправка файлов в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c95e7-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="c95e7-104">Авторы: [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth) и [Пранав Кришнамурти](https://github.com/pranavkm) (Pranav Krishnamoorthy)</span><span class="sxs-lookup"><span data-stu-id="c95e7-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="c95e7-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c95e7-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c95e7-106">Используйте компонент `InputFile` для считывания данных файла браузера в код .NET, в том числе для отправки файлов.</span><span class="sxs-lookup"><span data-stu-id="c95e7-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="c95e7-107">Всегда следуйте рекомендациям по безопасности при отправке файлов.</span><span class="sxs-lookup"><span data-stu-id="c95e7-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="c95e7-108">Для получения дополнительной информации см. <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="c95e7-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="c95e7-109">`InputFile`</span><span class="sxs-lookup"><span data-stu-id="c95e7-109">`InputFile` component</span></span>

<span data-ttu-id="c95e7-110">Компонент `InputFile` отрисовывается как входные данные HTML типа `file`.</span><span class="sxs-lookup"><span data-stu-id="c95e7-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="c95e7-111">По умолчанию пользователь выбирает отдельные файлы.</span><span class="sxs-lookup"><span data-stu-id="c95e7-111">By default, the user selects single files.</span></span> <span data-ttu-id="c95e7-112">Добавьте атрибут `multiple`, чтобы разрешить пользователю отправлять несколько файлов одновременно.</span><span class="sxs-lookup"><span data-stu-id="c95e7-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="c95e7-113">Когда пользователь выбирает один или несколько файлов, компонент `InputFile` вызывает событие `OnChange` и передает `InputFileChangeEventArgs`, который предоставляет доступ к выбранному списку файлов и сведениям о каждом файле.</span><span class="sxs-lookup"><span data-stu-id="c95e7-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="c95e7-114">Чтобы считать данные из выбранного пользователем файла, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="c95e7-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="c95e7-115">Вызовите `OpenReadStream` для файла и считайте данные из возвращенного потока.</span><span class="sxs-lookup"><span data-stu-id="c95e7-115">Call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="c95e7-116">Дополнительные сведения см. в разделе [Файловые потоки](#file-streams).</span><span class="sxs-lookup"><span data-stu-id="c95e7-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="c95e7-117">Используйте ключевое слово `ReadAsync`.</span><span class="sxs-lookup"><span data-stu-id="c95e7-117">Use `ReadAsync`.</span></span> <span data-ttu-id="c95e7-118">По умолчанию `ReadAsync` допускает чтение файла размером менее 524 288 КБ (512 КБ).</span><span class="sxs-lookup"><span data-stu-id="c95e7-118">By default, `ReadAsync` only allows reading a file smaller than 524,288 KB (512 KB) in size.</span></span> <span data-ttu-id="c95e7-119">Это ограничение позволяет предотвратить случайное считывание больших файлов в память.</span><span class="sxs-lookup"><span data-stu-id="c95e7-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="c95e7-120">Если должно поддерживаться считывание файлов большего размера, укажите разумный максимальный ожидаемый размер файла.</span><span class="sxs-lookup"><span data-stu-id="c95e7-120">Specify a reasonable approximation for the maximum expected file size if larger files must be supported.</span></span> <span data-ttu-id="c95e7-121">Избегайте считывания входящего файлового потока непосредственно в память.</span><span class="sxs-lookup"><span data-stu-id="c95e7-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="c95e7-122">Например, не копируйте байты файлов в <xref:System.IO.MemoryStream> и не считывайте файлы как массив байтов.</span><span class="sxs-lookup"><span data-stu-id="c95e7-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="c95e7-123">Это может привести к проблемам с производительностью и безопасностью, особенно в Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="c95e7-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="c95e7-124">Вместо этого рекомендуется копировать байты файлов во внешнее хранилище, например в большой двоичный объект или файл на диске.</span><span class="sxs-lookup"><span data-stu-id="c95e7-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="c95e7-125">Компонент, получающий файл изображения, может вызвать для файла удобный метод `RequestImageFileAsync`, чтобы изменить размер данных изображения в среде выполнения JavaScript браузера до передачи изображения в приложение.</span><span class="sxs-lookup"><span data-stu-id="c95e7-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="c95e7-126">В приведенном ниже примере показана отправка нескольких файлов изображений в компоненте.</span><span class="sxs-lookup"><span data-stu-id="c95e7-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="c95e7-127">`InputFileChangeEventArgs.GetMultipleFiles` позволяет считывать несколько файлов.</span><span class="sxs-lookup"><span data-stu-id="c95e7-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="c95e7-128">Укажите максимальное число считываемых файлов, чтобы предотвратить отправку злоумышленником большего количества файлов, чем то, на которое рассчитано приложение.</span><span class="sxs-lookup"><span data-stu-id="c95e7-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="c95e7-129">`InputFileChangeEventArgs.File` позволяет считывать только первый файл, если отправка нескольких файлов не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="c95e7-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="c95e7-130">`IBrowserFile` возвращает метаданные, [предоставленные браузером](https://developer.mozilla.org/docs/Web/API/File#Instance_properties), как свойства.</span><span class="sxs-lookup"><span data-stu-id="c95e7-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="c95e7-131">Эти метаданные могут быть полезны для предварительной проверки.</span><span class="sxs-lookup"><span data-stu-id="c95e7-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="c95e7-132">Например, см. [примеры компонентов `FileUpload.razor` и `FilePreview.razor`](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="c95e7-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="c95e7-133">Файловые потоки</span><span class="sxs-lookup"><span data-stu-id="c95e7-133">File streams</span></span>

<span data-ttu-id="c95e7-134">В приложении Blazor WebAssembly данные передаются непосредственно в код .NET в браузере.</span><span class="sxs-lookup"><span data-stu-id="c95e7-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="c95e7-135">В приложении Blazor Server данные файлов передаются через подключение SignalR в код .NET на сервере, так как файл считывается из потока.</span><span class="sxs-lookup"><span data-stu-id="c95e7-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="c95e7-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) позволяет настраивать характеристики отправки файлов для Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="c95e7-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c95e7-137">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c95e7-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
