---
title: Borttagning i complete-läget
description: Visar hur resurs typer hanterar fullständig borttagning av läge i Azure Resource Manager mallar.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 9b1c12c061149d60f1e279c78c141cd8b245218c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228035"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Borttagning av Azure-resurser för komplett läges distributioner

I den här artikeln beskrivs hur resurs typer hanterar borttagning när de inte finns i en mall som distribueras i fullständigt läge.

De resurs typer som marker ATS med **Ja** tas bort när typen inte finns i mallen distribuerad med slutfört läge.

Resurs typerna som marker ATS med **Nej** tas inte bort automatiskt när de inte finns i mallen. de tas dock bort om den överordnade resursen tas bort. En fullständig beskrivning av beteendet finns i [Azure Resource Manager distributions lägen](deployment-modes.md).

Om du distribuerar till [fler än en resurs grupp i en mall](./deploy-to-resource-group.md)är resurserna i resurs gruppen som anges i distributions åtgärden tillgängliga för borttagning. Resurser i de sekundära resurs grupperna tas inte bort.

Resurserna visas i namn området för resurs leverantören. Information om hur du matchar ett namn område för en resurs leverantör med dess Azure-tjänst namn finns i [Resource providers för Azure-tjänster](../management/azure-services-resource-providers.md).

> [!NOTE]
> Använd alltid [åtgärden konsekvens](template-deploy-what-if.md) innan du distribuerar en mall i fullständigt läge. Vad händer om du ser vilka resurser som kommer att skapas, tas bort eller ändras. Använd what-if för att undvika oavsiktlig borttagning av resurser.

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
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DomainServices | Ja |
> | DomainServices / oucontainer | Inga |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | supportProviders | Inga |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
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
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | advisorScore | Inga |
> | konfigurationer | Inga |
> | generateRecommendations | Inga |
> | metadata | Inga |
> | rekommendationer | Inga |
> | utelämningar | Inga |

## <a name="microsoftagfoodplatform"></a>Microsoft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | farmBeats | Ja |
> | farmBeats / eventGridFilters | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | actionRules | Ja |
> | aviseringar | Inga |
> | alertsList | Inga |
> | alertsMetaData | Inga |
> | alertsSummary | Inga |
> | alertsSummaryList | Inga |
> | migrateFromSmartDetection | Inga |
> | resourceHealthAlertRules | Ja |
> | smartDetectorAlertRules | Ja |
> | smartGroups | Inga |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | brygghuvudservrar | Ja |

## <a name="microsoftanybuild"></a>Microsoft. AnyBuild

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedServices | Inga |
> | getDomainOwnershipIdentifier | Inga |
> | reportFeedback | Inga |
> | tjänst | Ja |
> | validateServiceName | Inga |

## <a name="microsoftappassessment"></a>Microsoft. AppAssessment

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | migrateProjects | Ja |
> | migrateProjects/-utvärderingar | Inga |
> | migrateProjects/assessments/assessedApplications | Inga |
> | migrateProjects/assessments/assessedApplications/Machines | Inga |
> | migrateProjects/assessments/assessedMachines | Inga |
> | migrateProjects/bedömningar/assessedMachines/program | Inga |
> | migrateProjects/assessments/machinesToAssess | Inga |
> | migrateProjects/platser | Inga |
> | migrateProjects/Sites/applianceConfigurations | Inga |
> | migrateProjects/platser/datorer | Inga |
> | osVersions | Inga |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Inga |
> | configurationStores/-värde | Inga |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Spring | Ja |
> | Våren/appar | Inga |
> | Våren/appar/distributioner | Inga |

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attestationProviders | Ja |
> | defaultProviders | Inga |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | Inga |
> | accessReviewScheduleSettings | Inga |
> | classicAdministrators | Inga |
> | dataAliases | Inga |
> | dataPolicyManifests | Inga |
> | denyAssignments | Inga |
> | elevateAccess | Inga |
> | findOrphanRoleAssignments | Inga |
> | hålls | Inga |
> | behörigheter | Inga |
> | policyAssignments | Inga |
> | policyDefinitions | Inga |
> | policyExemptions | Inga |
> | policySetDefinitions | Inga |
> | privateLinkAssociations | Inga |
> | providerOperations | Inga |
> | resourceManagementPrivateLinks | Ja |
> | roleAssignmentApprovals | Inga |
> | roleAssignments | Inga |
> | roleAssignmentScheduleInstances | Inga |
> | roleAssignmentScheduleRequests | Inga |
> | roleAssignmentSchedules | Inga |
> | roleAssignmentsUsageMetrics | Inga |
> | roleDefinitions | Inga |
> | roleEligibilityScheduleInstances | Inga |
> | roleEligibilityScheduleRequests | Inga |
> | roleEligibilitySchedules | Inga |
> | roleManagementPolicies | Inga |
> | roleManagementPolicyAssignments | Inga |

## <a name="microsoftautomanage"></a>Microsoft. autohantera

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | configurationProfileAssignments | Inga |
> | configurationProfilePreferences | Ja |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | automationAccounts | Ja |
> | automationAccounts/konfigurationer | Ja |
> | automationAccounts/jobb | Inga |
> | automationAccounts / privateEndpointConnectionProxies | Inga |
> | automationAccounts / privateEndpointConnections | Inga |
> | automationAccounts / privateLinkResources | Inga |
> | automationAccounts/Runbooks | Ja |
> | automationAccounts / softwareUpdateConfigurations | Inga |
> | automationAccounts/Webhooks | Inga |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateClouds | Ja |
> | privateClouds/tillägg | Inga |
> | privateClouds/auktoriseringar | Inga |
> | privateClouds / cloudLinks | Inga |
> | privateClouds/kluster | Inga |
> | privateClouds/kluster/data lager | Inga |
> | privateClouds / globalReachConnections | Inga |
> | privateClouds / hcxEnterpriseSites | Inga |
> | privateClouds / scriptExecutions | Inga |
> | privateClouds / scriptPackages | Inga |
> | privateClouds / scriptPackages / scriptCmdlets | Inga |
> | privateClouds / workloadNetworks | Inga |
> | privateClouds / workloadNetworks / dhcpConfigurations | Inga |
> | privateClouds / workloadNetworks / dnsServices | Inga |
> | privateClouds / workloadNetworks / dnsZones | Inga |
> | privateClouds/workloadNetworks/gateways | Inga |
> | privateClouds / workloadNetworks / portMirroringProfiles | Inga |
> | privateClouds/workloadNetworks/segment | Inga |
> | privateClouds / workloadNetworks / virtualMachines | Inga |
> | privateClouds / workloadNetworks / vmGroups | Inga |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genève

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | Inga |
> | miljöer/konton | Inga |
> | miljöer/konton/namn områden | Inga |
> | miljöer/konton/namn rymder/konfigurationer | Inga |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | b2cDirectories | Ja |
> | b2ctenants | Inga |
> | guestUsages | Ja |

## <a name="microsoftazurearcdata"></a>Microsoft. AzureArcData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataControllers | Ja |
> | dataWarehouseInstances | Ja |
> | postgresInstances | Ja |
> | sqlManagedInstances | Ja |
> | sqlServerInstances | Ja |

## <a name="microsoftazurecis"></a>Microsoft. AzureCIS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autopilotEnvironments | Ja |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | sqlServerRegistrations | Ja |
> | sqlServerRegistrations/sqlServer | Inga |

## <a name="microsoftazuresphere"></a>Microsoft. AzureSphere

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kataloger | Ja |
> | kataloger/produkter | Ja |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cloudManifestFiles | Inga |
> | edgeSubscriptions | Ja |
> | linkedSubscriptions | Ja |
> | registreringar | Ja |
> | registreringar/customerSubscriptions | Inga |
> | registreringar/produkter | Inga |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | galleryImages | Ja |
> | networkInterfaces | Ja |
> | virtualHardDisks | Ja |
> | virtualMachines | Ja |
> | virtualNetworks | Ja |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | bareMetalInstances | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | batchAccounts | Ja |
> | batchAccounts/certifikat | Inga |
> | batchAccounts/pooler | Inga |

## <a name="microsoftbilling"></a>Microsoft. fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | billingAccounts | Inga |
> | billingAccounts/avtal | Inga |
> | billingAccounts / billingPermissions | Inga |
> | billingAccounts / billingProfiles | Inga |
> | billingAccounts / billingProfiles / billingPermissions | Inga |
> | billingAccounts / billingProfiles / billingRoleAssignments | Inga |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Inga |
> | billingAccounts / billingProfiles / billingSubscriptions | Inga |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Inga |
> | billingAccounts/billingProfiles/kunder | Inga |
> | billingAccounts/billingProfiles/instruktioner | Inga |
> | billingAccounts/billingProfiles/fakturor | Inga |
> | billingAccounts/billingProfiles/fakturor/pris dokument | Inga |
> | billingAccounts/billingProfiles/fakturor/transaktioner | Inga |
> | billingAccounts / billingProfiles / invoiceSections | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Inga |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Inga |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Inga |
> | billingAccounts/billingProfiles/invoiceSections/Products | Inga |
> | billingAccounts/billingProfiles/invoiceSections/Products/transfer | Inga |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Inga |
> | billingAccounts/billingProfiles/invoiceSections/transaktioner | Inga |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Inga |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | Inga |
> | billingAccounts / BillingProfiles / patchOperations | Inga |
> | billingAccounts / billingProfiles / paymentMethods | Inga |
> | billingAccounts/billingProfiles/policys | Inga |
> | billingAccounts/billingProfiles/pris dokument | Inga |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Inga |
> | billingAccounts/billingProfiles/Products | Inga |
> | billingAccounts/billingProfiles/reservationer | Inga |
> | billingAccounts/billingProfiles/transaktioner | Inga |
> | billingAccounts / billingProfiles / validateDeleteBillingProfileEligibility | Inga |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Inga |
> | billingAccounts / billingRoleAssignments | Inga |
> | billingAccounts / billingRoleDefinitions | Inga |
> | billingAccounts / billingSubscriptions | Inga |
> | billingAccounts / billingSubscriptions / elevateRole | Inga |
> | billingAccounts/billingSubscriptions/fakturor | Inga |
> | billingAccounts / createBillingRoleAssignment | Inga |
> | billingAccounts / createInvoiceSectionOperations | Inga |
> | billingAccounts/kunder | Inga |
> | billingAccounts/kunder/billingPermissions | Inga |
> | billingAccounts/kunder/billingSubscriptions | Inga |
> | billingAccounts/kunder/initiateTransfer | Inga |
> | billingAccounts/kunder/principer | Inga |
> | billingAccounts/kunder/produkter | Inga |
> | billingAccounts/kunder/transaktioner | Inga |
> | billingAccounts/kunder/överföringar | Inga |
> | billingAccounts/avdelningar | Inga |
> | billingAccounts/avdelningar/billingPermissions | Inga |
> | billingAccounts/avdelningar/billingRoleAssignments | Inga |
> | billingAccounts/avdelningar/billingRoleDefinitions | Inga |
> | billingAccounts/avdelningar/billingSubscriptions | Inga |
> | billingAccounts / enrollmentAccounts | Inga |
> | billingAccounts / enrollmentAccounts / billingPermissions | Inga |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Inga |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Inga |
> | billingAccounts / enrollmentAccounts / billingSubscriptions | Inga |
> | billingAccounts/fakturor | Inga |
> | billingAccounts/fakturor/transaktioner | Inga |
> | billingAccounts/fakturor/transactionSummary | Inga |
> | billingAccounts / invoiceSections | Inga |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Inga |
> | billingAccounts / invoiceSections / billingSubscriptions | Inga |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Inga |
> | billingAccounts/invoiceSections/höjning | Inga |
> | billingAccounts / invoiceSections / initiateTransfer | Inga |
> | billingAccounts / invoiceSections / patchOperations | Inga |
> | billingAccounts / invoiceSections / productMoveOperations | Inga |
> | billingAccounts/invoiceSections/Products | Inga |
> | billingAccounts/invoiceSections/Products/transfer | Inga |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Inga |
> | billingAccounts/invoiceSections/transaktioner | Inga |
> | billingAccounts/invoiceSections/överföringar | Inga |
> | billingAccounts / lineOfCredit | Inga |
> | billingAccounts / patchOperations | Inga |
> | billingAccounts / payableOverage | Inga |
> | billingAccounts / paymentMethods | Inga |
> | billingAccounts / payNow | Inga |
> | billingAccounts/produkter | Inga |
> | billingAccounts/reservationer | Inga |
> | billingAccounts/transaktioner | Inga |
> | billingPeriods | Inga |
> | billingPermissions | Inga |
> | billingProperty | Inga |
> | billingRoleAssignments | Inga |
> | billingRoleDefinitions | Inga |
> | createBillingRoleAssignment | Inga |
> | enheten | Inga |
> | enrollmentAccounts | Inga |
> | fakturor | Inga |
> | kampanjer | Inga |
> | överlåtelse | Inga |
> | överföringar/acceptTransfer | Inga |
> | överföringar/declineTransfer | Inga |
> | överföringar/operationStatus | Inga |
> | överföringar/validateTransfer | Inga |
> | validateAddress | Inga |

## <a name="microsoftbingmaps"></a>Microsoft. Bingkartssökning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | mapApis | Ja |
> | updateCommunicationPreference | Inga |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blockchainMembers | Ja |
> | cordaMembers | Ja |
> | Övervakare | Ja |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | TokenServices | Ja |
> | TokenServices / BlockchainNetworks | Inga |
> | TokenServices/grupper | Inga |
> | TokenServices/grupper/konton | Inga |
> | TokenServices / TokenTemplates | Inga |

## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blueprintAssignments | Inga |
> | blueprintAssignments / assignmentOperations | Inga |
> | blueprintAssignments/åtgärder | Inga |
> | modeller | Inga |
> | skisser/artefakter | Inga |
> | skisser/versioner | Inga |
> | skisser/versioner/artefakter | Inga |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | botServices | Ja |
> | botServices/kanaler | Inga |
> | botServices/anslutningar | Inga |
> | hostSettings | Inga |
> | språk | Inga |
> | mallar | Inga |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Redis | Ja |
> | Redis/EventGridFilters | Inga |
> | Redis/privateEndpointConnectionProxies | Inga |
> | Redis/privateEndpointConnectionProxies/validate | Inga |
> | Redis/privateEndpointConnections | Inga |
> | Redis/privateLinkResources | Inga |
> | redisEnterprise | Ja |
> | redisEnterprise/databaser | Inga |
> | RedisEnterprise / privateEndpointConnectionProxies | Inga |
> | RedisEnterprise/privateEndpointConnectionProxies/validate | Inga |
> | RedisEnterprise / privateEndpointConnections | Inga |
> | RedisEnterprise / privateLinkResources | Inga |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appliedReservations | Inga |
> | autoQuotaIncrease | Inga |
> | calculateExchange | Inga |
> | calculatePrice | Inga |
> | calculatePurchasePrice | Inga |
> | kataloger | Inga |
> | commercialReservationOrders | Inga |
> | utväxla | Inga |
> | ownReservations | Inga |
> | placePurchaseOrder | Inga |
> | reservationOrders | Inga |
> | reservationOrders / calculateRefund | Inga |
> | reservationOrders/slå samman | Inga |
> | reservationOrders/reservationer | Inga |
> | reservationOrders/reservationer/revisioner | Inga |
> | reservationOrders/retur | Inga |
> | reservationOrders/Split | Inga |
> | reservationOrders/växling | Inga |
> | reservera | Inga |
> | resourceProviders | Inga |
> | resources | Inga |
> | validateReservationOrder | Inga |

## <a name="microsoftcascade"></a>Microsoft. kaskad

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | webbplatser | Ja |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Inga |
> | CdnWebApplicationFirewallPolicies | Ja |
> | edgenodes | Inga |
> | filer | Ja |
> | profiler/afdendpoints | Ja |
> | profiler/afdendpoints/vägar | Inga |
> | profiler/customdomains | Inga |
> | profiler/slut punkter | Ja |
> | profiler/slut punkter/customdomains | Inga |
> | profiler/slut punkter/origingroups | Inga |
> | profiler/slut punkter/ursprung | Inga |
> | profiler/origingroups | Inga |
> | profiler/origingroups/ursprung | Inga |
> | profiler/rulesets | Inga |
> | profiler/rulesets/regler | Inga |
> | profiler/hemligheter | Inga |
> | profiler/securitypolicies | Inga |
> | validateProbe | Inga |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certificateOrders | Ja |
> | certificateOrders/certifikat | Inga |
> | validateCertificateRegistrationInformation | Inga |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | något | Inga |
> | profil | Inga |
> | resourceChanges | Inga |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | Inga |
> | Domän namn | Ja |
> | Domän namn/funktioner | Inga |
> | Domän namn/internalLoadBalancers | Inga |
> | Domän namn/serviceCertificates | Inga |
> | Domän namn/platser | Inga |
> | Domän namn/platser/roller | Inga |
> | Domän namn/platser/roller/metricDefinitions | Inga |
> | Domän namn/platser/roller/mått | Inga |
> | moveSubscriptionResources | Inga |
> | operatingSystemFamilies | Inga |
> | operatingSystems | Inga |
> | quotas | Inga |
> | resourceTypes | Inga |
> | validateSubscriptionMoveAvailability | Inga |
> | virtualMachines | Ja |
> | virtualMachines / diagnosticSettings | Inga |
> | virtualMachines / metricDefinitions | Inga |
> | virtualMachines/mått | Inga |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicInfrastructureResources | Inga |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | Inga |
> | expressRouteCrossConnections | Inga |
> | expressRouteCrossConnections/peering | Inga |
> | gatewaySupportedDevices | Inga |
> | networkSecurityGroups | Ja |
> | quotas | Inga |
> | reservedIps | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Inga |
> | virtualNetworks/virtualNetworkPeerings | Inga |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | Inga |
> | disk | Inga |
> | images | Inga |
> | osImages | Inga |
> | osPlatformImages | Inga |
> | publicImages | Inga |
> | quotas | Inga |
> | storageAccounts | Ja |
> | storageAccounts/blobServices | Inga |
> | storageAccounts/fileServices | Inga |
> | storageAccounts/metricDefinitions | Inga |
> | storageAccounts/mått | Inga |
> | storageAccounts/queueServices | Inga |
> | storageAccounts/tjänster | Inga |
> | storageAccounts/tjänster/diagnosticSettings | Inga |
> | storageAccounts/tjänster/metricDefinitions | Inga |
> | storageAccounts/tjänster/mått | Inga |
> | storageAccounts/tableServices | Inga |
> | storageAccounts/vmImages | Inga |
> | vmImages | Inga |

## <a name="microsoftclusterstor"></a>Microsoft. ClusterStor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | artikelnoder | Ja |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utgå | Ja |
> | registeredSubscriptions | Inga |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/privateEndpointConnectionProxies | Inga |
> | konton/privateEndpointConnections | Inga |
> | konton/privateLinkResources | Inga |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | RateCard | Inga |
> | UsageAggregates | Inga |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilitySets | Ja |
> | cloudServices | Ja |
> | cloudServices/networkInterfaces | Inga |
> | cloudServices/publicIPAddresses | Inga |
> | cloudServices / roleInstances | Inga |
> | cloudServices/roleInstances/networkInterfaces | Inga |
> | cloudServices/roller | Inga |
> | diskAccesses | Ja |
> | diskEncryptionSets | Ja |
> | disk | Ja |
> | gallerier | Ja |
> | gallerier/program | Inga |
> | gallerier/program/versioner | Inga |
> | gallerier/bilder | Inga |
> | gallerier/avbildningar/versioner | Inga |
> | hostGroups | Ja |
> | hostGroups/värdar | Ja |
> | images | Ja |
> | proximityPlacementGroups | Ja |
> | restorePointCollections | Ja |
> | restorePointCollections / restorePoints | Inga |
> | sharedVMExtensions | Ja |
> | sharedVMExtensions/versioner | Inga |
> | sharedVMImages | Ja |
> | sharedVMImages/versioner | Inga |
> | snapshots | Ja |
> | sshPublicKeys | Ja |
> | virtualMachines | Ja |
> | virtualMachines/tillägg | Ja |
> | virtualMachines / metricDefinitions | Inga |
> | virtualMachines / runCommands | Ja |
> | virtualMachineScaleSets | Ja |
> | virtualMachineScaleSets/tillägg | Inga |
> | virtualMachineScaleSets/networkInterfaces | Inga |
> | virtualMachineScaleSets/publicIPAddresses | Inga |
> | virtualMachineScaleSets/virtualMachines | Inga |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Inga |

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CacheNodes | Ja |

## <a name="microsoftconnectedvehicle"></a>Microsoft. ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | platformAccounts | Ja |
> | registeredSubscriptions | Inga |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft. ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ResourcePools | Ja |
> | VCenter | Ja |
> | VCenter/InventoryItems | Inga |
> | VirtualMachines | Ja |
> | VirtualMachines/tillägg | Ja |
> | VirtualMachines / GuestAgents | Inga |
> | VirtualMachines / HybridIdentityMetadata | Inga |
> | VirtualMachineTemplates | Ja |
> | VirtualNetworks | Ja |

## <a name="microsoftconsumption"></a>Microsoft. förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | AggregatedCost | Inga |
> | Saldon | Inga |
> | Budgetar | Inga |
> | Avgifter | Inga |
> | CostTags | Inga |
> | krediter | Inga |
> | händelser | Inga |
> | Prognoser | Inga |
> | samtliga | Inga |
> | Marknads platser | Inga |
> | Prisdokument | Inga |
> | läkemedle | Inga |
> | ReservationDetails | Inga |
> | ReservationRecommendationDetails | Inga |
> | ReservationRecommendations | Inga |
> | ReservationSummaries | Inga |
> | ReservationTransactions | Nej |
> | Taggar | Nej |
> | klienter | Inga |
> | Termer | Inga |
> | UsageDetails | Inga |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerGroups | Ja |
> | serviceAssociationLinks | Inga |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | register | Ja |
> | register/agentPools | Ja |
> | register/versioner | Inga |
> | register/versioner/Avbryt | Inga |
> | register/build/getLogLink | Inga |
> | register/buildTasks | Ja |
> | register/buildTasks/steg | Inga |
> | register/connectedRegistries | Inga |
> | register/connectedRegistries/inaktivera | Inga |
> | register/eventGridFilters | Inga |
> | register/exportPipelines | Inga |
> | register/generateCredentials | Inga |
> | register/getBuildSourceUploadUrl | Inga |
> | register/GetCredentials | Inga |
> | register/importImage | Inga |
> | register/importPipelines | Inga |
> | register/pipelineRuns | Inga |
> | register/privateEndpointConnectionProxies | Inga |
> | register/privateEndpointConnectionProxies/validate | Inga |
> | register/privateEndpointConnections | Inga |
> | register/privateLinkResources | Inga |
> | register/queueBuild | Inga |
> | register/regenerateCredential | Inga |
> | register/regenerateCredentials | Inga |
> | register/replikeringar | Ja |
> | register/körningar | Inga |
> | register/körningar/Avbryt | Inga |
> | register/scheduleRun | Inga |
> | register/scopeMaps | Inga |
> | register/taskRuns | Inga |
> | register/uppgifter | Ja |
> | register/token | Inga |
> | register/updatePolicies | Inga |
> | register/Webhooks | Ja |
> | register/Webhooks/getCallbackConfig | Inga |
> | register/Webhooks/ping | Inga |

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerServices | Ja |
> | managedClusters | Ja |
> | ManagedClusters / eventGridFilters | Inga |
> | openShiftManagedClusters | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aviseringar | Inga |
> | BillingAccounts | Inga |
> | Budgetar | Inga |
> | CloudConnectors | Inga |
> | Anslutningar | Ja |
> | costAllocationRules | Inga |
> | Avdelningar | Inga |
> | Dimensioner | Inga |
> | EnrollmentAccounts | Inga |
> | Exporter | Inga |
> | ExternalBillingAccounts | Inga |
> | ExternalBillingAccounts/aviseringar | Inga |
> | ExternalBillingAccounts/dimensioner | Inga |
> | ExternalBillingAccounts/prognos | Inga |
> | ExternalBillingAccounts/fråga | Inga |
> | ExternalSubscriptions | Inga |
> | ExternalSubscriptions/aviseringar | Inga |
> | ExternalSubscriptions/dimensioner | Inga |
> | ExternalSubscriptions/prognos | Inga |
> | ExternalSubscriptions/fråga | Inga |
> | fetchPrices | Inga |
> | Prognos | Inga |
> | GenerateDetailedCostReport | Inga |
> | GenerateReservationDetailsReport | Inga |
> | Insikter | Inga |
> | Fråga | Inga |
> | registrera | Inga |
> | Reportconfigs | Inga |
> | Rapporter | Inga |
> | ScheduledActions | Inga |
> | Inställningar | Inga |
> | showbackRules | Inga |
> | Vyer | Inga |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DisableLockbox | Inga |
> | EnableLockbox | Inga |
> | autentiseringsbegäran | Inga |
> | TenantOptedIn | Inga |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | typer | Inga |
> | resourceProviders | Ja |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | pipe | Ja |

## <a name="microsoftdatabox"></a>Microsoft. data-

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utskrifts | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Ja |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbetsytor | Ja |
> | arbets ytor/dbWorkspaces | Inga |
> | arbets ytor/virtualNetworkPeerings | Inga |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kataloger | Ja |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataFactories | Ja |
> | dataFactories / diagnosticSettings | Inga |
> | dataFactories / metricDefinitions | Inga |
> | dataFactorySchema | Inga |
> | fabriker | Ja |
> | fabriker/integrationRuntimes | Inga |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/dataLakeStoreAccounts | Inga |
> | konton/storageAccounts | Inga |
> | konton/storageAccounts/behållare | Inga |
> | konton/transferAnalyticsUnits | Inga |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/eventGridFilters | Inga |
> | konton/firewallRules | Inga |

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DatabaseMigrations | Inga |
> | services | Ja |
> | tjänster/projekt | Ja |
> | SqlMigrationServices | Ja |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | BackupVaults | Ja |
> | ResourceGuards | Ja |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/resurser | Inga |
> | konton/resurser/data uppsättningar | Inga |
> | konton/resurser/inbjudningar | Inga |
> | konton/resurser/providersharesubscriptions | Inga |
> | konton/resurser/synchronizationSettings | Inga |
> | konton/sharesubscriptions | Inga |
> | konton/sharesubscriptions/consumerSourceDataSets | Inga |
> | konton/sharesubscriptions/datasetmappings | Inga |
> | konton/sharesubscriptions/utlösare | Inga |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | brygghuvudservrar | Ja |
> | servrar/rådgivare | Inga |
> | servrar/nycklar | Inga |
> | servrar/privateEndpointConnectionProxies | Inga |
> | servrar/privateEndpointConnections | Inga |
> | servrar/privateLinkResources | Inga |
> | servrar/queryTexts | Inga |
> | servrar/recoverableServers | Inga |
> | servrar/resetQueryPerformanceInsightData | Inga |
> | servrar/start | Inga |
> | servrar/stoppa | Inga |
> | servrar/topQueryStatistics | Inga |
> | servrar/virtualNetworkRules | Inga |
> | servrar/waitStatistics | Inga |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | flexibleServers | Ja |
> | brygghuvudservrar | Ja |
> | servrar/rådgivare | Inga |
> | servrar/nycklar | Inga |
> | servrar/privateEndpointConnectionProxies | Inga |
> | servrar/privateEndpointConnections | Inga |
> | servrar/privateLinkResources | Inga |
> | servrar/queryTexts | Inga |
> | servrar/recoverableServers | Inga |
> | servrar/resetQueryPerformanceInsightData | Inga |
> | servrar/start | Inga |
> | servrar/stoppa | Inga |
> | servrar/topQueryStatistics | Inga |
> | servrar/uppgradering | Inga |
> | servrar/virtualNetworkRules | Inga |
> | servrar/waitStatistics | Inga |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | flexibleServers | Ja |
> | serverGroups | Ja |
> | serverGroupsv2 | Ja |
> | brygghuvudservrar | Ja |
> | servrar/rådgivare | Inga |
> | servrar/nycklar | Inga |
> | servrar/privateEndpointConnectionProxies | Inga |
> | servrar/privateEndpointConnections | Inga |
> | servrar/privateLinkResources | Inga |
> | servrar/queryTexts | Inga |
> | servrar/recoverableServers | Inga |
> | servrar/resetQueryPerformanceInsightData | Inga |
> | servrar/topQueryStatistics | Inga |
> | servrar/virtualNetworkRules | Inga |
> | servrar/waitStatistics | Inga |
> | serversv2 | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | artifactSources | Ja |
> | distributioner | Ja |
> | serviceTopologies | Ja |
> | serviceTopologies/tjänster | Ja |
> | serviceTopologies/tjänster/serviceUnits | Ja |
> | steg | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationgroups | Ja |
> | applicationgroups/program | Inga |
> | applicationgroups/Station ära datorer | Inga |
> | applicationgroups / startmenuitems | Inga |
> | hostpools | Ja |
> | hostpools / msixpackages | Inga |
> | hostpools / sessionhosts | Inga |
> | hostpools / sessionhosts / usersessions | Inga |
> | hostpools / usersessions | Inga |
> | scalingPlans | Ja |
> | arbetsytor | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ElasticPools | Ja |
> | ElasticPools / IotHubTenants | Ja |
> | ElasticPools/IotHubTenants/securitySettings | Inga |
> | IotHubs | Ja |
> | IotHubs / eventGridFilters | Inga |
> | IotHubs/securitySettings | Inga |
> | ProvisioningServices | Ja |
> | användningar | Inga |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/instanser | Ja |
> | registeredSubscriptions | Inga |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | pipelines | Ja |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domänkontrollanter | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labcenters | Ja |
> | Laboration | Ja |
> | labb/miljöer | Ja |
> | labb/serviceRunners | Ja |
> | labb/virtualMachines | Ja |
> | scheman | Ja |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | digitalTwinsInstances | Ja |
> | digitalTwinsInstances/slut punkter | Inga |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cassandraClusters | Ja |
> | databaseAccountNames | Inga |
> | databaseAccounts | Ja |
> | restorableDatabaseAccounts | Inga |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domäner | Ja |
> | domäner/domainOwnershipIdentifiers | Inga |
> | generateSsoRequest | Inga |
> | topLevelDomains | Inga |
> | validateDomainRegistrationInformation | Inga |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | lcsprojects | Inga |
> | lcsprojects / clouddeployments | Inga |
> | lcsprojects/kopplingar | Inga |

## <a name="microsoftedgeorder"></a>Microsoft. EdgeOrder

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | adresser | Ja |
> | orderCollections | Ja |
> | beställningar | Ja |
> | productFamiliesMetadata | Inga |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domäner | Ja |
> | domäner/ämnen | Inga |
> | eventSubscriptions | Inga |
> | extensionTopics | Inga |
> | partnerNamespaces | Ja |
> | partnerNamespaces/eventChannels | Inga |
> | partnerRegistrations | Ja |
> | partnerTopics | Ja |
> | partnerTopics / eventSubscriptions | Inga |
> | systemTopics | Ja |
> | systemTopics / eventSubscriptions | Inga |
> | avsnitt | Ja |
> | topicTypes | Inga |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | namn områden | Ja |
> | namnrymder/authorizationrules | Inga |
> | namnrymder/disasterrecoveryconfigs | Inga |
> | namnrymder/eventhubs | Inga |
> | namnrymder/eventhubs/authorizationrules | Inga |
> | namnrymder/eventhubs/consumergroups | Inga |
> | namnrymder/networkrulesets | Inga |
> | namnrymder/privateEndpointConnections | Inga |

## <a name="microsoftexperimentation"></a>Microsoft. experimentering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | experimentWorkspaces | Ja |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja |

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | featureConfigurations | Inga |
> | featureProviderNamespaces | Inga |
> | featureProviders | Inga |
> | funktioner | Inga |
> | finansiär | Inga |
> | subscriptionFeatureRegistrations | Inga |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certifiering | Inga |
> | galleryitems | Inga |
> | generateartifactaccessuri | Inga |
> | områden | Inga |
> | område/områden | Inga |
> | område/områden/områden | Inga |
> | område/områden/områden/galleryitems | Inga |
> | områdets/områden/galleryitems | Inga |
> | områden/galleryitems | Inga |
> | registrera | Inga |
> | resources | Inga |
> | retrieveresourcesbyid | Inga |

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autoManagedAccounts | Ja |
> | autoManagedVmConfigurationProfiles | Ja |
> | configurationProfileAssignments | Inga |
> | guestConfigurationAssignments | Inga |
> | IntelliPoint | Inga |
> | softwareUpdateProfile | Inga |
> | softwareUpdates | Inga |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hanaInstances | Ja |
> | sapMonitors | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedHSMs | Ja |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | clusterPools | Ja |
> | clusterPools/kluster | Ja |
> | kluster | Ja |
> | kluster/program | Inga |

## <a name="microsofthealthbot"></a>Microsoft. HealthBot

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | healthBots | Ja |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |
> | tjänster/iomtconnectors | Inga |
> | tjänster/iomtconnectors/anslutningar | Inga |
> | tjänster/iomtconnectors/mappningar | Inga |
> | tjänster/privateEndpointConnectionProxies | Inga |
> | tjänster/privateEndpointConnections | Inga |
> | tjänster/privateLinkResources | Inga |
> | arbetsytor | Ja |
> | arbets ytor/dicomservices | Ja |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | faxar | Ja |
> | datorer/assessPatches | Inga |
> | datorer/tillägg | Ja |
> | datorer/installPatches | Inga |
> | datorer/privateLinkScopes | Inga |
> | privateLinkScopes | Ja |
> | privateLinkScopes / privateEndpointConnectionProxies | Inga |
> | privateLinkScopes / privateEndpointConnections | Inga |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataManagers | Ja |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | devices | Ja |
> | networkfunctions | Ja |
> | networkFunctionVendors | Inga |
> | registeredSubscriptions | Inga |
> | Leverantören | Inga |
> | Leverantörer/vendorskus | Inga |
> | Leverantörer/vendorskus/previewsubscriptions | Inga |
> | virtualNetworkFunctions | Ja |
> | virtualNetworkFunctionVendors | Inga |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | delarna | Ja |
> | networkScopes | Ja |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utskrifts | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Inga |
> | diagnosticSettingsCategories | Inga |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appTemplates | Inga |
> | IoTApps | Ja |

## <a name="microsoftiotsecurity"></a>Microsoft. IoTSecurity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | defenderSettings | Inga |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Graph | Ja |

## <a name="microsoftkeyvault"></a>Microsoft. nyckel valv

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedManagedHSMs | Inga |
> | deletedVaults | Inga |
> | hsmPools | Ja |
> | managedHSMs | Ja |
> | valv | Ja |
> | valv/accessPolicies | Inga |
> | valv/eventGridFilters | Inga |
> | valv/nycklar | Inga |
> | valv/nycklar/versioner | Inga |
> | valv/hemligheter | Inga |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | connectedClusters | Ja |
> | registeredSubscriptions | Inga |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | tillägg | Inga |
> | sourceControlConfigurations | Inga |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | kluster/attacheddatabaseconfigurations | Inga |
> | kluster/databaser | Inga |
> | kluster/databaser/dataconnections | Inga |
> | kluster/databaser/eventhubconnections | Inga |
> | kluster/databaser/principalassignments | Inga |
> | kluster/databaser/skript | Inga |
> | kluster/dataconnections | Inga |
> | kluster/principalassignments | Inga |
> | kluster/sharedidentities | Inga |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labaccounts | Ja |
> | labplans | Ja |
> | Laboration | Ja |
> | användare | Inga |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hostingEnvironments | Ja |
> | integrationAccounts | Ja |
> | integrationServiceEnvironments | Ja |
> | integrationServiceEnvironments/managedApis | Ja |
> | isolatedEnvironments | Ja |
> | arbetsflöden | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | commitmentPlans | Ja |
> | WebServices | Ja |
> | Arbetsytor | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | modelinventories | Ja |
> | virtualclusters | Ja |
> | arbetsytor | Ja |
> | arbets ytor/batchEndpoints | Ja |
> | arbets ytor/batchEndpoints/distributioner | Ja |
> | arbets ytor/batchEndpoints/distributioner/jobb | Inga |
> | arbets ytor/batchEndpoints/jobb | Inga |
> | arbets ytor/koder | Inga |
> | arbets ytor/koder/versioner | Inga |
> | arbets ytor/beräkningar | Inga |
> | arbets ytor/data | Inga |
> | arbets ytor/data lager | Inga |
> | arbets ytor/miljöer | Inga |
> | arbets ytor/eventGridFilters | Inga |
> | arbets ytor/jobb | Inga |
> | arbets ytor/labelingJobs | Inga |
> | arbets ytor/linkedServices | Inga |
> | arbets ytor/modeller | Inga |
> | arbets ytor/modeller/versioner | Inga |
> | arbets ytor/onlineEndpoints | Ja |
> | arbets ytor/onlineEndpoints/distributioner | Ja |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applyUpdates | Inga |
> | configurationAssignments | Inga |
> | maintenanceConfigurations | Ja |
> | publicMaintenanceConfigurations | Inga |
> | uppdateringar | Inga |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Identiteter | Inga |
> | userAssignedIdentities | Ja |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managedNetworks | Ja |
> | managedNetworks / managedNetworkGroups | Ja |
> | managedNetworks / managedNetworkPeeringPolicies | Ja |
> | avisering | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Inga |
> | registrationAssignments | Inga |
> | registrationDefinitions | Inga |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | getEntities | Inga |
> | managementGroups | Inga |
> | managementGroups/inställningar | Inga |
> | resources | Inga |
> | startTenantBackfill | Inga |
> | tenantBackfillStatus | Inga |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/skapare | Ja |
> | konton/eventGridFilters | Inga |
> | konton/privateAtlases | Ja |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | macc | Inga |
> | budgivning | Inga |
> | offerTypes | Inga |
> | offerTypes/utgivare | Inga |
> | offerTypes/utgivare/erbjudanden | Inga |
> | offerTypes/utgivare/erbjudanden/planer | Inga |
> | offerTypes/utgivare/erbjudanden/planer/avtal | Inga |
> | offerTypes/utgivare/erbjudanden/planer/konfigurationer | Inga |
> | offerTypes/utgivare/erbjudanden/planer/configs/importImage | Inga |
> | privategalleryitems | Inga |
> | privateStoreClient | Inga |
> | privateStores | Inga |
> | privateStores / AdminRequestApprovals | Inga |
> | privateStores/erbjudanden | Inga |
> | privateStores/erbjudanden/acknowledgeNotification | Inga |
> | privateStores / queryNotificationsState | Inga |
> | privateStores / RequestApprovals | Inga |
> | privateStores/requestApprovals/fråga | Inga |
> | privateStores / requestApprovals / withdrawPlan | Inga |
> | läkemedle | Inga |
> | Utgivare | Inga |
> | Utgivare/erbjudanden | Inga |
> | Utgivare/erbjudanden/ändringar | Inga |
> | registrera | Inga |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicDevServices | Ja |
> | updateCommunicationPreference | Inga |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | villkor | Inga |
> | offertypes | Inga |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Media Services | Ja |
> | Media Services/accountFilters | Inga |
> | Media Services/till gångar | Inga |
> | Media Services/assets/assetFilters | Inga |
> | Media Services/contentKeyPolicies | Inga |
> | Media Services/eventGridFilters | Inga |
> | Media Services/graphInstances | Inga |
> | Media Services/graphTopologies | Inga |
> | Media Services/liveEventOperations | Inga |
> | Media Services/liveEvents | Ja |
> | Media Services/liveEvents/liveOutputs | Inga |
> | Media Services/liveOutputOperations | Inga |
> | Media Services/mediaGraphs | Inga |
> | Media Services/privateEndpointConnectionOperations | Inga |
> | Media Services/privateEndpointConnectionProxies | Inga |
> | Media Services/privateEndpointConnections | Inga |
> | Media Services/streamingEndpointOperations | Inga |
> | Media Services/strömnings slut punkter | Ja |
> | Media Services/streamingLocators | Inga |
> | Media Services/streamingPolicies | Inga |
> | Media Services/transformeringar | Inga |
> | Media Services/transformeringar/jobb | Inga |
> | videoAnalyzers | Ja |
> | videoAnalyzers / edgeModules | Inga |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appClusters | Ja |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | assessmentProjects | Ja |
> | migrateprojects | Ja |
> | moveCollections | Ja |
> | samarbetsprojekt | Ja |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Ja |
> | objectAnchorsAccounts | Ja |
> | objectUnderstandingAccounts | Ja |
> | remoteRenderingAccounts | Ja |
> | spatialAnchorsAccounts | Ja |

## <a name="microsoftmobilenetwork"></a>Microsoft. MobileNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | nätet | Ja |
> | nätverk/platser | Ja |
> | packetCores | Ja |
> | sims | Ja |
> | sims / simProfiles | Ja |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | netAppAccounts | Ja |
> | netAppAccounts / accountBackups | Inga |
> | netAppAccounts / capacityPools | Ja |
> | netAppAccounts/capacityPools/Volumes | Ja |
> | netAppAccounts/capacityPools/volym/ögonblicks bilder | Inga |
> | netAppAccounts / volumeGroups | Inga |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationGateways | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja |
> | applicationSecurityGroups | Ja |
> | azureFirewallFqdnTags | Inga |
> | azureFirewalls | Ja |
> | bastionHosts | Ja |
> | bgpServiceCommunities | Inga |
> | anslutning | Ja |
> | ddosCustomPolicies | Ja |
> | ddosProtectionPlans | Ja |
> | dnsOperationStatuses | Inga |
> | dnszones | Ja |
> | dnszones/A | Inga |
> | dnszones/AAAA | Inga |
> | dnszones/alla | Inga |
> | dnszones/CAA | Inga |
> | dnszones/CNAME | Inga |
> | dnszones/MX | Inga |
> | dnszones/NS | Inga |
> | dnszones/PTR | Inga |
> | dnszones/Recordset | Inga |
> | dnszones/SOA | Inga |
> | dnszones/SRV | Inga |
> | dnszones/TXT | Inga |
> | expressRouteCircuits | Ja |
> | expressRouteCrossConnections | Ja |
> | expressRouteGateways | Ja |
> | expressRoutePorts | Ja |
> | expressRouteServiceProviders | Inga |
> | firewallPolicies | Ja |
> | frontdoors | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Inga |
> | frontdoorWebApplicationFirewallPolicies | Ja |
> | getDnsResourceReference | Inga |
> | internalNotify | Inga |
> | ipGroups | Ja |
> | Belastningsutjämnare | Ja |
> | localNetworkGateways | Ja |
> | natGateways | Ja |
> | networkIntentPolicies | Ja |
> | networkInterfaces | Ja |
> | networkProfiles | Ja |
> | networkSecurityGroups | Ja |
> | networkWatchers | Ja |
> | networkWatchers / connectionMonitors | Ja |
> | networkWatchers / flowLogs | Ja |
> | networkWatchers/linser | Ja |
> | networkWatchers / pingMeshes | Ja |
> | p2sVpnGateways | Ja |
> | privateDnsOperationStatuses | Inga |
> | privateDnsZones | Ja |
> | privateDnsZones/A | Inga |
> | privateDnsZones/AAAA | Inga |
> | privateDnsZones/alla | Inga |
> | privateDnsZones/CNAME | Inga |
> | privateDnsZones/MX | Inga |
> | privateDnsZones/PTR | Inga |
> | privateDnsZones/SOA | Inga |
> | privateDnsZones/SRV | Inga |
> | privateDnsZones/TXT | Inga |
> | privateDnsZones / virtualNetworkLinks | Ja |
> | privateEndpoints | Ja |
> | privateLinkServices | Ja |
> | publicIPAddresses | Ja |
> | publicIPPrefixes | Ja |
> | routeFilters | Ja |
> | routeTables | Ja |
> | serviceEndpointPolicies | Ja |
> | trafficManagerGeographicHierarchies | Inga |
> | trafficmanagerprofiles | Ja |
> | trafficmanagerprofiles/termiska kartor | Inga |
> | trafficManagerUserMetricsKeys | Inga |
> | virtualHubs | Ja |
> | virtualNetworkGateways | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks/undernät | Inga |
> | virtualNetworkTaps | Ja |
> | virtualWans | Ja |
> | vpnGateways | Ja |
> | vpnSites | Ja |
> | webApplicationFirewallPolicies | Ja |

## <a name="microsoftnotebooks"></a>Microsoft. Notebook

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | NotebookProxies | Inga |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja |
> | namnrymder/notificationHubs | Ja |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | osNamespaces | Ja |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | HyperVSites | Ja |
> | ImportSites | Ja |
> | MasterSites | Ja |
> | ServerSites | Ja |
> | VMwareSites | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | deletedWorkspaces | Inga |
> | linkTargets | Inga |
> | querypacks | Ja |
> | storageInsightConfigs | Inga |
> | arbetsytor | Ja |
> | arbets ytor/dataExports | Inga |
> | arbets ytor/data källor | Inga |
> | arbets ytor/linkedServices | Inga |
> | arbets ytor/linkedStorageAccounts | Inga |
> | arbets ytor/metadata | Inga |
> | arbets ytor/fråga | Inga |
> | arbets ytor/scopedPrivateLinkProxies | Inga |
> | arbets ytor/storageInsightConfigs | Inga |
> | arbets ytor/tabeller | Inga |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managementassociations | Inga |
> | managementconfigurations | Ja |
> | lösningar | Ja |
> | vyer | Ja |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cdnPeeringPrefixes | Inga |
> | legacyPeerings | Inga |
> | peerAsns | Inga |
> | peerings | Ja |
> | peeringServiceCountries | Inga |
> | peeringServiceProviders | Inga |
> | peeringServices | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attesteringar | Inga |
> | eventGridFilters | Inga |
> | policyEvents | Inga |
> | policyMetadata | Inga |
> | policyStates | Inga |
> | policyTrackedResources | Inga |
> | reparationer | Inga |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> |  -konsoler | Inga |
> | instrumentpaneler | Ja |
> | tenantconfigurations | Inga |
> | userSettings | Inga |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Ja |
> | klienter | Ja |
> | innehavare/arbets ytor | Inga |
> | workspaceCollections | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autoScaleVCores | Ja |
> | kapaciteter | Ja |

## <a name="microsoftpowerplatform"></a>Microsoft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | enterprisePolicies | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | deletedAccounts | Inga |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | Inga |
> | providerRegistrations / customRollouts | Inga |
> | providerRegistrations / defaultRollouts | Inga |
> | providerRegistrations / resourceTypeRegistrations | Inga |

## <a name="microsoftpurview"></a>Microsoft. avdelningens kontroll

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | deletedAccounts | Inga |
> | getDefaultAccount | Inga |
> | removeDefaultAccount | Inga |
> | setDefaultAccount | Inga |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Arbetsytor | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | backupProtectedItems | Inga |
> | valv | Ja |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | OpenShiftClusters | Ja |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja |
> | namnrymder/authorizationrules | Inga |
> | namnrymder/hybridconnections | Inga |
> | namnrymder/hybridconnections/authorizationrules | Inga |
> | namnrymder/privateEndpointConnections | Inga |
> | namnrymder/wcfrelays | Inga |
> | namnrymder/wcfrelays/authorizationrules | Inga |

## <a name="microsoftresourceconnector"></a>Microsoft. ResourceConnector

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | redskap | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | skickar | Ja |
> | resourceChangeDetails | Inga |
> | resourceChanges | Inga |
> | resources | Inga |
> | resourcesHistory | Inga |
> | subscriptionsStatus | Inga |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilityStatuses | Inga |
> | childAvailabilityStatuses | Inga |
> | childResources | Inga |
> | emergingissues | Inga |
> | händelser | Inga |
> | impactedResources | Inga |
> | metadata | Inga |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | distributioner | Inga |
> | distributioner/åtgärder | Inga |
> | deploymentScripts | Ja |
> | deploymentScripts/loggar | Inga |
> | Länkar | Inga |
> | finansiär | Inga |
> | resourceGroups | Inga |
> | prenumerationer | Inga |
> | templateSpecs | Ja |
> | templateSpecs/versioner | Ja |
> | klienter | Inga |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | resources | Ja |
> | saasresources | Inga |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | moln | Ja |
> | VirtualMachines | Ja |
> | VirtualMachineTemplates | Ja |
> | VirtualNetworks | Ja |
> | vmmservers | Ja |

## <a name="microsoftsearch"></a>Microsoft. search

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | resourceHealthMetadata | Inga |
> | searchServices | Ja |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Inga |
> | advancedThreatProtectionSettings | Inga |
> | aviseringar | Inga |
> | alertsSuppressionRules | Inga |
> | allowedConnections | Inga |
> | applicationWhitelistings | Inga |
> | assessmentMetadata | Inga |
> | utvärderingar | Inga |
> | autoDismissAlertsRules | Inga |
> | automatiseringar | Ja |
> | AutoProvisioningSettings | Inga |
> | Godkännanden | Inga |
> | anslutningar | Inga |
> | dataCollectionAgents | Inga |
> | devices | Inga |
> | deviceSecurityGroups | Inga |
> | discoveredSecuritySolutions | Inga |
> | externalSecuritySolutions | Inga |
> | InformationProtectionPolicies | Inga |
> | ingestionSettings | Inga |
> | Insights | Inga |
> | iotAlerts | Inga |
> | iotAlertTypes | Inga |
> | iotDefenderSettings | Inga |
> | iotRecommendations | Inga |
> | iotRecommendationTypes | Inga |
> | iotSecuritySolutions | Ja |
> | iotSecuritySolutions / analyticsModels | Inga |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Inga |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Inga |
> | iotSecuritySolutions / iotAlerts | Inga |
> | iotSecuritySolutions / iotAlertTypes | Inga |
> | iotSecuritySolutions / iotRecommendations | Inga |
> | iotSecuritySolutions / iotRecommendationTypes | Inga |
> | iotSensors | Inga |
> | iotSites | Inga |
> | jitNetworkAccessPolicies | Inga |
> | jitPolicies | Inga |
> | onPremiseIotSensors | Inga |
> | policies | Inga |
> | prissättningar | Inga |
> | regulatoryComplianceStandards | Inga |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Inga |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Inga |
> | secureScoreControlDefinitions | Inga |
> | secureScoreControls | Inga |
> | secureScores | Inga |
> | secureScores / secureScoreControls | Inga |
> | securityContacts | Inga |
> | securitySolutions | Inga |
> | securitySolutionsReferenceData | Inga |
> | securityStatuses | Inga |
> | securityStatusesSummaries | Inga |
> | serverVulnerabilityAssessments | Inga |
> | inställningar | Inga |
> | sqlVulnerabilityAssessments | Inga |
> | underbedömningar | Inga |
> | uppgifter | Inga |
> | topologier | Inga |
> | workspaceSettings | Inga |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Inga |
> | diagnosticSettingsCategories | Inga |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | agg regeringar | Inga |
> | alertRules | Inga |
> | alertRuleTemplates | Inga |
> | automationRules | Inga |
> | bokmärken | Inga |
> | fall | Inga |
> | dataConnectors | Inga |
> | dataConnectorsCheckRequirements | Inga |
> | berikande | Inga |
> | poster | Inga |
> | entityQueries | Inga |
> | entityQueryTemplates | Inga |
> | incidenter | Inga |
> | officeConsents | Inga |
> | inställningar | Inga |
> | threatIntelligence | Inga |
> | watchlists | Inga |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | consoleServices | Inga |
> | serialPorts | Inga |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja |
> | namnrymder/authorizationrules | Inga |
> | namnrymder/disasterrecoveryconfigs | Inga |
> | namnrymder/eventgridfilters | Inga |
> | namnrymder/networkrulesets | Inga |
> | namnrymder/privateEndpointConnections | Inga |
> | namnrymder/köer | Inga |
> | namnrymder/köer/authorizationrules | Inga |
> | namn områden/ämnen | Inga |
> | namnrymder/ämnen/authorizationrules | Inga |
> | namnrymder/ämnen/prenumerationer | Inga |
> | namn områden/ämnen/prenumerationer/regler | Inga |
> | premiumMessagingRegions | Inga |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | kluster | Ja |
> | kluster/program | Inga |
> | containerGroups | Ja |
> | containerGroupSets | Ja |
> | edgeclusters | Ja |
> | edgeclusters/program | Inga |
> | managedclusters | Ja |
> | managedclusters/program | Inga |
> | managedclusters/program/tjänster | Inga |
> | managedclusters / applicationTypes | Inga |
> | managedclusters/applicationTypes/-versioner | Inga |
> | managedclusters/nodetypes | Inga |
> | nätet | Ja |
> | secretstores | Ja |
> | secretstores/certifikat | Inga |
> | secretstores/hemligheter | Inga |
> | volumes | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | containerGroups | Ja |
> | gatewayer | Ja |
> | nätet | Ja |
> | secrets | Ja |
> | volumes | Ja |

## <a name="microsoftservicelinker"></a>Microsoft. ServiceLinker

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Länkar | Inga |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | Inga |
> | providerRegistrations / resourceTypeRegistrations | Inga |
> | distributioner | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SignalR | Ja |
> | SignalR/eventGridFilters | Inga |
> | WebPubSub | Ja |

## <a name="microsoftsingularity"></a>Microsoft. Singularity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/accountQuotaPolicies | Inga |
> | konton/groupPolicies | Inga |
> | konton/jobb | Inga |
> | konton/storageContainers | Inga |
> | images | Inga |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hybridUseBenefits | Inga |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationDefinitions | Ja |
> | program | Ja |
> | jitRequests | Ja |

## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managedInstances | Ja |
> | managedInstances/databaser | Ja |
> | managedInstances/databaser/backupShortTermRetentionPolicies | Inga |
> | managedInstances/databaser/scheman/tabeller/kolumner/sensitivityLabels | Inga |
> | managedInstances/databaser/vulnerabilityAssessments | Inga |
> | managedInstances/databaser/vulnerabilityAssessments/regler/bas linjer | Inga |
> | managedInstances / encryptionProtector | Inga |
> | managedInstances/nycklar | Inga |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Inga |
> | managedInstances / vulnerabilityAssessments | Inga |
> | brygghuvudservrar | Ja |
> | servrar/administratörer | Inga |
> | servrar/communicationLinks | Inga |
> | servrar/databaser | Ja |
> | servrar/encryptionProtector | Inga |
> | servrar/firewallRules | Inga |
> | servrar/nycklar | Inga |
> | servrar/restorableDroppedDatabases | Inga |
> | servrar/serviceobjectives | Inga |
> | servrar/tdeCertificates | Inga |
> | virtualClusters | Inga |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Inga |
> | SqlVirtualMachines | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedAccounts | Inga |
> | storageAccounts | Ja |
> | storageAccounts/blobServices | Inga |
> | storageAccounts/fileServices | Inga |
> | storageAccounts/queueServices | Inga |
> | storageAccounts/tjänster | Inga |
> | storageAccounts/tjänster/metricDefinitions | Inga |
> | storageAccounts/tableServices | Inga |
> | användningar | Inga |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | amlFilesystems | Ja |
> | cacheminnen | Ja |
> | cache-/storageTargets | Inga |
> | usageModels | Inga |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | replicationGroups | Inga |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Inga |
> | storageSyncServices / syncGroups | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga |
> | storageSyncServices/arbets flöden | Inga |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Inga |
> | storageSyncServices / syncGroups | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga |
> | storageSyncServices/arbets flöden | Inga |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Inga |
> | storageSyncServices / syncGroups | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga |
> | storageSyncServices/arbets flöden | Inga |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hantera | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | kluster/privateEndpoints | Inga |
> | streamingjobs | Ja |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | acceptChangeTenant | Inga |
> | acceptOwnership | Inga |
> | acceptOwnershipStatus | Inga |
> | alias | Inga |
> | avbryt | Inga |
> | changeTenantRequest | Inga |
> | changeTenantStatus | Inga |
> | CreateSubscription | Inga |
> | Aktivera | Inga |
> | policies | Inga |
> | byt namn | Inga |
> | SubscriptionDefinitions | Inga |
> | SubscriptionOperations | Inga |
> | prenumerationer | Inga |

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateLinkHubs | Ja |
> | arbetsytor | Ja |
> | arbets ytor/bigDataPools | Ja |
> | arbets ytor/operationStatuses | Inga |
> | arbets ytor/sqlDatabases | Ja |
> | arbets ytor/sqlPools | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | Ja |
> | miljöer/accessPolicies | Inga |
> | miljöer/eventsources | Ja |
> | miljöer/privateEndpointConnectionProxies | Inga |
> | miljöer/privateEndpointConnections | Inga |
> | miljöer/privateLinkResources | Inga |
> | miljöer/referenceDataSets | Ja |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Auktoriseringshanteraren | Ja |
> | butiker/accessPolicies | Inga |
> | butiker/tjänster | Inga |
> | butiker/tjänster/token | Inga |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | imageTemplates | Ja |
> | imageTemplates / runOutputs | Inga |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ArcZones | Ja |
> | ResourcePools | Ja |
> | VCenter | Ja |
> | VCenter/InventoryItems | Inga |
> | virtualmachines | Ja |
> | VirtualMachineTemplates | Ja |
> | VirtualNetworks | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ja |
> | dedicatedCloudServices | Ja |
> | virtualMachines | Ja |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | devices | Ja |
> | registeredSubscriptions | Inga |
> | leverantören | Inga |
> | leverantörer/SKU: er | Inga |
> | leverantörer/vnfs | Inga |
> | virtualNetworkFunctionSkus | Inga |
> | vnfs | Ja |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | utgå | Ja |
> | registeredSubscriptions | Inga |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | apiManagementAccounts | Inga |
> | apiManagementAccounts / apiAcls | Inga |
> | apiManagementAccounts/API: er | Inga |
> | apiManagementAccounts/API/apiAcls | Inga |
> | apiManagementAccounts/API/connectionAcls | Inga |
> | apiManagementAccounts/API/anslutningar | Inga |
> | apiManagementAccounts/API/Connections/connectionAcls | Inga |
> | apiManagementAccounts/API/localizedDefinitions | Inga |
> | apiManagementAccounts / connectionAcls | Inga |
> | apiManagementAccounts/anslutningar | Inga |
> | billingMeters | Inga |
> | certifikat | Ja |
> | connectionGateways | Ja |
> | anslutning | Ja |
> | customApis | Ja |
> | deletedSites | Inga |
> | functionAppStacks | Inga |
> | generateGithubAccessTokenForAppserviceCLI | Inga |
> | hostingEnvironments | Ja |
> | hostingEnvironments / eventGridFilters | Inga |
> | hostingEnvironments / multiRolePools | Inga |
> | hostingEnvironments / workerPools | Inga |
> | kubeEnvironments | Ja |
> | publishingUsers | Inga |
> | rekommendationer | Inga |
> | resourceHealthMetadata | Inga |
> | körningar | Inga |
> | Server grupper | Ja |
> | Server grupper/eventGridFilters | Inga |
> | Server grupper/firstPartyApps | Inga |
> | Server grupper/firstPartyApps/keyVaultSettings | Inga |
> | webbplatser | Ja |
> | platser/konfig  | Inga |
> | platser/eventGridFilters | Inga |
> | platser/hostNameBindings | Inga |
> | platser/networkConfig | Inga |
> | platser/premieraddons | Ja |
> | platser/platser | Ja |
> | platser/platser/eventGridFilters | Inga |
> | platser/platser/hostNameBindings | Inga |
> | platser/platser/networkConfig | Inga |
> | sourceControls | Inga |
> | staticSites | Ja |
> | kontrollerar | Inga |
> | verifyHostingEnvironmentVnet | Inga |
> | webAppStacks | Inga |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Inga |
> | diagnosticSettingsCategories | Inga |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | multipleActivationKeys | Ja |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DeviceServices | Ja |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | migrationAgents | Ja |
> | arbets belastningar | Ja |
> | arbets belastningar/instanser | Inga |
> | arbets belastningar/versioner | Inga |
> | arbets belastningar/versioner/artefakter | Inga |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Övervakare | Nej |

## <a name="next-steps"></a>Nästa steg

Hämta [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)för att hämta samma data som en fil med kommaavgränsade värden.
