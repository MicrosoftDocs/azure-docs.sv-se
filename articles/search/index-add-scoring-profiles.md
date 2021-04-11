---
title: Öka Sök rankningen med hjälp av bedömnings profiler
titleSuffix: Azure Cognitive Search
description: Höj Sök ranknings poängen för Azure Kognitiv sökning resultat genom att lägga till bedömnings profiler.
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: afe56bb8637c9b2a88bda23944fd5097413fce97
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077728"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Lägga till rankningsprofiler i ett Azure Cognitive Search-index

*Poängsättning* beräknar en Sök Poäng för varje objekt i en rangordnad resultat uppsättning. Varje objekt i en Sök Resultat uppsättning tilldelas en Sök Poäng och rangordnas sedan högst till lägsta.

 Azure Kognitiv sökning använder standard poängsättning för att beräkna en första poäng, men du kan anpassa beräkningen genom en *bedömnings profil*. Med bedömnings profiler får du bättre kontroll över rankningen av objekt i Sök resultaten. Du kanske till exempel vill öka objekt baserat på deras intäkts potential, befordra nya objekt eller kanske förbättra objekt som har varit i lager för länge.  

 Följande video segment spolas framåt och hur Poäng profilerna fungerar i Azure Kognitiv sökning.
 
> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="scoring-profile-definitions"></a>Definitioner av bedömnings profiler

 En bedömnings profil är en del av index definitionen, som består av viktade fält, funktioner och parametrar.  

 I följande exempel visas en enkel profil med namnet "Geo" för att ge dig en uppfattning om vad en bedömnings profil ser ut. Den här en stärker objekt som har Sök termen i fältet **hotelName** . Den använder också `distance` funktionen för att prioritera objekt som ligger inom tio kilo meter på den aktuella platsen. Om någon söker på termen "Inn" och "Inn" ska vara en del av hotell namnet, kommer dokument som innehåller hotell med "Inn" inom en radie på 10 KM på den aktuella platsen att visas högre upp i Sök resultaten.  


```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 Om du vill använda den här bedömnings profilen formuleras din fråga för att ange profilen i frågesträngen. Lägg märke till frågeparametern i förfrågan i frågan nedan `scoringProfile=geo` .  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2020-06-30 
```  

 Den här frågan söker efter termen "Inn" och skickar den aktuella platsen. Observera att frågan innehåller andra parametrar, till exempel `scoringParameter` . Frågeparametrar beskrivs i [Sök dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents).  

 Klicka på [exempel](#bkmk_ex) om du vill granska ett mer detaljerat exempel på en bedömnings profil.  

## <a name="what-is-default-scoring"></a>Vad är standard Poängsättning?  
 Poängsättning beräknar en Sök Poäng för varje objekt i en rangordnad resultat uppsättning. Varje objekt i en Sök Resultat uppsättning tilldelas en Sök Poäng och rangordnas sedan högst till lägsta. Objekt med de högre poängen returneras till programmet. Som standard returneras de översta 50, men du kan använda `$top` parametern för att returnera ett mindre eller större antal objekt (upp till 1000 i ett enda svar).  

Sök poängen beräknas baserat på statistiska egenskaper för data och frågan. Azure Kognitiv sökning hittar dokument som innehåller Sök villkoren i frågesträngen (vissa eller alla, beroende på `searchMode` ) och prioriterar dokument som innehåller många instanser av Sök termen. Sök poängen går till ännu högre om termen är sällsynt över data indexet, men vanligt i dokumentet. Grunden för den här metoden för att beräkna relevans kallas [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) eller term frekvens – invertering av dokument frekvens.  

 Förutsatt att det inte finns någon anpassad sortering rangordnas resultatet efter Sök poäng innan de returneras till det anropande programmet. Om $top inte anges returneras 50 objekt med högst Sök resultat.  

 Sök Resultat värden kan upprepas i en resultat uppsättning. Du kan till exempel ha 10 objekt med en poäng på 1,2, 20 objekt med en poäng på 1,0 och 20 objekt med en poäng på 0,5. Om flera träffar har samma Sök poäng, är ordningen för samma poäng objekt inte definierad och är inte stabil. Kör frågan igen så kan du se objektets Skift läge. Om två objekt har samma poäng finns det ingen garanti för att det visas först.  

## <a name="when-to-add-scoring-logic"></a>När du ska lägga till bedömnings logik 
 Du bör skapa en eller flera bedömnings profiler när standard ranknings beteendet inte är tillräckligt långt för att uppfylla dina affärs mål. Du kan till exempel bestämma att Sök relevans ska prioritera nyligen tillagda objekt. På samma sätt kan du ha ett fält som innehåller vinst marginal, eller något annat fält som visar potentiella intäkter. Det kan vara viktigt att förbättra träffarna som gör det möjligt för ditt företag att välja att använda bedömnings profiler.  

 Relevanta-baserad ordning implementeras också genom bedömnings profiler. Överväg att använda Sök Resultat sidor som du tidigare har använt, så att du kan sortera efter pris, datum, betyg eller relevans. I Azure Kognitiv sökning driver bedömnings profiler alternativet relevans. Definitionen av relevans styrs av dig, predikat på affärs mål och den typ av Sök upplevelse som du vill leverera.  

##  <a name="example"></a><a name="bkmk_ex"></a> Exempel  
 Som nämnts tidigare implementeras anpassad bedömning via en eller flera bedömnings profiler som definierats i ett index schema.  

 I det här exemplet visas schemat för ett index med två bedömnings profiler ( `boostGenre` , `newAndHighlyRated` ). Alla frågor mot detta index som inkluderar profil som en frågeparameter använder profilen för att räkna med resultat uppsättningen.  

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>Arbetsflöde  
 Om du vill implementera anpassade bedömnings beteende lägger du till en bedömnings profil i schemat som definierar indexet. Du kan ha upp till 100 bedömnings profiler i ett index (se [tjänst begränsningar](search-limits-quotas-capacity.md)), men du kan bara ange en profil i taget i en specifik fråga.  

 Börja med [mallen](#bkmk_template) som anges i det här avsnittet.  

 Ange ett namn. Bedömnings profiler är valfria, men om du lägger till en måste du ange namnet. Se till att följa namngivnings konventionerna för fält (börjar med en bokstav, undviker specialtecken och reserverade ord). Se [namngivnings regler &#40;Azure Kognitiv sökning&#41;](/rest/api/searchservice/naming-rules) för den fullständiga listan.  

 Texten i bedömnings profilen är konstruerad från viktade fält och funktioner.  

|Egenskaper |Beskrivning|  
|-|-|  
|**Lekar**|Ange namn/värde-par som tilldelar en relativ vikt till ett fält. I [exemplet](#bkmk_ex)är fälten albumTitle, genre och artistName förstärkta 1,5, 5 respektive 2. Varför ökar genren så mycket högre än de andra? Om sökningen utförs över data som är något homogena (vilket är fallet med "genre" i) kan `musicstoreindex` du behöva en större varians i de relativa vikterna. Till exempel `musicstoreindex` visas "Rock" som både en genre och i identiskt fraserade Genre beskrivningar. Om du vill att Genre ska uppväga Genre-Beskrivning behöver fältet Genre en mycket högre relativ vikt.|  
|**Funktioner**|Används när ytterligare beräkningar krävs för vissa kontexter. Giltiga värden är `freshness`, `magnitude`, `distance` och `tag`. Varje funktion har parametrar som är unika för den.<br /><br /> -   `freshness` ska användas när du vill öka med hur nytt eller gammalt objekt är. Den här funktionen kan endast användas med `datetime` fält (EDM. DataTimeOffset). Observera att `boostingDuration` attributet endast används med `freshness` funktionen.<br />-   `magnitude` ska användas när du vill öka det högsta eller lägsta värdet för ett numeriskt värde. Scenarier som anropar för den här funktionen är ökningar per vinst marginal, högsta pris, lägsta pris eller antal hämtningar. Den här funktionen kan endast användas med Double-och Integer-fält.<br />     För `magnitude` -funktionen kan du ändra intervallet, högt till lågt, om du vill använda det inverterade mönstret (till exempel för att öka pris poster med lägre pris mer än den högsta pris nivån). Med ett pris intervall från $100 till $1 skulle du ställa in `boostingRangeStart` på 100 och `boostingRangeEnd` 1 för att förstärka de lägre pris artiklarna.<br />-   `distance` ska användas när du vill öka med närhet eller geografisk plats. Den här funktionen kan endast användas med `Edm.GeographyPoint` fält.<br />-   `tag` ska användas när du vill öka med taggar i vanliga mellan dokument och Sök frågor. Den här funktionen kan endast användas med `Edm.String` och- `Collection(Edm.String)` fält.<br /><br /> **Regler för att använda funktioner**<br /><br /> Funktions typ ( `freshness` , `magnitude` , `distance` ) `tag` måste vara gemener.<br /><br /> Funktioner får inte innehålla värden som är null eller tomma. Mer specifikt, om du inkluderar FieldName, måste du ange det till något.<br /><br /> Funktioner kan bara användas för filter bara fält. Mer information om filter bara fält finns i [skapa Index &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/create-index) .<br /><br /> Funktioner kan endast tillämpas på fält som har definierats i fält samlingen för ett index.|  

 När indexet har definierats skapar du indexet genom att ladda upp index schemat, följt av dokument. Se [skapa Index &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/create-index) och [lägga till, uppdatera eller ta bort dokument &#40;Azure kognitiv sökning ](/rest/api/searchservice/addupdate-or-delete-documents) REST API&#41;för instruktioner om dessa åtgärder. När indexet har skapats bör du ha en funktionell bedömnings profil som fungerar med dina Sök data.  

##  <a name="template"></a><a name="bkmk_template"></a> Webbplatsmall  
 I det här avsnittet visas syntax och mall för bedömnings profiler. Mer information om attributen hittar du i avsnittet [index attribut referens](#bkmk_indexref) i nästa avsnitt.  

```  
. . .   
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "...",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a> Referens för index attribut  

> [!NOTE]  
>  En poängsättnings funktion kan endast användas för fält som är filter bara.  

|Attribut|Beskrivning|  
|---------------|-----------------|  
|`name`|Krävs. Detta är namnet på bedömnings profilen. Det följer samma namn konventioner för ett fält. Det måste börja med en bokstav, får inte innehålla punkter, kolon eller @-symboler och får inte börja med frasen "azureSearch" (Skift läges känsligt).|  
|`text`|Innehåller egenskapen viktningar.|  
|`weights`|Valfritt. Innehåller namn/värde-par som var och en anger ett fält namn och relativ vikt. Relativ vikt måste vara ett positivt heltal eller ett flytt ALS nummer.<br /><br /> Vikter används för att ange vikten av ett sökbart fält i förhållande till ett annat.|  
|`functions`|Valfritt. En poängsättnings funktion kan endast användas för fält som är filter bara.|  
|`type`|Krävs för poängsättnings funktioner. Anger vilken typ av funktion som ska användas. Giltiga värden är storlek, aktualitet, avstånd och tagg. Du kan inkludera fler än en funktion i varje bedömnings profil. Funktions namnet måste vara gemener.|  
|`boost`|Krävs för poängsättnings funktioner. Ett positivt tal som används som multiplikator för rå score. Det får inte vara lika med 1.|  
|`fieldname`|Krävs för poängsättnings funktioner. En poängsättnings funktion kan endast användas för fält som ingår i indexets fält samling och som kan filtreras. Dessutom introducerar varje funktions typ ytterligare begränsningar (färskhet används med DATETIME-fält, storlek med heltal eller dubbla fält och avstånd med plats fält). Du kan bara ange ett enskilt fält per funktions definition. Om du till exempel vill använda två gånger i samma profil måste du inkludera två definitions storlek, en för varje fält.|  
|`interpolation`|Krävs för poängsättnings funktioner. Definierar lutningen för vilken poängen ökar från början av intervallet till slutet av intervallet. Giltiga värden är linjär (standard), konstant, kvadratisk och logaritmisk. Mer information finns i [Ange interpolation](#bkmk_interpolation) .|  
|`magnitude`|Funktionen poängsättnings funktion används för att ändra rangordningar baserat på värde intervallet för ett numeriskt fält. Några av de vanligaste användnings exemplen för detta är:<br /><br /> -   **Stjärn klassificeringar:** Ändra poängen baserat på värdet i fältet "stjärn klassificering". När två objekt är relevanta kommer objektet med den högre klassificeringen att visas först.<br />-   **Marginal:** När två dokument är relevanta kan en åter försäljare vilja öka dokument som har högre marginaler först.<br />-   **Klicka på antal:** För program som spårar klickning genom åtgärder till produkter eller sidor kan du använda omfattning för att förstärka objekt som tenderar att få ut mesta möjliga trafik.<br />-   **Antal hämtningar:** För program som spårar hämtningar kan du förbättra objekt som har flest hämtningar genom att använda funktionen storlek.|  
|`magnitude` &#124; `boostingRangeStart`|Anger startvärdet för intervallet, vars storlek är resultatet. Värdet måste vara ett heltals värde eller ett flyttal. För stjärn klassificeringar mellan 1 och 4 blir det 1. För marginaler över 50% är detta 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Anger slutvärdet för intervallet, vars storlek är resultatet. Värdet måste vara ett heltals värde eller ett flyttal. För stjärn klassificeringar mellan 1 och 4 är detta 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Giltiga värden är true eller false (standard). När värdet är true fortsätter den fullständiga höjningen att gälla för dokument som har ett värde för fältet mål som är högre än det övre slutet av intervallet. Om värdet är false används inte ökningen av den här funktionen för dokument som har ett värde för det målfält som ligger utanför intervallet.|  
|`freshness`|Funktionen för uppdaterings funktionen används för att ändra rangordnings poängen för objekt baserat på värden i `DateTimeOffset` fält. Till exempel kan ett objekt med ett senare datum rangordnas högre än äldre objekt.<br /><br /> Det är också möjligt att rangordna objekt som Kalender händelser med framtida datum, så att objekt som är närmare för närvarande kan rangordnas högre än objekten längre fram i framtiden.<br /><br /> I den aktuella Service Release-versionen kommer en ände av intervallet att åtgärdas till den aktuella tiden. Den andra parten är en tid i det förflutna baserat på `boostingDuration` . Använd ett negativt värde för att öka antalet gånger i framtiden `boostingDuration` .<br /><br /> Den hastighet med vilken de ökade ändringarna från ett högsta och lägsta intervall bestäms av den interpolering som tillämpas på bedömnings profilen (se bilden nedan). Om du vill ändra förstärknings faktorn väljer du en förstärknings faktor på mindre än 1.|  
|`freshness` &#124; `boostingDuration`|Anger en förfallo period efter vilken förstärkning kommer att stoppas för ett visst dokument. Se [set boostingDuration](#bkmk_boostdur) i följande avsnitt för syntax och exempel.|  
|`distance`|Funktionen för avstånds resultat används för att påverka poängen för dokument baserat på hur nära eller hur långt de är i förhållande till en referens geografisk plats. Referens platsen anges som en del av frågan i en parameter (med `scoringParameterquery` alternativet sträng) som ett Lon, lat-argument.|  
|`distance` &#124; `referencePointParameter`|En parameter som ska skickas i frågor som ska användas som referens plats. `scoringParameter` är en frågeparameter. Se [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents) för beskrivningar av frågeparametrar.|  
|`distance` &#124; `boostingDistance`|Ett tal som anger avståndet i kilo meter från referens platsen där förstärknings intervallet slutar.|  
|`tag`|Funktionen för att räkna poängen används för att påverka poängen för dokument baserat på taggar i dokument och Sök frågor. Dokument som har taggar i gemensamma med Sök frågan kommer att öka. Taggarna för Sök frågan anges som en bedömnings parameter i varje search-begäran (med `scoringParameterquery` alternativet sträng).|  
|`tag` &#124; `tagsParameter`|En parameter som ska skickas i frågor för att ange taggar för en viss begäran. `scoringParameter` är en frågeparameter. Se [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents) för beskrivningar av frågeparametrar.|  
|`functionAggregation`|Valfritt. Gäller endast när funktioner anges. Giltiga värden är: sum (standard), genomsnitt, lägsta, högsta och firstMatching. En Sök Poäng är ett enskilt värde som beräknas från flera variabler, inklusive flera funktioner. Det här attributet anger hur förstärkningarna av alla funktioner kombineras till en enda sammanställd ökning som sedan tillämpas på grund dokument poängen. Bas poängen baseras på det [TF-IDF-](http://www.tfidf.com/) värde som beräknas från dokumentet och Sök frågan.|  
|`defaultScoringProfile`|Om ingen bedömnings profil anges när du kör en Sök förfrågan används standard bedömnings processen (endast[TF-IDF](http://www.tfidf.com/) ).<br /><br /> Du kan ställa in ett standard bedömnings profil namn här, vilket gör att Azure Kognitiv sökning använda profilen när ingen specifik profil anges i Sök förfrågan.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a> Ange interpolation  
 Med interpolation kan du ange formen på den lutning som används för poängsättning. Eftersom poängsättningen är hög till låg, minskar lutningen alltid, men interpolation anger kurvan för den nedåtriktade lutningen. Följande interpolation kan användas:  

| Interpol | Beskrivning |  
|-|-|  
|`linear`|För objekt som ligger inom max-och min-intervallet görs ökningen som tillämpas på objektet i ett konstant minsknings belopp. Linjär är standard-interpolation för en bedömnings profil.|  
|`constant`|För objekt som ligger inom start-och Slutintervallet används en konstant förstärkning för ranknings resultatet.|  
|`quadratic`|I jämförelse med en linjär interpolation som har en konstant minskning av ökningen minskar kvadratisk inlednings vis vid mindre takt och sedan den närmar sig slut intervallet, minskar den med ett mycket högre intervall. Det här alternativet för interpolation tillåts inte i taggnings bedömnings funktioner.|  
|`logarithmic`|I jämförelse med en linjär interpolation som har en konstant minskning av ökningen minskar logaritmen inlednings vis vid högre takt och när det närmar sig slut intervallet, minskar det med ett mycket mindre intervall. Det här alternativet för interpolation tillåts inte i taggnings bedömnings funktioner.|  

 ![Konstant, linjär, kvadratisk, log10 linjer i grafen](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a> Ange boostingDuration  
 `boostingDuration` är ett attribut för `freshness` funktionen. Du använder den för att ange en utgångs period efter vilken förstärkning kommer att stoppas för ett visst dokument. Om du till exempel vill öka en produkt linje eller ett varumärke för en 10-dagars kampanj period anger du 10-dagars perioden som "P10D" för dessa dokument.  

 `boostingDuration` måste formateras som ett XSD "dayTimeDuration"-värde (en begränsad delmängd av ett varaktighets värde på ISO 8601). Mönstret för detta är: "P [nD] [T [nH] [nM] [nS]]".  

 Följande tabell innehåller flera exempel.  

|Varaktighet|boostingDuration|  
|--------------|----------------------|  
|1 dag|"P1D"|  
|2 dagar och 12 timmar|"P2DT12H"|  
|15 minuter|"PT15M"|  
|30 dagar, 5 timmar, 10 minuter och 6,334 sekunder|"P30DT5H10M 6.334 S"|  

 Fler exempel finns i [XML-schema: data typer (w3.org webbplats)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Se även  

+ [REST API referens](/rest/api/searchservice/)
+ [Skapa index-API](/rest/api/searchservice/create-index)
+ [Azure Kognitiv sökning .NET SDK](/dotnet/api/overview/azure/search?)
