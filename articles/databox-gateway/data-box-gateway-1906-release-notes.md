---
title: Azure Data Box Gateway & Azure Data Box Edge 1906 viktig information | Microsoft Docs
description: Beskriver kritiska öppna problem och lösningar för Azure Data Box Gateway och Azure Data Box Edge som kör 1906-versionen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 51f4995ffd6a86022d95df15ae0eb7694d878c60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582839"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Viktig information om Azure Data Box Edge och Azure Data Box Gateway 1906

I följande viktig information identifieras kritiska öppna problem och de lösta problemen för 1906-versionen för Azure Data Box Edge och Azure Data Box Gateway. 

Viktig information uppdateras kontinuerligt och eftersom kritiska problem som kräver en lösning upptäcks, läggs de till. Innan du distribuerar Data Box Edge/Data Box Gateway bör du gå igenom informationen i viktig information.

Den här versionen motsvarar program varu versionerna:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> Uppdatering 1906 kan endast tillämpas på Data Box Edge enheter som kör allmän tillgänglighet (GA) eller 1905-versionen av program varan.

## <a name="whats-new"></a>Nyheter

- **Fel korrigering i arbets flödet för återställnings nyckel hantering** – i den tidigare versionen uppstod ett fel på grund av att återställnings nyckeln inte användes. Den här buggen har åtgärd ATS i den här versionen. Vi rekommenderar starkt att du installerar den här uppdateringen eftersom återställnings nyckeln låter dig återställa data på enheten, i händelse av att enheten inte startar. Mer information finns i så här [sparar du återställnings nyckeln när du distribuerar data Box Edge eller data Box Gateway](../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- **Förbättringar av FPGA-loggning (fält programmerbara grind mat ris)** – starta 1905 version, loggning och aviserings förbättringar relaterade till FPGA gjordes. Detta fortsätter att vara en nödvändig uppdatering för Data Box Edge om du använder Edge Compute-funktionen med FPGA. Mer information finns i så här [omvandlar du data med Edge Compute på din data Box Edge](../databox-online/azure-stack-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Kända problem i GA-versionen

Inga nya problem har antecknats för den här versionen. Alla eventuella problem som har antecknats har överförts från tidigare versioner. Om du vill se en lista över kända problem går du till [kända problem i ga-versionen](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Nästa steg

- [Förbereda distributionen av Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Förbered att distribuera Azure Data Box Edge](../databox-online/azure-stack-edge-deploy-prep.md)
