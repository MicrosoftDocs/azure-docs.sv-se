---
title: Azure Policy gästkonfigurationstillägg
description: Lär dig mer om tillägget som används för att granska/konfigurera inställningar i virtuella datorer
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.openlocfilehash: 2fda3cc2cf9adc3a734780209a0c9cc06a04e7cf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368718"
---
# <a name="overview-of-the-azure-policy-guest-configuration-extension"></a>Översikt över Azure Policy gästkonfigurationstillägget

Gästkonfigurationstillägget är en komponent Azure Policy utför gransknings- och konfigurationsåtgärder på virtuella datorer.
Principer som säkerhetsbaslinjedefinitioner [för Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) [och Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) kan inte kontrollera inställningarna i datorer förrän tillägget har installerats.

## <a name="prerequisites"></a>Förutsättningar

Datorn måste ha en system tilldelad hanterad identitet för att datorn ska kunna autentiseras mot [gästkonfigurationstjänsten.](../../active-directory/managed-identities-azure-resources/overview.md)
Identitetskravet på en virtuell dator uppfylls om följande egenskap har angetts.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>Operativsystem

Stöd för gästkonfigurationstillägget är detsamma som operativsystemets stöd som [dokumenterats för lösningen från slutet till slut.](../../governance/policy/concepts/guest-configuration.md#supported-client-types)

### <a name="internet-connectivity"></a>Internetanslutning

Agenten som installeras av gästkonfigurationstillägget måste kunna nå innehållspaket som anges av gästkonfigurationstilldelningar och rapportera status till gästkonfigurationstjänsten.
Datorn kan ansluta med utgående HTTPS via TCP-port 443 eller om en anslutning tillhandahålls via privata nätverk.
Mer information om privata nätverk finns i följande artiklar:

- [Gästkonfiguration, kommunicera via privat länk i Azure](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Använda privata slutpunkter för Azure Storage](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>Hur installerar jag tillägget?

Om du vill distribuera den senaste versionen av tillägget i stor skala, inklusive identitetskrav, tilldelar du Azure Policy, Distribuera krav för att aktivera [gästkonfigurationsprinciper på virtuella datorer.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json)
För enskilda datorer kan tillägget distribueras med Hjälp av Azure CLI, PowerShell, Resource Manager-mallar eller verktyg från tredje part.

Instansnamnet för tillägget måste anges till "AzurePolicyforWindows" eller "AzurePolicyforLinux", eftersom de principer som refereras ovan kräver dessa specifika strängar.

Som standard uppdateras alla distributioner till den senaste versionen. Värdet för egenskapen _autoUpgradeMinorVersion_ får som standard värdet "true" om inget annat anges. Du behöver inte bekymra dig om att uppdatera koden när nya versioner av tillägget släpps.

### <a name="azure-cli"></a>Azure CLI

Så här distribuerar du tillägget för Linux:


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

Så här distribuerar du tillägget för Windows:

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

Så här distribuerar du tillägget för Linux:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

Så här distribuerar du tillägget för Windows:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Resource Manager-mall

Så här distribuerar du tillägget för Linux:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Så här distribuerar du tillägget för Windows:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="terraform"></a>Terraform

Så här distribuerar du tillägget för Linux:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforLinux"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforLinux"
  type_handler_version  = "1.0"
}
```

Så här distribuerar du tillägget för Windows:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforWindows"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforWindows"
  type_handler_version  = "1.0"
}
```

## <a name="settings"></a>Inställningar

Du behöver inte inkludera några inställningar eller egenskaper för skyddade inställningar i tillägget.
All sådan information hämtas av agenten från [tilldelningsresurser för gästkonfiguration.](/rest/api/guestconfiguration/guestconfigurationassignments) Egenskaperna [ConfigurationUri,](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation) [Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode)och [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) är till exempel alla hanterade per konfiguration i stället för vm-tillägget.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Policy gästkonfiguration finns i [Förstå Azure Policy gästkonfiguration](../../governance/policy/concepts/guest-configuration.md)
* Mer information om hur Linux-agenten och tillägg fungerar finns i [Azure VM-tillägg och -funktioner för Linux.](features-linux.md)
* Mer information om hur Windows-gästagenten och tillägg fungerar finns i [Azure VM-tillägg och -funktioner för Windows.](features-windows.md)  
* Information om hur du installerar Windows-gästagenten finns [i Översikt över agenten för virtuella Windows-datorer i Azure.](agent-windows.md)  
* Information om hur du installerar Linux-agenten finns i Översikt över Agent för virtuella [Linux-datorer i Azure.](agent-linux.md)  
