---
title: Använd den utökade Spark historik servern för att felsöka appar
description: Använd den utökade Spark historik servern för att felsöka och diagnostisera Spark-program i Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4471fed7b423fe05147db30afe57f6c845fe640e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670703"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Använd utökad Apache Spark historik Server för att felsöka och diagnostisera Apache Spark program

Den här artikeln innehåller rikt linjer för hur du använder den utökade Apache Spark historik servern för att felsöka och diagnostisera slutförda och köra Spark-program.

Tillägget innehåller fliken data, fliken Graph och fliken diagnostik. Använd fliken **data** för att kontrol lera indata och utdata för Spark-jobbet. På fliken **Graph** visas data flödet och uppspelning av jobb diagrammet. På fliken **diagnos** visas  **data skevning**, **tids skevning** och **användnings analys av utförar**.

## <a name="access-the-apache-spark-history-server"></a>Få åtkomst till Apache Spark-historikservern

Apache Spark historik Server är webb användar gränssnittet för slutförd och körning av Spark-program. Du kan öppna webb gränssnittet för Apache Spark historik Server från Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Öppna webb gränssnittet för Spark-historiken från Apache Spark-program-noden

1. Öppna [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Välj **övervaka** och välj sedan **Apache Spark program**.

    ![Välj övervaka och välj Spark-program.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Välj ett program och öppna sedan **logg frågan** genom att markera den.

    ![Öppna logg frågas fönster.](./media/apache-spark-history-server/open-application-window.png)

4. Välj **Spark historik Server**. sedan visas Server webb gränssnittet för Spark-historiken.

    ![Öppna Spark historik Server.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node&quot;></a>Öppna webb gränssnittet för Spark-historiken från datanoden

1. Från din Azure Synapse Studio-anteckningsbok väljer du **Spark historik Server** från cellen för att köra utdata för jobb eller från panelen status längst ned i Anteckningsbok-dokumentet. Välj **Sessionsinformation**.

   ![Starta Spark-historik Server 1](./media/apache-spark-history-server/launch-history-server2.png &quot;Starta Spark-historik Server")

2. Välj **Spark historik Server** på panelen bild ut.

   ![Starta Spark historik Server 2](./media/apache-spark-history-server/launch-history-server.png "Starta Spark-historik Server")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Utforska fliken data i Spark historik Server

Välj jobb-ID för det jobb som du vill visa. Välj sedan **data** på verktygs menyn för att hämta datavyn. I det här avsnittet visas hur du utför olika uppgifter på fliken data.

* Kontrol lera **indata**-, **utdata**-och **tabell åtgärder** genom att välja flikarna separat.

    ![Data för Spark-Programflikar](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Kopiera alla rader genom att välja **Kopiera**.

    ![Data för Spark-programkopia](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Spara alla data som CSV-fil genom att välja **CSV**.

    ![Data för Spark-program spara](./media/apache-spark-history-server/apache-spark-data-save.png)

* Sök genom att ange nyckelord i fälts **ökning**. Sök resultatet visas omedelbart.

    ![Data för Spark-Programsökning](./media/apache-spark-history-server/apache-spark-data-search.png)

* Välj kolumn rubriken för att sortera tabellen, Välj plus tecknet för att expandera en rad för att visa mer information eller Välj minus tecknet för att komprimera en rad.

    ![Data för Spark program tabell](./media/apache-spark-history-server/apache-spark-data-table.png)

* Hämta en enda fil genom att välja **delvis hämtning**. Den valda filen laddas ned till lokalt. Om filen inte finns längre visas en ny flik med ett fel meddelande.

    ![Data för hämtnings raden för Spark-program](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Om du vill kopiera en fullständig sökväg eller relativ sökväg väljer du alternativet **Kopiera fullständig sökväg** eller **Kopiera relativa sökvägar** som visas på den nedrullningsbara menyn. För Azure Data Lake Storage filer **öppnar du Azure Storage Explorer i** startar Azure Storage Explorer och letar upp mappen när du är inloggad.

    ![Data för Spark-programmets kopierings Sök väg](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Välj sid nummer nedanför tabellen för att navigera sidor när det finns för många rader att visa på en sida.

    ![Sidan data för Spark-program](./media/apache-spark-history-server/apache-spark-data-page.png)

* Hovra över frågetecknet bredvid **data** för att Visa knapp beskrivningen, eller Välj frågetecknet om du vill ha mer information.

    ![Data för Spark-program, mer information](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Skicka feedback med problem genom att välja **ge oss feedback**.

    ![Spark-diagram ge oss feedback igen](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Fliken diagram på Apache Spark historik Server

Välj jobb-ID för det jobb som du vill visa. Välj sedan **diagram** på menyn verktyg för att hämta vyn jobb diagram.

### <a name="overview"></a>Översikt

Du kan se en översikt över jobbet i diagrammet genererat jobb. Som standard visar diagrammet alla jobb. Du kan filtrera den här vyn efter **jobb-ID**.

![Jobb-ID för Spark-program och jobb diagram](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Skärm

Som standard är visnings **förloppet** markerat. Du kan kontrol lera data flödet genom att välja **läsa** eller **skriva** i list rutan **Visa** .

![Visning av Spark-program och jobb diagram](./media/apache-spark-history-server/sparkui-graph-display.png)

Graf-noden visar de färger som visas i termisk karta-förklaringen.

![Termisk karta för Spark-program och jobb diagram](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Uppspelning

Om du vill spela upp jobbet väljer du **uppspelning**. Du kan välja **stoppa** när som helst för att stoppa. Aktivitets färgerna visar olika status vid uppspelning:

|Färg|Innebörd|
|-|-|
|Green|Lyckades: jobbet har slutförts.|
|Orange|Nytt försök: instanser av aktiviteter som misslyckats men inte påverkar jobbets slut resultat. De här uppgifterna hade dubbla eller nya försök för instanser som kan lyckas senare.|
|Blue|Körs: aktiviteten körs.|
|Vit|Väntar eller hoppas över: aktiviteten väntar på att köras eller så har fasen hoppats över.|
|Red|Misslyckades: aktiviteten har misslyckats.|

Följande bild visar status färgerna grön, orange och blå.

![Färg exempel för Spark-program och jobb diagram, köra](./media/apache-spark-history-server/sparkui-graph-color-running.png)

Följande bild visar de gröna och vita status färgerna.

![Färg exempel för Spark-program och jobb diagram, hoppa över](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

Följande bild visar status färgerna röd och grön.

![Färg exemplet för Spark-program och jobb diagram misslyckades](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Uppspelning för varje jobb tillåts. Uppspelning stöds inte för ofullständiga jobb.

### <a name="zoom"></a>Zoom

Använd musen för att zooma in och ut i jobb diagrammet, eller Välj **Zooma för** att anpassas för att få plats på skärmen.

![Spark-program och jobb diagram anpassas till](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Knappbeskrivningar

Hovra över diagram-noden för att se knapp beskrivningen när det inte finns några misslyckade uppgifter och välj ett stadium för att öppna dess scen sida.

![Verktygs tips för Spark-program och jobb diagram](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

På fliken jobb diagram har stadier en knapp beskrivning och en liten ikon som visas om de har aktiviteter som uppfyller följande villkor:

|Villkor|Beskrivning|
|-|-|
|Data skevning|data Läs storlek > genomsnittlig läsnings storlek för alla aktiviteter i det här steget * 2 och dataens Läs storlek > 10 MB|
|Tids skevning|körnings tid > genomsnittlig körnings tid för alla aktiviteter i det här steget * 2 och körnings tiden > 2 minuter|
   
![Ikon för skevning av Spark-program och jobb diagram](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Beskrivning av graf-nod

I noden jobb diagram visas följande information om varje steg:

  * Identitet.
  * Namn eller beskrivning.
  * Totalt aktivitets nummer.
  * Lästa data: summan av storleken på indata och den blandade Läs storleken.
  * Data skrivning: summan av storlek och blandnings storlek för utdata.
  * Körnings tid: tiden mellan start tiden för det första försöket och slut för ande tiden för det senaste försöket.
  * Radantal: summan av inmatnings poster, utgående poster, blandar Läs poster och blanda Skriv poster.
  * Pågår.

    > [!NOTE]  
    > Som standard visar noden jobb diagram information från det senaste försöket för varje steg (förutom körnings tid för fas). Men under uppspelningen visar graf-noden information om varje försök.
    >  
    > Data storleken Read och Write är 1 MB = 1000 KB = 1000 * 1000 byte.

### <a name="provide-feedback"></a>Ge feedback

Skicka feedback med problem genom att välja **ge oss feedback**.

![Feedback om Spark-program och jobb diagram](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Utforska fliken diagnos i Apache Spark historik Server

Välj ett jobb-ID för att få åtkomst till fliken diagnos. Välj sedan **diagnos** på verktygs menyn för att hämta vyn jobb diagnos. Fliken diagnos innehåller **data skevning**, **tids skevning** och **användnings analys av utförar**.

Kontrol lera **data skevningen**, **tids skevningen** och **utförar användnings analys** genom att välja flikarna.

![SparkUI för att förvränga data igen](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Data skevning

När du väljer fliken **data skevning** visas motsvarande skevade uppgifter baserat på de angivna parametrarna.

* **Ange parametrar** – det första avsnittet visar parametrarna som används för att identifiera data skevning. Standard regeln är: lästa aktivitets data är större än tre gånger i Genomsnittligt aktivitets data, och uppgifts data är större än 10 MB. Om du vill definiera en egen regel för skevade uppgifter kan du välja parametrar. De **skevade stegen** för att skeva och **skeva char** -avsnitt uppdateras därefter.

* **Skevad fas** – det andra avsnittet visar steg, som har skevade uppgifter som uppfyller de villkor som anges ovan. Om det finns fler än en skevad aktivitet i ett stadium visar den skevade scen tabellen bara den mest sneda aktiviteten (till exempel de största data för data skevning).

    ![sparkui för att förvränga data](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Skeva diagram** – när en rad i tabellen skeva steg är markerad visar skev diagrammet mer information om aktivitets distribution baserat på data Läs-och körnings tid. De sneda aktiviteterna markeras i rött och normala aktiviteter markeras i blått. Diagrammet visar upp till 100 exempel aktiviteter och aktivitets informationen visas i den högra nedre panelen.

    ![sparkui skeva diagram för steg 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tids skevning

På fliken **tids skevning** visas skevade uppgifter utifrån aktivitetens körnings tid.

* **Ange parametrar** – det första avsnittet visar parametrarna som används för att identifiera tids skevning. Standard kriteriet för att identifiera tids skevning är: aktivitets körnings tiden är större än tre gånger i genomsnittlig körnings tid och uppgifts körnings tid är längre än 30 sekunder. Du kan ändra parametrarna utifrån dina behov. Det **sneda steget** och **skeva diagrammet** visar motsvarande steg och aktivitets information precis som fliken **dataskevning** ovan.

* Välj **tids skevning** och sedan visas det filtrerade resultatet i avsnittet **skevad fas** enligt parametrarna som anges i avsnittet **Ange parametrar**. Markera ett objekt i avsnittet **skevat Stadium** . därefter skapas motsvarande diagram i section3 och uppgifts informationen visas i den högra panelen.

    ![sparkui diagnos tid skeva avsnitt](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Användnings analys av utförar

Användnings diagrammet för utförar visualiserar Spark-jobbets utförar allokering och körnings status.  

1. Välj **utförar-användnings analys**, och sedan fyra typer av kurvor om utförar-användning är utkast, inklusive **allokerade körningar**, **körnings** körningar, **inaktiva körningar** och **högst utförar-instanser**. För allokerade körningar ökar eller minskar de allokerade körningarna med händelsen "utförar tillagt" eller "utförar borttaget". Du kan kontrol lera "händelsens tids linje" på fliken "jobb" för mer jämförelse.

   ![fliken sparkui-diagnostik](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Välj färg ikonen för att markera eller avmarkera motsvarande innehåll i alla utkast.

    ![sparkui diagnostiserar Välj diagram](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Kända problem

Indata/utdata-data med elastiska distribuerade data uppsättningar (RDD) visas inte i fliken data.

## <a name="next-steps"></a>Nästa steg

- [Azure Synapse Analytics](../overview-what-is.md)
- [Dokumentation om .NET för Apache Spark](/dotnet/spark)

