---
title: Kopiera aktivitets prestanda optimerings funktioner
description: Lär dig mer om de viktiga funktioner som hjälper dig att optimera prestanda för kopierings aktiviteten i Azure Data Factory från en omfattande
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/24/2020
ms.openlocfilehash: ecb4550b218b069273cba2e3d70a9510c1cc74ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387809"
---
# <a name="copy-activity-performance-optimization-features"></a>Kopiera aktivitets prestanda optimerings funktioner

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver prestanda optimerings funktionerna för kopierings aktiviteten som du kan utnyttja i Azure Data Factory.

## <a name="data-integration-units"></a>Dataintegrationsenheter

En data integrerings enhet är ett mått som representerar styrkan (en kombination av processor, minne och tilldelning av nätverks resurser) för en enskild enhet i Azure Data Factory. Data integrerings enheten gäller endast för [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime), men inte för [integration runtime med egen värd](concepts-integration-runtime.md#self-hosted-integration-runtime).

Den tillåtna DIUs som gör att kopierings aktiviteten körs är **mellan 2 och 256**. Om detta inte anges eller om du väljer Auto i användar gränssnittet, använder Data Factory dynamiskt inställningen för optimal DIU baserat på ditt käll-Sink-par och data mönster. I följande tabell visas de DIU-intervall som stöds och standard beteendet i olika kopierings scenarier:

| Kopierings scenario | DIU-intervall som stöds | Standard DIUs som fastställs av tjänsten |
|:--- |:--- |---- |
| Mellan filarkiv |- **Kopiera från eller till en enskild fil**: 2-4 <br>- **Kopiera från och till flera filer**: 2-256 beroende på antalet och storleken på filerna <br><br>Om du till exempel kopierar data från en mapp med 4 stora filer och väljer att bevara hierarkin är den högsta effektiva DIU 16. När du väljer att sammanfoga filen är den högsta effektiva DIU 4. |Mellan 4 och 32 beroende på antalet och storleken på filerna |
| Från fil arkiv till icke-filarkiv |- **Kopiera från en enskild fil**: 2-4 <br/>- **Kopiera från flera filer**: 2-256 beroende på antalet och storleken på filerna <br/><br/>Om du t. ex. kopierar data från en mapp med 4 stora filer är den högsta effektiva DIU 16. |- **Kopiera till Azure SQL Database eller Azure Cosmos DB**: mellan 4 och 16 beroende på mottagar nivå (DTU: er/ru: er) och käll fils mönster<br>- **Kopiera till Azure Synapse Analytics** med PolyBase eller Copy-sats: 2<br>– Annat scenario: 4 |
| Från icke-filarkiv till fil lagring |- **Kopiera från partitioner – alternativ-aktiverade data lager** (inklusive [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL-hanterad instans](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)och [Teradata](connector-teradata.md#teradata-as-source)): 2-256 vid skrivning till en mapp och 2-4 vid skrivning till en enda fil. Obs! per datakälla kan använda upp till 4 DIUs.<br>- **Andra scenarier**: 2-4 |- **Kopiera från rest eller http**: 1<br/>- **Kopiera från Amazon RedShift** med Unload: 2<br>- **Annat scenario**: 4 |
| Mellan icke-filarkiv |- **Kopiera från partitioner – alternativ-aktiverade data lager** (inklusive [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL-hanterad instans](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)och [Teradata](connector-teradata.md#teradata-as-source)): 2-256 vid skrivning till en mapp och 2-4 vid skrivning till en enda fil. Obs! per datakälla kan använda upp till 4 DIUs.<br/>- **Andra scenarier**: 2-4 |- **Kopiera från rest eller http**: 1<br>- **Annat scenario**: 4 |

Du kan se DIUs som används för varje kopierings körning i vyn för övervakning av kopierings aktiviteter eller Uppgiftsutdata. Mer information finns i avsnittet [Kopiera aktivitets övervakning](copy-activity-monitoring.md). Om du vill åsidosätta standardvärdet anger du ett värde för `dataIntegrationUnits` egenskapen enligt följande. Det *faktiska antalet DIUs* som kopierings åtgärden använder vid körningen är lika med eller mindre än det konfigurerade värdet, beroende på ditt data mönster.

Du debiteras **antalet använda DIUs \* kopierings \* pris/DIU-timme**. Se aktuella priser [här](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). Lokal valuta och separat rabatt kan gälla per prenumerations typ.

**Exempel:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Skalbarhet för integration runtime med egen värd

Om du vill uppnå högre data flöde kan du skala upp eller skala ut den egna IR: en:

- Om processorn och det tillgängliga minnet på IR-noden med egen värd inte används fullt ut, men körningen av samtidiga jobb når gränsen, bör du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod.  Mer information finns [här](create-self-hosted-integration-runtime.md#scale-up) .
- Om å andra sidan är processorn hög på den lokala IR-noden eller tillgängligt minne är lågt, kan du lägga till en ny nod för att skala belastningen över flera noder.  Mer information finns [här](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

Observera i följande scenarier kan körning av en enskild kopierings aktivitet använda flera IR-noder med egen värd:

- Kopiera data från filbaserade butiker, beroende på antalet filer och filernas storlek.
- Kopiera data från partition-alternativt-aktiverat data lager (inklusive [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL-hanterad instans](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)och [Teradata](connector-teradata.md#teradata-as-source)), beroende på antalet datapartitioner.

## <a name="parallel-copy"></a>Parallell kopia

Du kan ange parallell kopiering ( `parallelCopies` egenskap) för kopierings aktivitet för att ange vilken parallellitet som ska användas för kopierings aktiviteten. Du kan tänka på den här egenskapen som det maximala antalet trådar i kopierings aktiviteten som läser från din källa eller skriver till dina mottagar data lager parallellt.

Den parallella kopian är rätvinklig till [data integrerings enheter](#data-integration-units) eller [IR-noder med egen värd](#self-hosted-integration-runtime-scalability). Det räknas i alla DIUs-eller IR-noder med egen värd.

För varje kopierings aktivitets körning tillämpar Azure Data Factory dynamiskt den optimala parallell kopierings inställningen baserat på ditt käll-Sink-par och data mönster. 

> [!TIP]
> Standard beteendet för parallell kopiering ger vanligt vis bästa möjliga data flöde, vilket automatiskt avgörs av ADF baserat på ditt käll-Sink-par, data mönster och antalet DIUs eller den lokala IR-CPU/minne/antal noder. Se [fel sökning av prestanda för kopierings aktivitet](copy-activity-performance-troubleshooting.md) på när du ska justera parallell kopia.

I följande tabell visas den parallella kopierings beteendet:

| Kopierings scenario | Parallell kopierings beteende |
| --- | --- |
| Mellan filarkiv | `parallelCopies` fastställer parallellitet **på filnivå**. Delningen i varje fil sker under automatiskt och transparent. Den är utformad för att använda den bästa lämpliga segment storleken för en specifik data lager typ för att läsa in data parallellt. <br/><br/>Det faktiska antalet kopierings aktiviteter för parallella kopior som används vid körning är inte mer än antalet filer som du har. Om kopierings beteendet är **mergeFile** till filsink kan kopierings aktiviteten inte dra nytta av Parallel på filnivå. |
| Från fil arkiv till icke-filarkiv | – När du kopierar data till Azure SQL Database eller Azure Cosmos DB, är standard parallell kopia också beroende av mottagar nivå (antal DTU: er/ru: er).<br>– När du kopierar data till Azure-tabellen är standard parallell kopia 4. |
| Från icke-filarkiv till fil lagring | – När du kopierar data från partitioner – alternativt aktiverade data lager (inklusive [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL-hanterad instans](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)och [Teradata](connector-teradata.md#teradata-as-source)), är standard parallell kopia 4. Det faktiska antalet kopierings aktiviteter för parallella kopior som används vid körning är inte mer än antalet datapartitioner som du har. Observera att den högsta effektiva parallella kopieringen är 4 eller 5 per IR-nod när du använder egen värd Integration Runtime och kopierar till Azure Blob/ADLS Gen2.<br>– För andra scenarier börjar inte parallell kopieringen gälla. Även om parallelitet anges används inte det. |
| Mellan icke-filarkiv | – När du kopierar data till Azure SQL Database eller Azure Cosmos DB, är standard parallell kopia också beroende av mottagar nivå (antal DTU: er/ru: er).<br/>– När du kopierar data från partitioner – alternativt aktiverade data lager (inklusive [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL-hanterad instans](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)och [Teradata](connector-teradata.md#teradata-as-source)), är standard parallell kopia 4.<br>– När du kopierar data till Azure-tabellen är standard parallell kopia 4. |

Om du vill styra belastningen på datorer som är värdar för dina data lager, eller om du vill justera kopierings prestanda, kan du åsidosätta standardvärdet och ange ett värde för `parallelCopies` egenskapen. Värdet måste vara ett heltal som är större än eller lika med 1. Vid körning, för bästa prestanda, använder kopierings aktiviteten ett värde som är mindre än eller lika med det värde som du anger.

När du anger ett värde för `parallelCopies` egenskapen ska du ta belastnings ökningen på din källa och ta emot data lager i kontot. Överväg även belastnings ökningen till den egna värdbaserade integrerings körningen om kopierings aktiviteten har befogenheter av den. Den här belastnings ökningen sker särskilt när du har flera aktiviteter eller samtidiga körningar av samma aktiviteter som körs mot samma data lager. Om du ser att antingen data lagret eller den egen värdbaserade integrerings körningen är överbelastad med belastningen minskar du `parallelCopies` värdet för att avlasta belastningen.

**Exempel:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>Mellanlagrad kopia

När du kopierar data från ett käll data lager till ett data lager för mottagare kan du välja att använda Azure Blob Storage eller Azure Data Lake Storage Gen2 som ett interimistiskt mellanlagrings lager. Mellanlagring är särskilt användbart i följande fall:

- **Du vill mata in data från olika data lager i Azure Synapse Analytics via PolyBase, kopiera data från/till snö flingor eller mata in data från Amazon RedShift/HDFS performantly.** Lär dig mer om att:
  - [Använd PolyBase för att läsa in data i Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics).
  - [Snö-koppling](connector-snowflake.md)
  - [Anslutningsapp för Amazon Redshift](connector-amazon-redshift.md)
  - [HDFS-anslutning](connector-hdfs.md)
- **Du vill inte öppna andra portar än port 80 och port 443 i brand väggen på grund av företagets IT-principer.** När du till exempel kopierar data från ett lokalt data lager till en Azure SQL Database eller en Azure Synapse-analys måste du aktivera utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företags brand väggen. I det här scenariot kan mellanlagrad kopiering dra nytta av den lokala integrerings körningen för att först kopiera data till en mellanlagring via HTTP eller HTTPS på port 443 och sedan läsa in data från mellanlagring i SQL Database eller Azure Synapse Analytics. I det här flödet behöver du inte aktivera port 1433.
- **Ibland tar det en stund att utföra en hybrid data förflyttning (det vill säga kopiera från ett lokalt data lager till ett moln data lager) över en långsam nätverks anslutning.** Du kan förbättra prestanda genom att använda mellanlagrad kopia för att komprimera data lokalt så att det tar mindre tid att flytta data till lagrings data lagret i molnet. Sedan kan du expandera data i mellanlagrings platsen innan du läser in i mål data lagret.

### <a name="how-staged-copy-works"></a>Så här fungerar mellanlagrad kopiering

När du aktiverar mellanlagrings funktionen kopieras först data från käll data lagret till mellanlagringsplatsen (ta med din egen Azure-Blob eller Azure Data Lake Storage Gen2). Därefter kopieras data från mellanlagringen till data lagret för mottagare. Azure Data Factory kopierings aktivitet hanterar automatiskt flödet i två steg åt dig och rensar även tillfälliga data från mellanlagringen när data förflyttningen är klar.

![Mellanlagrad kopia](media/copy-activity-performance/staged-copy.png)

När du aktiverar data förflyttning med hjälp av ett mellanlagrings lager kan du ange om du vill att data ska komprimeras innan du flyttar data från käll data lagret till mellanlagrings platsen och sedan expanderar innan du flyttar data från ett interimistiskt eller mellanlagrat data lager till data lagret för mottagare.

För närvarande kan du inte kopiera data mellan två data lager som är anslutna via en annan egen värd, varken med eller utan mellanlagrad kopia. I det här scenariot kan du konfigurera två explicit länkade kopierings aktiviteter för kopiering från källa till mellanlagring och från mellanlagring till mottagare.

### <a name="configuration"></a>Konfiguration

Konfigurera **enableStaging** -inställningen i kopierings aktiviteten och ange om du vill att data ska mellanlagras i lagrings utrymmet innan du läser in dem i ett mål data lager. När du ställer in **enableStaging** på `TRUE` anger du ytterligare egenskaper som anges i följande tabell. 

| Egenskap | Beskrivning | Standardvärde | Obligatorisk |
| --- | --- | --- | --- |
| enableStaging |Ange om du vill kopiera data via ett interimistiskt lagrings lager. |Falskt |Inga |
| linkedServiceName |Ange namnet på en [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) -eller [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) -länkad tjänst, som refererar till den lagrings instans som du använder som ett interimistiskt mellanlagrings lager. |Ej tillämpligt |Ja, när **enableStaging** är inställt på True |
| path |Ange den sökväg som du vill ska innehålla mellanlagrade data. Om du inte anger en sökväg skapar tjänsten en behållare för att lagra temporära data. |Ej tillämpligt |Inga |
| enableCompression |Anger om data ska komprimeras innan de kopieras till målet. Den här inställningen minskar mängden data som överförs. |Falskt |Inga |

>[!NOTE]
> Om du använder mellanlagrad kopiering med komprimering aktive rad stöds inte tjänstens huvud namn eller MSI-autentisering för den länkade Blob-tjänsten.

Här är en exempel definition av en kopierings aktivitet med de egenskaper som beskrivs i föregående tabell:

```json
"activities":[
    {
        "name": "CopyActivityWithStaging",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
            },
            "sink": {
                "type": "SqlDWSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path"
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Fakturerings effekt för mellanlagrad kopiering

Du debiteras baserat på två steg: kopiera varaktighet och kopierings typ.

* När du använder mellanlagring under en moln kopia, som kopierar data från ett moln data lager till ett annat moln data lager, båda stegen som du har fått av Azure integration runtime debiteras du [summan av kopierings tiden för steg 1 och steg 2] x [Cloud Copy Unit Price].
* När du använder mellanlagring under en hybrid kopiering, som kopierar data från ett lokalt data lager till ett moln data lager, en fas som har en egen värd för integration runtime debiteras du för [hybrid kopieringens varaktighet] x [hybrid kopiera enhets pris] + [moln kopieringens varaktighet] x [Cloud Copy Unit Price].

## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Vägledning om prestanda och skalbarhet för kopieringsaktivitet](copy-activity-performance.md)
- [Felsöka prestanda för kopierings aktivitet](copy-activity-performance-troubleshooting.md)
- [Använd Azure Data Factory för att migrera data från data Lake eller data lager till Azure](data-migration-guidance-overview.md)
- [Migrera data från Amazon S3 till Azure Storage](data-migration-guidance-s3-azure-storage.md)