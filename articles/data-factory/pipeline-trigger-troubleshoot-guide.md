---
title: Felsöka dirigering av pipelines och utlösare i Azure Data Factory
description: Använd olika metoder för att felsöka problem med pipeline-utlösare i Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 03/13/2021
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 72f2a5eec25b9acc2aedd7b006fe3380141781c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563420"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Felsöka dirigering av pipelines och utlösare i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En pipelinekörning i Azure Data Factory definierar en instans av en pipeline-åtgärd. Anta till exempel att du har en pipeline som körs kl. 8:00, 9:00 AM och 10:00. I det här fallet finns det tre separata pipeline-körningar. Varje pipelinekörning har ett unikt pipelinekörnings-ID. Ett körnings-ID är en globalt unik identifierare (GUID) som definierar den specifika pipeline-körningen.

Pipelinekörningar instansieras normalt genom att skicka argument till parametrar som du definierar i pipelinen. Du kan köra en pipeline antingen manuellt eller genom att använda en utlösare. Mer information finns i [pipeline-körning och utlösare i Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Vanliga problem, orsaker och lösningar

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>En Azure Functions app-pipeline genererar ett fel med anslutning till privat slut punkt
 
Du har Data Factory och en Azure Function-app som körs på en privat slut punkt. Du försöker köra en pipeline som samverkar med Function-appen. Du har provat tre olika metoder, men ett returnerar fel "felaktig begäran", och de andra två metoderna returnerar "103-fel förbjuden".

**Orsak**

Data Factory har för närvarande inte stöd för en privat slut punkts koppling för Function-appar. Azure Functions avvisar anrop eftersom det har kon figurer ATS för att endast tillåta anslutningar från en privat länk.

**Lösning**

Skapa en **PrivateLinkService** -slutpunkt och ange din Function-appens DNS.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>En pipeline-körning avbryts men övervakaren visar fortfarande förlopps status

**Orsak**

När du avbryter en pipeline-körning visar pipelinen ofta fortfarande status för förloppet. Detta inträffar på grund av ett problem med webbläsarens cacheminne. Du kanske inte har rätt övervaknings filter.

**Lösning**

Uppdatera webbläsaren och tillämpa rätt övervaknings filter.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Du ser ett "DelimitedTextMoreColumnsThanDefined"-fel när du kopierar en pipeline
 
 **Orsak**
 
Om en mapp som du kopierar innehåller filer med olika scheman, t. ex. variabel antal kolumner, olika avgränsare, citat teckens inställningar eller vissa data problem kan Data Factory pipelinen leda till det här felet:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Lösning**

Välj alternativet för **binär kopia** när du skapar kopierings aktiviteten. På så sätt kan du, för Mass kopiering eller migrering av data från ett data Lake till ett annat, Data Factory inte öppna filerna för att läsa schemat. I stället behandlar Data Factory varje fil som binär och kopierar den till den andra platsen.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>En pipeline-körning fungerar inte när du når kapacitets gränsen för integrerings körningen för data flödet

**Problem**

Felmeddelande:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Orsak**

Du har nått kapacitets gränsen för integration Runtime. Du kanske kör en stor mängd data flöde genom att använda samma integration runtime på samma gång. Se [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) för mer information.

**Lösning**
 
- Kör dina pipelines vid olika utlösnings tider.
- Skapa en ny integrerings körning och dela dina pipeliner över flera integrerings körningar.

### <a name="how-to-perform-activity-level-errors-and-failures-in-pipelines"></a>Så här utför du fel och fel på aktivitets nivå i pipelines

**Orsak**

Azure Data Factory Orchestration tillåter villkorlig logik och gör det möjligt för användarna att ta olika sökvägar baserat på resultatet av en tidigare aktivitet. Det tillåter fyra villkorliga sökvägar: när det är **klart** (standard pass) vid **haverining** **, och** **vid hoppa över**. 

Azure Data Factory utvärderar resultatet av alla aktiviteter på lövnivå. Pipeline-resultat fungerar bara om alla löv lyckas. Om en löv aktivitet hoppades över, utvärderar vi dess överordnade aktivitet i stället. 

**Lösning**

* Implementera kontroller på aktivitets nivå genom [att följa hur du hanterar pipelines fel och fel](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
* Använd Azure Logic Apps för att övervaka pipeliner med jämna mellanrum efter [fråga efter fabrik](/rest/api/datafactory/pipelineruns/querybyfactory).
* [Övervaka pipeline visuellt](./monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Så här övervakar du fel i pipelinen med jämna mellanrum

**Orsak**

Du kan behöva övervaka misslyckade Data Factory pipeliner i intervall, på 5 minuter. Du kan fråga efter och filtrera pipelinen som körs från en data fabrik med hjälp av slut punkten. 

**Lösning**
* Du kan konfigurera en Azure Logic-app för att fråga alla misslyckade pipeliner var femte minut, enligt beskrivningen i [fråga efter fabrik](/rest/api/datafactory/pipelineruns/querybyfactory). Sedan kan du rapportera incidenter till ditt biljett system.
* [Övervaka pipeline visuellt](./monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>Graden av Parallel-ökning resulterar inte i högre data flöde

**Orsak** 

Graden av parallellitet *är i* själva verket den högsta graden av parallellitet. Vi kan inte garantera att ett angivet antal körningar sker samtidigt, men den här parametern garanterar att vi aldrig hamnar ovanför det angivna värdet. Du bör se detta som en gräns för att utnyttjas när du styr samtidig åtkomst till dina källor och mottagare.

Kända *fakta om* Förkunskap
 * I förvars egenskap kallas batch Count (n), där standardvärdet är 20 och Max värdet är 50.
 * Batch Count, n, används för att konstruera n köer. Senare kommer vi att diskutera lite information om hur dessa köer skapas.
 * Varje kö körs sekventiellt, men du kan ha flera köer som körs parallellt.
 * Köerna skapas i förväg. Det innebär att det inte finns någon ombalansering av köerna under körningen.
 * När som helst har du minst ett objekt som ska bearbetas per kö. Det innebär att de flesta n objekt bearbetas vid en bestämd tidpunkt.
 * Den totala bearbetnings tiden motsvarar bearbetnings tiden för den längsta kön. Det innebär att den förgrunds aktiviteten är beroende av hur köerna konstrueras.
 
**Lösning**

 * Du bör inte använda *SetVariable* -aktivitet i *för varje* som körs parallellt.
 * Med hänsyn till hur köerna är konstruerade kan kunden förbättra prestanda genom att ställa in flera *foreaches* där varje förvars bedömning kommer att ha objekt med liknande bearbetnings tid. Detta säkerställer att långvariga körningar bearbetas parallellt i stället i tur och ordning.

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>Pipeline-status är i kö eller fastnar under en längre tid
 
 **Orsak**
 
 Detta kan inträffa av olika orsaker, t. ex. När det gäller samtidiga samtidighets gränser, avbrott i tjänsten, nätverks haverier och så vidare.
 
 **Lösning**
 
* Samtidiga gränser: om din pipeline har en concurrency-princip kontrollerar du att det inte finns några gamla pipelines-körningar. Den maximala pipeline-samtidigheten som tillåts i Azure Data Factory är 10 pipelines. 
* Övervaknings gränser: gå till arbets ytan för ADF-redigering, välj din pipeline och ta reda på om den har tilldelats en concurrency-egenskap. I så fall går du till övervakningsvyn och kontrollerar att ingenting pågår från de senaste 45 dagarna. Om något pågår kan du avbryta det och den nya pipeline-körningen ska starta.
* Tillfälliga problem: det är möjligt att din körning påverkades av ett tillfälligt nätverks problem, auktoriseringsfel, drifts avbrott och tjänster.  I så fall har Azure Data Factory en intern återställnings process som övervakar alla körningar och startar dem när ett meddelande om att något har gått fel. Den här processen sker var 1: a timme, så om din körning har fastnat i mer än en timme skapar du ett support ärende.
 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>Längre start tider för aktiviteter i ADF-kopiering och data flöde

**Orsak**

Detta kan inträffa om du inte har implementerat Time to Live-funktionen för data flöde eller optimerade SHIR.

**Lösning**

* Om varje enskild kopieringsaktivitet tar upp till två minuter att starta och problemet huvudsakligen inträffar med en VNet-anslutning (kontra Azure IR) kan det röra sig om ett problem med kopieringsprestanda. Om du vill se fel söknings stegen går du till [Kopiera prestanda förbättring.](./copy-activity-performance-troubleshooting.md)
* Du kan använda Time to Live-funktionen för att minska kluster start tiden för data flödes aktiviteter. Granska [integration runtime för data flöde.](./control-flow-execute-data-flow-activity.md#data-flow-integration-runtime)

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>Träffar på kapacitets problem i SHIR (egen värd Integration Runtime)
 
 **Orsak**
 
Detta kan inträffa om du inte har skalat upp SHIR per arbets belastning.

**Lösning**

* Om du stöter på ett kapacitets problem från SHIR ska du uppgradera den virtuella datorn för att öka noden för att utjämna aktiviteterna. Om du får ett fel meddelande om ett allmänt underordnat IR-fel eller fel, en IR-uppgradering med egen värd eller problem med IR-anslutning med egen värd, som kan generera en lång kö, går du till [Felsöka integration runtime med egen värd.](./self-hosted-integration-runtime-troubleshoot-guide.md)

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>Fel meddelanden på grund av långa köer för ADF-kopiering och data flöde

**Orsak**

Fel meddelanden om långa köer kan visas av olika orsaker. 

**Lösning**
* Om du får ett fel meddelande från en källa eller ett mål via kopplingar, som kan generera en lång kö, går du till [fel söknings guide för anslutning.](./connector-troubleshoot-guide.md)
* Om du får ett fel meddelande om att mappa data flödet, som kan generera en lång kö, går du till [fel söknings guide för data flöden.](./data-flow-troubleshoot-guide.md)
* Om du får ett fel meddelande om andra aktiviteter, till exempel Databricks, anpassade aktiviteter eller HDI, som kan generera en lång kö, går du till [fel söknings guide för aktiviteter.](./data-factory-troubleshoot-guide.md)
* Om du får ett fel meddelande om att köra SSIS-paket, som kan generera en lång kö, går du till [fel söknings guiden för Azure-SSIS för paket körning](./ssis-integration-runtime-ssis-activity-faq.md) och [integration runtime fel söknings guide för hantering.](./ssis-integration-runtime-management-troubleshoot.md)


## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Sidan Microsoft Q&en fråga](/answers/topics/azure-data-factory.html)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)