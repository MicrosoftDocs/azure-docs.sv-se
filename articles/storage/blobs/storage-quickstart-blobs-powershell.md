---
title: Snabb start – skapa en blob med PowerShell
titleSuffix: Azure Storage
description: I den här snabbstarten använder du Azure PowerShell i objektlagring (Blob). Sedan använder du PowerShell och laddar upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en container.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 21fc7e6de38a98e4f0853cf513f9f3441c7e2106
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735360"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Snabb start: Ladda upp, ladda ned och lista blobar med PowerShell

Använd Azure PowerShell-modulen för att skapa och hantera Azure-resurser. Du kan skapa och hantera Azure-resurser via PowerShell-kommandoraden eller i skript. I den här guiden beskrivs hur du använder PowerShell för att överföra filer mellan en lokal disk och Azure Blob Storage.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att få åtkomst till Azure Storage. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Du kommer också att behöva rollen Storage BLOB data Contributor för att läsa, skriva och ta bort Azure Storage behållare och blobbar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här snabbstarten kräver Azure PowerShell-modulen Az version 0.7 eller senare. Kör `Get-InstalledModule -Name Az -AllVersions | select Name,Version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Skapa en container

Blobar laddas alltid upp till en container. Du kan ordna grupper av blobar på samma sätt som du ordnar filer i mappar på datorn.

Ange containernamnet och skapa sedan containern med hjälp av [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Ange behörigheten för `blob` att tillåta offentlig åtkomst av filerna. Containerns namn i det här exemplet är *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i containern

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. VHD-filer som stöder virtuella IaaS-datorer är sidblobar. Använd tilläggsblobar för loggning, till exempel när du vill skriva till en fil och sedan fortsätta att lägga till mer information. De flesta filer som lagras i Blob Storage är blockblobar. 

Om du vill ladda upp en fil till en blockblob ska du hämta en referens för containern och sedan hämta en referens för blockbloben i den containern. När du har en blobreferensen kan du ladda upp data till den med hjälp av [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent). Den här åtgärden skapar bloben om den inte finns eller skriver över bloben om den finns.

I följande exempel laddas *Image001.jpg* och *Image002.png* upp från mappen *D:\\_TestImages* på den lokala disken till containern som du skapade.

```powershell
# upload a file to the default account (inferred) access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image000.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload a file to the Hot access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 
  -StandardBlobTier Hot

# upload another file to the Cool access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
  -StandardBlobTier Cool

# upload a file to a folder to the Archive access tier
Set-AzStorageBlobContent -File "D:\_TestImages\foldername\Image003.jpg" `
  -Container $containerName `
  -Blob "Foldername/Image003.jpg" `
  -Context $ctx 
  -StandardBlobTier Archive
```

Ladda upp så många filer som du vill innan du fortsätter.

## <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Hämta en lista över blobar i containern med hjälp av [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob). Det här exemplet visar bara namnen på de blobar som har laddats upp.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned blobarna till den lokala disken. För varje blob du vill ladda ned anger du namnet och anropar [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent).

I det här exemplet hämtas blobarna till *D: \\ _TestImages \downloads* på den lokala disken. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Dataöverföring med AzCopy

Kommando rads verktyget AzCopy erbjuder högpresterande, skript bara data överföring för Azure Storage. Du kan använda AzCopy för att överföra data till och från Blob Storage och Azure Files. För ytterligare information om AzCopy v10, den senaste versionen av AzCopy, se [Kom igång med AZCopy](../common/storage-use-azcopy-v10.md). Mer information om hur du använder AzCopy-v10 med Blob Storage finns i [överföra data med AzCopy och Blob Storage](../common/storage-use-azcopy-v10.md#transfer-data).

I följande exempel används AzCopy för att ladda upp en lokal fil till en blob. Kom ihåg att ersätta exempel värden med dina egna värden:

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort alla resurser som du har skapat. Det enklaste sättet att ta bort tillgångarna är för att ta bort resursgruppen. Om du tar bort resursgruppen tas även alla resurser bort som ingår i gruppen. När du i följande exempel tar bort resursgruppen tas lagringskontot och själva resursgruppen bort.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten överför du filer mellan ett lokalt fil system och Azure Blob Storage. Om du vill lära dig mer om att arbeta med Blob Storage med hjälp av PowerShell kan du utforska Azure PowerShell exempel för Blob Storage.

> [!div class="nextstepaction"]
> [Azure PowerShell-exempel för Azure Blob Storage](storage-samples-blobs-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Referens för Microsoft Azure PowerShell Storage-cmdletar

* [Storage PowerShell cmdletar](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
