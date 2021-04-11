---
title: Introduktion till mikrotjänster på Azure
description: En översikt över varför det är viktigt att skapa moln program med en mikrotjänster-metod för modern program utveckling och hur Azure Service Fabric tillhandahåller en plattform för att uppnå detta.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 46d4be7321057bd7a5ee19e0aca136c8c60d43bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044438"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Varför ska man använda en mikrotjänst metod för att skapa program

För programutvecklare är inget nytt att koppla ett program till komponent delar. Normalt används en metod med flera nivåer, med backend-butik, affärs logik på mellan nivå och ett användar gränssnitt på klient sidan. Vad som *har* ändrats under de senaste åren är utvecklare som skapar distribuerade program för molnet.

Här följer några föränderliga affärs behov:

* En tjänst som är byggd och drivs i stor skala för att uppnå kunder i nya geografiska regioner.
* Snabbare leverans av funktioner och funktioner för att svara på kund krav på ett flexibelt sätt.
* Förbättrad resursutnyttjande för att minska kostnaderna.

De här affärs behoven påverkar *hur* vi skapar program.

Mer information om Azure-metoden för mikrotjänster finns i [mikrotjänster: en program revolution som drivs av molnet](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Design metod för monolitisk vs. mikrotjänster

Programmen utvecklas med tiden. Framgångs rika program utvecklas genom att vara användbara för personer. Program som inte lyckades utvecklas inte och är slutligen inaktuella. Här är frågan: hur mycket behöver du veta mer om dina krav idag och vad de kommer att vara i framtiden? Anta till exempel att du skapar ett rapporterings program för en avdelning i företaget. Du är säker på att programmet endast gäller inom omfånget för ditt företag och att rapporterna inte hålls långa. Din metod skiljer sig från den, t. ex. att skapa en tjänst som levererar video innehåll till flera miljoner kunder.

Ibland är det en drivande faktor när du får ut något från dörren som ett koncept bevis. Du vet att programmet kan återskapas senare. Det finns ingen punkt i ett överbyggnads kort som aldrig används. Å andra sidan, när företag skapar för molnet, är förväntat tillväxt och användning. Tillväxt och skala är oförutsägbara. Vi vill prototypa snabbt samtidigt som du vet att vi har en sökväg som kan hantera framtida framgångar. Det här är en Lean-start metod: Bygg, Mät, lär och iterera.

Under en-klient/server-era är vi inriktad på att utveckla program med flera nivåer med hjälp av olika tekniker på varje nivå. Termen *monolitisk* program har uppställts för att beskriva dessa metoder. Gränssnitten som används för att ligga mellan nivåerna och en mer tätt kopplad design användes mellan komponenterna i varje nivå. Utvecklare utformade och fördelade klasser som har kompilerats i bibliotek och kopplats ihop till några körbara filer och DLL-filer.

Det finns fördelar med en monolitisk design metod. Monolitisk-program är ofta enklare att utforma, och anrop mellan komponenter går snabbare eftersom dessa anrop ofta är över interprocess communication (IPC). Alla testar också en enskild produkt, vilket är en mer effektiv användning av personal resurser. Nack delen är att det finns en tätt koppling mellan skikt skikt och att du inte kan skala enskilda komponenter. Om du behöver göra korrigeringar eller uppgraderingar måste du vänta på att andra ska kunna slutföra testningen. Det är svårare att vara smidigare.

Mikrotjänster riktar sig mot dessa nack tjänster och är mer tätt justerade med föregående affärs behov. Men de har också både fördelar och skulder. Fördelarna med mikrotjänster är att var och en vanligt vis kapslar in enklare företags funktioner, som du kan skala ut eller i, testa, distribuera och hantera oberoende av varandra. En viktig fördel med en mikrotjänster-metod är att teamen drivs mer i affärs scenarier än med teknik. Mindre team utvecklar en mikrotjänst baserat på ett kund scenario och använder alla tekniker som de vill använda.

Det innebär att organisationen inte behöver standardisera tekniska lösningar för att underhålla mikrotjänstprogram. Enskilda team som äger tjänster kan göra vad som är lämpligt för dem baserat på grupp expert kunskaper eller vad som är lämpligast för att lösa problemet. I praktiken är en uppsättning rekommenderade tekniker, till exempel ett visst NoSQL lager eller ramverk för webb program, lämpligt.

Nack delen med mikrotjänster är att du måste hantera fler separata entiteter och hantera mer komplexa distributioner och versioner. Nätverks trafiken mellan mikrotjänsterna ökar, precis som motsvarande nätverks fördröjningar. Många samtals tjänster kan orsaka en prestanda Nightmare. Utan verktyg som hjälper dig att visa dessa beroenden är det svårt att se hela systemet.

Standarder gör mikrotjänster-metoden att fungera genom att ange hur du ska kommunicera och tolerera enbart de saker du behöver från en tjänst i stället för fasta avtal. Det är viktigt att definiera dessa kontrakt direkt i designen eftersom tjänsterna uppdateras oberoende av varandra. En annan beskrivning som myntade för att utforma med en mikrotjänst metod är "detaljerad service-orienterad arkitektur (SOA)".

***Det är enkelt att utforma mikrotjänster som är en fristående Federation av tjänster, med oberoende ändringar i alla och överenskomna standarder för kommunikation.***

När fler moln program skapas har människor upptäckt att den här dekompositionen av den övergripande applikationen i oberoende, scenario-fokuserade tjänster är ett bättre långsiktigt tillvägagångs sätt.

## <a name="comparison-between-application-development-approaches"></a>Jämförelse mellan program utvecklings metoder

![Service Fabric utveckling av plattforms program][Image1]

1) Ett monolitisk-program innehåller domänfunktionalitet och är vanligt vis indelat i funktionella lager, t. ex. webb, företag och data.

2) Du kan skala ett monolitisk-program genom att klona det på flera servrar/virtuella datorer/behållare.

3) Ett program för mikrotjänster separerar funktioner till separata mindre tjänster.

4) Mikrotjänsternas tillvägagångs sätt skalas ut genom att distribuera varje tjänst oberoende av varandra, vilket skapar instanser av dessa tjänster mellan servrar/virtuella datorer/behållare.

Att designa med en mikrotjänster är inte lämpligt för alla projekt, men det kan justeras närmare de affärs mål som beskrivs ovan. Att börja med en monolitisk metod kan vara bra om du vet att du har möjlighet att arbeta om koden senare i en design för mikrotjänster. Oftast börjar du med ett monolitisk-program och delar upp det långsamt i steg, med de funktions områden som behöver vara mer skalbara eller flexibla.

När du använder en metod för mikrotjänster kan du skapa ett program med många små tjänster. De här tjänsterna körs i behållare som distribueras i ett kluster med datorer. Mindre team utvecklar en tjänst som fokuserar på ett scenario och oberoende test, version, distribution och skalning av varje tjänst så att hela programmet kan utvecklas.

## <a name="what-is-a-microservice"></a>Vad är en mikrotjänst?

Det finns olika definitioner av mikrotjänster. De flesta av dessa egenskaper hos mikrotjänster godkänns ofta:

* Kapsla in en kund eller ett affärs scenario. Vilket problem löser du?
* Utvecklat av ett litet teknik team.
* Skrivet i valfritt programmeringsspråk med valfritt ramverk.
* Består av kod, och alternativt tillstånd, som båda är oberoende versioner, distribueras och skalas.
* Interagera med andra mikrotjänster över väldefinierade gränssnitt och protokoll.
* Ha unika namn (URL: er) som används för att matcha deras plats.
* Vara konsekvent och tillgänglig i närvaro av problem.

Så här summerar du:

***Mikrotjänstprogram består av små, oberoende versioner av och skalbara kundfokuserade tjänster som kommunicerar med varandra via standard protokoll med väldefinierade gränssnitt.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Skrivet i valfritt programmeringsspråk med valfritt ramverk

Som utvecklare vill vi vara kostnads fria att välja ett språk eller ramverk, beroende på våra kunskaper och behoven hos tjänsten som vi skapar. För vissa tjänster kan du påverka prestanda fördelarna med C++ över något annat. För andra är det lätt att hanterad utveckling som du får från C# eller Java att vara viktigare. I vissa fall kan du behöva använda ett särskilt partner bibliotek, data lagrings teknik eller en metod för att exponera tjänsten för klienter.

När du har valt en teknik måste du överväga driften eller livs cykeln för hantering och skalning av tjänsten.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Tillåter att kod och tillstånd blir oberoende versions hantering, distribueras och skalas

Oavsett hur du skriver dina mikrotjänster, koden, och om du vill, bör du distribuera, uppgradera och skala oberoende av varandra. Det här problemet är svårt att lösa eftersom det följer ditt val av teknik. För skalning är det svårt att förstå hur man partitionerar (eller Shard) både koden och statusen. När kod och tillstånd använder olika tekniker, som är vanliga idag, måste distributions skripten för mikrotjänsten kunna skala dem båda. Den här separationen är också flexibel och flexibel, så du kan uppgradera vissa mikrotjänster utan att behöva uppgradera alla på en gång.

Vi går tillbaka till vår jämförelse av monolitisk och mikrotjänsternas metoder för en stund. Det här diagrammet visar skillnaderna i metoderna för att lagra tillstånd:

#### <a name="state-storage-for-the-two-approaches"></a>Tillstånds lagring för de två metoderna

![Lagring av Service Fabric plattforms tillstånd][Image2]

***Monolitisk-metoden till vänster har en enda databas och nivåer av en viss teknik.***

***Mikrotjänster-metoden, till höger, har ett diagram över sammankopplade mikrotjänster där tillstånd vanligt vis är begränsat till mikrotjänsten och olika tekniker används.***

I en monolitisk metod använder programmet vanligt vis en enda databas. Fördelen med att använda en databas är att den finns på en enda plats, vilket gör det enkelt att distribuera. Varje komponent kan ha en enda tabell för att lagra dess tillstånd. Teamen måste ha en strikt separat status, vilket är en utmaning. Oundvikligen kommer någon att vara frestad att lägga till en kolumn i en befintlig kund tabell, göra en koppling mellan tabeller och skapa beroenden i lagrings skiktet. När detta inträffar kan du inte skala enskilda komponenter.

I metoden för mikrotjänster hanterar och lagrar varje tjänst sin egen status. Varje tjänst ansvarar för skalning av både kod och tillstånd tillsammans för att uppfylla tjänstens krav. En nack är att när du behöver skapa vyer, eller frågor, av programmets data, måste du fråga över flera tillstånds lager. Det här problemet löses vanligt vis av en separat mikrotjänst som skapar en vy över en samling mikrotjänster. Om du behöver köra flera Impromptu-frågor på data, bör du tänka på att skriva varje mikrotjänsts data till en data lager tjänst för offline-analys.

Mikrotjänster har versions hantering. Det är möjligt att olika versioner av en mikrotjänst körs sida vid sida. En nyare version av en mikrotjänst kan krascha under en uppgradering och måste återställas till en tidigare version. Versions hantering är också användbart för A/B-testning, där olika användare upplever olika versioner av tjänsten. Det är till exempel vanligt att uppgradera en mikrotjänst för en speciell uppsättning kunder för att testa nya funktioner innan du lyfter ut den mer ofta.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interagerar med andra mikrotjänster över väldefinierade gränssnitt och protokoll

Under de senaste 10 åren har omfattande information publicerats som beskriver kommunikations mönster i tjänsteorienterade arkitekturer. Generellt använder tjänst kommunikation en REST-metod med HTTP-och TCP-protokoll och XML eller JSON som serialiserat format. I ett gränssnitts perspektiv är det att ta en webb design metod. Men ingenting bör hindra dig från att använda binära protokoll eller dina egna data format. Tänk på att människor kommer att ha en svårare tid med dina mikrotjänster om dessa protokoll och format inte är tillgängliga på ett öppet sätt.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Har ett unikt namn (URL) som används för att matcha dess plats

Din mikrotjänst måste vara adresserbar överallt där den körs. Om du tänker på datorer och vilken som kör en viss mikrotjänst, kan saker gå dåligt.

På samma sätt som DNS löser en viss URL till en viss dator, behöver mikrotjänsten ett unikt namn så att dess aktuella plats kan identifieras. Mikrotjänster behöver adresser bara namn som är oberoende av den infrastruktur de använder. Detta innebär att det finns en interaktion mellan hur din tjänst distribueras och hur den identifieras, eftersom det måste vara ett tjänst register. När en dator Miss lyckas måste registrerings tjänsten meddela dig var tjänsten flyttades till.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Förblir konsekvent och tillgänglig i närvaro av problem

Att hantera oväntade fel är ett av de svåraste problemen att lösa, särskilt i ett distribuerat system. Mycket av koden som vi skriver som utvecklare är för att hantera undantag. Under testningen kommer vi även att spendera den senaste tiden vid undantags hantering. Processen är mer engagerad än att skriva kod för att hantera problem. Vad händer när den dator där mikrotjänsten körs Miss lyckas? Du måste identifiera felet, vilket är ett eget hård problem. Men du måste också starta om mikrotjänsten.

För tillgänglighet måste en mikrotjänst vara elastisk för att Miss lyckas och kunna starta om på en annan dator. Förutom dessa återhämtnings krav bör data inte försvinna och data måste vara konsekventa.

Återhämtnings förmågan är svår att uppnå när felen inträffar under en program uppgradering. Mikrotjänsten, som arbetar med distributions systemet, behöver inte återställas. Den måste avgöra om den kan fortsätta att gå framåt till den nyare versionen eller återställas till en tidigare version för att upprätthålla ett konsekvent tillstånd. Du måste överväga några frågor, till exempel om det finns tillräckligt många datorer för att fortsätta flytta framåt och hur du återställer tidigare versioner av mikrotjänsten. För att fatta dessa beslut behöver du mikrotjänsten för att generera hälso information.

### <a name="reports-health-and-diagnostics"></a>Rapporterar hälsa och diagnostik

Det kan verka självklart, och den är ofta överblickad, men en mikrotjänst behöver rapportera sin hälsa och diagnostik. Annars har du lite inblick i hälsan från ett åtgärds perspektiv. Att korrelera diagnostiska händelser i en uppsättning oberoende tjänster och hantera dator klockor för att se händelse ordningen, är en utmaning. På samma sätt som du interagerar med en mikrotjänst över överenskomna protokoll och data format måste du standardisera hur du loggar hälso-och diagnostiska händelser som i slut ändan i ett händelse Arkiv för att fråga och Visa. Med en metod för mikrotjänster behöver olika team samtycka till ett enda loggnings format. Det måste finnas en konsekvent metod för att Visa diagnostiska händelser i programmet som helhet.

Hälsan skiljer sig från diagnostiken. Hälso tillståndet för den mikrotjänst som rapporterar det aktuella tillståndet för att vidta lämpliga åtgärder. Ett exempel är att arbeta med metoder för att uppgradera och distribuera för att upprätthålla tillgänglighet. Även om en tjänst kanske inte är felfri på grund av en process krasch eller omstart av datorn kan tjänsten fortfarande fungera. Det sista du behöver är att göra situationen sämre genom att starta en uppgradering. Den bästa metoden är att undersöka första eller tillåta tid för mikrotjänsten att återställa. Hälso händelser från en mikrotjänst hjälper oss att fatta välgrundade beslut och hjälper dig att skapa självbetjänings tjänster.

## <a name="guidance-for-designing-microservices-on-azure"></a>Vägledning för att utforma mikrotjänster på Azure

Besök Azure Architecture Center för att få hjälp med att [utforma och skapa mikrotjänster på Azure](/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric som plattform för mikrotjänster

Azure Service Fabric när Microsoft övergår från att leverera inramade produkter, som vanligt vis monolitisk, för att leverera tjänster. Upplevelsen av att skapa och driva stora tjänster, t. ex. Azure SQL Database och Azure Cosmos DB, formad Service Fabric. Plattformen utvecklades över tid då fler tjänster antog den. Service Fabric kördes inte bara i Azure utan även i fristående distributioner av Windows Server.

***Syftet med Service Fabric är att lösa de hårda problemen med att skapa och köra en tjänst och att använda infrastruktur resurser på ett effektivt sätt, så att team kan lösa affärs problem med hjälp av en metod för mikrotjänster.***

Denna korta video presenterar Service Fabric och mikrotjänster:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

Service Fabric hjälper dig att bygga program som använder en mikrotjänster-metod genom att tillhandahålla:

* En plattform som tillhandahåller system tjänster för att distribuera, uppgradera, identifiera och starta om misslyckade tjänster, upptäcka tjänster, dirigera meddelanden, hantera tillstånd och övervaka hälso tillstånd.
* Möjligheten att distribuera program som antingen körs i behållare eller som processer. Service Fabric är en behållare och process Orchestrator.
* Effektiva programmerings-API: er som hjälper dig att bygga program som mikrotjänster: [ASP.net Core, Reliable Actors och Reliable Services](service-fabric-choose-framework.md). Du kan till exempel få information om hälso tillstånd och diagnostik, eller så kan du dra nytta av inbyggd hög tillgänglighet.

***Service Fabric är oberoende om hur du skapar tjänsten och du kan använda valfri teknik. Men det tillhandahåller inbyggda programmerings-API: er som gör det enklare att skapa mikrotjänster.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrera befintliga program till Service Fabric

Med Service Fabric kan du återanvända befintlig kod och modernisera den med nya mikrotjänster. Det finns fem steg i program modernisering och du kan starta och stoppa i alla steg. Stegen är:

1) Börja med ett traditionellt monolitisk-program.  
2) Flyttar. Använd behållare eller körbara gäst program för att vara värd för befintlig kod i Service Fabric.  
3) Modernisera. Lägg till nya mikrotjänster bredvid befintlig container kod.  
4) Utveckla. Bryt monolitisk-programmet i mikrotjänster baserat på behov.  
5) Transformera program till mikrotjänster. Transformera befintliga monolitisk-program eller bygg nya bygg-program.

![Migrering till mikrotjänster][Image3]

Kom ihåg att du kan *starta och stoppa i någon av dessa steg*. Du behöver inte fortsätta till nästa steg. 

Nu ska vi titta på exempel för var och en av dessa steg.

**Migrera**  
Av två skäl migrerar många företag befintliga monolitisk-program till behållare:

* Kostnads minskning, antingen på grund av konsolidering och borttagning av befintlig maskin vara eller på grund av program som körs vid högre densitet.
* Ett konsekvent distributions avtal mellan utveckling och åtgärder.

Kostnads minskningar är enkla. På Microsoft är många befintliga program behållare, vilket leder till miljon tals besparingar. Konsekvent distribution är svårare att utvärdera men är lika viktigt. Det innebär att utvecklare kan välja de tekniker som passar dem, men åtgärder accepterar bara en enda metod för att distribuera och hantera programmen. Det minskar driften från att behöva hantera komplexiteten med stöd för olika tekniker utan att utvecklare bara behöver välja några av dem. I princip är varje program behållare i distributions avbildningar som är fristående.

Många organisationer slutar här. De har redan fördelarna med behållare och Service Fabric ger en heltäckande hanterings upplevelse, inklusive distribution, uppgraderingar, versions hantering, återställning och hälso övervakning.

**Modernisera**  
Modernisering är att lägga till nya tjänster tillsammans med befintlig container kod. Om du tänker skriva ny kod är det bäst att ta små steg nedåt i mikrotjänsternas sökväg. Detta kan innebära att du lägger till en ny REST API-slutpunkt eller ny affärs logik. På så sätt kan du starta processen med att skapa nya mikrotjänster och öva på att utveckla och distribuera dem.

**Förnya**  
En metod för mikrotjänster hanterar föränderliga affärs behov. I det här skedet måste du bestämma om du vill börja dela monolitisk-programmet i tjänster eller förnya. Ett klassiskt exempel är när en databas som du använder som en arbets flödes kö blir en bearbetnings Flask hals. När antalet arbets flödes begär Anden ökar måste arbetet distribueras för skalning. Ta den specifika delen av programmet som inte kan skalas, eller som behöver uppdateras oftare, och dela upp det som en mikrotjänst och förnya.

**Transformera program till mikrotjänster**  
I det här skedet består ditt program helt av (eller delas in i) mikrotjänster. För att uppnå det här läget har du gjort mikrotjänster-resan. Du kan börja här, men för att göra det utan en plattform för mikrotjänster för att hjälpa dig att kräva en betydande investering.

### <a name="are-microservices-right-for-my-application"></a>Är mikrotjänster rätt för mitt program?

Kanske. På Microsoft, eftersom fler Team började utveckla för molnet av affärs skäl, gjorde många av dem fördelarna med att ta en mikroservice-liknande metod. Bing har till exempel använt mikrotjänster för år. För andra team var mikrotjänster-metoden ny. Team upptäckte att det fanns hårda problem att lösa utanför deras kärn starka områden. Det är därför Service Fabric fått till drag som teknik för att skapa tjänster.

Målet med Service Fabric är att minska komplexiteten för att skapa mikrotjänstprogram så att du inte behöver gå igenom så många dyra omdesigner som möjligt. Starta små, skala vid behov, föråldrade tjänster, Lägg till nya och utveckla med kund användning. Vi vet också att det finns många andra problem som ännu inte kan lösas för att göra mikrotjänster mer effektiva för de flesta utvecklare. Behållare och aktörens programmerings modell är exempel på små steg i den riktningen. Vi är säkra på att fler innovationer kommer att göra det enklare att skapa mikrotjänster.

## <a name="next-steps"></a>Nästa steg

* [Mikrotjänster: en program revolution som drivs av molnet](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: skapa mikrotjänster på Azure](/azure/architecture/microservices/)
* [Metodtips för Azure Service Fabric-program och -kluster](./service-fabric-best-practices-security.md)
* [Översikt över Service Fabric terminologi](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png