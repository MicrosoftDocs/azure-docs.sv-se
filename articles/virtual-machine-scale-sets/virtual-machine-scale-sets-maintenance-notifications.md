---
title: Underhålls meddelanden för skalnings uppsättningar för virtuella datorer i Azure
description: Visa underhålls meddelanden och starta självbetjänings underhåll för skalnings uppsättningar för virtuella datorer i Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 85e4b6a4d0ff1c3bd7e634311a36396a74408419
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594446"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Meddelanden för planerat underhåll för VM-skalningsuppsättningar


Azure utför regelbundet uppdateringar för att förbättra tillförlitligheten, prestanda och säkerheten i värd infrastrukturen för virtuella datorer (VM). Uppdateringar kan omfatta en korrigering av värd miljön eller uppgradering och inaktive ring av maskin vara. De flesta uppdateringar påverkar inte de värdar som är värdar för virtuella datorer. Uppdateringar påverkar dock virtuella datorer i följande scenarier:

- Om underhållet inte kräver en omstart pausar Azure den virtuella datorn i några sekunder medan värden uppdateras. De här typerna av underhålls åtgärder tillämpas på fel domän av fel domän. Förloppet stoppas om eventuella varnings hälso signaler tas emot.

- Om underhåll kräver en omstart får du ett meddelande om när underhållet planeras. I dessa fall får du ett tidsfönster som normalt är 35 dagar där du kan starta underhållet själv när det passar dig.


Planerat underhåll som kräver en omstart är schemalagt i vågor. Varje våg har olika omfång (regioner):

- En våg börjar med ett meddelande till kunderna. Som standard skickas ett meddelande till Prenumerationens ägare och medägare. Du kan lägga till mottagare och meddelande alternativ som e-post, SMS och Webhooks i meddelanden med hjälp av Azure [aktivitets logg aviseringar](../azure-monitor/essentials/platform-logs-overview.md).  
- Med Notification görs ett *självbetjänings fönster* tillgängligt. Under det här fönstret som vanligt vis är 35 dagar kan du se vilka av dina virtuella datorer som ingår i vågen. Du kan aktivera underhåll proaktivt enligt dina egna schemaläggnings behov.
- Efter självbetjänings fönstret startar en *schemalagd underhålls period* . Vid något tillfälle under det här fönstret schemalägger Azure och tillämpar det nödvändiga underhållet på den virtuella datorn. 

Målet med två Windows är att ge dig tillräckligt med tid för att starta underhållet och starta om den virtuella datorn samtidigt som du vet när Azure startar underhåll automatiskt.

Du kan använda Azure Portal, PowerShell, REST API och Azure CLI för att fråga efter underhålls perioder för virtuella datorer i den virtuella datorns skalnings uppsättning och för att starta självbetjänings underhållet.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Bör du starta underhåll under självbetjänings fönstret?  

Följande rikt linjer kan hjälpa dig att avgöra om du vill starta underhållet vid en tidpunkt som du väljer.

> [!NOTE] 
> Självbetjänings underhåll kanske inte är tillgängligt för alla dina virtuella datorer. För att avgöra om proaktiv omdistribution är tillgängligt för den virtuella datorn, tittar du på **Starta nu** i underhålls status. För närvarande är självbetjänings underhåll inte tillgängligt för Azure Cloud Services (webb-och arbets roll) och Azure Service Fabric.


Självbetjänings underhåll rekommenderas inte för distributioner som använder *tillgänglighets uppsättningar*. Tillgänglighets uppsättningar är hög tillgängliga inställningar där endast en uppdaterings domän påverkas när som helst. För tillgänglighets uppsättningar:

- Låt Azure utlösa underhållet. För underhåll som kräver en omstart görs en uppdaterings domän av en uppdaterings domän. Uppdaterings domäner får inte nödvändigt vis underhållet sekventiellt. Det finns 30 minuters paus mellan uppdaterings domäner.
- Om en tillfällig förlust av en del av din kapacitet (1/uppdatering av domän antal) är ett problem, kan du enkelt kompensera för förlusten genom att allokera ytterligare instanser under underhålls perioden.
- För underhåll som inte kräver en omstart tillämpas uppdateringar på fel domän nivå. 
    
Använd **inte** självbetjänings underhåll i följande scenarier: 

- Om du stänger av de virtuella datorerna ofta, antingen manuellt, genom att använda DevTest Labs, med automatisk avstängning eller genom att följa ett schema. Självbetjänings underhåll i dessa scenarier kan återställa underhålls statusen och orsaka ytterligare stillestånds tid.
- På virtuella datorer med korta livs längder som du vet kommer att tas bort innan underhålls vågen är slut. 
- För arbets belastningar med stor status lagrad på den lokala (tillfälliga) disken som du vill underhålla efter uppdateringen. 
- Om du ändrar storlek på den virtuella datorn ofta. Det här scenariot kan återställa underhålls statusen. 
- Om du har antagit schemalagda händelser som möjliggör proaktiv redundansväxling eller på en korrekt avstängning av arbets belastningen 15 minuter innan underhålls stängningen påbörjas.

**Använd** självbetjänings underhåll om du planerar att köra den virtuella datorn utan avbrott under fasen för schemalagt underhåll och ingen av föregående counterindications tillämpas. 

Det är bäst att använda självbetjänings underhåll i följande fall:

- Du måste kommunicera ett exakt underhålls fönster för hantering eller din kund. 
- Du måste slutföra underhållet med ett visst datum. 
- Du måste kontrol lera sekvensen av underhåll, till exempel i ett program med flera nivåer, för att garantera säker återställning.
- Du behöver mer än 30 minuter återställnings tid för virtuella datorer mellan två uppdaterings domäner. Om du vill kontrol lera tiden mellan uppdaterings domäner måste du aktivera underhåll av virtuella datorer en uppdaterings domän i taget.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Visa skalnings uppsättningar för virtuella datorer som påverkas av underhåll i portalen

När du har schemalagt en planerad underhålls våg kan du Visa en lista över de skalnings uppsättningar för virtuella datorer som påverkas av den kommande underhålls vågen med hjälp av Azure Portal. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** på den vänstra menyn och välj sedan **Virtual Machine Scale Sets**.
3. Under **skalnings uppsättningar för virtuella datorer** väljer du **Redigera kolumner** för att öppna listan över tillgängliga kolumner.
4. I avsnittet **tillgängliga kolumner** väljer du **självbetjänings underhåll** och flyttar det sedan till listan över **valda kolumner** . Välj **Använd**.  

    Om du vill göra det lättare att hitta **självbetjänings** objekt kan du ändra List alternativet i avsnittet **tillgängliga kolumner** från **alla** till- **Egenskaper**.

Kolumnen för självbetjänings **Underhåll** visas nu i listan över skalnings uppsättningar för virtuella datorer. Varje skalnings uppsättning för virtuella datorer kan ha något av följande värden för kolumnen självbetjänings underhåll:

| Värde | Beskrivning |
|-------|-------------|
| Ja | Minst en virtuell dator i skalnings uppsättningen för den virtuella datorn är i ett självbetjänings fönster. Du kan starta underhåll när som helst under den här självbetjänings perioden. | 
| No | Inga virtuella datorer är i ett självbetjänings fönster i den berörda skalnings uppsättningen för virtuella datorer. | 
| - | Skalnings uppsättningar för virtuella datorer ingår inte i en planerad underhålls våg.| 

## <a name="notification-and-alerts-in-the-portal"></a>Aviseringar och aviseringar i portalen

Azure kommunicerar ett schema för planerat underhåll genom att skicka ett e-postmeddelande till Prenumerationens ägare och gruppen med medägare. Du kan lägga till mottagare och kanaler i den här kommunikationen genom att skapa aktivitets logg aviseringar. Mer information finns i [övervaka prenumerations aktivitet med Azure aktivitets logg](../azure-monitor/essentials/platform-logs-overview.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På den vänstra menyn väljer du **övervaka**. 
3. I fönstret **Övervaka – aviseringar (klassisk)** väljer du **+ Lägg till aktivitets logg avisering**.
4. På sidan **Lägg till aktivitets logg avisering** väljer eller anger du den begärda informationen. I **villkor**, se till att du anger följande värden:
   - **Händelse kategori**: Välj **service Health**.
   - **Tjänster**: Välj **Virtual Machine Scale Sets och Virtual Machines**.
   - **Typ**: Välj **planerat underhåll**. 
    
Mer information om hur du konfigurerar aktivitets logg aviseringar finns i [skapa aktivitets logg aviseringar](../azure-monitor/alerts/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Starta underhåll av den virtuella datorns skalnings uppsättning från portalen

Du kan se mer underhålls relaterad information i översikten över skalnings uppsättningar för virtuella datorer. Om minst en virtuell dator i den virtuella datorns skalnings uppsättning ingår i den planerade underhålls vågen läggs ett nytt meddelande band till längst upp på sidan. Välj menyfliksområdet avisering för att gå till **underhålls** sidan. 

På sidan **Underhåll** kan du se vilken virtuell dator instans som påverkas av planerat underhåll. Starta underhållet genom att markera kryss rutan som motsvarar den berörda virtuella datorn. Välj sedan  **Starta underhåll**.

När du har startat underhåll genomgår de virtuella datorerna i den virtuella datorns skalnings uppsättning underhåll och är tillfälligt otillgängliga. Om du missade fönstret för självbetjäning kan du fortfarande se tidsfönstret när din skalnings uppsättning för den virtuella datorn kommer att behållas av Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Kontrol lera underhålls status med hjälp av PowerShell

Du kan använda Azure PowerShell för att se när virtuella datorer i dina skalnings uppsättningar för virtuella datorer är schemalagda för underhåll. Information om planerat underhåll är tillgänglig med hjälp av cmdleten [Get-AzVmss](/powershell/module/az.compute/get-azvmss) när du använder- `-InstanceView` parametern.
 
Underhålls information returneras endast om underhållet är planerat. Om inget underhåll har schemalagts som påverkar VM-instansen returnerar cmdleten ingen underhålls information. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Följande egenskaper returneras under **MaintenanceRedeployStatus**: 

| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta underhåll på den virtuella datorn för tillfället. |
| PreMaintenanceWindowStartTime         | Början av självbetjänings fönstret för underhåll när du kan starta underhåll på den virtuella datorn. |
| PreMaintenanceWindowEndTime           | Slutet av självbetjänings fönstret för underhåll när du kan starta underhåll på den virtuella datorn. |
| MaintenanceWindowStartTime            | Början av det underhåll som schemalagts för Azure initierar underhåll på den virtuella datorn. |
| MaintenanceWindowEndTime              | Slutet av den schemalagda underhålls perioden i vilken Azure initierar underhåll på den virtuella datorn. |
| LastOperationResultCode               | Resultatet av det senaste försöket att initiera underhåll på den virtuella datorn. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Starta underhåll på VM-instansen med hjälp av PowerShell

Du kan starta underhåll på en virtuell dator om **IsCustomerInitiatedMaintenanceAllowed** är inställt på **Sant**. Använd cmdleten [set-AzVmss](/powershell/module/az.compute/set-azvmss) med `-PerformMaintenance` parametern.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Kontrol lera underhålls status med hjälp av CLI

Du kan visa information om planerat underhåll genom att använda [AZ VMSS List-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Underhålls information returneras endast om underhållet är planerat. Om inget underhåll som påverkar den virtuella dator instansen har schemalagts, returnerar inte kommandot eventuell underhålls information. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Följande egenskaper returneras under **MaintenanceRedeployStatus** för varje VM-instans: 

| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta underhåll på den virtuella datorn för tillfället. |
| PreMaintenanceWindowStartTime         | Början av självbetjänings fönstret för underhåll när du kan starta underhåll på den virtuella datorn. |
| PreMaintenanceWindowEndTime           | Slutet av självbetjänings fönstret för underhåll när du kan starta underhåll på den virtuella datorn. |
| MaintenanceWindowStartTime            | Början av det underhåll som schemalagts för Azure initierar underhåll på den virtuella datorn. |
| MaintenanceWindowEndTime              | Slutet av den schemalagda underhålls perioden i vilken Azure initierar underhåll på den virtuella datorn. |
| LastOperationResultCode               | Resultatet av det senaste försöket att initiera underhåll på den virtuella datorn. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Starta underhåll på VM-instansen med hjälp av CLI

Följande anrop initierar underhåll av en VM-instans om `IsCustomerInitiatedMaintenanceAllowed` har angetts till **True**:

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Vanliga frågor

**F: Varför måste du starta om mina virtuella datorer nu?**

**A:** Även om de flesta uppdateringar och uppgraderingar av Azure-plattformen inte påverkar tillgänglighet för virtuella datorer, i vissa fall kan vi inte undvika att starta om virtuella datorer som finns i Azure. Vi har samlat flera ändringar som kräver att vi startar om våra servrar som kommer att leda till en omstart av datorn.

**F: om jag följer dina rekommendationer för hög tillgänglighet med hjälp av en tillgänglighets uppsättning, är jag säker?**

**A:** Virtuella datorer som distribueras i en tillgänglighets uppsättning eller i en virtuell dators skalnings uppsättningar använder uppdaterings domäner. När underhåll utförs följer Azure uppdaterings domän begränsningen och startar inte om virtuella datorer från en annan uppdaterings domän (inom samma tillgänglighets uppsättning). Azure väntar också i minst 30 minuter innan det flyttas till nästa grupp av virtuella datorer. 

Mer information om hög tillgänglighet finns i [regioner och tillgänglighet för virtuella datorer i Azure](../virtual-machines/availability.md).

**F: Hur kan jag få ett meddelande om planerat underhåll?**

**A:** En planerad underhålls våg börjar genom att ange ett schema för en eller flera Azure-regioner. Strax efter skickas ett e-postmeddelande till prenumerations administratörerna, medadministratörer, ägare och deltagare (ett e-postmeddelande per prenumeration). Ytterligare kanaler och mottagare för det här meddelandet kan konfigureras med aktivitets logg aviseringar. Om du distribuerar en virtuell dator till en region där planerat underhåll redan har schemalagts visas inte meddelandet. Kontrol lera i stället underhålls status för den virtuella datorn.

**F: Jag ser inga uppgifter om planerat underhåll i portalen, PowerShell eller CLI. Vad är fel?**

**A:** Information som rör planerat underhåll är bara tillgänglig under en planerad underhålls våg för de virtuella datorer som påverkas av det planerade underhållet. Om du inte ser några data kanske underhålls vågen redan är avslutad (eller inte startad) eller så kanske den virtuella datorn redan finns på en uppdaterad Server.

**F: finns det ett sätt att veta exakt när min virtuella dator kommer att påverkas?**

**A:** När vi ställer in schemat definierar vi en tids period på flera dagar. Den exakta sekvenseringen av servrar (och virtuella datorer) under den här perioden är okänd. Om du vill veta exakt hur lång tid dina virtuella datorer kommer att uppdateras kan du använda [schemalagda händelser](../virtual-machines/windows/scheduled-events.md). När du använder schemalagda händelser kan du fråga inifrån den virtuella datorn och få en 15 minuters avisering innan en omstart av datorn görs.

**F: hur lång tid tar det att starta om den virtuella datorn?**

**A:**  Beroende på storleken på den virtuella datorn kan det ta upp till flera minuter att starta om i fönstret för självbetjänings underhåll. Under den Azure-initierade omstarten i den schemalagda underhålls perioden tar det vanligt vis ungefär 25 minuter att starta om. Om du använder Cloud Services (webb-och arbets roll), skalnings uppsättningar för virtuella datorer eller tillgänglighets uppsättningar, får du 30 minuter mellan varje grupp med virtuella datorer (uppdaterings domän) under den schemalagda underhålls perioden. 

**F: Jag ser ingen underhålls information på mina virtuella datorer. Vad gick fel?**

**A:** Det finns flera orsaker till varför du kanske inte ser någon underhålls information på dina virtuella datorer:
   - Du använder en prenumeration som marker ATS som *Microsoft Internal*.
   - De virtuella datorerna är inte schemalagda för underhåll. Det kan bero på att underhålls vågen slutade, avbröts eller ändrades så att dina virtuella datorer inte längre påverkas av den.
   - Du har inte lagt till **underhålls** kolumnen i listvyn för din VM. Även om vi har lagt till den här kolumnen i standardvyn, om du konfigurerar vyn för att se kolumner som inte är standard, måste du manuellt lägga till **underhålls** kolumnen i vyn för din VM-lista.

**F: min virtuella dator är schemalagd för underhåll för andra gången. Varför?**

**A:** I flera användnings fall schemaläggs den virtuella datorn för underhåll när du redan har slutfört underhållet och omdistribuerat:
   - Vi har avbrutit underhålls vågen och startat om den med en annan nytto Last. Det kan bero på att vi har identifierat en felaktig nytto last och att vi bara behöver distribuera ytterligare en nytto Last.
   - Den virtuella datorn har *tjänsten lagas* till en annan nod på grund av ett maskin varu fel.
   - Du har valt att stoppa (frigöra) och starta om den virtuella datorn.
   - Du har aktiverat **Automatisk avstängning** för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du registrerar för underhålls händelser inifrån den virtuella datorn med hjälp av [schemalagda händelser](../virtual-machines/windows/scheduled-events.md).
