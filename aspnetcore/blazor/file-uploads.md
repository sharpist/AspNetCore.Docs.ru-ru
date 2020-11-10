---
title: 'Отправка файлов в ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Узнайте, как отправлять файлы в :::no-loc(Blazor)::: с помощью компонента InputFile.'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: c0806c3a68a4d9e698925f6ec955dd2f53d7818f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056131"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="a44f5-103">Отправка файлов в ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="a44f5-103">ASP.NET Core :::no-loc(Blazor)::: file uploads</span></span>

<span data-ttu-id="a44f5-104">Авторы: [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth) и [Пранав Кришнамурти](https://github.com/pranavkm) (Pranav Krishnamoorthy)</span><span class="sxs-lookup"><span data-stu-id="a44f5-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="a44f5-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a44f5-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a44f5-106">Используйте компонент `InputFile` для считывания данных файла браузера в код .NET, в том числе для отправки файлов.</span><span class="sxs-lookup"><span data-stu-id="a44f5-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="a44f5-107">Всегда следуйте рекомендациям по безопасности при отправке файлов.</span><span class="sxs-lookup"><span data-stu-id="a44f5-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="a44f5-108">Для получения дополнительной информации см. <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="a44f5-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="a44f5-109">`InputFile`</span><span class="sxs-lookup"><span data-stu-id="a44f5-109">`InputFile` component</span></span>

<span data-ttu-id="a44f5-110">Компонент `InputFile` отрисовывается как входные данные HTML типа `file`.</span><span class="sxs-lookup"><span data-stu-id="a44f5-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="a44f5-111">По умолчанию пользователь выбирает отдельные файлы.</span><span class="sxs-lookup"><span data-stu-id="a44f5-111">By default, the user selects single files.</span></span> <span data-ttu-id="a44f5-112">Добавьте атрибут `multiple`, чтобы разрешить пользователю отправлять несколько файлов одновременно.</span><span class="sxs-lookup"><span data-stu-id="a44f5-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="a44f5-113">Когда пользователь выбирает один или несколько файлов, компонент `InputFile` вызывает событие `OnChange` и передает `InputFileChangeEventArgs`, который предоставляет доступ к выбранному списку файлов и сведениям о каждом файле.</span><span class="sxs-lookup"><span data-stu-id="a44f5-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="a44f5-114">Чтобы считать данные из выбранного пользователем файла, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="a44f5-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="a44f5-115">Вызовите `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` для файла и считайте данные из возвращенного потока.</span><span class="sxs-lookup"><span data-stu-id="a44f5-115">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="a44f5-116">Дополнительные сведения см. в разделе [Файловые потоки](#file-streams).</span><span class="sxs-lookup"><span data-stu-id="a44f5-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="a44f5-117">Значение <xref:System.IO.Stream>, возвращаемое `OpenReadStream`, определяет максимальный размер (в байтах) для считываемого потока (`Stream`).</span><span class="sxs-lookup"><span data-stu-id="a44f5-117">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="a44f5-118">Как правило, считываются только файлы размером менее 524,288 КБ (512 КБ). При попытке считать любой файл больше этого размера будет возвращаться исключение.</span><span class="sxs-lookup"><span data-stu-id="a44f5-118">By default, only files smaller than 524,288 KB (512 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="a44f5-119">Это ограничение позволяет предотвратить случайное считывание больших файлов в память.</span><span class="sxs-lookup"><span data-stu-id="a44f5-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="a44f5-120">При необходимости можно указать больший размер с помощью параметра `maxAllowedSize` в `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`.</span><span class="sxs-lookup"><span data-stu-id="a44f5-120">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="a44f5-121">Избегайте считывания входящего файлового потока непосредственно в память.</span><span class="sxs-lookup"><span data-stu-id="a44f5-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="a44f5-122">Например, не копируйте байты файлов в <xref:System.IO.MemoryStream> и не считывайте файлы как массив байтов.</span><span class="sxs-lookup"><span data-stu-id="a44f5-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="a44f5-123">Это может привести к проблемам с производительностью и безопасностью, особенно в :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="a44f5-123">These approaches can result in performance and security problems, especially in :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="a44f5-124">Вместо этого рекомендуется копировать байты файлов во внешнее хранилище, например в большой двоичный объект или файл на диске.</span><span class="sxs-lookup"><span data-stu-id="a44f5-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="a44f5-125">Компонент, получающий файл изображения, может вызвать для файла удобный метод `RequestImageFileAsync`, чтобы изменить размер данных изображения в среде выполнения JavaScript браузера до передачи изображения в приложение.</span><span class="sxs-lookup"><span data-stu-id="a44f5-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="a44f5-126">В приведенном ниже примере показана отправка нескольких файлов изображений в компоненте.</span><span class="sxs-lookup"><span data-stu-id="a44f5-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="a44f5-127">`InputFileChangeEventArgs.GetMultipleFiles` позволяет считывать несколько файлов.</span><span class="sxs-lookup"><span data-stu-id="a44f5-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="a44f5-128">Укажите максимальное число считываемых файлов, чтобы предотвратить отправку злоумышленником большего количества файлов, чем то, на которое рассчитано приложение.</span><span class="sxs-lookup"><span data-stu-id="a44f5-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="a44f5-129">`InputFileChangeEventArgs.File` позволяет считывать только первый файл, если отправка нескольких файлов не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="a44f5-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

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

<span data-ttu-id="a44f5-130">`IBrowserFile` возвращает метаданные, [предоставленные браузером](https://developer.mozilla.org/docs/Web/API/File#Instance_properties), как свойства.</span><span class="sxs-lookup"><span data-stu-id="a44f5-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="a44f5-131">Эти метаданные могут быть полезны для предварительной проверки.</span><span class="sxs-lookup"><span data-stu-id="a44f5-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="a44f5-132">Например, см. [примеры компонентов `FileUpload.razor` и `FilePreview.razor`](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="a44f5-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="a44f5-133">Файловые потоки</span><span class="sxs-lookup"><span data-stu-id="a44f5-133">File streams</span></span>

<span data-ttu-id="a44f5-134">В приложении :::no-loc(Blazor WebAssembly)::: данные передаются непосредственно в код .NET в браузере.</span><span class="sxs-lookup"><span data-stu-id="a44f5-134">In a :::no-loc(Blazor WebAssembly)::: app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="a44f5-135">В приложении :::no-loc(Blazor Server)::: данные файлов передаются через подключение :::no-loc(SignalR)::: в код .NET на сервере, так как файл считывается из потока.</span><span class="sxs-lookup"><span data-stu-id="a44f5-135">In a :::no-loc(Blazor Server)::: app, the file data is streamed over the :::no-loc(SignalR)::: connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="a44f5-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) позволяет настраивать характеристики отправки файлов для :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="a44f5-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for :::no-loc(Blazor Server):::.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a44f5-137">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a44f5-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
