---
title: Ansluta en hanterad datadisk till en virtuell Windows-dator – Azure
description: Så här ansluter du en hanterad datadisk till en virtuell Windows-dator med hjälp av Azure Portal.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 8c64b0ff5b7a9abfa58ec17d0ebcabe05b0ed6e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550815"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Koppla en hanterad datadisk till en virtuell Windows-dator med hjälp av Azure Portal

Den här artikeln visar hur du ansluter en ny hanterad datadisk till en virtuell Windows-dator med hjälp av Azure Portal. Storleken på den virtuella datorn avgör hur många data diskar du kan koppla. Mer information finns i [storlekar för virtuella datorer](../sizes.md).


## <a name="add-a-data-disk"></a>Lägg till en datadisk

1. Gå till [Azure Portal](https://portal.azure.com) för att lägga till en datadisk. Sök efter och välj **virtuella datorer**.
2. Välj en virtuell dator i listan.
3. På sidan **virtuell dator** väljer du **diskar**.
4. På sidan **diskar** väljer du **Lägg till datadisk**.
5. Välj **skapa disk** i list rutan för den nya disken.
6. På sidan **skapa hanterad disk** skriver du ett namn för disken och justerar de andra inställningarna efter behov. När du är färdig väljer du **Skapa**.
7. På sidan **diskar** väljer du **Spara** för att spara den nya disk konfigurationen för den virtuella datorn.
8. När Azure har skapat disken och bifogat den till den virtuella datorn visas den nya disken i den virtuella datorns disk inställningar under **data diskar**.


## <a name="initialize-a-new-data-disk"></a>Initiera en ny datadisk

1. Anslut till den virtuella datorn.
1. Välj **Start** menyn i Windows på den virtuella datorn som körs och skriv **diskmgmt. msc** i sökrutan. **Disk hanterings** konsolen öppnas.
2. Disk hantering känner av att du har en ny, oinitierad disk och fönstret **initiera disk** visas.
3. Kontrol lera att den nya disken är markerad och välj sedan **OK** för att initiera den.
4. Den nya disken visas som ej **allokerad**. Högerklicka var som helst på disken och välj **Ny enkel volym**. **Guide fönstret Ny enkel volym** öppnas.
5. Fortsätt med guiden och Behåll alla standardvärden och när du är klar väljer du **Slutför**.
6. Stäng **disk hantering**.
7. Ett popup-fönster visas med en varning om att du måste formatera den nya disken innan du kan använda den. Välj **Formatera disk**.
8. I fönstret **Formatera ny disk** kontrollerar du inställningarna och väljer sedan **Starta**.
9. En varning visas som meddelar dig att formatering av diskarna tar bort alla data. Välj **OK**.
10. När formateringen är klar väljer du **OK**.

## <a name="next-steps"></a>Nästa steg

- Du kan också [ansluta en datadisk med hjälp av PowerShell](attach-disk-ps.md).
- Om ditt program behöver använda *D:* -enheten för att lagra data, kan du [ändra enhets beteckningen för den temporära Windows-disken](change-drive-letter.md).