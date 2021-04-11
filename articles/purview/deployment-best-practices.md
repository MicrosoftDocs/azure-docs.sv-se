---
title: Bästa metoder för distribution
description: Den här artikeln innehåller metod tips för att distribuera Azure-avdelningens kontroll. Med Azure avdelningens kontroll kan alla användare registrera, identifiera, förstå och använda data källor.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e02ad9187743603d46259d70965e49d6839ecd71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104949846"
---
# <a name="azure-purview-deployment-best-practices"></a>Metod tips för Azure avdelningens kontroll-distribution

Den här artikeln identifierar vanliga uppgifter som kan hjälpa dig att distribuera avdelningens kontroll till produktion. Dessa uppgifter kan utföras i faser, under en månad eller mer. Även organisationer som redan har distribuerat avdelningens kontroll kan använda den här guiden för att se till att de får ut mesta möjliga av sina investeringar.

En väl planerad distribution av en data styrnings plattform (till exempel Azure avdelningens kontroll) kan ge följande fördelar:

- Bättre data identifiering
- Förbättrat analytiskt samarbete
- Maximerad avkastning på investeringen.

## <a name="prerequisites"></a>Förutsättningar

* Åtkomst till Microsoft Azure med en utvecklings-eller produktions prenumeration
* Möjlighet att skapa Azure-resurser inklusive avdelningens kontroll
* Åtkomst till data källor som Azure Data Lake Storage eller Azure SQL i test-, utvecklings-eller produktions miljöer
  * För Data Lake Storage är den roll som krävs för att genomsöka rollen läsare
  * För SQL måste identiteten kunna fråga tabeller om sampling av klassificeringar
* Åtkomst till Azure Security Center eller möjlighet att samar beta med Security Centers administratör för data etiketter

## <a name="identify-objectives-and-goals"></a>Identifiera mål och mål

Många organisationer har startat sin data styrnings resa genom att utveckla enskilda lösningar som uppfyller specifika krav för isolerade grupper och data domäner i hela organisationen. Även om upplevelsen kan variera beroende på bransch, produkt och kultur, hittar de flesta organisationer det svårt att upprätthålla konsekventa kontroller och principer för dessa typer av lösningar.

Några av de vanliga data styrnings mål som du kanske vill identifiera i de tidiga faserna är:

* Maximera affärs värdet för dina data
* Aktivera en data kultur där data konsumenter lätt kan hitta, tolka och lita på data
* Öka samarbetet mellan olika affär senheter för att ge en konsekvent data upplevelse
* Främja innovation genom att påskynda data analysen och dra nytta av fördelarna med molnet
* Minska tiden för att identifiera data med hjälp av självbetjänings alternativ för olika kunskaps grupper
* Minska tiden till marknaden för leverans av analys lösningar som förbättrar tjänsten för kunderna
* Minska de drift risker som beror på användningen av domänanslutna verktyg och teknik som inte stöds

Den allmänna metoden är att dela upp de övergripande målen i olika kategorier och mål. Några exempel är:

|Kategori|Mål|
|---------|---------|
|Identifiering|Administratörs användare bör kunna söka igenom Azure-och icke-Azure-datakällor (inklusive lokala källor) för att samla in information om data till gångarna automatiskt.|
|Klassificering|Plattformen ska automatiskt klassificera data baserat på en sampling av data och tillåta manuell åsidosättning med anpassade klassificeringar.|
|Förbrukning|Företags användarna bör kunna hitta information om varje till gång för både affärs-och tekniska metadata.|
|Ursprung|Varje till gång måste visa en grafisk vy över underliggande data uppsättningar så att användarna förstår de ursprungliga källorna och vilka ändringar som har gjorts.|
|Samarbete|Plattformen måste ge användarna möjlighet att samar Beta genom att ange ytterligare information om varje data till gång.|
|Rapportera|Användarna måste kunna visa rapportering om datafastigheten, inklusive känsliga data och data som behöver ytterligare berikning.|
|Datastyrning|Plattformen måste tillåta administratören att definiera principer för åtkomst kontroll och automatiskt genomdriva data åtkomst baserat på varje användare.|
|Arbetsflöde|Plattformen måste kunna skapa och ändra arbets flöde så att det är enkelt att skala ut och automatisera olika uppgifter i plattformen.|
|Integrering|Andra tredjeparts tekniker, till exempel biljetter eller dirigering, måste kunna integreras i plattformen via skript eller REST-API: er.|

## <a name="top-questions-to-ask"></a>Vanliga frågor att ställa

När din organisation godkänner de övergripande målen och målen finns det många frågor från flera grupper. Det är viktigt att samla in dessa frågor för att kunna utforma en plan för att lösa alla problem. Några exempel på frågor som du kan köra i första fasen:

1. Vilka är de viktigaste data källorna och data systemen i organisationen?
2. Vilka är mina alternativ för data källor som inte stöds än av avdelningens kontroll?
3. Hur många avdelningens kontroll-instanser behöver vi?
4. Vilka är användarna?
5. Vem kan genomsöka nya data källor?
6. Vem kan ändra innehållet i avdelningens kontroll?
7. Vilken process kan jag använda för att förbättra data kvaliteten i avdelningens kontroll?
8. Hur startar du plattformen med befintliga kritiska till gångar, ord listans villkor och kontakter?
9. Hur integrerar jag med befintliga system?
10. Hur samlar du in feedback och skapar en hållbar process?

Även om du kanske inte har svarat på de flesta av dessa frågor direkt, kan det hjälpa din organisation att rama in projektet och se till att alla krav som måste uppfyllas kan uppfyllas.

## <a name="include-the-right-stakeholders"></a>Ta med rätt intressenter

För att säkerställa att implementeringen av avdelningens kontroll för hela företaget lyckas är det viktigt att involvera rätt intressenter. Endast några få personer ingår i den inledande fasen. När omfånget expanderas behöver du dock ytterligare personer för att bidra till projektet och ge feedback.

Några viktiga intressenter som du kanske vill inkludera:

|Person|Roller|
|---------|---------|
|**Chefs data chef**|CDO ser ett antal funktioner som kan omfatta data hantering, data kvalitet, Master Data Management, data vetenskap, Business Intelligence och skapande av data strategi. De kan vara sponsrat av avdelningens kontroll-implementerings projektet.|
|**Domän/företags ägare**|En affärs person som påverkar användningen av verktyg och har budget kontroll|
|**Dataanalytiker**|Möjlighet att Rama ett affärs problem och analysera data för att hjälpa ledare fatta affärs beslut|
|**Data arkitekt**|Utforma databaser för verksamhets kritiska branschspecifika appar tillsammans med att designa och implementera data säkerhet|
|**Datatekniker**|Hantera data stacken, hämta data från olika källor, integrera och förbereda data, konfigurera datapipelines|
|**Datavetare**|Skapa analytiska modeller och konfigurera data produkter som ska användas av API: er|
|**DB-administratör**|Äga, spåra och lös databas-relaterade incidenter och förfrågningar inom service avtal (service avtal). Kan konfigurera data pipelines|
|**DevOps**|Affärs program utveckling och implementering, kan omfatta Skriv-skript och Orchestration-funktioner|
|**Data säkerhets specialist**|Utvärdera övergripande nätverks-och data säkerhet, som omfattar data som kommer in och ut ur avdelningens kontroll|

## <a name="identify-key-scenarios"></a>Identifiera nyckel scenarier

Avdelningens kontroll kan användas för att centralt hantera data styrning över en organisations datafastighet som sträcker sig över molnet och lokala miljöer. Om du vill ha en lyckad implementering måste du identifiera viktiga scenarier som är viktiga för verksamheten. De här scenarierna kan korsa affär senhets gränser eller påverka flera användares personer antingen överordnade eller underordnade.

Dessa scenarier kan skrivas upp på olika sätt, men du bör inkludera minst följande fem dimensioner:

1. Persona – vilka är användarna?
2. Käll system – vilka är data källorna som Azure Data Lake Storage Gen2 eller Azure SQL Database?
3. Påverkan – vad är kategorin för det här scenariot?
4. Informations scenarier – hur användarna använder avdelningens kontroll för att lösa problem?
5. Förväntat resultat – vad är framgångs villkoren?

Scenarierna måste vara speciella, åtgärds bara och körbara med mätbara resultat. Några exempel scenarier som du kan använda:

|Scenario|Detalj|Person|
|---------|---------|---------|
|Katalog affärs kritiska till gångar|Jag behöver information om varje data uppsättning för att få en god förståelse för vad det är. Det här scenariot omfattar både affärs data och tekniska metadata om data uppsättningen i katalogen. Data källorna omfattar Azure Data Lake Storage Gen2, Azure Synapse DW och/eller Power BI. Det här scenariot omfattar även lokala resurser som SQL Server.|Affärsanalytiker, data expert, data tekniker|
|Identifiera affärs kritiska till gångar|Jag vill ha en sökmotor som kan söka igenom alla metadata i katalogen. Jag bör kunna söka med hjälp av en teknisk term, affärs period med enkel eller komplex sökning med jokertecken.|Affärsanalytiker, data expert, data tekniker, data administratör|
|Spåra data för att förstå sitt ursprung och felsöka problem med data|Jag behöver ha data härkomst för att spåra data i rapporter, förutsägelser eller modeller tillbaka till den ursprungliga källan och förstå ändringarna och var data har bearbetats genom data livs cykeln. Det här scenariot måste stödja prioriterade data pipelines Azure Data Factory och Databricks.|Data tekniker, data expert|
|Utöka metadata för kritiska data till gångar|Jag behöver utöka data uppsättningen i katalogen med tekniska metadata som genereras automatiskt. Klassificering och märkning är några exempel.|Data tekniker, domän/affärs ägare|
|Styr data till gångar med användarvänlig användar upplevelse|Jag behöver ett företags ord lista för företagsspecifika metadata. Företags användare kan använda avdelningens kontroll för självbetjänings scenarier för att kommentera sina data och göra det möjligt att identifiera data enkelt via sökning.|Domän/företags ägare, affärsanalytiker, data expert, data tekniker|

## <a name="deployment-models"></a>Distributionsmodeller

Om du bara har en liten grupp med avdelningens kontroll med grundläggande användnings fall kan metoden vara så enkel som att ha en avdelningens kontroll-instans för att betjäna hela gruppen. Du kan dock också fundera över om din organisation behöver fler än en avdelningens kontroll-instans. Och om du använder flera avdelningens kontroll-instanser kan anställda befordra till gångarna från ett stadium till en annan.

### <a name="determine-the-number-of-purview-instances"></a>Fastställa antalet avdelningens kontroll-instanser

I de flesta fall bör det bara finnas ett avdelningens kontroll-konto för hela organisationen. Den här metoden tar maximal nytta av "nätverks effekter" där plattformens värde ökar exponentiellt som en funktion av de data som finns i plattformen.

Det finns dock undantag för det här mönstret:

1. **Testa nya konfigurationer** – organisationer kan vilja skapa flera instanser för att testa genomsökning av konfigurationer eller klassificeringar i isolerade miljöer. Även om det finns en funktion för versions hantering i vissa delar av plattformen, till exempel ord lista, är det enklare att få en "disponibel" instans att fritt testa.
2. Att **separera test, för produktion och produktion** – organisationer vill skapa olika plattformar för olika typer av data som lagras i olika miljöer. Vi rekommenderar inte att dessa typer av data är olika innehålls typer. Du kan använda ord listan på den översta hierarkin eller kategorin för att åtskilja innehålls typer.
3. **Konglomerat och federerade modeller** – konglomerat har ofta många affär Senheter (BUs) som fungerar separat, och i vissa fall kan de inte ens dela faktureringen med varandra. I dessa fall kommer organisationen att få skapa en avdelningens kontroll-instans för varje BU. Den här modellen är inte idealisk, men det kan vara nödvändigt, särskilt på grund av att bussen ofta inte är villiga att dela fakturering.
4. **Kompatibilitet** – det finns vissa strikta efterlevnadsprinciper som behandlar jämna metadata som känsliga och kräver att de befinner sig i ett visst geografiskt område. Om ett företag har flera geografiska områden, är den enda lösningen att ha flera avdelningens kontroll-instanser, en för varje geografi.

### <a name="create-a-process-to-move-to-production"></a>Skapa en process för att flytta till produktion

Vissa organisationer kan bestämma sig för att hålla saker enkla genom att arbeta med en enda produktions version av avdelningens kontroll. De behöver förmodligen inte gå utöver identifiering, Sök och bläddra i scenarier. Om vissa till gångar har fel ord lista, är det ganska strikt att låta dem vara självkorrigerande. De flesta organisationer som vill distribuera avdelningens kontroll över olika affär senheter kommer dock att ha någon form av process och kontroll.

En annan viktig aspekt att ta med i din produktions process är hur klassificeringar och etiketter kan migreras. Avdelningens kontroll har över 90 system klassificerare. Du kan använda system eller anpassade klassificeringar för fil-, tabell-eller kolumn till gångar. Klassificeringar är som ämnes koder och används för att markera och identifiera innehåll av en speciell typ som finns i din datafastighet under genomsökningen. Känslighets etiketter används för att identifiera kategorier av klassificerings typer i dina organisations data och gruppera sedan de principer som du vill använda för varje kategori. Den använder samma känsliga informations typer som Microsoft 365, så att du kan sträcka ut dina befintliga säkerhets principer och skydd över hela innehållet och data fastigheten. Den kan genomsöka och klassificera dokument automatiskt. Om du till exempel har en fil med namnet multiple.docx och den har ett nationellt ID-nummer i sitt innehåll, kommer avdelningens kontroll att lägga till klassificering som till exempel EU: s nationella identifierings nummer på sidan till gångs information.

I avdelningens kontroll finns det flera områden där katalog administratörerna måste se till att bästa praxis för konsekvens och underhåll över hela livs cykeln:

* **Data till gångar** – data källor måste genomsökas om i olika miljöer. Vi rekommenderar inte att du bara söker i utvecklingen och sedan återskapar dem med hjälp av API: er i produktionen. Det främsta skälet är att avdelningens kontroll-skannerrna gör mycket mer "kablar" bakom scenerna på data till gångarna, som kan vara komplexa för att flytta dem till en annan avdelningens kontroll-instans. Det är mycket enklare att bara lägga till samma data källa i produktionen och skanna källorna igen. Den allmänna rekommenderade metoden är att ha dokumentation om alla genomsökningar, anslutningar och autentiseringsmekanismer som används.
* **Skannings regel uppsättningar** – det här är din samling av regler som har tilldelats till en speciell sökning, till exempel filtyp och klassificeringar som ska identifieras. Om du inte har det många skannings regel uppsättningar är det möjligt att bara återskapa dem manuellt via produktion. Detta kräver en intern process och en lämplig dokumentation. Men om du regel uppsättningarna ändras per dag eller varje vecka kan detta åtgärdas genom att utforska REST API vägen.
* **Anpassade klassificeringar** – dina klassificeringar får inte också ändras regelbundet. Under den inledande fasen av distributionen kan det ta lite tid att förstå olika krav för att komma igång med anpassade klassificeringar. Men när det har kvittats behöver detta vara lite ändring. Så här gör du rekommendationerna för att manuellt migrera eventuella anpassade klassificeringar över eller använda REST API.
* **Ord lista** – det går att exportera och importera ord listans villkor via UX. För Automation-scenarier kan du också använda REST API.
* **Principer för resurs uppsättnings mönster** – den här funktionen är mycket avancerad för alla typiska organisationer att tillämpa. I vissa fall har Azure Data Lake Storage namngivnings konventioner för mappar och en viss struktur som kan orsaka problem i avdelningens kontroll för att generera resurs uppsättningen. Din affär senhet kanske också vill ändra resurs uppsättnings konstruktionen med ytterligare anpassningar som passar affärs behoven. I det här scenariot är det bäst att hålla koll på alla ändringar via REST API och dokumentera ändringarna via den externa versions plattformen.
* **Roll tilldelning** – här kan du kontrol lera vem som har åtkomst till avdelningens kontroll och vilka behörigheter de har. Avdelningens kontroll har också REST API för att stödja export och import av användare och roller, men detta är inte ett Atlas-API-kompatibelt. Rekommendationen är att tilldela en Azure-säkerhetsgrupp och hantera grupp medlemskapet i stället.

### <a name="plan-and-implement-different-integration-points-with-purview"></a>Planera och implementera olika integrerings platser med avdelningens kontroll

Det är troligt att en vuxen organisation redan har en befintlig data katalog. Den viktigaste frågan är att fortsätta att använda den befintliga tekniken och synkronisera med avdelningens kontroll eller inte. För att hantera synkronisering med befintliga produkter i en organisation tillhandahåller avdelningens kontroll Atlas REST-API: er. Atlas-API: er tillhandahåller en kraftfull och flexibel mekanism som hanterar både push-och pull-scenarier. Information kan publiceras till avdelningens kontroll med hjälp av Atlas-API: er för start eller för att push-överföra de senaste uppdateringarna från ett annat system till avdelningens kontroll. Informationen som är tillgänglig i avdelningens kontroll kan också läsas med hjälp av Atlas-API: er och sedan synkroniseras tillbaka till befintliga produkter. 

För andra integrerings scenarier som biljetter, anpassat användar gränssnitt och dirigering kan du använda Atlas-API: er och Kafka-slutpunkter. I allmänhet finns fyra integrerings punkter med avdelningens kontroll:

* **Data till gång** – detta gör att avdelningens kontroll kan söka igenom en butiks till gångar för att räkna upp vad dessa till gångar är och samla in alla lättillgängliga metadata om dem. Så för SQL kan det vara en lista över databaser, tabeller, lagrade procedurer, vyer och konfigurations data om de behålls på samma platser `sys.tables` . För något som liknar Azure Data Factory (ADF) kan du räkna upp alla pipeliner och hämta data när de skapades, senaste körning, aktuell status.
* **Härkomst** – detta gör att avdelningens kontroll kan samla in information från ett analys-/data Mutations system för hur data flyttas runt. För något som liknar Spark kan detta samla in information från körningen av en antecknings bok för att se vilka data som antecknings boken har matat in, hur den omvandlades och var den fanns. För något som SQL kan det analyseras med fråge loggar för att bakåtkompilera vilka Mutations åtgärder som utfördes och vad de gjorde. Vi stöder både push-och pull-baserade härkomst beroende på behoven.
* **Klassificering** – detta gör att avdelningens kontroll kan ta fysiska exempel från data källor och köra dem via vårt klassificerings system. Klassificerings systemet avgränsar semantiken för en del av data. Till exempel kanske vi vet att en fil är en Parquet-fil och har tre kolumner och att den tredje är en sträng. Men de klassificerare som vi kör i exemplen meddelar oss att strängen är ett namn, en adress eller ett telefonnummer. Att visa den här integrerings punkten innebär att vi har definierat hur avdelningens kontroll kan öppna objekt som antecknings böcker, pipeliner, Parquet-filer, tabeller och behållare.
* **Inbäddad upplevelse** – produkter som har en "Studio" som upplevelse (till exempel ADF, SYNAPSE, SQL Studio, PBI och Dynamics) vill vanligt vis göra det möjligt för användare att identifiera data som de vill interagera med och även hitta platser för att mata ut data. Avdelningens kontroll-katalogen kan hjälpa dig att påskynda dessa upplevelser genom att tillhandahålla en inbäddnings upplevelse. Den här upplevelsen kan inträffa i API: et eller på UX-nivån i partnerns alternativ. Genom att bädda in ett anrop till avdelningens kontroll kan organisationen dra nytta av avdelningens kontroll: s karta över datafastigheten för att hitta data till gångar, se härkomst, kontrol lera scheman, titta på klassificeringar, kontakter osv.

## <a name="phase-1-pilot"></a>Fas 1: pilot

I den här fasen måste avdelningens kontroll skapas och konfigureras för en mycket liten uppsättning användare. Vanligt vis är det bara en grupp med 2-3 personer som arbetar tillsammans för att köra genom slutpunkt-till-slutpunkt-scenarier. De anses vara General avdelningens kontroll i organisationen. Huvud syftet med den här fasen är att säkerställa att viktiga funktioner kan uppfyllas och att rätt intressenter är medvetna om projektet.

### <a name="tasks-to-complete"></a>Aktiviteter som ska slutföras

|Uppgift|Detalj|Varaktighet|
|---------|---------|---------|
|Samla in & samtycker till krav|Diskussion med alla intressenter för att samla in en fullständig uppsättning krav. Olika personer måste delta för att godkänna en delmängd av krav som ska slutföras för varje fas i projektet.|1 vecka|
|Konfigurera Start paketet|Gå igenom [avdelningens kontroll Snabbstart](create-catalog-portal.md) och konfigurera [avdelningens kontroll Starter Kit](tutorial-scan-data.md) för att demonstrera avdelningens kontroll till alla intressenter.|1 dag|
|Navigera i avdelningens kontroll|Lär dig hur du använder avdelningens kontroll från start sidan.|1 dag|
|Konfigurera ADF för härkomst|Identifiera nyckel pipeliner och data till gångar. Samla in all information som krävs för att ansluta till ett internt ADF-konto.|1 dag|
|Skanna en data källa som Azure Data Lake Storage|Lägg till data källan och konfigurera en genomsökning. Se till att genomsökningen identifierar alla till gångar.|2 dag|
|Sök och bläddra|Ge slutanvändare åtkomst till avdelningens kontroll och utför sökning från slut punkt till slut punkt och bläddra i scenarier.|1 dag|

### <a name="acceptance-criteria"></a>Godkännande kriterier

* Avdelningens kontroll-kontot har skapats i organisations prenumeration under organisationens klient organisation.
* En liten grupp användare med flera roller kan komma åt avdelningens kontroll.
* Avdelningens kontroll har kon figurer ATS för att genomsöka minst en data källa.
* Användare ska kunna extrahera nyckel värden för avdelningens kontroll, till exempel:
  * Sök och bläddra
  * Ursprung
* Användare ska kunna tilldela ägande till gång på sidan till gång.
* Presentation och demo för att öka medvetenheten för viktiga intressenter.
* Köp in från hantering för att godkänna ytterligare resurser för MVP-fasen.

## <a name="phase-2-minimum-viable-product"></a>Fas 2: minimal livskraftig produkt

När du har fastställt de överenskomna kraven och deltog affär senheter till att publicera avdelningens kontroll, är nästa steg att arbeta med en minimal version av en praktisk produkt (MVP). I det här steget ska du utöka användningen av avdelningens kontroll till fler användare som kommer att ha ytterligare behov vågrätt och lodrätt. Det kommer att finnas nyckel scenarier som måste uppfyllas vågrätt för alla användare, till exempel ord lista, Sök och bläddra. Det kommer också att finnas djupgående krav lodrätt för varje affär senhet eller grupp för att få fram specifika scenarier från slut punkt till slut punkt, till exempel härkomst från Azure Data Lake Storage till Azure Synapse DW för att Power BI.

### <a name="tasks-to-complete"></a>Aktiviteter som ska slutföras

|Uppgift|Detalj|Varaktighet|
|---------|---------|---------|
|[Sök igenom Azure Synapse Analytics](register-scan-azure-synapse-analytics.md)|Börja publicera dina databas källor och Sök igenom dem för att fylla i viktiga till gångar|2 dagar|
|[Skapa anpassade klassificeringar och regler](create-a-custom-classification-and-classification-rule.md)|När dina till gångar genomsöks kan användarna vara medvetna om att det finns ytterligare användnings fall för mer klassificering vid standard klassificeringar från avdelningens kontroll.|2-4 veckor|
|[Sök Power BI](register-scan-power-bi-tenant.md)|Om din organisation använder Power BI kan du skanna Power BI för att samla in alla data till gångar som används av data experter eller data analytiker som har krav på att inkludera härkomst från lagrings lagret.|1-2 veckor|
|[Importera ord listans villkor](how-to-create-import-export-glossary.md)|I de flesta fall kan din organisation redan utveckla en samling ord lista och villkors tilldelning till till gångar. Detta kräver en import process i avdelningens kontroll via. csv-filen.|1 vecka|
|Lägg till kontakter till till gångar|För Top till gångar kanske du vill upprätta en process för att antingen tillåta andra personer att tilldela kontakter eller importera via REST-API: er.|1 vecka|
|Lägg till känsliga etiketter och skanna|Detta kan vara valfritt för vissa organisationer, beroende på användningen av etiketter från M365.|1-2 veckor|
|Få klassificering och känsliga insikter|För rapportering och insyn i avdelningens kontroll kan du komma åt den här funktionen för att få olika rapporter och tillhandahålla presentationer till hantering.|1 dag|
|Extra användare som använder avdelningens kontroll-hanterade användare|Det här steget kräver att avdelningens kontroll-administratören arbetar med Azure Active Directory-administratören för att upprätta nya säkerhets grupper för att ge åtkomst till avdelningens kontroll.|1 vecka|

### <a name="acceptance-criteria"></a>Godkännande kriterier

* En större grupp användare har publicerats till avdelningens kontroll (50 +)
* Genomsök affärs kritiska data källor
* Importera och tilldela alla kritiska ord listas villkor
* Viktiga etiketter har testats på viktiga till gångar
* Minimi scenarion har uppfyllts för kunder med deltagit i affär senheter

## <a name="phase-3-pre-production"></a>Fas 3: för produktion

När MVP-fasen har passerat är det dags att planera för mil stolpe för för produktion. Din organisation kan välja att ha en separat instans av avdelningens kontroll för för produktion och produktion, eller behålla samma instans men begränsa åtkomsten. I den här fasen kanske du vill ta med skanning på lokala data källor som SQL Server. Om det finns luckor i data källor som inte stöds av avdelningens kontroll är det dags att utforska Atlas-API: et för att förstå ytterligare alternativ.

### <a name="tasks-to-complete"></a>Aktiviteter som ska slutföras

|Uppgift|Detalj|Varaktighet|
|---------|---------|---------|
|Förfina din sökning med skannings regel uppsättningen|Din organisation kommer att ha många data källor för för produktion. Det är viktigt att definiera nyckel villkor för genomsökning så att klassificeringar och fil namns tillägg kan tillämpas konsekvent på tavlan.|1-2 dagar|
|Bedöm regions tillgänglighet för genomsökning|Beroende på regionen för data källorna och organisationens krav på efterlevnad och säkerhet, kanske du vill överväga vilka regioner som måste vara tillgängliga för genomsökning.|1 dag|
|Förstå brand Väggs koncept vid genomsökning|Det här steget kräver viss granskning av hur organisationen konfigurerar brand väggen och hur avdelningens kontroll kan autentisera sig själv för att få åtkomst till data källorna för genomsökning.|1 dag|
|Förstå privat länk koncept vid genomsökning|Om din organisation använder privat länk måste du utforma grunden för nätverks säkerhet för att inkludera privat länk som en del av kraven.|1 dag|
|[Genomsök lokala SQL Server](register-scan-on-premises-sql-server.md)|Detta är valfritt om du har lokala SQL Server. Genomsökningen kräver att du konfigurerar [integration runtime med egen värd](manage-integration-runtimes.md) och att du lägger till SQL Server som en data källa.|1-2 veckor|
|Använda avdelningens kontroll-REST API för integrations scenarier|Om du har krav på att integrera avdelningens kontroll med andra tekniker från tredje part, till exempel Dirigerings-eller biljett system, kanske du vill utforska REST API-ytan.|1-4 veckor|
|Förstå avdelningens kontroll-priser|Det här steget ger organisationen viktig finansiell information som hjälper dig att fatta beslut.|1-5 dagar|

### <a name="acceptance-criteria"></a>Godkännande kriterier

* Minst en affär senhet har publicerats med alla användare
* Genomsök lokala data källor som SQL Server
* POC minst ett integrations scenario som använder REST API
* Slutför en plan för att gå till produktion som bör innehålla viktiga områden för infrastruktur och säkerhet

## <a name="phase-4-production"></a>Fas 4: produktion

Stegen ovan bör följas för att skapa en effektiv informations styrning, som är grunden för bättre styrnings program. Data styrning hjälper din organisation att förbereda för växande trender som AI, Hadoop, IoT och blockchain. Det är bara start för många saker som data och analyser, och det finns mycket mer som kan diskuteras. Resultatet av den här lösningen skulle leverera:

* **Verksamhet fokuserad** – en lösning som är anpassad till affärs krav och scenarier med tekniska krav.
* **Framtiden är klar** – en lösning maximerar standard funktionerna i plattformen och använder standardiserade bransch metoder för konfigurations-och skript aktiviteter för att stödja utvecklingen/utvecklingen av plattformen.

### <a name="tasks-to-complete"></a>Aktiviteter som ska slutföras

|Uppgift|Detalj|Varaktighet|
|---------|---------|---------|
|Genomsök produktions data källor med brand vägg aktive rad|Om detta är valfritt när brand väggen är på plats, men det är viktigt att utforska alternativen för att skärpa infrastrukturen.|1-5 dagar|
|Aktivera privat länk|Om detta är valfritt när en privat länk används. Annars kan du hoppa över detta eftersom det är ett krav som måste vara ett villkor när privat är aktiverat.|1-5 dagar|
|Skapa automatiserat arbets flöde|Arbets flödet är viktigt för att automatisera processer som godkännande, eskalering, granskning och ärende hantering.|2-3 veckor|
|Åtgärds dokumentation|Data styrning är inte ett engångs projekt. Det är ett kontinuerligt program för data drivna besluts fattande och skapa affärs möjligheter. Det är viktigt att dokumentera viktiga procedurer och affärs standarder.|1 vecka|

### <a name="acceptance-criteria"></a>Godkännande kriterier

* Alla affär senheter och deras användare har publicerats
* Uppfyller infrastruktur-och säkerhets kraven för produktion
* Uppfyller alla användnings fall som krävs av användarna

## <a name="platform-hardening"></a>Plattforms härdning

Ytterligare härdnings steg kan vidtas:

* Öka säkerhets position genom att aktivera skanning på brand Väggs resurser eller Använd privat länk
* Finjustera omfattnings skanning för att förbättra genomsöknings prestanda
* Använd REST-API: er för att exportera kritiska metadata och egenskaper för säkerhets kopiering och återställning
* Använd arbets flöde för att automatisera biljetter och händelser för att undvika mänskliga fel

## <a name="next-steps"></a>Nästa steg

- [Självstudie: kör Start paket och Genomsök data](tutorial-scan-data.md)
- [Självstudie: gå till start sidan och Sök efter en till gång](tutorial-asset-search.md)