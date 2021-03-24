---
title: 'Snabb start: Apache Hadoop, Apache Hive & Azure HDInsight-portalen'
description: I den här snabb starten använder du Azure Portal för att skapa ett HDInsight Hadoop-kluster
keywords: hadoop komma igång, hadoop linux, hadoop Snabbstart, komma igång, hive hive-Snabbstart
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 92520be7e9d2bb83cb61856fe3df38487445ad9a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863895"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Snabb start: skapa Apache Hadoop kluster i Azure HDInsight med Azure Portal

I den här artikeln får du lära dig hur du skapar Apache Hadoop-kluster i HDInsight med Azure-portalen och sedan kör Apache Hive-jobb i HDInsight. De flesta Hadoop-jobb är batchjobb. Du skapar ett kluster, kör vissa jobb och tar sedan bort klustret. I den här artikeln utför du alla tre aktiviteterna. Djupgående förklaringar av tillgängliga konfigurationer finns i [Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur du använder portalen för att skapa kluster finns i [skapa kluster i portalen](../hdinsight-hadoop-create-linux-clusters-portal.md).

I den här snabbstarten använder du Azure Portal för att skapa ett HDInsight Hadoop-kluster. Du kan också skapa ett kluster med hjälp av [Azure Resource Manager-mallen](apache-hadoop-linux-tutorial-get-started.md).

HDInsight innehåller för närvarande [sju olika kluster typer](../hdinsight-overview.md#cluster-types-in-hdinsight). Varje typ av kluster har stöd för olika komponentuppsättningar. Samtliga klustertyper stöder Hive. En lista över komponenter som stöds och som hanteras i HDInsight finns i [Vad är nytt i de Apache Hadoop-klusterversioner som tillhandahålls av HDInsight?](../hdinsight-component-versioning.md)  

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-apache-hadoop-cluster"></a>Skapa ett Apache Hadoop-kluster

I det här avsnittet skapar du ett Hadoop-kluster i HDInsight med hjälp av Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **+ skapa en resurs** på den översta menyn.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png" alt-text="Skapa ett resurs-HDInsight-kluster" border="true":::

1. Välj **Analytics**  >  **Azure HDInsight** för att gå till sidan **skapa HDInsight-kluster** .

1. På fliken **grundläggande** anger du följande information:

   |Egenskap  |Beskrivning  |
   |---------|---------|
   |Prenumeration    |  I list rutan väljer du den Azure-prenumeration som används för klustret. |
   |Resursgrupp     | Välj din befintliga resurs grupp i list rutan eller Välj **Skapa ny**.|
   |Klusternamn   | Ange ett globalt unikt namn. Namnet kan bestå av upp till 59 tecken, inklusive bokstäver, siffror och bindestreck. Det första och sista tecknet i namnet får inte vara bindestreck. |
   |Region    | Välj en region där klustret skapas i list rutan.  Välj en plats närmare så får du bättre prestanda. |
   |Kluster typ| Välj **Välj kluster typ**. Välj sedan **Hadoop** som kluster typ.|
   |Version|Välj en **version** i list rutan. Använd standard versionen om du inte vet vad du ska välja.|
   |Användarnamn och lösenord för klusterinloggning    | Standard inloggnings namnet är **admin**. Lösen ordet måste bestå av minst 10 tecken och måste innehålla minst en siffra, en versal och en gemen bokstav, ett icke-alfanumeriskt tecken (förutom tecknen "" " \) . Se till att du **inte anger** vanliga lösenord som Pass@word1.|
   |Secure Shell (SSH)-användarnamn | Standardanvändarnamnet är **sshuser**.  Du kan ange ett annat namn som SSH-användarnamn. |
   |Använd lösen ord för kluster inloggning för SSH| Markera den här kryss rutan om du vill använda samma lösen ord för SSH-användare som det du angav för kluster inloggnings användaren.|

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png" alt-text="HDInsight Linux kom igång ger kluster grundläggande värden" border="true":::

   Välj **Nästa: lagrings >>** för att gå vidare till lagrings inställningarna.

1. Ange följande värden på fliken **lagring** :

   |Egenskap  |Beskrivning  |
   |---------|---------|
   |Primär lagringstyp|Använd standardvärdet **Azure Storage**.|
   |Urvalsmetod|Använd standardvärdet **Select i list**.|
   |Primärt lagringskonto|Använd List rutan för att välja ett befintligt lagrings konto eller Välj **Skapa nytt**. Om du skapar ett nytt konto måste namnet vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener|
   |Container|Använd det automatiskt ifyllda värdet.|

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png" alt-text="HDInsight Linux kom igång ger kluster lagrings värden" border="true":::

   Varje kluster har ett [Azure Storage konto](../hdinsight-hadoop-use-blob-storage.md), ett [Azure Data Lake gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)eller ett [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) beroende. Det kallas för standard lagrings kontot. HDInsight-kluster och dess standard lagrings konto måste samplaceras i samma Azure-region. Om du tar bort kluster tas inte lagrings kontot bort.

   Välj fliken **Granska + skapa** .

1. På fliken **Granska och skapa** kontrollerar du de värden som du valde i de föregående stegen.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png" alt-text="HDInsight Linux kom igång-kluster Sammanfattning" border="true":::

1. Välj **Skapa**. Det tar cirka 20 minuter att skapa ett kluster.

   När klustret har skapats visas en klusteröversiktssida i Azure Portal.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png" alt-text="HDInsight Linux komma igång klusterinställningar" border="true":::

## <a name="run-apache-hive-queries"></a>Köra Apache Hive-frågor

[Apache Hive](hdinsight-use-hive.md) är den populäraste komponenten som används i HDInsight. Det finns många sätt att köra Hive-jobb i HDInsight. I den här snabb starten använder du Ambari Hive-vyn från portalen. Andra metoder för att skicka Hive-jobb beskrivs i [Använda Hive-data i HDInsight](hdinsight-use-hive.md).

> [!NOTE]
> Apache Hive vy är inte tillgänglig i HDInsight 4,0.

1. Öppna Ambari genom att välja **Klusterinstrumentpanel** i föregående skärmbild.  Du kan också bläddra till  `https://ClusterName.azurehdinsight.net` var `ClusterName` är klustret du skapade i föregående avsnitt.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png" alt-text="HDInsight Linux kom igång-kluster instrument panel" border="true":::

2. Ange det Hadoop-användarnamn och -lösenord som du angav när du skapade klustret. Standardanvändarnamnet är **admin**.

3. Öppna **Hive-vy** så som det visas på följande skärmbild:

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png" alt-text="Välja Hive-vy från Ambari" border="true":::

4. På fliken **FRÅGA** klistrar du in följande HiveQL-instruktioner i kalkylbladet:

   ```sql
   SHOW TABLES;
   ```

    :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png" alt-text="HDInsight Hive Visa Frågeredigeraren" border="true":::

5. Välj **Kör**. Fliken **RESULTAT** visas under fliken **FRÅGA** och visar information om jobbet.

   När frågan har slutförts visas resultatet av åtgärden i fliken **fråga** . En tabell med namnet **hivesampletable** bör visas. Detta exempel på en Hive-tabell kommer med alla HDInsight-kluster.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png" alt-text="HDInsight Apache Hive Visa resultat" border="true":::

6. Upprepa steg 4 och 5 för att köra följande fråga:

   ```sql
   SELECT * FROM hivesampletable;
   ```

7. Du kan också spara frågans resultat. Välj menyknappen till höger och ange om du vill ladda ned resultatet som en CSV-fil eller lagra den på lagringskontot som är associerat till klustret.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png" alt-text="Spara resultatet av Apache Hive fråga" border="true":::

När du har slutfört ett Hive-jobb kan du [Exportera resultatet till Azure SQL Database eller SQL Server databas, men](apache-hadoop-use-sqoop-mac-linux.md)du kan också [visualisera resultaten med hjälp av Excel](apache-hadoop-connect-excel-power-query.md). Mer information om hur du använder Hive i HDInsight finns i [Använda Apache Hive och HiveQL med Apache Hadoop i HDInsight för att analysera ett exempel i en Apache log4j-fil](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabb starten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger mer än avgifterna för lagring, är det ekonomiskt klokt att ta bort kluster när de inte används.

> [!NOTE]  
> Om du *omedelbart* fortsätter till nästa artikel och lär dig hur du kör ETL-åtgärder med Hadoop i HDInsight, kanske du vill hålla klustret igång. Detta beror på att du i självstudien måste skapa ett Hadoop-kluster igen. Men om du inte går igenom nästa artikel direkt måste du ta bort klustret nu.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Ta bort klustret och/eller Storage-kontot av standardtyp

1. Gå tillbaka till webbläsarfliken där du har Azure-portalen. Du bör vara på översiktssidan för klustret. Om du endast vill ta bort klustret men behålla standardlagringskontot kan du klicka på **Ta bort**.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png" alt-text="Azure HDInsight ta bort kluster" border="true":::

2. Om du vill ta bort klustret och standardlagringskontot öppnar du resursgruppssidan genom att välja resursgruppens namn (markerat i föregående skärmbild).

3. Ta bort resursgruppen som innehåller klustret och standardlagringskontot genom att välja **Ta bort resursgrupp**. Tänk på att lagringskontot tas bort om du tar bort resursgruppen. Välj att bara ta bort klustret om du vill behålla Storage-kontot.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Linux-baserat HDInsight-kluster med hjälp av en Resource Manager-mall och hur du utför grundläggande Hive-frågor. I nästa artikel får du lära dig hur du utför en extraktions-, transformations- eller inläsningsåtgärd (ETL) med Hadoop på HDInsight.

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in data med interaktiv fråga på HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
