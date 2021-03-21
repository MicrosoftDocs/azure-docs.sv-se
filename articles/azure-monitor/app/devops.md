---
title: Prestanda övervakning av webbapp – Azure Application insikter
description: Hur Application Insights passar i devOps-cykeln
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: bc897cc0259894964e0b6164219b1e5459e824b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579561"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Djup diagnostik för webbappar och tjänster med Application Insights
## <a name="why-do-i-need-application-insights"></a>Varför behöver jag Application Insights?
Application Insights övervakar den webbapp som körs. Du får information om fel och prestanda problem och hjälper dig att analysera hur kunderna använder din app. Det fungerar för appar som körs på många plattformar (ASP.NET, Java EE, Node.js,...) och finns i molnet eller lokalt. 

![Aspekter av komplexiteten med att leverera webb program](./media/devops/010.png)

Det är viktigt att övervaka ett modernt program medan det körs. Det viktigaste är att du vill upptäcka felen innan de flesta kunder gör. Du vill också identifiera och åtgärda prestanda problem som, även om de inte är oåterkalleliga, kanske saktar ned saker eller drabbas av användare. Och när systemet presterar för din belåtenhet vill du veta vad användarna gör med det: är de som använder den senaste funktionen? Lyckas det?

Moderna webb program utvecklas i en cykel med kontinuerlig leverans: släpp en ny funktion eller förbättringar. Observera hur väl det fungerar för användarna. planera nästa steg med utveckling utifrån den kunskapen. En viktig del av den här cykeln är observations fasen. Application Insights innehåller verktyg för att övervaka webb program för prestanda och användning.

Den viktigaste aspekten av den här processen är diagnostik och diagnostik. Om programmet Miss lyckas försvinner verksamheten. Den viktigaste rollen i ett övervaknings ramverk kan därför upptäcka fel på ett tillförlitligt sätt, meddela dig omedelbart och presentera den information som krävs för att diagnostisera problemet. Detta är precis vad Application Insights gör.

### <a name="where-do-bugs-come-from"></a>Var kommer buggar från?
Fel i webb system uppstår vanligt vis vid konfigurations problem eller felaktiga interaktioner mellan deras många komponenter. Den första aktiviteten för att hantera en Live site-incident är därför att identifiera Locus av problemet: vilken komponent eller relation är orsaken?

En del av oss, de med grå hår, kan komma ihåg en enklare stund där ett dator program kördes på en dator. Utvecklarna skulle testa det noggrant innan det levereras. och har levererat den, skulle det sällan se eller fundera på det igen. Användarna måste kunna få fram de återstående buggar i många år. 

Sådant är mycket annorlunda nu. Din app har en mängd olika med olika enheter som kan köras på, och det kan vara svårt att garantera exakt samma beteende på var och en. Att vara värd för appar i molnet innebär att buggar kan åtgärdas snabbt, men det innebär även kontinuerlig konkurrens och att nya funktioner förväntas med frekventa intervall. 

I dessa fall är det enda sättet att behålla en bekräftad kontroll av antalet fel som automatisk enhets testning. Det skulle vara omöjligt att manuellt testa om allting på varje leverans. Enhets test är nu en vanliga del av bygg processen. Verktyg som Xamarin Test Cloud hjälp genom att tillhandahålla automatiserat UI-testning på flera webb läsar versioner. Dessa test regler gör det möjligt för oss att hoppas att frekvensen av buggar som finns i en app kan hållas till ett minimum.

Vanliga webb program har många Live-komponenter. Förutom klienten (i en webbläsare eller enhets app) och webb servern är det troligt att det finns stor server dels bearbetning. Server delen är kanske en pipeline med komponenter eller en glesare samling av samarbets delar. Och många av dem är inte i din kontroll – de är externa tjänster som du är beroende av.

I konfigurationer som dessa kan det vara svårt och kostnads fritt att testa eller förutse varje möjligt felläge, förutom i själva Live-systemet. 

### <a name="questions-"></a>Frågor...
Några frågor som vi frågar när vi utvecklar ett webb system:

* Kraschar min app? 
* Vad är exakt hänt? – Om det inte gick att ta emot en begäran vill du veta hur det gick dit. Vi behöver en spårning av händelser...
* Är min app tillräckligt snabb? Hur lång tid tar det att svara på vanliga förfrågningar?
* Kan servern hantera belastningen? Är svars tiden konstant när antalet begär Anden ökar?
* Hur svar är mina beroenden – REST-API: er, databaser och andra komponenter som min app anropar. I synnerhet om systemet är långsamt, är det min komponent eller jag får jag långsamma svar från någon annan?
* Är min app upp eller ned? Kan den ses från hela världen? Meddela mig om det stoppas...
* Vad är rotor saken? Gick det inte att utföra någon komponent eller ett beroende? Är det ett kommunikations problem?
* Hur många användare påverkas? Om jag har fler än ett problem att ta itu, vilket är den viktigaste?

## <a name="what-is-application-insights"></a>Vad är Application Insights?
![Grundläggande arbets flöde för Application Insights](./media/devops/020.png)

1. Application Insights instrumenter din app och skickar telemetri om den medan appen körs. Du kan antingen bygga Application Insights SDK i appen eller använda instrumentering vid körning. Den tidigare metoden är mer flexibel, eftersom du kan lägga till din egen telemetri i de vanliga modulerna.
2. Telemetrin skickas till Application Insights-portalen, där den lagras och bearbetas. (Även om Application Insights finns i Microsoft Azure kan den övervaka alla webbappar – inte bara Azure-appar.)
3. Telemetri visas i form av diagram och tabell händelser.

Det finns två huvud typer av telemetri: aggregerade och råa instanser. 

* Instans data inkluderar exempelvis en rapport över en begäran som har tagits emot av din webbapp. Du kan söka efter och granska information om en begäran med hjälp av sökverktyget i Application Insights portalen. Instansen skulle omfatta data, till exempel hur länge din app tog att svara på begäran, samt den begärda URL: en, ungefärlig plats för klienten och andra data.
* Sammanställda data inkluderar antal händelser per tidsenhets tid, så att du kan jämföra antalet begär Anden med svars tiderna. Den innehåller också genomsnitt av mått, till exempel svars tider för begäran.

Huvud kategorier av data är:

* Begär anden till din app (vanligt vis HTTP-begäranden) med data på URL, svars tid och lyckade eller misslyckade.
* Beroenden – REST-och SQL-anrop som görs av din app, även med URI, svars tider och lyckade
* Undantag, inklusive stack spårningar.
* Sid visnings data, som kommer från användarnas webbläsare.
* Mått som prestanda räknare, samt mått som du skriver själv. 
* Anpassade händelser som du kan använda för att spåra affärs händelser
* Logga spårningar som används för fel sökning.

## <a name="case-study-real-madrid-fc"></a>Fallstudie: Real Madrid F.C.
Webb tjänsten för [verklig Madrid fotbolls klubb](https://www.realmadrid.com/) har cirka 450 000 000 fläktar runtom i världen. Fläktar får åtkomst till det både via webbläsare och klubbens mobila appar. Fläktar kan inte bara boka biljetter, utan även få åtkomst till information och videoklipp på resultat, spelare och kommande spel. De kan söka med filter, t. ex. antal mål resultat. Det finns också länkar till sociala medier. Användar upplevelsen är mycket personlig och utformad som en tvåvägs kommunikation för att engagera fläktar.

Lösningen [är ett system med tjänster och program på Microsoft Azure](https://www.microsoft.com/inculture/sports/real-madrid/). Skalbarhet är ett nyckel krav: trafiken är variabel och kan komma åt mycket höga volymer under och runt matchningar.

För verkliga Madrid är det viktigt att övervaka systemets prestanda. Azure Application insikter ger en omfattande vy över systemet, vilket garanterar en tillförlitlig och hög service nivå. 

Föreningen får också djupgående förståelse för dess fläktar: var de är (endast 3% är i Spanien), vilken intresserar de har i spelare, historiska resultat och kommande spel, och hur de svarar för att matcha resultat.

De flesta av dessa telemetridata samlas in automatiskt med ingen tillagd kod, som förenklar lösningen och minskad drifts komplexitet.  För verkliga Madrid Application Insights-avtal med 3 800 000 000 telemetri punkter varje månad.

Real Madrid använder Power BI-modulen för att visa sin telemetri.

![Power BI vy av Application Insights telemetri](./media/devops/080.png)

## <a name="smart-detection"></a>Smart identifiering
[Proaktiv diagnostik](./proactive-diagnostics.md) är en ny funktion. Utan någon speciell konfiguration av dig kan Application Insights automatiskt identifiera och varna dig om ovanliga ökningar i en felaktig frekvens i din app. Det är tillräckligt smart att ignorera en bakgrund med tillfälliga haverier, och kan också medföra att de bara står i proportion till en ökning i begär Anden. Till exempel, om det uppstår ett fel i en av de tjänster som du är beroende av, eller om den nya versionen som du precis har distribuerat inte fungerar så bra, så vet du om det så snart du tittar på din e-post. (Och det finns Webhooks så att du kan utlösa andra appar.)

En annan aspekt av den här funktionen utför en daglig analys av din telemetri, som söker efter ovanliga mönster på prestanda som är svåra att upptäcka. Den kan till exempel hitta långsamma prestanda som är kopplade till ett visst geografiskt område eller med en viss webb läsar version.

I båda fallen anger aviseringen inte bara de symtom som identifieras, men ger dig även de data du behöver för att kunna diagnostisera problemet, till exempel relevanta undantags rapporter.

![E-post från proaktiv diagnostik](./media/devops/030.png)

Kunden Samtec: "under en nyligen utförd funktion start punkt hittade vi en under skalad databas som påträffade resurs gränserna och orsakade timeout. Proaktiva identifierings aviseringar kom genom det som vi sorterar problemet, mycket nära real tid som annonserat. Den här aviseringen tillsammans med Azures plattforms aviseringar hjälpte oss att snabbt åtgärda problemet. Total stillestånds tid < 10 minuter. "

## <a name="live-metrics-stream"></a>Live-ström med mätvärden
Det kan vara en Anxious upplevelse att distribuera den senaste versionen. Om det finns några problem vill du veta mer om dem direkt, så att du kan säkerhetskopiera vid behov. Live Metrics Stream ger dig viktiga mått med en fördröjning på ungefär en sekund.

![Live-mått](./media/devops/0040.png)

Och låter dig omedelbart inspektera ett exempel på fel eller undantag.

![Händelser för Live-avbrott](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Programkarta
Program kartan identifierar automatiskt programtopologin, vilket ger prestanda informationen ovanpå, så att du enkelt kan identifiera Flask halsar i prestanda och problematiska flöden i den distribuerade miljön. Det gör att du kan identifiera program beroenden på Azure-tjänster. Du kan prioritering problemet genom att förstå om det är kod relaterat eller beroende relaterat och från en enda plats i detalj i relaterad diagnostik. Ditt program kan till exempel Miss lyckas på grund av prestanda försämring i SQL-nivån. Med program kartan kan du se den direkt och gå in i SQL Index Advisor eller ställa frågor till insikter.

![Programkarta](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Application Insights analys
Med [Analytics](../logs/log-query-overview.md)kan du skriva godtyckliga frågor på ett kraftfullt SQL-liknande språk.  Att diagnostisera hela app-stacken är enkelt att ansluta till olika perspektiv och du kan ställa rätt frågor för att korrelera tjänst prestanda med affärs mått och kund upplevelse. 

Du kan fråga alla din telemetri-instans och dina mått rå data som lagras i portalen. Språket innehåller filter, Join, aggregation och andra åtgärder. Du kan beräkna fält och utföra statistisk analys. Det finns både tabell-och grafiska visualiseringar.

![Diagram över analys fråga och resultat](./media/devops/0025.png)

Det är till exempel enkelt att:

* Segmentera programmets prestanda data efter kund nivåer för att förstå deras erfarenhet.
* Sök efter vissa felkoder eller anpassade händelse namn under pågående webbplats undersökningar.
* Lär dig mer om användningen av appar för vissa kunder för att förstå hur funktioner förvärvas och antas.
* Spåra sessioner och svars tider för specifika användare för att ge support och drifts team möjlighet att ge omedelbar kund support.
* Bestäm vilka appar som används ofta för att besvara frågor om funktions prioritering.

Kund DNN: "Application Insights har gett oss en del av ekvationen för att kunna kombinera, sortera, fråga och filtrera data efter behov. Att låta vårt team använda sin egen Ingenuity och erfarenhet av att söka efter data med ett kraftfullt frågespråk har gett oss möjlighet att hitta insikter och lösa problem som vi inte ens visste. Många intressanta svar kommer från frågorna som börjar med *"Jag undrar om...".*

## <a name="development-tools-integration"></a>Integrering av utvecklingsverktyg
### <a name="configuring-application-insights"></a>Konfigurera Application Insights
Visual Studio och Sol förmörkelse innehåller verktyg för att konfigurera rätt SDK-paket för projektet som du utvecklar. Det finns ett meny kommando för att lägga till Application Insights.

Om du råkar använda ett spårnings loggnings ramverk, till exempel Log4N, NLog eller system. Diagnostics. trace, får du alternativet att skicka loggarna till Application Insights tillsammans med den andra Telemetrin, så att du enkelt kan korrelera spåren med begär Anden, beroende anrop och undantag.

### <a name="search-telemetry-in-visual-studio"></a>Sök telemetri i Visual Studio
När du utvecklar och felsöker en funktion kan du Visa och söka i telemetri direkt i Visual Studio med hjälp av samma Sök funktioner som i webb portalen.

När Application Insights loggar ett undantag kan du visa data punkten i Visual Studio och gå direkt till relevant kod.

![Visual Studio-sökning](./media/devops/060.png)

Under fel sökning har du möjlighet att behålla Telemetrin i din utvecklings dator och visa den i Visual Studio, men utan att skicka den till portalen. Det här lokala alternativet förhindrar att du blandar fel sökning med produktions telemetri.

### <a name="work-items"></a>Arbets objekt
När en avisering aktive ras kan Application Insights automatiskt skapa ett arbets objekt i ditt arbets spårnings system.

## <a name="but-what-about"></a>Men vad händer om...?
* [Sekretess och lagring](./data-retention-privacy.md) – din telemetri sparas på Azures säkra servrar.
* Prestanda – påverkan är mycket låg. Telemetri är satsvis.
* [Priser](./pricing.md) – du kan sätta igång kostnads fritt och fortsätta när du befinner dig i låg volym.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
Det är enkelt att komma igång med Application Insights. Huvud alternativen är:

* [IIS-servrar](./monitor-performance-live-website-now.md)och även för [Azure App Service](./app-insights-overview.md).
* Instrumentera ditt projekt under utveckling. Du kan göra detta för [ASP.net](./asp-net.md) -eller [Java](./java-get-started.md) -appar, samt [Node.js](./nodejs.md) och en värd av [andra typer](./platforms.md). 
* Instrumentera en [webb sida](./javascript.md) genom att lägga till ett kort kodfragment.

