---
title: Distribuera en modell för användning med Cognitive Search
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Machine Learning för att distribuera en modell för användning med Kognitiv sökning. Modellen används som en anpassad färdighet för att förbättra Sök funktionen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 03/11/2021
ms.custom: deploy
ms.openlocfilehash: 22c8880cbcde1f1a55fa66beee0323e0348e1164
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149621"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Distribuera en modell för användning med Cognitive Search


Den här artikeln lär dig hur du använder Azure Machine Learning för att distribuera en modell för användning med [Azure kognitiv sökning](../search/search-what-is-azure-search.md).

Kognitiv sökning utför innehålls bearbetning över heterogena-innehåll, så att den kan frågas av människor eller program. Den här processen kan utökas med hjälp av en modell som distribueras från Azure Machine Learning.

Azure Machine Learning kan distribuera en utbildad modell som en webb tjänst. Webb tjänsten är sedan inbäddad i en Kognitiv sökning- _kunskap_, som blir en del av bearbetnings pipelinen.

> [!IMPORTANT]
> Informationen i den här artikeln är speciell för distributionen av modellen. Den innehåller information om de distributions konfigurationer som stöds och som tillåter att modellen används av Kognitiv sökning.
>
> Information om hur du konfigurerar Kognitiv sökning att använda den distribuerade modellen finns i själv studie kursen [bygga och distribuera en anpassad kunskap med Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md) .
>
> För exemplet som självstudien baseras på, se [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

När du distribuerar en modell för användning med Azure Kognitiv sökning måste distributionen uppfylla följande krav:

* Använd Azure Kubernetes-tjänsten som värd för modellen för härledning.
* Aktivera TLS (Transport Layer Security) för Azure Kubernetes-tjänsten. TLS används för att skydda HTTPS-kommunikation mellan Kognitiv sökning och den distribuerade modellen.
* Entry-skriptet måste använda `inference_schema` paketet för att generera ett openapi-schema (Swagger) för tjänsten.
* Entry-skriptet måste också acceptera JSON-data som indata och generera JSON som utdata.


## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* En python-utvecklings miljö med Azure Machine Learning SDK installerat. Mer information finns i [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).  

* En registrerad modell. Om du inte har någon modell använder du exempel antecknings boken på [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

* En allmän förståelse för [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Anslut till arbetsytan

En Azure Machine Learning arbets yta är en central plats där du kan arbeta med alla artefakter som du skapar när du använder Azure Machine Learning. Arbets ytan har en historik över alla utbildningar som körs, inklusive loggar, mått, utdata och en ögonblicks bild av dina skript.

Använd följande kod för att ansluta till en befintlig arbets yta:

> [!IMPORTANT]
> Det här kodfragmentet förväntar sig att arbets ytans konfiguration sparas i den aktuella katalogen eller dess överordnade. Mer information finns i [skapa och hantera Azure Machine Learning arbets ytor](how-to-manage-workspace.md). Mer information om hur du sparar konfigurationen till filen finns i [skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Skapa ett Kubernetes-kluster

**Tids uppskattning**: cirka 20 minuter.

Ett Kubernetes-kluster är en uppsättning instanser av virtuella datorer (kallas noder) som används för att köra program i behållare.

När du distribuerar en modell från Azure Machine Learning till Azure Kubernetes-tjänsten paketeras modellen och alla till gångar som krävs för att vara värd för den som en webb tjänst i en Docker-behållare. Den här behållaren distribueras sedan till klustret.

Följande kod visar hur du skapar ett nytt Azure Kubernetes service-kluster (AKS) för din arbets yta:

> [!TIP]
> Du kan också bifoga en befintlig Azure Kubernetes-tjänst till din Azure Machine Learning-arbetsyta. Mer information finns i [Distribuera modeller till Azure Kubernetes-tjänsten](how-to-deploy-azure-kubernetes-service.md).

> [!IMPORTANT]
> Observera att koden använder `enable_ssl()` metoden för att aktivera TLS (Transport Layer Security) för klustret. Detta krävs när du planerar att använda den distribuerade modellen från Cognitive Services.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Azure kommer att debiteras så länge AKS-klustret finns. Se till att ta bort ditt AKS-kluster när du är klar.

Mer information om hur du använder AKS med Azure Machine Learning finns i [distribuera till Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Skriv start skriptet

Inmatnings skriptet tar emot data som skickats till webb tjänsten, skickar dem till modellen och returnerar poängsättnings resultatet. Följande skript läser in modellen vid start och använder sedan modellen för att räkna data. Den här filen kallas ibland `score.py` .

> [!TIP]
> Startskriptet är specifikt för din modell. Skriptet måste till exempel känna till ramverket som ska användas med din modell, data format osv.

> [!IMPORTANT]
> När du planerar att använda den distribuerade modellen från Azure Cognitive Services måste du använda `inference_schema` paketet för att aktivera skapande av scheman för distributionen. Det här paketet innehåller dekoratörer som gör att du kan definiera indata-och utdata för den webb tjänst som utför en härledning med hjälp av modellen.

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

Mer information om Entry-skript finns i [hur och var du ska distribuera](how-to-deploy-and-where.md).

## <a name="define-the-software-environment"></a>Definiera program varu miljön

Miljö klassen används för att definiera python-beroenden för tjänsten. Den innehåller beroenden som krävs av både modellen och Entry-skriptet. I det här exemplet installeras paket från det vanliga pypi-indexet, samt från en GitHub-lagrings platsen. 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

Mer information om miljöer finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

## <a name="define-the-deployment-configuration"></a>Definiera distributions konfigurationen

Distributions konfigurationen definierar Azure Kubernetes-tjänstens värd miljö som används för att köra webb tjänsten.

> [!TIP]
> Om du inte är säker på minnes-, processor-eller GPU-behoven för distributionen kan du använda profilering för att lära dig mer. Mer information finns i [hur och var du distribuerar en modell](how-to-deploy-and-where.md).

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

Mer information finns i referens dokumentationen för [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definiera konfigurationen för konfigurations störningar

Konfigurationen pekar på Start-och miljö objekt:

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

Mer information finns i referens dokumentationen för [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig).

## <a name="deploy-the-model"></a>Distribuera modellen

Distribuera modellen till ditt AKS-kluster och vänta tills den har skapat din tjänst. I det här exemplet läses två registrerade modeller in från registret och distribueras till AKS. Efter distributionen `score.py` laddar filen i distributionen dessa modeller och använder dem för att utföra en bättre härledning.

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

Mer information finns i referens dokumentationen för- [modellen](/python/api/azureml-core/azureml.core.model.model).

## <a name="issue-a-sample-query-to-your-service"></a>Skicka en exempel fråga till din tjänst

I följande exempel används distributions informationen som lagras i `aks_service` variabeln enligt föregående kod avsnitt. Den här variabeln används för att hämta den bedömnings-URL och autentiseringstoken som krävs för att kommunicera med tjänsten:

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

Resultatet som returneras från tjänsten liknar följande JSON:

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Anslut till Kognitiv sökning

Information om hur du använder den här modellen från Kognitiv sökning finns i själv studie kursen [bygga och distribuera en anpassad kunskap med Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md) .

## <a name="clean-up-the-resources"></a>Rensa resurserna

Om du har skapat AKS-klustret specifikt för det här exemplet tar du bort resurserna när du är klar med att testa det med Kognitiv sökning.

> [!IMPORTANT]
> Azure-räkningar baseras på hur länge AKS-klustret distribueras. Se till att rensa upp den när du är klar.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Nästa steg

* [Bygg och distribuera en anpassad kunskap med Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md)