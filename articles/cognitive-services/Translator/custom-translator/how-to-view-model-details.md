---
title: Visa modell information – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Fliken modeller under ett projekt visar information om varje modell, till exempel modell namn, modell status, BLEU poäng, utbildning, justering, antal test meningar.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 52e305f9d50ab0f383810778be681ac41be97c72
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509812"
---
# <a name="view-model-details"></a>Visa modellinformation

På fliken modeller under projektet visas alla modeller i projektet. Alla modeller som har tränats för projektet visas på den här fliken.

För varje modell i projektet visas dessa uppgifter.

1. Modell namn: visar modell namnet för en specifik modell.

2. Status: visar status för en specifik modell. Din nya utbildning kommer att ha statusen skickad tills den accepteras. Statusen ändras till data bearbetning medan tjänsten utvärderar dokumentets innehåll. När utvärderingen av dokumenten har slutförts ändras statusen till körs och du kan se antalet meningar som är en del av utbildningen, inklusive de justerings-och test uppsättningar som skapas automatiskt. Nedan visas en lista med modell status som beskriver modellens tillstånd.

    - Skickat: anger att Server delen bearbetar dokumenten för den modellen.

    - TrainingQueued: anger att utbildningen har placerats i kö till MT-system för den modellen.

    - Kör: anger att träningen körs i MT-systemet för den modellen.

    - Lyckades: anger att utbildningen lyckades i MT-systemet och att en modell är tillgänglig. I den här statusen visas en BLEU Poäng för den modellen.

    - Distribuerad: anger att den lyckade tränade modellen skickas till MT-systemet för distribution.

    - Avdistribuera: anger att den distribuerade modellen är avdistribuerad.

    - Inte distribuerad: anger att processen för att distribuera en modell har slutförts utan problem.

    - Inlärningen misslyckades: anger att träningen misslyckades. Om ett inlärnings fel inträffar försöker du igen med utbildnings jobbet. Om felet kvarstår kan du kontakta oss. Ta inte bort den misslyckade modellen.

    - DataProcessingFailed: anger att data bearbetningen har misslyckats för ett eller flera dokument som tillhör modellen.

    - DeploymentFailed: anger att modell distributionen har misslyckats.

    - MigratedDraft: anger att modellen är i utkast tillstånd efter migrering från hubben till en anpassad översättare.

3. BLEU Poäng: visar BLEU (tvåspråkig utvärdering understudie) av modellen, vilket anger kvaliteten på översättnings systemet. Det här resultatet visar hur nära översättnings systemet som är resultatet av den här utbildningen som matchar referens meningarna i test data uppsättningen. BLEU-poängen visas om träningen har slutförts. Om inlärningen inte är slutförd/misslyckad visas inga BLEU poäng.

4. Antal inlärnings meningar: visar det totala antalet meningar som används som inlärnings uppsättning.

5. Justera menings antal: visar det totala antalet meningar som används som justerings uppsättning.

6.  Antal inlärnings meningar: visar det totala antalet meningar som används som testnings uppsättning.

7.  Antal svartvita meningar: visar det totala antalet meningar som används som mono uppsättning.

8.  Knappen distribuera åtgärd: för en lyckad tränad modell visas knappen distribuera om den inte har distribuerats. Om en modell distribueras visas en "avdistribuera"-knapp.

9. Ta bort: du kan använda den här knappen om du vill ta bort modellen. Om du tar bort en modell raderas inte alla dokument som används för att skapa modellen.

    ![Visa modellinformation](media/how-to/how-to-view-model-details.png)

>[!Note]
>För att jämföra efterföljande utbildningar för samma system är det viktigt att du behåller justerings uppsättningen och testnings uppsättnings konstanten.

## <a name="view-model-training-details"></a>Visa information om modell utbildning

När din utbildning är klar kan du granska information om utbildningen från informations sidan. Välj ett projekt, leta upp och välj fliken modeller och välj en modell.

Modell sidan har två flikar: tränings information och test.

1.  **Tränings information:** På den här fliken visas en lista över dokument som används i utbildningen:

    -  Dokument namn: det här fältet visar namnet på dokumentet

    -  Dokument typ: det här fältet visar om det här dokumentet är parallellt/mono.

    -  Antal meningar i käll språk: det här fältet visar antalet meningar som en del av käll språket.

    -  Antal meningar i målspråk: det här fältet visar antalet meningar som ingår i mål språket.

    -  Justerade meningar: det här fältet visar antalet meningar har justerats efter anpassad översättare under justerings processen.

    -  Använda meningar: det här fältet visar antalet meningar som har använts av en anpassad översättare under utbildningen.

    ![Information om modell utbildning](media/how-to/how-to-model-training-details.png)

2.  **Test:** På den här fliken visas test informationen för en lyckad utbildning.

## <a name="next-steps"></a>Nästa steg

- Granska [test resultat](how-to-view-system-test-results.md) och analysera utbildnings resultatet.
