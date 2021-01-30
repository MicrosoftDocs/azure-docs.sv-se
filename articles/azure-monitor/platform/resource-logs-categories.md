---
title: Azure Monitor resurs loggar som stöds tjänster och kategorier
description: Referens för Azure Monitor förstå tjänster och händelse scheman som stöds för Azures resurs loggar.
ms.subservice: logs
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 02488e1a3ff26acf9ff318a2d5c09115aaba8df9
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070754"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Kategorier som stöds för Azure-resurs loggar

> [!NOTE]
> Resurs loggar kallades tidigare för diagnostikloggar. Namnet ändrades i oktober 2019 när de typer av loggar som samlats in av Azure Monitor flyttas för att inkludera mer än bara Azure-resursen.

[Azure Monitor resurs loggar](./platform-logs-overview.md) genereras av Azure-tjänster som beskriver driften av dessa tjänster eller resurser. Alla resurs loggar som är tillgängliga via Azure Monitor dela ett gemensamt schema på högsta nivå, med flexibilitet för varje tjänst för att generera unika egenskaper för sina egna händelser.

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
|AccountLogon|AccountLogon|Inga|
|AccountManagement|AccountManagement|Inga|
|DetailTracking|DetailTracking|Inga|
|DirectoryServiceAccess|DirectoryServiceAccess|Inga|
|LogonLogoff|LogonLogoff|Inga|
|ObjectAccess|ObjectAccess|Inga|
|PolicyChange|PolicyChange|Inga|
|PrivilegeUse|PrivilegeUse|Inga|
|SystemSecurity|SystemSecurity|Inga|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Motor|Motor|Inga|
|Tjänst|Tjänst|Inga|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|GatewayLogs|Loggar som rör API Management-Gateway|Inga|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|HttpRequest|HTTP-begäranden|Ja|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/våren

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ApplicationConsole|Program konsol|Inga|
|SystemLogs|System loggar|Inga|


## <a name="microsoftattestationattestationproviders"></a>Microsoft. attestering/attestationProviders

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AuditEvent|AuditEvent meddelande logg kategori.|Inga|
|FELET|Logg kategori för fel meddelande.|Inga|
|INF|Informations meddelandets loggnings kategori.|Inga|
|VARN|Logg kategori för varnings meddelande.|Inga|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DscNodeStatus|DSC-nods status|Inga|
|JobLogs|Jobb loggar|Inga|
|JobStreams|Jobb strömmar|Inga|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ServiceLog|Tjänst loggar|Inga|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|Inga|
|BaiClusterNodeEvent|BaiClusterNodeEvent|Inga|
|BaiJobEvent|BaiJobEvent|Inga|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BlockchainApplication|Blockchain-program|Inga|
|FabricOrderer|Fabric-beställare|Inga|
|FabricPeer|Infrastruktur resurs|Inga|
|Proxy|Proxy|Inga|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. blockchain/cordaMembers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BlockchainApplication|Blockchain-program|Inga|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BotRequest|Begär Anden från kanaler till bot|Inga|
|DependencyRequest|Begär anden till beroenden|Inga|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|WebApplicationFirewallLogs|Brand Väggs loggar för webb webbappen|Inga|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profiler

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AzureCdnAccessLog|Azure CDN Access-logg|Inga|
|FrontDoorAccessLog|Ytterdörr åtkomst logg|Ja|
|FrontDoorHealthProbeLog|Ytterdörr hälso avsöknings logg|Ja|
|FrontDoorWebApplicationFirewallLog|Ytterdörr WebApplicationFirewall-logg|Ja|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/profiler/slut punkter

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|CoreAnalytics|Hämtar Mät värdena för slut punkten, t. ex. bandbredd, utgående data osv.|Inga|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Regel flödes händelse för nätverks säkerhets grupp|Regel flödes händelse för nätverks säkerhets grupp|Inga|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granskningsloggar|Inga|
|RequestResponse|Förfrågningar och svars loggar|Inga|
|Spårning|Spårnings loggar|Inga|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ChatOperational|Fungerande chatt loggar|Inga|
|SMSOperational|Operativa SMS-loggar|Inga|
|Användning|Användnings poster|Inga|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ContainerRegistryLoginEvents|Inloggnings händelser|Inga|
|ContainerRegistryRepositoryEvents|RepositoryEvent-loggar|Inga|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|kluster – autoskalning|Kubernetes-kluster autoskalning|Inga|
|skyddade|skyddade|Inga|
|kube-apiserver|Kubernetes-API-Server|Inga|
|Kube – granskning|Kubernetes granskning|Inga|
|Kube-audit-admin|Kubernetes granska administratörs loggar|Inga|
|Kube-Controller-Manager|Kubernetes Controller Manager|Inga|
|Kube – Scheduler|Kubernetes Scheduler|Inga|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AuditLogs|Gransknings loggar för MiniRP-anrop|Inga|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft. D365CustomerInsights/instances

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granska händelser|Inga|
|Operativ|Drift händelser|Inga|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. Databricks/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|konton|Databricks-konton|Inga|
|kluster|Databricks-kluster|Inga|
|dBFS|Databricks-filsystem|Inga|
|instancePools|Instans-pooler|Inga|
|utskrifts|Databricks-jobb|Inga|
|notebook-fil|Databricks-anteckningsbok|Inga|
|secrets|Databricks hemligheter|Inga|
|sqlPermissions|Databricks SQLPermissions|Inga|
|SSH|Databricks SSH|Inga|
|arbetsyta|Databricks-arbetsyta|Inga|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. DataCollaboration/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|CollaborationAudit|Granskning av samarbete|Ja|
|DataAssets|Data till gångar|Inga|
|Pipelines|Pipelines|Inga|
|Förslag|Förslag|Inga|
|Skript|Skript|Inga|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ActivityRuns|Pipeline-aktivitet kör logg|Inga|
|PipelineRuns|Pipeline kör logg|Inga|
|SSISIntegrationRuntimeLogs|SSIS för integration runtime|Inga|
|SSISPackageEventMessageContext|Händelse meddelande kontext för SSIS-paket|Inga|
|SSISPackageEventMessages|Händelse meddelanden för SSIS-paket|Inga|
|SSISPackageExecutableStatistics|SSIS-paketets körbara statistik|Inga|
|SSISPackageExecutionComponentPhases|Steg för körnings komponent för SSIS-paket|Inga|
|SSISPackageExecutionDataStatistics|SSIS-paket exeution data statistik|Inga|
|TriggerRuns|Utlös körnings logg|Inga|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granskningsloggar|Inga|
|Begäranden|Begär ande loggar|Inga|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granskningsloggar|Inga|
|Begäranden|Begär ande loggar|Inga|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ReceivedShareSnapshots|Mottagna resurs ögonblicks bilder|Inga|
|SentShareSnapshots|Ögonblicks bilder av skickade resurser|Inga|
|Resurser|Resurser|Inga|
|ShareSubscriptions|Dela prenumerationer|Inga|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|MySqlAuditLogs|Gransknings loggar för MariaDB|Inga|
|MySqlSlowLogs|MariaDB Server-loggar|Inga|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|MySqlAuditLogs|MySQL gransknings loggar|Inga|
|MySqlSlowLogs|Långsamma loggfiler för MySQL|Inga|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|MySqlAuditLogs|MySQL gransknings loggar|Inga|
|MySqlSlowLogs|MySQL server-loggar|Inga|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|Inga|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servers

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|Inga|
|QueryStoreRuntimeStatistics|PostgreSQL för fråge lagrings körning|Inga|
|QueryStoreWaitStatistics|Väntande statistik för PostgreSQL Query Store|Inga|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|Inga|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Checkpoint|Checkpoint|Inga|
|Fel|Fel|Inga|
|Hantering|Hantering|Inga|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|Inga|
|Checkpoint|Checkpoint|Inga|
|Anslutning|Anslutning|Inga|
|Fel|Fel|Inga|
|HostRegistration|HostRegistration|Inga|
|Hantering|Hantering|Inga|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Checkpoint|Checkpoint|Inga|
|Fel|Fel|Inga|
|Feed|Feed|Inga|
|Hantering|Hantering|Inga|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/ElasticPools/IotHubTenants

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|C2DCommands|C2D-kommandon|Inga|
|C2DTwinOperations|C2D dubbla åtgärder|Inga|
|Konfigurationer|Konfigurationer|Inga|
|Anslutningar|Anslutningar|Inga|
|D2CTwinOperations|D2CTwinOperations|Inga|
|DeviceIdentityOperations|Enhets identitets åtgärder|Inga|
|DeviceStreams|Enhets strömmar (förhands granskning)|Inga|
|DeviceTelemetry|Enhetstelemetri|Inga|
|DirectMethods|Direkta metoder|Inga|
|DistributedTracing|Distribuerad spårning (för hands version)|Inga|
|FileUploadOperations|Fil överförings åtgärder|Inga|
|JobsOperations|Jobb åtgärder|Inga|
|Vägar|Vägar|Inga|
|TwinQueries|Dubbla frågor|Inga|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|C2DCommands|C2D-kommandon|Inga|
|C2DTwinOperations|C2D dubbla åtgärder|Inga|
|Konfigurationer|Konfigurationer|Inga|
|Anslutningar|Anslutningar|Inga|
|D2CTwinOperations|D2CTwinOperations|Inga|
|DeviceIdentityOperations|Enhets identitets åtgärder|Inga|
|DeviceStreams|Enhets strömmar (förhands granskning)|Inga|
|DeviceTelemetry|Enhetstelemetri|Inga|
|DirectMethods|Direkta metoder|Inga|
|DistributedTracing|Distribuerad spårning (för hands version)|Inga|
|FileUploadOperations|Fil överförings åtgärder|Inga|
|JobsOperations|Jobb åtgärder|Inga|
|Vägar|Vägar|Inga|
|TwinQueries|Dubbla frågor|Inga|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeviceOperations|Enhets åtgärder|Inga|
|ServiceOperations|Tjänst åtgärder|Inga|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|Inga|
|EventRoutesOperation|EventRoutesOperation|Inga|
|ModelsOperation|ModelsOperation|Inga|
|QueryOperation|QueryOperation|Inga|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|CassandraRequests|CassandraRequests|Inga|
|ControlPlaneRequests|ControlPlaneRequests|Inga|
|DataPlaneRequests|DataPlaneRequests|Inga|
|GremlinRequests|GremlinRequests|Inga|
|MongoRequests|MongoRequests|Inga|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|Inga|
|PartitionKeyStatistics|PartitionKeyStatistics|Inga|
|QueryRuntimeStatistics|QueryRuntimeStatistics|Inga|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeliveryFailures|Loggar för leverans problem|Inga|
|PublishFailures|Publicera Felaktiga loggar|Inga|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeliveryFailures|Loggar för leverans problem|Inga|
|PublishFailures|Publicera Felaktiga loggar|Inga|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeliveryFailures|Loggar för leverans problem|Inga|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeliveryFailures|Loggar för leverans problem|Inga|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DeliveryFailures|Loggar för leverans problem|Inga|
|PublishFailures|Publicera Felaktiga loggar|Inga|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/Namespaces

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ArchiveLogs|Arkiv loggar|Inga|
|AutoScaleLogs|Automatisk skalnings loggar|Inga|
|CustomerManagedKeyUserLogs|Kund hanterade nyckel loggar|Inga|
|EventHubVNetConnectionEvent|Anslutnings loggar för VNet/IP-filtrering|Inga|
|KafkaCoordinatorLogs|Kafka koordinator loggar|Inga|
|KafkaUserErrorLogs|Kafka användar fel loggar|Inga|
|OperationalLogs|Drift loggar|Inga|


## <a name="microsoftexperimentationexperimentworkspaces"></a>Microsoft. experiment/experimentWorkspaces

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Förfrågan|Förfrågan|Inga|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/Services

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AuditLogs|Granskningsloggar|Inga|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/autoscalesettings

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AutoscaleEvaluations|Autoskala-utvärderingar|Inga|
|AutoscaleScaleActions|Åtgärder för autoskalning av skala|Inga|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AppAvailabilityResults|Tillgänglighets resultat|Inga|
|AppBrowserTimings|Tids inställningar för webbläsare|Inga|
|AppDependencies|Beroenden|Inga|
|AppEvents|Händelser|Inga|
|AppExceptions|Undantag|Inga|
|AppMetrics|Mått|Inga|
|AppPageViews|Sid visningar|Inga|
|AppPerformanceCounters|Prestandaräknare|Inga|
|AppRequests|Begäranden|Inga|
|AppSystemEvents|System händelser|Inga|
|AppTraces|Spårningar|Inga|


## <a name="microsoftiotspacesgraph"></a>Microsoft. IoTSpaces/Graph

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granska|Inga|
|Utgående|Utgående|Inga|
|Ingress|Ingress|Inga|
|Operativ|Operativ|Inga|
|Spårning|Spårning|Inga|
|UserDefinedFunction|UserDefinedFunction|Inga|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. nyckel valv/managedhsms

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AuditEvent|Granska händelse|Inga|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. nyckel valv/-valv

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AuditEvent|Granskningsloggar|Inga|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Kommando|Kommando|Inga|
|FailedIngestion|Misslyckade inmatnings åtgärder|Inga|
|IngestionBatching|Inmatningsbatchning|Inga|
|Söka i data|Söka i data|Inga|
|SucceededIngestion|Framgångs åtgärder|Inga|
|TableDetails|Tabell information|Inga|
|TableUsageStatistics|Statistik över Tabell användning|Inga|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|IntegrationAccountTrackingEvents|Spårnings händelser för integrations konto|Inga|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/arbets flöden

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|WorkflowRuntime|Diagnostiska händelser för arbets flödes körning|Inga|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|Inga|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|Inga|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|Inga|
|AmlComputeJobEvent|AmlComputeJobEvent|Inga|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|Inga|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Media Services

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|KeyDeliveryRequests|Begär Anden om nyckel leverans|Inga|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ApplicationGatewayAccessLog|Application Gateway åtkomst logg|Inga|
|ApplicationGatewayFirewallLog|Application Gateway brand Väggs logg|Inga|
|ApplicationGatewayPerformanceLog|Application Gateway prestanda logg|Inga|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AzureFirewallApplicationRule|Regel för Azure brand Väggs program|Inga|
|AzureFirewallDnsProxy|Azure Firewall DNS-proxy|Inga|
|AzureFirewallNetworkRule|Nätverks regel för Azure Firewall|Inga|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BastionAuditLogs|Gransknings loggar för skydds|Inga|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|PeeringRouteLog|Tabell loggar för peering-routning|Inga|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|FrontdoorAccessLog|Ytterdörr åtkomst logg|Inga|
|FrontdoorWebApplicationFirewallLog|Ytterdörr webb program brand Väggs logg|Inga|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/belastningsutjämnare

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|LoadBalancerAlertEvent|Load Balancer aviserings händelser|Inga|
|LoadBalancerProbeHealthStatus|Load Balancer avsökningens hälso status|Inga|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|NetworkSecurityGroupEvent|Händelse för nätverks säkerhets grupp|Inga|
|NetworkSecurityGroupFlowEvent|Regel flödes händelse för nätverks säkerhets grupp|Inga|
|NetworkSecurityGroupRuleCounter|Regel räknare för nätverks säkerhets grupp|Inga|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|GatewayDiagnosticLog|Gateway-diagnostikloggar|Inga|
|IKEDiagnosticLog|IKE-diagnostikloggar|Inga|
|P2SDiagnosticLog|P2S diagnostikloggar|Inga|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DDoSMitigationFlowLogs|Flödes loggar för DDoSa beslut om minskning|Inga|
|DDoSMitigationReports|Rapporter för DDoS-åtgärder|Inga|
|DDoSProtectionNotifications|DDoS skydds meddelanden|Inga|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ProbeHealthStatusEvents|Traffic Manager händelse av hälso resultat för avsökning|Inga|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|GatewayDiagnosticLog|Gateway-diagnostikloggar|Inga|
|IKEDiagnosticLog|IKE-diagnostikloggar|Inga|
|P2SDiagnosticLog|P2S diagnostikloggar|Inga|
|RouteDiagnosticLog|Vidarebefordra diagnostikloggar|Inga|
|TunnelDiagnosticLog|Tunnel diagnostikloggar|Inga|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|VMProtectionAlerts|Aviseringar för VM-skydd|Inga|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|GatewayDiagnosticLog|Gateway-diagnostikloggar|Inga|
|IKEDiagnosticLog|IKE-diagnostikloggar|Inga|
|RouteDiagnosticLog|Vidarebefordra diagnostikloggar|Inga|
|TunnelDiagnosticLog|Tunnel diagnostikloggar|Inga|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft. NotificationHubs/namnrymder

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|OperationalLogs|Drift loggar|Inga|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Granska|Granskningsloggar|Inga|


## <a name="microsoftpowerbitenants"></a>Microsoft. PowerBI/klient organisationer

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Motor|Motor|Inga|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft. PowerBI/klient organisationer/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Motor|Motor|Inga|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Motor|Motor|Inga|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Inga|


## <a name="microsoftpurviewaccounts"></a>Microsoft. avdelningens kontroll/konton

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Inga|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. RecoveryServices/valv

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AddonAzureBackupAlerts|Aviserings data för addon Azure Backup|Inga|
|AddonAzureBackupJobs|Jobb data för addon Azure Backup|Inga|
|AddonAzureBackupPolicy|Princip data för addon Azure Backup|Inga|
|AddonAzureBackupProtectedInstance|Addon Azure Backup skyddade instans data|Inga|
|AddonAzureBackupStorage|Tillägg för Azure Backup lagrings data|Inga|
|AzureBackupReport|Azure Backup rapporterings data|Inga|
|AzureSiteRecoveryEvents|Azure Site Recovery händelser|Inga|
|AzureSiteRecoveryJobs|Azure Site Recovery jobb|Inga|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery skyddad disk data omsättning|Inga|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery återställnings punkter|Inga|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikerade objekt|Inga|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery överförings hastighet för replikeringsdata|Inga|
|AzureSiteRecoveryReplicationStats|Statistik för Azure Site Recovery-replikering|Inga|
|CoreAzureBackup|Kärn Azure Backup data|Inga|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namnrymder

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|HybridConnectionsEvent|HybridConnections-händelser|Inga|
|HybridConnectionsLogs|HybridConnectionsLogs|Inga|


## <a name="microsoftsearchsearchservices"></a>Microsoft. search/searchServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|OperationLogs|Åtgärds loggar|Inga|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. Service Bus/namnrymder

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|OperationalLogs|Drift loggar|Inga|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AllLogs|Azure SignalR service-loggar.|Inga|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DevOpsOperationsAudit|Gransknings loggar för DevOps-åtgärder|Inga|
|ResourceUsageStats|Statistik för resursanvändning|Inga|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|Inga|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/-databaser

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Fel|Fel|Inga|
|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|Inga|
|QueryStoreWaitStatistics|Väntande statistik för Query Store|Inga|
|SQLInsights|SQL Insights|Inga|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/databaser

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AutomaticTuning|Automatisk inställning|Inga|
|Delar|Delar|Inga|
|DatabaseWaitStatistics|Väntande statistik över databasen|Inga|
|Dödlägen|Dödlägen|Inga|
|DevOpsOperationsAudit|Gransknings loggar för DevOps-åtgärder|Inga|
|DmsWorkers|DMS-arbetare|Inga|
|Fel|Fel|Inga|
|ExecRequests|Exec-begäranden|Inga|
|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|Inga|
|QueryStoreWaitStatistics|Väntande statistik för Query Store|Inga|
|RequestSteps|Förfrågnings steg|Inga|
|SQLInsights|SQL Insights|Inga|
|SqlRequests|SQL-begäranden|Inga|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|Inga|
|Timeouter|Timeouter|Inga|
|Väntar|Väntar|Inga|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Redigering|Redigering|Inga|
|Körnings-|Körnings-|Inga|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/arbets ytor

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BuiltinSqlReqsEnded|De inbyggda SQL-pool-begärandena avslutas|Inga|
|GatewayApiRequests|API-begäranden för Synapse Gateway|Inga|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|Inga|
|SynapseRbacOperations|Synapse RBAC-åtgärder|Inga|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/arbets ytor/bigDataPools

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|BigDataPoolAppsEnded|Big data pool program har slutförts|Inga|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/arbets ytor/sqlPools

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|DmsWorkers|DMS-arbetare|Inga|
|ExecRequests|Exec-begäranden|Inga|
|RequestSteps|Förfrågnings steg|Inga|
|SqlRequests|SQL-begäranden|Inga|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|Inga|
|Väntar|Väntar|Inga|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/miljöer

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Ingress|Ingress|Inga|
|Hantering|Hantering|Inga|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/miljöer/eventsources

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|Ingress|Ingress|Inga|
|Hantering|Hantering|Inga|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|App Service-miljön plattforms loggar|Inga|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Rapportera Antivirus gransknings loggar|Inga|
|AppServiceAppLogs|App Service program loggar|Inga|
|AppServiceAuditLogs|Åtkomst gransknings loggar|Inga|
|AppServiceConsoleLogs|App Service konsol loggar|Inga|
|AppServiceFileAuditLogs|Gransknings loggar för ändring av webbplats innehåll|Inga|
|AppServiceHTTPLogs|HTTP-loggar|Inga|
|AppServiceIPSecAuditLogs|IPSecurity gransknings loggar|Inga|
|AppServicePlatformLogs|App Service plattforms loggar|Inga|
|FunctionAppLogs|Funktions program loggar|Inga|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/lotss

|Kategori|Kategori visnings namn|Kostnader för export|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Rapportera Antivirus gransknings loggar|Inga|
|AppServiceAppLogs|App Service program loggar|Inga|
|AppServiceAuditLogs|Åtkomst gransknings loggar|Inga|
|AppServiceConsoleLogs|App Service konsol loggar|Inga|
|AppServiceFileAuditLogs|Gransknings loggar för ändring av webbplats innehåll|Inga|
|AppServiceHTTPLogs|HTTP-loggar|Inga|
|AppServiceIPSecAuditLogs|IPSecurity gransknings loggar|Inga|
|AppServicePlatformLogs|App Service plattforms loggar|Inga|
|FunctionAppLogs|Funktions program loggar|Inga|



## <a name="next-steps"></a>Nästa steg

* [Läs mer om resurs loggar](./platform-logs-overview.md)
* [Strömma resurs resurs loggar till **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Ändra diagnostikinställningar för resurs loggen med hjälp av Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Analysera loggar från Azure Storage med Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

