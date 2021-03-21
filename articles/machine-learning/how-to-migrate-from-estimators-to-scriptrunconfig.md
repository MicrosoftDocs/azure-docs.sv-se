---
title: Migrera från Uppskattningar till ScriptRunConfig
titleSuffix: Azure Machine Learning
description: Migrations guide för att migrera från uppskattningar till ScriptRunConfig för att konfigurera utbildnings jobb.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ae0623a11b940a4d142f6bfae02d4b20727a6f55
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518880"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Migrera från uppskattningar till ScriptRunConfig

Fram till nu har det funnits flera metoder för att konfigurera ett utbildnings jobb i Azure Machine Learning via SDK, inklusive uppskattningar, ScriptRunConfig och RunConfiguration på lägre nivå.   För att lösa den här tvetydigheten och inkonsekvensen fören klar vi jobb konfigurations processen i Azure ML.  Du bör nu använda ScriptRunConfig som rekommenderat alternativ för att konfigurera utbildnings jobb. 

Uppskattningar är föråldrade i 1.19.0-versionen av python SDK. Du bör också normalt undvika att uttryckligen instansiera ett RunConfiguration-objekt själv, och i stället konfigurera jobbet med ScriptRunConfig-klassen.

Den här artikeln beskriver vanliga överväganden när du migrerar från uppskattningar till ScriptRunConfig.

> [!IMPORTANT]
> Om du vill migrera till ScriptRunConfig från uppskattningar kontrollerar du att du använder >= 1.15.0 för python SDK.

## <a name="scriptrunconfig-documentation-and-samples"></a>Dokumentation och exempel för ScriptRunConfig
Azure Machine Learning dokumentation och exempel har uppdaterats för att använda [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) för jobb konfiguration och överföring.

Information om hur du använder ScriptRunConfig finns i följande dokumentation:
* [Konfigurera och skicka träningskörningar](how-to-set-up-training-targets.md)
* [Konfigurera PyTorch Training-körningar](how-to-train-pytorch.md)
* [Konfigurera TensorFlow Training-körningar](how-to-train-tensorflow.md)
* [Konfigurera scikit – lära dig utbildnings körningar](how-to-train-scikit-learn.md)

Läs dessutom följande exempel & Självstudier:
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml – exempel](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>Definiera tränings miljön
Även om de olika ramverken för ramverk har förkonfigurerade miljöer som backas upp av Docker-avbildningar, är Dockerfiles för dessa avbildningar privata.  Därför har du inte mycket insyn i vad dessa miljöer innehåller. Dessutom tar uppskattningarna i miljö-relaterade konfigurationer som enskilda parametrar (till exempel `pip_packages` `custom_docker_image` ) på sina respektive konstruktorer.

När du använder ScriptRunConfig kapslas alla miljörelaterade konfigurationer in i `Environment` objektet som skickas till- `environment` parametern för ScriptRunConfig-konstruktorn. Om du vill konfigurera ett utbildnings jobb anger du en miljö som har alla beroenden som krävs för ditt utbildnings skript. Om ingen miljö tillhandahålls använder Azure ML en av bas avbildningarna i Azure ML, särskilt den som definieras av `azureml.core.environment.DEFAULT_CPU_IMAGE` , som standard miljö. Det finns ett par olika sätt att tillhandahålla en miljö:

* [Använd en granskad miljö](how-to-use-environments.md#use-a-curated-environment) -granskade miljöer är fördefinierade miljöer som är tillgängliga på arbets ytan som standard. Det finns en motsvarande granskad miljö för var och en av Docker-avbildningarna för de förkonfigurerade ramverk/versioner som har säkerhetskopierat varje Ramverks uppskattning.
* [Definiera en egen anpassad miljö](how-to-use-environments.md)

Här är ett exempel på hur du använder den granskade PyTorch 1,6-miljön för utbildning:

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

Om du vill ange **miljövariabler** som ska ställas in för processen där övnings skriptet körs, använder du miljö objekt:
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Information om hur du konfigurerar och hanterar Azure ML-miljöer finns i:
* [Så här använder du miljöer](how-to-use-environments.md)
* [Organiserade miljöer](resource-curated-environments.md)
* [Träna med en anpassad Docker-avbildning](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>Använda data för utbildning
### <a name="datasets"></a>Datauppsättningar
Om du använder en Azure ML-datauppsättning för utbildning skickar du data uppsättningen som ett argument till skriptet med hjälp av `arguments` parametern. På så sätt får du data Sök vägen (monterings punkt eller hämtnings Sök väg) i utbildnings skriptet via argument.

I följande exempel konfigureras ett utbildnings jobb där FileDataset, som `mnist_ds` kommer att monteras på fjärrdatorn.
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (gammal)
Vi rekommenderar att du använder Azure ML-datauppsättningar över det gamla DataReference-sättet, om du fortfarande använder DataReferences av någon anledning, måste du konfigurera jobbet enligt följande:
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

Mer information om hur du använder data för utbildning finns i:
* [Träna med data uppsättningar i Azure ML](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>Distribuerad träning
Om du behöver konfigurera ett distribuerat jobb för utbildning gör du det genom att ange `distributed_job_config` parametern i ScriptRunConfig-konstruktorn. Skicka in en [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)eller [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) för distribuerade jobb av respektive typ.

I följande exempel konfigureras ett PyTorch utbildnings jobb för att använda distribuerad utbildning med MPI/Horovod:
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

Mer information finns i:
* [Distribuerad träning med PyTorch](how-to-train-pytorch.md#distributed-training)
* [Distribuerad utbildning med TensorFlow](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>Övriga farliga ämnen
Om du behöver komma åt det underliggande RunConfiguration-objektet för en ScriptRunConfig kan du göra det på följande sätt:
```
src.run_config
```

## <a name="next-steps"></a>Nästa steg

* [Konfigurera och skicka träningskörningar](how-to-set-up-training-targets.md)