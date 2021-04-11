---
title: Resurser som stöds för mått varningar i Azure Monitor
description: Referens för support mått och loggar för mått varningar i Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 24298734a46b8339a2a8818692641b4c10812294
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104889"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Resurser som stöds för mått varningar i Azure Monitor

Azure Monitor har nu stöd för en [ny mått varnings typ](./alerts-overview.md) som har betydande fördelar jämfört med äldre [klassiska mått varningar](./alerts-classic.overview.md). Mått är tillgängliga för [stor lista över Azure-tjänster](../essentials/metrics-supported.md). De nya aviseringarna stöder en (växande) del av resurs typerna. Den här artikeln visar en delmängd.

Du kan också använda nya mått varningar på populära loggdata som lagras i en Log Analytics arbets yta som har extraherats som mått. Om du vill ha mer information kan du Visa [mått aviseringar för loggar](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, REST-support
För närvarande kan du bara skapa nya mått varningar i Azure Portal-, [REST API](/rest/api/monitor/metricalerts/)-eller [Resource Manager-mallar](./alerts-metric-create-templates.md). Stöd för att konfigurera nya aviseringar med PowerShell och Azure CLI version 2,0 och senare kommer snart.

## <a name="metrics-and-dimensions-supported"></a>Mått och dimensioner som stöds
Nya mått aviseringar stöder aviseringar för mått som använder dimensioner. Du kan använda dimensioner för att filtrera måttet till rätt nivå. Alla mått som stöds tillsammans med tillämpliga dimensioner kan utforskas och visualiseras från [Azure Monitor-Metrics Explorer](../essentials/metrics-charts.md).

Här är en fullständig lista över Azure Monitor mått källor som stöds av de nyare aviseringarna:

|Resurstyp  |Dimensioner som stöds |Aviseringar med flera resurser| Tillgängliga mått|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Ja | Inga | [Azure AD](../essentials/metrics-supported.md#microsoftaadiamazureadmetrics) |
|Microsoft.ApiManagement/service | Ja | Inga | [API Management](../essentials/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Ja | Inga | [App Configuration](../essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/våren | Ja | Inga | [Azure Spring Cloud](../essentials/metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Ja| Inga | [Automation-konton](../essentials/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | Inga | Inga | [Azure VMware Solution](../essentials/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.BatCH/batchAccounts | Ja | Inga | [Batch-konton](../essentials/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. BotService/botServices | Ja | Inga | [Robot tjänster](../essentials/metrics-supported.md#microsoftbotservicebotservices) |
|Microsoft. cache/Redis | Ja | Ja | [Azure Cache for Redis](../essentials/metrics-supported.md#microsoftcacheredis) |
|Utforskaren. CDN/profiler | Ja | Inga | [CDN-profiler](../essentials/metrics-supported.md#microsoftcdnprofiles) |
|Microsoft. ClassicCompute/domän namn/platser/roller | Inga | Inga | [Klassisk Cloud Services](../essentials/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | Inga | Inga | [Klassisk Virtual Machines](../essentials/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Ja | Inga | [Lagrings konton (klassiska)](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Ja | Inga | [Lagrings konton (klassiska)-blobbar](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft. ClassicStorage/storageAccounts/fileServices | Ja | Inga | [Lagrings konton (klassiska) – filer](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Ja | Inga | [Lagrings konton (klassiska) – köer](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Ja | Inga | [Lagrings konton (klassiska) – tabeller](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft. CognitiveServices/konton | Ja | Inga | [Cognitive Services](../essentials/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft. Compute/cloudServices | Ja | Inga |  [Molntjänster](../essentials/metrics-supported.md#microsoftcomputecloudservices) |
|Microsoft. Compute/cloudServices/roles | Ja | Inga |  [Moln tjänst roller](../essentials/metrics-supported.md#microsoftcomputecloudservicesroles) |
|Microsoft.Compute/virtualMachines | Ja | Ja<sup>1</sup> | [Virtual Machines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Ja | Inga |[Virtual Machine Scale Sets](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Ja| Inga | [Behållar grupper](../essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/register | Inga | Inga | [Behållar register](../essentials/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. container service/managedClusters | Ja | Inga | [Hanterade kluster](../essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | [Data Box](../essentials/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/datafactories| Ja| Inga | [Data fabriker v1](../essentials/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/fabriker |Ja | Inga | [Data fabriker v2](../essentials/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. DataShare/konton | Ja | Inga | [Data resurser](../essentials/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft. DBforMariaDB/servers | Inga | Inga | [DATABAS för MariaDB](../essentials/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/servers | Inga | Inga |[DB för MySQL](../essentials/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/flexibleServers | Ja | Inga | [DB för PostgreSQL (flexibla servrar)](../essentials/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. DBforPostgreSQL/serverGroupsv2 | Ja | Inga | DB för PostgreSQL (skala) |
|Microsoft. DBforPostgreSQL/servers | Inga | Inga | [DATABAS för PostgreSQL](../essentials/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | Inga | Inga | [DB för PostgreSQL v2](../essentials/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. Devices/IotHubs | Ja | Inga |[IoT Hub](../essentials/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Ja | Inga | [Enhets etablerings tjänster](../essentials/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Ja | Inga | [Digital Twins](../essentials/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Ja | Inga | [Cosmos DB](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/Domains | Ja | Inga | [Event Grid-domäner](../essentials/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Ja | Inga | [Avsnitt om Event Grid system](../essentials/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/ämnen |Ja | Inga | [Event Grid-ämnen](../essentials/metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/Clusters |Ja| Inga | [Event Hubs kluster](../essentials/metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/Namespaces |Ja| Inga | [Event Hubs](../essentials/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/kluster | Ja | Inga | [HDInsight-kluster](../essentials/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. Insights/komponenter | Ja | Inga | [Application Insights](../essentials/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft. nyckel valv/-valv | Ja |Ja |[Valv](../essentials/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/kluster | Ja |Inga |[Datautforskaren kluster](../essentials/metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Ja | Inga |[Integrerings tjänst miljöer](../essentials/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/arbets flöden | Inga | Inga |[Logic Apps](../essentials/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/arbets ytor | Ja | Inga | [Machine Learning](../essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. MachineLearningServices/arbets ytor/onlineEndpoints | Ja | Inga | Machine Learning-slutpunkter |
|Microsoft. MachineLearningServices/arbets ytor/onlineEndpoints/distributioner | Ja | Inga | Distributioner av Machine Learning-slutpunkt |
|Microsoft. Maps/konton | Ja | Inga | [Mappar konton](../essentials/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/Media Services | Inga | Inga | [Media Services](../essentials/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/Media Services/strömnings slut punkter | Ja | Inga | [Media Services slut punkter för direkt uppspelning](../essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Ja | Ja | [Pool för Azure NetApp-kapacitet](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/Volumes | Ja | Ja | [Azure NetApp-volymer](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Ja | Inga | [Programgateways](../essentials/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Ja | Inga | [Brandväggar](../essentials/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | Inga | Inga | [DNS-zoner](../essentials/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | Ja | Inga |[ExpressRoute-kretsar](../essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/expressRoutePorts | Ja | Inga |[ExpressRoute Direct](../essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft. Network/belastningsutjämnare (endast för standard-SKU: er)| Ja| Inga | [Belastnings utjämning](../essentials/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Inga | Inga | [NAT-gatewayer](../essentials/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| Inga | Inga | [Privata slut punkter](../essentials/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft. Network/privateLinkServices| Inga | Inga | [Privata länk tjänster](../essentials/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft. Network/publicipaddresses | Inga | Inga | [offentliga IP-adresser](../essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Ja | Inga | [Traffic Manager-profiler](../essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/arbets ytor| Ja | Inga | [Log Analytics-arbetsytor](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. peering/peering | Ja | Inga | [Peering-sessioner](../essentials/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. peering/peeringServices | Ja | Inga | [Peering-tjänster](../essentials/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/kapacitet | Inga | Inga | [Kapacitet](../essentials/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/namnrymder | Ja | Inga | [Reläer](../essentials/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. search/searchServices | Inga | Inga | [Sök tjänster](../essentials/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. Service Bus/namnrymder | Ja | Inga | [Service Bus](../essentials/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | Inga | Ja | [SQL-hanterade instanser](../essentials/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/Servers/databaser | Inga | Ja | [SQL-databaser](../essentials/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/Servers/elasticPools | Inga | Ja | [Elastiska SQL-pooler](../essentials/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Ja | Inga | [Lagringskonton](../essentials/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/blobServices | Ja| Inga | [Lagrings konton – blobbar](../essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft. Storage/storageAccounts/fileServices | Ja| Inga | [Lagrings konton – filer](../essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft. Storage/storageAccounts/queueServices | Ja| Inga | [Lagrings konton – köer](../essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft. Storage/storageAccounts/tableServices | Ja| Inga | [Lagrings konton – tabeller](../essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft. StorageCache/cacheminnen | Ja | Inga | [HPC-cacheminnen](../essentials/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Ja | Inga | [Tjänster för synkronisering av lagring](../essentials/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Ja | Inga | [Stream Analytics](../essentials/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. Synapse/arbets ytor | Ja | Inga | [Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. Synapse/arbets ytor/bigDataPools | Ja | Inga | [Synapse-analys Apache Spark pooler](../essentials/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. Synapse/arbets ytor/sqlPools | Ja | Inga | [SQL-pooler för Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Ja | Inga | [Virtuella CloudSimple-datorer](../essentials/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Ja | Inga | [App Service-miljön pooler med flera roller](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Ja | Inga | [App Service-miljön Worker-pooler](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/Server grupper | Ja | Inga | [App Services planer](../essentials/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/Sites | Ja | Inga | [App Services och funktioner](../essentials/metrics-supported.md#microsoftwebsites)|
|Microsoft. Web/Sites/lotss | Ja | Inga | [App Service platser](../essentials/metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> stöds inte för nätverks mått för virtuella datorer (totalt nätverk, totalt antal nätverk, inkommande flöden, utgående flöden, inkommande flöden, högsta skapande frekvens, utgående flöden, högsta skapande frekvens) och anpassade mått.

## <a name="payload-schema"></a>Nytto Last schema

> [!NOTE]
> Du kan också använda det [vanliga aviserings schemat](./alerts-common-schema.md), vilket ger fördelarna med att ha en enda utöknings bar och enhetlig aviserings nytto Last i alla aviserings tjänster i Azure Monitor, för dina webhook-integrationer. [Lär dig mer om vanliga aviserings schema definitioner.](./alerts-common-schema-definitions.md)


POST-åtgärden innehåller följande JSON-nyttolast och schema för alla nära nyare mått-varningar när en lämplig konfigurerad [Åtgärds grupp](./action-groups.md) används:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om den nya [aviserings upplevelsen](./alerts-overview.md).
* Lär dig mer om [logg aviseringar i Azure](./alerts-unified-log.md).
* Lär dig mer om [aviseringar i Azure](./alerts-overview.md).
