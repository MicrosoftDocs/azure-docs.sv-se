---
title: Resurser utan gräns för 800
description: Visar en lista över de Azure-resurs typer som kan ha fler än 800 instanser i en resurs grupp.
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 6736118f0713a27a91618173e06423530e6f10cb
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563121"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Resurser som inte är begränsade till 800 instanser per resurs grupp

Som standard kan du distribuera upp till 800 instanser av en resurs typ i varje resurs grupp. Vissa resurs typer är dock undantagna från gränsen på 800-instanser. Den här artikeln innehåller en lista över de Azure-resurs typer som kan ha fler än 800 instanser i en resurs grupp. Alla andra resurs typer är begränsade till 800 instanser.

För vissa resurs typer måste du kontakta supporten om du vill ta bort instans gränsen på 800. Dessa resurs typer anges i den här artikeln.

## <a name="microsoftautomation"></a>Microsoft. Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

* edgeSubscriptions
* linkedSubscriptions
* registreringar
* registreringar/customerSubscriptions
* registreringar/produkter

## <a name="microsoftbotservice"></a>Microsoft. BotService

* botServices – som standard är begränsad till 800 instanser. Du kan öka gränsen genom att kontakta supporten.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disk
* gallerier
* gallerier/bilder
* gallerier/avbildningar/versioner
* images
* snapshots
* virtualMachineScaleSets – som standard är begränsad till 800 instanser. Du kan öka gränsen genom att kontakta supporten.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

* register/buildTasks
* register/buildTasks/listSourceRepositoryProperties
* register/buildTasks/steg
* register/buildTasks/steg/listBuildArguments
* register/eventGridFilters
* register/replikeringar
* register/uppgifter
* register/Webhooks

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

* pipe

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

* brygghuvudservrar

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

* flexibleServers
* brygghuvudservrar

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

* flexibleServers
* serverGroups
* brygghuvudservrar
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

* scheman

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft. EventHub

* kluster
* namn områden

## <a name="microsoftexperimentation"></a>Microsoft. experimentering

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* IntelliPoint
* softwareUpdateProfile
* softwareUpdates

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

* datorer – stöder upp till 5 000 instanser
* tillägg – stöder ett obegränsat antal instanser av virtuella dator tillägg

## <a name="microsoftinsights"></a>Microsoft. Insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft. Logic

* integrationAccounts
* arbetsflöden

## <a name="microsoftmedia"></a>Microsoft. Media

* Media Services/liveEvents

## <a name="microsoftnetapp"></a>Microsoft. NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/Volumes
* netAppAccounts/capacityPools/Volumes/mountTargets
* netAppAccounts/capacityPools/volym/ögonblicks bilder

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
* dnszones/alla
* dnszones/Recordset
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
* privateDnsZones/alla
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses – som standard är begränsad till 800 instanser. Du kan öka gränsen genom att kontakta supporten.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

* workspaceCollections – som standard är begränsad till 800 instanser. Du kan öka gränsen genom att kontakta supporten.

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

* kapaciteter – som standard är begränsad till 800 instanser. Du kan öka gränsen genom att kontakta supporten.

## <a name="microsoftrelay"></a>Microsoft. Relay

* namn områden

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

* förfrågningsåtgärder

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* namn områden

## <a name="microsoftsingularity"></a>Microsoft. Singularity

* konton
* konton/accountQuotaPolicies
* konton/groupPolicies
* konton/jobb
* konton/storageContainers

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftsql"></a>Microsoft.Sql

* servrar/databaser

## <a name="microsoftweb"></a>Microsoft. Web

* apiManagementAccounts/API: er
* webbplatser

## <a name="next-steps"></a>Nästa steg

En fullständig lista över kvoter och begränsningar finns i [Azure-prenumerationer, tjänst gränser, kvoter och begränsningar](azure-subscription-service-limits.md).
