---
title: Felsöka dirigering av pipelines och utlösare i ADF
description: Använd olika metoder för att felsöka problem med pipeline-utlösare i ADF
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: ed3728513820da9f4ef85d44cac983dc09c3fc7d
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97521852"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-adf"></a>Felsöka dirigering av pipelines och utlösare i ADF

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En pipelinekörning i Azure Data Factory definierar en instans av en pipeline-åtgärd. Du kan till exempel ha en pipeline som körs kl. 8:00, 9:00 AM och 10:00. I det här fallet finns det tre separata körningar av pipelinen eller pipeline-körningarna. Varje pipelinekörning har ett unikt pipelinekörnings-ID. Ett körnings-ID är ett GUID (globalt unik identifierare) som definierar en viss pipeline-körning.

Pipelinekörningar instansieras normalt genom att skicka argument till parametrar som du definierar i pipelinen. Du kan köra en pipeline antingen manuellt eller via en utlösare. Mer information finns [i pipeline-körning och utlösare i Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Vanliga problem, orsaker och lösningar

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>Pipeline med Azure Function genererar fel med privat slut punkts anslutning
 
#### <a name="issue"></a>Problem
För vissa sammanhang har du ADF och Azure Funktionsapp som körs på en privat slut punkt. Du försöker få en pipeline som samverkar med Azure-Funktionsapp för att fungera. Du har provat tre olika metoder, men ett returnerar fel `Bad Request` , de andra två metoderna returnerar `103 Error Forbidden` .

#### <a name="cause"></a>Orsak 
ADF stöder för närvarande inte en privat slut punkts koppling för Azure Funktionsapp. Detta bör vara orsaken till varför Azure Funktionsapp avvisar anropen eftersom det skulle konfigureras för att endast tillåta anslutningar från en privat länk.

#### <a name="resolution"></a>Lösning
Du kan skapa en privat slut punkt av typen **PrivateLinkService** och tillhandahålla appens DNS-funktion och anslutningen bör fungera.

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>Pipeline-körningen stoppas, men övervakaren visar fortfarande förlopps status

#### <a name="issue"></a>Problem
Ofta när du avvisar en pipeline-körning visar pipelinen övervakningen fortfarande förlopps status. Detta inträffar på grund av ett problem i cachen i webbläsaren och du har inte rätt filter för övervakning.

#### <a name="resolution"></a>Lösning
Uppdatera webbläsaren och Använd rätt filter för övervakning.
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>Kopiera pipeline-problem – hittade fler kolumner än förväntat kolumn antal (DelimitedTextMoreColumnsThanDefined)

#### <a name="issue"></a>Problem  
Om filerna i en viss mapp som du kopierar innehåller filer med olika scheman, till exempel variabel antal kolumner, olika avgränsare, citat teckens inställningar eller vissa data problem, kommer ADF-pipeline att köras i det här felet:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>Lösning
Välj alternativet "binär kopia" när du skapar Kopiera data-aktiviteten. På så sätt kan du, för Mass kopiering eller migrering av data från en Data Lake till en annan, med alternativet **Binary** , inte öppna filerna för att läsa schemat, men bara behandla varje fil som binär och kopiera dem till den andra platsen.

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>Pipelinen körs inte när kapacitets gränsen för integration Runtime har uppnåtts

#### <a name="issue"></a>Problem
Felmeddelande:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

Felet indikerar begränsningen av per integration runtime, som för närvarande är 50. Mer information finns i [begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) .

Om du kör stor mängd data flöde med samma integration runtime samtidigt kan det orsaka den här typen av fel.

#### <a name="resolution"></a>Lösning 
- Separera de här pipelinen för att köra en annan utlösnings tid.
- Skapa en ny integrerings körning och dela dessa pipeliner över flera integrerings körningar.

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>Övervaka fel i pipelinen med jämna mellanrum

#### <a name="issue"></a>Problem
Det finns ofta ett behov av att övervaka ADF-pipeliner i intervall, på 5 minuter. Du kan fråga efter och filtrera pipelinen som körs från en data fabrik med hjälp av slut punkten. 

#### <a name="recommendation"></a>Rekommendation
1. Konfigurera en Azure Logic-app för att fråga alla misslyckade pipeliner var femte minut.
2. Sedan kan du rapportera incidenter till vårt biljett system enligt [QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

#### <a name="reference"></a>Referens
- [Externt-skicka meddelanden från ADF](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>Hantera fel och fel på aktivitets nivå i pipelines

#### <a name="issue"></a>Problem
Azure Data Factory Orchestration tillåter villkorlig logik och gör det möjligt för användaren att ta olika sökvägar baserat på resultat från en tidigare aktivitet. Det tillåter fyra villkorliga sökvägar: "vid lyckad (standard pass)", "vid haveri", "vid slut för ande" och "vid hoppa över". Användning av olika sökvägar tillåts.

Azure Data Factory definierar slutförd körning och misslyckad pipeline-körning enligt följande:

- Utvärdera resultatet för alla aktiviteter på lövnivå. Om en löv aktivitet hoppades över, utvärderar vi dess överordnade aktivitet i stället.
- Pipeline-resultatet fungerar om och bara om alla löv lyckas.

#### <a name="recommendation"></a>Rekommendation
- Implementera kontroller på aktivitets nivå efter [hur du hanterar pipelines fel och fel](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
- Använd Azure Logic app för att övervaka pipelines i regelbundna intervall efter [fråga från DataFactory]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Sidan Microsoft Q&en fråga](/answers/topics/azure-data-factory.html)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)