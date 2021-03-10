---
title: Skapa en virtuell Windows-dator från en specialiserad virtuell hård disk i Azure Portal
description: Skapa en ny virtuell Windows-dator från en virtuell hård disk i Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: a93ab207960133ecbf5a68927c5d16a6daf94fd4
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562222"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Skapa en virtuell dator från en virtuell hård disk med hjälp av Azure Portal

Det finns flera sätt att skapa en virtuell dator (VM) i Azure: 

- Om du redan har en virtuell hård disk (VHD) som ska användas eller om du vill kopiera den virtuella hård disken från en befintlig virtuell dator som ska användas, kan du skapa en ny virtuell dator genom att *koppla* den virtuella hård disken till den nya virtuella datorn som en OS-disk. 

- Du kan skapa en ny virtuell dator från den virtuella hård disken för en virtuell dator som har tagits bort. Om du till exempel har en virtuell Azure-dator som inte fungerar som den ska, kan du ta bort den virtuella datorn och använda den virtuella hård disken för att skapa en ny virtuell dator. Du kan antingen återanvända samma virtuella hård disk eller skapa en kopia av den virtuella hård disken genom att skapa en ögonblicks bild och sedan skapa en ny hanterad disk från ögonblicks bilden. Även om det tar några fler steg att skapa en ögonblicks bild, bevarar den den ursprungliga virtuella hård disken och ger dig en återställning.

- Ta en klassisk virtuell dator och Använd den virtuella hård disken för att skapa en ny virtuell dator som använder distributions modellen för Resource Manager och de hanterade diskarna. **Stoppa** den klassiska virtuella datorn i Azure Portal innan du skapar ögonblicks bilden för bästa möjliga resultat.
 
- Du kan skapa en virtuell Azure-dator från en lokal virtuell hård disk genom att ladda upp den lokala virtuella hård disken och koppla den till en ny virtuell dator. Du kan använda PowerShell eller något annat verktyg för att ladda upp den virtuella hård disken till ett lagrings konto och sedan skapa en hanterad disk från den virtuella hård disken. Mer information finns i [Ladda upp en specialiserad virtuell hård disk](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

> [!IMPORTANT]
> 
> När du använder en specialiserad disk för att skapa en ny virtuell dator behåller den nya virtuella datorn dator namnet för den ursprungliga virtuella datorn. Annan datorspecifik information (t. ex. CMID) hålls även och, i vissa fall kan denna Duplicerad information orsaka problem. När du kopierar en virtuell dator bör du vara medveten om vilken typ av datorspecifik information som dina program förlitar sig på.  
> Använd därför inte en specialiserad disk om du vill skapa flera virtuella datorer. I stället [skapar du en avbildning](capture-image-resource.md) och [använder avbildningen för att skapa flera virtuella datorer](create-vm-generalized-managed.md)i stället för större distributioner.

Vi rekommenderar att du begränsar antalet samtidiga distributioner till 20 virtuella datorer från en enda ögonblicks bild eller virtuell hård disk. 

## <a name="copy-a-disk"></a>Kopiera en disk

Skapa en ögonblicks bild och skapa sedan en disk från ögonblicks bilden. Med den här strategin kan du behålla den ursprungliga virtuella hård disken som reserv:

1. Välj **alla tjänster** på den vänstra menyn i [Azure Portal](https://portal.azure.com).
2. I sökrutan **alla tjänster** anger du **diskar** och väljer sedan **diskar** för att visa listan över tillgängliga diskar.
3. Välj den disk som du vill använda. Disk **sidan för** disken visas.
4. I menyn högst upp väljer du **skapa ögonblicks bild**. 
5. Ange ett **namn** för ögonblicks bilden.
6. Välj en **resurs grupp** för ögonblicks bilden. Du kan antingen använda en befintlig resurs grupp eller skapa en ny.
7. För **Kontotyp** väljer du antingen **standard lagring (HDD)** eller **Premium (SSD)** -lagring.
8. När du är klar väljer du **skapa** för att skapa ögonblicks bilden.
9. När ögonblicks bilden har skapats väljer du **skapa en resurs** på den vänstra menyn.
10. I rutan Sök anger du **hanterad disk** och väljer sedan **Managed disks** i listan.
11. På sidan **Managed disks** väljer du **skapa**.
12. Ange ett **namn** för disken.
13. Välj en **resurs grupp** för disken. Du kan antingen använda en befintlig resurs grupp eller skapa en ny. Det här valet används också som resurs grupp där du skapar den virtuella datorn från disken.
14. För **Kontotyp** väljer du antingen **standard lagring (HDD)** eller **Premium (SSD)** -lagring.
15. Kontrol lera att **Snapshot** är markerat i **Källtyp**.
16. I list rutan **käll ögonblicks bild** väljer du den ögonblicks bild som du vill använda.
17. Gör eventuella justeringar vid behov och välj sedan **skapa** för att skapa disken.

## <a name="create-a-vm-from-a-disk"></a>Skapa en virtuell dator från en disk

När du har den virtuella hård disk som du vill använda kan du skapa den virtuella datorn i portalen:

1. Välj **alla tjänster** på den vänstra menyn i [Azure Portal](https://portal.azure.com).
2. I sökrutan **alla tjänster** anger du **diskar** och väljer sedan **diskar** för att visa listan över tillgängliga diskar.
3. Välj den disk som du vill använda. **Disk** sidan för disken öppnas.
4. På sidan **Översikt** kontrollerar du att **disk tillstånd** visas som **frånkopplat**. Om den inte är det kan du behöva koppla bort disken från den virtuella datorn eller ta bort den virtuella datorn för att frigöra disken.
4. På menyn längst upp på sidan väljer du **Skapa virtuell dator**.
5. På sidan **grundläggande** för den nya virtuella datorn anger du ett **namn på den virtuella datorn** och väljer antingen en befintlig **resurs grupp** eller skapar en ny.
6. I **storlek** väljer du **ändra storlek** för att komma åt sidan **storlek** .
7. Välj en virtuell dator storleks rad och välj sedan **Välj**.
8. På sidan **nätverk** kan du antingen låta portalen skapa alla nya resurser eller så kan du välja ett befintligt **virtuellt nätverk** och en **nätverks säkerhets grupp**. Portalen skapar alltid ett nytt nätverks gränssnitt och en offentlig IP-adress för den nya virtuella datorn. 
9. Gör ändringar i övervaknings alternativen på sidan **hantering** .
10. På sidan **gäst konfiguration** lägger du till eventuella tillägg vid behov.
11. När du är klar väljer du **Granska + skapa**. 
12. Om den virtuella dator konfigurationen klarar valideringen väljer du **skapa** för att starta distributionen.


## <a name="next-steps"></a>Nästa steg

Du kan också använda PowerShell för att [Ladda upp en virtuell hård disk till Azure och skapa en specialiserad virtuell dator](create-vm-specialized.md).


