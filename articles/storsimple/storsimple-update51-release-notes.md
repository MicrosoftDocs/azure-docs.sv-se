---
title: StorSimple 8000-serien uppdatering 5,1 viktig information
description: Beskriver de nya funktionerna, problemen och lösningarna för StorSimple 8000-serien uppdatering 5,1.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: cdb971851ba678ce18f5a1c7954e5620740f3a4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657577"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>StorSimple 8000-serien uppdatering 5,1 viktig information

## <a name="overview"></a>Översikt

I följande versions information beskrivs de nya funktionerna och de kritiska öppnings problemen för StorSimple 8000-serien uppdateras 5,1. De innehåller också en lista över de StorSimple program uppdateringar som ingår i den här versionen.

Uppdatering 5,1 kan tillämpas på alla StorSimple-enheter som kör uppdatering 5. Om du använder en version som är lägre än 5 ska du först använda uppdatering 5 och sedan använda 5,1. Den enhets version som är associerad med uppdatering 5,1 är 6.3.9600.17885.

Granska informationen i versions anteckningarna innan du distribuerar uppdateringen i StorSimple-lösningen.

> [!IMPORTANT]
>
> * Uppdatering 5,1 är en obligatorisk uppdatering och måste installeras omedelbart. Mer information finns i så här [installerar du uppdatering 5,1](storsimple-8000-install-update-51.md).
> * Uppdatering 5,1 har endast säkerhets uppdateringar. Det tar cirka 30 minuter att installera den här uppdateringen. Vi rekommenderar starkt att du installerar uppdatering 5,1 för att säkerställa att enheten fungerar.
> * För nya versioner kanske du inte ser uppdateringar direkt eftersom vi gör en stegvis distribution av uppdateringarna. Vänta några dagar och Sök sedan efter uppdateringar igen eftersom uppdateringarna blir tillgängliga snart.

## <a name="whats-new-in-update-51"></a>Nyheter i uppdatering 5,1

Följande viktiga förbättringar och fel korrigeringar har gjorts i uppdatering 5,1:

* **Tls 1,2** – den här StorSimple-uppdateringen kommer att verkställa TLS 1,2 på alla klienter. Uppdateringen är obligatorisk för alla enheter i StorSimple 8000-serien.

   Om du ser följande varning måste du uppdatera program varan på enheten innan du fortsätter:

   En eller flera StorSimple-enheter kör en äldre program varu version. Den senaste tillgängliga uppdateringen för TLS 1,2 är en obligatorisk uppdatering och installeras omedelbart på dessa enheter. TLS 1,2 används för all Azure Portal kommunikation och utan den här uppdateringen kommer enheten inte att kunna kommunicera med StorSimple-tjänsten.

## <a name="known-issues-in-update-51-from-previous-releases"></a>Kända problem i uppdatering 5,1 från tidigare versioner

Det finns inga nya kända problem i uppdatering 5,1. En lista över problem som överförts till uppdatering 5,1 från tidigare versioner finns i [viktig information om uppdatering 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>StorSimple Cloud Appliance uppdateringar i uppdatering 5,1

Den här uppdateringen kan inte tillämpas på StorSimple Cloud Appliance (kallas även den virtuella enheten). Nya moln enheter måste skapas med uppdatering 5,1-avbildningen. Information om hur du skapar en StorSimple Cloud Appliance finns i [distribuera och hantera en StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Nästa steg

Lär dig hur du [installerar uppdatering 5,1](storsimple-8000-install-update-51.md) på din StorSimple-enhet.
