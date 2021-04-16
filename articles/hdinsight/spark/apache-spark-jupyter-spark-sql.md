---
title: 'Snabbstart: Skapa Apache Spark kluster med hjälp av mall – Azure HDInsight'
description: Den här snabbstarten visar hur du använder Resource Manager för att skapa ett Apache Spark kluster i Azure HDInsight och köra en Spark SQL-fråga.
ms.date: 03/13/2020
ms.topic: quickstart
ms.service: hdinsight
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6d3f8c3313d9e6be38acdbecb683899ea8dd5376
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538009"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-arm-template"></a>Snabbstart: Skapa Apache Spark kluster i Azure HDInsight arm-mall

I den här snabbstarten använder du en Azure Resource Manager (ARM-mall) för att skapa [ett Apache Spark](./apache-spark-overview.md) kluster i Azure HDInsight. Sedan skapar du en Jupyter Notebook fil och använder den för att köra Spark SQL-frågor mot Apache Hive tabeller. Azure HDInsight är en hanterad analystjänst med fullständigt spektrum med öppen källkod för företag. Det Apache Spark ramverket för HDInsight möjliggör snabb dataanalys och klusterberäkning med hjälp av minnesinaktiv bearbetning. Jupyter Notebook kan du interagera med dina data, kombinera kod med markdown-text och göra enkla visualiseringar.

Om du använder flera kluster tillsammans bör du skapa ett virtuellt nätverk, och om du använder ett Spark-kluster bör du också använda Hive Warehouse Connector. Mer information finns i [Planera ett virtuellt nätverk för Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) och Integrera Apache Spark och Apache Hive med [Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

* [Microsoft.Storage/storageAccounts:](/azure/templates/microsoft.storage/storageaccounts)skapa ett Azure Storage konto.
* [Microsoft.HDInsight/cluster:](/azure/templates/microsoft.hdinsight/clusters)skapa ett HDInsight-kluster.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj knappen **Distribuera till Azure** nedan för att logga in på Azure och öppna ARM-mallen.

    [:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Ange eller välj följande värden:

    |Egenskap |Beskrivning |
    |---|---|
    |Prenumeration|I listrutan väljer du den Azure-prenumeration som används för klustret.|
    |Resursgrupp|Välj din befintliga resursgrupp i listrutan eller välj **Skapa ny.**|
    |Location|Värdet kommer att fyllas i automatiskt med den plats som används för resursgruppen.|
    |Klusternamn|Ange ett globalt unikt namn. Använd endast gemener och siffror för den här mallen.|
    |Användarnamn för klusterinloggning|Ange användarnamnet, standard är **admin**.|
    |Lösenord för klusterinloggning|Ange ett lösenord. Lösenordet måste innehålla minst 10 tecken och måste innehålla minst en siffra, en versal och en gemen, ett icke-alfanumeriskt tecken (förutom tecknen " " ' ). |
    |SSH-användarnamn|Ange användarnamnet, standard är **sshuser**|
    |SSH-lösenord|Ange lösenordet.|

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png " alt-text="Skapa Spark-kluster i HDInsight med Azure Resource Manager mall" border="true":::

1. Granska **VILLKOREN.** Välj sedan **Jag godkänner villkoren som anges ovan och** sedan **Köp**. Du får ett meddelande om att distributionen pågår. Det tar cirka 20 minuter att skapa ett kluster.

Om du får problem med att skapa HDInsight-kluster kan det vara så att du inte har rätt behörighet för att göra det. Mer information finns i [åtkomstkravkontrollen](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När klustret har skapats får du meddelandet **Distributionen lyckades med** länken **Gå till** resurs. På sidan Resursgrupp visas en lista över ditt nya HDInsight-kluster och standardlagringsutrymmet som är associerat till klustret. Varje kluster har [en Azure Storage,](../hdinsight-hadoop-use-blob-storage.md) [ett Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)eller ett [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) beroende. Det kallas för standardlagringskontot. HDInsight-klustret och dess standardlagringskonto måste finnas i samma Azure-region. Om du tar bort kluster tas inte lagringskontots beroende bort. Det kallas för standardlagringskontot. HDInsight-klustret och dess standardlagringskonto måste finnas i samma Azure-region. Lagringskontot tas inte bort om du tar bort kluster.

## <a name="create-a-jupyter-notebook-file"></a>Skapa en Jupyter Notebook-fil

[Jupyter Notebook är](https://jupyter.org/) en interaktiv notebook-miljö som stöder olika programmeringsspråk. Du kan använda en Jupyter Notebook för att interagera med dina data, kombinera kod med markdown-text och utföra enkla visualiseringar.

1. Öppna [Azure-portalen](https://portal.azure.com).

2. Välj **HDInsight-kluster** och välj sedan det kluster som du skapade.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png" alt-text="Öppna HDInsight-klustret i Azure Portal" border="true":::

3. I portalen går du till **avsnittet Klusterinstrumentpaneler** och väljer **Jupyter Notebook**. Ange autentiseringsuppgifterna för klustret om du uppmanas att göra det.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Öppna Jupyter Notebook för att köra en interaktiv Spark SQL-fråga" border="true":::

4. Välj **Ny**  >  **PySpark för** att skapa en notebook-dator.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Skapa en Jupyter Notebook för att köra en interaktiv Spark SQL-fråga" border="true":::

   En ny anteckningsbok skapas och öppnas med namnet Untitled(Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Köra Apache Spark SQL-uttryck

SQL (Structured Query Language) är det vanligaste språket för frågor och transformering av data. Spark SQL fungerar som ett tillägg till Apache Spark för bearbetning av strukturerade data med den välbekanta SQL-syntaxen.

1. Verifiera att kerneln är klar. Kerneln är klar när du ser en tom cirkel bredvid kernelnamnet i den bärbara datorn. En fylld cirkel anger att kerneln är upptagen.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png " alt-text="Kernel status" border="true"::: alt-text="Kernel status" border="true":::

    När du startar den bärbara datorn för första gången utför kerneln några uppgifter i bakgrunden. Vänta tills kerneln är klar.

1. Klistra in följande kod i en tom cell och tryck sedan på **SKIFT+RETUR** för att köra koden. Kommandot listar Hive-tabellerna i klustret:

    ```sql
    %%sql
    SHOW TABLES
    ```

    När du använder en Jupyter Notebook-fil med ditt HDInsight-kluster får du en förinställd session som du kan använda för att köra `spark` Hive-frågor med Spark SQL. `%%sql` anger att Jupyter Notebook ska använda den förinställda sessionen `spark` när Hive-frågan ska köras. Frågan hämtar de översta 10 raderna från en Hive-tabell (**hivesampletable**) som medföljer alla HDInsight-kluster som standard. Första gången du skickar frågan skapar Jupyter ett Spark-program för anteckningsboken. Det tar ungefär 30 sekunder att slutföra. När Spark-programmet är klart körs frågan i ungefär en sekund och ger resultatet. Utdata ser ut så här:

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png " alt-text="Apache Hive fråga i HDInsight" border="true":::y i HDInsight" border="true":::

    Varje gång du kör en fråga i Jupyter visar fönsterrubriken i webbläsaren statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet.

1. Kör ytterligare en fråga för att visa data i `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Skärmen bör uppdateras så att frågeresultatet visas.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png " alt-text="Hive-frågeutdata i HDInsight" border="true"::: Insight" border="true":::

1. Välj **Stäng och stoppa** på anteckningsbokens **Arkiv**-meny. När du stänger notebook-datorn frigörs klusterresurserna, inklusive Spark-programmet.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även om det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Från Azure Portal du till klustret och väljer Ta **bort**.

:::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png " alt-text="Azure Portal bort ett kluster för HDInsight-kluster&quot;" border="true":::border="true":::

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. Genom att ta bort resursgruppen tar du bort både HDInsight-klustret och standardlagringskontot.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Apache Spark i HDInsight och kör en grundläggande Spark SQL-fråga. Gå vidare till nästa självstudie och lär dig hur du använder ett HDInsight-kluster för att köra interaktiva frågor på exempeldata.

> [!div class="nextstepaction"]
> [Köra interaktiva frågor i Apache Spark](./apache-spark-load-data-run-query.md)
