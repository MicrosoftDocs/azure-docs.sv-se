---
title: Azure snabbstart – Skapa en blob i objektlagring med hjälp av Go | Microsoft Docs
description: I den här snabbstarten skapar du ett lagringskonto och en container i objektlagring (Blob). Sedan använder du lagringsklientbiblioteket för Go och laddar upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en container.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: bf0e868e9ee746da1dfe1b03403d21f7edb3bd5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544657"
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>Snabbstart: Ladda upp, ladda ned och lista blobar med Go

I den här snabbstarten får du lära dig att använda Go-programmeringsspråket för att ladda upp, hämta och lista blockblobar i en container i Azure Blob-lagring. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Kontrollera att du har följande ytterligare krav installerade:
 
* [Go 1.8 eller senare](https://golang.org/dl/)
* [Azure Storage Blob SDK for Go](https://github.com/azure/azure-storage-blob-go/) med följande kommando:

    ```
    go get -u github.com/Azure/azure-storage-blob-go/azblob
    ``` 

    > [!NOTE]
    > Se till att du använder versaler `Azure` i webbadressen för att undvika ärenderelaterade importproblem när du arbetar med SDK:n. Använd även versaler för `Azure` i importinstruktioner.
    
## <a name="download-the-sample-application"></a>Hämta exempelprogrammet
[Exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) som används i den här snabbstarten är ett enkelt Go-program.  

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

Det här kommandot klonar lagret till den lokala git-mappen. När du vill öppna Go-exemplet för Blob Storage ska du leta efter filen storage-quickstart.go.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
Den här lösningen kräver att ditt lagringskontonamn och nyckel är säkert lagrade i miljövariabler som ligger lokalt på datorn som kör exemplet. Följ något av exemplen nedan beroende på operativsystemet för att skapa miljövariabeln.

# <a name="linux"></a>[Linux](#tab/linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windows"></a>[Windows](#tab/windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>Kör exemplet
Exemplet skapar en testfil i den aktuella mappen, laddar upp testfilen till Blob Storage, listar blobarna i containern och laddar ned filen till en buffert. 

Om du vill köra exemplet kör du följande kommando: 

```go run storage-quickstart.go```

Följande utdata är ett exempel på utdata som returneras när programmet körs:
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
När du trycker på valfri tangent för att fortsätta tar exempelprogrammet bort containern och filerna. 

> [!TIP]
> Du kan också använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com) för att visa filerna i blobblagringen. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som gör det möjligt att komma åt information på lagringskontot. 
>

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst går vi igenom exempelkoden så att du kan förstå hur den fungerar.

### <a name="create-containerurl-and-bloburl-objects"></a>Skapa ContainerURL- och BlobURL-objekt
Det första du ska göra är att skapa referenser till ContainerURL- och BlobURL-objekten som används för att komma åt och hantera Blob Storage. De här objekten erbjuder lågnivå-API:er som Create (Skapa), Upload (Ladda upp) och Download (Ladda ned) för att utfärda REST-API:er.

* Använd struct-datatypen [**SharedKeyCredential**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#SharedKeyCredential) för att lagra autentiseringsuppgifterna. 

* Skapa en [**pipeline**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#NewPipeline) med autentiseringsuppgifterna och alternativen. Pipelinen specificerar saker som principer för återförsök, loggning, deserialisering av HTTP-nyttolaster för svar med mera.  

* Instansiera en ny [**ContainerURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#ContainerURL) och ett nytt [**BlobURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#BlobURL)-objekt som ska köra åtgärder på containern (Create) och blobar (Upload och Download).


När du har ContainerURL kan du instansiera **BlobURL**-objektet som pekar på en blob och vidta åtgärder som uppladdning, nedladdning och kopiering.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om containrar och blobnamn finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

I det här avsnittet skapar du en ny container. Containern heter **quickstartblobs-[random string]**. 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential, err := azblob.NewSharedKeyCredential(accountName, accountKey)
if err != nil {
    log.Fatal("Invalid credentials with error: " + err.Error())
}
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err = containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i containern

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobar är vanligast och används i denna snabbstart.  

Om du vill ladda upp en fil till en blob öppnar du filen med **os.Open**. Du kan därefter ladda upp filen till den angivna sökvägen med någon av REST API:erna: Upload (PutBlob), StageBlock/CommitBlockList (PutBlock/PutBlockList). 

SDK:erna erbjuder även [högnivå-API:er](https://github.com/Azure/azure-storage-blob-go/blob/master/azblob/highlevel.go) som är skapade ovanpå lågnivå-REST API:erna. Som ett exempel på detta använder funktionen ***UploadFileToBlockBlob*** StageBlock (PutBlock)-åtgärder för att ladda upp en fil i segment för att optimera dataflödet samtidigt. Om filen är mindre än 256 MB använder den Upload (PutBlob) i stället för att slutföra överföringen i en enda transaktion.

I följande exempel överförs filen till containern med namnet **quickstartblobs-[randomstring]**.

```go
// Create a file to test the upload and download.
fmt.Printf("Creating a dummy file to test the upload and download\n")
data := []byte("hello world this is a blob\n")
fileName := randomString()
err = ioutil.WriteFile(fileName, data, 0700)
handleErrors(err)

// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level Upload (PutBlob) API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that Upload can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// To upload more than 256MB, use StageBlock (PutBlock) and CommitBlockList (PutBlockList) functions. 
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.Upload(ctx, file, azblob.BlobHTTPHeaders{ContentType: "text/plain"}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls StageBlock/CommitBlockList for files larger 256 MBs, and calls Upload for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Hämta en lista över filer i behållaren med hjälp av metoden **ListBlobs** på en **ContainerURL**. ListBlobs returnerar ett enda segment med blobar (upp till 5000) från den angivna **markören**. Använd en tom markör för att börja uppräkningen från början. Blobnamn returneras i lexikografisk ordning. När du har hämtat ett segment ska du behandla det och sedan anropa ListBlobs igen och skicka den tidigare returnerade markören.  

```go
// List the container that we have created above
fmt.Println("Listing the blobs in the container:")
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobsFlatSegment(ctx, marker, azblob.ListBlobsSegmentOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Segment.BlobItems {
        fmt.Print(" Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Ladda ned bloben

Ladda ned blobar med hjälp av **Download**-lågnivåfunktionen på en BlobURL. Detta returnerar struct-datatypen **DownloadResponse**. Kör funktionen **Body** på struct-datatypen för att få en **RetryReader**-ström för läsning av data. Om anslutningen Miss lyckas under läsningen, kommer den att göra ytterligare förfrågningar för att återupprätta anslutningen och fortsätta att läsa. Om du anger en RetryReaderOption med MaxRetryRequests inställt på 0 (standardvärdet) returneras den ursprungliga svarstexten, och det utförs inga nya försök. Alternativt kan du använda de högnivå-API:erna **DownloadBlobToBuffer** eller **DownloadBlobToFile** för att förenkla koden.

Följande kod laddar ned bloben med hjälp av funktionen **Download**. Blobens innehåll skrivs till en buffert och visas på konsolen.

```go
// Here's how to download the blob
downloadResponse, err := blobURL.Download(ctx, 0, azblob.CountToEnd, azblob.BlobAccessConditions{}, false)

// NOTE: automatically retries are performed if the connection fails
bodyStream := downloadResponse.Body(azblob.RetryReaderOptions{MaxRetryRequests: 20})

// read the body into a buffer
downloadedData := bytes.Buffer{}
_, err = downloadedData.ReadFrom(bodyStream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver blobarna som laddades upp i denna snabbstart kan du ta bort hela containern med hjälp av **Delete**. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>Resurser för att utveckla Go-program med blobar

Följande extra resurser för Go-utveckling med Blob Storage finns:

- Visa och installera [Go-klientbibliotekets källkod](https://github.com/Azure/azure-storage-blob-go) för Azure Storage på GitHub.
- Utforska [Bloblagringsexempel](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#pkg-examples) som skrivits med Go-klientbiblioteket.

## <a name="next-steps"></a>Nästa steg
 
I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med Go. Mer information om Azure Storage Blob SDK finns i [källkoden](https://github.com/Azure/azure-storage-blob-go/) och [API-referensen](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob).