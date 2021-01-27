---
title: Azure PowerShell exempel – hämta Azure Cloud Service (utökad support) information
description: Exempel skript för att hämta information om en Azure Cloud Services-distribution (utökad support)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 522ba95a5d6d97a8fde242274b1b0af969af20a5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881508"
---
# <a name="retrieve-information-about-your-azure-cloud-service-extended-support-deployments"></a>Hämta information om Azure Cloud Service (utökad support) distributioner

De här exemplen behandlar olika var för att hämta information om befintliga distributioner av Azure Cloud Service (utökad support).

## <a name="get-all-cloud-services-under-a-resource-group"></a>Hämta alla Cloud Services under en resurs grupp

```powershell
Get-AzCloudService -ResourceGroup "ContosOrg"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded
ContosOrg         ContosoCSTest     eastus2euap Failed
```

## <a name="get-single-cloud-service"></a>Hämta en enda moln tjänst
```powershell
Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded

$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
$cloudService | Format-List
ResourceGroupName                       : ContosOrg
Configuration                           : <?xml version="1.0" encoding="utf-8"?>
                                          <ServiceConfiguration 
                                          xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
                                          </ServiceConfiguration>
ConfigurationUrl                        :
ExtensionProfileExtension               : {RDPExtension}
Id                                      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ContosOrg/providers/Microsoft.Compute/cloudServices/ContosoCS
Location                                : eastus2euap
Name                                    : ContosoCS
NetworkProfileLoadBalancerConfiguration : {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
OSProfileSecret                         : {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
PackageUrl                              : https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
ProvisioningState                       : Succeeded
RoleProfileRole                         : {ContosoFrontEnd, ContosoBackEnd}
StartCloudService                       :
SwappableCloudServiceId                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ContosOrg/providers/Microsoft.Compute/cloudServices/ContosoCSTest
Tag                                     : {
                                            "Owner": "Contos"
                                          }
Type                                    : Microsoft.Compute/cloudServices
UniqueId                                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
UpgradeMode                             : Auto
```

## <a name="get-cloud-service-instance-view"></a>Hämta instans visning av moln tjänst
```powershell
Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS" -InstanceView | Format-List

RoleInstanceStatusesSummary : {{
                                "code": "ProvisioningState/succeeded",
                                "count": 4
                              }, {
                                "code": "PowerState/started",
                                "count": 4
                              }}
Statuses                    : {{
                                "code": "ProvisioningState/succeeded",
                                "displayStatus": "Provisioning succeeded",
                                "level": "Info",
                                "time": "2020-10-20T13:13:45.0067685Z"
                              }, {
                                "code": "PowerState/started",
                                "displayStatus": "Started",
                                "level": "Info",
                                "time": "2020-10-20T13:13:45.0067685Z"
                              }, {
                                "code": "CurrentUpgradeDomain/-1",
                                "displayStatus": "Current Upgrade Domain of Cloud Service is -1.",
                                "level": "Info"
                              }, {
                                "code": "MaxUpgradeDomain/1",
                                "displayStatus": "Max Upgrade Domain of Cloud Service is 1.",
                                "level": "Info"
                              }}
```

## <a name="next-steps"></a>Nästa steg

- Mer information om Azure Cloud Services (utökad support) finns i [Översikt över Azure Cloud Services (utökad support)](overview.md).
- Besök den [Cloud Services (utökad support) exempel lagrings plats](https://github.com/Azure-Samples/cloud-services-extended-support)