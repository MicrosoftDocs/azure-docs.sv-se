---
title: Träna en modell – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Träna en modell är ett viktigt steg när du skapar en översättnings modell. Utbildning sker baserat på dokument som du väljer för den utbildningen.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 824516a327d45feb5b6a084a113633bd3a486abe
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508380"
---
# <a name="train-a-model"></a>Träna en modell

Träna en modell är det första och viktigaste steget för att skapa en översättnings modell, annars går det inte att skapa modellen. Utbildning sker baserat på dokument som du väljer för utbildningarna. När du väljer dokument av typen "utbildning" måste du vara mindful av minst 10 000 parallella meningar minimi kravet. När du väljer dokument visar vi det totala antalet tränings meningar som hjälper dig. Detta krav gäller inte när du bara väljer dokument av dokument typen ord lista för att träna en modell.

För att träna en modell:

1. Välj det projekt där du vill bygga en modell.

2. På fliken data för projektet visas alla relevanta dokument för projekt språk paret. Välj de dokument som du vill använda för att träna din modell manuellt. Du kan välja utbildnings-, justerings-och test dokument från den här skärmen. Du behöver också bara välja inlärnings uppsättningen och har anpassad översättare skapa justerings-och test uppsättningarna åt dig.

    - Dokument namn: namnet på dokumentet.

    - Koppling: om det här dokumentet är ett parallellt eller monolingual dokument. Monolingual-dokument stöds för närvarande inte för utbildning.

    - Dokument typ: kan vara utbildning, justering, testning eller ord lista.

    - Språk par: Detta visar käll-och mål språk för projektet.

    - Käll meningar: visar antalet meningar som extraherats från käll filen.

    - Mål meningar: visar antalet meningar som extraherats från målfilen.

    ![Träningsmodell](media/how-to/how-to-train-model.png)

3. Klicka på knappen "skapa modell".

4. Ange namnet på din modell i dialog rutan. Som standard är "träna direkt" valt för att starta en pipeline för utbildning när du klickar på knappen "skapa modell". Du kan välja "Spara som utkast" om du vill skapa modellens metadata och lägga modellen i utkast läge, men modell träningen startar inte. Vid ett senare tillfälle måste du manuellt välja modeller i utkast läge för att träna.

5. Klicka på knappen "skapa modell".

    ![Dialog rutan träna modell](media/how-to/how-to-train-model-2.png)

6. Anpassad översättare kommer att skicka utbildningen och Visa status för utbildningen på fliken modeller.

    ![Träna modell sida](media/how-to/how-to-train-model-3.png)

>[!Note]
>Anpassad översättare stöder 10 samtidiga utbildningar inom en arbets yta vid varje tidpunkt.

## <a name="modify-a-model-name"></a>Ändra ett modell namn

Du kan ändra ett modell namn från sidan modell information.

1. Från sidan projekt klickar du på det projekt namn där modellen finns.
2. Klicka på fliken modell.
3. Klicka på modell namnet för att Visa modell informationen.
4. Klicka på Penn ikonen.

    ![Redigera modellen](media/how-to/how-to-edit-model.png)

5. I dialog rutan ändrar du modell namnet och ger din modell ett beskrivande namn.

    ![Redigera mer dialog](media/how-to/how-to-edit-model-dialog.png)

6. Klicka på Spara.

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du visar modell information](how-to-view-model-details.md).
