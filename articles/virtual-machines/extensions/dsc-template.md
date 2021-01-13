---
title: Önskat tillstånds konfigurations tillägg med Azure Resource Manager mallar
description: Lär dig mer om Resource Manager-mallen för Desired State Configuration (DSC)-tillägget i Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: 01a41e9acccdb40bf198031d13c3ea3f13c079ce
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180157"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Önskat tillstånds konfigurations tillägg med Azure Resource Manager mallar

Den här artikeln beskriver Azure Resource Manager mall för den [önskade tilläggs hanteraren för DSC (Desired State Configuration)](dsc-overview.md). Många av exemplen använder **RegistrationURL** (tillhandahålls som en sträng) och **RegistrationKey** (anges som en [PSCredential](/dotnet/api/system.management.automation.pscredential) för att publicera med Azure Automation. Mer information om hur du hämtar dessa värden finns i [onboarding Machines for Management by Azure Automation State Configuration – säker registrering](../../automation/automation-dsc-onboarding.md#enable-machines-securely-using-registration).

> [!NOTE]
> Du kan stöta på något annorlunda schema exempel. Ändringen i schemat inträffade i utgåvan från oktober 2016. Mer information finns i [Uppdatera från ett tidigare format](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Mall-exempel för en virtuell Windows-dator

Följande kodfragment placeras i avsnittet **resurs** i mallen.
DSC-tillägget ärver standard tilläggs egenskaper.
Mer information finns i [VirtualMachineExtension-klass](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-06-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Mall-exempel för skalnings uppsättningar för virtuella Windows-datorer

En nod för skalnings uppsättning för virtuell dator har ett **egenskaps** avsnitt som har ett **VirtualMachineProfile, extensionProfile-** attribut.
Under **tillägg** lägger du till information om DSC-tillägg.

DSC-tillägget ärver standard tilläggs egenskaper.
Mer information finns i [VirtualMachineScaleSetExtension-klass](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Detaljerad inställnings information

Använd följande schema i avsnittet **Inställningar** i Azure DSC-tillägget i en Resource Manager-mall.

En lista över de argument som är tillgängliga för standard konfigurations skriptet finns i [standard konfigurations skript](#default-configuration-script).

```json
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
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
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
```

## <a name="details"></a>Information

| Egenskapsnamn | Typ | Description |
| --- | --- | --- |
| Settings. wmfVersion |sträng |Anger den version av Windows Management Framework (WMF) som ska installeras på den virtuella datorn. Om du anger den här egenskapen till **senaste** installeras den senaste versionen av WMF. För närvarande är de enda möjliga värdena för den här egenskapen **4,0**, **5,0**, **5,1** och **senaste**. Dessa möjliga värden är beroende av uppdateringar. Standardvärdet är **senaste**. |
| settings.configuration. URL |sträng |Anger den URL-plats från vilken du vill ladda ned din DSC-konfiguration. zip-fil. Om den angivna URL: en kräver en SAS-token för åtkomst, ställer du in egenskapen **protectedSettings.configurationUrlSasToken** på värdet för din SAS-token. Den här egenskapen krävs om **settings.configuration. script** eller **settings.configuration. Function** definieras. Om inget värde anges för dessa egenskaper anropar tillägget standard konfigurations skriptet för att ange plats Configuration Manager (LCM) metadata och argument ska anges. |
| settings.configuration. script |sträng |Anger fil namnet på skriptet som innehåller definitionen av din DSC-konfiguration. Det här skriptet måste finnas i rotmappen i zip-filen som hämtats från den URL som anges av egenskapen **settings.configuration. URL** . Den här egenskapen krävs om **settings.configuration. URL** eller **settings.configuration. script** definieras. Om inget värde anges för dessa egenskaper anropar tillägget standard konfigurations skriptet för att ange LCM metadata och argument ska anges. |
| settings.configuration. Function |sträng |Anger namnet på din DSC-konfiguration. Den angivna konfigurationen måste inkluderas i skriptet som **settings.configuration. script** definierar. Den här egenskapen krävs om **settings.configuration. URL** eller **settings.configuration. Function** definieras. Om inget värde anges för dessa egenskaper anropar tillägget standard konfigurations skriptet för att ange LCM metadata och argument ska anges. |
| settings.configurationArguments |Samling |Definierar alla parametrar som du vill skicka till din DSC-konfiguration. Den här egenskapen är inte krypterad. |
| settings.configurationData. URL |sträng |Anger den URL från vilken du vill ladda ned konfigurations data filen (. psd1) som ska användas som indata för din DSC-konfiguration. Om den angivna URL: en kräver en SAS-token för åtkomst, ställer du in egenskapen **protectedSettings.configurationDataUrlSasToken** på värdet för din SAS-token. |
| Settings. privacy. dataCollection |sträng |Aktiverar eller inaktiverar telemetri-samling. De enda möjliga värdena för den här egenskapen är **Aktivera**, inaktivera **, eller** **$Null**.  Om du lämnar den här egenskapen tom eller null aktive ras telemetri. Standardvärdet är **.** Mer information finns i [data insamling för Azure DSC-tillägg](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/). |
| Settings. advancedOptions. downloadMappings |Samling |Definierar alternativa platser som WMF ska hämtas från. Mer information finns i [Azure DSC-tillägg 2,8 och så här mappar du hämtning av fil namns beroenden till din egen plats](https://devblogs.microsoft.com/powershell/azure-dsc-extension-2-8-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location/). |
| protectedSettings.configurationArguments |Samling |Definierar alla parametrar som du vill skicka till din DSC-konfiguration. Den här egenskapen är krypterad. |
| protectedSettings.configurationUrlSasToken |sträng |Anger den SAS-token som ska användas för att få åtkomst till URL: en som **settings.configuration. URL** definierar. Den här egenskapen är krypterad. |
| protectedSettings.configurationDataUrlSasToken |sträng |Anger den SAS-token som ska användas för att få åtkomst till URL: en som  **settings.configurationData. URL** definierar. Den här egenskapen är krypterad. |

## <a name="default-configuration-script"></a>Standard konfigurations skript

Mer information om följande värden finns i [grundläggande inställningar för lokala Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings).
Du kan använda standard konfigurations skriptet för DSC-tillägget för att konfigurera LCM-egenskaperna som visas i följande tabell.

| Egenskapsnamn | Typ | Description |
| --- | --- | --- |
| protectedSettings.configurationArguments. RegistrationKey |PSCredential |Nödvändig egenskap. Anger den nyckel som används för en nod för att registrera med Azure Automation tjänsten som lösen ord för ett PowerShell-Credential-objekt. Det här värdet kan identifieras automatiskt med hjälp av metoden **listnycklar** mot Automation-kontot.  Se [exemplet](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments. RegistrationUrl |sträng |Nödvändig egenskap. Anger URL: en för den Automation-slutpunkt där noden försöker registrera sig. Det här värdet kan identifieras automatiskt med hjälp av **referens** metoden mot Automation-kontot. |
| settings.configurationArguments. NodeConfigurationName |sträng |Nödvändig egenskap. Anger nodens konfiguration i Automation-kontot som ska tilldelas noden. |
| settings.configurationArguments.ConfigurationMode |sträng |Anger läget för LCM. Giltiga alternativ är **ApplyOnly**, **ApplyandMonitor** och **ApplyandAutoCorrect**.  Standardvärdet är **ApplyandMonitor**. |
| settings.configurationArguments. RefreshFrequencyMins | UInt32 | Anger hur ofta LCM försöker kontrol lera med Automation-kontot för uppdateringar.  Standardvärdet är **30**.  Minimalt värde är **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | UInt32 | Anger hur ofta LCM verifierar den aktuella konfigurationen. Standardvärdet är **15**. Minimalt värde är **15**. |
| settings.configurationArguments. RebootNodeIfNeeded | boolean | Anger om en nod kan startas om automatiskt om en DSC-åtgärd begär det. Standardvärdet är **false**. |
| settings.configurationArguments. ActionAfterReboot | sträng | Anger vad som händer efter en omstart när en konfiguration har tillämpats. Giltiga alternativ är **ContinueConfiguration** och **StopConfiguration**. Standardvärdet är **ContinueConfiguration**. |
| settings.configurationArguments. AllowModuleOverwrite | boolean | Anger om LCM skriver över befintliga moduler på noden. Standardvärdet är **false**. |

## <a name="settings-vs-protectedsettings"></a>inställningar kontra protectedSettings

Alla inställningar sparas i text filen inställningar på den virtuella datorn.
Egenskaper som anges under **Inställningar** är offentliga egenskaper.
Offentliga egenskaper krypteras inte i text filen inställningar.
Egenskaperna som anges under **protectedSettings** krypteras med ett certifikat och visas inte som oformaterad text i inställnings filen på den virtuella datorn.

Om konfigurationen behöver autentiseringsuppgifter kan du inkludera autentiseringsuppgifterna i **protectedSettings**:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Exempel på konfigurations skript

I följande exempel visas standard beteendet för DSC-tillägget, vilket innebär att du kan ange inställningar för LCM och registrera dig för den Automation DSC tjänsten.
Konfigurations argument krävs.
Konfigurations argument skickas till standard konfigurations skriptet för att ange LCM-metadata.

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Exempel på hur du använder konfigurations skriptet i Azure Storage

I följande exempel är [översikten DSC Extension handle](dsc-overview.md).
I det här exemplet används Resource Manager-mallar i stället för cmdlets för att distribuera tillägget.
Spara IisInstall.ps1-konfigurationen, placera den i en. zip-fil (exempel: `iisinstall.zip` ) och ladda upp filen i en tillgänglig URL.
I det här exemplet används Azure Blob Storage, men du kan ladda ned ZIP-filer från valfri plats.

I Resource Manager-mallen instruerar följande kod den virtuella datorn att ladda ned rätt fil och kör sedan lämplig PowerShell-funktion:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Exempel som använder refererade Azure Automation registrerings värden

I följande exempel hämtas **RegistrationUrl** och **RegistrationKey** genom att referera till Azure Automation konto egenskaper och använda  **listnycklar** -metoden för att hämta primär nyckeln (0).  I det här exemplet angavs parametrarna **automationAccountName** och **NodeConfigName** för mallen.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Uppdatera från ett tidigare format

Alla inställningar i ett tidigare format för tillägget (och som har de offentliga egenskaperna **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken** eller **Properties**) anpassas automatiskt till det aktuella fil formatet.
De körs precis som de gjorde tidigare.

Följande schema visar hur det tidigare inställnings schemat såg ut som:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Så här anpassas det tidigare formatet till det aktuella formatet:

| Aktuellt egenskaps namn | Föregående schema motsvarande |
| --- | --- |
| Settings. wmfVersion |autentiseringsinställningar. WMFVersion |
| settings.configuration. URL |autentiseringsinställningar. ModulesUrl |
| settings.configuration. script |Första delen av settings.ConfigurationFunction (före \\ \\ ) |
| settings.configuration. Function |Den andra delen av settings.ConfigurationFunction (efter \\ \\ ) |
| settings.configuration.module.name | autentiseringsinställningar. ModuleSource |
| settings.configuration. module. version | autentiseringsinställningar. ModuleVersion |
| settings.configurationArguments |autentiseringsinställningar. Egenskaperna |
| settings.configurationData. URL |protectedSettings. DataBlobUri (utan SAS-token) |
| Settings. privacy. dataCollection |autentiseringsinställningar. Sekretess. dataCollection |
| Settings. advancedOptions. downloadMappings |autentiseringsinställningar. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings. Properties |
| protectedSettings.configurationUrlSasToken |autentiseringsinställningar. SasToken |
| protectedSettings.configurationDataUrlSasToken |SAS-token från protectedSettings. DataBlobUri |

## <a name="troubleshooting"></a>Felsökning

Här följer några av de fel som du kan köra i och hur du kan åtgärda dem.

### <a name="invalid-values"></a>Ogiltiga värden

"Privacy. dataCollection är" {0} ".
De enda möjliga värdena är ' ', ' Enable ' och ' Disable '.
"WmfVersion är" {0} .
Endast möjliga värden är... och "senaste" ".

**Problem**: ett angivet värde är inte tillåtet.

**Lösning**: ändra det ogiltiga värdet till ett giltigt värde.
Mer information finns i tabellen i [information](#details).

### <a name="invalid-url"></a>Ogiltig URL

"ConfigurationData. URL är" {0} ". Detta är inte en giltig URL "" DataBlobUri är " {0} . Detta är inte en giltig URL ""-konfiguration. URL är "" {0} . Detta är inte en giltig URL "

**Problem**: en angiven URL är ogiltig.

**Lösning**: kontrol lera alla dina tillhandahållna URL: er.
Se till att alla webb adresser matchar giltiga platser som tillägget kan komma åt på fjärrdatorn.

### <a name="invalid-registrationkey-type"></a>Ogiltig RegistrationKey-typ

"Ogiltig typ för parametern RegistrationKey av typen PSCredential."

**Problem**: värdet *RegistrationKey* i protectedSettings.configurationArguments kan inte anges som någon annan typ än en PSCredential.

**Lösning**: ändra protectedSettings.configurationArguments-posten för RegistrationKey till en PSCredential-typ med följande format:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Ogiltig ConfigurationArgument-typ

"Ogiltig configurationArguments-typ {0} "

**Problem**: egenskapen *ConfigurationArguments* kan inte matchas mot ett **hash-tabell** objekt.

**Lösning**: gör din *ConfigurationArguments* -egenskap till en **hash-tabell**.
Följ det format som visas i föregående exempel. Titta efter citat tecken, kommatecken och klammerparenteser.

### <a name="duplicate-configurationarguments"></a>Duplicera ConfigurationArguments

"Hittade dubbla argument {0} i både offentliga och skyddade configurationArguments"

**Problem**: *ConfigurationArguments* i offentliga inställningar och *ConfigurationArguments* i skyddade inställningar har egenskaper med samma namn.

**Lösning**: ta bort en av de duplicerade egenskaperna.

### <a name="missing-properties"></a>Saknade egenskaper

"settings.Configuration. Function kräver att settings.configuration. URL eller settings.configuration. module har angetts"

"settings.Configuration. URL kräver att settings.configuration. script anges"

"settings.Configuration. script kräver att settings.configuration. URL har angetts"

"settings.Configuration. URL kräver att settings.configuration. Function har angetts"

"protectedSettings.ConfigurationUrlSasToken kräver att settings.configuration. URL har angetts"

"protectedSettings.ConfigurationDataUrlSasToken kräver att settings.configurationData. URL har angetts"

**Problem**: en definierad egenskap kräver en annan egenskap som saknas.

**Lösningar**:

- Ange den saknade egenskapen.
- Ta bort egenskapen som kräver den saknade egenskapen.

## <a name="next-steps"></a>Nästa steg

- Läs om hur [du använder skalnings uppsättningar för virtuella datorer med Azure DSC-tillägget](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Hitta mer information om [DSC: s säkra hantering av autentiseringsuppgifter](dsc-credentials.md).
- Få en [Introduktion till tilläggs hanteraren för Azure DSC](dsc-overview.md).
- Mer information om PowerShell DSC finns i [PowerShell-dokumentations centret](/powershell/scripting/dsc/overview/overview).
