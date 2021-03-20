---
title: Stöd för att flytta Azure-resurser mellan regioner
description: Visar en lista över de Azure-resurs typer som kan flyttas mellan Azure-regioner
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 18d4d84462d528b718d784ff6a16ecf990ed0d20
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100094023"
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
> - [Microsoft. avdelningens kontroll](#microsoftpurview)
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
> | domainservices | Inga | 


## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | diagnosticsettings | Inga |
> | diagnosticsettingscategories | Inga |
> | privatelinkforazuread | Inga |
> | klienter |  Inga |

## <a name="microsoftaddons"></a>Utforskaren. Tillägg

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | supportproviders | Inga |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | aadsupportcases | Inga |
> | addsservices | Inga | 
> | aktörer | Inga | 
> | anonymousapiusers | Inga |
> | konfiguration | Inga | 
> | loggar | Inga | 
> | rapporter | Inga | 
> | servicehealthmetrics | Inga | 
> | services | Inga | 

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | konfigurationer | Inga | 
> | generaterecommendations | Inga |
> | metadata | Inga |
> | rekommendationer | Inga |
> | utelämningar | Inga | 

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | actionrules | Inga | 
> | aviseringar | Inga | 
> | alertslist | Inga | 
> | alertsmetadata | Inga | 
> | alertssummary | Inga | 
> | alertssummarylist | Inga | 
> | smartdetectoralertrules | Inga | 
> | smartgroups | Inga | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | brygghuvudservrar | Inga |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | reportfeedback | Inga |
> | tjänst |  Ja (med mall) <br/><br/> [Flytta API Management över flera regioner](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | configurationstores | Inga | 
> | configurationstores / eventgridfilters | Inga |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | upphängning | Inga | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | apiapps | Ja (med mall)<br/><br/> [Flytta en App Service app till en annan region](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Inga | 
> | gatewayer | Inga | 

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | attestationproviders | Inga | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | classicadministrators | Inga | 
> | dataaliases | Inga | 
> | denyassignments | Inga | 
> | elevateaccess | Inga | 
> | findorphanroleassignments | Inga | 
> | hålls | Inga | 
> | behörigheter | Inga | 
> | policyassignments | Inga | 
> | policydefinitions | Inga | 
> | policysetdefinitions | Inga | 
> | privatelinkassociations | Inga | 
> | resourcemanagementprivatelinks | Inga | 
> | RoleAssignments | Inga | 
> | roleassignmentsusagemetrics | Inga | 
> | roledefinitions | Inga | 

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | automationaccounts | Ja (med mall) <br/><br/> [Använda geo-replikering](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfigurationer | Inga | 
> | automationaccounts/Runbooks | Inga | 

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | Prenumeration |
> | ------------- | ----------- | 
> | privateclouds | Inga | 


## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | b2cdirectories | Inga | 
> | b2ctenants | Inga | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datacontrollers | Inga | 
> | hybriddatamanagers | Inga | 
> | postgresinstances | Inga | 
> | sqlinstances | Inga | 
> | sqlmanagedinstances | Inga |
> | sqlserverinstances | Inga | 
> | sqlserverregistrations | Inga |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cloudmanifestfiles | Inga |
> | registreringar | Inga | 

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | kluster | Inga | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | batchaccounts |  Batch-konton kan inte flyttas direkt från en region till en annan, men du kan använda en mall för att exportera en mall, ändra den och distribuera mallen till den nya regionen. <br/><br/> Lär dig mer om att [Flytta ett batch-konto över flera regioner](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft. fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | billingaccounts | Inga | 
> | billingperiods | Inga | 
> | billingpermissions | Inga | 
> | billingproperty | Inga | 
> | billingroleassignments | Inga | 
> | billingroledefinitions | Inga | 
> | enheten | Inga | 
> | enrollmentaccounts | Inga | 
> | fakturor | Inga | 
> | överlåtelse | Inga | 

## <a name="microsoftbingmaps"></a>Microsoft. Bingkartssökning

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | mapapis | Inga | 

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | biztalk | Inga | 

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | blockchainmembers | Inga <br/><br/> Blockchain-nätverket kan inte ha noder i olika regioner. 
> | cordamembers | Inga |
> | Övervakare | Inga | 

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | tokenservices | Inga |


## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | blueprintassignments | Inga | 
> | modeller | Inga |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | botservices | Inga | 

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Redis | Inga | 
> | redisenterprise | Inga | 

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | appliedreservations | Inga | 
> | calculateexchange | Inga | 
> | calculateprice | Inga | 
> | calculatepurchaseprice | Inga | 
> | kataloger | Inga | 
> | commercialreservationorders | Inga | 
> | utväxla | Inga |
> | reservationorders | Inga | 
> | reservera | Inga | 
> | resources | Inga | 
> | validatereservationorder | Inga | 

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Inga |
> | edgenodes | Inga
> | filer | Inga | 
> | profiler/slut punkter | Inga | 

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | certificateorders | Inga | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | funktioner | Inga | 
> | domän namn | Ja | Inga |
> | quotas | Inga | 
> | resourcetypes | Inga |
> | validatesubscriptionmoveavailability | Inga | 
> | virtualmachines | Inga 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | Inga | 

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | funktioner | Inga | 
> | expressroutecrossconnections | Inga | 
> | expressroutecrossconnections/peering | Inga | 
> | gatewaysupporteddevices | Inga | 
> | networksecuritygroups | Inga |
> | quotas | Inga |
> | reservedips | Inga | 
> | virtualnetworks | Inga | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | disk | Inga | 
> | images | Inga | 
> | osimages | Inga | 
> | osplatformimages | Inga | 
> | publicimages | Inga | 
> | quotas | Inga | 
> | storageaccounts | Ja |  
> | vmimages | Inga |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | operations | Inga | 

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 
> | Cognitive Search | Stöds med manuella steg.<br/><br/> Läs om hur du [flyttar din Azure kognitiv sökning-tjänst till en annan region](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | ratecard | Inga | 
> | usageaggregates | Inga | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | availabilitysets | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta tillgänglighets uppsättningar. | 
> | diskaccesses | Inga |
> | diskencryptionsets | Inga | 
> | disk | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta virtuella Azure-datorer och relaterade diskar. | 
> | gallerier | Inga | 
> | gallerier/bilder | Inga | 
> | gallerier/avbildningar/versioner | Inga | 
> | hostgroups | Inga | 
> | hostgroups/värdar | Inga | 
> | images | Inga | 
> | proximityplacementgroups | Inga | 
> | restorepointcollections | Inga | 
> | sharedvmimages | Inga | 
> | sharedvmimages/versioner | Inga | 
> | snapshots | Inga | 
> | sshpublickeys | Inga |
> | virtualmachines | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta virtuella Azure-datorer. | 
> | virtualmachines/tillägg | Inga | 
> | virtualmachinescalesets | Inga | 

## <a name="microsoftconsumption"></a>Microsoft. förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | aggregatedcost | Inga | 
> | balanserar | Inga | 
> | budget | Inga | 
> | utgifts | Inga | 
> | costtags | Inga | 
> | krediter | Inga | 
> | händelser | Inga | 
> | prognoser | Inga | 
> | samtliga | Inga | 
> | marknads platser | Inga | 
> | pricesheets | Inga | 
> | läkemedle | Inga | 
> | reservationdetails | Inga | 
> | reservationrecommendationdetails | Inga | 
> | reservationrecommendations | Inga | 
> | reservationsummaries | Inga | 
> | reservationtransactions | Inga | 
> | tags | Inga | 
> | klienter | Inga | 
> | begreppen | Inga | 
> | usagedetails | Inga | 


## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containergroups | Inga | 
> | serviceassociationlinks | Inga |


## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | register | Inga |  
> | register/agentpools | Inga | 
> | register/buildtasks | Inga |  
> | register/replikeringar | Inga | 
> | register/uppgifter | Inga |  
> | register/Webhooks | Inga | 

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containerservices | Inga |
> | managedclusters | Inga | 
> | openshiftmanagedclusters | Inga | 

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program | Inga | 

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | aviseringar | Inga | 
> | billingaccounts | Inga | 
> | budget | Inga | 
> | cloudconnectors | Inga | 
> | anslutningar | Inga | 
> | enheten | Inga | 
> | enheter | Inga | 
> | enrollmentaccounts | Inga | 
> | exporteras | Inga | 
> | externalbillingaccounts | Inga | 
> | forecast | Inga | 
> | DocumentDB | Inga | 
> | registrera | Inga | 
> | reportconfigs | Inga | 
> | rapporter | Inga | 
> | inställningar | Inga | 
> | showbackrules | Inga | 
> | vyer | Inga | 

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | nav | Inga |  

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | autentiseringsbegäran | Inga | 

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | typer | Inga |
> | resourceproviders | Inga | 

## <a name="microsoftdatabox"></a>Microsoft. data-

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utskrifts | Inga | 

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | availableskus | Inga |
> | databoxedgedevices | Inga | 

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbetsytor | Inga | 

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kataloger | Inga | 
> | datacatalogs | Inga | 

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | connectionmanagers | Inga | 

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | distributionspaket | Inga | 
> | utgå | Inga | 

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datafactories | Inga | 
> | fabriker | Inga |  

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datalakeaccounts | Inga | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Inga | 
> | tjänster/projekt | Inga | 
> | lots | Inga | 

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | ---------- |
> | backupvaults | Inga | 

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

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
> | servergroups | Inga | 
> | brygghuvudservrar | Du kan använda en skrivskyddad replik i flera regioner för att flytta en befintlig server. [Lär dig mer](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Inga | 

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | artifactsources | Inga | 
> | distributioner | Inga |  
> | servicetopologies | Inga | 
> | servicetopologies/tjänster | Inga |  
> | servicetopologies/tjänster/serviceunits | Inga | 
> | steg | Inga | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | applicationgroups | Inga | 
> | arbetsytor | Inga | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | elasticpools | Nej. Resursen är inte exponerad.
> | elasticpools / iothubtenants | Nej. Resursen är inte exponerad.
> | iothubs | Ja. [Läs mer](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Inga | 

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domänkontrollanter | Inga | 


## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domänkontrollanter | Inga | 
> | AKS-kluster | Inga<br/><br/> [Läs mer](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) om att flytta till en annan region.

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | labcenters | Inga | 
> | Laboration | Inga | 
> | labb/miljöer | Inga |  
> | labb/servicerunners | Inga | 
> | labb/virtualmachines | Inga |  
> | scheman | Inga |  

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Ja, genom att återskapa resurser i ny region. [Läs mer](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | databaseaccounts | Inga | 
> | databaseaccounts | Inga | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domäner | Inga | 
> | generatessorequest | Inga | 
> | topleveldomains | Inga | 
> | validatedomainregistrationinformation | Inga |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Inga |  

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domäner | Inga | 
> | eventsubscriptions | Inga |
> | extensiontopics | Inga | 
> | partnernamespaces | Inga | 
> | partnerregistrations | Inga | 
> | partnertopics | Inga | 
> | systemtopics | Inga | 
> | avsnitt | Inga | 
> | topictypes | Inga | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Inga |  
> | namn områden | Ja (med mall)<br/><br/> [Flytta en Event Hub-namnrymd till en annan region](../../event-hubs/move-across-regions.md) | 
> | sku | Inga |  

## <a name="microsoftexperimentation"></a>Microsoft. experimentering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | experimentworkspaces | Inga | 

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | namn områden | Inga | 

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | featureproviders | Inga | 
> | funktioner | Inga | 
> | finansiär | Inga | 
> | subscriptionfeatureregistrations | Inga | 

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | automanagedaccounts | Inga | 
> | automanagedvmconfigurationprofiles | Inga | 
> | guestconfigurationassignments | Inga | 
> | IntelliPoint | Inga | 
> | softwareupdateprofile | Inga | 
> | softwareupdates | Inga | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hanainstances | Inga | 
> | sapmonitors | Inga |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | dedicatedhsms | Inga | 


## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Inga | 

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Inga |  

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | faxar | Inga | 
> | datorer/tillägg | Inga |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datamanagers |  Inga | 

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | devices | Inga | 
> | vnfs | Inga | 

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | delarna | Inga | 
> | networkscopes | Inga | 

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utskrifts |  Inga | 

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej. [Läs mer](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | actiongroups |  Inga | 
> | activitylogalerts | Inga | 
> | alertrules |  Inga | 
> | autoscalesettings |  Inga | 
> | baslinje | Inga |
> | delarna |  Inga |  
> | datacollectionrules | Inga | 
> | diagnosticsettings | Inga | 
> | diagnosticsettingscategories | Inga | 
> | eventcategories | Inga | 
> | eventtypes | Inga | 
> | extendeddiagnosticsettings | Inga | |
> | guestdiagnosticsettings | Inga | 
> | listmigrationdate | Inga | 
> | logdefinitions | Inga | 
> | logprofiles | Inga | 
> | loggar | Inga | Inga |
> | metricalerts | Inga | 
> | metricbaselines | Inga | 
> | metricbatch | Inga | 
> | metricdefinitions | Inga | 
> | metricnamespaces | Inga | 
> | metrics | Inga | 
> | migratealertrules | Inga |
> | migratetonewpricingmodel | Inga | 
> | Mina arbets böcker | Inga |
> | notificationgroups | Inga | 
> | privatelinkscopes | Inga |
> | rollbacktolegacypricingmodel | Inga |
> | scheduledqueryrules |  Inga | 
> | topologi | Inga |
> | transaktioner | Inga |
> | vminsightsonboardingstatuses | Inga |
> | webbtester |  Inga | 
> | webbtester/gettestresultfile | Inga |
> | arbetsböcker |  Inga |  
> | workbooktemplates | Inga |


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | apptemplates | Inga | 
> | iotapps | Inga | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> |  iothub |  Ja (klon hubb) <br/><br/> [Klona en IoT-hubb till en annan region](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region |
> | ------------- | ----------- | 
> | Rita | Inga | 

## <a name="microsoftkeyvault"></a>Microsoft. nyckel valv

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | deletedvaults | Inga |
> | hsmpools | Inga | 
> | managedhsms | Inga |
> | valv |  Inga | 

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | connectedclusters | Inga | 
> | registeredsubscriptions | Inga | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | Inga | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster |  Inga |  

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | labaccounts | Inga | 
> | användare | Inga | 

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftlocationservices"></a>Microsoft. filen LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej, det är en global tjänst.

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hostingenvironments | Inga | 
> | integrationaccounts |  Inga |  
> | integrationserviceenvironments | Inga | 
> | integrationserviceenvironments/managedapis | Inga |
> | isolatedenvironments | Inga | 
> | arbetsflöden |  Inga |  

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | commitmentplans |  Inga | 
> | WebServices |  Inga | 
> | arbetsytor |  Inga | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | operationalizationclusters |  Inga | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 
> | teamaccounts | Inga | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbetsytor | Inga | 

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | configurationassignments | Ja. [Läs mer](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Ja. [Läs mer](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | uppdateringar | Inga | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | identiteter | Inga | 
> | userassignedidentities | Inga | 

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | managednetworks | Inga | 
> | managednetworks / managednetworkgroups | Inga |
> | managednetworks / managednetworkpeeringpolicies | Inga | 
> | avisering | Inga | 

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | Inga | 
> | registrationassignments | Inga |
> | registrationdefinitions | Inga | 

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | getentities | Inga | 
> | managementgroups | Inga | 
> | managementgroups/inställningar | Inga | 
> | resources | Inga | 
> | starttenantbackfill | Inga | 
> | tenantbackfillstatus | Inga | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton |  Nej, Azure Maps är en Geospatial tjänst. 
> | konton/privateatlases | Inga

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | budgivning | Inga | 
> | offertypes | Inga | 
> | privategalleryitems | Inga | 
> | privatestoreclient | Inga | 
> | privatestores | Inga | 
> | läkemedle | Inga | 
> | Utgivare | Inga | 
> | registrera | Inga | 

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | classicdevservices | Inga | 

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | villkor | Inga | 
> | offertypes | Inga | 

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Media Services |  Inga | 
> | Media Services/liveevents |  Inga | 
> | Media Services/strömnings slut punkter |  Inga | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | appclusters | Inga | 

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | assessmentprojects | Inga | 
> | migrateprojects | Inga | 
> | movecollections | Inga
> | samarbetsprojekt | Inga | 

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Inga | 
> | objectunderstandingaccounts | Inga | 
> | remoterenderingaccounts | Inga | 
> | spatialanchorsaccounts | Inga | 

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | netappaccounts | Inga | 
> | netappaccounts / capacitypools | Inga | 
> | netappaccounts/capacitypools/Volumes | Inga | 
> | netappaccounts/capacitypools/Volumes/mounttargets | Inga | 
> | netappaccounts/capacitypools/volym/ögonblicks bilder | Inga | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | applicationgateways | Inga |
> | applicationgatewaywebapplicationfirewallpolicies | Inga | 
> | applicationsecuritygroups |  Inga |  
> | azurefirewalls |  Inga |  
> | bastionhosts | Inga | 
> | bgpservicecommunities | Inga |
> | anslutning |  Inga | 
> | ddoscustompolicies |  Inga | 
> | ddosprotectionplans | Inga | 
> | dnszones |  Inga | 
> | expressroutecircuits | Inga | 
> | expressroutegateways | Inga | 
> | expressrouteserviceproviders | Inga | 
> | firewallpolicies | Inga |
> | frontdoors | Inga | 
> | ipallocations | Inga |
> | ipgroups | Inga |
> | belastningsutjämnare | Ja <br/><br/> Använd [Azure Resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -arbetsbelastning för att flytta interna och externa belastningsutjämnare. |
> | localnetworkgateways |  Inga | 
> | natgateways |  Inga | 
> | networkexperimentprofiles | Inga |
> | networkintentpolicies |  Inga | 
> | NetworkInterfaces | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta nätverkskort. | 
> | networkprofiles | Inga | 
> | networksecuritygroups | Ja <br/><br/> Använd [Azure resurs förflyttning](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta nätverks säkerhets grupper (NGSs). | 
> | networkwatchers |  Inga |  
> | networkwatchers / connectionmonitors |  Inga | 
> | networkwatchers / flowlogs |  Inga | 
> | networkwatchers / pingmeshes |  Inga | 
> | p2svpngateways | Inga | 
> | privatednszones |  Inga |  
> | privatednszones / virtualnetworklinks | Inga |> | privatednszonesinternal | Inga |
> | privateendpointredirectmaps | Inga |
> | privateendpoints | Inga | 
> | privatelinkservices | Inga | 
> | publicipaddresses | Ja<br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta offentliga IP-adresser. |
> | publicipprefixes | Inga | 
> | routefilters | Inga | 
> | routetables |  Inga | 
> | securitypartnerproviders | Inga |
> | serviceendpointpolicies |  Inga | 
> | trafficmanagergeographichierarchies | Inga | 
> | trafficmanagerprofiles |  Inga | 
> | trafficmanagerusermetricskeys | Inga |
> | virtualhubs | Inga | 
> | virtualnetworkgateways |  Inga |  
> | virtualnetworks |  Inga | 
> | virtualnetworktaps | Inga | 
> | virtualwans | Inga | 
> | vpngateways (virtuellt WAN) | Inga | 
> | vpnsites (virtuellt WAN) | Inga | 
> | vpnsites (virtuellt WAN) | Inga |


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Inga | 
> | namnrymder/notificationhubs |  Inga |  

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | osnamespaces | Inga | 

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | hypervsites | Inga | 
> | importsites | Inga | 
> | serversites | Inga | 
> | vmwaresites | Inga | 

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Inga | 
> | deletedworkspaces | Inga | 
> | linktargets | Inga | 
> | storageinsightconfigs | Inga |
> | arbetsytor | Inga |



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | managementassociations | Inga |
> | managementconfigurations |  Inga | 
> | lösningar | Inga |
> | vyer |  Inga | 

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | legacypeerings | Inga | 
> | peerasns | Inga | 
> | peeringlocations | Inga | 
> | peerings | Inga | 
> | peeringservicecountries | Inga | 
> | peeringservicelocations | Inga | 
> | peeringserviceproviders | Inga | 
> | peeringservices | Inga | 

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | policyevents | Inga | 
> | policystates | Inga | 
> | policytrackedresources | Inga | 
> | reparationer | Inga | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> |  -konsoler | Inga |
> | instrumentpaneler | Inga | 
> | usersettings | Inga | 


## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | workspacecollections |  Inga | 

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kapaciteter |  Inga | 

## <a name="microsoftpurview"></a>Microsoft. avdelningens kontroll

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | availableaccounts | Inga | 
> | providerregistrations | Inga | 
> | distributioner | Inga | 

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | arbetsytor | Inga | 

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | replicationeligibilityresults | Inga |
> | valv | Nej.<br/><br/> Det finns inte stöd för att flytta Recovery Services valv för Azure Backup i Azure-regioner.<br/><br/> I Recovery Services valv för Azure Site Recovery kan du [inaktivera och återskapa valvet](../../site-recovery/move-vaults-across-regions.md) i mål regionen. | 

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | openshiftclusters | Inga | 

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Inga | 

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | skickar |  Inga |  
> | resourcechangedetails | Inga | 
> | resourcechanges | Inga | 
> | resources | Inga | 
> | resourceshistory | Inga | 
> | subscriptionsstatus | Inga | 

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | childresources | Inga | 
> | emergingissues | Inga | 
> | händelser | Inga | 
> | metadata | Inga | 
> | meddelanden | Inga | 

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
> | program |  Inga | 
> | saasresources | Inga | 


## <a name="microsoftsearch"></a>Microsoft. search

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | resourcehealthmetadata | Inga |
> | searchservices |  Inga | 


## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | Inga | 
> | advancedthreatprotectionsettings | Inga | 
> | aviseringar | Inga | 
> | allowedconnections | Inga | 
> | applicationwhitelistings | Inga | 
> | assessmentmetadata | Inga | 
> | utvärderingar | Inga | 
> | autodismissalertsrules | Inga | 
> | automatiseringar | Inga | 
> | autoprovisioningsettings | Inga |
> | complianceresults | Inga | 
> | godkännanden | Inga | 
> | datacollectionagents | Inga | 
> | devicesecuritygroups | Inga | 
> | discoveredsecuritysolutions | Inga | 
> | externalsecuritysolutions | Inga | 
> | informationprotectionpolicies | Inga | 
> | iotsecuritysolutions | Inga | 
> | iotsecuritysolutions / analyticsmodels | Inga | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Inga | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Inga | 
> | jitnetworkaccesspolicies | Inga | 
> | policies | Inga | 
> | prissättningar | Inga | 
> | regulatorycompliancestandards | Inga | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | Inga | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Inga | 
> | securitycontacts | Inga | 
> | securitysolutions | Inga | 
> | securitysolutionsreferencedata | Inga | 
> | securitystatuses | Inga | 
> | securitystatusessummaries | Inga | 
> | servervulnerabilityassessments | Inga | 
> | inställningar | Inga | 
> | underbedömningar | Inga |
> | uppgifter | Inga | 
> | topologier | Inga | 
> | workspacesettings | Inga | 

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | agg regeringar | Inga | 
> | alertrules | Inga | 
> | alertruletemplates | Inga | 
> | automationrules | Inga |
> | fall | Inga | 
> | dataconnectors | Inga | 
> | poster | Inga | 
> | entityqueries | Inga |
> | incidenter | Inga | 
> | officeconsents | Inga | 
> | inställningar | Inga | 
> | threatintelligence | Inga | 

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | consoleservices | Inga | 

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | gatewayer | Inga | 
> | artikelnoder | Inga | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Inga | 
> | premiummessagingregions | Inga | 
> | sku | Inga | 

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program | Inga | 
> | kluster |  Inga |  
> | containergroups | Inga | 
> | containergroupsets | Inga | 
> | edgeclusters | Inga | 
> | managedclusters | Inga |
> | nätet | Inga | 
> | secretstores | Inga | 
> | volumes | Inga | 

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program |  Inga | 
> | containergroups | Inga | 
> | gatewayer |  Inga | 
> | nätet |  Inga | 
> | secrets |  Inga | 
> | volumes |  Inga |  

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | distributioner | Inga | 

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | SignalR |  Inga |  

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | hybridusebenefits | Inga | 

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | appliancedefinitions | Inga | 
> | redskap | Inga | 
> | jitrequests | Inga | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | instancepools | Inga | 
> | platser | Inga |
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
> | sqlvirtualmachinegroups |  Inga |  
> | sqlvirtualmachines |  Inga |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storageaccounts | Ja<br/><br/> [Flytta ett Azure Storage-konto till en annan region](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cacheminnen | Inga | 

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices |  Inga | 

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices | Inga | 

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices | Inga | 

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hantera | Inga | 

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Inga |
> | streamingjobs |  Inga |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utrymmen | Inga | 
> | pipe | Inga | 

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | prenumerationer | Inga | 

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | services | Inga | 
> | supporttickets | Inga | 

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | arbetsytor | Inga | 
> | arbets ytor/bigdatapools | Inga | 
> | arbets ytor/sqlpools | Inga | 


## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utrymmen |  Inga | 
> | miljöer/eventsources |  Inga |  
> | miljöer/referencedatasets |  Inga | 

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Auktoriseringshanteraren | Inga | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | imagetemplates | Inga | 

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konto |  Inga | 
> | konto/tillägg |  Inga | 
> | konto/projekt |  Inga | 

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | arczones | Inga | 
> | resourcepools | Inga | 
> | vCenter | Inga | 
> | virtualmachines | Inga | 
> | virtualmachinetemplates | Inga | 
> | virtualnetworks | Inga | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Inga | 
> | dedicatedcloudservices | Inga | 
> | virtualmachines | Inga | 

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | devices | Inga | 
> | vnfs | Inga | 

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | konton | Inga | 
> | utgå | Inga | 
> | registeredsubscriptions | Inga |


## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | availablestacks | Inga | 
> | billingmeters | Inga | 
> | certifikat | Inga | 
> | connectiongateways |  Inga |  
> | anslutning |  Inga |  
> | customapis |  Inga | 
> | deletedsites | Inga | 
> | deploymentlocations | Inga | 
> | regioner | Inga | 
> | hostingenvironments | Inga | 
> | kubeenvironments | Inga | 
> | publishingusers | Inga |
> | rekommendationer | Inga | 
> | resourcehealthmetadata | Inga | 
> | körningar | Inga | 
> | Server grupper | Inga |  
> | Server grupper/eventgridfilters | N
> | webbplatser |  Inga | 
> | platser/premieraddons |  Inga |  
> | platser/platser |  Inga |  
> | sourcecontrols | Inga |
> | staticsites | Inga | 

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | multipleactivationkeys | Inga |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | deviceservices | Inga | 

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | arbets belastningar | Inga | 

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | delarna | Inga |
> | componentssummary | Inga | 
> | monitorinstances | Inga | 
> | monitorinstancessummary | Inga | 
> | Övervakare | Inga | 
## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flytt åtgärden.

## <a name="next-steps"></a>Nästa steg

[Läs mer](../../resource-mover/overview.md) om resurs förflyttnings tjänsten.

