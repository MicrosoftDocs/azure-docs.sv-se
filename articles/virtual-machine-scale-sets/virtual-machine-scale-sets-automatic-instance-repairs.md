---
title: Automatiska instansreparationer med skalningsuppsättningar för virtuella Azure-datorer
description: Lär dig hur du konfigurerar automatiska reparationsprinciper för VM-instanser i en skalningsuppsättning
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 733f4602e43511924783f6bc8cb1bad29edb5ea0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762919"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatisk reparation av instanser för skalningsuppsättningar med virtuella Azure-datorer

Genom att aktivera automatiska instansreparationer för Skalningsuppsättningar för virtuella Azure-datorer kan du uppnå hög tillgänglighet för program genom att upprätthålla en uppsättning felfria instanser. Om en instans i skalningsuppsättningen inte är [](./virtual-machine-scale-sets-health-extension.md) feltillstånd som rapporteras av hälsotillägget för program eller hälsoavsökningar för [lastbalanserare](../load-balancer/load-balancer-custom-probe-overview.md), utför den här funktionen automatiskt instansreparation genom att ta bort den felaktiga instansen och skapa en ny som ersätter den.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Krav för att använda automatiska instansreparationer

**Aktivera övervakning av programhälsa för skalningsuppsättning**

Skalningsuppsättningen ska ha övervakning av programhälsa för instanser aktiverade. Detta kan göras med hjälp av [antingen Application Health-tillägget](./virtual-machine-scale-sets-health-extension.md) eller [Hälsoavsökningar för lastbalanserare.](../load-balancer/load-balancer-custom-probe-overview.md) Endast en av dessa kan aktiveras i taget. Programhälsotillägget eller lastbalanseringsavsökningen pingar programslutpunkten som konfigurerats på virtuella datorinstanser för att fastställa programmets hälsostatus. Den här hälsostatusen används av skalningsuppsättningens initierare för att övervaka instanshälsan och utföra reparationer när det behövs.

**Konfigurera slutpunkten för att ange hälsostatus**

Innan du aktiverar en princip för automatisk instansreparation bör du se till att skalningsuppsättningsinstanserna har programslutpunkter konfigurerade för att generera programmets hälsostatus. När en instans returnerar status 200 (OK) på den här programslutpunkten markeras instansen som "Felfri". I alla andra fall markeras instansen som "Inte feltillstånd", inklusive följande scenarier:

- När det inte finns någon programslutpunkt konfigurerad i de virtuella datorinstanserna för att ange programmets hälsostatus
- När programslutpunkten är felaktigt konfigurerad
- När programslutpunkten inte kan nås

För instanser som markerats som "Inte felsamma" utlöses automatiska reparationer av skalningsuppsättningen. Kontrollera att programslutpunkten är korrekt konfigurerad innan du aktiverar principen för automatiska reparationer för att undvika oavsiktliga instansreparationer medan slutpunkten konfigureras.

**Maximalt antal instanser i skalningsuppsättningen**

Den här funktionen är för närvarande endast tillgänglig för skalningsuppsättningar som har högst 200 instanser. Skalningsuppsättningen kan distribueras som antingen en enda placeringsgrupp eller en flerplaceringsgrupp, men instansantalet kan inte vara över 200 om automatiska instansreparationer har aktiverats för skalningsuppsättningen.

**API-version**

Princip för automatiska reparationer stöds för beräknings-API-version 2018-10-01 eller senare.

**Begränsningar för flytt av resurser eller prenumerationer**

Flytt av resurser eller prenumerationer stöds för närvarande inte för skalningsuppsättningar när funktionen för automatiska reparationer är aktiverad.

**Begränsning för Service Fabric-skalningsuppsättningar**

Den här funktionen stöds för närvarande inte för Service Fabric-skalningsuppsättningar.

## <a name="how-do-automatic-instance-repairs-work"></a>Hur fungerar automatiska instansreparationer?

Funktionen för automatisk instansreparation är beroende av hälsoövervakning av enskilda instanser i en skalningsuppsättning. VM-instanser i en skalningsuppsättning kan konfigureras för att generera programhälsostatus med hjälp av antingen [application health-tillägget](./virtual-machine-scale-sets-health-extension.md) eller [lastbalanseringshälsoavsökningar .](../load-balancer/load-balancer-custom-probe-overview.md) Om en instans inte är fel kommer skalningsuppsättningen att utföra reparationsåtgärden genom att ta bort den felaktiga instansen och skapa en ny som ersätter den. Den senaste vm-skalningsuppsättningsmodellen används för att skapa den nya instansen. Den här funktionen kan aktiveras i VM-skalningsuppsättningsmodellen med hjälp av *objektet automaticRepairsPolicy.*

### <a name="batching"></a>Batchbearbetning

De automatiska reparationsåtgärderna utförs i batchar. Vid en given tidpunkt repareras högst 5 % av instanserna i skalningsuppsättningen via den automatiska reparationsprincipen. På så sätt undviker du samtidig borttagning och att ett stort antal instanser skapas på nytt om de inte är felhälsosamma samtidigt.

### <a name="grace-period"></a>Respitperiod

När en instans går igenom en tillståndsändringsåtgärd på grund av en PUT-, PATCH- eller POST-åtgärd som utförts på skalningsuppsättningen (till exempel återimage, omdistribuering, uppdatering osv.) utförs alla reparationsåtgärder på den instansen först efter att ha väntat på respitperioden. Respitperiod är hur lång tid det tar att tillåta att instansen återgår till felfritt tillstånd. Respitperioden startar när tillståndsändringen har slutförts. På så sätt undviker du förtida eller oavsiktliga reparationsåtgärder. Respitperioden gäller för alla nyligen skapade instanser i skalningsuppsättningen (inklusive den som skapats som ett resultat av reparationsåtgärden). Respitperioden anges i minuter i ISO 8601-format och kan anges med egenskapen *automaticRepairsPolicy.gracePeriod*. Respitperioden kan vara mellan 30 minuter och 90 minuter och har standardvärdet 30 minuter.

### <a name="suspension-of-repairs"></a>Indragning av reparationer 

Skalningsuppsättningar för virtuella datorer ger möjlighet att tillfälligt pausa automatiska instansreparationer om det behövs. *ServiceState för* automatiska reparationer under egenskapen *orchestrationServices* i instansvyn för VM-skalningsuppsättningen visar det aktuella tillståndet för de automatiska reparationerna. När en skalningsuppsättning används för automatiska reparationer anges värdet för *parametern serviceState* till *Körs.* När de automatiska reparationerna pausas för en skalningsuppsättning anges parametern *serviceState* till *Pausad*. Om *automaticRepairsPolicy* har definierats på en skalningsuppsättning men funktionen för automatiska reparationer inte är aktiverad är parametern *serviceState* inställd *på Körs inte.*

Om nyligen skapade instanser för att ersätta de felande instanserna i en skalningsuppsättning fortsätter att vara felaktiga även efter upprepade reparationsåtgärder, uppdaterar plattformen som en säkerhetsåtgärd *serviceState* för automatiska reparationer till *Pausad.* Du kan återuppta de automatiska reparationarna igen genom att ange värdet *för serviceState* för automatiska reparationer till *Körs.* Detaljerade anvisningar finns i avsnittet om att [visa och uppdatera tjänsttillståndet för principen för automatiska reparationer för](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) din skalningsuppsättning. 

Processen för automatiska instansreparationer fungerar på följande sätt:

1. [Application Health-tillägget](./virtual-machine-scale-sets-health-extension.md) eller [Lastbalanseringshälsoavsökningar](../load-balancer/load-balancer-custom-probe-overview.md) pingar programslutpunkten i varje virtuell dator i skalningsuppsättningen för att hämta programmets hälsostatus för varje instans.
2. Om slutpunkten svarar med statusen 200 (OK) markeras instansen som "Felfri". I alla andra fall (inklusive om slutpunkten inte kan nås) markeras instansen som "Inte felig".
3. När en instans är skadad utlöser skalningsuppsättningen en reparationsåtgärd genom att ta bort den felaktiga instansen och skapa en ny för att ersätta den.
4. Instansreparationer utförs i batchar. Vid en given tidpunkt repareras inte mer än 5 % av det totala antalet instanser i skalningsuppsättningen. Om en skalningsuppsättning har färre än 20 instanser utförs reparationerna för en instans med fel i taget.
5. Processen ovan fortsätter tills alla felaktiga instanser i skalningsuppsättningen har reparerats.

## <a name="instance-protection-and-automatic-repairs"></a>Instansskydd och automatiska reparationer

Om en instans i en skalningsuppsättning [](./virtual-machine-scale-sets-instance-protection.md)skyddas av en av skyddsprinciperna utförs inte automatiska reparationer på den instansen. Detta gäller både skyddsprinciperna: *Skydda från inskalning* *och Skydda från skalningsuppsättningsåtgärder.* 

## <a name="terminatenotificationandautomaticrepairs"></a>Avsluta meddelande och automatiska reparationer

Om funktionen [avsluta meddelande](./virtual-machine-scale-sets-terminate-notification.md) är aktiverad på en skalningsuppsättning, kommer borttagningen av en instans med feltillstånd att följa aviseringskonfigurationen för att avsluta under den automatiska reparationen. Ett avisering om uppsägning skickas via Azures metadatatjänst – schemalagda händelser – och borttagningen av instansen fördröjs under den konfigurerade tidsgränsen. Men skapandet av en ny instans som ersätter den felaktiga väntar inte på att tidsgränsen för fördröjning ska slutföras.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Aktivera princip för automatisk reparation när du skapar en ny skalningsuppsättning

För att aktivera automatisk reparationsprincip när du skapar [](#requirements-for-using-automatic-instance-repairs) en ny skalningsuppsättning, se till att alla krav för att välja den här funktionen är uppfyllda. Programslutpunkten bör vara korrekt konfigurerad för skalningsuppsättningsinstanser för att undvika att utlösa oönskade reparationer medan slutpunkten konfigureras. För nyligen skapade skalningsuppsättningar utförs alla instansreparationer först efter väntetiden för respitperioden. Om du vill aktivera automatisk instansreparation i en skalningsuppsättning använder *du automaticRepairsPolicy-objektet* i VM-skalningsuppsättningsmodellen.

Du kan också använda den här [snabbstartsmallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) för att distribuera en VM-skalningsuppsättning med hälsoavsökning för lastbalanserare och automatiska instansreparationer aktiverade med en respitperiod på 30 minuter.

### <a name="azure-portal"></a>Azure Portal
 
Följande steg aktiverar princip för automatisk reparation när du skapar en ny skalningsuppsättning.
 
1. Gå till **VM-skalningsuppsättningar.**
1. Välj **+ Lägg till** för att skapa en ny skalningsuppsättning.
1. Gå till **fliken** Hälsa. 
1. Leta upp **avsnittet** Hälsa.
1. Aktivera alternativet **Övervaka programmets** hälsotillstånd.
1. Leta upp **avsnittet Princip för automatisk** reparation.
1. Aktivera **alternativet** **Automatiska** reparationer.
1. I **Respitperiod (min)** anger du respitperioden i minuter, tillåtna värden är mellan 30 och 90 minuter. 
1. När du är klar med att skapa den nya skalningsuppsättningen väljer **du knappen Granska +** skapa.

### <a name="rest-api"></a>REST-API

I följande exempel visas hur du aktiverar automatisk instansreparation i en skalningsuppsättningsmodell. Använd API-version 2018-10-01 eller senare.

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

Funktionen för automatisk instansreparation kan aktiveras när du skapar en ny skalningsuppsättning med hjälp av cmdleten [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Det här exempelskriptet går igenom skapandet av en skalningsuppsättning och associerade resurser med hjälp av konfigurationsfilen: [Skapa en fullständig VM-skalningsuppsättning.](./scripts/powershell-sample-create-complete-scale-set.md) Du kan konfigurera en princip för automatisk instansreparation genom att lägga till parametrarna *EnableAutomaticRepair* och *AutomaticRepairGracePeriod* i konfigurationsobjektet för att skapa skalningsuppsättningen. I följande exempel aktiveras funktionen med en respitperiod på 30 minuter.

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

I följande exempel aktiveras principen för automatiska reparationer när du skapar en ny skalningsuppsättning med *[az vmss create](/cli/azure/vmss#az_vmss_create)*. Skapa först en resursgrupp och skapa sedan en ny skalningsuppsättning med respitperioden för automatiska reparationer på 30 minuter.

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

I exemplet ovan används en befintlig lastbalanserare och hälsoavsökning för övervakning av instansers programhälsostatus. Om du föredrar att använda ett programhälsotillägg för övervakning i stället kan du skapa en skalningsuppsättning, konfigurera tillägget för programhälsa och sedan aktivera principen för automatisk instansreparation med hjälp av *az vmss update*, enligt förklaringen i nästa avsnitt.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Aktivera princip för automatisk reparation vid uppdatering av en befintlig skalningsuppsättning

Se till att alla krav för att välja den [](#requirements-for-using-automatic-instance-repairs) här funktionen är uppfyllda innan du aktiverar principen för automatiska reparationar i en befintlig skalningsuppsättning. Programslutpunkten bör vara korrekt konfigurerad för skalningsuppsättningsinstanser för att undvika att utlösa oönskade reparationer medan slutpunkten konfigureras. Om du vill aktivera automatisk instansreparation i en skalningsuppsättning använder *du automaticRepairsPolicy-objektet* i VM-skalningsuppsättningsmodellen.

När du har uppdaterat modellen för en befintlig skalningsuppsättning ser du till att den senaste modellen tillämpas på alla instanser av skalan. Se instruktionen om hur du gör virtuella datorer uppdaterade med [den senaste skalningsuppsättningsmodellen.](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-portal"></a>Azure Portal

Du kan ändra principen för automatiska reparationer för en befintlig skalningsuppsättning via Azure Portal. 
 
1. Gå till en befintlig VM-skalningsuppsättning.
1. Under **Inställningar** i menyn till vänster väljer du **Hälsa och reparation.**
1. Aktivera alternativet **Övervaka programmets** hälsotillstånd.
1. Leta upp **avsnittet Automatisk reparationsprincip.**
1. Aktivera **alternativet** **Automatiska** reparationer.
1. I **Respitperiod (min)** anger du respitperioden i minuter. Tillåtna värden är mellan 30 och 90 minuter. 
1. När du är klar väljer du **Spara**. 

### <a name="rest-api"></a>REST-API

I följande exempel aktiveras principen med en respitperiod på 40 minuter. Använd API-version 2018-10-01 eller senare.

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

Använd [cmdleten Update-AzVmss](/powershell/module/az.compute/update-azvmss) för att ändra konfigurationen av funktionen för automatisk instansreparation i en befintlig skalningsuppsättning. I följande exempel uppdateras respitperioden till 40 minuter.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Följande är ett exempel på hur du uppdaterar principen för automatisk instansreparation för en befintlig skalningsuppsättning med *[az vmss update](/cli/azure/vmss#az_vmss_update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Visa och uppdatera tjänsttillståndet för automatisk instansreparationsprincip

### <a name="rest-api"></a>REST-API 

Använd [Hämta](/rest/api/compute/virtualmachinescalesets/getinstanceview) instansvy med API-version 2019-12-01 eller senare för VM-skalningsuppsättning för att visa *serviceState* för automatiska reparationer under egenskapen *orchestrationServices*. 

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

Använd *setOrchestrationServiceState* API med API-version 2019-12-01 eller senare på en VM-skalningsuppsättning för att ange tillståndet för automatiska reparationer. När skalningsuppsättningen har valt funktionen för automatiska reparationer kan du använda det här API:et för att pausa eller återuppta automatiska reparationer för din skalningsuppsättning. 

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

Använd [cmdleten get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view) för att visa *serviceState* för automatiska instansreparationer. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Använd cmdleten [set-orchestration-service-state](/cli/azure/vmss#az_vmss_set_orchestration_service_state) för att uppdatera *serviceState* för automatiska instansreparationer. När skalningsuppsättningen har valt funktionen för automatisk reparation kan du använda denna cmdlet för att pausa eller återuppta automatiska reparationer för din skalningsuppsättning. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Använd [cmdleten Get-AzVmss](/powershell/module/az.compute/get-azvmss) med *parametern InstanceView* för att visa *ServiceState* för automatiska instansreparationer.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Använd Set-AzVmssOrchestrationServiceState för att uppdatera *serviceState för* automatiska instansreparationer. När skalningsuppsättningen har valt funktionen för automatisk reparation kan du använda denna cmdlet för att pausa eller återuppta automatiska reparationer för din skalningsuppsättning.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Felsöka

**Det gick inte att aktivera principen för automatiska reparationer**

Om du får ett "BadRequest"-fel med meddelandet "Det gick inte att hitta medlemmen "automaticRepairsPolicy" för objektet av typen "properties" kontrollerar du den API-version som används för VM-skalningsuppsättningen. API-version 2018-10-01 eller senare krävs för den här funktionen.

**Instansen repareras inte även när principen är aktiverad**

Instansen kan vara inom respitperioden. Det här är den tid det tar att vänta efter en tillståndsändring av instansen innan reparationen. Detta för att undvika för tidiga eller oavsiktliga reparationer. Reparationsåtgärden bör utföras när respitperioden har slutförts för instansen.

**Visa programhälsostatus för skalningsuppsättningsinstanser**

Du kan använda [API:et Hämta instansvy](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) för instanser i en VM-skalningsuppsättning för att visa programmets hälsostatus. Med Azure PowerShell kan du använda cmdleten [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) med *flaggan -InstanceView.* Programmets hälsostatus anges under egenskapen *vmHealth*.

I Azure Portal kan du även se hälsostatusen. Gå till en befintlig skalningsuppsättning, välj Instanser på menyn  till vänster och titta på kolumnen Hälsotillstånd för hälsostatus för varje skalningsuppsättningsinstans.  

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar hälsoavsökningar för](./virtual-machine-scale-sets-health-extension.md) Application Health eller [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) för dina skalningsuppsättningar.
