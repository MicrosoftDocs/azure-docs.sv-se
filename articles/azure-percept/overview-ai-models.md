---
title: Azure percept AI-modeller
description: Lär dig mer om AI-modeller som är tillgängliga för prototyper och distribution
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: e31a696dfb443c20566f13f5a228eefac7af5ecc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595931"
---
# <a name="azure-percept-ai-models"></a>Azure percept AI-modeller

Med Azure percept kan du utveckla och distribuera AI-modeller direkt till din Azure percept DK från [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Modell distribution använder [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) och [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview).

## <a name="sample-ai-models"></a>Exempel på AI-modeller

Azure percept Studio innehåller exempel modeller för följande program:

- person identifiering
- fordons identifiering
- allmän objekt identifiering
- produkter-vid hylla identifiering

För förtränade modeller krävs ingen kod eller inlärnings data insamling. Distribuera bara din önskade modell till din Azure percept DK från portalen och öppna din devkits video ström för att se modell inferencing i praktiken. Inferencing telemetri för modell kan också nås via [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) -verktyget.

## <a name="reference-solutions"></a>Referenslösningar

Det finns också en [inventerings referens lösning för människor](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app) . Den här referens lösningen är ett AI-program med öppen källkod som ger gräns-baserade personer som räknar med användardefinierade zon ingångs-eller avslutnings händelser. Video-och AI-utdata från den lokala Edge-enheten utsätts till [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/), med användar gränssnittet som körs som en Azure-webbplats. AI-inferencing tillhandahålls av en AI-modell med öppen källkod för identifiering av personer.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="GIF-GIF med fördefinierad lösning för spatial analys":::

## <a name="custom-no-code-solutions"></a>Anpassade lösningar utan kod

Med Azure percept Studio kan du utveckla anpassade lösningar för [visioner](./tutorial-nocode-vision.md) och tal, utan att behöva koda.

För anpassade visioner-lösningar är både objekt identifiering och klassificering AI-modeller tillgängliga. Ladda bara upp och tagga dina utbildnings avbildningar, som kan hämtas direkt med Azure percept-visionen som i Azure percept DK, om så önskas. Modell inlärning och utvärdering utförs enkelt i [Custom vision](https://www.customvision.ai/), som är en del av [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview).

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

För anpassade tal lösningar är röst assistent mallar för närvarande tillgängliga för följande program:

- Service: hotell rummet utrustad med Voice-styrda smarta enheter.
- Sjukvård: vård utrustning som är utrustad med Voice-styrda smarta enheter.
- Inventering: Inventerad hubb med Voice-styrda smarta enheter.
- Bil: bil hubb som är utrustad med Voice-styrda smarta enheter.

Förskapade nyckelord och kommandon för röst assistenten är tillgängliga direkt via portalen. Anpassade nyckelord och kommandon kan skapas och tränas i [tal Studio](https://speech.microsoft.com/), som också ingår i Azure Cognitive Services.

## <a name="advanced-development"></a>Avancerad utveckling

Se [Azure percept Advanced Advanced Development GitHub](https://github.com/microsoft/azure-percept-advanced-development) för Uppdaterad vägledning, självstudier och exempel för saker som:

* Placera en anpassad AI-modell på enheten
* Uppdatering av en modell som vi redan har stöd för med överförings Inlärning
* och mycket mer.
