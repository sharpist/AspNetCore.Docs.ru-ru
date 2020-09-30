---
title: Отправка файлов в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как отправлять файлы в Blazor с помощью компонента InputFile.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
ms.openlocfilehash: de4654f2efc401143e066628b096052efa65d7a0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722945"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="9287d-103">Отправка файлов в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="9287d-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="9287d-104">Автор: [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="9287d-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9287d-105">Используйте компонент `InputFile` для считывания данных файла браузера в код .NET, в том числе для отправки файлов.</span><span class="sxs-lookup"><span data-stu-id="9287d-105">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span> <span data-ttu-id="9287d-106">Компонент `InputFile` отрисовывается как входные данные HTML типа `file`.</span><span class="sxs-lookup"><span data-stu-id="9287d-106">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="9287d-107">По умолчанию пользователь выбирает отдельные файлы.</span><span class="sxs-lookup"><span data-stu-id="9287d-107">By default, the user selects single files.</span></span> <span data-ttu-id="9287d-108">Добавьте атрибут `multiple`, чтобы разрешить пользователю отправлять несколько файлов одновременно.</span><span class="sxs-lookup"><span data-stu-id="9287d-108">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="9287d-109">Когда пользователь выбирает один или несколько файлов, компонент `InputFile` вызывает событие `OnChange` и передает `InputFileChangeEventArgs`, который предоставляет доступ к выбранному списку файлов и сведениям о каждом файле.</span><span class="sxs-lookup"><span data-stu-id="9287d-109">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="9287d-110">Компонент, получающий файл изображения, может вызвать для файла удобный метод `RequestImageFileAsync`, чтобы изменить размер данных изображения в среде выполнения JavaScript браузера до передачи изображения в приложение.</span><span class="sxs-lookup"><span data-stu-id="9287d-110">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="9287d-111">В следующем примере показана отправка нескольких файлов изображений в компоненте:</span><span class="sxs-lookup"><span data-stu-id="9287d-111">The following example demonstrates multiple image file upload in a component:</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h3>Images</h3>

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
        var imageFiles = e.GetMultipleFiles();
        var format = "image/png";

        foreach (var imageFile in imageFiles)
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

<span data-ttu-id="9287d-112">Чтобы считать данные из выбранного пользователем файла, вызовите `OpenReadStream` для файла и считайте данные из возвращенного потока.</span><span class="sxs-lookup"><span data-stu-id="9287d-112">To read data from a user-selected file, call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="9287d-113">В приложении Blazor WebAssembly данные передаются непосредственно в код .NET в браузере.</span><span class="sxs-lookup"><span data-stu-id="9287d-113">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span> <span data-ttu-id="9287d-114">В приложении Blazor Server данные файлов передаются в код .NET на сервере, так как файл считывается из потока.</span><span class="sxs-lookup"><span data-stu-id="9287d-114">In a Blazor Server app, the file data is streamed into .NET code on the server as the file is read from the stream.</span></span> 
