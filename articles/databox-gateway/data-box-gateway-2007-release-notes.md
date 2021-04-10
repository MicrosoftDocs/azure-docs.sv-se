---
title: Azure Stack Edge & Azure Data Box Gateway 2007 viktig information | Microsoft Docs
description: Beskriver kritiska öppna problem och lösningar för Azure Stack Edge och Data Box Gateway som kör 2007-versionen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582784"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Viktig information om Azure Stack Edge och Azure Data Box Gateway 2007

I följande viktig information identifieras kritiska öppna problem och de lösta problemen för 2007-versionen för Azure Stack Edge och Data Box Gateway.

Viktig information uppdateras kontinuerligt och eftersom kritiska problem som kräver en lösning upptäcks, läggs de till. Innan du distribuerar Azure Stack Edge/Data Box Gateway bör du gå igenom informationen i viktig information.

Den här versionen motsvarar program varu versionerna:

- **Azure Stack Edge 2007 (1.6.1280.1667)** – KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** – KB 4566550

> [!NOTE]
> Uppdatering 2007 kan endast tillämpas på alla enheter som kör program varan för allmän tillgänglighet (GA) eller senare.

## <a name="whats-new"></a>Nyheter

Den här versionen innehåller följande fel korrigering:

- **Överförings problem** – den här versionen åtgärdar ett uppladdnings problem där uppladdnings omstarten på grund av fel kan minska hastigheten för uppladdningen. Det här problemet kan uppstå när du överför en data uppsättning som främst består av filer som är stora i storlek i förhållande till tillgänglig bandbredd, särskilt, men inte begränsat till, när bandbredds begränsningen är aktiv. Den här ändringen ser till att en tillräcklig affärs möjlighet ges för att slutföra uppladdningen innan du startar om överföringen av en specifik fil.

Den här versionen innehåller även följande uppdateringar:

- Bas avbildningen för Windows VHD har uppdaterats.
- Alla kumulativa uppdateringar för Windows och .NET Framework ingår som släpptes genom maj 2020.
- Den här versionen stöder IoT Edge 1.0.9.3 på Azure Stack Edge-enheter.

## <a name="known-issues-in-this-release"></a>Kända problem i den här versionen

Inga nya problem har antecknats för den här versionen. Alla eventuella problem som har antecknats har överförts från tidigare versioner. Om du vill se en lista över kända problem går du till [kända problem i ga-versionen](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Nästa steg

- [Förbered för att distribuera Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)
- [Förbereda distributionen av Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
