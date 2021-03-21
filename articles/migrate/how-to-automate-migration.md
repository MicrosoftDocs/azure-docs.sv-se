---
title: Automatisera agentbaserade VMware-migreringar i Azure Migrate
description: Beskriver hur du använder skript för att migrera ett stort antal virtuella VMware-datorer i Azure Migrate
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: cdae1fe13f8e08cb6b817f8ec6431c77013020d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754274"
---
# <a name="scale-migration-of-vmware-vms"></a>Skala migrering av virtuella VMware-datorer 

Den här artikeln hjälper dig att förstå hur du använder skript för att migrera ett stort antal virtuella VMware-datorer med hjälp av metoden utan agent. Om du vill skala migreringar använder du [Azure Migrate PowerShell-modul](./tutorial-migrate-vmware-powershell.md). 

Automation-skripten Azure Migrate VMware-migrering är tillgängliga för hämtning på [Azure PowerShell samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) lagrings platsen på GitHub. Skripten kan användas för att migrera virtuella VMware-datorer till Azure med hjälp av metoden för att migrera utan agent. De Azure Migrate PowerShell-kommandon som används i dessa skript dokumenteras [här](./tutorial-migrate-vmware-powershell.md).

## <a name="current-limitations"></a>Aktuella begränsningar
- Dessa skript stöder migrering av virtuella VMware-datorer med alla diskar. Du kan uppdatera skripten om du vill replikera diskarna som är anslutna till en virtuell VMware-dator selektivt. 
- Skripten stöder användning av bedömnings rekommendationer. Om utvärderings rekommendationer inte används migreras alla diskar som är anslutna till den virtuella VMware-datorn till samma typ av hanterad disk (standard eller Premium). Du kan uppdatera skripten om du vill använda flera typer av hanterade diskar med samma virtuella dator

## <a name="prerequisites"></a>Förutsättningar

- [Slutför identifierings självstudien](tutorial-discover-vmware.md) för att förbereda Azure och VMware för migrering.
- Vi rekommenderar att du slutför den andra självstudien för att [utvärdera virtuella VMware-datorer](./tutorial-assess-vmware-azure-vm.md) innan du migrerar dem till Azure.
- Du har Azure PowerShell- `Az` modulen. Om du behöver installera eller uppgradera Azure PowerShell, följ den här [guiden för att installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>Installera Azure Migrate PowerShell-modul

Azure Migrate PowerShell-modulen är tillgänglig som för hands version. Du måste installera PowerShell-modulen med hjälp av följande kommando. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>CSV-indatafil
När du har slutfört alla nödvändiga komponenter måste du skapa en CSV-fil med data för varje virtuell käll dator som du vill migrera. Alla skript är utformade för att fungera på samma CSV-fil. En exempel-CSV-mall är tillgänglig i mappen skript för din referens. CSV-filen kan konfigureras så att du kan använda bedömnings rekommendationer och även ange om vissa åtgärder inte ska aktive ras för en viss virtuell dator. 

> [!NOTE]
> Samma CSV-fil kan användas för att migrera virtuella datorer i flera Azure Migrate-projekt.

### <a name="csv-file-schema"></a>CSV-filschema

**Kolumnrubrik** | **Beskrivning**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Ange Azure Migrate prenumerations-ID för projekt.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Ange Azure Migrate resurs gruppens namn.
AZMIGRATEPROJECT_NAME | Ange namnet på det Azure Migrate projekt som du vill migrera servrar till. 
SOURCE_MACHINE_NAME | Ange ett eget namn (visnings namn) för den identifierade virtuella datorn i Azure Migrate projektet.
AZMIGRATEASSESSMENT_NAME | Ange namnet på utvärderingen som måste användas för migrering.
AZMIGRATEGROUP_NAME | Ange namnet på den grupp som användes för Azure Migrate utvärderingen.
TARGET_RESOURCE_GROUP_NAME | Ange namnet på den Azure-resurs grupp som den virtuella datorn måste migreras till.
TARGET_VNET_NAME| Ange namnet på den Azure-Virtual Network som den migrerade virtuella datorn ska använda.
TARGET_SUBNET_NAME | Ange namnet på under nätet i det virtuella mål nätverket som den migrerade virtuella datorn ska använda. Om inget anges används "default"-undernätet.
TARGET_MACHINE_NAME | Ange det namn som den migrerade virtuella datorn ska använda i Azure. Om inget anges används käll dator namnet.  
TARGET_MACHINE_SIZE | Ange den SKU som den virtuella datorn ska använda i Azure. Om du vill migrera en virtuell dator till D2_v2 virtuell dator i Azure anger du värdet i det här fältet som "Standard_D2_v2". Om du använder en utvärdering kommer det här värdet att härledas baserat på bedömnings rekommendation.
LICENSE_TYPE | Ange om du vill använda Azure Hybrid-förmån för virtuella Windows Server-datorer. Använd värdet "Windows Server" för att dra nytta av Azure Hybrid-förmån. Lämna annars tomt eller Använd "NoLicenseType".
OS_DISK_ID | Ange OS-disk-ID: t för den virtuella dator som ska migreras. Det disk-ID som ska användas är egenskapen unik identifierare (UUID) för disken som hämtats med hjälp av Get-AzMigrateServer cmdlet. Skriptet kommer att använda den första disken i den virtuella datorn som OS-disk om inget värde anges.
TARGET_DISKTYPE | Ange vilken typ av disk som ska användas för alla diskar i den virtuella datorn i Azure. Använd Premium_LRS för Premium-hanterade diskar, StandardSSD_LRS för standard SSD-diskar och Standard_LRS för att använda standard diskar för hård diskar. Om du väljer att använda en utvärdering prioriteras skriptet med rekommenderade disk typer för varje disk i den virtuella datorn. Om du inte använder bedömning eller anger något värde, använder skriptet standard diskar för hård diskar som standard.    
AVAILABILITYZONE_NUMBER | Ange det tillgänglighets zon nummer som ska användas för den migrerade virtuella datorn. Du kan lämna detta tomt om du inte vill använda tillgänglighets zoner. 
AVAILABILITYSET_NAME | Ange namnet på den tillgänglighets uppsättning som ska användas för den migrerade virtuella datorn. Du kan lämna detta tomt om du inte vill använda tillgänglighets uppsättningen.
TURNOFF_SOURCESERVER | Ange "Y" om du vill stänga av den virtuella käll datorn vid migreringen. Använd N annars. Om inget anges antar skriptet värdet som "N".
TESTMIGRATE_VNET_NAME | Ange namnet på det virtuella nätverk som ska användas för att testa migreringen.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Om du vill uppdatera resurs gruppen som ska användas av den migrerade virtuella datorn i Azure anger du namnet på Azure-resurs gruppen, annars lämnar du tomt. 
UPDATED_TARGET_VNET_NAME | Om du vill uppdatera Virtual Network som ska användas av den migrerade virtuella datorn i Azure anger du namnet på Azure-Virtual Network, annars lämnar du tomt.
UPDATED_TARGET_MACHINE_NAME | Om du vill uppdatera namnet som ska användas av den migrerade virtuella datorn i Azure anger du det nya namnet som ska användas, annars lämnar du tomt.
UPDATED_TARGET_MACHINE_SIZE | Om du vill uppdatera SKU: n som ska användas av den migrerade virtuella datorn i Azure anger du det nya SKU: n som ska användas, annars lämnar du tomt.
UPDATED_AVAILABILITYZONE_NUMBER | Om du vill uppdatera tillgänglighets zonen som ska användas av den migrerade virtuella datorn i Azure anger du den nya tillgänglighets zon som ska användas, annars lämnar du tomt.
UPDATED_AVAILABILITYSET_NAME | Om du vill uppdatera tillgänglighets uppsättningen som ska användas av den migrerade virtuella datorn i Azure anger du den nya tillgänglighets uppsättningen som ska användas, annars lämnar du tomt.
UPDATE_NIC1_ID | Ange ID för NÄTVERKSKORTet som ska uppdateras. Om inget anges antar skriptet värdet som det första NÄTVERKSKORTet för den identifierade virtuella datorn. Om du inte vill uppdatera NÄTVERKSKORTet för den virtuella datorn lämnar du alla fält som innehåller NIC-namnet tomt. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | Ange det värde som ska användas för det här NÄTVERKSKORTet. Använd "primär", "sekundär" eller "DoNotCreate" för att ange om det här NÄTVERKSKORTet ska vara primärt, sekundärt eller ska inte skapas på den migrerade virtuella datorn. Det går bara att ange ett nätverkskort som primärt nätverkskort för den virtuella datorn. Lämna tomt om du inte vill uppdatera.
UPDATED_TARGET_NIC1_SUBNET_NAME | Ange namnet på det undernät som ska användas för NÄTVERKSKORTet på den migrerade virtuella datorn. Lämna tomt om du inte vill uppdatera.
UPDATED_TARGET_NIC1_IP | Ange IPv4-adressen som ska användas av NÄTVERKSKORTet på den migrerade virtuella datorn om du vill använda statisk IP. Använd "Auto" om du vill tilldela IP-adressen automatiskt. Lämna tomt om du inte vill uppdatera.
UPDATE_NIC2_ID | Ange ID för NÄTVERKSKORTet som ska uppdateras. Om inget anges förutsätter skriptet att värdet ska vara det andra NÄTVERKSKORTet för den identifierade virtuella datorn. Om du inte vill uppdatera NÄTVERKSKORTet för den virtuella datorn lämnar du alla fält som innehåller NIC-namnet tomt.
UPDATED_TARGET_NIC2_SELECTIONTYPE | Ange det värde som ska användas för det här NÄTVERKSKORTet. Använd "primär", "sekundär" eller "DoNotCreate" för att ange om det här NÄTVERKSKORTet ska vara primärt, sekundärt eller ska inte skapas på den migrerade virtuella datorn. Det går bara att ange ett nätverkskort som primärt nätverkskort för den virtuella datorn. Lämna tomt om du inte vill uppdatera.
UPDATED_TARGET_NIC2_SUBNET_NAME | Ange namnet på det undernät som ska användas för NÄTVERKSKORTet på den migrerade virtuella datorn. Lämna tomt om du inte vill uppdatera.
UPDATED_TARGET_NIC2_IP | Ange IPv4-adressen som ska användas av NÄTVERKSKORTet på den migrerade virtuella datorn om du vill använda statisk IP. Använd "Auto" om du vill tilldela IP-adressen automatiskt. Lämna tomt om du inte vill uppdatera.
OK_TO_UPDATE | Använd "Y" för att ange om de virtuella dator egenskaperna måste uppdateras när du kör AzMigrate_UpdateMachineProperties-skriptet. Använd N eller lämna annars tomt.
OK_TO_MIGRATE | Använd "Y" för att ange om den virtuella datorn ska migreras när du kör AzMigrate_StartMigration-skriptet. Använd "N" eller lämna tomt om du inte vill migrera den virtuella datorn. 
OK_TO_USE_ASSESSMENT | Använd "Y" för att ange om den virtuella datorn ska starta replikeringen med bedömnings rekommendationer när du kör AzMigrate_StartReplication-skriptet. Detta åsidosätter TARGET_MACHINE_SIZE och TARGET_DISKTYPE värden i CSV-filen. Använd "N" eller lämna tomt om du inte vill använda bedömnings rekommendationer.
OK_TO_TESTMIGRATE | Använd "Y" för att ange om den virtuella datorn ska testas när du kör AzMigrate_StartTestMigration-skriptet. Använd "N" eller lämna tomt om du inte vill testa migreringen av den virtuella datorn. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | Använd "Y" för att ange om replikeringsstatus för den virtuella datorn ska uppdateras när du kör AzMigrate_ReplicationStatus-skriptet. Använd "N" eller lämna tomt om du inte vill uppdatera replikeringsstatus.
OK_TO_CLEANUP | Använd "Y" för att ange om replikeringen för den virtuella datorn ska rensas när du kör AzMigrate_StopReplication-skriptet. Använd N eller lämna annars tomt.
OK_TO_TESTMIGRATE_CLEANUP | Använd "Y" för att ange om testmigreringen för den virtuella datorn ska rensas när du kör AzMigrate_CleanUpTestMigration-skriptet. Använd N eller lämna annars tomt.


## <a name="script-execution"></a>Skript körning

När CSV-filen är klar kan du utföra följande steg för att migrera dina lokala virtuella VMware-datorer.

**Aktivitets #** | **Skript namn** | **Beskrivning**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | Aktivera replikering för alla virtuella datorer som anges i CSV-skriptet skapar ett CSV-utdata och en loggfil för fel sökning.
2 | AzMigrate_ReplicationStatus.ps1 | Kontrol lera status för replikeringen. skriptet skapar en CSV-fil med statusen för varje virtuell dator och en loggfil för fel sökning.
3 | AzMigrate_UpdateMachineProperties.ps1 | När de virtuella datorerna har slutfört den inledande replikeringen kan du använda det här skriptet för att uppdatera mål egenskaperna för den virtuella datorn (beräknings-och nätverks egenskaper). Skriptet skapar ett CSV-utdata med jobb informationen för varje virtuell dator.
4 | AzMigrate_StartTestMigration.ps1 |  Starta redundanstestning för alla virtuella datorer som anges i CSV-filen som har kon figurer ATS för att testa migrering. Skriptet skapar ett CSV-utdata med jobb informationen för varje virtuell dator.
5 | AzMigrate_CleanUpTestMigration.ps1 | När du manuellt validerar de virtuella datorer som testet misslyckades – använder du det här skriptet för att rensa de virtuella datorerna för redundanstest för alla virtuella datorer som anges i CSV-filen som har kon figurer ATS för rensning av testmigrering. Skriptet skapar ett CSV-utdata med jobb informationen för varje virtuell dator.
6 | AzMigrate_StartMigration.ps1 | Starta migreringen för alla virtuella datorer som anges i CSV-filen som har kon figurer ATS för migrering. Skriptet skapar ett CSV-utdata med jobb informationen för varje virtuell dator.
7 | AzMigrate_StopReplication.ps1 | Stoppar replikeringen för den virtuella datorn efter att den har migrerats eller om du vill avbryta replikeringen på grund av andra orsaker. Skriptet skapar ett CSV-utdata med jobb informationen för varje virtuell dator.


Följande skript anropas av andra skript för alla Azure Migrate åtgärder som att aktivera replikering, starta testmigrering, uppdatera VM-egenskaper och så vidare. Se till att alla skript finns i samma mapp/sökväg. 

**Aktivitets #** | **Skript namn** | **Beskrivning**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | Vanligt skript som innehåller funktioner för att hämta bedömnings egenskaper (via API), identifierade virtuella datorer och replikera virtuella datorer. 
2 | AzMigrate_CSV_Processor.ps1 | Vanligt skript som innehåller funktioner som används för CSV-fil åtgärder, inklusive inläsning, läsning och utskrift av loggar. 
3 | AzMigrate_Logger.ps1 | Vanligt skript anropas för att generera logg filen för Azure Migrate Automation-åtgärder. Logg filen får formatet log.Scriptname.Datetime.txt.

Förutom ovanstående innehåller mappen också AzMigrate_Template.ps1 som innehåller Skeleton-ramverket för att skapa anpassade skript för olika Azure Migrate-åtgärder. 

### <a name="script-execution-syntax"></a>Syntax för skript körning

När du har hämtat skripten kan skripten köras på följande sätt.

Använd följande syntax om du vill köra skriptet för att starta replikering för virtuella datorer med hjälp av Input.csv-filen. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Om du vill veta mer om hur du använder Azure PowerShell för att migrera virtuella VMware-datorer med Azure Migrate följer du [självstudierna](./tutorial-migrate-vmware-powershell.md).