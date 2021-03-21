---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044674"
---
### <a name="build-and-deploy-the-app"></a>Skapa och distribuera appen

Öppna `.sln` filen som genererats av Unit. Ändra build-konfigurationen till följande.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="Bygg konfiguration":::

Därefter måste du konfigurera den **fjärranslutna datorns IP-adress** för att distribuera och felsöka appen.

Högerklicka på App-projektet och välj **Egenskaper**. På sidan Egenskaper väljer du **konfigurations egenskaper-> fel sökning**. Ändra värdet för **dator namn** till din HoloLens-enhets IP-adress och klicka på **Använd**.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="fjärrfelsökning":::

Stäng egenskaps sidan. Klicka på **fjärran sluten dator**. Appen ska börja bygga och distribuera till din fjärranslutna enhet. Kontrol lera att enheten är aktiv.
