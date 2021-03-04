---
title: Skapa en Cognitive Services resurs i Azure Portal
titleSuffix: Azure Cognitive Services
description: Kom igång med Azure Cognitive Services genom att skapa och prenumerera på en resurs i Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
keywords: kognitiva tjänster, kognitiv intelligens, kognitiva lösningar, AI-tjänster
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 0ad2d0a10b20aa049147d61bd4e8168ea956392c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031930"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Snabb start: skapa en Cognitive Services resurs med hjälp av Azure Portal

Använd den här snabb starten för att börja använda Azure Cognitive Services. När du har skapat en kognitiv tjänst resurs i Azure Portal får du en slut punkt och en nyckel för att autentisera dina program.

Azure Cognitive Services är molnbaserade tjänster med REST API: er och SDK: er för klient bibliotek som är tillgängliga för att hjälpa utvecklare att bygga kognitiv information i program utan att ha direkt artificiell intelligens (AI) eller data vetenskaps kunskaper eller kunskap. Azure Cognitive Services gör det möjligt för utvecklare att enkelt lägga till kognitiva funktioner i sina program med kognitiva lösningar som kan se, höra, tala, förstå och till och med börja på skäl.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Förutsättningar

* En giltig Azure-prenumeration – [skapa en kostnads fri](https://azure.microsoft.com/free/cognitive-services/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Skapa en ny Azure Cognitive Services-resurs

1. skapa en resurs

    #### <a name="multi-service-resource"></a>[Resurs för flera tjänster](#tab/multiservice)

    Resursen för flera tjänster heter **Cognitive Services** i portalen. [Skapa en Cognitive Services-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

    För tillfället ger flera tjänst resurser åtkomst till följande Cognitive Services:

    - Visuellt innehåll
    - Content Moderator
    - Ansikte
    - Language Understanding (LUIS)
    - Textanalys
    - Översättare
    - Bing-sökning v7 <br>(Webb, bild, nyheter, video, visualisering)
    - Anpassad sökning i Bing
    - Entitetssökning i Bing
    - Automatiska förslag i Bing
    - Stavningskontroll i Bing

    #### <a name="single-service-resource"></a>[Resurs för enskild tjänst](#tab/singleservice)

    Använd länkarna nedan för att skapa en resurs för de tillgängliga Cognitive Services:

    | Visuellt innehåll                      | Tal                  | Språk                          | Beslut             | Sök                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Dator vision](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Tal tjänster](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Avancerad läsare](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Avvikelseidentifiering](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing-sökning API-v7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Custom vision service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Talarigenkänning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Anpassad sökning i Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Ansiktsigenkänning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personanpassning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Entitetssökning i Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Handskriftsigenkänning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Textanalys](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    | [Stavningskontroll i Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Översättare](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Automatiska förslag i Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |

    ***

3. Ange följande information på sidan **skapa** :

    #### <a name="multi-service-resource"></a>[Resurs för flera tjänster](#tab/multiservice)

    |    |    |
    |--|--|
    | **Namn** | Ett beskrivande namn för din resurs för kognitiva tjänster. Till exempel *MyCognitiveServicesResource*. |
    | **Prenumeration** | Välj en av dina tillgängliga Azure-prenumerationer. |
    | **Plats** | Platsen för din kognitiva tjänst instans. Olika platser kan orsaka svars tid, men har ingen inverkan på resursens tillgänglighet för körning. |
    | **Prisnivå** | Kostnaden för ditt Cognitive Services-konto beror på vilka alternativ du väljer och din användning. Mer information finns i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/)för API.
    | **Resursgrupp** | Den Azure-resurs grupp som ska innehålla din Cognitive Services-resurs. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

    ![Skärmen skapa resurs resurs för flera tjänster](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Klicka på **Skapa**.

    #### <a name="single-service-resource"></a>[Resurs för enskild tjänst](#tab/singleservice)

    |    |    |
    |--|--|
    | **Namn** | Ett beskrivande namn för din resurs för kognitiva tjänster. Till exempel *TextAnalyticsResource*. |
    | **Prenumeration** | Välj en av dina tillgängliga Azure-prenumerationer. |
    | **Plats** | Platsen för din kognitiva tjänst instans. Olika platser kan orsaka svars tid, men har ingen inverkan på resursens tillgänglighet för körning. |
    | **Prisnivå** | Kostnaden för ditt Cognitive Services-konto beror på vilka alternativ du väljer och din användning. Mer information finns i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/)för API.
    | **Resursgrupp** | Den Azure-resurs grupp som ska innehålla din Cognitive Services-resurs. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

    ![Skärm bild för att skapa resurs för enskild tjänst](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Klicka på **Skapa**.

    ***

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Hämta nycklar för din resurs

1. När resursen har distribuerats klickar **du på gå till resurs** under **Nästa steg**.

    ![Sök efter Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. Från snabb starts fönstret som öppnas kan du komma åt nyckeln och slut punkten.

    ![Hämta nyckel och slut punkt](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen tas även andra resurser som ingår i gruppen bort.

1. I Azure-portalen expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **Resursgrupper** för att visa listan över dina resursgrupper.
2. Leta upp resurs gruppen som innehåller resursen som ska tas bort
3. Högerklicka på listan över resurs grupper. Välj **Ta bort resursgrupp** och bekräfta.

## <a name="see-also"></a>Se även

* [Autentisera begär anden till Azure Cognitive Services](authentication.md)
* [Vad är Azure Cognitive Services?](./what-are-cognitive-services.md)
* [Skapa en ny resurs med hjälp av klient biblioteket för Azure-hantering](.\cognitive-services-apis-create-account-client-library.md)
* [Stöd för naturligt språk](language-support.md)
* [Stöd för Docker-behållare](cognitive-services-container-support.md)