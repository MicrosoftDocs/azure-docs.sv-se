---
title: Azure Monitor resurs loggar som stöds tjänster och kategorier
description: Referens för Azure Monitor förstå tjänster och händelse scheman som stöds för Azures resurs loggar.
ms.subservice: logs
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 39ff78cd97682096fb284e137868c246dfdd7f14
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623147"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Kategorier som stöds för Azure-resurs loggar

> [!NOTE]
> Resurs loggar kallades tidigare för diagnostikloggar. Namnet ändrades i oktober 2019 när de typer av loggar som samlats in av Azure Monitor flyttas för att inkludera mer än bara Azure-resursen.

[Azure Monitor resurs loggar](../essentials/platform-logs-overview.md) genereras av Azure-tjänster som beskriver driften av dessa tjänster eller resurser. Alla resurs loggar som är tillgängliga via Azure Monitor dela ett gemensamt schema på högsta nivå, med flexibilitet för varje tjänst för att generera unika egenskaper för sina egna händelser.

En kombination av resurs typen (tillgänglig i `resourceId` egenskapen) och `category` unikt identifiera ett schema. Det finns ett gemensamt schema för alla resurs loggar med tjänstspecifika fält som har lagts till för olika logg kategorier. Mer information finns i [common and Service-Specific schema för Azures resurs loggar]()


## <a name="costs"></a>Kostnader

Det finns kostnader som är kopplade till att skicka och lagra data i Log Analytics Azure Storage och/eller Händelsehubben. Du kan betala för kostnaden för att hämta data till dessa platser och för att hålla dem där.  Resurs loggar är en typ av data som du kan skicka till dessa platser. 

Det finns ytterligare kostnader för att exportera vissa kategorier av resurs loggar till dessa platser. Loggarna med export kostnader visas i tabellen nedan. Mer information om den här prissättningen finns i avsnittet plattforms loggar på [sidan Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="supported-log-categories-per-resource-type"></a>Logg kategorier som stöds per resurs typ

Följande är en lista över de typer av loggar som är tillgängliga för varje resurs typ. 

Vissa kategorier kan bara användas för vissa typer av resurser. Se den Resource-/regionsspecifika dokumentationen om du anser att du saknar en resurs. Till exempel är inte kategorierna Microsoft. SQL/Server/Databass tillgängliga för alla typer av databaser. Mer information finns i [information om SQL Database diagnostisk loggning](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Om du tror att något saknas kan du öppna en GitHub-kommentar längst ned i den här artikeln.
## <a name="microsoftaaddomainservices"></a>Microsoft. AAD/domainServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AccountLogon|AccountLogon|No|
|AccountManagement|AccountManagement|No|
|DetailTracking|DetailTracking|No|
|DirectoryServiceAccess|DirectoryServiceAccess|No|
|LogonLogoff|LogonLogoff|No|
|ObjectAccess|ObjectAccess|No|
|PolicyChange|PolicyChange|No|
|PrivilegeUse|PrivilegeUse|No|
|SystemSecurity|SystemSecurity|No|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Motor|Motor|No|
|Tjänst|Tjänst|No|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|GatewayLogs|Loggar som rör API Management-Gateway|No|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|HttpRequest|HTTP-begäranden|Yes|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/våren

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ApplicationConsole|Program konsol|No|
|SystemLogs|System loggar|No|


## <a name="microsoftattestationattestationproviders"></a>Microsoft. attestering/attestationProviders

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AuditEvent|AuditEvent meddelande logg kategori.|No|
|FELET|Logg kategori för fel meddelande.|No|
|INF|Informations meddelandets loggnings kategori.|No|
|VARN|Logg kategori för varnings meddelande.|No|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DscNodeStatus|DSC-nods status|No|
|JobLogs|Jobb loggar|No|
|JobStreams|Jobb strömmar|No|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ServiceLog|Tjänst loggar|No|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|No|
|BaiClusterNodeEvent|BaiClusterNodeEvent|No|
|BaiJobEvent|BaiJobEvent|No|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BlockchainApplication|Blockchain-program|No|
|FabricOrderer|Fabric-beställare|No|
|FabricPeer|Infrastruktur resurs|No|
|Proxy|Proxy|No|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. blockchain/cordaMembers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BlockchainApplication|Blockchain-program|No|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BotRequest|Begär Anden från kanaler till bot|No|
|DependencyRequest|Begär anden till beroenden|No|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|WebApplicationFirewallLogs|Brand Väggs loggar för webb webbappen|No|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profiler

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AzureCdnAccessLog|Azure CDN Access-logg|No|
|FrontDoorAccessLog|Ytterdörr åtkomst logg|Yes|
|FrontDoorHealthProbeLog|Ytterdörr hälso avsöknings logg|Yes|
|FrontDoorWebApplicationFirewallLog|Ytterdörr WebApplicationFirewall-logg|Yes|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/profiler/slut punkter

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|CoreAnalytics|Hämtar Mät värdena för slut punkten, t. ex. bandbredd, utgående data osv.|No|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Regel flödes händelse för nätverks säkerhets grupp|Regel flödes händelse för nätverks säkerhets grupp|No|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granskningsloggar|No|
|RequestResponse|Förfrågningar och svars loggar|No|
|Spårning|Spårnings loggar|No|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ChatOperational|Fungerande chatt loggar|No|
|SMSOperational|Operativa SMS-loggar|No|
|Användning|Användnings poster|No|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ContainerRegistryLoginEvents|Inloggnings händelser|No|
|ContainerRegistryRepositoryEvents|RepositoryEvent-loggar|No|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|kluster – autoskalning|Kubernetes-kluster autoskalning|No|
|skyddade|skyddade|No|
|kube-apiserver|Kubernetes-API-Server|No|
|Kube – granskning|Kubernetes granskning|No|
|Kube-audit-admin|Kubernetes granska administratörs loggar|No|
|Kube-Controller-Manager|Kubernetes Controller Manager|No|
|Kube – Scheduler|Kubernetes Scheduler|No|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AuditLogs|Gransknings loggar för MiniRP-anrop|No|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft. D365CustomerInsights/instances

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granska händelser|No|
|Operativ|Drift händelser|No|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. Databricks/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|konton|Databricks-konton|No|
|kluster|Databricks-kluster|No|
|dBFS|Databricks-filsystem|No|
|instancePools|Instans-pooler|No|
|utskrifts|Databricks-jobb|No|
|notebook-fil|Databricks-anteckningsbok|No|
|secrets|Databricks hemligheter|No|
|sqlPermissions|Databricks SQLPermissions|No|
|SSH|Databricks SSH|No|
|arbetsyta|Databricks-arbetsyta|No|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. DataCollaboration/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|CollaborationAudit|Granskning av samarbete|Yes|
|DataAssets|Data till gångar|No|
|Pipelines|Pipelines|No|
|Förslag|Förslag|No|
|Skript|Skript|No|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ActivityRuns|Pipeline-aktivitet kör logg|No|
|PipelineRuns|Pipeline kör logg|No|
|SSISIntegrationRuntimeLogs|SSIS för integration runtime|No|
|SSISPackageEventMessageContext|Händelse meddelande kontext för SSIS-paket|No|
|SSISPackageEventMessages|Händelse meddelanden för SSIS-paket|No|
|SSISPackageExecutableStatistics|SSIS-paketets körbara statistik|No|
|SSISPackageExecutionComponentPhases|Steg för körnings komponent för SSIS-paket|No|
|SSISPackageExecutionDataStatistics|SSIS-paket exeution data statistik|No|
|TriggerRuns|Utlös körnings logg|No|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granskningsloggar|No|
|Begäranden|Begär ande loggar|No|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granskningsloggar|No|
|Begäranden|Begär ande loggar|No|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ReceivedShareSnapshots|Mottagna resurs ögonblicks bilder|No|
|SentShareSnapshots|Ögonblicks bilder av skickade resurser|No|
|Resurser|Resurser|No|
|ShareSubscriptions|Dela prenumerationer|No|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|MySqlAuditLogs|Gransknings loggar för MariaDB|No|
|MySqlSlowLogs|MariaDB Server-loggar|No|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|MySqlAuditLogs|MySQL gransknings loggar|No|
|MySqlSlowLogs|Långsamma loggfiler för MySQL|No|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|MySqlAuditLogs|MySQL gransknings loggar|No|
|MySqlSlowLogs|MySQL server-loggar|No|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|No|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|No|
|QueryStoreRuntimeStatistics|PostgreSQL för fråge lagrings körning|No|
|QueryStoreWaitStatistics|Väntande statistik för PostgreSQL Query Store|No|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|No|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Checkpoint|Checkpoint|No|
|Fel|Fel|No|
|Hantering|Hantering|No|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|No|
|Checkpoint|Checkpoint|No|
|Anslutning|Anslutning|No|
|Fel|Fel|No|
|HostRegistration|HostRegistration|No|
|Hantering|Hantering|No|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Checkpoint|Checkpoint|No|
|Fel|Fel|No|
|Feed|Feed|No|
|Hantering|Hantering|No|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/ElasticPools/IotHubTenants

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|C2DCommands|C2D-kommandon|No|
|C2DTwinOperations|C2D dubbla åtgärder|No|
|Konfigurationer|Konfigurationer|No|
|Anslutningar|Anslutningar|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Enhets identitets åtgärder|No|
|DeviceStreams|Enhets strömmar (förhands granskning)|No|
|DeviceTelemetry|Enhetstelemetri|No|
|DirectMethods|Direkta metoder|No|
|DistributedTracing|Distribuerad spårning (för hands version)|No|
|FileUploadOperations|Fil överförings åtgärder|No|
|JobsOperations|Jobb åtgärder|No|
|Vägar|Vägar|No|
|TwinQueries|Dubbla frågor|No|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|C2DCommands|C2D-kommandon|No|
|C2DTwinOperations|C2D dubbla åtgärder|No|
|Konfigurationer|Konfigurationer|No|
|Anslutningar|Anslutningar|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Enhets identitets åtgärder|No|
|DeviceStreams|Enhets strömmar (förhands granskning)|No|
|DeviceTelemetry|Enhetstelemetri|No|
|DirectMethods|Direkta metoder|No|
|DistributedTracing|Distribuerad spårning (för hands version)|No|
|FileUploadOperations|Fil överförings åtgärder|No|
|JobsOperations|Jobb åtgärder|No|
|Vägar|Vägar|No|
|TwinQueries|Dubbla frågor|No|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeviceOperations|Enhets åtgärder|No|
|ServiceOperations|Tjänst åtgärder|No|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|No|
|EventRoutesOperation|EventRoutesOperation|No|
|ModelsOperation|ModelsOperation|No|
|QueryOperation|QueryOperation|No|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|CassandraRequests|CassandraRequests|No|
|ControlPlaneRequests|ControlPlaneRequests|No|
|DataPlaneRequests|DataPlaneRequests|No|
|GremlinRequests|GremlinRequests|No|
|MongoRequests|MongoRequests|No|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|No|
|PartitionKeyStatistics|PartitionKeyStatistics|No|
|QueryRuntimeStatistics|QueryRuntimeStatistics|No|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeliveryFailures|Loggar för leverans problem|No|
|PublishFailures|Publicera Felaktiga loggar|No|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeliveryFailures|Loggar för leverans problem|No|
|PublishFailures|Publicera Felaktiga loggar|No|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeliveryFailures|Loggar för leverans problem|No|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeliveryFailures|Loggar för leverans problem|No|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeliveryFailures|Loggar för leverans problem|No|
|PublishFailures|Publicera Felaktiga loggar|No|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/Namespaces

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ArchiveLogs|Arkiv loggar|No|
|AutoScaleLogs|Automatisk skalnings loggar|No|
|CustomerManagedKeyUserLogs|Kund hanterade nyckel loggar|No|
|EventHubVNetConnectionEvent|Anslutnings loggar för VNet/IP-filtrering|No|
|KafkaCoordinatorLogs|Kafka koordinator loggar|No|
|KafkaUserErrorLogs|Kafka användar fel loggar|No|
|OperationalLogs|Drift loggar|No|


## <a name="microsoftexperimentationexperimentworkspaces"></a>Microsoft. experiment/experimentWorkspaces

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Förfrågan|Förfrågan|No|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/Services

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AuditLogs|Granskningsloggar|No|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/autoscalesettings

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AutoscaleEvaluations|Autoskala-utvärderingar|No|
|AutoscaleScaleActions|Åtgärder för autoskalning av skala|No|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AppAvailabilityResults|Tillgänglighets resultat|No|
|AppBrowserTimings|Tids inställningar för webbläsare|No|
|AppDependencies|Beroenden|No|
|AppEvents|Händelser|No|
|AppExceptions|Undantag|No|
|AppMetrics|Mått|No|
|AppPageViews|Sid visningar|No|
|AppPerformanceCounters|Prestandaräknare|No|
|AppRequests|Begäranden|No|
|AppSystemEvents|System händelser|No|
|AppTraces|Spårningar|No|


## <a name="microsoftiotspacesgraph"></a>Microsoft. IoTSpaces/Graph

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granska|No|
|Utgående|Utgående|No|
|Ingress|Ingress|No|
|Operativ|Operativ|No|
|Spårning|Spårning|No|
|UserDefinedFunction|UserDefinedFunction|No|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. nyckel valv/managedhsms

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AuditEvent|Granska händelse|No|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. nyckel valv/-valv

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AuditEvent|Granskningsloggar|No|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Kommando|Kommando|No|
|FailedIngestion|Misslyckade inmatnings åtgärder|No|
|IngestionBatching|Inmatningsbatchning|No|
|Söka i data|Söka i data|No|
|SucceededIngestion|Framgångs åtgärder|No|
|TableDetails|Tabell information|No|
|TableUsageStatistics|Statistik över Tabell användning|No|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|IntegrationAccountTrackingEvents|Spårnings händelser för integrations konto|No|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/arbets flöden

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|WorkflowRuntime|Diagnostiska händelser för arbets flödes körning|No|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|No|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|No|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|No|
|AmlComputeJobEvent|AmlComputeJobEvent|No|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|No|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Media Services

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|KeyDeliveryRequests|Begär Anden om nyckel leverans|No|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ApplicationGatewayAccessLog|Application Gateway åtkomst logg|No|
|ApplicationGatewayFirewallLog|Application Gateway brand Väggs logg|No|
|ApplicationGatewayPerformanceLog|Application Gateway prestanda logg|No|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AzureFirewallApplicationRule|Regel för Azure brand Väggs program|No|
|AzureFirewallDnsProxy|Azure Firewall DNS-proxy|No|
|AzureFirewallNetworkRule|Nätverks regel för Azure Firewall|No|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BastionAuditLogs|Gransknings loggar för skydds|No|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|PeeringRouteLog|Tabell loggar för peering-routning|No|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|FrontdoorAccessLog|Ytterdörr åtkomst logg|No|
|FrontdoorWebApplicationFirewallLog|Ytterdörr webb program brand Väggs logg|No|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/belastningsutjämnare

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|LoadBalancerAlertEvent|Load Balancer aviserings händelser|No|
|LoadBalancerProbeHealthStatus|Load Balancer avsökningens hälso status|No|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|NetworkSecurityGroupEvent|Händelse för nätverks säkerhets grupp|No|
|NetworkSecurityGroupFlowEvent|Regel flödes händelse för nätverks säkerhets grupp|No|
|NetworkSecurityGroupRuleCounter|Regel räknare för nätverks säkerhets grupp|No|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|GatewayDiagnosticLog|Gateway-diagnostikloggar|No|
|IKEDiagnosticLog|IKE-diagnostikloggar|No|
|P2SDiagnosticLog|P2S diagnostikloggar|No|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DDoSMitigationFlowLogs|Flödes loggar för DDoSa beslut om minskning|No|
|DDoSMitigationReports|Rapporter för DDoS-åtgärder|No|
|DDoSProtectionNotifications|DDoS skydds meddelanden|No|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ProbeHealthStatusEvents|Traffic Manager händelse av hälso resultat för avsökning|No|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|GatewayDiagnosticLog|Gateway-diagnostikloggar|No|
|IKEDiagnosticLog|IKE-diagnostikloggar|No|
|P2SDiagnosticLog|P2S diagnostikloggar|No|
|RouteDiagnosticLog|Vidarebefordra diagnostikloggar|No|
|TunnelDiagnosticLog|Tunnel diagnostikloggar|No|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|VMProtectionAlerts|Aviseringar för VM-skydd|No|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|GatewayDiagnosticLog|Gateway-diagnostikloggar|No|
|IKEDiagnosticLog|IKE-diagnostikloggar|No|
|RouteDiagnosticLog|Vidarebefordra diagnostikloggar|No|
|TunnelDiagnosticLog|Tunnel diagnostikloggar|No|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft. NotificationHubs/namnrymder

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|OperationalLogs|Drift loggar|No|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granskningsloggar|No|


## <a name="microsoftpowerbitenants"></a>Microsoft. PowerBI/klient organisationer

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Motor|Motor|No|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft. PowerBI/klient organisationer/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Motor|Motor|No|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Motor|Motor|No|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftpurviewaccounts"></a>Microsoft. avdelningens kontroll/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. RecoveryServices/valv

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AddonAzureBackupAlerts|Aviserings data för addon Azure Backup|No|
|AddonAzureBackupJobs|Jobb data för addon Azure Backup|No|
|AddonAzureBackupPolicy|Princip data för addon Azure Backup|No|
|AddonAzureBackupProtectedInstance|Addon Azure Backup skyddade instans data|No|
|AddonAzureBackupStorage|Tillägg för Azure Backup lagrings data|No|
|AzureBackupReport|Azure Backup rapporterings data|No|
|AzureSiteRecoveryEvents|Azure Site Recovery händelser|No|
|AzureSiteRecoveryJobs|Azure Site Recovery jobb|No|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery skyddad disk data omsättning|No|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery återställnings punkter|No|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikerade objekt|No|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery överförings hastighet för replikeringsdata|No|
|AzureSiteRecoveryReplicationStats|Statistik för Azure Site Recovery-replikering|No|
|CoreAzureBackup|Kärn Azure Backup data|No|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namnrymder

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|HybridConnectionsEvent|HybridConnections-händelser|No|
|HybridConnectionsLogs|HybridConnectionsLogs|No|


## <a name="microsoftsearchsearchservices"></a>Microsoft. search/searchServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|OperationLogs|Åtgärds loggar|No|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. Service Bus/namnrymder

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|OperationalLogs|Drift loggar|No|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AllLogs|Azure SignalR service-loggar.|No|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DevOpsOperationsAudit|Gransknings loggar för DevOps-åtgärder|No|
|ResourceUsageStats|Statistik för resursanvändning|No|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|No|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/-databaser

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Fel|Fel|No|
|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|No|
|QueryStoreWaitStatistics|Väntande statistik för Query Store|No|
|SQLInsights|SQL Insights|No|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/databaser

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AutomaticTuning|Automatisk inställning|No|
|Delar|Delar|No|
|DatabaseWaitStatistics|Väntande statistik över databasen|No|
|Dödlägen|Dödlägen|No|
|DevOpsOperationsAudit|Gransknings loggar för DevOps-åtgärder|No|
|DmsWorkers|DMS-arbetare|No|
|Fel|Fel|No|
|ExecRequests|Exec-begäranden|No|
|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|No|
|QueryStoreWaitStatistics|Väntande statistik för Query Store|No|
|RequestSteps|Förfrågnings steg|No|
|SQLInsights|SQL Insights|No|
|SqlRequests|SQL-begäranden|No|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|No|
|Timeouter|Timeouter|No|
|Väntar|Väntar|No|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Redigering|Redigering|No|
|Körnings-|Körnings-|No|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BuiltinSqlReqsEnded|De inbyggda SQL-pool-begärandena avslutas|No|
|GatewayApiRequests|API-begäranden för Synapse Gateway|No|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|No|
|SynapseRbacOperations|Synapse RBAC-åtgärder|No|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/arbets ytor/bigDataPools

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BigDataPoolAppsEnded|Big data pool program har slutförts|No|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/arbets ytor/sqlPools

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DmsWorkers|DMS-arbetare|No|
|ExecRequests|Exec-begäranden|No|
|RequestSteps|Förfrågnings steg|No|
|SqlRequests|SQL-begäranden|No|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|No|
|Väntar|Väntar|No|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/miljöer

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Ingress|Ingress|No|
|Hantering|Hantering|No|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/miljöer/eventsources

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Ingress|Ingress|No|
|Hantering|Hantering|No|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|App Service-miljön plattforms loggar|No|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Rapportera Antivirus gransknings loggar|No|
|AppServiceAppLogs|App Service program loggar|No|
|AppServiceAuditLogs|Åtkomst gransknings loggar|No|
|AppServiceConsoleLogs|App Service konsol loggar|No|
|AppServiceFileAuditLogs|Gransknings loggar för ändring av webbplats innehåll|No|
|AppServiceHTTPLogs|HTTP-loggar|No|
|AppServiceIPSecAuditLogs|IPSecurity gransknings loggar|No|
|AppServicePlatformLogs|App Service plattforms loggar|No|
|FunctionAppLogs|Funktions program loggar|No|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/lotss

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Rapportera Antivirus gransknings loggar|No|
|AppServiceAppLogs|App Service program loggar|No|
|AppServiceAuditLogs|Åtkomst gransknings loggar|No|
|AppServiceConsoleLogs|App Service konsol loggar|No|
|AppServiceFileAuditLogs|Gransknings loggar för ändring av webbplats innehåll|No|
|AppServiceHTTPLogs|HTTP-loggar|No|
|AppServiceIPSecAuditLogs|IPSecurity gransknings loggar|No|
|AppServicePlatformLogs|App Service plattforms loggar|No|
|FunctionAppLogs|Funktions program loggar|No|



## <a name="next-steps"></a>Nästa steg

* [Läs mer om resurs loggar](../essentials/platform-logs-overview.md)
* [Strömma resurs resurs loggar till **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Ändra diagnostikinställningar för resurs loggen med hjälp av Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Analysera loggar från Azure Storage med Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

