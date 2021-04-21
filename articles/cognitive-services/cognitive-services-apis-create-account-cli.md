---
title: Skapa en Cognitive Services resurs med hjälp av Azure CLI
titleSuffix: Azure Cognitive Services
description: Kom igång med Azure Cognitive Services genom att skapa och prenumerera på en resurs med hjälp av Azure-kommandoradsgränssnittet.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: cognitive services, cognitive intelligence, cognitive solutions, ai services
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.openlocfilehash: 26e3b264b7268f7a9ffdb592beef7d76844646f5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789149"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Snabbstart: Skapa en Cognitive Services resurs med hjälp av Azure Command-Line Interface (CLI)

Använd den här snabbstarten för att komma igång Azure Cognitive Services med [hjälp av Azure-kommandoradsgränssnittet (CLI).](/cli/azure/install-azure-cli)

Azure Cognitive Services är molnbaserade tjänster med REST API:er och klientbiblioteks-API:er som är tillgängliga för att hjälpa utvecklare att bygga in kognitiv intelligens i program utan att ha direkt artificiell intelligens (AI) eller datavetenskapskunskaper. Azure Cognitive Services utvecklare enkelt kan lägga till kognitiva funktioner i sina program med kognitiva lösningar som kan se, höra, tala, förstå och till och med börja förstå.

Cognitive Services representeras av [Azure-resurser som](../azure-resource-manager/management/manage-resources-portal.md) du skapar i din Azure-prenumeration. När du har skapat resursen använder du de nycklar och slutpunkter som genereras för att autentisera dina program.

I den här snabbstarten lär du dig hur du registrerar dig för Azure Cognitive Services och skapar ett konto som har en prenumeration med en tjänst eller flera tjänster, med hjälp av [Azure-kommandoradsgränssnittet (CLI).](/cli/azure/install-azure-cli) Dessa tjänster representeras av [Azure-resurser,](../azure-resource-manager/management/manage-resources-portal.md)vilket gör att du kan ansluta till en eller flera av de Azure Cognitive Services API:erna.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Förutsättningar

* En giltig Azure-prenumeration [– Skapa en](https://azure.microsoft.com/free/cognitive-services) kostnadsfritt.
* [Azure-kommandoradsgränssnittet (CLI)](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Installera Azure CLI och logga in

Installera [Azure CLI.](/cli/azure/install-azure-cli) Logga in på din lokala installation av CLI genom att köra [kommandot az login:](/cli/azure/reference-index#az_login)

```azurecli-interactive
az login
```

Du kan också använda den gröna **knappen Prova** för att köra dessa kommandon i webbläsaren.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Skapa en ny Azure Cognitive Services resursgrupp

Innan du skapar Cognitive Services resurs måste du ha en Azure-resursgrupp som ska innehålla resursen. När du skapar en ny resurs kan du antingen skapa en ny resursgrupp eller använda en befintlig. Den här artikeln visar hur du skapar en ny resursgrupp.

### <a name="choose-your-resource-group-location"></a>Välj din resursgruppsplats

Om du vill skapa en resurs behöver du någon av de Azure-platser som är tillgängliga för din prenumeration. Du kan hämta en lista över tillgängliga platser med [kommandot az account list-locations.](/cli/azure/account#az_account_list_locations) De Cognitive Services kan nås från flera platser. Välj den som är närmast dig eller se vilka platser som är tillgängliga för tjänsten.

> [!IMPORTANT]
> * Kom ihåg din Azure-plats eftersom du behöver den när du anropar Azure Cognitive Services.
> * Tillgängligheten för vissa Cognitive Services kan variera beroende på region. Mer information finns i [Azure-produkter efter region.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

När du har din Azure-plats skapar du en ny resursgrupp i Azure CLI med kommandot [az group create.](/cli/azure/group#az_group_create)

I exemplet nedan ersätter du Azure-platsen `westus2` med någon av de Azure-platser som är tillgängliga för din prenumeration.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Skapa en -resurs för Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Välj en kognitiv tjänst och prisnivå

När du skapar en ny resurs måste du känna till vilken "typ" av tjänst du vill använda, tillsammans med [prisnivån](https://azure.microsoft.com/pricing/details/cognitive-services/) (eller SKU:n) som du vill använda. Du använder den här och annan information som parametrar när du skapar resursen.

### <a name="multi-service"></a>Flera tjänster

| Tjänst                    | Variant                      |
|----------------------------|---------------------------|
| Flera tjänster. Mer information [finns](https://azure.microsoft.com/pricing/details/cognitive-services/) på prissättningssidan.            | `CognitiveServices`     |


> [!NOTE]
> Många av Cognitive Services här nedan har en kostnadsfri nivå som du kan använda för att prova tjänsten. Om du vill använda den kostnadsfria nivån `F0` använder du som SKU för din resurs.

### <a name="vision"></a>Visuellt innehåll

| Tjänst                    | Variant                      |
|----------------------------|---------------------------|
| Visuellt innehåll            | `ComputerVision`          |
| Custom Vision – Förutsägelse | `CustomVision.Prediction` |
| Custom Vision – Utbildning   | `CustomVision.Training`   |
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
| Formförståelse | `FormUnderstanding` |
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

Du hittar en lista över tillgängliga Cognitive Service-"typer" med kommandot [az cognitiveservices account list-kinds:](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_kinds)

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Lägga till en ny resurs i resursgruppen

Om du vill skapa och prenumerera på Cognitive Services resurs använder du [kommandot az cognitiveservices account create.](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_create) Det här kommandot lägger till en ny fakturerbar resurs i resursgruppen som skapades tidigare. När du skapar din nya resurs måste du känna till vilken "typ" av tjänst du vill använda, tillsammans med dess prisnivå (eller SKU) och en Azure-plats:

Du kan skapa en F0-resurs (kostnadsfri) Avvikelseidentifiering med `anomaly-detector-resource` namnet med kommandot nedan.

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

## <a name="get-the-keys-for-your-resource"></a>Hämta nycklarna för din resurs

Logga in på din lokala installation av Command-Line Interface(CLI) med kommandot [az login.](/cli/azure/reference-index#az_login)

```azurecli-interactive
az login
```

Använd kommandot [az cognitiveservices account keys list för](/cli/azure/cognitiveservices/account/keys#az_cognitiveservices_account_keys_list) att hämta nycklarna för din Cognitive Service-resurs.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Prisnivåer och fakturering

Prisnivåer (och det belopp som du debiteras) baseras på antalet transaktioner som du skickar med hjälp av din autentiseringsinformation. Varje prisnivå anger:
* maximalt antal tillåtna transaktioner per sekund (TPS).
* tjänstfunktioner som är aktiverade på prisnivån.
* Kostnaden för ett fördefinierat antal transaktioner. Om du går över det här beloppet debiteras du extra enligt [prisinformationen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för din tjänst.

## <a name="get-current-quota-usage-for-your-resource"></a>Hämta aktuell kvotanvändning för din resurs

Använd kommandot [az cognitiveservices account list-usage](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_usage) för att hämta användningen för din Cognitive Service-resurs.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services resurs kan du ta bort den eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som ingår i gruppen bort.

Om du vill ta bort resursgruppen och dess associerade resurser använder du kommandot az group delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Se även

* Se **[Autentisera begäranden till Azure Cognitive Services](authentication.md)** information om hur du arbetar säkert med Cognitive Services.
* Se **[Vad är Azure Cognitive Services?](./what-are-cognitive-services.md)** för att hämta en lista över olika kategorier inom Cognitive Services.
* Se **[Stöd för naturligt](language-support.md)** språk för att se listan över naturliga språk som Cognitive Services stöder.
* Se **[Använda Cognitive Services som containrar](cognitive-services-container-support.md)** för att förstå hur du Cognitive Services på plats.
* Se **[Planera och hantera kostnader för Cognitive Services](plan-manage-costs.md)** att beräkna kostnaden för att använda Cognitive Services.
