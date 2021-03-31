---
title: NVIDIA GPU-drivrutins tillägg – virtuella Azure-datorer i Windows
description: Microsoft Azure tillägget för att installera NVIDIA GPU-drivrutiner i virtuella datorer i N-serien som kör Windows.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: 7cd2c5e54ccb81294a93c0ecebaa174df8d14011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559672"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>NVIDIA GPU-drivrutins tillägg för Windows

## <a name="overview"></a>Översikt

Det här tillägget installerar NVIDIA GPU-drivrutiner på virtuella datorer med Windows N-serien. Beroende på VM-serien installerar tillägget CUDA eller GRID-drivrutiner. När du installerar NVIDIA-drivrutiner med det här tillägget accepterar du och godkänner villkoren i [licens avtalet för NVIDIA-End-User](https://go.microsoft.com/fwlink/?linkid=874330). Under installationen kan den virtuella datorn startas om för att slutföra driv rutins konfigurationen.

Anvisningar om manuell installation av driv rutinerna och de aktuella versioner som stöds finns [här](../windows/n-series-driver-setup.md).
Det finns också ett tillägg för att installera NVIDIA GPU-drivrutiner på [virtuella datorer i Linux N-serien](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Det här tillägget har stöd för följande OSs:

| Distribution | Version |
|---|---|
| Windows 10 | Kärna |
| Windows Server 2016 | Kärna |
| Windows Server 2012 R2 | Kärna |

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
    "type": "NvidiaGpuDriverWindows",
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
| typ | NvidiaGpuDriverWindows | sträng |
| typeHandlerVersion | 1.3 | int |


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
    "type": "NvidiaGpuDriverWindows",
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
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
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

Utökning av utdata loggas i följande katalog:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\
```

### <a name="error-codes"></a>Felkoder

| Felkod | Innebörd | Möjlig åtgärd |
| :---: | --- | --- |
| 0 | Åtgärden lyckades |
| 1 | Åtgärden lyckades. Omstart krävs. |
| 100 | Åtgärden stöds inte eller kunde inte slutföras. | Möjliga orsaker: PowerShell-versionen stöds inte, VM-storleken är inte en virtuell dator i N-serien, fel vid hämtning av data. Kontrol lera loggfilerna för att ta reda på orsaken till felet. |
| 240, 840 | Åtgärds-timeout. | Försök igen. |
| -1 | Ett undantag inträffade. | Kontrol lera loggfilerna för att ta reda på orsaken till undantaget. |
| -5x | Åtgärden avbröts på grund av en väntande omstart. | Starta om virtuell dator. Installationen fortsätter efter att datorn har startats om. Avinstallationen ska anropas manuellt. |


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/community/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [tillägg för virtuella datorer och funktioner för Windows](features-windows.md).

Mer information om virtuella datorer i N-serien finns i [GPU-optimerade storlekar för virtuella datorer](../sizes-gpu.md).
