---
title: Migrera data från ett lokalt Hadoop-kluster till Azure Storage
description: Lär dig hur du använder Azure Data Factory för att migrera data från ett lokalt Hadoop-kluster till Azure Storage.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 9959a37d9b68d756437a3b4f0d75a2d63385758e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367800"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Använd Azure Data Factory för att migrera data från ett lokalt Hadoop-kluster till Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory tillhandahåller en genomförd, robust och kostnads effektiv mekanism för att migrera data i stor skala från en lokal HDFS till Azure Blob Storage eller Azure Data Lake Storage Gen2. 

Data Factory erbjuder två grundläggande metoder för att migrera data från en lokal HDFS till Azure. Du kan välja metod baserat på ditt scenario. 

- **Data Factory DistCp-läge** (rekommenderas): i Data Factory kan du använda [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (distribuerad kopia) för att kopiera filer som-är till Azure Blob Storage (inklusive [mellanlagrad kopia](./copy-activity-performance.md#staged-copy)) eller Azure Data Lake Store Gen2. Använd Data Factory integrerat med DistCp för att dra nytta av ett befintligt kraftfullt kluster för att uppnå bästa kopiering av data flödet. Du får också fördelarna med flexibel schemaläggning och en enhetlig övervaknings upplevelse från Data Factory. Beroende på din Data Factory konfiguration konstruerar kopierings aktiviteten automatiskt ett DistCp-kommando, skickar data till ditt Hadoop-kluster och övervakar sedan kopierings statusen. Vi rekommenderar Data Factory DistCp-läge för att migrera data från ett lokalt Hadoop-kluster till Azure.
- **Data Factory ursprungligt integrerings körnings läge**: DistCp är inte ett alternativ i alla scenarier. I en Azure Virtual Networks-miljö stöder t. ex. DistCp-verktyget inte Azure ExpressRoute Private-peering med en Azure Storage virtuell nätverks slut punkt. I vissa fall vill du inte använda ditt befintliga Hadoop-kluster som en motor för att migrera data så att du inte lägger till tung belastning i klustret, vilket kan påverka prestanda för befintliga ETL-jobb. I stället kan du använda den inbyggda funktionen i Data Factory integration runtime som motor som kopierar data från en lokal HDFS till Azure.

Den här artikeln innehåller följande information om båda metoderna:
> [!div class="checklist"]
> * Prestanda 
> * Kopiera återhämtning
> * Nätverkssäkerhet
> * Hög nivå lösnings arkitektur 
> * Metod tips för implementering  

## <a name="performance"></a>Prestanda

I Data Factory DistCp-läge är genomflödet detsamma som om du använder DistCp-verktyget oberoende av varandra. Data Factory DistCp-läge maximerar kapaciteten hos ditt befintliga Hadoop-kluster. Du kan använda DistCp för kopiering mellan kluster eller inom kluster. 

DistCp använder MapReduce för att påverka distribution, fel hantering och återställning samt rapportering. Den expanderar en lista över filer och kataloger i indatamängden för aktivitets mappning. Varje aktivitet kopierar en filpartition som anges i listan källa. Du kan använda Data Factory integrerat med DistCp för att bygga pipeliner för att fullt ut utnyttja din nätverks bandbredd, lagrings-IOPS och bandbredd för att maximera data flödet för data förflyttning i din miljö.  

Data Factory ursprungligt integration runtime-läge tillåter också parallellitet på olika nivåer. Du kan använda parallellitet för att helt utnyttja din nätverks bandbredd, Storage IOPS och bandbredd för att maximera data flödet för data förflyttning:

- En enda kopierings aktivitet kan dra nytta av skalbara beräknings resurser. Med en egen värd för integration runtime kan du manuellt skala upp datorn eller skala ut till flera datorer ([upp till fyra noder](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)). En enda kopierings aktivitet partitionerar sin fil uppsättning på alla noder. 
- En enskild kopierings aktivitet läser från och skriver till data lagret genom att använda flera trådar. 
- Data Factory kontroll flöde kan starta flera kopierings aktiviteter parallellt. Du kan till exempel använda en [for each-slinga](./control-flow-for-each-activity.md). 

Mer information finns i [prestanda guiden för kopierings aktivitet](./copy-activity-performance.md).

## <a name="resilience"></a>Återhämtning

I Data Factory DistCp-läge kan du använda olika DistCp-kommando rads parametrar (till exempel `-i` Ignorera skrivfel eller `-update` skriva data när käll filen och målfilen skiljer sig i storlek) för olika återhämtnings nivåer.

I en enskild kopierings aktivitets körning i Data Factory Native integration runtime-läge har Data Factory en inbyggd metod för att försöka igen. Den kan hantera en viss nivå av tillfälliga haverier i data lager eller i det underliggande nätverket. 

När du gör en binär kopiering från en lokal HDFS till Blob Storage och från den lokala HDFS till Data Lake Store Gen2, Data Factory automatiskt utföra kontroll punkter i stor utsträckning. Om en kopierings aktivitet körs under ett senare tillfälle (se till att antalet återförsök är > 1), återupptas kopieringen från den senaste fel punkten i stället för från början.

## <a name="network-security"></a>Nätverkssäkerhet 

Data Factory överför som standard data från en lokal HDFS till Blob Storage eller Azure Data Lake Storage Gen2 genom att använda en krypterad anslutning över HTTPS-protokollet. HTTPS tillhandahåller data kryptering vid överföring och förhindrar avlyssning och man-in-the-middle-attacker. 

Alternativt, om du inte vill att data ska överföras via det offentliga Internet, kan du överföra data via en privat peering-länk via ExpressRoute, för högre säkerhet. 

## <a name="solution-architecture"></a>Lösningsarkitektur

Den här bilden visar hur du migrerar data via det offentliga Internet:

![Diagram som visar lösnings arkitekturen för att migrera data via ett offentligt nätverk](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- I den här arkitekturen överförs data säkert med hjälp av HTTPS via det offentliga Internet. 
- Vi rekommenderar att du använder Data Factory DistCp-läge i en offentlig nätverks miljö. Du kan dra nytta av ett kraftfullt befintligt kluster för att uppnå bästa kopiering av data flödet. Du får också fördelarna med flexibel schemaläggning och enhetlig övervaknings upplevelse från Data Factory.
- För den här arkitekturen måste du installera den Data Factory integration runtime med egen värd på en Windows-dator bakom en företags brand vägg för att skicka DistCp-kommandot till ditt Hadoop-kluster och för att övervaka kopierings statusen. Eftersom datorn inte är motorn som kommer att flytta data (endast för kontroll syfte), påverkar kapaciteten för datorn inte data flödet.
- Befintliga parametrar från DistCp-kommandot stöds.

Den här bilden visar hur du migrerar data via en privat länk: 

![Diagram som visar lösnings arkitekturen för att migrera data via ett privat nätverk](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- I den här arkitekturen migreras data via en privat peering-länk via Azure ExpressRoute. Data passerar aldrig över det offentliga Internet.
- DistCp-verktyget stöder inte ExpressRoute privat peering med en Azure Storage virtuell nätverks slut punkt. Vi rekommenderar att du använder Data Factory inbyggda funktioner via integration runtime för att migrera data.
- För den här arkitekturen måste du installera Data Factory integration runtime med egen värd på en virtuell Windows-dator i ditt virtuella Azure-nätverk. Du kan skala upp den virtuella datorn manuellt eller skala ut till flera virtuella datorer för att helt använda nätverket och lagrings-IOPS eller bandbredd.
- Den rekommenderade konfigurationen för att börja med för varje virtuell Azure-dator (med den Data Factory egen värd för integration runtime installerad) är Standard_D32s_v3 med 32 vCPU och 128 GB minne. Du kan övervaka processor-och minnes användningen för den virtuella datorn under datamigreringen för att se om du behöver skala upp den virtuella datorn för bättre prestanda eller skala ned den virtuella datorn för att minska kostnaderna.
- Du kan också skala ut genom att associera upp till fyra VM-noder med en enda egen värd för integration Runtime. Ett enda kopierings jobb som körs mot en egen värd integrerings körning partitionerar automatiskt fil uppsättningen och använder alla VM-noder för att kopiera filerna parallellt. För hög tillgänglighet rekommenderar vi att du börjar med två noder i virtuella datorer för att undvika ett problem med en enda punkt under datamigreringen.
- När du använder den här arkitekturen, är den första migreringen av ögonblicks bilder och delta data tillgängliga.

## <a name="implementation-best-practices"></a>Metod tips för implementering

Vi rekommenderar att du följer dessa rekommendationer när du implementerar datamigreringen.

### <a name="authentication-and-credential-management"></a>Autentisering och hantering av autentiseringsuppgifter 

- Om du vill autentisera till HDFS kan du [antingen använda Windows (Kerberos) eller anonym](./connector-hdfs.md#linked-service-properties). 
- Flera autentiseringstyper stöds för att ansluta till Azure Blob Storage.  Vi rekommenderar starkt att du använder [hanterade identiteter för Azure-resurser](./connector-azure-blob-storage.md#managed-identity). Med hanterade identiteter som bygger på en automatiskt hanterad Data Factory identitet i Azure Active Directory (Azure AD) kan du konfigurera pipelines utan att ange autentiseringsuppgifter i den länkade tjänst definitionen. Alternativt kan du autentisera till Blob Storage med hjälp av ett [huvud namn för tjänsten](./connector-azure-blob-storage.md#service-principal-authentication), en [signatur för delad åtkomst](./connector-azure-blob-storage.md#shared-access-signature-authentication)eller en [lagrings konto nyckel](./connector-azure-blob-storage.md#account-key-authentication). 
- Flera autentiseringstyper stöds också för att ansluta till Data Lake Storage Gen2.  Vi rekommenderar starkt att du använder [hanterade identiteter för Azure-resurser](./connector-azure-data-lake-storage.md#managed-identity), men du kan också använda ett [huvud namn för tjänsten](./connector-azure-data-lake-storage.md#service-principal-authentication) eller en [lagrings konto nyckel](./connector-azure-data-lake-storage.md#account-key-authentication). 
- När du inte använder hanterade identiteter för Azure-resurser rekommenderar vi starkt att du [lagrar autentiseringsuppgifterna i Azure Key Vault](./store-credentials-in-key-vault.md) för att göra det lättare att hantera och rotera nycklar centralt utan att ändra Data Factory länkade tjänster. Detta är också en [bra metod för CI/CD](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Första migrering av ögonblicks bild data 

I Data Factory DistCp-läge kan du skapa en kopierings aktivitet för att skicka DistCp-kommandot och använda olika parametrar för att styra det ursprungliga beteendet för data migrering. 

I Data Factory ursprungligt integrerings körnings läge rekommenderar vi datapartition, särskilt när du migrerar mer än 10 TB data. Om du vill partitionera data använder du mappnamnen på HDFS. Varje Data Factory kopierings jobb kan kopiera en partition i taget. Du kan köra flera Data Factory kopierings jobb samtidigt för bättre data flöde.

Om något av kopierings jobben Miss lyckas på grund av tillfälliga problem med nätverks-eller data lager kan du köra om det misslyckade kopierings jobbet för att läsa in den specifika partitionen från HDFS igen. Andra kopierings jobb som läser in andra partitioner påverkas inte.

### <a name="delta-data-migration"></a>Migrering av delta data 

I Data Factory DistCp-läge kan du använda kommando rads parametern DistCp `-update` , skriva data när käll filen och målfilen skiljer sig i storlek, för migrering av delta data.

I Data Factory enhetligt integrerings läge, är det mest presterande sättet att identifiera nya eller ändrade filer från HDFS genom att använda en tidspartitionerad namngivnings konvention. När dina data i HDFS har tidspartitioner ATS med Time-slice-information i fil-eller mappnamnet (till exempel */yyyy/mm/dd/file.csv*), kan pipelinen enkelt identifiera vilka filer och mappar som ska kopieras stegvis.

Alternativt, om dina data i HDFS inte är tidspartitionerade, kan Data Factory identifiera nya eller ändrade filer med hjälp av deras **LastModifiedDate** -värde. Data Factory genomsöker alla filer från HDFS och kopierar endast nya och uppdaterade filer som har en senast modifierad tidstämpel som är större än ett angivet värde. 

Om du har ett stort antal filer i HDFS kan den första fil genomsökningen ta lång tid, oavsett hur många filer som matchar filter villkoret. I det här scenariot rekommenderar vi att du först partitionerar data genom att använda samma partition som du använde för den första migreringen av ögonblicks bilder. Sedan kan fil genomsökningen ske parallellt.

### <a name="estimate-price"></a>Beräkna pris 

Tänk på följande pipeline för att migrera data från HDFS till Azure Blob Storage: 

![Diagram som visar prissättnings pipelinen](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Vi antar följande information: 

- Total data volym är 1 PB.
- Du migrerar data med hjälp av Data Factory ursprungligt integrerings körnings läge.
- 1 PB är uppdelad i 1 000 partitioner och varje kopia flyttas en partition.
- Varje kopierings aktivitet konfigureras med en egen värd för integrerings körning som är associerad med fyra datorer och som uppnår 500-Mbit/s-dataflöde.
- Förväntad concurrency är **4** och sammanställt data flöde är 2 Gbit/s.
- Totalt tar det 146 timmar att slutföra migreringen.

Här är det uppskattade priset baserat på våra antaganden: 

![Tabell som visar pris beräkningar](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Detta är ett exempel på en hypotetisk prissättning. Din faktiska prissättning beror på det faktiska data flödet i din miljö.
> Priset för en virtuell Azure Windows-dator (med lokal integration runtime installerad) ingår inte.

### <a name="additional-references"></a>Ytterligare referenser

- [HDFS-anslutning](./connector-hdfs.md)
- [Azure Blob Storage-anslutning](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2-anslutning](./connector-azure-data-lake-storage.md)
- [Prestanda justerings guide för kopierings aktivitet](./copy-activity-performance.md)
- [Skapa och konfigurera lokalt installerad integrationskörning](./create-self-hosted-integration-runtime.md)
- [Egen värd för integration runtime med hög tillgänglighet och skalbarhet](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Säkerhetsöverväganden vid dataflytt](./data-movement-security-considerations.md)
- [Spara autentiseringsuppgifter i Azure Key Vault](./store-credentials-in-key-vault.md)
- [Kopiera en fil i taget baserat på ett tidspartitionat fil namn](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Kopiera nya och ändrade filer baserat på LastModifiedDate](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Sidan Data Factory prissättning](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Nästa steg

- [Kopiera filer från flera behållare med hjälp av Azure Data Factory](solution-template-copy-files-multiple-containers.md)