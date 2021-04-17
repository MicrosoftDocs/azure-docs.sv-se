---
title: Borttagning i complete-läget
description: Visar hur resurstyper hanterar borttagning i fullständigt läge i Azure Resource Manager mallar.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 6986f600274beaaa67f2f6ce64cbc3d0ceaf322e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576078"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Borttagning av Azure-resurser för distributioner i fullständigt läge

Den här artikeln beskriver hur resurstyper hanterar borttagning när de inte finns i en mall som distribueras i fullständigt läge.

Resurstyperna som markerats **med** Ja tas bort när typen inte är i mallen som distribuerats med fullständigt läge.

Resurstyperna som markerats **med** Nej tas inte bort automatiskt när de inte finns i mallen. De tas dock bort om den överordnade resursen tas bort. En fullständig beskrivning av beteendet finns i Azure Resource Manager [distributionslägen](deployment-modes.md).

Om du [distribuerar till fler än en resursgrupp](./deploy-to-resource-group.md)i en mall kan resurser i resursgruppen som anges i distributionsåtgärden tas bort. Resurser i de sekundära resursgrupperna tas inte bort.

Resurserna visas i listan efter resursproviderns namnområde. Information om hur du matchar ett resursprovidernamnområde med dess Azure-tjänstnamn finns [i Resursproviders för Azure-tjänster.](../management/azure-services-resource-providers.md)

> [!NOTE]
> Använd alltid [vad om-åtgärden innan](template-deploy-what-if.md) du distribuerar en mall i fullständigt läge. Vad som händer om visar vilka resurser som ska skapas, tas bort eller ändras. Använd vad om för att undvika att oavsiktligt ta bort resurser.

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
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DomainServices | Yes |
> | DomainServices/oucontainer | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | supportProviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | aadsupportcases | No |
> | addsservices | No |
> | Agenter | No |
> | anonymousapiusers | No |
> | konfiguration | No |
> | loggar | No |
> | rapporter | No |
> | servicehealthmetrics | No |
> | services | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | advisorScore | No |
> | Konfigurationer | No |
> | generateRecommendations | No |
> | metadata | No |
> | rekommendationer | No |
> | undertryckningar | No |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | farmBeats | Yes |
> | farmBeats/eventGridFilters | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | actionRules | Yes |
> | aviseringar | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | migrateFromSmartDetection | No |
> | resourceHealthAlertRules | Yes |
> | smartDetectorAlertRules | Yes |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Servrar | Yes |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kluster | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedServices | No |
> | getDomainOwnershipIdentifier | No |
> | reportFeedback | No |
> | tjänst | Yes |
> | validateServiceName | No |

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | migrateProjects | Yes |
> | migrateProjects/assessments | No |
> | migrateProjects/assessments/assessedApplications | No |
> | migrateProjects/assessments/assessedApplications/machines | No |
> | migrateProjects/assessments/assessedMachines | No |
> | migrateProjects/assessments/assessedMachines/applications | No |
> | migrateProjects/assessments/machinesToAssess | No |
> | migrateProjects/sites | No |
> | migrateProjects/sites/applianceConfigurations | No |
> | migrateProjects/sites/machines | No |
> | osVersions | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Yes |
> | configurationStores/eventGridFilters | No |
> | configurationStores/keyValues | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Spring | Yes |
> | Spring/appar | No |
> | Spring/appar/distributioner | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attestationProviders | Yes |
> | defaultProviders | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | No |
> | accessReviewScheduleSettings | No |
> | classicAdministrators | No |
> | dataAliases | No |
> | dataPolicyManifests | No |
> | denyAssignments | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | No |
> | Lås | No |
> | behörigheter | No |
> | policyAssignments | No |
> | policyDefinitions | No |
> | policyExemptions | No |
> | policySetDefinitions | No |
> | privateLinkAssociations | No |
> | providerOperations | No |
> | resourceManagementPrivateLinks | Yes |
> | roleAssignmentApprovals | No |
> | roleAssignments | No |
> | roleAssignmentScheduleInstances | No |
> | roleAssignmentScheduleRequests | No |
> | roleAssignmentSchedules | No |
> | roleAssignmentsUsageMetrics | No |
> | roleDefinitions | No |
> | roleEligibilityScheduleInstances | No |
> | roleEligibilityScheduleRequests | No |
> | roleEligibilitySchedules | No |
> | roleManagementPolicies | No |
> | roleManagementPolicyAssignments | No |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | configurationProfileAssignments | No |
> | configurationProfilePreferences | Yes |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | automationAccounts | Yes |
> | automationAccounts/configurations | Yes |
> | automationKonto/jobb | No |
> | automationAccounts/privateEndpointConnectionProxies | No |
> | automationAccounts/privateEndpointConnections | No |
> | automationAccounts/privateLinkResources | No |
> | automationAccounts/runbooks | Yes |
> | automationAccounts/softwareUpdateConfigurations | No |
> | automationAccounts/webhooks | No |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateClouds | Yes |
> | privateClouds/addons | No |
> | privateClouds/auktoriseringar | No |
> | privateClouds/cloudLinks | No |
> | privateClouds/clusters | No |
> | privateClouds/clusters/datastores | No |
> | privateClouds/globalReachConnections | No |
> | privateClouds/hcxEnterpriseSites | No |
> | privateClouds/scriptExecutions | No |
> | privateClouds/scriptPackages | No |
> | privateClouds/scriptPackages/scriptCmdlets | No |
> | privateClouds/workloadNetworks | No |
> | privateClouds/workloadNetworks/dhcpConfigurations | No |
> | privateClouds/workloadNetworks/dnsServices | No |
> | privateClouds/workloadNetworks/dnsZones | No |
> | privateClouds/workloadNetworks/gateways | No |
> | privateClouds/workloadNetworks/portMirroringProfiles | No |
> | privateClouds/workloadNetworks/segment | No |
> | privateClouds/workloadNetworks/virtualMachines | No |
> | privateClouds/workloadNetworks/vmGroups | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Genève

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Miljöer | No |
> | miljöer/konton | No |
> | miljöer/konton/namnområden | No |
> | miljöer/konton/namnområden/konfigurationer | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | b2cDirectories | Yes |
> | b2ctenants | No |
> | guestUsages | Yes |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataControllers | Yes |
> | dataWarehouseInstances | Yes |
> | postgresInstances | Yes |
> | sqlManagedInstances | Yes |
> | sqlServerInstances | Yes |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autopilotEnvironments | Yes |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | sqlServerRegistrations | Yes |
> | sqlServerRegistrations/sqlServers | No |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kataloger | Yes |
> | kataloger/produkter | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | edgeSubscriptions | Yes |
> | linkedSubscriptions | Yes |
> | Registreringar | Yes |
> | registreringar/customerSubscriptions | No |
> | registreringar/produkter | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kluster | Yes |
> | galleryImages | Yes |
> | networkInterfaces | Yes |
> | virtualHardDisks | Yes |
> | virtualMachines | Yes |
> | virtualNetworks | Yes |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | bareMetalInstances | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | batchAccounts | Yes |
> | batchAccounts/certificates | No |
> | batchAccounts/pools | No |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts/agreements | No |
> | billingAccounts/billingPermissions | No |
> | billingAccounts/billingProfiles | No |
> | billingAccounts/billingProfiles/billingPermissions | No |
> | billingAccounts/billingProfiles/billingRoleAssignments | No |
> | billingAccounts/billingProfiles/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/billingSubscriptions | No |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/customers | No |
> | billingAccounts/billingProfiles/instructions | No |
> | billingAccounts/billingProfiles/invoices | No |
> | billingAccounts/billingProfiles/invoices/pricesheet | No |
> | billingAccounts/billingProfiles/invoices/transactions | No |
> | billingAccounts/billingProfiles/invoiceSections | No |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | No |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products | No |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/billingProfiles/invoiceSections/transactions | No |
> | billingAccounts/billingProfiles/invoiceSections/transfers | No |
> | billingAccounts/billingProfiles/invoiceSections/validateDeleteInvoiceSectionEligibility | No |
> | billingAccounts/BillingProfiles/patchOperations | No |
> | billingAccounts/billingProfiles/paymentMethods | No |
> | billingAccounts/billingProfiles/policies | No |
> | billingAccounts/billingProfiles/pricesheet | No |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | No |
> | billingAccounts/billingProfiles/products | No |
> | billingAccounts/billingProfiles/reservations | No |
> | billingAccounts/billingProfiles/transactions | No |
> | billingAccounts/billingProfiles/validateDeleteBillingProfileEligibility | No |
> | billingAccounts/billingProfiles/validateDetachPaymentMethodEligibility | No |
> | billingAccounts/billingRoleAssignments | No |
> | billingAccounts/billingRoleDefinitions | No |
> | billingAccounts/billingSubscriptions | No |
> | billingAccounts/billingSubscriptions/elevateRole | No |
> | billingAccounts/billingSubscriptions/invoices | No |
> | billingAccounts/createBillingRoleAssignment | No |
> | billingAccounts/createInvoiceSectionOperations | No |
> | billingAccounts/customers | No |
> | billingAccounts/customers/billingPermissions | No |
> | billingAccounts/customers/billingSubscriptions | No |
> | billingAccounts/customers/initiateTransfer | No |
> | billingAccounts/customers/policies | No |
> | billingAccounts/customers/products | No |
> | billingAccounts/customers/transactions | No |
> | billingAccounts/customers/transfers | No |
> | billingAccounts/departments | No |
> | billingAccounts/departments/billingPermissions | No |
> | billingAccounts/departments/billingRoleAssignments | No |
> | billingAccounts/departments/billingRoleDefinitions | No |
> | billingAccounts/departments/billingSubscriptions | No |
> | billingAccounts/enrollmentAccounts | No |
> | billingAccounts/enrollmentAccounts/billingPermissions | No |
> | billingAccounts/enrollmentAccounts/billingRoleAssignments | No |
> | billingAccounts/enrollmentAccounts/billingRoleDefinitions | No |
> | billingAccounts/enrollmentAccounts/billingSubscriptions | No |
> | billingAccounts/invoices | No |
> | billingAccounts/invoices/transactions | No |
> | billingAccounts/invoices/transactionSummary | No |
> | billingAccounts/invoiceAvsnitt | No |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | No |
> | billingAccounts/invoiceSections/billingSubscriptions | No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No |
> | billingAccounts/invoiceSections/elevate | No |
> | billingAccounts/invoiceSections/initiateTransfer | No |
> | billingAccounts/invoiceSections/patchOperations | No |
> | billingAccounts/invoiceSections/productMoveOperations | No |
> | billingAccounts/invoiceSections/products | No |
> | billingAccounts/invoiceAvsnitt/produkter/överföring | No |
> | billingAccounts/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/invoiceAvsnitt/transaktioner | No |
> | billingAccounts/invoiceSections/transfers | No |
> | billingAccounts/lineOfCredit | No |
> | billingAccounts/patchOperations | No |
> | billingAccounts/payableOverage | No |
> | billingAccounts/paymentMethods | No |
> | billingAccounts/payNow | No |
> | billingAccounts/products | No |
> | billingAccounts/reservations | No |
> | billingAccounts/transactions | No |
> | billingPeriods | No |
> | billingPermissions | No |
> | billingProperty | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | No |
> | Avdelningar | No |
> | enrollmentAccounts | No |
> | Fakturor | No |
> | Kampanjer | No |
> | Överföringar | No |
> | transfers/acceptTransfer | No |
> | transfers/declineTransfer | No |
> | transfers/operationStatus | No |
> | transfers/validateTransfer | No |
> | validateAddress | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | mapApis | Yes |
> | updateCommunicationPreference | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | cordaMembers | Yes |
> | Watchers | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | TokenServices | Yes |
> | TokenServices/BlockchainNetworks | No |
> | TokenServices/Grupper | No |
> | TokenTjänster/grupper/konton | No |
> | TokenServices/TokenTemplates | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments/assignmentOperations | No |
> | blueprintAssignments/operations | No |
> | Ritningar | No |
> | skisser/artefakter | No |
> | skisser/versioner | No |
> | skisser/versioner/artefakter | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | botServices | Yes |
> | botServices/kanaler | No |
> | botServices/connections | No |
> | hostSettings | No |
> | språk | No |
> | mallar | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Redis | Yes |
> | Redis/EventGridFilters | No |
> | Redis/privateEndpointConnectionProxies | No |
> | Redis/privateEndpointConnectionProxies/validate | No |
> | Redis/privateEndpointConnections | No |
> | Redis/privateLinkResources | No |
> | redisEnterprise | Yes |
> | redisEnterprise/databases | No |
> | RedisEnterprise/privateEndpointConnectionProxies | No |
> | RedisEnterprise/privateEndpointConnectionProxies/validate | No |
> | RedisEnterprise/privateEndpointConnections | No |
> | RedisEnterprise/privateLinkResources | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appliedReservations | No |
> | autoQuotaIncrease | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice | No |
> | Kataloger | No |
> | commercialReservationOrders | No |
> | Exchange | No |
> | ownReservations | No |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders/calculateRenot | No |
> | reservationOrders/merge | No |
> | reservationOrders/reservationer | No |
> | reservationOrders/reservationer/revisioner | No |
> | reservationOrders/return | No |
> | reservationOrders/split | No |
> | reservationOrders/swap | No |
> | Reservationer | No |
> | resourceProviders | No |
> | resources | No |
> | validateReservationOrder | No |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | webbplatser | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Yes |
> | kantnoder | No |
> | Profiler | Yes |
> | profiler/afdendpoints | Yes |
> | profiler/afdendpoints/routes | No |
> | profiler/anpassadedomäner | No |
> | profiler/slutpunkter | Yes |
> | profiler/slutpunkter/anpassadedomäner | No |
> | profiler/slutpunkter/origingroups | No |
> | profiler/slutpunkter/ursprung | No |
> | profiler/origingroups | No |
> | profiler/origingroups/origins | No |
> | profiler/regeluppsättningar | No |
> | profiler/regeluppsättningar/regler | No |
> | profiler/hemligheter | No |
> | profiler/säkerhetpolicies | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certificateOrders | Yes |
> | certificateOrders/certificates | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Ändringar | No |
> | profil | No |
> | resourceChanges | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | No |
> | domainNames | Yes |
> | domainNames/capabilities | No |
> | domainNames/internalLoadBalancers | No |
> | domainNames/serviceCertificates | No |
> | domainNames/slots | No |
> | domainNames/slots/roles | No |
> | domainNames/slots/roles/metricDefinitions | No |
> | domainNames/slots/roles/metrics | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | quotas | No |
> | resourceTypes | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Yes |
> | virtualMachines/diagnosticSettings | No |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/metrics | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | No |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections/peerings | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | Yes |
> | quotas | No |
> | reservedIps | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks/virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | No |
> | Diskar | No |
> | images | No |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | No |
> | quotas | No |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/metricDefinitions | No |
> | storageAccounts/metrics | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts/services/diagnosticSettings | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/services/metrics | No |
> | storageAccounts/tableServices | No |
> | storageAccounts/vmImages | No |
> | vmImages | No |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Noder | Yes |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Planer | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | accounts/privateEndpointConnectionProxies | No |
> | accounts/privateEndpointConnections | No |
> | accounts/privateLinkResources | No |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | RateCard | No |
> | UsageAggregates | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilitySets | Yes |
> | cloudServices | Yes |
> | cloudServices/networkInterfaces | No |
> | cloudServices/publicIPAddresses | No |
> | cloudServices/roleInstances | No |
> | cloudServices/roleInstances/networkInterfaces | No |
> | cloudServices/roles | No |
> | diskAccesses | Yes |
> | diskEncryptionSets | Yes |
> | Diskar | Yes |
> | Gallerier | Yes |
> | gallerier/program | No |
> | gallerier/program/versioner | No |
> | gallerier/bilder | Yes |
> | gallerier/bilder/versioner | Yes |
> | hostGroups | Yes |
> | hostGroups/hosts | Yes |
> | images | Yes |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Yes |
> | restorePointCollections/restorePoints | No |
> | sharedVMExtensions | Yes |
> | sharedVMExtensions/versions | No |
> | sharedVMImages | Yes |
> | sharedVMImages/versions | No |
> | snapshots | Yes |
> | sshPublicKeys | Yes |
> | virtualMachines | Yes |
> | virtualMachines/extensions | Yes |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/runCommands | Yes |
> | virtualMachineScaleSets | Yes |
> | virtualMachineScaleSets/extensions | No |
> | virtualMachineScaleSets/networkInterfaces | No |
> | virtualMachineScaleSets/publicIPAddresses | No |
> | virtualMachineScaleSets/virtualMachines | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CacheNodes | Yes |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | platformAccounts | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ResourcePools | Yes |
> | VCenters | Yes |
> | VCenters/InventoryItems | No |
> | VirtualMachines | Yes |
> | VirtualMachines/Extensions | Yes |
> | VirtualMachines/GuestAgents | No |
> | VirtualMachines/HybridIdentityMetadata | No |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | AggregatedCost | No |
> | Saldon | No |
> | Budgetar | No |
> | Avgifter | No |
> | CostTags | No |
> | Krediter | No |
> | händelser | No |
> | Prognoser | No |
> | Massor | No |
> | Marknadsplatser | No |
> | Prisdokument | No |
> | Produkter | No |
> | ReservationDetails | No |
> | ReservationRecommendationDetails | No |
> | ReservationRecommendations | No |
> | ReservationSummaries | No |
> | ReservationTransactions | Nej |
> | Taggar | Nej |
> | Hyresgäster | No |
> | Termer | No |
> | UsageDetails | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerGroups | Yes |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Register | Yes |
> | register/agentPools | Yes |
> | register/byggen | No |
> | register/byggen/avbryt | No |
> | register/byggen/getLogLink | No |
> | register/buildTasks | Yes |
> | register/buildTasks/steps | No |
> | register/anslutna register | No |
> | register/anslutna register/inaktivera | No |
> | register/eventGridFilters | No |
> | register/exportPipelines | No |
> | register/generateCredentials | No |
> | register/getBuildSourceUploadUrl | No |
> | register/GetCredentials | No |
> | register/importImage | No |
> | register/importPipelines | No |
> | register/pipelineRuns | No |
> | register/privateEndpointConnectionProxies | No |
> | register/privateEndpointConnectionProxies/validate | No |
> | register/privateEndpointConnections | No |
> | register/privateLinkResources | No |
> | register/queueBuild | No |
> | register/regenerateCredential | No |
> | register/regenerateCredentials | No |
> | register/replikeringar | Yes |
> | register/körningar | No |
> | register/körningar/avbryt | No |
> | register/scheduleRun | No |
> | register/scopeMaps | No |
> | register/taskRuns | No |
> | register/uppgifter | Yes |
> | register/token | No |
> | register/updatePolicies | No |
> | register/webhooks | Yes |
> | register/webhooks/getCallbackConfig | No |
> | register/webhooks/ping | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerServices | Yes |
> | managedClusters | Yes |
> | ManagedClusters/eventGridFilters | No |
> | openShiftManagedClusters | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aviseringar | No |
> | FaktureringSkonto | No |
> | Budgetar | No |
> | CloudConnectors | No |
> | Anslutningar | Yes |
> | costAllocationRules | No |
> | Avdelningar | No |
> | Dimensioner | No |
> | EnrollmentAccounts | No |
> | Exporter | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts/Alerts | No |
> | ExternalBillingAccounts/Dimensions | No |
> | ExternalBillingAccounts/Forecast | No |
> | ExternalBillingAccounts/Query | No |
> | ExternalSubscriptions | No |
> | ExternalSubscriptions/Alerts | No |
> | ExternalSubscriptions/Dimensions | No |
> | ExternalSubscriptions/Forecast | No |
> | ExternalSubscriptions/Query | No |
> | fetchPrices | No |
> | Prognos | No |
> | GenerateDetailedCostReport | No |
> | GenerateReservationDetailsReport | No |
> | Insikter | No |
> | Fråga | No |
> | registrera | No |
> | Reportconfigs | No |
> | Rapporter | No |
> | ScheduledActions | No |
> | Inställningar | No |
> | showbackRules | No |
> | Vyer | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DisableLockbox | No |
> | EnableLockbox | No |
> | Begäranden | No |
> | TenantOptedIn | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Sammanslutningar | No |
> | resourceProviders | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Instanser | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Jobb | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbetsytor | Yes |
> | arbetsytor/dbWorkspaces | No |
> | arbetsytor/virtualNetworkPeerings | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kataloger | Yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataFactories | Yes |
> | dataFactories/diagnosticSettings | No |
> | dataFactories/metricDefinitions | No |
> | dataFactorySchema | No |
> | Fabriker | Yes |
> | fabriker/integrationRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | accounts/dataLakeStoreAccounts | No |
> | konton/storageAccounts | No |
> | konton/storageAccounts/containers | No |
> | accounts/transferAnalyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | accounts/eventGridFilters | No |
> | konton/firewallRules | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DatabaseMigrations | No |
> | services | Yes |
> | tjänster/projekt | Yes |
> | SqlMigrationServices | Yes |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | BackupVaults | Yes |
> | ResourceGuards | Yes |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | konton/resurser | No |
> | konton/resurser/datauppsättningar | No |
> | konton/resurser/inbjudningar | No |
> | konton/resurser/providersharesubscriptions | No |
> | konton/resurser/synkroniseringInställningar | No |
> | konton/sharesubscriptions | No |
> | accounts/sharesubscriptions/consumerSourceDataSets | No |
> | konton/sharesubscriptions/datasetmappings | No |
> | konton/sharesubscriptions/utlösare | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMarinaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Servrar | Yes |
> | servrar/rådgivare | No |
> | servrar/nycklar | No |
> | servrar/privateEndpointConnectionProxies | No |
> | servrar/privateEndpointConnections | No |
> | servrar/privateLinkResources | No |
> | servrar/queryTexts | No |
> | servrar/recoverableServers | No |
> | servrar/resetQueryPerformanceInsightData | No |
> | servrar/starta | No |
> | servrar/stoppa | No |
> | servrar/topQueryStatistics | No |
> | servrar/virtualNetworkRules | No |
> | servrar/waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | Servrar | Yes |
> | servrar/rådgivare | No |
> | servrar/nycklar | No |
> | servrar/privateEndpointConnectionProxies | No |
> | servrar/privateEndpointConnections | No |
> | servrar/privateLinkResources | No |
> | servrar/queryTexts | No |
> | servrar/recoverableServers | No |
> | servrar/resetQueryPerformanceInsightData | No |
> | servrar/starta | No |
> | servrar/stoppa | No |
> | servrar/topQueryStatistics | No |
> | servrar/uppgradera | No |
> | servrar/virtualNetworkRules | No |
> | servrar/waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | serverGroups | Yes |
> | serverGroupsv2 | Yes |
> | Servrar | Yes |
> | servrar/rådgivare | No |
> | servrar/nycklar | No |
> | servrar/privateEndpointConnectionProxies | No |
> | servrar/privateEndpointConnections | No |
> | servrar/privateLinkResources | No |
> | servrar/queryTexts | No |
> | servrar/recoverableServers | No |
> | servrar/resetQueryPerformanceInsightData | No |
> | servrar/topQueryStatistics | No |
> | servrar/virtualNetworkRules | No |
> | servrar/waitStatistics | No |
> | serversv2 | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | artifactSources | Yes |
> | Utbyggnader | Yes |
> | serviceTopologies | Yes |
> | serviceTopologies/services | Yes |
> | serviceTopologies/services/serviceUnits | Yes |
> | steg | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualisering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationgroups | Yes |
> | applicationgroups/applications | No |
> | applicationgroups/desktops | No |
> | applicationgroups/startmenuitems | No |
> | värdpooler | Yes |
> | hostpools/msixpackages | No |
> | hostpools/sessionhosts | No |
> | hostpools/sessionhosts/usersessions | No |
> | hostpools/usersessions | No |
> | scalingPlans | Yes |
> | arbetsytor | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ElasticPools | Yes |
> | ElasticPools/IotHubTenants | Yes |
> | ElasticPools/IotHubTenants/securitySettings | No |
> | IotHubs | Yes |
> | IotHubs/eventGridFilters | No |
> | IotHubs/securitySettings | No |
> | ProvisioningServices | Yes |
> | Användningsområden | No |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | konton/instanser | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Rörledningar | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Styrenheter | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labcenters | Yes |
> | Labs | Yes |
> | labb/miljöer | Yes |
> | labb/serviceRunners | Yes |
> | labb/virtualMachines | Yes |
> | Scheman | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | digitalTwinsInstances | Yes |
> | digitalTwinsInstances/endpoints | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cassandraClusters | Yes |
> | databaseAccountNames | No |
> | databaseAccounts | Yes |
> | restorableDatabaseAccounts | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Domäner | Yes |
> | domäner/domainOwnershipIdentifiers | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | validateDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects/clouddeployments | No |
> | lcsprojects/connectors | No |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Adresser | Yes |
> | orderCollections | Yes |
> | beställningar | Yes |
> | productFamiliesMetadata | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Domäner | Yes |
> | domäner/ämnen | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | partnerNamespaces | Yes |
> | partnerNamespaces/eventChannels | No |
> | partnerRegistreringar | Yes |
> | partnerTopics | Yes |
> | partnerTopics/eventSubscriptions | No |
> | systemTopics | Yes |
> | systemTopics/eventSubscriptions | No |
> | Ämnen | Yes |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kluster | Yes |
> | Namnområden | Yes |
> | namespaces/authorizationrules | No |
> | namespaces/disasterrecoveryconfigs | No |
> | namnrymder/eventhubs | No |
> | namespaces/eventhubs/authorizationrules | No |
> | namnområden/eventhubs/consumergroups | No |
> | namnrymder/networkrulesets | No |
> | namespaces/privateEndpointConnections | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | experimentArbetsområden | Yes |

## <a name="microsoftfalcon"></a>Microsoft.Sint

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Namnområden | Yes |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | featureConfigurations | No |
> | featureProviderNamespaces | No |
> | featureProviders | No |
> | funktioner | No |
> | Leverantörer | No |
> | subscriptionFeatureRegistrations | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Registrera | No |
> | galleryitems | No |
> | generateartifactaccessuri | No |
> | myareas | No |
> | myareas/areas | No |
> | myareas/areas/areas | No |
> | myareas/areas/areas/galleryitems | No |
> | myareas/areas/galleryitems | No |
> | myareas/galleryitems | No |
> | registrera | No |
> | resources | No |
> | retrieveresourcesbyid | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autoManagedAccounts | Yes |
> | autoManagedVmConfigurationProfiles | Yes |
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | Programvara | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hanaInstances | Yes |
> | sapMonitors | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedHSM | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | clusterPools | Yes |
> | clusterPools/clusters | Yes |
> | Kluster | Yes |
> | kluster/program | No |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | healthBots | Yes |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Yes |
> | tjänster/iomtconnectors | No |
> | tjänster/iomtconnectors/connections | No |
> | tjänster/iomtconnectors/mappningar | No |
> | tjänster/privateEndpointConnectionProxies | No |
> | tjänster/privateEndpointConnections | No |
> | tjänster/privateLinkResources | No |
> | arbetsytor | Yes |
> | arbetsytor/dicomservices | Yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Maskiner | Yes |
> | datorer/assessPatches | No |
> | datorer/tillägg | Yes |
> | datorer/installPatches | No |
> | datorer/privateLinkScopes | No |
> | privateLinkScopes | Yes |
> | privateLinkScopes/privateEndpointConnectionProxies | No |
> | privateLinkScopes/privateEndpointConnections | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataManagers | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | devices | Yes |
> | networkfunctions | Yes |
> | networkFunctionVendors | No |
> | registeredSubscriptions | No |
> | Leverantörer | No |
> | Leverantörer/vendorskus | No |
> | Leverantör/leverantörskus/förhandsversionprenumereringar | No |
> | virtualNetworkFunctions | Yes |
> | virtualNetworkFunctionVendors | No |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Komponenter | Yes |
> | networkScopes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Jobb | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appTemplates | No |
> | IoTApps | Yes |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | defenderSettings | No |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Graph | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedManagedHSMs | No |
> | deletedVaults | No |
> | hsmPools | Yes |
> | managedHSM | Yes |
> | Valv | Yes |
> | valv/accessPolicies | No |
> | valv/eventGridFilters | No |
> | valv/nycklar | No |
> | valv/nycklar/versioner | No |
> | valv/hemligheter | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | connectedClusters | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Tillägg | No |
> | sourceControlConfigurations | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kluster | Yes |
> | kluster/anslutnadatabaskonfigurationer | No |
> | kluster/databaser | No |
> | kluster/databaser/dataanslutning | No |
> | kluster/databaser/eventhubconnections | No |
> | kluster/databaser/principalassignments | No |
> | kluster/databaser/skript | No |
> | kluster/dataanslutning | No |
> | kluster/principalassignments | No |
> | kluster/deladeidentiteter | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labaccounts | Yes |
> | labplans | Yes |
> | Labs | Yes |
> | användare | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | integrationAccounts | Yes |
> | integrationServiceEnvironments | Yes |
> | integrationServiceEnvironments/managedApis | Yes |
> | isolatedEnvironments | Yes |
> | Arbetsflöden | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | commitmentPlans | Yes |
> | Webservices | Yes |
> | Arbetsytor | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | modelinventories | Yes |
> | virtualclusters | Yes |
> | arbetsytor | Yes |
> | arbetsytor/batchEndpoints | Yes |
> | arbetsytor/batchEndpoints/distributioner | Yes |
> | arbetsytor/batchEndpoints/distributioner/jobb | No |
> | arbetsytor/batchEndpoints/jobs | No |
> | arbetsytor/koder | No |
> | arbetsytor/koder/versioner | No |
> | arbetsytor/beräkningar | No |
> | arbetsytor/data | No |
> | arbetsytor/datalager | No |
> | arbetsytor/miljöer | No |
> | arbetsytor/eventGridFilters | No |
> | arbetsytor/jobb | No |
> | arbetsytor/labelingJobs | No |
> | arbetsytor/linkedServices | No |
> | arbetsytor/modeller | No |
> | arbetsytor/modeller/versioner | No |
> | arbetsytor/onlineEndpoints | Yes |
> | arbetsytor/onlineEndpoints/distributioner | Yes |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applyUpdates | No |
> | configurationAssignments | No |
> | maintenanceConfigurations | Yes |
> | publicMaintenanceConfigurations | No |
> | uppdateringar | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Identiteter | No |
> | userAssignedIdentities | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managedNetworks | Yes |
> | managedNetworks/managedNetworkGroups | Yes |
> | managedNetworks/managedNetworkPeeringPolicies | Yes |
> | avisering | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registrationAssignments | No |
> | registrationDefinitions | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | getEntities | No |
> | managementGroups | No |
> | managementGroups/settings | No |
> | resources | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | konton/skapare | Yes |
> | accounts/eventGridFilters | No |
> | konton/privateAtlases | Yes |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Macc | No |
> | Erbjuder | No |
> | offerTypes | No |
> | offerTypes/publishers | No |
> | offerTypes/publishers/offers | No |
> | offerTypes/publishers/offers/plans | No |
> | offerTypes/publishers/offers/plans/agreements | No |
> | offerTypes/publishers/offers/plans/configs | No |
> | offerTypes/publishers/offers/plans/configs/importImage | No |
> | privategalleryitems | No |
> | privateStoreClient | No |
> | privateStores | No |
> | privateStores/AdminRequestApprovals | No |
> | privateStores/offers | No |
> | privateStores/offers/acknowledgeNotification | No |
> | privateStores/queryNotificationsState | No |
> | privateStores/RequestApprovals | No |
> | privateStores/requestApprovals/query | No |
> | privateStores/requestApprovals/vargPlan | No |
> | Produkter | No |
> | Förlag | No |
> | utgivare/erbjudanden | No |
> | utgivare/erbjudanden/ändringar | No |
> | registrera | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicDevServices | Yes |
> | updateCommunicationPreference | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Avtal | No |
> | offertypes | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | mediaservices | Yes |
> | mediaservices/accountFilters | No |
> | mediaservices/assets | No |
> | mediaservices/assets/assetFilters | No |
> | mediaservices/contentKeyPolicies | No |
> | mediaservices/eventGridFilters | No |
> | mediaservices/graphInstances | No |
> | mediaservices/graphTopologies | No |
> | mediaservices/liveEventOperations | No |
> | mediaservices/liveEvents | Yes |
> | mediaservices/liveEvents/liveOutputs | No |
> | mediaservices/liveOutputOperations | No |
> | mediaservices/mediaGraphs | No |
> | mediaservices/privateEndpointConnectionOperations | No |
> | mediaservices/privateEndpointConnectionProxies | No |
> | mediaservices/privateEndpointConnections | No |
> | mediaservices/streamingEndpointOperations | No |
> | mediaservices/streamingEndpoints | Yes |
> | mediaservices/streamingLocators | No |
> | mediaservices/streamingPolicies | No |
> | mediaservices/transformeringar | No |
> | mediaservices/transforms/jobs | No |
> | videoAnalyzers | Yes |
> | videoAnalyzers/edgeModules | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appClusters | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | assessmentProjects | Yes |
> | migrateprojects | Yes |
> | moveCollections | Yes |
> | Projekt | Yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Yes |
> | objectAnchorsAccounts | Yes |
> | objectUnderstandingAccounts | Yes |
> | remoteRenderingAccounts | Yes |
> | spatialAnchorsAccounts | Yes |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Nätverk | Yes |
> | nätverk/platser | Yes |
> | packetCores | Yes |
> | Sims | Yes |
> | simProfiles | Yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | netAppAccounts | Yes |
> | netAppAccounts/accountBackups | No |
> | netAppAccounts/capacityPools | Yes |
> | netAppAccounts/capacityPools/volumes | Yes |
> | netAppAccounts/capacityPools/volumes/snapshots | No |
> | netAppAccounts/volumeGroups | No |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationGateways | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Yes |
> | bastionHosts | Yes |
> | bgpServiceCommunities | No |
> | Anslutningar | Yes |
> | ddosCustomPolicies | Yes |
> | ddosProtectionPlans | Yes |
> | dnsOperationStatuses | No |
> | dnszones | Yes |
> | dnszones/A | No |
> | dnszones/AAAA | No |
> | dnszones/all | No |
> | dnszones/CAA | No |
> | dnszones/CNAME | No |
> | dnszones/MX | No |
> | dnszones/NS | No |
> | dnszones/PTR | No |
> | dnszones/recordsets | No |
> | dnszones/SOA | No |
> | dnszones/SRV | No |
> | dnszones/TXT | No |
> | expressRouteCircuits | Yes |
> | expressRouteCrossConnections | Yes |
> | expressRouteGateways | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Yes |
> | frontdoors | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | ipGroups | Yes |
> | loadBalancers | Yes |
> | localNetworkGateways | Yes |
> | natGateways | Yes |
> | networkIntentPolicies | Yes |
> | networkInterfaces | Yes |
> | networkProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers/connectionMonitors | Yes |
> | networkWatchers/flowLogs | Yes |
> | networkWatchers/lenses | Yes |
> | networkWatchers/pingMeshes | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Yes |
> | privateDnsZones/A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones/all | No |
> | privateDnsZones/CNAME | No |
> | privateDnsZones/MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones/TXT | No |
> | privateDnsZones/virtualNetworkLinks | Yes |
> | privateEndpoints | Yes |
> | privateLinkServices | Yes |
> | publicIPAddresses | Yes |
> | publicIPPrefixes | Yes |
> | routeFilters | Yes |
> | routeTables | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Yes |
> | trafficmanagerprofiles/heatMaps | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/undernät | No |
> | virtualNetworkTaps | Yes |
> | virtualWans | Yes |
> | vpnGateways | Yes |
> | vpnSites | Yes |
> | webApplicationFirewallPolicies | Yes |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | NotebookProxies | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Namnområden | Yes |
> | namespaces/notificationHubs | Yes |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | osNamespaces | Yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | HyperVSites | Yes |
> | ImportPlatser | Yes |
> | MasterPlatser | Yes |
> | ServerPlatser | Yes |
> | VMwareSites | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kluster | Yes |
> | deletedWorkspaces | No |
> | linkTargets | No |
> | querypacks | Yes |
> | storageInsightConfigs | No |
> | arbetsytor | Yes |
> | arbetsytor/dataExporter | No |
> | arbetsytor/dataKällor | No |
> | arbetsytor/linkedServices | No |
> | arbetsytor/linkedStorageAccounts | No |
> | arbetsytor/metadata | No |
> | arbetsytor/fråga | No |
> | arbetsytor/scopedPrivateLinkProxies | No |
> | arbetsytor/storageInsightConfigs | No |
> | arbetsytor/tabeller | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hanteringsassociationer | No |
> | hanteringskonfigurationer | Yes |
> | lösningar | Yes |
> | Visningar | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cdnPeeringPrefixes | No |
> | legacyPeerings | No |
> | peerAsns | No |
> | peerings | Yes |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | peeringServices | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Intyg | No |
> | eventGridFilters | No |
> | policyEvents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | No |
> | åtgärder | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> |  -konsoler | No |
> | instrumentpaneler | Yes |
> | tenantconfigurations | No |
> | userSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Yes |
> | Hyresgäster | Yes |
> | klienter/arbetsytor | No |
> | workspaceCollections | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autoScaleVCores | Yes |
> | Kapacitet | Yes |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | enterprisePolicies | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | deletedAccounts | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations/customRollouts | No |
> | providerRegistrations/defaultRollouts | No |
> | providerRegistrations/resourceTypeRegistrations | No |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | deletedAccounts | No |
> | getDefaultAccount | No |
> | removeDefaultAccount | No |
> | setDefaultAccount | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Arbetsytor | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | Valv | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | OpenShiftClusters | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Namnområden | Yes |
> | namespaces/authorizationrules | No |
> | namnområden/hybridanslutningar | No |
> | namespaces/hybridconnections/authorizationrules | No |
> | namespaces/privateEndpointConnections | No |
> | namnrymder/wcfrelays | No |
> | namespaces/wcfrelays/authorizationrules | No |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Apparater | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Frågor | Yes |
> | resourceChangeDetails | No |
> | resourceChanges | No |
> | resources | No |
> | resourcesHistory | No |
> | subscriptionsStatus | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilityStatuses | No |
> | childAvailabilityStatuses | No |
> | childResources | No |
> | emergingissues | No |
> | händelser | No |
> | impactedResources | No |
> | metadata | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Distributioner | No |
> | distributioner/åtgärder | No |
> | deploymentScripts | Yes |
> | deploymentScripts/logs | No |
> | Länkar | No |
> | Leverantörer | No |
> | resourceGroups | No |
> | Prenumerationer | No |
> | templateSpecs | Yes |
> | templateSpecs/versions | Yes |
> | Hyresgäster | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Yes |
> | resources | Yes |
> | saasresources | No |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Moln | Yes |
> | VirtualMachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |
> | vmmservrar | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | aviseringar | No |
> | alertsSuppressionRules | No |
> | allowedConnections | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | utvärderingar | No |
> | autoDismissAlertsRules | No |
> | automatiseringar | Yes |
> | Automatisk etableringInställningar | No |
> | Efterlevnad | No |
> | anslutningar | No |
> | dataCollectionAgents | No |
> | devices | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | ingestionSettings | No |
> | Insikter | No |
> | iotAlerts | No |
> | iotAlertTypes | No |
> | iotDefenderSettings | No |
> | iotRecommendations | No |
> | iotRecommendationTypes | No |
> | iotSecuritySolutions | Yes |
> | iotSecuritySolutions/analyticsModels | No |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | No |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | No |
> | iotSecuritySolutions/iotAlerts | No |
> | iotSecuritySolutions/iotAlertTypes | No |
> | iotSecuritySolutions/iotRecommendations | No |
> | iotSecuritySolutions/iotRecommendationTypes | No |
> | iotSensors | No |
> | iotSites | No |
> | jitNetworkAccessPolicies | No |
> | jitPolicies | No |
> | onPremiseIotSensors | No |
> | policies | No |
> | priser | No |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | secureScores | No |
> | secureScores/secureScoreControls | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | No |
> | inställningar | No |
> | sqlVulnerabilityAssessments | No |
> | undervärderingar | No |
> | uppgifter | No |
> | Topologier | No |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aggregeringar | No |
> | alertRules | No |
> | alertRuleTemplates | No |
> | automationRules | No |
> | bokmärken | No |
> | Fall | No |
> | dataConnectors | No |
> | dataConnectorsCheckRequirements | No |
> | Anrikning | No |
> | Enheter | No |
> | entityQueries | No |
> | entityQueryTemplates | No |
> | incidenter | No |
> | officeConsents | No |
> | inställningar | No |
> | threatIntelligence | No |
> | visningslistor | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | consoleServices | No |
> | serialPorts | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Namnområden | Yes |
> | namespaces/authorizationrules | No |
> | namnrymder/disasterrecoveryconfigs | No |
> | namnrymder/eventgridfilters | No |
> | namnrymder/networkrulesets | No |
> | namespaces/privateEndpointConnections | No |
> | namnområden/köer | No |
> | namnområden/köer/auktoriseringsregler | No |
> | namnrymder/ämnen | No |
> | namespaces/topics/authorizationrules | No |
> | namnrymder/ämnen/prenumerationer | No |
> | namnrymder/ämnen/prenumerationer/regler | No |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Yes |
> | Kluster | Yes |
> | kluster/program | No |
> | containerGroups | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters/applications | No |
> | managedclusters | Yes |
> | managedclusters/applications | No |
> | managedclusters/applications/services | No |
> | managedclusters/applicationTypes | No |
> | managedclusters/applicationTypes/versions | No |
> | managedclusters/nodetypes | No |
> | Nätverk | Yes |
> | secretstores | Yes |
> | secretstores/certificates | No |
> | secretstores/secrets | No |
> | volumes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Yes |
> | containerGroups | Yes |
> | Gateways | Yes |
> | Nätverk | Yes |
> | secrets | Yes |
> | volumes | Yes |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Linkers | No |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations/resourceTypeRegistrations | No |
> | Utbyggnader | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SignalR | Yes |
> | SignalR/eventGridFilters | No |
> | WebPubSub | Yes |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | accounts/accountQuotaPolicies | No |
> | accounts/groupPolicies | No |
> | konton/jobb | No |
> | accounts/storageContainers | No |
> | images | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hybridUseBenefits | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationDefinitions | Yes |
> | program | Yes |
> | jitRequests | Yes |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managedInstances | Yes |
> | managedInstances/databases | Yes |
> | managedInstances/databases/backupShortTermRetentionPolicies | No |
> | managedInstances/databases/scheman/tables/columns/sensitivityLabels | No |
> | managedInstances/databases/vulnerabilityAssessments | No |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | No |
> | managedInstances/encryptionProtector | No |
> | managedInstances/keys | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No |
> | managedInstances/vulnerabilityAssessments | No |
> | Servrar | Yes |
> | servrar/administratörer | No |
> | servrar/communicationLinks | No |
> | servrar/databaser | Yes |
> | servrar/encryptionProtector | No |
> | servrar/firewallRules | No |
> | servrar/nycklar | No |
> | servrar/restorableDroppedDatabases | No |
> | servrar/serviceobjectives | No |
> | servrar/tdeCertificates | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedAccounts | No |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/tableServices | No |
> | Användningsområden | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | amlFilesystems | Yes |
> | Cachar | Yes |
> | cacheminnen/storageTargets | No |
> | usageModels | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | replicationGroups | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/arbetsflöden | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/arbetsflöden | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Chefer | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kluster | Yes |
> | kluster/privateEndpoints | No |
> | streamingjobs | Yes |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | acceptChangeTenant | No |
> | acceptOwnership | No |
> | acceptOwnershipStatus | No |
> | Alias | No |
> | avbryt | No |
> | changeTenantRequest | No |
> | changeTenantStatus | No |
> | CreateSubscription | No |
> | Aktivera | No |
> | policies | No |
> | byt namn | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |
> | Prenumerationer | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateLinkHubs | Yes |
> | arbetsytor | Yes |
> | arbetsytor/bigDataPools | Yes |
> | arbetsytor/operationStatuses | No |
> | arbetsytor/sqlDatabaser | Yes |
> | arbetsytor/sqlPools | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Miljöer | Yes |
> | miljöer/accessPolicies | No |
> | miljöer/eventsources | Yes |
> | miljöer/privateEndpointConnectionProxies | No |
> | miljöer/privateEndpointConnections | No |
> | miljöer/privateLinkResources | No |
> | miljöer/referenceDataSets | Yes |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Butiker | Yes |
> | stores/accessPolicies | No |
> | butiker/tjänster | No |
> | stores/services/tokens | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | imageTemplates | Yes |
> | imageTemplates/runOutputs | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ArcZones | Yes |
> | ResourcePools | Yes |
> | VCenters | Yes |
> | VCenters/InventoryItems | No |
> | virtualmachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Yes |
> | dedicatedCloudServices | Yes |
> | virtualMachines | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | devices | Yes |
> | registeredSubscriptions | No |
> | Leverantörer | No |
> | leverantörer/SKU:er | No |
> | leverantörer/vnfs | No |
> | virtualNetworkFunctionSkus | No |
> | vnfs | Yes |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | Planer | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts/apiAcls | No |
> | apiManagementAccounts/apis | No |
> | apiManagementAccounts/apis/apiAcls | No |
> | apiManagementAccounts/apis/connectionAcls | No |
> | apiManagementAccounts/apis/connections | No |
> | apiManagementAccounts/apis/connections/connectionAcls | No |
> | apiManagementAccounts/apis/localizedDefinitions | No |
> | apiManagementAccounts/connectionAcls | No |
> | apiManagementAccounts/connections | No |
> | billingMeters | No |
> | certifikat | Yes |
> | connectionGateways | Yes |
> | Anslutningar | Yes |
> | customApis | Yes |
> | deletedSites | No |
> | functionAppStacks | No |
> | generateGithubAccessTokenForAppserviceCLI | No |
> | hostingEnvironments | Yes |
> | hostingEnvironments/eventGridFilters | No |
> | hostingEnvironments/multiRolePools | No |
> | hostingEnvironments/workerPools | No |
> | kubeEnvironments | Yes |
> | publishingUsers | No |
> | rekommendationer | No |
> | resourceHealthMetadata | No |
> | Runtimes | No |
> | serverFarms | Yes |
> | serverFarms/eventGridFilters | No |
> | serverFarms/firstPartyApps | No |
> | serverFarms/firstPartyApps/keyVaultSettings | No |
> | webbplatser | Yes |
> | platser/konfiguration  | No |
> | sites/eventGridFilters | No |
> | sites/hostNameBindings | No |
> | platser/networkConfig | No |
> | platser/premieraddons | Yes |
> | platser/platser | Yes |
> | platser/platser/eventGridFilters | No |
> | platser/platser/hostNameBindings | No |
> | platser/platser/networkConfig | No |
> | sourceControls | No |
> | staticSites | Yes |
> | Validera | No |
> | verifyHostingEnvironmentVnet | No |
> | webAppStacks | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | multipleActivationKeys | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DeviceServices | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | migrationAgents | Yes |
> | Arbetsbelastningar | Yes |
> | arbetsbelastningar/instanser | No |
> | arbetsbelastningar/versioner | No |
> | arbetsbelastningar/versioner/artefakter | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Bildskärmar | Nej |

## <a name="next-steps"></a>Nästa steg

Om du vill hämta samma data som en fil med kommaavgränsade värden laddar du [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
