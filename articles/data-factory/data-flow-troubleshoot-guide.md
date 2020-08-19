---
title: Felsöka data flöden
description: Lär dig hur du felsöker data flödes problem i Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/16/2020
ms.openlocfilehash: 0a691b562ebf030712eb0c13a688ea9a52fdb164
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263477"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Felsöka data flöden i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för data flöden i Azure Data Factory.

## <a name="common-errors-and-messages"></a>Vanliga fel och meddelanden

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Felkod: DF-utförar-SourceInvalidPayload
- **Meddelande**: det gick inte att köra data flöde för förhands granskning, fel sökning och pipeline-data flöde eftersom behållaren inte finns
- **Orsaker**: när data uppsättningen innehåller en behållare som inte finns i lagringen
- **Rekommendation**: kontrol lera att den behållare som refereras till i data uppsättningen finns eller är tillgänglig.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Felkod: DF-utförar-SystemImplicitCartesian

- **Meddelande**: implicit kartesiska-produkt för inre koppling stöds inte, Använd kors koppling i stället. Kolumner som används i Join ska skapa en unik nyckel för rader.
- **Orsaker**: implicit kartesiska-produkt för inre koppling mellan logiska planer stöds inte. Om de kolumner som används i kopplingen skapar den unika nyckeln, krävs minst en kolumn från båda sidor i relationen.
- **Rekommendation**: för icke-jämlikhetbaserade kopplingar måste du välja anpassad kors koppling.

### <a name="error-code-df-executor-systeminvalidjson"></a>Felkod: DF-utförar-SystemInvalidJson

- **Meddelande**: JSON-parsningsfel, kodning eller Multiline stöds inte
- **Orsaker**: möjliga problem med JSON-filen: kodning som inte stöds, skadade byte eller med JSON-källa som enskilt dokument på flera kapslade rader
- **Rekommendation**: kontrol lera att JSON-filens kodning stöds. På den käll omvandling som använder en JSON-datauppsättning expanderar du JSON-inställningar och aktiverar ett enskilt dokument.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Felkod: DF-utförar-BroadcastTimeout

- **Meddelande**: timeout-fel vid sändnings anslutning, kontrol lera att sändnings strömmen genererar data inom 60 sekunder i fel söknings körningar och 300 sekunder i jobb körningar
- **Orsaker**: sändningen har en standard tids gräns på 60 sekunder i fel söknings körningar och 300 sekunder i jobb körningarna. Den data ström som valts för sändning verkar vara stor för att producera data inom den här gränsen.
- **Rekommendation**: kontrol lera att fliken optimera finns i dina data flödes omvandlingar för Join, exists och lookup. Standard alternativet för sändning är "Auto". Om detta är inställt, eller om du anger att den vänstra eller högra sidan ska sändas manuellt under "fast", kan du antingen ange en större Azure Integration Runtime-konfiguration eller stänga av sändningen. Den rekommenderade metoden för bästa prestanda i data flöden är att tillåta Spark att sända med "Auto" och använda ett Minnesoptimerade Azure IR.

### <a name="error-code-df-executor-conversion"></a>Felkod: DF-utförar-Conversion

- **Meddelande**: det gick inte att konvertera till ett datum eller en tid på grund av ett ogiltigt Character
- **Orsaker**: data har inte förväntat format
- **Rekommendation**: Använd rätt datatyp

### <a name="error-code-df-executor-invalidcolumn"></a>Felkod: DF-utförar-InvalidColumn

- **Meddelande**: kolumn namnet måste anges i frågan, ange ett alias om du använder en SQL-funktion
- **Orsaker**: inget kolumn namn har angetts
- **Rekommendation**: Ange ett alias om du använder en SQL-funktion som min ()/max () osv.

### <a name="error-code-getcommand-outputasync-failed"></a>Felkod: GetCommand OutputAsync misslyckades

- **Meddelande**: vid fel sökning av data flöde och data förhands granskning: GetCommand OutputAsync misslyckades med...
- **Orsaker**: det här är ett fel i backend-tjänsten. Du kan försöka igen och starta om din felsökningssession.
- **Rekommendation**: om försök igen och omstart inte löser problemet kontaktar du kund support.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Felkod: ett oväntat undantag och körning misslyckades

- **Meddelande**: vid körning av data flödes aktivitet: det gick inte att köra ett oväntat undantag och körnings fel.
- **Orsaker**: det här är ett fel i backend-tjänsten. Du kan försöka igen och starta om din felsökningssession.
- **Rekommendation**: om försök igen och omstart inte löser problemet kontaktar du kund support.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>Felkod: fel meddelande vid förhands granskning av data inga utdata vid koppling

- **Meddelande**: det finns ett stort antal null-värden eller saknade värden som kan bero på att det inte finns några rader som samplats. Försök att uppdatera gränsen för fel söknings rad och uppdatera data.
- **Orsaker**: JOIN-villkoret matchade inte några rader eller resulterade i ett stort antal null-värden under förhands granskningen.
- **Rekommendation**: gå till fel söknings inställningar och öka antalet rader i käll rad gränsen. Kontrol lera att du har valt och Azure IR med ett tillräckligt stort data flödes kluster för att hantera mer data.


## <a name="general-troubleshooting-guidance"></a>Allmän fel söknings vägledning

1. Kontrol lera statusen för dina data uppsättnings anslutningar. I varje käll-och mottagar omvandling kan du gå till den länkade tjänsten för varje data uppsättning som du använder och testa anslutningarna.
1. Kontrol lera status för dina fil-och tabell anslutningar från data flödes designern. Växla vid fel sökning och klicka på Förhandsgranska data på käll omvandlingarna för att säkerställa att du kan komma åt dina data.
1. Om allt ser bra ut från data förhands granskningen, går du till pipeline-designern och sätter ditt data flöde i en pipeline-aktivitet. Felsök pipelinen för ett slut punkt till slut punkts test.

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:
*  [Data Factory blogg](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Sidan Microsoft Q&en fråga](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestanda guide för ADF-mappning av data flöden](concepts-data-flow-performance.md)
