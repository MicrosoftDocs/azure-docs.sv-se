---
title: Azure Network Watcher agent virtuell dator tillägg för Linux
description: Distribuera Network Watcher agenten på den virtuella Linux-datorn med ett tillägg för virtuell dator.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/14/2017
ms.openlocfilehash: bc252e560df782625d795b30c6688a34f5c2bd79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563599"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Virtuell dator-tillägget för Network Watcher Agent för Linux

## <a name="overview"></a>Översikt

[Azure Network Watcher](../../network-watcher/index.yml) är en övervaknings-, diagnostik-och analys tjänst för nätverks prestanda som gör det möjligt att övervaka för Azure-nätverk. Tillägget Network Watcher agent virtuell dator (VM) är ett krav för några av de Network Watcher funktionerna på virtuella Azure-datorer, till exempel för att samla in nätverks trafik på begäran och andra avancerade funktioner.

Den här artikeln beskriver de plattformar och distributions alternativ som stöds för det virtuella Network Watcher agent-tillägget för Linux. Installationen av agenten störs inte eller kräver en omstart av den virtuella datorn. Du kan distribuera tillägget till virtuella datorer som du distribuerar. Om den virtuella datorn distribueras av en Azure-tjänst kan du läsa dokumentationen för tjänsten för att avgöra om den tillåter att installera tillägg på den virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Tillägget Network Watcher agent kan konfigureras för följande Linux-distributioner:

| Distribution | Version |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 och 8 |
| Red Hat | 6 och 7 |
| Oracle Linux | 6,8 + och 7 |
| SUSE Linux Enterprise Server | 11 och 12 |
| OpenSUSEt hopp | 42.3 + |
| CentOS | 6.5 + och 7 |
| CoreOS | 899.17.0 + |


### <a name="internet-connectivity"></a>Internetanslutning

Vissa av de Network Watcher agent funktionerna kräver att en virtuell dator är ansluten till Internet. Om du inte kan upprätta utgående anslutningar kan vissa av de Network Watcher agent funktionerna bli fel eller bli otillgängliga. Mer information om Network Watcher funktioner som kräver agenten finns i Network Watcher-[dokumentationen](../../network-watcher/index.yml).

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Network Watcher agent-tillägget. Tillägget kräver inte, eller stöder inte några användar inställningar. Tillägget förlitar sig på standard konfigurationen.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Egenskaps värden

| Name | Värde/exempel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| utgivare | Microsoft. Azure. NetworkWatcher |
| typ | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Malldistribution

Du kan distribuera virtuella Azure-tillägg med en Azure Resource Manager-mall. Använd föregående JSON-schema i mallen för att distribuera Network Watcher agent-tillägget.

## <a name="azure-classic-cli-deployment"></a>Klassisk Azure-CLI-distribution

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

I följande exempel distribueras det virtuella Network Watcher agent-tillägget till en befintlig virtuell dator som distribueras via den klassiska distributions modellen:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

I följande exempel distribueras Network Watcher-agentens VM-tillägg till en befintlig virtuell dator som distribueras via Resource Manager:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Fel sökning och support

### <a name="troubleshooting"></a>Felsökning

Du kan hämta data om statusen för tillägg distributioner med antingen Azure Portal eller Azure CLI.

I följande exempel visas distributions statusen för NetworkWatcherAgentLinux-tillägget för en virtuell dator som distribueras via Resource Manager med hjälp av Azure CLI:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du läsa Network Watcher- [dokumentationen](../../network-watcher/index.yml)eller kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
