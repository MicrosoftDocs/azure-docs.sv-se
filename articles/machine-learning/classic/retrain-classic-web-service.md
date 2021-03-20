---
title: 'ML Studio (klassisk): återträna klassisk webb tjänst – Azure'
description: Lär dig hur du omtränar en modell och uppdaterar en klassisk webb tjänst för att använda den nyligen utbildade modellen i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: 90c968ee953e80238775639964cb09a25741b33d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517577"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Omträna och distribuera en klassisk Studio (klassisk) webb tjänst

**gäller för:** ![ Grön bock markering. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassiskt) ![ X som anger Nej.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Retraining Machine Learning-modeller är ett sätt att se till att de håller sig noggrann och utifrån de mest relevanta data som finns tillgängliga. I den här artikeln visas hur du återskapar en klassisk Studio (klassisk) webb tjänst. En guide om hur du återskapar en ny Studio-webbtjänst (klassisk) finns i [den här instruktions artikeln.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har både ett omskolnings experiment och ett förutsägelse experiment. De här stegen beskrivs i [omträna och distribuera en maskin inlärnings modell.](./retrain-machine-learning-model.md) Men i stället för att distribuera din Machine Learning-modell som en ny webb tjänst, kommer du att distribuera ditt förutsägelse experiment som en klassisk webb tjänst.
     
## <a name="add-a-new-endpoint"></a>Lägg till en ny slut punkt

Den förutsägbara webb tjänsten som du har distribuerat innehåller en standard slut punkt för poäng som är synkroniserad med den ursprungliga inlärnings-och bedömnings experimentets tränade modell. Om du vill uppdatera din webb tjänst till med en ny utbildad modell måste du skapa en ny bedömnings slut punkt.

Du kan lägga till en ny slut punkt i en webb tjänst på två sätt:

* Programmässigt
* Använda Azure Web Services-portalen

> [!NOTE]
> Se till att du lägger till slut punkten till den förutsägbara webb tjänsten, inte utbildnings webb tjänsten. Om du har distribuerat både en utbildning och en förutsägbar webb tjänst, bör du se två separata webb tjänster i listan. Den förutsägbara webb tjänsten måste sluta med "[förutsägande exp.]".
>

### <a name="programmatically-add-an-endpoint"></a>Lägg till en slut punkt program mässigt

Du kan lägga till bedömnings slut punkter med hjälp av exempel koden som anges i den här [GitHub-lagringsplatsen](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Använd Azure Web Services-portalen för att lägga till en slut punkt

1. I Machine Learning Studio (klassisk) i den vänstra navigerings kolumnen klickar du på webb tjänster.
1. Klicka på **Hantera slut punkter för förhands granskning** längst ned på instrument panelen för webb tjänster.
1. Klicka på **Lägg till**.
1. Ange ett namn och en beskrivning för den nya slut punkten. Välj loggnings nivå och om exempel data är aktiverade. Mer information om loggning finns i [Aktivera loggning för Machine Learning-webbtjänster](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Uppdatera den utbildade modell som lagts till för slut punkten

### <a name="retrieve-patch-url"></a>Hämta KORRIGERINGs webb adress

Följ dessa steg för att hämta rätt KORRIGERINGs-URL med hjälp av webb portalen:

1. Logga in på [Azure Machine Learning Web Services-](https://services.azureml.net/) portalen.
1. Klicka på **webb tjänster** eller **klassiska webb tjänster** längst upp.
1. Klicka på den bedömnings webb tjänst som du arbetar med (om du inte har ändrat standard namnet på webb tjänsten slutar den i "[bedömnings exp.]").
1. Klicka på **+ ny**.
1. När du har lagt till slut punkten klickar du på slut punktens namn.
1. Klicka på **API-hjälp** under **korrigerings** webb adressen för att öppna hjälp sidan för korrigerings filen.

> [!NOTE]
> Om du har lagt till slut punkten för webb tjänsten utbildning i stället för den förutsägbara webb tjänsten, får du följande fel meddelande när du klickar på länken **Uppdatera resurs** : "Tyvärr, men den här funktionen stöds inte eller är inte tillgänglig i den här kontexten. Den här webb tjänsten har inga uppdaterings bara resurser. Vi beklagar besväret och arbetar med att förbättra det här arbets flödet. "
>

Hjälp sidan för KORRIGERINGs filen innehåller KORRIGERINGs-URL: en som du måste använda och innehåller exempel kod som du kan använda för att anropa den.

![Korrigerings webb adress.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Uppdatera slut punkten

Nu kan du använda den tränade modellen för att uppdatera poäng slut punkten som du skapade tidigare.

Följande exempel kod visar hur du använder URL: en för *BaseLocation*, *RelativeLocation*, *SasBlobToken* och patch för att uppdatera slut punkten.

```csharp
private async Task OverwriteModel()
{
    var resourceLocations = new
    {
        Resources = new[]
        {
            new
            {
                Name = "Census Model [trained model]",
                Location = new AzureBlobDataReference()
                {
                    BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                    RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                    SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
                }
            }
        }
    };

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

        using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
        {
            request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
            HttpResponseMessage response = await client.SendAsync(request);

            if (!response.IsSuccessStatusCode)
            {
                await WriteFailedResponse(response);
            }

            // Do what you want with a successful response here.
        }
    }
}
```

*ApiKey* och *endpointUrl* för anropet kan hämtas från slut punktens instrument panel.

Värdet för *namn* parametern i *resurser* måste matcha resurs namnet för den sparade tränade modellen i förutsägande experimentet. Så här hämtar du resurs namnet:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Machine Learning** på den vänstra menyn.
1. Under namn klickar du på din arbets yta och sedan på **webb tjänster**.
1. Under namn klickar du på **inventerings modell [förutsägande exp.]**.
1. Klicka på den nya slut punkten som du har lagt till.
1. Klicka på **Uppdatera resurs** på instrument panelen för slut punkten.
1. På sidan för uppdatering av resurs-API-dokumentation för-webb tjänsten kan du hitta **resurs namnet** under **uppdaterings bara resurser**.

Om din SAS-token upphör att gälla innan du Slutför uppdateringen av slut punkten måste du utföra en GET med jobb-ID: t för att hämta en ny token.

När koden har körts, ska den nya slut punkten börja använda den omtränade modellen på cirka 30 sekunder.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar webb tjänster eller håller reda på flera experiment körningar finns i följande artiklar:

* [Utforska webb tjänst portalen](manage-new-webservice.md)
* [Hantera experimentupprepningar](manage-experiment-iterations.md)