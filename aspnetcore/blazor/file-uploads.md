---
title: Отправка файлов в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как отправлять файлы в Blazor с помощью компонента InputFile.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: 77c2874eef788b8083758c087913a7a04c55fa2b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "94691174"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a>Отправка файлов в ASP.NET Core Blazor

Авторы: [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth) и [Пранав Кришнамурти](https://github.com/pranavkm) (Pranav Krishnamoorthy)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

Используйте компонент `InputFile` для считывания данных файла браузера в код .NET, в том числе для отправки файлов.

> [!WARNING]
> Всегда следуйте рекомендациям по безопасности при отправке файлов. Для получения дополнительной информации см. <xref:mvc/models/file-uploads#security-considerations>.

## <a name="inputfile-component"></a>`InputFile`

Компонент `InputFile` отрисовывается как входные данные HTML типа `file`.

По умолчанию пользователь выбирает отдельные файлы. Добавьте атрибут `multiple`, чтобы разрешить пользователю отправлять несколько файлов одновременно. Когда пользователь выбирает один или несколько файлов, компонент `InputFile` вызывает событие `OnChange` и передает `InputFileChangeEventArgs`, который предоставляет доступ к выбранному списку файлов и сведениям о каждом файле.

Чтобы считать данные из выбранного пользователем файла, выполните указанные ниже действия.

* Вызовите `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` для файла и считайте данные из возвращенного потока. Дополнительные сведения см. в разделе [Файловые потоки](#file-streams).
* Значение <xref:System.IO.Stream>, возвращаемое `OpenReadStream`, определяет максимальный размер (в байтах) для считываемого потока (`Stream`). По умолчанию считываются файлы размером менее 512 000 байт (500 КБ). При попытке считать любой файл больше этого размера будет возвращаться исключение. Это ограничение позволяет предотвратить случайное считывание больших файлов в память. При необходимости можно указать больший размер с помощью параметра `maxAllowedSize` в `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`.
* Избегайте считывания входящего файлового потока непосредственно в память. Например, не копируйте байты файлов в <xref:System.IO.MemoryStream> и не считывайте файлы как массив байтов. Это может привести к проблемам с производительностью и безопасностью, особенно в Blazor Server. Вместо этого рекомендуется копировать байты файлов во внешнее хранилище, например в большой двоичный объект или файл на диске.

Компонент, получающий файл изображения, может вызвать для файла удобный метод `RequestImageFileAsync`, чтобы изменить размер данных изображения в среде выполнения JavaScript браузера до передачи изображения в приложение.

В приведенном ниже примере показана отправка нескольких файлов изображений в компоненте. `InputFileChangeEventArgs.GetMultipleFiles` позволяет считывать несколько файлов. Укажите максимальное число считываемых файлов, чтобы предотвратить отправку злоумышленником большего количества файлов, чем то, на которое рассчитано приложение. `InputFileChangeEventArgs.File` позволяет считывать только первый файл, если отправка нескольких файлов не поддерживается.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> находится в пространстве имен <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName>, которое обычно является одним из пространств имен в файле `_Imports.razor` приложения.

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
    private IList<string> imageDataUrls = new List<string>();

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

`IBrowserFile` возвращает метаданные, [предоставленные браузером](https://developer.mozilla.org/docs/Web/API/File#Instance_properties), как свойства. Эти метаданные могут быть полезны для предварительной проверки. Например, см. [примеры компонентов `FileUpload.razor` и `FilePreview.razor`](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).

## <a name="file-streams"></a>Файловые потоки

В приложении Blazor WebAssembly данные передаются непосредственно в код .NET в браузере.

В приложении Blazor Server данные файлов передаются через подключение SignalR в код .NET на сервере, так как файл считывается из потока. [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) позволяет настраивать характеристики отправки файлов для Blazor Server.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
