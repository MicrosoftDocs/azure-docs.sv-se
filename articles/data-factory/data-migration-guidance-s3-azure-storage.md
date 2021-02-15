---
title: Migrera data från Amazon S3 till Azure Storage
description: Använd Azure Data Factory för att migrera data från Amazon S3 till Azure Storage.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 2be8a9c7476bda6952ed1eaa15d29fe9c01b59a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371319"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Använd Azure Data Factory för att migrera data från Amazon S3 till Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory ger en genomförd, robust och kostnads effektiv mekanism för att migrera data i stor skala från Amazon S3 till Azure Blob Storage eller Azure Data Lake Storage Gen2.  Den här artikeln innehåller följande information för data tekniker och utvecklare: 

> [!div class="checklist"]
> * Prestanda 
> * Kopiera återhämtning
> * Nätverkssäkerhet
> * Hög nivå lösnings arkitektur 
> * Metod tips för implementering  

## <a name="performance"></a>Prestanda

ADF erbjuder en server lös arkitektur som gör det möjligt att använda parallellitet på olika nivåer, vilket gör att utvecklare kan bygga pipeliner för att fullt ut utnyttja din nätverks bandbredd samt lagrings-IOPS och bandbredd för att maximera data flödet för data flödet i din miljö. 

Kunderna har migrerat petabyte av data som består av hundratals miljoner filer från Amazon S3 till Azure Blob Storage, med ett varaktigt data flöde på 2 Gbit/s och högre. 

![Diagrammet visar flera filpartitioner i ett W S S3-lager med associerade kopierings åtgärder till Azure Blob Storage en D L S Gen2.](media/data-migration-guidance-s3-to-azure-storage/performance.png)

Bilden ovan illustrerar hur du kan uppnå hög hastighet för data förflyttning genom olika nivåer av parallellitet:
 
- En enda kopierings aktivitet kan dra nytta av skalbara beräknings resurser: när du använder Azure Integration Runtime kan du ange [upp till 256 DIUs](./copy-activity-performance.md#data-integration-units) för varje kopierings aktivitet på ett Server lös sätt. När du använder Integration Runtime med egen värd kan du skala upp datorn manuellt eller skala ut till flera datorer ([upp till 4 noder](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)) och en enda kopierings aktivitet partitionerar dess fil uppsättning på alla noder. 
- En enskild kopierings aktivitet läser från och skriver till data lagret med hjälp av flera trådar. 
- ADF-kontroll flödet kan starta flera kopierings aktiviteter parallellt, till exempel använda [för varje slinga](./control-flow-for-each-activity.md). 

## <a name="resilience"></a>Återhämtning

I en enda kopierings aktivitet har ADF inbyggd återförsöks funktion så att den kan hantera en viss nivå av tillfälliga fel i data lager eller i det underliggande nätverket. 

När du gör en binär kopiering från S3 till blob och från S3 till ADLS Gen2, utför ADF automatiskt kontroll punkter.  Om körningen av kopierings aktiviteten har misslyckats eller nått tids gränsen, fortsätter kopian från den senaste fel punkten i stället för att börja från början. 

## <a name="network-security"></a>Nätverkssäkerhet 

Som standard överför ADF data från Amazon S3 till Azure Blob Storage eller Azure Data Lake Storage Gen2 med hjälp av krypterad anslutning över HTTPS-protokoll.  HTTPS tillhandahåller data kryptering vid överföring och förhindrar avlyssning och man-in-the-middle-attacker. 

Alternativt, om du inte vill att data ska överföras över offentliga Internet, kan du öka säkerheten genom att överföra data via en privat peering-länk mellan AWS Direct Connect och Azure Express Route.  Se lösnings arkitekturen nedan om hur detta kan uppnås. 

## <a name="solution-architecture"></a>Lösningsarkitektur

Migrera data över offentliga Internet:

![Diagrammet visar migrering över Internet med H T T P från en A W S S3-butik via Azure Integration Runtime i en D Azure-Azure Storage. Körningen har en kontroll kanal med Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- I den här arkitekturen överförs data säkert med HTTPS över offentligt Internet. 
- Både källan Amazon S3 och Azure-Blob Storage eller Azure Data Lake Storage Gen2 har kon figurer ATS för att tillåta trafik från alla IP-adresser i nätverket.  Se den andra arkitekturen nedan om hur du kan begränsa nätverks åtkomsten till ett speciellt IP-adressintervall. 
- Du kan enkelt skala upp mängden häst krafter på ett kostnads effektivt sätt för att utnyttja nätverks-och lagrings bandbredden så att du kan få bästa möjliga data flöde för din miljö. 
- Både första migreringen av ögonblicks bilder och delta data kan uppnås med den här arkitekturen. 

Migrera data över privat länk: 

![Diagrammet visar migrering över en privat peering-anslutning från ett W S S3-lager via lokal integration runtime på Azure virtuella datorer till V net service-slutpunkter till Azure Storage. Körningen har en kontroll kanal med Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- I den här arkitekturen görs datamigrering via en privat peering-länk mellan AWS Direct Connect och Azure Express Route, så att data aldrig passerar över offentliga Internet.  Den kräver användning av AWS VPC och Azure Virtual Network. 
- Du måste installera ADF-integration runtime med egen värd på en virtuell Windows-dator i ditt virtuella Azure-nätverk för att uppnå den här arkitekturen.  Du kan skala upp dina egna IR-VM: ar manuellt eller skala ut till flera virtuella datorer (upp till 4 noder) för att helt utnyttja nätverket och lagrings-IOPS/bandbredd. 
- Om det är acceptabelt att överföra data via HTTPS, men du vill låsa nätverks åtkomsten till källa S3 till ett särskilt IP-intervall, kan du anta en variant av den här arkitekturen genom att ta bort AWS VPC och ersätta privat länk med HTTPS.  Du vill behålla Azure Virtual och lokal IR på Azure VM så att du kan ha en statisk, offentligt dirigerad IP-adress för filtrerings ändamål. 
- Migreringen av både inledande ögonblicks data och delta data kan uppnås med den här arkitekturen. 

## <a name="implementation-best-practices"></a>Metod tips för implementering 

### <a name="authentication-and-credential-management"></a>Autentisering och hantering av autentiseringsuppgifter 

- Om du vill autentisera till Amazon S3-kontot måste du använda [åtkomst nyckel för IAM-kontot](./connector-amazon-simple-storage-service.md#linked-service-properties). 
- Flera typer av autentisering stöds för att ansluta till Azure Blob Storage.  Du rekommenderas att använda [hanterade identiteter för Azure-resurser](./connector-azure-blob-storage.md#managed-identity) : byggt ovanpå en automatiskt hanterad ADF-identifiering i Azure AD, där du kan konfigurera pipelines utan att ange autentiseringsuppgifter i den länkade tjänst definitionen.  Alternativt kan du autentisera till Azure Blob Storage med [tjänstens huvud namn](./connector-azure-blob-storage.md#service-principal-authentication), [signatur för delad åtkomst](./connector-azure-blob-storage.md#shared-access-signature-authentication)eller [lagrings konto nyckel](./connector-azure-blob-storage.md#account-key-authentication). 
- Flera autentiseringstyper stöds också för att ansluta till Azure Data Lake Storage Gen2.  Användning av [hanterade identiteter för Azure-resurser](./connector-azure-data-lake-storage.md#managed-identity) är starkt rekommenderat, även om [tjänstens huvud namn](./connector-azure-data-lake-storage.md#service-principal-authentication) eller [lagrings konto nyckel](./connector-azure-data-lake-storage.md#account-key-authentication) också kan användas. 
- Om du inte använder hanterade identiteter för Azure-resurser rekommenderar vi att du [sparar autentiseringsuppgifterna i Azure Key Vault](./store-credentials-in-key-vault.md) för att göra det enklare att hantera och rotera nycklar centralt utan att ändra ADF-länkade tjänster.  Detta är också en av de [rekommenderade metoderna för CI/CD](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Första migrering av ögonblicks bild data 

Data partition rekommenderas särskilt när du migrerar mer än 100 TB data.  För att partitionera data kan du använda "prefix"-inställningen för att filtrera mapparna och filerna i Amazon S3 efter namn och sedan kan varje jobb i ADF-kopieringen kopiera en partition i taget.  Du kan köra flera jobb för ADF-kopiering samtidigt för bättre data flöde. 

Om något av kopierings jobben Miss lyckas på grund av ett tillfälligt problem med nätverket eller data lagret kan du köra om det misslyckade kopierings jobbet för att läsa in den aktuella partitionen igen från AWS S3.  Alla andra kopierings jobb som läser in andra partitioner påverkas inte. 

### <a name="delta-data-migration"></a>Migrering av delta data 

Det vanligaste sättet att identifiera nya eller ändrade filer från AWS S3 är att använda tidspartitionerad namngivnings konvention – när dina data i AWS S3 har tidsdelats med Time-slice-information i fil-eller mappnamnet (till exempel/YYYY/MM/DD/file.csv) kan pipelinen enkelt identifiera vilka filer/mappar som ska kopieras stegvis. 

Alternativt, om dina data i AWS S3 inte är tidspartitionerade, kan ADF identifiera nya eller ändrade filer med deras LastModifiedDate.   Hur det fungerar är att ADF genomsöker alla filer från AWS S3 och bara kopierar den nya och uppdaterade filen vars senast ändrade tidstämpel är större än ett visst värde.  Tänk på att om du har ett stort antal filer i S3 kan den första fil genomsökningen ta lång tid, oavsett hur många filer som matchar filter villkoret.  I det här fallet rekommenderar vi att du först partitionerar data med samma prefix-inställning för inledande ögonblicks bild migrering, så att fil genomsökningen kan ske parallellt.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>För scenarier som kräver integration runtime med egen värd på den virtuella Azure-datorn 

Oavsett om du migrerar data via en privat länk eller om du vill tillåta ett speciellt IP-intervall i Amazon S3-brand väggen, måste du installera integration runtime med egen värd på en virtuell Windows-dator i Azure. 

- Den rekommenderade konfigurationen att börja med för varje virtuell Azure-dator är Standard_D32s_v3 med 32-vCPU och 128 GB-minne.  Du kan fortsätta att övervaka processor-och minnes användning för den virtuella IR-datorn under datamigreringen för att se om du behöver skala upp den virtuella datorn ytterligare för bättre prestanda eller skala ned den virtuella datorn för att spara pengar. 
- Du kan också skala ut genom att associera upp till 4 VM-noder med en enda egen IR.  Ett enda kopierings jobb som körs mot en egen värd-IR partitionerar automatiskt fil uppsättningen och utnyttjar alla VM-noder för att kopiera filerna parallellt.  För hög tillgänglighet rekommenderar vi att du börjar med 2 VM-noder för att undvika en enskild felpunkt under datamigreringen. 

### <a name="rate-limiting"></a>Frekvensbegränsning 

Vi rekommenderar att du utför en prestanda-POC med en representativ exempel data uppsättning, så att du kan fastställa lämplig partitionsstorlek. 

Börja med en enda partition och en enskild kopierings aktivitet med standardinställningen DIU.  Öka inställningen för DIU gradvis tills du når bandbredds gränsen för din nätverks eller IOPS/bandbredds gräns för data lager, eller så har du nått Max 256-DIU som tillåts för en enskild kopierings aktivitet. 

Därefter ökar du gradvis antalet samtidiga kopierings aktiviteter tills du når gränserna för din miljö. 

Om du stöter på fel som rapporteras av ADF Copy-aktivitet kan du antingen minska samtidighets-eller DIU-inställningen i ADF eller överväga att öka bandbredds-/IOPS-gränserna för nätverket och data lager.  

### <a name="estimating-price"></a>Uppskattar pris 

> [!NOTE]
> Detta är ett exempel på en hypotetisk prissättning.  Din faktiska prissättning beror på det faktiska data flödet i din miljö.

Tänk på följande pipeline som skapats för att migrera data från S3 till Azure Blob Storage: 

![Diagrammet visar en pipeline för att migrera data, med manuell utlösare som flödar till lookup, som flödar till en underordnad pipeline för varje partition som innehåller kopierings flödet till den lagrade proceduren. Utanför pipelinen flödar den lagrade proceduren till Azure SQL D B, som flödar för att söka efter och ett W S S3-flöden att kopiera, som flödar till Blob Storage.](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Låt oss anta följande: 

- Total data volym är 2 PB 
- Migrera data via HTTPS med den första lösnings arkitekturen 
- 2 PB är uppdelad i 1 K partitioner och varje kopia flyttas en partition 
- Varje kopia konfigureras med DIU = 256 och ger 1 Gbit/s data flöde 
- Förfallet concurrency är inställt på 2 och det sammanlagda data flödet är 2 Gbit/s 
- Totalt tar det 292 timmar att slutföra migreringen 

Här är det uppskattade priset baserat på ovanstående antaganden: 

![Skärm bild av en tabell visar ett uppskattat pris.](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Ytterligare referenser 
- [Amazon Simple Storage Service Connector](./connector-amazon-simple-storage-service.md)
- [Azure Blob Storage-anslutning](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2-anslutning](./connector-azure-data-lake-storage.md)
- [Prestanda justerings guide för kopierings aktivitet](./copy-activity-performance.md)
- [Skapa och konfigurera egen värd Integration Runtime](./create-self-hosted-integration-runtime.md)
- [Egen värd för integration runtime-tillgänglighet och skalbarhet](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Säkerhetsöverväganden vid dataflytt](./data-movement-security-considerations.md)
- [Spara autentiseringsuppgifter i Azure Key Vault](./store-credentials-in-key-vault.md)
- [Kopierings fil, stegvis baserat på tidspartitionerat fil namn](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Kopiera nya och ändrade filer baserat på LastModifiedDate](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Sidan med priser för ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Mall

Här är den [mall](solution-template-migration-s3-azure.md) som ska börja med för att migrera petabyte av data som består av hundratals miljoner filer från Amazon S3 till Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Nästa steg

- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)