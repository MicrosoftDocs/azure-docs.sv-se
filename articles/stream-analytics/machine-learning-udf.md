---
title: Integrera Azure Stream Analytics med Azure Machine Learning
description: I den här artikeln beskrivs hur du integrerar ett Azure Stream Analytics-jobb med Azure Machine Learning-modeller.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: devx-track-js
ms.openlocfilehash: c35d5d2f63f4a7abe80a0ff19e5994013355c386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020527"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integrera Azure Stream Analytics med Azure Machine Learning (förhands granskning)

Du kan implementera maskin inlärnings modeller som en användardefinierad funktion (UDF) i dina Azure Stream Analytics jobb för att utföra resultat i real tid och förutsägelser på dina strömmande indata. Med [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) kan du använda alla populära verktyg för öppen källkod, till exempel Tensorflow, scikit-lära eller PyTorch, för att prepa, träna och distribuera modeller.

## <a name="prerequisites"></a>Förutsättningar

Slutför följande steg innan du lägger till en Machine Learning-modell som en funktion i Stream Analytics jobbet:

1. Använd Azure Machine Learning för att [distribuera din modell som en webb tjänst](../machine-learning/how-to-deploy-and-where.md).

2. Ditt bedömnings skript ska ha [exempel på indata och utdata](../machine-learning/how-to-deploy-and-where.md) som används av Azure Machine Learning för att generera en schema specifikation. Stream Analytics använder schemat för att förstå funktions signaturen för webb tjänsten. Du kan använda den här [exempel Swagger-definitionen](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json) som en referens för att kontrol lera att du har konfigurerat den korrekt.

3. Kontrol lera att din webb tjänst accepterar och returnerar JSON-serialiserade data.

4. Distribuera din modell på [Azure Kubernetes service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) för storskaliga produktions distributioner. Om webb tjänsten inte kan hantera antalet begär Anden som kommer från jobbet försämras prestanda för ditt Stream Analytics jobb, vilket påverkar svars tiden. Modeller som distribueras på Azure Container Instances stöds bara när du använder Azure Portal. Modeller som skapats med [Azure Machine Learning designer](../machine-learning/concept-designer.md) stöds ännu inte i Stream Analytics.

## <a name="add-a-machine-learning-model-to-your-job"></a>Lägga till en maskin inlärnings modell i ditt jobb

Du kan lägga till Azure Machine Learning funktioner till ditt Stream Analytics-jobb direkt från Azure Portal eller Visual Studio Code.

### <a name="azure-portal"></a>Azure Portal

1. Navigera till Stream Analytics jobb i Azure Portal och välj **funktioner** under **jobb sto pol Ogin**. Välj sedan **Azure Machine Learning tjänst** på list menyn **+ Lägg till** .

   ![Lägg till Azure Machine Learning UDF](./media/machine-learning-udf/add-azure-machine-learning-udf.png)

2. Fyll i formuläret för **Azure Machine Learning tjänst funktionen** med följande egenskaps värden:

   ![Konfigurera Azure Machine Learning UDF](./media/machine-learning-udf/configure-azure-machine-learning-udf.png)

### <a name="visual-studio-code"></a>Visuell Studio-kod

1. Öppna ditt Stream Analytics-projekt i Visual Studio Code och högerklicka på mappen **Functions** . Välj sedan **Lägg till funktion**. Välj **Machine Learning UDF** i list rutan.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function.png" alt-text="Lägg till UDF i VS Code":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function-2.png" alt-text="Lägg till Azure Machine Learning UDF i VS Code":::

2. Ange funktions namnet och fyll i inställningarna i konfigurations filen genom **att använda Välj från dina prenumerationer** i CodeLens.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-function-name.png" alt-text="Välj Azure Machine Learning UDF i VS Code":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-configure-settings.png" alt-text="Konfigurera Azure Machine Learning UDF i VS Code":::

I följande tabell beskrivs varje egenskap för Azure Machine Learning tjänst funktioner i Stream Analytics.

|Egenskap|Beskrivning|
|--------|-----------|
|Funktions Ali Aset|Ange ett namn för att anropa funktionen i frågan.|
|Prenumeration|Din Azure-prenumeration..|
|Azure Machine Learning-arbetsyta|Arbets ytan Azure Machine Learning som du använde för att distribuera din modell som en webb tjänst.|
|Distributioner|Webb tjänsten som är värd för din modell.|
|Funktions under skrift|Signaturen för webb tjänsten härleds från API: ns schema specifikation. Om din signatur inte kan läsas in, kontrollerar du att du har angett exempel indata och utdata i bedömnings skriptet för att automatiskt generera schemat.|
|Antal parallella förfrågningar per partition|Det här är en avancerad konfiguration för optimering av storskaliga data flöden. Det här talet representerar de samtidiga förfrågningar som skickas från varje partition av ditt jobb till webb tjänsten. Jobb med sex enheter för strömning (SU) och lägre har en partition. Jobb med 12 SUs har två partitioner, 18 SUs har tre partitioner och så vidare.<br><br> Om ditt jobb till exempel har två partitioner och du anger den här parametern till fyra, kommer det att finnas åtta samtidiga förfrågningar från jobbet till din webb tjänst. Vid den här tiden är standardvärdet 20 och kan inte uppdateras.|
|Max antal batchar|Det här är en avancerad konfiguration för optimering av storskalig data flöde. Det här antalet representerar det maximala antalet händelser som grupperas i en enskild begäran som skickas till din webb tjänst.|

## <a name="supported-input-parameters"></a>Indataparametrar som stöds

När din Stream Analytics fråga anropar en Azure Machine Learning UDF, skapar jobbet en JSON-serialiserad begäran till webb tjänsten. Begäran baseras på ett modell speciellt schema. Du måste ange ett exempel på indata och utdata i bedömnings skriptet för att [automatiskt generera ett schema](../machine-learning/how-to-deploy-and-where.md). Schemat tillåter Stream Analytics att skapa en JSON-serialiserad begäran för någon av de data typer som stöds, till exempel numpy, Pandas och PySpark. Flera ingångs händelser kan grupperas tillsammans i en enskild begäran.

Följande Stream Analytics fråga är ett exempel på hur du anropar en Azure Machine Learning UDF:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics har endast stöd för att skicka en parameter för Azure Machine Learning-funktioner. Du kan behöva förbereda dina data innan du skickar dem som indata till Machine Learning UDF. Du måste se till att indata till ML UDF inte är null eftersom null-indata kan leda till att jobbet Miss fungerar.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Skicka flera indataparametrar till UDF

De vanligaste exempel på indata till Machine Learning-modeller är numpy-matriser och DataFrames. Du kan skapa en matris med ett JavaScript-skript UDF och skapa en JSON-serialiserad DataFrame med hjälp av- `WITH` satsen.

### <a name="create-an-input-array"></a>Skapa en indatakälla

Du kan skapa ett JavaScript-skript UDF som accepterar *N* antal indata och skapar en matris som kan användas som indata till Azure Machine Learning UDF.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

När du har lagt till JavaScript UDF-filen till jobbet kan du anropa Azure Machine Learning UDF med följande fråga:

```SQL
WITH 
ModelInput AS (
#use JavaScript UDF to construct array that will be used as input to ML UDF
SELECT udf.createArray(vendorid, weekday, pickuphour, passenger, distance) as inputArray
FROM input
)

SELECT udf.score(inputArray)
INTO output
FROM ModelInput
#validate inputArray is not null before passing it to ML UDF to prevent job from failing
WHERE inputArray is not null
```

Följande JSON är en exempel förfrågan:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Skapa en Pandas-eller PySpark-DataFrame

Du kan använda- `WITH` satsen för att skapa en JSON-serialiserad DataFrame som kan skickas som inmatad till Azure Machine Learning UDF som visas nedan.

Följande fråga skapar en DataFrame genom att välja de nödvändiga fälten och använda DataFrame som indatamängden för Azure Machine Learning UDF.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM Dataframe
```

Följande JSON är en exempel förfrågan från föregående fråga:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Optimera prestanda för Azure Machine Learning UDF: er

När du distribuerar din modell till Azure Kubernetes-tjänsten kan du [profilera din modell för att avgöra resursutnyttjande](../machine-learning/how-to-deploy-profile-model.md). Du kan också [aktivera App Insights för dina distributioner](../machine-learning/how-to-enable-app-insights.md) för att förstå begär ande frekvenser, svars tider och felaktiga frekvenser.

Om du har ett scenario med hög händelse genom strömning kan du behöva ändra följande parametrar i Stream Analytics för att uppnå optimala prestanda med låg latens från slut punkt till slut punkt:

1. Max antal batchar.
2. Antal parallella förfrågningar per partition.

### <a name="determine-the-right-batch-size"></a>Fastställ rätt batchstorlek

När du har distribuerat din webb tjänst skickar du exempel förfrågan med varierande batch-storlekar som börjar från 50 och ökar den i storleksordningen hundratals. Till exempel 200, 500, 1000, 2000 och så vidare. Du ser att efter en viss batchstorlek ökar svars tiden för svaret. Den punkt efter vilken svars tiden ökar ska vara det maximala antalet batchar för ditt jobb.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Fastställa antalet parallella förfrågningar per partition

Vid optimal skalning bör ditt Stream Analyticss jobb kunna skicka flera parallella förfrågningar till din webb tjänst och få svar inom några millisekunder. Svars tiden för webb tjänstens svar kan påverka svars tiden och prestandan för ditt Stream Analytics jobb direkt. Om anropet från ditt jobb till webb tjänsten tar lång tid, ser du förmodligen en ökning i vatten märkes fördröjningen och kan också se en ökning av antalet eftersläpande inloggade ingångs händelser.

För att förhindra sådan fördröjning kontrollerar du att ditt Azure Kubernetes service-kluster (AKS) har etablerats med [rätt antal noder och repliker](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). Det är viktigt att webb tjänsten har hög tillgänglighet och returnerar lyckade svar. Om ditt jobb tar emot ett svar som inte är tillgängligt för tjänsten (503) från din webb tjänst, kommer det fort löp ande att göras igen med exponentiell avstängning. Om något annat svar än lyckat (200) och tjänsten inte är tillgängligt (503) kommer att leda till att jobbet hamnar i ett felaktigt tillstånd.

## <a name="next-steps"></a>Nästa steg

* [Självstudier: Användardefinierade funktioner i Azure Stream Analytics JavaScript](stream-analytics-javascript-user-defined-functions.md)
* [Skala ditt Stream Analytics jobb med Azure Machine Learning Studio (klassisk)-funktionen](stream-analytics-scale-with-machine-learning-functions.md)
