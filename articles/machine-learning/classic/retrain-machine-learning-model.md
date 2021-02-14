---
title: 'ML Studio (klassisk): återträna en webb tjänst – Azure'
description: Lär dig hur du uppdaterar en webb tjänst för att använda en nytränad maskin inlärnings modell i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: a4fe9e54e5e03a8dbf2a727b22f784c36d6c65f9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517594"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Omträna och distribuera en maskin inlärnings modell

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Retraining är ett sätt att se till att maskin inlärnings modeller hålls korrekta och baseras på de mest relevanta data som finns tillgängliga. Den här artikeln visar hur du omtränar och distribuerar en maskin inlärnings modell som en ny webb tjänst i Studio (klassisk). Om du vill träna en klassisk webb tjänst kan du [läsa den här instruktions artikeln.](retrain-classic-web-service.md)

Den här artikeln förutsätter att du redan har en förutsägbar webb tjänst distribuerad. Om du inte redan har en förutsägbar webb tjänst, lär du [dig hur du distribuerar en Studio-webbtjänst (klassisk) här.](deploy-a-machine-learning-web-service.md)

Du följer de här stegen för att omträna och distribuera en ny webb tjänst för Machine Learning:

1. Distribuera en **webb tjänst för återutbildning**
1. Träna en ny modell med hjälp av din **webb tjänst för återutbildning**
1. Uppdatera ditt befintliga **förutsägelse experiment** för att använda den nya modellen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Distribuera webb tjänsten för retraining

Med en retraining-webbtjänst kan du träna om din modell med en ny uppsättning parametrar, t. ex. nya data och spara den för senare. När du ansluter en **webb tjänst utmatning**  till en **tåg modell**, kommer övnings experimentet att generera en ny modell som du kan använda.

Använd följande steg för att distribuera en retraining-webb tjänst:

1. Anslut en **webb tjänst inmatnings** modul till dina data inmatning. Normalt vill du se till att indatan bearbetas på samma sätt som dina ursprungliga tränings data.
1. Anslut en **webb tjänst utmatnings** modul till utdatan från din **träna-modell**.
1. Om du har en **modell för utvärderings modell** kan du ansluta en **webb tjänst utmatnings-** modul för att generera utvärderings resultaten
1. Kör experimentet.

    När du har kört experimentet bör det resulterande arbets flödet likna följande bild:

    ![Resulterande arbets flöde](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Nu ska du distribuera övnings experimentet som en utbildnings webb tjänst som utvärderar en utbildad modell och utvärderings resultat för modeller.

1. Längst ned i experiment arbets ytan klickar du på **Konfigurera webb tjänsten**
1. Välj **distribuera webb tjänst [ny]**. Azure Machine Learning Web Services-portalen öppnas på sidan **distribuera webb tjänst** .
1. Skriv ett namn för webb tjänsten och välj en betalnings plan.
1. Välj **Distribuera**.

## <a name="retrain-the-model"></a>Träna om modellen

I det här exemplet använder vi C# för att skapa retraining-programmet. Du kan också använda python-eller R-exempel koden för att utföra den här uppgiften.

Använd följande steg för att anropa API: erna för omträning:

1. Skapa ett C#-konsol program i Visual Studio: **nytt**  >  **projekt**  >  **Visual C#**  >  **Windows klassisk Desktop**  >  **console-app (.NET Framework)**.
1. Logga in på Machine Learning Web Services-portalen.
1. Klicka på den webb tjänst som du arbetar med.
1. Klicka på **förbruka**.
1. Klicka på **batch** i avsnittet **exempel kod** längst ned på sidan **förbrukare** .
1. Kopiera exempel koden C# för batch-körning och klistra in den i Program.cs-filen. Se till att namn området förblir intakt.

Lägg till NuGet-paketet Microsoft. ASPNET. WebApi. client enligt vad som anges i kommentarerna. Om du vill lägga till referensen i Microsoft.WindowsAzure.Storage.dll kan du behöva installera [klient biblioteket för Azure Storage-tjänster](https://www.nuget.org/packages/WindowsAzure.Storage).

Följande skärm bild visar sidan **förbruka** i Azure Machine Learning Web Services-portalen.

![Använd sida](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Uppdatera apiKey-deklarationen

Leta upp **apiKey** -deklarationen:

```csharp
const string apiKey = "abc123"; // Replace this with the API key for the web service
```

I avsnittet **grundläggande förbruknings information** på sidan **förbrukning** letar du upp den primära nyckeln och kopierar den till **apiKey** -deklarationen.

### <a name="update-the-azure-storage-information"></a>Uppdatera Azure Storage information

Exempel koden BES laddar upp en fil från en lokal enhet (till exempel "C:\temp\CensusInput.csv") för att Azure Storage, bearbetar den och skriver tillbaka resultatet till Azure Storage.

1. Logga in på Azure-portalen
1. Klicka på **fler tjänster** i den vänstra navigerings kolumnen, Sök efter **lagrings konton** och markera det.
1. I listan över lagrings konton väljer du ett för att lagra den omtränade modellen.
1. Klicka på **åtkomst nycklar** i den vänstra navigerings kolumnen.
1. Kopiera och spara den **primära åtkomst nyckeln**.
1. Klicka på **blobbar** i den vänstra navigerings kolumnen.
1. Välj en befintlig behållare eller skapa en ny och spara namnet.

Leta upp *StorageAccountName*-, *StorageAccountKey*-och *StorageContainerName* -deklarationerna och uppdatera värdena som du sparade från portalen.

```csharp
const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name
```

Du måste också se till att indatafilen är tillgänglig på den plats som du anger i koden.

### <a name="specify-the-output-location"></a>Ange platsen för utdata

När du anger platsen för utdata i nytto lasten för begäran måste fil namns tillägget som anges i *RelativeLocation* anges som `ilearner` .

```csharp
Outputs = new Dictionary<string, AzureBlobDataReference>() {
    {
        "output1",
        new AzureBlobDataReference()
        {
            ConnectionString = storageConnectionString,
            RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
        }
    },
```

Här är ett exempel på omskolning av utdata:

![Omträning av utdata](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Utvärdera resultatet av återinlärningen

När du kör programmet, innehåller utdata URL och signaturer för delad åtkomst som krävs för att få åtkomst till utvärderings resultatet.

Du kan se prestanda resultatet för den omtränade modellen genom att kombinera *BaseLocation*, *RelativeLocation* och *SasBlobToken* från resultatet av utdata för *output2* och klistra in den fullständiga URL-adressen i webbläsarens Adress fält.

Granska resultaten för att avgöra om den nyligen intränade modellen fungerar bättre än den befintliga.

Spara *BaseLocation*, *RelativeLocation* och *SasBlobToken* från resultatet av utdata.

## <a name="update-the-predictive-experiment"></a>Uppdatera förutsägande experiment

### <a name="sign-in-to-azure-resource-manager"></a>Logga in på Azure Resource Manager

Logga först in på ditt Azure-konto inifrån PowerShell-miljön med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

### <a name="get-the-web-service-definition-object"></a>Hämta definitions objekt för webb tjänst

Hämta sedan webb tjänst definitions objekt genom att anropa cmdleten [Get-AzMlWebService](/powershell/module/az.machinelearning/get-azmlwebservice) .

```azurepowershell
$wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

Du kan ta reda på resurs grupps namnet för en befintlig webb tjänst genom att köra cmdleten Get-AzMlWebService utan parametrar för att Visa webb tjänsterna i din prenumeration. Leta upp webb tjänsten och titta på webb tjänstens ID. Namnet på resurs gruppen är det fjärde elementet i ID: t, precis efter *resourceGroups* -elementet. I följande exempel är resurs gruppens namn standard-MachineLearning-Usasödracentrala.

```azurepowershell
Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
Name : RetrainSamplePre.2016.8.17.0.3.51.237
Location : South Central US
Type : Microsoft.MachineLearning/webServices
Tags : {}
```

Du kan också ta reda på resurs grupp namnet för en befintlig webb tjänst genom att logga in på Azure Machine Learning Web Services-portalen. Välj webb tjänsten. Resurs gruppens namn är det femte elementet i URL: en för webb tjänsten, precis efter *resourceGroups* -elementet. I följande exempel är resurs gruppens namn standard-MachineLearning-Usasödracentrala.

`https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237`

### <a name="export-the-web-service-definition-object-as-json"></a>Exportera webb tjänst definitions objekt som JSON

Om du vill ändra definitionen av den tränade modellen till att använda den nyligen utbildade modellen måste du först använda cmdleten [export-AzMlWebService](/powershell/module/az.machinelearning/export-azmlwebservice) för att exportera den till en JSON-format fil.

```azurepowershell
Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-reference-to-the-ilearner-blob"></a>Uppdatera referensen till ilearner-blobben

Leta upp [tränad modell] i till gångarna och uppdatera *URI* -värdet i *locationInfo* -noden med URI: n för ilearner-blobben. URI: n genereras genom att kombinera *BaseLocation* och *RelativeLocation* från utdata från anropet bes Retrain.

```json
"asset3": {
    "name": "Retrain Sample [trained model]",
    "type": "Resource",
    "locationInfo": {
        "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
    },
    "outputPorts": {
        "Results dataset": {
            "type": "Dataset"
        }
    }
},
```

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importera JSON till ett webb tjänst definitions objekt

Använd cmdleten [import-AzMlWebService](/powershell/module/az.machinelearning/import-azmlwebservice) för att konvertera den ändrade JSON-filen tillbaka till ett definitions objekt för webb tjänsten som du kan använda för att uppdatera predicative-experimentet.

```azurepowershell
$wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-web-service"></a>Uppdatera webb tjänsten

Använd slutligen cmdleten [Update-AzMlWebService](/powershell/module/az.machinelearning/update-azmlwebservice) för att uppdatera förutsägelse experimentet.

```azurepowershell
Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar webb tjänster eller håller reda på flera experiment körningar finns i följande artiklar:

* [Utforska webb tjänst portalen](manage-new-webservice.md)
* [Hantera experimentupprepningar](manage-experiment-iterations.md)