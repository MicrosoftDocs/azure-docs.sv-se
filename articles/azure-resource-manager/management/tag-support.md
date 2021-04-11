---
title: Tagga stöd för resurser
description: Visar vilka typer av Azure-resurs typer som stöder taggar. Innehåller information om alla Azure-tjänster.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 713fb5401db965787c2d9458da9afc3183a4c6a9
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227338"
---
# <a name="tag-support-for-azure-resources"></a>Tagga stöd för Azure-resurser
I den här artikeln beskrivs om en resurs typ stöder [taggar](tag-resources.md). Den kolumn som har etiketten **stöder Taggar** anger om resurs typen har en egenskap för taggen. Kolumnen med etiketten **tagg i Cost** visar om den resurs typen skickar taggen till kostnads rapporten. Du kan visa kostnader efter taggar i [Cost Management kostnads analys](../../cost-management-billing/costs/group-filter.md) och Azure- [faktura och användnings data per dag](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Hämta [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)för att hämta samma data som en fil med kommaavgränsade värden.

Hoppa till ett namn område för en resurs leverantör:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. AnyBuild](#microsoftanybuild)
> - [Microsoft. API Management](#microsoftapimanagement)
> - [Microsoft. AppAssessment](#microsoftappassessment)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. attestering](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. autohantera](#microsoftautomanage)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. Azure. Genève](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureArcData](#microsoftazurearcdata)
> - [Microsoft. AzureCIS](#microsoftazurecis)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureSphere](#microsoftazuresphere)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft. BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. fakturering](#microsoftbilling)
> - [Microsoft. Bingkartssökning](#microsoftbingmaps)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. skiss](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. kaskad](#microsoftcascade)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. ClusterStor](#microsoftclusterstor)
> - [Microsoft. Codespaces](#microsoftcodespaces)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. ConnectedCache](#microsoftconnectedcache)
> - [Microsoft. ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft. ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft. förbrukning](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. container service](#microsoftcontainerservice)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft. data-](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataFactory](#microsoftdatafactory)
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
> - [Microsoft. DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EdgeOrder](#microsoftedgeorder)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. experimentering](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft. genomik](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthBot](#microsofthealthbot)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSecurity](#microsoftiotsecurity)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. nyckel valv](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
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
> - [Microsoft. MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. Notebook](#microsoftnotebooks)
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
> - [Microsoft. PowerPlatform](#microsoftpowerplatform)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. avdelningens kontroll](#microsoftpurview)
> - [Microsoft. Quantum](#microsoftquantum)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceConnector](#microsoftresourceconnector)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. ScVmm](#microsoftscvmm)
> - [Microsoft. search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. ServiceLinker](#microsoftservicelinker)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. Singularity](#microsoftsingularity)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. Synapse](#microsoftsynapse)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ja | Ja |
> | DomainServices / oucontainer | Inga | Inga |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | supportProviders | Inga | Inga |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
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
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | advisorScore | Inga | Inga |
> | konfigurationer | Inga | Inga |
> | generateRecommendations | Inga | Inga |
> | metadata | Inga | Inga |
> | rekommendationer | Inga | Inga |
> | utelämningar | Inga | Inga |

## <a name="microsoftagfoodplatform"></a>Microsoft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | farmBeats | Ja | Ja |
> | farmBeats / eventGridFilters | Inga | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
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

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | brygghuvudservrar | Ja | Ja |

## <a name="microsoftanybuild"></a>Microsoft. AnyBuild

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kluster | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | deletedServices | Inga | Inga |
> | getDomainOwnershipIdentifier | Inga | Inga |
> | reportFeedback | Inga | Inga |
> | tjänst | Ja | Ja |
> | validateServiceName | Inga | Inga |

> [!NOTE]
> Azure API Management har endast stöd för att skapa högst 15 märkes namn/värdepar för varje tjänst.

## <a name="microsoftappassessment"></a>Microsoft. AppAssessment

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | migrateProjects | Ja | Ja |
> | migrateProjects/-utvärderingar | Inga | Inga |
> | migrateProjects/assessments/assessedApplications | Inga | Inga |
> | migrateProjects/assessments/assessedApplications/Machines | Inga | Inga |
> | migrateProjects/assessments/assessedMachines | Inga | Inga |
> | migrateProjects/bedömningar/assessedMachines/program | Inga | Inga |
> | migrateProjects/assessments/machinesToAssess | Inga | Inga |
> | migrateProjects/platser | Inga | Inga |
> | migrateProjects/Sites/applianceConfigurations | Inga | Inga |
> | migrateProjects/platser/datorer | Inga | Inga |
> | osVersions | Inga | Inga |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Inga |
> | configurationStores / eventGridFilters | Inga | Inga |
> | configurationStores/-värde | Inga | Inga |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Spring | Ja | Ja |
> | Våren/appar | Inga | Inga |
> | Våren/appar/distributioner | Inga | Inga |

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Ja | Ja |
> | defaultProviders | Inga | Inga |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | Inga | Inga |
> | accessReviewScheduleSettings | Inga | Inga |
> | classicAdministrators | Inga | Inga |
> | dataAliases | Inga | Inga |
> | dataPolicyManifests | Inga | Inga |
> | denyAssignments | Inga | Inga |
> | elevateAccess | Inga | Inga |
> | findOrphanRoleAssignments | Inga | Inga |
> | hålls | Inga | Inga |
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

## <a name="microsoftautomanage"></a>Microsoft. autohantera

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | configurationProfileAssignments | Inga | Inga |
> | configurationProfilePreferences | Ja | Ja |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ja | Ja |
> | automationAccounts/konfigurationer | Ja | Ja |
> | automationAccounts/jobb | Inga | Inga |
> | automationAccounts / privateEndpointConnectionProxies | Inga | Inga |
> | automationAccounts / privateEndpointConnections | Inga | Inga |
> | automationAccounts / privateLinkResources | Inga | Inga |
> | automationAccounts/Runbooks | Ja | Ja |
> | automationAccounts / softwareUpdateConfigurations | Inga | Inga |
> | automationAccounts/Webhooks | Inga | Inga |

> [!NOTE]
> Azure Automation har endast stöd för att skapa högst 15 märkes namn/värdepar för varje Automation-resurs.

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | privateClouds | Ja | Ja |
> | privateClouds/tillägg | Inga | Inga |
> | privateClouds/auktoriseringar | Inga | Inga |
> | privateClouds / cloudLinks | Inga | Inga |
> | privateClouds/kluster | Inga | Inga |
> | privateClouds/kluster/data lager | Inga | Inga |
> | privateClouds / globalReachConnections | Inga | Inga |
> | privateClouds / hcxEnterpriseSites | Inga | Inga |
> | privateClouds / scriptExecutions | Inga | Inga |
> | privateClouds / scriptPackages | Inga | Inga |
> | privateClouds / scriptPackages / scriptCmdlets | Inga | Inga |
> | privateClouds / workloadNetworks | Inga | Inga |
> | privateClouds / workloadNetworks / dhcpConfigurations | Inga | Inga |
> | privateClouds / workloadNetworks / dnsServices | Inga | Inga |
> | privateClouds / workloadNetworks / dnsZones | Inga | Inga |
> | privateClouds/workloadNetworks/gateways | Inga | Inga |
> | privateClouds / workloadNetworks / portMirroringProfiles | Inga | Inga |
> | privateClouds/workloadNetworks/segment | Inga | Inga |
> | privateClouds / workloadNetworks / virtualMachines | Inga | Inga |
> | privateClouds / workloadNetworks / vmGroups | Inga | Inga |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genève

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | utrymmen | Inga | Inga |
> | miljöer/konton | Inga | Inga |
> | miljöer/konton/namn områden | Inga | Inga |
> | miljöer/konton/namn rymder/konfigurationer | Inga | Inga |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Ja | Inga |
> | b2ctenants | Inga | Inga |
> | guestUsages | Ja | Ja |

## <a name="microsoftazurearcdata"></a>Microsoft. AzureArcData

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | dataControllers | Ja | Ja |
> | dataWarehouseInstances | Ja | Ja |
> | postgresInstances | Ja | Ja |
> | sqlManagedInstances | Ja | Ja |
> | sqlServerInstances | Ja | Ja |

## <a name="microsoftazurecis"></a>Microsoft. AzureCIS

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | autopilotEnvironments | Ja | Ja |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | sqlServerRegistrations | Ja | Ja |
> | sqlServerRegistrations/sqlServer | Inga | Inga |

## <a name="microsoftazuresphere"></a>Microsoft. AzureSphere

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kataloger | Ja | Ja |
> | kataloger/produkter | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Inga | Inga |
> | edgeSubscriptions | Ja | Ja |
> | linkedSubscriptions | Ja | Ja |
> | registreringar | Ja | Ja |
> | registreringar/customerSubscriptions | Inga | Inga |
> | registreringar/produkter | Inga | Inga |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kluster | Ja | Ja |
> | galleryImages | Ja | Ja |
> | networkInterfaces | Ja | Ja |
> | virtualHardDisks | Ja | Ja |
> | virtualMachines | Ja | Ja |
> | virtualNetworks | Ja | Ja |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Ja | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ja | Ja |
> | batchAccounts/certifikat | Inga | Inga |
> | batchAccounts/pooler | Inga | Inga |

## <a name="microsoftbilling"></a>Microsoft. fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Inga | Inga |
> | billingAccounts/avtal | Inga | Inga |
> | billingAccounts / billingPermissions | Inga | Inga |
> | billingAccounts / billingProfiles | Inga | Inga |
> | billingAccounts / billingProfiles / billingPermissions | Inga | Inga |
> | billingAccounts / billingProfiles / billingRoleAssignments | Inga | Inga |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Inga | Inga |
> | billingAccounts / billingProfiles / billingSubscriptions | Inga | Inga |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Inga | Inga |
> | billingAccounts/billingProfiles/kunder | Inga | Inga |
> | billingAccounts/billingProfiles/instruktioner | Inga | Inga |
> | billingAccounts/billingProfiles/fakturor | Inga | Inga |
> | billingAccounts/billingProfiles/fakturor/pris dokument | Inga | Inga |
> | billingAccounts/billingProfiles/fakturor/transaktioner | Inga | Inga |
> | billingAccounts / billingProfiles / invoiceSections | Inga | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Inga | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Inga | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Inga | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Inga | Inga |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Inga | Inga |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/Products | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/Products/transfer | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/transaktioner | Inga | Inga |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Inga | Inga |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | Inga | Inga |
> | billingAccounts / BillingProfiles / patchOperations | Inga | Inga |
> | billingAccounts / billingProfiles / paymentMethods | Inga | Inga |
> | billingAccounts/billingProfiles/policys | Inga | Inga |
> | billingAccounts/billingProfiles/pris dokument | Inga | Inga |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Inga | Inga |
> | billingAccounts/billingProfiles/Products | Inga | Inga |
> | billingAccounts/billingProfiles/reservationer | Inga | Inga |
> | billingAccounts/billingProfiles/transaktioner | Inga | Inga |
> | billingAccounts / billingProfiles / validateDeleteBillingProfileEligibility | Inga | Inga |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Inga | Inga |
> | billingAccounts / billingRoleAssignments | Inga | Inga |
> | billingAccounts / billingRoleDefinitions | Inga | Inga |
> | billingAccounts / billingSubscriptions | Inga | Inga |
> | billingAccounts / billingSubscriptions / elevateRole | Inga | Inga |
> | billingAccounts/billingSubscriptions/fakturor | Inga | Inga |
> | billingAccounts / createBillingRoleAssignment | Inga | Inga |
> | billingAccounts / createInvoiceSectionOperations | Inga | Inga |
> | billingAccounts/kunder | Inga | Inga |
> | billingAccounts/kunder/billingPermissions | Inga | Inga |
> | billingAccounts/kunder/billingSubscriptions | Inga | Inga |
> | billingAccounts/kunder/initiateTransfer | Inga | Inga |
> | billingAccounts/kunder/principer | Inga | Inga |
> | billingAccounts/kunder/produkter | Inga | Inga |
> | billingAccounts/kunder/transaktioner | Inga | Inga |
> | billingAccounts/kunder/överföringar | Inga | Inga |
> | billingAccounts/avdelningar | Inga | Inga |
> | billingAccounts/avdelningar/billingPermissions | Inga | Inga |
> | billingAccounts/avdelningar/billingRoleAssignments | Inga | Inga |
> | billingAccounts/avdelningar/billingRoleDefinitions | Inga | Inga |
> | billingAccounts/avdelningar/billingSubscriptions | Inga | Inga |
> | billingAccounts / enrollmentAccounts | Inga | Inga |
> | billingAccounts / enrollmentAccounts / billingPermissions | Inga | Inga |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Inga | Inga |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Inga | Inga |
> | billingAccounts / enrollmentAccounts / billingSubscriptions | Inga | Inga |
> | billingAccounts/fakturor | Inga | Inga |
> | billingAccounts/fakturor/transaktioner | Inga | Inga |
> | billingAccounts/fakturor/transactionSummary | Inga | Inga |
> | billingAccounts / invoiceSections | Inga | Inga |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Inga | Inga |
> | billingAccounts / invoiceSections / billingSubscriptions | Inga | Inga |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Inga | Inga |
> | billingAccounts/invoiceSections/höjning | Inga | Inga |
> | billingAccounts / invoiceSections / initiateTransfer | Inga | Inga |
> | billingAccounts / invoiceSections / patchOperations | Inga | Inga |
> | billingAccounts / invoiceSections / productMoveOperations | Inga | Inga |
> | billingAccounts/invoiceSections/Products | Inga | Inga |
> | billingAccounts/invoiceSections/Products/transfer | Inga | Inga |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Inga | Inga |
> | billingAccounts/invoiceSections/transaktioner | Inga | Inga |
> | billingAccounts/invoiceSections/överföringar | Inga | Inga |
> | billingAccounts / lineOfCredit | Inga | Inga |
> | billingAccounts / patchOperations | Inga | Inga |
> | billingAccounts / payableOverage | Inga | Inga |
> | billingAccounts / paymentMethods | Inga | Inga |
> | billingAccounts / payNow | Inga | Inga |
> | billingAccounts/produkter | Inga | Inga |
> | billingAccounts/reservationer | Inga | Inga |
> | billingAccounts/transaktioner | Inga | Inga |
> | billingPeriods | Inga | Inga |
> | billingPermissions | Inga | Inga |
> | billingProperty | Inga | Inga |
> | billingRoleAssignments | Inga | Inga |
> | billingRoleDefinitions | Inga | Inga |
> | createBillingRoleAssignment | Inga | Inga |
> | enheten | Inga | Inga |
> | enrollmentAccounts | Inga | Inga |
> | fakturor | Inga | Inga |
> | kampanjer | Inga | Inga |
> | överlåtelse | Inga | Inga |
> | överföringar/acceptTransfer | Inga | Inga |
> | överföringar/declineTransfer | Inga | Inga |
> | överföringar/operationStatus | Inga | Inga |
> | överföringar/validateTransfer | Inga | Inga |
> | validateAddress | Inga | Inga |

## <a name="microsoftbingmaps"></a>Microsoft. Bingkartssökning

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | mapApis | Ja | Ja |
> | updateCommunicationPreference | Inga | Inga |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Ja | Ja |
> | cordaMembers | Ja | Ja |
> | Övervakare | Ja | Ja |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | TokenServices | Ja | Ja |
> | TokenServices / BlockchainNetworks | Inga | Inga |
> | TokenServices/grupper | Inga | Inga |
> | TokenServices/grupper/konton | Inga | Inga |
> | TokenServices / TokenTemplates | Inga | Inga |

## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Inga | Inga |
> | blueprintAssignments / assignmentOperations | Inga | Inga |
> | blueprintAssignments/åtgärder | Inga | Inga |
> | modeller | Inga | Inga |
> | skisser/artefakter | Inga | Inga |
> | skisser/versioner | Inga | Inga |
> | skisser/versioner/artefakter | Inga | Inga |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | botServices | Ja | Ja |
> | botServices/kanaler | Inga | Inga |
> | botServices/anslutningar | Inga | Inga |
> | hostSettings | Inga | Inga |
> | språk | Inga | Inga |
> | mallar | Inga | Inga |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Redis | Ja | Ja |
> | Redis/EventGridFilters | Inga | Inga |
> | Redis/privateEndpointConnectionProxies | Inga | Inga |
> | Redis/privateEndpointConnectionProxies/validate | Inga | Inga |
> | Redis/privateEndpointConnections | Inga | Inga |
> | Redis/privateLinkResources | Inga | Inga |
> | redisEnterprise | Ja | Ja |
> | redisEnterprise/databaser | Inga | Inga |
> | RedisEnterprise / privateEndpointConnectionProxies | Inga | Inga |
> | RedisEnterprise/privateEndpointConnectionProxies/validate | Inga | Inga |
> | RedisEnterprise / privateEndpointConnections | Inga | Inga |
> | RedisEnterprise / privateLinkResources | Inga | Inga |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Inga | Inga |
> | autoQuotaIncrease | Inga | Inga |
> | calculateExchange | Inga | Inga |
> | calculatePrice | Inga | Inga |
> | calculatePurchasePrice | Inga | Inga |
> | kataloger | Inga | Inga |
> | commercialReservationOrders | Inga | Inga |
> | utväxla | Inga | Inga |
> | ownReservations | Inga | Inga |
> | placePurchaseOrder | Inga | Inga |
> | reservationOrders | Inga | Inga |
> | reservationOrders / calculateRefund | Inga | Inga |
> | reservationOrders/slå samman | Inga | Inga |
> | reservationOrders/reservationer | Inga | Inga |
> | reservationOrders/reservationer/revisioner | Inga | Inga |
> | reservationOrders/retur | Inga | Inga |
> | reservationOrders/Split | Inga | Inga |
> | reservationOrders/växling | Inga | Inga |
> | reservera | Inga | Inga |
> | resourceProviders | Inga | Inga |
> | resources | Inga | Inga |
> | validateReservationOrder | Inga | Inga |

## <a name="microsoftcascade"></a>Microsoft. kaskad

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | webbplatser | Ja | Ja |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Inga | Inga |
> | CdnWebApplicationFirewallPolicies | Ja | Ja |
> | edgenodes | Inga | Inga |
> | filer | Ja | Ja |
> | profiler/afdendpoints | Ja | Ja |
> | profiler/afdendpoints/vägar | Inga | Inga |
> | profiler/customdomains | Inga | Inga |
> | profiler/slut punkter | Ja | Ja |
> | profiler/slut punkter/customdomains | Inga | Inga |
> | profiler/slut punkter/origingroups | Inga | Inga |
> | profiler/slut punkter/ursprung | Inga | Inga |
> | profiler/origingroups | Inga | Inga |
> | profiler/origingroups/ursprung | Inga | Inga |
> | profiler/rulesets | Inga | Inga |
> | profiler/rulesets/regler | Inga | Inga |
> | profiler/hemligheter | Inga | Inga |
> | profiler/securitypolicies | Inga | Inga |
> | validateProbe | Inga | Inga |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ja | Ja |
> | certificateOrders/certifikat | Inga | Inga |
> | validateCertificateRegistrationInformation | Inga | Inga |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | något | Inga | Inga |
> | profil | Inga | Inga |
> | resourceChanges | Inga | Inga |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | funktioner | Inga | Inga |
> | Domän namn | Inga | Inga |
> | Domän namn/funktioner | Inga | Inga |
> | Domän namn/internalLoadBalancers | Inga | Inga |
> | Domän namn/serviceCertificates | Inga | Inga |
> | Domän namn/platser | Inga | Inga |
> | Domän namn/platser/roller | Inga | Inga |
> | Domän namn/platser/roller/metricDefinitions | Inga | Inga |
> | Domän namn/platser/roller/mått | Inga | Inga |
> | moveSubscriptionResources | Inga | Inga |
> | operatingSystemFamilies | Inga | Inga |
> | operatingSystems | Inga | Inga |
> | quotas | Inga | Inga |
> | resourceTypes | Inga | Inga |
> | validateSubscriptionMoveAvailability | Inga | Inga |
> | virtualMachines | Inga | Inga |
> | virtualMachines / diagnosticSettings | Inga | Inga |
> | virtualMachines / metricDefinitions | Inga | Inga |
> | virtualMachines/mått | Inga | Inga |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Inga | Inga |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | funktioner | Inga | Inga |
> | expressRouteCrossConnections | Inga | Inga |
> | expressRouteCrossConnections/peering | Inga | Inga |
> | gatewaySupportedDevices | Inga | Inga |
> | networkSecurityGroups | Inga | Inga |
> | quotas | Inga | Inga |
> | reservedIps | Inga | Inga |
> | virtualNetworks | Inga | Inga |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Inga | Inga |
> | virtualNetworks/virtualNetworkPeerings | Inga | Inga |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | funktioner | Inga | Inga |
> | disk | Inga | Inga |
> | images | Inga | Inga |
> | osImages | Inga | Inga |
> | osPlatformImages | Inga | Inga |
> | publicImages | Inga | Inga |
> | quotas | Inga | Inga |
> | storageAccounts | Inga | Inga |
> | storageAccounts/blobServices | Inga | Inga |
> | storageAccounts/fileServices | Inga | Inga |
> | storageAccounts/metricDefinitions | Inga | Inga |
> | storageAccounts/mått | Inga | Inga |
> | storageAccounts/queueServices | Inga | Inga |
> | storageAccounts/tjänster | Inga | Inga |
> | storageAccounts/tjänster/diagnosticSettings | Inga | Inga |
> | storageAccounts/tjänster/metricDefinitions | Inga | Inga |
> | storageAccounts/tjänster/mått | Inga | Inga |
> | storageAccounts/tableServices | Inga | Inga |
> | storageAccounts/vmImages | Inga | Inga |
> | vmImages | Inga | Inga |

## <a name="microsoftclusterstor"></a>Microsoft. ClusterStor

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | artikelnoder | Ja | Ja |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | utgå | Ja | Inga |
> | registeredSubscriptions | Inga | Inga |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/privateEndpointConnectionProxies | Inga | Inga |
> | konton/privateEndpointConnections | Inga | Inga |
> | konton/privateLinkResources | Inga | Inga |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | RateCard | Inga | Inga |
> | UsageAggregates | Inga | Inga |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Ja | Ja |
> | cloudServices | Ja | Ja |
> | cloudServices/networkInterfaces | Inga | Inga |
> | cloudServices/publicIPAddresses | Inga | Inga |
> | cloudServices / roleInstances | Inga | Inga |
> | cloudServices/roleInstances/networkInterfaces | Inga | Inga |
> | cloudServices/roller | Inga | Inga |
> | diskAccesses | Ja | Ja |
> | diskEncryptionSets | Ja | Ja |
> | disk | Ja | Ja |
> | gallerier | Ja | Ja |
> | gallerier/program | Inga | Inga |
> | gallerier/program/versioner | Inga | Inga |
> | gallerier/bilder | Inga | Inga |
> | gallerier/avbildningar/versioner | Inga | Inga |
> | hostGroups | Ja | Ja |
> | hostGroups/värdar | Ja | Ja |
> | images | Ja | Ja |
> | proximityPlacementGroups | Ja | Ja |
> | restorePointCollections | Ja | Ja |
> | restorePointCollections / restorePoints | Inga | Inga |
> | sharedVMExtensions | Ja | Ja |
> | sharedVMExtensions/versioner | Inga | Inga |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages/versioner | Inga | Inga |
> | snapshots | Ja | Ja |
> | sshPublicKeys | Ja | Ja |
> | virtualMachines | Ja | Ja |
> | virtualMachines/tillägg | Ja | Ja |
> | virtualMachines / metricDefinitions | Inga | Inga |
> | virtualMachines / runCommands | Ja | Ja |
> | virtualMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets/tillägg | Inga | Inga |
> | virtualMachineScaleSets/networkInterfaces | Inga | Inga |
> | virtualMachineScaleSets/publicIPAddresses | Ja | Inga |
> | virtualMachineScaleSets/virtualMachines | Inga | Inga |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Inga | Inga |

> [!NOTE]
> Du kan inte lägga till en tagg till en virtuell dator som har marker ATS som generaliserad. Du markerar en virtuell dator som generaliserad med [set-AzVm – generaliserad](/powershell/module/Az.Compute/Set-AzVM) eller [AZ VM generalize](/cli/azure/vm#az-vm-generalize).

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Ja | Ja |

## <a name="microsoftconnectedvehicle"></a>Microsoft. ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | platformAccounts | Ja | Ja |
> | registeredSubscriptions | Inga | Inga |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft. ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | ResourcePools | Ja | Ja |
> | VCenter | Ja | Ja |
> | VCenter/InventoryItems | Inga | Inga |
> | VirtualMachines | Ja | Ja |
> | VirtualMachines/tillägg | Ja | Ja |
> | VirtualMachines / GuestAgents | Inga | Inga |
> | VirtualMachines / HybridIdentityMetadata | Inga | Inga |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |

## <a name="microsoftconsumption"></a>Microsoft. förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Inga | Inga |
> | Saldon | Inga | Inga |
> | Budgetar | Inga | Inga |
> | Avgifter | Inga | Inga |
> | CostTags | Inga | Inga |
> | krediter | Inga | Inga |
> | händelser | Inga | Inga |
> | Prognoser | Inga | Inga |
> | samtliga | Inga | Inga |
> | Marknads platser | Inga | Inga |
> | Prisdokument | Inga | Inga |
> | läkemedle | Inga | Inga |
> | ReservationDetails | Inga | Inga |
> | ReservationRecommendationDetails | Inga | Inga |
> | ReservationRecommendations | Inga | Inga |
> | ReservationSummaries | Inga | Inga |
> | ReservationTransactions | Inga | Nej |
> | Taggar | Nej | Inga |
> | klienter | Inga | Inga |
> | Termer | Inga | Inga |
> | UsageDetails | Inga | Inga |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ja | Ja |
> | serviceAssociationLinks | Inga | Inga |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | register | Ja | Ja |
> | register/agentPools | Ja | Ja |
> | register/versioner | Inga | Inga |
> | register/versioner/Avbryt | Inga | Inga |
> | register/build/getLogLink | Inga | Inga |
> | register/buildTasks | Ja | Ja |
> | register/buildTasks/steg | Inga | Inga |
> | register/connectedRegistries | Inga | Inga |
> | register/connectedRegistries/inaktivera | Inga | Inga |
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
> | register/körningar/Avbryt | Inga | Inga |
> | register/scheduleRun | Inga | Inga |
> | register/scopeMaps | Inga | Inga |
> | register/taskRuns | Inga | Inga |
> | register/uppgifter | Ja | Ja |
> | register/token | Inga | Inga |
> | register/updatePolicies | Inga | Inga |
> | register/Webhooks | Ja | Ja |
> | register/Webhooks/getCallbackConfig | Inga | Inga |
> | register/Webhooks/ping | Inga | Inga |

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | containerServices | Ja | Ja |
> | managedClusters | Ja | Ja |
> | ManagedClusters / eventGridFilters | Inga | Inga |
> | openShiftManagedClusters | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Aviseringar | Inga | Inga |
> | BillingAccounts | Inga | Inga |
> | Budgetar | Inga | Inga |
> | CloudConnectors | Inga | Inga |
> | Anslutningar | Ja | Ja |
> | costAllocationRules | Inga | Inga |
> | Avdelningar | Inga | Inga |
> | Dimensioner | Inga | Inga |
> | EnrollmentAccounts | Inga | Inga |
> | Exporter | Inga | Inga |
> | ExternalBillingAccounts | Inga | Inga |
> | ExternalBillingAccounts/aviseringar | Inga | Inga |
> | ExternalBillingAccounts/dimensioner | Inga | Inga |
> | ExternalBillingAccounts/prognos | Inga | Inga |
> | ExternalBillingAccounts/fråga | Inga | Inga |
> | ExternalSubscriptions | Inga | Inga |
> | ExternalSubscriptions/aviseringar | Inga | Inga |
> | ExternalSubscriptions/dimensioner | Inga | Inga |
> | ExternalSubscriptions/prognos | Inga | Inga |
> | ExternalSubscriptions/fråga | Inga | Inga |
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

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | DisableLockbox | Inga | Inga |
> | EnableLockbox | Inga | Inga |
> | autentiseringsbegäran | Inga | Inga |
> | TenantOptedIn | Inga | Inga |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | typer | Inga | Inga |
> | resourceProviders | Ja | Ja |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | pipe | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft. data-

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | utskrifts | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | arbetsytor | Ja | Ja |
> | arbets ytor/dbWorkspaces | Inga | Inga |
> | arbets ytor/virtualNetworkPeerings | Inga | Inga |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kataloger | Ja | Ja |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | dataFactories | Ja | Ja |
> | dataFactories / diagnosticSettings | Inga | Inga |
> | dataFactories / metricDefinitions | Inga | Inga |
> | dataFactorySchema | Inga | Inga |
> | fabriker | Ja | Ja |
> | fabriker/integrationRuntimes | Inga | Inga |

> [!NOTE]
> Om du har Azure-SSIS integrerings körningar i din data fabrik kommer deras löpande kostnad att märkas med Data Factory-taggar. Körningen av Azure-SSIS integration runtime måste stoppas och startas om för att nya data Factory-Taggar ska tillämpas på deras löpande kostnad.

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/dataLakeStoreAccounts | Inga | Inga |
> | konton/storageAccounts | Inga | Inga |
> | konton/storageAccounts/behållare | Inga | Inga |
> | konton/transferAnalyticsUnits | Inga | Inga |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/eventGridFilters | Inga | Inga |
> | konton/firewallRules | Inga | Inga |

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | DatabaseMigrations | Inga | Inga |
> | services | Inga | Inga |
> | tjänster/projekt | Inga | Inga |
> | SqlMigrationServices | Ja | Ja |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Ja | Ja |
> | ResourceGuards | Ja | Ja |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/resurser | Inga | Inga |
> | konton/resurser/data uppsättningar | Inga | Inga |
> | konton/resurser/inbjudningar | Inga | Inga |
> | konton/resurser/providersharesubscriptions | Inga | Inga |
> | konton/resurser/synchronizationSettings | Inga | Inga |
> | konton/sharesubscriptions | Inga | Inga |
> | konton/sharesubscriptions/consumerSourceDataSets | Inga | Inga |
> | konton/sharesubscriptions/datasetmappings | Inga | Inga |
> | konton/sharesubscriptions/utlösare | Inga | Inga |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | brygghuvudservrar | Ja | Ja |
> | servrar/rådgivare | Inga | Inga |
> | servrar/nycklar | Inga | Inga |
> | servrar/privateEndpointConnectionProxies | Inga | Inga |
> | servrar/privateEndpointConnections | Inga | Inga |
> | servrar/privateLinkResources | Inga | Inga |
> | servrar/queryTexts | Inga | Inga |
> | servrar/recoverableServers | Inga | Inga |
> | servrar/resetQueryPerformanceInsightData | Inga | Inga |
> | servrar/start | Inga | Inga |
> | servrar/stoppa | Inga | Inga |
> | servrar/topQueryStatistics | Inga | Inga |
> | servrar/virtualNetworkRules | Inga | Inga |
> | servrar/waitStatistics | Inga | Inga |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ja | Ja |
> | brygghuvudservrar | Ja | Ja |
> | servrar/rådgivare | Inga | Inga |
> | servrar/nycklar | Inga | Inga |
> | servrar/privateEndpointConnectionProxies | Inga | Inga |
> | servrar/privateEndpointConnections | Inga | Inga |
> | servrar/privateLinkResources | Inga | Inga |
> | servrar/queryTexts | Inga | Inga |
> | servrar/recoverableServers | Inga | Inga |
> | servrar/resetQueryPerformanceInsightData | Inga | Inga |
> | servrar/start | Inga | Inga |
> | servrar/stoppa | Inga | Inga |
> | servrar/topQueryStatistics | Inga | Inga |
> | servrar/uppgradering | Inga | Inga |
> | servrar/virtualNetworkRules | Inga | Inga |
> | servrar/waitStatistics | Inga | Inga |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ja | Ja |
> | serverGroups | Ja | Ja |
> | serverGroupsv2 | Ja | Ja |
> | brygghuvudservrar | Ja | Ja |
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

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | artifactSources | Ja | Ja |
> | distributioner | Ja | Ja |
> | serviceTopologies | Ja | Ja |
> | serviceTopologies/tjänster | Ja | Ja |
> | serviceTopologies/tjänster/serviceUnits | Ja | Ja |
> | steg | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Ja | Ja |
> | applicationgroups/program | Inga | Inga |
> | applicationgroups/Station ära datorer | Inga | Inga |
> | applicationgroups / startmenuitems | Inga | Inga |
> | hostpools | Ja | Ja |
> | hostpools / msixpackages | Inga | Inga |
> | hostpools / sessionhosts | Inga | Inga |
> | hostpools / sessionhosts / usersessions | Inga | Inga |
> | hostpools / usersessions | Inga | Inga |
> | scalingPlans | Ja | Ja |
> | arbetsytor | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ja | Ja |
> | ElasticPools / IotHubTenants | Ja | Ja |
> | ElasticPools/IotHubTenants/securitySettings | Inga | Inga |
> | IotHubs | Ja | Ja |
> | IotHubs / eventGridFilters | Inga | Inga |
> | IotHubs/securitySettings | Inga | Inga |
> | ProvisioningServices | Ja | Ja |
> | användningar | Inga | Inga |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/instanser | Ja | Ja |
> | registeredSubscriptions | Inga | Inga |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | pipelines | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | domänkontrollanter | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | labcenters | Ja | Ja |
> | Laboration | Ja | Ja |
> | labb/miljöer | Ja | Ja |
> | labb/serviceRunners | Ja | Ja |
> | labb/virtualMachines | Ja | Ja |
> | scheman | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Ja | Ja |
> | digitalTwinsInstances/slut punkter | Inga | Inga |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | cassandraClusters | Ja | Ja |
> | databaseAccountNames | Inga | Inga |
> | databaseAccounts | Ja | Ja |
> | restorableDatabaseAccounts | Inga | Inga |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | domäner | Ja | Ja |
> | domäner/domainOwnershipIdentifiers | Inga | Inga |
> | generateSsoRequest | Inga | Inga |
> | topLevelDomains | Inga | Inga |
> | validateDomainRegistrationInformation | Inga | Inga |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Inga | Inga |
> | lcsprojects / clouddeployments | Inga | Inga |
> | lcsprojects/kopplingar | Inga | Inga |

## <a name="microsoftedgeorder"></a>Microsoft. EdgeOrder

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | adresser | Ja | Ja |
> | orderCollections | Ja | Ja |
> | beställningar | Ja | Ja |
> | productFamiliesMetadata | Inga | Inga |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | domäner | Ja | Ja |
> | domäner/ämnen | Inga | Inga |
> | eventSubscriptions | Inga | Inga |
> | extensionTopics | Inga | Inga |
> | partnerNamespaces | Ja | Ja |
> | partnerNamespaces/eventChannels | Inga | Inga |
> | partnerRegistrations | Ja | Ja |
> | partnerTopics | Ja | Ja |
> | partnerTopics / eventSubscriptions | Inga | Inga |
> | systemTopics | Ja | Ja |
> | systemTopics / eventSubscriptions | Inga | Inga |
> | avsnitt | Ja | Ja |
> | topicTypes | Inga | Inga |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kluster | Ja | Ja |
> | namn områden | Ja | Ja |
> | namnrymder/authorizationrules | Inga | Inga |
> | namnrymder/disasterrecoveryconfigs | Inga | Inga |
> | namnrymder/eventhubs | Inga | Inga |
> | namnrymder/eventhubs/authorizationrules | Inga | Inga |
> | namnrymder/eventhubs/consumergroups | Inga | Inga |
> | namnrymder/networkrulesets | Inga | Inga |
> | namnrymder/privateEndpointConnections | Inga | Inga |

## <a name="microsoftexperimentation"></a>Microsoft. experimentering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Ja | Ja |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | namn områden | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | featureConfigurations | Inga | Inga |
> | featureProviderNamespaces | Inga | Inga |
> | featureProviders | Inga | Inga |
> | funktioner | Inga | Inga |
> | finansiär | Inga | Inga |
> | subscriptionFeatureRegistrations | Inga | Inga |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | certifiering | Inga | Inga |
> | galleryitems | Inga | Inga |
> | generateartifactaccessuri | Inga | Inga |
> | områden | Inga | Inga |
> | område/områden | Inga | Inga |
> | område/områden/områden | Inga | Inga |
> | område/områden/områden/galleryitems | Inga | Inga |
> | områdets/områden/galleryitems | Inga | Inga |
> | områden/galleryitems | Inga | Inga |
> | registrera | Inga | Inga |
> | resources | Inga | Inga |
> | retrieveresourcesbyid | Inga | Inga |

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Ja | Ja |
> | autoManagedVmConfigurationProfiles | Ja | Ja |
> | configurationProfileAssignments | Inga | Inga |
> | guestConfigurationAssignments | Inga | Inga |
> | IntelliPoint | Inga | Inga |
> | softwareUpdateProfile | Inga | Inga |
> | softwareUpdates | Inga | Inga |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Ja | Ja |
> | sapMonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | clusterPools | Ja | Ja |
> | clusterPools/kluster | Ja | Ja |
> | kluster | Ja | Ja |
> | kluster/program | Inga | Inga |

## <a name="microsofthealthbot"></a>Microsoft. HealthBot

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | healthBots | Ja | Ja |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |
> | tjänster/iomtconnectors | Inga | Inga |
> | tjänster/iomtconnectors/anslutningar | Inga | Inga |
> | tjänster/iomtconnectors/mappningar | Inga | Inga |
> | tjänster/privateEndpointConnectionProxies | Inga | Inga |
> | tjänster/privateEndpointConnections | Inga | Inga |
> | tjänster/privateLinkResources | Inga | Inga |
> | arbetsytor | Ja | Ja |
> | arbets ytor/dicomservices | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | faxar | Ja | Ja |
> | datorer/assessPatches | Inga | Inga |
> | datorer/tillägg | Ja | Ja |
> | datorer/installPatches | Inga | Inga |
> | datorer/privateLinkScopes | Inga | Inga |
> | privateLinkScopes | Ja | Ja |
> | privateLinkScopes / privateEndpointConnectionProxies | Inga | Inga |
> | privateLinkScopes / privateEndpointConnections | Inga | Inga |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | dataManagers | Ja | Ja |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | devices | Ja | Ja |
> | networkfunctions | Ja | Ja |
> | networkFunctionVendors | Inga | Inga |
> | registeredSubscriptions | Inga | Inga |
> | Leverantören | Inga | Inga |
> | Leverantörer/vendorskus | Inga | Inga |
> | Leverantörer/vendorskus/previewsubscriptions | Inga | Inga |
> | virtualNetworkFunctions | Ja | Ja |
> | virtualNetworkFunctionVendors | Inga | Inga |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | delarna | Ja | Ja |
> | networkScopes | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | utskrifts | Ja | Ja |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | actionGroups | Ja | Ja |
> | activityLogAlerts | Ja | Ja |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | delarna | Ja | Ja |
> | komponenter/linkedStorageAccounts | Inga | Inga |
> | komponenter/ProactiveDetectionConfigs | Inga | Inga |
> | diagnosticSettings | Inga | Inga |
> | guestDiagnosticSettings | Ja | Ja |
> | guestDiagnosticSettingsAssociation | Ja | Ja |
> | logprofiles | Ja | Ja |
> | metricAlerts | Ja | Ja |
> | privateLinkScopes | Ja | Ja |
> | privateLinkScopes / privateEndpointConnections | Inga | Inga |
> | privateLinkScopes / scopedResources | Inga | Inga |
> | queryPacks | Ja | Ja |
> | queryPacks/frågor | Inga | Inga |
> | scheduledQueryRules | Ja | Ja |
> | webbtester | Ja | Ja |
> | arbetsböcker | Ja | Ja |
> | workbooktemplates | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Inga | Inga |
> | diagnosticSettingsCategories | Inga | Inga |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | appTemplates | Inga | Inga |
> | IoTApps | Ja | Ja |

## <a name="microsoftiotsecurity"></a>Microsoft. IoTSecurity

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | defenderSettings | Inga | Inga |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft. nyckel valv

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | deletedManagedHSMs | Inga | Inga |
> | deletedVaults | Inga | Inga |
> | hsmPools | Ja | Ja |
> | managedHSMs | Ja | Ja |
> | valv | Ja | Ja |
> | valv/accessPolicies | Inga | Inga |
> | valv/eventGridFilters | Inga | Inga |
> | valv/nycklar | Inga | Inga |
> | valv/nycklar/versioner | Inga | Inga |
> | valv/hemligheter | Inga | Inga |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Ja | Ja |
> | registeredSubscriptions | Inga | Inga |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | tillägg | Inga | Inga |
> | sourceControlConfigurations | Inga | Inga |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kluster | Ja | Ja |
> | kluster/attacheddatabaseconfigurations | Inga | Inga |
> | kluster/databaser | Inga | Inga |
> | kluster/databaser/dataconnections | Inga | Inga |
> | kluster/databaser/eventhubconnections | Inga | Inga |
> | kluster/databaser/principalassignments | Inga | Inga |
> | kluster/databaser/skript | Inga | Inga |
> | kluster/dataconnections | Inga | Inga |
> | kluster/principalassignments | Inga | Inga |
> | kluster/sharedidentities | Inga | Inga |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ja | Inga |
> | labplans | Ja | Ja |
> | Laboration | Ja | Ja |
> | användare | Inga | Inga |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ja | Ja |
> | integrationAccounts | Ja | Ja |
> | integrationServiceEnvironments | Ja | Ja |
> | integrationServiceEnvironments/managedApis | Inga | Inga |
> | isolatedEnvironments | Ja | Ja |
> | arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Ja | Ja |
> | WebServices | Ja | Ja |
> | Arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | modelinventories | Ja | Ja |
> | virtualclusters | Ja | Ja |
> | arbetsytor | Ja | Ja |
> | arbets ytor/batchEndpoints | Ja | Ja |
> | arbets ytor/batchEndpoints/distributioner | Ja | Ja |
> | arbets ytor/batchEndpoints/distributioner/jobb | Inga | Inga |
> | arbets ytor/batchEndpoints/jobb | Inga | Inga |
> | arbets ytor/koder | Inga | Inga |
> | arbets ytor/koder/versioner | Inga | Inga |
> | arbets ytor/beräkningar | Inga | Inga |
> | arbets ytor/data | Inga | Inga |
> | arbets ytor/data lager | Inga | Inga |
> | arbets ytor/miljöer | Inga | Inga |
> | arbets ytor/eventGridFilters | Inga | Inga |
> | arbets ytor/jobb | Inga | Inga |
> | arbets ytor/labelingJobs | Inga | Inga |
> | arbets ytor/linkedServices | Inga | Inga |
> | arbets ytor/modeller | Inga | Inga |
> | arbets ytor/modeller/versioner | Inga | Inga |
> | arbets ytor/onlineEndpoints | Ja | Ja |
> | arbets ytor/onlineEndpoints/distributioner | Ja | Ja |

> [!NOTE]
> Taggar för arbets ytor sprids inte till beräknings kluster och beräknings instanser.

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Inga | Inga |
> | configurationAssignments | Inga | Inga |
> | maintenanceConfigurations | Ja | Ja |
> | publicMaintenanceConfigurations | Inga | Inga |
> | uppdateringar | Inga | Inga |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Identiteter | Inga | Inga |
> | userAssignedIdentities | Ja | Ja |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Ja | Ja |
> | managedNetworks / managedNetworkGroups | Ja | Ja |
> | managedNetworks / managedNetworkPeeringPolicies | Ja | Ja |
> | avisering | Ja | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Inga | Inga |
> | registrationAssignments | Inga | Inga |
> | registrationDefinitions | Inga | Inga |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | getEntities | Inga | Inga |
> | managementGroups | Inga | Inga |
> | managementGroups/inställningar | Inga | Inga |
> | resources | Inga | Inga |
> | startTenantBackfill | Inga | Inga |
> | tenantBackfillStatus | Inga | Inga |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/skapare | Ja | Ja |
> | konton/eventGridFilters | Inga | Inga |
> | konton/privateAtlases | Ja | Ja |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | macc | Inga | Inga |
> | budgivning | Inga | Inga |
> | offerTypes | Inga | Inga |
> | offerTypes/utgivare | Inga | Inga |
> | offerTypes/utgivare/erbjudanden | Inga | Inga |
> | offerTypes/utgivare/erbjudanden/planer | Inga | Inga |
> | offerTypes/utgivare/erbjudanden/planer/avtal | Inga | Inga |
> | offerTypes/utgivare/erbjudanden/planer/konfigurationer | Inga | Inga |
> | offerTypes/utgivare/erbjudanden/planer/configs/importImage | Inga | Inga |
> | privategalleryitems | Inga | Inga |
> | privateStoreClient | Inga | Inga |
> | privateStores | Inga | Inga |
> | privateStores / AdminRequestApprovals | Inga | Inga |
> | privateStores/erbjudanden | Inga | Inga |
> | privateStores/erbjudanden/acknowledgeNotification | Inga | Inga |
> | privateStores / queryNotificationsState | Inga | Inga |
> | privateStores / RequestApprovals | Inga | Inga |
> | privateStores/requestApprovals/fråga | Inga | Inga |
> | privateStores / requestApprovals / withdrawPlan | Inga | Inga |
> | läkemedle | Inga | Inga |
> | Utgivare | Inga | Inga |
> | Utgivare/erbjudanden | Inga | Inga |
> | Utgivare/erbjudanden/ändringar | Inga | Inga |
> | registrera | Inga | Inga |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ja | Ja |
> | updateCommunicationPreference | Inga | Inga |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | villkor | Inga | Inga |
> | offertypes | Inga | Inga |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Media Services | Ja | Ja |
> | Media Services/accountFilters | Inga | Inga |
> | Media Services/till gångar | Inga | Inga |
> | Media Services/assets/assetFilters | Inga | Inga |
> | Media Services/contentKeyPolicies | Inga | Inga |
> | Media Services/eventGridFilters | Inga | Inga |
> | Media Services/graphInstances | Inga | Inga |
> | Media Services/graphTopologies | Inga | Inga |
> | Media Services/liveEventOperations | Inga | Inga |
> | Media Services/liveEvents | Ja | Ja |
> | Media Services/liveEvents/liveOutputs | Inga | Inga |
> | Media Services/liveOutputOperations | Inga | Inga |
> | Media Services/mediaGraphs | Inga | Inga |
> | Media Services/privateEndpointConnectionOperations | Inga | Inga |
> | Media Services/privateEndpointConnectionProxies | Inga | Inga |
> | Media Services/privateEndpointConnections | Inga | Inga |
> | Media Services/streamingEndpointOperations | Inga | Inga |
> | Media Services/strömnings slut punkter | Ja | Ja |
> | Media Services/streamingLocators | Inga | Inga |
> | Media Services/streamingPolicies | Inga | Inga |
> | Media Services/transformeringar | Inga | Inga |
> | Media Services/transformeringar/jobb | Inga | Inga |
> | videoAnalyzers | Ja | Ja |
> | videoAnalyzers / edgeModules | Inga | Inga |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | appClusters | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Ja | Ja |
> | migrateprojects | Ja | Ja |
> | moveCollections | Ja | Ja |
> | samarbetsprojekt | Ja | Ja |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ja | Ja |
> | objectAnchorsAccounts | Ja | Ja |
> | objectUnderstandingAccounts | Ja | Ja |
> | remoteRenderingAccounts | Ja | Ja |
> | spatialAnchorsAccounts | Ja | Ja |

## <a name="microsoftmobilenetwork"></a>Microsoft. MobileNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | nätet | Ja | Ja |
> | nätverk/platser | Ja | Ja |
> | packetCores | Ja | Ja |
> | sims | Ja | Ja |
> | sims / simProfiles | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ja | Inga |
> | netAppAccounts / accountBackups | Inga | Inga |
> | netAppAccounts / capacityPools | Ja | Inga |
> | netAppAccounts/capacityPools/Volumes | Ja | Inga |
> | netAppAccounts/capacityPools/volym/ögonblicks bilder | Inga | Inga |
> | netAppAccounts / volumeGroups | Inga | Inga |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ja | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja | Ja |
> | applicationSecurityGroups | Ja | Ja |
> | azureFirewallFqdnTags | Inga | Inga |
> | azureFirewalls | Ja | Inga |
> | bastionHosts | Ja | Inga |
> | bgpServiceCommunities | Inga | Inga |
> | anslutning | Ja | Ja |
> | ddosCustomPolicies | Ja | Ja |
> | ddosProtectionPlans | Ja | Ja |
> | dnsOperationStatuses | Inga | Inga |
> | dnszones | Ja | Ja |
> | dnszones/A | Inga | Inga |
> | dnszones/AAAA | Inga | Inga |
> | dnszones/alla | Inga | Inga |
> | dnszones/CAA | Inga | Inga |
> | dnszones/CNAME | Inga | Inga |
> | dnszones/MX | Inga | Inga |
> | dnszones/NS | Inga | Inga |
> | dnszones/PTR | Inga | Inga |
> | dnszones/Recordset | Inga | Inga |
> | dnszones/SOA | Inga | Inga |
> | dnszones/SRV | Inga | Inga |
> | dnszones/TXT | Inga | Inga |
> | expressRouteCircuits | Ja | Ja |
> | expressRouteCrossConnections | Ja | Ja |
> | expressRouteGateways | Ja | Ja |
> | expressRoutePorts | Ja | Ja |
> | expressRouteServiceProviders | Inga | Inga |
> | firewallPolicies | Ja | Ja |
> | frontdoors | Ja, men begränsat (se [Obs! nedan](#frontdoor)) | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ja, men begränsat (se [Obs! nedan](#frontdoor)) | Inga |
> | frontdoorWebApplicationFirewallPolicies | Ja, men begränsat (se [Obs! nedan](#frontdoor)) | Ja |
> | getDnsResourceReference | Inga | Inga |
> | internalNotify | Inga | Inga |
> | ipGroups | Ja | Ja |
> | Belastningsutjämnare | Ja | Ja |
> | localNetworkGateways | Ja | Ja |
> | natGateways | Ja | Ja |
> | networkIntentPolicies | Ja | Ja |
> | networkInterfaces | Ja | Ja |
> | networkProfiles | Ja | Ja |
> | networkSecurityGroups | Ja | Ja |
> | networkWatchers | Ja | Ja |
> | networkWatchers / connectionMonitors | Ja | Inga |
> | networkWatchers / flowLogs | Ja | Inga |
> | networkWatchers/linser | Ja | Inga |
> | networkWatchers / pingMeshes | Ja | Inga |
> | p2sVpnGateways | Ja | Ja |
> | privateDnsOperationStatuses | Inga | Inga |
> | privateDnsZones | Ja | Ja |
> | privateDnsZones/A | Inga | Inga |
> | privateDnsZones/AAAA | Inga | Inga |
> | privateDnsZones/alla | Inga | Inga |
> | privateDnsZones/CNAME | Inga | Inga |
> | privateDnsZones/MX | Inga | Inga |
> | privateDnsZones/PTR | Inga | Inga |
> | privateDnsZones/SOA | Inga | Inga |
> | privateDnsZones/SRV | Inga | Inga |
> | privateDnsZones/TXT | Inga | Inga |
> | privateDnsZones / virtualNetworkLinks | Ja | Ja |
> | privateEndpoints | Ja | Ja |
> | privateLinkServices | Ja | Ja |
> | publicIPAddresses | Ja | Ja |
> | publicIPPrefixes | Ja | Ja |
> | routeFilters | Ja | Ja |
> | routeTables | Ja | Ja |
> | serviceEndpointPolicies | Ja | Ja |
> | trafficManagerGeographicHierarchies | Inga | Inga |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/termiska kartor | Inga | Inga |
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
> För Azures frontend-tjänst kan du använda taggar när du skapar resursen, men att uppdatera eller lägga till taggar stöds inte för närvarande.


## <a name="microsoftnotebooks"></a>Microsoft. Notebook

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Inga | Inga |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | namn områden | Ja | Inga |
> | namnrymder/notificationHubs | Ja | Inga |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ja | Ja |
> | ImportSites | Ja | Ja |
> | MasterSites | Ja | Ja |
> | ServerSites | Ja | Ja |
> | VMwareSites | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kluster | Ja | Ja |
> | deletedWorkspaces | Inga | Inga |
> | linkTargets | Inga | Inga |
> | querypacks | Ja | Ja |
> | storageInsightConfigs | Inga | Inga |
> | arbetsytor | Ja | Ja |
> | arbets ytor/dataExports | Inga | Inga |
> | arbets ytor/data källor | Inga | Inga |
> | arbets ytor/linkedServices | Inga | Inga |
> | arbets ytor/linkedStorageAccounts | Inga | Inga |
> | arbets ytor/metadata | Inga | Inga |
> | arbets ytor/fråga | Inga | Inga |
> | arbets ytor/scopedPrivateLinkProxies | Inga | Inga |
> | arbets ytor/storageInsightConfigs | Inga | Inga |
> | arbets ytor/tabeller | Inga | Inga |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | managementassociations | Inga | Inga |
> | managementconfigurations | Ja | Ja |
> | lösningar | Ja | Ja |
> | vyer | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | cdnPeeringPrefixes | Inga | Inga |
> | legacyPeerings | Inga | Inga |
> | peerAsns | Inga | Inga |
> | peerings | Ja | Ja |
> | peeringServiceCountries | Inga | Inga |
> | peeringServiceProviders | Inga | Inga |
> | peeringServices | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | attesteringar | Inga | Inga |
> | eventGridFilters | Inga | Inga |
> | policyEvents | Inga | Inga |
> | policyMetadata | Inga | Inga |
> | policyStates | Inga | Inga |
> | policyTrackedResources | Inga | Inga |
> | reparationer | Inga | Inga |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> |  -konsoler | Inga | Inga |
> | instrumentpaneler | Ja | Ja |
> | tenantconfigurations | Inga | Inga |
> | userSettings | Inga | Inga |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Ja | Ja |
> | klienter | Ja | Ja |
> | innehavare/arbets ytor | Inga | Inga |
> | workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | autoScaleVCores | Ja | Ja |
> | kapaciteter | Ja | Ja |

## <a name="microsoftpowerplatform"></a>Microsoft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | enterprisePolicies | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | deletedAccounts | Inga | Inga |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Inga | Inga |
> | providerRegistrations / customRollouts | Inga | Inga |
> | providerRegistrations / defaultRollouts | Inga | Inga |
> | providerRegistrations / resourceTypeRegistrations | Inga | Inga |

## <a name="microsoftpurview"></a>Microsoft. avdelningens kontroll

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | deletedAccounts | Inga | Inga |
> | getDefaultAccount | Inga | Inga |
> | removeDefaultAccount | Inga | Inga |
> | setDefaultAccount | Inga | Inga |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Arbetsytor | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Inga | Inga |
> | valv | Ja | Ja |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | namn områden | Ja | Ja |
> | namnrymder/authorizationrules | Inga | Inga |
> | namnrymder/hybridconnections | Inga | Inga |
> | namnrymder/hybridconnections/authorizationrules | Inga | Inga |
> | namnrymder/privateEndpointConnections | Inga | Inga |
> | namnrymder/wcfrelays | Inga | Inga |
> | namnrymder/wcfrelays/authorizationrules | Inga | Inga |

## <a name="microsoftresourceconnector"></a>Microsoft. ResourceConnector

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | redskap | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | skickar | Ja | Ja |
> | resourceChangeDetails | Inga | Inga |
> | resourceChanges | Inga | Inga |
> | resources | Inga | Inga |
> | resourcesHistory | Inga | Inga |
> | subscriptionsStatus | Inga | Inga |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Inga | Inga |
> | childAvailabilityStatuses | Inga | Inga |
> | childResources | Inga | Inga |
> | emergingissues | Inga | Inga |
> | händelser | Inga | Inga |
> | impactedResources | Inga | Inga |
> | metadata | Inga | Inga |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | distributioner | Ja | Inga |
> | distributioner/åtgärder | Inga | Inga |
> | deploymentScripts | Ja | Ja |
> | deploymentScripts/loggar | Inga | Inga |
> | Länkar | Inga | Inga |
> | finansiär | Inga | Inga |
> | resourceGroups | Ja | Inga |
> | prenumerationer | Ja | Inga |
> | templateSpecs | Ja | Ja |
> | templateSpecs/versioner | Ja | Ja |
> | klienter | Inga | Inga |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | resources | Ja | Ja |
> | saasresources | Inga | Inga |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | moln | Ja | Ja |
> | VirtualMachines | Ja | Ja |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |
> | vmmservers | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft. search

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Inga | Inga |
> | searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
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
> | AutoProvisioningSettings | Inga | Inga |
> | Godkännanden | Inga | Inga |
> | anslutningar | Inga | Inga |
> | dataCollectionAgents | Inga | Inga |
> | devices | Inga | Inga |
> | deviceSecurityGroups | Inga | Inga |
> | discoveredSecuritySolutions | Inga | Inga |
> | externalSecuritySolutions | Inga | Inga |
> | InformationProtectionPolicies | Inga | Inga |
> | ingestionSettings | Inga | Inga |
> | Insights | Inga | Inga |
> | iotAlerts | Inga | Inga |
> | iotAlertTypes | Inga | Inga |
> | iotDefenderSettings | Inga | Inga |
> | iotRecommendations | Inga | Inga |
> | iotRecommendationTypes | Inga | Inga |
> | iotSecuritySolutions | Ja | Ja |
> | iotSecuritySolutions / analyticsModels | Inga | Inga |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Inga | Inga |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Inga | Inga |
> | iotSecuritySolutions / iotAlerts | Inga | Inga |
> | iotSecuritySolutions / iotAlertTypes | Inga | Inga |
> | iotSecuritySolutions / iotRecommendations | Inga | Inga |
> | iotSecuritySolutions / iotRecommendationTypes | Inga | Inga |
> | iotSensors | Inga | Inga |
> | iotSites | Inga | Inga |
> | jitNetworkAccessPolicies | Inga | Inga |
> | jitPolicies | Inga | Inga |
> | onPremiseIotSensors | Inga | Inga |
> | policies | Inga | Inga |
> | prissättningar | Inga | Inga |
> | regulatoryComplianceStandards | Inga | Inga |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Inga | Inga |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Inga | Inga |
> | secureScoreControlDefinitions | Inga | Inga |
> | secureScoreControls | Inga | Inga |
> | secureScores | Inga | Inga |
> | secureScores / secureScoreControls | Inga | Inga |
> | securityContacts | Inga | Inga |
> | securitySolutions | Inga | Inga |
> | securitySolutionsReferenceData | Inga | Inga |
> | securityStatuses | Inga | Inga |
> | securityStatusesSummaries | Inga | Inga |
> | serverVulnerabilityAssessments | Inga | Inga |
> | inställningar | Inga | Inga |
> | sqlVulnerabilityAssessments | Inga | Inga |
> | underbedömningar | Inga | Inga |
> | uppgifter | Inga | Inga |
> | topologier | Inga | Inga |
> | workspaceSettings | Inga | Inga |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Inga | Inga |
> | diagnosticSettingsCategories | Inga | Inga |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | agg regeringar | Inga | Inga |
> | alertRules | Inga | Inga |
> | alertRuleTemplates | Inga | Inga |
> | automationRules | Inga | Inga |
> | bokmärken | Inga | Inga |
> | fall | Inga | Inga |
> | dataConnectors | Inga | Inga |
> | dataConnectorsCheckRequirements | Inga | Inga |
> | berikande | Inga | Inga |
> | poster | Inga | Inga |
> | entityQueries | Inga | Inga |
> | entityQueryTemplates | Inga | Inga |
> | incidenter | Inga | Inga |
> | officeConsents | Inga | Inga |
> | inställningar | Inga | Inga |
> | threatIntelligence | Inga | Inga |
> | watchlists | Inga | Inga |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | consoleServices | Inga | Inga |
> | serialPorts | Inga | Inga |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | namn områden | Ja | Ja |
> | namnrymder/authorizationrules | Inga | Inga |
> | namnrymder/disasterrecoveryconfigs | Inga | Inga |
> | namnrymder/eventgridfilters | Inga | Inga |
> | namnrymder/networkrulesets | Inga | Inga |
> | namnrymder/privateEndpointConnections | Inga | Inga |
> | namnrymder/köer | Inga | Inga |
> | namnrymder/köer/authorizationrules | Inga | Inga |
> | namn områden/ämnen | Inga | Inga |
> | namnrymder/ämnen/authorizationrules | Inga | Inga |
> | namnrymder/ämnen/prenumerationer | Inga | Inga |
> | namn områden/ämnen/prenumerationer/regler | Inga | Inga |
> | premiumMessagingRegions | Inga | Inga |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | kluster | Ja | Ja |
> | kluster/program | Inga | Inga |
> | containerGroups | Ja | Ja |
> | containerGroupSets | Ja | Ja |
> | edgeclusters | Ja | Ja |
> | edgeclusters/program | Inga | Inga |
> | managedclusters | Ja | Ja |
> | managedclusters/program | Inga | Inga |
> | managedclusters/program/tjänster | Inga | Inga |
> | managedclusters / applicationTypes | Inga | Inga |
> | managedclusters/applicationTypes/-versioner | Inga | Inga |
> | managedclusters/nodetypes | Inga | Inga |
> | nätet | Ja | Ja |
> | secretstores | Ja | Ja |
> | secretstores/certifikat | Inga | Inga |
> | secretstores/hemligheter | Inga | Inga |
> | volumes | Ja | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | containerGroups | Ja | Ja |
> | gatewayer | Ja | Ja |
> | nätet | Ja | Ja |
> | secrets | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservicelinker"></a>Microsoft. ServiceLinker

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Länkar | Inga | Inga |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Inga | Inga |
> | providerRegistrations / resourceTypeRegistrations | Inga | Inga |
> | distributioner | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | SignalR | Ja | Ja |
> | SignalR/eventGridFilters | Inga | Inga |
> | WebPubSub | Ja | Ja |

## <a name="microsoftsingularity"></a>Microsoft. Singularity

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/accountQuotaPolicies | Inga | Inga |
> | konton/groupPolicies | Inga | Inga |
> | konton/jobb | Inga | Inga |
> | konton/storageContainers | Inga | Inga |
> | images | Inga | Inga |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Inga | Inga |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Ja | Ja |
> | program | Ja | Ja |
> | jitRequests | Ja | Ja |


## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | longtermRetentionManagedInstance/longtermRetentionDatabase/longtermRetentionBackup | Inga | Inga |
> | longtermRetentionServer/longtermRetentionDatabase/longtermRetentionBackup | Inga | Inga |
> | managedInstances | Ja | Ja |
> | managedInstances/databaser | Inga | Inga |
> | managedInstances/databaser/backupShortTermRetentionPolicies | Inga | Inga |
> | managedInstances/databaser/scheman/tabeller/kolumner/sensitivityLabels | Inga | Inga |
> | managedInstances/databaser/vulnerabilityAssessments | Inga | Inga |
> | managedInstances/databaser/vulnerabilityAssessments/regler/bas linjer | Inga | Inga |
> | managedInstances / encryptionProtector | Inga | Inga |
> | managedInstances/nycklar | Inga | Inga |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Inga | Inga |
> | managedInstances / vulnerabilityAssessments | Inga | Inga |
> | brygghuvudservrar | Ja | Ja |
> | servrar/administratörer | Inga | Inga |
> | servrar/communicationLinks | Inga | Inga |
> | servrar/databaser | Ja (se [Anmärkning nedan](#sqlnote)) | Ja |
> | servrar/encryptionProtector | Inga | Inga |
> | servrar/firewallRules | Inga | Inga |
> | servrar/nycklar | Inga | Inga |
> | servrar/restorableDroppedDatabases | Inga | Inga |
> | servrar/serviceobjectives | Inga | Inga |
> | servrar/tdeCertificates | Inga | Inga |
> | virtualClusters | Inga | Inga |

<a id="sqlnote"></a>

> [!NOTE]
> Huvud databasen stöder inte taggar, men andra databaser, inklusive Azure Synapse Analytics-databaser, stöder taggar. Azure Synapse Analytics-databaser måste vara i aktivt (inte pausat) tillstånd.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ja | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Inga | Inga |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | Inga | Inga |
> | storageAccounts | Ja | Ja |
> | storageAccounts/blobServices | Inga | Inga |
> | storageAccounts/fileServices | Inga | Inga |
> | storageAccounts/queueServices | Inga | Inga |
> | storageAccounts/tjänster | Inga | Inga |
> | storageAccounts/tjänster/metricDefinitions | Inga | Inga |
> | storageAccounts/tableServices | Inga | Inga |
> | användningar | Inga | Inga |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | amlFilesystems | Ja | Ja |
> | cacheminnen | Ja | Ja |
> | cache-/storageTargets | Inga | Inga |
> | usageModels | Inga | Inga |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Inga | Inga |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Inga | Inga |
> | storageSyncServices / syncGroups | Inga | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga | Inga |
> | storageSyncServices/arbets flöden | Inga | Inga |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Inga | Inga |
> | storageSyncServices / syncGroups | Inga | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga | Inga |
> | storageSyncServices/arbets flöden | Inga | Inga |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Inga | Inga |
> | storageSyncServices / syncGroups | Inga | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga | Inga |
> | storageSyncServices/arbets flöden | Inga | Inga |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | hantera | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kluster | Ja | Ja |
> | kluster/privateEndpoints | Inga | Inga |
> | streamingjobs | Ja (se anmärkning nedan) | Ja |

> [!NOTE]
> Du kan inte lägga till en tagg när streamingjobs körs. Stoppa resursen för att lägga till en tagg.

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | Inga | Inga |
> | acceptOwnership | Inga | Inga |
> | acceptOwnershipStatus | Inga | Inga |
> | alias | Inga | Inga |
> | avbryt | Inga | Inga |
> | changeTenantRequest | Inga | Inga |
> | changeTenantStatus | Inga | Inga |
> | CreateSubscription | Inga | Inga |
> | Aktivera | Inga | Inga |
> | policies | Inga | Inga |
> | byt namn | Inga | Inga |
> | SubscriptionDefinitions | Inga | Inga |
> | SubscriptionOperations | Inga | Inga |
> | prenumerationer | Inga | Inga |

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Ja | Ja |
> | arbetsytor | Ja | Ja |
> | arbets ytor/bigDataPools | Ja | Ja |
> | arbets ytor/operationStatuses | Inga | Inga |
> | arbets ytor/sqlDatabases | Ja | Ja |
> | arbets ytor/sqlPools | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | utrymmen | Ja | Inga |
> | miljöer/accessPolicies | Inga | Inga |
> | miljöer/eventsources | Ja | Inga |
> | miljöer/privateEndpointConnectionProxies | Inga | Inga |
> | miljöer/privateEndpointConnections | Inga | Inga |
> | miljöer/privateLinkResources | Inga | Inga |
> | miljöer/referenceDataSets | Ja | Inga |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Auktoriseringshanteraren | Ja | Ja |
> | butiker/accessPolicies | Inga | Inga |
> | butiker/tjänster | Inga | Inga |
> | butiker/tjänster/token | Inga | Inga |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Ja | Ja |
> | imageTemplates / runOutputs | Inga | Inga |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | ArcZones | Ja | Ja |
> | ResourcePools | Ja | Ja |
> | VCenter | Ja | Ja |
> | VCenter/InventoryItems | Inga | Inga |
> | virtualmachines | Ja | Ja |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ja | Ja |
> | dedicatedCloudServices | Ja | Ja |
> | virtualMachines | Ja | Ja |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | devices | Ja | Ja |
> | registeredSubscriptions | Inga | Inga |
> | leverantören | Inga | Inga |
> | leverantörer/SKU: er | Inga | Inga |
> | leverantörer/vnfs | Inga | Inga |
> | virtualNetworkFunctionSkus | Inga | Inga |
> | vnfs | Ja | Ja |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Inga |
> | utgå | Ja | Inga |
> | registeredSubscriptions | Inga | Inga |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Inga | Inga |
> | apiManagementAccounts / apiAcls | Inga | Inga |
> | apiManagementAccounts/API: er | Inga | Inga |
> | apiManagementAccounts/API/apiAcls | Inga | Inga |
> | apiManagementAccounts/API/connectionAcls | Inga | Inga |
> | apiManagementAccounts/API/anslutningar | Inga | Inga |
> | apiManagementAccounts/API/Connections/connectionAcls | Inga | Inga |
> | apiManagementAccounts/API/localizedDefinitions | Inga | Inga |
> | apiManagementAccounts / connectionAcls | Inga | Inga |
> | apiManagementAccounts/anslutningar | Inga | Inga |
> | billingMeters | Inga | Inga |
> | certifikat | Ja | Ja |
> | connectionGateways | Ja | Ja |
> | anslutning | Ja | Ja |
> | customApis | Ja | Ja |
> | deletedSites | Inga | Inga |
> | functionAppStacks | Inga | Inga |
> | generateGithubAccessTokenForAppserviceCLI | Inga | Inga |
> | hostingEnvironments | Ja | Ja |
> | hostingEnvironments / eventGridFilters | Inga | Inga |
> | hostingEnvironments / multiRolePools | Inga | Inga |
> | hostingEnvironments / workerPools | Inga | Inga |
> | kubeEnvironments | Ja | Ja |
> | publishingUsers | Inga | Inga |
> | rekommendationer | Inga | Inga |
> | resourceHealthMetadata | Inga | Inga |
> | körningar | Inga | Inga |
> | Server grupper | Ja | Ja |
> | Server grupper/eventGridFilters | Inga | Inga |
> | Server grupper/firstPartyApps | Inga | Inga |
> | Server grupper/firstPartyApps/keyVaultSettings | Inga | Inga |
> | webbplatser | Ja | Ja |
> | platser/konfig  | Inga | Inga |
> | platser/eventGridFilters | Inga | Inga |
> | platser/hostNameBindings | Inga | Inga |
> | platser/networkConfig | Inga | Inga |
> | platser/premieraddons | Ja | Ja |
> | platser/platser | Ja | Ja |
> | platser/platser/eventGridFilters | Inga | Inga |
> | platser/platser/hostNameBindings | Inga | Inga |
> | platser/platser/networkConfig | Inga | Inga |
> | sourceControls | Inga | Inga |
> | staticSites | Ja | Ja |
> | kontrollerar | Inga | Inga |
> | verifyHostingEnvironmentVnet | Inga | Inga |
> | webAppStacks | Inga | Inga |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Inga | Inga |
> | diagnosticSettingsCategories | Inga | Inga |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Ja | Ja |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Ja | Ja |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | migrationAgents | Ja | Ja |
> | arbets belastningar | Ja | Ja |
> | arbets belastningar/instanser | Inga | Inga |
> | arbets belastningar/versioner | Inga | Inga |
> | arbets belastningar/versioner/artefakter | Inga | Inga |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Övervakare | Inga | Nej |

## <a name="next-steps"></a>Nästa steg

Information om hur du använder taggar för resurser finns i [använda taggar för att ordna dina Azure-resurser](tag-resources.md).
