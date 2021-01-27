---
title: Stöd för att flytta Azure-resurser mellan regioner
description: Visar en lista över de Azure-resurs typer som kan flyttas mellan Azure-regioner
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 49c5828e02bf96a536ff14f6b84e81f7adbe3090
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806904"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Stöd för att flytta Azure-resurser mellan regioner

I den här artikeln bekräftas om en Azure-resurs har stöd för att flytta till en annan Azure-region. 

Hoppa till ett namn område för en resurs leverantör:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. API Management](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft. attestering](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. fakturering](#microsoftbilling)
> - [Microsoft. Bingkartssökning](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. skiss](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. förbrukning](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. container service](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. data-](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataConnect](#microsoftdataconnect)
> - [Microsoft. DataExchange](#microsoftdataexchange)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLake](#microsoftdatalake)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. DataLakeStore](#microsoftdatalakestore)
> - [Microsoft. data migration](#microsoftdatamigration)
> - [Microsoft. DataProtection](#microsoftdataprotection)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. experimentering](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. features](#microsoftfeatures)
> - [Microsoft. genomik](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. nyckel valv](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. filen LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrate](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft. PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. Quantum](#microsoftquantum)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. support](#microsoftsupport)
> - [Microsoft. Synapse](#microsoftsynapse)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domainservices | Nej | 


## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | diagnosticsettings | Nej |
> | diagnosticsettingscategories | Nej |
> | privatelinkforazuread | Nej |
> | klienter |  Nej |

## <a name="microsoftaddons"></a>Utforskaren. Tillägg

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | supportproviders | Nej |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | aadsupportcases | Nej |
> | addsservices | Nej | 
> | aktörer | Nej | 
> | anonymousapiusers | Nej |
> | konfiguration | Nej | 
> | loggar | Nej | 
> | rapporter | Nej | 
> | servicehealthmetrics | Nej | 
> | services | Nej | 

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | konfigurationer | Nej | 
> | generaterecommendations | Nej |
> | metadata | Nej |
> | rekommendationer | Nej |
> | utelämningar | Nej | 

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | actionrules | Nej | 
> | aviseringar | Nej | 
> | alertslist | Nej | 
> | alertsmetadata | Nej | 
> | alertssummary | Nej | 
> | alertssummarylist | Nej | 
> | smartdetectoralertrules | Nej | 
> | smartgroups | Nej | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | brygghuvudservrar | Nej |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | reportfeedback | Nej |
> | tjänst |  Ja (med mall) <br/><br/> [Flytta API Management över flera regioner](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | configurationstores | Nej | 
> | configurationstores / eventgridfilters | Nej |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | upphängning | Nej | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | apiapps | Ja (med mall)<br/><br/> [Flytta en App Service app till en annan region](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Nej | 
> | gatewayer | Nej | 

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | attestationproviders | Nej | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | classicadministrators | Nej | 
> | dataaliases | Nej | 
> | denyassignments | Nej | 
> | elevateaccess | Nej | 
> | findorphanroleassignments | Nej | 
> | hålls | Nej | 
> | behörigheter | Nej | 
> | policyassignments | Nej | 
> | policydefinitions | Nej | 
> | policysetdefinitions | Nej | 
> | privatelinkassociations | Nej | 
> | resourcemanagementprivatelinks | Nej | 
> | RoleAssignments | Nej | 
> | roleassignmentsusagemetrics | Nej | 
> | roledefinitions | Nej | 

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | automationaccounts | Ja (med mall) <br/><br/> [Använda geo-replikering](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfigurationer | Nej | 
> | automationaccounts/Runbooks | Nej | 

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | Prenumeration |
> | ------------- | ----------- | 
> | privateclouds | Nej | 


## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | b2cdirectories | Nej | 
> | b2ctenants | Nej | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datacontrollers | Nej | 
> | hybriddatamanagers | Nej | 
> | postgresinstances | Nej | 
> | sqlinstances | Nej | 
> | sqlmanagedinstances | Nej |
> | sqlserverinstances | Nej | 
> | sqlserverregistrations | Nej |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cloudmanifestfiles | Nej |
> | registreringar | Nej | 

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | kluster | Nej | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | batchaccounts |  Batch-konton kan inte flyttas direkt från en region till en annan, men du kan använda en mall för att exportera en mall, ändra den och distribuera mallen till den nya regionen. <br/><br/> Lär dig mer om att [Flytta ett batch-konto över flera regioner](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft. fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | billingaccounts | Nej | 
> | billingperiods | Nej | 
> | billingpermissions | Nej | 
> | billingproperty | Nej | 
> | billingroleassignments | Nej | 
> | billingroledefinitions | Nej | 
> | enheten | Nej | 
> | enrollmentaccounts | Nej | 
> | fakturor | Nej | 
> | överlåtelse | Nej | 

## <a name="microsoftbingmaps"></a>Microsoft. Bingkartssökning

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | mapapis | Nej | 

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | biztalk | Nej | 

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | blockchainmembers | Nej <br/><br/> Blockchain-nätverket kan inte ha noder i olika regioner. 
> | cordamembers | Nej |
> | Övervakare | Nej | 

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | tokenservices | Nej |


## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | blueprintassignments | Nej | 
> | modeller | Nej |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | botservices | Nej | 

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Redis | Nej | 
> | redisenterprise | Nej | 

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | appliedreservations | Nej | 
> | calculateexchange | Nej | 
> | calculateprice | Nej | 
> | calculatepurchaseprice | Nej | 
> | kataloger | Nej | 
> | commercialreservationorders | Nej | 
> | utväxla | Nej |
> | reservationorders | Nej | 
> | reservera | Nej | 
> | resources | Nej | 
> | validatereservationorder | Nej | 

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Nej |
> | edgenodes | Nej
> | filer | Nej | 
> | profiler/slut punkter | Nej | 

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | certificateorders | Nej | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | funktioner | Nej | 
> | domän namn | Ja | Nej |
> | quotas | Nej | 
> | resourcetypes | Nej |
> | validatesubscriptionmoveavailability | Nej | 
> | virtualmachines | Nej 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | Nej | 

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | funktioner | Nej | 
> | expressroutecrossconnections | Nej | 
> | expressroutecrossconnections/peering | Nej | 
> | gatewaysupporteddevices | Nej | 
> | networksecuritygroups | Nej |
> | quotas | Nej |
> | reservedips | Nej | 
> | virtualnetworks | Nej | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | disk | Nej | 
> | images | Nej | 
> | osimages | Nej | 
> | osplatformimages | Nej | 
> | publicimages | Nej | 
> | quotas | Nej | 
> | storageaccounts | Ja |  
> | vmimages | Nej |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | operations | Nej | 

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 
> | Cognitive Search | Stöds med manuella steg.<br/><br/> Läs om hur du [flyttar din Azure kognitiv sökning-tjänst till en annan region](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | ratecard | Nej | 
> | usageaggregates | Nej | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | availabilitysets | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta tillgänglighets uppsättningar. | 
> | diskaccesses | Nej |
> | diskencryptionsets | Nej | 
> | disk | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta virtuella Azure-datorer och relaterade diskar. | 
> | gallerier | Nej | 
> | gallerier/bilder | Nej | 
> | gallerier/avbildningar/versioner | Nej | 
> | hostgroups | Nej | 
> | hostgroups/värdar | Nej | 
> | images | Nej | 
> | proximityplacementgroups | Nej | 
> | restorepointcollections | Nej | 
> | sharedvmimages | Nej | 
> | sharedvmimages/versioner | Nej | 
> | snapshots | Nej | 
> | sshpublickeys | Nej |
> | virtualmachines | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta virtuella Azure-datorer. | 
> | virtualmachines/tillägg | Nej | 
> | virtualmachinescalesets | Nej | 

## <a name="microsoftconsumption"></a>Microsoft. förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | aggregatedcost | Nej | 
> | balanserar | Nej | 
> | budget | Nej | 
> | utgifts | Nej | 
> | costtags | Nej | 
> | krediter | Nej | 
> | händelser | Nej | 
> | prognoser | Nej | 
> | samtliga | Nej | 
> | marknads platser | Nej | 
> | pricesheets | Nej | 
> | läkemedle | Nej | 
> | reservationdetails | Nej | 
> | reservationrecommendationdetails | Nej | 
> | reservationrecommendations | Nej | 
> | reservationsummaries | Nej | 
> | reservationtransactions | Nej | 
> | tags | Nej | 
> | klienter | Nej | 
> | begreppen | Nej | 
> | usagedetails | Nej | 


## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containergroups | Nej | 
> | serviceassociationlinks | Nej |


## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | register | Nej |  
> | register/agentpools | Nej | 
> | register/buildtasks | Nej |  
> | register/replikeringar | Nej | 
> | register/uppgifter | Nej |  
> | register/Webhooks | Nej | 

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containerservices | Nej |
> | managedclusters | Nej | 
> | openshiftmanagedclusters | Nej | 

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program | Nej | 

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | aviseringar | Nej | 
> | billingaccounts | Nej | 
> | budget | Nej | 
> | cloudconnectors | Nej | 
> | anslutningar | Nej | 
> | enheten | Nej | 
> | enheter | Nej | 
> | enrollmentaccounts | Nej | 
> | exporteras | Nej | 
> | externalbillingaccounts | Nej | 
> | forecast | Nej | 
> | DocumentDB | Nej | 
> | registrera | Nej | 
> | reportconfigs | Nej | 
> | rapporter | Nej | 
> | inställningar | Nej | 
> | showbackrules | Nej | 
> | vyer | Nej | 

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | nav | Nej |  

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | autentiseringsbegäran | Nej | 

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | typer | Nej |
> | resourceproviders | Nej | 

## <a name="microsoftdatabox"></a>Microsoft. data-

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utskrifts | Nej | 

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | availableskus | Nej |
> | databoxedgedevices | Nej | 

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbetsytor | Nej | 

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kataloger | Nej | 
> | datacatalogs | Nej | 

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | connectionmanagers | Nej | 

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | distributionspaket | Nej | 
> | utgå | Nej | 

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datafactories | Nej | 
> | fabriker | Nej |  

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datalakeaccounts | Nej | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Nej | 
> | tjänster/projekt | Nej | 
> | lots | Nej | 

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | ---------- |
> | backupvaults | Nej | 

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | brygghuvudservrar | Du kan använda en skrivskyddad replik i flera regioner för att flytta en befintlig server. [Läs mer](../../postgresql/howto-move-regions-portal.md).<br/><br/> Om tjänsten är etablerad med Geo-redundant lagring, kan du använda geo-återställning för att återställa i andra regioner. [Läs mer](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | brygghuvudservrar | Du kan använda en skrivskyddad replik i flera regioner för att flytta en befintlig server. [Läs mer](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | servergroups | Nej | 
> | brygghuvudservrar | Du kan använda en skrivskyddad replik i flera regioner för att flytta en befintlig server. [Lär dig mer](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Nej | 

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | artifactsources | Nej | 
> | distributioner | Nej |  
> | servicetopologies | Nej | 
> | servicetopologies/tjänster | Nej |  
> | servicetopologies/tjänster/serviceunits | Nej | 
> | steg | Nej | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | applicationgroups | Nej | 
> | arbetsytor | Nej | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | elasticpools | Nej. Resursen är inte exponerad.
> | elasticpools / iothubtenants | Nej. Resursen är inte exponerad.
> | iothubs | Ja. [Läs mer](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Nej | 

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domänkontrollanter | Nej | 


## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domänkontrollanter | Nej | 
> | AKS-kluster | Nej<br/><br/> [Läs mer](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) om att flytta till en annan region.

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | labcenters | Nej | 
> | Laboration | Nej | 
> | labb/miljöer | Nej |  
> | labb/servicerunners | Nej | 
> | labb/virtualmachines | Nej |  
> | scheman | Nej |  

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Ja, genom att återskapa resurser i ny region. [Läs mer](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | databaseaccounts | Nej | 
> | databaseaccounts | Nej | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domäner | Nej | 
> | generatessorequest | Nej | 
> | topleveldomains | Nej | 
> | validatedomainregistrationinformation | Nej |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Nej |  

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domäner | Nej | 
> | eventsubscriptions | Nej |
> | extensiontopics | Nej | 
> | partnernamespaces | Nej | 
> | partnerregistrations | Nej | 
> | partnertopics | Nej | 
> | systemtopics | Nej | 
> | avsnitt | Nej | 
> | topictypes | Nej | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Nej |  
> | namn områden | Ja (med mall)<br/><br/> [Flytta en Event Hub-namnrymd till en annan region](../../event-hubs/move-across-regions.md) | 
> | sku | Nej |  

## <a name="microsoftexperimentation"></a>Microsoft. experimentering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | experimentworkspaces | Nej | 

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | namn områden | Nej | 

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | featureproviders | Nej | 
> | funktioner | Nej | 
> | finansiär | Nej | 
> | subscriptionfeatureregistrations | Nej | 

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | automanagedaccounts | Nej | 
> | automanagedvmconfigurationprofiles | Nej | 
> | guestconfigurationassignments | Nej | 
> | IntelliPoint | Nej | 
> | softwareupdateprofile | Nej | 
> | softwareupdates | Nej | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hanainstances | Nej | 
> | sapmonitors | Nej |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | dedicatedhsms | Nej | 


## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Nej | 

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Nej |  

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | faxar | Nej | 
> | datorer/tillägg | Nej |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datamanagers |  Nej | 

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | devices | Nej | 
> | vnfs | Nej | 

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | delarna | Nej | 
> | networkscopes | Nej | 

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utskrifts |  Nej | 

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej. [Läs mer](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | actiongroups |  Nej | 
> | activitylogalerts | Nej | 
> | alertrules |  Nej | 
> | autoscalesettings |  Nej | 
> | baslinje | Nej |
> | delarna |  Nej |  
> | datacollectionrules | Nej | 
> | diagnosticsettings | Nej | 
> | diagnosticsettingscategories | Nej | 
> | eventcategories | Nej | 
> | eventtypes | Nej | 
> | extendeddiagnosticsettings | Nej | |
> | guestdiagnosticsettings | Nej | 
> | listmigrationdate | Nej | 
> | logdefinitions | Nej | 
> | logprofiles | Nej | 
> | loggar | Nej | Nej |
> | metricalerts | Nej | 
> | metricbaselines | Nej | 
> | metricbatch | Nej | 
> | metricdefinitions | Nej | 
> | metricnamespaces | Nej | 
> | metrics | Nej | 
> | migratealertrules | Nej |
> | migratetonewpricingmodel | Nej | 
> | Mina arbets böcker | Nej |
> | notificationgroups | Nej | 
> | privatelinkscopes | Nej |
> | rollbacktolegacypricingmodel | Nej |
> | scheduledqueryrules |  Nej | 
> | topologi | Nej |
> | transaktioner | Nej |
> | vminsightsonboardingstatuses | Nej |
> | webbtester |  Nej | 
> | webbtester/gettestresultfile | Nej |
> | arbetsböcker |  Nej |  
> | workbooktemplates | Nej |


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | apptemplates | Nej | 
> | iotapps | Nej | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> |  iothub |  Ja (klon hubb) <br/><br/> [Klona en IoT-hubb till en annan region](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region |
> | ------------- | ----------- | 
> | Rita | Nej | 

## <a name="microsoftkeyvault"></a>Microsoft. nyckel valv

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | deletedvaults | Nej |
> | hsmpools | Nej | 
> | managedhsms | Nej |
> | valv |  Nej | 

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | connectedclusters | Nej | 
> | registeredsubscriptions | Nej | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | Nej | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster |  Nej |  

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | labaccounts | Nej | 
> | användare | Nej | 

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftlocationservices"></a>Microsoft. filen LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej, det är en global tjänst.

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hostingenvironments | Nej | 
> | integrationaccounts |  Nej |  
> | integrationserviceenvironments | Nej | 
> | integrationserviceenvironments/managedapis | Nej |
> | isolatedenvironments | Nej | 
> | arbetsflöden |  Nej |  

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | commitmentplans |  Nej | 
> | WebServices |  Nej | 
> | arbetsytor |  Nej | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | operationalizationclusters |  Nej | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 
> | teamaccounts | Nej | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbetsytor | Nej | 

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | configurationassignments | Ja. [Läs mer](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Ja. [Läs mer](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | uppdateringar | Nej | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | identiteter | Nej | 
> | userassignedidentities | Nej | 

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | managednetworks | Nej | 
> | managednetworks / managednetworkgroups | Nej |
> | managednetworks / managednetworkpeeringpolicies | Nej | 
> | avisering | Nej | 

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | Nej | 
> | registrationassignments | Nej |
> | registrationdefinitions | Nej | 

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | getentities | Nej | 
> | managementgroups | Nej | 
> | managementgroups/inställningar | Nej | 
> | resources | Nej | 
> | starttenantbackfill | Nej | 
> | tenantbackfillstatus | Nej | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton |  Nej, Azure Maps är en Geospatial tjänst. 
> | konton/privateatlases | Nej

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | budgivning | Nej | 
> | offertypes | Nej | 
> | privategalleryitems | Nej | 
> | privatestoreclient | Nej | 
> | privatestores | Nej | 
> | läkemedle | Nej | 
> | Utgivare | Nej | 
> | registrera | Nej | 

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | classicdevservices | Nej | 

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | villkor | Nej | 
> | offertypes | Nej | 

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Media Services |  Nej | 
> | Media Services/liveevents |  Nej | 
> | Media Services/strömnings slut punkter |  Nej | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | appclusters | Nej | 

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | assessmentprojects | Nej | 
> | migrateprojects | Nej | 
> | movecollections | Nej
> | samarbetsprojekt | Nej | 

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nej | 
> | objectunderstandingaccounts | Nej | 
> | remoterenderingaccounts | Nej | 
> | spatialanchorsaccounts | Nej | 

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | netappaccounts | Nej | 
> | netappaccounts / capacitypools | Nej | 
> | netappaccounts/capacitypools/Volumes | Nej | 
> | netappaccounts/capacitypools/Volumes/mounttargets | Nej | 
> | netappaccounts/capacitypools/volym/ögonblicks bilder | Nej | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | applicationgateways | Nej |
> | applicationgatewaywebapplicationfirewallpolicies | Nej | 
> | applicationsecuritygroups |  Nej |  
> | azurefirewalls |  Nej |  
> | bastionhosts | Nej | 
> | bgpservicecommunities | Nej |
> | anslutning |  Nej | 
> | ddoscustompolicies |  Nej | 
> | ddosprotectionplans | Nej | 
> | dnszones |  Nej | 
> | expressroutecircuits | Nej | 
> | expressroutegateways | Nej | 
> | expressrouteserviceproviders | Nej | 
> | firewallpolicies | Nej |
> | frontdoors | Nej | 
> | ipallocations | Nej |
> | ipgroups | Nej |
> | belastningsutjämnare | Ja <br/><br/> Använd [Azure Resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -arbetsbelastning för att flytta interna och externa belastningsutjämnare. |
> | localnetworkgateways |  Nej | 
> | natgateways |  Nej | 
> | networkexperimentprofiles | Nej |
> | networkintentpolicies |  Nej | 
> | NetworkInterfaces | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta nätverkskort. | 
> | networkprofiles | Nej | 
> | networksecuritygroups | Ja <br/><br/> Använd [Azure resurs förflyttning](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta nätverks säkerhets grupper (NGSs). | 
> | networkwatchers |  Nej |  
> | networkwatchers / connectionmonitors |  Nej | 
> | networkwatchers / flowlogs |  Nej | 
> | networkwatchers / pingmeshes |  Nej | 
> | p2svpngateways | Nej | 
> | privatednszones |  Nej |  
> | privatednszones / virtualnetworklinks | Nej |> | privatednszonesinternal | Nej |
> | privateendpointredirectmaps | Nej |
> | privateendpoints | Nej | 
> | privatelinkservices | Nej | 
> | publicipaddresses | Ja<br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta offentliga IP-adresser. |
> | publicipprefixes | Nej | 
> | routefilters | Nej | 
> | routetables |  Nej | 
> | securitypartnerproviders | Nej |
> | serviceendpointpolicies |  Nej | 
> | trafficmanagergeographichierarchies | Nej | 
> | trafficmanagerprofiles |  Nej | 
> | trafficmanagerusermetricskeys | Nej |
> | virtualhubs | Nej | 
> | virtualnetworkgateways |  Nej |  
> | virtualnetworks |  Nej | 
> | virtualnetworktaps | Nej | 
> | virtualwans | Nej | 
> | vpngateways (virtuellt WAN) | Nej | 
> | vpnsites (virtuellt WAN) | Nej | 
> | vpnsites (virtuellt WAN) | Nej |


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Nej | 
> | namnrymder/notificationhubs |  Nej |  

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | osnamespaces | Nej | 

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | hypervsites | Nej | 
> | importsites | Nej | 
> | serversites | Nej | 
> | vmwaresites | Nej | 

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Nej | 
> | deletedworkspaces | Nej | 
> | linktargets | Nej | 
> | storageinsightconfigs | Nej |
> | arbetsytor | Nej |



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | managementassociations | Nej |
> | managementconfigurations |  Nej | 
> | lösningar | Nej |
> | vyer |  Nej | 

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | legacypeerings | Nej | 
> | peerasns | Nej | 
> | peeringlocations | Nej | 
> | peerings | Nej | 
> | peeringservicecountries | Nej | 
> | peeringservicelocations | Nej | 
> | peeringserviceproviders | Nej | 
> | peeringservices | Nej | 

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | policyevents | Nej | 
> | policystates | Nej | 
> | policytrackedresources | Nej | 
> | reparationer | Nej | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> |  -konsoler | Nej |
> | instrumentpaneler | Nej | 
> | usersettings | Nej | 


## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | workspacecollections |  Nej | 

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kapaciteter |  Nej | 

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | availableaccounts | Nej | 
> | providerregistrations | Nej | 
> | distributioner | Nej | 

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | arbetsytor | Nej | 

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | replicationeligibilityresults | Nej |
> | valv | Nej.<br/><br/> Det finns inte stöd för att flytta Recovery Services valv för Azure Backup i Azure-regioner.<br/><br/> I Recovery Services valv för Azure Site Recovery kan du [inaktivera och återskapa valvet](../../site-recovery/move-vaults-across-regions.md) i mål regionen. | 

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | openshiftclusters | Nej | 

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Nej | 

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | skickar |  Nej |  
> | resourcechangedetails | Nej | 
> | resourcechanges | Nej | 
> | resources | Nej | 
> | resourceshistory | Nej | 
> | subscriptionsstatus | Nej | 

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | childresources | Nej | 
> | emergingissues | Nej | 
> | händelser | Nej | 
> | metadata | Nej | 
> | meddelanden | Nej | 

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region |
> | ------------- | ----------- |
> | deploymentScripts |  Ja<br/><br/>[Flytta Microsoft. Resources-resurser till ny region](microsoft-resources-move-regions.md) |
> | templateSpecs |  Ja<br/><br/>[Flytta Microsoft. Resources-resurser till ny region](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program |  Nej | 
> | saasresources | Nej | 


## <a name="microsoftsearch"></a>Microsoft. search

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | resourcehealthmetadata | Nej |
> | searchservices |  Nej | 


## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | Nej | 
> | advancedthreatprotectionsettings | Nej | 
> | aviseringar | Nej | 
> | allowedconnections | Nej | 
> | applicationwhitelistings | Nej | 
> | assessmentmetadata | Nej | 
> | utvärderingar | Nej | 
> | autodismissalertsrules | Nej | 
> | automatiseringar | Nej | 
> | autoprovisioningsettings | Nej |
> | complianceresults | Nej | 
> | godkännanden | Nej | 
> | datacollectionagents | Nej | 
> | devicesecuritygroups | Nej | 
> | discoveredsecuritysolutions | Nej | 
> | externalsecuritysolutions | Nej | 
> | informationprotectionpolicies | Nej | 
> | iotsecuritysolutions | Nej | 
> | iotsecuritysolutions / analyticsmodels | Nej | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nej | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nej | 
> | jitnetworkaccesspolicies | Nej | 
> | policies | Nej | 
> | prissättningar | Nej | 
> | regulatorycompliancestandards | Nej | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | Nej | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nej | 
> | securitycontacts | Nej | 
> | securitysolutions | Nej | 
> | securitysolutionsreferencedata | Nej | 
> | securitystatuses | Nej | 
> | securitystatusessummaries | Nej | 
> | servervulnerabilityassessments | Nej | 
> | inställningar | Nej | 
> | underbedömningar | Nej |
> | uppgifter | Nej | 
> | topologier | Nej | 
> | workspacesettings | Nej | 

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | agg regeringar | Nej | 
> | alertrules | Nej | 
> | alertruletemplates | Nej | 
> | automationrules | Nej |
> | fall | Nej | 
> | dataconnectors | Nej | 
> | poster | Nej | 
> | entityqueries | Nej |
> | incidenter | Nej | 
> | officeconsents | Nej | 
> | inställningar | Nej | 
> | threatintelligence | Nej | 

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | consoleservices | Nej | 

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | gatewayer | Nej | 
> | artikelnoder | Nej | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Nej | 
> | premiummessagingregions | Nej | 
> | sku | Nej | 

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program | Nej | 
> | kluster |  Nej |  
> | containergroups | Nej | 
> | containergroupsets | Nej | 
> | edgeclusters | Nej | 
> | managedclusters | Nej |
> | nätet | Nej | 
> | secretstores | Nej | 
> | volumes | Nej | 

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program |  Nej | 
> | containergroups | Nej | 
> | gatewayer |  Nej | 
> | nätet |  Nej | 
> | secrets |  Nej | 
> | volumes |  Nej |  

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | distributioner | Nej | 

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | SignalR |  Nej |  

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | hybridusebenefits | Nej | 

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | appliancedefinitions | Nej | 
> | redskap | Nej | 
> | jitrequests | Nej | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | instancepools | Nej | 
> | platser | Nej |
> | managedinstances | Ja <br/><br/> [Lär dig mer](../../azure-sql/database/move-resources-across-regions.md) om att flytta hanterade instanser i flera regioner. | 
> | managedinstances/databaser | Ja | 
> | brygghuvudservrar | Ja | 
> | servrar/databaser | Ja <br/><br/> [Lär dig mer](../../azure-sql/database/move-resources-across-regions.md) om att flytta databaser över flera regioner.<br/><br/> [Lär dig mer](../../resource-mover/tutorial-move-region-sql.md) om hur du använder Azure Resource-arbetskraft för att flytta Azure SQL-databaser.  | 
> | servrar/elasticpools | Ja <br/><br/> [Lär dig mer](../../azure-sql/database/move-resources-across-regions.md) om att flytta elastiska pooler över flera regioner.<br/><br/> [Läs mer](../../resource-mover/tutorial-move-region-sql.md) om hur du använder Azure Resource-arbetskraft för att flytta elastiska Azure SQL-pooler.  | 
> | virtualclusters | Ja | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Nej |  
> | sqlvirtualmachines |  Nej |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storageaccounts | Ja<br/><br/> [Flytta ett Azure Storage-konto till en annan region](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cacheminnen | Nej | 

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices |  Nej | 

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices | Nej | 

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices | Nej | 

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hantera | Nej | 

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Nej |
> | streamingjobs |  Nej |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utrymmen | Nej | 
> | pipe | Nej | 

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | prenumerationer | Nej | 

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | services | Nej | 
> | supporttickets | Nej | 

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | arbetsytor | Nej | 
> | arbets ytor/bigdatapools | Nej | 
> | arbets ytor/sqlpools | Nej | 


## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utrymmen |  Nej | 
> | miljöer/eventsources |  Nej |  
> | miljöer/referencedatasets |  Nej | 

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Auktoriseringshanteraren | Nej | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | imagetemplates | Nej | 

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konto |  Nej | 
> | konto/tillägg |  Nej | 
> | konto/projekt |  Nej | 

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | arczones | Nej | 
> | resourcepools | Nej | 
> | vCenter | Nej | 
> | virtualmachines | Nej | 
> | virtualmachinetemplates | Nej | 
> | virtualnetworks | Nej | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Nej | 
> | dedicatedcloudservices | Nej | 
> | virtualmachines | Nej | 

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | devices | Nej | 
> | vnfs | Nej | 

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | konton | Nej | 
> | utgå | Nej | 
> | registeredsubscriptions | Nej |


## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | availablestacks | Nej | 
> | billingmeters | Nej | 
> | certifikat | Nej | 
> | connectiongateways |  Nej |  
> | anslutning |  Nej |  
> | customapis |  Nej | 
> | deletedsites | Nej | 
> | deploymentlocations | Nej | 
> | regioner | Nej | 
> | hostingenvironments | Nej | 
> | kubeenvironments | Nej | 
> | publishingusers | Nej |
> | rekommendationer | Nej | 
> | resourcehealthmetadata | Nej | 
> | körningar | Nej | 
> | Server grupper | Nej |  
> | Server grupper/eventgridfilters | N
> | webbplatser |  Nej | 
> | platser/premieraddons |  Nej |  
> | platser/platser |  Nej |  
> | sourcecontrols | Nej |
> | staticsites | Nej | 

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | multipleactivationkeys | Nej |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | deviceservices | Nej | 

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | arbets belastningar | Nej | 

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | delarna | Nej |
> | componentssummary | Nej | 
> | monitorinstances | Nej | 
> | monitorinstancessummary | Nej | 
> | Övervakare | Nej | 
## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flytt åtgärden.

## <a name="next-steps"></a>Nästa steg

[Läs mer](../../resource-mover/overview.md) om resurs förflyttnings tjänsten.

