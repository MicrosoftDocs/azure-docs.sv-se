---
title: 'ML Studio (klassisk): skapa flera modell & slut punkter – Azure'
description: Använd PowerShell för att skapa flera Machine Learning-modeller och webb tjänst slut punkter med samma algoritm men olika data uppsättningar för utbildning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 35b5fe4556f1d557d3fc0420e9069f2fb510eec4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520518"
---
# <a name="create-multiple-web-service-endpoints-from-one-experiment-with-ml-studio-classic-and-powershell"></a>Skapa flera webb tjänst slut punkter från ett experiment med ML Studio (klassisk) och PowerShell

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Här är ett vanligt problem med Machine Learning: du vill skapa många modeller som har samma utbildnings arbets flöde och använda samma algoritm. Men du vill att de ska ha olika data uppsättningar för utbildning som indata. Den här artikeln visar hur du gör detta i skala i Azure Machine Learning Studio (klassisk) med bara ett enda experiment.

Anta till exempel att du äger en global cykel uthyrning av franchise företag. Du vill bygga en Regressions modell för att förutsäga hyres behovet baserat på historiska data. Du har 1 000 hyr platser över hela världen och du har samlat in en data uppsättning för varje plats. De innehåller viktiga funktioner som datum, tid, väder och trafik som är olika för varje plats.

Du kan träna din modell en gång med en sammanfogad version av alla data uppsättningar på alla platser. Men var och en av dina platser har en unik miljö. Ett bättre tillvägagångs sätt är att träna Regressions modellen separat med data uppsättningen för varje plats. På så sätt kan varje utbildad modell ta hänsyn till de olika lagrings storlekarna, volym, geografi, befolkning, cykel vänliga trafik miljö med mera.

Det kan vara den bästa metoden, men du vill inte skapa 1 000-tränings experiment i Azure Machine Learning Studio (klassisk) med var och en representerar en unik plats. Förutom att vara en överbelastad aktivitet verkar den också ineffektiv eftersom varje experiment skulle ha samma komponenter, förutom inlärnings data uppsättningen.

Lyckligt vis kan du göra detta med hjälp av [API: et för Azure Machine Learning Studio (klassisk)](./retrain-machine-learning-model.md) och automatisera uppgiften med [Azure Machine Learning Studio (klassisk) PowerShell](powershell-module.md).

> [!NOTE]
> Om du vill att exemplet ska köras snabbare minskar du antalet platser från 1 000 till 10. Men samma principer och procedurer gäller för 1 000 platser. Men om du vill träna från 1 000 data uppsättningar kanske du vill köra följande PowerShell-skript parallellt. Gör så här utöver den här artikelns omfattning, men du hittar exempel på PowerShell multi-threading på Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Konfigurera övnings experimentet
Använd exemplet på [inlärnings experiment](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) som finns i [Cortana Intelligence Gallery](https://gallery.azure.ai). Öppna det här experimentet i arbets ytan [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net) .

> [!NOTE]
> För att följa med i det här exemplet kanske du vill använda en standard arbets yta i stället för en kostnads fri arbets yta. Du skapar en slut punkt för varje kund, för totalt 10 slut punkter – och som kräver en standard arbets yta eftersom en kostnads fri arbets yta är begränsad till 3 slut punkter.
> 
> 

Experimentet använder modulen **Importera data** för att importera utbildnings data uppsättningen *customer001.csv* från ett Azure Storage-konto. Vi antar att du har samlat in inlärnings data uppsättningar från alla cykel uthyrnings platser och lagrat dem på samma Blob Storage-plats med fil namn som sträcker sig från *rentalloc001.csv* till *rentalloc10.csv*.

![Läsar modulen importerar data från en Azure-Blob](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Observera att en **webb tjänst utmatnings** modul har lagts till modulen **träna modell** .
När det här experimentet distribueras som en webb tjänst, returnerar slut punkten som är kopplad till utdata den tränade modellen i formatet för en. ilearner-fil.

Observera också att du konfigurerar en webb tjänst parameter som definierar den URL som används av modulen **Importera data** . På så sätt kan du använda-parametern för att ange enskilda inlärnings data uppsättningar för att träna modellen för varje plats.
Det finns andra sätt som du kan ha gjort. Du kan använda en SQL-fråga med en webb tjänst parameter för att hämta data från en databas i Azure SQL Database. Du kan också använda en modul för  **indata för webb tjänst** för att skicka in en data uppsättning till webb tjänsten.

![En utbildad modell modul ger utdata till en webb tjänst utmatnings modul](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Nu ska vi köra det här övnings experimentet med hjälp av standardvärdet *rental001.csv* som inlärnings data uppsättningen. Om du visar utdata från **evaluate** -modulen (klicka på utdata och väljer **visualisera**) kan du se att du får en vettigt prestanda på *AUC* = 0,91. Nu är du redo att distribuera en webb tjänst från det här övnings experimentet.

## <a name="deploy-the-training-and-scoring-web-services"></a>Distribuera utbildnings-och poängsättnings webb tjänster
Du distribuerar utbildnings webb tjänsten genom att klicka på knappen **Konfigurera webb tjänst** under experimentets arbets yta och välja **distribuera webb tjänst**. Anropa den här webb tjänsten "cykel uthyrnings utbildning".

Nu måste du distribuera bedömnings webb tjänsten.
Det gör du genom att klicka på **Konfigurera webb tjänsten** under arbets ytan och välja **förutsägbar webb tjänst**. Detta skapar ett Poäng experiment.
Du måste göra några små justeringar för att det ska fungera som en webb tjänst. Ta bort etikettens kolumn "CNT" från indata och begränsa utdata till endast instans-ID och motsvarande förutsägande värde.

För att du ska kunna spara pengar kan du öppna det [förutsägande experimentet](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) i galleriet som redan har förberetts.

Om du vill distribuera webb tjänsten kör du förutsägelse experimentet och klickar sedan på knappen **distribuera webb tjänst** under arbets ytan. Namnge poängsättnings webb tjänsten "cykel hyres Poäng".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Skapa 10 identiska webb tjänst slut punkter med PowerShell
Den här webb tjänsten levereras med en standard slut punkt. Men du är inte lika intresse rad av standard slut punkten eftersom den inte kan uppdateras. Vad du behöver göra är att skapa ytterligare 10 slut punkter, en för varje plats. Du kan göra detta med PowerShell.

Först ställer du in PowerShell-miljön:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and is properly set to point to the valid Workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'
```

Kör sedan följande PowerShell-kommando:

```powershell
# Create 10 endpoints on the scoring web service.
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpointName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}
```

Nu har du skapat 10 slut punkter och alla innehåller samma utbildade modell som har tränats på *customer001.csv*. Du kan visa dem i Azure Portal.

![Visa listan över tränade modeller i portalen](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Uppdatera slut punkterna för att använda separata inlärnings data uppsättningar med PowerShell
Nästa steg är att uppdatera slut punkterna med modeller som är unikt utbildade för varje kunds individuella data. Men först måste du skapa dessa modeller från utbildnings webb tjänsten för **cykel uthyrning** . Nu ska vi gå tillbaka till webb tjänsten för **cykel uthyrnings utbildning** . Du måste anropa dess BES-slutpunkt 10 gånger med 10 olika data uppsättningar för utbildning för att kunna producera 10 olika modeller. Använd **InovkeAmlWebServiceBESEndpoint** PowerShell-cmdleten för att göra detta.

Du måste också ange autentiseringsuppgifter för ditt Blob Storage-konto i `$configContent` . Dvs, i fälten `AccountName` , `AccountKey` och `RelativeLocation` . `AccountName`Kan vara ett av dina konto namn, som visas på fliken **Azure Portal** (fliken *lagring* ). När du klickar på ett lagrings konto `AccountKey` kan du hitta det genom att trycka på knappen **Hantera åtkomst nycklar** längst ned och kopiera den *primära åtkomst nyckeln*. `RelativeLocation`Är sökvägen i förhållande till lagrings utrymmet där en ny modell kommer att lagras. Till exempel `hai/retrain/bike_rental/` pekar sökvägen i följande skript till en behållare med namnet `hai` och `/retrain/bike_rental/` är undermappar. För närvarande kan du inte skapa undermappar via portalens användar gränssnitt, men det finns [flera Azure Storage Explorer](../../storage/common/storage-explorers.md) som gör det möjligt att göra det. Vi rekommenderar att du skapar en ny behållare i lagringen för att lagra nya utbildade modeller (. iLearner-filer) enligt följande: på sidan lagring klickar du på knappen **Lägg till** längst ned och namnger den `retrain` . I sammanfattning är de nödvändiga ändringarna i följande skript kopplade till `AccountName` , `AccountKey` och `RelativeLocation` (: `"retrain/model' + $seq + '.ilearner"` ).

```powershell
# Invoke the retraining API 10 times
# This is the default (and the only) endpoint on the training web service
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}
```

> [!NOTE]
> BES-slutpunkten är det enda läge som stöds för den här åtgärden. Det går inte att använda resurs poster för att producera utbildade modeller.
> 
> 

Som du ser ovan, i stället för att skapa 10 olika JSON-filer för BES-jobb, skapar du en dynamisk konfigurations sträng dynamiskt i stället. Mata sedan in den till parametern *jobConfigString* för **InvokeAmlWebServceBESEndpoint** -cmdleten. Det finns faktiskt inget behov av att spara en kopia på disk.

Om allt går bra, efter ett tag kan du se 10. iLearner-filer, från *model001. iLearner* till *model010. iLearner*, i ditt Azure Storage-konto. Nu är du redo att uppdatera webb tjänst slut punkterna för 10 Poäng med dessa modeller med hjälp av PowerShell-cmdleten **patch-AmlWebServiceEndpoint** . Kom ihåg att du bara kan korrigera de slut punkter som inte är standard som du har skapat tidigare.

```powershell
# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '<my_blob_sas_token>'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```

Detta bör köras ganska snabbt. När körningen har slutförts har du skapat 10 förutsägbara webb tjänst slut punkter. Var och en innehåller en utbildad modell som är unikt utbildad på den data uppsättning som är specifika för en hyres plats, allt från ett enda utbildnings experiment. Om du vill kontrol lera detta kan du prova att anropa dessa slut punkter med **InvokeAmlWebServiceRRSEndpoint** -cmdleten och ge dem samma indata. Du bör förvänta dig att se olika förutsägelse resultat eftersom modellerna tränas med olika inlärnings uppsättningar.

## <a name="full-powershell-script"></a>Fullständigt PowerShell-skript
Här är en lista över den fullständiga käll koden:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and properly set to point to the valid workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

# Create 10 endpoints on the scoring web service
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpontName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}

# Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}

# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '?test'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```