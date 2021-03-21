---
title: Viktig information om StorSimple 8000-seriens uppdatering 5
description: Beskriver de nya funktionerna, problemen och lösningarna för StorSimple 8000-serien uppdatering 5.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021052"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Viktig information om StorSimple 8000-seriens uppdatering 5

## <a name="overview"></a>Översikt

I följande versions information beskrivs de nya funktionerna och de kritiska öppnings problemen för StorSimple 8000-serien uppdatering 5. De innehåller också en lista över de StorSimple program uppdateringar som ingår i den här versionen.

Uppdatering 5 kan tillämpas på alla StorSimple-enheter som kör uppdatering 0,1 via uppdatering 4. Den enhets version som är associerad med uppdatering 5 är 6.3.9600.17845.

Granska informationen i versions anteckningarna innan du distribuerar uppdateringen i StorSimple-lösningen.

> [!IMPORTANT]
> * Uppdatering 5 är en obligatorisk uppdatering och måste installeras omedelbart. Mer information finns i så här [installerar du uppdatering 5](storsimple-8000-install-update-5.md).
> * Uppdatering 5 har enhets program vara, inbyggd disk program vara, OS-säkerhet och andra OS-uppdateringar. Det tar cirka 4 timmar att installera den här uppdateringen. Uppdatering av inbyggd disk program vara är en störnings uppdatering och resulterar i en stillestånds tid för enheten. Vi rekommenderar att du installerar uppdatering 5 för att hålla enheten uppdaterad.
> * För nya versioner kanske du inte ser uppdateringar direkt eftersom vi gör en stegvis distribution av uppdateringarna. Vänta några dagar och Sök sedan efter uppdateringar igen eftersom uppdateringarna blir tillgängliga snart.

## <a name="whats-new-in-update-5"></a>Nyheter i uppdatering 5

Följande viktiga förbättringar och fel korrigeringar har gjorts i uppdatering 5.

* **Användning av Azure Active Directory (AAD) för att autentisera med StorSimple Enhetshanteraren-tjänsten** – från och med uppdatering 5 och senare används Azure Active Directory för att autentisera med StorSimple Enhetshanteraren-tjänsten. Den gamla mekanismen för autentisering kommer att föråldras med 2017 december. Alla användare måste inkludera de nya autentiserings-URL: erna i brand Väggs reglerna. Mer information finns [i URL: erna för autentiseringar som anges i nätverks kraven för din StorSimple-enhet](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Om URL: en för autentisering inte ingår i brand Väggs reglerna, ser användarna en kritisk varning om att deras StorSimple-enhet inte kunde autentiseras med tjänsten. Om användarna ser den här aviseringen måste de inkludera den nya autentiserings-URL: en. Mer information finns i [StorSimple Network Alerts](storsimple-8000-manage-alerts.md#networking-alerts).

* **Ny version av StorSimple Snapshot Manager** – en ny version av StorSimple Snapshot Manager lanseras med uppdatering 5 och är kompatibel med alla StorSimple-enheter som kör uppdatering 4 eller senare. Vi rekommenderar att du uppdaterar till den här versionen. Den tidigare versionen av StorSimple Snapshot Manager används för StorSimple-enheter som kör uppdatering 3 eller tidigare. [Hämta rätt version av StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) och se [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problem som korrigeras i uppdatering 5

Följande tabell innehåller en sammanfattning av problem som korrigerades i uppdatering 5.

| Nej | Visning av aktuellt objekt | Problem | Gäller fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell-fjärrkommunikation |I den tidigare versionen skulle en användare få ett fel meddelande vid försök att upprätta en fjärr anslutning till StorSimple Cloud Appliance via Windows PowerShell. Det här problemet var rot-orsakade och korrigerade i den här versionen. |Inga |Ja |
| 2 |Mallar för bandbredd |I en tidigare version fick du ett problem med bandbredds-mallar som resulterade i lägre bandbredd än vad enheten hade kon figurer ATS för. Det här problemet har lösts i den här versionen. |Ja |Ja |
| 3 |Redundans |När en enhet med ett stort antal volymer redundansväxlats till en annan enhet som kör uppdatering 4 i en tidigare version, skulle processen Miss lyckas vid försök att tillämpa åtkomst kontroll posterna. Det här problemet åtgärdas i den här versionen. |Ja |Ja |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Kända problem i uppdatering 5 från tidigare versioner

Det finns inga nya kända problem i uppdatering 5. En lista över problem som överförts till uppdatering 5 från tidigare versioner finns i [viktig information om uppdatering 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Serial-Attached SCSI (SAS) och uppdateringar av inbyggd program vara i uppdatering 5

Den här versionen har SAS-styrenheter och uppdateringar för inbyggd driv rutin och inbyggd program vara. Mer information om hur du installerar dessa uppdateringar finns i [installera uppdatering 5](storsimple-8000-install-update-5.md) på din StorSimple-enhet.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>StorSimple Cloud Appliance uppdateringar i uppdatering 5

Den här uppdateringen kan inte tillämpas på StorSimple Cloud Appliance (kallas även den virtuella enheten). Nya moln enheter måste skapas med hjälp av uppdatering 5-avbildningen. Information om hur du skapar en StorSimple Cloud Appliance finns i [distribuera och hantera en StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Nästa steg

Lär dig hur du [installerar uppdatering 5](storsimple-8000-install-update-5.md) på din StorSimple-enhet.

