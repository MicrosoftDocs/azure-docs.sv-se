---
title: Ladda upp VHD-filen till Azure DevTest Labs med AzCopy | Microsoft Docs
description: Den här artikeln innehåller en genom gång för att använda kommando rads verktyget AzCopy för att ladda upp en VHD-fil till ett labbs lagrings konto i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1d8ede0f78726b04ac862a00b559b8d42c3ed1cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88650782"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Ladda upp VHD-filen till Labbets lagrings konto med AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar som används för att etablera virtuella datorer. Följande steg vägleder dig genom att använda kommando rads verktyget AzCopy för att ladda upp en VHD-fil till ett labbs lagrings konto. När du har laddat upp VHD-filen visas några artiklar i [avsnittet Nästa steg](#next-steps) som illustrerar hur du skapar en anpassad avbildning från den uppladdade VHD-filen. Mer information om diskar och virtuella hård diskar i Azure finns i [Introduktion till Managed disks](../virtual-machines/managed-disks-overview.md)

> [!NOTE] 
>  
> AzCopy är ett kommando rads verktyg med endast Windows.

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Följande steg beskriver hur du laddar upp en VHD-fil för att Azure DevTest Labs med [AzCopy](https://aka.ms/downloadazcopy). 

1. Hämta namnet på Labbets lagrings konto med hjälp av Azure Portal:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.

1. I listan med labb väljer du önskat labb.  

1. På labb bladet väljer du **konfiguration**. 

1. På bladet labb **konfiguration** väljer du **anpassade avbildningar (VHD)**.

1. På bladet **anpassade bilder** väljer du **+ Lägg till**. 

1. På bladet **anpassad avbildning** väljer du **VHD**.

1. På **VHD** -bladet väljer du **överför en virtuell hård disk med PowerShell**.

    ![Ladda upp VHD med PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. I bladet **Ladda upp en bild med PowerShell** visas ett anrop till cmdleten **Add-AzureVhd** . Den första parametern (*målet*) innehåller URI: n för en BLOB-behållare (*uppladdningar*) i följande format:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Anteckna hela URI-filen som den används i senare steg.

1. Överför VHD-filen med AzCopy:
 
1. [Hämta och installera den senaste versionen av AzCopy](https://aka.ms/downloadazcopy).

1. Öppna ett kommando fönster och navigera till installations katalogen för AzCopy. Alternativt kan du lägga till installations platsen för AzCopy till din system Sök väg. Som standard installeras AzCopy i följande katalog:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Kör följande kommando i kommando tolken med hjälp av lagrings konto nyckeln och URI för BLOB container. *VhdFileName* -värdet måste vara inom citat tecken. Processen för att ladda upp en VHD-fil kan vara lång beroende på storleken på VHD-filen och anslutnings hastigheten.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av Azure Portal](devtest-lab-create-template.md)
- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)