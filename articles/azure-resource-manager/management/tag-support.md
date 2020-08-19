---
title: Tagga stöd för resurser
description: Visar vilka typer av Azure-resurs typer som stöder taggar. Innehåller information om alla Azure-tjänster.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: e3a51bb7c21b6ea2e8baa86fe2981184c41d7d69
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272441"
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
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. API Management](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. attestering](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. Azure. Genève](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. fakturering](#microsoftbilling)
> - [Microsoft. Bingkartssökning](#microsoftbingmaps)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. skiss](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. ConnectedCache](#microsoftconnectedcache)
> - [Microsoft. förbrukning](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. container service](#microsoftcontainerservice)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
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
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
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
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
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
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
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
> - [Microsoft. VMwareOnAzure](#microsoftvmwareonazure)
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
> | DomainServices / oucontainer | Nej | Nej |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nej | Nej |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nej | Nej |
> | addsservices | Nej | Nej |
> | aktörer | Nej | Nej |
> | anonymousapiusers | Nej | Nej |
> | konfiguration | Nej | Nej |
> | loggar | Nej | Nej |
> | rapporter | Nej | Nej |
> | servicehealthmetrics | Nej | Nej |
> | services | Nej | Nej |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konfigurationer | Nej | Nej |
> | generateRecommendations | Nej | Nej |
> | metadata | Nej | Nej |
> | rekommendationer | Nej | Nej |
> | utelämningar | Nej | Nej |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | actionRules | Ja | Ja |
> | aviseringar | Nej | Nej |
> | alertsList | Nej | Nej |
> | alertsMetaData | Nej | Nej |
> | alertsSummary | Nej | Nej |
> | alertsSummaryList | Nej | Nej |
> | smartDetectorAlertRules | Ja | Ja |
> | smartGroups | Nej | Nej |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | brygghuvudservrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Nej | Nej |
> | tjänst | Ja | Ja |
> | validateServiceName | Nej | Nej |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores / eventGridFilters | Nej | Nej |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Spring | Ja | Ja |
> | Våren/appar | Nej | Nej |
> | Våren/appar/distributioner | Nej | Nej |

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Ja | Ja |
> | defaultProviders | Nej | Nej |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Nej | Nej |
> | dataAliases | Nej | Nej |
> | denyAssignments | Nej | Nej |
> | elevateAccess | Nej | Nej |
> | findOrphanRoleAssignments | Nej | Nej |
> | hålls | Nej | Nej |
> | behörigheter | Nej | Nej |
> | policyAssignments | Nej | Nej |
> | policyDefinitions | Nej | Nej |
> | policySetDefinitions | Nej | Nej |
> | privateLinkAssociations | Nej | Nej |
> | providerOperations | Nej | Nej |
> | resourceManagementPrivateLinks | Nej | Nej |
> | roleAssignments | Nej | Nej |
> | roleAssignmentsUsageMetrics | Nej | Nej |
> | roleDefinitions | Nej | Nej |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ja | Ja |
> | automationAccounts/konfigurationer | Ja | Ja |
> | automationAccounts/jobb | Nej | Nej |
> | automationAccounts / privateEndpointConnectionProxies | Nej | Nej |
> | automationAccounts / privateEndpointConnections | Nej | Nej |
> | automationAccounts / privateLinkResources | Nej | Nej |
> | automationAccounts/Runbooks | Ja | Ja |
> | automationAccounts / softwareUpdateConfigurations | Nej | Nej |
> | automationAccounts/Webhooks | Nej | Nej |

>[!NOTE]
>Azure Automation har endast stöd för att skapa högst 15 märkes namn/värdepar för varje Automation-resurs.
> 

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | privateClouds | Ja | Ja |
> | privateClouds/auktoriseringar | Nej | Nej |
> | privateClouds/kluster | Nej | Nej |
> | privateClouds / hcxEnterpriseSites | Nej | Nej |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genève

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | utrymmen | Nej | Nej |
> | miljöer/konton | Nej | Nej |
> | miljöer/konton/namn områden | Nej | Nej |
> | miljöer/konton/namn rymder/konfigurationer | Nej | Nej |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Ja | Nej |
> | b2ctenants | Nej | Nej |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | dataControllers | Ja | Ja |
> | hybridDataManagers | Ja | Ja |
> | postgresInstances | Ja | Ja |
> | sqlInstances | Ja | Ja |
> | sqlManagedInstances | Ja | Ja |
> | sqlServerInstances | Ja | Ja |
> | sqlServerRegistrations | Ja | Ja |
> | sqlServerRegistrations/sqlServer | Nej | Nej |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Nej | Nej |
> | edgeSubscriptions | Ja | Ja |
> | registreringar | Ja | Ja |
> | registreringar/customerSubscriptions | Nej | Nej |
> | registreringar/produkter | Nej | Nej |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kluster | Ja | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft. fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nej | Nej |
> | billingAccounts/avtal | Nej | Nej |
> | billingAccounts / billingPermissions | Nej | Nej |
> | billingAccounts / billingProfiles | Nej | Nej |
> | billingAccounts / billingProfiles / billingPermissions | Nej | Nej |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nej | Nej |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nej | Nej |
> | billingAccounts / billingProfiles / billingSubscriptions | Nej | Nej |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nej | Nej |
> | billingAccounts/billingProfiles/kunder | Nej | Nej |
> | billingAccounts/billingProfiles/instruktioner | Nej | Nej |
> | billingAccounts/billingProfiles/fakturor | Nej | Nej |
> | billingAccounts/billingProfiles/fakturor/pris dokument | Nej | Nej |
> | billingAccounts/billingProfiles/fakturor/transaktioner | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nej | Nej |
> | billingAccounts/billingProfiles/invoiceSections/Products | Nej | Nej |
> | billingAccounts/billingProfiles/invoiceSections/Products/transfer | Nej | Nej |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Nej | Nej |
> | billingAccounts/billingProfiles/invoiceSections/transaktioner | Nej | Nej |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Nej | Nej |
> | billingAccounts / BillingProfiles / patchOperations | Nej | Nej |
> | billingAccounts / billingProfiles / paymentMethods | Nej | Nej |
> | billingAccounts/billingProfiles/policys | Nej | Nej |
> | billingAccounts/billingProfiles/pris dokument | Nej | Nej |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nej | Nej |
> | billingAccounts/billingProfiles/Products | Nej | Nej |
> | billingAccounts/billingProfiles/transaktioner | Nej | Nej |
> | billingAccounts / billingRoleAssignments | Nej | Nej |
> | billingAccounts / billingRoleDefinitions | Nej | Nej |
> | billingAccounts / billingSubscriptions | Nej | Nej |
> | billingAccounts/billingSubscriptions/fakturor | Nej | Nej |
> | billingAccounts / createBillingRoleAssignment | Nej | Nej |
> | billingAccounts / createInvoiceSectionOperations | Nej | Nej |
> | billingAccounts/kunder | Nej | Nej |
> | billingAccounts/kunder/billingPermissions | Nej | Nej |
> | billingAccounts/kunder/billingSubscriptions | Nej | Nej |
> | billingAccounts/kunder/initiateTransfer | Nej | Nej |
> | billingAccounts/kunder/principer | Nej | Nej |
> | billingAccounts/kunder/produkter | Nej | Nej |
> | billingAccounts/kunder/transaktioner | Nej | Nej |
> | billingAccounts/kunder/överföringar | Nej | Nej |
> | billingAccounts/avdelningar | Nej | Nej |
> | billingAccounts/avdelningar/billingPermissions | Nej | Nej |
> | billingAccounts/avdelningar/billingRoleAssignments | Nej | Nej |
> | billingAccounts/avdelningar/billingRoleDefinitions | Nej | Nej |
> | billingAccounts / enrollmentAccounts | Nej | Nej |
> | billingAccounts / enrollmentAccounts / billingPermissions | Nej | Nej |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Nej | Nej |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Nej | Nej |
> | billingAccounts/fakturor | Nej | Nej |
> | billingAccounts/fakturor/transaktioner | Nej | Nej |
> | billingAccounts / invoiceSections | Nej | Nej |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nej | Nej |
> | billingAccounts / invoiceSections / billingSubscriptions | Nej | Nej |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nej | Nej |
> | billingAccounts/invoiceSections/höjning | Nej | Nej |
> | billingAccounts / invoiceSections / initiateTransfer | Nej | Nej |
> | billingAccounts / invoiceSections / patchOperations | Nej | Nej |
> | billingAccounts / invoiceSections / productMoveOperations | Nej | Nej |
> | billingAccounts/invoiceSections/Products | Nej | Nej |
> | billingAccounts/invoiceSections/Products/transfer | Nej | Nej |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Nej | Nej |
> | billingAccounts/invoiceSections/transaktioner | Nej | Nej |
> | billingAccounts/invoiceSections/överföringar | Nej | Nej |
> | billingAccounts / lineOfCredit | Nej | Nej |
> | billingAccounts / patchOperations | Nej | Nej |
> | billingAccounts / paymentMethods | Nej | Nej |
> | billingAccounts/produkter | Nej | Nej |
> | billingAccounts/transaktioner | Nej | Nej |
> | billingPeriods | Nej | Nej |
> | billingPermissions | Nej | Nej |
> | billingProperty | Nej | Nej |
> | billingRoleAssignments | Nej | Nej |
> | billingRoleDefinitions | Nej | Nej |
> | createBillingRoleAssignment | Nej | Nej |
> | enheten | Nej | Nej |
> | enrollmentAccounts | Nej | Nej |
> | fakturor | Nej | Nej |
> | överlåtelse | Nej | Nej |
> | överföringar/acceptTransfer | Nej | Nej |
> | överföringar/declineTransfer | Nej | Nej |
> | överföringar/operationStatus | Nej | Nej |
> | överföringar/validateTransfer | Nej | Nej |
> | validateAddress | Nej | Nej |

## <a name="microsoftbingmaps"></a>Microsoft. Bingkartssökning

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | mapApis | Ja | Ja |
> | updateCommunicationPreference | Nej | Nej |

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
> | TokenServices / BlockchainNetworks | Nej | Nej |
> | TokenServices/grupper | Nej | Nej |
> | TokenServices/grupper/konton | Nej | Nej |
> | TokenServices / TokenTemplates | Nej | Nej |

## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nej | Nej |
> | blueprintAssignments / assignmentOperations | Nej | Nej |
> | blueprintAssignments/åtgärder | Nej | Nej |
> | modeller | Nej | Nej |
> | skisser/artefakter | Nej | Nej |
> | skisser/versioner | Nej | Nej |
> | skisser/versioner/artefakter | Nej | Nej |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | botServices | Ja | Ja |
> | botServices/kanaler | Nej | Nej |
> | botServices/anslutningar | Nej | Nej |
> | språk | Nej | Nej |
> | mallar | Nej | Nej |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Redis | Ja | Ja |
> | Redis/EventGridFilters | Nej | Nej |
> | Redis/privateEndpointConnectionProxies | Nej | Nej |
> | Redis/privateEndpointConnectionProxies/validate | Nej | Nej |
> | Redis/privateEndpointConnections | Nej | Nej |
> | Redis/privateLinkResources | Nej | Nej |
> | redisEnterprise | Ja | Ja |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nej | Nej |
> | autoQuotaIncrease | Nej | Nej |
> | calculateExchange | Nej | Nej |
> | calculatePrice | Nej | Nej |
> | calculatePurchasePrice | Nej | Nej |
> | kataloger | Nej | Nej |
> | commercialReservationOrders | Nej | Nej |
> | utväxla | Nej | Nej |
> | placePurchaseOrder | Nej | Nej |
> | reservationOrders | Nej | Nej |
> | reservationOrders / calculateRefund | Nej | Nej |
> | reservationOrders/slå samman | Nej | Nej |
> | reservationOrders/reservationer | Nej | Nej |
> | reservationOrders/reservationer/revisioner | Nej | Nej |
> | reservationOrders/retur | Nej | Nej |
> | reservationOrders/Split | Nej | Nej |
> | reservationOrders/växling | Nej | Nej |
> | reservera | Nej | Nej |
> | resourceProviders | Nej | Nej |
> | resources | Nej | Nej |
> | validateReservationOrder | Nej | Nej |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nej | Nej |
> | CdnWebApplicationFirewallPolicies | Ja | Ja |
> | edgenodes | Nej | Nej |
> | filer | Ja | Ja |
> | profiler/slut punkter | Ja | Ja |
> | profiler/slut punkter/customdomains | Nej | Nej |
> | profiler/slut punkter/origingroups | Nej | Nej |
> | profiler/slut punkter/ursprung | Nej | Nej |
> | validateProbe | Nej | Nej |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ja | Ja |
> | certificateOrders/certifikat | Nej | Nej |
> | validateCertificateRegistrationInformation | Nej | Nej |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | profil | Nej | Nej |
> | resourceChanges | Nej | Nej |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | funktioner | Nej | Nej |
> | Domän namn | Nej | Nej |
> | Domän namn/funktioner | Nej | Nej |
> | Domän namn/internalLoadBalancers | Nej | Nej |
> | Domän namn/serviceCertificates | Nej | Nej |
> | Domän namn/platser | Nej | Nej |
> | Domän namn/platser/roller | Nej | Nej |
> | Domän namn/platser/roller/metricDefinitions | Nej | Nej |
> | Domän namn/platser/roller/mått | Nej | Nej |
> | moveSubscriptionResources | Nej | Nej |
> | operatingSystemFamilies | Nej | Nej |
> | operatingSystems | Nej | Nej |
> | quotas | Nej | Nej |
> | resourceTypes | Nej | Nej |
> | validateSubscriptionMoveAvailability | Nej | Nej |
> | virtualMachines | Nej | Nej |
> | virtualMachines / diagnosticSettings | Nej | Nej |
> | virtualMachines / metricDefinitions | Nej | Nej |
> | virtualMachines/mått | Nej | Nej |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nej | Nej |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | funktioner | Nej | Nej |
> | expressRouteCrossConnections | Nej | Nej |
> | expressRouteCrossConnections/peering | Nej | Nej |
> | gatewaySupportedDevices | Nej | Nej |
> | networkSecurityGroups | Nej | Nej |
> | quotas | Nej | Nej |
> | reservedIps | Nej | Nej |
> | virtualNetworks | Nej | Nej |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nej | Nej |
> | virtualNetworks/virtualNetworkPeerings | Nej | Nej |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | funktioner | Nej | Nej |
> | disk | Nej | Nej |
> | images | Nej | Nej |
> | osImages | Nej | Nej |
> | osPlatformImages | Nej | Nej |
> | publicImages | Nej | Nej |
> | quotas | Nej | Nej |
> | storageAccounts | Nej | Nej |
> | storageAccounts/blobServices | Nej | Nej |
> | storageAccounts/fileServices | Nej | Nej |
> | storageAccounts/metricDefinitions | Nej | Nej |
> | storageAccounts/mått | Nej | Nej |
> | storageAccounts/queueServices | Nej | Nej |
> | storageAccounts/tjänster | Nej | Nej |
> | storageAccounts/tjänster/diagnosticSettings | Nej | Nej |
> | storageAccounts/tjänster/metricDefinitions | Nej | Nej |
> | storageAccounts/tjänster/mått | Nej | Nej |
> | storageAccounts/tableServices | Nej | Nej |
> | storageAccounts/vmImages | Nej | Nej |
> | vmImages | Nej | Nej |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | RateCard | Nej | Nej |
> | UsageAggregates | Nej | Nej |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Ja | Ja |
> | diskAccesses | Ja | Ja |
> | diskEncryptionSets | Ja | Ja |
> | disk | Ja | Ja |
> | gallerier | Ja | Ja |
> | gallerier/program | Nej | Nej |
> | gallerier/program/versioner | Nej | Nej |
> | gallerier/bilder | Nej | Nej |
> | gallerier/avbildningar/versioner | Nej | Nej |
> | hostGroups | Ja | Ja |
> | hostGroups/värdar | Ja | Ja |
> | images | Ja | Ja |
> | proximityPlacementGroups | Ja | Ja |
> | restorePointCollections | Ja | Ja |
> | restorePointCollections / restorePoints | Nej | Nej |
> | sharedVMExtensions | Ja | Ja |
> | sharedVMExtensions/versioner | Nej | Nej |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages/versioner | Nej | Nej |
> | snapshots | Ja | Ja |
> | sshPublicKeys | Ja | Ja |
> | virtualMachines | Ja | Ja |
> | virtualMachines/tillägg | Ja | Ja |
> | virtualMachines / metricDefinitions | Nej | Nej |
> | virtualMachines / runCommands | Ja | Ja |
> | virtualMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets/tillägg | Nej | Nej |
> | virtualMachineScaleSets/networkInterfaces | Nej | Nej |
> | virtualMachineScaleSets/publicIPAddresses | Nej | Nej |
> | virtualMachineScaleSets/virtualMachines | Nej | Nej |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nej | Nej |

> [!NOTE]
> Du kan inte lägga till en tagg till en virtuell dator som har marker ATS som generaliserad. Du markerar en virtuell dator som generaliserad med [set-AzVm – generaliserad](/powershell/module/Az.Compute/Set-AzVM) eller [AZ VM generalize](/cli/azure/vm#az-vm-generalize).

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Ja | Ja |

## <a name="microsoftconsumption"></a>Microsoft. förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nej | Nej |
> | Saldon | Nej | Nej |
> | Budgetar | Nej | Nej |
> | Avgifter | Nej | Nej |
> | CostTags | Nej | Nej |
> | krediter | Nej | Nej |
> | händelser | Nej | Nej |
> | Prognoser | Nej | Nej |
> | samtliga | Nej | Nej |
> | Marknads platser | Nej | Nej |
> | Prisdokument | Nej | Nej |
> | läkemedle | Nej | Nej |
> | ReservationDetails | Nej | Nej |
> | ReservationRecommendationDetails | Nej | Nej |
> | ReservationRecommendations | Nej | Nej |
> | ReservationSummaries | Nej | Nej |
> | ReservationTransactions | Nej | Nej |
> | Taggar | Nej | Nej |
> | klienter | Nej | Nej |
> | Termer | Nej | Nej |
> | UsageDetails | Nej | Nej |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ja | Ja |
> | serviceAssociationLinks | Nej | Nej |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | register | Ja | Ja |
> | register/agentPools | Ja | Ja |
> | register/versioner | Nej | Nej |
> | register/versioner/Avbryt | Nej | Nej |
> | register/build/getLogLink | Nej | Nej |
> | register/buildTasks | Ja | Ja |
> | register/buildTasks/steg | Nej | Nej |
> | register/eventGridFilters | Nej | Nej |
> | register/exportPipelines | Nej | Nej |
> | register/generateCredentials | Nej | Nej |
> | register/getBuildSourceUploadUrl | Nej | Nej |
> | register/GetCredentials | Nej | Nej |
> | register/importImage | Nej | Nej |
> | register/importPipelines | Nej | Nej |
> | register/pipelineRuns | Nej | Nej |
> | register/privateEndpointConnectionProxies | Nej | Nej |
> | register/privateEndpointConnectionProxies/validate | Nej | Nej |
> | register/privateEndpointConnections | Nej | Nej |
> | register/privateLinkResources | Nej | Nej |
> | register/queueBuild | Nej | Nej |
> | register/regenerateCredential | Nej | Nej |
> | register/regenerateCredentials | Nej | Nej |
> | register/replikeringar | Ja | Ja |
> | register/körningar | Nej | Nej |
> | register/körningar/Avbryt | Nej | Nej |
> | register/scheduleRun | Nej | Nej |
> | register/scopeMaps | Nej | Nej |
> | register/taskRuns | Nej | Nej |
> | register/uppgifter | Ja | Ja |
> | register/token | Nej | Nej |
> | register/updatePolicies | Nej | Nej |
> | register/Webhooks | Ja | Ja |
> | register/Webhooks/getCallbackConfig | Nej | Nej |
> | register/Webhooks/ping | Nej | Nej |

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | containerServices | Ja | Ja |
> | managedClusters | Ja | Ja |
> | openShiftManagedClusters | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Aviseringar | Nej | Nej |
> | BillingAccounts | Nej | Nej |
> | Budgetar | Nej | Nej |
> | CloudConnectors | Nej | Nej |
> | Anslutningar | Ja | Ja |
> | costAllocationRules | Nej | Nej |
> | Avdelningar | Nej | Nej |
> | Dimensioner | Nej | Nej |
> | EnrollmentAccounts | Nej | Nej |
> | Exporteras | Nej | Nej |
> | ExternalBillingAccounts | Nej | Nej |
> | ExternalBillingAccounts/aviseringar | Nej | Nej |
> | ExternalBillingAccounts/dimensioner | Nej | Nej |
> | ExternalBillingAccounts/prognos | Nej | Nej |
> | ExternalBillingAccounts/fråga | Nej | Nej |
> | ExternalSubscriptions | Nej | Nej |
> | ExternalSubscriptions/aviseringar | Nej | Nej |
> | ExternalSubscriptions/dimensioner | Nej | Nej |
> | ExternalSubscriptions/prognos | Nej | Nej |
> | ExternalSubscriptions/fråga | Nej | Nej |
> | Prognos | Nej | Nej |
> | Söka i data | Nej | Nej |
> | registrera | Nej | Nej |
> | Reportconfigs | Nej | Nej |
> | Rapporter | Nej | Nej |
> | Inställningar | Nej | Nej |
> | showbackRules | Nej | Nej |
> | Vyer | Nej | Nej |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | autentiseringsbegäran | Nej | Nej |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | typer | Nej | Nej |
> | resourceProviders | Ja | Ja |

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
> | arbets ytor/dbWorkspaces | Nej | Nej |
> | arbets ytor/storageEncryption | Nej | Nej |
> | arbets ytor/virtualNetworkPeerings | Nej | Nej |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kataloger | Ja | Ja |
> | datacatalogs | Ja | Ja |
> | datacatalogs/data källor | Nej | Nej |
> | datacatalogs/data källor/-genomsökningar | Nej | Nej |
> | datacatalogs/data källor/genomsökningar/data uppsättningar | Nej | Nej |
> | datacatalogs/data källor/genomsökningar/utlösare | Nej | Nej |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | dataFactories | Ja | Nej |
> | dataFactories / diagnosticSettings | Nej | Nej |
> | dataFactories / metricDefinitions | Nej | Nej |
> | dataFactorySchema | Nej | Nej |
> | fabriker | Ja | Nej |
> | fabriker/integrationRuntimes | Nej | Nej |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/dataLakeStoreAccounts | Nej | Nej |
> | konton/storageAccounts | Nej | Nej |
> | konton/storageAccounts/behållare | Nej | Nej |
> | konton/transferAnalyticsUnits | Nej | Nej |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/eventGridFilters | Nej | Nej |
> | konton/firewallRules | Nej | Nej |

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | services | Nej | Nej |
> | tjänster/projekt | Nej | Nej |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Ja | Ja |
> | ResourceOperationGateKeepers | Ja | Ja |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/resurser | Nej | Nej |
> | konton/resurser/data uppsättningar | Nej | Nej |
> | konton/resurser/inbjudningar | Nej | Nej |
> | konton/resurser/providersharesubscriptions | Nej | Nej |
> | konton/resurser/synchronizationSettings | Nej | Nej |
> | konton/sharesubscriptions | Nej | Nej |
> | konton/sharesubscriptions/consumerSourceDataSets | Nej | Nej |
> | konton/sharesubscriptions/datasetmappings | Nej | Nej |
> | konton/sharesubscriptions/utlösare | Nej | Nej |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | brygghuvudservrar | Ja | Ja |
> | servrar/rådgivare | Nej | Nej |
> | servrar/nycklar | Nej | Nej |
> | servrar/privateEndpointConnectionProxies | Nej | Nej |
> | servrar/privateEndpointConnections | Nej | Nej |
> | servrar/privateLinkResources | Nej | Nej |
> | servrar/queryTexts | Nej | Nej |
> | servrar/recoverableServers | Nej | Nej |
> | servrar/topQueryStatistics | Nej | Nej |
> | servrar/virtualNetworkRules | Nej | Nej |
> | servrar/waitStatistics | Nej | Nej |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | brygghuvudservrar | Ja | Ja |
> | servrar/rådgivare | Nej | Nej |
> | servrar/nycklar | Nej | Nej |
> | servrar/privateEndpointConnectionProxies | Nej | Nej |
> | servrar/privateEndpointConnections | Nej | Nej |
> | servrar/privateLinkResources | Nej | Nej |
> | servrar/queryTexts | Nej | Nej |
> | servrar/recoverableServers | Nej | Nej |
> | servrar/topQueryStatistics | Nej | Nej |
> | servrar/virtualNetworkRules | Nej | Nej |
> | servrar/waitStatistics | Nej | Nej |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | serverGroups | Ja | Ja |
> | brygghuvudservrar | Ja | Ja |
> | servrar/rådgivare | Nej | Nej |
> | servrar/nycklar | Nej | Nej |
> | servrar/privateEndpointConnectionProxies | Nej | Nej |
> | servrar/privateEndpointConnections | Nej | Nej |
> | servrar/privateLinkResources | Nej | Nej |
> | servrar/queryTexts | Nej | Nej |
> | servrar/recoverableServers | Nej | Nej |
> | servrar/topQueryStatistics | Nej | Nej |
> | servrar/virtualNetworkRules | Nej | Nej |
> | servrar/waitStatistics | Nej | Nej |
> | serversv2 | Ja | Ja |
> | singleServers | Ja | Ja |

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
> | applicationgroups/program | Nej | Nej |
> | applicationgroups/Station ära datorer | Nej | Nej |
> | applicationgroups / startmenuitems | Nej | Nej |
> | hostpools | Ja | Ja |
> | hostpools / sessionhosts | Nej | Nej |
> | hostpools / sessionhosts / usersessions | Nej | Nej |
> | hostpools / usersessions | Nej | Nej |
> | arbetsytor | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ja | Ja |
> | ElasticPools / IotHubTenants | Ja | Ja |
> | ElasticPools/IotHubTenants/securitySettings | Nej | Nej |
> | IotHubs | Ja | Ja |
> | IotHubs / eventGridFilters | Nej | Nej |
> | IotHubs/securitySettings | Nej | Nej |
> | ProvisioningServices | Ja | Ja |
> | användningar | Nej | Nej |

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
> | digitalTwinsInstances/slut punkter | Nej | Nej |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nej | Nej |
> | databaseAccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | domäner | Ja | Ja |
> | domäner/domainOwnershipIdentifiers | Nej | Nej |
> | generateSsoRequest | Nej | Nej |
> | topLevelDomains | Nej | Nej |
> | validateDomainRegistrationInformation | Nej | Nej |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nej | Nej |
> | lcsprojects / clouddeployments | Nej | Nej |
> | lcsprojects/kopplingar | Nej | Nej |

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
> | domäner/ämnen | Nej | Nej |
> | eventSubscriptions | Nej | Nej |
> | extensionTopics | Nej | Nej |
> | partnerNamespaces | Ja | Ja |
> | partnerNamespaces/eventChannels | Nej | Nej |
> | partnerRegistrations | Ja | Ja |
> | partnerTopics | Ja | Ja |
> | partnerTopics / eventSubscriptions | Nej | Nej |
> | systemTopics | Ja | Ja |
> | systemTopics / eventSubscriptions | Nej | Nej |
> | avsnitt | Ja | Ja |
> | topicTypes | Nej | Nej |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kluster | Ja | Ja |
> | namn områden | Ja | Ja |
> | namnrymder/authorizationrules | Nej | Nej |
> | namnrymder/disasterrecoveryconfigs | Nej | Nej |
> | namnrymder/eventhubs | Nej | Nej |
> | namnrymder/eventhubs/authorizationrules | Nej | Nej |
> | namnrymder/eventhubs/consumergroups | Nej | Nej |
> | namnrymder/networkrulesets | Nej | Nej |

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
> | featureProviders | Nej | Nej |
> | funktioner | Nej | Nej |
> | finansiär | Nej | Nej |
> | subscriptionFeatureRegistrations | Nej | Nej |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | certifiering | Nej | Nej |
> | galleryitems | Nej | Nej |
> | generateartifactaccessuri | Nej | Nej |
> | områden | Nej | Nej |
> | område/områden | Nej | Nej |
> | område/områden/områden | Nej | Nej |
> | område/områden/områden/galleryitems | Nej | Nej |
> | områdets/områden/galleryitems | Nej | Nej |
> | områden/galleryitems | Nej | Nej |
> | registrera | Nej | Nej |
> | resources | Nej | Nej |
> | retrieveresourcesbyid | Nej | Nej |

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
> | configurationProfileAssignments | Nej | Nej |
> | guestConfigurationAssignments | Nej | Nej |
> | IntelliPoint | Nej | Nej |
> | softwareUpdateProfile | Nej | Nej |
> | softwareUpdates | Nej | Nej |

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
> | kluster | Ja | Ja |
> | kluster/program | Nej | Nej |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |
> | tjänster/iomtconnectors | Nej | Nej |
> | tjänster/iomtconnectors/anslutningar | Nej | Nej |
> | tjänster/iomtconnectors/mappningar | Nej | Nej |
> | tjänster/privateEndpointConnectionProxies | Nej | Nej |
> | tjänster/privateEndpointConnections | Ja | Ja |
> | tjänster/privateLinkResources | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | faxar | Ja | Ja |
> | datorer/tillägg | Ja | Ja |

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
> | registeredSubscriptions | Nej | Nej |
> | leverantören | Nej | Nej |
> | leverantörer/SKU: er | Nej | Nej |
> | leverantörer/vnfs | Nej | Nej |
> | virtualNetworkFunctionSkus | Nej | Nej |
> | vnfs | Ja | Ja |

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

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | actionGroups | Ja | Ja |
> | activityLogAlerts | Ja | Ja |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | delarna | Ja | Ja |
> | komponenter/linkedStorageAccounts | Nej | Nej |
> | komponenter/ProactiveDetectionConfigs | Nej | Nej |
> | diagnosticSettings | Nej | Nej |
> | guestDiagnosticSettings | Ja | Ja |
> | guestDiagnosticSettingsAssociation | Ja | Ja |
> | logprofiles | Ja | Ja |
> | metricAlerts | Ja | Ja |
> | privateLinkScopes | Ja | Ja |
> | privateLinkScopes / privateEndpointConnections | Nej | Nej |
> | privateLinkScopes / scopedResources | Nej | Nej |
> | queryPacks | Ja | Ja |
> | queryPacks/frågor | Nej | Nej |
> | scheduledQueryRules | Ja | Ja |
> | webbtester | Ja | Ja |
> | arbetsböcker | Ja | Ja |
> | workbooktemplates | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nej | Nej |
> | diagnosticSettingsCategories | Nej | Nej |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nej | Nej |
> | IoTApps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft. nyckel valv

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Nej | Nej |
> | hsmPools | Ja | Ja |
> | managedHSMs | Ja | Ja |
> | valv | Ja | Ja |
> | valv/accessPolicies | Nej | Nej |
> | valv/eventGridFilters | Nej | Nej |
> | valv/hemligheter | Nej | Nej |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Ja | Ja |
> | registeredSubscriptions | Nej | Nej |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | sourceControlConfigurations | Nej | Nej |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kluster | Ja | Ja |
> | kluster/attacheddatabaseconfigurations | Nej | Nej |
> | kluster/databaser | Nej | Nej |
> | kluster/databaser/dataconnections | Nej | Nej |
> | kluster/databaser/eventhubconnections | Nej | Nej |
> | kluster/databaser/principalassignments | Nej | Nej |
> | kluster/dataconnections | Nej | Nej |
> | kluster/principalassignments | Nej | Nej |
> | kluster/sharedidentities | Nej | Nej |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ja | Ja |
> | användare | Nej | Nej |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ja | Ja |
> | integrationAccounts | Ja | Ja |
> | integrationServiceEnvironments | Ja | Ja |
> | integrationServiceEnvironments/managedApis | Nej | Nej |
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
> | arbetsytor | Ja | Ja |
> | arbets ytor/beräkningar | Nej | Nej |
> | arbets ytor/eventGridFilters | Nej | Nej |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Nej | Nej |
> | configurationAssignments | Nej | Nej |
> | maintenanceConfigurations | Ja | Ja |
> | uppdateringar | Nej | Nej |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Identiteter | Nej | Nej |
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
> | marketplaceRegistrationDefinitions | Nej | Nej |
> | registrationAssignments | Nej | Nej |
> | registrationDefinitions | Nej | Nej |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | getEntities | Nej | Nej |
> | managementGroups | Nej | Nej |
> | managementGroups/inställningar | Nej | Nej |
> | resources | Nej | Nej |
> | startTenantBackfill | Nej | Nej |
> | tenantBackfillStatus | Nej | Nej |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton/eventGridFilters | Nej | Nej |
> | konton/privateAtlases | Ja | Ja |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | budgivning | Nej | Nej |
> | offerTypes | Nej | Nej |
> | offerTypes/utgivare | Nej | Nej |
> | offerTypes/utgivare/erbjudanden | Nej | Nej |
> | offerTypes/utgivare/erbjudanden/planer | Nej | Nej |
> | offerTypes/utgivare/erbjudanden/planer/avtal | Nej | Nej |
> | offerTypes/utgivare/erbjudanden/planer/konfigurationer | Nej | Nej |
> | offerTypes/utgivare/erbjudanden/planer/configs/importImage | Nej | Nej |
> | privategalleryitems | Nej | Nej |
> | privateStoreClient | Nej | Nej |
> | privateStores | Nej | Nej |
> | privateStores/erbjudanden | Nej | Nej |
> | läkemedle | Nej | Nej |
> | Utgivare | Nej | Nej |
> | Utgivare/erbjudanden | Nej | Nej |
> | Utgivare/erbjudanden/ändringar | Nej | Nej |
> | registrera | Nej | Nej |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ja | Ja |
> | updateCommunicationPreference | Nej | Nej |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | villkor | Nej | Nej |
> | offertypes | Nej | Nej |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Media Services | Ja | Ja |
> | Media Services/accountFilters | Nej | Nej |
> | Media Services/till gångar | Nej | Nej |
> | Media Services/assets/assetFilters | Nej | Nej |
> | Media Services/contentKeyPolicies | Nej | Nej |
> | Media Services/eventGridFilters | Nej | Nej |
> | Media Services/liveEventOperations | Nej | Nej |
> | Media Services/liveEvents | Ja | Ja |
> | Media Services/liveEvents/liveOutputs | Nej | Nej |
> | Media Services/liveEvents/privateEndpointConnectionProxies | Nej | Nej |
> | Media Services/liveOutputOperations | Nej | Nej |
> | Media Services/mediaGraphs | Nej | Nej |
> | Media Services/streamingEndpointOperations | Nej | Nej |
> | Media Services/strömnings slut punkter | Ja | Ja |
> | Media Services/strömnings slut punkter/privateEndpointConnectionProxies | Nej | Nej |
> | Media Services/streamingLocators | Nej | Nej |
> | Media Services/streamingPolicies | Nej | Nej |
> | Media Services/streamingPrivateEndpointConnectionProxyOperations | Nej | Nej |
> | Media Services/transformeringar | Nej | Nej |
> | Media Services/transformeringar/jobb | Nej | Nej |

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
> | objectUnderstandingAccounts | Ja | Ja |
> | remoteRenderingAccounts | Ja | Ja |
> | spatialAnchorsAccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ja | Nej |
> | netAppAccounts / accountBackups | Nej | Nej |
> | netAppAccounts / capacityPools | Ja | Nej |
> | netAppAccounts/capacityPools/Volumes | Ja | Nej |
> | netAppAccounts/capacityPools/volym/ögonblicks bilder | Nej | Nej |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ja | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja | Ja |
> | applicationSecurityGroups | Ja | Ja |
> | azureFirewallFqdnTags | Nej | Nej |
> | azureFirewalls | Ja | Nej |
> | bastionHosts | Ja | Nej |
> | bgpServiceCommunities | Nej | Nej |
> | anslutning | Ja | Ja |
> | ddosCustomPolicies | Ja | Ja |
> | ddosProtectionPlans | Ja | Ja |
> | dnsOperationStatuses | Nej | Nej |
> | dnszones | Ja | Ja |
> | dnszones/A | Nej | Nej |
> | dnszones/AAAA | Nej | Nej |
> | dnszones/alla | Nej | Nej |
> | dnszones/CAA | Nej | Nej |
> | dnszones/CNAME | Nej | Nej |
> | dnszones/MX | Nej | Nej |
> | dnszones/NS | Nej | Nej |
> | dnszones/PTR | Nej | Nej |
> | dnszones/Recordset | Nej | Nej |
> | dnszones/SOA | Nej | Nej |
> | dnszones/SRV | Nej | Nej |
> | dnszones/TXT | Nej | Nej |
> | expressRouteCircuits | Ja | Ja |
> | expressRouteCrossConnections | Ja | Ja |
> | expressRouteGateways | Ja | Ja |
> | expressRoutePorts | Ja | Ja |
> | expressRouteServiceProviders | Nej | Nej |
> | firewallPolicies | Ja | Ja |
> | frontdoors | Ja, men begränsat (se [Obs! nedan](#frontdoor)) | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ja, men begränsat (se [Obs! nedan](#frontdoor)) | Nej |
> | frontdoorWebApplicationFirewallPolicies | Ja, men begränsat (se [Obs! nedan](#frontdoor)) | Ja |
> | getDnsResourceReference | Nej | Nej |
> | internalNotify | Nej | Nej |
> | Belastningsutjämnare | Ja | Ja |
> | localNetworkGateways | Ja | Ja |
> | natGateways | Ja | Ja |
> | networkIntentPolicies | Ja | Ja |
> | networkInterfaces | Ja | Ja |
> | networkProfiles | Ja | Ja |
> | networkSecurityGroups | Ja | Ja |
> | networkWatchers | Ja | Ja |
> | networkWatchers / connectionMonitors | Ja | Nej |
> | networkWatchers / flowLogs | Nej | Nej |
> | networkWatchers/linser | Ja | Nej |
> | networkWatchers / pingMeshes | Ja | Nej |
> | p2sVpnGateways | Ja | Ja |
> | privateDnsOperationStatuses | Nej | Nej |
> | privateDnsZones | Ja | Ja |
> | privateDnsZones/A | Nej | Nej |
> | privateDnsZones/AAAA | Nej | Nej |
> | privateDnsZones/alla | Nej | Nej |
> | privateDnsZones/CNAME | Nej | Nej |
> | privateDnsZones/MX | Nej | Nej |
> | privateDnsZones/PTR | Nej | Nej |
> | privateDnsZones/SOA | Nej | Nej |
> | privateDnsZones/SRV | Nej | Nej |
> | privateDnsZones/TXT | Nej | Nej |
> | privateDnsZones / virtualNetworkLinks | Ja | Ja |
> | privateEndpoints | Ja | Ja |
> | privateLinkServices | Ja | Ja |
> | publicIPAddresses | Ja | Ja |
> | publicIPPrefixes | Ja | Ja |
> | routeFilters | Ja | Ja |
> | routeTables | Ja | Ja |
> | serviceEndpointPolicies | Ja | Ja |
> | trafficManagerGeographicHierarchies | Nej | Nej |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/termiska kartor | Nej | Nej |
> | trafficManagerUserMetricsKeys | Nej | Nej |
> | virtualHubs | Ja | Ja |
> | virtualNetworkGateways | Ja | Ja |
> | virtualNetworks | Ja | Ja |
> | virtualNetworks/undernät | Nej | Nej |
> | virtualNetworkTaps | Ja | Ja |
> | virtualWans | Ja | Nej |
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
> | NotebookProxies | Nej | Nej |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | namn områden | Ja | Nej |
> | namnrymder/notificationHubs | Ja | Nej |

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
> | ServerSites | Ja | Ja |
> | VMwareSites | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kluster | Ja | Ja |
> | deletedWorkspaces | Nej | Nej |
> | linkTargets | Nej | Nej |
> | storageInsightConfigs | Nej | Nej |
> | arbetsytor | Ja | Ja |
> | arbets ytor/dataExports | Nej | Nej |
> | arbets ytor/data källor | Nej | Nej |
> | arbets ytor/linkedServices | Nej | Nej |
> | arbets ytor/linkedStorageAccounts | Nej | Nej |
> | arbets ytor/metadata | Nej | Nej |
> | arbets ytor/fråga | Nej | Nej |
> | arbets ytor/scopedPrivateLinkProxies | Nej | Nej |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | managementassociations | Nej | Nej |
> | managementconfigurations | Ja | Ja |
> | lösningar | Ja | Ja |
> | vyer | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nej | Nej |
> | peerAsns | Nej | Nej |
> | peerings | Ja | Ja |
> | peeringServiceCountries | Nej | Nej |
> | peeringServiceProviders | Nej | Nej |
> | peeringServices | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | policyEvents | Nej | Nej |
> | policyMetadata | Nej | Nej |
> | policyStates | Nej | Nej |
> | policyTrackedResources | Nej | Nej |
> | reparationer | Nej | Nej |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> |  -konsoler | Nej | Nej |
> | instrumentpaneler | Ja | Ja |
> | userSettings | Nej | Nej |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Ja | Ja |
> | klienter | Ja | Ja |
> | innehavare/arbets ytor | Nej | Nej |
> | workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | kapaciteter | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nej | Nej |
> | providerRegistrations / defaultRollouts | Nej | Nej |
> | providerRegistrations / resourceTypeRegistrations | Nej | Nej |
> | distributioner | Ja | Ja |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Arbetsytor | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nej | Nej |
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
> | namnrymder/authorizationrules | Nej | Nej |
> | namnrymder/hybridconnections | Nej | Nej |
> | namnrymder/hybridconnections/authorizationrules | Nej | Nej |
> | namnrymder/privateEndpointConnections | Nej | Nej |
> | namnrymder/wcfrelays | Nej | Nej |
> | namnrymder/wcfrelays/authorizationrules | Nej | Nej |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | skickar | Ja | Ja |
> | resourceChangeDetails | Nej | Nej |
> | resourceChanges | Nej | Nej |
> | resources | Nej | Nej |
> | resourcesHistory | Nej | Nej |
> | subscriptionsStatus | Nej | Nej |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nej | Nej |
> | childAvailabilityStatuses | Nej | Nej |
> | childResources | Nej | Nej |
> | emergingissues | Nej | Nej |
> | händelser | Nej | Nej |
> | impactedResources | Nej | Nej |
> | metadata | Nej | Nej |
> | meddelanden | Nej | Nej |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | calculateTemplateHash | Nej | Nej |
> | distributioner | Ja | Nej |
> | distributioner/åtgärder | Nej | Nej |
> | deploymentScripts | Ja | Ja |
> | deploymentScripts/loggar | Nej | Nej |
> | Länkar | Nej | Nej |
> | notifyResourceJobs | Nej | Nej |
> | finansiär | Nej | Nej |
> | resourceGroups | Ja | Nej |
> | prenumerationer | Ja | Nej |
> | templateSpecs | Ja | Ja |
> | templateSpecs/versioner | Ja | Ja |
> | klienter | Nej | Nej |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | saasresources | Nej | Nej |

## <a name="microsoftsearch"></a>Microsoft. search

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nej | Nej |
> | searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nej | Nej |
> | advancedThreatProtectionSettings | Nej | Nej |
> | aviseringar | Nej | Nej |
> | alertsSuppressionRules | Nej | Nej |
> | allowedConnections | Nej | Nej |
> | applicationWhitelistings | Nej | Nej |
> | assessmentMetadata | Nej | Nej |
> | utvärderingar | Nej | Nej |
> | autoDismissAlertsRules | Nej | Nej |
> | automatiseringar | Ja | Ja |
> | AutoProvisioningSettings | Nej | Nej |
> | Godkännanden | Nej | Nej |
> | dataCollectionAgents | Nej | Nej |
> | deviceSecurityGroups | Nej | Nej |
> | discoveredSecuritySolutions | Nej | Nej |
> | externalSecuritySolutions | Nej | Nej |
> | InformationProtectionPolicies | Nej | Nej |
> | iotSecuritySolutions | Ja | Ja |
> | iotSecuritySolutions / analyticsModels | Nej | Nej |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nej | Nej |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nej | Nej |
> | iotSecuritySolutions / iotAlerts | Nej | Nej |
> | iotSecuritySolutions / iotAlertTypes | Nej | Nej |
> | jitNetworkAccessPolicies | Nej | Nej |
> | policies | Nej | Nej |
> | prissättningar | Nej | Nej |
> | regulatoryComplianceStandards | Nej | Nej |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nej | Nej |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nej | Nej |
> | secureScoreControlDefinitions | Nej | Nej |
> | secureScoreControls | Nej | Nej |
> | secureScores | Nej | Nej |
> | secureScores / secureScoreControls | Nej | Nej |
> | securityContacts | Nej | Nej |
> | securitySolutions | Nej | Nej |
> | securitySolutionsReferenceData | Nej | Nej |
> | securityStatuses | Nej | Nej |
> | securityStatusesSummaries | Nej | Nej |
> | serverVulnerabilityAssessments | Nej | Nej |
> | inställningar | Nej | Nej |
> | underbedömningar | Nej | Nej |
> | uppgifter | Nej | Nej |
> | topologier | Nej | Nej |
> | workspaceSettings | Nej | Nej |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nej | Nej |
> | diagnosticSettingsCategories | Nej | Nej |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | agg regeringar | Nej | Nej |
> | alertRules | Nej | Nej |
> | alertRuleTemplates | Nej | Nej |
> | automationRules | Nej | Nej |
> | bokmärken | Nej | Nej |
> | fall | Nej | Nej |
> | dataConnectors | Nej | Nej |
> | dataConnectorsCheckRequirements | Nej | Nej |
> | poster | Nej | Nej |
> | entityQueries | Nej | Nej |
> | incidenter | Nej | Nej |
> | officeConsents | Nej | Nej |
> | inställningar | Nej | Nej |
> | threatIntelligence | Nej | Nej |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | consoleServices | Nej | Nej |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | namn områden | Ja | Ja |
> | namnrymder/authorizationrules | Nej | Nej |
> | namnrymder/disasterrecoveryconfigs | Nej | Nej |
> | namnrymder/eventgridfilters | Nej | Nej |
> | namnrymder/networkrulesets | Nej | Nej |
> | namnrymder/köer | Nej | Nej |
> | namnrymder/köer/authorizationrules | Nej | Nej |
> | namn områden/ämnen | Nej | Nej |
> | namnrymder/ämnen/authorizationrules | Nej | Nej |
> | namnrymder/ämnen/prenumerationer | Nej | Nej |
> | namn områden/ämnen/prenumerationer/regler | Nej | Nej |
> | premiumMessagingRegions | Nej | Nej |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | kluster | Ja | Ja |
> | kluster/program | Nej | Nej |
> | containerGroups | Ja | Ja |
> | containerGroupSets | Ja | Ja |
> | edgeclusters | Ja | Ja |
> | edgeclusters/program | Nej | Nej |
> | managedclusters | Ja | Ja |
> | managedclusters/nodetypes | Nej | Nej |
> | nätet | Ja | Ja |
> | secretstores | Ja | Ja |
> | secretstores/certifikat | Nej | Nej |
> | secretstores/hemligheter | Nej | Nej |
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

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nej | Nej |
> | providerRegistrations / resourceTypeRegistrations | Nej | Nej |
> | distributioner | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | SignalR | Ja | Ja |
> | SignalR/eventGridFilters | Nej | Nej |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nej | Nej |

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
> | managedInstances | Ja | Ja |
> | managedInstances/databaser | Ja (se [Anmärkning nedan](#sqlnote)) | Ja |
> | managedInstances/databaser/backupShortTermRetentionPolicies | Nej | Nej |
> | managedInstances/databaser/scheman/tabeller/kolumner/sensitivityLabels | Nej | Nej |
> | managedInstances/databaser/vulnerabilityAssessments | Nej | Nej |
> | managedInstances/databaser/vulnerabilityAssessments/regler/bas linjer | Nej | Nej |
> | managedInstances / encryptionProtector | Nej | Nej |
> | managedInstances/nycklar | Nej | Nej |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nej | Nej |
> | managedInstances / vulnerabilityAssessments | Nej | Nej |
> | brygghuvudservrar | Ja | Ja |
> | servrar/administratörer | Nej | Nej |
> | servrar/communicationLinks | Nej | Nej |
> | servrar/databaser | Ja (se [Anmärkning nedan](#sqlnote)) | Ja |
> | servrar/encryptionProtector | Nej | Nej |
> | servrar/firewallRules | Nej | Nej |
> | servrar/nycklar | Nej | Nej |
> | servrar/restorableDroppedDatabases | Nej | Nej |
> | servrar/serviceobjectives | Nej | Nej |
> | servrar/tdeCertificates | Nej | Nej |
> | virtualClusters | Nej | Nej |

<a id="sqlnote"></a>

> [!NOTE]
> Huvud databasen stöder inte taggar, men andra databaser, inklusive Azure Synapse Analytics-databaser, stöder taggar. Azure Synapse Analytics-databaser måste vara i aktivt (inte pausat) tillstånd.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ja | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nej | Nej |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Ja | Ja |
> | storageAccounts/blobServices | Nej | Nej |
> | storageAccounts/fileServices | Nej | Nej |
> | storageAccounts/queueServices | Nej | Nej |
> | storageAccounts/tjänster | Nej | Nej |
> | storageAccounts/tjänster/metricDefinitions | Nej | Nej |
> | storageAccounts/tableServices | Nej | Nej |
> | användningar | Nej | Nej |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | cacheminnen | Ja | Ja |
> | cache-/storageTargets | Nej | Nej |
> | usageModels | Nej | Nej |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nej | Nej |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nej | Nej |
> | storageSyncServices / syncGroups | Nej | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej | Nej |
> | storageSyncServices/arbets flöden | Nej | Nej |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nej | Nej |
> | storageSyncServices / syncGroups | Nej | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej | Nej |
> | storageSyncServices/arbets flöden | Nej | Nej |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nej | Nej |
> | storageSyncServices / syncGroups | Nej | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej | Nej |
> | storageSyncServices/arbets flöden | Nej | Nej |

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
> | streamingjobs | Ja (se anmärkning nedan) | Ja |

> [!NOTE]
> Du kan inte lägga till en tagg när streamingjobs körs. Stoppa resursen för att lägga till en tagg.

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | avbryt | Nej | Nej |
> | CreateSubscription | Nej | Nej |
> | Aktivera | Nej | Nej |
> | byt namn | Nej | Nej |
> | SubscriptionDefinitions | Nej | Nej |
> | SubscriptionOperations | Nej | Nej |
> | prenumerationer | Nej | Nej |

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Ja | Ja |
> | arbetsytor | Ja | Ja |
> | arbets ytor/bigDataPools | Ja | Ja |
> | arbets ytor/operationStatuses | Nej | Nej |
> | arbets ytor/sqlPools | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | utrymmen | Ja | Nej |
> | miljöer/accessPolicies | Nej | Nej |
> | miljöer/eventsources | Ja | Nej |
> | miljöer/referenceDataSets | Ja | Nej |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | Auktoriseringshanteraren | Ja | Ja |
> | butiker/accessPolicies | Nej | Nej |
> | butiker/tjänster | Nej | Nej |
> | butiker/tjänster/token | Nej | Nej |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Ja | Ja |
> | imageTemplates / runOutputs | Nej | Nej |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | ArcZones | Ja | Ja |
> | ResourcePools | Ja | Ja |
> | VCenter | Ja | Ja |
> | VirtualMachines | Ja | Ja |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ja | Ja |
> | dedicatedCloudServices | Ja | Ja |
> | virtualMachines | Ja | Ja |

## <a name="microsoftvmwareonazure"></a>Microsoft. VMwareOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | privateClouds | Ja | Ja |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | devices | Ja | Ja |
> | registeredSubscriptions | Nej | Nej |
> | leverantören | Nej | Nej |
> | leverantörer/SKU: er | Nej | Nej |
> | leverantörer/vnfs | Nej | Nej |
> | virtualNetworkFunctionSkus | Nej | Nej |
> | vnfs | Ja | Ja |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | utgå | Ja | Ja |
> | registeredSubscriptions | Nej | Nej |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nej | Nej |
> | apiManagementAccounts / apiAcls | Nej | Nej |
> | apiManagementAccounts/API: er | Nej | Nej |
> | apiManagementAccounts/API/apiAcls | Nej | Nej |
> | apiManagementAccounts/API/connectionAcls | Nej | Nej |
> | apiManagementAccounts/API/anslutningar | Nej | Nej |
> | apiManagementAccounts/API/Connections/connectionAcls | Nej | Nej |
> | apiManagementAccounts/API/localizedDefinitions | Nej | Nej |
> | apiManagementAccounts / connectionAcls | Nej | Nej |
> | apiManagementAccounts/anslutningar | Nej | Nej |
> | billingMeters | Nej | Nej |
> | certifikat | Ja | Ja |
> | connectionGateways | Ja | Ja |
> | anslutning | Ja | Ja |
> | customApis | Ja | Ja |
> | deletedSites | Nej | Nej |
> | hostingEnvironments | Ja | Ja |
> | hostingEnvironments / eventGridFilters | Nej | Nej |
> | hostingEnvironments / multiRolePools | Nej | Nej |
> | hostingEnvironments / workerPools | Nej | Nej |
> | kubeEnvironments | Ja | Ja |
> | publishingUsers | Nej | Nej |
> | rekommendationer | Nej | Nej |
> | resourceHealthMetadata | Nej | Nej |
> | körningar | Nej | Nej |
> | Server grupper | Ja | Ja |
> | Server grupper/eventGridFilters | Nej | Nej |
> | webbplatser | Ja | Ja |
> | platser/konfig  | Nej | Nej |
> | platser/eventGridFilters | Nej | Nej |
> | platser/hostNameBindings | Nej | Nej |
> | platser/networkConfig | Nej | Nej |
> | platser/premieraddons | Ja | Ja |
> | platser/platser | Ja | Ja |
> | platser/platser/eventGridFilters | Nej | Nej |
> | platser/platser/hostNameBindings | Nej | Nej |
> | platser/platser/networkConfig | Nej | Nej |
> | sourceControls | Nej | Nej |
> | staticSites | Ja | Ja |
> | kontrollerar | Nej | Nej |
> | verifyHostingEnvironmentVnet | Nej | Nej |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nej | Nej |
> | diagnosticSettingsCategories | Nej | Nej |

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
> | arbets belastningar | Ja | Ja |
> | arbets belastningar/instanser | Nej | Nej |
> | arbets belastningar/versioner | Nej | Nej |
> | arbets belastningar/versioner/artefakter | Nej | Nej |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
> | ------------- | ----------- | ----------- |
> | delarna | Nej | Nej |
> | componentsSummary | Nej | Nej |
> | monitorInstances | Nej | Nej |
> | monitorInstancesSummary | Nej | Nej |
> | Övervakare | Nej | Nej |
> | notificationSettings | Nej | Nej |

## <a name="next-steps"></a>Nästa steg

Information om hur du använder taggar för resurser finns i [använda taggar för att ordna dina Azure-resurser](tag-resources.md).
