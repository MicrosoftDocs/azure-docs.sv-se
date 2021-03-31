---
title: Kunskaps lagrings koncept
titleSuffix: Azure Cognitive Search
description: Skicka berikade dokument till Azure Storage där du kan visa, ändra form på och använda berikade dokument i Azure Kognitiv sökning och i andra program.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3ec556c6198a00f217568f6591bd4b43c7fc743e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88924307"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Kunskaps lager i Azure Kognitiv sökning

Kunskaps lager är en funktion i Azure Kognitiv sökning som sparar utdata från en [AI-pipeline](cognitive-search-concept-intro.md) för oberoende analys eller underordnad bearbetning. Ett *berikat dokument* är en pipeline-utdata som skapas från innehåll som har extraherats, strukturer ATS och analyser ATS med hjälp av AI-processer. I en standard-AI-pipeline är berikade dokument övergående, som endast används vid indexering och sedan ignoreras. Om du väljer att skapa ett kunskaps lager kan du behålla de omfattande dokumenten. 

Om du har använt kognitiva färdigheter tidigare vet du redan att *färdighetsuppsättningar* flyttar ett dokument genom en sekvens av anrikninger. Resultatet kan vara ett sökindex eller prognoser i ett kunskaps lager. De två utmatningarna, Sök indexet och kunskaps lagret är produkter av samma pipeline. härleds från samma indata, men resulterar i utdata som är strukturerade, lagrade och används på olika sätt.

Fysiskt är ett kunskaps lager [Azure Storage](../storage/common/storage-account-overview.md), antingen Azure Table Storage, Azure Blob Storage eller båda. Alla verktyg eller processer som kan ansluta till Azure Storage kan använda innehållet i ett kunskaps lager.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=235&end=426]


![Kunskaps lager i Pipeline-diagram](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Kunskaps lager i Pipeline-diagram")

## <a name="benefits-of-knowledge-store"></a>Fördelar med kunskaps lager

I ett kunskaps lager får du struktur, kontext och faktiskt innehåll – uppnår från ostrukturerade och delvis strukturerade datafiler som blobbar, bildfiler som har genomgått analyser eller till och med strukturerade data som har ändrats till nya formulär. I en [steg-för-steg-genom gång](knowledge-store-create-rest.md)kan du se hur ett kompakt JSON-dokument är partitionerat i under strukturer, rekonstituerat i nya strukturer och på annat sätt görs tillgängligt för efterföljande processer som Machine Learning och data science-arbetsbelastningar.

Även om det är användbart att se vad en AI-pipeline kan skapa, är det möjligt att ändra form på data i en kunskaps butik. Du kan börja med en grundläggande färdigheter och sedan iterera över den för att lägga till ökande struktur nivåer, som du sedan kan kombinera i nya strukturer, i andra appar förutom Azure Kognitiv sökning.

Räknat, fördelarna med kunskaps lager inkluderar följande:

+ Använd omfattande dokument i andra [analys-och rapporterings verktyg](#tools-and-apps) än Sök. Power BI med Power Query är ett övertygande val, men alla verktyg och appar som kan ansluta till Azure Storage kan hämta från ett kunskaps lager som du skapar.

+ Förfina en AI-indexerings pipeline vid fel sökning av steg och färdigheter definitioner. I ett kunskaps lager visas produkten av en färdigheter-definition i en AI-indexerad pipeline. Du kan använda dessa resultat för att utforma en bättre färdigheter, eftersom du kan se exakt vad som kan se ut. Du kan använda [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) i Azure Storage om du vill visa innehållet i ett kunskaps lager.

+ Forma data till nya formulär. Omformningen är att kodifiera i färdighetsuppsättningar, men punkten är att en färdigheter nu kan tillhandahålla den här funktionen. [Formaren-kunskapen](cognitive-search-skill-shaper.md) i Azure kognitiv sökning har utökats för att kunna hantera den här uppgiften. Med hjälp av omformningen kan du definiera en projektion som anpassas efter den avsedda användningen av data medan du bevarar relationer.

> [!Note]
> Är du nybörjare på AI-anrikning och kognitiva färdigheter? Azure Kognitiv sökning integreras med Cognitive Services vision-och språk funktioner för att extrahera och utöka källdata med OCR (optisk tecken läsning) över bildfiler, enhets igenkänning och nyckel fras extrahering från textfiler med mera. Mer information finns i [AI-anrikning i Azure kognitiv sökning](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Fysisk lagring


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=455&end=542]


Det fysiska uttrycket för ett kunskaps lager har ledas genom en `projections` `knowledgeStore` definitions element i en färdigheter. Projektionen definierar en struktur för utdata så att den matchar den avsedda användningen.

Projektioner kan ledas som tabeller, objekt eller filer.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Den typ av projektion som du anger i den här strukturen avgör vilken typ av lagring som används av kunskaps lagret.

+ Table Storage används när du definierar `tables` . Definiera en tabell projektion när du behöver rapporterings strukturer i tabell form för indata till analys verktyg eller exportera som data ramar till andra data lager. Du kan ange flera `tables` för att hämta en delmängd eller ett tvärsnitt av de omfattande dokumenten. I samma projektions grupp bevaras tabell relationer så att du kan arbeta med alla.

+ Blob Storage används när du definierar `objects` eller `files` . Den fysiska representationen av en `object` är en hierarkisk JSON-struktur som representerar ett berikat dokument. En `file` är en avbildning som extraheras från ett dokument, överfördes till Blob Storage.

Ett enda projektions objekt innehåller en uppsättning `tables` , `objects` , `files` och för många scenarier. det kan vara tillräckligt med att skapa en projektion. 

Det är dock möjligt att skapa flera uppsättningar av `table` - `object` - `file` projektioner, och du kan göra det om du vill ha olika data relationer. I en mängd är data relaterade, förutsatt att dessa relationer finns och kan identifieras. Om du skapar ytterligare uppsättningar är dokumenten i varje grupp aldrig relaterade. Ett exempel på hur du kan använda flera projektions grupper kan vara om du vill att samma data ska användas med ditt online-system och det måste vara representerat på ett särskilt sätt. du vill även att samma data ska användas i en pipeline för data vetenskap som är representerad annorlunda.

## <a name="requirements"></a>Krav 

[Azure Storage](../storage/index.yml) krävs. Den tillhandahåller fysisk lagring. Du kan använda Blob Storage, Table Storage eller båda. Blob Storage används för informativt omfattande dokument, vanligt vis när utdata ska gå till efterföljande processer. Table Storage är till för segment av omfattande dokument, som ofta används för analys och rapportering.

[Färdigheter](cognitive-search-working-with-skillsets.md) krävs. Den innehåller `knowledgeStore` definitionen och bestämmer strukturen och sammansättningen för ett berikat dokument. Du kan inte skapa ett kunskaps lager med en tom färdigheter. Du måste ha minst en färdighet i en färdigheter.

[Indexerare](search-indexer-overview.md) krävs. En färdigheter anropas av en indexerare som styr körningen. Indexerare levereras med en egen uppsättning krav och attribut. Flera av dessa attribut har direkt försetts med ett kunskaps lager:

+ Indexerare kräver en [Azure-datakälla som stöds](search-indexer-overview.md#supported-data-sources) (pipelinen som slutligen skapar kunskaps lagret som börjar genom att hämta data från en källa som stöds i Azure). 

+ Indexerare kräver ett sökindex. En indexerare kräver att du anger ett index schema, även om du inte planerar att använda den. Ett minimalt index har ett sträng fält som anges som nyckel.

+ Indexerare tillhandahåller valfria fält mappningar som används för att ge alias till ett käll fält. Om en standard fält mappning behöver ändras (om du vill använda ett annat namn eller en annan typ) kan du skapa en [fält mappning](search-indexer-field-mappings.md) i en indexerare. För kunskaps lagers utdata kan målet vara ett fält i ett BLOB-objekt eller en tabell.

+ Indexerare har scheman och andra egenskaper, till exempel mekanismer för ändrings identifiering som tillhandahålls av olika data källor, kan också användas i ett kunskaps lager. Du kan till exempel [Schemalägga](search-howto-schedule-indexers.md) berikning med jämna mellanrum för att uppdatera innehållet. 

## <a name="how-to-create-a-knowledge-store"></a>Så här skapar du ett kunskaps lager

Om du vill skapa kunskaps lager använder du portalen eller REST API ( `api-version=2020-06-30` ).

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Guiden **Importera data** innehåller alternativ för att skapa ett kunskaps lager. För inledande utforskning [skapar du din första kunskaps lagring i fyra steg](knowledge-store-connect-power-bi.md).

1. Välj en data källa som stöds.

1. Ange berikning: Anslut en resurs, Välj kunskaper och ange ett kunskaps lager. 

1. Skapa ett index schema. Guiden kräver det och kan härleda en åt dig.

1. Kör guiden. Extrahering, berikning och lagring sker i det här sista steget.

### <a name="use-create-skillset-rest-api"></a>Använda skapa färdigheter (REST API)]

En `knowledgeStore` definieras i en [färdigheter](cognitive-search-working-with-skillsets.md), som i sin tur anropas av en [indexerare](search-indexer-overview.md). Under anrikningen skapar Azure Kognitiv sökning ett utrymme i ditt Azure Storage-konto och projekt utgör de dokument som blobar eller i tabeller, beroende på din konfiguration.

REST API är en mekanism med vilken du kan skapa ett kunskaps lager program mässigt. Ett enkelt sätt att utforska är att [skapa ditt första kunskaps lager med Postman och REST API](knowledge-store-create-rest.md).

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Så här ansluter du till verktyg och appar

När det finns lagrings utrymme kan alla verktyg och tekniker som ansluter till Azure Blob eller Table Storage användas för att utforska, analysera eller använda innehållet. Följande lista är en start:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) för att visa en omfattande dokument struktur och innehåll. Överväg detta som ditt bas linje verktyg för att Visa kunskaps lager innehåll.

+ [Power BI](knowledge-store-connect-power-bi.md) för rapportering och analys. 

+ [Azure Data Factory](../data-factory/index.yml) för ytterligare modifiering.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API-referens

REST API version `2020-06-30` ger kunskaps lager genom ytterligare definitioner på färdighetsuppsättningar. Förutom referensen kan du läsa  [skapa ett kunskaps lager med Postman](knowledge-store-create-rest.md) för information om hur du anropar API: erna.

+ [Skapa färdigheter (API-version = 2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Uppdatera färdigheter (API-version = 2020-06-30)](/rest/api/searchservice/update-skillset)


## <a name="next-steps"></a>Nästa steg

Kunskaps lager erbjuder beständiga dokument, är användbara när du utformar en färdigheter, eller när du skapar nya strukturer och innehåll för användning av klient program som kan komma åt ett Azure Storage-konto.

Det enklaste sättet att skapa omfattande dokument är [via portalen](knowledge-store-create-portal.md), men du kan också använda Postman och REST API, vilket är mer användbart om du vill veta mer om hur objekt skapas och refereras till.

> [!div class="nextstepaction"]
> [Skapa ett kunskaps lager med Postman och REST](knowledge-store-create-rest.md)

Om du vill veta mer om projektioner, funktionerna och hur du [definierar dem i en färdigheter](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projektioner i ett kunskaps lager](knowledge-store-projection-overview.md)

En själv studie kurs som täcker avancerade projektions koncept som segmentering, infogad form och relationer, börjar med [definiera projektioner i ett kunskaps lager](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definiera projektioner i ett kunskaps lager](knowledge-store-projections-examples.md)