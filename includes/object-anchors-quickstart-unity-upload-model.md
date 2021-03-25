---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d06a6ecd8af16da3e6df21e984fbf6a727fbc27e
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105663"
---
### <a name="upload-your-model"></a>Ladda upp din modell

Om du inte redan har en modell för objekt ankare följer du anvisningarna i [skapa en modell](../articles/object-anchors/quickstarts/get-started-model-conversion.md) för att skapa en. Gå sedan tillbaka hit.

När din HoloLens är ansluten till Windows Device Portal följer du de här stegen för att ladda upp en modell för appen som ska användas:

1. I Windows Device portal går du till **System > utforskaren > localappdata**. Där bör du se ditt program i listan över appar.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="Utforskaren":::

2. Öppna programmet och klicka på `LocalState` mappen.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Öppna mappen LocalState":::

3. Överför modell filen till `LocalState` mappen.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Ladda upp modellen i portalen":::

    Starta programmet från HoloLens igen. Nu kan du identifiera objekt som matchar modellen.