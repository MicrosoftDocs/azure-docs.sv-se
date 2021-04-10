---
title: Avsluta avisering för instanser i skalningsuppsättningar med virtuella Azure-datorer
description: Lär dig hur du aktiverar uppsägnings meddelanden för instanser av skalnings uppsättningar för virtuella Azure-datorer
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: terminate-notification
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ed042afbcbb67a88e304c92302b14af56b26c8e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933414"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Avsluta avisering för instanser i skalningsuppsättningar med virtuella Azure-datorer
Skalnings uppsättnings instanser kan välja att ta emot meddelanden om instans avslutning och ange en fördefinierad fördröjnings-timeout för åtgärden avsluta. Uppsägnings meddelandet skickas via Azure Metadata Service – [schemalagda händelser](../virtual-machines/windows/scheduled-events.md), som innehåller aviseringar för och fördröjning av påverkan på åtgärder som omstarter och omdistribueras. Lösningen lägger till en annan händelse – Avbryt – till listan över Schemalagda händelser och den associerade fördröjningen av händelsen avbryts beror på fördröjningen som anges av användarna i deras skal uppsättnings modell konfigurationer.

När du har registrerat i funktionen behöver inte skalnings uppsättnings instanserna vänta tills tids gränsen har gått ut innan instansen tas bort. När du har tagit emot ett avslutnings meddelande kan instansen välja att tas bort när som helst innan timeout-värdet upphör att gälla.

## <a name="enable-terminate-notifications"></a>Aktivera avsluta meddelanden
Det finns flera sätt att aktivera avslutnings meddelanden för dina skalnings uppsättnings instanser enligt beskrivningen i exemplen nedan.

### <a name="azure-portal"></a>Azure Portal

Med följande steg kan du avsluta avisering när du skapar en ny skalnings uppsättning. 

1. Gå till **skalnings uppsättningar för virtuella datorer**.
1. Välj **+ Lägg** till för att skapa en ny skalnings uppsättning.
1. Gå till fliken **hantering** . 
1. Leta upp avsnittet om **instans avslutning** .
1. För **instans avslutnings meddelande** väljer du **på**.
1. Ange önskad standard tids gräns för **avslutnings fördröjning (minuter)**.
1. När du är klar med att skapa den nya skalnings uppsättningen väljer du **Granska + skapa** . 

> [!NOTE]
> Du kan inte ange avsluta meddelanden i befintliga skalnings uppsättningar i Azure Portal

### <a name="rest-api"></a>REST-API

I följande exempel aktive ras meddelanden i skalnings uppsättnings modellen.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

Blocket ovan anger en tids gräns på 5 minuter (som anges av *PT5M*) för att avsluta åtgärd på alla instanser i din skalnings uppsättning. Fältet *notBeforeTimeout* kan ta ett värde mellan 5 och 15 minuter i ISO 8601-format. Du kan ändra standard tids gränsen för åtgärden avsluta genom att ändra egenskapen *notBeforeTimeout* under *terminateNotificationProfile* som beskrivs ovan.

När du har aktiverat *scheduledEventsProfile* i skalnings uppsättnings modellen och angett *notBeforeTimeout*, uppdaterar du de enskilda instanserna till den [senaste modellen](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) för att avspegla ändringarna.

> [!NOTE]
>Avsluta meddelanden i skalnings uppsättnings instanser kan bara aktive ras med API version 2019-03-01 och senare

### <a name="azure-powershell"></a>Azure PowerShell
När du skapar en ny skalnings uppsättning kan du aktivera meddelanden om uppsägning i skalnings uppsättningen med hjälp av cmdleten [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) .

Det här exempel skriptet går igenom skapandet av en skalnings uppsättning och associerade resurser med hjälp av konfigurations filen: [skapa en fullständig skalnings uppsättning för virtuella datorer](./scripts/powershell-sample-create-complete-scale-set.md). Du kan ange konfigurations avslutnings meddelande genom att lägga till parametrarna *TerminateScheduledEvents* och *TerminateScheduledEventNotBeforeTimeoutInMinutes* till konfigurationsobjektet för att skapa skalnings uppsättningar. I följande exempel aktive ras funktionen med en fördröjning på 10 minuter.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Använd cmdleten [Update-AzVmss](/powershell/module/az.compute/update-azvmss) för att aktivera avslutnings meddelanden för en befintlig skalnings uppsättning.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
Exemplet ovan aktiverar avslutning av meddelanden i en befintlig skalnings uppsättning och anger en tids gräns på 15 minuter för händelsen Avbryt.

När du har aktiverat schemalagda händelser i skalnings uppsättnings modellen och angett tids gränsen, uppdaterar du de enskilda instanserna till den [senaste modellen](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) för att avspegla ändringarna.

### <a name="azure-cli-20"></a>Azure CLI 2.0

I följande exempel används för att aktivera avslutnings meddelanden när du skapar en ny skalnings uppsättning.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

Exemplet ovan skapar först en resurs grupp och skapar sedan en ny skalnings uppsättning med avsluta meddelanden aktiverade för en standard-timeout på 10 minuter.

Följande exempel är för att aktivera avslutnings meddelande i en befintlig skalnings uppsättning.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Hämta meddelanden

Avsluta meddelanden levereras via [schemalagda händelser](../virtual-machines/windows/scheduled-events.md), vilket är en Azure-metadata service. Azure-metadatatjänsten visar information om att köra Virtual Machines att använda en REST-slutpunkt som är tillgänglig från den virtuella datorn. Informationen är tillgänglig via en icke-dirigerad IP-adress så att den inte exponeras utanför den virtuella datorn.

Schemalagda händelser aktive ras för din skalnings uppsättning första gången du gör en begäran om händelser. Du kan vänta ett fördröjt svar i ditt första anrop på upp till två minuter. Fråga slut punkten regelbundet för att identifiera kommande underhålls händelser och status för pågående underhålls aktiviteter.

Schemalagda händelser har inaktiverats för din skalnings uppsättning om skalnings uppsättnings instanserna inte gör en begäran i 24 timmar.

### <a name="endpoint-discovery"></a>Slut punkts identifiering
För virtuella VNET-aktiverade virtuella datorer är Metadata Service tillgängliga från en statisk icke-dirigerbart IP-adress, 169.254.169.254.

Den fullständiga slut punkten för den senaste versionen av Schemalagda händelser är:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Fråge svar
Ett svar innehåller en matris med schemalagda händelser. En tom matris innebär att det inte finns några schemalagda händelser för tillfället.

Om det finns schemalagda händelser innehåller svaret en händelse mat ris. För händelsen "avsluta" kommer svaret att se ut så här:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
*DocumentIncarnation* är en etag och ger ett enkelt sätt att kontrol lera om händelse nytto lasten har ändrats sedan den senaste frågan.

Mer information om vart och ett av fälten ovan finns i Schemalagda händelser dokumentationen för [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) och [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Svara på händelser
När du har lärt dig en kommande händelse och slutfört din logik för en korrekt avstängning kan du godkänna den utestående händelsen genom att göra ett POST-anrop till metadatatjänsten med EventId. Anropet POST visar till Azure att det kan fortsätta med den virtuella datorn ta bort.

Nedan förväntas JSON i POSTens brödtext. Begäran bör innehålla en lista över StartRequests. Varje StartRequest innehåller EventId för den händelse som du vill påskynda:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Se till att varje virtuell dator i skalnings uppsättningen bara godkänner EventID som är relevant för den virtuella datorn. En virtuell dator kan hämta sitt eget VM-namn [via instansens metadata](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Det här namnet använder formatet "{Scale-set-Name} _ {instance-ID}" och visas i avsnittet "resurser" i svars frågan som beskrivs ovan.

Du kan också referera till exempel skript för frågor och svar på händelser [python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Tips och regelverk
-   Avsluta endast meddelanden på Delete-åtgärder – alla borttagnings åtgärder (manuell borttagning eller autoskalning initierad skalning-i) genererar avslutande händelser om skalnings uppsättningen har *scheduledEventsProfile* aktive rad. Andra åtgärder, t. ex. omstart, avbildning, omdistribution och stoppa/frigör genererar inte avslutande händelser. Det går inte att aktivera meddelanden för virtuella datorer med låg prioritet.
-   Ingen obligatorisk vänte tid för timeout – du kan starta åtgärden avsluta när som helst efter att händelsen har mottagits och innan händelsens *NotBefore* tid upphör att gälla.
-   Obligatorisk borttagning vid timeout – det finns inte någon möjlighet att förlänga timeout-värdet efter att en händelse har genererats. När tids gränsen har gått ut bearbetas den väntande avslutnings händelsen och den virtuella datorn tas bort.
-   Ändrings Bart timeout-värde – du kan ändra timeout-värdet när som helst innan en instans tas bort, genom att ändra egenskapen *notBeforeTimeout* i skalnings uppsättnings modellen och uppdatera VM-instanserna till den senaste modellen.
-   Godkänn alla väntande borttagningar – om det finns en väntande borttagning på VM_1 som inte har godkänts, och du har godkänt en annan avbrotts händelse på VM_2, tas VM_2 inte bort förrän avbrotts händelsen för VM_1 har godkänts eller om tids gränsen har gått ut. När du godkänner händelsen Avsluta för VM_1 raderas både VM_1 och VM_2.
-   Godkänn alla samtidiga borttagningar – utöka ovanstående exempel, om VM_1 och VM_2 har samma *NotBefore* -tid måste båda säga upp-händelserna godkännas eller så tas ingen virtuell dator bort innan tids gränsen upphör att gälla.

## <a name="troubleshoot"></a>Felsöka
### <a name="failure-to-enable-scheduledeventsprofile"></a>Det gick inte att aktivera scheduledEventsProfile
Om du får ett "BadRequest"-fel med meddelandet "Det gick inte att hitta medlemmen" scheduledEventsProfile "för objekt av typen" VirtualMachineProfile "", kontrol lera den API-version som används för skalnings uppsättnings åtgärderna. Compute API version **2019-03-01** eller senare krävs. 

### <a name="failure-to-get-terminate-events"></a>Det gick inte att hämta avslutande händelser
Om du inte får några **avbrotts** händelser via schemalagda händelser kontrollerar du den API-version som används för att hämta händelserna. Metadata Service-API version **2019-01-01** eller högre krävs för att avsluta händelser.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Avbryter händelsen med felaktig NotBefore-tid  
När du har aktiverat *scheduledEventsProfile* i skalnings uppsättnings modellen och angett *notBeforeTimeout*, uppdaterar du de enskilda instanserna till den [senaste modellen](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) för att avspegla ändringarna.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på virtuella datorers skalnings uppsättningar.
