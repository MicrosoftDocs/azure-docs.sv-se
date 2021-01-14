---
title: Ladda ned en Linux-VHD från Azure
description: Ladda ned en Linux-VHD med Azure CLI och Azure Portal.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: f0c5e51665b4b1d31e7d2b3e25e7be31b481d0d9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203175"
---
# <a name="download-a-linux-vhd-from-azure"></a>Ladda ned en Linux-VHD från Azure

I den här artikeln får du lära dig hur du laddar ned en virtuell hård disk fil (VHD) från Azure med hjälp av Azure Portal. 

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell hård disk kan inte laddas ned från Azure om den är ansluten till en virtuell dator som körs. Du måste stoppa den virtuella datorn för att kunna ladda ned den virtuella hård disken. 

1.  Logga in på [Azure-portalen](https://portal.azure.com/).
2.  På den vänstra menyn väljer du **Virtual Machines**.
3.  Välj den virtuella datorn i listan.
4.  På sidan för den virtuella datorn väljer du **stoppa**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Visar meny knappen för att stoppa den virtuella datorn.":::

## <a name="generate-sas-url"></a>Skapa SAS-URL

Om du vill hämta VHD-filen måste du generera en URL för [signatur för delad åtkomst (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) . När URL: en genereras tilldelas URL: en förfallo tid.

1. På menyn på sidan för den virtuella datorn väljer du **diskar**.
2. Välj operativ system disk för den virtuella datorn och välj sedan **disk export**.
1. Om det behövs kan du uppdatera värdet för **URL: en upphör att gälla om (sekunder)** för att få tillräckligt med tid för att slutföra nedladdningen. Standardvärdet är 3600 sekunder (en timme).
3. Välj **skapa URL**.
 
      
## <a name="download-vhd"></a>Hämta VHD

1.  Under den URL som genererades väljer du **Hämta VHD-filen**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Visar knappen för att ladda ned den virtuella hård disken.":::

2.  Du kan behöva välja **Spara** i webbläsaren för att starta nedladdningen. Standard namnet för VHD-filen är *ABCD*.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [laddar upp och skapar en virtuell Linux-dator från en anpassad disk med Azure CLI](upload-vhd.md). 
- [Hantera Azure-diskar i Azure CLI](tutorial-manage-disks.md).
