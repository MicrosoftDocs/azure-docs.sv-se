---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008501"
---
1. Upprätta en Anslutning till fjärrskrivbord till den dator som kör processervern. 
2. Kör cspsconfigtool.exe för att starta konfigurations verktyget för process Server för Azure Site Recovery.
    - Verktyget startas automatiskt första gången du loggar in på processervern.
    - Om den inte öppnas automatiskt klickar du på genvägen på Skriv bordet.

3. Ange namn eller IP-adress för den konfigurations server som du vill registrera processervern med i **konfigurations serverns FQDN eller IP-** adress.
4. I **konfigurations server port** kontrollerar du att 443 har angetts. Detta är den port som konfigurations servern lyssnar efter begär Anden på.
5. I **anslutnings lösen fras** anger du den lösen fras som du angav när du konfigurerade konfigurations servern. Så här hittar du lösen frasen:
    -  På konfigurations servern navigerar du till mappen Site Recovery installation **\home\svssystems\bin \**:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Kör kommandot nedan för att skriva ut den aktuella lösen frasen:
    ```
    genpassphrase.exe -n
    ```

6. I **dataöverföring port** låter du standardvärdet vara kvar om du inte har angett en anpassad port.

7. Klicka på **Spara** Spara inställningarna och registrera processervern.

    
    ![Registrera processervern](./media/site-recovery-vmware-register-process-server/register-ps.png)
