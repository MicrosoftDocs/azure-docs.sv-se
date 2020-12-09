---
title: Käll omvandling i data flöde för mappning
description: Lär dig hur du konfigurerar en käll omvandling i mappnings data flödet.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: d375632ad02f9ec7cacf1708ac81c4f257916609
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929250"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Käll omvandling i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En käll omvandling konfigurerar data källan för data flödet. När du skapar data flöden konfigurerar det första steget alltid en käll omvandling. Om du vill lägga till en källa väljer du rutan **Lägg till källa** i arbets ytan data flöde.

Varje data flöde kräver minst en käll omvandling, men du kan lägga till så många källor som behövs för att slutföra dina data transformationer. Du kan koppla ihop dessa källor med en JOIN-, lookup-eller union-omvandling.

Varje käll omvandling är associerad med exakt en data uppsättning eller länkad tjänst. Data uppsättningen definierar formen och platsen för de data som du vill skriva till eller läsa från. Om du använder en filbaserad data uppsättning kan du använda jokertecken och fil listor i din källa för att arbeta med fler än en fil i taget.

## <a name="inline-datasets"></a>Infogade data uppsättningar

Det första beslut du fattar när du skapar en käll omvandling är om din käll information definieras i ett DataSet-objekt eller i käll omvandlingen. De flesta format är bara tillgängliga i en eller ett annat. Information om hur du använder en speciell koppling finns i lämpligt kopplings dokument.

När ett format stöds för både infogade och i ett data uppsättnings objekt, finns det fördelar med båda. Data uppsättnings objekt är återanvändbara entiteter som kan användas i andra data flöden och aktiviteter som kopiering. Dessa återanvändbara entiteter är särskilt användbara när du använder ett strikt schema. Data uppsättningar är inte baserade i Spark. Ibland kan du behöva åsidosätta vissa inställningar eller schema projektion i käll omvandlingen.

Infogade data uppsättningar rekommenderas när du använder flexibla scheman, ensidiga käll instanser eller parameter källor. Om källan är mycket parametriserad kan infogade data uppsättningar inte skapa ett "dummy"-objekt. Infogade data uppsättningar baseras i Spark och deras egenskaper är inbyggda i data flödet.

Om du vill använda en infogad data uppsättning väljer du det format som du vill använda i väljaren för **käll typ** . I stället för att välja en käll data uppsättning väljer du den länkade tjänst som du vill ansluta till.

![Skärm bild som visar infogad markering.](media/data-flow/inline-selector.png "Skärm bild som visar infogad markering.")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Käll typer som stöds

Genom att mappa data flödet följer du en metod för att extrahera, läsa in och transformera (ELT) och fungerar med *mellanlagring* av data uppsättningar som är alla i Azure. För närvarande kan följande data uppsättningar användas i en käll omvandling.

| Anslutning | Format | Data uppsättning/intern |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Avgränsad text](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Avgränsad text](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Service](format-common-data-model.md#source-properties)<br>[Avgränsad text](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL-hanterad instans (för hands version)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Hive](connector-hive.md#mapping-data-flow-properties) | | -/✓ |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Inställningar som är aktuella för dessa anslutningar finns på fliken **käll alternativ** . Skript exempel för information och data flöde i dessa inställningar finns i anslutnings dokumentationen.

Azure Data Factory har till gång till fler än [90 inbyggda anslutningar](connector-overview.md). Om du vill ta med data från de andra källorna i ditt data flöde använder du kopierings aktiviteten för att läsa in dessa data till något av de mellanliggande mellanlagrings områdena.

## <a name="source-settings"></a>Källinställningar

När du har lagt till en källa konfigurerar du via fliken **käll inställningar** . Här kan du välja eller skapa den data uppsättning som din käll pekar på. Du kan också välja schema-och samplings alternativ för dina data.

Utvecklings värden för data uppsättnings parametrar kan konfigureras i [fel söknings inställningar](concepts-data-flow-debug-mode.md). (Fel söknings läge måste vara aktiverat.)

![Skärm bild som visar fliken käll inställningar.](media/data-flow/source1.png "Skärm bild som visar fliken käll inställningar.")

**Namn på utdataström**: namnet på käll omvandlingen.

**Typ av källa**: Välj om du vill använda en infogad data uppsättning eller ett befintligt data uppsättnings objekt.

**Testa anslutning**: testa om data flödets Spark-tjänst kan ansluta till den länkade tjänsten som används i din käll data uppsättning. Fel söknings läge måste vara aktiverat för att den här funktionen ska vara aktive rad.

**Schema avvikelse**: [schema avvikelse](concepts-data-flow-schema-drift.md) är möjligheten att Data Factory att internt hantera flexibla scheman i dina data flöden utan att uttryckligen definiera kolumn ändringar.

* Markera kryss rutan **Tillåt schema avvikelse** om käll kolumnerna ska ändras ofta. Med den här inställningen kan alla inkommande källfält flöda genom omvandlingarna till mottagaren.

* Om du väljer **härledda kolumn typer** instrueras Data Factory att identifiera och definiera data typer för varje ny kolumn som identifieras. När den här funktionen är inaktive rad blir alla nedsatta kolumner av typen sträng.

**Verifiera schema:** Om **Verifiera schema** har valts kan data flödet inte köras om inkommande källdata inte matchar det definierade schemat för data uppsättningen.

**Hoppa över linje antal**: fältet **slopa rad antal** anger hur många rader som ska ignoreras i början av data uppsättningen.

**Sampling**: aktivera **sampling** för att begränsa antalet rader från källan. Använd den här inställningen när du testar eller samplar data från källan för fel sökning.

Verifiera att källan är korrekt konfigurerad genom att aktivera fel söknings läge och hämta en data förhands granskning. Mer information finns i [fel söknings läge](concepts-data-flow-debug-mode.md).

> [!NOTE]
> När fel söknings läget är aktiverat skriver rad begränsnings konfigurationen i fel söknings inställningarna över insamlings inställningen i källan under för hands versionen av data.

## <a name="source-options"></a>Käll alternativ

Fliken **käll alternativ** innehåller inställningar som är aktuella för den koppling och det format som valts. Mer information och exempel finns i relevant Connector- [dokumentation](#supported-sources).

## <a name="projection"></a>Projektion

Som scheman i data uppsättningar definierar projektionen i en källa data kolumnerna, typerna och formaten från käll data. För de flesta typer av data uppsättningar, till exempel SQL och Parquet, åtgärdas projektionen i en källa för att återspegla det schema som definierats i en data uppsättning. Om källfilerna inte är starkt skrivna (till exempel flat. csv-filer i stället för Parquet-filer) kan du definiera data typerna för varje fält i käll omvandlingen.

![Skärm bild som visar inställningarna på fliken projektion.](media/data-flow/source3.png "Skärm bild som visar inställningarna på fliken projektion.")

Om text filen inte har något definierat schema väljer du **identifiera data typ** så att Data Factory kan sampla och härleda data typerna. Välj **definiera standardformat** för att automatiskt identifiera standard data formaten.

**Återställ schema** återställer projektionen till det som definieras i den refererade data uppsättningen.

Du kan ändra kolumn data typerna i en underordnad härledd kolumn-omvandling. Använd en SELECT-omvandling för att ändra kolumn namnen.

### <a name="import-schema"></a>Importera schema

Välj knappen **Importera schema** på fliken **projektion** om du vill använda ett aktivt fel söknings kluster för att skapa en schema projektion. Den är tillgänglig i varje källtyp. Genom att importera schemat här åsidosätts projektionen som definierats i data uppsättningen. Dataset-objektet ändras inte.

Att importera schemat är användbart i data uppsättningar som Avro och Azure Cosmos DB som stöder komplexa data strukturer som inte kräver att schema definitioner finns i data uppsättningen. För infogade data uppsättningar är importera schema det enda sättet att referera till kolumnens metadata utan schema avvikelser.

## <a name="optimize-the-source-transformation"></a>Optimera käll omvandlingen

På fliken **optimera** kan du redigera partitionsinformation i varje omformnings steg. I de flesta fall optimeras **användningen av nuvarande partitionering** för den perfekta partitionerings strukturen för en källa.

Om du läser från en Azure SQL Database källa kommer den anpassade **käll** partitionering sannolikt att läsa data som är snabbast. Data Factory kommer att läsa stora frågor genom att göra anslutningar till databasen parallellt. Den här käll partitionering kan göras i en kolumn eller med hjälp av en fråga.

![Skärm bild som visar käll partitionens inställningar.](media/data-flow/sourcepart3.png "Skärm bild som visar käll partitionens inställningar.")

Mer information om optimering i data flödet för mappning finns på [fliken optimera](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Nästa steg

Börja bygga ditt data flöde med en [omvandling med härledd kolumn](data-flow-derived-column.md) och en [Välj omvandling](data-flow-select.md).
