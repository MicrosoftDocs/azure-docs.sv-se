---
title: Publicera din datafeed till Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Hur du kommer igång med att registrera dina datafeeds till Metrics Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: fe3b87c733f54d8bd52c4d973977e3c8cbfefe19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043227"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>Anvisningar: publicera dina mått data till Metrics Advisor

Använd den här artikeln för att lära dig att publicera dina data till Metrics Advisor. 

## <a name="data-schema-requirements-and-configuration"></a>Krav och konfiguration för data schema

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

## <a name="avoid-loading-partial-data"></a>Undvik inläsning av partiella data

Partiella data orsakas av inkonsekvenser mellan data som lagras i mått Advisor och data källan. Detta kan inträffa när data källan uppdateras när mått Advisor har tagit emot data. Mått Advisor hämtar endast data från en specifik data källa en gång.

Till exempel om ett mått har registrerats för mått Advisor för övervakning. Metrics Advisor hämtar Mät data vid tidsstämpel A och utför avvikelse identifiering på den. Men om Mät data för den specifika tidsstämpeln A har uppdaterats efter att data har matats in. Det nya datavärdet hämtades inte.

Du kan försöka att [fylla](manage-data-feeds.md#backfill-your-data-feed) i historiska data (beskrivs senare) för att minimera inkonsekvenser, men detta utlöser inte nya avvikelser om aviseringar för dessa tids punkter redan har utlösts. Den här processen kan lägga till ytterligare arbets belastning i systemet och är inte automatisk.

För att undvika inläsning av partiella data rekommenderar vi två metoder:

* Generera data i en transaktion:

    Se till att mått värden för alla dimensions kombinationer i samma tidsstämpel lagras i data källan i en transaktion. I exemplet ovan väntar du tills data från alla data källor är klara och läser in dem i mått Advisor i en transaktion. Mått Advisor kan avsöka datafeeden regelbundet tills data har hämtats (eller delvis).

* Fördröj data inmatning genom att ange ett korrekt värde för **förskjutnings** parametern för hämtnings tid:

    Ange **tids förskjutnings** parametern för inmatningen för datafeeden för att försena inmatningen tills data är fullständigt förberett. Detta kan vara användbart för vissa data källor som inte stöder transaktioner som Azure Table Storage. Mer information finns i [Avancerade inställningar](manage-data-feeds.md#advanced-settings) .

## <a name="add-a-data-feed-using-the-web-based-workspace"></a>Lägga till en datafeed med den webbaserade arbets ytan

När du har loggat in på mått Advisor-portalen och valt arbets ytan klickar du på **Kom igång**. Klicka på **Lägg till datafeed** på den vänstra menyn på arbets ytans huvud sida.

### <a name="add-connection-settings"></a>Lägg till anslutnings inställningar

Härnäst ska du ange en uppsättning parametrar för att ansluta till data källan för Time-serien. 
* Typ av **källa**: den typ av data källa där tids serie data lagras.
* **Granularitet**: intervallet mellan efterföljande data punkter i dina tids serie data. För närvarande stöder Metric Advisor: varje månad, varje vecka, varje dag, varje timme och anpassad. Det lägsta intervallet som anpassnings alternativet stöder är 60 sekunder.
  * **Sekunder**: antalet sekunder som *granularityName* ska *anpassas*.
* **Inmatnings data sedan (UTC)**: bas linje start tid för data inmatning. *startOffsetInSeconds* används ofta för att lägga till en förskjutning för att hjälpa till med data konsekvens.

Därefter måste du ange anslutnings informationen för data källan och de anpassade frågorna som används för att konvertera data till det nödvändiga schemat. Mer information om de andra fälten och hur du kopplar olika typer av data källor finns i [lägga till datafeeds från olika data källor](../data-feeds-from-different-sources.md).

[!INCLUDE [query requirements](../includes/query-requirements.md)]

### <a name="verify-and-get-schema"></a>Verifiera och hämta schema

När anslutnings strängen och frågesträngen har angetts väljer du **Verifiera och hämta schema** för att verifiera anslutningen och köra frågan för att hämta ditt data schema från data källan. Normalt tar det några sekunder beroende på anslutningen till din data källa. Om det finns ett fel i det här steget kontrollerar du att:

* Anslutnings strängen och frågan är korrekta.
* Din Metric Advisor-instans kan ansluta till data källan om det finns brand Väggs inställningar.

### <a name="schema-configuration"></a>Schema konfiguration

När data schemat har lästs in väljer du lämpliga fält.

Om tidsstämpeln för en data punkt utelämnas, använder Metric Advisor tidsstämpeln när data punkten matas in i stället. För varje datafeed kan du ange högst en kolumn som en tidsstämpel. Om du får ett meddelande om att en kolumn inte kan anges som en tidsstämpel, kontrollerar du din fråga eller data källa och om det finns flera tidsstämplar i frågeresultatet – inte bara i för hands versions data. När du utför data inmatningen kan mått Advisor bara förbruka ett segment (till exempel en dag, en timme-enligt granularitet) för tids serie data från den aktuella källan varje gång.

|Urval  |Beskrivning  |Kommentarer  |
|---------|---------|---------|
| **Visnings namn** | Namn som ska visas i din arbets yta i stället för det ursprungliga kolumn namnet. | |
|**Tidsstämpel**     | Tidsstämpeln för en data punkt. Om detta utelämnas använder Metric Advisor tidsstämpeln när data punkten matas in i stället. För varje datafeed kan du ange högst en kolumn som tidstämpel.        | Valfritt. Ska anges med högst en kolumn. Om du får en **kolumn som inte kan anges som tidsstämpel** -fel, kontrollerar du frågan eller data källan för dubbla tidsstämplar.      |
|**Mått**     |  De numeriska värdena i datafeeden. För varje datafeed kan du ange flera mått, men minst en kolumn ska vara markerad som mått.        | Måste anges med minst en kolumn.        |
|**Dimension**     | Kategoriska-värden. En kombination av olika värden identifierar en viss tids serie för en dimension, till exempel: land, språk, klient organisation. Du kan välja noll eller flera kolumner som dimensioner. Obs: var försiktig när du väljer en kolumn som inte är en sträng som en dimension. | Valfritt.        |
|**Ignorera**     | Ignorera den markerade kolumnen.        | Valfritt. Se nedanstående text.       |

Om du vill ignorera kolumner rekommenderar vi att du uppdaterar din fråga eller data källa för att undanta dessa kolumner. Du kan också ignorera kolumner med **Ignorera kolumner** och sedan **Ignorera** de angivna kolumnerna. Om en kolumn ska vara en dimension och anges felvärdet som *ignorerad* kan Metric Advisor komma att mata in ofullständiga data. Anta till exempel att data från din fråga är följande:

| Rad-ID | Timestamp | Land | Språk | Inkomst |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | Kina | ZH-CN | 10000 |
| 2 | 2019/11/10 | Kina | EN-US | 1000 |
| 3 | 2019/11/10 | USA | ZH-CN | 12000 |
| 4 | 2019/11/11 | USA | EN-US | 23000 |
| ... | ...| ... | ... | ... |

Om *land* är en dimension och ett *språk* anges som *ignorerat*, kommer de första och andra raderna ha samma dimensioner. Mått Advisor använder godtyckligt ett värde från de två raderna. Mått Advisor kommer inte att aggregera raderna i det här fallet.

### <a name="automatic-roll-up-settings"></a>Automatiska sammanslagnings inställningar

> [!IMPORTANT]
> Om du vill aktivera rotor Saks analys och andra diagnostiska funktioner måste du konfigurera **inställningarna för automatisk sammanslagning** . När den är aktive rad går det inte att ändra inställningarna för automatisk sammanslagning.

Mått Advisor kan automatiskt utföra Aggregation (till exempel SUM, MAX, MIN) för varje dimension under inmatningen, och skapar sedan en hierarki som ska användas i grund ärende analys och andra diagnostiska funktioner. 

Beakta följande scenarier:

* *Jag behöver inte inkludera sammanslagnings analys för mina data.*

    Du behöver inte använda klassificering av mått rådgivare.

* *Mina data har redan samlats in och dimension svärdet representeras av: NULL eller tomt (standard), endast NULL, andra.*

    Det här alternativet innebär att Metric Advisor inte behöver samla in data eftersom raderna redan har Summer ATS. Om du till exempel väljer *endast null* visas den andra data raden i exemplet nedan som en agg regering av alla länder och språk *en-US*. den fjärde data raden som har ett tomt värde för *landet* visas dock som en vanlig rad som kan indikera ofullständiga data.
    
    | Land | Språk | Inkomst |
    |---------|----------|--------|
    | Kina   | ZH-CN    | 10000  |
    | HA  | EN-US    | 999999 |
    | USA      | EN-US    | 12000  |
    |         | EN-US    | 5000   |

* *Jag behöver mått rådgivare för att samla in mina data genom att beräkna sum/max/min/genomsnitt/antal och representera det med <some string>*

    Vissa data källor som Cosmos DB eller Azure Blob Storage stöder inte vissa beräkningar som *Group by* eller *Cube*. Mått Advisor tillhandahåller alternativet samla in för att automatiskt generera en datakub under inmatningen.
    Det här alternativet innebär att du behöver klassificerings Advisor för att beräkna sammanslagningen med den algoritm som du har valt och använda den angivna strängen för att representera sammanfattningen i mått Advisor. Detta ändrar inte data källan.
    Anta till exempel att du har en uppsättning tids serier som står för försäljnings mått med dimensionen (land, region). För en specifik tidsstämpel kan det se ut så här:


    | Country       | Region           | Sales |
    |---------------|------------------|-------|
    | Kanada        | Alberta          | 100   |
    | Kanada        | British Columbia | 500   |
    | USA | Montana          | 100   |


    När du har aktiverat automatisk sammanslagning med *Sum* beräknar mått Advisor dimensions kombinationerna och summerar måtten under data inmatningen. Resultatet kan vara:

    | Country       | Region           | Sales |
    | ------------ | --------------- | ---- |
    | Kanada        | Alberta          | 100   |
    | NULL          | Alberta          | 100   |
    | Kanada        | British Columbia | 500   |
    | NULL          | British Columbia | 500   |
    | USA | Montana          | 100   |
    | NULL          | Montana          | 100   |
    | NULL          | NULL             | 700   |
    | Kanada        | NULL             | 600   |
    | USA | NULL             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` innebär summan av försäljningen i Kanada (alla regioner) är 600.

    Följande är omvandlingen i SQL-språk.

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    Tänk på följande innan du använder funktionen för automatisk sammanslagning:

    * Om du vill använda *Sum* för att aggregera dina data kontrollerar du att måtten är additiva i varje dimension. Här följer några exempel på *icke-additiva* mått:
      * Bråkbaserade mått. Detta inkluderar grad, procent och så vidare. Till exempel bör du inte lägga till arbetslöshets frekvensen för varje tillstånd för att beräkna arbetslöshets graden för hela landet.
      * Överlappa i dimension. Till exempel bör du inte lägga till antalet personer i varje sport för att beräkna antalet personer som gillar sport, eftersom det finns en överlappning mellan dem, men en person kan likna flera sporter.
    * För att säkerställa hälsan för hela systemet är kubens storlek begränsad. Gränsen är för närvarande 1 000 000. Om dina data överskrider den gränsen går det inte att mata in för den tidsstämpeln.

## <a name="advanced-settings"></a>Avancerade inställningar

Det finns flera avancerade inställningar för att göra det möjligt att mata in data på ett anpassat sätt, t. ex. genom att ange offset-förskjutning eller samtidighet. Mer information finns i avsnittet [Avancerade inställningar](manage-data-feeds.md#advanced-settings) i artikeln data flöde hantering.

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>Ange ett namn för datafeeden och kontrol lera inmatnings förloppet
 
Ange ett eget namn för datafeeden, som visas i arbets ytan. Klicka sedan på **Skicka**. På sidan information om data feed kan du använda förlopps indikatorn för inmatningar för att visa statusinformation.

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="Förlopps indikator för inmatning" lightbox="../media/datafeeds/ingestion-progress.png":::


Så här kontrollerar du information om inläsnings problem: 

1. Klicka på **Visa information**.
2. Klicka på **status** och välj sedan **misslyckades** eller **fel**.
3. Hovra över en misslyckad inmatning och visa det informations meddelande som visas.

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="Kontrol lera misslyckad inmatning":::

Status för *misslyckad* innebär att inmatningen för data källan kommer att göras senare.
En *fel* status indikerar att måtten Advisor inte försöker igen för data källan. Om du vill läsa in data på nytt måste du utlösa en bakfyllning/Läs in manuellt.

Du kan också läsa in förloppet för en inmatning igen genom att klicka på **uppdatera förloppet**. När data inmatningen är klar kan du klicka på mått och kontrol lera resultat för avvikelse identifiering.

## <a name="next-steps"></a>Nästa steg
- [Hantera dina data matningar](manage-data-feeds.md)
- [Konfigurationer för olika data källor](../data-feeds-from-different-sources.md)
- [Konfigurera mått och finjustera konfiguration för identifiering](configure-metrics.md)
