---
title: Ändra en skalnings uppsättning för virtuella Azure-datorer
description: 'Lär dig hur du ändrar och uppdaterar en skalnings uppsättning för en virtuell Azure-dator med REST-API: erna, Azure PowerShell och Azure CLI'
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/10/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: bd16f0ef330d1d4a33dd796af0ec3e94dda5acfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684601"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

Under hela livs cykeln för dina program kan du behöva ändra eller uppdatera skalnings uppsättningen för den virtuella datorn. Dessa uppdateringar kan omfatta hur du uppdaterar konfigurationen av skalnings uppsättningen eller ändrar program konfigurationen. I den här artikeln beskrivs hur du ändrar en befintlig skalnings uppsättning med REST-API: er, Azure PowerShell eller Azure CLI.

## <a name="fundamental-concepts"></a>Grundläggande koncept

### <a name="the-scale-set-model"></a>Skalnings uppsättnings modellen
En skalnings uppsättning har en "skalnings uppsättnings modell" som fångar det *önskade* läget för skalnings uppsättningen som helhet. Om du vill fråga modellen efter en skalnings uppsättning kan du använda 

- REST API med [Compute/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get) enligt följande:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI med [AZ VMSS show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Du kan också använda [Resources.Azure.com](https://resources.azure.com) eller språkspecifika Azure- [SDK](https://azure.microsoft.com/downloads/): er.

Den exakta presentationen av utdata beror på vilka alternativ du anger för kommandot. I följande exempel visas komprimerade exempel utdata från Azure CLI:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Dessa egenskaper gäller för skalnings uppsättningen som helhet.


### <a name="the-scale-set-instance-view"></a>Vyn skalnings uppsättnings instans
En skalnings uppsättning har också en "skalnings uppsättnings instans vy" som samlar in aktuellt *körnings* tillstånd för skalnings uppsättningen som helhet. Om du vill fråga instans visningen för en skalnings uppsättning kan du använda:

- REST API med [Compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) på följande sätt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI med [AZ VMSS get-instance-View](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Du kan också använda [Resources.Azure.com](https://resources.azure.com) eller språkspecifika Azure- [SDK](https://azure.microsoft.com/downloads/) : er

Den exakta presentationen av utdata beror på vilka alternativ du anger för kommandot. I följande exempel visas komprimerade exempel utdata från Azure CLI:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Dessa egenskaper ger en översikt över det aktuella körnings tillståndet för de virtuella datorerna i skalnings uppsättningen, till exempel status för tillägg som tillämpas på skalnings uppsättningen.


### <a name="the-scale-set-vm-model-view"></a>Vyn skalnings uppsättning för virtuell dator modell
På liknande sätt som en skalnings uppsättning har en modell-vy, har varje VM-instans i skalnings uppsättningen sin egen modell vy. Om du vill fråga vyn modell för en viss VM-instans i en skalnings uppsättning kan du använda:

- REST API med [Compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) enligt följande:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI med [AZ VMSS show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda [Resources.Azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/): er.

Den exakta presentationen av utdata beror på vilka alternativ du anger för kommandot. I följande exempel visas komprimerade exempel utdata från Azure CLI:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Dessa egenskaper beskriver konfigurationen av en virtuell dator instans i en skalnings uppsättning, inte konfigurationen av skalnings uppsättningen som helhet. Till exempel har skalnings uppsättnings modellen `overprovision` som en egenskap, medan modellen för en VM-instans i en skalnings uppsättning inte gör det. Den här skillnaden beror på att överetablering är en egenskap för skalnings uppsättningen som helhet, inte enskilda VM-instanser i skalnings uppsättningen (mer information om överetablering finns i [design överväganden för skalnings uppsättningar](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Vyn skalnings uppsättning för virtuell dator instans
På samma sätt som för en skalnings uppsättning har en instans visning, har varje VM-instans i skalnings uppsättningen en egen instans visning. Om du vill fråga instans visningen för en viss VM-instans i en skalnings uppsättning kan du använda:

- REST API med [Compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) på följande sätt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI med [AZ VMSS get-instance-View](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda [Resources.Azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) : er

Den exakta presentationen av utdata beror på vilka alternativ du anger för kommandot. I följande exempel visas komprimerade exempel utdata från Azure CLI:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Dessa egenskaper beskriver det aktuella körnings läget för en virtuell dator instans i en skalnings uppsättning, vilket inkluderar alla tillägg som tillämpas på skalnings uppsättningen.


## <a name="how-to-update-global-scale-set-properties"></a>Så här uppdaterar du egenskaper för global skalnings uppsättning
Om du vill uppdatera en global skalnings uppsättnings egenskap måste du uppdatera egenskapen i skalnings uppsättnings modellen. Du kan göra den här uppdateringen via:

- REST API med [Compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) på följande sätt:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Du kan distribuera en Resource Manager-mall med egenskaperna från REST API för att uppdatera globala skalnings uppsättnings egenskaper.

- Azure PowerShell med [Update-AzVmss](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI med [AZ VMSS-uppdatering](/cli/azure/vmss):
    - Ändra en egenskap:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Så här lägger du till ett objekt i en List egenskap i en skalnings uppsättning: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Ta bort ett objekt från en List egenskap i en skalnings uppsättning: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Om du tidigare har distribuerat skalnings uppsättningen med `az vmss create` kommandot kan du köra `az vmss create` kommandot igen för att uppdatera skalnings uppsättningen. Se till att alla egenskaper i `az vmss create` kommandot är desamma som förut, förutom de egenskaper som du vill ändra.

- Du kan också använda [Resources.Azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/): er.

När skalnings uppsättnings modellen har uppdaterats, gäller den nya konfigurationen för alla nya virtuella datorer som skapats i skalnings uppsättningen. Modellerna för befintliga virtuella datorer i skalnings uppsättningen måste dock fortfarande vara uppdaterade med den senaste modellen för skalnings uppsättningar. I modellen för varje virtuell dator är en boolesk egenskap `latestModelApplied` som anger om den virtuella datorn är uppdaterad med den senaste modellen för skalnings uppsättningar ( `true` innebär att den virtuella datorn är uppdaterad med den senaste modellen).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Så här tar du med virtuella datorer uppdaterade med den senaste skalnings uppsättnings modellen
Skalnings uppsättningar har en "uppgraderings princip" som avgör hur virtuella datorer hämtas med den senaste skalnings uppsättnings modellen. De tre lägena för uppgraderings principen är:

- **Automatiskt** i det här läget ger skalnings uppsättningen inga garantier om ordningen på de virtuella datorer som tas ur bruk. Skalnings uppsättningen kan ta bort alla virtuella datorer samtidigt. 
- I det här läget används skalnings uppsättningen för att **lyfta** fram uppdateringen i batchar med en valfri paus tid mellan batchar.
- **Manuellt** i det här läget, när du uppdaterar skalnings uppsättnings modellen, händer ingenting i befintliga virtuella datorer.
 
Om du vill uppdatera befintliga virtuella datorer måste du göra en "manuell uppgradering" av varje befintlig virtuell dator. Du kan göra den här manuella uppgraderingen med:

- REST API med [Compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) på följande sätt:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell med [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI med [AZ VMSS Update-instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Du kan också använda språkspecifika Azure- [SDK](https://azure.microsoft.com/downloads/): er.

>[!NOTE]
> Service Fabric kluster kan bara använda *automatiskt* läge, men uppdateringen hanteras på olika sätt. Mer information finns i [Service Fabric program uppgraderingar](../service-fabric/service-fabric-application-upgrade.md).

Det finns en typ av ändring i globala skalnings uppsättnings egenskaper som inte följer uppgraderings principen. Ändringar av profilen för skalnings uppsättningens operativ system och data diskar (till exempel Admin-användarnamn och-lösen ord) kan bara ändras i API version *2017-12-01* eller senare. Dessa ändringar gäller bara för virtuella datorer som skapats efter ändringen i skalnings uppsättnings modellen. Om du vill att befintliga virtuella datorer ska vara uppdaterade måste du göra en avbildning av varje befintlig virtuell dator. Du kan göra om avbildningen via:

- REST API med [Compute/virtualmachinescalesets/Reimage](/rest/api/compute/virtualmachinescalesets/reimage) enligt följande:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell med [set-AzVmssVm](/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI med [AZ VMSS-avbildning](/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda språkspecifika Azure- [SDK](https://azure.microsoft.com/downloads/): er.


## <a name="properties-with-restrictions-on-modification"></a>Egenskaper med begränsningar vid ändring

### <a name="create-time-properties"></a>Egenskaper för Create-Time
Vissa egenskaper kan bara anges när du skapar skalnings uppsättningen. Bland dessa egenskaper finns:

- Tillgänglighetszoner
- Bild referens utgivare
- Bild referens erbjudande
- Lagrings konto typ för hanterad OS-disk

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Egenskaper som bara kan ändras baserat på det aktuella värdet
Vissa egenskaper kan ändras, med undantag beroende på det aktuella värdet. Bland dessa egenskaper finns:

- **singlePlacementGroup** – om singlePlacementGroup är sant kan den ändras till false. Om singlePlacementGroup är false kan det dock **inte** ändras till sant.
- **undernät** – under nätet för en skalnings uppsättning kan ändras så länge det ursprungliga under nätet och det nya under nätet finns i samma virtuella nätverk.
- **imageReferenceSku** -image Reference SKU kan uppdateras för de signerade [Linux-distributioner](../virtual-machines/linux/endorsed-distros.md), Windows Server/Client-avbildningar och avbildningar utan [information om planer](../virtual-machines/linux/cli-ps-findimage.md#view-plan-properties). 

### <a name="properties-that-require-deallocation-to-change"></a>Egenskaper som kräver att tilldelningen ändras
Vissa egenskaper kan bara ändras till vissa värden om de virtuella datorerna i skalnings uppsättningen är friallokerade. Bland dessa egenskaper finns:

- **SKU-namn**– om den nya VM-SKU: n inte stöds på den maskin vara som skalnings uppsättningen är på, måste du frigöra de virtuella datorerna i skalnings uppsättningen innan du ändrar SKU-namnet. Mer information finns i [så här ändrar du storlek på en virtuell Azure-dator](../virtual-machines/windows/resize-vm.md). 

## <a name="vm-specific-updates"></a>VM-/regionsspecifika uppdateringar
Vissa ändringar kan tillämpas på specifika virtuella datorer i stället för globala skalnings uppsättnings egenskaper. För närvarande är den enda VM-anpassade uppdateringen som stöds att ansluta/koppla från data diskar till/från virtuella datorer i skalnings uppsättningen. Den här funktionen är en förhandsversion. Mer information finns i [förhands gransknings dokumentationen](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scenarier

### <a name="application-updates"></a>Program uppdateringar
Om ett program distribueras till en skalnings uppsättning via tillägg, gör en uppdatering av tilläggs konfigurationen att programmet uppdateras i enlighet med uppgraderings principen. Om du till exempel har en ny version av ett skript som ska köras i ett anpassat skript tillägg kan du uppdatera egenskapen *fileUris* så att den pekar på det nya skriptet. I vissa fall kanske du vill framtvinga en uppdatering även om tilläggs konfigurationen är oförändrad (till exempel om du uppdaterade skriptet utan att ändra URI för skriptet). I dessa fall kan du ändra *forceUpdateTag* för att framtvinga en uppdatering. Azure-plattformen tolkar inte den här egenskapen. Om du ändrar värdet finns det ingen påverkan på hur tillägget körs. En ändring tvingar bara tillägget att köras igen. Mer information om *forceUpdateTag* finns i [REST API dokumentationen för tillägg](/rest/api/compute/virtualmachineextensions/createorupdate). Observera att *forceUpdateTag* kan användas med alla tillägg, inte bara det anpassade skript tillägget.

Det är också vanligt att program distribueras via en anpassad avbildning. Det här scenariot beskrivs i följande avsnitt.

### <a name="os-updates"></a>OS-uppdateringar
Om du använder Azure Platform-avbildningar kan du uppdatera avbildningen genom att ändra *imageReference* (mer information finns i [dokumentationen för REST API](/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Med plattforms avbildningar är det vanligt att ange "senaste" för avbildnings referens versionen. När du skapar, skalar ut och återskapar en avbildning skapas virtuella datorer med den senaste tillgängliga versionen. Det innebär dock **inte** att operativ system avbildningen uppdateras automatiskt med tiden när nya avbildnings versioner släpps. En separat funktion är för närvarande en för hands version som ger automatiska OS-uppgraderingar. Mer information finns i dokumentationen om [automatiska operativ system uppgraderingar](virtual-machine-scale-sets-automatic-upgrade.md).

Om du använder anpassade avbildningar kan du uppdatera avbildningen genom att uppdatera *imageReference* -ID: t (mer information finns i [dokumentationen för REST API](/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Exempel

### <a name="update-the-os-image-for-your-scale-set"></a>Uppdatera operativ system avbildningen för din skalnings uppsättning
Du kan ha en skalnings uppsättning som kör en gammal version av Ubuntu LTS 16,04. Du vill uppdatera till en nyare version av Ubuntu LTS 16,04, till exempel version *16.04.201801090*. Egenskapen bild referens version är inte en del av en lista, så du kan ändra dessa egenskaper direkt med något av följande kommandon:

- Azure PowerShell med [Update-AzVmss](/powershell/module/az.compute/update-azvmss) på följande sätt:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI med [AZ VMSS-uppdatering](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Du kan också vilja ändra avbildningen som används i skalnings uppsättningen. Du kanske till exempel vill uppdatera eller ändra en anpassad avbildning som används av din skalnings uppsättning. Du kan ändra avbildningen som skalnings uppsättningen använder genom att uppdatera egenskapen bild referens-ID. Egenskapen bild referens-ID är inte en del av en lista, så du kan ändra den här egenskapen direkt med något av följande kommandon:

- Azure PowerShell med [Update-AzVmss](/powershell/module/az.compute/update-azvmss) på följande sätt:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI med [AZ VMSS-uppdatering](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Uppdatera belastningsutjämnaren för din skalnings uppsättning
Anta att du har en skalnings uppsättning med en Azure Load Balancer och att du vill ersätta Azure Load Balancer med en Azure Application Gateway. Belastnings utjämnings-och Application Gateway egenskaperna för en skalnings uppsättning är en del av en lista, så du kan använda kommandona för att ta bort eller lägga till List element i stället för att ändra egenskaperna direkt:

- Azure PowerShell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Dessa kommandon förutsätter att det bara finns en IP-konfiguration och belastningsutjämnare på skalnings uppsättningen. Om det finns flera kan du behöva använda ett List index som inte är *0*.


## <a name="next-steps"></a>Nästa steg
Du kan också utföra vanliga hanterings uppgifter i skalnings uppsättningar med [Azure CLI](virtual-machine-scale-sets-manage-cli.md) eller [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).