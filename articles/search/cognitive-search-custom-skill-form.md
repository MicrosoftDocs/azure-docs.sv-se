---
title: Formulär igenkänningens anpassade kompetens (C#)
titleSuffix: Azure Cognitive Search
description: Lär dig hur du skapar en anpassad kunskap för formulär igenkänning med C# och Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: a3b073cdb90e0c427bfbca15c1440b9122672610
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880143"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Exempel: skapa en anpassad färdighet för formulär igenkänning

I det här Azure Kognitiv sökning färdigheter-exemplet får du lära dig hur du skapar en anpassad kunskap för formulär igenkänning med C# och Visual Studio. Formulär tolken analyserar dokument och extraherar nyckel/värde-par och tabell data. Genom att använda igenkänning av formulär i det [anpassade kunskaps gränssnittet](cognitive-search-custom-skill-interface.md)kan du lägga till den här funktionen som ett steg i en pipeline för omfattande anrikning. Pipelinen kan sedan läsa in dokumenten och utföra andra transformeringar.

## <a name="prerequisites"></a>Förutsättningar

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (vilken utgåva som helst).
- Minst fem formulär av samma typ. Du kan använda exempel data som finns i den här guiden.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Träna modellen

Du måste träna en formulär igenkännings modell med dina ingångs formulär innan du använder den här kunskapen. Följ [snabb](../cognitive-services/form-recognizer/quickstarts/client-library.md?pivots=programming-language-rest-api) starten för att lära dig hur du tränar en modell. Du kan använda exempel formulären som tillhandahålls i snabb starten, eller så kan du använda dina egna data. När modellen har tränats kopierar du dess ID-värde till en säker plats.

## <a name="set-up-the-custom-skill"></a>Konfigurera den anpassade kompetensen

I den här självstudien används [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) -projektet i GitHub-lagringsplatsen [Azure Search Power skicklighet](https://github.com/Azure-Samples/azure-search-power-skills) . Klona den här lagrings platsen till din lokala dator och gå till **vision/AnalyzeForm/** för att få åtkomst till projektet. Öppna sedan _AnalyzeForm. CSPROJ_ i Visual Studio. Det här projektet skapar en Azure Function-resurs som uppfyller det [anpassade kunskaps gränssnittet](cognitive-search-custom-skill-interface.md) och kan användas för Azure kognitiv sökning-anrikning. Den tar formulär dokument som indata och matas in (som text) de nyckel/värde-par som du anger.

Lägg först till miljövariabler på projekt nivå. Leta upp projektet **AnalyzeForm** i det vänstra fönstret, högerklicka på det och välj **Egenskaper**. I fönstret **Egenskaper** klickar du på fliken **Felsök** och letar sedan upp fältet **miljövariabler** . Klicka på **Lägg** till för att lägga till följande variabler:
* `FORMS_RECOGNIZER_ENDPOINT_URL` med värdet inställt på slut punktens URL.
* `FORMS_RECOGNIZER_API_KEY` med värdet inställt på din prenumerations nyckel.
* `FORMS_RECOGNIZER_MODEL_ID` med värdet inställt på ID: t för den modell som du har tränat.
* `FORMS_RECOGNIZER_RETRY_DELAY` med värdet inställt på 1000. Det här värdet är den tid i millisekunder som programmet väntar innan den försöker köra frågan igen.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` med värdet inställt på 100. Det här värdet är antalet gånger som programmet kommer att fråga tjänsten vid försök att få ett lyckat svar.

Öppna sedan _AnalyzeForm.cs_ och hitta `fieldMappings` variabeln som refererar till *field-mappings.jspå* filen. Den här filen (och variabeln som refererar till den) definierar listan över nycklar som du vill extrahera från formulären och en anpassad etikett för varje nyckel. Ett värde på innebär till exempel att `{ "Address:", "address" }, { "Invoice For:", "recipient" }` skriptet bara sparar värdena för fälten identifierad och som `Address:` `Invoice For:` kommer att märka dessa värden med respektive `"address"` `"recipient"` .

Observera slutligen `contentType` variabeln. Det här skriptet kör den angivna formulär tolknings modellen på fjärrdokument som refereras till av webb adressen, så innehålls typen är `application/json` . Om du vill analysera lokala filer genom att inkludera sina byte-strömmar i HTTP-begärandena måste du ändra `contentType` till rätt [MIME-typ](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) för filen.

## <a name="test-the-function-from-visual-studio"></a>Testa funktionen från Visual Studio

När du har redigerat projektet sparar du det och anger **AnalyzeForm** -projektet som start projekt i Visual Studio (om det inte redan har angetts). Tryck sedan på **F5** för att köra funktionen i din lokala miljö. Använd en REST-tjänst som [Postman](https://www.postman.com/) för att anropa funktionen.

### <a name="http-request"></a>HTTP-begäran

Du ska göra följande begäran för att anropa funktionen.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Begärandetext

Börja med mallen för begär ande brödtext nedan.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Här måste du ange URL: en för ett formulär som har samma typ som de formulär som du har tränat med. I test syfte kan du använda något av dina utbildnings formulär. Om du har följt snabb starten för svängen kommer dina formulär att finnas i ett Azure Blob Storage-konto. Öppna Azure Storage Explorer, leta upp en formulär fil, högerklicka på den och välj **Hämta signatur för delad åtkomst**. Nästa dialog fönster kommer att tillhandahålla en URL och SAS-token. Ange de här strängarna `"formUrl"` i `"formSasToken"` fälten och i din begär ande brödtext.

> [!div class="mx-imgBorder"]
> ![Azure Storage Explorer; ett PDF-dokument har marker ATS](media/cognitive-search-skill-form/form-sas.png)

Om du vill analysera ett fjärrdokument som inte finns i Azure Blob Storage klistrar du in dess URL i `"formUrl"` fältet och lämnar `"formSasToken"` fältet tomt.

> [!NOTE]
> När kunskapen är integrerad i en färdigheter, kommer URL: en och token att tillhandahållas av Kognitiv sökning.

### <a name="response"></a>Svarsåtgärder

Du bör se ett svar som liknar följande exempel:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicera funktionen i Azure

När du är nöjd med funktions beteendet kan du publicera den.

1. I **Solution Explorer** i Visual Studio högerklickar du på projektet och väljer **publicera**. Välj **Skapa ny**  >  **publicera**.
1. Om du inte redan har anslutit Visual Studio till ditt Azure-konto väljer du **Lägg till ett konto....**
1. Följ anvisningarna på skärmen. Ange ett unikt namn för din app service, Azure-prenumerationen, resurs gruppen, värd planen och det lagrings konto som du vill använda. Du kan skapa en ny resurs grupp, en ny värd plan och ett nytt lagrings konto om du inte redan har dessa. När du är klar väljer du **skapa**.
1. När distributionen är klar, Lägg märke till webbplatsens URL. Den här URL: en är adressen till din Function-app i Azure. Spara den på en tillfällig plats.
1. I [Azure Portal](https://portal.azure.com)navigerar du till resurs gruppen och letar efter `AnalyzeForm` funktionen som du har publicerat. Under avsnittet **Hantera** bör du se värd nycklar. Kopiera *standard* värd nyckeln och spara den på en tillfällig plats.

## <a name="connect-to-your-pipeline"></a>Anslut till din pipeline

Om du vill använda den här kunskapen i en Kognitiv sökning pipeline måste du lägga till en kunskaps definition i din färdigheter. Följande JSON-block är ett exempel på en kunskaps definition (du bör uppdatera indata och utdata för att avspegla ditt specifika scenario och färdigheter-miljö). Ersätt `AzureFunctionEndpointUrl` med din funktions webb adress och Ersätt `AzureFunctionDefaultHostKey` med värd nyckeln.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

I den här hand boken har du skapat en anpassad kunskap från Azure formulär igenkännings tjänsten. Mer information om anpassade kunskaper finns i följande resurser. 

* [Azure Search energi kunskaper: ett lager med anpassade kunskaper](https://github.com/Azure-Samples/azure-search-power-skills)
* [Lägg till en anpassad färdighet till en AI-pipeline för anrikning](cognitive-search-custom-skill-interface.md)
* [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md)
* [Skapa en färdigheter (REST)](/rest/api/searchservice/create-skillset)
* [Mappa sammanrikade fält](cognitive-search-output-field-mapping.md)