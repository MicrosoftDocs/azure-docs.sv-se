---
title: Strömma Azure-övervakningsdata till händelsehubb och externa partner
description: Lär dig hur du strömmar dina Azure-övervakningsdata till en händelsehubb för att hämta data till ett SIEM-partnerverktyg eller analysverktyg.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 7592935afadc88c4b9e0e5f3c5f9c83d42c63209
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768749"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>Strömma Azure-övervakningsdata till en händelsehubb eller extern partner

Azure Monitor en komplett komplett övervakningslösning för program och tjänster i Azure, i andra moln och lokalt. Förutom att använda Azure Monitor för att analysera dessa data och utnyttja dem för olika övervakningsscenarier kan du behöva skicka dem till andra övervakningsverktyg i din miljö. I de flesta fall är den mest effektiva metoden för att strömma övervakningsdata till externa verktyg att [använda Azure Event Hubs](../../event-hubs/index.yml). Den här artikeln innehåller en kort beskrivning av hur du gör detta och listar sedan några av de partner där du kan skicka data. Vissa har särskild integrering med Azure Monitor och kan finnas i Azure.  

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Innan du konfigurerar strömning för en datakälla måste du [skapa Event Hubs namnrymd och händelsehubb.](../../event-hubs/event-hubs-create.md) Det här namnområdet och händelsehubben är målet för alla dina övervakningsdata. Ett Event Hubs är en logisk gruppering av händelsehubbar som delar samma åtkomstprincip, ungefär som ett lagringskonto har enskilda blobar i det lagringskontot. Tänk på följande information om händelsehubbens namnområde och händelsehubbb som du använder för strömmande övervakningsdata:

* Med antalet dataflödesenheter kan du öka dataflödesskalan för dina händelsehubbbar. Vanligtvis krävs bara en dataflödesenhet. Om du behöver skala upp allt eftersom logganvändningen ökar kan du manuellt öka antalet genomflödesenheter för namnområdet eller aktivera automatisk loggning.
* Med antalet partitioner kan du parallellisera förbrukningen mellan många konsumenter. En enda partition har stöd för upp till 20 MBps eller cirka 20 000 meddelanden per sekund. Beroende på vilket verktyg som använder data kan det hända att det inte går att använda från flera partitioner. Det är rimligt att börja med fyra partitioner om du inte är säker på antalet partitioner som ska anges.
* Du anger kvarhållning av meddelanden på händelsehubben till minst 7 dagar. Om ditt användningsverktyg ligger nere i mer än en dag säkerställer detta att verktyget kan fortsätta där det slutade för händelser som är upp till 7 dagar gamla.
* Du bör använda standardkonsumentgruppen för händelsehubben. Du behöver inte skapa andra konsumentgrupper eller använda en separat konsumentgrupp om du inte planerar att ha två olika verktyg som använder samma data från samma händelsehubb.
* För Azure-aktivitetsloggen väljer du Event Hubs namnområde och Azure Monitor skapar en händelsehubb i det namnområdet som kallas _insights-logs-operational-logs_. För andra loggtyper kan du antingen välja en befintlig händelsehubb eller Azure Monitor skapa en händelsehubb per loggkategori.
* Utgående port 5671 och 5672 måste vanligtvis öppnas på datorn eller VNET-användningsdata från händelsehubben.

## <a name="monitoring-data-available"></a>Tillgängliga övervakningsdata
[Källor för övervakningsdata för Azure Monitor](../agents/data-sources.md) beskriver olika datanivåer för Azure-program och vilka typer av övervakningsdata som är tillgängliga för var och en. I följande tabell visas var och en av dessa nivåer och en beskrivning av hur dessa data kan strömmas till en händelsehubb. Följ länkarna för mer information.

| Nivå | Data | Metod |
|:---|:---|:---|
| [Azure-klientorganisation](../agents/data-sources.md#azure-tenant) | Azure Active Directory granskningsloggar | Konfigurera en diagnostikinställning för klientorganisationen i din AAD-klientorganisation. Se  [Självstudie: Strömma Azure Active Directory loggar till en Azure-händelsehubb](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) för mer information. |
| [Azure-prenumeration](../agents/data-sources.md#azure-subscription) | Azure-aktivitetslogg | Skapa en loggprofil för att exportera aktivitetslogghändelser till Event Hubs.  Se [Stream Azure-plattformsloggar för Azure Event Hubs](../essentials/resource-logs.md#send-to-azure-event-hubs) mer information. |
| [Azure-resurser](../agents/data-sources.md#azure-resources) | Plattformsmått<br> Resursloggar |Båda typerna av data skickas till en händelsehubb med hjälp av en resursdiagnostikinställning. Mer [information finns i Strömma Azure-resursloggar till en](../essentials/resource-logs.md#send-to-azure-event-hubs) händelsehubb. |
| [Operativsystem (gäst)](../agents/data-sources.md#operating-system-guest) | Virtuella Azure-datorer | Installera Azure Diagnostics [på virtuella](../agents/diagnostics-extension-overview.md) Windows- och Linux-datorer i Azure. Se Strömma Azure Diagnostics data i den heta sökvägen med [hjälp av Event Hubs](../agents/diagnostics-extension-stream-event-hubs.md) för information om virtuella Windows-datorer och Använda [Linux-diagnostiktillägg](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) för att övervaka mått och loggar för information om virtuella Linux-datorer. |
| [Programkod](../agents/data-sources.md#application-code) | Application Insights | Application Insights tillhandahåller ingen direkt metod för att strömma data till event hubs. Du kan [konfigurera kontinuerlig export](../app/export-telemetry.md) av Application Insights-data till ett lagringskonto och sedan använda en logikapp för att skicka data till en händelsehubb enligt beskrivningen i Manuell [strömning](#manual-streaming-with-logic-app)med Logic App . |

## <a name="manual-streaming-with-logic-app"></a>Manuell strömning med logikapp
För data som du inte kan strömma direkt till en händelsehubb kan du skriva till Azure Storage och sedan använda en [tidsutlöst](../../connectors/connectors-create-api-azureblobstorage.md#add-action) logikapp som hämtar data från bloblagringen och push-erar dem som ett meddelande till [händelsehubben](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partnerverktyg med Azure Monitor integrering

Genom att dirigera dina övervakningsdata till en händelsehubb med Azure Monitor kan du enkelt integrera med externa SIEM- och övervakningsverktyg. Exempel på verktyg med Azure Monitor integrering är följande:

| Verktyg | Värd i Azure | Description |
|:---|:---| :---|
|  IBM QRadar | No | Det Microsoft Azure DSM och Microsoft Azure Event Hub Protocol finns att hämta från [IBM-supportwebbplatsen](https://www.ibm.com/support). Du kan läsa mer om integreringen med Azure i [QRadar DSM-konfiguration.](https://www.ibm.com/docs/en/dsm?topic=options-configuring-microsoft-azure-event-hubs-communicate-qradar) |
| Splunk | No | [Microsoft Azure Add-On för Splunk](https://splunkbase.splunk.com/app/3757/) är ett projekt med öppen källkod som är tillgängligt i Splunkbase. <br><br> Om du inte kan installera ett tillägg i din Splunk-instans kan du, om du till exempel använder en proxy eller som körs i Splunk Cloud, vidarebefordra dessa händelser till Splunk HTTP Event Collector med hjälp av [Azure Function for Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), som utlöses av nya meddelanden i händelsehubben. |
| SumoLogic | No | Anvisningar för att konfigurera SumoLogic för att använda data från en händelsehubb finns i Samla in loggar för [Azure-granskningsappen från Event Hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | No | Den smarta Anslutningsappen för ArcSight Azure Event Hub är tillgänglig som en [del av samlingen med smarta ArcSight-anslutningsappar.](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852) |
| Syslog-server | No | Om du vill strömma Azure Monitor data direkt till en syslog-server kan du använda en [lösning som baseras på en Azure-funktion](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | No| Anvisningar för att konfigurera LogRhythm för att samla in loggar från en händelsehubb finns [här.](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/) 
|Logz.io | Yes | Mer information finns i Komma [igång med övervakning och loggning med hjälp Logz.io Java-appar som körs på Azure](/azure/developer/java/fundamentals/java-get-started-with-logzio)

Andra partner kan också vara tillgängliga. En mer fullständig lista över alla Azure Monitor och deras funktioner finns i Azure Monitor [för partnerintegreringar.](../partners.md)

## <a name="next-steps"></a>Nästa steg
* [Arkivera aktivitetsloggen till ett lagringskonto](./activity-log.md#legacy-collection-methods)
* [Läs översikten över Azure-aktivitetsloggen](../essentials/platform-logs-overview.md)
* [Konfigurera en avisering baserat på en aktivitetslogghändelse](../alerts/alerts-log-webhook.md)
