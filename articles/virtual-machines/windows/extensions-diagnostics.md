---
title: Azure-diagnostik tillägg för Windows
description: Övervaka virtuella Azure Windows-datorer med Azure-diagnostik-tillägget
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e703a8f91f18467660ef7e1b91fdb034feeb00b1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549540"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Azure-diagnostik tillägg för virtuella Windows-datorer

## <a name="overview"></a>Översikt

Med tillägget Azure-diagnostik VM kan du samla in övervaknings data, till exempel prestanda räknare och händelse loggar, från din virtuella Windows-dator. Du kan i detalj ange vilka data du vill samla in och var du vill att data ska placeras, till exempel ett Azure Storage konto eller en Azure Event Hub. Du kan också använda dessa data för att skapa diagram i Azure Portal eller skapa mått aviseringar.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Azure-diagnostik-tillägget kan köras mot Windows 10-klienten, Windows Server 2008 R2, 2012, 2012 R2 och 2016.

### <a name="internet-connectivity"></a>Internetanslutning

Azure-diagnostik-tillägget kräver att den virtuella mål datorn är ansluten till Internet. 

## <a name="extension-schema"></a>Tilläggsschema

[Schemat för Azure-diagnostik-tillägg och egenskaps värden beskrivs i det här dokumentet.](../../azure-monitor/agents/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. Det JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra Azure-diagnostik-tillägget under en distribution av Azure Resource Manager mallar. Se [använda övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager mallar](../extensions/diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera Azure-diagnostik-tillägget till en befintlig virtuell dator. Ersätt egenskaperna Protected Settings och Settings med giltig JSON från tilläggs schemat ovan. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell-distribution

`Set-AzVMDiagnosticsExtension`Kommandot kan användas för att lägga till Azure-diagnostik-tillägget till en befintlig virtuell dator. Se även [använda PowerShell för att aktivera Azure-diagnostik på en virtuell dator som kör Windows](../extensions/diagnostics-windows.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och med hjälp av Azure CLI. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[I den här artikeln finns](../../azure-monitor/agents/diagnostics-extension-troubleshooting.md) en mer omfattande fel söknings guide för Azure-diagnostik-tillägget.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
* [Läs mer om Azure-diagnostik-tillägget](../../azure-monitor/agents/diagnostics-extension-overview.md)
* [Granska tilläggs schema och-versioner](../../azure-monitor/agents/diagnostics-extension-schema-windows.md)
