---
title: Etik och ansvarig användning – Personanpassare
titleSuffix: Azure Cognitive Services
description: Dessa rikt linjer syftar till att hjälpa dig att implementera anpassning på ett sätt som hjälper dig att bygga upp förtroende för ditt företag och din tjänst. Se till att pausa till forskning, lär dig och ta del av hur anpassningen påverkar användarnas liv. Vid tvivel, Sök vägledning.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 602da28f0c235fb0e797a493bc1160631c042a9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005987"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Rikt linjer för ansvarig implementering av Personanpassare

För att människor och samhället ska kunna utnyttja den fulla potentialen av AI måste implementeringarna utformas på ett sådant sätt att de får förtroende för de som lägger till AI i sina program och användare av program som skapats med AI. Dessa rikt linjer syftar till att hjälpa dig att implementera Personanpassare på ett sätt som hjälper dig att bygga upp förtroende för ditt företag och din tjänst. Se till att pausa till forskning, lär dig och ta del av hur anpassningen påverkar användarnas liv. Vid tvivel, Sök vägledning.

Dessa rikt linjer är inte avsedda som juridiska råd och du bör separat se till att programmet uppfyller den snabba utvecklingen i lagen i det här området och i din sektor.

När du designar ditt program med hjälp av Personanpassan bör du också fundera över en stor uppsättning ansvars områden som du har när du utvecklar datainriktade AI-system, inklusive etik, sekretess, säkerhet, upptagande, öppenhet och ansvar. Du kan läsa mer om dessa i det [rekommenderade](#recommended-reading) avsnittet om läsning.

Du kan använda följande innehåll som en start check lista och anpassa och förfina den i ditt scenario. Det här dokumentet har två huvud avsnitt: det första är dedikerat för att markera ansvariga för att tänka på när du väljer scenarier, funktioner och förmåner för personligt inriktade. Den andra tar en uppsättning värden som Microsoft bedömer bör överväga när du skapar AI-system och ger förslag på åtgärder och risker för hur din användning av Personanpassan påverkar dem.


## <a name="your-responsibility"></a>Ditt ansvar

Alla rikt linjer för ansvarig implementering bygger på grunden att utvecklare och företag som använder en person som är ansvariga för att kunna använda dessa algoritmer i samhället. Om du utvecklar ett program som din organisation ska distribuera bör du känna igen din roll och ditt ansvar och hur det påverkar dem. Om du utvecklar ett program som ska distribueras av en tredje part, kommer du till en delad förståelse med dem som i slut ändan ansvarar för programmets beteende och dokument som förstår.

Förtroende är byggt på begreppet uppfyllda åtaganden – Överväg dina användare, ditt samhälle och det juridiska ramverk som dina program arbetar i, för att identifiera tydliga och implicita åtaganden som de kan ha.

Microsoft lägger kontinuerligt till sina verktyg och dokument för att hjälpa dig att agera på dessa ansvars områden. [Ge feedback till Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) om du anser att ytterligare verktyg, produkt funktioner och dokument kan hjälpa dig att implementera dessa rikt linjer för att använda personanpassare.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Faktorer för ett ansvarsfullt sätt implementera Personanpassare

Att implementera personanpassa kan vara mycket bra för dina användare och ditt företag. Börja med att beakta följande rikt linjer när du ska implementera ett ansvarsfullt sätt:

* Välj användnings fall för att använda anpassningar.
* Skapa [belönings funktioner](concept-rewards.md).
* Välja vilka [funktioner](concepts-features.md) för kontext och möjliga åtgärder som du vill använda för anpassning.


## <a name="choosing-use-cases-for-personalizer"></a>Välja användnings fall för Personanpassare

Användning av en tjänst som lär sig att anpassa innehåll och användar gränssnitt är användbart. Det kan också vara fel om anpassningen skapar negativa sido effekter i verkligheten, inklusive om användarna inte kan anpassa innehållet.

Exempel på användning av Personanpassare med förhöjd potential för negativa sido effekter eller brist på genomskinlighet är scenarier där "belöningen" är beroende av många långsiktiga komplexa faktorer som, vid överanvändning i en omedelbar belöning, kan ha ofördelaktiga resultat för enskilda användare. Dessa tenderar att anses vara "sekventiella" alternativ eller val som innebär risk för skador. Exempel:


* **Finans**: personanpassa erbjudanden på låne-, finans-och försäkrings produkter, där riskfaktorer baseras på data som personer inte vet om, inte kan få eller inte kan ifrågasätta.
* **Utbildning**: att anpassa rankningen för skol kurser och utbildnings institutioner där rekommendationer kan spridas över flera nivåer och minska användarnas medvetenhet om andra alternativ.
* **Demokrati-och Civic-deltagande**: personanpassa innehåll för användare med målet att påverka yttranden är både följden och manipulering.
* **Utvärderings version av tredje part**: person uppgifter där belöningen baseras på en senare utvärdering av tredje part av användaren, i stället för att få en belöning som genereras av användarens egna beteende.
* **Intolerans för utforskning**: en situation där det kan orsaka skada om utforsknings beteendet för en personanpassare.

När du väljer användnings fall för Personanpassare:

* Starta design processen och fundera över hur anpassningen hjälper dina användare.
* Överväg de negativa konsekvenserna i verkligheten om några objekt inte rangordnas för användare på grund av anpassnings mönster eller utforskning.
* Överväg om ditt användnings fall utgör en automatiserad bearbetning, vilket avsevärt påverkar data ämnen som regleras i [GDPR](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) artikel 22 eller andra lagar.
* Överväg att själv uppfylla Prophecy-slingor. Detta kan inträffa om en anpassnings belöning tågen en modell så att den senare kan utesluta en demografisk grupp från att komma åt relevant innehåll. De flesta personer i en låg inkomst verksamhet får till exempel inget bidrags försäkrings erbjudande, och långsamt utan att kunna se erbjudandet om det inte finns tillräckligt med undersökning.
* Spara kopior av modeller och inlärnings principer om det är nödvändigt att producera om Personanpassaren i framtiden. Du kan göra detta regelbundet eller varje modell uppdaterings period.
* Tänk på vilken nivå av utforskning som är lämplig för utrymmet och hur du använder det som ett verktyg för att minimera effekten "eko kammare".


## <a name="selecting-features-for-personalizer"></a>Välja funktioner för Personanpassare

Att anpassa innehållet är beroende av att ha användbar information om innehållet och användaren. Tänk på att för vissa program och branscher kan vissa användar funktioner direkt eller indirekt betraktas som diskriminerande och potentiellt ogiltiga.

Överväg effekterna av dessa funktioner:

* **Användar demografiska** funktioner: funktioner för kön, kön, ålder, tävling, religion: dessa funktioner kan inte tillåtas i vissa program av reglerings skäl, och det kan inte vara etiska att anpassa dem på grund av att anpassningen skulle sprida generaliseringar och bias. Ett exempel på denna kompensations spridning är en jobb publicering för teknik som inte visas för äldre eller kön-baserade mål grupper.
* **Språk information**: på många platser i världen kan plats information (till exempel ett post nummer, post nummer eller namn) vara mycket korrelerad med intäkter, ras och religion.
* **Användar uppfattning om skälighet**: även i de fall där ditt program fattar ett ljud beslut, bör du tänka på hur användarna ser att innehållet visas i dina program ändringar på ett sätt som verkar korreleras med funktioner som skulle vara diskriminerande.
* **Oavsiktlig förskjutning i funktioner**: det finns typer av fördomar som kan introduceras med hjälp av funktioner som bara påverkar en del av populationen. Detta kräver extra uppmärksamhet om funktionerna genereras algorithmically, till exempel när du använder bild analys för att extrahera objekt i en bild eller text analys för att identifiera entiteter i text. Ta reda på egenskaperna för de tjänster som du använder för att skapa de här funktionerna.

Använd följande metoder när du väljer vilka funktioner som ska skickas i kontexter och åtgärder till Personanpassare:

* Överväg att använda vissa funktioner för vissa program och om Innocent funktioner kan vara proxyservrar för andra som du vill eller bör undvika,
* Var transparent för användare som algoritmer och data analys används för att anpassa de alternativ som de ser.
* Fråga dig själv: är mina användare försiktiga om jag använde den här informationen för att anpassa innehållet för dem? Vet jag att det var bra att visa hur beslutet har gjorts för att markera eller dölja vissa objekt?
* Använd beteende i stället för klassificerings-eller segmenterings data baserat på andra egenskaper. Demografisk information användes traditionellt av åter försäljare av historiska skäl – demografiska attribut verkade vara enkla att samla in och agera före en digital era,-men fråga hur relevant demografisk information är när du har verkliga interaktions-, sammanhangsbaserade och historiska data som är mer likt användarnas preferenser och identiteter.
* Överväg att förhindra att funktioner "manipuleras" av illvilliga användare, som om de utnyttjas i stora tal kan leda till utbildning i tränare på ett missvisande sätt för att purposefully störa, embarrass och trakassera vissa användar klasser.
* När det är lämpligt och möjligt kan du utforma ditt program så att användarna kan välja att välja ut eller välja att låta vissa personliga funktioner användas. Dessa kan grupperas, till exempel "plats information", "enhets information", "tidigare inköps historik" osv.


## <a name="computing-rewards-for-personalizer"></a>Beräknar förmåner för Personanpassare

Med personanpassare kan du förbättra valet av vilken åtgärd du behöver för att få en belöning utifrån belönings poängen från din program affärs logik.

En väl utformad belönings poäng kommer att fungera som en kortsiktig proxy till ett affärs mål, som är knutet till en organisations uppdrag.

Om du till exempel betjänar klickningar blir det enklare för tjänsten personanpassa att klicka på kostnaden för allt annat, även om det som klickas på är störande eller inte är kopplat till ett affärs resultat.

Som ett kontrast exempel kan en nyhets webbplats vilja ange förmåner som är knutna till något mer meningsfullt än klickningar, till exempel "gjorde användaren tillräckligt mycket tid för att läsa innehållet?" "Har de klickat på relevanta artiklar eller referenser?". Med Personanpassare är det enkelt att knyta måtten nära fördelar. Men var noga med att inte confound användar engagemang med lämpliga resultat.

### <a name="unintended-consequences-from-reward-scores"></a>Oönskade följder från belönings resultat
Belönings poängen kan skapas med det bästa av avsikten, men kan ändå skapa oväntade följder eller oönskade resultat på hur en Personanpassare rangordnar innehåll.

Överväg följande exempel:

* Att belöna anpassning av video innehåll i procent av den video längd som bevakas tenderar förmodligen att ranka kortare videor.
* Att belöna sociala medie resurser, utan sentiment analys av hur den delas eller själva innehållet, kan leda till att det går att rangordna stötande, inte måttlig eller inflammatory innehåll, vilket är till för att Incite mycket av "engagemang", men ger lite värde.
* Att belöna åtgärden för användar gränssnitts element som användare inte förväntar sig att ändra kan störa användar gränssnittets användbarhet och förutsägbarhet, där knappar överraskande ändrar plats eller syfte utan varning, vilket gör det svårare för vissa grupper av användare att vara produktiva.

Implementera följande metod tips:

* Kör offline-experiment med systemet med olika belönings metoder för att förstå påverkan och sido effekter.
* Utvärdera dina belönings funktioner och fråga dig själv hur skulle en mycket naïve person böja sitt tolkning och uppnå oönskade resultat.


## <a name="responsible-design-considerations"></a>Ansvariga design överväganden

Följande är design områden för ansvariga implementeringar av AI. Lär dig mer om verifieringen det här ramverket i [framtiden](https://news.microsoft.com/futurecomputed/).

![AI-värden från framtida Beräknad](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Ansvar
*Personer som utformar och distribuerar AI-system måste vara konto bara för hur deras system ska användas*.

* Skapa interna rikt linjer för hur du implementerar Personanpassare, dokument och kommunicerar med ditt team, chefer och leverantörer.
* Utför regelbundna granskningar av hur belönings poängen beräknas, utför offline-utvärderingar för att se vilka funktioner som påverkar personligare och Använd resultatet för att eliminera onödiga och onödiga funktioner.
* Kommunicera tydligt med dina användare så att en Personanpassare används, i vilket syfte och med vilka data.
* Arkivera information och till gångar – till exempel modeller, utbildnings principer och andra data – som Personanpassaren använder för att kunna återskapa resultat.

### <a name="transparency"></a>Transparens
*AI-system bör vara begripliga*. Med Personanpassare:

* *Ge användare information om hur innehållet har anpassats.* Du kan till exempel Visa dina användare en knapp `Why These Suggestions?` som visar vilka Top-funktioner i användaren och åtgärder som spelade en roll i resultatet av en personanpassare.
* Se till att användnings villkoren är tänkta att du använder information om användare och deras beteende för att anpassa upplevelsen.

### <a name="fairness"></a>Rättvisa
*AI-system bör behandla alla personer* på ett ganska mycket.

* Använd inte Personanpassare för användnings fall där resultatet är långsiktigt, följd eller rör verklig skada.
* Använd inte funktioner som inte är lämpliga för att anpassa innehåll med eller som kan hjälpa till att sprida oönskade kompensationer. Till exempel bör alla med liknande ekonomiska omständigheter se samma anpassade rekommendationer för finansiella produkter.
* Förstå eventuella kompensationer som kan finnas i funktioner som har en källa från redigerare, algoritmiska verktyg eller användare själva.

### <a name="reliability-and-safety"></a>Tillförlitlighet och säkerhet
*AI-system bör utföras på ett tillförlitligt och säkert sätt*. För Personanpassare:

* *Ange inte åtgärder för en personanpassare som inte ska väljas*. Till exempel bör filmer som inte är lämpligt filtreras bort från åtgärder för att anpassas vid rekommendation för en anonym användare eller under ålders användare.
* *Hantera din personanpassa modell som en affärs till gång*.  Fundera över hur ofta du ska spara och säkerhetskopiera modell-och inlärnings principerna bakom din personliga loop och på annat sätt behandla den som en viktig affärs till gång. Det är viktigt att återge tidigare resultat för att själv kunna granska och mäta förbättringar.
* *Tillhandahåll kanaler för att få direkt feedback från användarna*. Förutom att kunna koda säkerhets kontroller för att se till att endast rätt mål grupper ser rätt innehåll, ger du en feedback för användarna att rapportera innehåll som kan vara överraskande eller störa. I synnerhet om ditt innehåll kommer från användare eller tredje part, bör du överväga att använda Microsoft Content Moderator eller ytterligare verktyg för att granska och validera innehåll.
* *Utföra frekventa utvärderingar offline*. På så sätt kan du övervaka trender och se till att effektiviteten är känt.
* *Upprätta en process för att identifiera och agera vid skadlig behandling*. Det finns aktörer som kan dra nytta av Machine Learning-och AI-systemets förmåga att lära sig från deras miljö för att flytta ut resultatet mot deras mål. Om din användning av Personanpassan är i ett läge för att påverka viktiga val bör du se till att ha lämpliga metoder för att upptäcka och minimera dessa klasser av attacker, inklusive mänsklig granskning under lämpliga omständigheter.

### <a name="security-and-privacy"></a>Säkerhet och sekretess
*AI-system bör vara säkra och respektera sekretess*. När du använder Personanpassare:

* *Informera användarna om de data som samlas in och hur de används och få sitt samtycke i förväg* efter dina lokala och bransch bestämmelser.
* *Ge integritets skydd – skydda användar kontroller.* För program som lagrar personlig information bör du överväga att tillhandahålla en enkel att hitta-knapp för funktioner som:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

I vissa fall kan detta vara obligatoriskt. Fundera över kompromisserna i omtränings modeller regelbundet så att de inte innehåller några spårningar av borttagna data.

### <a name="inclusiveness"></a>Inkludering
*Hantera ett brett utbud av mänskliga behov och erfarenheter*.
* *Tillhandahålla anpassade funktioner för hjälpmedels aktiverade gränssnitt.* Den effektivitet som kommer från bra anpassning – används för att minska mängden arbete, förflyttning och behöver ingen upprepning i interaktioner – kan vara särskilt fördelaktigt för personer med funktions nedsättning.
* *Justera programmets beteende till kontexten*. Du kan använda en Personanpassare för att disambiguate mellan avsikter i en chatt-robot, till exempel när den högra tolkningen kan vara kontextuell och en storlek kanske inte passar alla.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktiv beredskap för ökad data skydd och styrning

Det är svårt att förutse vissa ändringar i reglerande sammanhang, men i allmänhet skulle det vara klokt att gå utöver det lägsta juridiska ramverket för att säkerställa respectful användning av person uppgifter och att tillhandahålla insyn och valmöjligheter relaterade till algoritmiska besluts fattande.


* Överväg att planera till en situation där det kan finnas nya begränsningar för data som samlas in från enskilda användare, och det finns behov av att visa hur den användes för att fatta beslut.
* Överväg extra beredskap där användare kan inkludera marginal känsliga populationer, barn, användare i ekonomisk sårbarhet eller användare som på annat sätt är känsliga för att påverka algoritmens manipulering.
* Fundera på den omfattande missnöjning med hur mål grupps mål och mål grupp som påverkar data insamlings program och algoritmer har spelat ut och hur man undviker beprövade strategiska fel.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proaktiva utvärderingar under projektets livs cykel

Överväg att skapa metoder för grupp medlemmar, användare och företags ägare för att rapportera om den ansvariga användningen, och skapa en process som prioriterar sin lösning och förhindrar Retaliation.

En person som tänker på sido effekter av att använda en teknik begränsas av deras perspektiv-och livs miljö. Utöka det tillgängliga antalet åsikter genom att ta in mer skiftande röster i dina team, användare eller rådgivnings tavlor. så att det är möjligt och uppmuntras att kontakta dig. Överväg att använda utbildnings-och utbildnings material för att ytterligare utöka team kunskaper i den här domänen och för att lägga till funktioner för att diskutera komplexa och känsliga ämnen.

Överväg att behandla uppgifter om ansvarig användning precis som andra crosscutting uppgifter i programmets livs cykel, till exempel uppgifter som rör användar upplevelse, säkerhet eller DevOps. Dessa uppgifter och deras krav kan inte vara en efterhand. Den ansvariga användningen bör diskuteras och kontrol leras under hela programmets livs cykel.

## <a name="questions-and-feedback"></a>Frågor och feedback

Microsoft lägger kontinuerligt till insatser i verktyg och dokument som hjälper dig att agera på dessa ansvars områden. Vårt team uppmanar dig att [ge feedback till Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D)  om du anser att ytterligare verktyg, produkt funktioner och dokument kan hjälpa dig att implementera dessa rikt linjer för att använda personanpassare.

## <a name="recommended-reading"></a>Rekommenderad läsning

* Se Microsofts sex principer för den ansvariga utvecklingen av AI som publicerats i januari 2018-boken, [den framtida beräknade](https://news.microsoft.com/futurecomputed/)
* [Vem äger framtiden?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) av Jaron Lanier.
* Cathy-O'Neil [för att avdöda matematik](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction)
* [Etik-och data vetenskap](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) från DJ Patil, Hilary Mason, Mike Loukides.
* [ACM-kod för etik](https://www.acm.org/code-of-ethics)
* [Genetisk information undiskriminering Act-GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [FATML-principer för konto bara algoritmer](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Nästa steg

[Funktioner: åtgärd och kontext](concepts-features.md).
