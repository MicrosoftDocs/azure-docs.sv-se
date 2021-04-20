---
title: Stöd för flyttåtgärd efter resurstyp
description: Visar de Azure-resurstyper som kan flyttas till en ny resursgrupp, prenumeration eller region.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: a56a9e6f04aa800e16bbab0190ce7b41d87da590
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740096"
---
# <a name="move-operation-support-for-resources"></a>Stöd för flytt av resurser

Den här artikeln visar om en Azure-resurstyp stöder flyttåtgärden. Den innehåller också information om särskilda villkor att tänka på när du flyttar en resurs.

> [!IMPORTANT]
> I de flesta fall kan en underordnad resurs inte flyttas oberoende av den överordnade resursen. Underordnade resurser har en resurstyp i formatet `<resource-provider-namespace>/<parent-resource>/<child-resource>` . Är till `Microsoft.ServiceBus/namespaces/queues` exempel en underordnad resurs för `Microsoft.ServiceBus/namespaces` . När du flyttar den överordnade resursen flyttas den underordnade resursen automatiskt med den. Om du inte ser en underordnad resurs i den här artikeln kan du anta att den flyttas med den överordnade resursen. Om den överordnade resursen inte stöder flytt kan den underordnade resursen inte flyttas.

Gå till ett resursprovidernamnområde:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aanoam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMarinaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualisering](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Sint](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | domainservices | Inga | Inga |  Inga |

## <a name="microsoftaadiam"></a>microsoft.a molnam

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnosticsettings | Inga | Inga | Inga |
> | diagnosticsettingscategories | Inga | Inga | Inga |
> | privatelinkforazuread | Ja | Ja | Inga |
> | Hyresgäster | Ja | Ja | Inga |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | supportproviders | Inga | Inga | Inga |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | Inga | Inga | Inga |
> | addsservices | Inga | Inga | Inga |
> | Agenter | Inga | Inga | Inga |
> | anonymousapiusers | Inga | Inga | Inga |
> | konfiguration | Inga | Inga | Inga |
> | loggar | Inga | Inga | Inga |
> | rapporter | Inga | Inga | Inga |
> | servicehealthmetrics | Inga | Inga | Inga |
> | services | Inga | Inga | Inga |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Konfigurationer | Inga | Inga | Inga |
> | generaterecommendations | Inga | Inga | Inga |
> | metadata | Inga | Inga | Inga |
> | rekommendationer | Inga | Inga | Inga |
> | undertryckningar | Inga | Inga | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | actionrules | Ja | Ja | Inga |
> | aviseringar | Inga | Inga | Inga |
> | alertslist | Inga | Inga | Inga |
> | alertsmetadata | Inga | Inga | Inga |
> | alertssummary | Inga | Inga | Inga |
> | alertssummarylist | Inga | Inga | Inga |
> | smartdetectectectertrules | Ja | Ja | Inga |
> | smartgroups | Inga | Inga | Inga |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Servrar | Ja | Ja | Inga |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> En API Management tjänst som är inställd på förbruknings-SKU:n kan inte flyttas.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback | Inga | Inga | Inga |
> | tjänst | Ja | Ja | Ja (med hjälp av mall) <br/><br/> [Flytta API Management mellan regioner](../../api-management/api-management-howto-migrate.md). |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationstores | Ja | Ja | Inga |
> | configurationstores/eventgridfilters | Inga | Inga | Inga |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Våren | Ja | Ja | Inga |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Se [App Service vägledning för flytt.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | Inga | Inga | Ja (med hjälp av mall)<br/><br/> [Flytta en App Service till en annan region](../../app-service/manage-move-across-regions.md) |
> | appidentiteter | Inga | Inga | Inga |
> | Gateways | Inga | Inga | Inga |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | attestationproviders | Ja | Ja | Inga |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | klassiskaadministratorer | Inga | Inga | Inga |
> | dataaliases | Inga | Inga | Inga |
> | denyassignments | Inga | Inga | Inga |
> | elevateaccess | Inga | Inga | Inga |
> | findorphanroleassignments | Inga | Inga | Inga |
> | Lås | Inga | Inga | Inga |
> | behörigheter | Inga | Inga | Inga |
> | policyassignments | Inga | Inga | Inga |
> | principdefinitioner | Inga | Inga | Inga |
> | policysetdefinitions | Inga | Inga | Inga |
> | privatelinkassociations | Inga | Inga | Inga |
> | resourcemanagementprivatelinks | Inga | Inga | Inga |
> | roleassignments | Inga | Inga | Inga |
> | roleassignmentsusagemetrics | Inga | Inga | Inga |
> | rolldefinitioner | Inga | Inga | Inga |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Runbooks måste finnas i samma resursgrupp som Automation-kontot.
>
> Mer information finns i [Flytta ditt Azure Automation till en annan prenumeration.](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | automationskonto | Ja | Ja | Ja (med hjälp av mall) <br/><br/> [Använda geo-replikering](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | automationaccounts/configurations | Ja | Ja | Inga |
> | automationaccounts/runbooks | Ja | Ja | Inga |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | privateclouds | Ja | Ja | Inga |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | b2cdirectories | Ja | Ja | Inga |
> | b2ctenants | Inga | Inga | Inga |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | datastyrare | Inga | Inga | Inga |
> | hybriddatamanagers | Inga | Inga | Inga |
> | postgresinstances | Inga | Inga | Inga |
> | sqlinstances | Inga | Inga | Inga |
> | sqlmanagedinstances | Inga | Inga | Inga |
> | sqlserverinstances | Inga | Inga | Inga |
> | sqlserverregistrations | Ja | Ja | Inga |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | Inga | Inga | Inga |
> | Registreringar | Ja | Ja | Inga |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Kluster | Inga | Inga | Inga |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | batchaccounts | Ja | Ja | Batch-konton kan inte flyttas direkt från en region till en annan, men du kan använda en mall för att exportera en mall, ändra den och distribuera mallen till den nya regionen. <br/><br/> Lär dig mer [om att flytta ett Batch-konto mellan regioner](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | faktureringskonto | Inga | Inga | Inga |
> | billingperiods | Inga | Inga | Inga |
> | faktureringbehörigheter | Inga | Inga | Inga |
> | billingproperty | Inga | Inga | Inga |
> | billingroleassignments | Inga | Inga | Inga |
> | billingroledefinitions | Inga | Inga | Inga |
> | Avdelningar | Inga | Inga | Inga |
> | enrollmentaccounts | Inga | Inga | Inga |
> | Fakturor | Inga | Inga | Inga |
> | Överföringar | Inga | Inga | Inga |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapis | Inga | Inga | Inga |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | Inga | Inga | Inga |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | blockchainmembers | Inga | Inga | Inga <br/><br/> Blockkedjenätverket kan inte ha noder i olika regioner. |
> | cordamembers | Inga | Inga | Inga |
> | Watchers | Inga | Inga | Inga |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | tokentjänster | Inga | Inga | Inga |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | skisstilldelningar | Inga | Inga | Inga |
> | Ritningar | Inga | Inga | Inga |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | robottjänster | Ja | Ja | Inga |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Om instansen Azure Cache for Redis med ett virtuellt nätverk kan instansen inte flyttas till en annan prenumeration. Se [Begränsningar för nätverksflyttning.](./move-limitations/networking-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Redis | Ja | Ja | Inga |
> | redisenterprise | Inga | Inga | Inga |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | appliedreservations | Inga | Inga | Inga |
> | calculateexchange | Inga | Inga | Inga |
> | calculateprice | Inga | Inga | Inga |
> | calculatepurchaseprice | Inga | Inga | Inga |
> | Kataloger | Inga | Inga | Inga |
> | commercialreservationorders | Inga | Inga | Inga |
> | Exchange | Inga | Inga | Inga |
> | reservationorders | Inga | Inga | Inga |
> | Reservationer | Inga | Inga | Inga |
> | resources | Inga | Inga | Inga |
> | validatereservationorder | Inga | Inga | Inga |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplicationfirewallmanagedrulesets | Inga | Inga | Inga |
> | cdnwebapplicationfirewallpolicies | Ja | Ja | Inga |
> | kantnoder | Inga | Inga | Inga |
> | Profiler | Ja | Ja | Inga |
> | profiler/slutpunkter | Ja | Ja | Inga |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Se [App Service vägledning för flytt.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | certificateorders | Ja | Ja | Inga |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Se [Vägledning för flytt av klassisk distribution.](./move-limitations/classic-model-move-limitations.md) Klassiska distributionsresurser kan flyttas mellan prenumerationer med en åtgärd som är specifik för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | funktioner | Inga | Inga | Inga |
> | domännamn | Ja | Inga | Inga |
> | quotas | Inga | Inga | Inga |
> | resourcetypes | Inga | Inga | Inga |
> | validatesubscriptionmoveavailability | Inga | Inga | Inga |
> | virtualmachines | Ja | Ja | Inga |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Se [Vägledning för flytt av klassisk distribution.](./move-limitations/classic-model-move-limitations.md) Klassiska distributionsresurser kan flyttas mellan prenumerationer med en åtgärd som är specifik för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | Inga | Inga | Inga |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Se [Vägledning för flytt av klassisk distribution.](./move-limitations/classic-model-move-limitations.md) Klassiska distributionsresurser kan flyttas mellan prenumerationer med en åtgärd som är specifik för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | funktioner | Inga | Inga | Inga |
> | expressroutecrossconnections | Inga | Inga | Inga |
> | expressroutecrossconnections/peerings | Inga | Inga | Inga |
> | gatewaysupporteddevices | Inga | Inga | Inga |
> | networksecuritygroups | Inga | Inga | Inga |
> | quotas | Inga | Inga | Inga |
> | reservedips | Inga | Inga | Inga |
> | virtualnetworks | Inga | Inga | Inga |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Se [Vägledning för flytt av klassisk distribution.](./move-limitations/classic-model-move-limitations.md) Klassiska distributionsresurser kan flyttas mellan prenumerationer med en åtgärd som är specifik för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Diskar | Inga | Inga | Inga |
> | images | Inga | Inga | Inga |
> | osimages | Inga | Inga | Inga |
> | osplatformimages | Inga | Inga | Inga |
> | publicimages | Inga | Inga | Inga |
> | quotas | Inga | Inga | Inga |
> | storageaccounts | Ja | Inga | Ja |
> | vmimages | Inga | Inga | Inga |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Se [Vägledning för flytt av klassisk distribution.](./move-limitations/classic-model-move-limitations.md) Klassiska distributionsresurser kan flyttas mellan prenumerationer med en åtgärd som är specifik för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | operations | Inga | Inga | Inga |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Ja | Ja | Inga |
> | Cognitive Search | **Väntande** | **Väntande** | Stöds med manuella steg.<br/><br/> Lär dig mer [om att flytta Azure Cognitive Search tjänst till en annan region](../../search/search-howto-move-across-regions.md) |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | ratecard | Inga | Inga | Inga |
> | usageaggregates | Inga | Inga | Inga |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Se [Virtual Machines vägledning för flytt.](./move-limitations/virtual-machines-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | availabilitysets | Ja | Ja |  Ja <br/><br/> Använd [Azure Resource Mover för](../../resource-mover/tutorial-move-region-virtual-machines.md) att flytta tillgänglighetsuppsättningar. |
> | diskaccesses | Inga | Inga | Inga |
> | diskencryptionsets | Inga | Inga | Inga |
> | Diskar | Ja | Ja | Ja <br/><br/> Använd [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta virtuella Azure-datorer och relaterade diskar. |
> | Gallerier | Inga | Inga | Inga |
> | gallerier/bilder | Inga | Inga | Inga |
> | gallerier/bilder/versioner | Inga | Inga | Inga |
> | värdgrupper | Inga | Inga | Inga |
> | värdgrupper/värdar | Inga | Inga | Inga |
> | images | Ja | Ja | Inga |
> | proximityplacementgroups | Ja | Ja | Inga |
> | restorepointcollections | Inga | Inga | Inga |
> | restorepointcollections/restorepoints | Inga | Inga | Inga |
> | sharedvmextensions | Inga | Inga | Inga |
> | sharedvmimages | Inga | Inga | Inga |
> | sharedvmimages/versions | Inga | Inga | Inga |
> | snapshots | Ja | Ja | Inga |
> | sshpublickeys | Inga | Inga | Inga |
> | virtualmachines | Ja | Ja | Ja <br/><br/> Använd [Azure Resource Mover för](../../resource-mover/tutorial-move-region-virtual-machines.md) att flytta virtuella Azure-datorer. |
> | virtualmachines/extensions | Ja | Ja | Inga |
> | virtualmachinescalesets | Ja | Ja | Inga |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregatedcost | Inga | Inga | Inga |
> | Saldon | Inga | Inga | Inga |
> | Budgetar | Inga | Inga | Inga |
> | Avgifter | Inga | Inga | Inga |
> | costtags | Inga | Inga | Inga |
> | Krediter | Inga | Inga | Inga |
> | händelser | Inga | Inga | Inga |
> | Prognoser | Inga | Inga | Inga |
> | Massor | Inga | Inga | Inga |
> | Marknadsplatser | Inga | Inga | Inga |
> | prisblad | Inga | Inga | Inga |
> | Produkter | Inga | Inga | Inga |
> | reservationdetails | Inga | Inga | Inga |
> | reservationrecommendationdetails | Inga | Inga | Inga |
> | reservationrecommendations | Inga | Inga | Inga |
> | reservationsummaries | Inga | Inga | Inga |
> | reservationtransactions | Inga | Inga | Inga |
> | tags | Inga | Inga | Inga |
> | Hyresgäster | Inga | Inga | Inga |
> | Villkor | Inga | Inga | Inga |
> | usagedetails | Inga | Inga | Inga |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | containergroups | Inga | Inga | Inga |
> | serviceassociationlinks | Inga | Inga | Inga |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Register | Ja | Ja | Inga |
> | register/agentpooler | Ja | Ja | Inga |
> | register/buildtasks | Ja | Ja | Inga |
> | register/replikeringar | Ja | Ja | Inga |
> | register/uppgifter | Ja | Ja | Inga |
> | register/webhooks | Ja | Ja | Inga |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | containerservices | Inga | Inga | Inga |
> | managedclusters | Inga | Inga | Inga |
> | openshiftmanagedclusters | Inga | Inga | Inga |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | program | Inga | Inga | Inga |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Inga | Inga | Inga |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | aviseringar | Inga | Inga | Inga |
> | faktureringskonto | Inga | Inga | Inga |
> | Budgetar | Inga | Inga | Inga |
> | cloudconnectors | Inga | Inga | Inga |
> | anslutningar | Ja | Ja | Inga |
> | Avdelningar | Inga | Inga | Inga |
> | Dimensioner | Inga | Inga | Inga |
> | enrollmentaccounts | Inga | Inga | Inga |
> | Export | Inga | Inga | Inga |
> | externalbillingaccounts | Inga | Inga | Inga |
> | forecast | Inga | Inga | Inga |
> | DocumentDB | Inga | Inga | Inga |
> | registrera | Inga | Inga | Inga |
> | reportconfigs | Inga | Inga | Inga |
> | rapporter | Inga | Inga | Inga |
> | inställningar | Inga | Inga | Inga |
> | showbackrules | Inga | Inga | Inga |
> | Visningar | Inga | Inga | Inga |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | nav | Inga | Inga | Inga |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Begäranden | Inga | Inga | Inga |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Sammanslutningar | Inga | Inga | Inga |
> | resourceproviders | Ja | Ja | Inga |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Jobb | Inga | Inga | Inga |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | availableskus | Inga | Inga | Inga |
> | databoxedgedevices | Inga | Inga | Inga |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | arbetsytor | Inga | Inga | Inga |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Kataloger | Ja | Ja | Inga |
> | datacatalogs | Inga | Inga | Inga |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | connectionmanagers | Inga | Inga | Inga |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Paket | Inga | Inga | Inga |
> | Planer | Inga | Inga | Inga |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | datafaktorer | Ja | Ja | Inga |
> | Fabriker | Ja | Ja | Inga |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | Inga | Inga | Inga |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Ja | Ja | Inga |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Ja | Ja | Inga |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Inga | Inga | Inga |
> | tjänster/projekt | Inga | Inga | Inga |
> | Platser | Inga | Inga | Inga |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ---------- |
> | backupvaults | Inga | Inga | Inga |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Ja | Ja | Inga |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforTillaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Servrar | Ja | Ja | Du kan använda en skrivskyddade replik mellan regioner för att flytta en befintlig server. [Läs mer](../../postgresql/howto-move-regions-portal.md).<br/><br/> Om tjänsten etableras med geo-redundant lagring av säkerhetskopior kan du använda geo-återställning för att återställa i andra regioner. [Läs mer](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | Inga | Inga | Inga |
> | Servrar | Ja | Ja | Du kan använda en skrivskyddade replik mellan regioner för att flytta en befintlig server. [Läs mer](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | Inga | Inga | Inga |
> | servergroups | Inga | Inga | Inga |
> | Servrar | Ja | Ja | Du kan använda en skrivskyddade replik mellan regioner för att flytta en befintlig server. [Läs mer](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Ja | Ja | Inga |
> | singleservers | Ja | Ja | Inga |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | artifactsources | Ja | Ja | Inga |
> | Utbyggnader | Ja | Ja | Inga |
> | servicetopologies | Ja | Ja | Inga |
> | servicetopologies/services | Ja | Ja | Inga |
> | servicetopologies/services/serviceunits | Ja | Ja | Inga |
> | steg | Ja | Ja | Inga |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualisering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgroups | Ja | Ja | Inga |
> | värdpooler | Ja | Ja | Inga |
> | arbetsytor | Ja | Ja | Inga |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | elasticpools | Inga | Inga | Nej. Resursen exponeras inte. |
> | elasticpools/iothubtenants | Inga | Inga | Nej. Resursen exponeras inte. |
> | iothubs | Ja | Ja | Ja. [Läs mer](../../iot-hub/iot-hub-how-to-clone.md) |
> | provisioningservices | Ja | Ja | Inga |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Rörledningar | Ja | Ja | Inga |
> | Styrenheter | **Väntande** | **Väntande** | No |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Styrenheter | Ja | Ja | Inga |
> | AKS-kluster | **Väntande** | **Väntande** | No<br/><br/> [Läs mer om](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) att flytta till en annan region.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | labcenters | Inga | Inga | Inga |
> | Labs | Ja | Inga | Inga |
> | labb/miljöer | Ja | Ja | Inga |
> | labb/servicerunners | Ja | Ja | Inga |
> | labb/virtualmachines | Ja | Inga | Inga |
> | Scheman | Ja | Ja | Inga |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwinsinstances | Inga | Inga | Ja, genom att återskapa resurser i en ny region. [Läs mer](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | databaseaccountnames | Inga | Inga | Inga |
> | databaseaccounts | Ja | Ja | Inga |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Domäner | Ja | Ja | Inga |
> | generatessorequest | Inga | Inga | Inga |
> | toppdomäner | Inga | Inga | Inga |
> | validatedomainregistrationinformation | Inga | Inga | Inga |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Ja | Ja | Inga |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Domäner | Ja | Ja | Inga |
> | eventsubscriptions | Nej – kan inte flyttas separat utan flyttas automatiskt med en resurs som prenumererar. | Nej – kan inte flyttas separat utan flyttas automatiskt med en resurs som prenumererar. | No |
> | extensiontopics | Inga | Inga | Inga |
> | partnernamespaces | Ja | Ja | Inga |
> | partnerregistreringar | Inga | Inga | Inga |
> | partnertopics | Ja | Ja | Inga |
> | systemtopics | Ja | Ja | Inga |
> | Ämnen | Ja | Ja | Inga |
> | topictypes | Inga | Inga | Inga |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Kluster | Ja | Ja | Inga |
> | Namnområden | Ja | Ja | Ja (med mall)<br/><br/> [Flytta ett Event Hub-namnområde till en annan region](../../event-hubs/move-across-regions.md) |
> | sku | Inga | Inga | Inga |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | experimentworkspaces | Inga | Inga | Inga |

## <a name="microsoftfalcon"></a>Microsoft.Office

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Namnområden | Ja | Ja | Inga |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | featureproviders | Inga | Inga | Inga |
> | funktioner | Inga | Inga | Inga |
> | Leverantörer | Inga | Inga | Inga |
> | subscriptionfeatureregistrations | Inga | Inga | Inga |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Inga | Inga | Inga |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | automanagedaccounts | Inga | Inga | Inga |
> | automanagedvmconfigurationprofiles | Inga | Inga | Inga |
> | guestconfigurationassignments | Inga | Inga | Inga |
> | Programvara | Inga | Inga | Inga |
> | softwareupdateprofile | Inga | Inga | Inga |
> | softwareupdates | Inga | Inga | Inga |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | hanainstances | Inga | Inga | Inga |
> | sapmonitors | Inga | Inga | Inga |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | dedikeradehsms | Inga | Inga | Inga |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> Du kan flytta HDInsight-kluster till en ny prenumeration eller resursgrupp. Du kan dock inte flytta mellan prenumerationer på nätverksresurser som är länkade till HDInsight-klustret (till exempel det virtuella nätverket, nätverkskortet eller lastbalanseraren). Dessutom kan du inte flytta ett nätverkskort som är kopplat till en virtuell dator för klustret till en ny resursgrupp.
>
> När du flyttar ett HDInsight-kluster till en ny prenumeration flyttar du först andra resurser (till exempel lagringskontot). Flytta sedan HDInsight-klustret på egen hand.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Kluster | Ja | Ja | Inga |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Ja | Ja | Inga |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Maskiner | Ja | Ja | Inga |
> | datorer/tillägg | Ja | Ja | Inga |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | datamanagers | Ja | Ja | Inga |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | Inga | Inga | Inga |
> | vnfs | Inga | Inga | Inga |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Komponenter | Inga | Inga | Inga |
> | networkscopes | Inga | Inga | Inga |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Jobb | Ja | Ja | Inga |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> Se till att flytten till den nya prenumerationen inte överskrider [prenumerationskvoterna](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | **Väntande** | **Väntande** | Nej. [Läs mer](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region). |
> | actiongroups | Ja | Ja | Inga |
> | activitylogalerts | Inga | Inga | Inga |
> | alertrules | Ja | Ja | Inga |
> | autoskalningsinställningar | Ja | Ja | Inga |
> | baslinje | Inga | Inga | Inga |
> | Komponenter | Ja | Ja | Inga |
> | datacollectionrules | Inga | Inga | Inga |
> | diagnosticsettings | Inga | Inga | Inga |
> | diagnosticsettingscategories | Inga | Inga | Inga |
> | eventcategories | Inga | Inga | Inga |
> | eventtypes | Inga | Inga | Inga |
> | extendeddiagnosticsettings | Inga | Inga | Inga |
> | guestdiagnosticsettings | Inga | Inga | Inga |
> | listmigrationdate | Inga | Inga | Inga |
> | logdefinitions | Inga | Inga | Inga |
> | logprofiles | Inga | Inga | Inga |
> | loggar | Inga | Inga | Inga |
> | metricalerts | Inga | Inga | Inga |
> | metricbaselines | Inga | Inga | Inga |
> | metricbatch | Inga | Inga | Inga |
> | metricdefinitions | Inga | Inga | Inga |
> | metricnamespaces | Inga | Inga | Inga |
> | metrics | Inga | Inga | Inga |
> | migratealertrules | Inga | Inga | Inga |
> | migratewpricingmodel | Inga | Inga | Inga |
> | myworkbooks | Inga | Inga | Inga |
> | notificationgroups | Inga | Inga | Inga |
> | privatelinkscopes | Inga | Inga | Inga |
> | rollbacktolegacypricingmodel | Inga | Inga | Inga |
> | scheduledqueryrules | Ja | Ja | Inga |
> | topologi | Inga | Inga | Inga |
> | transaktioner | Inga | Inga | Inga |
> | vminsightsonboardingstatuses | Inga | Inga | Inga |
> | webbtest | Ja | Ja | Inga |
> | webtests/gettestresultfile | Inga | Inga | Inga |
> | arbetsböcker | Ja | Ja | Inga |
> | workbooktemplates | Ja | Ja | Inga |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | apptemplates | Inga | Inga | Inga |
> | iotapps | Ja | Ja | Inga |

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | iothub | **Väntande** | **Väntande** | Ja (klonhubb) <br/><br/> [Klona en IoT-hubb till en annan region](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Diagram | Ja | Ja | Inga |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> Nyckelvalv som används för diskkryptering kan inte flyttas till en resursgrupp i samma prenumeration eller mellan prenumerationer.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | deletedvaults | Inga | Inga | Inga |
> | hsmpools | Inga | Inga | Inga |
> | managedhsms | Inga | Inga | Inga |
> | Valv | Ja | Ja | Inga |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | connectedclusters | Ja | Ja | Inga |
> | registreradeprenumereringar | Inga | Inga | Inga |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | sourcecontrolconfigurations | Inga | Inga | Inga |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Kluster | Ja | Ja | Inga |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | labaccounts | Inga | Inga | Inga |
> | användare | Inga | Inga | Inga |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Inga | Inga | Inga |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Inga | Inga | Nej, det är en global tjänst. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | värdmiljö | Inga | Inga | Inga |
> | integrationaccounts | Ja | Ja | Inga |
> | integrationserviceenvironments | Ja | Inga | Inga |
> | integrationserviceenvironments/managedapis | Ja | Inga | Inga |
> | isolatedenvironments | Inga | Inga | Inga |
> | Arbetsflöden | Ja | Ja | Inga |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | åtagandeplaner | Inga | Inga | Inga |
> | Webservices | Ja | Inga | Inga |
> | arbetsytor | Ja | Ja | Inga |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | operationalizationclusters | Inga | Inga | Inga |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Inga | Inga | Inga |
> | teamaccounts | Inga | Inga | Inga |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Inga | Inga | Inga |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | arbetsytor | Inga | Inga | Inga |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationassignments | Inga | Nej | Ja. [Läs mer](../../virtual-machines/move-region-maintenance-configuration.md) |
> | maintenanceconfigurations | Ja | Ja | Ja. [Läs mer](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | uppdateringar | Inga | Inga | Inga |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Identiteter | Inga | Inga | Inga |
> | userassignedidentities | Inga | Inga | Inga |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | Inga | Inga | Inga |
> | managednetworks/managednetworkgroups | Inga | Inga | Inga |
> | managednetworks/managednetworkpeeringpolicies | Inga | Inga | Inga |
> | avisering | Inga | Inga | Inga |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | marketplaceregistrationdefinitions | Inga | Inga | Inga |
> | registrationassignments | Inga | Inga | Inga |
> | registrationdefinitions | Inga | Inga | Inga |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities | Inga | Inga | Inga |
> | managementgroups | Inga | Inga | Inga |
> | managementgroups/settings | Inga | Inga | Inga |
> | resources | Inga | Inga | Inga |
> | starttenantbackfill | Inga | Inga | Inga |
> | tenantbackfillstatus | Inga | Inga | Inga |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Ja | Ja | Nej, Azure Maps är en geospatial tjänst. |
> | konton/privateatlases | Ja | Ja | Inga |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Erbjuder | Inga | Inga | Inga |
> | offertypes | Inga | Inga | Inga |
> | privategalleryitems | Inga | Inga | Inga |
> | privatestoreclient | Inga | Inga | Inga |
> | privatestores | Inga | Inga | Inga |
> | Produkter | Inga | Inga | Inga |
> | Förlag | Inga | Inga | Inga |
> | registrera | Inga | Inga | Inga |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | Inga | Inga | Inga |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Avtal | Inga | Inga | Inga |
> | offertypes | Inga | Inga | Inga |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | mediaservices | Ja | Ja | Inga |
> | mediaservices/liveevents | Ja | Ja | Inga |
> | mediaservices/streamingendpoints | Ja | Ja | Inga |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | appclusters | Inga | Inga | Inga |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | assessmentprojects | Inga | Inga | Inga |
> | migrateprojects | Inga | Inga | Inga |
> | movecollections | Inga | Inga | Inga |
> | Projekt | Inga | Inga | Inga |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ---------- |
> | holographicsbroadcastaccounts | Inga | Inga | Inga |
> | objectunderstandingaccounts | Inga | Inga | Inga |
> | remoterenderingaccounts | Ja | Ja | Inga |
> | spatialanchorsaccounts | Ja | Ja | Inga |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | netappaccounts | Inga | Inga | Inga |
> | netappaccounts/capacitypools | Inga | Inga | Inga |
> | netappaccounts/capacitypools/volumes | Inga | Inga | Inga |
> | netappaccounts/capacitypools/volumes/mounttargets | Inga | Inga | Inga |
> | netappaccounts/capacitypools/volumes/snapshots | Inga | Inga | Inga |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Se [Vägledning för nätverksflyttning.](./move-limitations/networking-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgateways | Inga | Inga | Inga |
> | applicationgatewaywebapplicationfirewallpolicies | Inga | Inga | Inga |
> | applicationsecuritygroups | Ja | Ja | Inga |
> | azurefirewalls | Inga | Inga | Inga |
> | bastionhosts | Inga | Inga | Inga |
> | bgpservicecommunities | Inga | Inga | Inga |
> | Anslutningar | Ja | Ja | Inga |
> | ddoscustompolicies | Ja | Ja | Inga |
> | ddosprotectionplans | Inga | Inga | Inga |
> | dnszones | Ja | Ja | Inga |
> | expressroutecircuits | Inga | Inga | Inga |
> | expressroutegateways | Inga | Inga | Inga |
> | expressrouteserviceproviders | Inga | Inga | Inga |
> | firewallpolicies | Ja | Ja | Inga |
> | frontdoors | Inga | Inga | Inga |
> | ipallocations | Ja | Ja | Inga |
> | ipgroups | Ja | Ja | Inga |
> | lastbalanserare | Ja – Grundläggande SKU<br> Ja – Standard-SKU | Ja – Grundläggande SKU<br>Nej – Standard-SKU | Yes <br/><br/> Använd [Azure Resource Mover för](../../resource-mover/tutorial-move-region-virtual-machines.md) att flytta interna och externa lastbalanserare. |
> | localnetworkgateways | Ja | Ja | Inga |
> | natgateways | Inga | Inga | Inga |
> | networkexperimentprofiles | Inga | Inga | Inga |
> | networkintentpolicies | Ja | Ja | Inga |
> | networkinterfaces | Ja | Ja | Ja <br/><br/> Använd [Azure Resource Mover för](../../resource-mover/tutorial-move-region-virtual-machines.md) att flytta nätverkskort. |
> | networkprofiles | Inga | Inga | Inga |
> | networksecuritygroups | Ja | Ja | Ja <br/><br/> Använd [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta nätverkssäkerhetsgrupper (NGS). |
> | networkwatchers | Inga | Inga | Inga |
> | networkwatchers/connectionmonitors | Ja | Inga | Inga |
> | networkwatchers/flowlogs | Ja | Inga | Inga |
> | networkwatchers/pingmeshes | Ja | Inga | Inga |
> | p2svpngateways | Inga | Inga | Inga |
> | privatednszones | Ja | Ja | Inga |
> | privatednszones/virtualnetworklinks | Ja | Ja | Inga |
> | privatednszonesinternal | Inga | Inga | Inga |
> | privateendpointredirectmaps | Inga | Inga | Inga |
> | privateendpoints | Inga | Inga | Inga |
> | privatelinkservices | Inga | Inga | Inga |
> | publicipaddresses | Ja – Grundläggande SKU<br>Ja – Standard-SKU | Ja – Grundläggande SKU<br>Nej – Standard-SKU | Yes<br/><br/> Använd [Azure Resource Mover för](../../resource-mover/tutorial-move-region-virtual-machines.md) att flytta offentliga IP-adresser. |
> | publicipprefixes | Ja | Ja | Inga |
> | routefilters | Inga | Inga | Inga |
> | routetables | Ja | Ja | Inga |
> | securitypartnerproviders | Ja | Ja | Inga |
> | serviceendpointpolicies | Ja | Ja | Inga |
> | trafficmanagergeographichierarchies | Inga | Inga | Inga |
> | trafficmanagerprofiles | Ja | Ja | Inga |
> | trafficmanagerprofiles/heatmaps | Inga | Inga | Inga |
> | trafficmanagerusermetricskeys | Inga | Inga | Inga |
> | virtualhubs | Inga | Inga | Inga |
> | virtualnetworkgateways | Ja | Ja | Inga |
> | virtualnetworks | Ja | Ja | Inga |
> | virtualnetworktaps | Inga | Inga | Inga |
> | virtualrouters | Ja | Ja | Inga |
> | virtualwans | Inga | Inga |
> | vpngateways (Virtual WAN) | Inga | Inga | Inga |
> | vpnserverkonfigurationer | Inga | Inga | Inga |
> | vpnsites (Virtual WAN) | Inga | Inga | Inga |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Namnområden | Ja | Ja | Inga |
> | namespaces/notificationhubs | Ja | Ja | Inga |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamespaces | Ja | Ja | Inga |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | hypervsites | Inga | Inga | Inga |
> | importerites | Inga | Inga | Inga |
> | serversites | Inga | Inga | Inga |
> | vmwaresites | Inga | Inga | Inga |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Se till att flytten till en ny prenumeration inte överskrider [prenumerationskvoterna](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Arbetsytor som har ett länkat Automation-konto kan inte flyttas. Innan du påbörjar en flyttåtgärd måste du ta bort länken till eventuella Automation-konton.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Kluster | Inga | Inga | Inga |
> | deletedworkspaces | Inga | Inga | Inga |
> | linktargets | Inga | Inga | Inga |
> | storageinsightconfigs | Inga | Inga | Inga |
> | arbetsytor | Ja | Ja | Inga |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | hanteringsassociationer | Inga | Inga | Inga |
> | hanteringskonfigurationer | Ja | Ja | Inga |
> | lösningar | Ja | Ja | Inga |
> | Visningar | Ja | Ja | Inga |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | legacypeerings | Inga | Inga | Inga |
> | peerasns | Inga | Inga | Inga |
> | peeringlocations | Inga | Inga | Inga |
> | peerings | Inga | Inga | Inga |
> | peeringservicecountries | Inga | Inga | Inga |
> | peeringservicelocations | Inga | Inga | Inga |
> | peeringserviceproviders | Inga | Inga | Inga |
> | peeringservices | Inga | Inga | Inga |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | policyevents | Inga | Inga | Inga |
> | policystates | Inga | Inga | Inga |
> | policytrackedresources | Inga | Inga | Inga |
> | åtgärder | Inga | Inga | Inga |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> |  -konsoler | Inga | Inga | Inga |
> | instrumentpaneler | Ja | Ja | Inga |
> | usersettings | Inga | Inga | Inga |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | workspacecollections | Ja | Ja | Inga |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Kapacitet | Ja | Ja | Inga |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ---------- |
> | konton | Inga | Inga | Inga |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ---------- |
> | konton | **Väntande** | **Väntande** | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | availableaccounts | Inga | Inga | Inga |
> | providerregistrations | Inga | Inga | Inga |
> | Utbyggnader | Inga | Inga | Inga |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | arbetsytor | Inga | Inga | Inga |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Se [Vägledning för Recovery Services-flytt.](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | replicationeligibilityresults | Inga | Inga | Inga |
> | Valv | Ja | Ja | Nej.<br/><br/> Det går inte att flytta Recovery Services Azure Backup mellan olika Azure-regioner.<br/><br/> I Recovery Services-valv Azure Site Recovery du [inaktivera och återskapa valvet](../../site-recovery/move-vaults-across-regions.md) i målregionen. |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | openshiftclusters | Inga | Inga | Inga |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Namnområden | Ja | Ja | Inga |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Frågor | Ja | Ja | Inga |
> | resourcechangedetails | Inga | Inga | Inga |
> | resourcechanges | Inga | Inga | Inga |
> | resources | Inga | Inga | Inga |
> | resourceshistory | Inga | Inga | Inga |
> | subscriptionsstatus | Inga | Inga | Inga |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | childresources | Inga | Inga | Inga |
> | emergingissues | Inga | Inga | Inga |
> | händelser | Inga | Inga | Inga |
> | metadata | Inga | Inga | Inga |
> | meddelanden | Inga | Inga | Inga |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Distributioner | Inga | Inga | Inga |
> | deploymentscripts | Inga | Inga | Ja<br/><br/>[Flytta Microsoft.Resources-resurser till en ny region](microsoft-resources-move-regions.md) |
> | deploymentscripts/logs | Inga | Inga | Inga |
> | Länkar | Inga | Inga | Inga |
> | Leverantörer | Inga | Inga | Inga |
> | resourcegroups | Inga | Inga | Inga |
> | resources | Inga | Inga | Inga |
> | Prenumerationer | Inga | Inga | Inga |
> | tags | Inga | Inga | Inga |
> | templatespecs | Inga | Inga | Ja<br/><br/>[Flytta Microsoft.Resources-resurser till en ny region](microsoft-resources-move-regions.md) |
> | templatespecs/versions | Inga | Inga | Inga |
> | Hyresgäster | Inga | Inga | Inga |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | program | Ja | Inga | Inga |
> | saasresources | Inga | Inga | Inga |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> Du kan inte flytta flera Sök-resurser i olika regioner i samma åtgärd. Flytta dem i stället i separata åtgärder.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | Inga | Inga | Inga |
> | searchservices | Ja | Ja | Inga |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptivenetworkhardenings | Inga | Inga | Inga |
> | advancedthreatprotectionsettings | Inga | Inga | Inga |
> | aviseringar | Inga | Inga | Inga |
> | allowedconnections | Inga | Inga | Inga |
> | applicationwhitelistings | Inga | Inga | Inga |
> | assessmentmetadata | Inga | Inga | Inga |
> | utvärderingar | Inga | Inga | Inga |
> | autodismissalertsrules | Inga | Inga | Inga |
> | automatiseringar | Ja | Ja | Inga |
> | inställningar för automatisk etablering | Inga | Inga | Inga |
> | complianceresults | Inga | Inga | Inga |
> | efterlevnad | Inga | Inga | Inga |
> | datacollectionagents | Inga | Inga | Inga |
> | devicesecuritygroups | Inga | Inga | Inga |
> | discoveredsecuritysolutions | Inga | Inga | Inga |
> | externalsecuritysolutions | Inga | Inga | Inga |
> | informationprotectionpolicies | Inga | Inga | Inga |
> | iotsecuritysolutions | Ja | Ja | Inga |
> | iotsecuritysolutions/analyticsmodels | Inga | Inga | Inga |
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | Inga | Inga | Inga |
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | Inga | Inga | Inga |
> | jitnetworkaccesspolicies | Inga | Inga | Inga |
> | policies | Inga | Inga | Inga |
> | priser | Inga | Inga | Inga |
> | regler för efterlevnadstandarder | Inga | Inga | Inga |
> | regulatorycompliancestandards/regulatorycompliancecontrols | Inga | Inga | Inga |
> | regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments | Inga | Inga | Inga |
> | säkerhetskontakter | Inga | Inga | Inga |
> | securitysolutions | Inga | Inga | Inga |
> | securitysolutionsreferencedata | Inga | Inga | Inga |
> | securitystatuses | Inga | Inga | Inga |
> | securitystatusessummaries | Inga | Inga | Inga |
> | servervulnerabilityassessments | Inga | Inga | Inga |
> | inställningar | Inga | Inga | Inga |
> | undervärderingar | Inga | Inga | Inga |
> | uppgifter | Inga | Inga | Inga |
> | Topologier | Inga | Inga | Inga |
> | workspacesettings | Inga | Inga | Inga |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Aggregeringar | Inga | Inga | Inga |
> | alertrules | Inga | Inga | Inga |
> | alertruletemplates | Inga | Inga | Inga |
> | automationrules | Inga | Inga | Inga |
> | bokmärken | Inga | Inga | Inga |
> | Fall | Inga | Inga | Inga |
> | dataanslutning | Inga | Inga | Inga |
> | Enheter | Inga | Inga | Inga |
> | entityqueries | Inga | Inga | Inga |
> | incidenter | Inga | Inga | Inga |
> | officeconsents | Inga | Inga | Inga |
> | inställningar | Inga | Inga | Inga |
> | hotintelligence | Inga | Inga | Inga |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | consoleservices | Inga | Inga | Inga |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Gateways | Inga | Inga | Inga |
> | Noder | Inga | Inga | Inga |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Namnområden | Ja | Ja | Inga |
> | premiummessagingregions | Inga | Inga | Inga |
> | sku | Inga | Inga | Inga |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | program | Inga | Inga | Inga |
> | Kluster | Ja | Ja | Inga |
> | containergroups | Inga | Inga | Inga |
> | containergroupsets | Inga | Inga | Inga |
> | edgeclusters | Inga | Inga | Inga |
> | managedclusters | Inga | Inga | Inga |
> | Nätverk | Inga | Inga | Inga |
> | secretstores | Inga | Inga | Inga |
> | volumes | Inga | Inga | Inga |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | program | Ja | Ja | Inga |
> | containergroups | Inga | Inga | Inga |
> | Gateways | Ja | Ja | Inga |
> | Nätverk | Ja | Ja | Inga |
> | secrets | Ja | Ja | Inga |
> | volumes | Ja | Ja | Inga |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Utbyggnader | Inga | Inga | Inga |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | signalr | Ja | Ja | Inga |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | hybridusebenefits | Inga | Inga | Inga |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationdefinitions | Inga | Inga | Inga |
> | program | Inga | Inga | Inga |
> | jitrequests | Inga | Inga | Inga |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> En databas och server måste finnas i samma resursgrupp. När du flyttar en SQL-server flyttas även alla dess databaser. Det här beteendet gäller för Azure SQL Database och Azure Synapse Analytics databaser.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | instancepools | Inga | Inga | Inga |
> | platser | Ja | Ja | Inga |
> | managedinstances | Inga | Inga | Ja <br/><br/> [Läs mer](../../azure-sql/database/move-resources-across-regions.md) om att flytta hanterade instanser mellan regioner. |
> | managedinstances/databases | Inga | Inga | Ja |
> | Servrar | Ja | Ja |Ja |
> | servrar/databaser | Ja | Ja | Ja <br/><br/> [Läs mer](../../azure-sql/database/move-resources-across-regions.md) om att flytta databaser mellan regioner.<br/><br/> [Läs mer](../../resource-mover/tutorial-move-region-sql.md) om hur du Azure Resource Mover för att Azure SQL databaser.  |
> | servrar/databaser/backuplongtermretentionpolicies | Ja | Ja | Inga |
> | servrar/elasticpools | Ja | Ja | Ja <br/><br/> [Läs mer om](../../azure-sql/database/move-resources-across-regions.md) att flytta elastiska pooler mellan regioner.<br/><br/> [Läs mer om](../../resource-mover/tutorial-move-region-sql.md) att använda Azure Resource Mover för att flytta Azure SQL elastiska pooler.  |
> | servrar/jobbkonto | Ja | Ja | Inga |
> | servrar/jobagents | Ja | Ja | Inga |
> | virtualclusters | Ja | Ja | Ja |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | sqlvirtualmachinegroups | Ja | Ja | Inga |
> | sqlvirtualmachines | Ja | Ja | Inga |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | storageaccounts | Ja | Ja | Ja<br/><br/> [Flytta ett Azure Storage till en annan region](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Cachar | Inga | Inga | Inga |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Ja | Ja | Inga |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Inga | Inga | Inga |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Inga | Inga | Inga |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Chefer | Inga | Inga | Inga |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Stream Analytics jobb kan inte flyttas i körningstillstånd.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Kluster | Inga | Inga | Inga |
> | streamingjobs | Ja | Ja | Inga |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Miljöer | Inga | Inga | Inga |
> | Instanser | Inga | Inga | Inga |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Prenumerationer | Inga | Inga | Inga |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Inga | Inga | Inga |
> | supporttickets | Inga | Inga | Inga |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | arbetsytor | Inga | Inga | Inga |
> | arbetsytor/bigdatapools | Inga | Inga | Inga |
> | arbetsytor/sqlpools | Inga | Inga | Inga |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Miljöer | Ja | Ja | Inga |
> | miljöer/eventsources | Ja | Ja | Inga |
> | miljöer/referencedatasets | Ja | Ja | Inga |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Butiker | Ja | Ja | Inga |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | imagetemplates | Inga | Inga | Inga |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!IMPORTANT]
> Om du vill ändra prenumerationen för Azure DevOps kan du läsa [ändra azure-prenumerationen som används för fakturering.](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konto | Inga | Inga | Inga |
> | konto/tillägg | Inga | Inga | Inga |
> | konto/projekt | Inga | Inga | Inga |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones | Inga | Inga | Inga |
> | resurspooler | Inga | Inga | Inga |
> | vcenters | Inga | Inga | Inga |
> | virtualmachines | Inga | Inga | Inga |
> | virtualmachinetemplates | Inga | Inga | Inga |
> | virtualnetworks | Inga | Inga | Inga |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | dedikerade molnnoder | Inga | Inga | Inga |
> | dedikerade molntjänster | Inga | Inga | Inga |
> | virtualmachines | Inga | Inga | Inga |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | Inga | Inga | Inga |
> | vnfs | Inga | Inga | Inga |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | konton | Inga | Inga | Inga |
> | Planer | Inga | Inga | Inga |
> | registeredsubscriptions | Inga | Inga | Inga |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Se [App Service vägledning för flytt.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | availablestacks | Inga | Inga | Inga |
> | billingmeters | Inga | Inga | Inga |
> | certifikat | Inga | Ja | Inga |
> | connectiongateways | Ja | Ja | Inga |
> | Anslutningar | Ja | Ja | Inga |
> | customapis | Ja | Ja | Inga |
> | deletedsites | Inga | Inga | Inga |
> | deploymentlocations | Inga | Inga | Inga |
> | georegioner | Inga | Inga | Inga |
> | hostingenvironments | Inga | Inga | Inga |
> | kubeenvironments | Ja | Ja | Inga |
> | publishingusers | Inga | Inga | Inga |
> | rekommendationer | Inga | Inga | Inga |
> | resourcehealthmetadata | Inga | Inga | Inga |
> | Runtimes | Inga | Inga | Inga |
> | serverfarms | Ja | Ja | Inga |
> | serverfarms/eventgridfilters | Inga | Inga | Inga |
> | webbplatser | Ja | Ja | Inga |
> | webbplatser/premieraddons | Ja | Ja | Inga |
> | platser/platser | Ja | Ja | Inga |
> | källkontroller | Inga | Inga | Inga |
> | staticsites | Inga | Inga | Inga |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | multipleactivationkeys | Inga | Inga | Inga |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | deviceservices | Inga | Inga | Inga |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Arbetsbelastningar | Inga | Inga | Inga |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration | Flytt av region |
> | ------------- | ----------- | ---------- | ----------- |
> | Komponenter | Inga | Inga | Inga |
> | componentssummary | Inga | Inga | Inga |
> | monitorinstances | Inga | Inga | Inga |
> | monitorinstancessummary | Inga | Inga | Inga |
> | Bildskärmar | Inga | Inga | Inga |

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flyttåtgärden.

## <a name="next-steps"></a>Nästa steg

- Kommandon för att flytta resurser finns i [Flytta resurser till en ny resursgrupp eller prenumeration.](move-resource-group-and-subscription.md)
- [Läs mer](../../resource-mover/overview.md) om tjänsten Resource Mover.
- Om du vill hämta samma data som en fil med kommaavgränsade värden laddar du [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
