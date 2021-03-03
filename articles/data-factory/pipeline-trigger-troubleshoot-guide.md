---
title: Felsöka dirigering av pipelines och utlösare i Azure Data Factory
description: Använd olika metoder för att felsöka problem med pipeline-utlösare i Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 2950c175acfdda33394c93649a3e2c41d1264dd2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706001"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Felsöka dirigering av pipelines och utlösare i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En pipelinekörning i Azure Data Factory definierar en instans av en pipeline-åtgärd. Anta till exempel att du har en pipeline som körs kl. 8:00, 9:00 AM och 10:00. I det här fallet finns det tre separata pipeline-körningar. Varje pipelinekörning har ett unikt pipelinekörnings-ID. Ett körnings-ID är en globalt unik identifierare (GUID) som definierar den specifika pipeline-körningen.

Pipelinekörningar instansieras normalt genom att skicka argument till parametrar som du definierar i pipelinen. Du kan köra en pipeline antingen manuellt eller genom att använda en utlösare. Mer information finns i [pipeline-körning och utlösare i Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Vanliga problem, orsaker och lösningar

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>En Azure Functions app-pipeline genererar ett fel med anslutning till privat slut punkt
 
Du har Data Factory och en Azure Function-app som körs på en privat slut punkt. Du försöker köra en pipeline som samverkar med Function-appen. Du har provat tre olika metoder, men ett returnerar fel "felaktig begäran", och de andra två metoderna returnerar "103-fel förbjuden".

**Orsak**: Data Factory för närvarande inte har stöd för en privat slut punkts koppling för Function-appar. Azure Functions avvisar anrop eftersom det har kon figurer ATS för att endast tillåta anslutningar från en privat länk.

**Lösning**: skapa en **PrivateLinkService** -slutpunkt och ange din Function app-DNS.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>En pipeline-körning avbryts men övervakaren visar fortfarande förlopps status

När du avbryter en pipeline-körning visar pipelinen ofta fortfarande status för förloppet. Detta inträffar på grund av ett problem med webbläsarens cacheminne. Du kanske inte har rätt övervaknings filter.

**Lösning**: uppdatera webbläsaren och tillämpa rätt övervaknings filter.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Du ser ett "DelimitedTextMoreColumnsThanDefined"-fel när du kopierar en pipeline
 
Om en mapp som du kopierar innehåller filer med olika scheman, t. ex. variabel antal kolumner, olika avgränsare, citat teckens inställningar eller vissa data problem kan Data Factory pipelinen leda till det här felet:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Lösning**: Välj alternativet för **binär kopia** när du skapar kopierings aktiviteten. På så sätt kan du, för Mass kopiering eller migrering av data från ett data Lake till ett annat, Data Factory inte öppna filerna för att läsa schemat. I stället behandlar Data Factory varje fil som binär och kopierar den till den andra platsen.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime"></a>En pipeline-körning fungerar inte när du når kapacitets gränsen för integration runtime

Felmeddelande:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Orsak**: du har nått kapacitets gränsen för integration Runtime. Du kanske kör en stor mängd data flöde genom att använda samma integration runtime på samma gång. Se [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) för mer information.

**Lösning**:
 
- Kör dina pipelines vid olika utlösnings tider.
- Skapa en ny integrerings körning och dela dina pipeliner över flera integrerings körningar.

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>Det finns fel och fel på aktivitets nivå i pipelines

Azure Data Factory Orchestration tillåter villkorlig logik och gör det möjligt för användarna att ta olika sökvägar baserat på resultatet av en tidigare aktivitet. Det tillåter fyra villkorliga sökvägar: när det är **klart** (standard pass) vid **haverining** **, och** **vid hoppa över**. 

Azure Data Factory utvärderar resultatet av alla aktiviteter på lövnivå. Pipeline-resultat fungerar bara om alla löv lyckas. Om en löv aktivitet hoppades över, utvärderar vi dess överordnade aktivitet i stället. 

**Lösning**

1. Implementera kontroller på aktivitets nivå genom [att följa hur du hanterar pipelines fel och fel](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
1. Använd Azure Logic Apps för att övervaka pipeliner med jämna mellanrum efter [fråga efter fabrik](/rest/api/datafactory/pipelineruns/querybyfactory).

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Så här övervakar du fel i pipelinen med jämna mellanrum

Du kan behöva övervaka misslyckade Data Factory pipeliner i intervall, på 5 minuter. Du kan fråga efter och filtrera pipelinen som körs från en data fabrik med hjälp av slut punkten. 

**Lösning** Du kan konfigurera en Azure Logic-app för att fråga alla misslyckade pipeliner var femte minut, enligt beskrivningen i [fråga efter fabrik](/rest/api/datafactory/pipelineruns/querybyfactory). Sedan kan du rapportera incidenter till ditt biljett system.

Mer information finns i [skicka meddelanden från Data Factory, del 2](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/).

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

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Sidan Microsoft Q&en fråga](/answers/topics/azure-data-factory.html)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
