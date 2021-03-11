---
title: Tilläggs hanterare för önskad tillstånds konfiguration i Azure
description: Ladda upp och tillämpa en PowerShell DSC-konfiguration på en virtuell Azure-dator med DSC-tillägg
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: bobbytreed
ms.author: robreed
ms.collection: windows
ms.date: 03/26/2018
ms.openlocfilehash: 72f66aeee64133a13ce0e49155c4b2a90240a3fb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559995"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC-tillägg

## <a name="overview"></a>Översikt

PowerShell DSC-tillägget för Windows publiceras och stöds av Microsoft. Tillägget överför och tillämpar en PowerShell DSC-konfiguration på en virtuell Azure-dator. DSC-tillägget anropar PowerShell DSC för att införa den mottagna DSC-konfigurationen på den virtuella datorn. Det här dokumentet innehåller information om plattformar, konfigurationer och distributions alternativ för DSC-tillägget för virtuella datorer för Windows.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

DSC-tillägget stöder följande operativ system

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-klient 7/8.1/10

### <a name="internet-connectivity"></a>Internetanslutning

DSC-tillägget för Windows kräver att den virtuella mål datorn kan kommunicera med Azure och platsen för konfigurations paketet (zip-filen) om den lagras på en plats utanför Azure. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för inställnings delen av DSC-tillägget i en Azure Resource Manager-mall. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
             "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Egenskaps värden

| Name | Värde/exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| utgivare | Microsoft. PowerShell. DSC | sträng |
| typ | DSC | sträng |
| typeHandlerVersion | 2,77 | int |

### <a name="settings-property-values"></a>Egenskaps värden för inställningar

| Name | Datatyp | Beskrivning
| ---- | ---- | ---- |
| Settings. wmfVersion | sträng | Anger den version av Windows Management Framework som ska installeras på den virtuella datorn. Om du anger den här egenskapen till "senaste" installeras den uppdaterade versionen av WMF. De enda aktuella möjliga värdena för den här egenskapen är "4,0", "5,0" och "senaste". Dessa möjliga värden är beroende av uppdateringar. Standardvärdet är "senaste". |
| settings.configuration. URL | sträng | Anger den URL-plats från vilken du vill ladda ned ZIP-filen för DSC-konfigurationen. Om den angivna webb adressen kräver en SAS-token för åtkomst måste du ange värdet för SAS-token för egenskapen protectedSettings.configurationUrlSasToken. Den här egenskapen krävs om settings.configuration. script och/eller settings.configuration. Function definieras.
| settings.configuration. script | sträng | Anger fil namnet på skriptet som innehåller definitionen av din DSC-konfiguration. Det här skriptet måste finnas i rotmappen för den zip-fil som hämtats från den URL som anges av egenskapen Configuration. URL. Den här egenskapen krävs om settings.configuration. URL och/eller settings.configuration. script definieras.
| settings.configuration. Function | sträng | Anger namnet på din DSC-konfiguration. Konfigurationen med namnet måste finnas i skriptet som definieras av Configuration. script. Den här egenskapen krävs om settings.configuration. URL och/eller settings.configuration. Function definieras.
| settings.configurationArguments | Samling | Definierar de parametrar som du vill skicka till din DSC-konfiguration. Den här egenskapen kommer inte att krypteras.
| settings.configurationData. URL | sträng | Anger den URL från vilken du vill ladda ned konfigurations data filen (. pds1) som ska användas som indata för din DSC-konfiguration. Om den angivna webb adressen kräver en SAS-token för åtkomst måste du ange värdet för SAS-token för egenskapen protectedSettings.configurationDataUrlSasToken.
| Settings. privacy. dataEnabled | sträng | Aktiverar eller inaktiverar telemetri-samling. De enda möjliga värdena för den här egenskapen är Enable, Disable, eller $null. Om du lämnar den här egenskapen tom eller null aktive ras telemetri
| Settings. advancedOptions. forcePullAndApply | Bool | Den här inställningen är utformad för att förbättra upplevelsen av att arbeta med tillägget för att registrera noder med Azure Automation DSC.  Om värdet är kommer `$true` tillägget att vänta på den första körningen av konfigurationen som hämtas från tjänsten innan den returnerar ett lyckat/misslyckat försök.  Om värdet är inställt på $false, kommer statusen som returneras av tillägget bara att referera till om noden registrerades med Azure Automation tillstånds konfiguration och nodens konfiguration inte ska köras under registreringen.
| Settings. advancedOptions. downloadMappings | Samling | Definierar alternativa platser för nedladdning av beroenden, till exempel WMF och .NET

### <a name="protected-settings-property-values"></a>Egenskaps värden för skyddade inställningar

| Name | Datatyp | Beskrivning
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | sträng | Definierar de parametrar som du vill skicka till din DSC-konfiguration. Den här egenskapen kommer att krypteras. |
| protectedSettings.configurationUrlSasToken | sträng | Anger SAS-token för åtkomst till den URL som definieras av Configuration. URL. Den här egenskapen kommer att krypteras. |
| protectedSettings.configurationDataUrlSasToken | sträng | Anger SAS-token för åtkomst till den URL som definieras av configurationData. URL. Den här egenskapen kommer att krypteras. |


## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar.
Mallarna är idealiska när du distribuerar en eller flera virtuella datorer som kräver konfiguration av efter distribution.
En exempel Resource Manager-mall som innehåller DSC-tillägget för Windows finns i [Azure Snabbstart-galleriet](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och med hjälp av Azure CLI. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tilläggs paketet laddas ned och distribueras till den här platsen på den virtuella Azure-datorn
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Tilläggs status filen innehåller fälten under status och status lyckades/Error tillsammans med det detaljerade felet och beskrivningen för varje tillägg som körs.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Utgående loggar loggas i följande katalog:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras betydelser

| Felkod | Innebörd | Möjlig åtgärd |
| :---: | --- | --- |
| 1000 | Allmänt fel | Meddelandet för det här felet tillhandahålls av det angivna undantaget i tilläggs loggar |
| 52 | Installations fel för tillägg | Meddelandet för det här felet tillhandahålls av det angivna undantaget |
| 1002 | Installations fel för WMF | Fel vid installation av WMF. |
| 1004 | Ogiltigt zip-paket | Ogiltigt zip; Fel vid uppackning av zip |
| 1100 | Argument fel | Indikerar ett problem i de indata som tillhandahålls av användaren. Meddelandet för felet anges i det angivna undantaget|


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
