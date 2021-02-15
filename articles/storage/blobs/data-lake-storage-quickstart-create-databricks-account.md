---
title: 'Snabb start: analysera data i Azure Data Lake Storage Gen2 med Azure Databricks | Microsoft Docs'
description: Lär dig hur du kör ett Spark-jobb med Azure Databricks med hjälp av Azure-portalen och ett Azure Data Lake Storage Gen2-lagringskonto.
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 06/12/2020
ms.reviewer: jeking
ms.openlocfilehash: 0d712dc0ebe91ea8815adf235e02b8945e0dea84
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518869"
---
# <a name="quickstart-analyze-data-with-databricks"></a>Snabb start: analysera data med Databricks

I den här snabb starten kör du ett Apache Spark jobb med Azure Databricks för att utföra analyser på data som lagras i ett lagrings konto. Som en del av Spark-jobbet analyserar du prenumerationsdata för en radiokanal, så att du får insikter om kostnadsfri/betald användning baserat på demografiska data.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Ett lagrings konto som har funktionen hierarkiskt namn område aktive rad. Om du vill skapa ett, se [skapa ett lagrings konto som ska användas med Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

* Klient-ID, app-ID och lösen ord för ett Azure-tjänstens huvud namn med en tilldelad roll för **data deltagare i Storage BLOB**. [Skapa ett huvud namn för tjänsten](../../active-directory/develop/howto-create-service-principal-portal.md).

  > [!IMPORTANT]
  > Tilldela rollen i omfånget för Data Lake Storage Gen2 lagrings kontot. Du kan tilldela en roll till den överordnade resursgruppen eller prenumerationen, men du får behörighetsrelaterade fel tills de rolltilldelningarna propageras till lagringskontot.

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

I det här avsnittet skapar du en Azure Databricks-arbetsyta med Azure-portalen.

1. I Azure Portal väljer du **skapa en resurs**  >  **analys**  >  **Azure Databricks**.

    ![Databricks på Azure Portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks på Azure Portal")

2. Under **Azure Databricks-tjänst** anger du värden för att skapa en Databricks-arbetsyta.

    ![Skapa en Azure Databricks-arbetsyta](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Skapa en Azure Databricks-arbetsyta")

    Ange följande värden:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn på arbetsyta**     | Ange ett namn för Databricks-arbetsytan        |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../../azure-resource-manager/management/overview.md). |
    |**Plats**     | Välj **USA, västra 2**. Du kan välja en annan offentlig region om du vill.        |
    |**Prisnivå**     |  Välj mellan **Standard** och **Premium**. Mer information om de här nivåerna finns på [prissättningssidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Det tar några minuter att skapa kontot. Du kan övervaka åtgärdsstatusen i förloppsindikatorn längst upp.

4. Välj **Fäst på instrumentpanelen** och välj sedan **Skapa**.

## <a name="create-a-spark-cluster-in-databricks"></a>Skapa ett Spark-kluster i Databricks

1. I Azure-portalen går du till Databricks-arbetsytan som du skapade. Välj sedan **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. Välj **nytt**  >  **kluster** från portalen.

    ![Databricks på Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

    Fyll i värden för följande fält och godkänn standardvärdena för de andra fälten:

    - Ange ett namn för klustret.
     
    - Se till att markera kryssrutan **Avsluta efter 120 minuters inaktivitet**. Ange en varaktighet (i minuter) för att avsluta klustret om klustret inte används.

4. Välj **skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

Mer information om att skapa kluster finns i [Skapa ett Spark-kluster i Azure Databricks](/azure/databricks/clusters/create).

## <a name="create-notebook"></a>Skapa antecknings bok

I det här avsnittet skapar du en anteckningsbok på Azure Databricks-arbetsytan och kör sedan kodfragment för att konfigurera lagringskontot.

1. Gå till arbetsytan Azure Databricks som du skapat i [Azure-portalen](https://portal.azure.com). Välj sedan **Starta arbetsyta**.

2. Välj **Arbetsyta** i det vänstra fönstret. I listrutan **Arbetsyta** väljer du **Skapa** > **Anteckningsbok**.

    ![Skärm bild som visar hur du skapar en antecknings bok i Databricks och markerar meny alternativet Skapa > Notebook.](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Skapa antecknings bok i Databricks")

3. Ge anteckningsboken ett namn i dialogrutan **Skapa anteckningsbok**. Välj **Scala** som språk och välj sedan det Spark-kluster som du skapade tidigare.

    ![Skapa antecknings bok i Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Skapa antecknings bok i Databricks")

    Välj **Skapa**.

4. Kopiera och klistra in följande kodblock i den första cellen, men kör inte den här koden än.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```
5. I det här kodblocket ersätter du platshållarvärdena `storage-account-name`, `appID`, `password` och `tenant-id` i det här kodblocket med de värden som du hämtade när du skapade tjänsthuvudnamnet. Ange `container-name` plats hållarens värde till det namn som du vill ge behållaren.

6. Tryck på **SKIFT + RETUR** för att köra koden i det här blocket.

## <a name="ingest-sample-data"></a>Mata in exempeldata

Innan du börjar med det här avsnittet måste du slutföra följande krav:

Ange följande kod i en cell i en arbetsbok:

```bash
%sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json
```

Kör koden genom att trycka på **SKIFT + RETUR** i cellen.

I en ny cell nedanför denna anger du följande kod, och ersätter värdena inom hakparentes med samma värden som du använde tidigare:

```python
dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")
```

Kör koden genom att trycka på **SKIFT + RETUR** i cellen.

## <a name="run-a-spark-sql-job"></a>Köra ett Spark SQL-jobb

Utför följande åtgärder för att köra ett Spark SQL-jobb på data.

1. Kör en SQL-instruktion för att skapa en temporär tabell med data från exempel-JSON-datafilen **small_radio_json.json**. I följande kodfragment ersätter du platshållarens värden med din containers namn och namnet på ditt lagringskonto. Använd anteckningsboken du skapade tidigare för att klistra in följande kodfragment i en kodcell och tryck sedan på SKIFT+RETUR.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json"
    )
    ```

    När kommandot har slutförts har du alla data från JSON-filen som en tabell i Databricks-klustret.

    Med det magiska språkkommandot `%sql` kan du köra en SQL-kod från anteckningsboken även om anteckningsboken är av en annan typ. Mer information finns i [Mixing languages in a notebook](/azure/databricks/notebooks/notebooks-use#mix-languages) (Blanda språk i en anteckningsbok).

2. Nu ska vi titta på en ögonblicksbild av JSON-exempeldata för att bättre förstå den fråga som körs. Klistra in följande kodfragment i kodcellen och tryck på **SKIFT + RETUR**.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

3. Du ser en tabellvy som i följande skärmbild (endast vissa kolumner visas):

    ![Exempel på JSON-data](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Exempel på JSON-data")

    Bland annat fångar exempeldata in könet på en radiokanals målgrupp (kolumnnamn, **kön**) och om deras prenumeration är kostnadsfri eller om den betalas (kolumnnamn, **nivå**).

4. Nu skapar du en visuell representation av dessa data för att visa hur många användare av varje kön som har kostnadsfria konton och hur många som är betalande prenumeranter. Längst ned i tabellvyn klickar du på ikonen **Stapeldiagram** och sedan på **Ritalternativ**.

    ![Skapa liggande stapeldiagram](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Skapa liggande stapeldiagram")

5. I **Anpassa ritning** drar och släpper du värden enligt skärmbilden.

    ![Skärm bild som visar skärmen anpassa rityta och de värden som du kan dra och släppa.](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Anpassa stapeldiagram")

    - Ställ in **Nycklar** på **kön**.
    - Ställ in **Seriegrupperingar** på **nivå**.
    - Ställ in **Värden** på **nivå**.
    - Ställ in **Sammansättning** på **COUNT** (Antal).

6. Klicka på **Applicera**.

7. Utdata visar den visuella representationen som visas i följande skärmbild:

     ![Anpassa stapeldiagram](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Anpassa stapeldiagram")

## <a name="clean-up-resources"></a>Rensa resurser

Du kan avsluta klustret när du är klar med den här artikeln. I Azure Databricks-arbetsytan väljer du **Kluster** och letar reda på det kluster som du vill avsluta. För markören över de tre punkterna under kolumnen **Åtgärder**. Välj sedan ikonen **Avsluta**.

![Stoppa ett Databricks-kluster](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Stoppa ett Databricks-kluster")

Om du inte avslutar klustret manuellt stoppas det automatiskt, förutsatt att du har markerat kryss rutan **Avsluta efter \_ \_ minuter av inaktivitet** när klustret skapades. Om du väljer det här alternativet avslutas klustret när det har varit inaktivt i den tid som angetts.

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du ett Spark-kluster i Azure Databricks och körde ett Spark-jobb med hjälp av data i ett lagringskonto med Data Lake Storage Gen2 aktiverat.

Gå till nästa artikel om du vill lära dig hur du utför en ETL-åtgärd (extrahera, transformera och läsa in data) med Azure Databricks.

> [!div class="nextstepaction"]
>[Extrahera, transformera och läsa in data med Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse).

- Information om hur du importerar data från andra data källor till Azure Databricks finns i [Spark-datakällor](/azure/databricks/data/data-sources/).

- Information om andra sätt att komma åt Azure Data Lake Storage Gen2 från en Azure Databricks arbets yta finns i [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2).