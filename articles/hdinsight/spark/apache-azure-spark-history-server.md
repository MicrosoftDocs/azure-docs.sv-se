---
title: Använd de utökade funktionerna på Apache Spark historik Server för att felsöka appar – Azure HDInsight
description: Använd de utökade funktionerna på Apache Spark historik Server för att felsöka och diagnostisera Spark-program – Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 1d53d9e9ee2a7fa6588ea0993b3bebdb2a287351
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98941053"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Använd de utökade funktionerna i Apache Spark historik servern för att felsöka och diagnostisera Spark-program

Den här artikeln visar hur du använder de utökade funktionerna i Apache Spark historik servern för att felsöka och diagnostisera slutförda eller aktiva Spark-program. Tillägget innehåller fliken **data** , fliken **diagram** och fliken **diagnos** . På fliken **data** kan du kontrol lera indata och utdata för Spark-jobbet. På fliken **diagram** kan du kontrol lera data flödet och spela upp jobb diagrammet. På fliken **diagnos** kan du referera till **data skevning**, **tids skevning** och **användnings analys** funktioner för utförar.

## <a name="get-access-to-the-spark-history-server"></a>Få åtkomst till Spark-historik servern

Spark-historik servern är webb gränssnittet för slutförd och körning av Spark-program. Du kan öppna den antingen från Azure Portal eller från en URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Öppna webb gränssnittet för Spark historik Server från Azure Portal

1. Öppna Spark-klustret från [Azure Portal](https://portal.azure.com/). Mer information finns i [lista och Visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Välj **Spark historik Server** från **kluster instrument paneler**. När du uppmanas till det anger du administratörs behörighet för Spark-klustret.

    ![Starta Spark historik servern från Azure Portal.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark-historik Server")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Öppna webb gränssnittet för Spark historik server med URL

Öppna Spark historik servern genom att bläddra till `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` , där **kluster** namn är namnet på ditt Spark-kluster.

Spark historik serverns webb gränssnitt kan se ut ungefär som den här bilden:

![Server sidan Spark-historik.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Använd fliken data på servern för Spark-historik

Välj jobb-ID och välj sedan **data** på verktygs menyn för att Visa datavyn.

+ Granska **indata**, **utdata** och **tabell åtgärder** genom att välja de enskilda flikarna.

    ![Fliken data på sidan data för Spark-program.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Kopiera alla rader genom att välja knappen **Kopiera** .

    ![Kopiera data på sidan Spark-program.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Spara alla data som en. CSV-fil genom att välja **CSV** -knappen.

    ![Spara data som en. CSV-fil från sidan data för Spark-program.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Sök i data genom att ange nyckelord i **Sök** fältet. Sök resultatet visas omedelbart.

    ![Sök efter data på sidan data för Spark-program.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Välj kolumn rubriken för att sortera tabellen. Välj plus tecknet för att expandera en rad för att visa mer information. Klicka på minus tecknet för att komprimera en rad.

    ![Data tabellen på sidan data för Spark-program.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Hämta en enda fil genom att välja knappen **del nedladdning** till höger. Den valda filen kommer att hämtas lokalt. Om filen inte finns längre öppnas en ny flik som visar fel meddelandena.

    ![Data hämtnings raden på sidan data för Spark-program.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Kopiera en fullständig sökväg eller en relativ sökväg genom att välja antingen alternativet **Kopiera fullständig sökväg** eller **Kopiera relativ sökväg** , som expanderar från nedladdnings menyn. För Azure Data Lake Storage filer väljer du **Öppna i Azure Storage Explorer** för att starta Azure Storage Explorer och letar upp mappen efter inloggning.

    ![Kopiera alternativ för fullständig sökväg och kopiera relativa sökvägar på sidan data för Spark-program.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Om det finns för många rader att visa på en enda sida, väljer du sid numren längst ned i tabellen för att navigera.

    ![Sid nummer på sidan data för Spark-program.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ För mer information, Hovra över eller Välj frågetecknet bredvid **data för Spark-programmet** för att Visa knapp beskrivningen.

    ![Hämta mer information från sidan data för Spark-program.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Om du vill skicka feedback om problem väljer du **ge oss feedback**.

    ![Lämna feedback från sidan data för Spark-program.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Använd fliken diagram på servern för Spark-historik

+ Välj jobb-ID och välj sedan **diagram** på verktygs menyn för att visa jobb diagrammet. Som standard visas alla jobb i diagrammet. Filtrera resultaten med hjälp av list menyn för **jobb-ID** .

    ![List rutan jobb-ID på sidan Spark-program & jobb diagram.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **Förloppet** är markerat som standard. Kontrol lera data flödet genom att välja **Läs** eller **Skriv** i den nedrullningsbara menyn **Visa** .

    ![Kontrol lera data flödet på sidan Spark-program & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ Bakgrunds färgen för varje aktivitet motsvarar en värme karta.

   ![Värme kartan på sidan Spark-program & jobb diagram.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Färg |Beskrivning |
    |---|---|
    |Green|Jobbet har slutförts.|
    |Orange|Uppgiften misslyckades, men detta påverkar inte det slutliga resultatet av jobbet. Dessa aktiviteter har dubbletter eller nya försök som kan utföras senare.|
    |Blue|Uppgiften körs.|
    |Vit|Uppgiften väntar på att köras eller så har fasen hoppats över.|
    |Red|Uppgiften misslyckades.|

     ![Köra en aktivitet på sidan Spark-program & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     De överhoppade faserna visas i vitt.
    ![En aktivitet som hoppades över på sidan Spark-program & jobb.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![En misslyckad aktivitet på sidan för Spark-programmets & jobb.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > Uppspelning är tillgängligt för slutförda jobb. Klicka på **uppspelnings** knappen för att spela upp jobbet igen. Stoppa jobbet när du vill genom att klicka på stopp knappen. När ett jobb spelas upp visas statusen efter färg i varje aktivitet. Uppspelning stöds inte för ofullständiga jobb.

+ Rulla för att zooma in eller ut i jobb diagrammet, eller Välj **Zooma för att passa** för att få plats på skärmen.

    ![Välj zooma för att få plats på sidan Spark-program & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ När aktiviteterna inte fungerar, Hovra över graf-noden för att se knapp beskrivningen och välj sedan scenen för att öppna den på en ny sida.

    ![Visa knapp beskrivningen på sidan för Spark-programmets & jobb diagram.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ På sidan Spark-program & jobb diagram visas knapp beskrivningar och små ikoner om aktiviteterna uppfyller följande villkor:
  + Data skevning: data Läs storlek > genomsnittlig data läsnings storlek för alla aktiviteter i det här steget * 2 *och* dataens läs storlek > 10 MB.
  + Tids skevning: körnings tid > genomsnittlig körnings tid för alla aktiviteter i det här steget * 2 *och* körnings tiden > 2 minuter.

    ![Ikonen skevad aktivitet på sidan för att & jobb diagram på Spark-programmet.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ I noden jobb diagram visas följande information om varje steg:
  + ID
  + Namn eller beskrivning
  + Totalt aktivitets nummer
  + Lästa data: summan av storleken på indata och den blandade Läs storleken
  + Data skrivning: summan av storleken på utdata och blanda Skriv storleken
  + Körnings tid: tiden mellan start tiden för det första försöket och slut för ande tiden för det senaste försöket
  + Radantal: summan av inmatnings poster, utgående poster, blanda Läs poster och blanda Skriv poster
  + Förlopp

    > [!NOTE]  
    > Som standard visar noden jobb diagram information från det senaste försöket i varje steg (utom för körnings tid för fas). Men under uppspelningen visas i noden jobb diagram information om varje försök.

    > [!NOTE]  
    > För data läsning och data skrivnings storlekar använder vi 1 MB = 1000 KB = 1000 * 1000 byte.

+ Skicka feedback om problem genom att välja **ge oss feedback**.

    ![Feedback-alternativet på sidan Spark-program & jobb diagram.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Använd fliken diagnos på servern för Spark-historik

Välj jobb-ID och välj sedan **diagnos** på verktygs menyn för att visa vyn jobb diagnos. Fliken **diagnos** innehåller **data skevning**, **tids skevning** och **användnings analys av utförar**.

+ Granska **data skevningen**, **tids skevningen** och **utförar användnings analys** genom att välja flikarna.

    ![Fliken Data skevning på fliken diagnostik.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Data skevning

Välj fliken **data skevning** . Motsvarande skevade uppgifter visas baserat på de angivna parametrarna.

#### <a name="specify-parameters"></a>Ange parametrar

I avsnittet **Ange parametrar** visas parametrarna, som används för att identifiera data skevning. Standard regeln är: den lästa aktivitets informationen är större än tre gånger i Genomsnittligt aktivitets data och aktivitets data är större än 10 MB. Om du vill definiera en egen regel för skevade uppgifter kan du välja parametrar. Den **skevade fasen** och **skeva diagram** avsnitten uppdateras enligt detta.

#### <a name="skewed-stage"></a>Skevad fas

I avsnittet **skevat Stadium** visas de steg som har skevade uppgifter som uppfyller de angivna kriterierna. Om det finns fler än en skevad aktivitet i ett stadium visar avsnittet **skevad fas** endast den mest sneda aktiviteten (det vill säga de största data för data förvrängning).

![Större visning av fliken Data skevning på fliken diagnostik.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Skeva diagram

När du väljer en rad i tabellen **skeva steg** , visar **skev diagrammet** mer information om aktivitets distribution baserat på data Läs-och körnings tid. De sneda aktiviteterna markeras i rött och normala aktiviteter markeras i blått. För prestanda överväganden visar diagrammet upp till 100 exempel aktiviteter. Uppgifts informationen visas i den nedre högra panelen.

![Diagrammet skeva för steg 10 i Spark-ANVÄNDARGRÄNSSNITTET.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tids skevning

På fliken **tids skevning** visas skevade uppgifter utifrån aktivitetens körnings tid.

#### <a name="specify-parameters"></a>Ange parametrar

I avsnittet **Ange parametrar** visas parametrarna, som används för att identifiera tids skevning. Standard regeln är: aktivitetens körnings tid är större än tre gånger i genomsnittlig körnings tid och körnings tiden för aktiviteten är mer än 30 sekunder. Du kan ändra parametrarna utifrån dina behov. Det **sneda steget** och **skeva diagrammet** visar motsvarande steg och aktivitets information, precis som på fliken **dataskevning** .

När du väljer **tids skevning** visas det filtrerade resultatet i avsnittet **skevad fas** enligt de parametrar som anges i avsnittet **Ange parametrar** . När du väljer ett objekt i avsnittet **skevat Stadium** , skapas motsvarande diagram i det tredje avsnittet och aktivitets informationen visas i den nedre högra panelen.

![Fliken tids skevning på fliken diagnostik.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Diagram över användnings analys av utförar

I **användnings diagrammet utförar** visas jobbets faktiska utförar-allokering och körnings status.  

När du väljer **användnings analys av utförar**, skapas fyra olika kurvor om utförar-användning: **allokerade körningar**, **köra körningar**, **inaktiva körningar** och **högst utförar-instanser**. Varje **utförar tillagd** eller **utförar borttagen** händelse ökar eller minskar de allokerade körningarna. Du kan kontrol lera **händelsens tids linje** på fliken **jobb** för fler jämförelser.

![Fliken användnings analys av utförar på fliken diagnos.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Välj färg ikonen för att markera eller avmarkera motsvarande innehåll i alla utkast.

 ![Välj diagrammet på fliken användnings analys av utförar.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Vanliga frågor

### <a name="how-do-i-revert-to-the-community-version"></a>Hur gör jag för att återgå till community-versionen?

Utför följande steg för att återgå till community-versionen.

1. Öppna klustret i Ambari.
1. Navigera till **Spark2**-  >  **konfigurationer**.
1. Välj **anpassade spark2 – standardvärden**.
1. Välj **Lägg till egenskap...**.
1. Lägg till **Spark. UI. förbättring. enabled = false** och spara den sedan.
1. Egenskapen anges till **false** nu.
1. Välj **Spara** för att spara konfigurationen.

    ![Inaktivera en funktion i Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Välj **Spark2** i den vänstra panelen. Välj sedan **Spark2 historik Server** på fliken **Sammanfattning** .

    ![Vyn Sammanfattning i Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Starta om Spark History-servern genom att klicka på knappen **Starta** till höger om **Spark2 historik Server** och sedan välja **starta om** på den nedrullningsbara menyn.

    ![Starta om Spark historik servern i Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Uppdatera Server webb gränssnittet för Spark-historiken. Den kommer att återgå till community-versionen.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Hur gör jag för att du ladda upp en spark historik Server-händelse för att rapportera den som ett problem?

Om du stöter på ett fel i Spark historik Server ska du utföra följande steg för att rapportera händelsen.

1. Ladda ned händelsen genom att välja **Hämta** i webb gränssnittet för Spark-historiken Server.

    ![Ladda ned händelsen i Server gränssnittet för Spark-historiken.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Välj **ge oss feedback** på sidan **Spark-program & Job Graph** .

    ![Ge feedback på sidan Spark-program & jobb diagram](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Ange rubriken och en beskrivning av felet. Dra sedan. zip-filen till fältet redigera och välj **Skicka nytt ärende**.

    ![Ladda upp och skicka ett nytt ärende.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Hur gör jag för att uppgraderar du en. jar-fil i ett snabb korrigerings scenario?

Om du vill uppgradera med en snabb korrigering använder du följande skript, som kommer att uppgraderas `spark-enhancement.jar*` .

**upgrade_spark_enhancement. sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>Användning

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Exempel

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Använd bash-filen från Azure Portal

1. Starta [Azure Portal](https://ms.portal.azure.com)och välj sedan klustret.
2. Slutför en [skript åtgärd](../hdinsight-hadoop-customize-cluster-linux.md) med följande parametrar.

    |Egenskap |Värde |
    |---|---|
    |Skript typ|– Anpassad|
    |Name|UpgradeJar|
    |Bash-skript-URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Node-typ (er)|Head, Worker|
    |Parametrar|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Azure Portal skicka skript åtgärd](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Kända problem

+ För närvarande fungerar Spark-historik servern bara för Spark 2,3 och 2,4.

+ Indata och utdata som använder RDD visas inte på fliken **data** .

## <a name="next-steps"></a>Nästa steg

+ [Hantera resurser för ett Apache Spark kluster i HDInsight](apache-spark-resource-manager.md)
+ [Konfigurera Apache Spark-inställningar](apache-spark-settings.md)

## <a name="suggestions"></a>Förslag

Skicka ett e-postmeddelande till () om du har någon feedback eller om du kommer över eventuella problem med det här verktyget [hdivstool@microsoft.com](mailto:hdivstool@microsoft.com) .
