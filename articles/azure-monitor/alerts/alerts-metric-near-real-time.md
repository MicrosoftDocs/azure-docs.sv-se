---
title: Resurser som stöds för mått varningar i Azure Monitor
description: Referens för support mått och loggar för mått varningar i Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 02/10/2021
ms.subservice: alerts
ms.openlocfilehash: 7b4cdd37cefb628c03129a990d52ed348acd3ac1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100622037"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Resurser som stöds för mått varningar i Azure Monitor

Azure Monitor har nu stöd för en [ny mått varnings typ](../platform/alerts-overview.md) som har betydande fördelar jämfört med äldre [klassiska mått varningar](./alerts-classic.overview.md). Mått är tillgängliga för [stor lista över Azure-tjänster](../platform/metrics-supported.md). De nya aviseringarna stöder en (växande) del av resurs typerna. Den här artikeln visar en delmängd.

Du kan också använda nya mått varningar på populära loggdata som lagras i en Log Analytics arbets yta som har extraherats som mått. Om du vill ha mer information kan du Visa [mått aviseringar för loggar](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, REST-support
För närvarande kan du bara skapa nya mått varningar i Azure Portal-, [REST API](/rest/api/monitor/metricalerts/)-eller [Resource Manager-mallar](./alerts-metric-create-templates.md). Stöd för att konfigurera nya aviseringar med PowerShell och Azure CLI version 2,0 och senare kommer snart.

## <a name="metrics-and-dimensions-supported"></a>Mått och dimensioner som stöds
Nya mått aviseringar stöder aviseringar för mått som använder dimensioner. Du kan använda dimensioner för att filtrera måttet till rätt nivå. Alla mått som stöds tillsammans med tillämpliga dimensioner kan utforskas och visualiseras från [Azure Monitor-Metrics Explorer](../essentials/metrics-charts.md).

Här är en fullständig lista över Azure Monitor mått källor som stöds av de nyare aviseringarna:

|Resurstyp  |Dimensioner som stöds |Aviseringar med flera resurser| Tillgängliga mått|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Ja | Nej | |
|Microsoft.ApiManagement/service | Ja | Nej | [API Management](../platform/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Ja | Nej | [App Configuration](../platform/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/våren | Ja | Nej | [Azure Spring Cloud](../platform/metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Ja| Nej | [Automation-konton](../platform/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | Nej | Nej | [Azure VMware Solution](../platform/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.BatCH/batchAccounts | Ja | Nej | [Batch-konton](../platform/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. cache/Redis | Ja | Ja | [Azure Cache for Redis](../platform/metrics-supported.md#microsoftcacheredis) |
|Microsoft. ClassicCompute/domän namn/platser/roller | Nej | Nej | [Klassisk Cloud Services](../platform/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | Nej | Nej | [Klassisk Virtual Machines](../platform/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Ja | Nej | [Lagrings konton (klassiska)](../platform/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Ja | Nej | [Lagrings konton (klassiska)-blobbar](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft. ClassicStorage/storageAccounts/fileServices | Ja | Nej | [Lagrings konton (klassiska) – filer](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Ja | Nej | [Lagrings konton (klassiska) – köer](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Ja | Nej | [Lagrings konton (klassiska) – tabeller](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft. CognitiveServices/konton | Ja | Nej | [Cognitive Services](../platform/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Yes | Ja<sup>1</sup> | [Virtual Machines](../platform/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Ja | Nej |[Skalnings uppsättningar för virtuella datorer](../platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Ja| Nej | [Containergrupper](../platform/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/register | Nej | Nej | [Behållar register](../platform/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. container service/managedClusters | Ja | Nej | [Hanterade kluster](../platform/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | [Data Box](../platform/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/datafactories| Ja| Nej | [Data fabriker v1](../platform/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/fabriker |Ja | Nej | [Data fabriker v2](../platform/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. DataShare/konton | Ja | Nej | [Data resurser](../platform/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft. DBforMariaDB/servers | Nej | Nej | [DATABAS för MariaDB](../platform/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/servers | Nej | Nej |[DB för MySQL](../platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/servers | Nej | Nej | [DATABAS för PostgreSQL](../platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | Nej | Nej | [DB för PostgreSQL v2](../platform/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. DBforPostgreSQL/flexibleServers | Ja | Nej | [DB för PostgreSQL (flexibla servrar)](../platform/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. Devices/IotHubs | Ja | Nej |[IoT Hub](../platform/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Ja | Nej | [Enhets etablerings tjänster](../platform/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Ja | Nej | [Digital Twins](../platform/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Ja | Nej | [Cosmos DB](../platform/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/Domains | Ja | Nej | [Event Grid-domäner](../platform/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Ja | Nej | [Avsnitt om Event Grid system](../platform/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/ämnen |Ja | Nej | [Event Grid-ämnen](../platform/metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/Clusters |Ja| Nej | [Event Hubs kluster](../platform/metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/Namespaces |Ja| Nej | [Event Hubs](../platform/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/kluster | Ja | Nej | [HDInsight-kluster](../platform/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. Insights/komponenter | Ja | Nej | [Application Insights](../platform/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft. nyckel valv/-valv | Ja |Ja |[Valv](../platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/kluster | Ja |Nej |[Datautforskaren kluster](../platform/metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Ja | Nej |[Integrerings tjänst miljöer](../platform/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/arbets flöden | Nej | Nej |[Logic Apps](../platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/arbets ytor | Ja | Nej | [Machine Learning](../platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. Maps/konton | Ja | Nej | [Mappar konton](../platform/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/Media Services | Nej | Nej | [Media Services](../platform/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/Media Services/strömnings slut punkter | Ja | Nej | [Media Services slut punkter för direkt uppspelning](../platform/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Ja | Ja | [Pool för Azure NetApp-kapacitet](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/Volumes | Ja | Ja | [Azure NetApp-volymer](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Ja | Nej | [Programgatewayer](../platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Ja | Nej | [Brandväggar](../platform/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | Nej | Nej | [DNS-zoner](../platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | Ja | Nej |[ExpressRoute-kretsar](../platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/expressRoutePorts | Ja | Nej |[ExpressRoute Direct](../platform/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft. Network/belastningsutjämnare (endast för standard-SKU: er)| Ja| Nej | [Belastnings utjämning](../platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Nej | Nej | [NAT-gatewayer](../platform/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| Nej | Nej | [Privata slutpunkter](../platform/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft. Network/privateLinkServices| Nej | Nej | [Privata länk tjänster](../platform/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft. Network/publicipaddresses | Nej | Nej | [offentliga IP-adresser](../platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Ja | Nej | [Traffic Manager-profiler](../platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/arbets ytor| Ja | Nej | [Log Analytics-arbetsytor](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. peering/peering | Ja | Nej | [Peering-sessioner](../platform/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. peering/peeringServices | Ja | Nej | [Peering-tjänster](../platform/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/kapacitet | Nej | Nej | [Kapacitet](../platform/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/namnrymder | Ja | Nej | [Reläer](../platform/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. search/searchServices | Nej | Nej | [Sök tjänster](../platform/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. Service Bus/namnrymder | Ja | Nej | [Service Bus](../platform/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | Nej | Ja | [SQL-hanterade instanser](../platform/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/Servers/databaser | Nej | Ja | [SQL-databaser](../platform/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/Servers/elasticPools | Nej | Ja | [Elastiska SQL-pooler](../platform/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Ja | Nej | [Lagringskonton](../platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/blobServices | Ja| Nej | [Lagrings konton – blobbar](../platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft. Storage/storageAccounts/fileServices | Ja| Nej | [Lagrings konton – filer](../platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft. Storage/storageAccounts/queueServices | Ja| Nej | [Lagrings konton – köer](../platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft. Storage/storageAccounts/tableServices | Ja| Nej | [Lagrings konton – tabeller](../platform/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft. StorageCache/cacheminnen | Ja | Nej | [HPC-cacheminnen](../platform/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Ja | Nej | [Tjänster för synkronisering av lagring](../platform/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Ja | Nej | [Stream Analytics](../platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. Synapse/arbets ytor | Ja | Nej | [Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. Synapse/arbets ytor/bigDataPools | Ja | Nej | [Synapse-analys Apache Spark pooler](../platform/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. Synapse/arbets ytor/sqlPools | Ja | Nej | [SQL-pooler för Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Ja | Nej | [Virtuella CloudSimple-datorer](../platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Ja | Nej | [App Service-miljön pooler med flera roller](../platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Ja | Nej | [App Service-miljön Worker-pooler](../platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/Server grupper | Ja | Nej | [App Services planer](../platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/Sites | Ja | Nej | [App Services och funktioner](../platform/metrics-supported.md#microsoftwebsites)|
|Microsoft. Web/Sites/lotss | Ja | Nej | [App Service platser](../platform/metrics-supported.md#microsoftwebsitesslots)|

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

* Lär dig mer om den nya [aviserings upplevelsen](../platform/alerts-overview.md).
* Lär dig mer om [logg aviseringar i Azure](./alerts-unified-log.md).
* Lär dig mer om [aviseringar i Azure](../platform/alerts-overview.md).