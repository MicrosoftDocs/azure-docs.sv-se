---
title: Azure DSC-tillägg för Linux
description: Installerar OMI-och DSC-paket så att en virtuell Azure Linux-dator kan konfigureras med önskad tillstånds konfiguration.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 59b05fcd7fbaf9b0fd9b4083c884edadb4bfef4e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505588"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC-tillägg för Linux (Microsoft. OSTCExtensions. DSCForLinux)

Önskad tillstånds konfiguration (DSC) är en hanterings plattform som du kan använda för att hantera IT-och utvecklings infrastrukturen med konfiguration som kod.

> [!NOTE]
> DSC-tillägget för Linux och [Azure Monitor tillägg för virtuell dator för Linux](./oms-linux.md) presenterar en konflikt som inte stöds i en sida-vid-sida-konfiguration. Använd inte de två lösningarna tillsammans på samma virtuella dator.

DSCForLinux-tillägget har publicerats och stöds av Microsoft. Tillägget installerar OMI-och DSC-agenten på virtuella Azure-datorer. DSC-tillägget kan också utföra följande åtgärder:

- Registrera den virtuella Linux-datorn till ett Azure Automation-konto för att hämta konfigurationer från tjänsten Azure Automation (registrera ExtensionAction).
- Skicka MOF-konfigurationer till den virtuella Linux-datorn (push-ExtensionAction).
- Använd meta MOF-konfiguration på den virtuella Linux-datorn om du vill konfigurera en pull-server för att hämta Node-konfigurationen (pull-ExtensionAction).
- Installera anpassade DSC-moduler till den virtuella Linux-datorn (installera ExtensionAction).
- Ta bort anpassade DSC-moduler från den virtuella Linux-datorn (ta bort ExtensionAction).

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

För noder som kör Linux stöder DSC Linux-tillägget alla Linux-distributioner som anges i [POWERSHELL DSC-dokumentationen](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

### <a name="internet-connectivity"></a>Internetanslutning

DSCForLinux-tillägget kräver att den virtuella mål datorn är ansluten till Internet. Register tillägget kräver till exempel anslutning till Automation-tjänsten.
För andra åtgärder som pull, pull, installation krävs anslutning till Azure Storage och GitHub. Det beror på de inställningar som tillhandahålls av kunden.

## <a name="extension-schema"></a>Tilläggsschema

### <a name="public-configuration"></a>Offentlig konfiguration

Här följer alla offentliga konfigurations parametrar som stöds:

* `FileUri`: (valfritt, sträng) URI för MOF-filen, meta MOF-filen eller den anpassade resursens zip-fil.
* `ResourceName`: (valfritt, sträng) namnet på den anpassade modulen.
* `ExtensionAction`: (valfritt, sträng) anger vad ett tillägg gör. Giltiga värden är registrera, push, pull, install och Remove. Om inget värde anges anses det som standard en push-åtgärd.
* `NodeConfigurationName`: (valfritt, sträng) namnet på den nods konfiguration som ska användas.
* `RefreshFrequencyMins`: (valfritt, int) anger hur ofta (i minuter) som DSC försöker hämta konfigurationen från hämtnings servern.
       Om konfigurationen på hämtnings servern skiljer sig från den nuvarande på målnoden kopieras den till den väntande lagringen och tillämpas.
* `ConfigurationMode`: (valfritt, sträng) anger hur DSC ska tillämpa konfigurationen. Giltiga värden är ApplyOnly, ApplyAndMonitor och ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (valfritt, int) anger hur ofta (i minuter) DSC säkerställer att konfigurationen är i önskat tillstånd.

> [!NOTE]
> Om du använder en tidigare version än 2,3 är läges parametern densamma som ExtensionAction. Läget verkar vara ett överbelastat villkor. För att undvika förvirring används ExtensionAction från version 2,3 och senare. För bakåtkompatibilitet stöder tillägget både läge och ExtensionAction.
>

### <a name="protected-configuration"></a>Skyddad konfiguration

Här följer alla skyddade konfigurations parametrar som stöds:

* `StorageAccountName`: (valfritt, sträng) namnet på det lagrings konto som innehåller filen
* `StorageAccountKey`: (valfritt, sträng) nyckeln för det lagrings konto som innehåller filen
* `RegistrationUrl`: (valfritt, sträng) URL: en för det Azure Automation kontot
* `RegistrationKey`: (valfritt, sträng) åtkomst nyckeln för det Azure Automation kontot


## <a name="scenarios"></a>Scenarier

### <a name="register-an-azure-automation-account"></a>Registrera ett Azure Automation konto
protected.jspå
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.jspå
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell-format
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Tillämpa en MOF-konfigurationsfil (i ett Azure Storage-konto) på den virtuella datorn

protected.jspå
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jspå
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell-format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Tillämpa en MOF-konfigurationsfil (i offentlig lagring) på den virtuella datorn

public.jspå
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell-format
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Tillämpa en konfigurations fil för meta MOF (i ett Azure Storage-konto) på den virtuella datorn

protected.jspå
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jspå
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell-format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Tillämpa en konfigurations fil för meta MOF (i offentlig lagring) på den virtuella datorn
public.jspå
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
PowerShell-format
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Installera en anpassad resurspool (en zip-fil i ett Azure Storage-konto) till den virtuella datorn
protected.jspå
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.jspå
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell-format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Installera en anpassad resurspool (en zip-fil i offentlig lagring) på den virtuella datorn
public.jspå
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell-format
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Ta bort en anpassad resurspool från den virtuella datorn
public.jspå
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
PowerShell-format
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. Mallarna är idealiska när du distribuerar en eller flera virtuella datorer som kräver konfiguration efter distribution, till exempel onboarding till Azure Automation.

Exempel Resource Manager-mallen är [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) och [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Mer information om Azure Resource Manager-mallen finns i [redigera Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md).


## <a name="azure-cli-deployment"></a>Azure CLI-distribution

### <a name="use-azure-cliazure-cli"></a>Använda [Azure CLI] [Azure-CLI]
Innan du distribuerar DSCForLinux-tillägget konfigurerar du `public.json` och `protected.json` enligt de olika scenarierna i avsnitt 3.

#### <a name="classic"></a>Klassisk

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Det klassiska distributions läget kallas även för Azure Service Management-läge. Du kan växla till den genom att köra:
```
$ azure config mode asm
```

Du kan distribuera DSCForLinux-tillägget genom att köra:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

För att lära dig den senaste tilläggs versionen som är tillgänglig, kör:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
Du kan växla till Azure Resource Manager läge genom att köra:
```
$ azure config mode arm
```

Du kan distribuera DSCForLinux-tillägget genom att köra:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> I Azure Resource Manager läge är `azure vm extension list` inte tillgängligt för tillfället.
>

### <a name="use-azure-powershellazure-powershell"></a>Använda [Azure PowerShell] [Azure-PowerShell]

#### <a name="classic"></a>Klassisk

Du kan logga in på ditt Azure-konto i Azure Service Management-läge genom att köra:

```powershell
Add-AzureAccount
```

Och distribuera DSCForLinux-tillägget genom att köra:

```powershell
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Ändra innehållet i $privateConfig och $publicConfig enligt olika scenarier i föregående avsnitt.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

Du kan logga in på ditt Azure-konto i Azure Resource Manager läge genom att köra:

```powershell
Login-AzAccount
```

Mer information om hur du använder Azure PowerShell med Azure Resource Manager finns i [Hantera Azure-resurser med hjälp av Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md).

Du kan distribuera DSCForLinux-tillägget genom att köra:

```powershell
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Ändra innehållet i $privateConfig och $publicConfig enligt olika scenarier i föregående avsnitt.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och med hjälp av Azure CLI. Om du vill se distributions statusen för tillägg för en specifik virtuell dator kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Utökning av utdata loggas i följande fil:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Felkod: 51 representerar en distribution som inte stöds eller en tilläggs åtgärd som inte stöds.
I vissa fall kan DSC Linux-tillägget inte installera OMI när det redan finns en senare version av OMI på datorn. [fel svar: (000003) nedgradering tillåts inte]



### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/community/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [tillägg för virtuella datorer och funktioner för Linux](features-linux.md).
