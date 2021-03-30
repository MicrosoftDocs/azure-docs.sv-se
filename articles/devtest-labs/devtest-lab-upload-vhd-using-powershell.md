---
title: Ladda upp VHD-filen till Azure DevTest Labs med PowerShell | Microsoft Docs
description: Den här artikeln innehåller en genom gång som visar hur du laddar upp en VHD-fil till Azure DevTest Labs med hjälp av PowerShell.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2b393b886a50f60a918690ee2a5583f9623dbe39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88650765"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Ladda upp VHD-filen till Labbets lagrings konto med hjälp av PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar som används för att etablera virtuella datorer. Följande steg beskriver hur du använder PowerShell för att ladda upp en VHD-fil till ett labbs lagrings konto. När du har laddat upp VHD-filen visas några artiklar i [avsnittet Nästa steg](#next-steps) som illustrerar hur du skapar en anpassad avbildning från den uppladdade VHD-filen. Mer information om diskar och virtuella hård diskar i Azure finns i [Introduktion till Managed disks](../virtual-machines/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Följande steg beskriver hur du laddar upp en VHD-fil för att Azure DevTest Labs med PowerShell. 

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.

1. I listan med labb väljer du önskat labb.  

1. På labb bladet väljer du **konfiguration**. 

1. På bladet labb **konfiguration** väljer du **anpassade avbildningar (VHD)**.

1. På bladet **anpassade bilder** väljer du **+ Lägg till**. 

1. På bladet **anpassad avbildning** väljer du **VHD**.

1. På **VHD** -bladet väljer du **överför en virtuell hård disk med PowerShell**.

    ![Ladda upp VHD med PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. På bladet **Ladda upp en bild med PowerShell** kopierar du det genererade PowerShell-skriptet till en text redigerare.

1. Ändra parametern **LocalFilePath** i cmdleten **Add-AzureVhd** för att peka på platsen för den VHD-fil som du vill ladda upp.

1. Kör cmdleten **Add-AzureVhd** (med parametern Modified **LocalFilePath** ) i PowerShell-prompten.

> [!WARNING] 
> 
> Processen för att ladda upp en VHD-fil kan vara lång beroende på storleken på VHD-filen och anslutnings hastigheten.

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av Azure Portal](devtest-lab-create-template.md)
- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
