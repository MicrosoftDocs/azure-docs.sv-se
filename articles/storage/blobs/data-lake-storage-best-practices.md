---
title: Metod tips för att använda Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig metod tips om data inmatning, datum säkerhet och prestanda som rör användning av Azure Data Lake Storage Gen2 (tidigare kallat Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 937048ce14b9b05b55cd8d76e7a8c1fd67c63e4d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933716"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Metod tips för att använda Azure Data Lake Storage Gen2

I den här artikeln får du lära dig mer om bästa praxis och överväganden för att arbeta med Azure Data Lake Storage Gen2. Den här artikeln innehåller information kring säkerhet, prestanda, återhämtning och övervakning för Data Lake Storage Gen2. Innan Data Lake Storage Gen2 arbetar du med verkligt stor data i tjänster som Azure HDInsight var komplex. Du var tvungen att Shard data över flera Blob Storage-konton så att petabyte-lagring och optimala prestanda vid denna skala skulle kunna uppnås. Data Lake Storage Gen2 stöder enskilda fil storlekar så högt som 190,7 TiB och de flesta hård gränserna för prestanda har tagits bort. Det finns dock fortfarande vissa överväganden som beskrivs i den här artikeln så att du kan få bästa möjliga prestanda med Data Lake Storage Gen2.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Azure Data Lake Storage Gen2 har POSIX-åtkomst kontroller för Azure Active Directory (Azure AD)-användare, grupper och tjänst huvud namn. Dessa åtkomst kontroller kan ställas in på befintliga filer och kataloger. Åtkomst kontrollerna kan också användas för att skapa standard behörigheter som kan tillämpas automatiskt på nya filer eller kataloger. Mer information om Data Lake Storage Gen2 ACL: er finns på [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Använd säkerhets grupper jämfört med enskilda användare

När du arbetar med Big data i Data Lake Storage Gen2 är det troligt att ett tjänst huvud namn används för att tillåta tjänster som Azure HDInsight att arbeta med data. Det kan dock finnas fall där enskilda användare behöver åtkomst till data också. I samtliga fall bör du starkt överväga att använda Azure Active Directory [säkerhets grupper](../common/storage-auth-aad.md) i stället för att tilldela enskilda användare till kataloger och filer.

När en säkerhets grupp har tilldelats behörigheter behöver inte några uppdateringar Data Lake Storage Gen2 för att lägga till eller ta bort användare från gruppen. Detta säkerställer också att du inte överskrider det maximala antalet åtkomst kontroll poster per ACL (Access Control List). För närvarande är det här värdet 32, (inklusive de fyra ACL: er för POSIX-typ som alltid är associerade med varje fil och katalog): ägande användare, ägande grupp, mask och annan. Varje katalog kan ha två typer av ACL, åtkomst-ACL och standard-ACL, för totalt 64 åtkomst kontroll poster. Mer information om dessa ACL: er finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Säkerhet för grupper

När du eller dina användare behöver åtkomst till data i ett lagrings konto med hierarkiskt namn område aktiverat, är det bäst att använda Azure Active Directory säkerhets grupper. Vissa rekommenderade grupper att starta med kan vara **ReadOnlyUsers**, **WriteAccessUsers** och **FullAccessUsers** för roten i behållaren och till och med separera dem för nyckel under kataloger. Om det finns andra förväntade grupper av användare som kan läggas till senare, men ännu inte har identifierats, kan du överväga att skapa dummy-säkerhetsgrupper som har åtkomst till vissa mappar. Med hjälp av säkerhets gruppen ser du till att du kan undvika lång bearbetnings tid när du tilldelar nya behörigheter till tusentals filer.

### <a name="security-for-service-principals"></a>Säkerhet för tjänstens huvud namn

Azure Active Directory tjänstens huvud namn används vanligt vis av tjänster som Azure Databricks för att få åtkomst till data i Data Lake Storage Gen2. För många kunder kan ett enda Azure Active Directory tjänstens huvud namn vara tillräckligt, och det kan ha fullständig behörighet i roten i Data Lake Storage Gen2 containern. Andra kunder kan kräva flera kluster med olika tjänst huvud namn där ett kluster har fullständig åtkomst till data och ett annat kluster med enbart Läs behörighet. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Aktivera Data Lake Storage Gen2 brand vägg med åtkomst till Azure-tjänsten

Data Lake Storage Gen2 stöder möjligheten att aktivera en brand vägg och begränsa åtkomsten till Azure-tjänster, vilket rekommenderas för att begränsa den externa attackens vektor. Brand väggen kan aktive ras på ett lagrings konto i Azure Portal via **brand väggen**  >  **Aktivera brand vägg (på)**  >  **Tillåt åtkomst till alternativen för Azure-tjänster** .

Om du vill komma åt ditt lagrings konto från Azure Databricks distribuerar du Azure Databricks till ditt virtuella nätverk och lägger sedan till det virtuella nätverket i brand väggen. Se [konfigurera Azure Storage brand väggar och virtuella nätverk](../common/storage-network-security.md).

## <a name="resiliency-considerations"></a>Att tänka på om återhämtning

När du skapar ett system med Data Lake Storage Gen2 eller en moln tjänst måste du ta hänsyn till dina tillgänglighets krav och hur du svarar på potentiella avbrott i tjänsten. Ett problem kan lokaliseras till den aktuella instansen eller till och med hela regionen, så det är viktigt att du har en plan för båda. Beroende på återställnings tid och återställnings punkt mål service avtal för din arbets belastning kan du välja en mer eller mindre aggressiv strategi för hög tillgänglighet och haveri beredskap.

### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap

Hög tillgänglighet (HA) och haveri beredskap (DR) kan ibland kombineras tillsammans, även om var och en har en annorlunda strategi, särskilt när den kommer till data. Data Lake Storage Gen2 hanterar redan 3x-replikering under huven för att skydda mot lokaliserade maskin varu problem. Dessutom kan andra replikeringsalternativ, till exempel ZRS eller GZRS, förbättra HA, medan GRS & RA-GRS förbättra DR. När du skapar en plan för HA, i händelse av en tjänst avbrott, behöver arbets belastningen till gång till den senaste informationen så snabbt som möjligt genom att växla över till en separat replikerad instans lokalt eller i en ny region.

I en katastrof strategi är det också viktigt att ha data som replikeras till en annan region med hjälp av GRS eller RA-GRS replikering för att förbereda sig på ett oåterkalleligt problem i en region. Du måste också ta hänsyn till dina krav för gräns ärenden, till exempel skadade data, där du kanske vill skapa regelbundna ögonblicks bilder för att återgå till. Beroende på informationens betydelse och storlek bör du överväga rullande delta-ögonblicks bilder på 1 –, 6-och 24-timmarsperiod enligt risk tolerans.

För data återhämtning med Data Lake Storage Gen2 rekommenderar vi att du geo-replikerar dina data via GRS eller RA-GRS som uppfyller kraven för HA/DR. Dessutom bör du överväga hur programmet ska använda Data Lake Storage Gen2 för att automatiskt redundansväxla till den sekundära regionen genom övervaknings utlösare eller längden på misslyckade försök, eller åtminstone skicka ett meddelande till administratörer för manuella åtgärder. Tänk på att det finns kompromisser med att redundansväxla och vänta på att en tjänst ska bli online igen.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Använd Distcp för data förflyttning mellan två platser

För distribuerad kopiering är DistCp ett Linux-kommando rads verktyg som medföljer Hadoop och som tillhandahåller distribuerad data förflyttning mellan två platser. De två platserna kan vara Data Lake Storage Gen2, HDFS eller S3. Det här verktyget använder MapReduce-jobb i ett Hadoop-kluster (till exempel HDInsight) för att skala ut på alla noder. Distcp betraktas som det snabbaste sättet att flytta Big data utan särskilda nätverks komprimerings anordningar. Distcp innehåller också ett alternativ för att endast uppdatera delta mellan två platser, hanterar automatiska återförsök, samt dynamisk skalning av data bearbetning. Den här metoden är otroligt effektiv när den kommer att replikera saker som Hive/Spark-tabeller som kan ha många stora filer i en enda katalog och som du bara vill kopiera över ändrade data. Av dessa skäl är Distcp det mest rekommenderade verktyget för att kopiera data mellan stora data lager.

Kopierings jobb kan utlösas av Apache Oozie-arbetsflöden med frekvens eller data utlösare, samt Linux cron-jobb. För intensiva replikerings jobb rekommenderas att du skapar ett separat HDInsight Hadoop-kluster som kan justeras och skalas specifikt för kopierings jobben. Detta säkerställer att kopierings jobb inte stör viktiga jobb. Om du kör replikeringen med tillräckligt många frekvenser kan klustret till och med göras mellan varje jobb. Om du växlar över till en sekundär region måste du se till att ett annat kluster också är i den sekundära regionen för att replikera nya data tillbaka till det primära Data Lake Storage Gen2s kontot när det är säkerhets kopie ras. Exempel på hur du använder Distcp finns i [använda Distcp för att kopiera data mellan Azure Storage blobbar och data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Använd Azure Data Factory för att schemalägga kopierings jobb

Azure Data Factory kan också användas för att schemalägga kopierings jobb med en kopierings aktivitet och kan till och med konfigureras på en frekvens via guiden Kopiera. Tänk på att Azure Data Factory har en gräns för moln data förflyttnings enheter (DMUs) och till och med till och med till och med för stora data arbets belastningar. Dessutom erbjuder Azure Data Factory för närvarande inte delta uppdateringar mellan Data Lake Storage Gen2-konton, så kataloger som Hive-tabeller kräver en fullständig kopiering för att replikeras. Mer information om hur du kopierar med Data Factory hittar du i [artikeln Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) .

## <a name="monitoring-considerations"></a>Övervaknings överväganden

Data Lake Storage Gen2 tillhandahåller mått i Azure Portal under Data Lake Storage Gen2-kontot och i Azure Monitor. Tillgängligheten för Data Lake Storage Gen2 visas i Azure Portal. För att få den senaste tillgängliga tillgängligheten för ett Data Lake Storage Gen2-konto måste du köra dina egna syntetiska tester för att verifiera tillgänglighet. Andra mått, till exempel den totala lagrings användningen, Läs-och skriv förfrågningar och ingående/utgående, är tillgängliga för användning av övervakningsprogram och kan även utlösa aviseringar när tröskelvärden (till exempel genomsnittlig svars tid eller antal fel per minut) överskrids.

## <a name="directory-layout-considerations"></a>Överväganden för katalogens layout

Vid landning av data till en data Lake är det viktigt att förplana data strukturen så att säkerhet, partitionering och bearbetning kan användas effektivt. Många av följande rekommendationer gäller för alla Big data-arbetsbelastningar. Varje arbets belastning har olika krav på hur data förbrukas, men nedan finns några vanliga layouter att tänka på när du arbetar med IoT-och batch-scenarier.

### <a name="iot-structure"></a>IoT-struktur

I IoT-arbetsbelastningar kan det finnas en stor mängd data som landats i det data lager som sträcker sig över flera produkter, enheter, organisationer och kunder. Det är viktigt att i förväg planera katalogens layout för organisation, säkerhet och effektiv bearbetning av data för äldre användare. En allmän mall som du kan tänka på kan vara följande layout:

*{Region}/{SubjectMatter (s)}/{YYYY}/{MM}/{DD}/{hh}/*

Till exempel kan landning av telemetri för en flyg Plans motor i Storbritannien se ut som följande struktur:

*Storbritannien/plan/BA1293/Engine1/2017/08/11/12/*

Det är viktigt att du försätter datumet i slutet av katalog strukturen. Om du vill låsa vissa regioner eller ämnes områden för användare/grupper kan du enkelt göra det med POSIX-behörigheterna. Annars, om det fanns ett behov av att begränsa en viss säkerhets grupp för att bara visa data från Storbritannien eller vissa plan, med datum strukturen i front, krävs en separat behörighet för flera kataloger under varje timmes katalog. Med datum strukturen i front skulle det dessutom öka antalet kataloger som tid gick på.

### <a name="batch-jobs-structure"></a>Batch-jobb struktur

Från en hög nivå är en ofta använd metod i batchbearbetningen att landa data i en "i"-katalog. När data har bearbetats sätter du in nya data i en "out"-katalog för efterföljande processer att använda. Den här katalog strukturen visas ibland för jobb som kräver bearbetning av enskilda filer och kanske inte kräver storskalig parallell bearbetning över stora data mängder. Precis som den IoT-struktur som rekommenderas ovan har en bra katalog struktur de överordnade katalogerna för saker som region och ämnes områden (till exempel organisation, produkt/producent). Den här strukturen hjälper till att skydda data i din organisation och bättre hantering av data i dina arbets belastningar. Dessutom kan du överväga datum och tid i strukturen för att ge bättre organisation, filtrerade sökningar, säkerhet och automatisering i bearbetningen. Nivån på granularitet för datum strukturen bestäms av intervallet för överföring eller bearbetning av data, till exempel varje timme, dagligen eller till och med varje månad.

Ibland Miss lyckas fil bearbetningen på grund av skadade data eller oväntade format. I sådana fall kan katalog strukturen dra nytta av en **/bad** -mapp för att flytta filerna till för ytterligare granskning. Batch-jobbet kan också hantera rapportering eller meddelanden om dessa *felaktiga* filer för manuella åtgärder. Tänk på följande mall:

*{Region}/{SubjectMatter (s)}/in/{YYYY}/{MM}/{DD}/{hh}/*\
*{Region}/{SubjectMatter (s)}/out/{YYYY}/{MM}/{DD}/{hh}/*\
*{Region}/{SubjectMatter (s)}/bad/{YYYY}/{MM}/{DD}/{hh}/*

Ett marknadsförings företag kan till exempel ta emot dagliga data utdrag för kund uppdateringar från sina klienter i Nordamerika. Det kan se ut som i följande kodfragment innan och efter att de har bearbetats:

*NA/extrakt/ACMEPaperCo/in/2017/08/14/updates_08142017.csv*\
*NA/extrakt/ACMEPaperCo/out/2017/08/14/processed_updates_08142017.csv*

I det vanliga fallet med batch-data som bearbetas direkt till databaser som Hive eller traditionella SQL-databaser, behöver inte en **/in** -eller **/out** -mapp eftersom utdata redan placeras i en separat mapp för Hive-tabellen eller den externa databasen. Dagliga utdrag från kunder skulle till exempel kunna hamna i sina respektive mappar och dirigeras av något som liknar Azure Data Factory, Apache Oozie eller Apache-luftflöde skulle utlösa ett dagligt Hive-eller Spark-jobb för att bearbeta och skriva data till en Hive-tabell.
