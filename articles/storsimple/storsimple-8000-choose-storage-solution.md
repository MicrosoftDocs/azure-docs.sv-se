---
title: Alternativ för dataöverföring till Azure med hjälp av en | Microsoft Docs
description: Lär dig hur du väljer rätt installation för lokal dataöverföring till Azure mellan Data Box-enhet Edge, Azure File Sync och StorSimple 8000.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 1345486e6bda7501a862612652b722b0075e190f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791179"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Jämföra StorSimple med dataöverföringsalternativen Azure File Sync och Data Box Edge 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Det här dokumentet innehåller en översikt över alternativen för lokal dataöverföring till Azure och jämför: Data Box-enhet Edge jämfört med Azure File Sync storsimple 8000-serien.

- **[Data Box-enhet Edge](../databox-online/azure-stack-edge-overview.md)** – Data Box-enhet Edge är en lokal nätverksenhet som flyttar data till och från Azure och har AI-aktiverad Edge-beräkning för förbearbetning av data under uppladdning. Data Box Gateway är en virtuell version av enheten med samma funktioner för dataöverföring.
- **[Azure File Sync](../storage/file-sync/file-sync-deployment-guide.md)** – Azure File Sync kan användas för att centralisera organisationens filresurser i Azure Files, samtidigt som du behåller flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Allmän tillgänglighet för Azure File Sync presenterades tidigare under 2018.
- **[StorSimple](./storsimple-overview.md)** – StorSimple är en hybridenhet som hjälper företag att konsolidera sin lagringsinfrastruktur för primär lagring, dataskydd, arkivering och haveriberedskap på en enda lösning genom nära integrering med Azure Storage. Produktlivscykeln för StorSimple finns [här.](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)

## <a name="comparison-summary"></a>Jämförelsesammanfattning

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|**Översikt**     |Nivåindelad hybridlagring och arkivering|Allmän filserverlagring med molnnivåindelning och synkronisering av flera webbplatser.  |Lagringslösning för att förbe bearbeta data och skicka dem via nätverk till Azure.        |
|**Scenarier**    |Filserver, arkivering, mål för säkerhetskopiering |Filserver, arkivering (flera webbplatser)   |Dataöverföring, förbearbetning av data inklusive ML-inferens, IoT, arkivering    |
|**Edge-beräkning** |Inte tillgänglig |Inte tillgänglig |Stöder körning av containrar med Azure IoT Edge    |
|**Formfaktor**  |Fysisk enhet   |Agent installerad på Windows Server |Fysisk enhet   |
|**Maskinvara**     |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten | Kunden har angett |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten  |
|**Dataformat**  |Anpassat format   |Filer         |Blobar eller filer    |
|**Protokollstöd** |iSCSI          |SMB, NFS    | SMB eller NFS      |
|**Prissättning**      |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Nästa steg

- Läs mer [om Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) och [Azure Data Box Gateway](../databox-gateway/data-box-gateway-overview.md)
- Läs mer [om Azure File Sync](../storage/file-sync/file-sync-deployment-guide.md)