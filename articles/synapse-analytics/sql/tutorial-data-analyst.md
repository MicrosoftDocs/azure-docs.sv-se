---
title: 'Självstudie: Använd Server lös SQL-pool för att analysera Azure Open-datauppsättningar i Azure Synapse Studio'
description: Den här självstudien visar hur du enkelt kan utföra analys av exempel data som kombinerar olika Azure Open-datauppsättningar med SQL-pool utan server och visualisera resultatet i Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 11/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d37597f8667c461e8d61f8214483f57eb702c2a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007559"
---
# <a name="tutorial-explore-and-analyze-data-lakes-with-serverless-sql-pool"></a>Självstudie: utforska och analysera data sjöar med en server lös SQL-pool

I den här självstudien får du lära dig hur du utför analys av exempel data. Du kan kombinera olika Azure Open-datauppsättningar med hjälp av SQL-poolen utan server. Sedan kan du visualisera resultaten i Synapse Studio för Azure Synapse Analytics.

Funktionen OpenRowSet (Mass...) gör att du kan komma åt filer i Azure Storage. [OpenRowSet](develop-openrowset.md) -funktionen läser innehåll i en fjärrdatakälla (till exempel fil) och returnerar innehållet som en uppsättning rader.

## <a name="automatic-schema-inference"></a>Automatisk schema härledning

Eftersom data lagras i fil formatet Parquet, är automatisk schema härledning tillgängligt. Du kan enkelt fråga data utan att lista data typerna för alla kolumner i filerna. Du kan också använda den virtuella kolumnens mekanism och funktionen Sök väg för att filtrera bort en del av filerna.

Låt oss först bekanta dig med NYC taxi-data genom att köra följande fråga:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

[New York City (NYC) taxi data uppsättningen](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) innehåller:

- Datum och tid för att hämta och ta bort.
- Plocknings-och list Rute platser. 
- Rese avstånd.
- Specificerade priser.
- Pris typer.
- Betalnings typer. 
- Driv rutin – rapporterade person antal.

På samma sätt kan du fråga den offentliga helgdags data uppsättningen med hjälp av följande fråga:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Slutligen kan du även fråga data uppsättningen för väder data med hjälp av följande fråga:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Du kan lära dig mer om innebörden av enskilda kolumner i beskrivningarna av data uppsättningarna: 
- [NYC taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)
- [Offentliga helgdagar](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
- [Väder data](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)

## <a name="time-series-seasonality-and-outlier-analysis"></a>Tids serier, säsongs beroende och avvikare analys

Du kan enkelt sammanfatta det årliga antalet taxi-åsidosättningar med hjälp av följande fråga:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Följande kodfragment visar resultatet för det årliga antalet taxi-åsidosättningar:

![Årligt antal taxi-åsidosättningar resultat-kodfragment](./media/tutorial-data-analyst/yearly-taxi-rides.png)

Data kan visualiseras i Synapse Studio genom att växla från **tabellen** till **diagramvyn** . Du kan välja bland olika diagram typer, till exempel **yt**-, **stapel**-, **kolumn**-, **linje**-, **cirkel**-och **punkt** diagram. I det här fallet ska du rita **stapeldiagrammet** med **kategori** kolumnen inställt på **current_year**:

![Stapeldiagram med val per år](./media/tutorial-data-analyst/column-chart-rides-year.png)

I den här visualiseringen kan du se en trend för att minska antalet avslags nummer under åren. Den här minskningen beror förmodligen på den senaste ökande populariteten av delnings företag.

> [!NOTE]
> Vid tidpunkten för den här självstudien är data för 2019 ofullständiga. Det innebär att det är mycket viktigt att ta hänsyn till antalet åsidosättningar för det året.

Nu ska vi fokusera på att analysera ett enda år, till exempel 2016. Följande fråga returnerar det dagliga antalet åsidosättningar under det året:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Följande fragment visar resultatet för den här frågan:

![Dagligt antal åsidosättningar för 2016 resultat-kodfragment](./media/tutorial-data-analyst/daily-rides.png)

Återigen kan du enkelt visualisera data genom att rita **stapeldiagrammet** med **kategori** kolumn uppsättningen till **current_day** och kolumnen **förklaring (serie)** är inställd på **rides_per_day**.

![Stapeldiagram som visar det dagliga antalet åsidosättningar för 2016](./media/tutorial-data-analyst/column-chart-daily-rides.png)

I diagrammet kan du se att det finns ett vecko mönster, med lördagar som topp dag. Under sommaren månader finns det färre taxi-åsidosättningar på grund av semester. Lägg också märke till några betydande droppar i antalet taxi-åsidosättningar utan tydliga mönster för när och varför de inträffar.

Nu ska vi se om minskningen i de här tilläggen motsvarar offentliga helgdagar. Vi kan se om det finns en korrelation genom att delta i NYC taxi-datauppsättningen med den offentliga helgdags data uppsättningen:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![NYC taxi-uppsättningar och offentliga helgdagar data uppsättningar resultat visualisering](./media/tutorial-data-analyst/rides-public-holidays.png)

Den här gången vill vi belysa antalet taxi-ändringar under offentliga helgdagar. För det syftet väljer vi **ingen** för kolumnen **kategori** och **rides_per_day** och **helgdag** som **förklarings kolumner (serie)** .

![Antal taxi-undantag under offentliga helgdagar diagram](./media/tutorial-data-analyst/plot-chart-public-holidays.png)

I diagrammet kan du se att antalet taxi-åsidosättningar är lägre under offentliga helgdagar. Det finns fortfarande en icke-förklarad stor minskning den 23 januari. Nu ska vi titta på väder i NYC den dagen genom att fråga data uppsättningen för väder data:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![Resultat visualisering för data uppsättning för väder data](./media/tutorial-data-analyst/weather-data-set-visualization.png)

Resultatet av frågan tyder på att minskningen av antalet taxi-åsidosättningar inträffade på grund av följande:

- Det fanns en Blizzard på den dagen i NYC med tunga snö (~ 30 cm).
- Den var kall (temperatur var lägre än noll grader Celsius).
- Den var lindning (~ 10 m/s).

Den här självstudien visar hur en dataanalytiker snabbt kan utföra analys av analys av data, enkelt kombinera olika data uppsättningar med hjälp av SQL-poolen utan server och visualisera resultaten med hjälp av Azure Synapse Studio.

## <a name="next-steps"></a>Nästa steg

Information om hur du ansluter en server lös SQL-pool för att Power BI Desktop och skapa rapporter finns i [ansluta Server lös SQL-pool till Power BI Desktop och skapa rapporter](tutorial-connect-power-bi-desktop.md).

Information om hur du använder externa tabeller i SQL-pool utan Server finns i [använda externa tabeller med SYNAPSE SQL](develop-tables-external-tables.md?tabs=sql-pool)
 
