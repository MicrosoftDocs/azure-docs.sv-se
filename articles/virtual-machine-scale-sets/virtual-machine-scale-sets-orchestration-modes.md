---
title: Orchestration-lägen för skalnings uppsättningar för virtuella datorer i Azure
description: Lär dig hur du använder flexibla och enhetliga Orchestration-lägen för skalnings uppsättningar för virtuella datorer i Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 316f97480dfe7ee6481ef88faf265051a92024da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937268"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>För hands version: Orchestration-lägen för skalnings uppsättningar för virtuella datorer i Azure 

Virtual Machines skalnings uppsättningar ger en logisk gruppering av plattforms hanterade virtuella datorer. Med skalnings uppsättningar skapar du en konfigurations modell för virtuella datorer, lägger automatiskt till eller tar bort ytterligare instanser baserat på CPU eller minnes belastning och uppgraderar automatiskt till den senaste versionen av operativ systemet. Som traditionellt kan du använda skalnings uppsättningar för att skapa virtuella datorer med en konfigurations modell för virtuella datorer som tillhandahålls när skalnings uppsättningen skapas, och skalnings uppsättningen kan bara hantera virtuella datorer som är implicit skapade baserat på konfigurations modellen. 

I Orchestration-lägena för skalnings uppsättning kan du få bättre kontroll över hur virtuella dator instanser hanteras av skalnings uppsättningen. 

> [!IMPORTANT]
> Orchestration-läget definieras när du skapar skalnings uppsättningen och kan inte ändras eller uppdateras senare.


## <a name="scale-sets-with-uniform-orchestration"></a>Skalnings uppsättningar med enhetligt dirigering
Optimerad för storskaliga tillstånds lösa arbets belastningar med identiska instanser.

Skalnings uppsättningar för virtuella datorer med enhetlig dirigering använder en profil för virtuell dator eller mall för att skala upp till önskad kapacitet. Även om det finns någon möjlighet att hantera eller anpassa enskilda instanser av virtuella datorer, använder enhetligt identiska VM-instanser. Enskilda enhetliga VM-instanser exponeras via de virtuella datorernas API-kommandon för skalnings uppsättning. Enskilda instanser är inte kompatibla med de vanliga Azure IaaS-API-kommandona för virtuella datorer, hanterings funktioner för Azure, till exempel Azure Resource Manager resurs tagga RBAC-behörighet, Azure Backup eller Azure Site Recovery. Enhetlig samordning ger problem med hög tillgänglighet för fel domäner när de konfigureras med färre än 100 instanser. Enhetligt dirigering är allmänt tillgängligt och har stöd för en fullständig uppsättning hantering och dirigering av skalnings uppsättningar, inklusive mått baserat på automatisk skalning, instans skydd och automatiska operativ system uppgraderingar. 


## <a name="scale-sets-with-flexible-orchestration"></a>Skalnings uppsättningar med flexibelt dirigering 
Uppnå hög tillgänglighet i skala med identiska eller flera typer av virtuella datorer.

Med flexibel dirigering ger Azure en enhetlig upplevelse i Azures eko system för virtuella datorer. Flexibel dirigering ger garantier för hög tillgänglighet (upp till 1000 virtuella datorer) genom att sprida virtuella datorer mellan fel domäner i en region eller i en tillgänglighets zon. På så sätt kan du skala ut programmet samtidigt som du underhåller fel domän isolering som är nödvändig för att köra kvorum-eller tillstånds känsliga arbets belastningar, inklusive:
- Kvorum-baserade arbets belastningar
- Open-Source databaser
- Tillstånds känsliga program
- Tjänster som kräver hög tillgänglighet och stor skala
- Tjänster som vill mixa typer av virtuella datorer eller använda virtuella datorer på plats och på begäran tillsammans
- Befintliga program för tillgänglighets uppsättningar  

> [!IMPORTANT]
> Skalnings uppsättningar för virtuella datorer i flexibelt Orchestration-läge finns för närvarande i offentlig för hands version. En opt-in-procedur krävs för att använda den offentliga för hands versions funktionen som beskrivs nedan.
> Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Vad har ändrats i flexibelt Orchestration-läge?
En av de största fördelarna med flexibel dirigering är att den tillhandahåller Orchestration-funktioner för virtuella Azure IaaS-datorer, i stället för skalnings uppsättningar för underordnade virtuella datorer. Det innebär att du kan använda alla standard-VM-API: er när du hanterar flexibla Orchestration-instanser, i stället för den virtuella datorns skalnings uppsättnings-API: er som du använder med enhetlig dirigering. Under för hands versions perioden finns det flera skillnader mellan att hantera instanser i flexibel dirigering jämfört med enhetligt dirigering. I allmänhet rekommenderar vi att du använder standard Azure IaaS-API: er för virtuella datorer när det är möjligt. I det här avsnittet ska vi Markera exempel på metod tips för att hantera VM-instanser med flexibel dirigering.  

### <a name="assign-fault-domain-during-vm-creation"></a>Tilldela fel domän vid skapande av virtuell dator
Du kan välja antalet fel domäner för den flexibla Dirigerings skalnings uppsättningen. Som standard när du lägger till en virtuell dator i en flexibel skalnings uppsättning, sprider sig Azure jämnt spridnings instanser mellan fel domäner. Vi rekommenderar att du låter Azure tilldela fel domänen, för avancerade eller fel söknings scenarier som du kan åsidosätta det här standard beteendet och ange den fel domän där instansen kommer land.

```azurecli-interactive 
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Namn på instans 
När du skapar en virtuell dator och lägger till den i en flexibel skalnings uppsättning har du fullständig kontroll över instans namn inom reglerna för namngivnings konvention i Azure. När virtuella datorer läggs till automatiskt i skalnings uppsättningen via automatisk skalning, anger du ett prefix och Azure lägger till ett unikt nummer i slutet av namnet.

### <a name="query-instances-for-power-state"></a>Fråga efter energi status i instanser
Den bästa metoden är att använda Azure Resource Graph för att fråga efter alla virtuella datorer i en skalnings uppsättning för virtuella datorer. Azure Resource Graph ger effektiva fråge funktioner för Azure-resurser i stor skala över prenumerationer. 

``` 
| where type =~ 'Microsoft.Compute/virtualMachines' 
| where properties.virtualMachineScaleSet contains "demo" 
| extend powerState = properties.extended.instanceView.powerState.code 
| project name, resourceGroup, location, powerState 
| order by resourceGroup desc, name desc 
```

Att fråga resurser med [Azure Resource Graph](../governance/resource-graph/overview.md) är ett bekvämt och effektivt sätt att fråga Azure-resurser och minimera API-anrop till resurs leverantören. Azure Resource Graph är ett konsekvent cacheminne där nya eller uppdaterade resurser kanske inte återspeglas i upp till 60 sekunder. Du kan:
- Lista virtuella datorer i en resurs grupp eller prenumeration.
- Använd alternativet Expand för att hämta instans visningen (fel domän tilldelning, energi-och etablerings tillstånd) för alla virtuella datorer i din prenumeration.
- Använd Get VM API och kommandon för att hämta modell-och instans läge för en enda instans.

### <a name="scale-sets-vm-batch-operations"></a>Skalnings uppsättningar VM batch-åtgärder
Använd standard kommandona för virtuella datorer för att starta, stoppa, starta om, ta bort instanser, i stället för virtuella datorers skalnings uppsättningar för virtuella datorer. Den virtuella datorns skalnings uppsättning för virtuella datorer (starta alla, stoppa alla, Återställ alla osv.) används inte med läget för flexibel dirigering. 

### <a name="monitor-application-health"></a>Övervaka programhälsa 
Med övervakning av program hälsa kan ditt program tillhandahålla Azure med ett pulsslag för att avgöra om ditt program är felfritt eller dåligt. Azure kan automatiskt ersätta VM-instanser som inte är felfria. För flexibla skalnings uppsättnings instanser måste du installera och konfigurera program hälso tillägget på den virtuella datorn. För enhetlig skalnings uppsättnings instanser kan du antingen använda program hälso tillägget eller mäta hälsan med en Azure Load Balancer anpassad hälso avsökning. 

### <a name="list-scale-sets-vm-api-changes"></a>Visa skalnings uppsättningar för VM-API-ändringar 
Med Virtual Machine Scale Sets kan du Visa en lista över de instanser som tillhör skalnings uppsättningen. Med flexibel dirigering innehåller List Virtual Machine Scale Sets VM-kommandot en lista över skalnings uppsättningar VM-ID: n. Du kan sedan anropa kommandot GET Virtual Machine Scale Sets VM-kommandon för att få mer information om hur skalnings uppsättningen fungerar med den virtuella dator instansen. Om du vill få fullständig information om den virtuella datorn använder du standard kommandona GET VM eller [Azure Resource Graph](../governance/resource-graph/overview.md). 

### <a name="retrieve-boot-diagnostics-data"></a>Hämta Boot Diagnostics-data 
Använd standard-API: erna och kommandona för virtuella datorer för att hämta instanser av instansen av startdiagnostik. API: er och kommandon i Virtual Machine Scale Sets VM-startdiagnostik används inte med flexibla Orchestration mode-instanser.

### <a name="vm-extensions"></a>VM-tillägg 
Använd tillägg som är riktade till standard-virtuella datorer, i stället för tillägg som riktas mot enhetligt Dirigerings läges instanser.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>En jämförelse mellan flexibla, enhetliga och tillgänglighets uppsättningar 
I följande tabell jämförs det flexibla Orchestration-läget, ett enhetligt Orchestration-läge och tillgänglighets uppsättningar för deras funktioner.

| Funktion | Stöds av flexibelt Orchestration (förhands granskning) | Stöds av enhetligt dirigering (allmän tillgänglighet) | Stöds av AvSets (allmän tillgänglighet) |
|-|-|-|-|
|         Typ av virtuell dator  | Standard Azure IaaS VM (Microsoft. Compute/virtualmachines)  | Skalnings uppsättning för vissa virtuella datorer (Microsoft. Compute/virtualmachinescalesets/virtualmachines)  | Standard Azure IaaS VM (Microsoft. Compute/virtualmachines)  |
|         SKU: er som stöds  |            D-serien, E-serien, F-serien, A-serien, B-serien, Intel, AMD  |            Alla SKU: er  |            Alla SKU: er  |
|         Tillgänglighetszoner  |            Du kan också ange alla instanser i en enda tillgänglighets zon |            Ange instanser i 1, 2 eller 3 tillgänglighets zoner  |            Stöds inte  |
|         Fullständig kontroll över virtuell dator, nätverkskort, diskar  |            Ja  |            Begränsad kontroll med Virtual Machine Scale Sets VM-API  |            Ja  |
|         Automatisk skalning  |            Inga  |            Ja  |            Inga  |
|         Tilldela en virtuell dator till en angiven fel domän  |            Ja  |             Inga   |            Inga  |
|         Ta bort nätverkskort och diskar när du tar bort VM-instanser  |            Inga  |            Ja  |            Inga  |
|         Uppgraderings princip (VM Scale set) |            Inga  |            Automatisk, rullande, manuell  |            Ej tillämpligt  |
|         Automatiska OS-uppdateringar (VM Scale set) |            Inga  |            Ja  |            Ej tillämpligt  |
|         I säkerhets korrigering av gäst  |            Ja  |            Inga  |            Ja  |
|         Avsluta meddelanden (VM Scale set) |            Inga  |            Ja  |            Ej tillämpligt  |
|         Instans reparation (VM Scale set) |            Inga  |            Ja   |            Ej tillämpligt  |
|         Snabbare nätverk  |            Ja  |            Ja  |            Ja  |
|         Punkt instanser och priser   |            Ja, du kan ha både plats-och prioritets instanser  |            Ja, instanserna måste antingen vara hela eller vanliga  |            Nej, endast antal instanser med normal prioritet  |
|         Blanda operativ system  |            Ja, Linux och Windows kan finnas i samma flexibla skalnings uppsättning |            Nej, instanserna är samma operativ system  |               Ja, Linux och Windows kan finnas i samma flexibla skalnings uppsättning |
|         Övervaka program hälsa  |            Program hälso tillägg  |            Avsökning av program hälso tillägg eller Azure Load Balancer  |            Program hälso tillägg  |
|         UltraSSD diskar   |            Ja  |            Ja, endast för zonindelade-distributioner  |            Inga  |
|         InfiniBand   |            Inga  |            Ja, endast en placerings grupp  |            Ja  |
|         Skrivningsaccelerator   |            Inga  |            Ja  |            Ja  |
|         Placerings grupper för närhet   |            Ja  |            Ja  |            Ja  |
|         Dedikerade Azure-värdar   |            Inga  |            Ja  |            Ja  |
|         Basic SLB   |            Inga  |            Ja  |            Ja  |
|         Azure Load Balancer standard-SKU |            Ja  |            Ja  |            Ja  |
|         Application Gateway  |            Inga  |            Ja  |            Ja  |
|         Underhålls kontroll   |            Inga  |            Ja  |            Ja  |
|         Lista virtuella datorer i uppsättningen  |            Ja  |            Ja  |            Ja, lista virtuella datorer i AvSet  |
|         Azure-aviseringar  |            Inga  |            Ja  |            Ja  |
|         VM-insikter  |            Inga  |            Ja  |            Ja  |
|         Azure Backup  |            Ja  |            Ja  |            Ja  |
|         Azure Site Recovery  |     Inga  |            Inga  |            Ja  |
|         Lägg till/ta bort befintlig virtuell dator i gruppen  |            Inga  |            Inga  |            Inga  | 


## <a name="register-for-flexible-orchestration-mode"></a>Registrera dig för flexibelt Orchestration-läge
Innan du kan distribuera skalnings uppsättningar för virtuella datorer i flexibelt Orchestration-läge måste du först registrera din prenumeration för för hands versions funktionen. Registreringen kan ta flera minuter att slutföra. Du kan använda följande Azure PowerShell-eller Azure CLI-kommandon för att registrera dig.

### <a name="azure-portal"></a>Azure-portalen
Gå till informations sidan för den prenumeration som du vill skapa en skalnings uppsättning i flexibelt Orchestration-läge och välj för hands versions funktioner på menyn. Välj de två Orchestrator-funktioner som ska aktive ras: _VMOrchestratorSingleFD_ och _VMOrchestratorMultiFD_ och tryck på knappen Registrera. Funktions registreringen kan ta upp till 15 minuter.

![Funktions registrering.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

När funktionerna har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräknings resurs leverantören. Gå till fliken Resource providers för din prenumeration, Välj Microsoft. Compute och klicka på registrera igen.

![Registrera igen](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


### <a name="azure-powershell"></a>Azure PowerShell 
Använd cmdleten [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) för att aktivera för hands versionen av din prenumeration. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräknings resurs leverantören. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Azure CLI 2.0 
Aktivera förhands granskningen för prenumerationen med hjälp av [AZ-funktionen registrera](/cli/azure/feature#az-feature-register) . 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräknings resurs leverantören. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Kom igång med flexibelt Orchestration-läge

Kom igång med flexibelt Orchestration-läge för dina skalnings uppsättningar via Azure Portal, Azure CLI, terraform eller REST API.

### <a name="azure-portal"></a>Azure Portal

Skapa en skalnings uppsättning för virtuella datorer i flexibelt Orchestration-läge via Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. I Sök fältet söker du efter och väljer **skalnings uppsättningar för virtuella datorer**. 
1. Välj **skapa** på sidan **skalnings uppsättningar för virtuell dator** .
1. På sidan **skapa en skalnings uppsättning för virtuell dator** visar du avsnittet **Orchestration** .
1. För **Orchestration-läget** väljer du alternativet **flexibel** .
1. Ange **antalet fel domäner**.
1. Slutför skapandet av din skalnings uppsättning. Mer information om hur du skapar en skalnings uppsättning finns [i skapa en skalnings uppsättning i Azure Portal](quick-create-portal.md#create-virtual-machine-scale-set) .

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Orchestration-läge i portalen när du skapar en skalnings uppsättning":::

Lägg sedan till en virtuell dator i skalnings uppsättningen i flexibelt Orchestration-läge.

1. Sök efter och välj **virtuella datorer** i Sök fältet.
1. Välj **Lägg till** på sidan **virtuella datorer** .
1. På fliken **grundläggande** visar du avsnittet **instans information** .
1. Lägg till den virtuella datorn i skalnings uppsättningen i läget för flexibelt Orchestration genom att välja skalnings uppsättningen i **tillgänglighets alternativen**. Du kan lägga till den virtuella datorn i en skalnings uppsättning i samma region, zon och resurs grupp.
1. Slutför skapandet av den virtuella datorn. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Lägg till virtuell dator i den flexibla Orchestration-lägets skalnings uppsättning":::


### <a name="azure-cli-20"></a>Azure CLI 2.0
Skapa en flexibel skalnings uppsättning för virtuella datorer med Azure CLI. I följande exempel visas hur du skapar en flexibel skalnings uppsättning där antalet fel domäner är inställt på 3, en virtuell dator skapas och läggs till i den flexibla skalnings uppsättningen. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Skapa en flexibel skalnings uppsättning för virtuella datorer med terraform. Den här processen kräver **terraform Azurerm Provider v 2.15.0** eller senare. Observera följande parametrar:
- Om ingen zon anges, `platform_fault_domain_count` kan vara 1, 2 eller 3 beroende på region.
- När en zon anges, `the fault domain count` kan vara 1.
- `single_placement_group` parametern måste vara `false` för flexibla skalnings uppsättningar för virtuella datorer.
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

1. Skapa en tom skalnings uppsättning. Följande parametrar måste anges:
    - API-version 2019-12-01 (eller senare) 
    - En enda placerings grupp måste vara `false` när du skapar en flexibel skalnings uppsättning

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

2. Lägg till virtuella datorer i skalnings uppsättningen.
    1. Tilldela `virtualMachineScaleSet` egenskapen till den skalnings uppsättning som du skapade tidigare. Du måste ange `virtualMachineScaleSet` egenskapen vid tidpunkten för skapandet av den virtuella datorn. 
    1. Du kan använda funktionen **Kopiera ()** Azure Resource Manager-mall för att skapa flera virtuella datorer samtidigt. Se [resurs upprepning](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) i Azure Resource Manager mallar. 

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

Se [Azure snabb start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) för ett fullständigt exempel.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Hur mycket skala stöder flexibla Orchestration-stöd?**

Du kan lägga till upp till 1000 virtuella datorer i en skalnings uppsättning i ett flexibelt Orchestration-läge.

**Hur jämförs tillgänglighet med flexibelt Orchestration med tillgänglighets uppsättningar eller enhetligt dirigering?**

|   | Flexibel dirigering  | Enhetlig dirigering  | Tillgänglighetsuppsättningar  |
|-|-|-|-|
| Distribuera över tillgänglighets zoner  | Inga  | Ja  | Inga  |
| Tillgänglighets garantier för fel domän i en region  | Ja, upp till 1000 instanser kan spridas över upp till 3 fel domäner i regionen. Det maximala antalet fel domäner varierar beroende på region  | Ja, upp till 100 instanser  | Ja, upp till 200 instanser  |
| Placeringsgrupper  | I flexibelt läge används alltid flera placerings grupper (singlePlacementGroup = false)  | Du kan välja en enskild placerings grupp eller flera placerings grupper | Ej tillämpligt  |
| Uppdateringsdomäner  | Ingen, underhåll eller värd uppdateringar är slutförda fel domän av fel domän  | Upp till 5 uppdaterings domäner  | Upp till 20 uppdaterings domäner  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Felsöka skalnings uppsättningar med flexibel dirigering
Hitta rätt lösning i fel söknings scenariot. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Orsak:** Prenumerationen är inte registrerad för den flexibla Orchestration-lägets offentliga för hands version. 

**Lösning:** Följ anvisningarna ovan för att registrera dig för den flexibla Dirigerings lägets offentliga för hands version. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Orsak:** `platformFaultDomainCount` Parametern är ogiltig för den region eller zon som valts. 

**Lösning:** Du måste välja ett giltigt `platformFaultDomainCount` värde. För zonindelade-distributioner är det maximala `platformFaultDomainCount` värdet 1. För regionala distributioner där ingen zon har angetts beror det på att det maximala värdet `platformFaultDomainCount` varierar beroende på region. Se [Hantera tillgängligheten för virtuella datorer för skript](../virtual-machines/availability.md) för att fastställa det maximala antalet fel domäner per region. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Orsak:** Försöker ta bort en skalnings uppsättning i ett flexibelt Orchestration-läge som är associerat med en eller flera virtuella datorer. 

**Lösning:** Ta bort alla virtuella datorer som är kopplade till skalnings uppsättningen i flexibelt Orchestration-läge. sedan kan du ta bort skalnings uppsättningen.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Orsak:** Prenumerationen är registrerad för för hands versionen av den flexibla Orchestration-läget. `singlePlacementGroup` parametern har dock angetts till *True*. 

**Lösning:** `singlePlacementGroup` Måste vara inställt på *falskt*. 


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Lär dig hur du distribuerar ditt program på en skalnings uppsättning.](virtual-machine-scale-sets-deploy-app.md)