---
title: Tips för AI-anriknings design
titleSuffix: Azure Cognitive Search
description: Tips och fel sökning för att ställa in AI-pipeline i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 5882cc949d88e8c2a4102362cf5d2a3613e1d714
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99475496"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Tips för AI-anrikning i Azure Kognitiv sökning

Den här artikeln innehåller en lista med tips och knep för att hålla dig igång med AI-anrikning i Azure Kognitiv sökning. 

Om du inte redan har gjort det kan du gå igenom [självstudien: Lär dig hur du anropar AI-anriknings-API: er](cognitive-search-quickstart-blob.md) för att använda AI-anrikninger till en BLOB-datakälla.

## <a name="tip-1-start-with-a-small-dataset"></a>Tips 1: börja med en liten data mängd
Det bästa sättet att hitta problem är att snabbt öka den hastighet som du kan åtgärda problem med. Det bästa sättet att minska indexerings tiden är genom att minska antalet dokument som ska indexeras. 

Börja med att skapa en data källa med bara en fåtal av dokument/poster. Ditt dokument exempel bör vara en bättre representation av de olika dokument som kommer att indexeras. 

Kör ditt dokument exempel via pipeline från slut punkt till slut punkt och kontrol lera att resultaten uppfyller dina behov. När du är nöjd med resultaten kan du lägga till fler filer till data källan.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tips 2: kontrol lera att autentiseringsuppgifterna för data källan är korrekta
Anslutningen till data källan verifieras inte förrän du definierar en indexerare som använder den. Om du ser några fel som nämner att indexeraren inte kan komma åt data, kontrollerar du att:
- Anslutnings strängen är korrekt. Särskilt när du skapar SAS-token, se till att använda det format som förväntas av Azure Kognitiv sökning. Se [avsnittet Ange autentiseringsuppgifter](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) för att lära dig om de olika format som stöds.
- Behållar namnet i indexeraren är korrekt.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tips 3: se vad som fungerar även om det finns några problem
Ibland stoppar ett litet avbrott en indexerare i sina spår. Det är bra om du planerar att åtgärda problem en i taget. Men du kanske vill ignorera en viss typ av fel, så att indexeraren kan fortsätta så att du kan se vilka flöden som faktiskt fungerar.

I så fall kanske du vill be indexeraren att ignorera fel. Det gör du genom att ange *maxFailedItems* och *maxFailedItemsPerBatch* som-1 som en del av index definitions definitionen.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
> [!NOTE]
> Vi rekommenderar att du ställer in maxFailedItems, maxFailedItemsPerBatch på 0 för produktions arbets belastningar

## <a name="tip-4-use-debug-sessions-to-identify-and-resolve-issues-with-your-skillset"></a>Tips 4: Använd debug-sessioner för att identifiera och lösa problem med din färdigheter 

Debug-sessioner är en visuell redigerare som fungerar med en befintlig färdigheter i Azure Portal. Inom en felsökningssession kan du identifiera och lösa fel, verifiera ändringar och spara ändringar i en produktions färdigheter i AI-pipeline för anrikning. Detta är en förhands gransknings funktion som [läser dokumentationen](./cognitive-search-debug-session.md). Mer information om begrepp och hur du kommer igång finns i [Felsöka sessioner](./cognitive-search-tutorial-debug-sessions.md).

Debug-sessioner fungerar på ett enda dokument är ett bra sätt för dig att på ett iterativt sätt bygga mer komplexa anriknings pipeliner.

## <a name="tip-5-looking-at-enriched-documents-under-the-hood"></a>Tips 5: titta på omfattande dokument under huven 
Omfattande dokument är temporära strukturer som skapats under anrikning och sedan tas bort när bearbetningen är klar.

Om du vill ta en ögonblicksbild av det berikade dokumentet som skapades under indexeringen lägger du till ett fält som heter ```enriched``` i ditt index. Indexeraren placerar automatiskt en strängrepresentation i fältet för alla berikanden för det dokumentet.

Fältet ```enriched``` innehåller en sträng som är en logisk representation av det berikade dokumentet i minnet i JSON.  Fältets värde är emellertid ett giltigt JSON-dokument. Kvoter är undantagna, så du behöver aldrig ersätta `\"` med `"` för att visa dokumenten som formaterad JSON. 

Det omfattande fältet är avsett för fel sökning för att hjälpa dig att förstå den logiska formen på det innehåll som uttryck utvärderas mot. Du bör inte vara beroende av det här fältet för indexerings syfte.

Lägg till ett ```enriched``` fält som en del av index definitionen för fel söknings syfte:

#### <a name="request-body-syntax"></a>Begärandetextsyntax
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-6-expected-content-fails-to-appear"></a>Tips 6: förväntat innehåll visas inte

Innehåll som saknas kan vara resultatet av dokument som tas bort under indexeringen. Kostnads fria och grundläggande nivåer har låg gräns för dokument storlek. Alla filer som överskrider gränsen bryts vid indexering. Du kan söka efter borttagna dokument i Azure Portal. I instrument panelen för Sök tjänsten dubbelklickar du på panelen indexerare. Granska förhållandet mellan lyckade dokument indexerade. Om den inte är 100% kan du klicka på kvoten för att få mer information. 

Om problemet är relaterat till fil storlek kan du se ett fel som detta: "blobben \<file-name> " har storleken på \<file-size> byte, vilket överskrider den maximala storleken för dokument extrahering för den aktuella tjänst nivån. " Mer information om gränser för indexerare finns i [tjänst begränsningar](search-limits-quotas-capacity.md).

En andra orsak till att innehållet som Miss söker visas kan vara relaterade till fel vid indata/utdata-mappning. Till exempel är ett utmatnings mål namn "personer", men index fält namnet är "människor". Systemet kan returnera 201 slutförda meddelanden för hela pipelinen så att du tror att indexeringen är klar, när fältet i själva verket är tomt. 

## <a name="tip-7-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tips 7: utöka bearbetning bortom den maximala körnings tiden (24-timmarsformat)

Bild analys är i beräknings intensiva för ännu enkla fall, så när avbildningar är särskilt stora eller komplexa kan bearbetnings tiderna överskrida den högsta tillåtna tiden. 

Maximal kör tid varierar beroende på nivå: flera minuter på den kostnads fria nivån, 24-timmars indexering på fakturerbara nivåer. Om bearbetningen inte kan slutföras inom en 24-timmarsperiod för bearbetning på begäran, byter du till ett schema så att indexeraren kan hämta bearbetningen där den slutade. 

Indexering av schemalagda indexerare återupptas enligt schema vid det senast fungerande dokumentet. Genom att använda ett återkommande schema kan indexeraren arbeta på ett sätt genom att vänta på en serie timmar eller dagar, tills alla icke bearbetade bilder bearbetas. Mer information om schema-syntax finns i [Schemalägga en indexerare](search-howto-schedule-indexers.md).

> [!NOTE]
> Om en indexerare har angetts till ett visst schema men upprepade gånger Miss lyckas på samma dokument över och över igen varje gång den körs, kommer indexeraren att köras på ett mindre frekvent intervall (upp till max minst en gång var 24: e timme) tills den har lyckats med att göra ett nytt försök.  Om du tror att du har åtgärdat problemet som gjorde att indexeraren har fastnat vid en viss tidpunkt, kan du utföra en körning av indexeraren på begäran, och om detta sker fortsätter indexeraren med angivet schema intervall igen.

För portalbaserade indexeringar (enligt beskrivningen i snabb starten) väljer du alternativet "kör en gång"-alternativ begränsar bearbetningen till 1 timme ( `"maxRunTime": "PT1H"` ). Du kanske vill utöka bearbetnings fönstret till något längre.

## <a name="tip-8-increase-indexing-throughput"></a>Tips 8: öka indexerings data flödet

För [parallell indexering](search-howto-large-index.md)ska du placera dina data i flera behållare eller flera virtuella mappar i samma behållare. Skapa sedan flera data källor och index-par. Alla indexerare kan använda samma färdigheter och skriva till samma mål Sök index, så din Sökapp behöver inte vara medveten om denna partitionering.

## <a name="see-also"></a>Se även

+ [Snabb start: skapa en AI-pipeline i portalen](cognitive-search-quickstart-blob.md)
+ [Självstudie: Lär dig AI-anrikning REST API: er](cognitive-search-tutorial-blob.md)
+ [Så här konfigurerar du Blob-indexerare](search-howto-indexing-azure-blob-storage.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Så här mappar du omfattande fält till ett index](cognitive-search-output-field-mapping.md)
