---
title: Azure Data Lake Storage fråga om acceleration
description: Med hjälp av frågekörning kan program och analys ramverk dramatiskt optimera data bearbetningen genom att bara hämta de data som krävs för en bearbetnings åtgärd.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 09/09/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: dc1d217dba64c36aa219abbd4d2220a494347689
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912764"
---
# <a name="azure-data-lake-storage-query-acceleration"></a>Azure Data Lake Storage fråga om acceleration

Med hjälp av frågekörning kan program och analys ramverk dramatiskt optimera data bearbetningen genom att bara hämta de data som de behöver för att utföra en specifik åtgärd. Detta minskar den tid och processor kraft som krävs för att få viktiga insikter om lagrade data.

## <a name="overview"></a>Översikt

Med frågans acceleration kan du filtrera *predikat* och *kolumn projektioner* som gör det möjligt för program att filtrera rader och kolumner vid den tidpunkt då data läses från disken. Endast de data som uppfyller villkoren i ett predikat överförs över nätverket till programmet. Detta minskar nätverks fördröjningen och beräknings kostnaderna.  

Du kan använda SQL för att ange rad filter-predikat och kolumn projektioner i en begäran om acceleration av frågor. En begäran bearbetar bara en fil. Därför stöds inte avancerade Relations funktioner i SQL, till exempel sammanfogningar och gruppering efter AGG regeringar. Frågans acceleration stöder CSV-och JSON-formaterade data som indata till varje begäran.

Funktionen för frågans acceleration är inte begränsad till Data Lake Storage (lagrings konton som har det hierarkiska namn området aktiverat). Frågans acceleration är helt kompatibel med blobbar i lagrings konton som **inte** har ett hierarkiskt namn område aktiverat på dem. Det innebär att du kan uppnå samma minskning av nätverks fördröjningen och beräknings kostnaderna när du bearbetar data som du redan har lagrat som blobar i lagrings konton.

Ett exempel på hur du använder frågans acceleration i ett klient program finns i [filtrera data med hjälp av Azure Data Lake Storage fråga acceleration](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Dataflöde

Följande diagram illustrerar hur ett typiskt program använder Query-acceleration för att bearbeta data.

> [!div class="mx-imgBorder"]
> ![Översikt över frågans acceleration](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. Klient programmet begär fildata genom att ange predikat och kolumn projektioner.

2. Med Query acceleration parsas angiven SQL-fråga och distribuerar arbete för att parsa och filtrera data.

3. Processorer läser data från disken, tolkar data med hjälp av lämpligt format och filtrerar sedan data genom att använda angivna predikat och kolumn projektioner.

4. Med Query acceleration kombineras svars Shards att strömma tillbaka till klient programmet.

5. Klient programmet tar emot och tolkar det strömmade svaret. Programmet behöver inte filtrera ytterligare data och kan tillämpa önskad beräkning eller omvandling direkt.

## <a name="better-performance-at-a-lower-cost"></a>Bättre prestanda till en lägre kostnad

Med frågekörning optimeras prestanda genom att minska mängden data som överförs och bearbetas av ditt program.

För att beräkna ett sammanställt värde hämtar program ofta **alla** data från en fil och bearbetar och filtrerar sedan data lokalt. En analys av indata/utdata-mönster för analys av arbets belastningar visar att program vanligt vis bara behöver 20% av de data som de har läst för att utföra någon bestämd beräkning. Den här statistiken är sann även när du har tillämpat metoder som [partition rensning](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md#hive-partitioning). Det innebär att 80% av dessa data överförs onödan över nätverket, parsas och filtreras efter program. Det här mönstret, som är utformat för att ta bort onödiga data, medför en betydande beräknings kostnad.  

Även om Azure har ett branschledande nätverk, vad gäller både genom strömning och latens, är onödan överföring av data i det nätverket fortfarande kostsamt för program prestanda. Genom att filtrera bort oönskade data under lagrings förfrågan eliminerar Query acceleration denna kostnad.

Dessutom kräver CPU-belastningen som krävs för att parsa och filtrera onödiga data ditt program att tillhandahålla ett större antal och större virtuella datorer för att utföra sitt arbete. Genom att överföra den här beräknings belastningen till fråga om acceleration kan program göra betydande kostnads besparingar.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Program som kan dra nytta av frågans acceleration

Fråga om acceleration är utformad för distribuerade analys ramverk och data bearbetnings program. 

Distribuerade analys ramverk som Apache Spark och Apache Hive innehåller ett lagrings abstraktions lager i ramverket. Dessa motorer innehåller även fråge optimeringar som kan innehålla kunskaper om de underliggande I/O-tjänstens funktioner när man fastställer en optimal frågeplan för användar frågor. Dessa ramverk börjar integrera frågans acceleration. Det innebär att användare av dessa ramverk ser förbättrad svars tid för frågor och en lägre totalkostnad utan att behöva göra några ändringar i frågorna. 

Fråga om acceleration är också utformad för data bearbetnings program. De här typerna av program utför vanligt vis storskaliga data omvandlingar som kanske inte direkt leder till analys insikter så att de inte alltid använder etablerade distribuerade analys ramverk. De här programmen har ofta en mer direkt relation med den underliggande lagrings tjänsten så att de kan dra nytta av funktioner som frågans acceleration. 

Ett exempel på hur ett program kan integrera frågans acceleration finns i [filtrera data med hjälp av Azure Data Lake Storage fråga acceleration](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Priser

På grund av den ökade beräknings belastningen i Azure Data Lake Storages tjänsten skiljer sig pris modellen för att använda frågans acceleration från den normala Azure Data Lake Storage transaktions modellen. För fråga acceleration debiteras en kostnad för mängden data som genomsöks samt en kostnad för den mängd data som returneras till anroparen. Mer information finns i [Azure Data Lake Storage Gen2 prissättning](https://azure.microsoft.com/pricing/details/storage/data-lake/).

Trots ändringen av fakturerings modellen är pris sättnings modellen för frågekörning utformad för att sänka den totala ägande kostnaden för en arbets belastning, med hänsyn till minskningen av de mycket dyrare kostnaderna för virtuella datorer.

## <a name="next-steps"></a>Nästa steg

- [Filtrera data med hjälp av Azure Data Lake Storage fråga om acceleration](data-lake-storage-query-acceleration-how-to.md)
- [Språk referens för Query acceleration SQL](query-acceleration-sql-reference.md)