---
title: Migrera data från en lokal Netezza-server till Azure
description: Använd Azure Data Factory för att migrera data från en lokal Netezza-server till Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: 36ca7b709e0ec945f1fb4a9a8b745d20e6a58fe1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367783"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Använd Azure Data Factory för att migrera data från en lokal Netezza-server till Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory ger en genomförd, robust och kostnads effektiv mekanism för att migrera data i stor skala från en lokal Netezza-server till ditt Azure Storage-konto eller Azure Synapse Analytics-databas. 

Den här artikeln innehåller följande information för data tekniker och utvecklare:

> [!div class="checklist"]
> * Prestanda 
> * Kopiera återhämtning
> * Nätverkssäkerhet
> * Hög nivå lösnings arkitektur 
> * Metod tips för implementering  

## <a name="performance"></a>Prestanda

Azure Data Factory erbjuder en server lös arkitektur som tillåter parallellitet på olika nivåer. Om du är utvecklare innebär det att du kan bygga pipelines för att fullt ut använda både nätverks-och databas bandbredd för att maximera data flödet för data förflyttning i din miljö.

![Prestanda diagram](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Föregående diagram kan tolkas på följande sätt:

- En enda kopierings aktivitet kan dra nytta av skalbara beräknings resurser. När du använder Azure Integration Runtime kan du ange [upp till 256 DIUs](./copy-activity-performance.md#data-integration-units) för varje kopierings aktivitet på ett Server lös sätt. Med en lokal integration Runtime (lokal IR) kan du manuellt skala upp datorn eller skala ut till flera datorer ([upp till fyra noder](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)) och en enda kopierings aktivitet distribuerar dess partition över alla noder. 

- En enskild kopierings aktivitet läser från och skriver till data lagret genom att använda flera trådar. 

- Azure Data Factory kontroll flöde kan starta flera kopierings aktiviteter parallellt. Den kan till exempel starta dem med hjälp av en [for each-loop](./control-flow-for-each-activity.md). 

Mer information finns i [guiden Kopiera aktivitets prestanda och skalbarhet](./copy-activity-performance.md).

## <a name="resilience"></a>Återhämtning

I en enda kopierings aktivitet körs Azure Data Factory en inbyggd försöks funktion som gör det möjligt att hantera en viss nivå av tillfälliga fel i data lager eller i det underliggande nätverket.

När du kopierar data mellan käll-och mottagar data lager med Azure Data Factory kopierings aktivitet kan du hantera inkompatibla rader på två sätt. Du kan antingen avbryta och utföra kopierings aktiviteten eller fortsätta att kopiera resten av data genom att hoppa över inkompatibla data rader. För att lära dig orsaken till felet kan du dessutom logga inkompatibla rader i Azure Blob Storage eller Azure Data Lake Store, korrigera data på data källan och sedan göra om kopierings aktiviteten.

## <a name="network-security"></a>Nätverkssäkerhet 

Som standard överför Azure Data Factory data från den lokala Netezza-servern till ett Azure Storage-konto eller Azure Synapse Analytics-databas med hjälp av en krypterad anslutning över Hypertext Transfer Protocol säker (HTTPS). HTTPS tillhandahåller data kryptering vid överföring och förhindrar avlyssning och man-in-the-middle-attacker.

Alternativt, om du inte vill att data ska överföras via det offentliga Internet, kan du få högre säkerhet genom att överföra data via en privat peering-länk via Azure Express Route. 

I nästa avsnitt beskrivs hur du uppnår högre säkerhet.

## <a name="solution-architecture"></a>Lösningsarkitektur

I det här avsnittet beskrivs två sätt att migrera dina data.

### <a name="migrate-data-over-the-public-internet"></a>Migrera data via det offentliga Internet

![Migrera data via det offentliga Internet](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Föregående diagram kan tolkas på följande sätt:

- I den här arkitekturen överför du data på ett säkert sätt med hjälp av HTTPS via det offentliga Internet.

- För att uppnå den här arkitekturen måste du installera Azure Data Factory integration Runtime (egen värd) på en Windows-dator bakom en företags brand vägg. Se till att integration runtime kan komma åt Netezza-servern direkt. För att fullständigt använda nätverket och data lagrar bandbredd för att kopiera data, kan du manuellt skala upp datorn eller skala ut till flera datorer.

- Genom att använda den här arkitekturen kan du migrera både inledande ögonblicks bild data och delta data.

### <a name="migrate-data-over-a-private-network"></a>Migrera data över ett privat nätverk 

![Migrera data över ett privat nätverk](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Föregående diagram kan tolkas på följande sätt:

- I den här arkitekturen migrerar du data via en privat peering-länk via Azure Express Route, och data passerar aldrig över det offentliga Internet. 

- För att uppnå den här arkitekturen måste du installera Azure Data Factory integration Runtime (lokal installation) på en virtuell Windows-dator (VM) i det virtuella Azure-nätverket. För att fullständigt använda nätverket och data lagrar bandbredd för att kopiera data kan du skala upp den virtuella datorn manuellt eller skala ut till flera virtuella datorer.

- Genom att använda den här arkitekturen kan du migrera både inledande ögonblicks bild data och delta data.

## <a name="implement-best-practices"></a>Implementera metod tips 

### <a name="manage-authentication-and-credentials"></a>Hantera autentisering och autentiseringsuppgifter 

- Du kan använda [ODBC-autentisering via anslutnings sträng](./connector-netezza.md#linked-service-properties)för att autentisera till Netezza. 

- För att autentisera till Azure Blob Storage: 

   - Vi rekommenderar starkt att du använder [hanterade identiteter för Azure-resurser](./connector-azure-blob-storage.md#managed-identity). Med hanterade identiteter som byggts ovanpå en automatiskt hanterad Azure Data Factory identitet i Azure Active Directory (Azure AD) kan du konfigurera pipelines utan att behöva ange autentiseringsuppgifter i den länkade tjänst definitionen.  

   - Alternativt kan du autentisera till Azure Blob Storage med hjälp av [tjänstens huvud namn](./connector-azure-blob-storage.md#service-principal-authentication), en [signatur för delad åtkomst](./connector-azure-blob-storage.md#shared-access-signature-authentication)eller en [lagrings konto nyckel](./connector-azure-blob-storage.md#account-key-authentication). 

- För att autentisera till Azure Data Lake Storage Gen2: 

   - Vi rekommenderar starkt att du använder [hanterade identiteter för Azure-resurser](./connector-azure-data-lake-storage.md#managed-identity).
   
   - Du kan också använda [tjänstens huvud namn](./connector-azure-data-lake-storage.md#service-principal-authentication) eller en [lagrings konto nyckel](./connector-azure-data-lake-storage.md#account-key-authentication). 

- För att autentisera till Azure Synapse Analytics:

   - Vi rekommenderar starkt att du använder [hanterade identiteter för Azure-resurser](./connector-azure-sql-data-warehouse.md#managed-identity).
   
   - Du kan också använda [tjänstens huvud namn](./connector-azure-sql-data-warehouse.md#service-principal-authentication) eller [SQL-autentisering](./connector-azure-sql-data-warehouse.md#sql-authentication).

- När du inte använder hanterade identiteter för Azure-resurser rekommenderar vi starkt att du [lagrar autentiseringsuppgifterna i Azure Key Vault](./store-credentials-in-key-vault.md) för att göra det lättare att hantera och rotera nycklar centralt utan att behöva ändra Azure Data Factory länkade tjänster. Detta är också en av de [rekommenderade metoderna för CI/CD](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="migrate-initial-snapshot-data"></a>Migrera inledande ögonblicks bild data 

För små tabeller (det vill säga tabeller med en volym på mindre än 100 GB eller som kan migreras till Azure inom två timmar) kan du göra varje belastnings data för kopierings jobb per tabell. För större data flöde kan du köra flera Azure Data Factory kopierings jobb för att läsa in separata tabeller samtidigt. 

I varje kopierings jobb, för att köra parallella frågor och kopiera data efter partitioner, kan du också komma åt en nivå av parallellitet genom att använda [ `parallelCopies` egenskaps inställningen](./copy-activity-performance.md#parallel-copy) med något av följande alternativ för datapartition:

- För att få bättre effektivitet rekommenderar vi att du börjar med en data sektor.  Kontrol lera att värdet i `parallelCopies` inställningen är mindre än det totala antalet data segment partitioner i tabellen på Netezza-servern.  

- Om volymen för varje data sektor partition fortfarande är stor (till exempel 10 GB eller mer) rekommenderar vi att du växlar till en partition med dynamiskt intervall. Det här alternativet ger dig större flexibilitet för att definiera antalet partitioner och volymen för varje partition efter partitionstabell, övre och nedre gränser.

För större tabeller (det vill säga tabeller med en volym på 100 GB eller högre eller som *inte kan* migreras till Azure inom två timmar) rekommenderar vi att du partitionerar data efter anpassad fråga och gör sedan varje kopierings jobb kopia på en partition i taget. För bättre data flöde kan du köra flera Azure Data Factory kopierings jobb samtidigt. För varje kopierings jobb mål för inläsning av en partition efter anpassad fråga kan du öka data flödet genom att aktivera parallellitet via antingen data sektorn eller det dynamiska intervallet. 

Om ett kopierings jobb Miss lyckas på grund av ett tillfälligt problem med nätverket eller data lagret kan du köra om det misslyckade kopierings jobbet för att läsa in den aktuella partitionen från tabellen igen. Andra kopierings jobb som läser in andra partitioner påverkas inte.

När du läser in data i en Azure Synapse Analytics-databas rekommenderar vi att du aktiverar PolyBase i kopierings jobbet med Azure Blob Storage som mellanlagring.

### <a name="migrate-delta-data"></a>Migrera delta data 

Om du vill identifiera nya eller uppdaterade rader från tabellen använder du en tidsstämpelkolumn eller en stegvis öknings nyckel i schemat. Sedan kan du lagra det senaste värdet som en övre vatten märke i en extern tabell och sedan använda det för att filtrera delta data nästa gången du läser in data. 

Varje tabell kan använda en annan vattenstämpel-kolumn för att identifiera nya eller uppdaterade rader. Vi rekommenderar att du skapar en extern kontroll tabell. I tabellen representerar varje rad en tabell på Netezza-servern med dess angivna storlek för vatten märkes kolumnen och värdet för hög vatten märket. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Konfigurera en integration runtime med egen värd

Om du migrerar data från Netezza-servern till Azure, oavsett om servern är lokal bakom din företags brand vägg eller i en virtuell nätverks miljö, måste du installera en lokal IR på en Windows-dator eller virtuell dator, som är den motor som används för att flytta data. När du installerar den egna IR-lösningen rekommenderar vi följande:

- Börja med en konfiguration på 32 vCPU och 128 GB minne för varje Windows-dator eller virtuell dator. Du kan fortsätta att övervaka processor-och minnes användningen för IR-datorn under datamigreringen för att se om du behöver skala upp datorn ytterligare för att få bättre prestanda eller skala ned datorn för att spara pengar.

- Du kan också skala ut genom att associera upp till fyra noder med en enda egen IR. Ett enda kopierings jobb som körs mot en lokal IR-post tillämpar automatiskt alla VM-noder för att kopiera data parallellt. För hög tillgänglighet börjar du med fyra VM-noder för att undvika en enskild felpunkt under datamigreringen.

### <a name="limit-your-partitions"></a>Begränsa dina partitioner

Bästa praxis är att utföra ett POC-värde (proof of Concept) med en representativ exempel data uppsättning, så att du kan fastställa lämplig partitionsstorlek för varje kopierings aktivitet. Vi rekommenderar att du läser in varje partition till Azure inom två timmar.  

Om du vill kopiera en tabell börjar du med en enda kopierings aktivitet med en enda IR-dator med egen värd. Öka inställningen gradvis `parallelCopies` baserat på antalet data segment partitioner i tabellen. Se om hela tabellen kan läsas in i Azure inom två timmar, enligt det data flöde som är resultatet från kopierings jobbet. 

Om det inte går att läsa in till Azure inom två timmar och kapaciteten för IR-noden med egen värd och data lagret inte används fullt ut, ökar du gradvis antalet samtidiga kopierings aktiviteter tills du når gränsen för ditt nätverk eller bandbredden för data lager. 

Fortsätt att övervaka processor-och minnes användningen på den lokala IR-datorn och var redo att skala upp datorn eller skala ut till flera datorer när du ser att CPU och minne används fullt ut. 

Om du stöter på fel, som rapporteras av Azure Data Factory kopierings aktivitet, minskar du samtidigheten eller `parallelCopies` inställningen i Azure Data Factory, eller så kan du överväga att öka bandbredden eller i/O-åtgärder per sekund (IOPS) för nätverket och data lager. 


### <a name="estimate-your-pricing"></a>Beräkna din prissättning 

Tänk på följande pipeline, som är konstruerad för att migrera data från den lokala Netezza-servern till en Azure Synapse Analytics-databas:

![Prissättnings pipelinen](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Vi antar att följande påståenden är sanna: 

- Den totala data volymen är 50 terabyte (TB). 

- Vi migrerar data med hjälp av den första lösnings arkitekturen (Netezza-servern är lokal, bakom brand väggen).

- Volymen på 50 TB är uppdelad i 500 partitioner och varje kopierings aktivitet flyttar en partition.

- Varje kopierings aktivitet konfigureras med en lokal IR-överföring mot fyra datorer och ger ett data flöde med 20 megabyte per sekund (Mbit/s). (Inom kopierings aktiviteten `parallelCopies` är inställt på 4 och varje tråd för att läsa in data från tabellen uppnår ett data flöde på 5 Mbit/s.)

- Förväntad concurrency är inställd på 3 och det sammanlagda data flödet är 60 Mbit/s.

- Totalt tar det 243 timmar att slutföra migreringen.

Här är det uppskattade priset baserat på föregående antaganden: 

![Pris tabellen](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Priserna som visas i föregående tabell är hypotetiska. Din faktiska prissättning beror på det faktiska data flödet i din miljö. Priset för Windows-datorn (med den egna IR-installationen) ingår inte. 

### <a name="additional-references"></a>Ytterligare referenser

Mer information finns i följande artiklar och guider:

- [Migrera data från en lokal databas för Relations informations lager till Azure med hjälp av Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza-anslutning](./connector-netezza.md)
- [ODBC-anslutningsapp](./connector-odbc.md)
- [Azure Blob Storage-anslutning](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2-anslutning](./connector-azure-data-lake-storage.md)
- [Azure Synapse Analytics-anslutning](./connector-azure-sql-data-warehouse.md)
- [Prestanda justerings guide för kopierings aktivitet](./copy-activity-performance.md)
- [Skapa och konfigurera lokalt installerad integrationskörning](./create-self-hosted-integration-runtime.md)
- [Egen värd för integration runtime-tillgänglighet och skalbarhet](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Säkerhetsöverväganden vid dataflytt](./data-movement-security-considerations.md)
- [Spara autentiseringsuppgifter i Azure Key Vault](./store-credentials-in-key-vault.md)
- [Kopiera data stegvis från en tabell](./tutorial-incremental-copy-portal.md)
- [Kopiera data stegvis från flera tabeller](./tutorial-incremental-copy-multiple-tables-portal.md)
- [Sidan Azure Data Factory prissättning](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Nästa steg

- [Kopiera filer från flera behållare med hjälp av Azure Data Factory](solution-template-copy-files-multiple-containers.md)