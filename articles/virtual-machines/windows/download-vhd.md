---
title: Ladda ned en virtuell Windows-hårddisk från Azure
description: Ladda ned en virtuell Windows-hårddisk med hjälp av Azure Portal.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 32b9753b79273ce747d00cba077dd8a5ee6d724d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565295"
---
# <a name="download-a-windows-vhd-from-azure"></a>Ladda ned en virtuell Windows-hårddisk från Azure

I den här artikeln får du lära dig hur du laddar ned en virtuell Windows-hårddiskfil (VHD) från Azure med hjälp av Azure Portal.

## <a name="optional-generalize-the-vm"></a>Valfritt: Generalisera den virtuella datorn

Om du vill använda den [](tutorial-custom-images.md) virtuella hårddisken som en avbildning för att skapa andra virtuella datorer bör du använda [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) för att generalisera operativsystemet. Annars måste du göra en kopia av disken för varje virtuell dator som du vill skapa.

Generalisera den virtuella datorn om du vill använda den virtuella hårddisken som en avbildning för att skapa andra virtuella datorer.

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/).
2. [Anslut till den virtuella datorn](connect-logon.md). 
3. Öppna kommandotolken som administratör på den virtuella datorn.
4. Ändra katalogen till *%windir%\system32\sysprep och* kör sysprep.exe.
5. I dialogrutan Systemförberedelseverktyget väljer du **Ange OOBE (System Out-of-Box Experience)** och kontrollerar att **Generalisera** är markerat.
6. I Avstängningsalternativ väljer du **Stäng** av och klickar sedan på **OK.** 

Om du inte vill generalisera den aktuella virtuella datorn kan du fortfarande skapa en generaliserad avbildning genom att först göra en ögonblicksbild av [OS-disken,](#alternative-snapshot-the-vm-disk)skapa en ny virtuell dator från ögonblicksbilden och sedan generalisera kopian.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell hårddisk kan inte laddas ned från Azure om den är ansluten till en virtuell dator som körs. Om du vill fortsätta köra den virtuella datorn kan du skapa [en ögonblicksbild och sedan ladda ned ögonblicksbilden](#alternative-snapshot-the-vm-disk).

1. På navmenyn i den Azure Portal klickar du **på Virtual Machines**.
1. Välj den virtuella datorn i listan.
1. Klicka på Stoppa på bladet för den **virtuella datorn.**

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
> Om du inte stoppar den virtuella datorn först rensas inte ögonblicksbilden. Ögonblicksbilden är i samma tillstånd som om den virtuella datorn hade varit strömcykel eller kraschade vid den tidpunkt då ögonblicksbilden gjordes.  Även om det vanligtvis är säkert kan det orsaka problem om de program som körs en gång inte är kraschfria.
>  
> Den här metoden rekommenderas endast för virtuella datorer med en enda OS-disk. Virtuella datorer med en eller flera datadiskar bör stoppas innan du laddar ned eller innan du skapar en ögonblicksbild för OS-disken och varje datadisk.

## <a name="generate-download-url"></a>Generera nedladdnings-URL

Om du vill ladda ned VHD-filen måste du generera en SAS-URL [(signatur för delad](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) åtkomst). När URL:en genereras tilldelas URL:en förfallotid.

1. På sidan för den virtuella datorn klickar du **på Diskar** på den vänstra menyn.
1. Välj operativsystemdisken för den virtuella datorn.
1. På sidan för disken väljer du **Diskexport på** den vänstra menyn.
1. Standardförfallotiden för URL:en *är 3 600* sekunder (en timme). Du kan behöva öka detta för Windows OS-diskar eller stora datadiskar. **36 000** sekunder (10 timmar) räcker vanligtvis.
1. Klicka **på Generera URL.**

> [!NOTE]
> Förfallotiden ökas från standardvärdet för att ge tillräckligt med tid för att ladda ned den stora VHD-filen för ett Windows Server-operativsystem. Stora virtuella hårddiskar kan ta upp till flera timmar att ladda ned beroende på din anslutning och storleken på den virtuella datorn. 
> 
> 

## <a name="download-vhd"></a>Ladda ned virtuell hårddisk

1. Under den URL som genererades klickar du på Ladda ned VHD-filen.
1. Du kan behöva klicka på **Spara** i webbläsaren för att starta nedladdningen. Standardnamnet för VHD-filen är *abcd*.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [laddar upp en VHD-fil till Azure](upload-generalized-managed.md). 
- [Skapa hanterade diskar från ohanterade diskar i ett lagringskonto](attach-disk-ps.md).
- [Hantera Azure-diskar med PowerShell](tutorial-manage-data-disk.md).
