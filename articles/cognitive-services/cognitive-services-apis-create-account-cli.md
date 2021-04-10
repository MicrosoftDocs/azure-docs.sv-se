---
title: Skapa en Cognitive Services resurs med hjälp av Azure CLI
titleSuffix: Azure Cognitive Services
description: Kom igång med Azure Cognitive Services genom att skapa och prenumerera på en resurs med hjälp av kommando rads gränssnittet i Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: kognitiva tjänster, kognitiv intelligens, kognitiva lösningar, AI-tjänster
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.openlocfilehash: 08ff2f416a00002cde5767111ba5a6824a721324
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868179"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Snabb start: skapa en Cognitive Services resurs med hjälp av Azure Command-Line Interface (CLI)

Använd den här snabb starten för att komma igång med Azure Cognitive Services med hjälp av [kommando rads gränssnittet för Azure (CLI)](/cli/azure/install-azure-cli).

Azure Cognitive Services är Cloud-Base-tjänster med REST API: er och SDK: er för klient bibliotek som är tillgängliga för att hjälpa utvecklare att bygga kognitiv information i program utan att ha direkt artificiell intelligens (AI) eller data vetenskaps kunskaper eller kunskap. Azure Cognitive Services gör det möjligt för utvecklare att enkelt lägga till kognitiva funktioner i sina program med kognitiva lösningar som kan se, höra, tala, förstå och till och med börja på skäl.

Cognitive Services representeras av Azure- [resurser](../azure-resource-manager/management/manage-resources-portal.md) som du skapar i din Azure-prenumeration. När du har skapat resursen använder du nycklarna och slut punkten som du skapade för att autentisera dina program.

I den här snabb starten får du lära dig hur du registrerar dig för Azure Cognitive Services och skapar ett konto som har en tjänst prenumeration eller flera tjänster med hjälp av [kommando rads gränssnittet för Azure (CLI)](/cli/azure/install-azure-cli). Dessa tjänster representeras av Azure- [resurser](../azure-resource-manager/management/manage-resources-portal.md), vilket gör att du kan ansluta till en eller flera av azure-API:er för Cognitive Services.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Förutsättningar

* En giltig Azure-prenumeration – [skapa en](https://azure.microsoft.com/free/cognitive-services) kostnads fri.
* [Kommando rads gränssnittet för Azure (CLI)](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Installera Azure CLI och logga in

Installera [Azure CLI](/cli/azure/install-azure-cli). Logga in på den lokala installationen av CLI genom att köra kommandot [AZ login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

Du kan också använda det gröna **try** -knappen för att köra kommandona i webbläsaren.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Skapa en ny resurs grupp för Azure Cognitive Services

Innan du skapar en Cognitive Services resurs måste du ha en Azure-resurs grupp som innehåller resursen. När du skapar en ny resurs har du möjlighet att antingen skapa en ny resurs grupp eller använda en befintlig. Den här artikeln visar hur du skapar en ny resurs grupp.

### <a name="choose-your-resource-group-location"></a>Välj plats för resurs grupp

Om du vill skapa en resurs behöver du en av de Azure-platser som är tillgängliga för din prenumeration. Du kan hämta en lista över tillgängliga platser med kommandot [AZ Account List-locations](/cli/azure/account#az-account-list-locations) . De flesta Cognitive Services kan nås från flera platser. Välj det som är närmast dig eller se vilka platser som är tillgängliga för tjänsten.

> [!IMPORTANT]
> * Kom ihåg Azure-platsen, eftersom du behöver den när du anropar Azure-Cognitive Services.
> * Tillgängligheten för vissa Cognitive Services kan variera beroende på region. Mer information finns i [Azure products by region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

När du har en Azure-plats skapar du en ny resurs grupp i Azure CLI med kommandot [AZ Group Create](/cli/azure/group#az-group-create) .

I exemplet nedan ersätter du Azure-platsen `westus2` med en av de Azure-platser som är tillgängliga för din prenumeration.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Skapa en -resurs för Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Välj en kognitiv tjänst och pris nivå

När du skapar en ny resurs behöver du veta vilken typ av tjänst du vill använda, tillsammans med den [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/) (eller SKU) som du vill använda. Du kommer att använda den här och andra information som parametrar när du skapar resursen.

### <a name="multi-service"></a>Multi-service

| Tjänst                    | Variant                      |
|----------------------------|---------------------------|
| Flera tjänster. Mer information finns på sidan med [pris](https://azure.microsoft.com/pricing/details/cognitive-services/) information.            | `CognitiveServices`     |


> [!NOTE]
> Många av de Cognitive Servicesna nedan har en kostnads fri nivå som du kan använda för att testa tjänsten. Använd den kostnads fria nivån `F0` som SKU för din resurs.

### <a name="vision"></a>Visuellt innehåll

| Tjänst                    | Variant                      |
|----------------------------|---------------------------|
| Visuellt innehåll            | `ComputerVision`          |
| Custom Vision förutsägelse | `CustomVision.Prediction` |
| Custom Vision-utbildning   | `CustomVision.Training`   |
| Ansikte                       | `Face`                    |
| Formigenkänning            | `FormRecognizer`          |
| Handskriftsigenkänning             | `InkRecognizer`           |

### <a name="speech"></a>Speech

| Tjänst            | Variant                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| Taligenkänning | `SpeakerRecognition` |

### <a name="language"></a>Språk

| Tjänst            | Variant                |
|--------------------|---------------------|
| Formulär förståelse | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Textanalys     | `TextAnalytics`     |
| Textöversättning   | `TextTranslation`   |

### <a name="decision"></a>Beslut

| Tjänst           | Variant               |
|-------------------|--------------------|
| Avvikelseidentifiering  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personanpassning      | `Personalizer`     |

Du hittar en lista över tillgängliga kognitiva tjänst typer med kommandot [AZ cognitiveservices Account List-typ](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Lägg till en ny resurs i resurs gruppen

Om du vill skapa och prenumerera på en ny Cognitive Services-resurs använder du kommandot [AZ cognitiveservices Account Create](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-create) . Detta kommando lägger till en ny fakturerbar resurs till resurs gruppen som skapades tidigare. När du skapar din nya resurs behöver du veta vilken typ av tjänst du vill använda, tillsammans med dess pris nivå (eller SKU) och en Azure-plats:

Du kan skapa en F0 (kostnads fri) resurs för avvikelse detektor, med namnet `anomaly-detector-resource` med kommandot nedan.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Hämta nycklar för din resurs

Använd kommandot [AZ login](/cli/azure/reference-index#az-login) för att logga in på den lokala installationen av Command-Line Interface (CLI).

```azurecli-interactive
az login
```

Använd kommandot [AZ cognitiveservices Account Keys List](/cli/azure/cognitiveservices/account/keys#az-cognitiveservices-account-keys-list) för att hämta nycklarna för din kognitiva tjänst resurs.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Pris nivåer och fakturering

Pris nivåerna (och den mängd du debiteras) baseras på antalet transaktioner som du skickar med hjälp av autentiseringsinformationen. Varje pris nivå anger:
* maximalt antal tillåtna transaktioner per sekund (TPS).
* tjänst funktioner som Aktiver ATS inom pris nivån.
* Kostnaden för ett fördefinierat antal transaktioner. Om du fortsätter över den här mängden kommer du att få en extra avgift som anges i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för din tjänst.

## <a name="get-current-quota-usage-for-your-resource"></a>Hämta aktuell kvot användning för din resurs

Använd kommandot [AZ cognitiveservices Account List-Usage](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-usage) för att få användningen av din kognitiva tjänst resurs.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services resurs kan du ta bort den eller resurs gruppen. Om du tar bort resurs gruppen tas även andra resurser som ingår i gruppen bort.

Om du vill ta bort resurs gruppen och dess associerade resurser använder du kommandot AZ Group Delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Se även

* Se **[autentisera begär anden till Azure Cognitive Services](authentication.md)** om hur du säkert arbetar med Cognitive Services.
* Se **[Vad är Azure Cognitive Services?](./what-are-cognitive-services.md)** för att hämta en lista över olika kategorier i Cognitive Services.
* Se den **[naturliga språk supporten](language-support.md)** om du vill se en lista över naturliga språk som Cognitive Services stöder.
* Se **[använda Cognitive Services som behållare](cognitive-services-container-support.md)** för att förstå hur du använder Cognitive Services lokal.
* Se **[planera och hantera kostnader för Cognitive Services](plan-manage-costs.md)** för att beräkna kostnaden för att använda Cognitive Services.
