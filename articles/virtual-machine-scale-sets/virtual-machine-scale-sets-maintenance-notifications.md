---
title: Underhållsmeddelanden för VM-skalningsuppsättningar i Azure
description: Visa underhållsaviseringar och starta självbetjäningsunderhåll för VM-skalningsuppsättningar i Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: maintenance-control
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: ec8d211bd25eb04f9e000af950cea9a28a0d1874
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762847"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Meddelanden för planerat underhåll för VM-skalningsuppsättningar


Azure utför regelbundet uppdateringar för att förbättra tillförlitligheten, prestanda och säkerheten för värdinfrastrukturen för virtuella datorer (VM). Uppdateringar kan omfatta uppdatering av värdmiljön eller uppgradering och inaktivering av maskinvara. De flesta uppdateringar påverkar inte de virtuella datorer som är värdar. Uppdateringar påverkar dock virtuella datorer i följande scenarier:

- Om underhållet inte kräver en omstart pausar Azure den virtuella datorn i några sekunder medan värden uppdateras. De här typerna av underhållsåtgärder tillämpas på feldomäner av feldomäner. Förloppet stoppas om några varningssignaler tas emot.

- Om underhållet kräver en omstart får du ett meddelande om när underhållet är planerat. I dessa fall får du en tidsperiod som vanligtvis är 35 dagar där du kan starta underhållet själv, när det fungerar åt dig.


Planerat underhåll som kräver en omstart schemaläggs i vågor. Varje våg har olika omfång (regioner):

- En våg börjar med ett meddelande till kunder. Som standard skickas ett meddelande till prenumerationens ägare och medägare. Du kan lägga till mottagare och meddelandealternativ som e-post, SMS och webhooks i meddelandena med hjälp av [Azure-aktivitetsloggaviseringar.](../azure-monitor/essentials/platform-logs-overview.md)  
- Med ett meddelande *blir ett självbetjäningsfönstret* tillgängligt. Under det här fönstret som vanligtvis är 35 dagar kan du se vilka av dina virtuella datorer som ingår i vågen. Du kan starta underhåll proaktivt enligt dina egna schemaläggningsbehov.
- Efter självbetjäningsfönstret börjar *en schemalagd underhållsfönstret.* Någon gång under det här fönstret schemalägger Och tillämpar Azure det underhåll som krävs på den virtuella datorn. 

Målet med att ha två fönster är att ge dig tillräckligt med tid för att starta underhåll och starta om den virtuella datorn samtidigt som du vet när Azure ska starta underhåll automatiskt.

Du kan använda Azure Portal, PowerShell, REST API och Azure CLI för att fråga efter underhåll windows för vm-skalningsuppsättningens virtuella datorer och för att starta självbetjäningsunderhåll.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Bör du starta underhållet under självbetjäningsfönstret?  

Följande riktlinjer kan hjälpa dig att avgöra om du vill starta underhållet vid en tidpunkt som du väljer.

> [!NOTE] 
> Självbetjäning av underhåll kanske inte är tillgängligt för alla dina virtuella datorer. Om du vill ta reda på om proaktiv omdistribution är tillgänglig för den virtuella datorn letar du **efter Starta nu** i underhållsstatusen. För närvarande är självbetjäning inte tillgängligt för Azure Cloud Services (webb-/arbetsroll) och Azure Service Fabric.


Självbetjäning av underhåll rekommenderas inte för distributioner som använder *tillgänglighetsuppsättningar.* Tillgänglighetsuppsättningar är konfigurationer med hög tillgänglighet där endast en uppdateringsdomän påverkas när som helst. För tillgänglighetsuppsättningar:

- Låt Azure utlösa underhållet. För underhåll som kräver en omstart utförs uppdateringsdomänen av uppdateringsdomänen. Uppdateringsdomäner tar inte nödvändigtvis emot underhållet sekventiellt. Det tar 30 minuter att pausa mellan uppdateringsdomäner.
- Om en tillfällig förlust av en del av din kapacitet (1/uppdatera domänantal) är ett problem kan du enkelt kompensera för förlusten genom att allokera ytterligare instanser under underhållsperioden.
- Uppdateringar tillämpas på feldomännivå för underhåll som inte kräver omstart. 
    
**Använd inte** självbetjäning i följande scenarier: 

- Om du stänger av dina virtuella datorer ofta, antingen manuellt, med hjälp av DevTest Labs, genom att använda automatisk avstängning eller genom att följa ett schema. Självbetjäning av underhåll i dessa scenarier kan återställa underhållsstatusen och orsaka ytterligare driftstopp.
- På kortvariga virtuella datorer som du vet kommer att tas bort före slutet av underhållsvågen. 
- För arbetsbelastningar med ett stort tillstånd som lagras på den lokala (tillfälliga) disken som du vill underhålla efter uppdateringen. 
- Om du ändrar storlek på den virtuella datorn ofta. Det här scenariot kan återställa underhållsstatusen. 
- Om du har infört schemalagda händelser som aktiverar proaktiv redundans eller en bra avstängning av arbetsbelastningen 15 minuter innan underhållsavstängningen påbörjas.

**Använd** självbetjäningsunderhåll om du planerar att köra den virtuella datorn utan avbrott under fasen för schemalagt underhåll och ingen av de föregående motindikationerna gäller. 

Det är bäst att använda självbetjäning i följande fall:

- Du måste kommunicera en exakt underhållsfönstret för hantering eller din kund. 
- Du måste slutföra underhållet senast ett visst datum. 
- Du måste kontrollera underhållssekvensen, till exempel i ett flernivåprogram, för att garantera säker återställning.
- Du behöver mer än 30 minuters återställningstid för virtuella datorer mellan två uppdateringsdomäner. Om du vill styra tiden mellan uppdateringsdomäner måste du utlösa underhåll på dina virtuella datorer en uppdateringsdomän i taget.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Visa VM-skalningsuppsättningar som påverkas av underhåll i portalen

När en planerad underhållsvåg schemaläggs kan du visa listan över VM-skalningsuppsättningar som påverkas av den kommande underhållsvågen med hjälp av Azure Portal. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I den vänstra menyn väljer du **Alla tjänster** och sedan **VM-skalningsuppsättningar.**
3. Under **VM-skalningsuppsättningar** väljer du **Redigera kolumner** för att öppna listan över tillgängliga kolumner.
4. I avsnittet **Tillgängliga kolumner** väljer du **Självbetjäning underhåll** och flyttar det sedan till listan **Valda** kolumner. Välj **Använd**.  

    Om du vill **göra det enklare att** hitta självbetjäningunderhållsobjektet kan du ändra listr listrutan i avsnittet Tillgängliga kolumner från **Alla** till **Egenskaper.** 

Kolumnen **Självbetjäningsunderhåll** visas nu i listan över VM-skalningsuppsättningar. Varje VM-skalningsuppsättning kan ha något av följande värden för kolumnen självbetjäningsunderhåll:

| Värde | Beskrivning |
|-------|-------------|
| Ja | Minst en virtuell dator i VM-skalningsuppsättningen finns i ett självbetjäningsfönstret. Du kan starta underhållet när som helst under självbetjäningsfönstret. | 
| No | Inga virtuella datorer finns i ett självbetjäningsfönstret i den berörda VM-skalningsuppsättningen. | 
| - | Dina VM-skalningsuppsättningar ingår inte i en planerad underhållsvåg.| 

## <a name="notification-and-alerts-in-the-portal"></a>Meddelanden och aviseringar i portalen

Azure kommunicerar ett schema för planerat underhåll genom att skicka ett e-postmeddelande till prenumerationens ägare och medägare. Du kan lägga till mottagare och kanaler i kommunikationen genom att skapa aktivitetsloggaviseringar. Mer information finns i [Övervaka prenumerationsaktivitet med Azure-aktivitetsloggen.](../azure-monitor/essentials/platform-logs-overview.md)

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I den vänstra menyn väljer du **Övervaka**. 
3. I fönstret **Övervaka – Aviseringar (klassisk) väljer** du **+Lägg till aktivitetsloggavisering**.
4. På sidan **Lägg till aktivitetsloggavisering** väljer eller anger du den begärda informationen. I **Villkor** kontrollerar du att du anger följande värden:
   - **Händelsekategori:** Välj **Service Health**.
   - **Tjänster:** Välj **Virtual Machine Scale Sets och Virtual Machines**.
   - **Typ:** Välj **Planned maintenance (Planerat underhåll).** 
    
Mer information om hur du konfigurerar aktivitetsloggaviseringar finns i [Skapa aktivitetsloggaviseringar](../azure-monitor/alerts/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Starta underhåll på VM-skalningsuppsättningen från portalen

Du kan se mer underhållsrelaterad information i översikten över VM-skalningsuppsättningar. Om minst en virtuell dator i VM-skalningsuppsättningen ingår i det planerade underhållet läggs ett nytt meddelandefliksområde till längst upp på sidan. Välj meddelandefliken för att gå till **sidan** Underhåll. 

På sidan **Underhåll** kan du se vilken VM-instans som påverkas av det planerade underhållet. Starta underhållet genom att markera kryssrutan som motsvarar den berörda virtuella datorn. Välj sedan **Starta underhåll.**

När du har börjat underhålla, genomgår de berörda virtuella datorerna i VM-skalningsuppsättningen underhåll och är tillfälligt otillgängliga. Om du missade självbetjäningsfönstret kan du fortfarande se tidsperioden då vm-skalningsuppsättningen kommer att underhållas av Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Kontrollera underhållsstatus med hjälp av PowerShell

Du kan använda Azure PowerShell för att se när virtuella datorer i dina VM-skalningsuppsättningar är schemalagda för underhåll. Information om planerat underhåll är tillgängligt med hjälp [av cmdleten Get-AzVmss](/powershell/module/az.compute/get-azvmss) när du använder `-InstanceView` parametern .
 
Underhållsinformation returneras endast om underhåll planeras. Om inget underhåll schemaläggs som påverkar den virtuella datorinstansen returnerar cmdleten ingen underhållsinformation. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Följande egenskaper returneras under **MaintenanceRedeployStatus**: 

| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta underhållet på den virtuella datorn just nu. |
| PreMaintenanceWindowStartTime         | I början av självbetjäningsfönstret för underhåll när du kan starta underhållet på den virtuella datorn. |
| PreMaintenanceWindowEndTime           | I slutet av självbetjäningsfönstret för underhåll när du kan starta underhållet på den virtuella datorn. |
| UnderhållWindowStartTime            | Början av det schemalagda underhållet där Azure initierar underhåll på den virtuella datorn. |
| MaintenanceWindowEndTime              | Slutet på den schemalagda underhållsfönstret där Azure initierar underhåll på den virtuella datorn. |
| LastOperationResultCode               | Resultatet av det senaste försöket att starta underhållet på den virtuella datorn. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Starta underhåll på den virtuella datorinstansen med hjälp av PowerShell

Du kan starta underhåll på en virtuell dator **om IsCustomerInitiatedMaintenanceAllowed** har angetts till **true**. Använd [cmdleten Set-AzVmss](/powershell/module/az.compute/set-azvmss) med `-PerformMaintenance` parametern .

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Kontrollera underhållsstatus med hjälp av CLI

Du kan visa information om planerat underhåll med [hjälp av az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances).
 
Underhållsinformation returneras endast om underhåll planeras. Om inget underhåll som påverkar den virtuella datorinstansen schemaläggs returnerar inte kommandot någon underhållsinformation. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Följande egenskaper returneras under **MaintenanceRedeployStatus för varje** VM-instans: 

| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta underhållet på den virtuella datorn just nu. |
| PreMaintenanceWindowStartTime         | I början av självbetjäningsfönstret för underhåll när du kan starta underhållet på den virtuella datorn. |
| PreMaintenanceWindowEndTime           | I slutet av självbetjäningsfönstret för underhåll när du kan starta underhållet på den virtuella datorn. |
| UnderhållWindowStartTime            | Början av det schemalagda underhållet där Azure initierar underhåll på den virtuella datorn. |
| MaintenanceWindowEndTime              | Slutet på den schemalagda underhållsfönstret där Azure initierar underhåll på den virtuella datorn. |
| LastOperationResultCode               | Resultatet av det senaste försöket att starta underhållet på den virtuella datorn. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Starta underhåll på den virtuella datorinstansen med hjälp av CLI

Följande anrop initierar underhåll på en VM-instans om `IsCustomerInitiatedMaintenanceAllowed` har angetts till **true**:

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Vanliga frågor

**F: Varför behöver du starta om mina virtuella datorer nu?**

**S:** Även om de flesta uppdateringar och uppgraderingar av Azure-plattformen inte påverkar tillgängligheten för virtuella datorer, kan vi i vissa fall inte undvika att starta om virtuella datorer i Azure. Vi har ackumulerat flera ändringar som kräver att vi startar om servrarna, vilket leder till omstart av den virtuella datorn.

**F: Om jag följer dina rekommendationer för hög tillgänglighet med hjälp av en tillgänglighetsuppsättning, är jag säker?**

**S:** Virtuella datorer som distribueras i en tillgänglighetsuppsättning eller i VM-skalningsuppsättningar använder uppdateringsdomäner. När du utför underhåll respekterar Azure begränsningen av uppdateringsdomänen och startar inte om virtuella datorer från en annan uppdateringsdomän (inom samma tillgänglighetsuppsättning). Azure väntar också i minst 30 minuter innan nästa grupp med virtuella datorer flyttas. 

Mer information om hög tillgänglighet finns i [Regioner och tillgänglighet för virtuella datorer i Azure.](../virtual-machines/availability.md)

**F: Hur meddelas jag om planerat underhåll?**

**S:** En planerad underhållsvåg börjar med att ange ett schema till en eller flera Azure-regioner. Strax därefter skickas ett e-postmeddelande till prenumerationsadministratörer, medadministratörer, ägare och deltagare (ett e-postmeddelande per prenumeration). Ytterligare kanaler och mottagare för det här meddelandet kan konfigureras med aktivitetsloggaviseringar. Om du distribuerar en virtuell dator till en region där planerat underhåll redan har schemalagts får du inte meddelandet. Kontrollera i stället underhållet för den virtuella datorn.

**F: Jag ser ingen indikation på planerat underhåll i portalen, PowerShell eller CLI. Vad är fel?**

**S:** Information som rör planerat underhåll är endast tillgänglig under en planerad underhållsvåg för de virtuella datorer som påverkas av det planerade underhållet. Om du inte ser några data kanske underhållsvågen redan är klar (eller inte har startats) eller så kanske den virtuella datorn redan finns på en uppdaterad server.

**F: Finns det något sätt att veta exakt när min virtuella dator kommer att påverkas?**

**S:** När vi anger schemat definierar vi ett tidsfönster på flera dagar. Den exakta sekvenseringen av servrar (och virtuella datorer) under den här perioden är okänd. Om du vill veta exakt när dina virtuella datorer kommer att uppdateras kan du använda [schemalagda händelser](../virtual-machines/windows/scheduled-events.md). När du använder schemalagda händelser kan du fråga inifrån den virtuella datorn och få ett meddelande i 15 minuter innan en omstart av den virtuella datorn.

**F: Hur lång tid tar det att starta om min virtuella dator?**

**S:**  Beroende på storleken på den virtuella datorn kan omstarten ta upp till flera minuter under underhållsfönstret för självbetjäning. Under de Azure-initierade omstarterna i det schemalagda underhållsfönstret tar omstarten vanligtvis cirka 25 minuter. Om du använder Cloud Services (webb-/arbetsroll), VM-skalningsuppsättningar eller tillgänglighetsuppsättningar får du 30 minuter mellan varje grupp av virtuella datorer (uppdateringsdomän) under den schemalagda underhållsfönstret. 

**F: Jag ser ingen underhållsinformation på mina virtuella datorer. Vad gick fel?**

**S:** Det finns flera orsaker till varför du kanske inte ser någon underhållsinformation på dina virtuella datorer:
   - Du använder en prenumeration som har markerats som *Microsoft Internal*.
   - Dina virtuella datorer är inte schemalagda för underhåll. Det kan vara så att underhållsvågen avslutades, avbröts eller ändrades så att dina virtuella datorer inte längre påverkas av den.
   - Du har inte kolumnen Underhåll **tillagd** i listvyn för den virtuella datorn. Även om vi har lagt till den här kolumnen i standardvyn måste du manuellt lägga till kolumnen **Underhåll** i listvyn för virtuella datorer om du konfigurerar vyn så att den visar kolumner som inte är standard.

**F: Min virtuella dator är schemalagd för underhåll den andra gången. Varför?**

**S:** I flera användningsfall schemaläggs den virtuella datorn för underhåll när du redan har slutfört underhållet och omdistribuerat:
   - Vi har avbrutit underhållsvågen och startat om den med en annan nyttolast. Det kan vara så att vi har identifierat en felaktig nyttolast och att vi bara behöver distribuera ytterligare en nyttolast.
   - Den virtuella *datorns tjänst har åtgärdats* till en annan nod på grund av ett maskinvarufel.
   - Du har valt att stoppa (avallokera) och starta om den virtuella datorn.
   - Du har **automatisk avstängning aktiverat** för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du registrerar dig för underhållshändelser från den virtuella datorn med hjälp av [schemalagda händelser.](../virtual-machines/windows/scheduled-events.md)
