---
title: Automatisera migreringen av migreringen i Azure Migrate
description: Beskriver hur du använder skript för att migrera ett stort antal datorer i Azure Migrate
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/01/2019
ms.openlocfilehash: 01196b23ce5bf9ce842da89d0773a77e6a1d5107
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754240"
---
# <a name="scale-migration-of-vms"></a>Skala migrering av virtuella datorer 

Den här artikeln hjälper dig att förstå hur du använder skript för att migrera ett stort antal virtuella datorer (VM). Om du vill skala migreringen använder du [Azure Site Recovery](../site-recovery/site-recovery-overview.md). 

Site Recovery skript är tillgängliga för nedladdning vid [Azure PowerShell exempel](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) lagrings platsen på GitHub. Skripten kan användas för att migrera VMware-, AWS-, GCP-VM: ar och fysiska servrar till hanterade diskar i Azure. Du kan också använda dessa skript för att migrera virtuella Hyper-V-datorer om du migrerar de virtuella datorerna som fysiska servrar. Skript som utnyttjar Azure Site Recovery PowerShell dokumenteras [här](../site-recovery/vmware-azure-disaster-recovery-powershell.md).

## <a name="current-limitations"></a>Aktuella begränsningar
- Stöd för att ange den statiska IP-adressen för det primära NÄTVERKSKORTet på den virtuella mål datorn
- Skripten tar inte Azure Hybrid-förmån relaterade indata. du måste uppdatera egenskaperna för den replikerade virtuella datorn manuellt i portalen

## <a name="how-does-it-work"></a>Hur fungerar det?

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar måste du utföra följande steg:
- Se till att Site Recoverys valvet har skapats i din Azure-prenumeration
- Kontrol lera att konfigurations servern och processervern är installerade i käll miljön och att valvet kan identifiera miljön
- Se till att en replikeringsprincip har skapats och associerats med konfigurations servern
- Se till att du har lagt till administratörs kontot för den virtuella datorn på konfigurations servern (som ska användas för att replikera lokala virtuella datorer)
- Se till att mål artefakterna i Azure skapas
    - Mål resurs grupp
    - Mål lagrings konto (och dess resurs grupp) – skapa ett Premium Storage-konto om du planerar att migrera till Premium-hanterade diskar
    - Lagrings konto för cache (och dess resurs grupp) – skapa ett standard lagrings konto i samma region som valvet
    - Mål Virtual Network för redundans (och dess resurs grupp)
    - Mål under nät
    - Mål Virtual Network för redundanstest (och dess resurs grupp)
    - Tillgänglighets uppsättning (vid behov)
    - Mål nätverks säkerhets grupp och dess resurs grupp
- Kontrol lera att du har valt egenskaper för den virtuella mål datorn
    - Namn på virtuell måldator
    - Storlek på virtuell måldator i Azure (kan bestämmas med hjälp av Azure Migrate bedömning)
    - Den privata IP-adressen för det primära NÄTVERKSKORTet på den virtuella datorn
- Ladda ned skripten från [Azure PowerShell samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) lagrings platsen på GitHub

### <a name="csv-input-file"></a>CSV-indatafil
När du har slutfört alla nödvändiga komponenter måste du skapa en CSV-fil som innehåller data för varje käll dator som du vill migrera. CSV-filen med indata måste ha en rubrik rad med information om indata och en rad med information för varje dator som behöver migreras. Alla skript är utformade för att fungera på samma CSV-fil. En exempel-CSV-mall är tillgänglig i mappen skript för din referens.

### <a name="script-execution"></a>Skript körning
När CSV-filen är klar kan du utföra följande steg för att utföra migreringen av de lokala virtuella datorerna:

**Aktivitets #** | **Skript namn** | **Beskrivning**
--- | --- | ---
1 | asr_startmigration.ps1 | Aktivera replikering för alla virtuella datorer som anges i CSV-skriptet skapar ett CSV-utdata med jobb informationen för varje virtuell dator
2 | asr_replicationstatus.ps1 | Kontrol lera status för replikeringen. skriptet skapar en CSV med status för varje virtuell dator
3 | asr_updateproperties.ps1 | När de virtuella datorerna replikeras/skyddas, använder du det här skriptet för att uppdatera mål egenskaperna för den virtuella datorn (beräknings-och nätverks egenskaper)
4 | asr_propertiescheck.ps1 | Kontrol lera att egenskaperna har uppdaterats korrekt
5 | asr_testmigration.ps1 |  Starta redundanstestningen av de virtuella datorerna som anges i CSV-skriptet skapar ett CSV-utdata med jobb informationen för varje virtuell dator
6 | asr_cleanuptestmigration.ps1 | När du manuellt validerar de virtuella datorer som testet misslyckades, kan du använda det här skriptet för att rensa de virtuella datorerna för redundanstest
7 | asr_migration.ps1 | Utföra en oplanerad redundansväxling för de virtuella datorer som anges i CSV-skriptet skapar skriptet en CSV-fil med jobb informationen för varje virtuell dator. Skriptet stänger inte av lokala virtuella datorer innan redundansväxlingen utlöses för program konsekvens, men du bör stänga av de virtuella datorerna manuellt innan du kör skriptet.
8 | asr_completemigration.ps1 | Utför åtgärden genomför på de virtuella datorerna och ta bort Azure Site Recovery entiteter
9 | asr_postmigration.ps1 | Om du planerar att tilldela nätverks säkerhets grupper till nätverkskortet efter redundansväxlingen kan du använda det här skriptet för att göra det. Det tilldelar ett NSG till ett nätverkskort i den virtuella mål datorn.

## <a name="how-to-migrate-to-managed-disks"></a>Hur migrerar jag till Managed disks?
Skriptet migrerar som standard de virtuella datorerna till Managed disks i Azure. Om det angivna mål lagrings kontot är ett Premium Storage-konto skapas Premium-hanterade diskar efter migreringen. Lagrings kontot för cachen kan fortfarande vara ett standard konto. Om mål lagrings kontot är ett standard lagrings konto skapas standard diskar efter migreringen. 

## <a name="next-steps"></a>Nästa steg

[Lär dig mer](../site-recovery/migrate-tutorial-on-premises-azure.md) om att migrera servrar till Azure med hjälp av Azure Site Recovery
