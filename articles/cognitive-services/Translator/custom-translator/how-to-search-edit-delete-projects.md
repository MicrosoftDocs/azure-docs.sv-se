---
title: Söka, redigera och ta bort projekt – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Anpassad översättare tillhandahåller olika sätt att hantera dina projekt på ett effektivt sätt. Du kan skapa flera projekt, söka baserat på dina kriterier, redigera dina projekt. Det går också att ta bort ett projekt i anpassad översättare.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 4c48a8a18a3ad0487b4c3840847c7f5edbb6d29f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510384"
---
# <a name="search-edit-and-delete-projects"></a>Sök, redigera och ta bort projekt

Anpassad översättare tillhandahåller flera olika sätt att hantera dina projekt på ett effektivt sätt. Du kan skapa många projekt, söka baserat på dina kriterier och redigera dina projekt. Det går också att ta bort ett projekt i anpassad översättare.  

## <a name="search-and-filter-projects"></a>Sök och filtrera projekt

Med filter verktyget kan du söka i projekt efter olika filter villkor. Den filtrerar som projekt namn, status, källa och mål och projekt kategori.

1. Klicka på filter knappen.

    ![Sök projekt](media/how-to/how-to-search-project.png)

2. Du kan filtrera efter alla (eller alla) av följande fält: projekt namn, käll språk, mål språk, kategori och projekt tillgänglighet.

3. Klicka på Använd.

    ![Sök efter projekt filter alternativ](media/how-to/how-to-search-project-filters.png)

4. Ta bort filtret om du vill visa alla dina projekt genom att trycka på "Rensa".

## <a name="edit-a-project"></a>Redigera ett projekt

Med anpassad översättare kan du redigera namn och beskrivning för ett projekt. Andra Project-metadata som kategori, käll språk och mål språk är inte tillgängliga för redigering. Stegen nedan beskriver hur du redigerar ett projekt.

1. Klicka på Penn ikonen som visas när du hovrar över ett projekt.

    ![Redigera projekt](media/how-to/how-to-edit-project.png)

2. I dialog rutan kan du ändra projekt namnet, beskrivningen av projektet, kategori beskrivningen och projekt etiketten om ingen modell har distribuerats. Du kan inte ändra kategori-eller språk paret när projektet har skapats.

    ![Dialog rutan Redigera projekt](media/how-to/how-to-edit-project-dialog.png)

3. Klicka på knappen Spara.

## <a name="delete-a-project"></a>Ta bort ett projekt

Du kan ta bort ett projekt när du inte längre behöver det. Se till att projektet inte har modeller i aktivt läge, t. ex. distribuerad, inskickad utbildning, data bearbetning, distribution osv. i annat fall Miss tagit borttagnings åtgärden. Stegen nedan beskriver hur du tar bort ett projekt.

1. Hovra över alla projekt poster och klicka på pappers korgs ikonen.

   ![Ta bort projekt](media/how-to/how-to-delete-project.png)

2. Bekräfta borttagning. Om du tar bort ett projekt tas alla modeller som skapats i projektet bort. Att ta bort projektet påverkar inte dina dokument.

   ![Bekräftelse dialog ruta för borttagning](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>Nästa steg

- [Ladda upp dokument](how-to-upload-document.md) för att börja skapa din anpassade översättnings modell.
