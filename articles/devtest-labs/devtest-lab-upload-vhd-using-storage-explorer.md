---
title: Ladda upp VHD-filen till Azure DevTest Labs med Storage Explorer
description: Ladda upp VHD-filen till Labbets lagrings konto med hjälp av Microsoft Azure Storage Explorer
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d4e421932ebba5d4f389000c12bcf44ac2f37599
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91282535"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Ladda upp VHD-filen till Labbets lagrings konto med hjälp av Microsoft Azure Storage Explorer

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar som används för att etablera virtuella datorer. Den här artikeln beskriver hur du använder [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att ladda upp en VHD-fil till ett labbs lagrings konto. När du har laddat upp VHD-filen visas några artiklar i [avsnittet Nästa steg](#next-steps) som illustrerar hur du skapar en anpassad avbildning från den uppladdade VHD-filen. Mer information om diskar och virtuella hård diskar i Azure finns i [Introduktion till Managed disks](../virtual-machines/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Följande steg beskriver hur du laddar upp en VHD-fil till DevTest Labs med [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Hämta och installera den senaste versionen av Microsoft Azure Storage Explorer](https://www.storageexplorer.com).

1. Hämta namnet på Labbets lagrings konto med hjälp av Azure Portal:

    1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
    
    1. I listan med labb väljer du önskat labb.  
    
    1. På labb bladet väljer du **konfiguration**. 
    
    1. På bladet labb **konfiguration** väljer du **anpassade avbildningar (VHD)**.
    
    1. På bladet **anpassade bilder** väljer du **+ Lägg till**. 
    
    1. På bladet **anpassad avbildning** väljer du **VHD**.
    
    1. På **VHD** -bladet väljer du **överför en virtuell hård disk med PowerShell**.
    
        ![Ladda upp VHD med PowerShell][0]
    
    1. I bladet **Ladda upp en bild med PowerShell** visas ett anrop till cmdleten **Add-AzureVhd** . Den första parametern (*målet*) innehåller lagrings konto namnet för labbet i följande format:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Anteckna lagrings kontots namn som det används i senare steg.
    
1. Anslut till ett Azure-prenumerations konto med hjälp av Storage Explorer.

    > [!TIP] 
    > 
    > Storage Explorer stöder flera anslutnings alternativ. I det här avsnittet beskrivs hur du ansluter till ett lagrings konto som är kopplat till din Azure-prenumeration. Om du vill se andra anslutnings alternativ som stöds av Storage Explorer, se artikeln [komma igång med Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Öppna Storage Explorer.
    
    1. I Storage Explorer väljer du **Inställningar för Azure-konto**. 
    
        ![Inställningar för Azure-konto][1]
    
    1. I den vänstra rutan visas de Microsoft-konton som du har loggat in på. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto** och följer dialogrutorna för att logga in med ett Microsoft-konto som är kopplat till minst en aktiv Azure-prenumeration.
    
        ![Lägga till ett konto][2]
    
    1. När du har loggat in med ett Microsoft-konto fylls den vänstra rutan i med de Azure-prenumerationer som är kopplade till kontot. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd**. (Om du väljer **alla prenumerationer** försätts valet av alla eller inga av de listade Azure-prenumerationerna.)
    
        ![Välja Azure-prenumerationer][3]
    
    1. I den vänstra rutan visas lagringskontona som är kopplade till de valda Azure-prenumerationerna.
    
        ![Valda Azure-prenumerationer][4]

1. Leta upp Labbets lagrings konto:

    1. Leta upp och expandera noden för den Azure-prenumeration som äger labbet i Storage Explorer vänstra fönstret.
    
    1. Expandera **lagrings konton** under prenumerationens nod.

    1. Utöka Labbets lagrings konto nod för att Visa noder för **BLOB-behållare**, **fil resurser**, **köer** och **tabeller**.
    
    1. Expandera noden **BLOB-behållare** .
    
    1. Välj den överförda BLOB-behållaren för att visa dess innehåll i den högra rutan.
        
        ![Ladda upp katalog][5]

1. Överför VHD-filen med Storage Explorer:

    1. I det högra fönstret i Storage Explorer bör du se en lista över blobarna i den **uppladdade** BLOB-behållaren för labbets lagrings konto. I verktygsfältet i BLOB Editor väljer du **överför** 
        
        ![Skärm bild som visar den Storage Explorer högra rutan med knappen Ladda upp markerad.][6]
    
    1. I den nedrullningsbara menyn **överför** väljer du **överför filer...**.
    
    1. I dialog rutan **överför filer** väljer du ellipsen.
        
        ![Skärm bild som visar dialog rutan Ladda upp filer med den valda ellipsen.][8]  

    1. I dialog rutan **Välj filer att ladda upp** bläddrar du till önskad VHD-fil, markerar den och väljer sedan **Öppna**.
    
    1. Ändra **Blob-typ** till **Page BLOB** när den returneras till dialog rutan **Ladda upp filer** .
    
    1. Välj **Överför**.

        ![Välj fil][9]  
    
    1. I fönstret Storage Explorer **aktivitets logg** visas hämtnings status (tillsammans med länkar för att avbryta uppladdningen). Processen för att ladda upp en VHD-fil kan vara lång beroende på storleken på VHD-filen och anslutnings hastigheten. 

        ![Ladda upp fil status][10]  

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av Azure Portal](devtest-lab-create-template.md)
- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
