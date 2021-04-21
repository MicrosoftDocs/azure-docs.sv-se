---
title: Felsöka ML-pipelines
titleSuffix: Azure Machine Learning
description: Så här felsöker du när du får fel när du kör en maskininlärningspipeline. Vanliga fallgropar och tips som hjälper dig att felsöka skript före och under fjärrkörning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 3a85566f395e97bbe88d52a8b306c7e0aa15669d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817349"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Felsöka pipelines för maskininlärning

I den här artikeln får du lära dig hur du felsöker när du får fel när du kör en [maskininlärningspipeline i Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) och Azure Machine Learning [designer](./concept-designer.md). [](concept-ml-pipelines.md) 

## <a name="troubleshooting-tips"></a>Felsökningstips

Följande tabell innehåller vanliga problem under pipelineutvecklingen, med potentiella lösningar.

| Problem | Möjlig lösning |
|--|--|
| Det går inte att skicka data till `PipelineData` katalogen | Se till att du har skapat en katalog i skriptet som motsvarar var din pipeline förväntar sig stegutdata. I de flesta fall definierar ett indataargument utdatakatalogen, och sedan skapar du katalogen explicit. Använd `os.makedirs(args.output_dir, exist_ok=True)` för att skapa utdatakatalogen. I [självstudien](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) finns ett exempel på ett bedömningsskript som visar det här designmönstret. |
| Beroendebuggar | Om du ser beroendefel i din fjärrpipeline som inte skedde vid en lokal testning bekräftar du att fjärrmiljöns beroenden och versioner matchar dem som finns i din testmiljö. (Se [Miljöskapande, cachelagring och återanvändning](./concept-environments.md#environment-building-caching-and-reuse)|
| Tvetydiga fel med beräkningsmål | Försök att ta bort och återskapa beräkningsmål. Det går snabbt att återskapa beräkningsmål och kan lösa vissa tillfälliga problem. |
| Pipeline som inte återanvänder steg | Återanvändning av steg är aktiverat som standard, men se till att du inte har inaktiverat det i ett pipelinesteg. Om återanvändning är inaktiverat `allow_reuse` anges parametern i steget till `False` . |
| Pipelinen kör om i onödan | För att säkerställa att stegen bara körs igen när deras underliggande data eller skript ändras, frikoppla dina källkodskataloger för varje steg. Om du använder samma källkatalog för flera steg kan det uppstå onödiga omkörningar. Använd parametern på ett pipelinestegobjekt för att peka på den isolerade katalogen för det steget och se till att du inte använder `source_directory` samma sökväg för flera `source_directory` steg. |
| Steg som saktar ned över träningsepoker eller annat loopbeteende | Försök att växla alla fil skrivningar, inklusive loggning, `as_mount()` från till `as_upload()` . **Monteringsläget** använder ett fjärrvirtualiserat filsystem och laddar upp hela filen varje gång den läggs till. |
| Det tar lång tid att starta beräkningsmålet | Docker-avbildningar för beräkningsmål läses in från Azure Container Registry (ACR). Som standard Azure Machine Learning en ACR som använder den *grundläggande* tjänstnivån. Om du ändrar ACR för din arbetsyta till standard- eller premiumnivån kan det minska den tid det tar att skapa och läsa in avbildningar. Mer information finns i [Azure Container Registry tjänstnivåer](../container-registry/container-registry-skus.md). |

### <a name="authentication-errors"></a>Autentiseringsfel

Om du utför en hanteringsåtgärd på ett beräkningsmål från ett fjärrjobb får du något av följande fel: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Du får till exempel ett felmeddelande om du försöker skapa eller koppla ett beräkningsmål från en ML-pipeline som skickas för fjärrkörning.
## <a name="troubleshooting-parallelrunstep"></a>Felsökning `ParallelRunStep` 

Skriptet för en `ParallelRunStep` *måste innehålla* två funktioner:
- `init()`: Använd den här funktionen för kostsamma eller vanliga förberedelser för senare slutsatsledning. Du kan till exempel använda den för att läsa in modellen i ett globalt objekt. Den här funktionen anropas bara en gång i början av processen.
-  `run(mini_batch)`: Funktionen körs för varje `mini_batch` instans.
    -  `mini_batch`: `ParallelRunStep` anropar körningsmetoden och skickar antingen en lista eller Pandas `DataFrame` som ett argument till metoden . Varje post i mini_batch är en filsökväg om indata är en `FileDataset` eller en Pandas `DataFrame` om indata är en `TabularDataset` .
    -  `response`: run()-metoden ska returnera en pandas `DataFrame` eller en matris. För append_row output_action läggs dessa returnerade element till i den gemensamma utdatafilen. Till summary_only ignoreras innehållet i elementen. För alla utdataåtgärder anger varje returnerat utdataelement en lyckad körning av indataelementet i indataminibatchen. Kontrollera att tillräckligt med data ingår i körningsresultatet för att mappa indata för att köra utdataresultat. Körningsutdata skrivs i utdatafilen och är inte garanterat i ordning. Du bör använda en nyckel i utdata för att mappa den till indata.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Om du har en annan fil eller mapp i samma katalog som ditt inferensskript kan du referera till den genom att söka efter den aktuella arbetskatalogen.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parametrar för ParallelRunConfig

`ParallelRunConfig` är den större konfigurationen för `ParallelRunStep` instansen inom Azure Machine Learning pipeline. Du kan använda det för att omsluta skriptet och konfigurera nödvändiga parametrar, inklusive alla följande poster:
- `entry_script`: Ett användarskript som en lokal filsökväg som ska köras parallellt på flera noder. Om `source_directory` finns använder du en relativ sökväg. Annars använder du alla sökvägar som är tillgängliga på datorn.
- `mini_batch_size`: Storleken på den minibatch som skickas till ett enda `run()` anrop. (valfritt; standardvärdet är `10` filer för och för `FileDataset` `1MB` `TabularDataset` .)
    - För `FileDataset` är det antalet filer med minimivärdet `1` . Du kan kombinera flera filer i en minibatch.
    - För `TabularDataset` är det storleken på data. Exempelvärden är `1024` , `1024KB` , och `10MB` `1GB` . Det rekommenderade värdet är `1MB` . Minibatchen från `TabularDataset` kommer aldrig att korsa filgränser. Om du till exempel har .csv-filer med olika storlekar är den minsta filen 100 KB och den största är 10 MB. Om du anger `mini_batch_size = 1MB` behandlas filer med en storlek som är mindre än 1 MB som en minibatch. Filer som är större än 1 MB delas upp i flera minibatchar.
- `error_threshold`: Antalet postfel för och `TabularDataset` filfel för `FileDataset` som ska ignoreras under bearbetningen. Om felantalet för hela indata går över det här värdet avbryts jobbet. Feltröskelvärdet är för hela indata och inte för enskilda minibatch som skickas till `run()` metoden. Intervallet är `[-1, int.max]` . Delen `-1` anger att alla fel ignoreras under bearbetningen.
- `output_action`: Ett av följande värden anger hur utdata ska ordnas:
    - `summary_only`: Användarskriptet lagrar utdata. `ParallelRunStep` använder endast utdata för beräkningen av feltröskelvärdet.
    - `append_row`: För alla indata skapas endast en fil i utdatamappen för att lägga till alla utdata avgränsade med rad.
- `append_row_file_name`: Om du vill anpassa namnet på utdatafilen för append_row output_action (valfritt; standardvärdet är `parallel_run_step.txt` ).
- `source_directory`: Sökvägar till mappar som innehåller alla filer som ska köras på beräkningsmålet (valfritt).
- `compute_target`: Stöds `AmlCompute` endast.
- `node_count`: Antalet beräkningsnoder som ska användas för att köra användarskriptet.
- `process_count_per_node`: Antalet processer per nod. Bästa praxis är att ange antalet GPU eller CPU som en nod har (valfritt; standardvärdet är `1` ).
- `environment`: Python-miljödefinitionen. Du kan konfigurera den så att den använder en befintlig Python-miljö eller för att konfigurera en tillfällig miljö. Definitionen ansvarar också för att ange nödvändiga programberoenden (valfritt).
- `logging_level`: Loggens verbositet. Värden i ökande verbositet är: `WARNING` `INFO` , och `DEBUG` . (valfritt; standardvärdet är `INFO` )
- `run_invocation_timeout`: `run()` Tidsgränsen för metodanrop i sekunder. (valfritt; standardvärdet är `60` )
- `run_max_try`: Maximalt antal försök `run()` för en minibatch. Ett `run()` misslyckades om ett undantag returneras eller inget returneras när `run_invocation_timeout` har uppnåtts (valfritt; standardvärdet är `3` ). 

Du kan ange , , , , och som , så att du kan finjustera parametervärdena när du skickar `mini_batch_size` `node_count` en `process_count_per_node` `logging_level` `run_invocation_timeout` `run_max_try` `PipelineParameter` pipelinekörning igen. I det här exemplet använder du `PipelineParameter` för och och du ändrar dessa värden när du skickar en körning igen `mini_batch_size` `Process_count_per_node` senare. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parametrar för att skapa ParallelRunStep

Skapa ParallelRunStep med hjälp av skriptet, miljökonfigurationen och parametrarna. Ange beräkningsmålet som du redan har kopplat till arbetsytan som körningsmål för ditt härledningsskript. Använd `ParallelRunStep` för att skapa pipelinesteget för batch-slutsatsledning, som tar alla följande parametrar:
- `name`: Namnet på steget, med följande namngivningsbegränsningar: unika, 3–32 tecken och regex ^ \[ a-z \] ([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: Ett `ParallelRunConfig` -objekt, som definierades tidigare.
- `inputs`: En eller flera datauppsättningar Azure Machine Learning som ska partitioneras för parallell bearbetning.
- `side_inputs`: En eller flera referensdata eller datauppsättningar som används som sidoindata utan att behöva partitioneras.
- `output`: Ett `OutputFileDatasetConfig` objekt som motsvarar utdatakatalogen.
- `arguments`: En lista med argument som skickas till användarskriptet. Använd unknown_args för att hämta dem i ditt startskript (valfritt).
- `allow_reuse`: Om steget ska återanvända tidigare resultat när det körs med samma inställningar/indata. Om den här `False` parametern är genereras alltid en ny körning för det här steget under pipelinekörningen. (valfritt; standardvärdet är `True` .)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-techniques"></a>Felsökningstekniker

Det finns tre huvudsakliga tekniker för felsökning av pipelines: 

* Felsöka enskilda pipelinesteg på din lokala dator
* Använda loggning Application Insights för att isolera och diagnostisera orsaken till problemet
* Koppla ett fjärrfelsökare till en pipeline som körs i Azure

### <a name="debug-scripts-locally"></a>Felsöka skript lokalt

Ett av de vanligaste felen i en pipeline är att domänskriptet inte körs som avsett eller innehåller körningsfel i fjärrbearbetningskontexten som är svåra att felsöka.

Själva pipelines kan inte köras lokalt, men om du kör skripten isolerat på den lokala datorn kan du felsöka snabbare eftersom du inte behöver vänta på beräknings- och miljöbyggprocessen. Vissa utvecklingsarbete krävs för att göra detta:

* Om dina data finns i ett molndatalager måste du ladda ned data och göra dem tillgängliga för ditt skript. Att använda ett litet exempel på dina data är ett bra sätt att minska körningen och snabbt få feedback om skriptbeteende
* Om du försöker simulera ett mellanliggande pipelinesteg kan du behöva skapa objekttyperna som det specifika skriptet förväntar sig från föregående steg manuellt
* Du måste också definiera din egen miljö och replikera de beroenden som definierats i fjärrbearbetningsmiljön

När du har en skriptkonfiguration som ska köras i din lokala miljö är det mycket enklare att utföra felsökningsuppgifter som:

* Koppla en anpassad felsökningskonfiguration
* Pausa körningen och inspektera objekttillståndet
* Fånga typ eller logiska fel som inte exponeras förrän körning

> [!TIP] 
> När du kan kontrollera att skriptet körs som förväntat är ett bra nästa steg att köra skriptet i en pipeline i ett steg innan du försöker köra det i en pipeline med flera steg.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Konfigurera, skriva till och granska pipelineloggar

Att testa skript lokalt är ett bra sätt att felsöka större kodfragment och komplex logik innan du börjar skapa en pipeline, men du kommer troligen att behöva felsöka skript under själva pipelinekörningen, särskilt när du diagnostiserar beteende som inträffar under interaktionen mellan pipelinestegen. Vi rekommenderar att du använder -instruktioner i dina stegskript så att du kan se objekttillstånd och förväntade värden under fjärrkörningen, ungefär som när du felsöker `print()` JavaScript-kod.

### <a name="logging-options-and-behavior"></a>Loggningsalternativ och beteende

Tabellen nedan innehåller information om olika felsökningsalternativ för pipelines. Det är inte en fullständig lista, eftersom det finns andra alternativ förutom de Azure Machine Learning, Python och OpenCensus som visas här.

| Bibliotek                    | Typ   | Exempel                                                          | Mål                                  | Resurser                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metric | `run.log(name, val)`                                             | Azure Machine Learning portalgränssnitt             | [Spåra experiment](how-to-log-view-metrics.md)<br>[azureml.core.Run-klass](/python/api/azureml-core/azureml.core.run%28class%29)                                                                                                                                                 |
| Python-utskrift/-loggning    | Loggas    | `print(val)`<br>`logging.info(message)`                          | Drivrutinsloggar, Azure Machine Learning designer | [Spåra experiment](how-to-log-view-metrics.md)<br><br>[Python-loggning](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Loggas    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – spårningar                | [Felsöka pipelines i Application Insights.](./how-to-log-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor-exportörer](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Cookbook för Python-loggning](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Exempel på loggningsalternativ

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning Designer

För pipelines som skapats i designern hittar du **70_driver_log** på redigeringssidan eller på informationssidan för pipelinekörningar.

### <a name="enable-logging-for-real-time-endpoints"></a>Aktivera loggning för realtidsslutpunkter

För att kunna felsöka realtidsslutpunkter i designern måste du aktivera Application Insight-loggning med SDK:n. Med loggning kan du felsöka problem med modelldistribution och användning. Mer information finns i [Loggning för distribuerade modeller.](./how-to-enable-app-insights.md) 

### <a name="get-logs-from-the-authoring-page"></a>Hämta loggar från redigeringssidan

När du skickar en pipelinekörning och är kvar på redigeringssidan kan du hitta loggfilerna som genererats för varje modul när varje modul har körts klart.

1. Välj en modul som har körts klart på redigeringsarbetsytan.
1. I den högra rutan i modulen går du till  **fliken Utdata +** loggar.
1. Expandera den högra rutan och välj den **70_driver_log.txtför** att visa filen i webbläsaren. Du kan också ladda ned loggar lokalt.

    ![Expanderat utdatafönster i designern](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Hämta loggar från pipelinekörningar

Du hittar även loggfilerna för specifika körningar på informationssidan för pipelinekörningar, som du hittar i avsnittet **Pipelines** eller **Experiment** i studio.

1. Välj en pipelinekörning som skapats i designern.

    ![Sidan Pipelinekörning](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Välj en modul i förhandsgranskningsfönstret.
1. I den högra rutan i modulen går du till  **fliken Utdata +** loggar.
1. Expandera det högra fönstret om du vill **70_driver_log.txt** filen i webbläsaren eller välj filen för att ladda ned loggarna lokalt.

> [!IMPORTANT]
> Om du vill uppdatera en pipeline från sidan med information om pipelinekörningen måste du **klona** pipelinekörningen till ett nytt pipelineutkast. En pipelinekörning är en ögonblicksbild av pipelinen. Det liknar en loggfil och kan inte ändras. 

## <a name="application-insights"></a>Application Insights
Mer information om hur du använder Python-biblioteket OpenCensus på det här sättet finns i den här guiden: Felsöka pipelines för [maskininlärning i Application Insights](./how-to-log-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interaktiv felsökning med Visual Studio Code

I vissa fall kan du behöva felsöka Python-koden som används i ML-pipelinen interaktivt. Med hjälp Visual Studio Code (VS Code) och debugpy kan du ansluta till koden när den körs i träningsmiljön. Mer information finns i guiden [för interaktiv felsökning i VS Code.](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)

## <a name="next-steps"></a>Nästa steg

* En fullständig självstudie med `ParallelRunStep` hjälp av finns i [Tutorial: Build an Azure Machine Learning pipeline for batch scoring](tutorial-pipeline-batch-scoring-classification.md).

* Ett komplett exempel som visar automatiserad maskininlärning i ML-pipelines finns i [Använda automatiserad ML i en Azure Machine Learning-pipeline i Python](how-to-use-automlstep-in-pipelines.md).

* I SDK-referensen finns hjälp med [paketet azureml-pipelines-core](/python/api/azureml-pipeline-core/) och [paketet azureml-pipelines-steps.](/python/api/azureml-pipeline-steps/)

* Se listan över [designerundantag och felkoder.](algorithm-module-reference/designer-error-codes.md)