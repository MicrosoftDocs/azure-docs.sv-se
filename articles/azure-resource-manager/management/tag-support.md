---
title: Tagga stöd för resurser
description: Visar vilka Azure-resurstyper som stöder taggar. Innehåller information om alla Azure-tjänster.
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: b196cae267a8d7dc878f055f6b2d70a3ff6f9313
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773971"
---
# <a name="tag-support-for-azure-resources"></a>Tagga stöd för Azure-resurser
I den här artikeln beskrivs om en resurstyp stöder [taggar](tag-resources.md). Kolumnen med etiketten **Stöder taggar anger** om resurstypen har en egenskap för taggen. Kolumnen med etiketten **Tagg i kostnadsrapport anger** om den resurstypen skickar taggen till kostnadsrapporten. Du kan visa kostnader efter taggar i [Cost Management kostnadsanalys och](../../cost-management-billing/costs/group-filter.md) [Azure-faktureringsfakturan och information om daglig användning.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Om du vill hämta samma data som en fil med kommaavgränsade värden laddar du [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Gå till ett resursprovidernamnområde:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
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
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Sint](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
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
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
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
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ja | Ja |
> | DomainServices/oucontainer | Inga | Inga |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | supportProviders | Inga | Inga |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Inga | Inga |
> | addsservices | Inga | Inga |
> | Agenter | Inga | Inga |
> | anonymousapiusers | Inga | Inga |
> | konfiguration | Inga | Inga |
> | loggar | Inga | Inga |
> | rapporter | Inga | Inga |
> | servicehealthmetrics | Inga | Inga |
> | services | Inga | Inga |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | advisorScore | Inga | Inga |
> | Konfigurationer | Inga | Inga |
> | generateRecommendations | Inga | Inga |
> | metadata | Inga | Inga |
> | rekommendationer | Inga | Inga |
> | undertryckningar | Inga | Inga |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | farmBeats | Ja | Ja |
> | farmBeats/eventGridFilters | Inga | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | actionRules | Ja | Ja |
> | aviseringar | Inga | Inga |
> | alertsList | Inga | Inga |
> | alertsMetaData | Inga | Inga |
> | alertsSummary | Inga | Inga |
> | alertsSummaryList | Inga | Inga |
> | migrateFromSmartDetection | Inga | Inga |
> | resourceHealthAlertRules | Ja | Ja |
> | smartDetectorAlertRules | Ja | Ja |
> | smartGroups | Inga | Inga |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Servrar | Ja | Ja |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kluster | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | deletedServices | Inga | Inga |
> | getDomainOwnershipIdentifier | Inga | Inga |
> | reportFeedback | Inga | Inga |
> | tjänst | Ja | Ja |
> | validateServiceName | Inga | Inga |

> [!NOTE]
> Azure API Management har endast stöd för att skapa högst 15 taggnamn/taggvärdepar för varje tjänst.

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | migrateProjects | Ja | Ja |
> | migrateProjects/assessments | Inga | Inga |
> | migrateProjects/assessments/assessedApplications | Inga | Inga |
> | migrateProjects/assessments/assessedApplications/machines | Inga | Inga |
> | migrateProjects/assessments/assessedMachines | Inga | Inga |
> | migrateProjects/assessments/assessedMachines/applications | Inga | Inga |
> | migrateProjects/assessments/machinesToAssess | Inga | Inga |
> | migrateProjects/sites | Inga | Inga |
> | migrateProjects/sites/applianceConfigurations | Inga | Inga |
> | migrateProjects/sites/machines | Inga | Inga |
> | osVersions | Inga | Inga |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Inga |
> | configurationStores/eventGridFilters | Inga | Inga |
> | configurationStores/keyValues | Inga | Inga |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Spring | Ja | Ja |
> | Spring/appar | Inga | Inga |
> | Spring/appar/distributioner | Inga | Inga |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Ja | Ja |
> | defaultProviders | Inga | Inga |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | Inga | Inga |
> | accessReviewScheduleSettings | Inga | Inga |
> | classicAdministrators | Inga | Inga |
> | dataAliases | Inga | Inga |
> | dataPolicyManifests | Inga | Inga |
> | denyAssignments | Inga | Inga |
> | elevateAccess | Inga | Inga |
> | findOrphanRoleAssignments | Inga | Inga |
> | Lås | Inga | Inga |
> | behörigheter | Inga | Inga |
> | policyAssignments | Inga | Inga |
> | policyDefinitions | Inga | Inga |
> | policyExemptions | Inga | Inga |
> | policySetDefinitions | Inga | Inga |
> | privateLinkAssociations | Inga | Inga |
> | providerOperations | Inga | Inga |
> | resourceManagementPrivateLinks | Ja | Ja |
> | roleAssignmentApprovals | Inga | Inga |
> | roleAssignments | Inga | Inga |
> | roleAssignmentScheduleInstances | Inga | Inga |
> | roleAssignmentScheduleRequests | Inga | Inga |
> | roleAssignmentSchedules | Inga | Inga |
> | roleAssignmentsUsageMetrics | Inga | Inga |
> | roleDefinitions | Inga | Inga |
> | roleEligibilityScheduleInstances | Inga | Inga |
> | roleEligibilityScheduleRequests | Inga | Inga |
> | roleEligibilitySchedules | Inga | Inga |
> | roleManagementPolicies | Inga | Inga |
> | roleManagementPolicyAssignments | Inga | Inga |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | configurationProfileAssignments | Inga | Inga |
> | configurationProfilePreferences | Ja | Ja |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ja | Ja |
> | automationAccounts/configurations | Ja | Ja |
> | automationKonto/jobb | Inga | Inga |
> | automationAccounts/privateEndpointConnectionProxies | Inga | Inga |
> | automationAccounts/privateEndpointConnections | Inga | Inga |
> | automationAccounts/privateLinkResources | Inga | Inga |
> | automationAccounts/runbooks | Ja | Ja |
> | automationAccounts/softwareUpdateConfigurations | Inga | Inga |
> | automationAccounts/webhooks | Inga | Inga |

> [!NOTE]
> Azure Automation har endast stöd för att skapa högst 15 taggnamn/taggvärdepar för varje Automation-resurs.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | privateClouds | Ja | Ja |
> | privateClouds/addons | Inga | Inga |
> | privateClouds/auktoriseringar | Inga | Inga |
> | privateClouds/cloudLinks | Inga | Inga |
> | privateClouds/clusters | Inga | Inga |
> | privateClouds/clusters/datastores | Inga | Inga |
> | privateClouds/globalReachConnections | Inga | Inga |
> | privateClouds/hcxEnterpriseSites | Inga | Inga |
> | privateClouds/scriptExecutions | Inga | Inga |
> | privateClouds/scriptPackages | Inga | Inga |
> | privateClouds/scriptPackages/scriptCmdlets | Inga | Inga |
> | privateClouds/workloadNetworks | Inga | Inga |
> | privateClouds/workloadNetworks/dhcpConfigurations | Inga | Inga |
> | privateClouds/workloadNetworks/dnsServices | Inga | Inga |
> | privateClouds/workloadNetworks/dnsZones | Inga | Inga |
> | privateClouds/workloadNetworks/gateways | Inga | Inga |
> | privateClouds/workloadNetworks/portMirroringProfiles | Inga | Inga |
> | privateClouds/workloadNetworks/segment | Inga | Inga |
> | privateClouds/workloadNetworks/virtualMachines | Inga | Inga |
> | privateClouds/workloadNetworks/vmGroups | Inga | Inga |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Miljöer | Inga | Inga |
> | miljöer/konton | Inga | Inga |
> | miljöer/konton/namnområden | Inga | Inga |
> | miljöer/konton/namnrymder/konfigurationer | Inga | Inga |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Ja | Inga |
> | b2ctenants | Inga | Inga |
> | guestUsages | Ja | Ja |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | dataControllers | Ja | Ja |
> | dataWarehouseInstances | Ja | Ja |
> | postgresInstances | Ja | Ja |
> | sqlManagedInstances | Ja | Ja |
> | sqlServerInstances | Ja | Ja |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | autopilotEnvironments | Ja | Ja |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | sqlServerRegistrations | Ja | Ja |
> | sqlServerRegistrations/sqlServers | Inga | Inga |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kataloger | Ja | Ja |
> | kataloger/produkter | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Inga | Inga |
> | edgeSubscriptions | Ja | Ja |
> | linkedSubscriptions | Ja | Ja |
> | Registreringar | Ja | Ja |
> | registreringar/customerSubscriptions | Inga | Inga |
> | registreringar/produkter | Inga | Inga |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kluster | Ja | Ja |
> | galleryImages | Ja | Ja |
> | networkInterfaces | Ja | Ja |
> | virtualHardDisks | Ja | Ja |
> | virtualMachines | Ja | Ja |
> | virtualNetworks | Ja | Ja |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Ja | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ja | Ja |
> | batchAccounts/certificates | Inga | Inga |
> | batchAccounts/pools | Inga | Inga |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Inga | Inga |
> | billingAccounts/agreements | Inga | Inga |
> | billingAccounts/billingPermissions | Inga | Inga |
> | billingAccounts/billingProfiles | Inga | Inga |
> | billingAccounts/billingProfiles/billingPermissions | Inga | Inga |
> | billingAccounts/billingProfiles/billingRoleAssignments | Inga | Inga |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Inga | Inga |
> | billingAccounts/billingProfiles/billingSubscriptions | Inga | Inga |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Inga | Inga |
> | billingAccounts/billingProfiles/customers | Inga | Inga |
> | billingAccounts/billingProfiles/instructions | Inga | Inga |
> | billingAccounts/billingProfiles/invoices | Inga | Inga |
> | billingAccounts/billingProfiles/invoices/pricesheet | Inga | Inga |
> | billingAccounts/billingProfiles/invoices/transactions | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/products | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/transactions | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/validateDeleteInvoiceSectionEligibility | Inga | Inga |
> | billingAccounts/BillingProfiles/patchOperations | Inga | Inga |
> | billingAccounts/billingProfiles/paymentMethods | Inga | Inga |
> | billingAccounts/billingProfiles/policies | Inga | Inga |
> | billingAccounts/billingProfiles/pricesheet | Inga | Inga |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Inga | Inga |
> | billingAccounts/billingProfiles/products | Inga | Inga |
> | billingAccounts/billingProfiles/reservations | Inga | Inga |
> | billingAccounts/billingProfiles/transactions | Inga | Inga |
> | billingAccounts/billingProfiles/validateDeleteBillingProfileEligibility | Inga | Inga |
> | billingAccounts/billingProfiles/validateDetachPaymentMethodEligibility | Inga | Inga |
> | billingAccounts/billingRoleAssignments | Inga | Inga |
> | billingAccounts/billingRoleDefinitions | Inga | Inga |
> | billingAccounts/billingSubscriptions | Inga | Inga |
> | billingAccounts/billingSubscriptions/elevateRole | Inga | Inga |
> | billingAccounts/billingSubscriptions/invoices | Inga | Inga |
> | billingAccounts/createBillingRoleAssignment | Inga | Inga |
> | billingAccounts/createInvoiceSectionOperations | Inga | Inga |
> | billingAccounts/customers | Inga | Inga |
> | billingAccounts/customers/billingPermissions | Inga | Inga |
> | billingAccounts/customers/billingSubscriptions | Inga | Inga |
> | billingAccounts/customers/initiateTransfer | Inga | Inga |
> | billingAccounts/customers/policies | Inga | Inga |
> | billingAccounts/customers/products | Inga | Inga |
> | billingAccounts/customers/transactions | Inga | Inga |
> | billingAccounts/customers/transfers | Inga | Inga |
> | billingAccounts/departments | Inga | Inga |
> | billingAccounts/departments/billingPermissions | Inga | Inga |
> | billingAccounts/departments/billingRoleAssignments | Inga | Inga |
> | billingAccounts/departments/billingRoleDefinitions | Inga | Inga |
> | billingAccounts/departments/billingSubscriptions | Inga | Inga |
> | billingAccounts/enrollmentAccounts | Inga | Inga |
> | billingAccounts/enrollmentAccounts/billingPermissions | Inga | Inga |
> | billingAccounts/enrollmentAccounts/billingRoleAssignments | Inga | Inga |
> | billingAccounts/enrollmentAccounts/billingRoleDefinitions | Inga | Inga |
> | billingAccounts/enrollmentAccounts/billingSubscriptions | Inga | Inga |
> | billingAccounts/invoices | Inga | Inga |
> | billingAccounts/invoices/transactions | Inga | Inga |
> | billingAccounts/invoices/transactionSummary | Inga | Inga |
> | billingAccounts/invoiceAvsnitt | Inga | Inga |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Inga | Inga |
> | billingAccounts/invoiceSections/billingSubscriptions | Inga | Inga |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Inga | Inga |
> | billingAccounts/invoiceSections/elevate | Inga | Inga |
> | billingAccounts/invoiceSections/initiateTransfer | Inga | Inga |
> | billingAccounts/invoiceSections/patchOperations | Inga | Inga |
> | billingAccounts/invoiceSections/productMoveOperations | Inga | Inga |
> | billingAccounts/invoiceSections/products | Inga | Inga |
> | billingAccounts/invoiceAvsnitt/produkter/överföring | Inga | Inga |
> | billingAccounts/invoiceAvsnitt/produkter/updateAutoRenew | Inga | Inga |
> | billingAccounts/invoiceAvsnitt/transaktioner | Inga | Inga |
> | billingAccounts/invoiceAvsnitt/överföringar | Inga | Inga |
> | billingAccounts/lineOfCredit | Inga | Inga |
> | billingAccounts/patchOperations | Inga | Inga |
> | billingAccounts/payableOverage | Inga | Inga |
> | billingAccounts/paymentMethods | Inga | Inga |
> | billingAccounts/payNow | Inga | Inga |
> | billingAccounts/products | Inga | Inga |
> | billingAccounts/reservations | Inga | Inga |
> | billingAccounts/transactions | Inga | Inga |
> | billingPeriods | Inga | Inga |
> | billingPermissions | Inga | Inga |
> | billingProperty | Inga | Inga |
> | billingRoleAssignments | Inga | Inga |
> | billingRoleDefinitions | Inga | Inga |
> | createBillingRoleAssignment | Inga | Inga |
> | Avdelningar | Inga | Inga |
> | enrollmentAccounts | Inga | Inga |
> | Fakturor | Inga | Inga |
> | Kampanjer | Inga | Inga |
> | Överföringar | Inga | Inga |
> | transfers/acceptTransfer | Inga | Inga |
> | transfers/declineTransfer | Inga | Inga |
> | transfers/operationStatus | Inga | Inga |
> | transfers/validateTransfer | Inga | Inga |
> | validateAddress | Inga | Inga |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | mapApis | Ja | Ja |
> | updateCommunicationPreference | Inga | Inga |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Ja | Ja |
> | cordaMembers | Ja | Ja |
> | Watchers | Ja | Ja |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | TokenServices | Ja | Ja |
> | TokenServices/BlockchainNetworks | Inga | Inga |
> | TokenServices/Grupper | Inga | Inga |
> | TokenTjänster/grupper/konton | Inga | Inga |
> | TokenServices/TokenTemplates | Inga | Inga |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Inga | Inga |
> | blueprintAssignments/assignmentOperations | Inga | Inga |
> | blueprintAssignments/operations | Inga | Inga |
> | Ritningar | Inga | Inga |
> | skisser/artefakter | Inga | Inga |
> | skisser/versioner | Inga | Inga |
> | skisser/versioner/artefakter | Inga | Inga |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | botServices | Ja | Ja |
> | botServices/channels | Inga | Inga |
> | botServices/connections | Inga | Inga |
> | hostSettings | Inga | Inga |
> | språk | Inga | Inga |
> | mallar | Inga | Inga |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Redis | Ja | Ja |
> | Redis/EventGridFilters | Inga | Inga |
> | Redis/privateEndpointConnectionProxies | Inga | Inga |
> | Redis/privateEndpointConnectionProxies/validate | Inga | Inga |
> | Redis/privateEndpointConnections | Inga | Inga |
> | Redis/privateLinkResources | Inga | Inga |
> | redisEnterprise | Ja | Ja |
> | redisEnterprise/databases | Inga | Inga |
> | RedisEnterprise/privateEndpointConnectionProxies | Inga | Inga |
> | RedisEnterprise/privateEndpointConnectionProxies/validate | Inga | Inga |
> | RedisEnterprise/privateEndpointConnections | Inga | Inga |
> | RedisEnterprise/privateLinkResources | Inga | Inga |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Inga | Inga |
> | autoQuotaIncrease | Inga | Inga |
> | calculateExchange | Inga | Inga |
> | calculatePrice | Inga | Inga |
> | calculatePurchasePrice | Inga | Inga |
> | Kataloger | Inga | Inga |
> | commercialReservationOrders | Inga | Inga |
> | Exchange | Inga | Inga |
> | ownReservations | Inga | Inga |
> | placePurchaseOrder | Inga | Inga |
> | reservationOrders | Inga | Inga |
> | reservationOrders/calculateRenot | Inga | Inga |
> | reservationOrders/merge | Inga | Inga |
> | reservationOrders/reservationer | Inga | Inga |
> | reservationOrders/reservationer/revisioner | Inga | Inga |
> | reservationOrders/return | Inga | Inga |
> | reservationOrders/split | Inga | Inga |
> | reservationOrders/swap | Inga | Inga |
> | Reservationer | Inga | Inga |
> | resourceProviders | Inga | Inga |
> | resources | Inga | Inga |
> | validateReservationOrder | Inga | Inga |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | webbplatser | Ja | Ja |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Inga | Inga |
> | CdnWebApplicationFirewallPolicies | Ja | Ja |
> | kantnoder | Inga | Inga |
> | Profiler | Ja | Ja |
> | profiler/afdendpoints | Ja | Ja |
> | profiler/afdendpoints/routes | Inga | Inga |
> | profiler/anpassadedomäner | Inga | Inga |
> | profiler/slutpunkter | Ja | Ja |
> | profiler/slutpunkter/anpassadedomäner | Inga | Inga |
> | profiler/slutpunkter/origingroups | Inga | Inga |
> | profiler/slutpunkter/ursprung | Inga | Inga |
> | profiler/origingroups | Inga | Inga |
> | profiler/origingroups/origins | Inga | Inga |
> | profiler/regeluppsättningar | Inga | Inga |
> | profiler/regeluppsättningar/regler | Inga | Inga |
> | profiler/hemligheter | Inga | Inga |
> | profiler/säkerhetpolicies | Inga | Inga |
> | validateProbe | Inga | Inga |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ja | Ja |
> | certificateOrders/certificates | Inga | Inga |
> | validateCertificateRegistrationInformation | Inga | Inga |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Ändringar | Inga | Inga |
> | profil | Inga | Inga |
> | resourceChanges | Inga | Inga |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | funktioner | Inga | Inga |
> | domainNames | Inga | Inga |
> | domainNames/capabilities | Inga | Inga |
> | domainNames/internalLoadBalancers | Inga | Inga |
> | domainNames/serviceCertificates | Inga | Inga |
> | domainNames/slots | Inga | Inga |
> | domainNames/slots/roles | Inga | Inga |
> | domainNames/slots/roles/metricDefinitions | Inga | Inga |
> | domainNames/slots/roles/metrics | Inga | Inga |
> | moveSubscriptionResources | Inga | Inga |
> | operatingSystemFamilies | Inga | Inga |
> | operatingSystems | Inga | Inga |
> | quotas | Inga | Inga |
> | resourceTypes | Inga | Inga |
> | validateSubscriptionMoveAvailability | Inga | Inga |
> | virtualMachines | Inga | Inga |
> | virtualMachines/diagnosticSettings | Inga | Inga |
> | virtualMachines/metricDefinitions | Inga | Inga |
> | virtualMachines/metrics | Inga | Inga |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Inga | Inga |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | funktioner | Inga | Inga |
> | expressRouteCrossConnections | Inga | Inga |
> | expressRouteCrossConnections/peerings | Inga | Inga |
> | gatewaySupportedDevices | Inga | Inga |
> | networkSecurityGroups | Inga | Inga |
> | quotas | Inga | Inga |
> | reservedIps | Inga | Inga |
> | virtualNetworks | Inga | Inga |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Inga | Inga |
> | virtualNetworks/virtualNetworkPeerings | Inga | Inga |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | funktioner | Inga | Inga |
> | Diskar | Inga | Inga |
> | images | Inga | Inga |
> | osImages | Inga | Inga |
> | osPlatformImages | Inga | Inga |
> | publicImages | Inga | Inga |
> | quotas | Inga | Inga |
> | storageAccounts | Inga | Inga |
> | storageAccounts/blobServices | Inga | Inga |
> | storageAccounts/fileServices | Inga | Inga |
> | storageAccounts/metricDefinitions | Inga | Inga |
> | storageAccounts/metrics | Inga | Inga |
> | storageAccounts/queueServices | Inga | Inga |
> | storageAccounts/services | Inga | Inga |
> | storageAccounts/services/diagnosticSettings | Inga | Inga |
> | storageAccounts/services/metricDefinitions | Inga | Inga |
> | storageAccounts/services/metrics | Inga | Inga |
> | storageAccounts/tableServices | Inga | Inga |
> | storageAccounts/vmImages | Inga | Inga |
> | vmImages | Inga | Inga |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Noder | Ja | Ja |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Planer | Ja | Inga |
> | registeredSubscriptions | Inga | Inga |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | accounts/privateEndpointConnectionProxies | Inga | Inga |
> | accounts/privateEndpointConnections | Inga | Inga |
> | accounts/privateLinkResources | Inga | Inga |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | RateCard | Inga | Inga |
> | UsageAggregates | Inga | Inga |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Ja | Ja |
> | cloudServices | Ja | Ja |
> | cloudServices/networkInterfaces | Inga | Inga |
> | cloudServices/publicIPAddresses | Inga | Inga |
> | cloudServices/roleInstances | Inga | Inga |
> | cloudServices/roleInstances/networkInterfaces | Inga | Inga |
> | cloudServices/roles | Inga | Inga |
> | diskAccesses | Ja | Ja |
> | diskEncryptionSets | Ja | Ja |
> | Diskar | Ja | Ja |
> | Gallerier | Ja | Ja |
> | gallerier/program | Inga | Inga |
> | gallerier/program/versioner | Inga | Inga |
> | gallerier/bilder | Inga | Inga |
> | gallerier/bilder/versioner | Inga | Inga |
> | hostGroups | Ja | Ja |
> | hostGroups/hosts | Ja | Ja |
> | images | Ja | Ja |
> | proximityPlacementGroups | Ja | Ja |
> | restorePointCollections | Ja | Ja |
> | restorePointCollections/restorePoints | Inga | Inga |
> | sharedVMExtensions | Ja | Ja |
> | sharedVMExtensions/versions | Inga | Inga |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages/versions | Inga | Inga |
> | snapshots | Ja | Ja |
> | sshPublicKeys | Ja | Ja |
> | virtualMachines | Ja | Ja |
> | virtualMachines/extensions | Ja | Ja |
> | virtualMachines/metricDefinitions | Inga | Inga |
> | virtualMachines/runCommands | Ja | Ja |
> | virtualMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets/extensions | Inga | Inga |
> | virtualMachineScaleSets/networkInterfaces | Inga | Inga |
> | virtualMachineScaleSets/publicIPAddresses | Ja | Inga |
> | virtualMachineScaleSets/virtualMachines | Inga | Inga |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Inga | Inga |

> [!NOTE]
> Du kan inte lägga till en tagg till en virtuell dator som har markerats som generaliserad. Du markerar en virtuell dator som generaliserad med [Set-AzVm -Generalized eller](/powershell/module/Az.Compute/Set-AzVM) [az vm generalize](/cli/azure/vm#az_vm_generalize).

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Ja | Ja |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | platformAccounts | Ja | Ja |
> | registeredSubscriptions | Inga | Inga |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | ResourcePools | Ja | Ja |
> | VCenters | Ja | Ja |
> | VCenters/InventoryItems | Inga | Inga |
> | VirtualMachines | Ja | Ja |
> | VirtualMachines/Tillägg | Ja | Ja |
> | VirtualMachines/GuestAgents | Inga | Inga |
> | VirtualMachines/HybridIdentityMetadata | Inga | Inga |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Inga | Inga |
> | Saldon | Inga | Inga |
> | Budgetar | Inga | Inga |
> | Avgifter | Inga | Inga |
> | CostTags | Inga | Inga |
> | Krediter | Inga | Inga |
> | händelser | Inga | Inga |
> | Prognoser | Inga | Inga |
> | Massor | Inga | Inga |
> | Marknadsplatser | Inga | Inga |
> | Prisdokument | Inga | Inga |
> | Produkter | Inga | Inga |
> | ReservationDetails | Inga | Inga |
> | ReservationRecommendationDetails | Inga | Inga |
> | ReservationRecommendations | Inga | Inga |
> | ReservationSummaries | Inga | Inga |
> | ReservationTransactions | Inga | Nej |
> | Taggar | Nej | Inga |
> | Hyresgäster | Inga | Inga |
> | Termer | Inga | Inga |
> | UsageDetails | Inga | Inga |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ja | Ja |
> | serviceAssociationLinks | Inga | Inga |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Register | Ja | Ja |
> | register/agentPools | Ja | Ja |
> | register/byggen | Inga | Inga |
> | register/byggen/avbryt | Inga | Inga |
> | register/byggen/getLogLink | Inga | Inga |
> | register/buildTasks | Ja | Ja |
> | register/buildTasks/steps | Inga | Inga |
> | register/anslutna register | Inga | Inga |
> | register/anslutna register/inaktivera | Inga | Inga |
> | register/eventGridFilters | Inga | Inga |
> | register/exportPipelines | Inga | Inga |
> | register/generateCredentials | Inga | Inga |
> | register/getBuildSourceUploadUrl | Inga | Inga |
> | register/GetCredentials | Inga | Inga |
> | register/importImage | Inga | Inga |
> | register/importPipelines | Inga | Inga |
> | register/pipelineRuns | Inga | Inga |
> | register/privateEndpointConnectionProxies | Inga | Inga |
> | register/privateEndpointConnectionProxies/validate | Inga | Inga |
> | register/privateEndpointConnections | Inga | Inga |
> | register/privateLinkResources | Inga | Inga |
> | register/queueBuild | Inga | Inga |
> | register/regenerateCredential | Inga | Inga |
> | register/regenerateCredentials | Inga | Inga |
> | register/replikeringar | Ja | Ja |
> | register/körningar | Inga | Inga |
> | register/körningar/avbryt | Inga | Inga |
> | register/scheduleRun | Inga | Inga |
> | register/scopeMaps | Inga | Inga |
> | register/taskRuns | Inga | Inga |
> | register/uppgifter | Ja | Ja |
> | register/token | Inga | Inga |
> | register/updatePolicies | Inga | Inga |
> | register/webhooks | Ja | Ja |
> | register/webhooks/getCallbackConfig | Inga | Inga |
> | register/webhooks/ping | Inga | Inga |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | containerServices | Ja | Ja |
> | managedClusters | Ja | Ja |
> | ManagedClusters/eventGridFilters | Inga | Inga |
> | openShiftManagedClusters | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Aviseringar | Inga | Inga |
> | FaktureringSkonto | Inga | Inga |
> | Budgetar | Inga | Inga |
> | CloudConnectors | Inga | Inga |
> | Anslutningar | Ja | Ja |
> | costAllocationRules | Inga | Inga |
> | Avdelningar | Inga | Inga |
> | Dimensioner | Inga | Inga |
> | EnrollmentAccounts | Inga | Inga |
> | Exporter | Inga | Inga |
> | ExternalBillingAccounts | Inga | Inga |
> | ExternalBillingAccounts/Alerts | Inga | Inga |
> | ExternalBillingAccounts/Dimensions | Inga | Inga |
> | ExternalBillingAccounts/Forecast | Inga | Inga |
> | ExternalBillingAccounts/Query | Inga | Inga |
> | ExternalSubscriptions | Inga | Inga |
> | ExternalSubscriptions/Aviseringar | Inga | Inga |
> | ExternalSubscriptions/Dimensions | Inga | Inga |
> | ExternalSubscriptions/Forecast | Inga | Inga |
> | ExternalSubscriptions/Query | Inga | Inga |
> | fetchPrices | Inga | Inga |
> | Prognos | Inga | Inga |
> | GenerateDetailedCostReport | Inga | Inga |
> | GenerateReservationDetailsReport | Inga | Inga |
> | Insikter | Inga | Inga |
> | Fråga | Inga | Inga |
> | registrera | Inga | Inga |
> | Reportconfigs | Inga | Inga |
> | Rapporter | Inga | Inga |
> | ScheduledActions | Inga | Inga |
> | Inställningar | Inga | Inga |
> | showbackRules | Inga | Inga |
> | Vyer | Inga | Inga |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | DisableLockbox | Inga | Inga |
> | EnableLockbox | Inga | Inga |
> | Begäranden | Inga | Inga |
> | TenantOptedIn | Inga | Inga |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Sammanslutningar | Inga | Inga |
> | resourceProviders | Ja | Ja |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Instanser | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Jobb | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | arbetsytor | Ja | Ja |
> | arbetsytor/dbWorkspaces | Inga | Inga |
> | arbetsytor/virtualNetworkPeerings | Inga | Inga |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kataloger | Ja | Ja |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | dataFactories | Ja | Ja |
> | dataFactories/diagnosticSettings | Inga | Inga |
> | dataFactories/metricDefinitions | Inga | Inga |
> | dataFactorySchema | Inga | Inga |
> | Fabriker | Ja | Ja |
> | fabriker/integrationRuntimes | Inga | Inga |

> [!NOTE]
> Om du har Azure-SSIS Integration Runtimes i din datafabrik taggas den löpande kostnaden med datafabrikstaggar. Du måste stoppa och starta om Azure SSIS-integreringskörningar för att nya datafabrikstaggar ska kunna tillämpas på deras löpande kostnad.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | accounts/dataLakeStoreAccounts | Inga | Inga |
> | konton/storageAccounts | Inga | Inga |
> | konton/storageAccounts/containers | Inga | Inga |
> | accounts/transferAnalyticsUnits | Inga | Inga |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/eventGridFilters | Inga | Inga |
> | konton/firewallRules | Inga | Inga |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | DatabaseMigrations | Inga | Inga |
> | services | Ja | Ja |
> | tjänster/projekt | Ja | Ja |
> | SqlMigrationServices | Ja | Ja |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Ja | Ja |
> | ResourceGuards | Ja | Ja |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/resurser | Inga | Inga |
> | konton/resurser/datauppsättningar | Inga | Inga |
> | konton/resurser/inbjudningar | Inga | Inga |
> | konton/resurser/providersharesubscriptions | Inga | Inga |
> | konton/resurser/synkroniseringInställningar | Inga | Inga |
> | konton/sharesubscriptions | Inga | Inga |
> | accounts/sharesubscriptions/consumerSourceDataSets | Inga | Inga |
> | konton/sharesubscriptions/datasetmappings | Inga | Inga |
> | konton/sharesubscriptions/utlösare | Inga | Inga |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforTillaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Servrar | Ja | Ja |
> | servrar/rådgivare | Inga | Inga |
> | servrar/nycklar | Inga | Inga |
> | servrar/privateEndpointConnectionProxies | Inga | Inga |
> | servrar/privateEndpointConnections | Inga | Inga |
> | servrar/privateLinkResources | Inga | Inga |
> | servrar/queryTexts | Inga | Inga |
> | servrar/recoverableServers | Inga | Inga |
> | servrar/resetQueryPerformanceInsightData | Inga | Inga |
> | servrar/starta | Inga | Inga |
> | servrar/stoppa | Inga | Inga |
> | servrar/topQueryStatistics | Inga | Inga |
> | servrar/virtualNetworkRules | Inga | Inga |
> | servrar/waitStatistics | Inga | Inga |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ja | Ja |
> | Servrar | Ja | Ja |
> | servrar/rådgivare | Inga | Inga |
> | servrar/nycklar | Inga | Inga |
> | servrar/privateEndpointConnectionProxies | Inga | Inga |
> | servrar/privateEndpointConnections | Inga | Inga |
> | servrar/privateLinkResources | Inga | Inga |
> | servrar/queryTexts | Inga | Inga |
> | servrar/recoverableServers | Inga | Inga |
> | servrar/resetQueryPerformanceInsightData | Inga | Inga |
> | servrar/starta | Inga | Inga |
> | servrar/stoppa | Inga | Inga |
> | servrar/topQueryStatistics | Inga | Inga |
> | servrar/uppgradera | Inga | Inga |
> | servrar/virtualNetworkRules | Inga | Inga |
> | servrar/waitStatistics | Inga | Inga |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ja | Ja |
> | serverGroups | Ja | Ja |
> | serverGroupsv2 | Ja | Ja |
> | Servrar | Ja | Ja |
> | servrar/rådgivare | Inga | Inga |
> | servrar/nycklar | Inga | Inga |
> | servrar/privateEndpointConnectionProxies | Inga | Inga |
> | servrar/privateEndpointConnections | Inga | Inga |
> | servrar/privateLinkResources | Inga | Inga |
> | servrar/queryTexts | Inga | Inga |
> | servrar/recoverableServers | Inga | Inga |
> | servrar/resetQueryPerformanceInsightData | Inga | Inga |
> | servrar/topQueryStatistics | Inga | Inga |
> | servrar/virtualNetworkRules | Inga | Inga |
> | servrar/waitStatistics | Inga | Inga |
> | serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | artifactSources | Ja | Ja |
> | Utbyggnader | Ja | Ja |
> | serviceTopologies | Ja | Ja |
> | serviceTopologies/services | Ja | Ja |
> | serviceTopologies/services/serviceUnits | Ja | Ja |
> | steg | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualisering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Ja | Ja |
> | applicationgroups/applications | Inga | Inga |
> | applicationgroups/desktops | Inga | Inga |
> | applicationgroups/startmenuitems | Inga | Inga |
> | värdpooler | Ja | Ja |
> | hostpools/msixpackages | Inga | Inga |
> | hostpools/sessionhosts | Inga | Inga |
> | hostpools/sessionhosts/usersessions | Inga | Inga |
> | hostpools/usersessions | Inga | Inga |
> | scalingPlans | Ja | Ja |
> | arbetsytor | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ja | Ja |
> | ElasticPools/IotHubTenants | Ja | Ja |
> | ElasticPools/IotHubTenants/securitySettings | Inga | Inga |
> | IotHubs | Ja | Ja |
> | IotHubs/eventGridFilters | Inga | Inga |
> | IotHubs/securitySettings | Inga | Inga |
> | ProvisioningServices | Ja | Ja |
> | Användningsområden | Inga | Inga |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/instanser | Ja | Ja |
> | registeredSubscriptions | Inga | Inga |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Rörledningar | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Styrenheter | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | labcenters | Ja | Ja |
> | Labs | Ja | Ja |
> | labb/miljöer | Ja | Ja |
> | labb/serviceRunners | Ja | Ja |
> | labb/virtualMachines | Ja | Ja |
> | Scheman | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Ja | Ja |
> | digitalTwinsInstances/endpoints | Inga | Inga |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | cassandraClusters | Ja | Ja |
> | databaseAccountNames | Inga | Inga |
> | databaseAccounts | Ja | Ja |
> | restorableDatabaseAccounts | Inga | Inga |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Domäner | Ja | Ja |
> | domäner/domainOwnershipIdentifiers | Inga | Inga |
> | generateSsoRequest | Inga | Inga |
> | topLevelDomains | Inga | Inga |
> | validateDomainRegistrationInformation | Inga | Inga |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Inga | Inga |
> | lcsprojects/clouddeployments | Inga | Inga |
> | lcsprojects/connectors | Inga | Inga |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Adresser | Ja | Ja |
> | orderCollections | Ja | Ja |
> | beställningar | Ja | Ja |
> | productFamiliesMetadata | Inga | Inga |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Domäner | Ja | Ja |
> | domäner/ämnen | Inga | Inga |
> | eventSubscriptions | Inga | Inga |
> | extensionTopics | Inga | Inga |
> | partnerNamespaces | Ja | Ja |
> | partnerNamespaces/eventChannels | Inga | Inga |
> | partnerRegistreringar | Ja | Ja |
> | partnerTopics | Ja | Ja |
> | partnerTopics/eventSubscriptions | Inga | Inga |
> | systemTopics | Ja | Ja |
> | systemTopics/eventSubscriptions | Inga | Inga |
> | Ämnen | Ja | Ja |
> | topicTypes | Inga | Inga |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kluster | Ja | Ja |
> | Namnområden | Ja | Ja |
> | namnområden/authorizationrules | Inga | Inga |
> | namespaces/disasterrecoveryconfigs | Inga | Inga |
> | namnrymder/eventhubs | Inga | Inga |
> | namespaces/eventhubs/authorizationrules | Inga | Inga |
> | namnområden/eventhubs/consumergroups | Inga | Inga |
> | namnrymder/networkrulesets | Inga | Inga |
> | namespaces/privateEndpointConnections | Inga | Inga |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | experimentArbetsområden | Ja | Ja |

## <a name="microsoftfalcon"></a>Microsoft.Sint

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Namnområden | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | featureConfigurations | Inga | Inga |
> | featureProviderNamespaces | Inga | Inga |
> | featureProviders | Inga | Inga |
> | funktioner | Inga | Inga |
> | Leverantörer | Inga | Inga |
> | subscriptionFeatureRegistrations | Inga | Inga |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Registrera | Inga | Inga |
> | galleryitems | Inga | Inga |
> | generateartifactaccessuri | Inga | Inga |
> | myareas | Inga | Inga |
> | myareas/areas | Inga | Inga |
> | myareas/areas/areas | Inga | Inga |
> | myareas/areas/areas/galleryitems | Inga | Inga |
> | myareas/areas/galleryitems | Inga | Inga |
> | myareas/galleryitems | Inga | Inga |
> | registrera | Inga | Inga |
> | resources | Inga | Inga |
> | retrieveresourcesbyid | Inga | Inga |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Ja | Ja |
> | autoManagedVmConfigurationProfiles | Ja | Ja |
> | configurationProfileAssignments | Inga | Inga |
> | guestConfigurationAssignments | Inga | Inga |
> | Programvara | Inga | Inga |
> | softwareUpdateProfile | Inga | Inga |
> | softwareUpdates | Inga | Inga |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Ja | Ja |
> | sapMonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | dedicatedHSM | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | clusterPools | Ja | Ja |
> | clusterPools/clusters | Ja | Ja |
> | Kluster | Ja | Ja |
> | kluster/program | Inga | Inga |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | healthBots | Ja | Ja |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |
> | tjänster/iomtconnectors | Inga | Inga |
> | tjänster/iomtconnectors/connections | Inga | Inga |
> | tjänster/iomtconnectors/mappningar | Inga | Inga |
> | tjänster/privateEndpointConnectionProxies | Inga | Inga |
> | tjänster/privateEndpointConnections | Inga | Inga |
> | tjänster/privateLinkResources | Inga | Inga |
> | arbetsytor | Ja | Ja |
> | arbetsytor/dicomservices | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Maskiner | Ja | Ja |
> | datorer/assessPatches | Inga | Inga |
> | datorer/tillägg | Ja | Ja |
> | datorer/installPatches | Inga | Inga |
> | datorer/privateLinkScopes | Inga | Inga |
> | privateLinkScopes | Ja | Ja |
> | privateLinkScopes/privateEndpointConnectionProxies | Inga | Inga |
> | privateLinkScopes/privateEndpointConnections | Inga | Inga |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | dataManagers | Ja | Ja |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | devices | Ja | Ja |
> | networkfunctions | Ja | Ja |
> | networkFunctionVendors | Inga | Inga |
> | registeredSubscriptions | Inga | Inga |
> | Leverantörer | Inga | Inga |
> | Leverantörer/vendorskus | Inga | Inga |
> | Leverantör/leverantörskus/förhandsversionprenumereringar | Inga | Inga |
> | virtualNetworkFunctions | Ja | Ja |
> | virtualNetworkFunctionVendors | Inga | Inga |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Komponenter | Ja | Ja |
> | networkScopes | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Jobb | Ja | Ja |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | actionGroups | Ja | Ja |
> | activityLogAlerts | Ja | Ja |
> | alertrules | Ja | Ja |
> | autoskalningsinställningar | Ja | Ja |
> | Komponenter | Ja | Ja |
> | komponenter/linkedStorageAccounts | Inga | Inga |
> | komponenter/ProactiveDetectionConfigs | Inga | Inga |
> | diagnosticSettings | Inga | Inga |
> | guestDiagnosticSettings | Ja | Ja |
> | guestDiagnosticSettingsAssociation | Ja | Ja |
> | logprofiles | Ja | Ja |
> | metricAlerts | Ja | Ja |
> | privateLinkScopes | Ja | Ja |
> | privateLinkScopes/privateEndpointConnections | Inga | Inga |
> | privateLinkScopes/scopedResources | Inga | Inga |
> | queryPacks | Ja | Ja |
> | queryPacks/queries | Inga | Inga |
> | scheduledQueryRules | Ja | Ja |
> | webbtest | Ja | Ja |
> | arbetsböcker | Ja | Ja |
> | workbooktemplates | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Inga | Inga |
> | diagnosticSettingsCategories | Inga | Inga |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | appTemplates | Inga | Inga |
> | IoTApps | Ja | Ja |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | defenderSettings | Inga | Inga |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | deletedManagedHSMs | Inga | Inga |
> | deletedVaults | Inga | Inga |
> | hsmPools | Ja | Ja |
> | managedHSM | Ja | Ja |
> | Valv | Ja | Ja |
> | valv/accessPolicies | Inga | Inga |
> | valv/eventGridFilters | Inga | Inga |
> | valv/nycklar | Inga | Inga |
> | valv/nycklar/versioner | Inga | Inga |
> | valv/hemligheter | Inga | Inga |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Ja | Ja |
> | registeredSubscriptions | Inga | Inga |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Tillägg | Inga | Inga |
> | sourceControlConfigurations | Inga | Inga |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kluster | Ja | Ja |
> | kluster/anslutnadatabaskonfigurationer | Inga | Inga |
> | kluster/databaser | Inga | Inga |
> | kluster/databaser/dataanslutning | Inga | Inga |
> | kluster/databaser/eventhubconnections | Inga | Inga |
> | kluster/databaser/principalassignments | Inga | Inga |
> | kluster/databaser/skript | Inga | Inga |
> | kluster/dataanslutning | Inga | Inga |
> | kluster/principalassignments | Inga | Inga |
> | kluster/deladeidentiteter | Inga | Inga |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ja | Inga |
> | labplans | Ja | Ja |
> | Labs | Ja | Ja |
> | användare | Inga | Inga |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ja | Ja |
> | integrationAccounts | Ja | Ja |
> | integrationServiceEnvironments | Ja | Ja |
> | integrationServiceEnvironments/managedApis | Inga | Inga |
> | isolatedEnvironments | Ja | Ja |
> | Arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Ja | Ja |
> | Webservices | Ja | Ja |
> | Arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | modelinventories | Ja | Ja |
> | virtualclusters | Ja | Ja |
> | arbetsytor | Ja | Ja |
> | arbetsytor/batchEndpoints | Ja | Ja |
> | arbetsytor/batchEndpoints/distributioner | Ja | Ja |
> | arbetsytor/batchEndpoints/distributioner/jobb | Inga | Inga |
> | arbetsytor/batchEndpoints/jobs | Inga | Inga |
> | arbetsytor/koder | Inga | Inga |
> | arbetsytor/koder/versioner | Inga | Inga |
> | arbetsytor/beräkningar | Inga | Inga |
> | arbetsytor/data | Inga | Inga |
> | arbetsytor/datalager | Inga | Inga |
> | arbetsytor/miljöer | Inga | Inga |
> | arbetsytor/eventGridFilters | Inga | Inga |
> | arbetsytor/jobb | Inga | Inga |
> | arbetsytor/labelingJobs | Inga | Inga |
> | arbetsytor/linkedServices | Inga | Inga |
> | arbetsytor/modeller | Inga | Inga |
> | arbetsytor/modeller/versioner | Inga | Inga |
> | arbetsytor/onlineEndpoints | Ja | Ja |
> | arbetsytor/onlineEndpoints/distributioner | Ja | Ja |

> [!NOTE]
> Taggar för arbetsytor sprids inte till beräkningskluster och beräkningsinstanser.

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Inga | Inga |
> | configurationAssignments | Inga | Inga |
> | maintenanceConfigurations | Ja | Ja |
> | publicMaintenanceConfigurations | Inga | Inga |
> | uppdateringar | Inga | Inga |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Identiteter | Inga | Inga |
> | userAssignedIdentities | Ja | Ja |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Ja | Ja |
> | managedNetworks/managedNetworkGroups | Ja | Ja |
> | managedNetworks/managedNetworkPeeringPolicies | Ja | Ja |
> | avisering | Ja | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Inga | Inga |
> | registrationAssignments | Inga | Inga |
> | registrationDefinitions | Inga | Inga |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | getEntities | Inga | Inga |
> | managementGroups | Inga | Inga |
> | managementGroups/settings | Inga | Inga |
> | resources | Inga | Inga |
> | startTenantBackfill | Inga | Inga |
> | tenantBackfillStatus | Inga | Inga |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/skapare | Ja | Ja |
> | konton/eventGridFilters | Inga | Inga |
> | konton/privateAtlases | Ja | Ja |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Macc | Inga | Inga |
> | Erbjuder | Inga | Inga |
> | offerTypes | Inga | Inga |
> | offerTypes/publishers | Inga | Inga |
> | offerTypes/publishers/offers | Inga | Inga |
> | offerTypes/publishers/offers/plans | Inga | Inga |
> | offerTypes/publishers/offers/plans/agreements | Inga | Inga |
> | offerTypes/publishers/offers/plans/configs | Inga | Inga |
> | offerTypes/publishers/offers/plans/configs/importImage | Inga | Inga |
> | privategalleryitems | Inga | Inga |
> | privateStoreClient | Inga | Inga |
> | privateStores | Inga | Inga |
> | privateStores/AdminRequestApprovals | Inga | Inga |
> | privateStores/offers | Inga | Inga |
> | privateStores/offers/acknowledgeNotification | Inga | Inga |
> | privateStores/queryNotificationsState | Inga | Inga |
> | privateStores/RequestApprovals | Inga | Inga |
> | privateStores/requestApprovals/query | Inga | Inga |
> | privateStores/requestApprovals/vargPlan | Inga | Inga |
> | Produkter | Inga | Inga |
> | Förlag | Inga | Inga |
> | utgivare/erbjudanden | Inga | Inga |
> | utgivare/erbjudanden/ändringar | Inga | Inga |
> | registrera | Inga | Inga |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ja | Ja |
> | updateCommunicationPreference | Inga | Inga |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Avtal | Inga | Inga |
> | offertypes | Inga | Inga |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | mediaservices | Ja | Ja |
> | mediaservices/accountFilters | Inga | Inga |
> | mediaservices/assets | Inga | Inga |
> | mediaservices/assets/assetFilters | Inga | Inga |
> | mediaservices/contentKeyPolicies | Inga | Inga |
> | mediaservices/eventGridFilters | Inga | Inga |
> | mediaservices/graphInstances | Inga | Inga |
> | mediaservices/graphTopologies | Inga | Inga |
> | mediaservices/liveEventOperations | Inga | Inga |
> | mediaservices/liveEvents | Ja | Ja |
> | mediaservices/liveEvents/liveOutputs | Inga | Inga |
> | mediaservices/liveOutputOperations | Inga | Inga |
> | mediaservices/mediaGraphs | Inga | Inga |
> | mediaservices/privateEndpointConnectionOperations | Inga | Inga |
> | mediaservices/privateEndpointConnectionProxies | Inga | Inga |
> | mediaservices/privateEndpointConnections | Inga | Inga |
> | mediaservices/streamingEndpointOperations | Inga | Inga |
> | mediaservices/streamingEndpoints | Ja | Ja |
> | mediaservices/streamingLocators | Inga | Inga |
> | mediaservices/streamingPolicies | Inga | Inga |
> | mediaservices/transforms | Inga | Inga |
> | mediaservices/transforms/jobs | Inga | Inga |
> | videoAnalyzers | Ja | Ja |
> | videoAnalyzers/edgeModules | Inga | Inga |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | appClusters | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Ja | Ja |
> | migrateprojects | Ja | Ja |
> | moveCollections | Ja | Ja |
> | Projekt | Ja | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ja | Ja |
> | objectAnchorsAccounts | Ja | Ja |
> | objectUnderstandingAccounts | Ja | Ja |
> | remoteRenderingAccounts | Ja | Ja |
> | spatialAnchorsAccounts | Ja | Ja |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Nätverk | Ja | Ja |
> | nätverk/platser | Ja | Ja |
> | packetCores | Ja | Ja |
> | Sims | Ja | Ja |
> | simProfiles | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ja | Inga |
> | netAppAccounts/accountBackups | Inga | Inga |
> | netAppAccounts/capacityPools | Ja | Inga |
> | netAppAccounts/capacityPools/volumes | Ja | Inga |
> | netAppAccounts/capacityPools/volumes/snapshots | Inga | Inga |
> | netAppAccounts/volumeGroups | Inga | Inga |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ja | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja | Ja |
> | applicationSecurityGroups | Ja | Ja |
> | azureFirewallFqdnTags | Inga | Inga |
> | azureFirewalls | Ja | Inga |
> | bastionHosts | Ja | Inga |
> | bgpServiceCommunities | Inga | Inga |
> | Anslutningar | Ja | Ja |
> | ddosCustomPolicies | Ja | Ja |
> | ddosProtectionPlans | Ja | Ja |
> | dnsOperationStatuses | Inga | Inga |
> | dnszones | Ja | Ja |
> | dnszones/A | Inga | Inga |
> | dnszones/AAAA | Inga | Inga |
> | dnszones/all | Inga | Inga |
> | dnszones/CAA | Inga | Inga |
> | dnszones/CNAME | Inga | Inga |
> | dnszones/MX | Inga | Inga |
> | dnszones/NS | Inga | Inga |
> | dnszones/PTR | Inga | Inga |
> | dnszones/recordsets | Inga | Inga |
> | dnszones/SOA | Inga | Inga |
> | dnszones/SRV | Inga | Inga |
> | dnszones/TXT | Inga | Inga |
> | expressRouteCircuits | Ja | Ja |
> | expressRouteCrossConnections | Ja | Ja |
> | expressRouteGateways | Ja | Ja |
> | expressRoutePorts | Ja | Ja |
> | expressRouteServiceProviders | Inga | Inga |
> | firewallPolicies | Ja | Ja |
> | frontdoors | Ja, men begränsad (se [anmärkning nedan)](#frontdoor) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ja, men begränsad (se [anmärkning nedan)](#frontdoor) | No |
> | frontdoorWebApplicationFirewallPolicies | Ja, men begränsad (se [anmärkning nedan)](#frontdoor) | Yes |
> | getDnsResourceReference | Inga | Inga |
> | internalNotify | Inga | Inga |
> | ipGroups | Ja | Ja |
> | loadBalancers | Ja | Ja |
> | localNetworkGateways | Ja | Ja |
> | natGateways | Ja | Ja |
> | networkIntentPolicies | Ja | Ja |
> | networkInterfaces | Ja | Ja |
> | networkProfiles | Ja | Ja |
> | networkSecurityGroups | Ja | Ja |
> | networkWatchers | Ja | Ja |
> | networkWatchers/connectionMonitors | Ja | Inga |
> | networkWatchers/flowLogs | Ja | Inga |
> | networkWatchers/lenses | Ja | Inga |
> | networkWatchers/pingMeshes | Ja | Inga |
> | p2sVpnGateways | Ja | Ja |
> | privateDnsOperationStatuses | Inga | Inga |
> | privateDnsZones | Ja | Ja |
> | privateDnsZones/A | Inga | Inga |
> | privateDnsZones/AAAA | Inga | Inga |
> | privateDnsZones/all | Inga | Inga |
> | privateDnsZones/CNAME | Inga | Inga |
> | privateDnsZones/MX | Inga | Inga |
> | privateDnsZones/PTR | Inga | Inga |
> | privateDnsZones/SOA | Inga | Inga |
> | privateDnsZones/SRV | Inga | Inga |
> | privateDnsZones/TXT | Inga | Inga |
> | privateDnsZones/virtualNetworkLinks | Ja | Ja |
> | privateEndpoints | Ja | Ja |
> | privateLinkServices | Ja | Ja |
> | publicIPAddresses | Ja | Ja |
> | publicIPPrefixes | Ja | Ja |
> | routeFilters | Ja | Ja |
> | routeTables | Ja | Ja |
> | serviceEndpointPolicies | Ja | Ja |
> | trafficManagerGeographicHierarchies | Inga | Inga |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/heatMaps | Inga | Inga |
> | trafficManagerUserMetricsKeys | Inga | Inga |
> | virtualHubs | Ja | Ja |
> | virtualNetworkGateways | Ja | Ja |
> | virtualNetworks | Ja | Ja |
> | virtualNetworks/undernät | Inga | Inga |
> | virtualNetworkTaps | Ja | Ja |
> | virtualWans | Ja | Inga |
> | vpnGateways | Ja | Ja |
> | vpnSites | Ja | Ja |
> | webApplicationFirewallPolicies | Ja | Ja |

<a id="frontdoor"></a>

> [!NOTE]
> Du Azure Front Door Service till exempel använda taggar när du skapar resursen, men det finns för närvarande inte stöd för att uppdatera eller lägga till taggar.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Inga | Inga |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Namnområden | Ja | Inga |
> | namespaces/notificationHubs | Ja | Inga |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ja | Ja |
> | ImportPlatser | Ja | Ja |
> | MasterSites | Ja | Ja |
> | ServerPlatser | Ja | Ja |
> | VMwareSites | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kluster | Ja | Ja |
> | deletedWorkspaces | Inga | Inga |
> | linkTargets | Inga | Inga |
> | querypacks | Ja | Ja |
> | storageInsightConfigs | Inga | Inga |
> | arbetsytor | Ja | Ja |
> | arbetsytor/dataExporter | Inga | Inga |
> | arbetsytor/dataKällor | Inga | Inga |
> | arbetsytor/linkedServices | Inga | Inga |
> | arbetsytor/linkedStorageAccounts | Inga | Inga |
> | arbetsytor/metadata | Inga | Inga |
> | arbetsytor/fråga | Inga | Inga |
> | arbetsytor/scopedPrivateLinkProxies | Inga | Inga |
> | arbetsytor/storageInsightConfigs | Inga | Inga |
> | arbetsytor/tabeller | Inga | Inga |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | hanteringsassociationer | Inga | Inga |
> | hanteringskonfigurationer | Ja | Ja |
> | lösningar | Ja | Ja |
> | Visningar | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | cdnPeeringPrefixes | Inga | Inga |
> | legacyPeerings | Inga | Inga |
> | peerAsns | Inga | Inga |
> | peerings | Ja | Ja |
> | peeringServiceCountries | Inga | Inga |
> | peeringServiceProviders | Inga | Inga |
> | peeringServices | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Intyg | Inga | Inga |
> | eventGridFilters | Inga | Inga |
> | policyEvents | Inga | Inga |
> | policyMetadata | Inga | Inga |
> | policyStates | Inga | Inga |
> | policyTrackedResources | Inga | Inga |
> | reparationsåtgärder | Inga | Inga |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> |  -konsoler | Inga | Inga |
> | instrumentpaneler | Ja | Ja |
> | tenantconfigurations | Inga | Inga |
> | userSettings | Inga | Inga |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Ja | Ja |
> | Hyresgäster | Ja | Ja |
> | klienter/arbetsytor | Inga | Inga |
> | workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | autoScaleVCores | Ja | Ja |
> | Kapacitet | Ja | Ja |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | enterprisePolicies | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | deletedAccounts | Inga | Inga |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Inga | Inga |
> | providerRegistrations/customRollouts | Inga | Inga |
> | providerRegistrations/defaultRollouts | Inga | Inga |
> | providerRegistrations/resourceTypeRegistrations | Inga | Inga |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | deletedAccounts | Inga | Inga |
> | getDefaultAccount | Inga | Inga |
> | removeDefaultAccount | Inga | Inga |
> | setDefaultAccount | Inga | Inga |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Arbetsytor | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Inga | Inga |
> | Valv | Ja | Ja |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Namnområden | Ja | Ja |
> | namnområden/authorizationrules | Inga | Inga |
> | namnområden/hybridanslutningar | Inga | Inga |
> | namespaces/hybridconnections/authorizationrules | Inga | Inga |
> | namespaces/privateEndpointConnections | Inga | Inga |
> | namnrymder/wcfrelays | Inga | Inga |
> | namespaces/wcfrelays/authorizationrules | Inga | Inga |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Apparater | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Frågor | Ja | Ja |
> | resourceChangeDetails | Inga | Inga |
> | resourceChanges | Inga | Inga |
> | resources | Inga | Inga |
> | resourcesHistory | Inga | Inga |
> | subscriptionsStatus | Inga | Inga |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Inga | Inga |
> | childAvailabilityStatuses | Inga | Inga |
> | childResources | Inga | Inga |
> | emergingissues | Inga | Inga |
> | händelser | Inga | Inga |
> | impactedResources | Inga | Inga |
> | metadata | Inga | Inga |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Distributioner | Ja | Inga |
> | distributioner/åtgärder | Inga | Inga |
> | deploymentScripts | Ja | Ja |
> | deploymentScripts/logs | Inga | Inga |
> | Länkar | Inga | Inga |
> | Leverantörer | Inga | Inga |
> | resourceGroups | Ja | Inga |
> | Prenumerationer | Ja | Inga |
> | templateSpecs | Ja | Ja |
> | templateSpecs/versions | Ja | Ja |
> | Hyresgäster | Inga | Inga |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | resources | Ja | Ja |
> | saasresources | Inga | Inga |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Moln | Ja | Ja |
> | VirtualMachines | Ja | Ja |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |
> | vmmservrar | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Inga | Inga |
> | searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Inga | Inga |
> | advancedThreatProtectionSettings | Inga | Inga |
> | aviseringar | Inga | Inga |
> | alertsSuppressionRules | Inga | Inga |
> | allowedConnections | Inga | Inga |
> | applicationWhitelistings | Inga | Inga |
> | assessmentMetadata | Inga | Inga |
> | utvärderingar | Inga | Inga |
> | autoDismissAlertsRules | Inga | Inga |
> | automatiseringar | Ja | Ja |
> | Automatisk etableringInställningar | Inga | Inga |
> | Efterlevnad | Inga | Inga |
> | anslutningar | Inga | Inga |
> | dataCollectionAgents | Inga | Inga |
> | devices | Inga | Inga |
> | deviceSecurityGroups | Inga | Inga |
> | discoveredSecuritySolutions | Inga | Inga |
> | externalSecuritySolutions | Inga | Inga |
> | InformationProtectionPolicies | Inga | Inga |
> | ingestionSettings | Inga | Inga |
> | Insikter | Inga | Inga |
> | iotAlerts | Inga | Inga |
> | iotAlertTypes | Inga | Inga |
> | iotDefenderSettings | Inga | Inga |
> | iotRecommendations | Inga | Inga |
> | iotRecommendationTypes | Inga | Inga |
> | iotSecuritySolutions | Ja | Ja |
> | iotSecuritySolutions/analyticsModels | Inga | Inga |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Inga | Inga |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Inga | Inga |
> | iotSecuritySolutions/iotAlerts | Inga | Inga |
> | iotSecuritySolutions/iotAlertTypes | Inga | Inga |
> | iotSecuritySolutions/iotRecommendations | Inga | Inga |
> | iotSecuritySolutions/iotRecommendationTypes | Inga | Inga |
> | iotSensors | Inga | Inga |
> | iotSites | Inga | Inga |
> | jitNetworkAccessPolicies | Inga | Inga |
> | jitPolicies | Inga | Inga |
> | onPremiseIotSensors | Inga | Inga |
> | policies | Inga | Inga |
> | priser | Inga | Inga |
> | regulatoryComplianceStandards | Inga | Inga |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Inga | Inga |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Inga | Inga |
> | secureScoreControlDefinitions | Inga | Inga |
> | secureScoreControls | Inga | Inga |
> | secureScores | Inga | Inga |
> | secureScores/secureScoreControls | Inga | Inga |
> | securityContacts | Inga | Inga |
> | securitySolutions | Inga | Inga |
> | securitySolutionsReferenceData | Inga | Inga |
> | securityStatuses | Inga | Inga |
> | securityStatusesSummaries | Inga | Inga |
> | serverVulnerabilityAssessments | Inga | Inga |
> | inställningar | Inga | Inga |
> | sqlVulnerabilityAssessments | Inga | Inga |
> | undervärderingar | Inga | Inga |
> | uppgifter | Inga | Inga |
> | Topologier | Inga | Inga |
> | workspaceSettings | Inga | Inga |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Inga | Inga |
> | diagnosticSettingsCategories | Inga | Inga |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Aggregeringar | Inga | Inga |
> | alertRules | Inga | Inga |
> | alertRuleTemplates | Inga | Inga |
> | automationRules | Inga | Inga |
> | bokmärken | Inga | Inga |
> | Fall | Inga | Inga |
> | dataConnectors | Inga | Inga |
> | dataConnectorsCheckRequirements | Inga | Inga |
> | Anrikning | Inga | Inga |
> | Enheter | Inga | Inga |
> | entityQueries | Inga | Inga |
> | entityQueryTemplates | Inga | Inga |
> | incidenter | Inga | Inga |
> | officeConsents | Inga | Inga |
> | inställningar | Inga | Inga |
> | threatIntelligence | Inga | Inga |
> | visningslistor | Inga | Inga |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | consoleServices | Inga | Inga |
> | serialPorts | Inga | Inga |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Namnområden | Ja | Ja |
> | namespaces/authorizationrules | Inga | Inga |
> | namespaces/disasterrecoveryconfigs | Inga | Inga |
> | namespaces/eventgridfilters | Inga | Inga |
> | namespaces/networkrulesets | Inga | Inga |
> | namespaces/privateEndpointConnections | Inga | Inga |
> | namnrymder/köer | Inga | Inga |
> | namnrymder/köer/auktoriseringsregler | Inga | Inga |
> | namnrymder/ämnen | Inga | Inga |
> | namespaces/topics/authorizationrules | Inga | Inga |
> | namnrymder/ämnen/prenumerationer | Inga | Inga |
> | namnrymder/ämnen/prenumerationer/regler | Inga | Inga |
> | premiumMessagingRegions | Inga | Inga |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | Kluster | Ja | Ja |
> | kluster/program | Inga | Inga |
> | containerGroups | Ja | Ja |
> | containerGroupSets | Ja | Ja |
> | edgeclusters | Ja | Ja |
> | edgeclusters/applications | Inga | Inga |
> | managedclusters | Ja | Ja |
> | managedclusters/applications | Inga | Inga |
> | managedclusters/applications/services | Inga | Inga |
> | managedclusters/applicationTypes | Inga | Inga |
> | managedclusters/applicationTypes/versions | Inga | Inga |
> | managedclusters/nodetypes | Inga | Inga |
> | Nätverk | Ja | Ja |
> | secretstores | Ja | Ja |
> | secretstores/certificates | Inga | Inga |
> | secretstores/secrets | Inga | Inga |
> | volumes | Ja | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | containerGroups | Ja | Ja |
> | Gateways | Ja | Ja |
> | Nätverk | Ja | Ja |
> | secrets | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Linkers | Inga | Inga |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Inga | Inga |
> | providerRegistrations/resourceTypeRegistrations | Inga | Inga |
> | Utbyggnader | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | SignalR | Ja | Ja |
> | SignalR/eventGridFilters | Inga | Inga |
> | WebPubSub | Ja | Ja |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | accounts/accountQuotaPolicies | Inga | Inga |
> | konton/groupPolicies | Inga | Inga |
> | konton/jobb | Inga | Inga |
> | konton/storageContainers | Inga | Inga |
> | images | Inga | Inga |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Inga | Inga |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Ja | Ja |
> | program | Ja | Ja |
> | jitRequests | Ja | Ja |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | longtermRetentionManagedInstance/longtermRetentionDatabase/longtermRetentionBackup | Inga | Inga |
> | longtermRetentionServer/longtermRetentionDatabase/longtermRetentionBackup | Inga | Inga |
> | managedInstances | Ja | Ja |
> | managedInstances/databases | Inga | Inga |
> | managedInstances/databases/backupShortTermRetentionPolicies | Inga | Inga |
> | managedInstances/databases/scheman/tables/columns/sensitivityLabels | Inga | Inga |
> | managedInstances/databases/vulnerabilityAssessments | Inga | Inga |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Inga | Inga |
> | managedInstances/encryptionProtector | Inga | Inga |
> | managedInstances/keys | Inga | Inga |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Inga | Inga |
> | managedInstances/vulnerabilityAssessments | Inga | Inga |
> | Servrar | Ja | Ja |
> | servrar/administratörer | Inga | Inga |
> | servrar/communicationLinks | Inga | Inga |
> | servrar/databaser | Ja (se [anteckningen nedan)](#sqlnote) | Yes |
> | servrar/encryptionProtector | Inga | Inga |
> | servrar/firewallRules | Inga | Inga |
> | servrar/nycklar | Inga | Inga |
> | servrar/restorableDroppedDatabases | Inga | Inga |
> | servrar/serviceobjectives | Inga | Inga |
> | servrar/tdeCertificates | Inga | Inga |
> | virtualClusters | Ja | Ja |

<a id="sqlnote"></a>

> [!NOTE]
> Huvuddatabasen stöder inte taggar, men andra databaser, inklusive Azure Synapse Analytics databaser, stöder taggar. Azure Synapse Analytics databaser måste vara i aktivt (inte pausat) tillstånd.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ja | Ja |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Inga | Inga |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | Inga | Inga |
> | storageAccounts | Ja | Ja |
> | storageAccounts/blobServices | Inga | Inga |
> | storageAccounts/fileServices | Inga | Inga |
> | storageAccounts/queueServices | Inga | Inga |
> | storageAccounts/services | Inga | Inga |
> | storageAccounts/services/metricDefinitions | Inga | Inga |
> | storageAccounts/tableServices | Inga | Inga |
> | Användningsområden | Inga | Inga |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | amlFilesystems | Ja | Ja |
> | Cachar | Ja | Ja |
> | cacheminnen/storageTargets | Inga | Inga |
> | usageModels | Inga | Inga |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Inga | Inga |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServers | Inga | Inga |
> | storageSyncServices/syncGroups | Inga | Inga |
> | storageSyncServices/syncGroups/cloudEndpoints | Inga | Inga |
> | storageSyncServices/syncGroups/serverEndpoints | Inga | Inga |
> | storageSyncServices/arbetsflöden | Inga | Inga |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServers | Inga | Inga |
> | storageSyncServices/syncGroups | Inga | Inga |
> | storageSyncServices/syncGroups/cloudEndpoints | Inga | Inga |
> | storageSyncServices/syncGroups/serverEndpoints | Inga | Inga |
> | storageSyncServices/workflows | Inga | Inga |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServers | Inga | Inga |
> | storageSyncServices/syncGroups | Inga | Inga |
> | storageSyncServices/syncGroups/cloudEndpoints | Inga | Inga |
> | storageSyncServices/syncGroups/serverEndpoints | Inga | Inga |
> | storageSyncServices/workflows | Inga | Inga |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Chefer | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kluster | Ja | Ja |
> | kluster/privateEndpoints | Inga | Inga |
> | streamingjobs | Ja (se anteckningen nedan) | Yes |

> [!NOTE]
> Du kan inte lägga till en tagg när streamingjobs körs. Stoppa resursen för att lägga till en tagg.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | Inga | Inga |
> | acceptOwnership | Inga | Inga |
> | acceptOwnershipStatus | Inga | Inga |
> | Alias | Inga | Inga |
> | avbryt | Inga | Inga |
> | changeTenantRequest | Inga | Inga |
> | changeTenantStatus | Inga | Inga |
> | CreateSubscription | Inga | Inga |
> | Aktivera | Inga | Inga |
> | policies | Inga | Inga |
> | byt namn | Inga | Inga |
> | SubscriptionDefinitions | Inga | Inga |
> | SubscriptionOperations | Inga | Inga |
> | Prenumerationer | Inga | Inga |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Ja | Ja |
> | arbetsytor | Ja | Ja |
> | arbetsytor/bigDataPools | Ja | Ja |
> | arbetsytor/operationStatuses | Inga | Inga |
> | arbetsytor/sqlDatabaser | Ja | Ja |
> | arbetsytor/sqlPools | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Miljöer | Ja | Inga |
> | miljöer/accessPolicies | Inga | Inga |
> | miljöer/eventsources | Ja | Inga |
> | miljöer/privateEndpointConnectionProxies | Inga | Inga |
> | miljöer/privateEndpointConnections | Inga | Inga |
> | miljöer/privateLinkResources | Inga | Inga |
> | miljöer/referenceDataSets | Ja | Inga |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Butiker | Ja | Ja |
> | stores/accessPolicies | Inga | Inga |
> | butiker/tjänster | Inga | Inga |
> | stores/services/tokens | Inga | Inga |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Ja | Ja |
> | imageTemplates/runOutputs | Inga | Inga |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | ArcZones | Ja | Ja |
> | ResourcePools | Ja | Ja |
> | VCenters | Ja | Ja |
> | VCenters/InventoryItems | Inga | Inga |
> | virtualmachines | Ja | Ja |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ja | Ja |
> | dedicatedCloudServices | Ja | Ja |
> | virtualMachines | Ja | Ja |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | devices | Ja | Ja |
> | registeredSubscriptions | Inga | Inga |
> | Leverantörer | Inga | Inga |
> | leverantörer/SKU:er | Inga | Inga |
> | leverantörer/vnfs | Inga | Inga |
> | virtualNetworkFunctionSkus | Inga | Inga |
> | vnfs | Ja | Ja |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Inga |
> | Planer | Ja | Inga |
> | registeredSubscriptions | Inga | Inga |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Inga | Inga |
> | apiManagementAccounts/apiAcls | Inga | Inga |
> | apiManagementAccounts/apis | Inga | Inga |
> | apiManagementAccounts/apis/apiAcls | Inga | Inga |
> | apiManagementAccounts/apis/connectionAcls | Inga | Inga |
> | apiManagementAccounts/apis/connections | Inga | Inga |
> | apiManagementAccounts/apis/connections/connectionAcls | Inga | Inga |
> | apiManagementAccounts/apis/localizedDefinitions | Inga | Inga |
> | apiManagementAccounts/connectionAcls | Inga | Inga |
> | apiManagementAccounts/connections | Inga | Inga |
> | billingMeters | Inga | Inga |
> | certifikat | Ja | Ja |
> | connectionGateways | Ja | Ja |
> | Anslutningar | Ja | Ja |
> | customApis | Ja | Ja |
> | deletedSites | Inga | Inga |
> | functionAppStacks | Inga | Inga |
> | generateGithubAccessTokenForAppserviceCLI | Inga | Inga |
> | hostingEnvironments | Ja | Ja |
> | hostingEnvironments/eventGridFilters | Inga | Inga |
> | hostingEnvironments/multiRolePools | Inga | Inga |
> | hostingEnvironments/workerPools | Inga | Inga |
> | kubeEnvironments | Ja | Ja |
> | publishingUsers | Inga | Inga |
> | rekommendationer | Inga | Inga |
> | resourceHealthMetadata | Inga | Inga |
> | Runtimes | Inga | Inga |
> | serverFarms | Ja | Ja |
> | serverFarms/eventGridFilters | Inga | Inga |
> | serverFarms/firstPartyApps | Inga | Inga |
> | serverFarms/firstPartyApps/keyVaultSettings | Inga | Inga |
> | webbplatser | Ja | Ja |
> | platser/konfiguration  | Inga | Inga |
> | sites/eventGridFilters | Inga | Inga |
> | sites/hostNameBindings | Inga | Inga |
> | platser/networkConfig | Inga | Inga |
> | webbplatser/premieraddons | Ja | Ja |
> | platser/platser | Ja | Ja |
> | platser/platser/eventGridFilters | Inga | Inga |
> | platser/platser/hostNameBindings | Inga | Inga |
> | platser/platser/networkConfig | Inga | Inga |
> | sourceControls | Inga | Inga |
> | staticSites | Ja | Ja |
> | Validera | Inga | Inga |
> | verifyHostingEnvironmentVnet | Inga | Inga |
> | webAppStacks | Inga | Inga |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Inga | Inga |
> | diagnosticSettingsCategories | Inga | Inga |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Ja | Ja |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Ja | Ja |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | migrationAgents | Ja | Ja |
> | Arbetsbelastningar | Ja | Ja |
> | arbetsbelastningar/instanser | Inga | Inga |
> | arbetsbelastningar/versioner | Inga | Inga |
> | arbetsbelastningar/versioner/artefakter | Inga | Inga |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagga i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Bildskärmar | Inga | Nej |

## <a name="next-steps"></a>Nästa steg

Information om hur du lägger till taggar för resurser finns i [Använda taggar för att organisera dina Azure-resurser.](tag-resources.md)
