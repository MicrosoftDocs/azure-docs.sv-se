---
title: Azure percept AI-modeller
description: Lär dig mer om AI-modeller som är tillgängliga för prototyper och distribution
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: 28a8de231f179cf69342da81e6a2ae1989d2a5d6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041594"
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

## <a name="pre-built-solutions"></a>Färdiga lösningar

En [förskapad lösning för avstånds analys för identifiering av personer](https://github.com/george-moore/Santa-Cruz-AI-App) är också tillgänglig. Den förskapade lösningen är ett AI-program med öppen källkod som ger gräns-baserade personer som räknar med användardefinierade zon ingångs-eller avslutnings händelser. Video-och AI-utdata från den lokala Edge-enheten utsätts till [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/), med användar gränssnittet som körs som en Azure-webbplats. AI-inferencing tillhandahålls av en AI-modell med öppen källkod för identifiering av personer.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="GIF-GIF med fördefinierad lösning för spatial analys":::

## <a name="custom-no-code-solutions"></a>Anpassade lösningar utan kod

Med Azure percept Studio kan du utveckla anpassade lösningar för [visioner](./tutorial-nocode-vision.md) och tal, utan att behöva koda.

För anpassade visioner-lösningar är både objekt identifiering och klassificering AI-modeller tillgängliga. Ladda bara upp och tagga dina utbildnings avbildningar, som kan hämtas direkt med Azure percept-visionen som i Azure percept DK, om så önskas. Modell inlärning och utvärdering utförs enkelt i [Custom vision](https://www.customvision.ai/), som är en del av [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview).

För anpassade tal lösningar är röst assistent mallar för närvarande tillgängliga för följande program:

- Service: hotell rummet utrustad med Voice-styrda smarta enheter.
- Sjukvård: vård utrustning som är utrustad med Voice-styrda smarta enheter.
- Inventering: Inventerad hubb med Voice-styrda smarta enheter.
- Bil: bil hubb som är utrustad med Voice-styrda smarta enheter.

Förskapade nyckelord och kommandon för röst assistenten är tillgängliga direkt via portalen. Anpassade nyckelord och kommandon kan skapas och tränas i [tal Studio](https://speech.microsoft.com/), som också ingår i Azure Cognitive Services.

## <a name="advanced-development"></a>Avancerad utveckling

För avancerade utvecklare utför den tillgängliga [Jupyter-anteckningsboken](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) överförings inlärning med en förtränad TensorFlow-modell (MobileNetSSDV2Lite) i python med en anpassad data uppsättning för objekt identifiering. Antecknings boken använder fjärrberäknings instanser via [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/#product-overview) och kan köras i molnet med hjälp av azureml-portalen eller lokalt i [Visual Studio Code](https://code.visualstudio.com/).

Här ingår också några användbara python- [skript](https://github.com/microsoft/Project-Santa-Cruz-Preview/tree/main/Sample-Scripts-and-Notebooks/Official/Scripts) för hantering av data uppsättningar och [installations program för dev tools](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/dev-tools-installer.md), som installerar och konfigurerar alla verktyg som krävs för att utveckla en avancerad AI-lösning.
