---
title: 'Så här uppdaterar du ditt projekt till 3,0-API: et'
titleSuffix: Azure Cognitive Services
description: 'Lär dig hur du uppdaterar Custom Vision-projekt från den tidigare versionen av API: et till 3,0-API: et.'
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "73647507"
---
# <a name="update-to-the-30-api"></a>Uppdatera till 3,0-API: et

Custom Vision har nu nått allmän tillgänglighet och har genomgått en API-uppdatering.
Den här uppdateringen innehåller några nya funktioner och några viktiga ändringar:

* Förutsägelse-API: t delas nu upp i två baserat på projekt typen.
* Export alternativet vision AI Developer Kit (VAIDK) kräver att du skapar ett projekt på ett särskilt sätt.
* Standard iterationer har tagits bort i stället för att publicera/avpublicera en namngiven iteration.

I den här guiden visas hur du uppdaterar dina projekt så att de fungerar med den nya API-versionen. I [viktig information](release-notes.md) finns en fullständig lista över ändringarna.

## <a name="use-the-updated-prediction-api"></a>Använda det uppdaterade förutsägelse-API: et

API: erna för 2. x använder samma förutsägelse anrop för både bild-och objekt detektor projekt. Båda projekt typerna är acceptabla för **PredictImage** -och **PredictImageUrl** -anropen. Från och med 3,0 har vi delat upp detta API så att du måste matcha projekt typen till anropet:

* Använd **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** och **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** för att hämta förutsägelser för bild klassificerings projekt.
* Använd **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** och **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** för att hämta förutsägelser för objekt identifierings projekt.

## <a name="use-the-new-iteration-publishing-workflow"></a>Använd det nya upprepnings arbets flödet

2. x-API: erna använder standard upprepning eller ett angivet upprepnings-ID för att välja den iteration som ska användas för förutsägelse. Från och med 3,0 har vi infört ett publicerings flöde där du först publicerar en iteration under ett angivet namn från utbildnings-API: et. Sedan skickar du namnet till förutsägelse metoderna för att ange vilken iteration som ska användas.

> [!IMPORTANT]
> API: erna 3,0 använder inte standard funktionen iteration. Tills vi tar bort de äldre API: erna kan du fortsätta att använda API: erna 2. x för att växla en iteration som standard. Dessa API: er kommer att behållas under en viss tids period och du kan anropa metoden **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** för att markera en iteration som standard.

### <a name="publish-an-iteration"></a>Publicera en iteration

När en iteration har tränats kan du göra den tillgänglig för förutsägelse med **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** -metoden. Om du vill publicera en iteration behöver du förutsäga resurs-ID: t, som finns på CustomVision webbplats inställnings sida.

![Sidan Custom Vision webbplats inställningar med resurs-ID för förutsägelsen som beskrivs.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Du kan också hämta den här informationen från [Azure Portal](https://portal.azure.com) genom att gå till Custom vision förutsägelse resurs och välja **Egenskaper**.

När iterationen har publicerats kan appar använda den för förutsägelse genom att ange namnet i API-anropet för förutsägelse. Om du vill göra en iteration otillgänglig för förutsägelse samtal använder du **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** -API: et.

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om utbildnings-API-referens (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Dokumentation om förutsägelse-API-referens (REST)](https://go.microsoft.com/fwlink/?linkid=865445)