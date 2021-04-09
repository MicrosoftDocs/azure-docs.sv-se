---
title: Viktig information om Azure Stack Edge Pro med FPGA 2101 | Microsoft Docs
description: Beskriver kritiska öppna problem och lösningar för Azure Stack Edge 2101-versionen.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 5c95da8d6ee88786ff983b4963c1cb96394886cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727557"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Viktig information om Azure Stack Edge Pro med FPGA 2101

I följande versions information identifieras kritiska öppna problem och de lösta problemen för 2101-versionen av Azure Stack Edge Pro med ett inbyggt fält programmerbar grind mat ris (FPGA).

Viktig information uppdateras kontinuerligt. När kritiska problem som kräver en lösning upptäcks, läggs de till. Innan du distribuerar Azure Stack Edge-enheten bör du läsa igenom informationen i viktig information.  

Den här versionen motsvarar program varu versionen:

- **Azure Stack Edge 2101 (1.6.1475.2528)** – KB 4599267

> [!NOTE]
> Uppdatering 2101 kan endast tillämpas på enheter som kör allmänna tillgänglighets versioner av program varan eller senare.

## <a name="whats-new"></a>Nyheter

Den här versionen innehåller följande fel korrigering:

- **Uppladdnings** problem – den här versionen åtgärdar ett uppladdnings problem, där uppladdnings omstarter som orsakas av ett fel kan minska antalet uppladdningar. Det här problemet kan uppstå när du överför en data uppsättning som främst består av filer som är stora i förhållande till tillgänglig bandbredd, särskilt, men inte begränsat till, när bandbredds begränsningen är aktiv. Den här ändringen innebär att du har tillräcklig möjlighet att ladda upp en fil innan du startar om överföringen för en specifik fil.

Den här versionen innehåller även följande uppdateringar:

- Alla kumulativa Windows-uppdateringar och .NET Framework-uppdateringar som publicerats till och med 2020 oktober.
- Den inbyggda versionen av BMC (Baseboard Management Controller) uppgraderas från 3.32.32.32 till 3.36.36.36 under fabriks installation för att åtgärda inkompatibilitet med nyare Dell-enheter för strömförsörjning.
- Den här versionen stöder IoT Edge 1.0.9.3 på Azure Stack Edge-enheter.

## <a name="known-issues-in-this-release"></a>Kända problem i den här versionen

Inga nya problem har antecknats för den här versionen. Alla eventuella problem som har antecknats har överförts från tidigare versioner. Om du vill se en lista över kända problem går du till [kända problem i ga-versionen](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Nästa steg

- [Förbered för att distribuera Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)