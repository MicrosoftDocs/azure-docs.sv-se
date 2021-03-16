---
title: 'Självstudie: skapa en app för formulär bearbetning med AI Builder – formulär tolken'
titleSuffix: Azure Cognitive Services
description: I den här självstudien använder du AI Builder för att skapa och träna ett formulär bearbetnings program.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: lajanuar
ms.openlocfilehash: 30c44ac60783132344cc018135d2c248f91f46bc
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467127"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>Självstudie: skapa en app för bearbetning av formulär med AI Builder

[AI Builder](/ai-builder/overview) är en Power Platform-funktion som gör att du kan automatisera processer och förutsäga resultat för att förbättra affärs prestanda. Du kan använda AI Builder-formulär bearbetning för att skapa AI-modeller som identifierar och extraherar nyckel/värde-par och tabell data från formulär dokument.

> [!NOTE]
> Det här projektet är också tillgängligt som en [Microsoft Learn modul](/learn/modules/get-started-with-form-processing/).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en AI-modell för formulär bearbetning
> * Träna modellen
> * Publicera din modell som ska användas i Azure Power Apps eller Power automatisering

## <a name="prerequisites"></a>Förutsättningar

* En uppsättning minst fem formulär av samma typ som används för utbildning/testning av data. Se [skapa en tränings data uppsättning](./build-training-data-set.md) för tips och alternativ för att sätta samman din tränings data uppsättning. I den här snabb starten kan du använda filerna under mappen **träna** i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2128080).
* En Power Apps eller en automatiserad energi licens – se [licens guiden](https://go.microsoft.com/fwlink/?linkid=2085130). Licensen måste innehålla [common data service](https://powerplatform.microsoft.com/common-data-service/).
* Ett AI Builder [-tillägg eller en utvärderings version](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409).


## <a name="create-a-form-processing-project"></a>Skapa ett formulär bearbetnings projekt

1. Gå till [Power Apps](https://make.powerapps.com/) eller [Power](https://flow.microsoft.com/signin)Apps och logga in med ditt organisations konto.
1. I den vänstra rutan väljer du **AI Builder**  >  **build**.
1. Välj **bearbetnings** kort för formuläret.
1. Ange ett namn för din modell.
1. Välj **Skapa**.

## <a name="upload-and-analyze-documents"></a>Ladda upp och analysera dokument

På sidan **Lägg till dokument** måste du tillhandahålla exempel dokument för att träna din modell för den typ av formulär som du vill extrahera information från. När du har laddat upp dokumenten analyserar AI Builder dem för att kontrol lera att de är tillräckliga för att träna en modell.

> [!NOTE]
> AI Builder stöder för närvarande inte följande typer av indata vid formulärbearbetning:
>
> - Komplexa tabeller (kapslade tabeller, sammanfogade rubriker eller celler osv.)
> - Kryssrutor eller alternativknappar
> - PDF-dokument som innehåller fler än 50 sidor
> - Ifyllbara PDF-filer
>
> Mer information om kraven för indata-dokument finns i [krav för indata](./overview.md#input-requirements).

### <a name="upload-your-documents"></a>Ladda upp dina dokument

1. Välj **Lägg till dokument**, välj minst fem dokument och välj sedan **Ladda upp**.
1. När uppladdningen är klar väljer du **Stäng**.
1. Välj sedan **analysera**.

> [!NOTE] 
> När du har laddat upp dessa dokument kan du fortfarande ta bort några av dokumenten eller ladda upp fler.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar knappen Lägg till dokument.](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>Analysera dina dokument

Under analys steget undersöker AI-verktyget dokumenten som du laddade upp och identifierar fälten och tabellerna. Hur lång tid det tar att slutföra den här åtgärden beror på antalet dokument som tillhandahålls, men i de flesta fall bör det bara ta några minuter.

När analysen är färdig väljer du miniatyren för att öppna val av fält.

> [!IMPORTANT]
> Om analysen misslyckades är det troligt att AI Builder inte kan identifiera strukturerad text i dina dokument. Kontrol lera att de dokument du har uppdaterat följer [ingångs kraven](./overview.md#input-requirements).

## <a name="select-your-form-fields"></a>Välj dina formulärfält

På sidan Val av fält väljer du de fält som är viktiga för dig:

1. Om du vill välja ett fält klickar du på en rektangel som anger ett identifierat fält i dokumentet eller väljer flera fält genom att klicka och dra. Du kan också välja fält från den högra rutan.
1. Klicka på namnet på det markerade fältet om du vill byta namn på det så att det passar dina behov eller normalisera de extraherade etiketterna.

    När du klickar på ett identifierat fält visas följande information:

    - **Fält namn**: namnet på etiketten för det identifierade fältet.
    - **Fält värde**: värdet för det identifierade fältet.

> [!div class="mx-imgBorder"]
> ![taggnings sida](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>Etiketter utan identifierade fält

Om fältet som du vill använda etiketten inte identifierades automatiskt av modellen, kan du rita en rektangel runt dess värde och ange ett etikett namn i dialog rutan som visas.

## <a name="train-your-model"></a>Träna modellen

1. Välj **Nästa** för att kontrollera de valda formulärfälten. Om allt ser bra ut väljer du **träna** för att träna din modell.

    > [!div class="mx-imgBorder"]
    > ![Sidan utbildning](./media/tutorial-ai-builder/summary-train-page.png)
1. När inlärningen är klar väljer du **Gå till informationssidan** i på skärmen **Träningen är klar**.
## <a name="quick-test-your-model"></a>Snabbtesta din modell

På informationssidan kan du testa din modell innan du publicerar eller använder den:

1. På informationssidan väljer du **Snabbtest**.
2. Du kan antingen dra och släppa ett dokument eller välja **Ladda upp från min enhet** för att ladda upp test filen. Snabbtestet bör bara ta några sekunder innan resultatet visas.
3. Du kan välja **börja** om för att köra ett annat test eller **stänga** om du är klar.

### <a name="troubleshooting-tips"></a>Felsökningstips

Om du får dåliga resultat eller låga förtroende poäng för vissa fält kan du prova följande tips:

- Träna med att använda formulär med olika värden i varje fält.
- Träna med en större uppsättning utbildnings dokument. Det fler dokument som du taggar, desto mer AI-verktyg kommer att lära sig att identifiera fälten bättre.
- Du kan optimera PDF-filer genom att bara välja vissa sidor att träna med. Använd alternativet **Skriv** ut  >  **till PDF** för att markera vissa sidor i dokumentet.

## <a name="publish-your-model"></a>Publicera din modell

Om du är nöjd med din modell väljer du **publicera**  för att publicera den. När publiceringen är klar höjs din modell som **Publicerad** och är redo att användas.

> [!div class="mx-imgBorder"]
> ![Sidan publicera modell](./media/tutorial-ai-builder/model-page.png)

När du har publicerat din formulär bearbetnings modell kan du använda den i en [app för Power Apps-arbetsytan](/ai-builder/form-processor-component-in-powerapps) eller i [energi spar läge](/ai-builder/form-processing-model-in-flow).

## <a name="next-steps"></a>Nästa steg

Följ AI Builder-dokumentationen för att använda en formulär bearbetnings modell.

* [Använd komponenten form-processor i Power Apps](/ai-builder/form-processor-component-in-powerapps)
* [Använd en formulär bearbetnings modell i automatiserad energi förbrukning](/ai-builder/form-processing-model-in-flow)