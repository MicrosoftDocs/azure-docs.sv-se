---
title: Åsidosätt SKU-information över CSCFG/CSDEF för Azure Cloud Services (utökad support)
description: Åsidosätt SKU-information över CSCFG/CSDEF för Azure Cloud Services (utökad support)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: 17e47b562c52ffce631a01cf03004d77053ea647
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387336"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Åsidosätt SKU-information över CSCFG/CSDEF i Cloud Services (utökad support) 

Med den här funktionen kan användaren uppdatera roll storleken och antalet instanser i moln tjänsten med hjälp av egenskapen **allowModelOverride** utan att behöva uppdatera tjänstens konfiguration och tjänst definitions filerna, vilket innebär att moln tjänsten kan skalas upp/ned/in/ut utan att göra en ompaketering och omdistribuera.

## <a name="set-allowmodeloverride-property"></a>Ange egenskapen allowModelOverride
Du kan ställa in egenskapen allowModelOverride på följande sätt:
* När allowModelOverride = True, kommer API-anropet att uppdatera roll storlek och instans antal för moln tjänsten utan att validera värdena med csdef-och cscfg-filerna. 
> [!Note]
> Cscfg-filen kommer att uppdateras för att avspegla roll instans antalet, men csdef (inom cspkg) behåller de gamla värdena
* När allowModelOverride = false skulle API-anropet orsaka ett fel när värdena för roll storlek och instans antal inte stämmer överens med csdef-och cscfg-filerna

Standardvärdet är inställt på false. Om egenskapen återställs till falskt tillbaka från True, kommer csdef-och cscfg-filerna återigen att kontrol leras för verifiering.

Gå igenom exemplen nedan för att tillämpa egenskapen i PowerShell, Template och SDK

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Om du anger egenskapen "allowModelOverride" = true här uppdateras moln tjänsten med roll egenskaperna som definierats i avsnittet roleProfile
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “**allowModelOverride**” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
Om du ställer in växeln "AllowModelOverride" på den nya New-AzCloudService-cmdleten uppdateras moln tjänsten med de SKU-egenskaper som definierats i RoleProfile
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK
Om du ställer in variabeln AllowModelOverride = True uppdateras moln tjänsten med de SKU-egenskaper som definierats i RoleProfile

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>Azure Portal
Portalen tillåter inte att egenskapen ovan åsidosätter roll storleken och antalet instanser i csdef och cscfg-filen. 


## <a name="next-steps"></a>Nästa steg 
- Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support).
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
