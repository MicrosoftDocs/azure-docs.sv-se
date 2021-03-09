---
title: Publicera ML-pipeliner
titleSuffix: Azure Machine Learning
description: Kör Machine Learning-arbetsflöden med maskin inlärnings pipeliner och Azure Machine Learning SDK för python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: efedb21a1ec1ed53a8c6bfadf337d23a89c04383
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520184"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Publicera och spåra maskin inlärnings pipeliner



Den här artikeln visar hur du kan dela en pipeline för maskin inlärning med dina kollegor eller kunder.

Maskin inlärnings pipeliner är återanvändbara arbets flöden för Machine Learning-uppgifter. En fördel med pipelines har ökat samarbete. Du kan också versions pipelines, så att kunderna kan använda den aktuella modellen när du arbetar med en ny version. 

## <a name="prerequisites"></a>Förutsättningar

* Skapa en [Azure Machine Learning arbets yta](how-to-manage-workspace.md) för att lagra alla dina pipeline-resurser

* [Konfigurera utvecklings miljön](how-to-configure-environment.md) för att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning beräknings instans](concept-compute-instance.md) med SDK redan installerad

* Skapa och kör en pipeline för maskin inlärning, till exempel i följande [självstudie: skapa en Azure Machine Learning pipeline för batch-Poäng](tutorial-pipeline-batch-scoring-classification.md). Andra alternativ finns i [skapa och köra Machine Learning-pipeliner med Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)

## <a name="publish-a-pipeline"></a>Publicera en pipeline

När du har en pipeline igång kan du publicera en pipeline så att den körs med olika indata. För REST-slutpunkten för en redan publicerad pipeline för att acceptera parametrar måste du konfigurera din pipeline så att `PipelineParameter` den använder objekt för argumenten som varierar.

1. Om du vill skapa en pipeline-parameter använder du ett [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter) -objekt med ett standardvärde.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Lägg till det här `PipelineParameter` objektet som en parameter till något av stegen i pipelinen enligt följande:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publicera den här pipelinen som ska ta emot en parameter när den anropas.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Köra en publicerad pipeline

Alla publicerade pipeliner har en REST-slutpunkt. Med pipeline-slutpunkten kan du utlösa en körning av pipelinen från alla externa system, inklusive icke-python-klienter. Den här slut punkten aktiverar "hanterad repeterbarhet" i batch-poängsättning och ominlärnings scenarier.

> [!IMPORTANT]
> Om du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera åtkomst till din pipeline, anger du [behörigheterna för ditt pipeline-scenario (utbildning eller poängsättning)](how-to-assign-roles.md#common-scenarios).

Om du vill anropa körningen av föregående pipeline behöver du en Azure Active Directory Authentication Head-token. Att hämta en sådan token beskrivs i [klass](/python/api/azureml-core/azureml.core.authentication.azurecliauthentication) referensen AzureCliAuthentication och i [autentiseringen i Azure Machine Learning](https://aka.ms/pl-restep-auth) Notebook.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

`json`Argumentet till post-begäran måste innehålla, för `ParameterAssignments` nyckeln, en ord lista som innehåller pipeline-parametrarna och deras värden. Dessutom `json` kan argumentet innehålla följande nycklar:

| Nyckel | Beskrivning |
| --- | --- | 
| `ExperimentName` | Namnet på experimentet som är associerat med den här slut punkten |
| `Description` | Fri hands text som beskriver slut punkten | 
| `Tags` | Nyckel/värde-par för fri hands text som kan användas för att märka och anteckna förfrågningar  |
| `DataSetDefinitionValueAssignments` | Ord lista som används för att ändra data uppsättningar utan omträning (se diskussionen nedan) | 
| `DataPathAssignments` | Ord lista som används för att ändra datapaths utan omträning (se diskussionen nedan) | 

### <a name="run-a-published-pipeline-using-c"></a>Köra en publicerad pipeline med hjälp av C # 

Följande kod visar hur du anropar en pipeline asynkront från C#. Det partiella kodfragmentet visar bara anrops strukturen och ingår inte i ett Microsoft-exempel. Det visar inte fullständiga klasser eller fel hantering. 

```csharp
[DataContract]
public class SubmitPipelineRunRequest
{
    [DataMember]
    public string ExperimentName { get; set; }

    [DataMember]
    public string Description { get; set; }

    [DataMember(IsRequired = false)]
    public IDictionary<string, string> ParameterAssignments { get; set; }
}

// ... in its own class and method ... 
const string RestEndpoint = "your-pipeline-endpoint";

using (HttpClient client = new HttpClient())
{
    var submitPipelineRunRequest = new SubmitPipelineRunRequest()
    {
        ExperimentName = "YourExperimentName", 
        Description = "Asynchronous C# REST api call", 
        ParameterAssignments = new Dictionary<string, string>
        {
            {
                // Replace with your pipeline parameter keys and values
                "your-pipeline-parameter", "default-value"
            }
        }
    };

    string auth_key = "your-auth-key"; 
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", auth_key);

    // submit the job
    var requestPayload = JsonConvert.SerializeObject(submitPipelineRunRequest);
    var httpContent = new StringContent(requestPayload, Encoding.UTF8, "application/json");
    var submitResponse = await client.PostAsync(RestEndpoint, httpContent).ConfigureAwait(false);
    if (!submitResponse.IsSuccessStatusCode)
    {
        await WriteFailedResponse(submitResponse); // ... method not shown ...
        return;
    }

    var result = await submitResponse.Content.ReadAsStringAsync().ConfigureAwait(false);
    var obj = JObject.Parse(result);
    // ... use `obj` dictionary to access results
}
```

### <a name="run-a-published-pipeline-using-java"></a>Köra en publicerad pipeline med Java

Följande kod visar ett anrop till en pipeline som kräver autentisering (se [Konfigurera autentisering för Azure Machine Learning resurser och arbets flöden](how-to-setup-authentication.md)). Om din pipeline distribueras offentligt behöver du inte de anrop som skapas `authKey` . Det partiella kodfragmentet visar inte något exempel på Java-klass och undantags hantering. Koden används `Optional.flatMap` för att länka samman funktioner som kan returnera ett tomt `Optional` . Användningen av `flatMap` förkortar och klargör koden, men Observera att `getRequestBody()` förtäring-undantag.

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Optional;
// JSON library
import com.google.gson.Gson;

String scoringUri = "scoring-endpoint";
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";
String dataToBeScored = "{ \"ExperimentName\" : \"My_Pipeline\", \"ParameterAssignments\" : { \"pipeline_arg\" : \"20\" }}";

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);
Optional<String> authBody = getRequestBody(client, tokenAuthenticationRequest);
Optional<String> authKey = authBody.flatMap(body -> Optional.of(gson.fromJson(body, AuthenticationBody.class).access_token);;
Optional<HttpRequest> scoringRequest = authKey.flatMap(key -> Optional.of(scoringRequest(key, scoringUri, dataToBeScored)));
Optional<String> scoringResult = scoringRequest.flatMap(req -> getRequestBody(client, req));
// ... etc (`scoringResult.orElse()`) ... 

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl)
{
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(authUrl))
        .POST(HttpRequest.BodyPublishers.ofString(bodyString))
        .build();
    return request;
}

static HttpRequest scoringRequest(String authKey, String scoringUri, String dataToBeScored)
{
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(scoringUri))
        .header("Authorization", String.format("Token %s", authKey))
        .POST(HttpRequest.BodyPublishers.ofString(dataToBeScored))
        .build();
    return request;

}

static Optional<String> getRequestBody(HttpClient client, HttpRequest request) {
    try {
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (response.statusCode() != 200) {
            System.out.println(String.format("Unexpected server response %d", response.statusCode()));
            return Optional.empty();
        }
        return Optional.of(response.body());
    }catch(Exception x)
    {
        System.out.println(x.toString());
        return Optional.empty();
    }
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

### <a name="changing-datasets-and-datapaths-without-retraining"></a>Ändra data uppsättningar och datapaths utan omträning

Du kanske vill träna och få en härledning på olika data uppsättningar och datapaths. Du kanske exempelvis vill träna på en mindre data uppsättning men en härledning på hela data uppsättningen. Du växlar data uppsättningar med `DataSetDefinitionValueAssignments` nyckeln i begärans `json` argument. Du växlar datapaths med `DataPathAssignments` . Tekniken för båda är liknande:

1. I definitions skriptet för pipelinen skapar du en `PipelineParameter` för data uppsättningen. Skapa en `DatasetConsumptionConfig` eller `DataPath` från `PipelineParameter` :

    ```python
    tabular_dataset = Dataset.Tabular.from_delimited_files('https://dprepdata.blob.core.windows.net/demo/Titanic.csv')
    tabular_pipeline_param = PipelineParameter(name="tabular_ds_param", default_value=tabular_dataset)
    tabular_ds_consumption = DatasetConsumptionConfig("tabular_dataset", tabular_pipeline_param)
    ```

1. I ML-skriptet får du åtkomst till den dynamiskt angivna data uppsättningen med `Run.get_context().input_datasets` :

    ```python
    from azureml.core import Run
    
    input_tabular_ds = Run.get_context().input_datasets['tabular_dataset']
    dataframe = input_tabular_ds.to_pandas_dataframe()
    # ... etc ...
    ```

    Observera att ML-skriptet använder värdet som angetts för `DatasetConsumptionConfig` ( `tabular_dataset` ) och inte värdet för `PipelineParameter` ( `tabular_ds_param` ).

1. I definitions skriptet för pipelinen anger du `DatasetConsumptionConfig` som en parameter till `PipelineScriptStep` :

    ```python
    train_step = PythonScriptStep(
        name="train_step",
        script_name="train_with_dataset.py",
        arguments=["--param1", tabular_ds_consumption],
        inputs=[tabular_ds_consumption],
        compute_target=compute_target,
        source_directory=source_directory)
    
    pipeline = Pipeline(workspace=ws, steps=[train_step])
    ```

1. Om du vill byta data uppsättningar dynamiskt i ditt inferencing REST-anrop använder du `DataSetDefinitionValueAssignments` :
    
    ```python
    tabular_ds1 = Dataset.Tabular.from_delimited_files('path_to_training_dataset')
    tabular_ds2 = Dataset.Tabular.from_delimited_files('path_to_inference_dataset')
    ds1_id = tabular_ds1.id
    d22_id = tabular_ds2.id
    
    response = requests.post(rest_endpoint, 
                             headers=aad_token, 
                             json={
                                "ExperimentName": "MyRestPipeline",
                               "DataSetDefinitionValueAssignments": {
                                    "tabular_ds_param": {
                                        "SavedDataSetReference": {"Id": ds1_id #or ds2_id
                                    }}}})
    ```

Antecknings böckerna som [demonstrerar data uppsättningen och PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) och [presenterar Datapath och PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) har fullständiga exempel på den här metoden.

## <a name="create-a-versioned-pipeline-endpoint"></a>Skapa en slut punkt för en versions pipeline

Du kan skapa en pipeline-slutpunkt med flera publicerade pipeliner bakom den. Den här tekniken ger dig en fast REST-slutpunkt när du itererar och uppdaterar dina ML-pipeliner.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Skicka ett jobb till en pipeline-slutpunkt

Du kan skicka ett jobb till standard versionen av en pipeline-slutpunkt:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Du kan också skicka ett jobb till en angiven version:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Samma kan åstadkommas med hjälp av REST API:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Använda publicerade pipelines i Studio

Du kan också köra en publicerad pipeline från Studio:

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. [Visa din arbets yta](how-to-manage-workspace.md#view).

1. Välj **slut punkter** till vänster.

1. Välj **pipeline-slutpunkter** högst upp.
 ![lista över de publicerade pipelinen för Machine Learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Välj en pipeline för att köra, använda eller Granska resultat från tidigare körningar av pipelinens slut punkt.

## <a name="disable-a-published-pipeline"></a>Inaktivera en publicerad pipeline

Om du vill dölja en pipeline från listan över publicerade pipeliner inaktiverar du den, antingen i Studio eller från SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Du kan aktivera den igen med `p.enable()` . Mer information finns i klass referens för [PublishedPipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline) .

## <a name="next-steps"></a>Nästa steg

- Använd [dessa Jupyter-anteckningsböcker på GitHub](https://aka.ms/aml-pipeline-readme) för att utforska maskin inlärnings pipeliner ytterligare.
- Se SDK Reference-hjälpen för [azureml-pipeline-Core-](/python/api/azureml-pipeline-core/) paketet och AzureML- [pipeline-steg-](/python/api/azureml-pipeline-steps/) paketet.
- Se tips om [hur du](how-to-debug-pipelines.md) felsöker och felsöker pipeliner.