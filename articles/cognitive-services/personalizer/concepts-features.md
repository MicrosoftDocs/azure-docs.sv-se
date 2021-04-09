---
title: 'Funktioner: åtgärd och kontext – Personanpassare'
titleSuffix: Azure Cognitive Services
description: Personanpassare använder funktioner, information om åtgärder och kontext för att göra bättre rangordnings förslag. Funktioner kan vara mycket generiska eller bara för ett objekt.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: f49abd4ca1cc1ccdcb7ba2b0fab3bad953dede5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100380550"
---
# <a name="features-are-information-about-actions-and-context"></a>Funktioner är information om åtgärder och kontext

Personanpassa tjänsten arbetar genom att lära dig vad ditt program ska visa för användare i en specifik kontext.

I personanpassaren används **funktioner**, som innehåller information om den **aktuella kontexten** för att välja den bästa **åtgärden**. Funktionerna representerar all information du tycker kan hjälpa dig att anpassa för att uppnå högre förmåner. Funktioner kan vara mycket generiska eller bara för ett objekt. 

Du kan till exempel ha en **funktion** om:

* _Användar personen_ , till exempel en `Sports_Shopper` . Detta bör inte vara ett enskilt användar-ID. 
* _Innehållet_ , till exempel om en video är en, `Documentary` en, `Movie` eller en `TV Series` , eller om en butiks artikel är tillgänglig i butiken.
* Den _aktuella_ tids perioden, till exempel vilken veckodag det är.

Personanpassaren anger inte, begränsar eller åtgärdar vilka funktioner du kan skicka för åtgärder och kontext:

* Du kan skicka vissa funktioner för vissa åtgärder och inte för andra, om du inte har dem. TV-serier kan t. ex. ha attributs-filmer.
* Vissa funktioner är bara tillgängliga vissa gånger. Ett mobil program kan till exempel ge mer information än en webb sida. 
* Med tiden kan du lägga till och ta bort funktioner för kontext och åtgärder. Personanpassaren fortsätter att lära sig från tillgänglig information.
* Det måste finnas minst en funktion för kontexten. Personanpassaren har inte stöd för en tom kontext. Om du bara skickar en fast kontext varje gång väljer Personanpassaren åtgärden för rankning enbart för funktionerna i åtgärderna.
* För kategoriska-funktioner behöver du inte definiera möjliga värden, och du behöver inte definiera intervall för numeriska värden.

## <a name="supported-feature-types"></a>Funktions typer som stöds

En personanpassare stöder funktioner av typerna String, numeric och Boolean. Det är mycket troligt att ditt program oftast använder sträng funktioner, med några undantag.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Hur valet av funktions typ påverkar Machine Learning i personanpassa

* **Strängar**: för sträng typer behandlas varje kombination av nyckel och värde som en One-Hot funktion (t. ex. Genre: "ScienceFiction" och Genre: "dokument" skulle skapa två nya ingångs funktioner för Machine Learning-modellen.
* **Numerisk**: du bör använda numeriska värden när talet är en omfattning som ska påverka anpassnings resultatet proportionellt. Detta är mycket scenariot beroende. I ett förenklat exempel, t. ex. När du anpassar en återförsäljarversion, kan NumberOfPetsOwned vara en funktion som är numerisk eftersom du vill att personer med 2 eller tre hus djur ska påverka anpassnings resultatet två gånger eller tre gånger om så mycket som med 1 hus djur. Funktioner som baseras på numeriska enheter men där innebörden inte är linjär, till exempel ålder, temperatur eller person höjd – är bäst kodad som strängar. Till exempel är DayOfMonth en sträng med "1", "2"... "31". Om du har många kategorier kan du normalt förbättra funktionens kvalitet genom att använda intervall. Till exempel kan ålder kodas som "ålder": "0-5", "Age": "6-10" osv.
* **Booleska** värden som skickas med värdet "false" fungerar som om de inte hade har skickats över huvud taget.

Funktioner som inte finns ska utelämnas från begäran. Undvik att skicka funktioner med ett null-värde eftersom det kommer att bearbetas som befintligt och med värdet "null" när du tränar modellen.

## <a name="categorize-features-with-namespaces"></a>Kategorisera funktioner med namn områden

En personanpassare använder funktioner som är ordnade i namn områden. Du fastställer, i ditt program, om namn områden används och vad de ska vara. Namn områden används för att gruppera funktioner om ett liknande ämne, eller funktioner som kommer från en viss källa.

Följande är exempel på funktions namn rymder som används av program:

* User_Profile_from_CRM
* Tid
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Vädret
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Du kan namnge funktions namn rymder efter dina egna konventioner så länge de är giltiga JSON-nycklar. Namn områden används för att organisera funktioner i olika uppsättningar och för att disambiguate funktioner med liknande namn. Du kan tänka på namn områden som "prefix" som läggs till i funktions namn. Det går inte att kapsla namn områden.


I följande JSON, `user` ,, `state` och `device` är funktions namn rymder. 

> [!Note]
> För närvarande rekommenderar vi starkt att du använder namn för funktions namn rymder som är UTF-8-baserade och börjar med olika bokstäver. Till exempel, `user` , `state` och `device` börjar med `u` , `s` , och `d` . För närvarande har namn rymder med samma första tecken kan leda till konflikter i index som används för maskin inlärning.

JSON-objekt kan innehålla kapslade JSON-objekt och enkla egenskaper/värden. En matris kan bara tas med om mat ris elementen är siffror. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": ["47.6,-122.1"]
            }
        },
        {
            "environment": {
                "dayOfMonth": "28",
                "monthOfYear": "8",
                "timeOfDay": "13:00",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        },
        {
            "userActivity" : {
                "itemsInCart": 3,
                "cartValue": 250,
                "appliedCoupon": true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>Begränsningar i teckenuppsättningar för namn områden

Strängen som du använder för att namnge namn området måste följa vissa begränsningar: 
* Det får inte vara Unicode.
* Du kan använda några av de utskrivbara symbolerna med koder < 256 för namn områdets namn. 
* Du kan inte använda symboler med koder < 32 (inte utskrivbart), 32 (blank steg), 58 (kolon), 124 (pipe) och 126 – 140.
* Den får inte börja med ett under streck (_) eller så ignoreras funktionen.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Så här gör du funktions uppsättningar mer effektiva för personligt anpassade

Med en bra funktions uppsättning kan Personanpassare lära sig hur man förutsäger den åtgärd som kommer att driva den högsta belöningen. 

Överväg att skicka funktioner till API: et för personanpassa rankning som följer dessa rekommendationer:

* Använd kategoriska-och sträng typer för funktioner som inte är av samma storlek. 

* Det finns tillräckligt med funktioner för att driva anpassning. Den mer precis riktade innehållet måste vara, desto fler funktioner behövs.

* Det finns tillräckligt med funktioner i olika *tätare*. En funktion är *tätare* om många objekt är grupperade i några buckets. Till exempel kan tusentals videor klassificeras som "Long" (över 5 min lång tid) och "Short" (under 5 min lång tid). Det här är en *mycket kompakt* funktion. Å andra sidan kan samma tusentals objekt ha ett attribut med namnet "title", som nästan aldrig har samma värde från ett objekt till ett annat. Det här är en mycket icke-kompakt eller *sparse* -funktion.  

Om du har funktioner med hög densitet blir det lättare för dig att lära sig lära från ett objekt till ett annat. Men om det bara finns några få funktioner och de är för enkla, kommer Personanpassaren att försöka exakt rikta in innehåll med bara några buckets att välja bland.

### <a name="improve-feature-sets"></a>Förbättra funktions uppsättningar 

Analysera användar beteendet genom att göra en offline-utvärdering. På så sätt kan du titta på tidigare data för att se vilka funktioner som bidrar kraftigt till positiva förmåner jämfört med de som bidrar mindre. Du kan se vilka funktioner som hjälper och det kommer att vara upp till dig och ditt program för att hitta bättre funktioner som kan skickas till Personanpassare för att förbättra resultaten ytterligare.

Följande avsnitt är vanliga metoder för att förbättra funktioner som skickas till Personanpassare.

#### <a name="make-features-more-dense"></a>Gör funktioner mer täta

Det är möjligt att förbättra dina funktions uppsättningar genom att ändra dem så att de blir större och mer eller mindre kompakta.

En tidsstämpel till den andra är till exempel en mycket sparse-funktion. Det kan göras mer kompakt (effektiv) genom att klassificera tiderna i "morgon", "kulminera", "eftermiddag" osv.

Plats informationen ger även vanligt vis fördelar från att skapa bredare klassificeringar. Exempel: en Latitude-Longitude koordinat som Lat: 47,67402 ° N, Long: 122,12154 ° W är för exakt och tvingar modellen att lära sig latitud och longitud som distinkta dimensioner. När du försöker anpassa baserat på plats information kan du gruppera plats information i större sektorer. Ett enkelt sätt att göra det är att välja en lämplig avrundnings precision för Lat-Long siffror och kombinera latitud och longitud i "områden" genom att göra dem till en sträng. Till exempel är ett bra sätt att representera 47,67402 ° N, långt: 122,12154 ° W i regionerna cirka några kilo meter breda skulle vara "plats": "34.3, 12,1".


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Expandera funktions uppsättningar med extrapolerad information

Du kan också få fler funktioner genom att tänka på ännu inte utforskade attribut som kan härledas från information som du redan har. Till exempel, i en fiktiv Films List anpassning, är det möjligt att en helg vs-veckodag gör olika saker från användarna? Det gick inte att expandera tiden till attributet "helg" eller "Weekday". Uppmärksammar vi nationella kultur helgdags enheter på vissa typer av filmer? Till exempel är ett "Halloween"-attribut användbart i platser där det är relevant. Är det möjligt att regna väder har stor inverkan på valet av en film för många personer? Med tid och plats kan en väder tjänst tillhandahålla den informationen och du kan lägga till den som en extra funktion. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Expandera funktions uppsättningar med artificiell intelligens och kognitiva tjänster

Artificiell intelligens och klar att köra Cognitive Services kan vara ett mycket kraftfullt tillägg till Personanpassaren. 

Genom att Förbearbeta dina objekt med hjälp av artificiell intelligens-tjänster kan du automatiskt extrahera information som troligen är relevant för anpassning.

Exempel:

* Du kan köra en filmfil via [video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) för att extrahera scen element, text, sentiment och många andra attribut. Dessa attribut kan sedan göras mer kompakta för att avspegla egenskaper som det ursprungliga objektets metadata inte hade. 
* Avbildningar kan köras genom objekt identifiering, ansikten genom sentiment osv.
* Information i text kan utökas genom att extrahera entiteter, sentiment, expandera entiteter med Bing Knowledge Graph osv.

Du kan använda flera andra [Azure-Cognitive Services](https://www.microsoft.com/cognitive-services), t. ex.

* [Entity Linking](../text-analytics/index.yml)
* [Textanalys](../text-analytics/overview.md)
* [Känsla](../face/overview.md)
* [Visuellt innehåll](../computer-vision/overview.md)

## <a name="actions-represent-a-list-of-options"></a>Åtgärder representerar en lista med alternativ

Varje åtgärd:

* Har ett _händelse_ -ID. Om du redan har ett händelse-ID bör du skicka in det. Om du inte har något händelse-ID ska du inte skicka en, Personanpassare skapar en åt dig och returnerar den i svaret på ranknings förfrågan. ID: t är associerat med rang-händelsen, inte användaren. Om du skapar ett ID fungerar ett GUID bäst. 
* Innehåller en lista med funktioner.
* Listan över funktioner kan vara stor (hundratals) men vi rekommenderar att du utvärderar funktions effektiviteten för att ta bort funktioner som inte bidrar till att få några fördelar. 
* Funktionerna i **åtgärderna** kan eller kanske inte har någon korrelation med funktioner i **kontexten** som används av personanpassan.
* Funktioner för åtgärder kan finnas i vissa åtgärder och inte andra. 
* Funktioner för ett visst åtgärds-ID kan vara tillgängliga en dag, men senare blir det otillgängligt. 

En personanpassas Machine Learning-algoritmer fungerar bättre när det finns stabila funktions uppsättningar, men ranknings anrop Miss Miss Miss kan inte utföras om funktionen har ändrats över tid.

Skicka inte mer än 50 åtgärder vid rangordning av åtgärder. Detta kan vara samma 50-åtgärder varje gång, eller så kan de ändras. Om du till exempel har en produkt katalog med 10 000 objekt för ett e-handelsprogram, kan du använda en rekommendations-eller filtrerings motor för att fastställa den översta 40 en kund kanske gillar, och använda Personanpassare för att hitta den som kommer att generera den största belöningen (till exempel att användaren kommer att läggas till i korgen) för den aktuella kontexten.


### <a name="examples-of-actions"></a>Exempel på åtgärder

De åtgärder som du skickar till ranknings-API: et beror på vad du försöker anpassa.

Här är några exempel:

|Syfte|Action|
|--|--|
|Anpassa vilken artikel som är markerad på en nyhets webbplats.|Varje åtgärd är en potentiell nyhets artikel.|
|Optimera AD-placering på en webbplats.|Varje åtgärd är en layout eller regler för att skapa en layout för annonserna (till exempel överst, till höger, små bilder, stora bilder).|
|Visa anpassad rangordning av rekommenderade objekt på en shopping webbplats.|Varje åtgärd är en enskild produkt.|
|Föreslå användar gränssnitts element, till exempel filter som ska användas för ett bestämt foto.|Varje åtgärd kan vara ett annat filter.|
|Välj ett svar för chatt-roboten för att klargöra användar avsikten eller föreslå en åtgärd.|Varje åtgärd är ett alternativ för att tolka svaret.|
|Välj vad som ska visas överst i en lista med Sök Resultat|Varje åtgärd är ett av de viktigaste Sök resultaten.|


### <a name="examples-of-features-for-actions"></a>Exempel på funktioner för åtgärder

Följande är användbara exempel på funktioner för åtgärder. De kommer att vara beroende av varje program.

* Funktioner med egenskaperna för åtgärderna. Är det till exempel en film eller TV-serie?
* Funktioner för hur användare kan ha interagerat med den här åtgärden tidigare. Den här filmen visas till exempel främst av personer i demografisk A eller B, men den spelas vanligt vis inte mer än en gången.
* Funktioner om egenskaperna för hur användaren *ser* de olika åtgärderna. Till exempel visas affischen för den film som visas i miniatyr bilden med ansikten, bilar eller landskap?

### <a name="load-actions-from-the-client-application"></a>Läs in åtgärder från klient programmet

Funktioner från åtgärder kan normalt komma från innehålls hanterings system, kataloger och rekommenderade system. Programmet ansvarar för att läsa in informationen om åtgärder från relevanta databaser och system som du har. Om åtgärderna inte ändras eller när de läses in varje gång har en onödig påverkan på prestanda kan du lägga till logik i programmet för att cachelagra den här informationen.

### <a name="prevent-actions-from-being-ranked"></a>Förhindra att åtgärder rangordnas

I vissa fall finns det åtgärder som du inte vill visa för användarna. Det bästa sättet att förhindra att en åtgärd rangordnas som översta är att inte ta med den i åtgärds listan till ranknings-API: et på den första platsen.

I vissa fall kan det bara fastställas senare i affärs logiken om en resulterande _åtgärd_ av ett rang-API-anrop visas för en användare. I dessa fall bör du använda _inaktiva händelser_.

## <a name="json-format-for-actions"></a>JSON-format för åtgärder

När du anropar rang skickas flera åtgärder som du kan välja bland:

JSON-objekt kan innehålla kapslade JSON-objekt och enkla egenskaper/värden. En matris kan bara tas med om mat ris elementen är siffror. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Exempel på Sammanhangs information

Informationen för _kontexten_ beror på varje program och användnings fall, men det kan vanligt vis omfatta information som:

* Demografisk och profil information om användaren.
* Information som extraherats från HTTP-huvuden som användar agent eller härleds från HTTP-information, till exempel omvänd geografisk sökning baserat på IP-adresser.
* Information om den aktuella tiden, till exempel dag i veckan, helg eller inte, morgon eller eftermiddag, jul säsong eller inte, osv.
* Information som extraherats från mobila program, till exempel plats, transport eller batteri nivå.
* Historiska mängder av användar beteende, t. ex. vilka film genrer den här användaren har sett mest.

Ditt program ansvarar för att läsa in informationen om kontexten från relevanta databaser, sensorer och system som du kan ha. Om kontext informationen inte ändras kan du lägga till logik i programmet för att cachelagra den här informationen innan du skickar den till ranknings-API: et.

## <a name="json-format-for-context"></a>JSON-format för kontext 

Kontexten uttrycks som ett JSON-objekt som skickas till ranknings-API: et:

JSON-objekt kan innehålla kapslade JSON-objekt och enkla egenskaper/värden. En matris kan bara tas med om mat ris elementen är siffror. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

[Kunskapsförmedling](concepts-reinforcement-learning.md)
