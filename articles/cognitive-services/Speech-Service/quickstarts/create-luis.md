---
title: 'Snabb start: skapa en LUIS-nyckel'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten får du lära dig hur du skapar ett LUIS-program och hämtar en nyckel.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: trbye
ms.openlocfilehash: 45c509ea2391c370aa0a399b2f0c621238fabc55
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209688"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Snabb start: Hämta en LUIS-slutpunkt-nyckel

## <a name="prerequisites"></a>Förutsättningar

Se till att du har följande saker innan du påbörjar den här självstudien:

* Ett LUIS-konto. Du kan skaffa ett kostnadsfritt via [LUIS-portalen](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>LUIS och tal

LUIS integreras med Speech-tjänsten för att identifiera avsikter från tal. Du behöver inte en Speech-tjänstprenumeration, bara LUIS.

LUIS använder tre typer av nycklar:

|Nyckeltyp|Syfte|
|--------|-------|
|Redigering|Gör att du kan skapa och ändra LUIS-appar program mässigt|
|Starter|Gör att du kan testa ditt LUIS-program med enbart text|
|Slutpunkt |Auktoriserar åtkomst till en viss LUIS-app|

För den här självstudien behöver du slut punkts nyckel typen. I självstudien används exempel programmet Home Automation LUIS, som du kan skapa genom att följa snabb starten för [välbyggda hem automatiserings program](../../luis/luis-get-started-create-app.md) . Om du har skapat en LUIS app som du själv har skapat kan du använda den i stället.

När du skapar en LUIS-app genererar LUIS automatiskt en start nyckel så att du kan testa appen med hjälp av text frågor. Den här nyckeln aktiverar inte röst tjänst integrering och fungerar inte med den här självstudien. Skapa en LUIS-resurs på Azure-instrumentpanelen och tilldela den till LUIS-appen. Du kan använda den kostnadsfria prenumerationsnivån för den här självstudiekursen.

När du har skapat LUIS-resursen på Azure-instrumentpanelen loggar du in på [Luis-portalen](https://www.luis.ai/home), väljer ditt program på sidan **Mina appar** och växlar sedan till appens **hanterings** sida. Slutligen väljer du **nycklar och slut punkter** i sido panelen.

![LUIS-portalens inställningar för nycklar och slutpunkter](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

På sidan **nycklar och inställningar för slut punkt** :

1. Rulla ned till avsnittet **resurser och nycklar** och välj **tilldela resurs**.
1. Gör följande ändringar i dialog rutan **tilldela en nyckel till din app** :

   * Under **klient** väljer du **Microsoft**.
   * Under **prenumerations namn** väljer du den Azure-prenumeration som innehåller den Luis-resurs som du vill använda.
   * Under **nyckel** väljer du den Luis-resurs som du vill använda med appen.

   Om en stund visas den nya prenumerationen i tabellen längst ned på sidan.

1. Välj ikonen bredvid en tangent för att kopiera den till Urklipp. (Du kan använda valfri nyckel.)

![Prenumerationsnycklar för LUIS-app](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera avsikter](~/articles/cognitive-services/Speech-Service/get-started-intent-recognition.md)