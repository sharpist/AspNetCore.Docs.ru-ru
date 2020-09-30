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
# <a name="aspnet-core-no-locblazor-file-uploads"></a>Отправка файлов в ASP.NET Core Blazor

Автор: [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

Используйте компонент `InputFile` для считывания данных файла браузера в код .NET, в том числе для отправки файлов. Компонент `InputFile` отрисовывается как входные данные HTML типа `file`.

По умолчанию пользователь выбирает отдельные файлы. Добавьте атрибут `multiple`, чтобы разрешить пользователю отправлять несколько файлов одновременно. Когда пользователь выбирает один или несколько файлов, компонент `InputFile` вызывает событие `OnChange` и передает `InputFileChangeEventArgs`, который предоставляет доступ к выбранному списку файлов и сведениям о каждом файле.

Компонент, получающий файл изображения, может вызвать для файла удобный метод `RequestImageFileAsync`, чтобы изменить размер данных изображения в среде выполнения JavaScript браузера до передачи изображения в приложение.

В следующем примере показана отправка нескольких файлов изображений в компоненте:

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

Чтобы считать данные из выбранного пользователем файла, вызовите `OpenReadStream` для файла и считайте данные из возвращенного потока. В приложении Blazor WebAssembly данные передаются непосредственно в код .NET в браузере. В приложении Blazor Server данные файлов передаются в код .NET на сервере, так как файл считывается из потока. 
