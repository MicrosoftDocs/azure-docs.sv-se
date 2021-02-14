---
title: Transformering med Azure Databricks
description: Lär dig hur du använder en lösnings mall för att transformera data med hjälp av en Databricks Notebook i Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: ee663423071458605f37f07293693dbc91f592bb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362122"
---
# <a name="transformation-with-azure-databricks"></a>Transformering med Azure Databricks

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien skapar du en pipeline från slut punkt till slut punkt som innehåller **verifierings**-, **kopierings data**-och **Notebook** -aktiviteter i Azure Data Factory.

- **Verifiering** säkerställer att käll data uppsättningen är klar för efterföljande konsumtion innan du aktiverar kopierings-och analys jobbet.

- **Kopiera data** duplicerar käll data uppsättningen till Sink-lagringen, som monteras som DBFS i den Azure Databricks Notebook. På så sätt kan data uppsättningen förbrukas direkt av Spark.

- **Notebook** utlöser den Databricks Notebook som transformerar data uppsättningen. Den lägger också till data uppsättningen i en bearbetad mapp eller Azure Azure Synapse Analytics.

För enkelhetens skull skapar mallen i den här självstudien inte en schemalagd utlösare. Du kan lägga till en om det behövs.

![Diagram över pipelinen](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure Blob Storage-konto med en behållare `sinkdata` som kallas för användning som mottagare.

  Anteckna lagrings kontots namn, behållar namn och åtkomst nyckel. Du behöver dessa värden senare i mallen.

- En Azure Databricks-arbetsyta.

## <a name="import-a-notebook-for-transformation"></a>Importera en antecknings bok för omvandling

Så här importerar du en **Transformations** -anteckningsbok till din Databricks-arbetsyta:

1. Logga in på Azure Databricks arbets ytan och välj sedan **Importera**.
       ![Meny kommando för att importera en arbets yta ](media/solution-template-Databricks-notebook/import-notebook.png) sökvägen till arbets ytan kan skilja sig från den som visas, men kom ihåg den för senare.
1. Välj **Importera från: URL**. I text rutan anger du `https://adflabstaging1.blob.core.windows.net/share/Transformations.html` .

   ![Val för att importera en bärbar dator](media/solution-template-Databricks-notebook/import-from-url.png)

1. Nu ska vi uppdatera **omvandlings** antecknings boken med information om lagrings anslutningen.

   I den importerade antecknings boken går du till **kommando 5** , som du ser i följande kodfragment.

   - Ersätt `<storage name>` och `<access key>` med din egen lagrings anslutnings information.
   - Använd lagrings kontot med `sinkdata` behållaren.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Generera en **Databricks** -åtkomsttoken för Data Factory att få åtkomst till Databricks.
   1. I arbets ytan Databricks väljer du ikonen användar profil längst upp till höger.
   1. Välj **användar inställningar**.
    ![Meny kommando för användar inställningar](media/solution-template-Databricks-notebook/user-setting.png)
   1. Välj **Skapa ny token** under fliken **åtkomst-token** .
   1. Välj **generera**.

    ![Knappen generera](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Spara åtkomsttoken* för senare användning i skapa en länkad Databricks-tjänst. Åtkomsttoken ser ut ungefär så här `dapi32db32cbb4w6eee18b7d87e45exxxxxx` .

## <a name="how-to-use-this-template"></a>Använda den här mallen

1. Gå till **transformeringen med Azure Databricks** mall och skapa nya länkade tjänster för följande anslutningar.

   ![Inställningar för anslutningar](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Källans BLOB-anslutning** – för att få åtkomst till käll data.

       I den här övningen kan du använda den offentliga blob-lagring som innehåller källfilerna. Referera till följande skärm bild för konfigurationen. Använd följande **SAS-URL** för att ansluta till käll lagring (skrivskyddad åtkomst):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Val för autentiseringsmetod och SAS-URL](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Mål-BLOB-anslutning** – för att lagra kopierade data.

       I fönstret **ny länkad tjänst** väljer du din BLOB för mottagar lagring.

       ![Sink Storage blob som en ny länkad tjänst](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** – för att ansluta till Databricks-klustret.

        Skapa en Databricks-länkad tjänst genom att använda den åtkomst nyckel som du skapade tidigare. Du kan välja ett *interaktivt kluster* om du har ett. I det här exemplet används alternativet **nytt jobb kluster** .

        ![Val för att ansluta till klustret](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Välj **Använd den här mallen**. Du ser en pipeline som skapats.

    ![Skapa en pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Introduktion och konfiguration av pipeline

I den nya pipelinen konfigureras de flesta inställningar automatiskt med standardvärdena. Granska konfigurationerna för din pipeline och gör nödvändiga ändringar.

1. I **tillgänglighets flaggan** för **validerings** aktivitet kontrollerar du att käll **data** uppsättning svärdet är inställt på det `SourceAvailabilityDataset` som du skapade tidigare.

   ![Käll data uppsättnings värde](media/solution-template-Databricks-notebook/validation-settings.png)

1. I **Kopiera data** aktivitet **filen till BLOB** kontrollerar du flikarna **källa** och **mottagare** . Ändra inställningarna vid behov.

   - Fliken Källa för **källa** ![](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **Fliken mottagare på fliken mottagare** ![](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. I **transformeringen** för **antecknings boks** aktivitet granskar och uppdaterar du Sök vägarna och inställningarna efter behov.

   **Databricks länkade tjänst** bör fyllas i med värdet från ett föregående steg, som det visas: ![ fyllt i värdet för den länkade tjänsten Databricks](media/solution-template-Databricks-notebook/notebook-activity.png)

   Så här kontrollerar du inställningarna för **bärbara datorer** :
  
    1. Välj fliken **Inställningar** . För **Notebook-sökväg** kontrollerar du att standard Sök vägen är korrekt. Du kan behöva bläddra och välja rätt sökväg för antecknings boken.

       ![Notebook-sökväg](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Expandera **bas parameter** väljaren och kontrol lera att parametrarna matchar vad som visas i följande skärm bild. Dessa parametrar skickas till den Databricks Notebook från Data Factory.

       ![Bas parametrar](media/solution-template-Databricks-notebook/base-parameters.png)

1. Kontrol lera att **pipeline-parametrarna** matchar vad som visas i följande skärm bild: ![ pipeline-parametrar](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Anslut till dina data uppsättningar.

    >[!NOTE]
    >I under data uppsättningar har fil Sök vägen automatiskt angetts i mallen. Om några ändringar krävs, se till att du anger sökvägen för både **behållare** och **katalog** i händelse av anslutnings fel.

   - **SourceAvailabilityDataset** – för att kontrol lera att källdata är tillgängliga.

     ![Alternativ för länkad tjänst och fil Sök väg för SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** – för att komma åt käll data.

       ![Alternativ för länkad tjänst och fil Sök väg för SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** – för att kopiera data till mottagar destinations platsen. Använd följande värden:

     - **Länkad tjänst**  -  `sinkBlob_LS` som skapats i ett föregående steg.

     - **Fil Sök väg**  -  `sinkdata/staged_sink` .

       ![Alternativ för länkad tjänst och fil Sök väg för DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Välj **Felsök** för att köra pipelinen. Du kan hitta länken till Databricks-loggar för mer detaljerade Spark-loggar.

    ![Länk till Databricks-loggar från utdata](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Du kan också kontrol lera data filen genom att använda Azure Storage Explorer.

    > [!NOTE]
    > För att korrelera med Data Factory pipelines körs lägger det här exemplet till pipeline-körningen från data fabriken till mappen utdata. På så sätt kan du hålla reda på vilka filer som genereras vid varje körning.
    > ![Körnings-ID för Lägg till pipeline](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)
