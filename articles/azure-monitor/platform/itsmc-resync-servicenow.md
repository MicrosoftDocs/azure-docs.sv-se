---
title: Åtgärda ServiceNow-synkroniseringsproblem manuellt
description: Återställ anslutningen till ServiceNow så att aviseringar i Microsoft Azure kan anropa ServiceNow igen
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/17/2021
ms.openlocfilehash: 1b2e914057aa72934524cc4977e3d2292394d0b7
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882109"
---
# <a name="how-to-manually-fix-sync-problems"></a>Så här åtgärdar du synkroniseringsproblem manuellt

Azure Monitor kan ansluta till ITSM-leverantörer (IT Service Management) från tredje part. ServiceNow är en av dessa leverantörer.

Av säkerhets skäl kan du behöva uppdatera autentiseringstoken som används för din anslutning med ServiceNow.
Använd följande synkroniseringsprocess för att återaktivera anslutningen och uppdatera token:

1. Sök efter lösningen i den översta sökbanderollen och välj sedan de relevanta lösningarna

    ![Skärm bild som visar den översta sökbanderollen och var du väljer de relevanta lösningarna.](media/itsmc-resync-servicenow/solution-search-8-bit.png)

1. I lösnings skärmen väljer du "Markera alla" i prenumerations filtret och filtrerar sedan efter "ServiceDesk"

    ![Skärm bild som visar var du väljer Markera alla och var du vill filtrera efter ServiceDesk.](media/itsmc-resync-servicenow/solutions-list-8-bit.png)

1. Välj lösning för din ITSM-anslutning.
1. Välj ITSM-anslutning i den vänstra banderollen.

    ![Skärm bild som visar var du väljer ITSM-anslutningar.](media/itsmc-resync-servicenow/itsm-connector-8-bit.png)

1. Välj varje koppling i listan. 
    1. Klicka på kopplingens namn för att konfigurera det
    1. Ta bort alla kopplingar som inte längre används

    1. Uppdatera fälten enligt [dessa definitioner](./itsmc-connections.md) baserat på din partner typ

    1. Klicka på synkronisera

       ![Skärm bild som visar knappen Synkronisera.](media/itsmc-resync-servicenow/resync-8-bit-2.png)

    1. Klicka på Spara

        ![Ny anslutning](media/itsmc-resync-servicenow/save-8-bit.png)

f.    Granska meddelandena för att se om processen har startats.
