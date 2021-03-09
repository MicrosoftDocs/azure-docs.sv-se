---
title: Distribuera ML-modeller till Kubernetes-tjänsten
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar dina Azure Machine Learning modeller som en webb tjänst med Azure Kubernetes-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: ef9c03b687bbc9b8fe736c872bbde14b8daba899
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519392"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuera en modell till ett Azure Kubernetes service-kluster

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webb tjänst på Azure Kubernetes service (AKS). Azure Kubernetes-tjänsten är lämplig för storskaliga produktions distributioner. Använd Azure Kubernetes-tjänsten om du behöver en eller flera av följande funktioner:

- __Snabb svars tid__
- Automatisk __skalning__ av den distribuerade tjänsten
- __Loggning__
- __Modell data insamling__
- __Autentisering__
- __TLS-terminering__
- Alternativ för __maskin varu acceleration__ , t. ex. GPU och fält-programmerbara grind mat ris (FPGA)

När du distribuerar till Azure Kubernetes-tjänsten distribuerar du till ett AKS-kluster som är __anslutet till din arbets yta__. Information om hur du ansluter ett AKS-kluster till din arbets yta finns i [skapa och ansluta ett Azure Kubernetes service-kluster](how-to-create-attach-kubernetes.md).

> [!IMPORTANT]
> Vi rekommenderar att du felsökr lokalt innan du distribuerar till webb tjänsten. Mer information finns i [Felsöka lokalt](./how-to-troubleshoot-deployment-local.md)
>
> Du kan också läsa Azure Machine Learning – [Distribuera till lokal notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- En Machine Learning-modell som registrerats i din arbets yta. Om du inte har en registrerad modell, se [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

- [Azure CLI-tillägget för Machine Learning-tjänst](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

- I __python__ -kodfragmenten i den här artikeln förutsätter vi att följande variabler har angetts:

    * `ws` – Ställ in på din arbets yta.
    * `model` – Ställ in på din registrerade modell.
    * `inference_config` -Ange som modellens konfigurations konfiguration.

    Mer information om hur du ställer in dessa variabler finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

- __CLI__ -kodfragmenten i den här artikeln förutsätter att du har skapat ett `inferenceconfig.json` dokument. Mer information om hur du skapar det här dokumentet finns i [så här distribuerar du modeller](how-to-deploy-and-where.md).

- Ett Azure Kubernetes service-kluster som är anslutet till din arbets yta. Mer information finns i [skapa och ansluta ett Azure Kubernetes service-kluster](how-to-create-attach-kubernetes.md).

    - Om du vill distribuera modeller till GPU-noder eller FPGA-noder (eller vissa SKU: er) måste du skapa ett kluster med den angivna SKU: n. Det finns inget stöd för att skapa en sekundär Node-pool i ett befintligt kluster och distribuera modeller i den sekundära noden.

## <a name="understand-the-deployment-processes"></a>Förstå distributions processerna

Ordet "Deployment" används i både Kubernetes och Azure Machine Learning. "Distribution" har olika betydelser i dessa två kontexter. I Kubernetes är en `Deployment` konkret entitet som anges med en DEKLARATIV yaml-fil. En Kubernetes `Deployment` har en definierad livs cykel och konkreta relationer till andra Kubernetes entiteter som `Pods` och `ReplicaSets` . Du kan lära dig mer om Kubernetes från dokument och videor på [Vad är Kubernetes?](https://aka.ms/k8slearning).

I Azure Machine Learning används "distribution" i den allmänna uppfattningen för att göra tillgängliga och rensa projekt resurserna. De steg som Azure Machine Learning anser en del av distributionen är:

1. Zippa upp filerna i projektmappen och ignorera dem som anges i. amlignore eller. gitignore
1. Skala upp beräknings klustret (relaterat till Kubernetes)
1. Skapa eller ladda ned Dockerfile till Compute-noden (relaterar till Kubernetes)
    1. Systemet beräknar en hash av: 
        - Bas avbildningen 
        - Anpassade Docker-steg (se [distribuera en modell med en anpassad Docker-bas avbildning](./how-to-deploy-custom-docker-image.md))
        - Conda definition YAML (se [skapa & använda program varu miljöer i Azure Machine Learning](./how-to-use-environments.md))
    1. Systemet använder denna hash som nyckel i en sökning i arbets ytans Azure Container Registry (ACR)
    1. Om den inte hittas söker den efter en matchning i den globala ACR
    1. Om den inte hittas skapar systemet en ny avbildning (som cachelagras och skickas till arbets ytan ACR)
1. Hämta den zippade projekt filen till tillfällig lagring på Compute-noden
1. Zippa upp projekt filen
1. Compute-noden körs `python <entry script> <arguments>`
1. Spara loggar, modellvariabler och andra filer som skrivs till `./outputs` det lagrings konto som är kopplat till arbets ytan
1. Skala ned beräkning, inklusive borttagning av tillfällig lagring (relatera till Kubernetes)

### <a name="azure-ml-router"></a>Azure ML-router

Klient dels komponenten (azureml-FE) som dirigerar inkommande härlednings förfrågningar till distribuerade tjänster skalas automatiskt efter behov. Skalning av azureml-FE baseras på AKS-klustrets syfte och storlek (antal noder). Klustrets syfte och noder konfigureras när du [skapar eller ansluter ett AKS-kluster](how-to-create-attach-kubernetes.md). Det finns en azureml-FE-tjänst per kluster, som kan köras på flera poddar.

> [!IMPORTANT]
> När du använder ett kluster som kon figurer ATS som __dev-test__ **inaktive ras** själv skalnings funktionen.

Azureml – FE skalar upp (lodrätt) för att använda fler kärnor och ut (vågrätt) för att använda fler poddar. När du fattar beslutet att skala upp används den tid det tar att dirigera inkommande begär Anden om att dirigera inkommande begär Anden. Om den här tiden överskrider tröskelvärdet sker en skalning. Om tiden för att dirigera inkommande begär Anden fortsätter att överskrida tröskelvärdet sker en skalbarhet.

Vid skalning och i används CPU-användning. Om tröskelvärdet för processor användning är uppfyllt, kommer först klient delen att skalas ned. Om CPU-användningen sjunker till skalnings tröskeln sker en skalnings åtgärd. Att skala upp och ut sker bara om det finns tillräckligt många tillgängliga kluster resurser.

## <a name="understand-connectivity-requirements-for-aks-inferencing-cluster"></a>Förstå anslutningskraven för slutsatsdragningskluster i AKS

När Azure Machine Learning skapar eller kopplar ett AKS-kluster, distribueras AKS-klustret med någon av följande två nätverks modeller:
* Kubernetes-nätverk – nätverks resurserna skapas och konfigureras vanligt vis när AKS-klustret distribueras.
* Azure CNI-nätverk (Container Networking Interface) – AKS-klustret ansluts till befintliga resurser och konfigurationer för virtuella nätverk.

För det första nätverks läget skapas och konfigureras nätverk korrekt för Azure Machine Learning tjänst. För det andra nätverks läget, eftersom klustret är anslutet till ett befintligt virtuellt nätverk, särskilt när anpassad DNS används för ett befintligt virtuellt nätverk, måste kunden betala extra uppmärksamhet för anslutnings kraven för AKS inferencing-kluster och kontrol lera DNS-matchning och utgående anslutning för AKS inferencing.

Följande diagram fångar alla anslutnings krav för AKS-inferencing. Svarta pilar representerar den faktiska kommunikationen och blå pilar representerar domän namnen, som kundkontrollerad DNS ska lösa.

 ![Anslutnings krav för AKS-Inferencing](./media/how-to-deploy-aks/aks-network.png)

### <a name="overall-dns-resolution-requirements"></a>Övergripande krav för DNS-matchning
DNS-matchning i befintligt VNET är under kundens kontroll. Följande DNS-poster ska kunna matchas:
* AKS-API-server i formatet \<cluster\> . HCP. \<region\> .. azmk8s.io
* Microsoft Container Registry (MCR): mcr.microsoft.com
* Kundens Azure Container Registry (båge) i form av \<ACR name\> . azurecr.io
* Azure Storage konto i formatet \<account\> . Table.Core.Windows.net och \<account\> . blob.Core.Windows.net
* Valfritt För AAD-autentisering: api.azureml.ms
* Bedömnings slut punktens domän namn, antingen automatiskt genererat av Azure ML eller det anpassade domän namnet. Det automatiskt genererade domän namnet skulle se ut så här: \<leaf-domain-label \+ auto-generated suffix\> . \<region\> . cloudapp.azure.com

### <a name="connectivity-requirements-in-chronological-order-from-cluster-creation-to-model-deployment"></a>Anslutnings krav i kronologisk ordning: från kluster skapas till modell distribution

I processen för AKS Create eller Attach distribueras Azure ML router (azureml-FE) till AKS-klustret. För att kunna distribuera Azure ML-routern ska AKS-noden kunna:
* Matcha DNS för AKS-API-Server
* Matcha DNS för MCR för att hämta Docker-avbildningar för Azure ML router
* Ladda ned bilder från MCR, där utgående anslutning krävs

Direkt efter att azureml-FE har distribuerats försöker det att starta och detta kräver:
* Matcha DNS för AKS-API-Server
* Fråga AKS-API-Server för att identifiera andra instanser av sig själv (det är en POD-tjänst)
* Anslut till andra instanser av sig själv

När azureml-FE startas krävs ytterligare anslutning för att fungera korrekt:
* Anslut till Azure Storage för att ladda ned dynamisk konfiguration
* Matcha DNS för AAD Authentication Server-api.azureml.ms och kommunicera med den när den distribuerade tjänsten använder AAD-autentisering.
* Fråga AKS API-Server för att identifiera distribuerade modeller
* Kommunicera med distribuerad modell poddar

Vid modell distributions tiden ska en lyckad modell distributions AKS-nod kunna: 
* Matcha DNS för kundens ACR
* Ladda ned bilder från kundens ACR
* Matcha DNS för Azure-BLOBBAR där modellen lagras
* Ladda ned modeller från Azure-BLOBBAR

När modellen har distribuerats och tjänsten startar identifierar azureml-FE automatiskt den med hjälp av AKS API och är redo att dirigera begäran till den. Den måste kunna kommunicera med modell-poddar.
>[!Note]
>Om den distribuerade modellen kräver anslutning (t. ex. genom att fråga extern databas eller annan REST-tjänst, hämta en blogg osv.), ska både DNS-matchning och utgående kommunikation för dessa tjänster vara aktiverade.

## <a name="deploy-to-aks"></a>Distribuera till AKS

Om du vill distribuera en modell till Azure Kubernetes-tjänsten skapar du en __distributions konfiguration__ som beskriver de beräknings resurser som behövs. Till exempel antal kärnor och minne. Du behöver också en __konfiguration__ med en konfiguration som beskriver den miljö som krävs för att vara värd för modellen och webb tjänsten. Mer information om hur du skapar en konfigurations konfiguration finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

> [!NOTE]
> Antalet modeller som ska distribueras är begränsat till 1 000 modeller per distribution (per behållare).

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration)
* [Modell. Deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande kommando för att distribuera med hjälp av CLI. Ersätt `myaks` med namnet på AKS Compute Target. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen. Ersätt `myservice` med namnet för att ge den här tjänsten:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Mer information finns i [distributions referens för AZ ml-modellen](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Information om hur du använder VS Code finns i [distribuera till AKS via vs Code-tillägget](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Att distribuera via VS Code kräver att AKS-klustret skapas eller kopplas till din arbets yta i förväg.

---

### <a name="autoscaling"></a>Automatisk skalning

Komponenten som hanterar autoskalning för distributioner av Azure ML-modeller är azureml-FE, som är en router för smart begäran. Eftersom alla härlednings begär Anden går igenom den, har den nödvändiga data för att automatiskt skala de distribuerade modellerna.

> [!IMPORTANT]
> * **Aktivera inte Kubernetes horisontell Pod autoskalning (hPa) för modell distributioner**. Detta skulle leda till att de två komponenterna för automatisk skalning konkurrerar med varandra. Azureml-FE är utformat för att skala modeller som distribueras av Azure ML, där HPA skulle behöva gissa eller uppskatta modell användning från ett allmänt mått som CPU-användning eller en anpassad mått konfiguration.
> 
> * **Azureml-FE skalar inte antalet noder i ett AKS-kluster** eftersom det kan leda till oväntade kostnads ökningar. I stället **skalas antalet repliker för modellen** i de fysiska klustrets gränser. Om du behöver skala antalet noder i klustret kan du skala klustret manuellt eller [Konfigurera AKS-klustrets autoskalning](../aks/cluster-autoscaler.md).

Automatisk skalning kan styras genom att ställa in `autoscale_target_utilization` , `autoscale_min_replicas` och `autoscale_max_replicas` för AKS-webbtjänsten. Följande exempel visar hur du aktiverar automatisk skalning:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Beslut om att skala upp/ned baseras på användning av aktuella behållares repliker. Det totala antalet repliker som är upptagna (bearbetning av en begäran) dividerat med det totala antalet aktuella repliker är den aktuella användningen. Om det här värdet överskrider så `autoscale_target_utilization` skapas fler repliker. Om den är lägre minskas replikerna. Som standard är mål användningen 70%.

Beslut om att lägga till repliker är Eager och snabbt (cirka 1 sekund). Beslut att ta bort repliker är försiktigt (cirka 1 minut).

Du kan beräkna de repliker som krävs med hjälp av följande kod:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Mer information om hur du ställer in `autoscale_target_utilization` , `autoscale_max_replicas` och finns `autoscale_min_replicas` i referens för [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice) -modulen.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Distribuera modeller till AKS med hjälp av kontrollerad distribution (för hands version)

Analysera och uppgradera modell versioner på ett kontrollerat sätt med hjälp av slut punkter. Du kan distribuera upp till sex versioner bakom en enda slut punkt. Slut punkter tillhandahåller följande funktioner:

* Konfigurera __procent andelen av bedömnings trafik som skickas till varje slut punkt__. Du kan till exempel skicka 20% av trafiken till slut punkten ' test ' och 80% till ' produktion '.

    > [!NOTE]
    > Om du inte tar hänsyn till 100% av trafiken dirigeras eventuella återstående procent till __standard__ slut punkts versionen. Om du till exempel konfigurerar slut punkts version "test" för att få 10% av trafiken och "Prod" i 30% skickas återstående 60% till standard slut punkts versionen.
    >
    > Den första slut punkts versionen som skapas konfigureras automatiskt som standard. Du kan ändra detta genom att ställa in `is_default=True` när du skapar eller uppdaterar en slut punkts version.
     
* Tagga en slut punkts version som __kontroll__ eller __behandling__. Till exempel kan den aktuella produktions slut punkts versionen vara kontrollen, medan eventuella nya modeller distribueras som behandlings versioner. När du har utvärderat prestandan för behandlings versionerna, om en utför den aktuella kontrollen, kan den höjas till den nya produktionen/kontrollen.

    > [!NOTE]
    > Du kan bara ha __en__ kontroll. Du kan ha flera behandlingar.

Du kan aktivera App Insights om du vill visa drifts mått för slut punkter och distribuerade versioner.

### <a name="create-an-endpoint"></a>Skapa en slutpunkt
När du är redo att distribuera dina modeller skapar du en poäng slut punkt och distribuerar din första version. I följande exempel visas hur du distribuerar och skapar slut punkten med hjälp av SDK. Den första distributionen definieras som standard versionen, vilket innebär att ospecificerad trafik percentil över alla versioner kommer att gå till standard versionen.  

> [!TIP]
> I följande exempel anger konfigurationen den ursprungliga slut punkts versionen för att hantera 20% av trafiken. Eftersom det här är den första slut punkten är det även standard versionen. Eftersom vi inte har några andra versioner av den andra 80% av trafiken dirigeras de också till standard. Till dess att andra versioner som tar en procent andel av trafiken distribueras, tar den här en effektiv emot 100% av trafiken.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')

# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Uppdatera och lägga till versioner till en slut punkt

Lägg till en annan version till din slut punkt och konfigurera bedömnings trafikens percentil till versionen. Det finns två typer av versioner, en kontroll och en behandlings version. Det kan finnas flera behandlings versioner som hjälper dig att jämföra med en enda kontroll version.

> [!TIP]
> Den andra versionen, som skapats av följande kodfragment, accepterar 10% av trafiken. Den första versionen har kon figurer ATS för 20%, så endast 30% av trafiken har kon figurer ATS för vissa versioner. Den återstående 70% skickas till den första slut punkts versionen, eftersom den också är standard versionen.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Uppdatera befintliga versioner eller ta bort dem i en slut punkt. Du kan ändra versionens standard typ, kontroll typ och trafik percentilen. I följande exempel ökar den andra versionen sin trafik till 40% och är nu standard.

> [!TIP]
> Efter följande kodfragment är den andra versionen nu standard. Den har nu kon figurer ATS för 40%, medan den ursprungliga versionen fortfarande har kon figurer ATS för 20%. Det innebär att 40% av trafiken inte redovisas för av versions konfigurationerna. Den överblivna trafiken kommer att dirigeras till den andra versionen eftersom den nu är standard. Det tar effektivt emot 80% av trafiken.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>Autentisering av webbtjänst

När du distribuerar till Azure Kubernetes-tjänsten aktive ras __nyckelbaserad__ autentisering som standard. Du kan också aktivera __tokenbaserad__ autentisering. Token-baserad autentisering kräver att klienter använder ett Azure Active Directory konto för att begära en autentiseringstoken, som används för att göra förfrågningar till den distribuerade tjänsten.

Om du vill __inaktivera__ autentisering ställer du in `auth_enabled=False` parametern när du skapar distributions konfigurationen. I följande exempel inaktive ras autentisering med hjälp av SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Information om hur du autentiserar från ett klient program finns i [använda en Azure Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autentisering med nycklar

Om du har aktiverat autentisering av nycklar kan du använda- `get_keys` metoden för att hämta en primär nyckel och en sekundär autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du behöver återskapa en nyckel använder du [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29)

### <a name="authentication-with-tokens"></a>Autentisering med token

Om du vill aktivera token-autentisering anger `token_auth_enabled=True` du parametern när du skapar eller uppdaterar en distribution. I följande exempel aktive ras token-autentisering med SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Om token-autentisering har Aktiver ATS kan du använda `get_token` metoden för att hämta en JWT-token och dess förfallo tid för token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter det att token har `refresh_by` uppnåtts.
>
> Microsoft rekommenderar starkt att du skapar din Azure Machine Learning arbets yta i samma region som ditt Azure Kubernetes service-kluster. För att autentisera med en token kommer webb tjänsten att ringa till den region där din Azure Machine Learning arbets yta skapas. Om arbets ytans region inte är tillgänglig kan du inte hämta en token för din webb tjänst även om klustret finns i en annan region än din arbets yta. Detta leder till att tokenbaserad autentisering inte är tillgängligt förrän arbets ytans region är tillgänglig igen. Dessutom ökar avståndet mellan klustrets region och arbets ytans region, desto längre tid tar det att hämta en token.
>
> Om du vill hämta en token måste du använda kommandot Azure Machine Learning SDK eller [AZ ml-tjänsten get-Access-token](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-get-access-token) .


### <a name="vulnerability-scanning"></a>Sårbarhetsgenomsökning

Azure Security Center erbjuder enhetlig säkerhetshantering och avancerat skydd mot hot i olika hybridmolnarbetsbelastningar. Du bör tillåta Azure Security Center att söka igenom dina resurser och följa rekommendationerna. Mer information finns i [integrering med Azure Kubernetes Services med Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="next-steps"></a>Nästa steg

* [Använd Azure RBAC för Kubernetes-auktorisering](../aks/manage-azure-rbac.md)
* [Skydda inferencing-miljön med Azure Virtual Network](how-to-secure-inferencing-vnet.md)
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Distributions fel sökning](how-to-troubleshoot-deployment.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Använda en ML-modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
