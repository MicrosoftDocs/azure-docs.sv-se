---
title: Versions anmärkningar för Azure Data Box Gateway 2101 | Microsoft Docs
description: Beskriver kritiska öppna problem och lösningar för Azure Data Box Gateway som kör 2101-versionen.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072729"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Viktig information om Azure Data Box Gateway 2101

I följande versions information identifieras kritiska öppna problem och lösta problem för 2101-versionen av Azure Data Box Gateway.

Viktig information uppdateras kontinuerligt. När kritiska problem som kräver en lösning upptäcks, läggs de till. Innan du distribuerar Azure Data Box Gateway bör du läsa igenom informationen i viktig information.  

Den här versionen motsvarar program varu versionerna:

- **Data Box Gateway 2101 (1.6.1475.2528)** – KB 4603462

> [!NOTE]
> Uppdatering 2101 kan endast tillämpas på alla enheter som kör program varan för allmän tillgänglighet (GA) eller senare.

## <a name="whats-new"></a>Nyheter

Den här versionen innehåller följande fel korrigering:

- **Överförings problem** – den här versionen åtgärdar ett uppladdnings problem där uppladdnings omstarten på grund av fel kan minska antalet uppladdningar som slutförs. Det här problemet kan uppstå när du överför en data uppsättning som främst består av filer som är stora i storlek i förhållande till tillgänglig bandbredd, särskilt, men inte begränsat till, när bandbredds begränsningen är aktiv. Den här ändringen innebär att du har tillräcklig möjlighet att ladda upp en fil innan du startar om överföringen för en specifik fil.

Den här versionen innehåller även följande uppdateringar:

- Alla kumulativa Windows-uppdateringar och .NET Framework-uppdateringar som publicerats till och med 2020 oktober.
- Den statiska IP-adressen för Azure Data Box Gateway kvarhålls över program uppdateringar.

## <a name="known-issues-in-this-release"></a>Kända problem i den här versionen

Inga nya problem har antecknats för den här versionen. Alla eventuella problem som har antecknats har överförts från tidigare versioner. Om du vill se en lista över kända problem går du till [kända problem i ga-versionen](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Nästa steg

- [Förbereda distributionen av Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
