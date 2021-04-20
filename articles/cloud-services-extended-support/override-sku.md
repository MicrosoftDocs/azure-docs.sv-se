---
title: Åsidosätt SKU-information över CSCFG/CSDEF för Azure Cloud Services (utökat stöd)
description: Åsidosätt SKU-information över CSCFG/CSDEF för Azure Cloud Services (utökat stöd)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: d5dfae4b5cfee8f61e11e418a05e86017d119410
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739268"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Åsidosätt SKU-information över CSCFG/CSDEF i Cloud Services (utökat stöd) 

Med den här funktionen kan användaren uppdatera rollstorleken och instansantalet i sin molntjänst med hjälp av egenskapen **allowModelOverride** utan att behöva uppdatera tjänstkonfigurationen och tjänstdefinitionsfilerna, vilket gör att molntjänsten kan skala upp/ned/in/ut utan att behöva packa om och distribuera om.

## <a name="set-allowmodeloverride-property"></a>Ange egenskapen allowModelOverride
Egenskapen allowModelOverride kan anges på följande sätt:
* När allowModelOverride = true uppdaterar API-anropet rollstorleken och instansantalet för molntjänsten utan att verifiera värdena med csdef- och cscfg-filerna. 
> [!Note]
> Cscfg uppdateras för att återspegla antalet rollinstanser, men csdef (inom cspkg) behåller de gamla värdena
* När allowModelOverride = false skulle API-anropet ge ett fel när rollstorleken och instansantalet inte matchar csdef- respektive cscfg-filerna

Standardvärdet är inställt på falskt. Om egenskapen återställs till false back från true kontrolleras återigen csdef- och cscfg-filerna för verifiering.

Gå igenom exemplen nedan för att tillämpa egenskapen i PowerShell, mallen och SDK

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Om du anger egenskapen "allowModelOverride" = true här uppdateras molntjänsten med rollegenskaperna som definierats i avsnittet roleProfile
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
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
Om du anger växeln "AllowModelOverride" på den nya New-AzCloudService-cmdleten uppdateras molntjänsten med de SKU-egenskaper som definierats i RoleProfile
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
Om du anger variabeln AllowModelOverride= true uppdateras molntjänsten med de SKU-egenskaper som definierats i RoleProfile

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
Portalen tillåter inte att egenskapen ovan åsidosätter rollstorleken och instansantalet i csdef och cscfg. 


## <a name="next-steps"></a>Nästa steg 
- Granska [distributionens krav för](deploy-prerequisite.md) Cloud Services (utökad support).
- Läs [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
