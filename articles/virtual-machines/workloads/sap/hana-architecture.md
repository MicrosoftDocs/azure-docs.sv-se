---
title: Arkitektur för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Arkitektur för hur du distribuerar SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f45b84360c941e879461500a033ada3642fd5cbb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671610"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>SAP HANA-arkitektur (stora instanser) på Azure

På hög nivå har SAP HANA på Azure-lösningen (stora instanser) det SAP-program skikt som finns på virtuella datorer. Databas lagret finns på den SAP TDI-konfigurerade maskin varan som finns i en stor instans stämpel i samma Azure-region som är ansluten till Azure-IaaS.

> [!NOTE]
> Distribuera SAP-program skiktet i samma Azure-region som SAP-DBMS-skiktet. Den här regeln är väl dokumenterad i publicerad information om SAP-arbetsbelastningar på Azure. 

Den övergripande arkitekturen i SAP HANA på Azure (stora instanser) tillhandahåller en SAP TDI-certifierad maskin varu konfiguration, som är en icke-virtualiserad, Bare Metal-server med hög prestanda för SAP HANA-databasen. Det ger också möjlighet och flexibilitet i Azure att skala resurser för SAP-programlagret så att de passar dina behov.

![Arkitektur översikt över SAP HANA på Azure (stora instanser)](./media/hana-overview-architecture/image1-architecture.png)

Arkitekturen som visas är uppdelad i tre delar:

- **Höger**: visar en lokal infrastruktur som kör olika program i Data Center så att slutanvändare kan komma åt LOB-program, till exempel SAP. Vi rekommenderar att den här lokala infrastrukturen är ansluten till Azure med [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center**: visar Azure-IaaS och i det här fallet använder virtuella datorer som värd för SAP eller andra program som använder SAP HANA som ett DBMS-system. Mindre HANA-instanser som fungerar med det minne som de virtuella datorerna tillhandahåller distribueras i virtuella datorer tillsammans med deras program lager. Mer information om virtuella datorer finns i [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/).

   Azure nätverks tjänster används för att gruppera SAP-system tillsammans med andra program i virtuella nätverk. Dessa virtuella nätverk ansluter till lokala system och SAP HANA på Azure (stora instanser).

   För SAP NetWeaver-program och databaser som stöds för körning i Azure, se [SAP support Note #1928533 – SAP-program på Azure: produkter och typer av virtuella Azure-datorer som stöds](https://launchpad.support.sap.com/#/notes/1928533). För dokumentation om hur du distribuerar SAP-lösningar på Azure, se:

  -  [Använda SAP på virtuella Windows-datorer](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Använd SAP-lösningar på Azure Virtual Machines](get-started.md)

- **Left**: visar SAP HANA TDI-certifierad maskin vara i den stora Azure-instansnamnet. De stora instanser av HANA är anslutna till de virtuella nätverken i Azure-prenumerationen genom att använda samma teknik som anslutningen från lokala enheter till Azure. Från maj till 2019 har en optimering lanserats som gör det möjligt att kommunicera mellan de stora instanser av HANA och de virtuella Azure-datorerna utan medverkan av ExpressRoute-gatewayen. Den här optimeringen heter ExpressRoute snabb sökväg visas i den här arkitekturen (röda linjer). 

Den stora Azure-instansnamnet kombinerar följande komponenter:

- **Data behandling**: servrar som baseras på olika generations Intel Xeon-processorer och som ger den nödvändiga dator kapaciteten och som är SAP HANA certifierade.
- **Nätverk**: en enhetlig höghastighets nätverks infrastruktur resurs som sammankopplar data bearbetnings-, lagrings-och LAN-komponenter.
- **Lagring**: en lagrings infrastruktur som nås via en enhetlig nätverks infrastruktur resurs. Vilken lagrings kapacitet som anges beror på den aktuella SAP HANA på Azure (stora instanser) konfiguration som distribueras. Mer lagrings kapacitet är tillgängligt ytterligare en månads kostnad.

Inom infrastrukturen för flera innehavare av den stora instans stämpeln distribueras kunderna som isolerade klienter. Vid distribution av klienten namnger du en Azure-prenumeration i din Azure-registrering. Den här Azure-prenumerationen är den som den stora HANA-instansen debiteras mot. Dessa klienter har en 1:1-relation till Azure-prenumerationen. För ett nätverk är det möjligt att komma åt en HANA-stor instans enhet som distribuerats i en klient i en Azure-region från olika virtuella nätverk som tillhör olika Azure-prenumerationer. Dessa Azure-prenumerationer måste tillhöra samma Azure-registrering. 

Precis som med virtuella datorer erbjuds SAP HANA på Azure (stora instanser) i flera Azure-regioner. Om du vill erbjuda haveri beredskap kan du välja att välja. Olika stora instans stämplar inom en geo-politisk region är anslutna till varandra. Till exempel är HANA stora instanser i västra USA och östra USA anslutna via en dedikerad nätverks länk för replikering av haveri beredskap. 

Precis som du kan välja mellan olika typer av virtuella datorer med Azure Virtual Machines kan du välja mellan olika SKU: er av HANA stor instans som är skräddarsydda för olika arbets belastnings typer för SAP HANA. SAP tillämpar förhållandet mellan förhållandet mellan minne och processor för olika arbets belastningar baserat på Intel processor generationer. I följande tabell visas de tillgängliga SKU-typerna.

Du hittar tillgängliga SKU: [er för HLI](hana-available-skus.md).

**Nästa steg**
- Se [SAP HANA (stora instanser) nätverks arkitektur](hana-network-architecture.md)
