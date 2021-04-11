---
title: Metod och process för att börja använda Azure Data Catalog
description: I den här artikeln beskriver vi en metod och process för organisationer som funderar på att börja använda Azure Data Catalog. Den omfattar bland annat hur du fastställer en vision, identifierar viktiga användningsområden och väljer ett pilotprojekt.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: ebfaa59f1800da084d611f6e050a59da63a7f195
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674758"
---
# <a name="approach-and-process-for-adopting-azure-data-catalog"></a>Metod och process för att börja använda Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Den här artikeln hjälper dig att komma igång med **Azure Data Catalog** i din organisation. Fokusera på följande tre huvudpunkter för att komma igång med **Azure Data Catalog** på ett smidigt sätt: fastställ din vision, identifiera viktiga användningsområden inom organisationen och välj ett pilotprojekt.

## <a name="introducing-the-azure-data-catalog"></a>Introduktion till Azure Data Catalog

Inom arbetslivet har förväntningarna på hur man hittar expertinformation om datatillgångar förändrats. I dagens läge, med arbetsplatsernas allt större användning av sociala medier som Yammer, väntar sig medarbetarna att de snabbt ska kunna få hjälp och råd på många olika områden. **Azure Data Catalog** hjälper företag och team att konsolidera information om företagets datatillgångar i ett centrallager. Datakonsumenter kan identifiera dessa datatillgångar och få information som skapats av ämnesexperter.

I den här artikeln beskriver vi en metod för att komma igång med **Azure Data Catalog**. Den här artikeln innehåller en implementeringsplan för Data Catalog för det fiktiva företaget Adventure Works.

**Vad är Azure Data Catalog?**

**Azure Data Catalog** är en helt hanterad tjänst i Azure och en företagsomfattande katalog med information (metadata) där användaren själv kan identifiera datakällor. Med Data Catalog kan du registrera, identifiera, kommentera och ansluta till datatillgångar. Data Catalog är utformat för att hantera olika informationstillgångar så att de enkelt att hitta datatillgångar, förstå dem och ansluta till dem. Det minskar den tid det tar att få insikter från tillgängliga data och ökar värdet för organisationer. Mer information finns i [Microsoft Azure Data Catalog](https://azure.microsoft.com/services/data-catalog/).

## <a name="azure-data-catalog-adoption-plan"></a>Implementeringsplan för Azure Data Catalog

En implementeringsplan för **Azure Data Catalog** beskriver hur du informerar intressenter och användare om fördelarna med att använda tjänsten och vilken typ av utbildning användarna kommer att få. Hur väl övergången till Data Catalog kommer att gå beror till stor del på hur väl du informerar användare och intressenter om tjänstens värde. Den primära målgruppen för en inledande implementeringsplan är tjänstens användare. Oavsett hur mycket olika intressenter satsar kommer implementeringen inte att bli en framgång om inte de användare eller kunder som erbjuds tjänsterna i Data Catalog engagerar sig. I den här artikeln förutsätter vi därför att du har stöd från nödvändiga intressenter och fokuserar på att skapa en plan för att få användarna att övergå till Data Catalog.
En effektiv implementeringsplan får medarbetarna att se potentialen i Data Catalog och ger dem den information och vägledning som krävs för att kunna utnyttja den potentialen. Användarna måste förstå hur Data Catalog hjälper dem att utföra arbetet på ett bättre sätt. När medarbetarna ser hur Data Catalog kan hjälpa dem använda data på ett bättre sätt blir mervärdet mycket tydligt. Förändringar är alltid svåra, så en effektiv plan måste ta hänsyn till utmaningarna.

En implementeringsplan hjälper dig att berätta vad som är viktigt för att användarna ska lyckas och nå sina mål. En typisk plan beskriver hur Data Catalog gör användarnas liv enklare. Den består av följande delar:

* **Vision** – En vision hjälper dig att diskutera implementeringsplanen med användare och intressenter på ett kort och koncist sätt. Visionen är ditt snabba försäljningsargument.
* **Pilotteam och påverkare** – Genom att inhämta information från ett pilotteam och påverkare kan du presentera Data Catalog för team och användare på ett bättre sätt. Påverkare kan hjälpa till att vägleda andra användare. Det hjälper dig också att se vad som hindrar och vad som främjar implementeringen.
* **Informations- och marknadsföringsplan** – Planen hjälper användarna att förstå hur Data Catalog kan hjälpa dem och underlättar en organisk implementering i de olika teamen och i slutänden hela organisationen.
* **Utbildningsplan** – Omfattande utbildning ger vanligtvis en framgångsrik implementering och goda resultat.

Här kommer några tips på hur du formulerar en implementeringsplan för **Azure Data Catalog**.

## <a name="define-your-data-catalog-project-vision"></a>Fastställ din projektvision för Data Catalog

Det första steget i att skapa en implementeringsplan för **Azure Data Catalog** är att skriva ned en målbeskrivning för vad du vill uppnå. Det är bäst att hålla visionen allmän, men ändå koncis nog för att fastställa både kortsiktiga och långsiktiga mål.

Här följer några tips om hur du skapar en vision:

* **Hitta den viktigaste motiveringen till implementering** – Fundera på de specifika behov som rör datakällor i företaget som Data Catalog kan hantera. Det hjälper dig också att formulera de största fördelarna med att använda Data Catalog. Det kan till exempel finnas gemensamma datakällor som alla nya medarbetare behöver känna till och använda, eller viktiga och komplicerade datakällor som bara några få nyckelpersoner verkligen förstår. **Azure Data Catalog** kan göra det enkelt att identifiera dessa datakällor och förstå hur de kan användas, så att sådana vanliga svårigheter kan hanteras direkt och tidigt under implementeringsprocessen.
* **Var klar och tydlig** – En tydlig vision ger hela organisationen samma uppfattning om värdet av Data Catalog för organisationen och om hur visionen stöder organisationens mål.
* **Inspirera medarbetarna att vilja använda Data Catalog** – Din vision och din kommunikationsplan ska fungera som inspiration så att medarbetarna inser att Data Catalog kan hjälpa dem att hitta och ansluta till datakällor och därmed få bättre resultat av dataanvändningen.
* **Ange specifika mål och tidsfrister** – Det säkerställer att implementeringsplanen har specifika och realistiska mål. Med tidsfrister behåller du fokus och kan ha milstolpar där framgångarna mäts.

Här följer en exempelvision i en implementeringsplan för Data Catalog för det fiktiva företaget Adventure Works.

**Azure Data Catalog** ger Adventure Worker ekonomi teamet möjlighet att samar beta på viktiga data källor, så att varje grupp medlem lätt kan hitta och använda de data som de behöver och de kan dela med sig av sina kunskaper med teamet som helhet.

När du har en tydlig vision är det dags att hitta ett lämpligt pilotprojekt för Data Catalog. Det finns ofta flera olika användningsområden för Data Catalog, så nästa avsnitt innehåller några tips på hur du hittar lämpliga områden.

## <a name="identify-data-catalog-business-use-cases"></a>Identifiera användningsområden för Data Catalog

Tala med experter från olika affärsenheter för att identifiera lämpliga användningsområden och verksamhetsproblem som kan lösas med Data Catalog. Granska befintliga problem med att hitta och förstå datatillgångar. Behöver teamen till exempel fråga flera personer i organisationen som har relevanta datakällor innan de får nödvändig information om datatillgångarna?

Det är bäst att välja användningsområden där du lätt kan nå goda resultat, dvs. områden som är viktiga och som med stor sannolikhet kan lösas på ett bra sätt med Data Catalog.

Här följer några tips på hur du identifierar användningsområden:

* **Definiera teamets mål** – Hur uppnår teamet sina mål? Fokusera inte på Data Catalog ännu, eftersom du vill vara objektiv i det här skedet. Kom ihåg att det handlar om verksamhetsresultat, inte om tekniken.
* **Definiera problem i verksamheten** – Vilka problem har teamet med att hitta och få information om datatillgångar? Till exempel finns kanske informationen om viktiga datakällor i Excel-arbetsböcker i en nätverksmapp, och teamet tillbringar mycket tid med att hitta arbetsböckerna.
* **Förstå hur förändringskulturen ser ut i teamet** – Många problem i samband med förändring gäller motstånd mot förändring snarare än motstånd mot att använda ett nytt verktyg. Hur ett team reagerar på förändringar är viktigt när du identifierar användningsområden. Befintliga processer kanske finns ”för att vi alltid har gjort så” eller på grund av tanken ”varför ändra på något som fungerar”? Det är alltid lättast att implementera nya verktyg eller processer när de medarbetare som påverkas förstår värdet av förändringen liksom vikten av problemen som behöver lösas.
* **Behåll fokus som är relaterad till data till gångar** – när du diskuterar affärs problem som ett team är på plats måste du "klippa igenom ogräs" och fokusera på vad som är relevant för att utnyttja företagets data till gångar mer effektivt.

Här är några exempel på användningsområden för Data Catalog:

### <a name="example-use-cases"></a>Exempel på användningsområden

* **Registrera centrala värdefulla datakällor** – IT-avdelningen hanterar datakällor som används i organisationen. IT-avdelningen kan vara först med Data Catalog genom att registrera och kommentera företagets gemensamma datakällor.
* **Registrera teambaserade datakällor** – De olika teamen har användbara verksamhetsspecifika datakällor. Kom igång med **Azure Data Catalog** genom att identifiera och registrera viktiga datakällor som används av många olika team och få med teamens specifika kunskaper genom kommentarer i **Azure Data Catalog**.
* **Affärsinformation med självbetjäning** – Teamen lägger mycket tid på att kombinera data från flera källor. Registrera och kommentera datakällor på en central plats för att inte behöva söka manuellt efter datakällor.

Mer information om scenarier för Data Catalog finns i [vanliga scenarier för Azure Data Catalog](data-catalog-common-scenarios.md).

När du har identifierat några användningsområden för Data Catalog bör du se ett mönster av vanliga scenarier. I nästa avsnitt beskriver vi hur du identifierar ett första pilotprojekt baserat på ett användningsområde.

## <a name="choose-a-data-catalog-pilot-project"></a>Välja pilotprojekt för Data Catalog

En stor framgångsfaktor är att börja litet och enkelt. Ett väldefinierat pilotprojekt med begränsad omfattning hjälper till att hålla projektet igång utan att fördröjas av att projektet är för komplicerat eller har för många deltagare. Det är också viktigt att ha blandade användare, från sådana som gärna provar nyheter till skeptiker. De användare som tar till sig lösningen hjälper dig att formulera din framtida informations- och marknadsföringsplan. Skeptiker hjälper dig att identifiera och åtgärda hinder och problem. När skeptikerna blir experter kan du använda deras feedback för att identifiera framgångsfaktorer.

Pilotprojektet bör fasa in de verksamhetsmål som du vill nå med hjälp av Data Catalog. Du kan utöka användarbasen medan du lär dig mer av pilotprojektet. Ett stängt pilotprojekt är bra till en början för att skapa mätbara framgångar, men slutmålet är organisk tillväxt eller viral spridning. När användningen av Data Catalog växer organiskt har användarna kontrollen över sin egen dataanvändning och kan påverka och uppmuntra andra att använda och bidra till katalogen.

### <a name="target-the-right-team"></a>Satsa på rätt team

När du väljer pilotprojekt ska du satsa på ett team med intressanta scenarier som löser befintliga verksamhetsproblem. Exempel: En verksamhetsanalytiker skapar rapporter från en SQL Server-databas. Problemet är att de har blivit medvetna om data källan endast efter att ha pratat med flera kollegor. Efter att du slösat upp tiden som försöker hitta vilka data källor som ska användas, har de hittat en Excel-arbetsbok som innehåller en beskrivning av varje data källa. Även om Excel-arbetsboken på ett bra sätt beskriver de tabeller som de behöver, skulle de snabbt kunna hitta dessa data källor om de registrerades och kommenterades i **Azure Data Catalog**.

### <a name="identify-data-heroes"></a>Identifiera datahjältar

Ditt första pilotprojekt bör innehålla några personer som skapar data och några som använder data, så att teamet är balanserat.

**Dataproducenter** är personer med expertkunskaper om datakällor. Till exempel har David i ett annat team arbetat mycket med Adventure Works viktiga datakällor. Före införandet av **Azure Data Catalog** hade David skapat en Excel-arbetsbok där han samlat in information om Adventure Works datakällor.

**Datakonsumenter** är personer med expertkunskaper i att lösa verksamhetsproblem med hjälp av data. Till exempel är använder verksamhetsanalytikern Nancy Adventure Works SQL-datakällor för att analysera data.

Ett av de verksamhetsproblem som **Azure Data Catalog** löser är sammankoppling av **dataproducenter** och **datakonsumenter**. Katalogen fungerar som ett centrallager för information om företagets datakällor. David registrerar Adventure Works- och SQL Server-datakällor i Data Catalog. Genom att använda gemensamt skapade kan alla användare som identifierar den här data källan dela sitt yttrande om data, förutom att använda de data som de har identifierat. Till exempel identifierar Nancy datakällor genom att söka i katalogen och delar med sig av sina specialkunskaper om dem.  Nu kan andra i organisationen dra nytta av kunskapen hon delat med sig av genom att söka i datakatalogen.

* Mer information om hur du registrerar datakällor finns i [Registrera datakällor](data-catalog-get-started.md).
* Mer information om hur du identifierar datakällor finns i [Söka efter datakällor](data-catalog-get-started.md).

### <a name="start-small-and-focused"></a>Börja litet och fokuserat

För de flesta pilotprojekt i företag bör du fylla katalogen med värdefulla datakällor så att användare i verksamheten snabbt kan se värdet av Data Catalog. IT-avdelningen är en bra plats att börja identifiera gemensamma datakällor som kan vara av intresse för pilotteamet. Vi rekommenderar att du använder registreringsverktyget för datakällor i **Azure Data Catalog** för de datakällor som stöds, till exempel SQL Server. Med registreringsverktyget för datakällor kan du registrera många olika typer av datakällor, till exempel SQL Server- och Oracle-databaser samt rapporter i SQL Server Reporting Services. En fullständig lista över aktuella datakällor finns i [Datakällor som stöds i Azure Data Catalog](data-catalog-dsr.md).

När du har identifierat och registrerat viktiga datakällor kan du också importera beskrivningar av datakällor som lagrats på andra platser. Med Data Catalog-API:et kan användarna läsa in beskrivningar och kommentarer från en annan plats, som den Excel-arbetsbok som David har skapat och upprätthåller.

I nästa avsnitt beskrivs ett exempelprojekt från företaget Adventure Works.

### <a name="an-example-project"></a>Ett exempelprojekt

I det här exemplet skapar verksamhetsanalytikern Nancy rapporter för sitt team med hjälp av data från en SQL Server-databas. Problemet är att de har blivit medvetna om data källan endast efter att ha pratat med flera kollegor. De har snabbt hittat dessa data källor om de registrerades och kommenterades på en central plats, till exempel **Azure Data Catalog**.

För att visa hur lätt Nancy och hennes team kan hitta värdefulla datakällor kan du fylla katalogen med information (metadata) om datakällorna med hjälp av registreringsverktyget för datakällor. På så vis finns information om databasen tillgänglig för hela teamet och företaget, inte bara för några få personer. När datakällor väl är registrerade i Data Catalog kan Nancy och teamet enkelt hitta datakällorna som de använder. Resultatet är en mer omfattande och relevant datakatalog för teamet och företaget. När fler team börjar använda Data Catalog blir det lättare att hitta och använda verksamhetsdata. Det skapar i sin tur en mer datacentrerad kultur så att företaget får största möjliga nytta av sina data.

Mer information om registreringsverktyget för datakällor finns i [Kom igång med Azure Data Catalog](data-catalog-get-started.md).

Som en del i pilotprojektet använder Nancys team även datakällor som beskrivs i den Excel-arbetsbok som David och hans kollegor upprätthåller. Eftersom andra team i företaget också använder Excel-arbetsböcker för att beskriva datakällor beslutar IT-teamet att skapa ett verktyg för att migrera Excel-arbetsboken till Data Catalog. Genom att importera befintliga kommentarer med hjälp av Data Catalog-REST-API:et kan pilotprojektteamet få en komplett datakatalog med metadata som extraherats från datakällorna med registreringsverktyget för datakällor. Katalogen kompletteras med information som tidigare dokumenterats av dataproducenter och konsumenter utan att något behöver matas in manuellt. När företagets datakatalog växer kan organisationen använda registreringsverktyget för datakällor för gemensamma datakällor, till exempel Data Catalog-API:et för anpassade källor och ovanliga scenarier.

> [!NOTE]
> Vi har skrivit ett exempelverktyg där API:et för **Azure Data Catalog** används för att migrera en Excel-arbetsbok till Data Catalog. Mer information om Data Catalog-API:et och exempelverktyget finns i [Ladda ned kodexempel för ad hoc-arbetsbok](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets) och i dokumentationen för [Azure Data Catalog-REST-API:et](/rest/api/datacatalog/).

När pilotprojektet är slutfört är det dags att genomföra implementeringsplanen.

### <a name="execute"></a>Genomförande

Nu har du identifierat användningsområden för Data Catalog liksom ditt första projekt. Du har dessutom registrerat Adventure Works viktigaste datakällor och lagt till information från den befintliga Excel-arbetsboken med verktyget som IT-avdelningen skrev. Nu är det dags att arbeta med pilotteamet för att inleda implementeringsprocessen för Data Catalog.

Här följer några tips för att komma igång:

* **Se till att användarna är entusiastiska** – Användare i verksamheten kommer att vara entusiastiska om de är övertygade om att **Azure Data Catalog** kommer att göra deras liv enklare. Försök att hålla diskussionerna kring lösningen och fördelarna, inte tekniken.
* **Underlätta förändringen** – Börja litet och beskriv planen för användare i verksamheten. För att lyckas är det viktigt att användarna är engagerade från början, så att de kan påverka resultatet och känna sig delaktiga i lösningen.
* **Leta rätt på tidiga brukare** – Tidiga brukare är användare i verksamheten som arbetar med stor passion och gärna berättar för kollegorna om fördelarna med **Azure Data Catalog**.
* **Riktad utbildning** – Användare i verksamheten behöver inte veta allt om Data Catalog, så rikta utbildningen så att teamens specifika mål tas upp. Fokusera på vad användarna kommer att göra och hur deras arbetsuppgifter kan komma att ändras när de införlivar **Azure Data Catalog** i sina dagliga rutiner.
* **Var beredd att misslyckas** – Om pilotprojektet inte går som planerat är det dags att omvärdera och identifiera förbättringsområden. Rätta till problem i pilotprojektet innan du fortsätter till ett större område.

Boka in ett inledande möte för att diskutera förväntningar på pilotprojektet och ge grundläggande utbildning innan pilotteamet börjar använda Data Catalog.

### <a name="set-expectations"></a>Fastslå förväntningar

Förväntningar och mål hjälper användare i verksamheten att fokusera på specifika resultat. För att se till att projektet framskrider som önskat kan du ge regelbundna hemuppgifter (kanske dagliga eller veckovisa, beroende på pilotprojektets omfattning och längd). En av de mest värdefulla funktionerna i Data Catalog är att flera användare kan bidra till datatillgångarna, så att användare i verksamheten kan dra nytta av de kunskaper som finns om företagets data. En bra hemuppgift är att be alla medlemmar i pilotteamet att registrera eller kommentera minst en datakälla de har använt. Se [Registrera en datakälla](data-catalog-get-started.md) och [Så här kommenterar du datakällor](data-catalog-get-started.md).

Träffa gruppen regelbundet och gå igenom några av anteckningarna. Bra anteckningar om datakällorna är kärnan i en lyckad implementering av Data Catalog eftersom de ger meningsfull information om datakällan på en central plats. Utan bra anteckningar förblir kunskapen om datakällor utspridd i hela företaget. Se [Så här kommenterar du datakällor](data-catalog-get-started.md).

Det ultimata testet av projektet är förstås om användarna kan hitta nödvändiga datakällor och förstå hur de ska användas. Pilotanvändarna bör regelbundet testa katalogen för att kontrollera att datakällorna de använder i sitt dagliga arbete är relevanta. Om en datakälla som behövs saknas eller inte är korrekt kommenterad, ska detta fungera som en påminnelse om att registrera ytterligare datakällor eller ange ytterligare anteckningar. På så vis läggs mervärde till pilotprojektet. Det skapar också effektiva vanor som sprids till övriga team när pilotprojektet är avslutat.

### <a name="provide-training"></a>Erbjud utbildning

Utbildningen ska vara precis tillräcklig för att användarna ska komma igång och skräddarsydd för pilotteamets specifika mål och erfarenheter. Du kan följa stegen i artikeln [Kom igång med Azure Data Catalog](data-catalog-get-started.md) för att komma igång med utbildningen. Du kan dessutom ladda ned [Presentation av utbildning för pilotprojekt i Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/blob/master/Azure%20Data%20Catalog%20Training.pptx?raw=true). PowerPoint-presentationen bör hjälpa dig att komma igång med att introducera Data Catalog för pilotteamet.

## <a name="conclusion"></a>Slutsats

När pilotteamet fungerar någorlunda smidigt och du har nått dina inledande mål ska användningen av Data Catalog utvidgas till flera team. Använd och utveckla vad du har lärt dig genom pilotprojektet för att utvidga Data Catalog till hela organisationen.

Tidiga brukare som har deltagit i pilotprojektet kan vara till stor nytta när det gäller att sprida information om fördelarna med Data Catalog. De kan berätta för andra team hur Data Catalog har hjälpt deras team lösa verksamhetsproblem och gjort det lättare att hitta datakällor och dela information om datakällorna de använder. Till exempel kan tidiga brukare i Adventure Works pilotteam visa andra hur lätt det är att hitta information om Adventure Works datatillgångar som en gång var svåra att hitta och förstå.

Den här artikeln handlar om att komma igång med **Azure Data Catalog** i din organisation. Vi hoppas att du kunde starta ett pilotprojekt i Data Catalog och utvidga Data Catalog till hela organisationen.

## <a name="next-steps"></a>Nästa steg

[Skapa Azure Data Catalog](data-catalog-get-started.md)
