---
title: Övervaknings Media Services
description: Börja här om du vill veta mer om hur du övervakar Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.date: 03/17/2021
ms.openlocfilehash: 783d9e1b4ab86f6580cf3418a0676921aef2db6a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598209"
---
# <a name="monitor-media-services"></a>Övervaka Media Services

När du har viktiga program och affärs processer som förlitar sig på Azure-resurser, vill du övervaka resurserna för deras tillgänglighet, prestanda och drift. I den här artikeln beskrivs övervaknings data som genereras av Media Services och hur du kan använda funktionerna i Azure Monitor för att analysera och varna för dessa data.

## <a name="metrics-are-useful"></a>Mått är användbara

Här följer exempel på hur du kan använda övervaknings Media Services mått för att förstå hur dina appar presterar. Några frågor som kan åtgärdas med Media Services mått är:

- Vill du Hur gör jag för att övervaka min standard slut punkt för direkt uppspelning för att veta om jag har överskridit gränserna?
- Hur gör jag för att vet du om det finns tillräckligt med skalnings enheter för slut punkter för direkt uppspelning?
- Hur kan jag ange en avisering för att veta när de ska skala upp mina slut punkter för strömning?
- Hur gör jag för att ange en avisering om du vill veta när det högsta utgående värdet som har kon figurer ATS på kontot uppnåddes?
- Hur kan jag se en nedbrytning av begär Anden som Miss lyckas och vad som orsakar felet?
- Hur kan jag se hur många HLS-eller BINDESTRECKs-begäranden som hämtas från Paketeraren?
- Hur gör jag för att ange en avisering om jag vill veta när jag har nått tröskelvärdet för misslyckade förfrågningar?

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?

Media Services skapar övervaknings data med [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview), som är en fullständig stack övervaknings tjänst i Azure som innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser, förutom resurser i andra moln och lokalt.

Börja med att läsa artikeln [övervakning av Azure-resurser med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), som beskriver följande begrepp:

- Vad är Azure Monitor?
- Kostnader för övervakning
- Övervaknings data som samlas in i Azure
- Konfigurerar data insamling
- Standard verktyg i Azure för analys och avisering om övervaknings data

## <a name="monitoring-data"></a>Övervaka data

Media Services samlar in samma typer av övervaknings data som andra Azure-resurser som beskrivs i [övervaknings data från Azure-resurser](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources).

Alla data som samlas in av Azure Monitor tillhör en av två grundläggande typer: mått och loggar. Med de här två typerna kan du:

- Visualisera och analysera mått data med hjälp av Metrics Explorer.
- Övervaka Media Services diagnostikloggar och skapa aviseringar och meddelanden för dem.
- Med loggar kan du:
  - Skicka dem till Azure Storage
  - Strömma dem till Azure Event Hubs
  - Exportera dem till Log Analytics
  - Använd tjänster från tredje part

Se artikeln [övervaknings Media Services data referens](monitor-media-services-data-reference.md) för detaljerad information om mått och loggar mått som skapats av Media Services.

## <a name="collection-and-routing"></a>Samling och routning

*Plattforms mått* och *aktivitets loggen* samlas in och lagras automatiskt, men kan dirigeras till andra platser med hjälp av en diagnostisk inställning.  

*Resurs loggar* samlas **inte** in och lagras förrän du skapar en diagnostisk inställning och dirigerar dem till en eller flera platser.

Se artikeln [skapa diagnostikinställningar för att samla in plattforms loggar och statistik i Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) för den detaljerade processen med att skapa en diagnostisk inställning med hjälp av Azure Portal, CLI eller PowerShell.

När du skapar en diagnostisk inställning anger du vilka kategorier av loggar som ska samlas in. Kategorierna för Media Services anges i [Media Services övervaknings data referens](monitor-media-services-data-reference.md).

## <a name="analyzing-metrics"></a>Analyserar mått

Du kan analysera mått för Media Services med mått från andra Azure-tjänster med hjälp av Metric Explorer genom att öppna **mått** från **Azure Monitor** -menyn. Mer information om hur du använder det här verktyget finns i [komma igång med Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) .

En lista över de mått som samlas in för Media Services finns i [övervaknings Media Services data referens](monitor-media-services-data-reference.md).

## <a name="analyzing-logs"></a>Analysera loggar

Data i Azure Monitor loggar lagras i tabeller där varje tabell har en egen uppsättning unika egenskaper.  

Alla resurs loggar i Azure Monitor har samma fält följt av tjänstespecific-fält. Det gemensamma schemat beskrivs i [Azure Monitor resurs logg schema](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema).

Schemat för Media Services resurs loggar finns i [övervaknings Media Services data referens](monitor-media-services-data-reference.md).

[Aktivitets loggen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log) är en plattforms logg i Azure som ger inblick i händelser på prenumerations nivå. Du kan visa den oberoende av varandra eller dirigera den till Azure Monitor loggar, där du kan göra mycket mer komplexa frågor med Log Analytics.

En lista över typer av resurs loggar som samlas in för Media Services finns i [övervaknings Media Services data referens](monitor-media-services-data-reference.md).

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>Varför skulle jag vilja använda diagnostikloggar?

Några saker som du kan undersöka med diagnostikloggar är:

- Antalet licenser som levererats av DRM-typ.
- Antalet licenser som har levererats av en princip.
- Fel per DRM eller princip typ.
- Antalet otillåtna licens begär Anden från klienter.

## <a name="alerts"></a>Aviseringar

Azure Monitor aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem i systemet innan kunderna märker dem. Du kan ställa in aviseringar för [mått](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview), [loggar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)och [aktivitets loggen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts).

Media Services mått samlas in med jämna mellanrum oavsett om värdet ändras eller inte. De är användbara för aviseringar eftersom de kan samplas ofta. En avisering kan utlösas snabbt med relativt enkel logik.

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
