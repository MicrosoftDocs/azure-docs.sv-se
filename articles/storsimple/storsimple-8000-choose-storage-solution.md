---
title: Alternativ för data överföring till Azure med hjälp av en apparat | Microsoft Docs
description: Lär dig hur du väljer rätt installation för lokal data överföring till Azure mellan Data Box Edge, Azure File Sync och StorSimple 8000-serien.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 4fc92146d6f076287fe37f64734bb07e8250792b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98882220"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Jämföra StorSimple med dataöverföringsalternativen Azure File Sync och Data Box Edge 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Det här dokumentet innehåller en översikt över alternativ för lokal data överföring till Azure, som jämför: Data Box Edge vs. Azure File Sync vs. StorSimple 8000-serien.

- **[Data Box Edge](../databox-online/azure-stack-edge-overview.md)** – data Box Edge är en lokal nätverks enhet som flyttar data till och från Azure och har AI-aktiverad Edge-beräkning för att Förbearbeta data under uppladdningen. Data Box Gateway är en virtuell version av enheten med samma funktioner för dataöverföring.
- **[Azure File Sync](../storage/files/storage-sync-files-deployment-guide.md)** – Azure File Sync kan användas för att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibilitet, prestanda och kompatibilitet för en lokal fil server. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Allmän tillgänglighet för Azure File Sync annonserades tidigare i 2018.
- **[StorSimple](./storsimple-overview.md)** – StorSimple är en hybrid enhet som hjälper företag att konsolidera sin lagrings infrastruktur för primär lagring, data skydd, arkivering och haveri beredskap på en enskild lösning genom att noggrant integrera med Azure Storage. Produktens livs cykel för StorSimple hittar du [här](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Jämförelse Sammanfattning

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|**Översikt**     |Nivå hybrid lagring och arkivering|Allmän fil Server lagring med moln nivåer och synkronisering på flera platser.  |Lagrings lösning för att Förbearbeta data och skicka den via nätverket till Azure.        |
|**Scenarier**    |Fil server, arkivering, säkerhets kopierings mål |Fil server, arkivering (flera platser)   |Data överföring, för bearbetning av data, inklusive ML inferencing, IoT, arkivering    |
|**Edge-beräkning** |Inte tillgänglig |Inte tillgänglig |Stöder körning av behållare med hjälp av Azure IoT Edge    |
|**Form faktor**  |Fysisk enhet   |Agent som är installerad på Windows Server |Fysisk enhet   |
|**Maskinvara**     |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten | Kunden tillhandahöll |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten  |
|**Dataformat**  |Anpassat format   |Filer         |Blobbar eller filer    |
|**Protokoll stöd** |iSCSI          |SMB, NFS    | SMB eller NFS      |
|**Prissättning**      |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) och [Azure Data Box Gateway](../databox-gateway/data-box-gateway-overview.md)
- Läs mer om [Azure File Sync](../storage/files/storage-sync-files-deployment-guide.md)