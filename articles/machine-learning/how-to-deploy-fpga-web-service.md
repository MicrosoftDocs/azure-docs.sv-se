---
title: Distribuera ML-modeller till FPGAs
titleSuffix: Azure Machine Learning
description: Lär dig mer om Field-programmerbara grind mat ris. Du kan distribuera en webb tjänst på en FPGA med Azure Machine Learning för att få en härledning på ultralåg latens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, devx-track-python, deploy
ms.openlocfilehash: 9e43291325510f92f2e5fd6c07cdb9257fdede9d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033076"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>Distribuera ML-modeller till Field-programmerbara grind mat ris (FPGAs) med Azure Machine Learning 

I den här artikeln får du lära dig mer om FPGAs och hur du distribuerar dina ML-modeller till en Azure-FPGA med hjälp av [python-paketet för maskin accelererade modeller](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py) från [Azure Machine Learning](overview-what-is-azure-ml.md).

## <a name="what-are-fpgas"></a>Vad är FPGAs?
En FPGA innehåller en matris med programmerbara logiska block och en hierarki med omkonfigurerbara anslutningar. Med samkopplingarna kan dessa block konfigureras på olika sätt efter tillverkningen. Jämfört med andra kretsar ger FPGAs en kombination av programmering och prestanda. 

FPGAs gör det möjligt att uppnå låg latens för real tids härledning (eller modell poängsättning). Asynkrona begär Anden (batching) behövs inte. Satsvis kompilering kan orsaka svars tider eftersom mer data behöver bearbetas. Implementeringar av bearbetnings enheter för neurala kräver inte batchbearbetning. svars tiden kan därför vara många gånger lägre jämfört med processor-och GPU-processorer.

Du kan konfigurera om FPGAs för olika typer av Machine Learning-modeller. Den här flexibiliteten gör det enklare att påskynda program baserat på den optimala numeriska precisions-och minnes modellen som används. Eftersom FPGAs kan konfigureras om kan du hålla dig uppdaterad med kraven för att snabbt ändra AI-algoritmer.

![Diagram över Azure Machine Learning FPGA-jämförelse](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processor| Förkortning |Description|
|---|:-------:|------|
|Programspecifika integrerade kretsar|ASICs|Anpassade kretsar, till exempel Googles beskrivare processor enheter (TPU), ger högsta möjliga effektivitet. De kan inte konfigureras om när dina behov ändras.|
|Fält-programmerbara grind mat ris|FPGA:er|FPGAs, till exempel de som är tillgängliga på Azure, ger prestanda nära ASICs. De är också flexibla och Omkonfigurerade över tid, för att implementera ny logik.|
|Enheter för grafik bearbetning|GPU: er|Ett populärt alternativ för AI-beräkningar. GPU: er erbjuder parallella bearbetnings funktioner, vilket gör det snabbare vid bild åter givning än CPU: er.|
|Enheter för central bearbetning|Processorer|Allmänna processorer, vars prestanda inte är idealisk för grafik-och video bearbetning.|

## <a name="fpga-support-in-azure"></a>FPGA-stöd i Azure

Microsoft Azure är världens största moln investering i FPGAs. Microsoft använder FPGAs för DNN-utvärdering (djup neurala Networks), Bing search-rangordning och SDN-acceleration (Software Defined Networking) för att minska svars tiden, samtidigt som processorer frigörs för andra uppgifter.

FPGAs på Azure baseras på Intel: s FPGA-enheter, som data forskare och utvecklare använder för att påskynda AI-beräkningar i real tid. Den här FPGA-aktiverade arkitekturen ger prestanda, flexibilitet och skalbarhet och är tillgänglig i Azure.

Azure FPGAs är integrerade med Azure Machine Learning. Azure kan parallellisera förtränade DNN över FPGAs för att skala ut din tjänst. Hyperoptimerade kan vara förtränad, som en djup upplärda för överförings Inlärning eller finjusteras med uppdaterade vikter.

|Scenarier & konfigurationer på Azure|DNN-modeller som stöds|Regional support|
|--------------------------|--------------------|----------------|
|+ Bild klassificering och igenkännings scenarier<br/>+ TensorFlow-distribution (kräver Tensorflow 1. x)<br/>+ Intel FPGA-maskinvara|– ResNet 50<br/>– ResNet 152<br/>-DenseNet-121<br/>-VGG-16<br/>-SSD-VGG|– Östra USA<br/>-Sydostasien<br/>– Västeuropa<br/>– Västra USA 2|

För att optimera svars tid och data flöde bör klienten som skickar data till FPGA-modellen vara i någon av regionerna ovan (den som du distribuerade modellen till).

**PBS-serien med virtuella Azure-datorer** innehåller Intel Arria 10-FPGAs. Den visas som "standard PBS Family virtuella processorer" när du kontrollerar din Azure-kvot tilldelning. Den virtuella PB6-datorn har sex virtuella processorer och en FPGA. PB6 VM tillhandahålls automatiskt genom Azure Machine Learning under modell distributionen till en FPGA. Den används endast med Azure ML och det går inte att köra godtyckliga bitstreams. Du kommer till exempel inte att kunna blinka FPGA med bitstreams för att göra kryptering, kodning osv.

## <a name="deploy-models-on-fpgas"></a>Distribuera modeller på FPGAs

Du kan distribuera en modell som en webb tjänst på FPGAs med [Azure Machine Learning maskinvaruaccelererade modeller](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py). Om du använder FPGAs får du en utgångs punkt för extremt låg latens, även med en enda batchstorlek. 

I det här exemplet skapar du en TensorFlow-graf för att Förbearbeta indatabilden, gör den till en upplärda med ResNet 50 på en FPGA och kör sedan funktionerna via en klassificerare som har tränats på ImageNet data uppsättningen. Sedan distribueras modellen till ett AKS-kluster.

### <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett konto där [du betalar per](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) användning (kostnads fria Azure-konton är inte berättigade till FPGA-kvot).

- En Azure Machine Learning arbets yta och Azure Machine Learning SDK för python installerat, enligt beskrivningen i [skapa en arbets yta](how-to-manage-workspace.md).
 
- Paketet med maskin varu accelererade modeller:  `pip install --upgrade azureml-accel-models[cpu]`    
    
- [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)

- FPGA-kvot. Skicka en [begäran om kvot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u)eller kör detta CLI-kommando för att kontrol lera kvoten: 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   Kontrol lera att du har minst 6 virtuella processorer under __CurrentValue__ som returneras.  

### <a name="define-the-tensorflow-model"></a>Definiera TensorFlow-modellen

Börja med att använda [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) för att skapa en tjänst definition. En tjänst definition är en fil som beskriver en pipeline med grafer (indata, upplärda och klassificerare) baserat på TensorFlow. Kommandot Deployment komprimerar definitionen och graferna till en ZIP-fil och överför ZIP till Azure Blob Storage. DNN har redan distribuerats för att köras på FPGA.

1. Läs in Azure Machine Learning arbets yta

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. Förbearbeta avbildningen. Indata till webb tjänsten är en JPEG-bild.  Det första steget är att avkoda JPEG-avbildningen och Förbearbeta den.  JPEG-bilderna behandlas som strängar och resultatet är för-och-nivåer som inkommer till ResNet 50-modellen.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Läs in upplärda. Initiera modellen och ladda ned en TensorFlow-kontrollpunkt för quantized-versionen av ResNet50 som ska användas som upplärda.  Ersätt "QuantizedResnet50" i kodfragmentet för att importera andra djup neurala-nätverk:

   - QuantizedResnet152
   - QuantizedVgg16
   - Densenet121

   ```python
   from azureml.accel.models import QuantizedResnet50
   save_path = os.path.expanduser('~/models')
   model_graph = QuantizedResnet50(save_path, is_frozen=True)
   feature_tensor = model_graph.import_graph_def(image_tensors)
   print(model_graph.version)
   print(feature_tensor.name)
   print(feature_tensor.shape)
   ```

1. Lägg till en klassificerare. Den här klassificeraren tränades på ImageNet-data uppsättningen.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. Spara modellen. Nu när Preprocessor, ResNet 50-upplärda och klassificeraren har lästs in, sparar du grafen och de associerade variablerna som en modell.

   ```python
   model_name = "resnet50"
   model_save_path = os.path.join(save_path, model_name)
   print("Saving model in {}".format(model_save_path))
   
   with tf.Session() as sess:
       model_graph.restore_weights(sess)
       tf.saved_model.simple_save(sess, model_save_path,
                                  inputs={'images': in_images},
                                  outputs={'output_alias': classifier_output})
   ```

1. Spara indata och utmatnings nivåer **när du använder dem för modell konverterings-och härlednings begär Anden**. 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   Följande modeller är tillgängliga med sina klassificerare output-nivåer för att få en härledning om du använder standard klassificeraren.

   + Resnet50, QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152, QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121, QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16, QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg, QuantizedSsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>Konvertera modellen till Open neurala Network Exchange format (ONNX)

Innan du kan distribuera till FPGAs konverterar du modellen till [ONNX](https://onnx.ai/) -formatet.

1. [Registrera](concept-model-management-and-deployment.md) modellen med hjälp av SDK med zip-filen i Azure Blob Storage. Genom att lägga till taggar och andra metadata om modellen kan du hålla koll på dina utbildade modeller.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   Om du redan har registrerat en modell och vill läsa in den, kan du hämta den.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. Konvertera TensorFlow-diagrammet till ONNX-formatet.  Du måste ange namnen på indata-och utdataström, så att klienten kan använda dem när du använder webb tjänsten.

   ```python
   from azureml.accel import AccelOnnxConverter

   convert_request = AccelOnnxConverter.convert_tf_model(
       ws, registered_model, input_tensors, output_tensors)

   # If it fails, you can run wait_for_completion again with show_output=True.
   convert_request.wait_for_completion(show_output=False)

   # If the above call succeeded, get the converted model
   converted_model = convert_request.result
   print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
         converted_model.id, converted_model.created_time, '\n')
   ```

### <a name="containerize-and-deploy-the-model"></a>Använd och distribuera modellen

Skapa sedan en Docker-avbildning från den konverterade modellen och alla beroenden.  Docker-avbildningen kan sedan distribueras och instansieras.  Distributions mål som stöds är Azure Kubernetes service (AKS) i molnet eller en gräns enhet som [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md).  Du kan också lägga till taggar och beskrivningar för din registrerade Docker-avbildning.

   ```python
   from azureml.core.image import Image
   from azureml.accel import AccelContainerImage

   image_config = AccelContainerImage.image_configuration()
   # Image name must be lowercase
   image_name = "{}-image".format(model_name)

   image = Image.create(name=image_name,
                        models=[converted_model],
                        image_config=image_config,
                        workspace=ws)
   image.wait_for_creation(show_output=False)
   ```

   Lista avbildningarna efter tagg och hämta detaljerade loggar för eventuell fel sökning.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>Distribuera till ett Azure Kubernetes service-kluster

1. Använd AKS om du vill distribuera din modell som en storskalig produktions webb tjänst. Du kan skapa en ny med hjälp av Azure Machine Learning SDK, CLI eller [Azure Machine Learning Studio](https://ml.azure.com).

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget
    
    # Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
    prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                        agent_count = 1,
                                                        location = "eastus")
    
    aks_name = 'my-aks-cluster'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws,
                                      name=aks_name,
                                      provisioning_configuration=prov_config)
    ```

    AKS-distributionen kan ta cirka 15 minuter.  Kontrol lera om distributionen har slutförts.

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. Distribuera behållaren till AKS-klustret.

    ```python
    from azureml.core.webservice import Webservice, AksWebservice
    
    # For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
    aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=1,
                                                    auth_enabled=False)
    
    aks_service_name = 'my-aks-service'
    
    aks_service = Webservice.deploy_from_image(workspace=ws,
                                               name=aks_service_name,
                                               image=image,
                                               deployment_config=aks_config,
                                               deployment_target=aks_target)
    aks_service.wait_for_deployment(show_output=True)
    ```

#### <a name="deploy-to-a-local-edge-server"></a>Distribuera till en lokal Edge-Server

Alla [Azure Data Box Edge enheter](../databox-online/azure-stack-edge-overview.md
) innehåller en FPGA för att köra modellen.  Endast en modell kan köras på FPGA i taget.  Om du vill köra en annan modell distribuerar du bara en ny behållare. Du hittar anvisningar och exempel kod i [det här Azure-exemplet](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

### <a name="consume-the-deployed-model"></a>Använda den distribuerade modellen

Använd slutligen exempel klienten för att anropa Docker-avbildningen för att hämta förutsägelser från modellen.  Exempel på klient kod är tillgänglig:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Docker-avbildningen stöder gRPC och TensorFlow-ANROPet "predict".

Du kan också hämta en exempel klient för TensorFlow-servar.

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Eftersom den här klassificeraren tränades på [ImageNet](http://www.image-net.org/) -datauppsättningen mappar du klasserna till läsbara etiketter.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-resources"></a>Rensa resurser

Du undviker onödiga kostnader genom att rensa dina resurser **i följande ordning**: webb tjänsten och sedan bild och sedan modellen.

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>Nästa steg

+ Lär dig hur du [skyddar ditt webb tjänst](how-to-secure-web-service.md) dokument.

+ Lär dig mer om FPGA och [Azure Machine Learning priser och kostnader](https://azure.microsoft.com/pricing/details/machine-learning/).

+ [Storskalig maskin vara: ML i skala ovanpå Azure + FPGA: build 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Microsoft FPGA-baserat konfigurerbart moln (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave för AI i real tid](https://www.microsoft.com/research/project/project-brainwave/)

+ [Automatiskt optiskt kontroll system](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
