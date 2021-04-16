---
title: Ladda ned en virtuell Linux-hårddisk från Azure
description: Ladda ned en virtuell Linux-hårddisk med Hjälp av Azure CLI och Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 8def06990b72d6e08127e8c4f16e0dfd87905d4f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565193"
---
# <a name="download-a-linux-vhd-from-azure"></a>Ladda ned en virtuell Linux-hårddisk från Azure

I den här artikeln får du lära dig hur du laddar ned en virtuell Linux-hårddiskfil (VHD) från Azure med hjälp av Azure Portal. 

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell hårddisk kan inte laddas ned från Azure om den är ansluten till en virtuell dator som körs. Om du vill köra den virtuella datorn kan du skapa [en ögonblicksbild och sedan ladda ned ögonblicksbilden](#alternative-snapshot-the-vm-disk).

Så här stoppar du den virtuella datorn:

1.  Logga in på [Azure-portalen](https://portal.azure.com/).
2.  På den vänstra menyn väljer du **Virtual Machines**.
3.  Välj den virtuella datorn i listan.
4.  På sidan för den virtuella datorn väljer du **Stoppa**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Visar menyknappen för att stoppa den virtuella datorn.":::

### <a name="alternative-snapshot-the-vm-disk"></a>Alternativ: Ögonblicksbild av VM-disken

Ta en ögonblicksbild av disken att ladda ned.

1. Välj den virtuella datorn i [portalen](https://portal.azure.com).
2. Välj **Diskar på** den vänstra menyn och välj sedan den disk som du vill ta en ögonblicksbild av. Information om disken visas.  
3. Välj **Skapa ögonblicksbild** på menyn längst upp på sidan. Sidan **Skapa ögonblicksbild** öppnas.
4. I **Namn** anger du ett namn för ögonblicksbilden. 
5. För **Typ av ögonblicksbild** väljer du Fullständig **eller** **Inkrementell.**
6. När du är klar väljer du **Granska + skapa**.

Ögonblicksbilden skapas inom kort och kan sedan användas för att ladda ned eller skapa en annan virtuell dator från.

> [!NOTE]
> Om du inte stoppar den virtuella datorn först blir ögonblicksbilden inte ren. Ögonblicksbilden är i samma tillstånd som om den virtuella datorn hade varit strömcykel eller kraschade vid den tidpunkt då ögonblicksbilden gjordes.  Även om det vanligtvis är säkert kan det orsaka problem om de program som körs en gång inte är kraschfria.
>  
> Den här metoden rekommenderas endast för virtuella datorer med en enda OS-disk. Virtuella datorer med en eller flera datadiskar bör stoppas innan du laddar ned eller innan du skapar en ögonblicksbild för OS-disken och varje datadisk.

## <a name="generate-sas-url"></a>Generera SAS-URL

Om du vill ladda ned VHD-filen måste du generera en SAS-URL [(signatur för delad](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) åtkomst). När URL:en genereras tilldelas URL:en förfallotid.

1. På menyn på sidan för den virtuella datorn väljer du **Diskar.**
2. Välj operativsystemdisken för den virtuella datorn och välj sedan **Diskexport.**
1. Om det behövs uppdaterar du värdet för **URL:en inom (sekunder) för** att ge dig tillräckligt med tid för att slutföra nedladdningen. Standardvärdet är 3 600 sekunder (en timme).
3. Välj **Generera URL.**
 
      
## <a name="download-vhd"></a>Ladda ned virtuell hårddisk

1.  Under den URL som genererades väljer du **Ladda ned VHD-filen**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Visar knappen för att ladda ned den virtuella hårddisken.":::

2.  Du kan behöva välja **Spara i** webbläsaren för att starta nedladdningen. Standardnamnet för VHD-filen är *abcd*.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [laddar upp och skapar en virtuell Linux-dator från en anpassad disk med Azure CLI](upload-vhd.md). 
- [Hantera Azure-diskar med Azure CLI.](tutorial-manage-disks.md)
