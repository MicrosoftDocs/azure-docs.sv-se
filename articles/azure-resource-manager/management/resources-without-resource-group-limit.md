---
title: Resurser utan gräns för antal på 800
description: Listar de Azure-resurstyper som kan ha fler än 800 instanser i en resursgrupp.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: d132773ff35d53dc373c759326efc8179f4993d6
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366546"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Resurser som inte är begränsade till 800 instanser per resursgrupp

Som standard kan du distribuera upp till 800 instanser av en resurstyp i varje resursgrupp. Vissa resurstyper är dock undantagna från gränsen på 800 instanser. Den här artikeln listar de Azure-resurstyper som kan ha fler än 800 instanser i en resursgrupp. Alla andra resurstyper är begränsade till 800 instanser.

För vissa resurstyper måste du kontakta supporten för att ta bort gränsen på 800 instanser. Dessa resurstyper beskrivs i den här artikeln.


## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

* resourceHealthAlertRules
* smartDetectorAlertRules

## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* edgeSubscriptions
* linkedSubscriptions
* Registreringar
* registreringar/customerSubscriptions
* registreringar/produkter

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices – som standard begränsat till 800 instanser. Den gränsen kan ökas genom att kontakta supporten.

## <a name="microsoftcompute"></a>Microsoft.Compute

* Diskar
* Gallerier
* gallerier/bilder
* gallerier/bilder/versioner
* images
* snapshots
* virtualMachineScaleSets – som standard begränsat till 800 instanser. Den gränsen kan ökas genom att kontakta supporten.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* register/buildTasks
* register/buildTasks/listSourceRepositoryProperties
* register/buildTasks/steps
* register/buildTasks/steps/listBuildArguments
* register/eventGridFilters
* register/replikeringar
* register/uppgifter
* register/webhooks

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

* Instanser

## <a name="microsoftdbformariadb"></a>Microsoft.DBforTillaDB

* Servrar

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers
* Servrar

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* flexibleServers
* serverGroups
* serverGroupsv2
* Servrar
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* Scheman

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* Kluster
* Namnområden

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentArbetsområden

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* Programvara
* softwareUpdateProfile
* softwareUpdates

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

* datorer – stöder upp till 5 000 instanser
* datorer/tillägg – stöder ett obegränsat antal instanser av VM-tillägg

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* Arbetsflöden

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots
* netAppAccounts/volumeGroups

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses – som standard begränsat till 800 instanser. Den gränsen kan ökas genom att kontakta supporten.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections – som standard begränsat till 800 instanser. Gränsen kan ökas genom att kontakta supporten.

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* autoScaleVCores – som standard begränsat till 800 instanser. Gränsen kan ökas genom att kontakta supporten.
* kapaciteter – som standard begränsat till 800 instanser. Gränsen kan ökas genom att kontakta supporten.

## <a name="microsoftrelay"></a>Microsoft.Relay

* Namnområden

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* Namnområden

## <a name="microsoftsingularity"></a>Microsoft.Singularity

* konton
* accounts/accountQuotaPolicies
* accounts/groupPolicies
* konton/jobb
* accounts/storageContainers

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftsql"></a>Microsoft.Sql

* servrar/databaser

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* webbplatser

## <a name="next-steps"></a>Nästa steg

En fullständig lista över kvoter och gränser finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar.](azure-subscription-service-limits.md)
