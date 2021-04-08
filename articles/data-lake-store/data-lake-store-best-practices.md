---
title: Metod tips för att använda Azure Data Lake Storage Gen1 | Microsoft Docs
description: Lär dig metod tips om data inmatning, datum säkerhet och prestanda som rör användning av Azure Data Lake Storage Gen1 (tidigare kallat Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 9a5c5f9a4033b70a664071d6077a69f38c905093
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452220"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Metod tips för att använda Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

I den här artikeln får du lära dig mer om bästa praxis och överväganden för att arbeta med Azure Data Lake Storage Gen1. Den här artikeln innehåller information kring säkerhet, prestanda, återhämtning och övervakning för Data Lake Storage Gen1. Innan Data Lake Storage Gen1 arbetar du med verkligt stor data i tjänster som Azure HDInsight var komplex. Du var tvungen att Shard data över flera Blob Storage-konton så att petabyte-lagring och optimala prestanda vid denna skala skulle kunna uppnås. Med Data Lake Storage Gen1 tas de flesta hård gränserna för storlek och prestanda bort. Det finns dock fortfarande vissa överväganden som beskrivs i den här artikeln så att du kan få bästa möjliga prestanda med Data Lake Storage Gen1.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Azure Data Lake Storage Gen1 erbjuder POSIX-åtkomst kontroller och detaljerad granskning för Azure Active Directory (Azure AD) användare, grupper och tjänstens huvud namn. Dessa åtkomst kontroller kan ställas in på befintliga filer och mappar. Åtkomst kontrollerna kan också användas för att skapa standardvärden som kan tillämpas på nya filer eller mappar. När behörigheter har angetts till befintliga mappar och underordnade objekt, måste behörigheterna spridas rekursivt på varje objekt. Om det finns många filer kan det ta lång tid att sprida behörigheterna. Åtgången tid kan vara mellan 30-50 objekt som bearbetas per sekund. Planera därför mappstrukturen och användar grupperna på lämpligt sätt. Annars kan det orsaka oväntade fördröjningar och problem när du arbetar med dina data.

Anta att du har en mapp med 100 000 underordnade objekt. Om du tar den nedre gränserna av 30 objekt som bearbetas per sekund, kan det ta en timme att uppdatera behörigheten för hela mappen. Mer information om Data Lake Storage Gen1 ACL: er finns på [åtkomst kontroll i Azure Data Lake Storage gen1](data-lake-store-access-control.md). För bättre prestanda vid tilldelning av ACL: er rekursivt kan du använda verktyget Azure Data Lake Command-Line. Verktyget skapar flera trådar och rekursiv navigerings logik för att snabbt tillämpa ACL: er för miljon tals filer. Verktyget är tillgängligt för Linux och Windows, och [dokumentationen](https://github.com/Azure/data-lake-adlstool) och [nedladdningarna](https://aka.ms/adlstool-download) för verktyget finns på GitHub. Samma prestanda förbättringar kan aktive ras med egna verktyg som skrivs med Data Lake Storage Gen1 [.net](data-lake-store-data-operations-net-sdk.md) -och [Java](data-lake-store-get-started-java-sdk.md) -SDK: er.

### <a name="use-security-groups-versus-individual-users"></a>Använd säkerhets grupper jämfört med enskilda användare

När du arbetar med Big data i Data Lake Storage Gen1 används förmodligen ett tjänst objekt för att tillåta tjänster som Azure HDInsight att arbeta med data. Det kan dock finnas fall där enskilda användare behöver åtkomst till data också. I sådana fall måste du använda Azure Active Directory [säkerhets grupper](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) i stället för att tilldela enskilda användare till mappar och filer.

När en säkerhets grupp har tilldelats behörigheter behöver inte några uppdateringar Data Lake Storage Gen1 för att lägga till eller ta bort användare från gruppen. Detta hjälper också till att se till att du inte överskrider gränsen på [32 åtkomst-och standard-ACL](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits) : er (Detta inkluderar de fyra ACL: er för POSIX-typ som alltid är associerade med varje fil och mapp: [ägande användare](data-lake-store-access-control.md#the-owning-user), [ägande grupp](data-lake-store-access-control.md#the-owning-group), [mask](data-lake-store-access-control.md#the-mask)och annat).

### <a name="security-for-groups"></a>Säkerhet för grupper

Som diskuteras, är det bäst att använda Azure Active Directory säkerhets grupper när användare behöver åtkomst till Data Lake Storage Gen1. Vissa rekommenderade grupper att starta med kan vara **ReadOnlyUsers**, **WriteAccessUsers** och **FullAccessUsers** för roten av kontot och även separera dem för nyckel undermappar. Om det finns andra förväntade grupper av användare som kan läggas till senare, men ännu inte har identifierats, kan du överväga att skapa dummy-säkerhetsgrupper som har åtkomst till vissa mappar. Med hjälp av säkerhets gruppen ser du till att du inte längre behöver en lång bearbetnings tid för att tilldela nya behörigheter till tusentals filer.

### <a name="security-for-service-principals"></a>Säkerhet för tjänstens huvud namn

Azure Active Directory tjänstens huvud namn används vanligt vis av tjänster som Azure HDInsight för att komma åt data i Data Lake Storage Gen1. Beroende på åtkomst kraven för flera arbets belastningar kan det finnas några överväganden för att säkerställa säkerheten i och utanför organisationen. För många kunder kan ett enda Azure Active Directory tjänstens huvud namn vara tillräckligt, och det kan ha fullständig behörighet i roten för det Data Lake Storage Gen1 kontot. Andra kunder kan kräva flera kluster med olika tjänst huvud namn där ett kluster har fullständig åtkomst till data och ett annat kluster med enbart Läs behörighet. Precis som med säkerhets grupperna kan du överväga att skapa ett huvud namn för tjänsten för varje förväntat scenario (läsa, skriva, fullständig) när ett Data Lake Storage Gen1-konto har skapats.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Aktivera Data Lake Storage Gen1 brand vägg med åtkomst till Azure-tjänsten

Data Lake Storage Gen1 stöder möjligheten att aktivera en brand vägg och begränsa åtkomsten till Azure-tjänster, vilket rekommenderas för en mindre angrepps vektor från externa intrång. Brand väggen kan aktive ras på data Lake Storage gen1 kontot i Azure Portal via **brand väggen**  >  **Aktivera brand vägg (på)**  >  **Tillåt åtkomst till Azure-tjänster** .

![Brand Väggs inställningar i Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Brand Väggs inställningar i Data Lake Storage Gen1")

När brand väggen är aktive rad har endast Azure-tjänster som HDInsight, Data Factory, Azure Synapse Analytics, osv. åtkomst till Data Lake Storage Gen1. På grund av de interna Network Address Translation som används av Azure stöder Data Lake Storage Gen1 brand väggen inte begränsning av vissa tjänster av IP och är bara avsedd för begränsningar för slut punkter utanför Azure, till exempel lokalt.

## <a name="performance-and-scale-considerations"></a>Överväganden för prestanda och skalning

En av de mest kraftfulla funktionerna i Data Lake Storage Gen1 är att den tar bort hård gränserna för data genom strömning. Genom att ta bort gränserna kan kunderna öka sin data storlek och åtföljda prestanda krav utan att behöva Shard data. Ett av de viktigaste övervägandena för att optimera Data Lake Storage Gen1 prestanda är att det fungerar bäst när det gäller parallellitet.

### <a name="improve-throughput-with-parallelism"></a>Förbättra data flödet med parallellitet

Överväg att ge 8-12 trådar per kärna för det mest optimala Skriv-och Skriv data flödet. Detta beror på att blockera läsningar/skrivningar på en enskild tråd, och fler trådar kan tillåta högre samtidighet på den virtuella datorn. För att säkerställa att nivåerna är felfria och parallellt kan ökas, måste du övervaka den virtuella datorns processor användning.

### <a name="avoid-small-file-sizes"></a>Undvik små fil storlekar

POSIX-behörigheter och granskning i Data Lake Storage Gen1 levereras med en kostnad som är synlig när du arbetar med många små filer. Som bästa praxis måste du gruppera dina data i större filer jämfört med att skriva tusentals eller miljon tals små filer till Data Lake Storage Gen1. Att undvika små fil storlekar kan ha flera fördelar, till exempel:

* Minska verifierings kontrollerna över flera filer
* Färre öppna fil anslutningar
* Snabbare kopiering/replikering
* Färre filer att bearbeta vid uppdatering av Data Lake Storage Gen1 POSIX-behörigheter

Beroende på vilka tjänster och arbets belastningar som använder data, är en bra storlek att överväga för filer som är 256 MB eller mer. Om fil storlekarna inte kan grupperas vid landning i Data Lake Storage Gen1, kan du ha ett separat komprimerings jobb som kombinerar dessa filer till större. Mer information och rekommendationer om fil storlekar och hur du ordnar data i Data Lake Storage Gen1 finns i [strukturera din data uppsättning](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Stora fil storlekar och potentiell prestanda påverkan

Även om Data Lake Storage Gen1 stöder stora filer upp till petabyte i storlek, för optimala prestanda och beroende på processen som läser data, kanske det inte är idealiskt att gå över 2 GB i genomsnitt. Till exempel, när du använder **Distcp** för att kopiera data mellan platser eller olika lagrings konton, är filer den Finest nivån av granularitet som används för att fastställa kart aktiviteter. Så om du kopierar 10 filer som är 1 TB, allokeras de högst 10 mapparna. Om du har flera filer med kopplade mappers, fungerar mapparna från början parallellt för att flytta stora filer. Men eftersom jobbet börjar stängas av bara några få mapparna förblir allokerade och du kan fastna med en enda mappare som är kopplad till en stor fil. Microsoft har skickat förbättringar till Distcp för att åtgärda problemet i framtida Hadoop-versioner.

Ett annat exempel är om du använder Azure Data Lake Analytics med Data Lake Storage Gen1. Beroende på den bearbetning som utförs av Extractor kan vissa filer som inte kan delas (till exempel XML, JSON) försämras i prestandan om större än 2 GB. I de fall där filer kan delas av en Extractor (till exempel CSV) är stora filer önskade.

### <a name="capacity-plan-for-your-workload"></a>Kapacitets plan för din arbets belastning

Azure Data Lake Storage Gen1 tar bort de begränsade IO-gränser som placeras på Blob Storage-konton. Det finns dock fortfarande mjuka gränser som behöver beaktas. Standard begränsningen för ingångs-/utgångs begränsningar uppfyller de flesta scenariernas behov. Om din arbets belastning måste öka, arbetar du med Microsoft support. Du kan också titta på gränserna under POC-fasen så att IO-begränsningen inte uppnåddes under produktionen. Om detta inträffar kan det krävas en väntan på en manuell ökning från Microsofts teknik team. Om IO-begränsning inträffar returnerar Azure Data Lake Storage Gen1 felkoden 429 och helst bör nya försök göras med en lämplig exponentiell backoff-princip.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Optimera "skrivningar" med Data Lake Storage Gen1-drivrutinens buffert

För att optimera prestanda och minska IOPS vid skrivning till Data Lake Storage Gen1 från Hadoop, utför Skriv åtgärder så nära den Data Lake Storage Gen1 driv Rutinens buffertstorlek som möjligt. Försök att inte överskrida buffertstorleken innan du tömmer, till exempel vid strömning med Apache Storm eller Spark streaming-arbetsbelastningar. När du skriver till Data Lake Storage Gen1 från HDInsight/Hadoop är det viktigt att veta att Data Lake Storage Gen1 har en driv rutin med en 4 MB-buffert. Precis som många fil Systems driv rutiner kan den här bufferten tömmas manuellt innan den når 4 MB-storleken. Om inte, töms den omedelbart till lagring om nästa skrivning överskrider buffertens maximala storlek. Där det är möjligt måste du undvika en överskriden eller en betydande underrun av bufferten när du synkroniserar/tömmer principer efter antal eller tidsfönster.

## <a name="resiliency-considerations"></a>Att tänka på om återhämtning

När du skapar ett system med Data Lake Storage Gen1 eller en moln tjänst måste du ta hänsyn till dina tillgänglighets krav och hur du svarar på potentiella avbrott i tjänsten. Ett problem kan lokaliseras till den aktuella instansen eller till och med hela regionen, så det är viktigt att du har en plan för båda. Beroende på **återställnings tid** och **återställnings punkt mål** service avtal för din arbets belastning kan du välja en mer eller mindre aggressiv strategi för hög tillgänglighet och haveri beredskap.

### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap

Hög tillgänglighet (HA) och haveri beredskap (DR) kan ibland kombineras tillsammans, även om var och en har en annorlunda strategi, särskilt när den kommer till data. Data Lake Storage Gen1 hanterar redan 3x-replikering under huven för att skydda mot lokaliserade maskin varu problem. Eftersom replikering mellan regioner inte är inbyggt i måste du dock själv hantera det. När du skapar en plan för HA, i händelse av en tjänst avbrott, behöver arbets belastningen till gång till den senaste informationen så snabbt som möjligt genom att växla över till en separat replikerad instans lokalt eller i en ny region.

I en katastrof strategi är det också viktigt att ha data som replikeras till en annan region för att förbereda dig för att få ett oåterkalleligt avbrott i en region. Dessa data kan ursprungligen vara samma som de replikerade HA-data. Du måste dock också ta hänsyn till dina krav för Edge-fall, till exempel skadade data, där du kanske vill skapa regelbundna ögonblicks bilder för att återgå till. Beroende på informationens betydelse och storlek bör du överväga rullande delta-ögonblicks bilder av 1 –, 6-och 24-timmarsperiod på den lokala och/eller sekundära lagrings platsen, enligt risk tolerans.

För data återhämtning med Data Lake Storage Gen1 rekommenderar vi att du geo-replikerar dina data till en separat region med en frekvens som uppfyller dina krav för HA/DR, helst varje timme. Den här frekvensen av replikering minimerar enorma data rörelser som kan ha konkurrerande data flöde med huvud systemet och ett bättre återställnings punkt mål. Dessutom bör du överväga hur programmet ska använda Data Lake Storage Gen1 för att automatiskt redundansväxla till det sekundära kontot genom övervaknings utlösare eller längden på misslyckade försök, eller åtminstone skicka ett meddelande till administratörer för manuell inblandning. Tänk på att det finns kompromisser med att redundansväxla och vänta på att en tjänst ska bli online igen. Om data inte har repliker ATS kan en redundansväxling orsaka potentiell data förlust, inkonsekvens eller komplex sammanslagning av data.

Nedan visas de tre vanligaste alternativen för att dirigera replikering mellan Data Lake Storage Gen1 konton och viktiga skillnader mellan var och en av dem.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Skalnings gränser**     | Begränsas av arbetsnoder        | Begränsas av max enheter för data förflyttning i molnet        | Begränsas av Analytics-enheter        |
|**Stöder kopiering av delta**     |   Ja      | Inga         | Inga         |
|**Inbyggd dirigering**     |  Nej (Använd Oozie-flöde eller cron-jobb)       | Ja        | Nej (Använd Azure Automation-eller Windows-Schemaläggaren)         |
|**Fil system som stöds**     | ADL, HDFS, WASB, S3, GS, CFS        |Flera, se [kopplingar](../data-factory/connector-azure-blob-storage.md).         | ADL till ADL, WASB till ADL (endast samma region)        |
|**OS-stöd**     |Alla operativ system som kör Hadoop         | Ej tillämpligt          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Använd Distcp för data förflyttning mellan två platser

För distribuerad kopiering är Distcp ett Linux-kommando rads verktyg som medföljer Hadoop och som tillhandahåller distribuerad data förflyttning mellan två platser. De två platserna kan vara Data Lake Storage Gen1, HDFS, WASB eller S3. Det här verktyget använder MapReduce-jobb i ett Hadoop-kluster (till exempel HDInsight) för att skala ut på alla noder. Distcp betraktas som det snabbaste sättet att flytta Big data utan särskilda nätverks komprimerings anordningar. Distcp innehåller också ett alternativ för att endast uppdatera delta mellan två platser, hanterar automatiska återförsök, samt dynamisk skalning av data bearbetning. Den här metoden är otroligt effektiv när den kommer att replikera saker som Hive/Spark-tabeller som kan ha många stora filer i en enda katalog och som du bara vill kopiera över ändrade data. Av dessa skäl är Distcp det mest rekommenderade verktyget för att kopiera data mellan stora data lager.

Kopierings jobb kan utlösas av Apache Oozie-arbetsflöden med frekvens eller data utlösare, samt Linux cron-jobb. För intensiva replikerings jobb rekommenderas att du skapar ett separat HDInsight Hadoop-kluster som kan justeras och skalas specifikt för kopierings jobben. Detta säkerställer att kopierings jobb inte stör viktiga jobb. Om du kör replikeringen med tillräckligt många frekvenser kan klustret till och med göras mellan varje jobb. Om du växlar över till en sekundär region måste du se till att ett annat kluster också är i den sekundära regionen för att replikera nya data tillbaka till det primära Data Lake Storage Gen1s kontot när det är säkerhets kopie ras. Exempel på hur du använder Distcp finns i [använda Distcp för att kopiera data mellan Azure Storage blobbar och data Lake Storage gen1](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Använd Azure Data Factory för att schemalägga kopierings jobb

Azure Data Factory kan också användas för att schemalägga kopierings jobb med en **kopierings aktivitet** och kan till och med konfigureras på en frekvens via **guiden Kopiera**. Tänk på att Azure Data Factory har en gräns för moln data förflyttnings enheter (DMUs) och till och med till och med till och med för stora data arbets belastningar. Dessutom erbjuder Azure Data Factory för närvarande inte delta uppdateringar mellan Data Lake Storage Gen1-konton, så mappar som Hive-tabeller kräver en fullständig kopiering för att replikeras. Se [justerings guiden för kopierings aktivitet](../data-factory/copy-activity-performance.md) för mer information om hur du kopierar med Data Factory.

### <a name="adlcopy"></a>AdlCopy

AdlCopy är ett kommando rads verktyg för Windows som gör att du kan kopiera data mellan två Data Lake Storage Gen1 konton inom samma region. Verktyget AdlCopy innehåller ett fristående alternativ eller ett alternativ för att använda ett Azure Data Lake Analytics konto för att köra kopierings jobbet. Även om det ursprungligen skapades för kopior på begäran, till skillnad från en robust replikering, ger det ett annat alternativ för att utföra distribuerad kopiering mellan Data Lake Storage Gen1-konton inom samma region. För tillförlitlighet rekommenderar vi att du använder Premium Data Lake Analytics-alternativet för alla produktions arbets belastningar. Den fristående versionen kan returnera upptaget svar och har begränsad skalning och övervakning.

Precis som Distcp måste AdlCopy dirigeras av något som Azure Automation eller Schemaläggaren i Windows. Precis som med Data Factory stöder AdlCopy inte kopiering av endast uppdaterade filer, utan kopierar och skriver över befintliga filer. Mer information och exempel på hur du använder AdlCopy finns i [Kopiera data från Azure Storage blobbar till data Lake Storage gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Övervaknings överväganden

Data Lake Storage Gen1 innehåller detaljerade diagnostikloggar och granskning. Data Lake Storage Gen1 innehåller några grundläggande mått i Azure Portal under Data Lake Storage Gen1-kontot och i Azure Monitor. Tillgängligheten för Data Lake Storage Gen1 visas i Azure Portal. Detta mått uppdateras dock var sjunde minut och kan inte frågas via ett offentligt exponerat API. För att få den senaste tillgängliga tillgängligheten för ett Data Lake Storage Gen1-konto måste du köra dina egna syntetiska tester för att verifiera tillgänglighet. Andra mått, till exempel den totala lagrings användningen, Läs-och skriv förfrågningar och ingående/utgående data kan ta upp till 24 timmar innan uppdateringen. Därför måste du beräkna mer aktuella mått manuellt via Hadoop-kommando rads verktyg eller aggregera logg information. Det snabbaste sättet att få den senaste lagrings användningen att köra detta HDFS-kommando från en Hadoop-klusternod (till exempel Head-nod):

```console
hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/
```

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Exportera Data Lake Storage Gen1 diagnostik

Ett av de snabbaste sätten att få åtkomst till sökbara loggar från Data Lake Storage Gen1 är att aktivera logg överföring till **Log Analytics** under **diagnostik** -bladet för data Lake Storage gen1-kontot. Detta ger omedelbar åtkomst till inkommande loggar med tids-och innehålls filter, tillsammans med aviserings alternativ (e-post/webhook) som utlösts inom 15-minuters intervall. Instruktioner finns i [komma åt diagnostikloggar för Azure Data Lake Storage gen1](data-lake-store-diagnostic-logs.md).

Om du vill ha mer aviseringar om real tids avisering och mer kontroll över var loggarna finns kan du exportera loggar till Azure-EventHub där innehåll kan analyseras separat eller över en tids period för att skicka meddelanden i real tid till en kö. Ett separat program, till exempel en [Logic app](../connectors/connectors-create-api-azure-event-hubs.md) , kan sedan använda och kommunicera aviseringarna till lämplig kanal, samt skicka Mät värden till övervaknings verktyg som NewRelic, Datadog eller AppDynamics. Om du använder ett verktyg från tredje part, till exempel ElasticSearch, kan du exportera loggarna till Blob Storage och använda [Azure Logstash-plugin-programmet](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) för att använda data i ElasticSearch-, Kibana-och Logstash-stacken (Elk).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Aktivera loggning på fel söknings nivå i HDInsight

Om Data Lake Storage Gen1 logg överföring inte är aktive rad kan du också använda Azure HDInsight för att aktivera [loggning på klient sidan för data Lake Storage gen1](data-lake-store-performance-tuning-mapreduce.md) via log4j. Du måste ange följande egenskap i **Ambari**  >  **garn**  >  **config**  >  **Advanced garn-log4j konfigurationer**:

`log4j.logger.com.microsoft.azure.datalake.store=DEBUG`

När egenskapen har angetts och noderna har startats om, skrivs Data Lake Storage Gen1 diagnostik till garn loggarna på noderna (/TMP/ \<user\> /yarn.log) och viktig information som fel eller begränsning (HTTP 429-felkod) kan övervakas. Samma information kan också övervakas i Azure Monitor loggar eller där loggar skickas till i bladet [diagnostik](data-lake-store-diagnostic-logs.md) i data Lake Storage gen1-kontot. Vi rekommenderar minst att logga in på klient sidan har Aktiver ATS eller använda alternativet logg överföring med Data Lake Storage Gen1 för att se om den är synlig och enklare att felsöka.

### <a name="run-synthetic-transactions"></a>Köra syntetiska transaktioner

För närvarande har tjänst tillgänglighets måttet för Data Lake Storage Gen1 i Azure Portal uppdaterings fönstret för 7 minuter. Det går inte heller att fråga på ett offentligt exponerat API. Därför rekommenderar vi att du skapar ett grundläggande program som gör syntetiska transaktioner till Data Lake Storage Gen1 som kan ge upp till minut tillgänglighet. Ett exempel kan vara att skapa ett webb jobb, en logisk app eller Azure-Funktionsapp för att utföra en läsning, skapa och uppdatera mot Data Lake Storage Gen1 och skicka resultatet till din övervaknings lösning. Åtgärderna kan utföras i en tillfällig mapp och tas sedan bort efter testet, vilket kan köras var 30-60: e sekund, beroende på kraven.

## <a name="directory-layout-considerations"></a>Överväganden för katalogens layout

Vid landning av data till en data Lake är det viktigt att förplana data strukturen så att säkerhet, partitionering och bearbetning kan användas effektivt. Många av följande rekommendationer kan användas oavsett om det är med Azure Data Lake Storage Gen1, Blob Storage eller HDFS. Varje arbets belastning har olika krav på hur data förbrukas, men nedan finns några vanliga layouter att tänka på när du arbetar med IoT-och batch-scenarier.

### <a name="iot-structure"></a>IoT-struktur

I IoT-arbetsbelastningar kan det finnas en stor mängd data som landats i det data lager som sträcker sig över flera produkter, enheter, organisationer och kunder. Det är viktigt att i förväg planera katalogens layout för organisation, säkerhet och effektiv bearbetning av data för äldre användare. En allmän mall som du kan tänka på kan vara följande layout:

```console
{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/
```

Till exempel kan landning av telemetri för en flyg Plans motor i Storbritannien se ut som följande struktur:

```console
UK/Planes/BA1293/Engine1/2017/08/11/12/
```

Det är viktigt att du försätter datumet i slutet av mappstrukturen. Om du vill låsa vissa regioner eller ämnes områden för användare/grupper kan du enkelt göra det med POSIX-behörigheterna. Annars, om det fanns ett behov av att begränsa en viss säkerhets grupp för att bara visa data från Storbritannien eller vissa plan, med datum strukturen i front, krävs en separat behörighet för flera mappar under varje Hour-mapp. Med datum strukturen i front skulle du dessutom öka antalet mappar som tid gick till.

### <a name="batch-jobs-structure"></a>Batch-jobb struktur

Från en hög nivå är en ofta använd metod i batchbearbetningen att landa in data i en "i"-mapp. När data har bearbetats sätter du in nya data i en "out"-mapp för efterföljande processer att använda. Den här katalog strukturen visas ibland för jobb som kräver bearbetning av enskilda filer och kanske inte kräver storskalig parallell bearbetning över stora data mängder. Precis som den IoT-struktur som rekommenderas ovan, har en bra katalog struktur de överordnade mapparna för saker som region och ämnes områden (till exempel organisation, produkt/producent). Den här strukturen hjälper till att skydda data i din organisation och bättre hantering av data i dina arbets belastningar. Dessutom kan du överväga datum och tid i strukturen för att ge bättre organisation, filtrerade sökningar, säkerhet och automatisering i bearbetningen. Nivån på granularitet för datum strukturen bestäms av intervallet för överföring eller bearbetning av data, till exempel varje timme, dagligen eller till och med varje månad.

Ibland Miss lyckas fil bearbetningen på grund av skadade data eller oväntade format. I sådana fall kan katalog strukturen dra nytta av en **/bad** -mapp för att flytta filerna till för ytterligare granskning. Batch-jobbet kan också hantera rapportering eller meddelanden om dessa *felaktiga* filer för manuella åtgärder. Tänk på följande mall:

```console
{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/
```

Ett marknadsförings företag kan till exempel ta emot dagliga data utdrag för kund uppdateringar från sina klienter i Nordamerika. Det kan se ut som i följande kodfragment innan och efter att de har bearbetats:

```console
NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv
```

I det vanliga fallet med batch-data som bearbetas direkt till databaser som Hive eller traditionella SQL-databaser, behöver inte en **/in** -eller **/out** -mapp eftersom utdata redan placeras i en separat mapp för Hive-tabellen eller den externa databasen. Dagliga utdrag från kunder skulle till exempel kunna hamna i sina respektive mappar och dirigeras av något som liknar Azure Data Factory, Apache Oozie eller Apache-luftflöde skulle utlösa ett dagligt Hive-eller Spark-jobb för att bearbeta och skriva data till en Hive-tabell.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Access Control i Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Säkerhet i Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Justera Azure Data Lake Storage Gen1 för prestanda](data-lake-store-performance-tuning-guidance.md)
* [Vägledning för prestanda justering för att använda HDInsight Spark med Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Vägledning för prestanda justering för att använda HDInsight Hive med Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [Skapa HDInsight-kluster med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
