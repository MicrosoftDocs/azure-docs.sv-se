---
title: Färdigheter-koncept och arbets flöde
titleSuffix: Azure Cognitive Search
description: Färdighetsuppsättningar är där du skapar en pipeline för AI-anrikning i Azure Kognitiv sökning. Lär dig viktiga begrepp och information om färdigheter-kompositionen.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: b5a893ee1923ba4b2bec53b20fb164337bd65902
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96558121"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Färdigheter-koncept i Azure Kognitiv sökning

Den här artikeln är för utvecklare som behöver en djupare förståelse för färdigheter-koncept och-sammansättning, och som förutsätter att AI-anrikningen är välbekant. Om du är nybörjare på det här konceptet börjar du med [AI-anrikning i Azure kognitiv sökning](cognitive-search-concept-intro.md).

## <a name="introducing-skillsets"></a>Introduktion till färdighetsuppsättningar

En färdigheter är en återanvändbart resurs i Azure Kognitiv sökning som är kopplad till en indexerare, och den specificerar en samling kunskaper som används för att analysera, transformera och berika text-eller bild innehåll under indexeringen. Färdigheter har indata och utdata, och ofta blir resultatet av en färdighet indata för en annan i en kedja eller sekvens med processer.

En färdigheter har tre huvud egenskaper:

+ `skills`, en osorterad samling kunskaper för vilka plattformen fastställer körnings ordningen baserat på de indata som krävs för varje färdighet.
+ `cognitiveServices`, nyckeln för en Cognitive Services resurs som utför bild-och text bearbetning för färdighetsuppsättningar som innehåller inbyggda kunskaper.
+ `knowledgeStore`, (valfritt) ett Azure Storage konto där dina berikade dokument kommer att projiceras. Omfattande dokument används också av Sök index.

Färdighetsuppsättningar har skapats i JSON. Följande exempel är en något förenklad version av denna [hotell gransknings färdigheter](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json), som används för att illustrera koncept i den här artikeln. 

De två första färdigheterna visas nedan:

+ Kunskaps #1 är en [text delnings färdighet](cognitive-search-skill-textsplit.md) som accepterar innehållet i fältet "reviews_text" som indata och delar innehållet i "sidor" av 5000 tecken som utdata.
+ Kunskaps #2 är en [sentiment identifierings färdighet](cognitive-search-skill-sentiment.md) accepterar "Pages" som indata och skapar ett nytt fält med namnet "sentiment" som utdata som innehåller resultatet av sentiment-analys.


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> Du kan skapa komplexa färdighetsuppsättningar med slingor och förgreningar med hjälp av den [villkorliga kompetensen](cognitive-search-skill-conditional.md) för att skapa uttrycken. Syntaxen baseras på JSON- [pekarens](https://tools.ietf.org/html/rfc6901) Sök vägs notation, med några ändringar för att identifiera noder i berikande trädet. En `"/"` korsar en nivå som är lägre i trädet och  `"*"` fungerar som en for-each-operator i kontexten. I många exempel i den här artikeln visas syntaxen. 

### <a name="enrichment-tree"></a>Anriknings träd

Vid förloppet av [stegen i en anriknings pipeline](cognitive-search-concept-intro.md#enrichment-steps)följer innehålls bearbetningen den *dokument sprickors* fas där text och bilder extraheras från källan. Bild innehåll kan sedan dirigeras till färdigheter som anger bild bearbetning, medan text innehåll placeras i kö för text bearbetning. För käll dokument som innehåller stora mängder text kan du ange ett *tolknings läge* på indexeraren för att segmentera text i mindre segment för mer optimal bearbetning. 

![Kunskaps lager i Pipeline-diagram](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Kunskaps lager i Pipeline-diagram")

När ett dokument har berikats pipelinen visas det som ett träd med innehåll och tillhör ande berikare. Trädet instansieras som utdata från dokument sprickor.  Formatet för anriknings träd möjliggör anriknings pipelinen för att bifoga metadata till till och med primitiva data typer, men det är inte ett giltigt JSON-objekt, men kan projiceras i ett giltigt JSON-format. I följande tabell visas en status för ett dokument som anges i pipelinen:

|Data Source\Parsing läge|Standardvärde|JSON, JSON-linjer & CSV|
|---|---|---|
|Blob Storage|/document/content<br>/Document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|Ej tillämpligt |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|Ej tillämpligt|

 När färdigheter körs lägger de till nya noder i det berikande trädet. Dessa nya noder kan sedan användas som indata för underordnade kunskaper, projicera till kunskaps lagret eller mappa till index fält. Berikningar är inte föränderligt: när de har skapats går det inte att redigera noder. När din färdighetsuppsättningar får mer komplexa, så kommer ditt anriknings träd, men inte alla noder i anriknings trädet behöver göra det till indexet eller kunskaps lagret. 

Du kan selektivt bevara endast en delmängd av anrikningerna i indexet eller kunskaps lagret.

### <a name="context"></a>Kontext

Varje färdighet kräver en kontext. En kontext fastställer:

+ Antalet gånger som kompetensen körs, baserat på de valda noderna. Om du lägger till en i slutet av Sammanhangs värden av typen samling leder det till att en `/*` färdighet anropas en gång för varje instans i samlingen. 

+ Var i anriknings trädet läggs färdighets utmatningarna till. Utdata läggs alltid till i trädet som underordnade noder till kontextnoden. 

+ Figuren för indata. För samlingar med flera nivåer påverkar att ange kontexten till den överordnade samlingen formen på indata för kunskapen. Om du till exempel har ett berikande träd med en lista över länder/regioner, var och en med en lista med delstater som innehåller en lista med post nummer.

|Kontext|Indata|Inmatad form|Kompetens anrop|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |En lista över alla post nummer i landet/regionen |En gång per land/region |
|`/document/countries/*/states/*` |'/Document/countries/*/States/*/ZipCodes/* ' ' |En lista med post nummer i status | En gång per kombination av land/region och delstat|

## <a name="generate-enriched-data"></a>Generera utförliga data 

Med [hotell gransknings färdigheter](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) som en referens punkt ska vi titta på:

+ Hur anriknings trädet utvecklas med körningen av varje färdighet
+ Så här fungerar kontext och indata för att avgöra hur många gånger en färdighet körs
+ Hur indatatypen baseras på sammanhanget

Ett "dokument" i beriknings processen representerar en enda rad (en hotell granskning) i hotel_reviews.csv käll filen.

### <a name="skill-1-split-skill"></a>Kunskaps #1: dela kunskaper

När käll innehållet består av stora delar av text är det bra att dela upp det i mindre delar för att öka precisionen för språk, sentiment och nyckel fras identifiering. Det finns två tillgängliga kärnor: sidor och meningar. En sida består av ungefär 5000 tecken.

En text delnings färdighet är vanligt vis först i en färdigheter.

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

Med kunskaps kontexten `"/document/reviews_text"` utför den delade kunskapen en gång för `reviews_text` . Kunskaps resultatet är en lista där `reviews_text` är segmenterad i 5000-Character-segment. Resultatet från den delade kunskapen namnges `pages` och läggs till i berikande trädet. Med `targetName` funktionen kan du byta namn på en färdighets utmatning innan du lägger till den i berikande trädet.

Ditt anriknings träd har nu en ny nod som placerats under kunskaps kontexten. Den här noden är tillgänglig för alla kunskaper, projektioner och fält mappningar. Trädet ser ut så här:

![anriknings träd efter dokument sprickor](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Anriknings träd efter dokument sprickor och innan kompetens körning")

Rotnoden för alla-berikningar är `"/document"` . När du arbetar med BLOB-indexerare, `"/document"` kommer noden att ha underordnade noder till `"/document/content"` och `"/document/normalized_images"` . När du arbetar med CSV-data, som vi är i det här exemplet, kommer kolumn namnen att mappas till noderna under `"/document"` . 

För att få åtkomst till någon av de omfattande tillägg som läggs till i en nod av en färdighet behövs den fullständiga sökvägen för berikning. Om du till exempel vill använda texten från ```pages``` noden som inmatad till en annan färdighet måste du ange den som ```"/document/reviews_text/pages/*"``` .
 
 ![anriknings träd efter färdighets #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Anriknings träd efter att kunskaps #1 körts")

### <a name="skill-2-language-detection"></a>Identifiering av kunskaps #2 språk

Hotell gransknings dokument innehåller kundfeedback som uttrycks på flera språk. Språk identifierings kunskapen avgör vilket språk som används. Resultatet skickas sedan till extrahering av nyckel fraser och sentiment, vilket kan vara ett språk att tänka på när man identifierar sentiment och fraser.

Även om språket för språk identifiering är den tredje kunskaps #3s kompetensen som definierats i färdigheter, är det nästa färdighet att köra. Eftersom den inte blockeras genom att kräva några indata körs den parallellt med den tidigare kunskapen. Precis som den delade kunskapen som föregår den, anropas även språk identifierings kunskapen en gång för varje dokument. Ditt anriknings träd har nu en ny nod för språk.

 ![anriknings träd efter färdighets #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Anriknings träd efter att kunskaps #2 körts")
 
 ### <a name="skill-3-key-phrases-skill"></a>Kompetens #3: kompetens för nyckel fraser 

Med tanke på att `/document/reviews_text/pages/*` nyckel frasernas färdighet anropas en gång för varje objekt i `pages` samlingen. Utdata från färdigheten är en nod under det associerade sid elementet. 

 Nu bör du kunna titta på resten av färdigheterna i färdigheter och visualisera hur trädet i berikarna kommer att fortsätta att växa med körningen av varje färdighet. Vissa kunskaper, till exempel sammanfognings kunskaper och formaren-kunskaper, skapar också nya noder, men använder bara data från befintliga noder och skapar inte nya, nya-anrikninger.

![anriknings träd efter alla kunskaper](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Anriknings träd efter alla kunskaper")

Färgerna på kopplingarna i trädet ovan anger att berikarna har skapats av olika kunskaper och att noderna måste adresseras individuellt och kommer inte att ingå i det objekt som returneras när den överordnade noden väljs.

## <a name="save-enrichments"></a>Spara omfattande funktioner

I Azure Kognitiv sökning sparar en indexerare de utdata som skapas. En av utdata är alltid ett [sökbart index](search-what-is-an-index.md). Att ange ett index är ett krav, och när du bifogar en färdigheter, innehåller data som matas in av ett index innehållet i berikarna. Vanligt vis matas utdata av vissa kunskaper, till exempel nyckel fraser eller sentiment poäng, in i indexet i ett fält som skapats för detta ändamål.

Alternativt kan en indexerare även skicka utdata till ett [kunskaps lager](knowledge-store-concept-intro.md) för användning i andra verktyg eller processer. Ett kunskaps lager definieras som en del av färdigheter. Den här definitionen bestämmer om dina berikade dokument projiceras som tabeller eller objekt (filer eller blobbar). Tabell projektioner lämpar sig väl för interaktiv analys i verktyg som Power BI, medan filer och blobbar vanligt vis används i data vetenskap eller liknande processer. I det här avsnittet får du lära dig hur färdigheter-sammansättningen kan forma de tabeller eller objekt som du vill projicera.

### <a name="projections"></a>Projektioner

För innehåll som är riktat till ett kunskaps lager bör du fundera över hur innehållet är strukturerat. *Projektion* är en process för att välja noderna från det berikande trädet och skapa ett fysiskt uttryck för dem i kunskaps lagret. Projektioner är anpassade former i dokumentet (innehåll och anrikninger) som kan matas ut antingen som tabell-eller objekt projektioner. Mer information om hur du arbetar med projektioner finns i [arbeta med projektioner](knowledge-store-projection-overview.md).

![Alternativ för fält mappning](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Fält mappnings alternativ för anriknings pipeline")

### <a name="sourcecontext"></a>SourceContext

`sourceContext`Elementet används endast i färdighets inmatning och projektioner. Den används för att skapa kapslade objekt på flera nivåer. Du kan behöva skapa ett nytt objekt för att antingen skicka det som inmatat till en kunskap eller ett projekt i kunskaps lagret. Eftersom anriknings noder kanske inte är ett giltigt JSON-objekt i ett berikande träd och som refererar till en nod i trädet, returnerar bara det läget för noden när den skapades, med hjälp av användnings området som kunskaps inmatning eller projektioner, vilket innebär att du kan skapa ett välformulerat JSON-objekt. Med `sourceContext` kan du skapa ett hierarkiskt, anonymt typ objekt, vilket kräver flera kunskaper om du bara använde kontexten. 

Med `sourceContext` visas i följande exempel. Titta på de kunskaps utdata som genererade en anrikning för att avgöra om det är ett giltigt JSON-objekt och inte en primitiv typ.

### <a name="slicing-projections"></a>Projektion av segmentering

När du definierar en tabell projektions grupp kan en enda nod i ett berikande träd segmenteras i flera relaterade tabeller. Om du lägger till en tabell med en käll Sök väg som är underordnad en befintlig tabell projektion, kommer den resulterande underordnade noden inte att vara underordnad den befintliga Table-projektionen, utan kommer i stället att projiceras i den nya, relaterade tabellen. Med den här segmenterings metoden kan du definiera en enskild nod i en formaren-färdighet som kan vara källa för alla tabell projektioner. 

### <a name="shaping-projections"></a>Utformning av projektioner

Det finns två sätt att definiera en projektion:

+ Använd texten formaren skicklighet för att skapa en ny nod som är rotnoden för alla berikade projekt. I dina projektioner refererar du sedan bara till utdata från formaren-kompetensen.

+ Använd en infogad form som en projektion i själva projektions definitionen.

Formaren-metoden är mer utförlig än infogad form men säkerställer att alla Mutations träd finns i kunskaperna och att utdata är ett objekt som kan återanvändas. Med hjälp av infogad form givning kan du däremot skapa den form du behöver, men är ett anonymt objekt och är bara tillgängligt för projektionen som den har definierats för. Metoderna kan användas tillsammans eller separat. Färdigheter som skapas åt dig i Portal arbets flödet innehåller båda. Den använder en formaren-färdighet för tabell projektioner, men använder infogad form för att projicera nyckel fraserna.

Om du vill utöka exemplet kan du välja att ta bort infogad form och använda en formaren-färdighet för att skapa en ny nod för nyckel fraserna. För att skapa en form projicerad i tre tabeller, nämligen,, `hotelReviewsDocument` `hotelReviewsPages` och `hotelReviewsKeyPhrases` , beskrivs de två alternativen i följande avsnitt.

#### <a name="shaper-skill-and-projection"></a>Formaren-kunskaper och projektion

> [!Note]
> Några av kolumnerna från dokument tabellen har tagits bort från det här exemplet för det kortfattat.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

Med `tableprojection` noden som definieras i `outputs` avsnittet ovan kan vi nu använda segmenterings funktionen för att projicera delar av `tableprojection` noden i olika tabeller:

> [!Note]
> Detta är bara ett kodfragment av projektionen i kunskaps lager konfigurationen.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Inline Forming-projektioner

Den infogade form metoden kräver ingen formaren-kompetens eftersom alla former som behövs för projektionerna skapas vid den tidpunkt då de behövs. För att projicera samma data som i föregående exempel skulle alternativet infogad projektion se ut så här:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
En observation från båda metoderna är hur värdena i `"Keyphrases"` projiceras med hjälp av `"sourceContext"` . `"Keyphrases"`Noden, som innehåller en samling med strängar, är en underordnad sid text. Men eftersom projektioner kräver ett JSON-objekt och sidan är primitiv (sträng) `"sourceContext"` används den för att omsluta nyckel frasen till ett objekt med en namngiven egenskap. Den här tekniken gör att även primitiver kan projiceras oberoende av varandra.

## <a name="next-steps"></a>Nästa steg

I nästa steg ska du skapa din första färdigheter med kognitiva kunskaper.

> [!div class="nextstepaction"]
> [Skapa din första färdigheter](cognitive-search-defining-skillset.md).
