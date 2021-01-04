---
title: Övervaka och samla in data från Machine Learning webb tjänst slut punkter
titleSuffix: Azure Machine Learning
description: Lär dig hur du samlar in data från modeller som distribueras till webb tjänst slut punkter i Azure Kubernetes service (AKS) eller Azure Container Instances (ACI).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 13b99fe129191b89b5bb2d7f5473e910fa619ce7
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739849"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Se Övervaka och samla in data från webbtjänstslutpunkter i ML


I den här artikeln får du lära dig hur du samlar in data från modeller som distribuerats till webb tjänst slut punkter i Azure Kubernetes service (AKS) eller Azure Container Instances (ACI). Använd [Azure Application insikter](../azure-monitor/app/app-insights-overview.md) för att samla in följande data från en slut punkt:
* Utdata
* Svar
* Begär ande frekvens, svars tider och felaktiga frekvenser
* Beroende frekvens, svars tider och felaktiga frekvenser
* Undantag

Den [Enable-App-Insights-in-producting-service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) Notebook visar begrepp i den här artikeln.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
 
## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – testa den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* En Azure Machine Learning arbets yta, en lokal katalog som innehåller dina skript och Azure Machine Learning SDK för python installerat. Mer information finns i [så här konfigurerar du en utvecklings miljö](how-to-configure-environment.md).

* En utbildad maskin inlärnings modell. Mer information finns i själv studie kursen [träna image klassificerings modell](tutorial-train-models-with-aml.md) .

<a name="python"></a>

## <a name="configure-logging-with-the-python-sdk"></a>Konfigurera loggning med python SDK

I det här avsnittet får du lära dig hur du aktiverar program insikts loggning med python SDK. 

### <a name="update-a-deployed-service"></a>Uppdatera en distribuerad tjänst

Använd följande steg för att uppdatera en befintlig webb tjänst:

1. Identifiera tjänsten i din arbets yta. Värdet för `ws` är namnet på din arbets yta

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Uppdatera tjänsten och aktivera Azure Application insikter

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Logga anpassade spårningar i din tjänst

> [!IMPORTANT]
> Azure Application Insights loggar bara nytto laster på upp till 64 kB. Om den här gränsen nås kan du se fel, till exempel slut på minne, eller så kan ingen information loggas. Om de data som du vill logga in är större än 64 KB bör du i stället lagra dem till Blob Storage med hjälp av informationen i [samla in data för modeller i produktion](how-to-enable-data-collection.md).
>
> För mer komplexa situationer, till exempel modell spårning i en AKS-distribution, rekommenderar vi att du använder ett bibliotek från tredje part som [openräkning](https://opencensus.io).

Om du vill logga anpassade spår följer du standard distributions processen för AKS eller ACI i avsnittet [så här distribuerar och var](how-to-deploy-and-where.md) dokumentet. Använd sedan följande steg:

1. Uppdatera bedömnings filen genom att lägga till utskrifts instruktioner för att skicka data till Application Insights under härledningen. Om du vill ha mer komplex information, till exempel begär ande data och svar, använder du en JSON-struktur. 

    I följande exempel `score.py` fil loggas när modellen initierades, indata och utdata under härledningen och hur länge eventuella fel inträffar.

    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. Uppdatera tjänst konfigurationen och se till att aktivera Application Insights.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Bygg en avbildning och distribuera den på AKS eller ACI. Mer information finns i [distribuera och var](how-to-deploy-and-where.md).


### <a name="disable-tracking-in-python"></a>Inaktivera spårning i python

Om du vill inaktivera Azure Application insikter använder du följande kod:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="configure-logging-with-azure-machine-learning-studio"></a>Konfigurera loggning med Azure Machine Learning Studio

Du kan också aktivera Azure Application insikter från Azure Machine Learning Studio. När du är redo att distribuera din modell som en webb tjänst kan du använda följande steg för att aktivera Application Insights:

1. Logga in på Studio på https://ml.azure.com .
1. Gå till **modeller** och välj den modell som du vill distribuera.
1. Välj  **+ distribuera**.
1. Fyll i formuläret **distribuera modell** .
1. Expandera menyn **Avancerat** .

    ![Distribuera formulär](./media/how-to-enable-app-insights/deploy-form.png)
1. Välj **aktivera Application Insights diagnostik och data insamling**.

    ![Aktivera App Insights](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Visa mått och loggar

### <a name="query-logs-for-deployed-models"></a>Fråga efter loggar för distribuerade modeller

Loggar över real tids slut punkter är kund information. Du kan använda `get_logs()` funktionen för att hämta loggar från en tidigare distribuerad webb tjänst. Loggarna kan innehålla detaljerad information om eventuella fel som uppstod under distributionen.

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Om du har flera klienter kan du behöva lägga till följande autentiserings kod innan `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

### <a name="view-logs-in-the-studio"></a>Visa loggar i Studio

Azure Application Insights lagrar dina tjänst loggar i samma resurs grupp som arbets ytan Azure Machine Learning. Använd följande steg för att visa dina data med hjälp av Studio:

1. Gå till din Azure Machine Learning arbets yta i [Studio](https://ml.azure.com/).
1. Välj **slut punkter**.
1. Välj den distribuerade tjänsten.
1. Välj länken **Application Insights-URL** .

    [![Hitta Application Insights-URL](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. I Application Insights går du till fliken **Översikt** eller avsnittet __övervakning__ och väljer __loggar__.

    [![Fliken Översikt för övervakning](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Om du vill visa information som loggats från score.py-filen tittar du på tabellen __spår__ . Följande fråga söker efter loggar där __indatavärdet__ loggades:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![spårnings data](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Mer information om hur du använder Azure Application Insights finns i [Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="web-service-metadata-and-response-data"></a>Metadata och svars data för webb tjänst

> [!IMPORTANT]
> Azure Application Insights loggar bara nytto laster på upp till 64 kB. Om den här gränsen har uppnåtts kan du se fel, till exempel slut på minne, eller så kan ingen information loggas.

Om du vill logga information om webb tjänst begär Anden lägger `print` du till instruktioner till din score.py-fil. Varje `print` instruktion resulterar i en post i tabellen Application Insights spårning under meddelandet `STDOUT` . Application Insights lagrar `print` utdraget utdata i  `customDimensions` och i `Contents` spårnings tabellen. Om du skriver ut JSON-strängar skapas en hierarkisk data struktur i spårningens utdata under `Contents` .

## <a name="export-data-for-retention-and-processing"></a>Exportera data för kvarhållning och bearbetning

>[!Important]
> Azure Application Insights stöder endast export till Blob Storage. Mer information om gränserna för den här implementeringen finns i [Exportera telemetri från App Insights](../azure-monitor/app/export-telemetry.md#continuous-export-advanced-storage-configuration).

Använd Application Insights " [löpande export](../azure-monitor/app/export-telemetry.md) för att exportera data till ett Blob Storage-konto där du kan definiera inställningar för kvarhållning. Application Insights exporterar data i JSON-format. 

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Löpande export":::

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar loggnings-och visnings loggar för webb tjänst slut punkter. Prova de här artiklarna för nästa steg:


* [Så här distribuerar du en modell till ett AKS-kluster](./how-to-deploy-azure-kubernetes-service.md)

* [Så här distribuerar du en modell till Azure Container Instances](./how-to-deploy-azure-container-instance.md)

* [MLOps: hantera, distribuera och övervaka modeller med Azure Machine Learning](./concept-model-management-and-deployment.md) för att lära dig mer om att använda data som samlas in från modeller i produktionen. Sådana data kan hjälpa till att kontinuerligt förbättra din Machine Learning-process.