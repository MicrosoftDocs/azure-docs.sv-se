---
title: Diagnostikloggning
titleSuffix: Azure Cognitive Services
description: Den här guiden innehåller stegvisa instruktioner för att aktivera diagnostisk loggning för en Azure-tjänst. Dessa loggar ger omfattande, frekventa data om driften av en resurs som används för problem identifiering och fel sökning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: a2005ca7b32136ff0032d27e04035c46b2e4e904
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595368"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Aktivera diagnostisk loggning för Azure Cognitive Services

Den här guiden innehåller stegvisa instruktioner för att aktivera diagnostisk loggning för en Azure-tjänst. Dessa loggar ger omfattande, frekventa data om driften av en resurs som används för problem identifiering och fel sökning. Innan du fortsätter måste du ha ett Azure-konto med en prenumeration på minst en kognitiv tjänst, till exempel [webbsökning i Bing](./bing-web-search/overview.md), [tal tjänster](./speech-service/overview.md)eller [Luis](./luis/what-is-luis.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill aktivera diagnostikloggning måste du lagra dina loggdata någonstans. I den här självstudien används Azure Storage och Log Analytics.

* [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) – behåller diagnostikloggar för princip granskning, statisk analys eller säkerhets kopiering. Lagrings kontot behöver inte finnas i samma prenumeration som resursen som skapar loggar så länge som den användare som konfigurerar inställningen har lämplig Azure RBAC-åtkomst till båda prenumerationerna.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) – ett flexibelt loggs öknings-och analys verktyg som möjliggör analys av obehandlade loggar som genereras av en Azure-resurs.

> [!NOTE]
> Det finns ytterligare konfigurations alternativ. Mer information finns i [samla in och använda loggdata från dina Azure-resurser](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="enable-diagnostic-log-collection"></a>Aktivera insamling av diagnostikdata  

Låt oss börja med att aktivera diagnostisk loggning med hjälp av Azure Portal.

> [!NOTE]
> Om du vill aktivera den här funktionen med PowerShell eller Azure CLI använder du anvisningarna i [samla in och Använd loggdata från dina Azure-resurser](../azure-monitor/essentials/platform-logs-overview.md).

1. Gå till Azure-portalen. Leta sedan reda på och välj en Cognitive Services resurs. Till exempel din prenumeration på Webbsökning i Bing.   
2. Gå sedan till den vänstra navigerings menyn och leta upp **övervakning** och välj **diagnostikinställningar**. Den här skärmen innehåller alla tidigare skapade diagnostikinställningar för den här resursen.
3. Om det finns en resurs som du redan har skapat som du vill använda, kan du välja den nu. Annars väljer du **+ Lägg till diagnostisk inställning**.
4. Ange ett namn för inställningen. Välj sedan **Arkiv till ett lagrings konto** och **Skicka till Log Analytics**.
5. När du uppmanas att konfigurera väljer du det lagrings konto och den OMS-arbetsyta som du vill använda för att lagra dina diagnostikloggar. **Obs!** om du inte har ett lagrings konto eller en OMS-arbetsyta följer du anvisningarna för att skapa ett.
6. Välj **granskning**, **RequestResponse** och **AllMetrics**. Ange sedan kvarhållningsperioden för din diagnostiska loggdata. Om en bevarande princip är inställd på noll lagras händelser för den logg kategorin under obestämd tid.
7. Klicka på **Spara**.

Det kan ta upp till två timmar innan loggnings data är tillgängliga för att fråga och analysera. Oroa dig inte om du inte ser något direkt.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Visa och exportera diagnostikdata från Azure Storage

Azure Storage är en robust lösning för objekt lagring som är optimerad för att lagra stora mängder ostrukturerade data. I det här avsnittet får du lära dig att fråga ditt lagrings konto efter totalt antal transaktioner under 30 dagar och exportera data till Excel.

1. Leta upp den Azure Storage resurs som du skapade i det sista avsnittet från Azure Portal.
2. På den vänstra navigerings menyn letar du reda på **övervakning** och väljer **mått**.
3. Använd de tillgängliga List rutorna för att konfigurera din fråga. I det här exemplet ska vi ställa in tidsintervallet för de **senaste 30 dagarna** och måttet på **transaktionen**.
4. När frågan har slutförts visas en visualisering av transaktionen under de senaste 30 dagarna. Om du vill exportera dessa data använder du knappen **Exportera till Excel** som finns överst på sidan.

Lär dig mer om vad du kan göra med diagnostikdata i [Azure Storage](../storage/blobs/storage-blobs-introduction.md).

## <a name="view-logs-in-log-analytics"></a>Visa loggar i Log Analytics

Följ dessa anvisningar för att utforska logg analys data för din resurs.

1. Leta upp och välj **Log Analytics** i den vänstra navigerings menyn från Azure Portal.
2. Leta upp och välj den resurs som du skapade när du aktiverade diagnostik.
3. Leta upp och välj **loggar** under **Allmänt**. Från den här sidan kan du köra frågor mot dina loggar.

### <a name="sample-queries"></a>Exempelfrågor

Här följer några grundläggande Kusto-frågor som du kan använda för att utforska dina loggdata.

Kör den här frågan för alla diagnostikloggar från Azure Cognitive Services under en angiven tids period:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Kör den här frågan för att se de 10 senaste loggarna:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Kör den här frågan för att gruppera åtgärder efter **resurs**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Kör den här frågan för att hitta den genomsnittliga tid det tar att utföra en åtgärd:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Kör den här frågan om du vill visa mängden åtgärder över tid som delas av OperationName med antal diskretiseras för varje tiotal.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du aktiverar loggning och även de mått och logg kategorier som stöds av de olika Azure-tjänsterna finns i både [översikten över mått](../azure-monitor/data-platform.md) i Microsoft Azure och [Översikt över artiklar om Azure Diagnostic-loggar](../azure-monitor/essentials/platform-logs-overview.md) .
* Läs de här artiklarna om du vill veta mer om Event Hub:
  * [Vad är Azure Event Hubs?](../event-hubs/event-hubs-about.md)
  * [Kom igång med Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* Läs [hämtnings statistik och diagnostikloggar från Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
* Läs [förstå loggs ökningar i Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md).