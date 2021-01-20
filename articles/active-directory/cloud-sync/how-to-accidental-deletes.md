---
title: Azure AD Connect moln synkronisering, oavsiktliga borttagningar
description: I det här avsnittet beskrivs hur du använder funktionen för oavsiktlig borttagning för att förhindra borttagningar.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef7b6d9495b1431e03808b830671e839b90d436
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614287"
---
# <a name="accidental-delete-prevention"></a>Förhindra borttagning av misstag

I följande dokument beskrivs funktionen för oavsiktlig borttagning av Azure AD Connect Cloud Sync.  Funktionen för oavsiktlig borttagning är utformad för att skydda dig från oavsiktliga konfigurations ändringar och ändringar i din lokala katalog som påverkar många användare och grupper.  Med den här funktionen kan du:

- Konfigurera möjligheten att förhindra oavsiktliga borttagningar automatiskt. 
- Ange antalet objekt (tröskel) utöver vilka konfigurationen börjar gälla 
- Konfigurera en e-postadress för avisering så att de kan få ett e-postmeddelande när synkroniseringen i fråga placeras i karantän för det här scenariot 

Om du vill använda den här funktionen anger du tröskelvärdet för antalet objekt, om det tas bort, synkroniseringen ska stoppas.  Så om det här numret nås stoppas synkroniseringen och ett meddelande skickas till det angivna e-postmeddelandet.  På så sätt kan du undersöka vad som händer.


## <a name="configure-accidental-delete-prevention"></a>Konfigurera förebyggande borttagning av misstag
Följ stegen nedan om du vill använda den nya funktionen.


1.  Välj **Azure Active Directory** i Azure Portal.
2.  Välj **Azure AD Connect**.
3.  Välj **hantera synkronisering av moln**.
4. Under **konfiguration** väljer du din konfiguration.
5. Under **Inställningar** fyller du i följande:
    - **E-postavisering** – e-post som används för meddelanden
    - **Förhindra oavsiktliga borttagningar** – Markera den här kryss rutan om du vill aktivera funktionen
    - **Tröskelvärde för oavsiktlig borttagning** – ange ett antal objekt för att utlösa stopp och meddelande för synkronisering

![Oavsiktliga borttagningar](media/how-to-accidental-deletes/accident-1.png)

## <a name="next-steps"></a>Nästa steg 

- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
- [Så här installerar du Azure AD Connect Cloud Sync](how-to-install.md)
 

