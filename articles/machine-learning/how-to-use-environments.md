---
title: Använda program miljöer
titleSuffix: Azure Machine Learning
description: Skapa och hantera miljöer för modell utbildning och distribution. Hantera python-paket och andra inställningar för miljön.
services: machine-learning
author: saachigopal
ms.author: sagopal
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 4388c1a43cf958133356ed9d1ac80aec3ba0ce16
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093649"
---
# <a name="create--use-software-environments-in-azure-machine-learning"></a>Skapa & använda program varu miljöer i Azure Machine Learning


I den här artikeln får du lära dig hur du skapar och hanterar Azure Machine Learning [miljöer](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py). Använd miljöer för att spåra och återskapa dina projekts program varu beroenden när de utvecklas.

Program beroende hantering är en vanlig uppgift för utvecklare. Du vill se till att build-versioner är reproducerbara utan en omfattande manuell program konfiguration. Azure Machine Learning `Environment` klass konton för lokala utvecklings lösningar som pip och Conda och distribuerad moln utveckling med Docker-funktioner.

I exemplen i den här artikeln visas hur du:

* Skapa en miljö och ange paket beroenden.
* Hämta och uppdatera miljöer.
* Använd en miljö för utbildning.
* Använd en miljö för webb tjänst distribution.

En översikt över hur miljöer fungerar i Azure Machine Learning finns i [Vad är ml-miljöer?](concept-environments.md) Information om hur du konfigurerar utvecklings miljöer finns [här](how-to-configure-environment.md).

## <a name="prerequisites"></a>Förutsättningar

* [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0)
* En [Azure Machine Learning arbets yta](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Skapa en miljö

I följande avsnitt hittar du flera olika sätt att skapa en miljö för experimenten.

### <a name="instantiate-an-environment-object"></a>Instansiera ett miljö objekt

Om du vill skapa en miljö manuellt importerar du `Environment` klassen från SDK: n. Använd sedan följande kod för att instansiera ett miljö objekt.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-a-curated-environment"></a>Använda en granskad miljö

Granskade miljöer innehåller samlingar med python-paket och är tillgängliga i arbets ytan som standard. De här miljöerna backas upp av cachelagrade Docker-avbildningar som minskar kostnaden för att köra förberedelser. Du kan välja någon av dessa populära granskade miljöer för att börja med: 

* _Azureml-minimal_ miljö innehåller en minimal uppsättning paket för att aktivera kör spårning och till gångs överföring. Du kan använda den som utgångs punkt för din egen miljö.

* _Azureml – själv studie_ miljön innehåller gemensamma data vetenskaps paket. Paketen innehåller Scikit-lär, Pandas, matplotlib och en större uppsättning azureml-SDK-paket.

En lista över granskade miljöer finns i [artikeln granskade miljöer](resource-curated-environments.md).

Använd `Environment.get` metoden för att välja en av de granskade miljöerna:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Du kan visa en lista över de granskade miljöerna och deras paket med hjälp av följande kod:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Starta inte ditt eget miljö namn med _azureml_ -prefixet. Det här prefixet är reserverat för granskade miljöer.

### <a name="use-conda-dependencies-or-pip-requirements-files"></a>Använd filer för Conda-beroenden eller pip-krav

Du kan skapa en miljö från en Conda-specifikation eller en fil för pip-krav. Använd [`from_conda_specification()`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-conda-specification-name--file-path-) metoden eller [`from_pip_requirements()`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-pip-requirements-name--file-path-) metoden. I metod argumentet inkluderar du ditt miljö namn och sökvägen till den fil som du vill använda. 

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv",
                                          file_path = "path-to-pip-requirements-file")                                          
```

### <a name="enable-docker"></a>Aktivera Docker

När du aktiverar Docker skapar Azure Machine Learning en Docker-avbildning och skapar en python-miljö i behållaren, utifrån dina specifikationer. Docker-avbildningarna cachelagras och återanvänds: den första körningen i en ny miljö tar vanligt vis längre tid när avbildningen skapas.

[`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection?preserve-view=true&view=azure-ml-py)I `Environment` klassen Azure Machine Learning kan du finjustera och kontrol lera gäst operativ systemet som du kör din utbildning på. `arguments`Variabeln kan användas för att ange extra argument som ska skickas till kommandot Docker Run.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Som standard visas den nyligen inbyggda Docker-avbildningen i behållar registret som är kopplat till arbets ytan.  Databas namnet har formatet *azureml/azureml_ \<uuid\>*. Den unika identifieraren (*UUID*) i namnet motsvarar en hash som beräknas från miljö konfigurationen. Den här korrespondensen gör att tjänsten kan avgöra om det redan finns en avbildning för den aktuella miljön för åter användning.

#### <a name="use-a-prebuilt-docker-image"></a>Använd en fördefinierad Docker-avbildning

Som standard använder tjänsten automatiskt en av de Ubuntu Linux-baserade [bas avbildningarna](https://github.com/Azure/AzureML-Containers), särskilt den som definieras av `azureml.core.environment.DEFAULT_CPU_IMAGE` . Sedan installeras alla angivna python-paket som definieras av den angivna Azure ML-miljön. Andra Azure ML-processorer och GPU-grundavbildningar är tillgängliga i behållar [lagrings platsen](https://github.com/Azure/AzureML-Containers). Det är också möjligt att använda en [anpassad Docker-bas avbildning](./how-to-deploy-custom-docker-image.md#create-a-custom-base-image).

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

>[!IMPORTANT]
> Azure Machine Learning stöder endast Docker-avbildningar som tillhandahåller följande program vara:
> * Ubuntu 16,04 eller senare.
> * Conda 4.5. # eller senare.
> * Python 3.5 +.

#### <a name="use-your-own-dockerfile"></a>Använd din egen Dockerfile 

Du kan också ange en anpassad Dockerfile. Det är enklast att starta från en av Azure Machine Learning Base-avbildningar med hjälp av Docker ```FROM``` -kommandot och sedan lägga till egna anpassade steg. Använd den här metoden om du behöver installera icke-python-paket som beroenden. Kom ihåg att ställa in bas avbildningen på ingen.

Observera att python är ett implicit beroende i Azure Machine Learning så att en anpassad Dockerfile måste ha python installerat.

```python
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = "./Dockerfile"
```

När du använder anpassade Docker-avbildningar, rekommenderar vi att du fäster paket versioner för att bättre garantera reproducerbarhet.

#### <a name="specify-your-own-python-interpreter"></a>Ange din egen python-tolk

I vissa situationer kan din anpassade bas avbildning redan innehålla en python-miljö med paket som du vill använda.

Ange parametern om du vill använda dina egna installerade paket och inaktivera Conda `Environment.python.user_managed_dependencies = True` . Se till att bas avbildningen innehåller en python-tolk och har de paket som krävs för utbildnings skriptet.

Om du till exempel vill köra i en grundläggande Miniconda-miljö där NumPy-paketet är installerat ska du först ange en Dockerfile med ett steg för att installera paketet. Ange sedan de användar hanterade beroendena till `True` . 

Du kan också ange en sökväg till en speciell python-tolk i bilden, genom att ange `Environment.python.interpreter_path` variabeln.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

> [!WARNING]
> Om du installerar vissa python-beroenden i Docker-avbildningen och glömmer att ange så `user_managed_dependencies=True` kommer dessa paket inte att finnas i körnings miljön, vilket orsakar körnings problem. Som standard skapar Azure ML en Conda-miljö med beroenden som du har angett, och kör i den miljön i stället för att använda python-bibliotek som du har installerat på bas avbildningen.

#### <a name="retrieve-image-details"></a>Hämta avbildnings information

För en registrerad miljö kan du hämta avbildnings information med hjälp av följande kod där `details` är en instans av [DockerImageDetails](/python/api/azureml-core/azureml.core.environment.dockerimagedetails?preserve-view=true&view=azure-ml-py) (AzureML python SDK >= 1,11) och ger all information om miljö avbildningen, till exempel Dockerfile, register och avbildnings namn.

```python
details = environment.get_image_details(workspace=ws)
```

Använd följande kod för att hämta avbildnings information från en miljö som sparats automatiskt från körningen av en körning:

```python
details = run.get_environment().get_image_details(workspace=ws)
```

### <a name="use-existing-environments"></a>Använda befintliga miljöer

Om du har en befintlig Conda-miljö på den lokala datorn kan du använda tjänsten för att skapa ett miljö objekt. Genom att använda den här strategin kan du återanvända din lokala interaktiva miljö på fjärrkörningar.

Följande kod skapar ett miljö objekt från den befintliga Conda-miljön `mycondaenv` . [`from_existing_conda_environment()`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-existing-conda-environment-name--conda-environment-name-)Metoden används.

``` python
myenv = Environment.from_existing_conda_environment(name="myenv",
                                                    conda_environment_name="mycondaenv")
```

En miljö definition kan sparas i en katalog i ett enkelt redigerbart format med- [`save_to_directory()`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truesave-to-directory-path--overwrite-false-) metoden. När den har ändrats kan en ny miljö instansieras genom att läsa in filer från katalogen.

```python
myenv = Environment.save_to_directory(path="path-to-destination-directory", overwrite=False)
# modify the environment definition
newenv = Environment.load_from_directory(path="path-to-source-directory")
```

### <a name="implicitly-use-the-default-environment"></a>Använd standard miljön implicit

Om du inte anger en miljö i skript körnings konfigurationen innan du skickar körningen, skapas en standard miljö åt dig.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
src = ScriptRunConfig(source_directory=".", script="example.py", compute_target="local")

# Submit the run
run = myexp.submit(config=src)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

## <a name="add-packages-to-an-environment"></a>Lägga till paket i en miljö

Lägg till paket i en miljö med Conda-, pip-eller privata Wheel-filer. Ange varje paket beroende med hjälp av [`CondaDependency`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?preserve-view=true&view=azure-ml-py) klassen. Lägg till den i miljöns `PythonSection` .

### <a name="conda-and-pip-packages"></a>Conda-och pip-paket

Om ett paket är tillgängligt i en Conda-lagringsplats, rekommenderar vi att du använder Conda-installationen i stället för pip-installationen. Conda-paket levereras normalt med färdiga binärfiler som gör installationen mer tillförlitlig.

Följande exempel lägger till i miljön `myenv` . Den lägger till version 1.17.0 av `numpy` . Det lägger också till `pillow` paketet. Exemplet använder metoden respektive [`add_conda_package()`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?preserve-view=true&view=azure-ml-py#&preserve-view=trueadd-conda-package-conda-package-) [`add_pip_package()`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?preserve-view=true&view=azure-ml-py#&preserve-view=trueadd-pip-package-pip-package-) metod.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

Du kan också lägga till miljövariabler i din miljö. De blir sedan tillgängliga med hjälp av OS. Environ. kom i ditt utbildnings skript.

```python
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

>[!IMPORTANT]
> Om du använder samma miljö definition för en annan körning återanvänds den cachelagrade avbildningen av miljön i Azure Machine Learnings tjänsten. Om du skapar en miljö med ett ej fixerat paket beroende kan du till exempel ```numpy``` använda paket versionen som är installerad _när miljön skapades_. Dessutom fortsätter all framtida miljö med matchnings definition att använda den gamla versionen. Mer information finns i [miljö utveckling, cachelagring och åter användning](./concept-environments.md#environment-building-caching-and-reuse).

### <a name="private-python-packages"></a>Privata python-paket

Information om hur du använder python-paket privat och säkert utan att exponera dem för det offentliga Internet finns i artikeln [så här använder du privata python-paket](how-to-use-private-python-packages.md).

## <a name="manage-environments"></a>Hantera miljöer

Hantera miljöer så att du kan uppdatera, spåra och återanvända dem i beräknings mål och med andra användare av arbets ytan.

### <a name="register-environments"></a>Registrera miljöer

Miljön registreras automatiskt med din arbets yta när du skickar en kör eller distribuerar en webb tjänst. Du kan också registrera miljön manuellt med hjälp av- [`register()`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-workspace-) metoden. Den här åtgärden gör miljön till en entitet som spåras och versions hantering i molnet. Entiteten kan delas mellan arbets ytans användare.

Följande kod registrerar `myenv` miljön på `ws` arbets ytan.

```python
myenv.register(workspace=ws)
```

När du använder miljön för första gången i utbildning eller distribution är den registrerad i arbets ytan. Sedan skapas och distribueras det på beräknings målet. Tjänsten cachelagrar miljöerna. Att återanvända en cachelagrad miljö tar mycket mindre tid än att använda en ny tjänst eller en som har uppdaterats.

### <a name="get-existing-environments"></a>Hämta befintliga miljöer

`Environment`Klassen erbjuder metoder som gör att du kan hämta befintliga miljöer i din arbets yta. Du kan hämta miljöer efter namn, som en lista eller av en speciell utbildning. Den här informationen är användbar för fel sökning, granskning och reproducerbarhet.

#### <a name="view-a-list-of-environments"></a>Visa en lista över miljöer

Visa miljöerna i arbets ytan med hjälp av- [`Environment.list(workspace="workspace_name")`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truelist-workspace-) klassen. Välj sedan en miljö som ska återanvändas.

#### <a name="get-an-environment-by-name"></a>Hämta en miljö efter namn

Du kan också få en speciell miljö efter namn och version. I följande kod används [`get()`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-workspace--name--version-none-) metoden för att hämta versionen `1` av `myenv` miljön på `ws` arbets ytan.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Träna en miljö som körs

Om du vill hämta miljön som användes för en speciell körning när utbildningen har slutförts använder du- [`get_environment()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-environment--) metoden i- `Run` klassen.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Uppdatera en befintlig miljö

Anta att du ändrar en befintlig miljö, till exempel genom att lägga till ett python-paket. Det tar tid att skapa en ny version av miljön som sedan skapas när du skickar en körning, distribuerar en modell eller manuellt registrerar miljön. Med-versionen kan du Visa miljöns ändringar över tid. 

Om du vill uppdatera en python-paket version i en befintlig miljö anger du versions numret för paketet. Om du inte använder det exakta versions numret kommer Azure Machine Learning att återanvända den befintliga miljön med dess ursprungliga paket versioner.

### <a name="debug-the-image-build"></a>Felsöka avbildnings versionen

I följande exempel används [`build()`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truebuild-workspace--image-build-compute-none-) metoden för att skapa en miljö manuellt som en Docker-avbildning. Den övervakar utgående loggar från avbildnings versionen med hjälp av [`wait_for_completion()`](/python/api/azureml-core/azureml.core.image%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truewait-for-creation-show-output-false-) . Den skapade avbildningen visas sedan i arbets ytans Azure Container Registry instans. Den här informationen är användbar för fel sökning.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

Det är praktiskt att först bygga avbildningar lokalt med hjälp av- [`build_local()`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truebuild-local-workspace--platform-none----kwargs-) metoden. Ange den valfria parametern om du vill skapa en Docker-avbildning `useDocker=True` . Ange om du vill skicka den resulterande avbildningen till AzureML-arbetsytans behållare register `pushImageToWorkspaceAcr=True` .

```python
build = env.build_local(workspace=ws, useDocker=True, pushImageToWorkspaceAcr=True)
```

> [!WARNING]
>  Att ändra ordningen på beroenden eller kanaler i en miljö leder till en ny miljö och kräver en ny avbildnings version. Dessutom kommer anrop av `build()` metoden för en befintlig avbildning att uppdatera dess beroenden om det finns nya versioner. 

## <a name="use-environments-for-training"></a>Använda miljöer för utbildning

Om du vill skicka en utbildnings körning måste du kombinera din miljö, [Beräkna mål](concept-compute-target.md)och ditt Python-skript i en körnings konfiguration. Den här konfigurationen är ett wrapper-objekt som används för att skicka körningar.

När du skickar in en utbildnings körning kan det ta flera minuter att skapa en ny miljö. Varaktigheten beror på storleken på de nödvändiga beroendena. Miljöerna cachelagras av tjänsten. Så länge miljö definitionen är oförändrad, tillkommer du bara den fullständiga konfigurations tiden en gång.

Följande lokala skript körnings exempel visar var du skulle använda [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?preserve-view=true&view=azure-ml-py) som wrapper-objekt.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Configure the ScriptRunConfig and specify the environment
src = ScriptRunConfig(source_directory=".", script="train.py", target="local", environment=myenv)

# Submit run 
run = exp.submit(src)
```

> [!NOTE]
> Om du vill inaktivera körnings historik eller köra ögonblicks bilder använder du inställningen under `src.run_config.history` .

Om du inte anger miljön i din körnings konfiguration skapar tjänsten en standard miljö när du skickar in din körning.

## <a name="use-environments-for-web-service-deployment"></a>Använda miljöer för webb tjänst distribution

Du kan använda miljöer när du distribuerar din modell som en webb tjänst. Den här funktionen möjliggör ett återreproducerbart, anslutet arbets flöde. I det här arbets flödet kan du träna, testa och distribuera din modell genom att använda samma bibliotek i både din utbildning och din data bearbetning.


Om du definierar en egen miljö för webb tjänst distribution måste du lista `azureml-defaults` med version >= 1.0.45 som ett pip-beroende. Det här paketet innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst.

Om du vill distribuera en webb tjänst kombinerar du miljön, ett bedömnings beräknings skript och en registrerad modell i ditt distributions objekt [`deploy()`](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) . Mer information finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

I det här exemplet förutsätter vi att du har slutfört en tränings körning. Nu vill du distribuera modellen till Azure Container Instances. När du skapar webb tjänsten monteras modell-och poängsättnings filerna på avbildningen och den Azure Machine Learning-härlednings stacken läggs till i avbildningen.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="notebooks"></a>Notebooks

Den här [artikeln](./how-to-access-terminal.md#add-new-kernels) innehåller information om hur du installerar en Conda-miljö som en kernel i en bärbar dator.

[Distribuera en modell med hjälp av en anpassad Docker-basadress](how-to-deploy-custom-docker-image.md) visar hur du distribuerar en modell med hjälp av en anpassad Docker-bas avbildning.

Den här [exempel datorn](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) visar hur du distribuerar en spark-modell som en webb tjänst.

## <a name="create-and-manage-environments-with-the-cli"></a>Skapa och hantera miljöer med CLI

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) speglar de flesta funktionerna i python SDK. Du kan använda den för att skapa och hantera miljöer. De kommandon som vi diskuterar i det här avsnittet demonstrerar de grundläggande funktionerna.

Följande kommando autogenererar filerna för en standard miljö definition i den angivna katalogen. De här filerna är JSON-filer. De fungerar som motsvarande klass i SDK. Du kan använda filerna för att skapa nya miljöer med anpassade inställningar. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Kör följande kommando för att registrera en miljö från en angiven katalog.

```azurecli-interactive
az ml environment register -d myenvdir
```

Kör följande kommando för att visa en lista över alla registrerade miljöer.

```azurecli-interactive
az ml environment list
```

Hämta en registrerad miljö med hjälp av följande kommando.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Nästa steg

* Om du vill använda ett hanterat beräknings mål för att träna en modell, se [Självstudier: träna en modell](tutorial-train-models-with-aml.md).
* När du har en tränad modell lär du dig [hur och var modeller ska distribueras](how-to-deploy-and-where.md).
* Visa [ `Environment` klass SDK-referensen](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py).
