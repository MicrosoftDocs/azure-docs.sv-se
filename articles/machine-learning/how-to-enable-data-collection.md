---
title: Samla in data på dina produktions modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du samlar in data från en Azure Machine Learning modell som distribuerats i ett Azure Kubernetes service-kluster (AKS).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.openlocfilehash: fc890dbaf717d3eb9ec87afcb69c87e80c7f14bc
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680966"
---
# <a name="collect-data-from-models-in-production"></a>Samla in data från modeller i produktion

Den här artikeln visar hur du samlar in data från en Azure Machine Learning modell som distribuerats i ett Azure Kubernetes service-kluster (AKS). Insamlade data lagras sedan i Azure Blob Storage.

När samlingen har Aktiver ATS hjälper de data som du samlar in dig att:

* [Övervaka data](how-to-monitor-datasets.md) driften på de produktions data som du samlar in.

* Analysera insamlade data med [Power BI](#powerbi) eller [Azure Databricks](#databricks)

* Fatta bättre beslut om när du ska träna eller optimera din modell.

* Träna modellen med insamlade data.

## <a name="what-is-collected-and-where-it-goes"></a>Det som samlas in och där det går

Följande data kan samlas in:

* Modellera indata från webb tjänster som distribuerats i ett AKS-kluster. Röst ljud, bilder och video samlas *inte* in.
  
* Modell förutsägelser med hjälp av produktions data.

>[!NOTE]
> Föragg regering och för beräkningar av dessa data ingår inte för närvarande i insamlings tjänsten.

Utdata sparas i Blob Storage. Eftersom data har lagts till i Blob Storage kan du välja ditt favorit verktyg för att köra analysen.

Sökvägen till utdata i bloben följer den här syntaxen:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> I versioner av Azure Machine Learning SDK för python tidigare än version 0.1.0 A16 `designation` heter argumentet `identifier` . Om du har utvecklat din kod med en tidigare version måste du uppdatera den.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLFree) innan du börjar.

- En Azure Machine Learning arbets yta, en lokal katalog som innehåller dina skript och Azure Machine Learning SDK för python måste vara installerad. Information om hur du installerar dem finns i [så här konfigurerar du en utvecklings miljö](how-to-configure-environment.md).

- Du behöver en utbildad maskin inlärnings modell för att kunna distribueras till AKS. Om du inte har någon modell kan du läsa själv studie kursen [träna image klassificerings modell](tutorial-train-models-with-aml.md) .

- Du behöver ett AKS-kluster. Information om hur du skapar en och distribuerar till den finns i [distribuera och var](how-to-deploy-and-where.md).

- [Konfigurera din miljö](how-to-configure-environment.md) och installera [Azure Machine Learning övervaknings-SDK: n](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

## <a name="enable-data-collection"></a>Aktivera datainsamling

Du kan aktivera [data insamling](/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector?preserve-view=true&view=azure-ml-py) oavsett vilken modell du distribuerar via Azure Machine Learning eller andra verktyg.

Om du vill aktivera data insamling måste du:

1. Öppna bedömnings filen.

1. Lägg till följande kod högst upp i filen:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Deklarera dina variabler för data insamling i din `init` funktion:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3", "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* är en valfri parameter. Du behöver inte använda den om din modell inte kräver det. Med hjälp av *correlationId* kan du enklare mappa med andra data, till exempel *LoanNumber* eller *CustomerId*.
    
    Parametern *Identifier* används senare för att skapa mappstrukturen i blobben. Du kan använda den för att särskilja rå data från bearbetade data.

1. Lägg till följande rader med kod till `run(input_df)` funktionen:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Data insamling anges *inte* automatiskt till **Sant** när du distribuerar en tjänst i AKS. Uppdatera konfigurations filen, som i följande exempel:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Du kan också aktivera Application Insights för service övervakning genom att ändra den här konfigurationen:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Information om hur du skapar en ny avbildning och distribuerar Machine Learning-modellen finns i [distribuera och var](how-to-deploy-and-where.md).

1. Lägg till pip-paketet "Azure-Monitoring" i Conda-beroendena för webb tjänst miljön:
  ```Python
    env = Environment('webserviceenv')
    env.python.conda_dependencies = CondaDependencies.create(conda_packages=['numpy'],pip_packages=['azureml-defaults','azureml-monitoring','inference-schema[numpy-support]'])
  ```


## <a name="disable-data-collection"></a>Inaktivera datainsamling

Du kan när som helst sluta samla in data. Använd python code för att inaktivera data insamling.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Validera och analysera dina data

Du kan välja ett verktyg för att analysera data som samlas in i blob-lagringen.

### <a name="quickly-access-your-blob-data"></a>Kom snabbt åt dina BLOB-data

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Öppna din arbets yta.

1. Välj **lagring**.

    [![Välj lagrings alternativet](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Följ sökvägen till blobens utgående data med följande syntax:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a> Analysera modell data med Power BI

1. Ladda ned och öppna [Power BI Desktop](https://www.powerbi.com).

1. Välj **Hämta data** och välj [**Azure Blob Storage**](/power-bi/desktop-data-sources).

    [![Power BI BLOB-installation](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Lägg till ditt lagrings konto namn och ange din lagrings nyckel. Du kan hitta den här informationen genom att välja **Inställningar**  >  **åtkomst nycklar** i blobben.

1. Välj **modellen data** behållare och välj **Redigera**.

    [![Power BI Navigator](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. I Frågeredigeraren klickar du under kolumnen **namn** och lägger till ditt lagrings konto.

1. Ange din modell Sök väg i filtret. Om du bara vill titta på filer från ett speciellt år eller månad expanderar du bara filter Sök vägen. Använd till exempel följande filter Sök väg om du vill visa enbart mars data:

   /modeldata/ \<subscriptionid> / \<resourcegroupname> / \<workspacename> / \<webservicename> / \<modelname> / \<modelversion> / \<designation> / \<year> /3

1. Filtrera data som är relevanta för dig baserat på **namn** värden. Om du har lagrat förutsägelser och indata måste du skapa en fråga för var och en.

1. Välj de nedåtriktade dubbla pilarna bredvid kolumn rubriken **innehåll** för att kombinera filerna.

    [![Power BI innehåll](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Välj **OK**. Data inläsningar.

    [![Power BI kombinera filer](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Välj **Stäng och Använd**.

1. Om du har lagt till indata och förutsägelser sorteras dina tabeller automatiskt efter **RequestId** -värden.

1. Börja skapa dina anpassade rapporter på dina modell data.

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> Analysera modell data med Azure Databricks

1. Skapa en [Azure Databricks-arbetsyta](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).

1. Gå till din Databricks-arbetsyta.

1. I arbets ytan Databricks väljer du **Ladda upp data**.

    [![Välja alternativet Databricks upload data](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Välj **Skapa ny tabell** och välj **andra data källor**  >  **Azure Blob Storage**  >  **skapa en tabell i antecknings boken**.

    [![Skapa Databricks tabell](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Uppdatera platsen för dina data. Här är ett exempel:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks-installation](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Följ stegen i mallen för att visa och analysera dina data.

## <a name="next-steps"></a>Nästa steg

[Identifiera data avvikelser](how-to-monitor-datasets.md) för de data du har samlat in.