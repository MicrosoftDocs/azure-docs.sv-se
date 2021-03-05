---
title: Automatisk instans reparation med skalnings uppsättningar för virtuella Azure-datorer
description: Lär dig hur du konfigurerar princip för automatisk reparation för VM-instanser i en skalnings uppsättning
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 080666c9857c1a3dc509ca980bc85b1dc11b5975
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214294"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatisk reparation av instanser för skalningsuppsättningar med virtuella Azure-datorer

Om du aktiverar automatiska instans reparationer för skalnings uppsättningar för virtuella Azure-datorer kan du få hög tillgänglighet för program genom att underhålla en uppsättning felfria instanser. Om en instans i skalnings uppsättningen inte är felfri som rapporteras av hälso tillstånds [tillägg för program](./virtual-machine-scale-sets-health-extension.md) eller hälso [avsökningar för belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md), utför den här funktionen automatiskt instans reparation genom att ta bort den felaktiga instansen och skapa en ny för att ersätta den.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Krav för att använda automatiska instans reparationer

**Aktivera övervakning av program hälsa för skalnings uppsättning**

Skalnings uppsättningen ska ha program hälso övervakning för aktiverade instanser. Detta kan göras med hjälp av antingen [program hälso tillägg](./virtual-machine-scale-sets-health-extension.md) eller [hälso avsökningar för belastnings utjämning](../load-balancer/load-balancer-custom-probe-overview.md). Endast ett av dessa kan aktive ras åt gången. Program hälso tillägget eller belastningsutjämnaren avsöker pinga program slut punkten som kon figurer ATS på virtuella dator instanser för att fastställa programmets hälso status. Denna hälso status används av skalnings uppsättningens Orchestrator för att övervaka instans hälsa och utföra reparationer vid behov.

**Konfigurera slut punkten för att ange hälso status**

Innan du aktiverar principen för automatiska reparationer av instanser måste du kontrol lera att skalnings uppsättnings instanserna har program slut punkten konfigurerad för att generera programmets hälso status. När en instans returnerar status 200 (OK) för den här program slut punkten markeras instansen som "felfri". I alla andra fall markeras instansen som "skadad", inklusive följande scenarier:

- När ingen program slut punkt har kon figurer ATS i de virtuella dator instanserna för att tillhandahålla program hälso status
- När program slut punkten är felaktigt konfigurerad
- När det inte går att komma åt program slut punkten

För instanser som har marker ATS som "ohälsosam" utlöses automatiska reparationer av skalnings uppsättningen. Se till att program slut punkten är korrekt konfigurerad innan du aktiverar principen för automatisk reparation för att undvika oavsiktliga instans reparationer, medan slut punkten konfigureras.

**Maximalt antal instanser i skalnings uppsättningen**

Den här funktionen är för närvarande endast tillgänglig för skalnings uppsättningar som har högst 200 instanser. Skalnings uppsättningen kan distribueras antingen som en enda placerings grupp eller med flera placerings grupper, men instans antalet får inte vara högre än 200 om automatiska instans reparationer är aktiverat för skalnings uppsättningen.

**API-version**

Princip för automatisk reparation stöds för Compute API version 2018-10-01 eller senare.

**Begränsningar för flytt av resurser eller prenumeration**

Flytt av resurser eller prenumerationer stöds för närvarande inte för skalnings uppsättningar när funktionen automatiska reparationer är aktive rad.

**Begränsning för Service Fabric Scale Sets**

Den här funktionen stöds för närvarande inte för Service Fabric-skalnings uppsättningar.

## <a name="how-do-automatic-instance-repairs-work"></a>Hur fungerar automatisk reparation av instanser?

Funktionen reparera automatisk instans använder hälso övervakning av enskilda instanser i en skalnings uppsättning. Virtuella dator instanser i en skalnings uppsättning kan konfigureras för att generera program hälso status med antingen [program hälso tillägg](./virtual-machine-scale-sets-health-extension.md) eller [hälso avsökningar för belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md). Om en instans är ohälsosam, utför skalnings uppsättningen reparations åtgärden genom att ta bort den felaktiga instansen och skapa en ny för att ersätta den. Den senaste modellen för skalnings uppsättningen för virtuella datorer används för att skapa den nya instansen. Den här funktionen kan aktive ras i den virtuella datorns skalnings uppsättnings modell med hjälp av *automaticRepairsPolicy* -objektet.

### <a name="batching"></a>Batchbearbetning

Reparations åtgärder för automatisk instans utförs i batchar. Vid en angiven tidpunkt repare ras inte mer än 5% av instanserna i skalnings uppsättningen genom principen för automatisk reparation. Detta bidrar till att undvika Samtidig borttagning och att skapa ett stort antal instanser på nytt, om det upptäcks dåligt på samma gång.

### <a name="grace-period"></a>Respitperiod

När en instans går igenom en status ändrings åtgärd på grund av en skicka-, KORRIGERINGs-eller POST-åtgärd som utförs på skalnings uppsättningen (till exempel avbildning, omdistribution, uppdatering osv.), utförs eventuell reparations åtgärd på den instansen först efter att du har väntat på respitperioden. Respitperiod är hur lång tid som tillåts för instansen att återgå till felfritt tillstånd. Grace-perioden startar efter att tillstånds ändringen har slutförts. Detta bidrar till att undvika oförutsedda eller oavsiktliga reparations åtgärder. Respittiden gäller för alla nyskapade instanser i skalnings uppsättningen (inklusive den som skapats som ett resultat av reparations åtgärden). Grace-perioden anges i minuter i ISO 8601-format och kan anges med egenskapen *automaticRepairsPolicy. gracePeriod*. Grace-perioden kan vara mellan 30 minuter och 90 minuter och har ett standardvärde på 30 minuter.

### <a name="suspension-of-repairs"></a>Upphävande av reparationer 

Skalnings uppsättningar för virtuella datorer ger möjlighet att tillfälligt pausa automatiska instans reparationer vid behov. *ServiceState* för automatisk reparation under egenskapen *orchestrationServices* i instans visning av skalnings uppsättningen för virtuella datorer visar det aktuella läget för de automatiska reparationerna. När en skalnings uppsättning väljs för automatiska reparationer anges värdet för parametern *serviceState* till *körs*. När de automatiska reparationerna har inaktiverats för en skalnings uppsättning är parametern *serviceState* inställd på *pausad*. Om *automaticRepairsPolicy* har definierats i en skalnings uppsättning men funktionen automatiska reparationer inte är aktive rad, är parametern *serviceState* inställd på att *inte köras*.

Om nyligen skapade instanser för att ersätta de defekta i en skalnings uppsättning fortsätter att vara felfria även när reparations åtgärderna har utförts upprepade gånger, och som en säkerhets åtgärd, uppdaterar plattformen *serviceState* för automatisk reparation till *pausad*. Du kan återuppta den automatiska reparationen igen genom att ange värdet för *serviceState* för automatisk reparation till *körning*. Detaljerade instruktioner finns i avsnittet om att [Visa och uppdatera tjänst status för principen för automatisk reparation](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) för din skalnings uppsättning. 

Processen för automatisk instans reparation fungerar på följande sätt:

1. [Program hälso tillägg](./virtual-machine-scale-sets-health-extension.md) eller [hälso avsökningar för belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md) pingar program slut punkten i varje virtuell dator i skalnings uppsättningen för att få program hälso status för varje instans.
2. Om slut punkten svarar med statusen 200 (OK) markeras instansen som "felfri". I alla andra fall (inklusive om slut punkten inte kan komma åt) är instansen markerad som "ej felfri".
3. När en instans har visat sig vara ohälsosam, utlöser skalnings uppsättningen en reparations åtgärd genom att ta bort den felaktiga instansen och skapa en ny för att ersätta den.
4. Instans reparationer utförs i batchar. Vid en angiven tidpunkt repare ras inte fler än 5% av de totala instanserna i skalnings uppsättningen. Om en skalnings uppsättning har färre än 20 instanser görs reparationen för en skadad instans i taget.
5. Ovanstående process fortsätter tills all skadad instans i skalnings uppsättningen har reparerats.

## <a name="instance-protection-and-automatic-repairs"></a>Instans skydd och automatiska reparationer

Om en instans i en skalnings uppsättning skyddas genom att tillämpa en av [skydds principerna](./virtual-machine-scale-sets-instance-protection.md)utförs inte automatiska reparationer på den instansen. Detta gäller både skydds principerna: *skydda från skalbarhet* och *skydda från skalnings uppsättnings* åtgärder. 

## <a name="terminatenotificationandautomaticrepairs"></a>Avsluta meddelanden och automatiska reparationer

Om funktionen [Avsluta avisering](./virtual-machine-scale-sets-terminate-notification.md) är aktive rad på en skalnings uppsättning, kommer borttagningen av en ohälsosam instans att följa konfigurationen för att avsluta aviseringen under den automatiska reparations åtgärden. Ett avslutnings meddelande skickas via Azure metadata service – schemalagda händelser – och instansen av instansen försenas under den angivna tids gränsen för fördröjning. Skapandet av en ny instans för att ersätta den felaktiga instansen väntar dock inte på att tids gränsen för fördröjningen ska slutföras.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Aktivera automatisk reparations princip när du skapar en ny skalnings uppsättning

För att aktivera principen för automatisk reparation när du skapar en ny skalnings uppsättning, se till att alla [krav](#requirements-for-using-automatic-instance-repairs) för väljer i den här funktionen är uppfyllda. Program slut punkten måste vara korrekt konfigurerad för skalnings uppsättnings instanser för att undvika att oönskade reparationer utlöses när slut punkten konfigureras. Vid nyskapade skalnings uppsättningar utförs alla instans reparationer endast efter att ha väntat på giltighets perioden. Om du vill aktivera reparation av den automatiska instansen i en skalnings uppsättning använder du *automaticRepairsPolicy* -objekt i den virtuella datorns skal uppsättnings modell.

Du kan också använda den här [snabb starts mal len](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) för att distribuera en skalnings uppsättning för virtuella datorer med belastnings Utjämnings hälso avsökning och automatiska instans reparationer aktiverade med en respitperiod på 30 minuter.

### <a name="azure-portal"></a>Azure Portal
 
Följande steg aktiverar automatisk reparations princip när du skapar en ny skalnings uppsättning.
 
1. Gå till **skalnings uppsättningar för virtuella datorer**.
1. Välj **+ Lägg** till för att skapa en ny skalnings uppsättning.
1. Gå till fliken **hälso tillstånd** . 
1. Leta upp avsnittet **hälso tillstånd** .
1. Aktivera alternativet **övervaka program hälsa** .
1. Leta upp avsnittet **Automatisk reparations princip** .
1. Aktivera **alternativet** **automatiska reparationer** .
1. I **respitperiod (min)** anger du respitperioden i minuter, tillåtna värden är mellan 30 och 90 minuter. 
1. När du är klar med att skapa den nya skalnings uppsättningen väljer du **Granska + skapa** .

### <a name="rest-api"></a>REST-API

I följande exempel visas hur du aktiverar automatisk instans reparation i en skalnings uppsättnings modell. Använd API version 2018-10-01 eller senare.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Funktionen reparera automatisk instans kan aktive ras när du skapar en ny skalnings uppsättning med hjälp av cmdleten [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) . Det här exempel skriptet går igenom skapandet av en skalnings uppsättning och associerade resurser med hjälp av konfigurations filen: [skapa en fullständig skalnings uppsättning för virtuella datorer](./scripts/powershell-sample-create-complete-scale-set.md). Du kan konfigurera principen för automatiska reparationer av instanser genom att lägga till parametrarna *EnableAutomaticRepair* och *AutomaticRepairGracePeriod* till konfigurationsobjektet för att skapa skalnings uppsättningen. I följande exempel aktive ras funktionen med en Grace-period på 30 minuter.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

I följande exempel aktive ras principen för automatisk reparation när du skapar en ny skalnings uppsättning med hjälp av *[AZ VMSS Create](/cli/azure/vmss#az-vmss-create)*. Skapa först en resurs grupp och skapa sedan en ny skalnings uppsättning med en respitperiod för automatisk reparations policy angiven till 30 minuter.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

Exemplet ovan använder en befintlig belastningsutjämnare och hälso avsökning för att övervaka program hälso status för instanser. Om du föredrar att använda ett program hälso tillägg för övervakning i stället kan du skapa en skalnings uppsättning, konfigurera program hälso tillägget och sedan aktivera den automatiska instansen reparera principen med hjälp av *AZ VMSS-uppdateringen*, enligt beskrivningen i nästa avsnitt.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Aktivera automatisk reparations princip när du uppdaterar en befintlig skalnings uppsättning

Innan du aktiverar principen för automatisk reparation i en befintlig skalnings uppsättning måste du se till att alla [krav](#requirements-for-using-automatic-instance-repairs) för väljer i den här funktionen är uppfyllda. Program slut punkten måste vara korrekt konfigurerad för skalnings uppsättnings instanser för att undvika att oönskade reparationer utlöses när slut punkten konfigureras. Om du vill aktivera reparation av den automatiska instansen i en skalnings uppsättning använder du *automaticRepairsPolicy* -objekt i den virtuella datorns skal uppsättnings modell.

När du har uppdaterat modellen för en befintlig skalnings uppsättning ser du till att den senaste modellen används på alla instanser av skalan. Se instruktionen om [hur du gör virtuella datorer uppdaterade med den senaste skalnings uppsättnings modellen](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-portal"></a>Azure Portal

Du kan ändra principen för automatisk reparation för en befintlig skalnings uppsättning via Azure Portal. 
 
1. Gå till en befintlig skalnings uppsättning för virtuella datorer.
1. Under **Inställningar** i menyn till vänster väljer du **hälsa och reparation**.
1. Aktivera alternativet **övervaka program hälsa** .
1. Leta upp avsnittet **Automatisk reparations princip** .
1. Aktivera **alternativet** **automatiska reparationer** .
1. I **respitperiod (min)** anger du respitperioden i minuter, tillåtna värden är mellan 30 och 90 minuter. 
1. När du är klar väljer du **Spara**. 

### <a name="rest-api"></a>REST-API

I följande exempel aktive ras principen med en Grace-period på 40 minuter. Använd API version 2018-10-01 eller senare.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Använd cmdleten [Update-AzVmss](/powershell/module/az.compute/update-azvmss) för att ändra konfigurationen av funktionen reparera automatiska instanser i en befintlig skalnings uppsättning. I följande exempel uppdateras Grace-perioden till 40 minuter.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Följande är ett exempel på hur du uppdaterar den automatiska instansen reparerar principen för en befintlig skalnings uppsättning med hjälp av *[AZ VMSS Update](/cli/azure/vmss#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Visa och uppdatera tjänst status för princip för automatisk instans reparation

### <a name="rest-api"></a>REST-API 

Använd [vyn Hämta instans](/rest/api/compute/virtualmachinescalesets/getinstanceview) med API version 2019-12-01 eller högre för skalnings uppsättningen för virtuella datorer för att visa *serviceState* för automatiska reparationer under egenskapen *orchestrationServices*. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Använd *setOrchestrationServiceState* -API med api version 2019-12-01 eller högre på en virtuell dators skalnings uppsättning för att ange status för automatiska reparationer. När skalnings uppsättningen har valts i funktionen automatiska reparationer kan du använda detta API för att pausa eller återuppta automatiska reparationer för din skalnings uppsättning. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

Använd cmdleten [Get-instance-View](/cli/azure/vmss#az-vmss-get-instance-view) för att visa *serviceState* för automatiska instans reparationer. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Använd cmdleten [set-Orchestration-Service-State](/cli/azure/vmss#az-vmss-set-orchestration-service-state) för att uppdatera *serviceState* för automatiska instans reparationer. När skalnings uppsättningen har valts i den automatiska reparations funktionen kan du använda denna cmdlet för att pausa eller återuppta automatiska reparationer för skalnings uppsättningen. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Använd [Get-AzVmss](/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) -cmdlet: en med parametern *InstanceView* för att visa *ServiceState* för automatiska instans reparationer.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Använd Set-AzVmssOrchestrationServiceState cmdlet för att uppdatera *serviceState* för automatiska instans reparationer. När skalnings uppsättningen har valts i den automatiska reparations funktionen kan du använda denna cmdlet för att pausa eller återuppta automatiska reparationer för skalnings uppsättningen.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Felsöka

**Det gick inte att aktivera principen för automatisk reparation**

Om du får ett "BadRequest"-fel med meddelandet "Det gick inte att hitta medlemmen" automaticRepairsPolicy "för objekt av typen" Properties "", kontrollerar du den API-version som används för skalnings uppsättningen för den virtuella datorn. API version 2018-10-01 eller senare krävs för den här funktionen.

**Instansen kan inte repare ras även när principen är aktive rad**

Instansen kan vara i Grace-period. Detta är vänte tiden efter en tillstånds ändring på instansen innan reparationen utförs. Detta är för att undvika oförutsedda eller oavsiktliga reparationer. Reparations åtgärden bör utföras när Grace-perioden har slutförts för instansen.

**Visa program hälso status för skalnings uppsättnings instanser**

Du kan använda [Hämta instans Visa API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) för instanser i en skalnings uppsättning för virtuella datorer för att Visa programmets hälso status. Med Azure PowerShell kan du använda cmdleten [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) med flaggan *-InstanceView* . Programmets hälso status anges under egenskapen *vmHealth*.

I Azure Portal kan du även se hälso status. Gå till en befintlig skalnings uppsättning, Välj **instanser** på menyn till vänster och titta i kolumnen **hälso tillstånd** för hälso status för varje skalnings uppsättnings instans. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar [program hälso tillägg](./virtual-machine-scale-sets-health-extension.md) eller [hälso avsökningar för belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md) för dina skalnings uppsättningar.
