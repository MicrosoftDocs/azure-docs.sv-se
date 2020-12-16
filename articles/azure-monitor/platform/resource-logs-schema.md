---
title: Tjänster och scheman som stöds av Azure-resurs loggar
description: Förstå tjänster och händelse schema som stöds för Azures resurs loggar.
ms.subservice: logs
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: c1c5ed715527add4218378c05602ed3352e55461
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586949"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Gemensamt och tjänstspecifikt schema för Azure-resurs loggar

> [!NOTE]
> Resurs loggar kallades tidigare för diagnostikloggar. Namnet ändrades i oktober 2019 när de typer av loggar som samlats in av Azure Monitor flyttas för att inkludera mer än bara Azure-resursen. Listan över resurs logg kategorier som du kan samla in används också i den här artikeln. De har flyttats till [resurs logg kategorier](resource-logs-categories.md). 

[Azure Monitor resurs loggar](./platform-logs-overview.md) genereras av Azure-tjänster som beskriver driften av dessa tjänster eller resurser. Alla resurs loggar som är tillgängliga via Azure Monitor dela ett gemensamt schema på högsta nivå, med flexibilitet för varje tjänst för att generera unika egenskaper för sina egna händelser.

En kombination av resurs typen (tillgänglig i `resourceId` egenskapen) och `category` unikt identifiera ett schema. Den här artikeln beskriver schemat på högsta nivån för resurs loggar och länkar till scheman för varje tjänst.


## <a name="top-level-common-schema"></a>Gemensamt schema på översta nivån

| Name | Obligatorisk/valfri | Beskrivning |
|---|---|---|
| time | Obligatorisk | Tids stämplingen (UTC) för händelsen. |
| resourceId | Obligatorisk | Resurs-ID för den resurs som har orsakat händelsen. För klient tjänster är detta av formatet/Tenants/Tenant-ID/providers/Provider-Name. |
| tenantId | Krävs för klient loggar | Klient-ID för den Active Directory klient som den här händelsen är kopplad till. Den här egenskapen används bara för loggar på klient nivå, den visas inte i loggar på resurs nivå. |
| operationName | Obligatorisk | Namnet på åtgärden som representeras av den här händelsen. Om händelsen representerar en Azure RBAC-åtgärd är det här Azure RBAC-åtgärds namnet (till exempel Microsoft. Storage/storageAccounts/blobServices/blobbar/Read). Vanligt vis modelleras i form av en Resource Manager-åtgärd, även om de inte är faktiska dokumenterade Resource Manager-åtgärder ( `Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>` ) |
| operationVersion | Valfritt | Den API-version som är kopplad till åtgärden, om operationName utfördes med hjälp av ett API (till exempel `http://myservice.windowsazure.net/object?api-version=2016-06-01` ). Om det inte finns något API som motsvarar den här åtgärden representerar-versionen den åtgärd som är associerad med åtgärden i framtiden. |
| category | Obligatorisk | Händelsens logg kategori. Kategori är den granularitet som du kan använda för att aktivera eller inaktivera loggar för en viss resurs. Egenskaperna som visas i en händelses egenskaps-BLOB är desamma inom en viss logg kategori och resurs typ. Typiska logg kategorier är "granskning", "körning" och "begäran". |
| resultType | Valfritt | Händelsens status. Vanliga värden är startad, pågår, lyckades, misslyckades, aktivt och löst. |
| resultSignature | Valfritt | Händelsens under status. Om den här åtgärden motsvarar ett REST API-anrop är det här fältet HTTP-statuskod för motsvarande REST-anrop. |
| resultDescription | Valfritt | Den statiska text beskrivningen för den här åtgärden, till exempel "Hämta lagrings fil". |
| durationMs | Valfritt | Åtgärdens varaktighet i millisekunder. |
| callerIpAddress | Valfritt | IP-adressen för anroparen, om åtgärden motsvarar ett API-anrop som kommer från en entitet med en offentligt tillgänglig IP-adress. |
| correlationId | Valfritt | Ett GUID som används för att gruppera samman en uppsättning relaterade händelser. Om två händelser har samma operationName men två olika status värden (till exempel "startad" och "lyckades") delar de samma korrelations-ID. Detta kan även representera andra relationer mellan händelser. |
| identity | Valfritt | En JSON-blob som beskriver identiteten för den användare eller det program som utförde åtgärden. Detta fält inkluderar vanligt vis auktorisering och anspråk/JWT-token från Active Directory. |
| Nivå | Valfritt | Händelsens allvarlighets grad. Måste vara en av information, varning, fel eller kritisk. |
| location | Valfritt | Regionen för den resurs som avger händelsen, till exempel "USA, östra" eller "Frankrike syd" |
| properties | Valfritt | Eventuella utökade egenskaper som är relaterade till den här specifika kategorin av händelser. Alla anpassade/unika egenskaper måste placeras i det här "del B" av schemat. |

## <a name="service-specific-schemas"></a>Tjänstspecifika scheman

Schemat för resurs loggar varierar beroende på resurs-och logg kategori. I den här listan visas tjänster som gör tillgängliga resurs loggar och länkar till tjänsten och det projektspecifika schemat där det är tillgängligt. Den här listan ändrar alla tidpunkter då nya tjänster läggs till, så om du inte ser vad du behöver kan du använda en sökmotor för att identifiera ytterligare dokumentation. Det är kostnads fritt att öppna ett GitHub-problem i den här artikeln så att vi kan uppdatera det.

| Tjänst | Schema & dokument |
| --- | --- |
| Azure Active Directory | [Översikt](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema för [Gransknings logg](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) och [inloggnings tillägg](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | [Azure Analysis Services-Konfigurera diagnostisk loggning](../../analysis-services/analysis-services-logging.md) |
| API Management | [API Management resurs loggar](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| App Service | [App Service loggar](../../app-service/troubleshoot-diagnostic-logs.md)
| Programgateways |[Loggning för Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics för Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch loggning](../../batch/batch-diagnostics.md) |
| Cognitive Services | [Loggning för Azure-Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Loggning för Azure Container Registry](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [Azure-loggar för CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB loggning](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [Övervaka data fabriker med hjälp av Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Åtkomst till loggar för Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Åtkomst till loggar för Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure-datautforskaren | [Azure Datautforskaren-loggar](/azure/data-explorer/using-diagnostic-logs) |
| Azure Database for MySQL | [Azure Database for MySQL diagnostikloggar](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL loggar](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Diagnostisk loggning i Azure Databricks](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Azure Digital Twins | [Konfigurera Azure Digitals dubblare-diagnostik](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Event Hubs |[Azure Event Hubs-loggar](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Schemat är inte tillgängligt. |
| Azure Firewall | Schemat är inte tillgängligt. |
| Front Door | [Loggning för front dörr](../../frontdoor/front-door-diagnostics.md) |
| IoT Hub | [IoT Hub åtgärder](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Key Vault |[Azure Key Vault loggning](../../key-vault/general/logging.md) |
| Kubernetes Service |[Azure Kubernetes-loggning](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Log Analytics för Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Anpassat Logic Apps B2B-spårningsschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Nätverkssäkerhetsgrupper |[Log Analytics för nätverkssäkerhetsgrupper (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS-skydd | [Hantera Azure DDoS Protection standard](../../ddos-protection/reports-and-flow-logs.md#sample-log-outputs) |
| Dedikerad Power BI | [Loggning för Power BI Embedded i Azure](/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Data modell för Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Aktivera och använda Sök Trafikanalys](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus loggar](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database loggning](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[Jobbloggar](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager logg schema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuella nätverk | Schemat är inte tillgängligt. |
| Virtuella nätverksgatewayer | Schemat är inte tillgängligt. |



## <a name="next-steps"></a>Nästa steg

* [Se resurs loggs kategorierna som du kan samla in](resource-logs-categories.md)
* [Läs mer om resurs loggar](./platform-logs-overview.md)
* [Strömma resurs resurs loggar till **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Ändra diagnostikinställningar för resurs loggen med hjälp av Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Analysera loggar från Azure Storage med Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)
