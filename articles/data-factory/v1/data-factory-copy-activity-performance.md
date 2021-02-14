---
title: Guide till prestandajustering för kopieringsaktiviteter
description: Lär dig mer om viktiga faktorer som påverkar prestanda för data förflyttning i Azure Data Factory när du använder kopierings aktivitet.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9a890719de39a71d8336d39f9932e73f7baccf87
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377218"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guide till prestandajustering för kopieringsaktiviteter

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-copy-activity-performance.md)
> * [Version 2 (aktuell version)](../copy-activity-performance.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se prestanda- [och justerings guiden för kopierings aktiviteter för Data Factory](../copy-activity-performance.md).

Azure Data Factory kopierings aktivitet ger en första klass säker, tillförlitlig och högpresterande data inläsnings lösning. Det gör att du kan kopiera flera terabyte data varje dag i flera olika moln-och lokala data lager. Blixt snabba data inläsnings prestanda är en nyckel för att säkerställa att du kan fokusera på de grundläggande problemen med Big data: skapa avancerade analys lösningar och få djupgående insikter från alla dessa data.

Azure tillhandahåller en uppsättning data lagrings-och informations lager lösningar i företags klass, och kopierings aktiviteten ger en mycket optimerad data inläsnings upplevelse som är enkel att konfigurera och konfigurera. Med bara en enda kopierings aktivitet kan du uppnå:

* Läser in data i **Azure Synapse Analytics** med **1,2 Gbit/s**. För en genom gång med ett användnings fall läser du [läsa in 1 TB i Azure Synapse Analytics under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Läsa in data i **Azure Blob Storage** med **1,0 Gbit/s**
* Läser in data  i Azure Data Lake Store **1,0 Gbit/s**

I den här artikeln beskrivs:

* [Prestanda referens nummer](#performance-reference) för käll-och mottagar data lager som stöds för att hjälpa dig att planera ditt projekt.
* Funktioner som kan förbättra kopierings flödet i olika scenarier, inklusive [enheter för moln data flytt](#cloud-data-movement-units), [parallell kopiering](#parallel-copy)och [mellanlagrad kopiering](#staged-copy).
* [Vägledning för prestanda justering](#performance-tuning-steps) om hur du finjusterar prestanda och viktiga faktorer som kan påverka kopierings prestanda.

> [!NOTE]
> Om du inte är bekant med kopierings aktiviteten i allmänhet, se [Flytta data med hjälp av kopierings aktivitet](data-factory-data-movement-activities.md) innan du läser den här artikeln.
>

## <a name="performance-reference"></a>Prestanda referens

Som referens visas det kopierade data flödes numret i MBps i Mbit/s för den aktuella käll-och mottagar paret, baserat på intern testning. För jämförelse visar det också hur olika inställningar för [moln data förflyttnings enheter](#cloud-data-movement-units) eller [Data Management Gateway skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md) (flera gateway-noder) kan hjälpa till med kopierings prestanda.

![Prestanda mat ris](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>I Azure Data Factory version 1 är de minsta moln data flytt enheterna för moln-till-moln-kopia två. Om inget värde anges, se standard enheter för data förflyttning som används i [enheter för moln data flytt](#cloud-data-movement-units).

**Poäng till Anmärkning:**
* Data flödet beräknas med hjälp av följande formel: [storlek på data som läses från källa]/[varaktighet för kopierings aktivitets körning].
* Prestanda referens numren i tabellen mättes med hjälp av [TPC-H-](http://www.tpc.org/tpch/) data uppsättning i en enda kopierings aktivitet.
* I Azure data lager finns källan och mottagaren i samma Azure-region.
* För Hybrid kopiering mellan lokala och molnbaserade data lager, kördes varje gateway-nod på en dator som var skild från det lokala data lagret med specifikationen nedan. När en enskild aktivitet kördes på Gateway, förbrukade kopierings åtgärden bara en liten del av test datorns processor, minne eller nätverks bandbredd. Lär dig mer [om att tänka på Data Management Gateway](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>Processor</td>
        <td>32 kärnor 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Minne</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Nätverk</td>
        <td>Internet gränssnitt: 10 Gbit/s; intranäts gränssnitt: 40 Gbit/s</td>
    </tr>
    </table>


> [!TIP]
> Du kan få högre genomflöde genom att använda fler data rörelse enheter (DMUs) än den högsta standard DMUs, som är 32 för en kopierings aktivitet från moln till molnet. Med 100 DMUs kan du till exempel få kopiera data från Azure blob till Azure Data Lake Store vid **1,0 Gbit/s**. Mer information om den här funktionen och det scenario som stöds finns i avsnittet [Cloud data transport enheter](#cloud-data-movement-units) . Kontakta [Azure-supporten](https://azure.microsoft.com/support/) om du vill begära fler DMUs.

## <a name="parallel-copy"></a>Parallell kopia
Du kan läsa data från källan eller skriva data till målet **parallellt i en kopierings aktivitets körning**. Den här funktionen förbättrar data flödet för en kopierings åtgärd och minskar den tid det tar att flytta data.

Den här inställningen skiljer sig från egenskapen **concurrency** i aktivitets definitionen. Egenskapen **concurrency** bestämmer antalet **samtidiga kopierings aktiviteter som körs** för att bearbeta data från olika aktivitets fönster (1 till 2 fm, 2 am till 3 am, 3, och så vidare). Den här funktionen är användbar när du utför en historisk belastning. Funktionen för parallell kopiering gäller för en **enda aktivitets körning**.

Nu ska vi titta på ett exempel scenario. I följande exempel måste flera sektorer från det förflutna bearbetas. Data Factory kör en instans av kopierings aktiviteten (en aktivitets körning) för varje sektor:

* Data sektorn från det första aktivitets fönstret (1 till 2 AM) = => aktivitet kör 1
* Data sektorn från det andra aktivitets fönstret (mellan 2 och 3 AM) = => aktivitet kör 2
* Data sektorn från det andra aktivitets fönstret (3 till 4 AM) = => aktivitet kör 3

Och så vidare.

I det här exemplet, när **samtidighets** värdet är inställt på 2, körs **aktivitet 1** och **aktivitet kör 2** kopiera data från två aktivitets fönster **samtidigt** för att förbättra data flyttens prestanda. Men om flera filer är associerade med aktivitet kör 1, kopierar tjänsten för data flyttning filer från källan till en fil i taget.

### <a name="cloud-data-movement-units"></a>Enheter för flytt av moln data
En **moln data förflyttnings enhet (DMU)** är ett mått som representerar styrkan (en kombination av processor, minne och tilldelning av nätverks resurser) för en enskild enhet i Data Factory. DMU kan användas för kopiering av moln till molnet, men inte i en hybrid kopia.

**De minimala moln data flytt enheterna för att kunna köra kopierings aktiviteter är två.** Om detta inte anges visas standard-DMUs som används i olika kopierings scenarier i följande tabell:

| Kopierings scenario | Standard DMUs som fastställs av tjänsten |
|:--- |:--- |
| Kopiera data mellan filbaserade Arkiv | Mellan 4 och 16 beroende på filernas antal och storlek. |
| Alla andra kopierings scenarier | 4 |

Om du vill åsidosätta standardvärdet anger du ett värde för egenskapen **cloudDataMovementUnits** enligt följande. De **tillåtna värdena** för egenskapen **cloudDataMovementUnits** är 2, 4, 8, 16, 32. Det **faktiska antalet moln DMUs** som kopierings åtgärden använder vid körningen är lika med eller mindre än det konfigurerade värdet, beroende på ditt data mönster. Information om den prestanda nivå som du kan få när du konfigurerar fler enheter för en bestämd kopierings källa och mottagare finns i [prestanda referensen](#performance-reference).

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Kontakta [Azure-supporten](https://azure.microsoft.com/support/)om du behöver fler moln DMUs för ett högre data flöde. Inställningen av 8 och senare fungerar för närvarande bara när du **kopierar flera filer från Blob Storage/data Lake Store/Amazon S3/Cloud FTP/Cloud SFTP till Blob Storage/data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Du kan använda egenskapen **parallelCopies** för att ange vilken parallellitet som ska användas för kopierings aktiviteten. Du kan tänka på den här egenskapen som det maximala antalet trådar i kopierings aktiviteten som kan läsa från din källa eller skriva till dina mottagar data lager parallellt.

För varje kopierings aktivitets körning fastställer Data Factory antalet parallella kopior som ska användas för att kopiera data från käll data lagret och till mål data lagret. Standard antalet parallella kopior som används beror på vilken typ av källa och mottagare du använder.

| Källa och mottagare | Standard antal parallella kopior som bestäms av tjänsten |
| --- | --- |
| Kopiera data mellan filbaserade arkiv (Blob Storage; Data Lake Store; Amazon S3; ett lokalt fil system; en lokal HDFS) |Mellan 1 och 32. Beror på storleken på filerna och antalet moln data förflyttnings enheter (DMUs) som används för att kopiera data mellan två moln data lager eller den fysiska konfigurationen för den Gateway-dator som används för en hybrid kopia (för att kopiera data till eller från ett lokalt data lager). |
| Kopiera data från **ett käll data lager till Azure Table Storage** |4 |
| Alla andra käll-och mottagar par |1 |

Normalt bör standard beteendet ge dig det bästa data flödet. Men för att kontrol lera belastningen på datorer som är värdar för dina data lager, eller för att justera kopierings prestanda, kan du välja att åsidosätta standardvärdet och ange ett värde för egenskapen **parallelCopies** . Värdet måste vara mellan 1 och 32 (båda). Vid körning för bästa prestanda använder kopierings aktiviteten ett värde som är mindre än eller lika med det värde som du anger.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Poäng till Anmärkning:

* När du kopierar data mellan filbaserade butiker bestämmer **parallelCopies** parallellt på fil nivå. Delningen i en enskild fil sker under automatiskt och transparent, och den är utformad för att använda den bästa lämpliga segment storleken för en viss käll data lager typ för att läsa in data parallellt och rätvinkligt till parallelCopies. Det faktiska antalet parallella kopieringar som används för kopierings åtgärden vid körning är inte större än antalet filer som du har. Om kopierings beteendet är **mergeFile** kan inte kopierings aktiviteten dra nytta av Parallel på filnivå.
* När du anger ett värde för egenskapen **parallelCopies** bör du ta hänsyn till belastnings ökningen på käll-och mottagar data lager och till gateway om det är en hybrid kopia. Detta sker särskilt om du har flera aktiviteter eller samtidiga körningar av samma aktiviteter som körs mot samma data lager. Om du ser att data lagret eller gatewayen är överbelasta med belastningen minskar du **parallelCopies** -värdet för att minska belastningen.
* När du kopierar data från butiker som inte är filbaserade till arkiv som är filbaserade ignorerar tjänsten för data förflyttning **parallelCopies** -egenskapen. Även om parallelitet har angetts används det inte i det här fallet.

> [!NOTE]
> Du måste använda Data Management Gateway version 1,11 eller senare för att använda funktionen **parallelCopies** när du gör en hybrid kopia.
>
>

För att bättre använda de här två egenskaperna och för att förbättra data flödet, se exempel användnings fall. Du behöver inte konfigurera **parallelCopies** för att dra nytta av standard beteendet. Om du konfigurerar och **parallelCopies** är för liten används kanske inte flera moln DMUs fullt ut.

### <a name="billing-impact"></a>Fakturerings påverkan
Det är **viktigt** att komma ihåg att du debiteras utifrån den totala tiden för kopierings åtgärden. Om ett kopierings jobb som används för att ta en timme med en molnen het och nu tar 15 minuter med fyra moln enheter, förblir den övergripande fakturan nästan samma. Du kan till exempel använda fyra moln enheter. Den första moln enheten tillbringar 10 minuter, den andra, 10 minuter, den tredje, 5 minuter och den fjärde, 5 minuter, allt i en kopierings aktivitet. Du debiteras för den totala kopierings tiden (data förflyttnings tiden), som är 10 + 10 + 5 + 5 = 30 minuter. Att använda **parallelCopies** påverkar inte faktureringen.

## <a name="staged-copy"></a>Mellanlagrad kopia
När du kopierar data från ett käll data lager till ett data lager för mottagare kan du välja att använda Blob Storage som ett interimistiskt mellanlagrings lager. Mellanlagring är särskilt användbart i följande fall:

1. **Du vill mata in data från olika data lager i Azure Synapse Analytics via PolyBase**. Azure Synapse Analytics använder PolyBase som en mekanism för hög genom strömning för att läsa in en stor mängd data i Azure Synapse Analytics. Källdata måste dock vara i Blob Storage och måste uppfylla ytterligare kriterier. När du läser in data från ett annat data lager än Blob Storage, kan du aktivera data kopiering via tillfällig mellanlagring av blob-lagring. I så fall utför Data Factory nödvändiga data transformationer för att säkerställa att de uppfyller kraven för PolyBase. Sedan använder den PolyBase för att läsa in data i Azure Synapse Analytics. Mer information finns i [använda PolyBase för att läsa in data i Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics). För en genom gång med ett användnings fall läser du [läsa in 1 TB i Azure Synapse Analytics under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Ibland tar det en stund att utföra en hybrid data förflyttning (det vill säga kopiera mellan ett lokalt data lager och ett moln data lager) över en långsam nätverks anslutning**. Du kan förbättra prestanda genom att komprimera data lokalt så att det tar mindre tid att flytta data till lagrings data lagret i molnet. Sedan kan du expandera data i mellanlagrings platsen innan du läser in den i mål data lagret.
3. **Du vill inte öppna andra portar än port 80 och port 443 i brand väggen, på grund av företagets IT-principer**. När du t. ex. kopierar data från ett lokalt data lager till en Azure SQL Database mottagare eller en Azure Synapse Analytics-mottagare måste du aktivera utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företags brand väggen. I det här scenariot kan du dra nytta av gatewayen för att först kopiera data till en mellanlagringsplats för Blob Storage via HTTP eller HTTPS på port 443. Läs sedan in data i SQL Database eller Azure Synapse Analytics från mellanlagring av blob-lagring. I det här flödet behöver du inte aktivera port 1433.

### <a name="how-staged-copy-works"></a>Så här fungerar mellanlagrad kopiering
När du aktiverar mellanlagrings funktionen kopieras först data från käll data lagret till lagrings data lagret (ta med din egen). Därefter kopieras data från mellanlagrings data lagringen till data lagret för mottagaren. Data Factory hanterar automatiskt flödet i två steg åt dig. Data Factory rensar också tillfälliga data från mellanlagringen när data förflyttningen är klar.

I moln kopierings scenariot (både käll-och mottagar data lager finns i molnet) används inte gatewayen. Tjänsten Data Factory utför kopierings åtgärderna.

![Mellanlagrad kopiering: moln scenario](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

I hybrid kopierings scenariot (källan är lokalt och mottagaren är i molnet) flyttar gatewayen data från käll data lagringen till ett mellanlagrat data lager. Data Factory tjänst flyttar data från lagrings data lagret till data lagret för mottagare. Att kopiera data från ett moln data lager till ett lokalt data lager via mellanlagring, stöds också med det omvända flödet.

![Mellanlagrad kopiering: hybrid scenario](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

När du aktiverar data förflyttning med hjälp av ett mellanlagrings lager kan du ange om du vill att data ska komprimeras innan data flyttas från käll data lagret till ett interimistiskt eller mellanlagrat data lager och sedan expanderas innan data flyttas från ett interimistiskt eller mellanlagrat data lager till data lagret för mottagare.

För närvarande kan du inte kopiera data mellan två lokala data lager med hjälp av ett mellanlagrings lager. Vi förväntar dig att det här alternativet ska vara tillgängligt snart.

### <a name="configuration"></a>Konfiguration
Konfigurera **enableStaging** -inställningen i kopierings aktivitet för att ange om du vill att data ska mellanlagras i Blob Storage innan du läser in dem i ett mål data lager. När du anger **enableStaging** till true anger du ytterligare egenskaper som anges i nästa tabell. Om du inte har en sådan måste du också skapa en signatur för delad åtkomst för en Azure Storage eller lagrings plats för delad åtkomst för mellanlagring.

| Egenskap | Beskrivning | Standardvärde | Obligatorisk |
| --- | --- | --- | --- |
| **enableStaging** |Ange om du vill kopiera data via ett interimistiskt lagrings lager. |Falskt |Inga |
| **linkedServiceName** |Ange namnet på en länkad [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) -eller [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) -tjänst som refererar till den lagrings instans som du använder som ett interimistiskt mellanlagrings lager. <br/><br/> Du kan inte använda Storage med en signatur för delad åtkomst för att läsa in data i Azure Synapse Analytics via PolyBase. Du kan använda den i alla andra scenarier. |Ej tillämpligt |Ja, när **enableStaging** är inställt på True |
| **sökväg** |Ange den Blob Storage-sökväg som du vill ska innehålla de mellanlagrade data. Om du inte anger en sökväg, skapar tjänsten en behållare för att lagra temporära data. <br/><br/> Ange endast en sökväg om du använder lagring med en signatur för delad åtkomst, eller om du vill att tillfälliga data ska finnas på en bestämd plats. |Ej tillämpligt |Inga |
| **enableCompression** |Anger om data ska komprimeras innan de kopieras till målet. Den här inställningen minskar mängden data som överförs. |Falskt |Inga |

Här är en exempel definition av kopierings aktiviteten med de egenskaper som beskrivs i föregående tabell:

```json
"activities":[
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Fakturerings påverkan
Du debiteras baserat på två steg: kopiera varaktighet och kopierings typ.

* När du använder mellanlagring under en moln kopia (kopiera data från ett moln data lager till ett annat moln data lager) debiteras du [summan av kopierings tiden för steg 1 och steg 2] x [Cloud Copy Unit Price].
* När du använder mellanlagring under en hybrid kopiering (kopiering av data från ett lokalt data lager till ett moln data lager) debiteras du för [hybrid kopieringens varaktighet] x [hybrid kopiera enhets pris] + [moln kopieringens varaktighet] x [Cloud Copy Unit Price].

## <a name="performance-tuning-steps"></a>Prestanda justerings steg
Vi rekommenderar att du vidtar de här stegen för att justera prestandan för din Data Factory-tjänst med kopierings aktivitet:

1. **Upprätta en bas linje**. Under utvecklings fasen kan du testa din pipeline genom att använda kopierings aktivitet mot ett representativt data exempel. Du kan använda Data Factory [segmenterings modell](data-factory-scheduling-and-execution.md) för att begränsa den mängd data som du arbetar med.

   Samla in körnings tid och prestanda egenskaper med hjälp av **appen övervakning och hantering**. Välj **övervaka & hantera** på din Data Factory start sida. I trädvyn väljer du **data uppsättningen för utdata**. I listan **aktivitets fönster** väljer du kopierings aktiviteten Kör. **Aktivitets fönster** visar varaktigheten för kopierings aktiviteten och storleken på de data som kopieras. Data flödet visas i **Utforskaren i aktivitets fönstret**. Mer information om appen finns i [övervaka och hantera Azure Data Factory pipelines med hjälp av appen övervakning och hantering](data-factory-monitor-manage-app.md).

   ![Aktivitetskörningsinformation](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Senare i artikeln kan du jämföra prestanda och konfiguration för ditt scenario för att kopiera aktivitetens [prestanda referens](#performance-reference) från våra tester.
2. **Diagnostisera och optimera prestanda**. Om den prestanda du anser inte uppfyller dina förväntningar måste du identifiera Flask halsar i prestanda. Optimera sedan prestanda för att ta bort eller minska antalet Flask halsar. En fullständig beskrivning av prestanda diagnosen ligger utanför omfånget för den här artikeln, men här är några vanliga överväganden:

   * Prestanda funktioner:
     * [Parallell kopia](#parallel-copy)
     * [Enheter för flytt av moln data](#cloud-data-movement-units)
     * [Mellanlagrad kopia](#staged-copy)
     * [Data Management Gateway skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Gateway för datahantering](#considerations-for-data-management-gateway)
   * [Källa](#considerations-for-the-source)
   * [Kanalmottagare](#considerations-for-the-sink)
   * [Serialisering och deserialisering](#considerations-for-serialization-and-deserialization)
   * [Komprimering](#considerations-for-compression)
   * [Kolumnmappning](#considerations-for-column-mapping)
   * [Ytterligare överväganden](#other-considerations)
3. **Expandera konfigurationen till hela data uppsättningen**. När du är nöjd med körnings resultatet och prestandan kan du expandera definitionen och den aktiva pipeline-perioden så att hela data uppsättningen täcker hela din data uppsättning.

## <a name="considerations-for-data-management-gateway"></a>Överväganden för Data Management Gateway
**Gateway-konfiguration**: Vi rekommenderar att du använder en dedikerad dator som värd för data Management Gateway. Se [överväganden vid användning av data Management Gateway](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Gateway-övervakning och skalbarhet**: en enda logisk Gateway med en eller flera gateway-noder kan betjäna flera kopierings aktiviteter samtidigt samtidigt. Du kan visa en ögonblicks bild av resursutnyttjande i real tid (CPU, minne, nätverk (in/ut) osv.) på en Gateway-dator samt antalet samtidiga jobb som körs mot gränsen i Azure Portal. mer information finns i [övervaka gateway i portalen](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Om du har hög behov av hybrid data förflyttning antingen med ett stort antal samtidiga kopierings aktiviteter körs eller med stor mängd data som ska kopieras, bör du överväga att [skala upp eller skala ut gatewayen](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) så att du bättre kan använda din resurs eller för att etablera mer resurs för att ge en kopia.

## <a name="considerations-for-the-source"></a>Överväganden för källan
### <a name="general"></a>Allmänt
Se till att det underliggande data lagret inte är överbelastat av andra arbets belastningar som körs på eller mot det.

Information om Microsoft-datalager finns i avsnittet om [övervakning och justering](#performance-reference) som är specifika för data lager och hjälper dig att förstå prestanda egenskaper för data lager, minimera svars tider och maximera data flödet.

Om du kopierar data från Blob Storage till Azure Synapse Analytics bör du överväga att använda **PolyBase** för att öka prestandan. Se [använda PolyBase för att läsa in data i Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) för mer information. För en genom gång med ett användnings fall läser du [läsa in 1 TB i Azure Synapse Analytics under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Filbaserade data lager
*(Inkluderar Blob Storage, Data Lake Store, Amazon S3, lokala fil system och lokala HDFS)*

* **Genomsnittlig fil storlek och antal filer**: kopierings aktivitet överför data en fil i taget. Med samma mängd data som ska flyttas är det totala data flödet lägre om data består av många små filer i stället för några stora filer på grund av start fasen för varje fil. Därför bör du, om möjligt, kombinera små filer till större filer för att få större data flöde.
* **Fil format och komprimering**: Mer information om hur du kan förbättra prestanda finns i [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimerings](#considerations-for-compression) avsnitt.
* I det **lokala fil system** scenariot, där **Data Management Gateway** krävs, se avsnittet [överväganden för data Management Gateway](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Relations data lager
*(Innehåller SQL Database; Azure Synapse Analytics; Amazon-RedShift; SQL Server-databaser; och Oracle-, MySQL-, DB2-, Teradata-, Sybase-och PostgreSQL-databaser osv.)*

* **Data mönster**: tabellens schema påverkar kopiering av data flöde. En stor rad storlek ger bättre prestanda än liten rad storlek, för att kopiera samma mängd data. Orsaken är att databasen kan få mer effektiv hämtning av färre batchar med data som innehåller färre rader.
* **Fråga eller lagrad procedur**: optimera logiken för frågan eller den lagrade proceduren som du anger i kopierings aktivitets källan för att hämta data mer effektivt.
* Information om **lokala Relations databaser**, till exempel SQL Server och Oracle, som kräver användning av **Data Management Gateway**, finns i avsnittet överväganden för data Management Gateway.

## <a name="considerations-for-the-sink"></a>Överväganden för mottagaren
### <a name="general"></a>Allmänt
Se till att det underliggande data lagret inte är överbelastat av andra arbets belastningar som körs på eller mot det.

Information om Microsoft-datalager finns i [avsnittet om övervakning och justering](#performance-reference) som är specifika för data lager. Dessa avsnitt kan hjälpa dig att förstå prestanda egenskaperna för data lagring och hur du minimerar svars tiderna och maximerar data flödet.

Om du kopierar data från **Blob Storage** till **Azure Synapse Analytics** kan du använda **PolyBase** för att öka prestandan. Se [använda PolyBase för att läsa in data i Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) för mer information. För en genom gång med ett användnings fall läser du [läsa in 1 TB i Azure Synapse Analytics under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Filbaserade data lager
*(Inkluderar Blob Storage, Data Lake Store, Amazon S3, lokala fil system och lokala HDFS)*

* **Kopierings beteende**: om du kopierar data från ett annat filbaserat data lager, har kopierings aktiviteten tre alternativ via egenskapen **copyBehavior** . Den bevarar hierarkin, fören klar hierarkin eller sammanfogar filer. Att bevara eller förenkla hierarkin har små eller inga prestanda kostnader, men att slå samman filer medför ökade prestanda.
* **Fil format och komprimering**: Mer information om hur du kan förbättra prestanda finns i [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimerings](#considerations-for-compression) avsnitt.
* **Blob Storage**: för närvarande stöder Blob Storage bara block-blobbar för optimerad data överföring och data flöde.
* För scenarier med **lokala fil system** som kräver användning av **Data Management Gateway**, se avsnittet [överväganden för data Management Gateway](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Relations data lager
*(Innehåller SQL Database, Azure Synapse Analytics, SQL Server-databaser och Oracle-databaser)*

* **Kopierings beteende**: beroende på de egenskaper som du har angett för **sqlSink** skriver kopierings aktiviteten data till mål databasen på olika sätt.
  * Som standard använder tjänsten för Mass kopiering API för att infoga data i läget append, vilket ger bästa möjliga prestanda.
  * Om du konfigurerar en lagrad procedur i mottagaren, tillämpar databasen data en rad i taget i stället för som en Mass inläsning. Prestanda sjunker avsevärt. Om din data uppsättning är stor kan du, när det är tillämpligt, byta till att använda egenskapen **sqlWriterCleanupScript** .
  * Om du konfigurerar egenskapen **sqlWriterCleanupScript** för varje kopierings aktivitet, utlöser tjänsten skriptet och använder sedan Mass kopierings-API: et för att infoga data. Om du till exempel vill skriva över hela tabellen med den senaste informationen kan du ange ett skript för att först ta bort alla poster innan du läser in nya data från källan.
* **Data mönster och batchstorlek**:
  * Ditt tabell schema påverkar kopiering av data flödet. För att kopiera samma mängd data ger en stor rad storlek bättre prestanda än en liten rad storlek eftersom databasen kan utföra färre batchar av data på ett effektivare sätt.
  * Kopierings aktiviteten infogar data i en serie batchar. Du kan ange antalet rader i en batch med hjälp av egenskapen **writeBatchSize** . Om dina data har små rader kan du ange egenskapen **writeBatchSize** med ett högre värde för att dra nytta av den lägre batch-overheadkostnaden och högre data flöde. Var försiktig när du ökar **writeBatchSize** om rad storleken för dina data är stor. Ett högt värde kan leda till ett kopierings haveri som orsakas av att databasen överbelastas.
* Information om **lokala Relations databaser** som SQL Server och Oracle, som kräver användning av **Data Management Gateway**, finns i avsnittet [överväganden för data Management Gateway](#considerations-for-data-management-gateway) .

### <a name="nosql-stores"></a>NoSQL-butiker
*(Innehåller tabell lagring och Azure Cosmos DB)*

* För **Table Storage**:
  * **Partition**: om du skriver data till överlagrade partitioner försämras prestanda avsevärt. Sortera dina källdata efter partitionsnyckel så att data infogas effektivt i en partition efter en annan eller justera logiken för att skriva data till en enda partition.
* För **Azure Cosmos DB**:
  * **Batchstorlek**: egenskapen **writeBatchSize** anger antalet parallella förfrågningar till Azure Cosmos DB tjänsten för att skapa dokument. Du kan vänta bättre prestanda när du ökar **writeBatchSize** eftersom fler parallella förfrågningar skickas till Azure Cosmos dB. Titta dock efter begränsning när du skriver till Azure Cosmos DB (fel meddelandet är "begär ande frekvens är stort"). Olika faktorer kan orsaka begränsning, inklusive dokument storlek, antalet termer i dokumenten och mål samlingens indexerings princip. Överväg att använda en bättre samling, till exempel S3, för att uppnå högre kopierings data flöde.

## <a name="considerations-for-serialization-and-deserialization"></a>Överväganden för serialisering och deserialisering
Serialisering och deserialisering kan inträffa när data uppsättningen för indata eller utdata är en fil. Se [fil-och komprimerings format som stöds](data-factory-supported-file-and-compression-formats.md) med information om vilka fil format som stöds genom kopierings aktivitet.

**Kopierings beteende**:

* Kopiera filer mellan filbaserade data lager:
  * När indata-och utdata-data anges både har samma eller inga fil format inställningar, kör data flyttnings tjänsten en binär kopia utan serialisering eller deserialisering. Du ser ett högre data flöde jämfört med scenariot, där käll-och mottagar fil format inställningarna skiljer sig från varandra.
  * Om både indata och utdata är i text format och endast kodnings typen är annorlunda, stöder endast data flyttnings tjänsten konvertering. Ingen serialisering och deserialisering sker, vilket medför en del prestanda jämfört med en binär kopia.
  * När indata-och utmatnings data uppsättningar båda har olika fil format eller olika konfigurationer, t. ex. avgränsare, deserialiserar data flyttnings tjänsten källdata till Stream, transformerar och serialiserar sedan den till det utdataformat du angav. Den här åtgärden resulterar i mycket mer betydande prestanda jämfört med andra scenarier.
* När du kopierar filer till/från ett data lager som inte är filbaserat (till exempel från en filbaserad lagring till ett Relations lager), krävs steget serialisering eller avserialisering. Det här steget resulterar i avsevärda prestanda kostnader.

**Fil format**: det fil format som du väljer kan påverka kopierings prestandan. Till exempel är Avro ett kompakt binärt format som lagrar metadata med data. Det har brett stöd i Hadoop-eko systemet för bearbetning och frågor. Avro är dock dyrare för serialisering och deserialisering, vilket resulterar i lägre kopierings data flöde jämfört med text format. Välj fil format i hela bearbetnings flödet. Börja med det formulär som data lagras i, käll data lagras eller extraheras från externa system; Det bästa formatet för lagring, analys bearbetning och frågor; och i vilket format ska data exporteras till datamarts för rapporterings-och visualiserings verktyg. Ibland kan ett fil format som är under optimalt för Läs-och skriv prestanda vara ett bra val när du bedömer den övergripande analys processen.

## <a name="considerations-for-compression"></a>Att tänka på vid komprimering
När din indata-eller utdata-data uppsättning är en fil kan du ange kopierings aktivitet för att utföra komprimering eller dekomprimering när data skrivs till målet. När du väljer komprimering gör du en kompromiss mellan indata/utdata (I/O) och CPU. Komprimera data kostnader extra i beräknings resurser. Men i detta utbyte minskar nätverks-I/O och lagring. Beroende på dina data kan du se en ökning i det totala kopierings flödet.

**Codec**: kopierings aktiviteten stöder gzip-, bzip2-och DEFLATE-komprimerings typer. Azure HDInsight kan använda alla tre typerna för bearbetning. Varje komprimerings-codec har fördelar. Till exempel har bzip2 det lägsta kopierings flödet, men du får bästa Hive-frågans prestanda med bzip2 eftersom du kan dela den för bearbetning. GZIP är det mest balanserade alternativet och används oftast. Välj den codec som passar bäst för ditt scenario från slut punkt till slut punkt.

**Nivå**: du kan välja mellan två alternativ för varje komprimerings-Codec: snabbast komprimerad och optimalt komprimerad. Det snabbast komprimerade alternativet komprimerar data så snabbt som möjligt, även om den resulterande filen inte komprimeras optimalt. Det optimala komprimerade alternativet tillbringar mer tid på komprimering och ger en minimal mängd data. Du kan testa båda alternativen för att se vilka som ger bättre övergripande prestanda i ditt fall.

**Tänk på följande:** om du vill kopiera en stor mängd data mellan en lokal lagrings plats och molnet kan du överväga att använda interimistisk Blob Storage med komprimering. Att använda mellanlagring är användbart när bandbredden i företagets nätverk och dina Azure-tjänster är den begränsande faktorn, och du vill att indata och utdata för indata ska vara i okomprimerat format. Mer specifikt kan du dela upp en enskild kopierings aktivitet i två kopierings aktiviteter. Den första kopierings aktiviteten kopierar från källan till en tillfällig eller mellanlagringsplats av BLOB i komprimerad form. Den andra kopierings aktiviteten kopierar komprimerade data från mellanlagring och dekomprimerar när den skrivs till mottagaren.

## <a name="considerations-for-column-mapping"></a>Överväganden för kolumn mappning
Du kan ställa in egenskapen **columnMappings** i kopierings aktivitet för att mappa alla eller en delmängd av inmatnings kolumnerna till utdatakolumner. När data flyttnings tjänsten har läst data från källan måste den utföra kolumn mappning för data innan de skriver data till mottagaren. Den här extra bearbetningen minskar kopierings data flödet.

Om ditt källdata till exempel kan frågas, till exempel om det är en Relations butik som SQL Database eller SQL Server, eller om det är en NoSQL-butik som Table Storage eller Azure Cosmos DB, bör du överväga att flytta kolumn filtreringen och ändra ordningen på logiken till egenskapen **fråga** i stället för att använda kolumn mappning. På så sätt inträffar projektionen medan data flyttnings tjänsten läser data från käll data lagret, där det är mycket mer effektivt.

## <a name="other-considerations"></a>Ytterligare överväganden
Om storleken på de data som du vill kopiera är stor kan du justera affärs logiken för att ytterligare partitionera data med hjälp av segmenterings metoden i Data Factory. Schemalägg sedan kopierings aktiviteten så att den körs oftare för att minska data storleken för varje kopierings aktivitet.

Var försiktig med antalet data uppsättningar och kopierings aktiviteter som kräver Data Factory att ansluta till samma data lager samtidigt. Många samtidiga kopierings jobb kan begränsa ett data lager och leda till försämrade prestanda, kopiera jobb interna återförsök och i vissa fall körnings problem.

## <a name="sample-scenario-copy-from-a-sql-server-database-to-blob-storage"></a>Exempel scenario: kopiera från en SQL Server-databas till Blob Storage
**Scenario**: en pipeline är byggd för att kopiera data från en SQL Server databas till Blob Storage i CSV-format. Om du vill göra kopierings jobbet snabbare ska CSV-filerna komprimeras till bzip2-format.

**Test och analys**: data flödet för kopierings aktiviteten är mindre än 2 Mbit/s, vilket är mycket långsammare än prestanda måttet.

**Prestanda analys och-justering**: för att felsöka prestanda problemet ska vi titta på hur data bearbetas och flyttas.

1. **Läsa data**: Gateway öppnar en anslutning till SQL Server och skickar frågan. SQL Server svarar genom att skicka data strömmen till gateway via intranätet.
2. **Serialisera och komprimera data**: gatewayen serialiserar data strömmen till CSV-format och komprimerar data till en bzip2-dataström.
3. **Skriv data**: Gateway laddar upp bzip2-dataströmmen till Blob Storage via Internet.

Som du kan se bearbetas och flyttas data i ett sekventiellt strömnings sätt: SQL Server > LAN > Gateway > WAN-> Blob Storage. **Den övergripande prestandan är gated med minsta data flöde i pipelinen**.

![Dataflöde](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

En eller flera av följande faktorer kan orsaka Flask hals för prestanda:

* **Källa**: SQL Server har låg genom strömning på grund av tung belastning.
* **Data Management Gateway**:
  * **LAN**: gatewayen ligger långt från SQL Server datorn och har en anslutning med låg bandbredd.
  * **Gateway**: gatewayen har nått sina inläsnings begränsningar för att utföra följande åtgärder:
    * **Serialisering**: serialisering av data strömmen till CSV-format har långsam data flöde.
    * **Komprimering**: du väljer en låg komprimerings-codec (till exempel bzip2, som är 2,8 Mbit/s med Core i7).
  * **WAN**: bandbredden mellan företags nätverket och Azure-tjänsterna är låg (till exempel T1 = 1 544 kbps; T2 = 6 312 kbit/s).
* **Sink**: Blob Storage har ett lågt data flöde. (Det här scenariot är osannolikt eftersom dess SLA garanterar minst 60 Mbit/s.)

I det här fallet kan bzip2 data komprimering sakta ned hela pipelinen. Att växla till en gzip-komprimerings-codec kan under lätta denna Flask hals.

## <a name="sample-scenarios-use-parallel-copy"></a>Exempel scenarier: Använd parallell kopia
**Scenario I:** Kopiera 1 000 1 MB-filer från det lokala fil systemet till Blob Storage.

**Analys och prestanda justering**: om du till exempel har installerat gateway på en quad core-dator använder Data Factory 16 parallella kopior för att flytta filer från fil systemet till Blob Storage samtidigt. Den här parallella körningen bör resultera i högt data flöde. Du kan också uttryckligen ange antalet parallella kopior. När du kopierar många små filer, bidrar parallella kopior dramatiskt genom att använda resurser på ett mer effektivt sätt.

![Scenario 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenario II**: kopiera 20 blobbar på 500 MB från Blob storage till data Lake Store Analytics och justera sedan prestanda.

**Analys och prestanda justering**: i det här scenariot kopierar data Factory data från Blob storage till data Lake Store genom att använda en enda kopia (**parallelCopies** inställt på 1) och data flytt enheter med en enda moln mängd. Data flödet du ser ligger nära det som beskrivs i avsnittet om [prestanda referens](#performance-reference).

![Scenario 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenario III**: individuell fil storlek är större än dussin tals MB och den totala volymen är stor.

**Analys och prestanda**: om du ökar **parallelCopies** resulterar det inte i bättre kopierings prestanda på grund av resurs begränsningarna i en DMU med ett enda moln. I stället bör du ange fler moln DMUs för att få fler resurser för att utföra data förflyttningen. Ange inget värde för egenskapen **parallelCopies** . Data Factory hanterar parallellitet åt dig. I det här fallet, om du anger **cloudDataMovementUnits** till 4, sker en genom strömning av fyra gånger.

![Scenario 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referens
Här följer prestanda övervakning och justering av referenser för några av de data lager som stöds:

* Azure Blob Storage: [skalbarhets-och prestanda mål för Blob Storage](../../storage/blobs/scalability-targets.md) och [Check lista för prestanda och skalbarhet för Blob Storage](../../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: [skalbarhets-och prestanda mål för](../../storage/tables/scalability-targets.md) [Check lista för tabell lagring och prestanda och skalbarhet för Table Storage](../../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: du kan [övervaka prestanda](../../azure-sql/database/monitor-tune-overview.md) och kontrol lera DTU-procenten (Database Transaction Unit)
* Azure Synapse Analytics: dess funktion mäts i informations lager enheter (DWU: er). Se [hantera beräknings kraft i Azure Synapse Analytics (översikt)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [prestanda nivåer i Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* Lokala SQL Server: [övervaka och justera för prestanda](/sql/relational-databases/performance/monitor-and-tune-for-performance)
* Lokal fil Server: [prestanda justering för fil servrar](/previous-versions//dn567661(v=vs.85))