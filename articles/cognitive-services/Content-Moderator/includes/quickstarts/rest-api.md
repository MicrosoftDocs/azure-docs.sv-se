---
title: Snabb start för Content Moderator REST API
titleSuffix: Azure Cognitive Services
description: I den här snabb starten lär du dig att komma igång med Azure Content Moderator REST API. Bygg program vara för innehålls filtrering i din app för att följa regler eller underhålla användarnas avsedda miljö.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: 859c57fb6caeee730d5ba937bacf5d29a25ca173
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510898"
---
Kom igång med Azure Content Moderator-REST API. 

Content Moderator är en AI-tjänst som låter dig hantera innehåll som är potentiellt stötande, riskfylld eller på annat sätt olämpligt. Använd den AI-drivna Content moderatoring-tjänsten för att söka igenom text, bild och videor och tillämpa innehålls flaggor automatiskt. Integrera sedan din app med gransknings verktyget, en online moderator miljö för ett team av mänskliga granskare. Bygg program vara för innehålls filtrering i din app för att följa regler eller underhålla användarnas avsedda miljö.

Använd Content Moderator REST API för att:

* Måttlig text
* Måttliga bilder

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" skapar du en Content moderator resurs "  target="_blank"> skapa en Content moderator resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Content Moderator. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* [PowerShell version 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)eller liknande kommando rads program.


## <a name="moderate-text"></a>Måttlig text

Du använder ett kommando som följande för att anropa Content Moderator-API: et för att analysera text texten och skriva ut resultatet i-konsolen.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

Kopiera kommandot till en text redigerare och gör följande ändringar:

1. Tilldela `Ocp-Apim-Subscription-Key` din giltiga prenumerations nyckel för ansikts.
1. Ändra den första delen av fråge-URL: en så att den matchar slut punkten som motsvarar din prenumerations nyckel.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Du kan också ändra bröd texten i begäran till den text sträng som du vill analysera.

När du har gjort dina ändringar öppnar du en kommandotolk och anger det nya kommandot. 

### <a name="examine-the-results"></a>Granska resultaten

Du bör se de text redigerings resultat som visas som JSON-data i konsol fönstret. Exempel:

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

Mer information om textattributen som Content Moderator skärmar för finns i begrepps hand boken för [text redigering](../../text-moderation-api.md) .

## <a name="moderate-images"></a>Måttliga bilder

Du använder ett kommando som följande för att anropa Content Moderator-API: et för att använda en fjärravbildning och skriva ut resultatet till-konsolen.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

Kopiera kommandot till en text redigerare och gör följande ändringar:

1. Tilldela `Ocp-Apim-Subscription-Key` din giltiga prenumerations nyckel för ansikts.
1. Ändra den första delen av fråge-URL: en så att den matchar slut punkten som motsvarar din prenumerations nyckel.
1. Alternativt kan du ändra `"Value"` URL: en i begär ande texten till vilken fjärravbildning som du vill måttlig.

> [!TIP]
> Du kan också måttligt lokala bilder genom att skicka sina byte-data till begär ande texten. Mer information om hur du gör detta finns i [referens dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) .

När du har gjort dina ändringar öppnar du en kommandotolk och anger det nya kommandot. 

### <a name="examine-the-results"></a>Granska resultaten

Du bör se de bild moderator resultat som visas som JSON-data i konsol fönstret. 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

Mer information om bildattributen som Content Moderator skärmar för finns i hand boken för [bild moderator](../../image-moderation-api.md) .

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder Content Moderator REST API för att göra redigerings uppgifter. Läs sedan mer om hur du använder avbildningen eller andra media genom att läsa en konceptuell guide.

* [Bild moderator koncept](../../image-moderation-api.md)
* [Text moderator koncept](../../text-moderation-api.md)
* [Vad är Azure Content Moderator?](../../overview.md)