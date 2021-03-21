---
title: InfiniBand-drivrutins tillägg – virtuella Azure Linux-datorer
description: Microsoft Azure tillägget för att installera InfiniBand-drivrutiner på virtuella datorer i H-och N-serien som kör Linux.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: 1219f5befb4de81850681a53c10f03dc81249d53
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559740"
---
# <a name="infiniband-driver-extension-for-linux"></a>InfiniBand-drivrutins tillägg för Linux

Det här tillägget installerar InfiniBand OFED-drivrutiner på InfiniBand-och SR-IOV-aktiverade (r-storlekar) [H-seriens](../sizes-hpc.md) och [N-seriens](../sizes-gpu.md) virtuella datorer som kör Linux. Beroende på VM-serien installerar tillägget lämpliga driv rutiner för Connect-X-NÄTVERKSKORTet.

Anvisningar om manuell installation av OFED-drivrutinerna finns [här](../workloads/hpc/enable-infiniband.md#manual-installation).

Det finns också ett tillägg för att installera InfiniBand-drivrutiner för [virtuella Windows-datorer](hpc-compute-infiniband-windows.md).

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Det här tillägget har stöd för följande OS-distributioner, beroende på driv rutins stöd för en speciell OS-version.

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS, 20,04 LTS |
| Linux: CentOS | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |

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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
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
| typ | InfiniBandDriverLinux | sträng |
| typeHandlerVersion | 1.1 | int |



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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
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
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Lägg till tillägg i en skalnings uppsättning för virtuell dator

I följande exempel installeras det senaste version 1,1 InfiniBandDriverLinux-tillägget på alla RDMA-kompatibla virtuella datorer i en befintlig skalnings uppsättning för virtuella datorer med namnet *myVMSS* som distribuerats i resurs gruppen med namnet *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
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

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>Avslutnings koder

I följande tabell beskrivs innebörden och den rekommenderade åtgärden baserat på slut koderna för installations processen för tillägg.

| Slutkod | Innebörd | Möjlig åtgärd |
| :---: | --- | --- |
| 0 | Åtgärden lyckades |
| 1 | Felaktig användning av tillägg | Kontrol lera loggen för körnings utdata |
| 10 | Linux-integrerings tjänster för Hyper-V och Azure är inte tillgängliga eller installerade | Kontrol lera utdata från lspci |
| 11 | Mellanox InfiniBand hittades inte på den här virtuella dator storleken | Använd en [VM-storlek och ett operativ system som stöds](../sizes-hpc.md) |
| 12 | Bild erbjudandet stöds inte |
| 13 | VM-storleken stöds inte | Använd en InfiniBand-aktiverad (r-storlek) [H-seriens](../sizes-hpc.md) och [n](../sizes-gpu.md)-seriens virtuella dator för att distribuera |
| 14 | Åtgärden lyckades inte | Kontrol lera loggen för körnings utdata |


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/community/). Du kan också välja en support incident via [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/). Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om InfiniBand-aktiverade (r-storlekar) finns i [d-](../sizes-hpc.md) och [N-seriens](../sizes-gpu.md) virtuella datorer.

> [!div class="nextstepaction"]
> [Läs mer om tillägg och funktioner för virtuella Linux-datorer](features-linux.md)