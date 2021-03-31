---
title: Göra förutsägelser med AutoML ONNX-modellen i .NET
description: Lär dig hur du gör förutsägelser med en AutoML ONNX-modell i .NET med ML.NET
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to, automl
ms.openlocfilehash: 4fb147dc5c57c3a98607a025f566fa583bf87460
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93358821"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>Göra förutsägelser med en AutoML ONNX-modell i .NET

I den här artikeln får du lära dig hur du använder en automatiserad ML-AutoML (neurala Network Exchange) för att göra förutsägelser i ett C# .NET Core-konsol program med ML.NET.

[Ml.net](/dotnet/machine-learning/) är ett ramverk för maskin inlärning med öppen källkod, plattforms oberoende ramverk för .net-eko system som gör att du kan träna och använda anpassade modeller för maskin inlärning med en kod för första metoden i C# eller F # samt genom att använda verktyg med lågt kod, till exempel [Model Builder](/dotnet/machine-learning/automate-training-with-model-builder) och [ml.net CLI](/dotnet/machine-learning/automate-training-with-cli). Ramverket är också utöknings Bart och gör att du kan utnyttja andra populära ramverk för maskin inlärning som TensorFlow och ONNX.

ONNX är ett format med öppen källkod för AI-modeller. ONNX stöder samverkan mellan ramverk. Det innebär att du kan träna en modell i ett av de många populära ramverken för maskin inlärning som PyTorch, konvertera den till ONNX-format och använda ONNX-modellen i ett annat ramverk som ML.NET. Mer information finns på webbplatsen för [ONNX](https://onnx.ai/).

## <a name="prerequisites"></a>Förutsättningar

- [.NET Core SDK 3,1 eller mer](https://dotnet.microsoft.com/download)
- Text redigerare eller IDE (till exempel [Visual Studio](https://visualstudio.microsoft.com/vs/) eller [Visual Studio Code](https://code.visualstudio.com/Download))
- ONNX-modell. Information om hur du tränar en AutoML ONNX-modell finns i följande [Banks antecknings bok för bank marknadsföring](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).
- [Netron](https://github.com/lutzroeder/netron) (valfritt)

## <a name="create-a-c-console-application"></a>Skapa ett C#-konsol program

I det här exemplet använder du .NET Core CLI för att skapa programmet, men du kan göra samma uppgifter med Visual Studio. Läs mer om [.net Core CLI](/dotnet/core/tools/).

1. Öppna en Terminal och skapa ett nytt C# .NET Core-konsolprogram. I det här exemplet är namnet på programmet `AutoMLONNXConsoleApp` . En katalog skapas med samma namn med innehållet i ditt program.

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. I terminalen navigerar du till *AutoMLONNXConsoleApp* -katalogen.

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>Lägg till program varu paket

1. Installera **Microsoft.ml**, **Microsoft. ml. OnnxRuntime** och **Microsoft. ml. OnnxTransformer** NuGet-paketen med hjälp av .net Core cli.

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    Dessa paket innehåller de beroenden som krävs för att använda en ONNX-modell i ett .NET-program. ML.NET tillhandahåller ett API som använder [ONNX runtime](https://github.com/Microsoft/onnxruntime) för förutsägelser.

1. Öppna filen *program. cs* och Lägg till följande- `using` instruktioner överst för att referera till lämpliga paket.

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>Lägg till en referens till ONNX-modellen

Ett sätt för konsol programmet att komma åt ONNX-modellen är att lägga till den i skapa utdata-katalogen.  Mer information om MSBuild common-objekt finns i [MSBuild-guiden](/visualstudio/msbuild/common-msbuild-project-items).

Lägg till en referens till din ONNX-modell fil i ditt program

1. Kopiera ONNX-modellen till programmets rot Katalog för *AutoMLONNXConsoleApp* .
1. Öppna filen *AutoMLONNXConsoleApp. CSPROJ* och Lägg till följande innehåll i `Project` noden.

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    I det här fallet är namnet på ONNX Model *-filen automl-Model. ONNX*.

1. Öppna filen *program. cs* och Lägg till följande rad inuti `Program` klassen.

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>Initiera MLContext

I `Main` -metoden för din `Program` klass skapar du en ny instans av [`MLContext`](xref:Microsoft.ML.MLContext) .

```csharp
MLContext mlContext = new MLContext();
```

[`MLContext`](xref:Microsoft.ML.MLContext)Klassen är en start punkt för alla ml.net-åtgärder och initiering `mlContext` skapar en ny ml.net-miljö som kan delas över modell livs cykeln. Det är liknande, konceptuellt, att DbContext i Entity Framework.

## <a name="define-the-model-data-schema"></a>Definiera modell data schema

Din modell förväntar sig dina indata och utdata i ett särskilt format. Med ML.NET kan du definiera formatet för dina data via klasser. Ibland kanske du redan vet vad det formatet ser ut. Om du inte känner till data formatet kan du använda verktyg som Netron för att inspektera din ONNX-modell.

Modellen som används i det här exemplet använder data från data uppsättningen NYC TLC taxi-resa. Du kan se ett exempel på data nedan:

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3.75|CRD|15,5|
|VTS|1|1|480|2,72|CRD|10,0|
|VTS|1|1|1 680|7,8|CSH|26,5|

### <a name="inspect-the-onnx-model-optional"></a>Granska ONNX-modellen (valfritt)

Använd ett verktyg som Netron för att kontrol lera din modells indata och utdata.

1. Öppna Netron.
1. I den översta meny raden väljer du **fil > öppna** och använder fil läsaren för att välja din modell.
1. Din modell öppnas. Strukturen för modellen *automl-Model. Onnx* ser till exempel ut så här:

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML ONNX Model":::

1. Välj den sista noden längst ned i diagrammet ( `variable_out1` i det här fallet) för att Visa modellens metadata. Indata och utdata på sid panelen visar modellens förväntade indata, utdata och data typer. Använd den här informationen för att definiera schemat för indata och utdata för din modell.

### <a name="define-model-input-schema"></a>Definiera schema för modell inflöde

Skapa en ny klass `OnnxInput` som kallas med följande egenskaper i filen *program. cs* .

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

Var och en av egenskaperna mappas till en kolumn i data uppsättningen. Egenskaperna kommenteras ytterligare med attribut.

Med [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) attributet kan du ange hur ml.net ska referera till kolumnen vid användning av data. Till exempel, även om `TripDistance` egenskapen följer standard namngivnings konventionerna för .net vet modellen bara för en kolumn eller funktion som kallas `trip_distance` . [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute)Attributet mappar `TripDistance` egenskapen till en kolumn eller funktion med namnet för att åtgärda den här namngivnings avvikelsen `trip_distance` .
  
För numeriska värden fungerar ML.NET endast på [`Single`](xref:System.Single) värde typer. Den ursprungliga data typen för vissa av kolumnerna är dock heltal. [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute)Attributet Maps typer mellan ONNX och ml.net.

Läs mer om dataattribut i [ml.net load data guide](/dotnet/machine-learning/how-to-guides/load-data-ml-net).

### <a name="define-model-output-schema"></a>Definiera schema för modell utdata

När data har bearbetats skapas utdata av ett visst format. Definiera schemat för datautdata. Skapa en ny klass `OnnxOutput` som kallas med följande egenskaper i filen *program. cs* .

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

Liknar `OnnxInput` använder du [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) attributet för att mappa `variable_out1` utdata till ett mer beskrivande namn `PredictedFare` .

## <a name="define-a-prediction-pipeline"></a>Definiera en förutsägelse pipeline

En pipeline i ML.NET är vanligt vis en serie kedjade omvandlingar som fungerar med indata för att generera utdata. Mer information om data [omvandlingar finns i ml.net data Transformation guide](/dotnet/machine-learning/resources/transforms).

1. Skapa en ny metod `GetPredictionPipeline` som kallas inuti `Program` klassen

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. Definiera namnet på kolumnerna indata och utdata. Lägg till följande kod i- `GetPredictionPipeline` metoden.

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. Definiera din pipeline. En [`IEstimator`](xref:Microsoft.ML.IEstimator%601) innehåller en skiss över drift-, indata och utdata-scheman för din pipeline.

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    I det här fallet [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) är den enda transformeringen i pipelinen, som tar in namnen på indata-och utdatakolumner samt sökvägen till ONNX-modell filen.

1. En [`IEstimator`](xref:Microsoft.ML.IEstimator%601) definierar bara den uppsättning åtgärder som ska användas för dina data. Det som fungerar på dina data kallas för [`ITransformer`](xref:Microsoft.ML.ITransformer) . Använd `Fit` metoden för att skapa en från din `onnxPredictionPipeline` .

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A)Metoden förväntar sig ett [`IDataView`](xref:Microsoft.ML.IDataView) as-inflöde för att utföra åtgärder på. En [`IDataView`](xref:Microsoft.ML.IDataView) är ett sätt att visa data i ml.net med hjälp av tabell format. Eftersom pipelinen bara används för förutsägelser, kan du ange en tom [`IDataView`](xref:Microsoft.ML.IDataView) för att ge den [`ITransformer`](xref:Microsoft.ML.ITransformer) nödvändiga informationen om indata och utdata. Den monterade [`ITransformer`](xref:Microsoft.ML.ITransformer) sedan returneras för att användas i ditt program.

    > [!TIP]
    > I det här exemplet definieras pipelinen och används i samma program. Vi rekommenderar dock att du använder separata program för att definiera och använda din pipeline för att göra förutsägelser. I ML.NET kan dina pipelines serialiseras och sparas för att användas på annat sätt i andra .NET-slut användar program. ML.NET har stöd för olika distributions mål som Skriv bords program, webb tjänster, webb sammansättnings program * och många fler. Mer information om hur du sparar pipelines finns i [guiden för att spara och läsa in tränade modeller i ml.net](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net).
    >
    > * WebAssembly stöds bara i .NET Core 5 eller senare

1. I `Main` -metoden anropar du `GetPredictionPipeline` metoden med de obligatoriska parametrarna.

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>Använd modellen för att göra förutsägelser

Nu när du har en pipeline är det dags att använda den för att göra förutsägelser. ML.NET tillhandahåller ett bekvämlighets-API för att göra förutsägelser på en enskild data instans som kallas [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) .

1. I `Main` -metoden skapar du en [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) med hjälp av- [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) metoden.

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. Skapa en test data inmatning.

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. Använd `predictionEngine` för att göra förutsägelser baserat på nya `testInput` data med hjälp av [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) metoden.

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. Visar resultatet av din förutsägelse till-konsolen.

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. Använd .NET Core CLI för att köra programmet.

    ```dotnetcli
    dotnet run
    ```

    Resultatet bör se ut ungefär så här:

    ```text
    Predicted Fare: 15.621523
    ```

Mer information om hur du gör förutsägelser i ML.NET finns i [använda en modell för att skapa förutsägelse guide](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net).

## <a name="next-steps"></a>Nästa steg

- [Distribuera din modell som en ASP.NET Core webb-API](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [Distribuera din modell som en server lös .NET Azure-funktion](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)