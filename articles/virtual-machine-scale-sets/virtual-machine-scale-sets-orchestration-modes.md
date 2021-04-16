---
title: Orkestreringslägen för VM-skalningsuppsättningar i Azure
description: Lär dig hur du använder flexibla och enhetliga orkestreringslägen för VM-skalningsuppsättningar i Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 72e36a942eeaea00699f346db99a7ca3503495da
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481658"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Förhandsversion: Orkestreringslägen för VM-skalningsuppsättningar i Azure 

Virtual Machines Scale Sets tillhandahåller en logisk gruppering av plattformsbaserade virtuella datorer. Med skalningsuppsättningar skapar du en konfigurationsmodell för virtuella datorer, lägger automatiskt till eller tar bort ytterligare instanser baserat på CPU- eller minnesbelastning och uppgraderar automatiskt till den senaste versionen av operativsystemet. Traditionellt gör skalningsuppsättningar att du kan skapa virtuella datorer med hjälp av en VM-konfigurationsmodell som tillhandahålls när skalningsuppsättningen skapas, och skalningsuppsättningen kan bara hantera virtuella datorer som skapas implicit baserat på konfigurationsmodellen. 

Med orkestreringslägen för skalningsuppsättningen kan du få större kontroll över hur instanser av virtuella datorer hanteras av skalningsuppsättningen. 

> [!IMPORTANT]
> Orkestreringsläget definieras när du skapar skalningsuppsättningen och kan inte ändras eller uppdateras senare.


## <a name="scale-sets-with-uniform-orchestration"></a>Skalningsuppsättningar med enhetlig orkestrering
Optimerad för storskaliga tillståndslösa arbetsbelastningar med identiska instanser.

VM-skalningsuppsättningar med enhetlig orkestrering använder en virtuell datorprofil eller mall för att skala upp till önskad kapacitet. Även om det finns viss möjlighet att hantera eller anpassa enskilda instanser av virtuella datorer använder Uniform identiska VM-instanser. Enskilda uniforma VM-instanser exponeras via API-kommandon för VM-skalningsuppsättningen för virtuella datorer. Enskilda instanser är inte kompatibla med standardkommandona för API:et för virtuella Azure IaaS-datorer, Azure-hanteringsfunktioner som Azure Resource Manager-resurstaggning av RBAC-behörigheter, Azure Backup eller Azure Site Recovery. Enhetlig orkestrering ger garantier för hög tillgänglighet för feldomäner när de konfigureras med färre än 100 instanser. Enhetlig orkestrering är allmänt tillgängligt och stöder en fullständig uppsättning hantering och orkestrering av skalningsuppsättning, inklusive måttbaserad automatisk skalning, instansskydd och automatiska os-uppgraderingar. 


## <a name="scale-sets-with-flexible-orchestration"></a>Skalningsuppsättningar med flexibel orkestrering 
Uppnå hög tillgänglighet i stor skala med identiska eller flera typer av virtuella datorer.

Med flexibel orkestrering ger Azure en enhetlig upplevelse i ekosystemet för virtuella Azure-datorer. Flexibel orkestrering ger garantier för hög tillgänglighet (upp till 1 000 virtuella datorer) genom att sprida virtuella datorer över feldomäner i en region eller i en tillgänglighetszon. På så sätt kan du skala ut ditt program samtidigt som du behåller den feldomänisolering som är nödvändig för att köra kvorumbaserade eller tillståndsfulla arbetsbelastningar, inklusive:
- Kvorumbaserade arbetsbelastningar
- Open-Source databaser
- Tillståndsful-program
- Tjänster som kräver hög tillgänglighet och stor skala
- Tjänster som vill blanda olika typer av virtuella datorer eller utnyttja virtuella datorer med plats och på begäran tillsammans
- Befintliga program för tillgänglighetsuppsättning  

> [!IMPORTANT]
> VM-skalningsuppsättningar i flexibelt orkestreringsläge är för närvarande i offentlig förhandsversion. En procedur för att delta krävs för att använda funktionerna i den offentliga förhandsversionen som beskrivs nedan.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Vad har ändrats med flexibelt orkestreringsläge?
En av de största fördelarna med flexibel orkestrering är att den tillhandahåller orkestreringsfunktioner över virtuella Azure IaaS-standarddatorer i stället för underordnade virtuella datorer med skalningsuppsättning. Det innebär att du kan använda alla STANDARD-API:er för virtuella datorer när du hanterar flexibla orkestreringsinstanser, i stället för VM-API:erna för VM-skalningsuppsättningen som du använder med Enhetlig orkestrering. Under förhandsversionsperioden finns det flera skillnader mellan att hantera instanser i flexibel orkestrering jämfört med enhetlig orkestrering. I allmänhet rekommenderar vi att du använder standard-API:er för virtuella Azure IaaS-datorer när det är möjligt. I det här avsnittet ska vi lyfta fram exempel på metodtips för att hantera VM-instanser med flexibel orkestrering.  

### <a name="assign-fault-domain-during-vm-creation"></a>Tilldela feldomän när den virtuella datorn skapas
Du kan välja antalet feldomäner för skalningsuppsättningen för flexibel orkestrering. När du lägger till en virtuell dator i en flexibel skalningsuppsättning sprider Azure som standard instanser jämnt över feldomäner. Vi rekommenderar att Du låter Azure tilldela feldomänen, men för avancerade scenarier eller felsökningsscenarier kan du åsidosätta det här standardbeteendet och ange feldomänen där instansen ska landa.

```azurecli-interactive 
az vm create â€“vmss "myVMSS"  â€“-platform_fault_domain 1
```

### <a name="instance-naming"></a>Namngivning av instanser 
När du skapar en virtuell dator och lägger till den i en flexibel skalningsuppsättning har du fullständig kontroll över instansnamn i reglerna för Namngivningskonventioner i Azure. När virtuella datorer läggs till automatiskt i skalningsuppsättningen via autoskalning anger du ett prefix och Azure lägger till ett unikt tal i slutet av namnet.

### <a name="query-instances-for-power-state"></a>Fråga instanser för energisparläge
Den bästa metoden är att använda Azure Resource Graph för att fråga efter alla virtuella datorer i en VM-skalningsuppsättning. Azure Resource Graph ger effektiva frågefunktioner för Azure-resurser i stor skala över prenumerationer. 

``` 
|â€¯whereâ€¯typeâ€¯=~â€¯'Microsoft.Compute/virtualMachines' 
|â€¯whereâ€¯properties.virtualMachineScaleSetâ€¯containsâ€¯"demo" 
|â€¯extendâ€¯powerStateâ€¯=â€¯properties.extended.instanceView.powerState.code 
|â€¯projectâ€¯name,â€¯resourceGroup,â€¯location,â€¯powerState 
|â€¯orderâ€¯byâ€¯resourceGroupâ€¯desc,â€¯nameâ€¯desc 
```

Att fråga resurser med [Azure Resource Graph är](../governance/resource-graph/overview.md) ett praktiskt och effektivt sätt att fråga Azure-resurser och minimera API-anrop till resursprovidern. Azure Resource Graph är ett konsekvent cacheminne där nya eller uppdaterade resurser kanske inte återspeglas på upp till 60 sekunder. Du kan:
- Lista virtuella datorer i en resursgrupp eller prenumeration.
- Använd expanderingsalternativet för att hämta instansvyn (tilldelning av feldomäner, ström- och etablerings tillstånd) för alla virtuella datorer i din prenumeration.
- Använd API:et Hämta virtuell dator och kommandon för att hämta modell- och instansvyn för en enskild instans.

### <a name="scale-sets-vm-batch-operations"></a>Skalningsuppsättningar för BATCH-åtgärder för virtuella datorer
Använd standardkommandona för virtuella datorer för att starta, stoppa, starta om och ta bort instanser i stället för VM-API:erna för skalningsuppsättning för virtuella datorer. Batchåtgärder för VM-skalningsuppsättningen (starta alla, stoppa alla, avbildning av alla osv.) används inte i flexibelt orkestreringsläge. 

### <a name="monitor-application-health"></a>Övervaka programhälsa 
Övervakning av programhälsa gör att ditt program kan ge Azure ett pulsslag för att avgöra om programmet är felfritt eller inte. Azure kan automatiskt ersätta VM-instanser som inte är felhälsosamma. För instanser av flexibel skalningsuppsättning måste du installera och konfigurera tillägget för programhälsa på den virtuella datorn. För instanser av en enhetlig skalningsuppsättning kan du använda antingen tillägget för programhälsa eller mäta hälsotillstånd med en Azure Load Balancer anpassad hälsoavsökning. 

### <a name="list-scale-sets-vm-api-changes"></a>Visa en lista över ändringar i VM-API:et för skalningsuppsättningar 
Virtual Machine Scale Sets kan du lista de instanser som tillhör skalningsuppsättningen. Med flexibel orkestrering innehåller listan Virtual Machine Scale Sets VM-kommandot en lista över VM-ID:n för skalningsuppsättningar. Du kan sedan anropa GET Virtual Machine Scale Sets VM-kommandon för att få mer information om hur skalningsuppsättningen fungerar med VM-instansen. Om du vill ha fullständig information om den virtuella datorn använder du standardkommandona för GET VM [eller Azure Resource Graph](../governance/resource-graph/overview.md). 

### <a name="retrieve-boot-diagnostics-data"></a>Hämta startdiagnostikdata 
Använd standard-API:er och kommandon för virtuella datorer för att hämta startdiagnostikdata för instanser och skärmbilder. De Virtual Machine Scale Sets API:erna och kommandona för start av virtuella datorer används inte med instanser i flexibelt orkestreringsläge.

### <a name="vm-extensions"></a>VM-tillägg 
Använd tillägg riktade för virtuella standarddatorer i stället för tillägg som är riktade till instanser i enhetligt orkestreringsläge.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>En jämförelse av flexibla, enhetliga och tillgänglighetsuppsättningar 
I följande tabell jämförs flexibelt orkestreringsläge, enhetligt orkestreringsläge och tillgänglighetsuppsättningar med hjälp av deras funktioner.

| Funktion | Stöds av flexibel orkestrering (förhandsversion) | Stöds av Enhetlig orkestrering (allmän tillgänglighet) | Stöds av AvSets (allmän tillgänglighet) |
|-|-|-|-|
|         Typ av virtuell dator  | Standard Azure IaaS VM (Microsoft.compute /virtualmachines)  | Skalningsuppsättningsspecifika virtuella datorer (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | Standard Azure IaaS VM (Microsoft.compute /virtualmachines)  |
|         SKU:er som stöds  |            D-serien, E-serien, F-serien, A-serien, B-serien, Intel, AMD  |            Alla SKU:er  |            Alla SKU:er  |
|         Tillgänglighetszoner  |            Om du vill kan du ange att alla instanser hamnar i en enda tillgänglighetszon |            Ange instanser som hamnar över 1, 2 eller 3 tillgänglighetszoner  |            Stöds inte  |
|         Fullständig kontroll över virtuella datorer, nätverkskort, diskar  |            Ja  |            Begränsad kontroll med VM Scale Sets VM API  |            Ja  |
|         Automatisk skalning  |            Inga  |            Ja  |            Inga  |
|         Tilldela en virtuell dator till en specifik feldomän  |            Ja  |             Inga   |            Inga  |
|         Ta bort nätverkskort och diskar när du tar bort VM-instanser  |            Inga  |            Ja  |            Inga  |
|         Uppgraderingsprincip (VM Scale Sets) |            Inga  |            Automatisk, rullande, manuell  |            Ej tillämpligt  |
|         Automatiska os-uppdateringar (VM Scale Sets) |            Inga  |            Ja  |            Ej tillämpligt  |
|         I Gästsäkerhetskorrigering  |            Ja  |            Inga  |            Ja  |
|         Avsluta meddelanden (VM Scale Sets) |            Inga  |            Ja  |            Ej tillämpligt  |
|         Instansreparation (VM Scale Sets) |            Inga  |            Ja   |            Ej tillämpligt  |
|         Snabbare nätverk  |            Ja  |            Ja  |            Ja  |
|         Spotâ€ âinstances and pricingâ€ â  |            Ja, du kan ha instanser med både punkt- och prioritetsordning  |            Ja, instanser måste antingen vara alla platser eller alla vanliga  |            Nej, endast instanser med vanlig prioritet  |
|         Blanda operativsystem  |            Ja, Linux och Windows kan finnas i samma flexibla skalningsuppsättning |            Nej, instanser är samma operativsystem  |               Ja, Linux och Windows kan finnas i samma flexibla skalningsuppsättning |
|         Övervaka programmets hälsotillstånd  |            Tillägg för programhälsa  |            Programhälsotillägg eller Azure Load Balancer-avsökning  |            Tillägg för programhälsa  |
|         UltraSSDâ€ âDisksâ€ °  |            Ja  |            Ja, endast för zonindeala distributioner  |            Inga  |
|         Infinibandâ€ °  |            Inga  |            Ja, endast en placeringsgrupp  |            Ja  |
|         Writeâ€ âAcceleratorâ€ °  |            Inga  |            Ja  |            Ja  |
|         Proximityâ€ âPlacement Groupsâ€ â  |            Ja  |            Ja  |            Ja  |
|         Dedikerade Azure-värdarâ° â  |            Inga  |            Ja  |            Ja  |
|         Basic SLBâ€ °  |            Inga  |            Ja  |            Ja  |
|         Azure Load Balancer Standard-SKU |            Ja  |            Ja  |            Ja  |
|         Application Gateway  |            Inga  |            Ja  |            Ja  |
|         Underhållskontrollâ€ °  |            Inga  |            Ja  |            Ja  |
|         Lista virtuella datorer i set  |            Ja  |            Ja  |            Ja, lista virtuella datorer i AvSet  |
|         Azure-aviseringar  |            Inga  |            Ja  |            Ja  |
|         VM-insikter  |            Inga  |            Ja  |            Ja  |
|         Azure Backup  |            Ja  |            Ja  |            Ja  |
|         Azure Site Recovery  |     Inga  |            Inga  |            Ja  |
|         Lägga till/ta bort befintlig virtuell dator i gruppen  |            Inga  |            Inga  |            Inga  | 


## <a name="register-for-flexible-orchestration-mode"></a>Registrera dig för flexibelt orkestreringsläge
Innan du kan distribuera VM-skalningsuppsättningar i flexibelt orkestreringsläge måste du först registrera din prenumeration för förhandsversionsfunktionen. Registreringen kan ta flera minuter att slutföra. Du kan använda följande kommandon Azure PowerShell Azure CLI för att registrera dig.

### <a name="azure-portal"></a>Azure-portalen
Gå till informationssidan för den prenumeration som du vill skapa en skalningsuppsättning i flexibelt orkestreringsläge och välj Förhandsgranskningsfunktioner på menyn. Välj de två orkestreringsfunktioner som ska aktiveras: _VMOrchestratorSingleFD_ och _VMOrchestratorMultiFD_ och tryck på knappen Registrera. Funktionsregistreringen kan ta upp till 15 minuter.

![Funktionsregistrering.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

När funktionerna har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern. Gå till fliken Resursproviders för din prenumeration, välj Microsoft.compute och klicka på Registrera igen.

![Registrera dig på ett annat sätt](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


### <a name="azure-powershell"></a>Azure PowerShell 
Använd [cmdleten Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) för att aktivera förhandsversionen för din prenumeration. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Azure CLI 2.0 
Använd [az feature register](/cli/azure/feature#az-feature-register) för att aktivera förhandsversionen för din prenumeration. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Kom igång med flexibelt orkestreringsläge

Kom igång med flexibelt orkestreringsläge för dina skalningsuppsättningar via Azure Portal, Azure CLI, Terraform eller REST API.

### <a name="azure-portal"></a>Azure Portal

Skapa en VM-skalningsuppsättning i flexibelt orkestreringsläge via Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. I sökfältet söker du efter och väljer **VM-skalningsuppsättningar.** 
1. Välj **Skapa** på sidan **VM-skalningsuppsättningar.**
1. På sidan **Skapa en VM-skalningsuppsättning** visar du **avsnittet Orkestrering.**
1. För **Orkestreringsläge** väljer du **alternativet** Flexibel.
1. Ange antal **feldomäner.**
1. Slutför skapandet av din skalningsuppsättning. Se [Skapa en skalningsuppsättning i Azure Portal](quick-create-portal.md#create-virtual-machine-scale-set) mer information om hur du skapar en skalningsuppsättning.

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Orkestreringsläge i portalen när du skapar en skalningsuppsättning":::

Lägg sedan till en virtuell dator i skalningsuppsättningen i flexibelt orkestreringsläge.

1. I sökfältet söker du efter och väljer **Virtuella datorer.**
1. Välj **Lägg** till på **sidan Virtuella** datorer.
1. På fliken **Grundläggande inställningar** visar du **avsnittet Instansinformation.**
1. Lägg till den virtuella datorn i skalningsuppsättningen i flexibelt orkestreringsläge genom att välja skalningsuppsättningen i **tillgänglighetsalternativen**. Du kan lägga till den virtuella datorn i en skalningsuppsättning i samma region, zon och resursgrupp.
1. Slutför skapandet av den virtuella datorn. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Lägga till en virtuell dator i skalningsuppsättningen för flexibelt orkestreringsläge":::


### <a name="azure-cli-20"></a>Azure CLI 2.0
Skapa en flexibel VM-skalningsuppsättning med Azure CLI. I följande exempel visas skapandet av en flexibel skalningsuppsättning där antalet feldomäner är inställt på 3, en virtuell dator skapas och sedan läggs till i den flexibla skalningsuppsättningen. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Skapa en flexibel VM-skalningsuppsättning med Terraform. Den här processen **kräver Terraform Azurerm-providern v2.15.0** eller senare. Observera följande parametrar:
- När ingen zon har `platform_fault_domain_count` angetts kan vara 1, 2 eller 3 beroende på region.
- När en zon anges kan `the fault domain count` vara 1.
- `single_placement_group` Parametern måste vara `false` för flexibla VM-skalningsuppsättningar.
- Om du utför en regional distribution behöver du inte ange `zones` .

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>REST-API

1. Skapa en tom skalningsuppsättning. Följande parametrar krävs:
    - API-version 2019-12-01 (eller senare) 
    - En enskild placeringsgrupp måste vara `false` när du skapar en flexibel skalningsuppsättning

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. Lägg till virtuella datorer i skalningsuppsättningen.
    1. Tilldela egenskapen `virtualMachineScaleSet` till skalningsuppsättningen som du skapade tidigare. Du måste ange egenskapen när `virtualMachineScaleSet` den virtuella datorn skapas. 
    1. Du kan använda **funktionen copy()** Azure Resource Manager mall för att skapa flera virtuella datorer på samma gång. Se [Resurs iteration](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) i Azure Resource Manager mallar. 

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

Ett [fullständigt exempel finns](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) i Azure-snabbstarten.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Hur stor skala stöder flexibel orkestrering?**

Du kan lägga till upp till 1 000 virtuella datorer till en skalningsuppsättning i flexibelt orkestreringsläge.

**Hur är tillgänglighet med flexibel orkestrering jämfört med tillgänglighetsuppsättningar eller enhetlig orkestrering?**

| Tillgänglighetsattribut  | Flexibel orkestrering  | Enhetlig orkestrering  | Tillgänglighetsuppsättningar  |
|-|-|-|-|
| Distribuera mellan tillgänglighetszoner  | Inga  | Ja  | Inga  |
| Garantier för tillgänglighet för feldomäner inom en region  | Ja, upp till 1 000 instanser kan spridas över upp till tre feldomäner i regionen. Maximalt antal feldomäner varierar beroende på region  | Ja, upp till 100 instanser  | Ja, upp till 200 instanser  |
| Placeringsgrupper  | Flexibelt läge använder alltid flera placeringsgrupper (singlePlacementGroup = false)  | Du kan välja en enskild placeringsgrupp eller flera placeringsgrupper | Ej tillämpligt  |
| Uppdateringsdomäner  | Ingen, underhåll eller värduppdateringar görs feldomän av feldomän  | Upp till 5 uppdateringsdomäner  | Upp till 20 uppdateringsdomäner  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Felsöka skalningsuppsättningar med flexibel orkestrering
Hitta rätt lösning för ditt felsökningsscenario. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Orsak:** Prenumerationen är inte registrerad för den offentliga förhandsversionen av flexibelt orkestreringsläge. 

**Lösning:** Följ anvisningarna ovan för att registrera dig för den allmänt använda förhandsversionen av flexibelt orkestreringsläge. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Orsak:** Parametern `platformFaultDomainCount` är ogiltig för den valda regionen eller zonen. 

**Lösning:** Du måste välja ett giltigt `platformFaultDomainCount` värde. För zonindeala distributioner är `platformFaultDomainCount` det högsta värdet 1. För regionala distributioner där ingen zon har angetts varierar `platformFaultDomainCount` maxvärdet beroende på region. Se [Hantera tillgängligheten för virtuella datorer för skript för att](../virtual-machines/availability.md) fastställa det maximala antalet feldomäner per region. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Orsak:** Försöker ta bort en skalningsuppsättning i flexibelt orkestreringsläge som är associerat med en eller flera virtuella datorer. 

**Lösning:** Ta bort alla virtuella datorer som är associerade med skalningsuppsättningen i flexibelt orkestreringsläge. Sedan kan du ta bort skalningsuppsättningen.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Orsak:** Prenumerationen är registrerad för förhandsversionen av flexibelt orkestreringsläge. parametern `singlePlacementGroup` är dock inställd på *True*. 

**Lösning:** måste `singlePlacementGroup` vara inställt på *False*. 


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Lär dig hur du distribuerar ditt program på en skalningsuppsättning.](virtual-machine-scale-sets-deploy-app.md)
