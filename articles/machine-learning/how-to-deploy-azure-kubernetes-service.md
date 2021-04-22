---
title: Distribuera ML-modeller till Kubernetes Service
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar Azure Machine Learning modeller som en webbtjänst med hjälp av Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: 6030462fc7c9678200aa14fa852a82d35f8703b6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877830"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuera en modell till ett Azure Kubernetes Service kluster

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webbtjänst på Azure Kubernetes Service (AKS). Azure Kubernetes Service är bra för storskaliga produktionsdistributioner. Använd Azure Kubernetes-tjänsten om du behöver en eller flera av följande funktioner:

- __Snabb svarstid__
- __Automatisk skalning av__ den distribuerade tjänsten
- __Loggning__
- __Insamling av modelldata__
- __Autentisering__
- __TLS-avslutning__
- __Maskinvaruaccelerationsalternativ__ som GPU och fält programmerbara grindmatriser (FPGA)

När du distribuerar till Azure Kubernetes Service distribuerar du till ett AKS-kluster som är __anslutet till din arbetsyta.__ Information om hur du ansluter ett AKS-kluster till arbetsytan finns i [Skapa och koppla ett Azure Kubernetes Service kluster](how-to-create-attach-kubernetes.md).

> [!IMPORTANT]
> Vi rekommenderar att du felsöker lokalt innan du distribuerar till webbtjänsten. Mer information finns i [Felsöka lokalt](./how-to-troubleshoot-deployment-local.md)
>
> Du kan också läsa Azure Machine Learning – [Distribuera till lokal notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i Skapa [en Azure Machine Learning arbetsyta.](how-to-manage-workspace.md)

- En maskininlärningsmodell registrerad på din arbetsyta. Om du inte har en registrerad modell kan du se Hur [och var du distribuerar modeller.](how-to-deploy-and-where.md)

- [Azure CLI-tillägget för Machine Learning,](reference-azure-machine-learning-cli.md) [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)eller Azure Machine Learning Visual Studio [Code-tillägget](tutorial-setup-vscode-extension.md).

- Python-kodfragmenten i den här artikeln förutsätter att följande variabler har angetts: 

    * `ws` – Ställ in på din arbetsyta.
    * `model` – Ange till din registrerade modell.
    * `inference_config` – Ställ in på modellens inferenskonfiguration.

    Mer information om hur du anger dessa variabler finns i [Hur och var du distribuerar modeller.](how-to-deploy-and-where.md)

- CLI-kodfragmenten i den här artikeln förutsätter att du har skapat ett  `inferenceconfig.json` dokument. Mer information om hur du skapar det här dokumentet finns [i Hur och var du distribuerar modeller.](how-to-deploy-and-where.md)

- Ett Azure Kubernetes Service som är anslutet till din arbetsyta. Mer information finns i Skapa [och koppla ett Azure Kubernetes Service kluster](how-to-create-attach-kubernetes.md).

    - Om du vill distribuera modeller till GPU-noder eller FPGA-noder (eller någon specifik SKU) måste du skapa ett kluster med den specifika SKU:n. Det finns inget stöd för att skapa en sekundär nodpool i ett befintligt kluster och distribuera modeller i den sekundära nodpoolen.

## <a name="understand-the-deployment-processes"></a>Förstå distributionsprocesserna

Ordet "distribution" används i både Kubernetes och Azure Machine Learning. "Distribution" har olika betydelser i dessa två kontexter. I Kubernetes är en `Deployment` konkret entitet som anges med en deklarativ YAML-fil. En Kubernetes har `Deployment` en definierad livscykel och konkreta relationer till andra Kubernetes-entiteter som `Pods` och `ReplicaSets` . Du kan lära dig mer om Kubernetes från dokument och videor [på Vad är Kubernetes?](https://aka.ms/k8slearning).

I Azure Machine Learning används "distribution" i en mer allmän betydelse för att göra tillgängliga och rensa dina projektresurser. De steg som Azure Machine Learning överväger en del av distributionen är:

1. Zippa filerna i projektmappen och ignorera dem som anges i .amlignore eller .gitignore
1. Skala upp beräkningsklustret (relaterar till Kubernetes)
1. Skapa eller ladda ned dockerfilen till beräkningsnoden (relaterar till Kubernetes)
    1. Systemet beräknar hashvärdet: 
        - Basavbildningen 
        - Anpassade dockersteg (se Distribuera [en modell med hjälp av en anpassad Docker-basavbildning](./how-to-deploy-custom-docker-image.md))
        - Conda-definitionen YAML (se [Skapa & använda programvarumiljöer i Azure Machine Learning](./how-to-use-environments.md))
    1. Systemet använder denna hash som nyckel i en sökning av arbetsytans Azure Container Registry (ACR)
    1. Om den inte hittas söker den efter en matchning i det globala ACR
    1. Om den inte hittas skapar systemet en ny avbildning (som cachelagras och push-skickas till arbetsytans ACR)
1. Ladda ned den komprimerade projektfilen till temporär lagring på beräkningsnoden
1. Packa upp projektfilen
1. Beräkningsnoden som körs `python <entry script> <arguments>`
1. Spara loggar, modellfiler och andra filer som skrivs till `./outputs` det lagringskonto som är associerat med arbetsytan
1. Skala ned beräkning, inklusive att ta bort tillfällig lagring (relaterar till Kubernetes)

### <a name="azure-ml-router"></a>Azure ML-router

Frontend-komponenten (azureml-fe) som dirigerar inkommande inferensbegäranden till distribuerade tjänster skalar automatiskt efter behov. Skalningen av azureml-fe baseras på AKS-klustrets syfte och storlek (antalet noder). Klustersyftet och noderna konfigureras när du [skapar eller kopplar ett AKS-kluster.](how-to-create-attach-kubernetes.md) Det finns en azureml-fe-tjänst per kluster, som kan köras på flera poddar.

> [!IMPORTANT]
> När du använder ett kluster som __konfigurerats som dev-test__ inaktiveras självskalaren . 

Azureml-fe skalar både upp (lodrätt) för att använda fler kärnor och ut (vågrätt) för att använda fler poddar. När du fattar beslutet att skala upp används den tid det tar att dirigera inkommande inferensbegäranden. Om den här tiden överskrider tröskelvärdet sker en uppskalning. Om tiden för att dirigera inkommande begäranden fortsätter att överskrida tröskelvärdet sker en utskalning.

Vid nedskalning och inskalning används CPU-användning. Om tröskelvärdet för CPU-användning uppnås skalas först fronten ned. Om CPU-användningen sjunker till tröskelvärdet för inskalning sker en inskalningsåtgärd. Upp- och utskalning sker bara om det finns tillräckligt med tillgängliga klusterresurser.

## <a name="understand-connectivity-requirements-for-aks-inferencing-cluster"></a>Förstå anslutningskraven för slutsatsdragningskluster i AKS

När Azure Machine Learning skapar eller bifogar ett AKS-kluster distribueras AKS-klustret med någon av följande två nätverksmodeller:
* Kubenet-nätverk – Nätverksresurserna skapas och konfigureras vanligtvis när AKS-klustret distribueras.
* Azure CNI-nätverk (Container Networking Interface) – AKS-klustret ansluts till befintliga resurser och konfigurationer för virtuella nätverk.

För det första nätverksläget skapas och konfigureras nätverk korrekt för Azure Machine Learning Service. Eftersom klustret är anslutet till ett befintligt virtuellt nätverk i det andra nätverksläget, särskilt när anpassat DNS används för befintligt virtuellt nätverk, måste kunden vara extra uppmärksam på anslutningskraven för AKS-inferenskluster och säkerställa DNS-upplösning och utgående anslutning för AKS-inferens.

Följande diagram visar alla anslutningskrav för AKS-inferens. Svarta pilar representerar faktisk kommunikation och blå pilar representerar domännamnen som kundkontrollerad DNS ska matcha.

 ![Anslutningskrav för AKS-inferens](./media/how-to-deploy-aks/aks-network.png)

### <a name="overall-dns-resolution-requirements"></a>Övergripande krav för DNS-lösning
DNS-upplösningen i det befintliga virtuella nätverket är under kundens kontroll. Följande DNS-poster ska kunna matchas:
* AKS API-server i form av \<cluster\> .hcp. \<region\> . azmk8s.io
* Microsoft Container Registry (MCR): mcr.microsoft.com
* Kundens Azure Container Registry (ARC) i form av \<ACR name\> .azurecr.io
* Azure Storage konto i form av \<account\> .table.core.windows.net och \<account\> .blob.core.windows.net
* (Valfritt) För AAD-autentisering: api.azureml.ms
* Bedömning av slutpunktens domännamn, antingen automatiskt genererat av Azure ML eller ett anpassat domännamn. Det automatiskt genererade domännamnet skulle se ut så här: \<leaf-domain-label \+ auto-generated suffix\> \<region\> . cloudapp.azure.com

### <a name="connectivity-requirements-in-chronological-order-from-cluster-creation-to-model-deployment"></a>Anslutningskrav i kronologisk ordning: från klusterskapande till modelldistribution

Under processen för att skapa eller koppla AKS distribueras Azure ML-routern (azureml-fe) till AKS-klustret. För att kunna distribuera Azure ML-routern ska AKS-noden kunna:
* Matcha DNS för AKS API-server
* Matcha DNS för MCR för att ladda ned Docker-avbildningar för Azure ML-routern
* Ladda ned avbildningar från MCR, där utgående anslutning krävs

Direkt efter att azureml-fe har distribuerats försöker den starta och detta kräver att:
* Matcha DNS för AKS API-server
* Fråga AKS API-servern för att identifiera andra instanser av sig själv (det är en tjänst med flera poddar)
* Ansluta till andra instanser av sig själv

När azureml-fe har startats krävs ytterligare anslutning för att fungera korrekt:
* Ansluta till Azure Storage för att ladda ned dynamisk konfiguration
* Matcha DNS för AAD-api.azureml.ms och kommunicera med den när den distribuerade tjänsten använder AAD-autentisering.
* Fråga AKS API-servern för att identifiera distribuerade modeller
* Kommunicera med distribuerade modell-POD:er

Vid modelldistributionen bör AKS-noden för en lyckad modelldistribution kunna: 
* Matcha DNS för kundens ACR
* Ladda ned avbildningar från kundens ACR
* Matcha DNS för Azure-blob där modellen lagras
* Ladda ned modeller från Azure-blob

När modellen har distribuerats och tjänsten startar identifierar azureml-fe den automatiskt med hjälp av AKS-API:et och är redo att dirigera begäran till den. Det måste kunna kommunicera med modell-POD:er.
>[!Note]
>Om den distribuerade modellen kräver anslutning (t.ex. frågor till en extern databas eller annan REST-tjänst, hämtning av en BLOB osv.) ska både DNS-upplösning och utgående kommunikation för dessa tjänster aktiveras.

## <a name="deploy-to-aks"></a>Distribuera till AKS

Om du vill distribuera en modell Azure Kubernetes Service skapa en __distributionskonfiguration som__ beskriver de beräkningsresurser som behövs. Till exempel antal kärnor och minne. Du behöver också en __härledningskonfiguration__ som beskriver den miljö som behövs som värd för modellen och webbtjänsten. Mer information om hur du skapar inferenskonfigurationen finns i [Så här och var du distribuerar modeller.](how-to-deploy-and-where.md)

> [!NOTE]
> Antalet modeller som ska distribueras är begränsat till 1 000 modeller per distribution (per container).

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

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referensdokument:

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill distribuera med hjälp av CLI använder du följande kommando. Ersätt `myaks` med namnet på AKS-beräkningsmålet. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen. Ersätt `myservice` med namnet som ska ge den här tjänsten:

```azurecli-interactive
az ml model deploy --ct myaks -m mymodel:1 -n myservice --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Mer information finns i referensen [för az ml model deploy.](/cli/azure/ml/model#az_ml_model_deploy)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Information om hur du använder VS Code finns [i Distribuera till AKS via VS Code-tillägget](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Distribution via VS Code kräver att AKS-klustret skapas eller kopplas till din arbetsyta i förväg.

---

### <a name="autoscaling"></a>Automatisk skalning

Komponenten som hanterar automatisk skalning för Azure ML-modelldistributioner är azureml-fe, som är en router för smart begäran. Eftersom alla inferensbegäranden går igenom den har den nödvändiga data för att automatiskt skala de distribuerade modellerna.

> [!IMPORTANT]
> * **Aktivera inte Kubernetes Horizontal Pod Autoscaler (HPA) för modelldistributioner.** Detta skulle leda till att de två komponenterna för automatisk skalning konkurrerar med varandra. Azureml-fe är utformat för att skala modeller automatiskt som distribueras av Azure ML, där HPA skulle behöva gissa eller göra en ungefärlig modellanvändning utifrån ett allmänt mått som CPU-användning eller en anpassad måttkonfiguration.
> 
> * **Azureml-fe skalar inte antalet noder i ett AKS-kluster** eftersom detta kan leda till oväntade kostnadsökningar. I stället **skalar den antalet repliker för modellen inom** de fysiska klustergränserna. Om du behöver skala antalet noder i klustret kan du skala klustret manuellt eller konfigurera autoskalning av [AKS-kluster.](../aks/cluster-autoscaler.md)

Autoskalning kan styras genom att `autoscale_target_utilization` ange , och för `autoscale_min_replicas` `autoscale_max_replicas` AKS-webbtjänsten. Följande exempel visar hur du aktiverar autoskalning:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Beslut om att skala upp/ned baseras på användningen av de aktuella containerreplikerna. Antalet repliker som är upptagna (bearbetning av en begäran) dividerat med det totala antalet aktuella repliker är den aktuella användningen. Om det här antalet `autoscale_target_utilization` överskrider skapas fler repliker. Om den är lägre minskas replikerna. Som standard är målanvändningen 70 %.

Beslut om att lägga till repliker är otåliga och snabba (cirka 1 sekund). Beslut om att ta bort repliker är försiktigt (cirka 1 minut).

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

Mer information om hur du `autoscale_target_utilization` anger , och finns i `autoscale_max_replicas` `autoscale_min_replicas` [modulreferensen för AksWebservice.](/python/api/azureml-core/azureml.core.webservice.akswebservice)

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Distribuera modeller till AKS med kontrollerad distribution (förhandsversion)

Analysera och höja upp modellversioner på ett kontrollerat sätt med hjälp av slutpunkter. Du kan distribuera upp till sex versioner bakom en enda slutpunkt. Slutpunkter har följande funktioner:

* Konfigurera __procentandelen bedömningstrafik som skickas till varje slutpunkt.__ Du kan till exempel dirigera 20 % av trafiken till slutpunkten "test" och 80 % till "produktion".

    > [!NOTE]
    > Om du inte står för 100 % av trafiken dirigeras eventuell återstående procentandel till __standardslutpunktens__ version. Om du till exempel konfigurerar slutpunktsversionen "test" för att få 10 % av trafiken och "prod" för 30 % skickas återstående 60 % till standardslutpunktsversionen.
    >
    > Den första slutpunktsversionen som skapas konfigureras automatiskt som standard. Du kan ändra detta genom att ange `is_default=True` när du skapar eller uppdaterar en slutpunktsversion.
     
* Tagga en slutpunktsversion __som__ kontroll eller __behandling__. Till exempel kan den aktuella versionen av produktionsslutpunkten vara kontrollen, medan potentiella nya modeller distribueras som behandlingsversioner. När du har utvärderat behandlingsversionerna och en överträffar den aktuella kontrollen kan den befordras till den nya produktionen/kontrollen.

    > [!NOTE]
    > Du kan bara ha __en__ kontroll. Du kan ha flera läkemedel.

Du kan aktivera App Insights för att visa driftmått för slutpunkter och distribuerade versioner.

### <a name="create-an-endpoint"></a>Skapa en slutpunkt
När du är redo att distribuera dina modeller skapar du en bedömningsslutpunkt och distribuerar din första version. I följande exempel visas hur du distribuerar och skapar slutpunkten med sdk:n. Den första distributionen definieras som standardversion, vilket innebär att ospecificerad trafik percentil för alla versioner kommer att gå till standardversionen.  

> [!TIP]
> I följande exempel anger konfigurationen den ursprungliga slutpunktsversionen för att hantera 20 % av trafiken. Eftersom det här är den första slutpunkten är det också standardversionen. Och eftersom vi inte har några andra versioner för den andra 80 % av trafiken dirigeras den även till standardversionen. Tills andra versioner som tar en procentandel av trafiken distribueras tar den här effektivt emot 100 % av trafiken.

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

### <a name="update-and-add-versions-to-an-endpoint"></a>Uppdatera och lägga till versioner till en slutpunkt

Lägg till en annan version i slutpunkten och konfigurera den bedömningstrafik percentil som går till versionen. Det finns två typer av versioner, en kontroll och en behandlingsversion. Det kan finnas flera behandlingsversioner för att jämföra med en enda kontrollversion.

> [!TIP]
> Den andra versionen, som skapas av följande kodfragment, accepterar 10 % av trafiken. Den första versionen är konfigurerad för 20 %, så endast 30 % av trafiken konfigureras för specifika versioner. Återstående 70 % skickas till den första slutpunktsversionen eftersom det också är standardversionen.

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

Uppdatera befintliga versioner eller ta bort dem i en slutpunkt. Du kan ändra versionens standardtyp, kontrolltyp och trafik percentil. I följande exempel ökar den andra versionen trafiken till 40 % och är nu standard.

> [!TIP]
> Efter följande kodfragment är den andra versionen nu standard. Den är nu konfigurerad för 40 %, medan den ursprungliga versionen fortfarande är konfigurerad för 20 %. Det innebär att 40 % av trafiken inte redovisas av versionskonfigurationer. Den överblandade trafiken dirigeras till den andra versionen eftersom den nu är standard. Den tar effektivt emot 80 % av trafiken.

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

När du distribuerar till Azure Kubernetes Service __aktiveras nyckelbaserad__ autentisering som standard. Du kan också aktivera __tokenbaserad__ autentisering. Tokenbaserad autentisering kräver att klienter använder ett Azure Active Directory för att begära en autentiseringstoken, som används för att göra begäranden till den distribuerade tjänsten.

Om __du vill__ inaktivera autentisering anger du `auth_enabled=False` parametern när du skapar distributionskonfigurationen. I följande exempel inaktiveras autentisering med hjälp av SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Information om hur du autentiserar från ett klientprogram finns i [Använda en Azure Machine Learning-modell distribuerad som en webbtjänst.](how-to-consume-web-service.md)

### <a name="authentication-with-keys"></a>Autentisering med nycklar

Om nyckelautentisering är aktiverat kan du använda metoden `get_keys` för att hämta en primär och sekundär autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du behöver återskapa en nyckel använder du [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29)

### <a name="authentication-with-tokens"></a>Autentisering med token

Om du vill aktivera tokenautentisering `token_auth_enabled=True` anger du parametern när du skapar eller uppdaterar en distribution. I följande exempel aktiveras tokenautentisering med hjälp av SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Om tokenautentisering är aktiverat kan du använda metoden för `get_token` att hämta en JWT-token och denna tokens förfallotid:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter tokens `refresh_by` tid.
>
> Microsoft rekommenderar starkt att du skapar din Azure Machine Learning i samma region som Azure Kubernetes Service klustret. För att autentisera med en token gör webbtjänsten ett anrop till den region där Azure Machine Learning-arbetsytan skapas. Om arbetsytans region inte är tillgänglig kan du inte hämta en token för webbtjänsten även om klustret finns i en annan region än din arbetsyta. Detta resulterar i att tokenbaserad autentisering blir otillgänglig tills arbetsytans region är tillgänglig igen. Ju större avståndet mellan klustrets region och arbetsytans region, desto längre tid tar det att hämta en token.
>
> Om du vill hämta en token måste du använda Azure Machine Learning SDK eller [kommandot az ml service get-access-token.](/cli/azure/ml/service#az_ml_service_get_access_token)


### <a name="vulnerability-scanning"></a>Sårbarhetsgenomsökning

Azure Security Center erbjuder enhetlig säkerhetshantering och avancerat skydd mot hot i olika hybridmolnarbetsbelastningar. Du bör tillåta Azure Security Center genomsöka dina resurser och följa dess rekommendationer. Mer information finns i [Azure Kubernetes Services-integrering med Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="next-steps"></a>Nästa steg

* [Använda Azure RBAC för Kubernetes-auktorisering](../aks/manage-azure-rbac.md)
* [Säker inferensmiljö med Azure Virtual Network](how-to-secure-inferencing-vnet.md)
* [Så här distribuerar du en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Felsökning av distribution](how-to-troubleshoot-deployment.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Använda en ML-modell som distribuerats som en webbtjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
