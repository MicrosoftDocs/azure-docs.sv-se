---
title: Strömma Azure övervaknings data till Event Hub och externa partners
description: Lär dig hur du direktuppspelar Azures övervaknings data till en Event Hub för att hämta data till en partner SIEM eller ett analys verktyg.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.subservice: ''
ms.openlocfilehash: db8f8628f77ef2a04a7e6d42d6470f254e458e01
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708092"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>Strömma Azure övervaknings data till en Event Hub-eller extern partner

Azure Monitor ger en fullständig lösning för stack övervakning för program och tjänster i Azure, i andra moln och lokalt. Förutom att använda Azure Monitor för att analysera data och använda dem för olika övervaknings scenarier, kan du behöva skicka dem till andra övervaknings verktyg i din miljö. I de flesta fall använder [Azure Event Hubs](../../event-hubs/index.yml)det mest effektiva sättet att strömma övervaknings data till externa verktyg. Den här artikeln innehåller en kort beskrivning av hur du gör detta och visar en lista över några av de partner där du kan skicka data. Vissa har särskild integrering med Azure Monitor och kan finnas i Azure.  

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Innan du konfigurerar strömning för en data källa måste du [skapa ett Event Hubs-namnområde och händelsehubben](../../event-hubs/event-hubs-create.md). Det här namn området och händelsehubben är målet för alla dina övervaknings data. Ett Event Hubs-namnområde är en logisk gruppering av händelse hubbar som delar samma åtkomst princip, ungefär som ett lagrings konto har enskilda blobbar inom det lagrings kontot. Tänk på följande om Event Hub-namnområdet och händelse nav som du använder för att strömma övervaknings data:

* Antalet data flödes enheter gör att du kan öka data flödets skala för dina Event Hub. Det krävs vanligt vis en data flödes enhet. Om du behöver skala upp när logg användningen ökar kan du manuellt öka antalet data flödes enheter för namn området eller aktivera automatisk inflation.
* Med antalet partitioner kan du parallellisera förbrukningen för många konsumenter. En enda partition har stöd för upp till 20MBps eller cirka 20 000 meddelanden per sekund. Beroende på vilket verktyg som utnyttjar data kan det hända att det inte går att använda från flera partitioner. Fyra partitioner är rimliga för att börja med om du inte är säker på hur många partitioner som ska ställas in.
* Du ställer in meddelande kvarhållning på händelsehubben till minst 7 dagar. Om ditt förbrukare verktyg går ned i mer än en dag, ser detta till att verktyget kan fortsätta där det slutade för händelser upp till 7 dagar gammal.
* Använd standard konsument gruppen för händelsehubben. Du behöver inte skapa andra konsument grupper eller använda en separat konsument grupp om du inte planerar att ha två olika verktyg som använder samma data från samma Event Hub.
* För Azure aktivitets loggen väljer du ett Event Hubs namn område och Azure Monitor skapar en händelsehubben i det namn området som kallas _Insights-loggar-operativa loggar_. För andra typer av loggar kan du antingen välja en befintlig händelsehubben eller låta Azure Monitor skapa en Event Hub per logg kategori.
* Utgående port 5671 och 5672 måste vanligt vis öppnas på datorn eller VNET som använder data från händelsehubben.

## <a name="monitoring-data-available"></a>Tillgängliga övervaknings data
[Källor för övervaknings data för Azure Monitor](../agents/data-sources.md) beskriver olika data nivåer för Azure-program och vilka typer av övervaknings data som är tillgängliga för var och en. I följande tabell visas var och en av dessa nivåer och en beskrivning av hur data kan strömmas till en händelsehubben. Följ länkarna som finns för ytterligare information.

| Nivå | Data | Metod |
|:---|:---|:---|
| [Azure-klientorganisation](../agents/data-sources.md#azure-tenant) | Azure Active Directory gransknings loggar | Konfigurera en inställning för klientens diagnostik på din AAD-klient. Mer information finns i  [Självstudier: strömma Azure Active Directory loggar till en Azure Event Hub](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) . |
| [Azure-prenumeration](../agents/data-sources.md#azure-subscription) | Azure-aktivitetslogg | Skapa en logg profil för att exportera aktivitets logg händelser till Event Hubs.  Mer information finns i [strömma Azure-plattforms loggar till azure Event Hubs](../essentials/resource-logs.md#send-to-azure-event-hubs) . |
| [Azure-resurser](../agents/data-sources.md#azure-resources) | Plattformsmått<br> Resursloggar |Båda typerna av data skickas till en Event Hub med en inställning för resurs diagnostik. Mer information finns i [strömma Azure-resurstilldelningar till en Event Hub](../essentials/resource-logs.md#send-to-azure-event-hubs) . |
| [Operativ system (gäst)](../agents/data-sources.md#operating-system-guest) | Virtuella Azure-datorer | Installera [Azure-diagnostik-tillägget](../agents/diagnostics-extension-overview.md) på virtuella Windows-och Linux-datorer i Azure. Se [strömmande Azure-diagnostik data i den aktiva sökvägen med hjälp av Event Hubs](../agents/diagnostics-extension-stream-event-hubs.md) för information om virtuella Windows-datorer och [Använd Linux Diagnostic-tillägg för att övervaka statistik och loggar](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) för information om virtuella Linux-datorer. |
| [Program kod](../agents/data-sources.md#application-code) | Application Insights | Application Insights tillhandahåller inte någon direkt metod för att strömma data till händelse nav. Du kan [Konfigurera kontinuerlig export](../app/export-telemetry.md) av Application Insights data till ett lagrings konto och sedan använda en Logi Kap par för att skicka data till en Event Hub enligt beskrivningen i [manuell strömning med Logic app](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Manuell strömning med Logic app
För data som du inte kan strömma direkt till en Event Hub kan du skriva till Azure Storage och sedan använda en tidsinitierad Logic-app som [hämtar data från Blob Storage](../../connectors/connectors-create-api-azureblobstorage.md#add-action) och skickar [dem som ett meddelande till händelsehubben](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partner verktyg med Azure Monitor-integrering

Genom att vidarebefordra dina övervaknings data till en Event Hub med Azure Monitor kan du enkelt integrera med externa SIEM och övervaknings verktyg. Exempel på verktyg med Azure Monitor-integrering är följande:

| Verktyg | Värd i Azure | Beskrivning |
|:---|:---| :---|
|  IBM QRadar | Inga | Microsoft Azure DSM-och Microsoft Azure Event Hub-protokollet kan hämtas från [IBM support-webbplatsen](https://www.ibm.com/support). Du kan lära dig mer om integrering med Azure på [QRADAR DSM-konfiguration](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | Inga | [Microsoft Azure Add-On för Splunk](https://splunkbase.splunk.com/app/3757/) är ett projekt med öppen källkod som finns i Splunkbase. <br><br> Om du inte kan installera ett tillägg i din Splunk-instans, om du t. ex. använder en proxy eller som körs på Splunk-molnet, kan du vidarebefordra händelserna till insamlaren för HTTP-Splunk med hjälp av [Azure Function för Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), som utlöses av nya meddelanden i händelsehubben. |
| SumoLogic | Inga | Anvisningar för att konfigurera SumoLogic för att använda data från en händelsehubben är tillgängliga vid [insamling av loggar för Azure audit-appen från Event Hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Inga | Smart Connector för ArcSight Azure Event Hub är tillgängligt som en del av [ArcSight Smart Connector-samlingen](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Syslog-server | Inga | Om du vill strömma Azure Monitor data direkt till en Syslog-server kan du använda en [lösning som baseras på en Azure-funktion](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | Inga| Anvisningar för att konfigurera LogRhythm för att samla in loggar från en Event Hub finns [här](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Ja | Mer information finns i [komma igång med övervakning och loggning med Logz.io för Java-appar som körs på Azure](/azure/developer/java/fundamentals/java-get-started-with-logzio)

Andra partner kan också vara tillgängliga. En fullständig lista över alla Azure Monitor partner och deras funktioner finns i [Azure Monitor partner integrationer](../partners.md).

## <a name="next-steps"></a>Nästa steg
* [Arkivera aktivitets loggen till ett lagrings konto](./activity-log.md#legacy-collection-methods)
* [Läs översikten över Azures aktivitets logg](../essentials/platform-logs-overview.md)
* [Konfigurera en avisering baserat på en aktivitets logg händelse](../alerts/alerts-log-webhook.md)