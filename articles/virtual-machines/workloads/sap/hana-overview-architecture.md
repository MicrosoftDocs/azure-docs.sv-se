---
title: Översikt över SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Översikt över hur du distribuerar SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/04/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d941bd9735149d08bfbd63ec97337dd7a3bac43b
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226666"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Vad är SAP HANA på stora Azure-instanser?

SAP HANA på Azure (stora instanser) är en unik lösning till Azure. Förutom att tillhandahålla virtuella datorer för att distribuera och köra SAP HANA, erbjuder Azure dig möjligheten att köra och distribuera SAP HANA på Bare Metal-servrar som är dedikerade till dig. SAP HANA på Azure (stora instanser) lösning bygger på icke-delad maskin vara som är tilldelad värd/server utan operativ system. Server maskin varan är inbäddad i större stämplar som innehåller beräknings-/Server-, nätverks-och lagrings infrastruktur. SAP HANA på Azure (stora instanser) erbjuder olika Server-SKU: er eller storlekar. Enheter kan ha 36 Intel CPU-kärnor och 768 GB minne och gå upp till enheter som har upp till 480 Intel CPU-kärnor och upp till 24 TB minne.

Kund isolering inom infrastrukturens stämpel utförs i klient organisationer, som ser ut så här:

- **Nätverk**: isolering av kunder i infrastruktur stack via virtuella nätverk per kundtilldelad klient. En klient tilldelas till en enskild kund. En kund kan ha flera klienter. Nätverks isoleringen av klienter förbjuder nätverkskommunikation mellan klienter på infrastrukturens stampnivå, även om klienterna tillhör samma kund.
- **Lagrings komponenter**: isolering genom virtuella lagrings datorer som har tilldelade lagrings volymer. Lagrings volymer kan endast tilldelas till en virtuell lagrings dator. En virtuell lagrings dator tilldelas exklusivt till en enda klient i infrastrukturs Tacken. Det innebär att lagrings volymer som tilldelas till en virtuell lagrings dator kan nås i en enda enskild och relaterad klient. De är inte synliga mellan de olika distribuerade klient organisationerna.
- **Server eller värd**: en server eller en värd enhet delas inte mellan kunder eller klienter. En server eller värd som har distribuerats till en kund är en atomisk dator som är tilldelad en enda klient. *Ingen* maskinvarubaserad partitionering eller mjuk partitionering används som kan leda till att du delar en värd eller en server med en annan kund. Lagrings volymer som är kopplade till den virtuella lagrings datorn för den angivna klienten monteras på en sådan server. En klient kan ha en till många Server enheter med olika SKU: er som tilldelats exklusivt.
- I en SAP HANA på en Azure-dator (stor instans) är många olika klienter distribuerade och isolerade mot varandra via klient koncepten för nätverk, lagring och beräknings nivåer. 


Dessa servrar för Bare Metal-enheter stöds endast för att köra SAP HANA. Lagret för SAP-program eller mellanliggande arbets belastning körs på virtuella datorer. De infrastruktur stämplar som kör SAP HANA på Azure-enheter (stora instanser) är anslutna till Azure Network Services-arbetsben. På så sätt tillhandahålls låg latens anslutning mellan SAP HANA på Azure-enheter (stora instanser) och virtuella datorer.

Från och med januari 2021 skiljer vi mellan två olika revisioner av HANA-stora instanser och distributions plats:

- "Revision 3" (rev 3): är de stämplar som har gjorts tillgängliga för kunden att distribuera före 2019 juli
- "Revision 4" (rev 4): ny stämpel-design som distribueras i nära närhet till Azure VM-värdar och som hittills är frigjord i Azure-regionerna av:
    -  USA, västra 2 
    -  East US
    -  Östra 2; USA (över två Tillgänglighetszoner)
    -  Södra centrala USA (över två Tillgänglighetszoner)
    -  Europa, västra
    -  Europa, norra


Det här dokumentet är ett av flera dokument som behandlar SAP HANA på Azure (stora instanser). Det här dokumentet innehåller en introduktion till den grundläggande arkitekturen, ansvars områden och tjänster som tillhandahålls av lösningen. Hög nivå funktioner i lösningen diskuteras också. För de flesta andra områden, t. ex. nätverk och anslutning, innehåller fyra andra dokument information och detaljerad information. Dokumentationen för SAP HANA på Azure (stora instanser) tar inte upp aspekter av SAP NetWeaver-installationen eller distributionerna av SAP NetWeaver på virtuella datorer. SAP NetWeaver på Azure beskrivs i separata dokument som finns i samma Azure-dokumentations behållare. 


De olika dokumenten i den här vägledningen HANA-stor instans tar upp följande områden:

- [Översikt över SAP HANA (stora instanser) och arkitektur på Azure](hana-overview-architecture.md)
- [SAP HANA (stora instanser) infrastruktur och anslutning i Azure](hana-overview-infrastructure-connectivity.md)
- [Installera och konfigurera SAP HANA (stora instanser) i Azure](hana-installation.md)
- [SAP HANA (stora instanser) hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md)
- [SAP HANA (stora instanser) fel sökning och övervakning i Azure](troubleshooting-monitoring.md)
- [Hög tillgänglighet har kon figurer ATS i SUSE med hjälp av STONITH](./ha-setup-with-stonith.md)
- [Säkerhets kopiering och återställning av OS för typ II SKU: er av revision 3-stämplar](./os-backup-type-ii-skus.md)
- [Få rabatt på SAP HANA – stora instanser med en Azure-reservation](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md)

**Nästa steg**
- Se mer information [om villkoren](hana-know-terms.md)
