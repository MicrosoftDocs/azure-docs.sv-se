---
title: NVIDIA GPU-drivrutins tillägg – virtuella Azure Linux-datorer
description: Microsoft Azure tillägget för att installera NVIDIA GPU-drivrutiner i virtuella datorer i N-serien som kör Linux.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: amverma
ms.openlocfilehash: fa2b82f3246fd87830010f572ba23aa2df075053
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566234"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>NVIDIA GPU-drivrutins tillägg för Linux

## <a name="overview"></a>Översikt

Det här tillägget installerar NVIDIA GPU-drivrutiner på virtuella datorer i Linux N-serien. Beroende på VM-serien installerar tillägget CUDA eller GRID-drivrutiner. När du installerar NVIDIA-drivrutiner med det här tillägget accepterar du och godkänner villkoren i [licens avtalet för NVIDIA-End-User](https://go.microsoft.com/fwlink/?linkid=874330). Under installationen kan den virtuella datorn startas om för att slutföra driv rutins konfigurationen.

Anvisningar om manuell installation av driv rutinerna och de aktuella versioner som stöds finns [här](../linux/n-series-driver-setup.md).
Det finns också ett tillägg för att installera NVIDIA GPU-drivrutiner på [virtuella datorer med Windows N-serien](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Det här tillägget har stöd för följande OS-distributioner, beroende på driv rutins stöd för en speciell OS-version.

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 16,04 LTS 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |
| Linux: CentOS | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |

### <a name="internet-connectivity"></a>Internetanslutning

Microsoft Azure-tillägget för NVIDIA GPU-drivrutiner kräver att den virtuella mål datorn är ansluten till Internet och har åtkomst.

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
| typ | NvidiaGpuDriverLinux | sträng |
| typeHandlerVersion | 1.3 | int |

### <a name="settings"></a>Inställningar

Alla inställningar är valfria. Standard beteendet är att inte uppdatera kerneln om det inte krävs för installation av driv rutiner, installera den senaste driv rutinen och CUDA Toolkit (i tillämpliga fall).

| Name | Beskrivning | Standardvärde | Giltiga värden | Datatyp |
| ---- | ---- | ---- | ---- | ---- |
| Uppdatering | Uppdatera kärnan även om det inte krävs för att installera driv rutiner | falskt | SANT, FALSKT | boolean |
| driverVersion | NV: RUTNÄTs driv rutins version<br> NC/ND: CUDA Toolkit-version. De senaste driv rutinerna för de valda CUDA installeras automatiskt. | senaste | [Lista](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) över driv rutins versioner som stöds | sträng |
| installCUDA | Installera CUDA Toolkit. Endast relevant för virtuella datorer i NC/ND-serien. | true | SANT, FALSKT | boolean |


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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

I följande exempel speglas ovanstående Azure Resource Manager-och PowerShell-exempel.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 
```

Följande exempel lägger också till två valfria anpassade inställningar som exempel på driv rutins installation som inte är standard. Mer specifikt uppdaterar OS-kerneln till den senaste och installerar en specifik versions driv rutin för CUDA Toolkit. Observera återigen att "--Settings" är valfria och standard. Observera att uppdateringen av kernel kan öka installations tiderna för tillägget. Du kan också välja en speciell (äldre) CUDA tolkit-version som inte alltid är kompatibel med nyare kärnor.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
  }'
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

Utökning av utdata loggas i följande fil. Läs den här filen om du vill spåra statusen för (alla tids krävande) installationer och för att felsöka eventuella fel.

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Avslutnings koder

| Slutkod | Innebörd | Möjlig åtgärd |
| :---: | --- | --- |
| 0 | Åtgärden lyckades |
| 1 | Felaktig användning av tillägg | Kontrol lera loggen för körnings utdata |
| 10 | Linux-integrerings tjänster för Hyper-V och Azure är inte tillgängliga eller installerade | Kontrol lera utdata från lspci |
| 11 | NVIDIA-GPU hittades inte på den här virtuella dator storleken | Använd en [VM-storlek och ett operativ system som stöds](../linux/n-series-driver-setup.md) |
| 12 | Bild erbjudandet stöds inte |
| 13 | VM-storleken stöds inte | Använd en virtuell dator i N-serien för att distribuera |
| 14 | Åtgärden lyckades inte | Kontrol lera loggen för körnings utdata |


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/community/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [tillägg för virtuella datorer och funktioner för Linux](features-linux.md).

Mer information om virtuella datorer i N-serien finns i [GPU-optimerade storlekar för virtuella datorer](../sizes-gpu.md).
