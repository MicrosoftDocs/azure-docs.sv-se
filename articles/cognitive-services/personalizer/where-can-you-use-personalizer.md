---
title: Där och hur du använder – personanpassa
description: Du kan använda en personanpassare i alla situationer där ditt program kan välja rätt objekt, åtgärd eller produkt som ska visas för att förbättra upplevelsen, uppnå bättre affärs resultat eller förbättra produktiviteten.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 87179950c33a6facb59ce499aa0ae393e53fb37f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91777232"
---
# <a name="where-and-how-to-use-personalizer"></a>Var och hur du använder en Personanpassare

Använd Personanpassare i alla situationer där ditt program behöver välja rätt åtgärd (innehåll) för att visa, för att förbättra upplevelsen, uppnå bättre affärs resultat eller förbättra produktiviteten.

Personanpassare använder förstärkt inlärning för att välja vilken åtgärd (innehåll) som ska Visa användaren. Valet kan variera drastiskt beroende på kvantitet, kvalitet och distribution av data som skickas till tjänsten.

## <a name="example-use-cases-for-personalizer"></a>Exempel på användnings fall för Personanpassare

* **Avsikt att klargöra & untvetydighet**: hjälpa dina användare att få en bättre upplevelse när deras avsikt inte är klar genom att tillhandahålla ett alternativ som är anpassat.
* **Standard förslag** för menyer & alternativ: låt bot-roboten föreslå det mest sannolika objektet på ett personligt sätt som ett första steg, i stället för att presentera en egen meny eller en lista med alternativ.
* **Bot-traiter & tonen**: för robotar som kan variera ton, utförlighet och skriv stil, bör du överväga att variera dessa egenskaper.
* Aviserings **& aviserings innehåll**: Bestäm vilken text som ska användas för aviseringar för att engagera användare.
* Aviserings **& tids inställning för aviseringar**: har anpassad inlärning av när du ska skicka meddelanden till användare för att kunna engagera dem.


## <a name="expectations-required-to-use-personalizer"></a>Förväntningar som krävs för att använda Personanpassare

Du kan använda Personanpassare i situationer där du uppfyller eller kan implementera följande rikt linjer.

|Riktlinjer|Förklaring|
|--|--|
|Affärs mål|Du har ett affärs-eller användbarhets mål för ditt program.|
|Innehåll|Du har en plats i ditt program där du får ett Sammanhangs beroende beslut av vad som ska visas för användarna att förbättra det målet.|
|Innehålls mängd|Du har färre än 50 åtgärder att rangordna per anrop.|
|Aggregera data|Det bästa valet kan och bör registreras från kollektivt användar beteende och total belönings poäng.|
|Etisk användning|Användningen av Machine Learning för anpassning följer de [rikt linjer](ethics-responsible-use.md) och alternativ som du väljer.
|Bästa enkla alternativet|Det sammanhangsbaserade beslutet kan uttryckas som rangordning av det bästa alternativet (åtgärd) från en begränsad uppsättning alternativ.|
|Resultat resultat|Hur väl det rankade valet av program kan bestämmas genom att mäta viss aspekt av användar beteendet och uttrycka det i en _[belönings Poäng](concept-rewards.md)_.|
|Relevant tids inställning|Belönings poängen tar inte upp för många påträffande eller externa faktorer. Experimentets varaktighet är för låg nog att belönings poängen kan beräknas när den fortfarande är relevant.|
|Tillräckliga kontext funktioner|Du kan uttrycka kontexten för rang som en lista över minst 5 [funktioner](concepts-features.md) som du tror kan hjälpa dig att välja rätt och som inte innehåller användarspecifik identifierbar information.|
|Tillräckliga åtgärds funktioner|Du har information om varje innehålls val, _åtgärd_, som en lista över minst 5 [funktioner](concepts-features.md) som du tror att du kan göra rätt val.|
|Dagliga data|Det finns tillräckligt många händelser för att få bästa möjliga anpassning om problemet uppstår över tid (t. ex. inställningar i nyheter eller mode). Personanpassaren anpassas efter kontinuerliga förändringar i verkligheten, men resultatet blir inte optimalt om det inte finns tillräckligt med händelser och data för att lära sig att identifiera och lösa nya mönster. Du bör välja ett användnings fall som ofta är tillräckligt. Överväg att söka efter användnings fall som inträffar minst 500 gånger per dag.|
|Historiska data|Ditt program kan lagra data tillräckligt länge för att samla en historik på minst 100 000 interaktioner. Detta gör att en Personanpassare kan samla in tillräckligt med data för att utföra offline-utvärdering och optimering av principer.|

**Använd inte personanpassare** där det personliga beteendet inte är något som kan upptäckas för alla användare. Till exempel är det bra att använda Personanpassare för att föreslå en första pizza-order från en lista med 20 möjliga meny alternativ, men vilken kontakt som ska anropas från användarnas kontakt lista när du behöver hjälp med Childcare (t. ex. "Grandma") är inte något som är personalizable i användar basen.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Så här använder du Personanpassare i ett webb program

Att lägga till en inlärnings slinga till ett webb program innehåller:

* Bestäm vilken upplevelse som ska anpassas, vilka åtgärder och funktioner du har, vilka Sammanhangs funktioner som ska användas och vilken belöning du ställer in.
* Lägg till en referens till anpassnings-SDK: n i ditt program.
* Anropa rang-API: et när du är redo att anpassa.
* Lagra eventId. Du skickar en belöning med belönings-API: et senare.
1. Anropa aktivera för händelsen när du är säker på att användaren har sett din anpassade sida.
1. Vänta på att användare väljer Rankat innehåll.
1. Ring belönings-API för att ange hur väl utdata från rang-API: n gjorde.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Så här använder du en Personanpassare med en chatt-robot

I det här exemplet får du se hur du använder anpassning för att göra ett standard förslag istället för att skicka användaren en serie menyer eller alternativ varje gång.

* Hämta [koden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) för det här exemplet.
* Konfigurera din bot-lösning. Se till att publicera ditt LUIS-program.
* Hantera rang-och belönings-API-anrop för bot.
    * Lägg till kod för att hantera bearbetning av LUIS-avsikt. Om **ingen** returneras som den främsta avsikten eller den främsta avsikts poängen är lägre än din affärs logiks tröskel, skickar du listan med intentor till personanpassaren för att rangordna intentarna.
    * Visa avsikts lista till användare som valbara länkar med det första syftet som det främsta ordnade syftet från rang-API-svar.
    * Avbilda användarens val och skicka detta i belönings-API-anropet.

### <a name="recommended-bot-patterns"></a>Rekommenderade robot mönster

* Gör en degrads-API-anrop varje gång en avtvetydighet behövs, i stället för att cachelagra resultat för varje användare. Resultatet av kontrollerades kan ändras med tiden för en person, och att rang-API: et kan upptäcka varianser för att påskynda den övergripande inlärningen.
* Välj en interaktion som är gemensam för många användare så att du har tillräckligt med data för att anpassa. Till exempel kan introduktions frågor vara bättre än mindre tydliga i konversations diagrammet som bara några få användare kan komma åt.
* Använd rang-API-anrop för att aktivera "första förslaget är rätt"-konversationer, där användaren får frågan "vill du ha X?" eller "menade du X?" och användaren kan bara bekräfta. i stället för att ge alternativ till användaren där de måste välja från en meny. Till exempel User: "Jag vill beställa en kaffe" robot: "vill du ha en dubbel espresso?". På så sätt kan belönings signalen också vara stark eftersom den gäller direkt till ett förslag.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Så här använder du en Personanpassare med en rekommendations lösning

Många företag använder rekommendations motorer, marknadsförings-och kampanj verktyg, mål grupp segmentering och klustring, samordnad filtrering och andra metoder för att rekommendera produkter från en stor katalog till kunder.

[Microsoft-rekommendationerna GitHub-lagringsplatsen](https://github.com/Microsoft/Recommenders) innehåller exempel och bästa metoder för att skapa rekommendations system, som tillhandahålls som Jupyter notebook-datorer. Den innehåller exempel på hur du förbereder data, bygger modeller, utvärderar, justerar och genomför rekommendationer för att använda rekommendationer för många vanliga metoder, inklusive xDeepFM, SAR, sensorn, RINGPÄRMSMEKANISMER, DKN.

Personanpassaren kan arbeta med en rekommendations motor när den är närvarande.

* Rekommendations motorer tar stora mängder objekt (till exempel 500 000) och rekommenderar en delmängd (till exempel de 20 främsta) från hundratals eller tusentals alternativ.
* Personanpassaren tar ett litet antal åtgärder med mycket information om dem och rangordnar dem i real tid för en viss rik kontext, medan de flesta rekommendations motorer bara använder några få attribut om användare, produkter och deras interaktioner.
* Personanpassaren är utformad för att på ett autonomt sätt utforska användar inställningarna hela tiden, vilket ger bättre resultat om innehållet ändras snabbt, till exempel nyheter, direktsända evenemang, innehåll i levande community, innehåll med dagliga uppdateringar eller säsongs innehåll.

Ett vanligt användnings sätt är att ta ut utdata från en rekommendations motor (till exempel de 20 främsta produkterna för en viss kund) och använda dem som ingångs åtgärder för Personanpassaren.

## <a name="adding-content-safeguards-to-your-application"></a>Lägga till innehålls skydd i ditt program

Om ditt program tillåter stora varianser i innehåll som visas för användarna, och en del av innehållet kan vara osäkert eller olämpligt för vissa användare, bör du planera framåt för att se till att rätt skydd är på plats för att förhindra att användarna ser oacceptabelt innehåll. Det bästa mönstret för att implementera säkerhets åtgärder är:
    * Hämta listan över åtgärder som ska rangordnas.
    * Filtrera bort de som inte är livskraftiga för mål gruppen.
    * Ranka bara de här lönsamma åtgärderna.
    * Visa den främsta rangordnade åtgärden för användaren.

I vissa arkitekturer kan ordningen ovan vara svår att implementera. I så fall finns det en alternativ metod för att implementera skydd efter rangordning, men en bestämmelse måste göras så att åtgärder som faller utanför skyddet inte används för att träna en personanpassa modell.

* Hämta listan med åtgärder som ska rangordnas med inlärning inaktiverat.
* Ranknings åtgärder.
* Kontrol lera om den främsta åtgärden är livskraftig.
    * Om den främsta åtgärden är livskraftig aktiverar du inlärning för den här rangordningen och visar den sedan för användaren.
    * Om den översta åtgärden inte är livskraftig aktiverar du inte inlärning för den här rangordningen och bestämmer dig genom din egen logik eller alternativa metoder vad som ska visas för användaren. Även om du använder det andra bästa rangordnings alternativet ska du inte aktivera inlärning för den här rangordningen.


## <a name="next-steps"></a>Nästa steg

[Etik & ansvarig användning](ethics-responsible-use.md).
