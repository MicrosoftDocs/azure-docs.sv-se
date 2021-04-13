---
title: 'ML Studio (klassisk): migrera till Azure Machine Learning-återskapa data uppsättning'
description: Återskapa data uppsättningar för Studio (klassisk) i Azure Machine Learning designer
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 9604fc9d862d94ba5e566753d2186d7d28aa37ee
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308854"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Migrera en Studio-datauppsättning (klassisk) till Azure Machine Learning

I den här artikeln får du lära dig hur du migrerar en Studio-datauppsättning (klassisk) till Azure Machine Learning. Mer information om hur du migrerar från Studio (klassisk) finns i [översikts artikeln om migrering](migrate-overview.md).

Det finns tre alternativ för att migrera en data uppsättning till Azure Machine Learning. Läs varje avsnitt för att avgöra vilket alternativ som passar bäst för ditt scenario.


|Var finns data? | Migreringsalternativ  |
|---------|---------|
|I Studio (klassisk)     |  Alternativ 1: [Ladda ned data uppsättningen från Studio (klassisk) och ladda upp den till Azure Machine Learning](#download-the-dataset-from-studio-classic).      |
|Molnlagring     | Alternativ 2: [Registrera en data uppsättning från en moln källa](#import-data-from-cloud-sources). <br><br>  Alternativ 3: [Använd modulen importera data för att hämta data från en moln källa](#import-data-from-cloud-sources).        |

> [!NOTE]
> Azure Machine Learning stöder också [kod-första arbets flöden](../how-to-create-register-datasets.md) för att skapa och hantera data uppsättningar. 

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En Azure Machine Learning-arbetsyta. [Skapa en Azure Machine Learning-arbetsyta](../how-to-manage-workspace.md#create-a-workspace).
- En Studio (klassisk) data uppsättning att migrera.


## <a name="download-the-dataset-from-studio-classic"></a>Hämta data uppsättningen från Studio (klassisk)

Det enklaste sättet att migrera en Studio (klassisk) data uppsättning till Azure Machine Learning är att ladda ned din data uppsättning och registrera den i Azure Machine Learning. Detta skapar en ny kopia av din data uppsättning och laddar upp den till ett Azure Machine Learning data lager.

Du kan hämta följande data uppsättnings typer för Studio (klassisk) direkt.

* Oformaterad text (. txt)
* Kommaavgränsade värden (CSV) med ett sidhuvud (. csv) eller utan (.nh.csv)
* Tabbavgränsade värden (TSV) med ett sidhuvud (. tsv) eller utan (. NH. tsv)
* Excel-fil
* Zip-fil (. zip)

Så här hämtar du data uppsättningar direkt:
1. Gå till din Studio (klassiska) arbets yta ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. I det vänstra navigerings fältet väljer du fliken **data uppsättningar** .
1. Välj den eller de data uppsättningar som du vill ladda ned.
1. Välj **Hämta** i det nedre åtgärds fältet.

    ![Skärm bild som visar hur du laddar ned en data uppsättning i Studio (klassisk)](./media/migrate-register-dataset/download-dataset.png)

För följande data typer måste du använda modulen **konvertera till CSV** för att hämta data uppsättningar.

* SVMLight-data (. SVMLight) 
* ARFF-data (Attribute Relations fil format) (. arff) 
* R-objekt eller arbetsytefil (. RData
* Data uppsättnings typ (. data). Data uppsättnings typen är Studio (klassisk) intern datatyp för modulens utdata.

Så här konverterar du data uppsättningen till en CSV och hämtar resultatet:

1. Gå till din Studio (klassiska) arbets yta ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. Skapa ett nytt experiment.
1. Dra och släpp den data uppsättning som du vill ladda ned till arbets ytan.
1. Lägg till en **Convert to CSV** -modul.
1. Anslut den **konverterade till CSV** -Indataporten till utdataporten för din data uppsättning.
1. Kör experimentet.
1. Högerklicka på modulen **konvertera till CSV** .
1. Välj hämtning av **resultat data uppsättning**  >  .

    ![Skärm bild som visar hur du ställer in en konvertering till CSV-pipeline](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Ladda upp data uppsättningen till Azure Machine Learning

När du har hämtat data filen kan du registrera data uppsättningen i Azure Machine Learning:

1. Gå till Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. I det vänstra navigerings fönstret väljer du fliken **data uppsättningar** .
1. Välj **skapa data uppsättning**  >  **från lokala filer**.
    ![Skärm bild som visar fliken Data uppsättningar och knappen för att skapa en lokal fil](./media/migrate-register-dataset/register-dataset.png)
1. Ange ett namn och en beskrivning.
1. För **data uppsättnings typ** väljer du **tabell**.

    > [!NOTE]
    > Du kan också ladda upp ZIP-filer som data uppsättningar. Om du vill överföra en ZIP-fil väljer du **fil** för **data uppsättnings typ**.

1. **För data lager och filval** väljer du det data lager som du vill ladda upp din data uppsättnings fil till.

    Som standard lagrar Azure Machine Learning data uppsättningen till standard arbets ytans blobstore. Mer information om data lager finns i [Anslut till lagrings tjänster](../how-to-access-data.md).

1. Ange data tolknings inställningar och schema för din data uppsättning. Bekräfta sedan inställningarna.

## <a name="import-data-from-cloud-sources"></a>Importera data från moln källor

Om dina data redan finns i en moln lagrings tjänst och du vill behålla dina data på den ursprungliga platsen. Du kan använda något av följande alternativ:

|Inmatnings metod|Description|
|---| --- |
|Registrera en Azure Machine Learning data uppsättning|Mata in data från lokala data källor och online-datakällor (BLOB, ADLS Gen1, ADLS Gen2, fil resurs, SQL DB). <br><br>Skapar en referens till data källan som Lazy utvärderas vid körning. Använd det här alternativet om du har åtkomst till den här data uppsättningen flera gånger och vill aktivera avancerade data funktioner som data version och övervakning.
|Importera datamodul|Mata in data från data källor online (BLOB, ADLS Gen1, ADLS Gen2, fil resurs, SQL DB). <br><br> Data uppsättningen importeras bara till den aktuella designer-pipeline-körningen.


>[!Note]
> Studio (klassiska) användare bör Observera att följande moln källor inte stöds internt i Azure Machine Learning:
> - Hive-fråga
> - Azure-tabell
> - Azure Cosmos DB
> - Lokala SQL Database
>
> Vi rekommenderar att användarna migrerar sina data till en lagrings tjänst som stöds med hjälp av Azure Data Factory.  

### <a name="register-an-azure-machine-learning-dataset"></a>Registrera en Azure Machine Learning data uppsättning

Använd följande steg för att registrera en data uppsättning för att Azure Machine Learning från en moln tjänst: 

1. [Skapa ett data lager](../how-to-connect-data-ui.md#create-datastores)som länkar moln lagrings tjänsten till din Azure Machine Learning-arbetsyta. 

1. [Registrera en data uppsättning](../how-to-connect-data-ui.md#create-datasets). Om du migrerar en Studio-datauppsättning (klassisk) väljer du inställningen **tabell** data uppsättning.

När du har registrerat en data uppsättning i Azure Machine Learning kan du använda den i designern:
 
1. Skapa en ny designer pipeline-utkast.
1. I paletten modul till vänster expanderar du avsnittet **data uppsättningar** .
1. Dra din registrerade data uppsättning till arbets ytan. 

### <a name="use-the-import-data-module"></a>Använda modulen importera data

Använd följande steg för att importera data direkt till din designer-pipeline:

1. [Skapa ett data lager](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores)som länkar moln lagrings tjänsten till din Azure Machine Learning-arbetsyta. 

När du har skapat data lagret kan du använda modulen [**Importera data**](../algorithm-module-reference/import-data.md) i designern för att mata in data från den:

1. Skapa en ny designer pipeline-utkast.
1. I paletten modul till vänster letar du reda på modulen **Importera data** och drar den till arbets ytan.
1. Välj modulen **Importera data** och konfigurera data källan med inställningarna i den högra panelen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du migrerar en Studio-datauppsättning (klassisk) till Azure Machine Learning. Nästa steg är att [återskapa en Studio (klassisk) utbildnings pipeline](migrate-rebuild-experiment.md).


Se de andra artiklarna i serien Studio (klassisk) migrering:

1. [Översikt över migrering](migrate-overview.md).
1. **Migrera data uppsättningar**.
1. [Återskapa en Studio (klassisk) utbildnings pipeline](migrate-rebuild-experiment.md).
1. [Återskapa en Studio-webbtjänst (klassisk)](migrate-rebuild-web-service.md).
1. [Integrera en Azure Machine Learning-webbtjänst med klient program](migrate-rebuild-integrate-with-client-app.md).
1. [Migrera kör R-skript](migrate-execute-r-script.md).
