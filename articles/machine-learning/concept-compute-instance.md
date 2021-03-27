---
title: Vad är en Azure Machine Learning-beräkningsinstans?
titleSuffix: Azure Machine Learning
description: Lär dig mer om Azure Machine Learning Compute-instansen, en fullständigt hanterad molnbaserad arbets Station.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/02/2020
ms.openlocfilehash: 4bc9ed87a243a1efb45d7db660ccdd86a19d9801
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628245"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Vad är en Azure Machine Learning-beräkningsinstans?

En Azure Machine Learning beräknings instans är en hanterad molnbaserad arbets station för data forskare.

Med beräknings instanser är det enkelt att komma igång med Azure Machine Learning utveckling samt tillhandahålla funktioner för hantering och företags beredskap för IT-administratörer.  

Använd en beräknings instans som din fullständigt konfigurerade och hanterade utvecklings miljö i molnet för Machine Learning. De kan också användas som beräknings mål för utbildning och inferencing för utvecklings-och testnings ändamål.  

För modell utbildning i produktions klass använder du ett [Azure Machine Learning beräknings kluster](how-to-create-attach-compute-cluster.md) med skalnings funktioner för flera noder. För modell distribution av produktions klass använder du [Azure Kubernetes service-kluster](how-to-deploy-azure-kubernetes-service.md).

För att Compute instance Jupyter-funktionen ska fungera kontrollerar du att WebSocket-kommunikation inte är inaktiverat. Kontrol lera att nätverket tillåter WebSocket-anslutningar till *. instances.azureml.net och *. instances.azureml.ms.

## <a name="why-use-a-compute-instance"></a>Varför ska man använda en beräknings instans?

En beräknings instans är en fullständigt hanterad molnbaserad arbets station som är optimerad för din Machine Learning Development-miljö. Det ger följande fördelar:

|Viktiga fördelar|Description|
|----|----|
|Produktivitet|Du kan bygga och distribuera modeller med integrerade antecknings böcker och följande verktyg i Azure Machine Learning Studio:<br/>– Jupyter<br/>- JupyterLab<br/>-RStudio (för hands version)<br/>Compute-instansen är helt integrerad med Azure Machine Learning-arbetsyta och Studio. Du kan dela antecknings böcker och data med andra data forskare på arbets ytan.<br/> Du kan också använda [vs Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) med beräknings instanser.
|Hanterad & säker|Minska din säkerhets storlek och Lägg till efterlevnad med företagets säkerhets krav. Beräknings instanser ger robusta hanterings principer och säkra nätverkskonfigurationer som:<br/><br/>– Autoetablering från Resource Manager-mallar eller Azure Machine Learning SDK<br/>- [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md)<br/>- [Stöd för virtuella nätverk](./how-to-secure-training-vnet.md#compute-instance)<br/>– SSH-princip för att aktivera/inaktivera SSH-åtkomst<br/>TLS 1,2 aktiverat |
|Förkonfigurerat &nbsp; för &nbsp; ml|Spara tid på installations aktiviteter med förkonfigurerade och uppdaterade ML-paket, ramverk för djup inlärning, GPU-drivrutiner.|
|Helt anpassningsbar|Brett stöd för virtuella Azure-datorer, inklusive GPU: er och beständiga anpassningar på låg nivå, till exempel installation av paket och driv rutiner gör avancerade scenarier till en enkelt. |

Du kan själv [skapa en beräknings instans](how-to-create-manage-compute-instance.md?tabs=python#create) eller en administratör för att [skapa en beräknings instans](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview).

## <a name="tools-and-environments"></a><a name="contents"></a>Verktyg och miljöer

> [!IMPORTANT]
> Objekt som marker ATS (för hands version) i den här artikeln finns för närvarande i offentlig för hands version.
> För hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azure Machine Learning Compute-instansen kan du skapa, träna och distribuera modeller i en helt integrerad Notebook-miljö i din arbets yta.

Du kan köra Jupyter-anteckningsböcker i [vs Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) med beräknings instanser som fjärrserver utan SSH behövs. Du kan också aktivera VS-kod integrering via [fjärr-SSH-tillägg](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/).

Du kan [installera paket](how-to-access-terminal.md#install-packages) och [lägga till kernels](how-to-access-terminal.md#add-new-kernels) i din beräknings instans.  

Följande verktyg och miljöer är redan installerade på beräknings instansen: 

|Allmänna verktyg & miljöer|Information|
|----|:----:|
|Drivrutiner|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-bibliotek||
|Azure CLI ||
|Azure Machine Learning exempel ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|**R** -verktyg & miljöer|Information|
|----|:----:|
|RStudio-server med öppen källkod (för hands version)||
|R-kernel||
|Azure Machine Learning SDK för R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK-exempel|

|**Python** -verktyg & miljöer|Information|
|----|----|
|Anaconda Python||
|Jupyter och tillägg||
|Jupyterlab och tillägg||
[Azure Machine Learning-SDK för Python](/python/api/overview/azure/ml/intro)</br>från PyPI|Innehåller de flesta av de azureml extra paketen.  Om du vill se hela listan [öppnar du ett terminalfönster på beräknings instansen](how-to-access-terminal.md) och kör <br/> `conda list -n azureml_py36 azureml*` |
|Andra PyPI-paket|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-paket|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Djup inlärnings paket|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-paket|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning python & R SDK-exempel||

Python-paketen installeras i **python 3,6-azureml-** miljön.  

## <a name="accessing-files"></a>Komma åt filer

Antecknings böcker och R-skript lagras på arbets ytans standard lagrings konto i Azure-filresursen.  Dessa filer finns under katalogen "användarfiler". Den här lagringen gör det enkelt att dela antecknings böcker mellan beräknings instanser. Lagrings kontot sparar också dina antecknings böcker på ett säkert sätt när du stoppar eller tar bort en beräknings instans.

Azure-filresursen på din arbets yta monteras som en enhet på beräknings instansen. Den här enheten är standard arbets katalogen för Jupyter, Jupyter Labs och RStudio. Det innebär att de antecknings böcker och andra filer som du skapar i Jupyter, JupyterLab eller RStudio automatiskt lagras på fil resursen och är tillgängliga för användning i andra beräknings instanser.

Filerna i fil resursen är tillgängliga från alla beräknings instanser i samma arbets yta. Eventuella ändringar av de här filerna på beräknings instansen kommer att bli tillförlitligt bestående tillbaka till fil resursen.

Du kan också klona de senaste Azure Machine Learning exemplen till din mapp under katalogen användarfiler i fil resursen för arbets ytan.

Att skriva små filer kan vara långsammare på nätverks enheter än att skriva till själva data bearbetnings instansen.  Om du skriver många små filer kan du försöka använda en katalog direkt på beräknings instansen, till exempel en `/tmp` katalog. Observera att de här filerna inte är tillgängliga från andra beräknings instanser. 

Du kan använda `/tmp` katalogen på beräknings instansen för dina temporära data.  Skriv dock inte stora filer av data på beräknings instansens OS-disk.  Använd [data lager](concept-azure-machine-learning-architecture.md#datasets-and-datastores) i stället. Om du har installerat JupyterLab git-tillägg kan det också leda till långsam prestanda för beräknings instanser.

## <a name="managing-a-compute-instance"></a>Hantera en beräknings instans

I arbets ytan i Azure Machine Learning Studio väljer du **Compute** och sedan **beräknings instans** överst.

![Hantera en beräknings instans](./media/concept-compute-instance/manage-compute-instance.png)

Du kan utföra följande åtgärder:

* [Skapa en beräknings instans](#create). 
* Uppdatera fliken beräknings instanser.
* Starta, stoppa och starta om en beräknings instans.  Du betalar för instansen när den körs. Stoppa beräknings instansen när du inte använder den för att minska kostnaderna. Att stoppa en beräknings instans frigör den. Starta den sedan igen när du behöver den. Obs! Om du stoppar beräknings instansen avbryts faktureringen för beräknings timmar, men du kommer fortfarande att faktureras för disk, offentlig IP och standard belastnings utjämning.
* Ta bort en beräknings instans.
* Filtrera listan över beräknings instanser så att endast de som du har skapat visas.

För varje beräknings instans i arbets ytan som du kan använda kan du:

* Åtkomst Jupyter, JupyterLab, RStudio på beräknings instansen
* SSH till beräknings instans. SSH-åtkomst är inaktive rad som standard men kan aktive ras vid skapande av beräknings instanser. SSH-åtkomst sker via en funktion för offentlig/privat nyckel. På fliken får du information om SSH-anslutning, till exempel IP-adress, användar namn och port nummer.
* Hämta information om en angiven beräknings instans, till exempel IP-adress och region.

Med [Azure RBAC](../role-based-access-control/overview.md) kan du styra vilka användare i arbets ytan som kan skapa, ta bort, starta, stoppa och starta om en beräknings instans. Alla användare i arbets ytans deltagare och ägar roll kan skapa, ta bort, starta, stoppa och starta om beräknings instanser i arbets ytan. Men endast skaparen av en angiven beräknings instans, eller användaren som tilldelats om den skapades för deras räkning, tillåts komma åt Jupyter, JupyterLab och RStudio på den beräknings instansen. En beräknings instans är dedikerad till en enda användare som har rot åtkomst och kan terminalen i genom Jupyter/JupyterLab/RStudio. Compute-instansen kommer att ha enkel inloggning och alla åtgärder kommer att använda användarens identitet för Azure RBAC och för att köra experiment körningar. SSH-åtkomsten styrs via mekanismen för offentlig/privat nyckel.

De här åtgärderna kan styras av Azure RBAC:
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/läsning*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/ta bort*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/start/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/stoppa/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/omstart/åtgärd*

Om du vill skapa en beräknings instans måste du ha behörighet för följande åtgärder:
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


### <a name="create-a-compute-instance"></a><a name="create"></a>Skapa en beräkningsinstans

I arbets ytan i Azure Machine Learning Studio [skapar du en ny beräknings instans](how-to-create-attach-compute-studio.md#compute-instance) från antingen **Compute** -avsnittet eller i avsnittet **antecknings böcker** när du är redo att köra en av dina antecknings böcker. 

Du kan också skapa en instans
* Direkt från den [integrerade Notebook-upplevelsen](tutorial-1st-experiment-sdk-setup.md#azure)
* I Azure Portal
* Från Azure Resource Manager mall. En exempel-mall finns i [mallen skapa en Azure Machine Learning beräknings instans](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* Med [Azure Machine Learning SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md)
* Från [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

De dedikerade kärnorna per region per VM-tullkvot och den totala regionala kvoten som gäller för skapande av beräknings instanser, är enhetliga och delade med Azure Machine Learning inlärnings kluster kvot. Att stoppa beräknings instansen frigör inte kvoten för att se till att du kommer att kunna starta om beräknings instansen.


### <a name="create-on-behalf-of-preview"></a>Skapa på uppdrag av (för hands version)

Som administratör kan du skapa en beräknings instans på uppdrag av en data expert och tilldela den instansen till dem med:
* [Azure Resource Manager mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Information om hur du hittar TenantID och ObjectID som behövs i den här mallen finns i [hitta ID-objekt-ID: n för konfiguration av autentisering](../healthcare-apis/fhir/find-identity-object-ids.md).  Du kan också hitta dessa värden i Azure Active Directory-portalen.
* REST-API

Data expert du skapar beräknings instansen för behöver följande Azure RBAC-behörigheter: 
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/start/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/stoppa/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/omstart/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/computes/applicationaccess/Action*

Data expert kan starta, stoppa och starta om beräknings instansen. De kan använda beräknings instansen för:
* Jupyter
* JupyterLab
* RStudio
* Integrerade antecknings böcker

## <a name="compute-target"></a>Beräkningsmål

Du kan använda beräknings instanser som ett [utbildnings mål](concept-compute-target.md#train) som liknar Azure Machine Learning Compute Training-kluster. 

En beräknings instans:
* Har en jobbkö.
* Kör jobb på ett säkert sätt i en virtuell nätverks miljö, utan att företag behöver öppna SSH-porten. Jobbet körs i en behållare miljö och paketerar dina modell beroenden i en Docker-behållare.
* Kan köra flera små jobb parallellt (för hands version).  Två jobb per kärna kan köras parallellt medan resten av jobben placeras i kö.
* Stöd för distribuerade utbildnings jobb med en nod med flera noder

Du kan använda Compute instance som ett lokalt inferencing distributions mål för test-/fel söknings scenarier.

> [!TIP]
> Beräknings instansen har 120 GB OS-disk. Om du får slut på disk utrymme kan du [använda terminalen](how-to-access-terminal.md) för att rensa minst 1-2 GB innan du [stoppar eller startar om](how-to-create-manage-compute-instance.md#manage) beräknings instansen.


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Vad hände med den virtuella Notebook-datorn?

Beräknings instanser ersätter den virtuella Notebook-datorn.  

Alla notebook-filer som lagras i fil resursen och data i arbets ytans data lager kommer att vara tillgängliga från en beräknings instans. Alla anpassade paket som tidigare har installerats på en virtuell dator måste dock installeras om på beräknings instansen. Kvot begränsningar som gäller för att skapa beräknings kluster gäller även för skapande av beräknings instanser.

Det går inte att skapa nya virtuella dator datorer. Men du kan fortfarande komma åt och använda de virtuella datorerna som du har skapat, med fullständig funktionalitet. Du kan skapa beräknings instanser i samma arbets yta som de befintliga virtuella datorerna.


## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera en beräknings instans](how-to-create-manage-compute-instance.md)
* [Självstudie: träna din första ml-modell](tutorial-1st-experiment-sdk-train.md) visar hur du använder en beräknings instans med en integrerad antecknings bok.
