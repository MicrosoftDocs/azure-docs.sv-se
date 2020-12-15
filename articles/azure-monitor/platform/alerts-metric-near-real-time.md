---
title: Resurser som stöds för mått varningar i Azure Monitor
description: Referens för support mått och loggar för mått varningar i Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 12/15/2020
ms.subservice: alerts
ms.openlocfilehash: 8f59f3488f6c8f5b35ec68d93db656447f882a92
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510690"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Resurser som stöds för mått varningar i Azure Monitor

Azure Monitor har nu stöd för en [ny mått varnings typ](./alerts-overview.md) som har betydande fördelar jämfört med äldre [klassiska mått varningar](./alerts-classic.overview.md). Mått är tillgängliga för [stor lista över Azure-tjänster](./metrics-supported.md). De nya aviseringarna stöder en (växande) del av resurs typerna. Den här artikeln visar en delmängd.

Du kan också använda nya mått varningar på populära loggdata som lagras i en Log Analytics arbets yta som har extraherats som mått. Om du vill ha mer information kan du Visa [mått aviseringar för loggar](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, REST-support
För närvarande kan du bara skapa nya mått varningar i Azure Portal-, [REST API](/rest/api/monitor/metricalerts/)-eller [Resource Manager-mallar](./alerts-metric-create-templates.md). Stöd för att konfigurera nya aviseringar med PowerShell och Azure CLI version 2,0 och senare kommer snart.

## <a name="metrics-and-dimensions-supported"></a>Mått och dimensioner som stöds
Nya mått aviseringar stöder aviseringar för mått som använder dimensioner. Du kan använda dimensioner för att filtrera måttet till rätt nivå. Alla mått som stöds tillsammans med tillämpliga dimensioner kan utforskas och visualiseras från [Azure Monitor-Metrics Explorer](./metrics-charts.md).

Här är en fullständig lista över Azure Monitor mått källor som stöds av de nyare aviseringarna:

|Resurstyp  |Dimensioner som stöds |Aviseringar med flera resurser| Tillgängliga mått|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Ja | Nej | |
|Microsoft.ApiManagement/service | Ja | Nej | [API Management](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Ja | Nej | [App Configuration](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/våren | Ja | Nej | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Ja| Nej | [Automation-konton](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | Nej | Nej | |
|Microsoft.BatCH/batchAccounts | Ja | Nej | [Batch-konton](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. cache/Redis | Ja | Ja | [Azure Cache for Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft. ClassicCompute/domän namn/platser/roller | Nej | Nej | [Klassisk Cloud Services](./metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | Nej | Nej | [Klassisk Virtual Machines](./metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Ja | Nej | [Lagrings konton (klassiska)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Ja | Nej | [Lagrings konton (klassiska)-blobbar](./metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft. ClassicStorage/storageAccounts/fileServices | Ja | Nej | [Lagrings konton (klassiska) – filer](./metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Ja | Nej | [Lagrings konton (klassiska) – köer](./metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Ja | Nej | [Lagrings konton (klassiska) – tabeller](./metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft. CognitiveServices/konton | Ja | Nej | [Cognitive Services](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Ja | Ja<sup>1</sup> | [Virtual Machines](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Ja | Nej |[Skalnings uppsättningar för virtuella datorer](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Ja| Nej | [Containergrupper](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/register | Nej | Nej | [Behållar register](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. container service/managedClusters | Ja | Nej | [Hanterade kluster](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/datafactories| Ja| Nej | [Data fabriker v1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/fabriker |Ja | Nej | [Data fabriker v2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. DataShare/konton | Ja | Nej | |
|Microsoft. DBforMariaDB/servers | Nej | Nej | [DATABAS för MariaDB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/servers | Nej | Nej |[DB för MySQL](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/servers | Nej | Nej | [DATABAS för PostgreSQL](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | Nej | Nej | [DB för PostgreSQL v2](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. DBforPostgreSQL/flexibleServers | Ja | Nej | [DB för PostgreSQL (flexibla servrar)](./metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. Devices/IotHubs | Ja | Nej |[IoT Hub](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Ja | Nej | [Enhets etablerings tjänster](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Ja | Nej | |
|Microsoft.DocumentDB/databaseAccounts | Ja | Nej | [Cosmos DB](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/Domains | Ja | Nej | [Event Grid-domäner](./metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Ja | Nej | [Avsnitt om Event Grid system](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/ämnen |Ja | Nej | [Event Grid-ämnen](./metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/Clusters |Ja| Nej | [Event Hubs kluster](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/Namespaces |Ja| Nej | [Event Hubs](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/kluster | Ja | Nej | [HDInsight-kluster](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. Insights/komponenter | Ja | Nej | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Microsoft. nyckel valv/-valv | Ja |Ja |[Valv](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/kluster | Ja |Nej |[Datautforskaren kluster](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Ja | Nej |[Integrerings tjänst miljöer](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/arbets flöden | Nej | Nej |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/arbets ytor | Ja | Nej | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. Maps/konton | Ja | Nej | [Mappar konton](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/Media Services | Nej | Nej | [Media Services](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/Media Services/strömnings slut punkter | Ja | Nej | [Media Services slut punkter för direkt uppspelning](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Ja | Ja | [Pool för Azure NetApp-kapacitet](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/Volumes | Ja | Ja | [Azure NetApp-volymer](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Ja | Nej | [Programgatewayer](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Ja | Nej | [Brandväggar](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | Nej | Nej | [DNS-zoner](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | Ja | Nej |[ExpressRoute-kretsar](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/expressRoutePorts | Ja | Nej |[ExpressRoute Direct](./metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft. Network/belastningsutjämnare (endast för standard-SKU: er)| Ja| Nej | [Belastnings utjämning](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Nej | Nej | |
|Microsoft. Network/privateEndpoints| Nej | Nej | |
|Microsoft. Network/privateLinkServices| Nej | Nej |
|Microsoft. Network/publicipaddresses | Nej | Nej |[offentliga IP-adresser](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Ja | Nej | [Traffic Manager-profiler](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/arbets ytor| Ja | Nej | [Log Analytics-arbetsytor](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. peering/peering | Ja | Nej | [Peering-sessioner](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. peering/peeringServices | Ja | Nej | [Peering-tjänster](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/kapacitet | Nej | Nej | [Kapacitet](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/namnrymder | Ja | Nej | [Reläer](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. search/searchServices | Nej | Nej | [Sök tjänster](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. Service Bus/namnrymder | Ja | Nej | [Service Bus](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | Nej | Ja | [SQL-hanterade instanser](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/Servers/databaser | Nej | Ja | [SQL-databaser](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/Servers/elasticPools | Nej | Ja | [Elastiska SQL-pooler](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Ja | Nej | [Lagringskonton](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/blobServices | Ja| Nej | [Lagrings konton – blobbar](./metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft. Storage/storageAccounts/fileServices | Ja| Nej | [Lagrings konton – filer](./metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft. Storage/storageAccounts/queueServices | Ja| Nej | [Lagrings konton – köer](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft. Storage/storageAccounts/tableServices | Ja| Nej | [Lagrings konton – tabeller](./metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft. StorageCache/cacheminnen | Ja | Nej | |
|Microsoft. StorageSync/storageSyncServices | Ja | Nej | [Tjänster för synkronisering av lagring](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Ja | Nej | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. Synapse/arbets ytor | Ja | Nej | [Synapse Analytics](./metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. Synapse/arbets ytor/bigDataPools | Ja | Nej | [Synapse-analys Apache Spark pooler](./metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. Synapse/arbets ytor/sqlPools | Ja | Nej | [SQL-pooler för Synapse Analytics](./metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Ja | Nej | [Virtuella CloudSimple-datorer](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Ja | Nej | [App Service-miljön pooler med flera roller](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Ja | Nej | [App Service-miljön Worker-pooler](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/Server grupper | Ja | Nej | [App Services planer](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/Sites | Ja | Nej | [App Services och funktioner](./metrics-supported.md#microsoftwebsites)|
|Microsoft. Web/Sites/lotss | Ja | Nej | [App Service platser](./metrics-supported.md#microsoftwebsitesslots)|

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
