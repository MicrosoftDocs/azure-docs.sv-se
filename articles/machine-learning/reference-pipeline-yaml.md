---
title: Machine Learning YAML-pipeline
titleSuffix: Azure Machine Learning
description: Lär dig hur du definierar en maskininlärningspipeline med hjälp av en YAML-fil. YAML-pipelinedefinitioner används med maskininlärningstillägget för Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 1c44f018d558b9426ba6271c0cbb1c2356a2a9b2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478292"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definiera maskininlärningspipelines i YAML

Lär dig hur du definierar dina maskininlärningspipelines i [YAML](https://yaml.org/). När du använder maskininlärningstillägget för Azure CLI förväntar sig många av de pipelinerelaterade kommandona en YAML-fil som definierar pipelinen.

I följande tabell visas vad som stöds och inte stöds för närvarande när du definierar en pipeline i YAML:

| Stegtyp | Stöds? |
| ----- | :-----: |
| PythonScriptStep | Ja |
| ParallelRunStep | Ja |
| AdlaStep | Ja |
| AzureBatchStep | Ja |
| DatabricksStep | Ja |
| DataTransferStep | Ja |
| AutoMLStep | Inga |
| HyperDriveSteg | Inga |
| ModuleStep | Ja |
| MPIStep | Inga |
| EstimatorStep | Inga |

## <a name="pipeline-definition"></a>Pipelinedefinition

En pipelinedefinition använder följande nycklar, som motsvarar [klassen Pipelines:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline)

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `name` | Beskrivning av pipelinen. |
| `parameters` | Parametrar till pipelinen. |
| `data_reference` | Definierar hur och var data ska göras tillgängliga i en körning. |
| `default_compute` | Standardvärde för beräkning där alla steg i pipelinen körs. |
| `steps` | Stegen som används i pipelinen. |

## <a name="parameters"></a>Parametrar

Avsnittet `parameters` använder följande nycklar, som motsvarar klassen [PipelineParameter:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter)

| YAML-nyckel | Beskrivning |
| ---- | ---- |
| `type` | Parameterns värdetyp. Giltiga typer är `string` , , , eller `int` `float` `bool` `datapath` . |
| `default` | Standardvärdet. |

Varje parameter namnges. Följande YAML-kodfragment definierar till exempel tre parametrar `NumIterationsParameter` med namnet , och `DataPathParameter` `NodeCountParameter` :

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

Avsnittet `data_references` använder följande nycklar, som motsvarar [DataReference:](/python/api/azureml-core/azureml.data.data_reference.datareference)

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `datastore` | Det datalager som ska refereras till. |
| `path_on_datastore` | Den relativa sökvägen i lagringsenheten för datareferensen. |

Varje datareferens finns i en nyckel. Följande YAML-kodfragment definierar till exempel en datareferens som lagras i nyckeln med namnet `employee_data` :

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

Stegen definierar en beräkningsmiljö, tillsammans med de filer som ska köras i miljön. Använd nyckeln för att definiera typen av `type` steg:

| Stegtyp | Beskrivning |
| ----- | ----- |
| `AdlaStep` | Kör ett U-SQL-skript med Azure Data Lake Analytics. Motsvarar klassen [AdlaStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep) |
| `AzureBatchStep` | Kör jobb med Azure Batch. Motsvarar klassen [AzureBatchStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) |
| `DatabricsStep` | Lägger till en Databricks-anteckningsbok, Python-skript eller JAR. Motsvarar klassen [DatabricksStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep) |
| `DataTransferStep` | Överför data mellan lagringsalternativ. Motsvarar klassen [DataTransferStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) |
| `PythonScriptStep` | Kör ett Python-skript. Motsvarar [PythonScriptStep-klassen.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) |
| `ParallelRunStep` | Kör ett Python-skript för att bearbeta stora mängder data asynkront och parallellt. Motsvarar [parallelRunStep-klassen.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) |

### <a name="adla-step"></a>ADLA-steg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `script_name` | Namnet på U-SQL-skriptet (i förhållande till `source_directory` ). |
| `compute` | Azure Data Lake-beräkningsmålet som ska användas för det här steget. |
| `parameters` | [Parametrar](#parameters) till pipelinen. |
| `inputs` | Indata kan vara [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference](#data-reference), [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)eller [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Utdata kan vara [PipelineData eller](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `source_directory` | Katalog som innehåller skriptet, sammansättningar osv. |
| `priority` | Prioritetsvärdet som ska användas för det aktuella jobbet. |
| `params` | Ordlista med namn/värde-par. |
| `degree_of_parallelism` | Graden av parallellitet som ska användas för det här jobbet. |
| `runtime_version` | Körningsversionen av Data Lake Analytics motorn. |
| `allow_reuse` | Anger om steget ska återanvända tidigare resultat när det körs igen med samma inställningar. |

Följande exempel innehåller en ADLA-stegdefinition:

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
| `compute` | Det Azure Batch beräkningsmålet som ska användas för det här steget. |
| `inputs` | Indata kan vara [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)eller [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Utdata kan vara [PipelineData eller](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `source_directory` | Katalog som innehåller modulens binärfiler, körbara filer, sammansättningar osv. |
| `executable` | Namnet på kommandot/den körbara fil som ska köras som en del av det här jobbet. |
| `create_pool` | Boolesk flagga för att ange om poolen ska skapas innan jobbet körs. |
| `delete_batch_job_after_finish` | Boolesk flagga som anger om jobbet ska tas bort från Batch-kontot när det är klart. |
| `delete_batch_pool_after_finish` | Boolesk flagga som anger om poolen ska tas bort när jobbet har avslutats. |
| `is_positive_exit_code_failure` | Boolesk flagga som anger om jobbet misslyckas om aktiviteten avslutas med en positiv kod. |
| `vm_image_urn` | Om `create_pool` är använder den virtuella datorn `True` `VirtualMachineConfiguration` . |
| `pool_id` | ID:t för poolen där jobbet ska köras. |
| `allow_reuse` | Anger om steget ska återanvända tidigare resultat när det körs igen med samma inställningar. |

Följande exempel innehåller en Azure Batch stegdefinition:

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
| `compute` | Det Azure Databricks beräkningsmålet som ska användas för det här steget. |
| `inputs` | Indata kan vara [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference](#data-reference), [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)eller [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Utdata kan vara [antingen PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [eller OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `run_name` | Namnet i Databricks för den här körningen. |
| `source_directory` | Katalog som innehåller skriptet och andra filer. |
| `num_workers` | Det statiska antalet arbetare för Databricks-körningsklustret. |
| `runconfig` | Sökvägen till en `.runconfig` fil. Den här filen är en YAML-representation [av klassen RunConfiguration.](/python/api/azureml-core/azureml.core.runconfiguration) Mer information om strukturen för den här filen finns i [runconfigschema.jspå](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Anger om steget ska återanvända tidigare resultat när det körs igen med samma inställningar. |

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

### <a name="data-transfer-step"></a>Steg för dataöverföring

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `compute` | Det Azure Data Factory beräkningsmålet som ska användas för det här steget. |
| `source_data_reference` | Indataanslutning som fungerar som källa för dataöverföringsåtgärder. Värden som stöds [är InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)eller [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `destination_data_reference` | Indataanslutning som fungerar som mål för dataöverföringsåtgärder. Värden som stöds [är PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [och OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `allow_reuse` | Anger om steget ska återanvända tidigare resultat när det körs igen med samma inställningar. |

Följande exempel innehåller ett dataöverföringssteg:

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

### <a name="python-script-step"></a>Python-skriptsteg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `inputs` | Indata kan vara [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference](#data-reference), [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)eller [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Utdata kan vara [PipelineData eller](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `script_name` | Namnet på Python-skriptet (i förhållande till `source_directory` ). |
| `source_directory` | Katalog som innehåller skriptet, Conda-miljön osv. |
| `runconfig` | Sökvägen till en `.runconfig` fil. Den här filen är en YAML-representation av [klassen RunConfiguration.](/python/api/azureml-core/azureml.core.runconfiguration) Mer information om strukturen för den här filen finns i [runconfig.jspå](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Anger om steget ska återanvända tidigare resultat när det körs igen med samma inställningar. |

Följande exempel innehåller ett Python-skriptsteg:

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

### <a name="parallel-run-step"></a>Parallella körningssteg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `inputs` | Indata kan vara [Datauppsättning,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)eller [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Utdata kan vara [antingen PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [eller OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `script_name` | Namnet på Python-skriptet (i förhållande till `source_directory` ). |
| `source_directory` | Katalog som innehåller skriptet, Conda-miljön osv. |
| `parallel_run_config` | Sökvägen till en `parallel_run_config.yml` fil. Den här filen är en YAML-representation av [klassen ParallelRunConfig.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) |
| `allow_reuse` | Anger om steget ska återanvända tidigare resultat när det körs igen med samma inställningar. |

Följande exempel innehåller ett parallellkörningssteg:

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
| `steps` | Sekvens av en eller flera PipelineStep-definitioner. Observera att `destination` nycklarna för ett steg blir nycklarna till i nästa `outputs` `source` `inputs` steg.| 

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

När du definierar schemat för en pipeline kan det antingen vara datalagerutlöst eller återkommande baserat på ett tidsintervall. Följande är de nycklar som används för att definiera ett schema:

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `description` | En beskrivning av schemat. |
| `recurrence` | Innehåller upprepningsinställningar om schemat är återkommande. |
| `pipeline_parameters` | Alla parametrar som krävs av pipelinen. |
| `wait_for_provisioning` | Om du vill vänta tills etableringen av schemat har slutförts. |
| `wait_timeout` | Antalet sekunder som ska vänta innan en tidsinställning går ut. |
| `datastore_name` | Det datalager som ska övervakas efter ändrade/tillagda blobar. |
| `polling_interval` | Hur lång tid, i minuter, mellan avsökning efter ändrade/tillagda blobar. Standardvärde: 5 minuter. Stöds endast för datalagerscheman. |
| `data_path_parameter_name` | Namnet på pipelineparametern för datasökvägen som ska anges med den ändrade blobsökvägen. Stöds endast för datalagerscheman. |
| `continue_on_step_failure` | Om du vill fortsätta att köra andra steg i den skickade PipelineKör om ett steg misslyckas. Om det anges åsidosätter `continue_on_step_failure` inställningen för pipelinen.
| `path_on_datastore` | Valfritt. Sökvägen i datalagringen för att övervaka ändrade/tillagda blobar. Sökvägen finns under containern för datalagringen, så den faktiska sökvägen som schemat övervakar är container/ `path_on_datastore` . Om det inte finns någon övervakas datalagercontainern. Tillägg/ändringar som görs i en undermapp av `path_on_datastore` övervakas inte. Stöds endast för datalagerscheman. |

Följande exempel innehåller definitionen för ett schema som utlöses av ett datalager:

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

När du **definierar ett återkommande** schema använder du följande nycklar under `recurrence` :

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `frequency` | Hur ofta schemat upprepas. Giltiga värden är `"Minute"` , , , eller `"Hour"` `"Day"` `"Week"` `"Month"` . |
| `interval` | Hur ofta schemat ska skjutas upp. Heltalsvärdet är antalet tidsenheter som ska vänta tills schemat sätts i brand igen. |
| `start_time` | Starttiden för schemat. Strängformatet för värdet är `YYYY-MM-DDThh:mm:ss` . Om ingen starttid anges körs den första arbetsbelastningen direkt och framtida arbetsbelastningar körs enligt schemat. Om starttiden ligger i det förflutna körs den första arbetsbelastningen vid nästa beräknade körningstid. |
| `time_zone` | Tidszonen för starttiden. Om ingen tidszon anges används UTC. |
| `hours` | Om är eller kan du ange ett eller flera heltal från 0 till `frequency` 23, avgränsade med kommatecken, som de timmar på dagen då `"Day"` `"Week"` pipelinen ska köras. Endast `time_of_day` eller och kan `hours` `minutes` användas. |
| `minutes` | Om är eller kan du ange ett eller flera heltal från 0 till `frequency` 59, avgränsade med kommatecken, som minuter i timmen när `"Day"` `"Week"` pipelinen ska köras. Endast `time_of_day` eller och kan `hours` `minutes` användas. |
| `time_of_day` | Om `frequency` är eller kan du ange en tid på dagen då schemat ska `"Day"` `"Week"` köras. Strängformatet för värdet är `hh:mm` . Endast `time_of_day` eller och kan `hours` `minutes` användas. |
| `week_days` | Om `frequency` är kan du ange en eller flera `"Week"` dagar, avgränsade med kommatecken, när schemat ska köras. Giltiga värden är `"Monday"` , , , , , , och `"Tuesday"` `"Wednesday"` `"Thursday"` `"Friday"` `"Saturday"` `"Sunday"` . |

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

Lär dig hur [du använder CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
