---
title: Använd förutsägelseslutpunkt för att programmatiskt testa bilder med klassificerare – Custom Vision
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder API:et för att programmatiskt testa bilder med din klassificerare för Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f1939536e033d2cf964dd2f4ee562e4ee20061b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934760"
---
# <a name="use-your-model-with-the-prediction-api"></a>Använd din modell med förutsägelse-API: et

När du har tränat din modell kan du testa bilderna program mässigt genom att skicka dem till förutsägelse-API-slutpunkten.

> [!NOTE]
> Det här dokumentet visar hur du använder C# för att skicka en bild till förutsägelse-API:et. Mer information och exempel finns i [förutsägelse API-referensen](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publicera din utbildade iteration

Från [Custom Vision-webbsidan](https://customvision.ai), markera projektet och välj sedan fliken __prestanda__.

Om du vill skicka avbildningar till förutsägelse-API: t måste du först publicera din iteration för förutsägelse, som du kan göra genom att välja __publicera__ och ange ett namn för den publicerade iterationen. Detta gör din modell tillgänglig för förutsägelse-API: t för din Custom Vision Azure-resurs.

![Fliken prestanda visas med en röd rektangel som omger publicerings knappen.](./media/use-prediction-api/unpublished-iteration.png)

När din modell har publicerats visas en "Publicerad" etikett bredvid iterationen i den vänstra sid panelen och dess namn visas i beskrivningen av iterationen.

![Fliken prestanda visas med en röd rektangel som omger den publicerade etiketten och namnet på den publicerade iterationen.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Hämta URL och förutsägelsenyckel

När din modell har publicerats kan du hämta den information som krävs genom att välja __förutsägelse-URL__. Då öppnas en dialog ruta med information om hur du använder förutsägelse-API, inklusive __förutsägelse-URL__ och __förutsägelse nyckel__.

![Fliken prestanda visas med en röd rektangel som omger URL-knappen för förutsägelse.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Fliken prestanda visas med en röd rektangel som omger värdet för förutsägelse-URL: en för att använda en avbildnings fil och värdet Prediction-Key.](./media/use-prediction-api/prediction-api-info.png)


I den här guiden ska du använda en lokal avbildning, så kopiera URL: en under **om du har en avbildnings fil** till en tillfällig plats. Kopiera även motsvarande __förutsägelse-nyckel__ värde.

## <a name="create-the-application"></a>Skapa programmet

1. Skapa ett nytt C#-konsol program i Visual Studio.

1. Använd följande kod som brödtext i filen __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Ändra följande information:
   * Ange `namespace` fältet till namnet på ditt projekt.
   * Ersätt plats hållaren `<Your prediction key>` med det nyckel värde som du hämtade tidigare.
   * Ersätt plats hållaren `<Your prediction URL>` med URL: en som du hämtade tidigare.

## <a name="run-the-application"></a>Kör programmet

När du kör programmet uppmanas du att ange en sökväg till en avbildnings fil i-konsolen. Avbildningen skickas sedan till förutsägelse-API: et och förutsägelse resultatet returneras som en JSON-formaterad sträng. Följande är ett exempel på ett svar.

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du skickar avbildningar till din anpassade avbildnings klassificering/detektor och får ett svar via programmering med C# SDK. Nu ska du lära dig hur du slutför scenarier från slut punkt till slut punkt med C# eller kom igång med ett annat språk-SDK.

* [Snabb start: Custom Vision SDK](quickstarts/image-classification.md)
