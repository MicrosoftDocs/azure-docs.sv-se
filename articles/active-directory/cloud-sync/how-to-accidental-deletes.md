---
title: Azure AD Connect moln synkronisering, oavsiktliga borttagningar
description: I det här avsnittet beskrivs hur du använder funktionen för oavsiktlig borttagning för att förhindra borttagningar.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785246"
---
# <a name="accidental-delete-prevention"></a>Förhindra borttagning av misstag

I följande dokument beskrivs funktionen för oavsiktlig borttagning av Azure AD Connect Cloud Sync.  Funktionen för oavsiktlig borttagning är utformad för att skydda dig från oavsiktliga konfigurations ändringar och ändringar i din lokala katalog som påverkar många användare och grupper.  Med den här funktionen kan du:

- Konfigurera möjligheten att förhindra oavsiktliga borttagningar automatiskt. 
- Ange antalet objekt (tröskel) utöver vilka konfigurationen börjar gälla 
- Konfigurera en e-postadress för e-post så att de kan få ett e-postmeddelande när den aktuella synkroniseringen har placerats i karantän för det här scenariot 

Om du vill använda den här funktionen anger du tröskelvärdet för antalet objekt, om det tas bort, synkroniseringen ska stoppas.  Så om det här numret nås stoppas synkroniseringen och ett meddelande skickas till det angivna e-postmeddelandet.  Med det här meddelandet kan du undersöka vad som händer.


## <a name="configure-accidental-delete-prevention"></a>Konfigurera förebyggande borttagning av misstag
Följ stegen nedan om du vill använda den nya funktionen.


1.  Välj **Azure Active Directory** i Azure Portal.
2.  Välj **Azure AD Connect**.
3.  Välj **hantera synkronisering av moln**.
4. Under **konfiguration** väljer du din konfiguration.
5. Under **Inställningar** fyller du i följande:
    - **E-postavisering** – e-post som används för meddelanden
    - **Förhindra oavsiktliga borttagningar** – Markera den här kryss rutan om du vill aktivera funktionen
    - **Tröskelvärde för oavsiktlig borttagning** – ange antalet objekt som du vill stoppa synkroniseringen för och skicka ett meddelande

![Oavsiktliga borttagningar](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Återställning från en instans av en oavsiktlig borttagning
Om du stöter på en oavsiktlig borttagning visas detta i status för konfigurationen av din etablerings agent.  Det kommer att säga att **tröskelvärdet för borttagning har överskridits**.
 
![Status för oavsiktlig borttagning](media/how-to-accidental-deletes/delete-1.png)

Genom att klicka på **tröskelvärdet för borttagning har överskridits** visas statusinformation för synkronisering.  Detta ger ytterligare information. 
 
 ![Synkroniseringsstatus](media/how-to-accidental-deletes/delete-2.png)

Genom att högerklicka på ellipserna visas följande alternativ:
 - Visa etablerings logg
 - Visa agent
 - Tillåt borttagningar

 ![Högerklick](media/how-to-accidental-deletes/delete-3.png)

Med hjälp av **Visa etablerings loggen** kan du se **StagedDelete** -posterna och granska informationen som finns på de användare som har tagits bort.
 
 ![Etableringsloggar](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Tillåter borttagning

Åtgärden **Tillåt borttagning** tar bort de objekt som utlöste tröskelvärdet för oavsiktlig borttagning.  Använd följande procedur för att godkänna borttagningarna.  

1. Högerklicka på ellipserna och välj **Tillåt borttagning**.
2. Klicka på **Ja** i bekräftelsen för att tillåta borttagningarna.
 
 ![Ja vid bekräftelse](media/how-to-accidental-deletes/delete-4.png)

3. Du kommer att se en bekräftelse på att borttagningarna har godkänts och att statusen går tillbaka till felfritt med nästa cykel. 
 
 ![Acceptera borttagningar](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Avvisar borttagningar

Om du inte vill tillåta borttagningarna måste du göra följande:
- Undersök källan för borttagningarna
- åtgärda problemet (exempel: OU har flyttats bort av misstag och du har nu lagt till det igen i omfånget)
- Kör **omstart av synkronisering** på agent konfigurationen

## <a name="next-steps"></a>Nästa steg 

- [Azure AD Connect fel sökning av Cloud Sync?](how-to-troubleshoot.md)
- [Fel koder för Azure AD Connect Cloud Sync](reference-error-codes.md)
 

