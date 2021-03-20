---
title: Skapa en kunskapsuppsättning
titleSuffix: Azure Cognitive Search
description: Definiera data extrahering, naturlig språk bearbetning eller bild analys steg för att utöka och extrahera strukturerad information från dina data för användning i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 39a7c92ca6c83684658cf767722698806ed994ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935457"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Så här skapar du en färdigheter i en pipeline för AI-anrikning i Azure Kognitiv sökning 

![indexerings steg](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "indexerings steg")

En färdigheter definierar de åtgärder som extraherar och utvärderar data så att de kan genomsökas. En färdigheter körs efter text-och bild innehållet extraheras från käll dokumenten och efter fält från käll dokumentet (valfritt) mappas till mål fälten i ett index eller kunskaps lager.

En färdigheter innehåller en eller flera *kognitiva färdigheter* som representerar en bestämd anriknings åtgärd, t. ex. Översättning av text, extrahering av nyckel fraser eller att utföra optisk tecken läsning från en bildfil. Om du vill skapa en färdigheter kan du använda [inbyggda kunskaper](cognitive-search-predefined-skills.md) från Microsoft eller anpassade kunskaper som innehåller modeller eller bearbetnings logik som du tillhandahåller (se [exempel: skapa en anpassad färdighet i en AI-pipeline](cognitive-search-create-custom-skill-example.md) för mer information).

I den här artikeln får du lära dig hur du skapar en rikare pipeline för de kunskaper du vill använda. En färdigheter är kopplad till en Azure Kognitiv sökning- [indexerare](search-indexer-overview.md). En del av pipeline-designen, som beskrivs i den här artikeln, konstruerar själva färdigheter. 

> [!NOTE]
> En annan del av pipeline-designen är att ange en indexerare som beskrivs i [Nästa steg](#next-step). En indexare-definition innehåller en referens till färdigheter, plus fält mappningar som används för att ansluta indata till utdata i mål indexet.

Viktiga punkter att komma ihåg:

+ Du kan bara ha en färdigheter per indexerare.
+ En färdigheter måste ha minst en färdighet.
+ Du kan skapa flera kunskaper av samma typ (till exempel varianter av en bild analys kunskaper).

## <a name="begin-with-the-end-in-mind"></a>Börja med slutet i åtanke

Ett rekommenderat inledande steg är att bestämma vilka data som ska extraheras från dina rå data och hur du vill använda dessa data i en Sök lösning. Att skapa en illustration av hela pipelinen kan hjälpa dig att identifiera nödvändiga steg.

Anta att du är intresse rad av att bearbeta en uppsättning ekonomiska analytikers kommentarer. För varje fil vill du extrahera företags namn och de allmänna sentiment i kommentarerna. Du kanske också vill skriva en anpassad berikare som använder Entitetssökning i Bings tjänsten för att hitta ytterligare information om företaget, till exempel vilken typ av verksamhet som företaget är förbrukat i. I princip vill du extrahera information som följande, som indexeras för varje dokument:

| post-text | tillverkare | sentiment | företags beskrivningar |
|--------|-----|-----|-----|
|exempel post| ["Microsoft", "LinkedIn"] | 0,99 | ["Microsoft Corporation är ett amerikanskt multinationellt teknik företag...", "LinkedIn är ett verksamhets-och arbetsorienteradt socialt nätverk..."]

Följande diagram illustrerar en hypotetisk anriknings pipeline:

![En hypotetisk anriknings pipeline](media/cognitive-search-defining-skillset/sample-skillset.png "En hypotetisk anriknings pipeline")


När du har en god uppfattning om vad du vill ha i pipelinen kan du uttrycka färdigheter som innehåller de här stegen. Funktionen färdigheter uttrycks när du laddar upp din index-definition till Azure Kognitiv sökning. Mer information om hur du överför indexeraren finns i [dokumentationen för indexeraren](/rest/api/searchservice/create-indexer).


I diagrammet sker steget för att *knäcka dokument* automatiskt. Azure Kognitiv sökning vet i princip hur man öppnar välkända filer och skapar ett *innehålls* fält som innehåller den text som extraheras från varje dokument. De vita rutorna är inbyggda och de prickade Entitetssökning i Bings rutan representerar en anpassad berikare som du skapar. Som illustreras innehåller färdigheter tre kunskaper.

## <a name="skillset-definition-in-rest"></a>Färdigheter-definition i REST

En färdigheter definieras som en matris med kunskaper. Varje kunskap definierar källan till sina indata och namnet på de utdata som produceras. Med hjälp av [create färdigheter REST API](/rest/api/searchservice/create-skillset)kan du definiera en färdigheter som motsvarar föregående diagram: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Skapa en kunskapsuppsättning

När du skapar en färdigheter kan du ange en beskrivning för att göra färdigheter själv dokumentning. En beskrivning är valfri, men användbart för att hålla reda på vad en färdigheter gör. Eftersom färdigheter är ett JSON-dokument som inte tillåter kommentarer, måste du använda ett- `description` element för detta.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Nästa del i färdigheter är en uppsättning kunskaper. Du kan tänka på varje färdighet som en primitiv av berikning. Varje färdighet utför en liten uppgift i denna pipeline för anrikning. Var och en tar indata (eller en uppsättning indata) och returnerar vissa utdata. I de följande avsnitten fokuserar vi på hur du anger inbyggda och anpassade kunskaper, länkar kunskaper tillsammans med indata och utdata. Indata kan komma från källdata eller från en annan färdighet. Utdata kan mappas till ett fält i ett sökindex eller användas som indata till en efterföljande färdighet.

## <a name="add-built-in-skills"></a>Lägg till inbyggda kunskaper

Nu ska vi titta på den första kunskapen, som är den inbyggda [kunskapen om enhets igenkänning](cognitive-search-skill-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Alla inbyggda kunskaper har `odata.type` , `input` , och `output` Egenskaper. Kunskapsbaserade egenskaper ger ytterligare information som är tillämplig för den kunskapen. För entitets igenkänning `categories` är en entitet mellan en fast uppsättning entitetstyper som den förtränade modellen kan identifiera.

* Varje färdighet bör ha en ```"context"``` . Kontexten representerar den nivå där åtgärder sker. I ovanstående kunskaper är kontexten hela dokumentet, vilket innebär att enhets igenkänningens färdighet anropas en gång per dokument. Utdata skapas också på den nivån. Mer specifikt ```"organizations"``` genereras som medlem i ```"/document"``` . I underordnade kunskaper kan du referera till den nyligen skapade informationen som ```"/document/organizations"``` .  Om ```"context"``` fältet inte uttryckligen anges är standard kontexten dokumentet.

* Kunskapen har en indatatyp som kallas "text", med en inmatad källa till ```"/document/content"``` . Kunskapen (enhets igenkänning) arbetar i *innehålls* fältet för varje dokument, vilket är ett standard fält som skapas av Azure Blob-indexeraren. 

* Kunskapen har en utmatning som kallas ```"organizations"``` . Utdata finns bara under bearbetning. Om du vill kedja dessa utdata till en efterföljande färdighets indata refererar du till utdata som ```"/document/organizations"``` .

* För ett visst dokument är värdet för ```"/document/organizations"``` en matris med organisationer som extraheras från texten. Exempel:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Vissa situationer anropar för att referera varje element i en matris separat. Anta till exempel att du vill skicka varje element ```"/document/organizations"``` separat till en annan kunskap (till exempel den anpassade Bing-enheten Sök berikare). Du kan referera till varje element i matrisen genom att lägga till en asterisk till sökvägen: ```"/document/organizations/*"``` 

Den andra kunskapen för sentiment-extraktion följer samma mönster som den första berikaren. Den tar sig ```"/document/content"``` som inmatad och returnerar en sentiment Poäng för varje innehålls instans. Eftersom du inte har angett ```"context"``` fältet explicit är resultatet (mySentiment) nu underordnat ```"/document"``` .

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Lägg till en anpassad kompetens

Återkalla strukturen för den anpassade Bing entitet search-berikaren:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Den här definitionen är en [anpassad färdighet](cognitive-search-custom-skill-web-api.md) som anropar ett webb-API som en del av anriknings processen. För varje organisation som identifieras av enhets igenkänningen anropar den här kunskapen ett webb-API för att hitta beskrivningen av organisationen. Dirigeringen av när du anropar webb-API: et och hur du kan flöda den mottagna informationen hanteras internt av anriknings motorn. Den initiering som krävs för att anropa det här anpassade API: et måste dock anges i JSON (till exempel URI, httpHeaders och förväntade indata). Information om hur du skapar ett anpassat webb-API för pipelinen finns i [så här definierar du ett anpassat gränssnitt](cognitive-search-custom-skill-interface.md).

Observera att fältet "context" är inställt på ```"/document/organizations/*"``` med en asterisk, vilket innebär att ett anriknings steg anropas *för varje* organisation under ```"/document/organizations"``` . 

Utdata, i det här fallet en företags beskrivning, genereras för varje organisation som identifieras. När du refererar till beskrivningen i ett underordnat steg (till exempel i extrahering av nyckel fraser) använder du sökvägen ```"/document/organizations/*/description"``` för att göra det. 

## <a name="add-structure"></a>Lägg till struktur

Färdigheter genererar strukturerad information från ostrukturerade data. Se följande exempel:

*"I sitt fjärde kvartal, loggade Microsoft in $1 100 000 000 i intäkter från LinkedIn, det sociala nätverk företaget köpte förra året. Förvärvet gör att Microsoft kan kombinera LinkedIn-funktioner med sina CRM-och Office-funktioner. Stockholders är fördelade med förloppet hittills. "*

Ett sannolikt resultat skulle vara en genererad struktur som liknar följande bild:

![Exempel på utdata-struktur](media/cognitive-search-defining-skillset/enriched-doc.png "Exempel på utdata-struktur")

Fram till nu har den här strukturen endast internt, endast minne och används i Azure Kognitiv sökning index. Tillägget av ett kunskaps lager ger dig ett sätt att spara formad berikare för användning utanför sökningen.

## <a name="add-a-knowledge-store"></a>Lägg till ett kunskaps lager

[Kunskaps lager](knowledge-store-concept-intro.md) är en funktion i Azure kognitiv sökning för att spara ditt förrikade dokument. Ett kunskaps lager som du skapar, som backas upp av ett Azure Storage-konto, är den lagrings plats där dina berikade data hamnar. 

En kunskaps lager definition har lagts till i en färdigheter. En genom gång av hela processen finns i [skapa ett kunskaps lager i rest](knowledge-store-create-rest.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Du kan välja att spara de berikade dokumenten som tabeller med hierarkiska relationer bevarade eller som JSON-dokument i Blob Storage. Utdata från någon av färdigheterna i färdigheter kan användas som indata för projektionen. Om du vill projicera data i en speciell form kan den uppdaterade [formaren-kompetensen](cognitive-search-skill-shaper.md) nu modellera komplexa typer som du kan använda. 

<a name="next-step"></a>

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med pipelinen och färdighetsuppsättningar kan du fortsätta med [hur du refererar till kommentarer i en färdigheter](cognitive-search-concept-annotations-syntax.md) eller [hur du mappar utdata till fält i ett index](cognitive-search-output-field-mapping.md).