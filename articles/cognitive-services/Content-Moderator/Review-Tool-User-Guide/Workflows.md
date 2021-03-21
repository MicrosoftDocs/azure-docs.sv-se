---
title: Definiera och Använd innehålls arbets flöden via gransknings verktyget – Content Moderator
titleSuffix: Azure Cognitive Services
description: Du kan använda Azure Content Moderator Workflow Designer för att definiera anpassade arbets flöden och tröskelvärden baserat på dina innehålls principer.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 6eb2a2d2762b60a12bb9a24b92e2edae4b846cd1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904150"
---
# <a name="define-and-use-moderation-workflows"></a>Definiera och använda moderator arbets flöden

I den här guiden får du lära dig hur du konfigurerar och använder [arbets flöden](../review-api.md#workflows) på webbplatsen [Granska verktyg](https://contentmoderator.cognitive.microsoft.com) . Arbets flöden är molnbaserade anpassade filter som du kan använda för att hantera innehåll mer effektivt. Arbets flöden kan ansluta till en mängd olika tjänster för att filtrera innehåll på olika sätt och vidta lämplig åtgärd. Den här guiden visar hur du använder Content Moderator-anslutningen (som ingår som standard) för att filtrera innehåll och ställa in mänsklig granskning i ett typiskt redigerings scenario.

## <a name="create-a-new-workflow"></a>Skapa ett nytt arbets flöde

Gå till [verktyget för Content moderator granskning](https://contentmoderator.cognitive.microsoft.com/) och logga in. På fliken **Inställningar** väljer du **arbets flöden**.

![Inställning av arbets flöden](images/2-workflows-0.png)

På nästa skärm väljer du **Lägg till arbets flöde**.

![Lägg till ett arbets flöde](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Tilldela ett namn och en beskrivning

Namnge ditt arbets flöde, ange en beskrivning och välj om arbets flödet ska hantera bilder eller text.

![Arbets flödes namn och beskrivning](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definiera bedömnings kriterier

På nästa skärm går du till **IF** -avsnittet. Välj **villkor** i den översta List menyn. På så sätt kan du konfigurera villkoret som arbets flödet ska vidta åtgärder för. Om du vill använda flera villkor väljer du **kombination** i stället. 

Välj sedan en koppling. I det här exemplet används **Content moderator**. Beroende på vilken anslutning du väljer får du olika alternativ för data utdata. Mer information om hur du konfigurerar andra anslutningar finns i avsnittet [anslutningar](./configure.md#connectors) i guiden granska verktyg inställningar.

![Välj arbets flödes koppling](images/image-workflow-connect-to.PNG)

Välj önskade utdata och ange villkoren för att kontrol lera den mot.

![Definiera arbets flödes villkor](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definiera åtgärden

Gå till avsnittet **och** där du väljer en åtgärd. I följande exempel skapas en bild granskning och tilldelar en tagg. Du kan också lägga till en alternativ sökväg (Else) och ange en åtgärd för det.

![Definiera arbets flödes åtgärd](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Spara arbets flödet

Anteckna namnet på arbets flödet; du behöver namnet för att starta ett redigerings jobb med arbets flödes-API: et (se nedan). Spara slutligen arbets flödet med knappen **Spara** överst på sidan.

## <a name="test-the-workflow"></a>Testa arbetsflödet

Nu när du har definierat ett anpassat arbets flöde testar du det med exempel innehåll. Gå till **arbets flöden** och välj motsvarande **Kör arbets flödes** knapp.

![Arbets flödes test](images/image-workflow-execute.PNG)

Spara den här [exempel bilden](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) på den lokala enheten. Välj sedan **Välj fil (er)** och överför avbildningen till arbets flödet.

![En löpare med ett citat ovanpå bilden](images/sample-text.jpg)

### <a name="track-progress"></a>Spåra förlopp

Du kan visa förloppet för arbets flödet i nästa popup-fönster.

![Spåra arbets flödes körning](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Bekräfta arbets flödes åtgärd

Gå till fliken **avbildning** under **Granska** och kontrol lera att det finns en nyligen skapad bild granskning.

![Granska bilder](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du konfigurerar och använder arbets flöden för redaktörer från [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com)för Content moderator. Sedan läser du [API-konsolens guide](../try-review-api-workflow.md) för att lära dig hur du skapar arbets flöden program mässigt.
