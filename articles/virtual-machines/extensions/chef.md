---
title: Chefs tillägg för virtuella Azure-datorer
description: Distribuera chefs klienten till en virtuell dator med hjälp av VM-tillägget kock.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 09/21/2018
ms.openlocfilehash: e316bf9763dd7c2cbbab21992086eac52d108912
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554793"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Kock VM-tillägg för Linux och Windows

Chef Software tillhandahåller en DevOps-plattform för automatisering för Linux och Windows som möjliggör hantering av både fysiska och virtuella serverkonfigurationer. Kock VM-tillägget är ett tillägg som möjliggör chef på virtuella datorer.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

VM-tillägget för chef stöds för alla [tillägg som stöds av operativ systemet](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) i Azure.

### <a name="internet-connectivity"></a>Internetanslutning

VM-tillägget för chef kräver att den virtuella mål datorn är ansluten till Internet för att hämta chefs klientens nytto Last från Content Delivery Network (CDN).  

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för VM-tillägget för chef. Tillägget kräver minst chefs serverns URL, verifierings klientens namn och validerings nyckeln för chefs servern. de här värdena finns i `knife.rb` filen i starter-kit.zip som hämtas när du installerar [chefs automatisering](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) eller en fristående [chefs Server](https://downloads.chef.io/chef-server). Eftersom validerings nyckeln ska behandlas som känsliga data, bör den konfigureras under **protectedSettings** -elementet, vilket innebär att den endast dekrypteras på den virtuella mål datorn.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.13",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Egenskaps värden för kärna

| Name | Värde/exempel | Datatyp
| ---- | ---- | ----
| apiVersion | `2017-12-01` | sträng (datum) |
| utgivare | `Chef.Bootstrap.WindowsAzure` | sträng |
| typ | `LinuxChefClient` (Linux), `ChefClient` (Windows) | sträng |
| typeHandlerVersion | `1210.13` | sträng (dubbel) |

### <a name="settings"></a>Inställningar

| Name | Värde/exempel | Datatyp | Obligatoriskt?
| ---- | ---- | ---- | ----
| inställningar/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | sträng (URL) | Y |
| inställningar/bootstrap_options/validation_client_name | `myorg-validator` | sträng | Y |
| inställningar/Runlist | `recipe[mycookbook::default]` | sträng | Y |

### <a name="protected-settings"></a>Skyddade inställningar

| Name | Exempel | Datatyp | Obligatoriskt?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | sträng | Y |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. Mallar kan användas för att distribuera en eller flera virtuella datorer, installera chefs klienten, ansluta till chefs servern och utföra den inledande konfigurationen på servern som definieras i [körnings listen](https://docs.chef.io/run_lists.html)

En exempel Resource Manager-mall som innehåller det virtuella chefs tillägget för VM finns i [Azure snabb starts galleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

JSON-konfigurationen för ett tillägg för virtuell dator kan kapslas i den virtuella dator resursen eller placeras på rot-eller toppnivå i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resurs namn och typ. Mer information finns i [Ange namn och typ för underordnade resurser](../../azure-resource-manager/templates/child-resource-name-type.md).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera kock VM-tillägget till en befintlig virtuell dator. Ersätt **validation_key** med innehållet i validerings nyckeln (filen som ett `.pem` tillägg).  Ersätt **validation_client_name** **chef_server_url** och **run_list** med dessa värden från `knife.rb` filen i ditt Start paket.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Fel sökning och support

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och med hjälp av Azure CLI. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Utökning av utdata loggas i följande fil:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras betydelser

| Felkod | Innebörd | Möjlig åtgärd |
| :---: | --- | --- |
| 51 | Det här tillägget stöds inte på den virtuella datorns operativ system | |

Ytterligare felsöknings information finns i [filen chef VM Extension](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> Kontakta [chefens support](https://www.chef.io/support/)för allt annat som är direkt relaterat till chef.

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
