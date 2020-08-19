---
title: Så här skapar du en projekt – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Den här artikeln beskriver hur du skapar och hanterar ett projekt i den anpassade Azure Cognitive Services-konverteraren.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 3043ec0600f802a409387f0114aae5f56151da16
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511003"
---
# <a name="create-a-project"></a>Skapa ett projekt

Ett projekt är en behållare för modeller, dokument och tester. Varje projekt innehåller automatiskt alla dokument som överförs till den arbets ytan som har rätt språk par.

Att skapa projektet är det första steget i att skapa en modell.

## <a name="create-a-project"></a>Skapa ett projekt:

1.  I den [anpassade översättnings](https://portal.customtranslator.azure.ai) portalen klickar du på skapa projekt.

    ![Skapa projekt](media/how-to/how-to-create-project.png)

2.  Ange följande information om ditt projekt i dialog rutan:

    a.  Projekt namn (obligatoriskt): ge projektet ett unikt, meningsfullt namn. Det är inte nödvändigt att nämna språken i rubriken.

    b.  Beskrivning: en kort sammanfattning om projektet. Den här beskrivningen påverkar inte beteendet för den anpassade översättaren eller det resulterande anpassade systemet, men kan hjälpa dig att skilja mellan olika projekt.

    c.  Språk par (obligatoriskt): Välj det språk som du vill översätta från och till.

    d.  Kategori (krävs): Välj den kategori som passar bäst för ditt projekt. Kategorin beskriver terminologin och formatet för dokumenten som du tänker översätta.

    e.  Kategori Beskrivning: Använd det här fältet för att bättre beskriva det specifika fält eller den bransch där du arbetar. Om din kategori till exempel är medicin kan du lägga till ett visst dokument, t. ex. en operation eller Pediatrics. Beskrivningen påverkar inte beteendet för den anpassade översättaren eller det resulterande anpassade systemet.

    f.  Projekt etikett: [projekt etiketten](workspace-and-project.md#project-labels) skiljer mellan projekt med samma språk par och kategori. Vi rekommenderar att du *bara* använder en etikett om du planerar att bygga flera projekt för samma språk par och samma kategori och vill komma åt dessa projekt med en annan kategori. Använd inte det här fältet om du bara skapar system för en kategori. Det krävs ingen projekt etikett och det är inte praktiskt att skilja mellan språk par. Du kan använda samma etikett för flera projekt.

    ![Dialog rutan skapa projekt](media/how-to/how-to-create-project-dialog.png)

3.  Klicka på Skapa

## <a name="view-project-details"></a>Visa projekt information

Landnings sidan för anpassad översättare visar de första 10 projekten i din arbets yta. Det visar projekt namn, språk par, kategori, status och BLEU poäng.

När du har valt ett projekt visas följande på projekt sidan:

- Kategorinr: en Kategorinhet skapas genom att sammanfoga WorkspaceID, projekt etiketten och kategori koden. Du kan använda kategorin med text Translator för att hämta anpassade översättningar. Kopiera genom att klicka på kopierings ikonen.

- Knappen träna: Använd den här knappen för att starta en [träna en modell](how-to-train-model.md).

- Knappen Lägg till dokument: Använd den här knappen för att [Ladda upp dokument](how-to-upload-document.md).

- Knappen Filtrera dokument: Använd den här knappen för att filtrera och söka efter vissa dokument.

    ![Visa projekt information](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du söker, redigerar och tar bort projekt](how-to-search-edit-delete-projects.md).
- Lär dig [hur du överför dokument](how-to-upload-document.md) för att bygga översättnings modeller.
