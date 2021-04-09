---
title: Flytta åtgärds stöd efter resurs typ
description: Visar en lista över de Azure-resurs typer som kan flyttas till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 3a15ddc62c19d74871de33003d46ec2fc5437f51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584694"
---
# <a name="move-operation-support-for-resources"></a>Åtgärds stöd för flytt av resurser

Den här artikeln visar om en Azure-resurs har stöd för flytt åtgärden. Den innehåller också information om särskilda villkor att tänka på när du flyttar en resurs.

> [!IMPORTANT]
> I de flesta fall kan inte en underordnad resurs flyttas oberoende av den överordnade resursen. Underordnade resurser har en resurs typ i formatet `<resource-provider-namespace>/<parent-resource>/<child-resource>` . Till exempel `Microsoft.ServiceBus/namespaces/queues` är en underordnad resurs till `Microsoft.ServiceBus/namespaces` . När du flyttar den överordnade resursen flyttas den underordnade resursen automatiskt till den. Om du inte ser någon underordnad resurs i den här artikeln kan du anta att den flyttas med den överordnade resursen. Om den överordnade resursen inte stöder flytta går det inte att flytta den underordnade resursen.

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
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domainservices | Inga | Inga |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Inga | Inga |
> | diagnosticsettingscategories | Inga | Inga |
> | privatelinkforazuread | Ja | Ja |
> | klienter | Ja | Ja |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | supportproviders | Inga | Inga |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Inga | Inga |
> | addsservices | Inga | Inga |
> | aktörer | Inga | Inga |
> | anonymousapiusers | Inga | Inga |
> | konfiguration | Inga | Inga |
> | loggar | Inga | Inga |
> | rapporter | Inga | Inga |
> | servicehealthmetrics | Inga | Inga |
> | services | Inga | Inga |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konfigurationer | Inga | Inga |
> | generaterecommendations | Inga | Inga |
> | metadata | Inga | Inga |
> | rekommendationer | Inga | Inga |
> | utelämningar | Inga | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | actionrules | Ja | Ja |
> | aviseringar | Inga | Inga |
> | alertslist | Inga | Inga |
> | alertsmetadata | Inga | Inga |
> | alertssummary | Inga | Inga |
> | alertssummarylist | Inga | Inga |
> | smartdetectoralertrules | Ja | Ja |
> | smartgroups | Inga | Inga |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | brygghuvudservrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!IMPORTANT]
> Det går inte att flytta en API Management tjänst som är inställd på förbruknings-SKU: n.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | reportfeedback | Inga | Inga |
> | tjänst | Ja | Ja |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ja | Ja |
> | configurationstores / eventgridfilters | Inga | Inga |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | upphängning | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | apiapps | Inga | Inga |
> | appidentities | Inga | Inga |
> | gatewayer | Inga | Inga |

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Ja | Ja |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | classicadministrators | Inga | Inga |
> | dataaliases | Inga | Inga |
> | denyassignments | Inga | Inga |
> | elevateaccess | Inga | Inga |
> | findorphanroleassignments | Inga | Inga |
> | hålls | Inga | Inga |
> | behörigheter | Inga | Inga |
> | policyassignments | Inga | Inga |
> | policydefinitions | Inga | Inga |
> | policysetdefinitions | Inga | Inga |
> | privatelinkassociations | Inga | Inga |
> | resourcemanagementprivatelinks | Inga | Inga |
> | RoleAssignments | Inga | Inga |
> | roleassignmentsusagemetrics | Inga | Inga |
> | roledefinitions | Inga | Inga |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!IMPORTANT]
> Runbooks måste finnas i samma resurs grupp som Automation-kontot.
>
> Mer information finns i [flytta ditt Azure Automation-konto till en annan prenumeration](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ja | Ja |
> | automationaccounts/konfigurationer | Ja | Ja |
> | automationaccounts/Runbooks | Ja | Ja |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | privateclouds | Ja | Ja |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ja | Ja |
> | b2ctenants | Inga | Inga |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Inga | Inga |
> | hybriddatamanagers | Inga | Inga |
> | postgresinstances | Inga | Inga |
> | sqlinstances | Inga | Inga |
> | sqlmanagedinstances | Inga | Inga |
> | sqlserverinstances | Inga | Inga |
> | sqlserverregistrations | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Inga | Inga |
> | registreringar | Ja | Ja |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Inga | Inga |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft. fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Inga | Inga |
> | billingperiods | Inga | Inga |
> | billingpermissions | Inga | Inga |
> | billingproperty | Inga | Inga |
> | billingroleassignments | Inga | Inga |
> | billingroledefinitions | Inga | Inga |
> | enheten | Inga | Inga |
> | enrollmentaccounts | Inga | Inga |
> | fakturor | Inga | Inga |
> | överlåtelse | Inga | Inga |

## <a name="microsoftbingmaps"></a>Microsoft. Bingkartssökning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | mapapis | Inga | Inga |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | biztalk | Inga | Inga |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Inga | Inga |
> | cordamembers | Inga | Inga |
> | Övervakare | Inga | Inga |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | tokenservices | Inga | Inga |

## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Inga | Inga |
> | modeller | Inga | Inga |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | botservices | Ja | Ja |

## <a name="microsoftcache"></a>Microsoft. cache

> [!IMPORTANT]
> Om Azure cache för Redis-instansen har kon figurer ATS med ett virtuellt nätverk kan instansen inte flyttas till en annan prenumeration. Se [begränsningar för nätverks flytt](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Redis | Ja | Ja |
> | redisenterprise | Inga | Inga |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Inga | Inga |
> | calculateexchange | Inga | Inga |
> | calculateprice | Inga | Inga |
> | calculatepurchaseprice | Inga | Inga |
> | kataloger | Inga | Inga |
> | commercialreservationorders | Inga | Inga |
> | utväxla | Inga | Inga |
> | reservationorders | Inga | Inga |
> | reservera | Inga | Inga |
> | resources | Inga | Inga |
> | validatereservationorder | Inga | Inga |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Inga | Inga |
> | cdnwebapplicationfirewallpolicies | Ja | Ja |
> | edgenodes | Inga | Inga |
> | filer | Ja | Ja |
> | profiler/slut punkter | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ja | Ja |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | funktioner | Inga | Inga |
> | domän namn | Ja | Inga |
> | quotas | Inga | Inga |
> | resourcetypes | Inga | Inga |
> | validatesubscriptionmoveavailability | Inga | Inga |
> | virtualmachines | Ja | Ja |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Inga | Inga |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | funktioner | Inga | Inga |
> | expressroutecrossconnections | Inga | Inga |
> | expressroutecrossconnections/peering | Inga | Inga |
> | gatewaysupporteddevices | Inga | Inga |
> | networksecuritygroups | Inga | Inga |
> | quotas | Inga | Inga |
> | reservedips | Inga | Inga |
> | virtualnetworks | Inga | Inga |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | disk | Inga | Inga |
> | images | Inga | Inga |
> | osimages | Inga | Inga |
> | osplatformimages | Inga | Inga |
> | publicimages | Inga | Inga |
> | quotas | Inga | Inga |
> | storageaccounts | Ja | Inga |
> | vmimages | Inga | Inga |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Inga | Inga |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | ratecard | Inga | Inga |
> | usageaggregates | Inga | Inga |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Se [Virtual Machines flytta vägledning](./move-limitations/virtual-machines-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Ja | Ja |
> | diskaccesses | Inga | Inga |
> | diskencryptionsets | Inga | Inga |
> | disk | Ja | Ja |
> | gallerier | Inga | Inga |
> | gallerier/bilder | Inga | Inga |
> | gallerier/avbildningar/versioner | Inga | Inga |
> | hostgroups | Inga | Inga |
> | hostgroups/värdar | Inga | Inga |
> | images | Ja | Ja |
> | proximityplacementgroups | Ja | Ja |
> | restorepointcollections | Inga | Inga |
> | restorepointcollections / restorepoints | Inga | Inga |
> | sharedvmextensions | Inga | Inga |
> | sharedvmimages | Inga | Inga |
> | sharedvmimages/versioner | Inga | Inga |
> | snapshots | Ja | Ja |
> | sshpublickeys | Inga | Inga |
> | virtualmachines | Ja | Ja |
> | virtualmachines/tillägg | Ja | Ja |
> | virtualmachinescalesets | Ja | Ja |

## <a name="microsoftconsumption"></a>Microsoft. förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Inga | Inga |
> | balanserar | Inga | Inga |
> | budget | Inga | Inga |
> | utgifts | Inga | Inga |
> | costtags | Inga | Inga |
> | krediter | Inga | Inga |
> | händelser | Inga | Inga |
> | prognoser | Inga | Inga |
> | samtliga | Inga | Inga |
> | marknads platser | Inga | Inga |
> | pricesheets | Inga | Inga |
> | läkemedle | Inga | Inga |
> | reservationdetails | Inga | Inga |
> | reservationrecommendationdetails | Inga | Inga |
> | reservationrecommendations | Inga | Inga |
> | reservationsummaries | Inga | Inga |
> | reservationtransactions | Inga | Inga |
> | tags | Inga | Inga |
> | klienter | Inga | Inga |
> | begreppen | Inga | Inga |
> | usagedetails | Inga | Inga |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containergroups | Inga | Inga |
> | serviceassociationlinks | Inga | Inga |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | register | Ja | Ja |
> | register/agentpools | Ja | Ja |
> | register/buildtasks | Ja | Ja |
> | register/replikeringar | Ja | Ja |
> | register/uppgifter | Ja | Ja |
> | register/Webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containerservices | Inga | Inga |
> | managedclusters | Inga | Inga |
> | openshiftmanagedclusters | Inga | Inga |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Inga | Inga |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aviseringar | Inga | Inga |
> | billingaccounts | Inga | Inga |
> | budget | Inga | Inga |
> | cloudconnectors | Inga | Inga |
> | anslutningar | Ja | Ja |
> | enheten | Inga | Inga |
> | enheter | Inga | Inga |
> | enrollmentaccounts | Inga | Inga |
> | exporteras | Inga | Inga |
> | externalbillingaccounts | Inga | Inga |
> | forecast | Inga | Inga |
> | DocumentDB | Inga | Inga |
> | registrera | Inga | Inga |
> | reportconfigs | Inga | Inga |
> | rapporter | Inga | Inga |
> | inställningar | Inga | Inga |
> | showbackrules | Inga | Inga |
> | vyer | Inga | Inga |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | nav | Inga | Inga |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | autentiseringsbegäran | Inga | Inga |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | typer | Inga | Inga |
> | resourceproviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft. data-

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utskrifts | Inga | Inga |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availableskus | Inga | Inga |
> | databoxedgedevices | Inga | Inga |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbetsytor | Inga | Inga |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kataloger | Ja | Ja |
> | datacatalogs | Inga | Inga |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Inga | Inga |

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | distributionspaket | Inga | Inga |
> | utgå | Inga | Inga |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datafactories | Ja | Ja |
> | fabriker | Ja | Ja |

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Inga | Inga |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | services | Inga | Inga |
> | tjänster/projekt | Inga | Inga |
> | lots | Inga | Inga |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | backupvaults | Inga | Inga |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | brygghuvudservrar | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Inga | Inga |
> | brygghuvudservrar | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Inga | Inga |
> | servergroups | Inga | Inga |
> | brygghuvudservrar | Ja | Ja |
> | serversv2 | Ja | Ja |
> | singleservers | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ja | Ja |
> | distributioner | Ja | Ja |
> | servicetopologies | Ja | Ja |
> | servicetopologies/tjänster | Ja | Ja |
> | servicetopologies/tjänster/serviceunits | Ja | Ja |
> | steg | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Ja | Ja |
> | hostpools | Ja | Ja |
> | arbetsytor | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | elasticpools | Inga | Inga |
> | elasticpools / iothubtenants | Inga | Inga |
> | iothubs | Ja | Ja |
> | provisioningservices | Ja | Ja |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | pipelines | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domänkontrollanter | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labcenters | Inga | Inga |
> | Laboration | Ja | Inga |
> | labb/miljöer | Ja | Ja |
> | labb/servicerunners | Ja | Ja |
> | labb/virtualmachines | Ja | Inga |
> | scheman | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Inga | Inga |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Inga | Inga |
> | databaseaccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domäner | Ja | Ja |
> | generatessorequest | Inga | Inga |
> | topleveldomains | Inga | Inga |
> | validatedomainregistrationinformation | Inga | Inga |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domäner | Ja | Ja |
> | eventsubscriptions | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. |
> | extensiontopics | Inga | Inga |
> | partnernamespaces | Ja | Ja |
> | partnerregistrations | Inga | Inga |
> | partnertopics | Ja | Ja |
> | systemtopics | Ja | Ja |
> | avsnitt | Ja | Ja |
> | topictypes | Inga | Inga |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Ja | Ja |
> | namn områden | Ja | Ja |
> | sku | Inga | Inga |

## <a name="microsoftexperimentation"></a>Microsoft. experimentering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Inga | Inga |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | namn områden | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | featureproviders | Inga | Inga |
> | funktioner | Inga | Inga |
> | finansiär | Inga | Inga |
> | subscriptionfeatureregistrations | Inga | Inga |

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Inga | Inga |
> | automanagedvmconfigurationprofiles | Inga | Inga |
> | guestconfigurationassignments | Inga | Inga |
> | IntelliPoint | Inga | Inga |
> | softwareupdateprofile | Inga | Inga |
> | softwareupdates | Inga | Inga |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hanainstances | Inga | Inga |
> | sapmonitors | Inga | Inga |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Inga | Inga |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!IMPORTANT]
> Du kan flytta HDInsight-kluster till en ny prenumeration eller resurs grupp. Du kan dock inte flytta över prenumerationer som nätverks resurserna är länkade till HDInsight-klustret (till exempel det virtuella nätverket, NIC eller belastningsutjämnaren). Dessutom kan du inte flytta till en ny resurs grupp ett nätverkskort som är kopplat till en virtuell dator för klustret.
>
> När du flyttar ett HDInsight-kluster till en ny prenumeration måste du först flytta andra resurser (t. ex. lagrings kontot). Flytta sedan HDInsight-klustret själv.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Ja | Ja |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | faxar | Ja | Ja |
> | datorer/tillägg | Ja | Ja |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | devices | Inga | Inga |
> | vnfs | Inga | Inga |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | delarna | Inga | Inga |
> | networkscopes | Inga | Inga |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utskrifts | Ja | Ja |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!IMPORTANT]
> Se till att flytta till den nya prenumerationen inte överskrider [prenumerations kvoterna](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | actiongroups | Ja | Ja |
> | activitylogalerts | Inga | Inga |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | baslinje | Inga | Inga |
> | delarna | Ja | Ja |
> | datacollectionrules | Inga | Inga |
> | diagnosticsettings | Inga | Inga |
> | diagnosticsettingscategories | Inga | Inga |
> | eventcategories | Inga | Inga |
> | eventtypes | Inga | Inga |
> | extendeddiagnosticsettings | Inga | Inga |
> | guestdiagnosticsettings | Inga | Inga |
> | listmigrationdate | Inga | Inga |
> | logdefinitions | Inga | Inga |
> | logprofiles | Inga | Inga |
> | loggar | Inga | Inga |
> | metricalerts | Inga | Inga |
> | metricbaselines | Inga | Inga |
> | metricbatch | Inga | Inga |
> | metricdefinitions | Inga | Inga |
> | metricnamespaces | Inga | Inga |
> | metrics | Inga | Inga |
> | migratealertrules | Inga | Inga |
> | migratetonewpricingmodel | Inga | Inga |
> | Mina arbets böcker | Inga | Inga |
> | notificationgroups | Inga | Inga |
> | privatelinkscopes | Inga | Inga |
> | rollbacktolegacypricingmodel | Inga | Inga |
> | scheduledqueryrules | Ja | Ja |
> | topologi | Inga | Inga |
> | transaktioner | Inga | Inga |
> | vminsightsonboardingstatuses | Inga | Inga |
> | webbtester | Ja | Ja |
> | webbtester/gettestresultfile | Inga | Inga |
> | arbetsböcker | Ja | Ja |
> | workbooktemplates | Ja | Ja |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | apptemplates | Inga | Inga |
> | iotapps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Rita | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft. nyckel valv

> [!IMPORTANT]
> Nyckel valv som används för disk kryptering kan inte flyttas till en resurs grupp i samma prenumeration eller mellan prenumerationer.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | deletedvaults | Inga | Inga |
> | hsmpools | Inga | Inga |
> | managedhsms | Inga | Inga |
> | valv | Ja | Ja |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Ja | Ja |
> | registeredsubscriptions | Inga | Inga |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Inga | Inga |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Ja | Ja |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labaccounts | Inga | Inga |
> | användare | Inga | Inga |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftlocationservices"></a>Microsoft. filen LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Inga | Inga |
> | integrationaccounts | Ja | Ja |
> | integrationserviceenvironments | Ja | Inga |
> | integrationserviceenvironments/managedapis | Ja | Inga |
> | isolatedenvironments | Inga | Inga |
> | arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Inga | Inga |
> | WebServices | Ja | Inga |
> | arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Inga | Inga |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |
> | teamaccounts | Inga | Inga |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbetsytor | Inga | Inga |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | configurationassignments | Inga | Inga |
> | maintenanceconfigurations | Ja | Ja |
> | uppdateringar | Inga | Inga |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | identiteter | Inga | Inga |
> | userassignedidentities | Inga | Inga |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | managednetworks | Inga | Inga |
> | managednetworks / managednetworkgroups | Inga | Inga |
> | managednetworks / managednetworkpeeringpolicies | Inga | Inga |
> | avisering | Inga | Inga |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Inga | Inga |
> | registrationassignments | Inga | Inga |
> | registrationdefinitions | Inga | Inga |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | getentities | Inga | Inga |
> | managementgroups | Inga | Inga |
> | managementgroups/inställningar | Inga | Inga |
> | resources | Inga | Inga |
> | starttenantbackfill | Inga | Inga |
> | tenantbackfillstatus | Inga | Inga |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | konton/privateatlases | Ja | Ja |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | budgivning | Inga | Inga |
> | offertypes | Inga | Inga |
> | privategalleryitems | Inga | Inga |
> | privatestoreclient | Inga | Inga |
> | privatestores | Inga | Inga |
> | läkemedle | Inga | Inga |
> | Utgivare | Inga | Inga |
> | registrera | Inga | Inga |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Inga | Inga |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | villkor | Inga | Inga |
> | offertypes | Inga | Inga |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Media Services | Ja | Ja |
> | Media Services/liveevents | Ja | Ja |
> | Media Services/strömnings slut punkter | Ja | Ja |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | appclusters | Inga | Inga |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Inga | Inga |
> | migrateprojects | Inga | Inga |
> | movecollections | Inga | Inga |
> | samarbetsprojekt | Inga | Inga |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Inga | Inga |
> | objectunderstandingaccounts | Inga | Inga |
> | remoterenderingaccounts | Ja | Ja |
> | spatialanchorsaccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Inga | Inga |
> | netappaccounts / capacitypools | Inga | Inga |
> | netappaccounts/capacitypools/Volumes | Inga | Inga |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Se [rikt linjer för nätverks flytt](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Inga | Inga |
> | applicationgatewaywebapplicationfirewallpolicies | Inga | Inga |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewalls | Inga | Inga |
> | bastionhosts | Inga | Inga |
> | bgpservicecommunities | Inga | Inga |
> | anslutning | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Inga | Inga |
> | dnszones | Ja | Ja |
> | expressroutecircuits | Inga | Inga |
> | expressroutegateways | Inga | Inga |
> | expressrouteserviceproviders | Inga | Inga |
> | firewallpolicies | Ja | Ja |
> | frontdoors | Inga | Inga |
> | ipallocations | Ja | Ja |
> | ipgroups | Ja | Ja |
> | belastningsutjämnare | Ja – grundläggande SKU<br> Ja – standard-SKU | Ja – grundläggande SKU<br>Standard-SKU: n |
> | localnetworkgateways | Ja | Ja |
> | natgateways | Inga | Inga |
> | networkexperimentprofiles | Inga | Inga |
> | networkintentpolicies | Ja | Ja |
> | NetworkInterfaces | Ja | Ja |
> | networkprofiles | Inga | Inga |
> | networksecuritygroups | Ja | Ja |
> | networkwatchers | Inga | Inga |
> | networkwatchers / connectionmonitors | Ja | Inga |
> | networkwatchers / flowlogs | Ja | Inga |
> | networkwatchers / pingmeshes | Ja | Inga |
> | p2svpngateways | Inga | Inga |
> | privatednszones | Ja | Ja |
> | privatednszones / virtualnetworklinks | Ja | Ja |
> | privatednszonesinternal | Inga | Inga |
> | privateendpointredirectmaps | Inga | Inga |
> | privateendpoints | Inga | Inga |
> | privatelinkservices | Inga | Inga |
> | publicipaddresses | Ja – grundläggande SKU<br>Ja – standard-SKU | Ja – grundläggande SKU<br>Standard-SKU: n |
> | publicipprefixes | Ja | Ja |
> | routefilters | Inga | Inga |
> | routetables | Ja | Ja |
> | securitypartnerproviders | Ja | Ja |
> | serviceendpointpolicies | Ja | Ja |
> | trafficmanagergeographichierarchies | Inga | Inga |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/termiska kartor | Inga | Inga |
> | trafficmanagerusermetricskeys | Inga | Inga |
> | virtualhubs | Inga | Inga |
> | virtualnetworkgateways | Ja | Ja |
> | virtualnetworks | Ja | Ja |
> | virtualnetworktaps | Inga | Inga |
> | virtualrouters | Ja | Ja |
> | virtualwans | Inga | Inga |
> | vpngateways (virtuellt WAN) | Inga | Inga |
> | vpnserverconfigurations | Inga | Inga |
> | vpnsites (virtuellt WAN) | Inga | Inga |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | namn områden | Ja | Ja |
> | namnrymder/notificationhubs | Ja | Ja |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hypervsites | Inga | Inga |
> | importsites | Inga | Inga |
> | serversites | Inga | Inga |
> | vmwaresites | Inga | Inga |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!IMPORTANT]
> Kontrol lera att flytta till en ny prenumeration inte överskrider [prenumerations kvoterna](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Det går inte att flytta arbets ytor som har ett länkat Automation-konto. Innan du påbörjar en flytt åtgärd måste du ta bort länken till eventuella Automation-konton.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Inga | Inga |
> | deletedworkspaces | Inga | Inga |
> | linktargets | Inga | Inga |
> | storageinsightconfigs | Inga | Inga |
> | arbetsytor | Ja | Ja |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | managementassociations | Inga | Inga |
> | managementconfigurations | Ja | Ja |
> | lösningar | Ja | Ja |
> | vyer | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | legacypeerings | Inga | Inga |
> | peerasns | Inga | Inga |
> | peeringlocations | Inga | Inga |
> | peerings | Inga | Inga |
> | peeringservicecountries | Inga | Inga |
> | peeringservicelocations | Inga | Inga |
> | peeringserviceproviders | Inga | Inga |
> | peeringservices | Inga | Inga |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | policyevents | Inga | Inga |
> | policystates | Inga | Inga |
> | policytrackedresources | Inga | Inga |
> | reparationer | Inga | Inga |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> |  -konsoler | Inga | Inga |
> | instrumentpaneler | Ja | Ja |
> | usersettings | Inga | Inga |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kapaciteter | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Inga | Inga |
> | providerregistrations | Inga | Inga |
> | distributioner | Inga | Inga |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbetsytor | Inga | Inga |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!IMPORTANT]
> Se [Recovery Services flytta vägledning](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityresults | Inga | Inga |
> | valv | Ja | Ja |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Inga | Inga |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | namn områden | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | skickar | Ja | Ja |
> | resourcechangedetails | Inga | Inga |
> | resourcechanges | Inga | Inga |
> | resources | Inga | Inga |
> | resourceshistory | Inga | Inga |
> | subscriptionsstatus | Inga | Inga |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | childresources | Inga | Inga |
> | emergingissues | Inga | Inga |
> | händelser | Inga | Inga |
> | metadata | Inga | Inga |
> | meddelanden | Inga | Inga |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | distributioner | Inga | Inga |
> | deploymentscripts | Inga | Inga |
> | deploymentscripts/loggar | Inga | Inga |
> | Länkar | Inga | Inga |
> | finansiär | Inga | Inga |
> | ResourceGroups | Inga | Inga |
> | resources | Inga | Inga |
> | prenumerationer | Inga | Inga |
> | tags | Inga | Inga |
> | templatespecs | Inga | Inga |
> | templatespecs/versioner | Inga | Inga |
> | klienter | Inga | Inga |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Inga |
> | saasresources | Inga | Inga |

## <a name="microsoftsearch"></a>Microsoft. search

> [!IMPORTANT]
> Du kan inte flytta flera Sök resurser i olika regioner i samma åtgärd. Flytta i stället dem i separata åtgärder.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | resourcehealthmetadata | Inga | Inga |
> | searchservices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Inga | Inga |
> | advancedthreatprotectionsettings | Inga | Inga |
> | aviseringar | Inga | Inga |
> | allowedconnections | Inga | Inga |
> | applicationwhitelistings | Inga | Inga |
> | assessmentmetadata | Inga | Inga |
> | utvärderingar | Inga | Inga |
> | autodismissalertsrules | Inga | Inga |
> | automatiseringar | Ja | Ja |
> | autoprovisioningsettings | Inga | Inga |
> | complianceresults | Inga | Inga |
> | godkännanden | Inga | Inga |
> | datacollectionagents | Inga | Inga |
> | devicesecuritygroups | Inga | Inga |
> | discoveredsecuritysolutions | Inga | Inga |
> | externalsecuritysolutions | Inga | Inga |
> | informationprotectionpolicies | Inga | Inga |
> | iotsecuritysolutions | Ja | Ja |
> | iotsecuritysolutions / analyticsmodels | Inga | Inga |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Inga | Inga |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Inga | Inga |
> | jitnetworkaccesspolicies | Inga | Inga |
> | policies | Inga | Inga |
> | prissättningar | Inga | Inga |
> | regulatorycompliancestandards | Inga | Inga |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Inga | Inga |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Inga | Inga |
> | securitycontacts | Inga | Inga |
> | securitysolutions | Inga | Inga |
> | securitysolutionsreferencedata | Inga | Inga |
> | securitystatuses | Inga | Inga |
> | securitystatusessummaries | Inga | Inga |
> | servervulnerabilityassessments | Inga | Inga |
> | inställningar | Inga | Inga |
> | underbedömningar | Inga | Inga |
> | uppgifter | Inga | Inga |
> | topologier | Inga | Inga |
> | workspacesettings | Inga | Inga |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | agg regeringar | Inga | Inga |
> | alertrules | Inga | Inga |
> | alertruletemplates | Inga | Inga |
> | automationrules | Inga | Inga |
> | bokmärken | Inga | Inga |
> | fall | Inga | Inga |
> | dataconnectors | Inga | Inga |
> | poster | Inga | Inga |
> | entityqueries | Inga | Inga |
> | incidenter | Inga | Inga |
> | officeconsents | Inga | Inga |
> | inställningar | Inga | Inga |
> | threatintelligence | Inga | Inga |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | consoleservices | Inga | Inga |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | gatewayer | Inga | Inga |
> | artikelnoder | Inga | Inga |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | namn områden | Ja | Ja |
> | premiummessagingregions | Inga | Inga |
> | sku | Inga | Inga |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Inga | Inga |
> | kluster | Ja | Ja |
> | containergroups | Inga | Inga |
> | containergroupsets | Inga | Inga |
> | edgeclusters | Inga | Inga |
> | managedclusters | Inga | Inga |
> | nätet | Inga | Inga |
> | secretstores | Inga | Inga |
> | volumes | Inga | Inga |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Ja |
> | containergroups | Inga | Inga |
> | gatewayer | Ja | Ja |
> | nätet | Ja | Ja |
> | secrets | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | distributioner | Inga | Inga |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | SignalR | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Inga | Inga |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Inga | Inga |
> | program | Inga | Inga |
> | jitrequests | Inga | Inga |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> En databas och en server måste finnas i samma resurs grupp. När du flyttar en SQL-Server flyttas även alla databaserna. Det här beteendet gäller för Azure SQL Database-och Azure Synapse Analytics-databaser.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | instancepools | Inga | Inga |
> | platser | Ja | Ja |
> | managedinstances | Inga | Inga |
> | brygghuvudservrar | Ja | Ja |
> | servrar/databaser | Ja | Ja |
> | servrar/databaser/backuplongtermretentionpolicies | Ja | Ja |
> | servrar/elasticpools | Ja | Ja |
> | servrar/jobaccounts | Ja | Ja |
> | servrar/jobagents | Ja | Ja |
> | virtualclusters | Ja | Ja |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Ja | Ja |
> | sqlvirtualmachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ja | Ja |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cacheminnen | Inga | Inga |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Inga | Inga |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Inga | Inga |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hantera | Inga | Inga |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!IMPORTANT]
> Det går inte att flytta Stream Analytics jobb i körnings läge.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Inga | Inga |
> | streamingjobs | Ja | Ja |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utrymmen | Inga | Inga |
> | pipe | Inga | Inga |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | prenumerationer | Inga | Inga |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | services | Inga | Inga |
> | supporttickets | Inga | Inga |

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbetsytor | Inga | Inga |
> | arbets ytor/bigdatapools | Inga | Inga |
> | arbets ytor/sqlpools | Inga | Inga |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utrymmen | Ja | Ja |
> | miljöer/eventsources | Ja | Ja |
> | miljöer/referencedatasets | Ja | Ja |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Auktoriseringshanteraren | Ja | Ja |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Inga | Inga |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!IMPORTANT]
> Information om hur du ändrar prenumerationen för Azure-DevOps finns i [ändra den Azure-prenumeration som används för fakturering](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konto | Inga | Inga |
> | konto/tillägg | Inga | Inga |
> | konto/projekt | Inga | Inga |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arczones | Inga | Inga |
> | resourcepools | Inga | Inga |
> | vCenter | Inga | Inga |
> | virtualmachines | Inga | Inga |
> | virtualmachinetemplates | Inga | Inga |
> | virtualnetworks | Inga | Inga |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Inga | Inga |
> | dedicatedcloudservices | Inga | Inga |
> | virtualmachines | Inga | Inga |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | devices | Inga | Inga |
> | vnfs | Inga | Inga |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |
> | utgå | Inga | Inga |
> | registeredsubscriptions | Inga | Inga |

## <a name="microsoftweb"></a>Microsoft. Web

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availablestacks | Inga | Inga |
> | billingmeters | Inga | Inga |
> | certifikat | Inga | Ja |
> | connectiongateways | Ja | Ja |
> | anslutning | Ja | Ja |
> | customapis | Ja | Ja |
> | deletedsites | Inga | Inga |
> | deploymentlocations | Inga | Inga |
> | regioner | Inga | Inga |
> | hostingenvironments | Inga | Inga |
> | kubeenvironments | Ja | Ja |
> | publishingusers | Inga | Inga |
> | rekommendationer | Inga | Inga |
> | resourcehealthmetadata | Inga | Inga |
> | körningar | Inga | Inga |
> | Server grupper | Ja | Ja |
> | Server grupper/eventgridfilters | Inga | Inga |
> | webbplatser | Ja | Ja |
> | platser/premieraddons | Ja | Ja |
> | platser/platser | Ja | Ja |
> | sourcecontrols | Inga | Inga |
> | staticsites | Inga | Inga |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | Inga | Inga |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | deviceservices | Inga | Inga |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbets belastningar | Inga | Inga |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | delarna | Inga | Inga |
> | componentssummary | Inga | Inga |
> | monitorinstances | Inga | Inga |
> | monitorinstancessummary | Inga | Inga |
> | Övervakare | Inga | Inga |

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flytt åtgärden.

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](move-resource-group-and-subscription.md).

Hämta [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)för att hämta samma data som en fil med kommaavgränsade värden.
