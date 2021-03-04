---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: 8b7ab183dfb7b6721beae7835acd75bc7a05e72c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044769"
---
### <a name="upload-your-model"></a>Ladda upp din modell

Om du inte redan har en modell för objekt ankare följer du anvisningarna i [skapa en modell](/object-anchors/quickstarts/get-started-model-conversion.md) för att skapa en. Gå sedan tillbaka hit.

När din HoloLens är ansluten till Windows Device Portal följer du de här stegen för att ladda upp en modell för appen som ska användas:

1. I Windows Device portal går du till **System > utforskaren > localappdata**. Där bör du se ditt program i listan över appar.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="Utforskaren":::

2. Öppna programmet och klicka på `LocalState` mappen.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Öppna mappen LocalState":::

3. Överför modell filen till `LocalState` mappen.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Ladda upp modellen i portalen":::

    Starta programmet från HoloLens igen. Nu kan du identifiera objekt som matchar modellen.
