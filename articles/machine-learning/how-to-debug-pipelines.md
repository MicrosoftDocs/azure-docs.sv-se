---
title: Felsöka ML-pipeliner
titleSuffix: Azure Machine Learning
description: Fel sökning när du får fel när du kör en maskin inlärnings pipeline. Vanliga fall GRO par och tips som hjälper dig att felsöka skript före och under fjärrkörning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 0f27688e31f772cc8d784371aa570d55c41f5695
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131822"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Felsöka maskin inlärnings pipeliner

I den här artikeln får du lära dig att felsöka när du får fel när du kör en [maskin inlärnings pipeline](concept-ml-pipelines.md) i [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) och [Azure Machine Learning designer](./concept-designer.md). 

## <a name="troubleshooting-tips"></a>Felsökningstips

Följande tabell innehåller vanliga problem under utveckling av pipeline, med möjliga lösningar.

| Problem | Möjlig lösning |
|--|--|
| Det gick inte att skicka data till `PipelineData` katalogen | Se till att du har skapat en katalog i skriptet som motsvarar var din pipeline förväntar dig utdata från steget. I de flesta fall definierar ett indataargument utdata-katalogen och du skapar katalogen explicit. Använd `os.makedirs(args.output_dir, exist_ok=True)` för att skapa utdatakatalogen. Se [självstudien](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) för ett bedömnings skript exempel som visar det här design mönstret. |
| Beroende buggar | Om du ser beroende fel i din fjärrpipeline som inte uppnåddes vid en lokal testning, bekräftar du beroenden för fjärrmiljön och versioner som matchar dem i din test miljö. (Se [miljö utveckling, cachelagring och åter användning](./concept-environments.md#environment-building-caching-and-reuse)|
| Tvetydiga fel med beräknings mål | Försök att ta bort och återskapa Compute-mål. Det går snabbt och kan lösa vissa tillfälliga problem genom att återskapa beräknings mål. |
| Pipeline återanvändar inte steg | Steg åter användning är aktiverat som standard, men se till att du inte har inaktiverat det i ett steg i pipeline. Om åter användning är inaktive rad `allow_reuse` kommer parametern i steget att ställas in på `False` . |
| Pipelinen körs inte nödvändigt vis | Om du vill se till att stegen bara körs igen när deras underliggande data eller skript ändras, kan du koppla från dina käll kods kataloger för varje steg. Om du använder samma käll katalog för flera steg kan du få onödig omkörning. Använd `source_directory` parametern i ett pipeline-steg-objekt för att peka på den isolerade katalogen för det steget och se till att du inte använder samma `source_directory` sökväg för flera steg. |
| Steg sakta ner över utbildningens epoker eller andra funktioner för upprepning | Försök att byta fil skrivning, inklusive loggning, från `as_mount()` till `as_upload()` . **Monterings** läget använder ett virtualiserat virtualiserat fil system och laddar upp hela filen varje gången det läggs till. |
| Compute Target tar lång tid att starta | Docker-avbildningar för beräknings mål läses in från Azure Container Registry (ACR). Azure Machine Learning skapar som standard en ACR som använder tjänst nivån *Basic* . Att ändra ACR för arbets ytan till standard-eller Premium-nivån kan minska den tid det tar att bygga och läsa in bilder. Mer information finns i [Azure Container Registry tjänst nivåer](../container-registry/container-registry-skus.md). |

### <a name="authentication-errors"></a>Autentiseringsfel

Om du utför en hanterings åtgärd på ett beräknings mål från ett Fjärrjobb får du ett av följande fel: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Till exempel visas ett fel meddelande om du försöker skapa eller ansluta ett beräknings mål från en ML-pipeline som skickas för fjärrkörning.
## <a name="troubleshooting-parallelrunstep"></a>Telefonbaserad `ParallelRunStep` 

Skriptet för en `ParallelRunStep` *måste innehålla* två funktioner:
- `init()`: Använd den här funktionen för eventuell kostsam eller vanlig förberedelse för senare härledning. Använd till exempel den för att läsa in modellen i ett globalt objekt. Den här funktionen kommer endast att anropas en gång i början av processen.
-  `run(mini_batch)`: Funktionen kommer att köras för varje `mini_batch` instans.
    -  `mini_batch`: `ParallelRunStep` anropar Run-metoden och skickar antingen en lista eller en Pandas `DataFrame` som ett argument till metoden. Varje post i mini_batch är en fil Sök väg om indata är en `FileDataset` eller en Pandas `DataFrame` om indata är en `TabularDataset` .
    -  `response`: Run ()-metoden ska returnera en Pandas `DataFrame` eller en matris. För append_row output_action läggs dessa returnerade element till i den gemensamma utdatafilen. För summary_only ignoreras innehållet i elementen. För alla utdata-åtgärder anger varje returnerat utdata en lyckad körning av indata-element i mini-batch för indata. Se till att tillräckligt med data inkluderas i körnings resultatet för att mappa indata till att köra resultatet av utdata. Kör utdata skrivs i utdatafilen och är inte garanterat i ordning, du bör använda vissa nycklar i utdata för att mappa den till indata.

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

Om du har en annan fil eller mapp i samma katalog som ditt härlednings skript kan du referera till den genom att söka efter den aktuella arbets katalogen.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parametrar för ParallelRunConfig

`ParallelRunConfig` är den viktigaste konfigurationen för `ParallelRunStep` instansen i Azure Machine Learning pipelinen. Du använder den för att omsluta ditt skript och konfigurera nödvändiga parametrar, inklusive alla följande poster:
- `entry_script`: Ett användar skript som en lokal fil Sök väg som ska köras parallellt på flera noder. Om `source_directory` det finns använder du en relativ sökväg. Annars använder du valfri sökväg som är tillgänglig på datorn.
- `mini_batch_size`: Den mini-batch-storlek som skickas till ett enda `run()` anrop. (valfritt; standardvärdet är `10` filer för `FileDataset` och `1MB` for `TabularDataset` .)
    - För är `FileDataset` det antalet filer med minimivärdet `1` . Du kan kombinera flera filer i en mini-batch.
    - För är `TabularDataset` det data storleken. Exempel värden är `1024` , `1024KB` , `10MB` och `1GB` . Det rekommenderade värdet är `1MB` . Mini-batch från `TabularDataset` kommer aldrig att överskrida fil gränser. Om du till exempel har CSV-filer med olika storlekar är den minsta filen 100 KB och störst är 10 MB. Om du anger `mini_batch_size = 1MB` kommer filer med en storlek som är mindre än 1 MB att behandlas som en mini-batch. Filer med en storlek som är större än 1 MB delas upp i flera mini-batchar.
- `error_threshold`: Antalet post-och fil haverier `TabularDataset` `FileDataset` som ska ignoreras under bearbetningen. Om antalet fel för hela inflödet överskrider det här värdet kommer jobbet att avbrytas. Fel tröskeln är för alla indatatyper och inte för enskilda mini-batchar som skickas till- `run()` metoden. Intervallet är `[-1, int.max]` . `-1`Delen indikerar att ignorera alla avbrott under bearbetningen.
- `output_action`: Ett av följande värden anger hur utdata ska ordnas:
    - `summary_only`: Användar skriptet kommer att lagra utdata. `ParallelRunStep` kommer bara att använda utdata för fel tröskel beräkningen.
    - `append_row`: För alla indata skapas bara en fil i mappen utdata för att lägga till alla utdata avgränsade med rad.
- `append_row_file_name`: Om du vill anpassa namnet på utdatafilen för append_row output_action (valfritt; standardvärde `parallel_run_step.txt` ).
- `source_directory`: Sökvägar till mappar som innehåller alla filer som ska köras på beräknings målet (valfritt).
- `compute_target`: `AmlCompute` Stöds endast.
- `node_count`: Antalet beräknade datornoder som ska användas för att köra användar skriptet.
- `process_count_per_node`: Antalet processer per nod. Bästa praxis är att ställa in på antalet GPU eller CPU en nod har (valfritt; standardvärde är `1` ).
- `environment`: Python-miljöns definition. Du kan konfigurera den att använda en befintlig python-miljö eller konfigurera en tillfällig miljö. Definitionen är också ansvarig för att ange nödvändiga program beroenden (valfritt).
- `logging_level`: Logg utförlighet. Värden i ökande utförlighet är: `WARNING` , `INFO` och `DEBUG` . (valfritt; standardvärdet är `INFO` )
- `run_invocation_timeout`: `run()` Tids gränsen för metod anrop i sekunder. (valfritt; standardvärdet är `60` )
- `run_max_try`: Maximalt antal försök `run()` för en mini-batch. A `run()` Miss lyckas om ett undantag genereras eller om inget returneras när `run_invocation_timeout` nås (valfritt `3` ). 

Du kan ange `mini_batch_size` , `node_count` ,,, `process_count_per_node` `logging_level` `run_invocation_timeout` och `run_max_try` som `PipelineParameter` , så att du kan finjustera parametervärdena när du skickar om en pipeline-körning. I det här exemplet använder du `PipelineParameter` för `mini_batch_size` och `Process_count_per_node` och du kommer att ändra dessa värden när du skickar om en körning senare. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parametrar för att skapa ParallelRunStep

Skapa ParallelRunStep med hjälp av skriptet, miljö konfigurationen och parametrarna. Ange det beräknings mål som du redan har kopplat till din arbets yta som mål för körning av ditt härlednings skript. Använd `ParallelRunStep` för att skapa pipeline-steget för batch-härledning, som tar alla följande parametrar:
- `name`: Namnet på steget med följande namngivnings begränsningar: unika, 3-32 tecken och regex ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: Ett `ParallelRunConfig` objekt som definieras tidigare.
- `inputs`: En eller flera data uppsättningar med en enkel Azure Machine Learning typ som ska partitioneras för parallell bearbetning.
- `side_inputs`: En eller flera referens data eller data uppsättningar som används som sid indata utan att behöva partitioneras.
- `output`: Ett `OutputFileDatasetConfig` objekt som motsvarar utdata-katalogen.
- `arguments`: En lista över argument som skickas till användar skriptet. Använd unknown_args för att hämta dem i ditt Entry-skript (valfritt).
- `allow_reuse`: Om steget ska återanvända tidigare resultat när det körs med samma inställningar/indata. Om den här parametern är är `False` en ny körning alltid att skapas för det här steget under pipeline-körningen. (valfritt; standardvärdet är `True` .)

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

## <a name="debugging-techniques"></a>Fel söknings tekniker

Det finns tre huvud tekniker för fel sökning av pipeliner: 

* Felsöka enskilda pipeline-steg på din lokala dator
* Använd loggning och Application Insights för att isolera och diagnostisera problemets källa
* Koppla en fjärr fel sökare till en pipeline som körs i Azure

### <a name="debug-scripts-locally"></a>Felsöka skript lokalt

Ett av de vanligaste felen i en pipeline är att domän skriptet inte körs som avsett eller innehåller körnings fel i fjärrberäknings kontexten som är svåra att felsöka.

Det går inte att köra pipeliner lokalt, men om du kör skripten i isolering på din lokala dator kan du felsöka snabbare eftersom du inte behöver vänta på bearbetningen av beräknings-och miljö versionen. Det krävs en del utvecklings arbete för att göra detta:

* Om dina data finns i ett moln data lager måste du hämta data och göra dem tillgängliga för ditt skript. Att använda ett litet exempel på dina data är ett bra sätt att skära ned i körningen och snabbt få feedback om skript beteende
* Om du försöker simulera ett mellanliggande pipeline-steg kan du behöva bygga de objekt typer som det aktuella skriptet förväntar sig i föregående steg
* Du måste också definiera en egen miljö och replikera de beroenden som definierats i fjärrberäknings miljön

När du har en skript konfiguration som ska köras i din lokala miljö är det mycket enklare att utföra fel sökning av uppgifter, t. ex.:

* Bifoga en anpassad fel söknings konfiguration
* Pausa körning och inspektera objekt tillstånd
* Fångst typ eller logiska fel som inte ska exponeras förrän runtime

> [!TIP] 
> När du kan kontrol lera att skriptet körs som förväntat kör du ett lämpligt nästa steg i en pipeline för ett enda steg innan du försöker köra det i en pipeline med flera steg.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Konfigurera, skriva till och granska pipeline-loggar

Att testa skript lokalt är ett bra sätt att felsöka större kodfragment och komplex logik innan du börjar skapa en pipeline, men ibland behöver du felsöka skript under själva pipeline-körningen, särskilt när du diagnostiserar beteende som inträffar under interaktionen mellan pipeline-steg. Vi rekommenderar att du använder `print()` instruktioner i dina steg skript så att du kan se objekt status och förväntade värden under fjärrkörning, på samma sätt som du skulle felsöka JavaScript-kod.

### <a name="logging-options-and-behavior"></a>Loggnings alternativ och beteende

Tabellen nedan innehåller information om olika fel söknings alternativ för pipelines. Det är inte en fullständig lista, som andra alternativ finns förutom bara de Azure Machine Learning-, python-och Open-räkningar som visas här.

| Bibliotek                    | Typ   | Exempel                                                          | Mål                                  | Resurser                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metric | `run.log(name, val)`                                             | Azure Machine Learning portalens användar gränssnitt             | [Spåra experiment](how-to-track-experiments.md)<br>[azureml. Core. Run-klass](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)                                                                                                                                                 |
| Python-utskrift/-loggning    | Loggas    | `print(val)`<br>`logging.info(message)`                          | Driv rutins loggar, Azure Machine Learning designer | [Spåra experiment](how-to-track-experiments.md)<br><br>[Python-loggning](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Loggas    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-spår                | [Felsöka pipelines i Application Insights.](./how-to-log-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor-exportörer](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Cookbook för python-loggning](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Exempel på loggnings alternativ

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

För pipeliner som skapats i designern kan du hitta **70_driver_log** -filen på antingen sidan redigering eller på sidan körnings information för pipelinen.

### <a name="enable-logging-for-real-time-endpoints"></a>Aktivera loggning för slut punkter i real tid

För att felsöka och felsöka real tids slut punkter i designern måste du aktivera program insikts loggning med hjälp av SDK. Med loggning kan du felsöka och felsöka problem med distribution och användning av modeller. Mer information finns i [Logga in för distribuerade modeller](./how-to-enable-app-insights.md). 

### <a name="get-logs-from-the-authoring-page"></a>Hämta loggar från sidan redigering

När du skickar en pipeline-körning och stannar på sidan redigering kan du hitta de loggfiler som genereras för varje modul när varje modul har slutförts.

1. Välj en modul som har körts klart på redigerings arbets ytan.
1. I den högra rutan i modulen går du till fliken  **utdata + loggar** .
1. Expandera den högra rutan och välj **70_driver_log.txt** för att visa filen i webbläsaren. Du kan också hämta loggar lokalt.

    ![Fönster för utökad utdata i designern](./media/how-to-debug-pipelines/designer-logs.png)? View = Azure-ml-py&bevara-Visa = sant)? Visa = Azure-ml-py&bevara-Visa = sant)

### <a name="get-logs-from-pipeline-runs"></a>Hämta loggar från pipeline-körningar

Du kan också hitta loggfilerna för vissa körningar på sidan körnings information för pipeline, som du hittar i avsnittet **pipelines** eller **experiment** i Studio.

1. Välj en pipeline-körning som skapats i designern.

    ![Sidan pipeline-körning](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Välj en modul i förhands gransknings fönstret.
1. I den högra rutan i modulen går du till fliken  **utdata + loggar** .
1. Expandera den högra rutan om du vill visa **70_driver_log.txt** filen i webbläsaren eller välj filen för att ladda ned loggarna lokalt.

> [!IMPORTANT]
> Om du vill uppdatera en pipeline från sidan körnings information för pipelinen måste du **klona** pipeline-körningen till ett nytt pipeline-utkast. En pipeline-körning är en ögonblicks bild av pipelinen. Det liknar en loggfil och kan inte ändras. 

## <a name="application-insights"></a>Application Insights
Mer information om hur du använder python-biblioteket för openräkning på det här sättet finns i den här guiden: [Felsöka och Felsök maskin inlärnings pipeliner i Application Insights](./how-to-log-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interaktiv fel sökning med Visual Studio Code

I vissa fall kan du behöva interaktivt felsöka python-koden som används i ML-pipeline. Genom att använda Visual Studio Code (VS Code) och debugpy kan du ansluta till koden när den körs i tränings miljön. Mer information finns [i interaktiv fel sökning i vs Code guide](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Nästa steg

* En fullständig självstudie med `ParallelRunStep` finns i [Självstudier: bygga en Azure Machine Learning pipeline för batch-Poäng](tutorial-pipeline-batch-scoring-classification.md).

* Ett fullständigt exempel som visar Automatisk maskin inlärning i ML pipelines finns i [använda automatisk ml i en Azure Machine Learning pipeline i python](how-to-use-automlstep-in-pipelines.md).

* Se SDK-referensen för hjälp med [azureml-pipeline – Core-](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) paketet och [azureml-pipeline-steg-](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py) paketet.

* Se listan över [designers undantag och felkoder](algorithm-module-reference/designer-error-codes.md).