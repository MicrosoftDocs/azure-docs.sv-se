---
title: Diagnostikloggning
titleSuffix: Azure Cognitive Services
description: Den här guiden innehåller stegvisa instruktioner för att aktivera diagnostisk loggning för en Azure Cognitive Service. Dessa loggar innehåller omfattande, frekventa data om driften av en resurs som används för identifiering och felsökning av problem.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 4a78e233a41bf3b6682f52bac912528d6bcab76c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816348"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Aktivera diagnostisk loggning för Azure Cognitive Services

Den här guiden innehåller stegvisa instruktioner för att aktivera diagnostisk loggning för en Azure Cognitive Service. Dessa loggar innehåller omfattande, frekventa data om driften av en resurs som används för identifiering och felsökning av problem. Innan du fortsätter måste du ha ett Azure-konto med en prenumeration på minst en Cognitive Service, till exempel [Webbsökning i Bing,](./bing-web-search/overview.md) [Speech Services](./speech-service/overview.md)eller [LUIS.](./luis/what-is-luis.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill aktivera diagnostisk loggning behöver du någonstans att lagra dina loggdata. I den här självstudien används Azure Storage och Log Analytics.

* [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) – Behåller diagnostikloggar för principgranskning, statisk analys eller säkerhetskopiering. Lagringskontot behöver inte finnas i samma prenumeration som den resurs som skickar loggar så länge användaren som konfigurerar inställningen har lämplig Azure RBAC-åtkomst till båda prenumerationerna.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) – ett flexibelt verktyg för loggsökning och analys som möjliggör analys av rådataloggar som genereras av en Azure-resurs.

> [!NOTE]
> Ytterligare konfigurationsalternativ är tillgängliga. Mer information finns i Samla [in och använda loggdata från dina Azure-resurser.](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="enable-diagnostic-log-collection"></a>Aktivera insamling av diagnostikloggar  

Vi börjar med att aktivera diagnostisk loggning med hjälp av Azure Portal.

> [!NOTE]
> Om du vill aktivera den här funktionen med PowerShell eller Azure CLI följer du anvisningarna i Samla in och [använda loggdata från dina Azure-resurser.](../azure-monitor/essentials/platform-logs-overview.md)

1. Gå till Azure-portalen. Leta sedan upp och välj Cognitive Services resurs. Till exempel din prenumeration på Webbsökning i Bing.   
2. I den vänstra navigeringsmenyn letar du sedan upp **Övervakning och** väljer **Diagnostikinställningar.** Den här skärmen innehåller alla tidigare skapade diagnostikinställningar för den här resursen.
3. Om det finns en tidigare skapad resurs som du vill använda kan du välja den nu. Annars väljer du **+ Lägg till diagnostikinställning**.
4. Ange ett namn för inställningen. Välj sedan **Arkivera till ett lagringskonto** **och Skicka till log Analytics.**
5. När du uppmanas att konfigurera väljer du det lagringskonto och den OMS-arbetsyta som du vill använda för att lagra diagnostikloggarna. **Obs!** Om du inte har ett lagringskonto eller en OMS-arbetsyta följer du anvisningarna för att skapa ett.
6. Välj **Granska,** **RequestResponse** och **AllMetrics.** Ange sedan kvarhållningsperioden för dina diagnostikloggdata. Om en bevarandeprincip är inställd på noll lagras händelser för den loggkategorin på obestämd tid.
7. Klicka på **Spara**.

Det kan ta upp till två timmar innan loggningsdata är tillgängliga för frågor och analys. Så oroa dig inte om du inte ser något direkt.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Visa och exportera diagnostikdata från Azure Storage

Azure Storage är en robust objektlagringslösning som är optimerad för att lagra stora mängder ostrukturerade data. I det här avsnittet lär du dig att fråga ditt lagringskonto efter totala transaktioner under en 30-dagars tidsram och exportera data till Excel.

1. Leta upp Azure Portal resurs som Azure Storage du skapade i det förra avsnittet från den senaste sidan.
2. I den vänstra navigeringsmenyn letar du **upp Övervakning** och väljer **Mått**.
3. Använd tillgängliga listrutan för att konfigurera din fråga. I det här exemplet ska vi ange intervallet till **Senaste 30 dagarna** och måttet till **Transaction**.
4. När frågan är klar visas en visualisering av transaktionen under de senaste 30 dagarna. Om du vill exportera dessa data **använder du knappen Exportera** till Excel längst upp på sidan.

Läs mer om vad du kan göra med diagnostikdata i [Azure Storage](../storage/blobs/storage-blobs-introduction.md).

## <a name="view-logs-in-log-analytics"></a>Visa loggar i Log Analytics

Följ de här instruktionerna för att utforska log analytics-data för din resurs.

1. I Azure Portal du och väljer **Log Analytics på** den vänstra navigeringsmenyn.
2. Leta upp och välj den resurs som du skapade när du aktiverar diagnostik.
3. Under **Allmänt** letar du upp och väljer **Loggar.** Från den här sidan kan du köra frågor mot dina loggar.

### <a name="sample-queries"></a>Exempelfrågor

Här är några grundläggande Kusto-frågor som du kan använda för att utforska dina loggdata.

Kör den här frågan för alla diagnostikloggar Azure Cognitive Services under en angiven tidsperiod:

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

Kör den här frågan för att visa mängden åtgärder över tid som delats efter OperationName med antal var 10:e sekund.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du aktiverar loggning och de mått och loggkategorier som stöds av de olika Azure-tjänsterna finns i artiklarna [Översikt](../azure-monitor/data-platform.md) över mått i Microsoft Azure och [Översikt över Azure-diagnostikloggar.](../azure-monitor/essentials/platform-logs-overview.md)
* Läs de här artiklarna om du vill veta mer om händelsehubbbar:
  * [Vad är Azure Event Hubs?](../event-hubs/event-hubs-about.md)
  * [Kom igång med Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* Läs [Ladda ned mått och diagnostikloggar från Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
* Läs [Förstå loggsökningar i Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md).