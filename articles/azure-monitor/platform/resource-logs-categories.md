---
title: Azure Monitor resurs loggar som stöds tjänster och kategorier
description: Referens för Azure Monitor förstå tjänster och händelse scheman som stöds för Azures resurs loggar.
ms.subservice: logs
ms.topic: reference
ms.date: 12/09/2020
ms.openlocfilehash: c7b2d48b40843930bba78f54d2294769d952daf6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931239"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Kategorier som stöds för Azure-resurs loggar

> [!NOTE]
> Resurs loggar kallades tidigare för diagnostikloggar. Namnet ändrades i oktober 2019 när de typer av loggar som samlats in av Azure Monitor flyttas för att inkludera mer än bara Azure-resursen.

[Azure Monitor resurs loggar](./platform-logs-overview.md) genereras av Azure-tjänster som beskriver driften av dessa tjänster eller resurser. Alla resurs loggar som är tillgängliga via Azure Monitor dela ett gemensamt schema på högsta nivå, med flexibilitet för varje tjänst för att generera unika egenskaper för sina egna händelser.

En kombination av resurs typen (tillgänglig i `resourceId` egenskapen) och `category` unikt identifiera ett schema. Det finns ett gemensamt schema för alla resurs loggar med tjänstspecifika fält och läggs till för olika logg kategorier. Mer information finns i [common and Service Specific schema för Azures resurs loggar]()


## <a name="costs"></a>Kostnader

Det finns kostnader som är kopplade till att skicka och lagra data i Log Analytics Azure Storage och/eller Händelsehubben. Du kan betala för kostnaden för att hämta data till dessa platser och för att hålla dem där.  Resurs loggar är en typ av data som du kan skicka till dessa platser. Det finns ytterligare [kostnader för att exportera vissa kategorier av resurs loggar](https://azure.microsoft.com/pricing/details/monitor/) till dessa platser, medan andra är fria från export kostnader. Exportera kostnads information visas i tabellen nedan.

## <a name="supported-log-categories-per-resource-type"></a>Logg kategorier som stöds per resurs typ

Följande är en lista över de typer av loggar som är tillgängliga för varje resurs typ. 

Vissa kategorier kan bara användas för vissa typer av resurser. Se den Resource-/regionsspecifika dokumentationen om du anser att du saknar en resurs. Till exempel är inte kategorierna Microsoft. SQL/Server/Databass tillgängliga för alla typer av databaser. Mer information finns i [information om SQL Database diagnostisk loggning](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Om du fortfarande saknar något kan du öppna en GitHub-kommentar längst ned i den här artikeln.
## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servers

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|Motor|Motor|
|Tjänst|Tjänst|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|GatewayLogs|Loggar som rör API Management-Gateway|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/våren

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|ApplicationConsole|Program konsol|
|SystemLogs|System loggar|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|DscNodeStatus|DSC-nods status|
|JobLogs|Jobb loggar|
|JobStreams|Jobb strömmar|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|ServiceLog|Tjänst loggar|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/arbets ytor

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|BlockchainApplication|Blockchain-program|
|FabricOrderer|Fabric-beställare|
|FabricPeer|Infrastruktur resurs|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. blockchain/cordaMembers

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|BlockchainApplication|Blockchain-program|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|WebApplicationFirewallLogs|Brand Väggs loggar för webb webbappen|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profiler

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AzureCdnAccessLog|Azure CDN Access-logg|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/profiler/slut punkter

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|CoreAnalytics|Hämtar Mät värdena för slut punkten, t. ex. bandbredd, utgående data osv.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|Regel flödes händelse för nätverks säkerhets grupp|Regel flödes händelse för nätverks säkerhets grupp|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konton

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|Granska|Granskningsloggar|
|RequestResponse|Förfrågningar och svars loggar|
|Spårning|Spårnings loggar|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|ContainerRegistryLoginEvents|Inloggnings händelser|
|ContainerRegistryRepositoryEvents|RepositoryEvent-loggar|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|kluster – autoskalning|Kubernetes-kluster autoskalning|
|kube-apiserver|Kubernetes-API-Server|
|Kube – granskning|Kubernetes granskning|
|Kube-Controller-Manager|Kubernetes Controller Manager|
|Kube – Scheduler|Kubernetes Scheduler|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AuditLogs|Gransknings loggar för MiniRP-anrop|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. Databricks/arbets ytor

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|konton|Databricks-konton|
|kluster|Databricks-kluster|
|dBFS|Databricks-filsystem|
|instancePools|Instans-pooler|
|utskrifts|Databricks-jobb|
|notebook|Databricks-anteckningsbok|
|secrets|Databricks hemligheter|
|sqlPermissions|Databricks SQLPermissions|
|SSH|Databricks SSH|
|arbetsyta|Databricks-arbetsyta|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|ActivityRuns|Pipeline-aktivitet kör logg|
|PipelineRuns|Pipeline kör logg|
|TriggerRuns|Utlös körnings logg|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/konton

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|Granska|Granskningsloggar|
|Begäranden|Begär ande loggar|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/konton

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|ReceivedShareSnapshots|Mottagna resurs ögonblicks bilder|
|SentShareSnapshots|Ögonblicks bilder av skickade resurser|
|Resurser|Resurser|
|ShareSubscriptions|Dela prenumerationer|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servers

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|MySqlAuditLogs|Gransknings loggar för MariaDB|
|MySqlSlowLogs|MariaDB Server-loggar|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|MySqlAuditLogs|MySQL gransknings loggar|
|MySqlSlowLogs|Långsamma loggfiler för MySQL|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|MySqlAuditLogs|MySQL gransknings loggar|
|MySqlSlowLogs|MySQL server-loggar|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servers

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|
|QueryStoreRuntimeStatistics|PostgreSQL för fråge lagrings körning|
|QueryStoreWaitStatistics|Väntande statistik för PostgreSQL Query Store|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|Checkpoint|Checkpoint|
|Fel|Fel|
|Hantering|Hantering|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|Checkpoint|Checkpoint|
|Anslutning|Anslutning|
|Fel|Fel|
|HostRegistration|HostRegistration|
|Hantering|Hantering|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/arbets ytor

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|Checkpoint|Checkpoint|
|Fel|Fel|
|Feed|Feed|
|Hantering|Hantering|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|C2DCommands|C2D-kommandon|
|C2DTwinOperations|C2D dubbla åtgärder|
|Konfigurationer|Konfigurationer|
|Anslutningar|Anslutningar|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|Enhets identitets åtgärder|
|DeviceStreams|Enhets strömmar (förhands granskning)|
|DeviceTelemetry|Enhetstelemetri|
|DirectMethods|Direkta metoder|
|DistributedTracing|Distribuerad spårning (för hands version)|
|FileUploadOperations|Fil överförings åtgärder|
|JobsOperations|Jobb åtgärder|
|Vägar|Vägar|
|TwinQueries|Dubbla frågor|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|DeviceOperations|Enhets åtgärder|
|ServiceOperations|Tjänst åtgärder|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|DeliveryFailures|Loggar för leverans problem|
|PublishFailures|Publicera Felaktiga loggar|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|DeliveryFailures|Loggar för leverans problem|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|DeliveryFailures|Loggar för leverans problem|
|PublishFailures|Publicera Felaktiga loggar|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/Namespaces

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|ArchiveLogs|Arkiv loggar|
|AutoScaleLogs|Automatisk skalnings loggar|
|CustomerManagedKeyUserLogs|Kund hanterade nyckel loggar|
|EventHubVNetConnectionEvent|Anslutnings loggar för VNet/IP-filtrering|
|KafkaCoordinatorLogs|Kafka koordinator loggar|
|KafkaUserErrorLogs|Kafka användar fel loggar|
|OperationalLogs|Drift loggar|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/Services

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AuditLogs|Granskningsloggar|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AutoscaleEvaluations|Autoskala-utvärderingar|
|AutoscaleScaleActions|Åtgärder för autoskalning av skala|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AppAvailabilityResults|Tillgänglighets resultat|
|AppBrowserTimings|Tids inställningar för webbläsare|
|AppDependencies|Beroenden|
|AppEvents|Händelser|
|AppExceptions|Undantag|
|AppMetrics|Mått|
|AppPageViews|Sid visningar|
|AppPerformanceCounters|Prestandaräknare|
|AppRequests|Begäranden|
|AppSystemEvents|System händelser|
|AppTraces|Spårningar|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. nyckel valv/-valv

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AuditEvent|Granskningsloggar|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|Kommando|Kommando|
|FailedIngestion|Misslyckade inmatnings åtgärder|
|IngestionBatching|Inmatningsbatchning|
|Söka i data|Söka i data|
|SucceededIngestion|Framgångs åtgärder|
|TableDetails|Tabell information|
|TableUsageStatistics|Statistik över Tabell användning|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|IntegrationAccountTrackingEvents|Spårnings händelser för integrations konto|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/arbets flöden

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|WorkflowRuntime|Diagnostiska händelser för arbets flödes körning|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/arbets ytor

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Media Services

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|KeyDeliveryRequests|Begär Anden om nyckel leverans|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|ApplicationGatewayAccessLog|Application Gateway åtkomst logg|
|ApplicationGatewayFirewallLog|Application Gateway brand Väggs logg|
|ApplicationGatewayPerformanceLog|Application Gateway prestanda logg|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AzureFirewallApplicationRule|Regel för Azure brand Väggs program|
|AzureFirewallNetworkRule|Nätverks regel för Azure Firewall|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|BastionAuditLogs|Gransknings loggar för skydds|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|PeeringRouteLog|Tabell loggar för peering-routning|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|FrontdoorAccessLog|Ytterdörr åtkomst logg|
|FrontdoorWebApplicationFirewallLog|Ytterdörr webb program brand Väggs logg|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/belastningsutjämnare

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|LoadBalancerAlertEvent|Load Balancer aviserings händelser|
|LoadBalancerProbeHealthStatus|Load Balancer avsökningens hälso status|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|NetworkSecurityGroupEvent|Händelse för nätverks säkerhets grupp|
|NetworkSecurityGroupFlowEvent|Regel flödes händelse för nätverks säkerhets grupp|
|NetworkSecurityGroupRuleCounter|Regel räknare för nätverks säkerhets grupp|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|DDoSMitigationFlowLogs|Flödes loggar för DDoSa beslut om minskning|
|DDoSMitigationReports|Rapporter för DDoS-åtgärder|
|DDoSProtectionNotifications|DDoS skydds meddelanden|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|ProbeHealthStatusEvents|Traffic Manager händelse av hälso resultat för avsökning|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|GatewayDiagnosticLog|Gateway-diagnostikloggar|
|IKEDiagnosticLog|IKE-diagnostikloggar|
|P2SDiagnosticLog|P2S diagnostikloggar|
|RouteDiagnosticLog|Vidarebefordra diagnostikloggar|
|TunnelDiagnosticLog|Tunnel diagnostikloggar|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|VMProtectionAlerts|Aviseringar för VM-skydd|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|Motor|Motor|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. RecoveryServices/valv

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AddonAzureBackupAlerts|Aviserings data för addon Azure Backup|
|AddonAzureBackupJobs|Jobb data för addon Azure Backup|
|AddonAzureBackupPolicy|Princip data för addon Azure Backup|
|AddonAzureBackupProtectedInstance|Addon Azure Backup skyddade instans data|
|AddonAzureBackupStorage|Tillägg för Azure Backup lagrings data|
|AzureBackupReport|Azure Backup rapporterings data|
|AzureSiteRecoveryEvents|Azure Site Recovery händelser|
|AzureSiteRecoveryJobs|Azure Site Recovery jobb|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery skyddad disk data omsättning|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery återställnings punkter|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikerade objekt|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery överförings hastighet för replikeringsdata|
|AzureSiteRecoveryReplicationStats|Statistik för Azure Site Recovery-replikering|
|CoreAzureBackup|Kärn Azure Backup data|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namnrymder

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|HybridConnectionsEvent|HybridConnections-händelser|


## <a name="microsoftsearchsearchservices"></a>Microsoft. search/searchServices

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|OperationLogs|Åtgärds loggar|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. Service Bus/namnrymder

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|OperationalLogs|Drift loggar|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AllLogs|Azure SignalR service-loggar.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|DevOpsOperationsAudit|Gransknings loggar för DevOps-åtgärder|
|ResourceUsageStats|Statistik för resursanvändning|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/-databaser

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|Fel|Fel|
|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|
|QueryStoreWaitStatistics|Väntande statistik för Query Store|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/databaser

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AutomaticTuning|Automatisk inställning|
|Delar|Delar|
|DatabaseWaitStatistics|Väntande statistik över databasen|
|Dödlägen|Dödlägen|
|DevOpsOperationsAudit|Gransknings loggar för DevOps-åtgärder|
|DmsWorkers|DMS-arbetare|
|Fel|Fel|
|ExecRequests|Exec-begäranden|
|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|
|QueryStoreWaitStatistics|Väntande statistik för Query Store|
|RequestSteps|Förfrågnings steg|
|SQLInsights|SQL Insights|
|SqlRequests|SQL-begäranden|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|
|Timeouter|Timeouter|
|Väntar|Väntar|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

Kostnad: betalad enligt beskrivningen i avsnittet plattforms loggar på [sidan Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/) 

|Kategori |Kategori visnings namn|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

Kostnad: betalad enligt beskrivningen i avsnittet plattforms loggar på [sidan Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/) 

|Kategori |Kategori visnings namn|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

Kostnad: betalad enligt beskrivningen i avsnittet plattforms loggar på [sidan Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Kategori |Kategori visnings namn|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

Kostnad: betalad enligt beskrivningen i avsnittet plattforms loggar på [sidan Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Kategori |Kategori visnings namn|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|Redigering|Redigering|
|Körnings-|Körnings-|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/arbets ytor

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|BuiltinSqlReqsEnded|De inbyggda SQL-pool-begärandena avslutas|
|GatewayApiRequests|API-begäranden för Synapse Gateway|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|
|SynapseRbacOperations|Synapse RBAC-åtgärder|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/arbets ytor/bigDataPools

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|BigDataPoolAppsEnded|Big data pool program har slutförts|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/arbets ytor/sqlPools

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|DmsWorkers|DMS-arbetare|
|ExecRequests|Exec-begäranden|
|RequestSteps|Förfrågnings steg|
|SqlRequests|SQL-begäranden|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|
|Väntar|Väntar|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

Kostnad: kostnads fri 

|Kategori |Kategori visnings namn|
|---|---|
|AppServiceEnvironmentPlatformLogs|App Service-miljön plattforms loggar|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

Kostnad: kostnads fri 


|Kategori |Kategori visnings namn|
|---|---|
|AppServiceAppLogs|App Service program loggar|
|AppServiceAuditLogs|Åtkomst gransknings loggar|
|AppServiceConsoleLogs|App Service konsol loggar|
|AppServiceFileAuditLogs|Gransknings loggar för ändring av webbplats innehåll|
|AppServiceHTTPLogs|HTTP-loggar|
|FunctionAppLogs|Funktions program loggar|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/lotss

Kostnad: kostnads fri 


|Kategori |Kategori visnings namn|
|---|---|
|AppServiceAppLogs|App Service program loggar|
|AppServiceAuditLogs|Åtkomst gransknings loggar|
|AppServiceConsoleLogs|App Service konsol loggar|
|AppServiceFileAuditLogs|Gransknings loggar för ändring av webbplats innehåll|
|AppServiceHTTPLogs|HTTP-loggar|
|FunctionAppLogs|Funktions program loggar|


## <a name="next-steps"></a>Nästa steg

* [Läs mer om resurs loggar](./platform-logs-overview.md)
* [Strömma resurs resurs loggar till **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Ändra diagnostikinställningar för resurs loggen med hjälp av Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Analysera loggar från Azure Storage med Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

