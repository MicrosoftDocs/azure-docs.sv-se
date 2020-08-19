---
title: Visa system test resultat och distribution – anpassad översättare
titleSuffix: Azure Cognitive Services
description: När din utbildning är klar kan du granska system testerna för att analysera dina utbildnings resultat. Om du är nöjd med utbildnings resultatet ska du placera en distributions förfrågan för den tränade modellen.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 04e21e4bcd56506ba27730ebc1f9b0db8dc39615
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509693"
---
# <a name="view-system-test-results"></a>Visa testresultat för system

När din utbildning är klar kan du granska system testerna för att analysera dina utbildnings resultat. Om du är nöjd med utbildnings resultatet ska du placera en distributions förfrågan för den tränade modellen.

## <a name="system-test-results-page"></a>Sidan system test resultat

Välj ett projekt, Välj fliken modeller i projektet, leta upp den modell som du vill använda och välj slutligen fliken test.

På fliken test visas:

1.  **System testresultat:** Resultatet av test processen i träningarna. Test processen genererar BLEU-poängen.

    **Antal meningar:** Hur många parallella meningar som användes i test uppsättningen.

     **Bleu Poäng:** BLEU Poäng som genererats för en modell efter att utbildningen har slutförts.

    **Status:** Anger om test processen är slutförd eller pågår.

    ![System test resultat](media/how-to/how-to-system-test-results.png)

2.  Klicka på system test resultatet så kommer du till sidan testa resultat information. Den här sidan visar dator översättning av meningar som ingick i test data uppsättningen.

3.  Tabellen på sidan med test resultat information har två kolumner – en för varje språk i paret. Kolumnen för käll språket visar meningen som ska översättas. Kolumnen för mål språket innehåller två meningar i varje rad.

    **Ref:** Den här meningen är referens översättningen av käll meningen som anges i test data uppsättningen.

    **MT:** Den här meningen är den automatiska översättningen av käll meningen som görs av modellen som skapats efter att utbildningen genomförts.

    ![Jämför med system test resultat](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Hämta test

Klicka på länken Hämta översättningar för att ladda ned en zip-fil. Zip-filen innehåller dator översättningar av käll meningar i data uppsättningen test.

![Hämta test](media/how-to/how-to-system-test-download.png)

Det hämtade ZIP-arkivet innehåller tre filer.

1.  **custom.mt.txt:** Den här filen innehåller dator översättningar av käll språk meningar på mål språket som sköts av modellen som tränas med användarens data.

2.  **ref.txt:** Den här filen innehåller användare som har angett översättningar av käll språkets meningar på mål språket.

3.  **source.txt:** Den här filen innehåller meningar i käll språket.

    ![Hämtade system test resultat](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Distribuera en modell

Så här begär du en distribution:

1.  Välj ett projekt, gå till fliken modeller.

2. För en lyckad tränad modell visas knappen "distribuera", om den inte distribueras.

    ![Distribuera modell](media/how-to/how-to-deploy-model.png)

3.  Klicka på distribuera.
4.  Välj **distribuerad** för de regioner där du vill att din modell ska distribueras och klicka sedan på Spara. Du kan välja **distribuerat** i flera regioner.

    ![Distribuera modell](media/how-to/how-to-deploy-model-regions.png)

5.  Du kan visa status för din modell i kolumnen "status".

>[!Note]
>Anpassad översättare stöder 10 distribuerade modeller inom en arbets yta vid varje tidpunkt.

## <a name="update-deployment-settings"></a>Uppdatera distributions inställningar

Så här uppdaterar du distributions inställningarna:

1.  Välj ett projekt och gå till fliken **modeller** .

2. För en lyckad distribuerad modell visas en **uppdaterings** knapp.

    ![Distribuera modell](media/how-to/how-to-update-undeploy-model.png)

3.  Välj **Uppdatera**.
4.  Välj **distribuerad** eller ej **distribuerad** för den eller de regioner där du vill att din modell ska distribueras eller avinstalleras och klicka sedan på **Spara**.

    ![Distribuera modell](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Om du väljer **distribuerat** i alla regioner, är modellen inte distribuerad från alla regioner och försätts i ett tillstånd där det inte har distribuerats. Den är nu inte tillgänglig för användning.

## <a name="next-steps"></a>Nästa steg

- Börja använda din distribuerade anpassade översättnings modell via [Microsoft Translator text API v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Lär dig [hur du hanterar inställningar](how-to-manage-settings.md) för att dela din arbets yta, hantera prenumerations nyckel.
- Lär dig [hur du migrerar din arbets yta och ditt projekt](how-to-migrate.md) från [Microsoft Translator Hub](https://hub.microsofttranslator.com)
