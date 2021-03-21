---
title: Machine Learning pipeline YAML
titleSuffix: Azure Machine Learning
description: Lär dig hur du definierar en Machine Learning-pipeline med hjälp av en YAML-fil. YAML pipeline-definitioner används med Machine Learning-tillägget för Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python
ms.openlocfilehash: e2b5a3322f633ca8301357c2186d78d3ac437ae2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521976"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definiera pipeliner för Machine Learning i YAML

Lär dig hur du definierar dina maskin inlärnings pipeliner i [yaml](https://yaml.org/). När du använder Machine Learning-tillägget för Azure CLI förväntas många av de pipeline-relaterade kommandona en YAML-fil som definierar pipelinen.

I följande tabell visas vad som inte stöds för närvarande när du definierar en pipeline i YAML:

| Steg typ | Stöds? |
| ----- | :-----: |
| PythonScriptStep | Ja |
| ParallelRunStep | Ja |
| AdlaStep | Ja |
| AzureBatchStep | Ja |
| DatabricksStep | Ja |
| DataTransferStep | Ja |
| AutoMLStep | Inga |
| HyperDriveStep | Inga |
| ModuleStep | Ja |
| MPIStep | Inga |
| EstimatorStep | Inga |

## <a name="pipeline-definition"></a>Pipeline-definition

En pipeline-definition använder följande nycklar som motsvarar [pipelines](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline) -klassen:

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `name` | En beskrivning av pipelinen. |
| `parameters` | Parameter (er) till pipelinen. |
| `data_reference` | Definierar hur och var data ska göras tillgängliga i en körning. |
| `default_compute` | Standard beräknings mål där alla steg i pipelinen körs. |
| `steps` | Stegen som används i pipelinen. |

## <a name="parameters"></a>Parametrar

I `parameters` avsnittet används följande nycklar som motsvarar klassen [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter) :

| YAML-nyckel | Beskrivning |
| ---- | ---- |
| `type` | Värde typen för parametern. Giltiga typer är `string` ,,, `int` `float` `bool` eller `datapath` . |
| `default` | Standardvärdet. |

Varje parameter kallas. Följande YAML-kodfragment definierar till exempel tre parametrar med namnet `NumIterationsParameter` , `DataPathParameter` , och `NodeCountParameter` :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Datareferens

I `data_references` avsnittet används följande nycklar, som motsvarar [DataReference](/python/api/azureml-core/azureml.data.data_reference.datareference):

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `datastore` | Data lagret som ska refereras. |
| `path_on_datastore` | Den relativa sökvägen i lagrings utrymmet för data referensen. |

Varje data referens finns i en nyckel. Följande YAML-kodfragment definierar till exempel en data referens som lagras i nyckeln med namnet `employee_data` :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Steg

Stegen definierar en beräknings miljö, tillsammans med de filer som ska köras i miljön. Använd nyckeln för att definiera typen av steg `type` :

| Steg typ | Beskrivning |
| ----- | ----- |
| `AdlaStep` | Kör ett U-SQL-skript med Azure Data Lake Analytics. Motsvarar klassen [AdlaStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep) . |
| `AzureBatchStep` | Kör jobb med Azure Batch. Motsvarar klassen [AzureBatchStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) . |
| `DatabricsStep` | Lägger till en Databricks Notebook, Python-skript eller JAR. Motsvarar klassen [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep) . |
| `DataTransferStep` | Överför data mellan lagrings alternativ. Motsvarar klassen [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) . |
| `PythonScriptStep` | Kör ett Python-skript. Motsvarar klassen [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) . |
| `ParallelRunStep` | Kör ett Python-skript för att bearbeta stora mängder data asynkront och parallellt. Motsvarar klassen [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) . |

### <a name="adla-step"></a>ADLA-steg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `script_name` | Namnet på U-SQL-skriptet (relativt till `source_directory` ). |
| `compute_target` | Det Azure Data Lake beräknings mål som ska användas för det här steget. |
| `parameters` | [Parametrar](#parameters) för pipelinen. |
| `inputs` | Indata kan vara [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [data uppsättning](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)och [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Utdata kan vara antingen [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) eller [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `source_directory` | Katalog som innehåller skriptet, sammansättningar osv. |
| `priority` | Prioritet svärdet som ska användas för det aktuella jobbet. |
| `params` | Ord lista för namn/värde-par. |
| `degree_of_parallelism` | Graden av parallellitet som ska användas för det här jobbet. |
| `runtime_version` | Körnings versionen av Data Lake Analyticss motorn. |
| `allow_reuse` | Bestämmer om steget ska återanvända tidigare resultat när de körs igen med samma inställningar. |

Följande exempel innehåller en ADLA steg definition:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch steg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `compute_target` | Det Azure Batch beräknings mål som ska användas för det här steget. |
| `inputs` | Indata kan vara [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [data uppsättning](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)och [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Utdata kan vara antingen [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) eller [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `source_directory` | Katalog som innehåller modulens binärfiler, körbara filer, sammansättningar osv. |
| `executable` | Namnet på kommandot/filen som kommer att köras som en del av det här jobbet. |
| `create_pool` | Boolesk flagga för att ange om poolen ska skapas innan jobbet körs. |
| `delete_batch_job_after_finish` | Boolesk flagga för att ange om jobbet ska tas bort från batch-kontot när det är klart. |
| `delete_batch_pool_after_finish` | Boolesk flagga för att ange om poolen ska tas bort när jobbet har slutförts. |
| `is_positive_exit_code_failure` | Boolesk flagga som anger om jobbet Miss lyckas om aktiviteten avslutas med en positiv kod. |
| `vm_image_urn` | Om `create_pool` är `True` , och virtuell dator använder `VirtualMachineConfiguration` . |
| `pool_id` | ID för poolen där jobbet ska köras. |
| `allow_reuse` | Bestämmer om steget ska återanvända tidigare resultat när de körs igen med samma inställningar. |

I följande exempel finns en definition av Azure Batch steg:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Databricks-steg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `compute_target` | Det Azure Databricks beräknings mål som ska användas för det här steget. |
| `inputs` | Indata kan vara [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [data uppsättning](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)och [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Utdata kan vara antingen [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) eller [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `run_name` | Namnet i Databricks för den här körningen. |
| `source_directory` | Katalog som innehåller skriptet och andra filer. |
| `num_workers` | Det statiska antalet arbetare för Databricks-kör kluster. |
| `runconfig` | Sökvägen till en `.runconfig` fil. Den här filen är en YAML representation av [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) -klassen. Mer information om strukturen för den här filen finns i [runconfigschema.jspå](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Bestämmer om steget ska återanvända tidigare resultat när de körs igen med samma inställningar. |

Följande exempel innehåller ett Databricks-steg:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Data överförings steg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `compute_target` | Det Azure Data Factory beräknings mål som ska användas för det här steget. |
| `source_data_reference` | Inmatnings anslutning som fungerar som källa för data överförings åtgärder. De värden som stöds är [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [data uppsättning](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)och [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `destination_data_reference` | Ingående anslutning som fungerar som mål för data överförings åtgärder. De värden som stöds är [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) och [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `allow_reuse` | Bestämmer om steget ska återanvända tidigare resultat när de körs igen med samma inställningar. |

Följande exempel innehåller ett data överförings steg:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Python-skript steg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `inputs` | Indata kan vara [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [data uppsättning](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)och [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Utdata kan vara antingen [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) eller [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `script_name` | Namnet på python-skriptet (relativt till `source_directory` ). |
| `source_directory` | Katalog som innehåller skriptet, Conda-miljön osv. |
| `runconfig` | Sökvägen till en `.runconfig` fil. Den här filen är en YAML representation av [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) -klassen. Mer information om strukturen för den här filen finns i [runconfig.jspå](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Bestämmer om steget ska återanvända tidigare resultat när de körs igen med samma inställningar. |

Följande exempel innehåller ett skript steg för python:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="parallel-run-step"></a>Parallellt körnings steg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `inputs` | Indata kan vara [dataset](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)eller [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Utdata kan vara antingen [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) eller [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `script_name` | Namnet på python-skriptet (relativt till `source_directory` ). |
| `source_directory` | Katalog som innehåller skriptet, Conda-miljön osv. |
| `parallel_run_config` | Sökvägen till en `parallel_run_config.yml` fil. Den här filen är en YAML representation av [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) -klassen. |
| `allow_reuse` | Bestämmer om steget ska återanvända tidigare resultat när de körs igen med samma inställningar. |

Följande exempel innehåller ett parallellt körnings steg:

```yaml
pipeline:
    description: SamplePipelineFromYaml
    default_compute: cpu-cluster
    data_references:
        MyMinistInput:
            dataset_name: mnist_sample_data
    parameters:
        PipelineParamTimeout:
            type: int
            default: 600
    steps:        
        Step1:
            parallel_run_config: "yaml/parallel_run_config.yml"
            type: "ParallelRunStep"
            name: "parallel-run-step-1"
            allow_reuse: True
            arguments:
            - "--progress_update_timeout"
            - parameter:timeout_parameter
            - "--side_input"
            - side_input:SideInputData
            parameters:
                timeout_parameter:
                    source: PipelineParamTimeout
            inputs:
                InputData:
                    source: MyMinistInput
            side_inputs:
                SideInputData:
                    source: Output4
                    bind_mode: mount
            outputs:
                OutputDataStep2:
                    destination: Output5
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Pipeline med flera steg 

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `steps` | Sekvens av en eller flera PipelineStep-definitioner. Observera att `destination` nycklarna i ett steg `outputs` blir `source` nycklar till `inputs` Nästa steg.| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Scheman

När du definierar schemat för en pipeline kan det vara antingen data lager utlösta eller återkommande baserat på ett tidsintervall. Följande är de nycklar som används för att definiera ett schema:

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `description` | En beskrivning av schemat. |
| `recurrence` | Innehåller inställningar för upprepning, om schemat är återkommande. |
| `pipeline_parameters` | Alla parametrar som krävs av pipelinen. |
| `wait_for_provisioning` | Om du vill vänta tills etableringen av schemat har slutförts. |
| `wait_timeout` | Antalet sekunder som ska förflyta innan tids gränsen uppnåddes. |
| `datastore_name` | Data lagret som ska övervakas för ändrade/tillagda blobbar. |
| `polling_interval` | Hur lång tid, i minuter, mellan avsökningen av ändrade/tillagda blobbar. Standardvärde: 5 minuter. Stöds endast för data lagrings scheman. |
| `data_path_parameter_name` | Namnet på den pipeline-parameter för data Sök väg som ska anges med den ändrade BLOB-sökvägen. Stöds endast för data lagrings scheman. |
| `continue_on_step_failure` | Om ett steg inte kan fortsätta att köra andra steg i den inskickade PipelineRun. Om det här alternativet anges åsidosätts `continue_on_step_failure` inställningen för pipelinen.
| `path_on_datastore` | Valfritt. Sökvägen till data lagret som ska övervakas för ändrade/tillagda blobbar. Sökvägen är under data lagrets behållare, så den faktiska sökvägen som schema övervakare är container/ `path_on_datastore` . Om inget anges, övervakas data lagrets behållare. Tillägg/ändringar som gjorts i en undermapp i `path_on_datastore` övervakas inte. Stöds endast för data lagrings scheman. |

I följande exempel visas definitionen för ett data lager som utlöses:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

När du definierar ett **återkommande schema**, använder du följande nycklar under `recurrence` :

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `frequency` | Hur ofta schemat ska upprepas. Giltiga värden är `"Minute"` ,,, `"Hour"` `"Day"` `"Week"` eller `"Month"` . |
| `interval` | Hur ofta schemat utlöses. Heltal svärdet är antalet tidsenheter som ska vänta tills schemat utlöses igen. |
| `start_time` | Start tiden för schemat. Sträng formatet för värdet är `YYYY-MM-DDThh:mm:ss` . Om ingen start tid anges körs den första arbets belastningen direkt och framtida arbets belastningar körs baserat på schemat. Om start tiden har passerat körs den första arbets belastningen vid nästa beräknade körnings tid. |
| `time_zone` | Tids zonen för start tiden. Om ingen tidszon anges används UTC. |
| `hours` | Om `frequency` är `"Day"` eller `"Week"` kan du ange ett eller flera heltal från 0 till 23, avgränsade med kommatecken, som de timmar på dagen då pipelinen ska köras. Endast `time_of_day` eller `hours` och `minutes` kan användas. |
| `minutes` | Om `frequency` är `"Day"` eller `"Week"` kan du ange ett eller flera heltal från 0 till 59, avgränsade med kommatecken, som minuter i timmen då pipelinen ska köras. Endast `time_of_day` eller `hours` och `minutes` kan användas. |
| `time_of_day` | Om `frequency` är `"Day"` eller `"Week"` kan du ange en tid på dagen då schemat ska köras. Sträng formatet för värdet är `hh:mm` . Endast `time_of_day` eller `hours` och `minutes` kan användas. |
| `week_days` | I så fall `frequency` `"Week"` kan du ange en eller flera dagar, avgränsade med kommatecken, när schemat ska köras. Giltiga värden är,,,,, `"Monday"` `"Tuesday"` `"Wednesday"` `"Thursday"` `"Friday"` `"Saturday"` och `"Sunday"` . |

Följande exempel innehåller definitionen för ett återkommande schema:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [använder CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).