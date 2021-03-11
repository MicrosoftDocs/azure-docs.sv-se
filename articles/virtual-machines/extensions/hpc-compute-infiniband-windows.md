---
title: InfiniBand-drivrutins tillägg – virtuella Azure-datorer i Windows
description: Microsoft Azure tillägget för att installera InfiniBand-drivrutiner på virtuella datorer i H-och N-serien som kör Windows.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: amverma
ms.openlocfilehash: 81720c18ce858cd5667413a9d39afdadc95acb23
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559706"
---
# <a name="infiniband-driver-extension-for-windows"></a>InfiniBand-drivrutins tillägg för Windows

Med det här tillägget installeras InfiniBand ND-drivrutiner (för ej SR-IOV-aktiverade) och OFED-drivrutiner (för SR-IOV-aktiverade) ("r"-storlekar) [H-serien](../sizes-hpc.md) och [N-seriens](../sizes-gpu.md) virtuella datorer som kör Windows. Beroende på VM-serien installerar tillägget lämpliga driv rutiner för Connect-X-NÄTVERKSKORTet.

Det finns också ett tillägg för att installera InfiniBand-drivrutiner för [virtuella Linux-datorer](hpc-compute-infiniband-linux.md).

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Det här tillägget har stöd för följande OS-distributioner, beroende på driv rutins stöd för en speciell OS-version. Observera lämplig InfiniBand NIC för storleken på virtuella datorer i H-och N-serien.

| Distribution | InfiniBand NIC-drivrutiner |
|---|---|
| Windows 10 | CX5, CX6 |
| Windows Server 2019 | CX5, CX6 |
| Windows Server 2016 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 R2 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 | CX3-Pro, CX5, CX6 |

### <a name="internet-connectivity"></a>Internetanslutning

Microsoft Azure-tillägget för InfiniBand-drivrutiner kräver att den virtuella mål datorn är ansluten till och har åtkomst till Internet.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Egenskaper

| Name | Värde/exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| utgivare | Microsoft. HpcCompute | sträng |
| typ | InfiniBandDriverWindows | sträng |
| typeHandlerVersion | 1.2 | int |



## <a name="deployment"></a>Distribution


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall 

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. Mallarna är idealiska när du distribuerar en eller flera virtuella datorer som kräver konfiguration av efter distribution.

JSON-konfigurationen för ett tillägg för virtuell dator kan kapslas i den virtuella dator resursen eller placeras på rot-eller toppnivå i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resurs namn och typ. Mer information finns i [Ange namn och typ för underordnade resurser](../../azure-resource-manager/templates/child-resource-name-type.md). 

I följande exempel förutsätts att tillägget är kapslat i den virtuella dator resursen. Vid kapsling av tilläggs resursen placeras JSON i `"resources": []` objektet på den virtuella datorn.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.2 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Lägg till tillägg i en skalnings uppsättning för virtuell dator

I följande exempel installeras det senaste version 1,2 InfiniBandDriverWindows-tillägget på alla RDMA-kompatibla virtuella datorer i en befintlig skalnings uppsättning för virtuella datorer med namnet *myVMSS* som distribuerats i resurs gruppen med namnet *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.2"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Information om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och genom att använda Azure PowerShell och Azure CLI. Kör följande kommando för att se distributions status för tillägg för en specifik virtuell dator.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Utökning av utdata loggas i följande fil. Läs den här filen för att spåra status för installationen samt för att felsöka eventuella fel.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>Avslutnings koder

I följande tabell beskrivs innebörden och den rekommenderade åtgärden baserat på slut koderna för installations processen för tillägg.

| Felkod | Innebörd | Möjlig åtgärd |
| :---: | --- | --- |
| 0 | Åtgärden lyckades |
| 3010 | Åtgärden lyckades. Omstart krävs. |
| 100 | Åtgärden stöds inte eller kunde inte slutföras. | Möjliga orsaker: PowerShell-versionen stöds inte, storleken på den virtuella datorn är inte en InfiniBand-aktiverad virtuell dator, fel vid hämtning av data. Kontrol lera loggfilerna för att ta reda på orsaken till felet. |
| 240, 840 | Åtgärds-timeout. | Försök igen. |
| -1 | Ett undantag inträffade. | Kontrol lera loggfilerna för att ta reda på orsaken till undantaget. |

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/community/). Du kan också välja en support incident via [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/). Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om InfiniBand-aktiverade (r-storlekar) finns i [d-](../sizes-hpc.md) och [N-seriens](../sizes-gpu.md) virtuella datorer.

> [!div class="nextstepaction"]
> [Läs mer om tillägg och funktioner för virtuella Linux-datorer](features-linux.md)
