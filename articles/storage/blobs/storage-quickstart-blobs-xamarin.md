---
title: 'Snabb start: Azure Blob Storage Library V12 – Xamarin'
description: I den här snabb starten får du lära dig hur du använder Azure-Blob Storage klient bibliotek version 12 med Xamarin för att skapa en behållare och en BLOB i blob-lagring (objekt). Härnäst får du lära dig hur du laddar ned blobben till din mobila enhet och hur du visar alla blobar i en behållare.
author: codemillmatt
ms.author: masoucou
ms.date: 10/09/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c6b502bbc56d6c9521ec736150fbc0046ff78125
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642484"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>Snabb start: Azure Blob Storage klient bibliotek V12 med Xamarin

Kom igång med klient biblioteket för Azure Blob Storage-V12 med Xamarin. Azure Blob Storage är Microsofts objekt lagrings lösning för molnet. Följ stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Blob Storage är optimerad för lagring av enorma mängder ostrukturerade data.

Använd klient biblioteket för Azure Blob Storage-V12 med Xamarin för att:

* Skapa en container
* Ladda upp en blob till Azure Storage
* Lista alla blobar i en behållare
* Ladda ned blobben till din enhet
* Ta bort en container

Referens länkar:

* [Referensdokumentation för API](/dotnet/api/azure.storage.blobs)
* [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Exempel](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Azure Storage-konto – [skapa ett lagrings konto](../common/storage-account-create.md)
* Visual Studio med [mobil utveckling för .net-arbetsbelastningar](/xamarin/get-started/installation/?pivots=windows) installerade eller [Visual Studio för Mac](/visualstudio/mac/installation?view=vsmac-2019&preserve-view=true)

## <a name="setting-up"></a>Konfigurera
    
Det här avsnittet beskriver hur du förbereder ett projekt för att arbeta med Azure Blob Storage-V12 för klient bibliotek med Xamarin.
    
### <a name="create-the-project"></a>Skapa projektet

1. Öppna Visual Studio och skapa en tom Forms-app.
1. Namn: BlobQuickstartV12

### <a name="install-the-package"></a>Installera paketet

1. Högerklicka på din lösning i fönstret Solution Explorer och välj **Hantera NuGet-paket för lösningen**.
1. Sök efter **Azure. Storage. blobs** och installera den senaste stabila versionen i alla projekt i lösningen.

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Från katalogen **BlobQuickstartV12** :

1. Öppna filen *mainpage. XAML* i redigeraren
1. Ta bort allt mellan `<ContentPage></ContentPage>` elementen och Ersätt med följande:

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>Objekt modell

Azure Blob Storage optimeras för att lagra enorma mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagrings kontot
* En container på lagringskontot
* En BLOB i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande .NET-klasser för att interagera med dessa resurser:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): `BlobServiceClient` klassen låter dig ändra Azure Storage-resurser och blob-behållare.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): `BlobContainerClient` klassen låter dig ändra Azure Storage behållare och deras blobbar.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): `BlobClient` klassen låter dig ändra Azure Storage blobbar.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): `BlobDownloadInfo` klassen representerar egenskaper och innehåll som returneras från att hämta en blob.

## <a name="code-examples"></a>Kodexempel

I de här exempel kods tycken visar vi hur du utför följande uppgifter med Azure Blob Storage-klient biblioteket för .NET i en Xamarin. Forms-app:

* [Skapa variabler på klass nivå](#create-class-level-variables)
* [Skapa en container](#create-a-container)
* [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
* [Visa blobar i en container](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="create-class-level-variables"></a>Skapa variabler på klass nivå

Koden nedan deklarerar flera variabler på klass nivå. De behövde kommunicera med Azure Blob Storage i resten av det här exemplet.

Dessa är förutom anslutnings strängen för lagrings kontot som anges i avsnittet [Konfigurera din lagrings anslutnings sträng](#configure-your-storage-connection-string) .

Lägg till den här koden som variabler på klass nivå i filen *mainpage. XAML. cs* :

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>Skapa en container

Välj ett namn för den nya behållaren. Koden nedan lägger till ett GUID-värde till behållar namnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa en instans av klassen [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) . Anropa sedan [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) -metoden för att skapa behållaren i ditt lagrings konto.

Lägg till den här koden i filen *mainpage. XAML. cs* :

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodfragment:

1. Skapar en `MemoryStream` av text.
1. Överför texten till en BLOB genom att anropa [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) -funktionen i [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) -klassen och skicka den i både fil namnet och i `MemoryStream` texten. Den här metoden skapar bloben om den inte redan finns, och skriver över den om den finns.

Lägg till den här koden i filen *mainpage. XAML. cs* :

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Visa en lista över blobarna i behållaren genom att anropa metoden [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) . I det här fallet har endast en BLOB lagts till i behållaren, så List åtgärden returnerar bara den en blob.

Lägg till den här koden i filen *mainpage. XAML. cs* :

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned den tidigare skapade blobben genom att anropa [DownloadToAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) -metoden. I exempel koden kopieras `Stream` åter givningen av blobben först till en `MemoryStream` och sedan till en `StreamReader` så att texten kan visas.

Lägg till den här koden i filen *mainpage. XAML. cs* :

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar resurserna som skapats av appen genom att ta bort hela behållaren med hjälp av [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync).

Appen måste först bekräfta innan den tar bort blobben och containern. Det här är en bra chans att verifiera att resurserna har skapats korrekt innan de tas bort.

Lägg till den här koden i filen *mainpage. XAML. cs* :

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>Kör koden

När appen startas skapas först behållaren som den visas. Sedan måste du klicka på knapparna för att ladda upp, lista, Hämta blobarna och ta bort behållaren.

Om du vill köra appen i Windows trycker du på F5. Om du vill köra appen på Mac trycker du på cmd + RETUR.

Appen skriver till skärmen efter varje åtgärd. Utdata från appen liknar följande exempel:

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

Innan du påbörjar rensnings processen kontrollerar du att utdata från blobens innehåll på skärmen matchar det värde som överfördes.

När du har verifierat värdena bekräftar du uppfrågan för att ta bort behållaren och slutför demonstrationen.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig att ladda upp, ladda ned och lista blobar med Azure Blob Storage-V12 för klient bibliotek med Xamarin.

Om du vill se exempel appar för Blob Storage fortsätter du till:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 Xamarin-exempel](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* För självstudier, exempel, snabb starter och annan dokumentation går du till [Azure för mobil utvecklare](/azure/mobile-apps).
* Mer information om Xamarin finns i [komma igång med Xamarin](/xamarin/get-started/).
