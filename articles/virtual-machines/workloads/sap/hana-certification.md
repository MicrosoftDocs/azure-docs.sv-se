---
title: Certifiering av SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Certifiering av SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8d4781ad9ca2a30e8523d0e2446dc3903a7cb8a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668856"
---
# <a name="certification"></a>Certifiering

Förutom NetWeaver-certifieringen kräver SAP en speciell certifiering för SAP HANA för att stödja SAP HANA på vissa infrastrukturer, till exempel Azure IaaS.

Core SAP-anteckningen på NetWeaver, och till en examen SAP HANA-certifiering, är [SAP obs #1928533 – SAP-program på Azure: produkter som stöds och Azure VM-typer](https://launchpad.support.sap.com/#/notes/1928533).

Certifierings posterna för SAP HANA på Azure-enheter (stora instanser) finns på webbplatsen [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . 

SAP HANA på Azure-typer (stora instanser) som hänvisas till i SAP HANA Certified IaaS Platforms site, ger Microsoft och SAP-kunder möjlighet att distribuera stora SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA eller andra SAP HANA arbets belastningar i Azure. Lösningen baseras på den SAP-HANA-certifierade dedikerade maskin varu stämpeln ([SAP HANA anpassad Data Center integration – TDI](https://scn.sap.com/docs/DOC-63140)). Om du kör en SAP HANA TDI-konfigurerad lösning fungerar alla SAP HANA-baserade program (t. ex. SAP Business Suite på SAP HANA, SAP BW på SAP HANA, S4/HANA och BW4/HANA) i maskin varu infrastrukturen.

Den här lösningen har en förmån jämfört med att köra SAP HANA i virtuella datorer. Det ger mycket större minnes volymer. Om du vill aktivera den här lösningen måste du förstå följande viktiga aspekter:

- SAP-program lagret och icke-SAP-program körs på virtuella datorer som finns i de vanliga datorerna för Azure-maskinvaran.
- Kundens lokala infrastruktur, data Center och program distributioner är anslutna till moln plattformen via ExpressRoute (rekommenderas) eller ett virtuellt privat nätverk (VPN). Active Directory och DNS utökas också till Azure.
- SAP HANA databas instansen för HANA-arbetsbelastningar körs på SAP HANA på Azure (stora instanser). Den stora instans stämpeln är ansluten till Azure-nätverk, så program som körs i virtuella datorer kan interagera med HANA-instansen som körs i HANA stor instans.
- Maskin vara för SAP HANA på Azure (stora instanser) är dedikerad maskin vara som tillhandahålls i en IaaS med SUSE Linux Enterprise Server eller Red Hat Enterprise Linux förinstallerad. Precis som med virtuella datorer är du ansvarig för ytterligare uppdateringar och underhåll av operativ systemet.
- Ditt ansvar är att installera HANA eller ytterligare komponenter som krävs för att köra SAP HANA på enheter av HANA stor instans. Alla pågående åtgärder och administration av SAP HANA på Azure är också ditt ansvar.
- Förutom de lösningar som beskrivs här kan du installera andra komponenter i din Azure-prenumeration som ansluter till SAP HANA på Azure (stora instanser). Exempel är komponenter som möjliggör kommunikation med eller direkt till SAP HANA-databasen, till exempel hopp servrar, RDP-servrar, SAP HANA Studio, SAP Data Services för SAP BI-scenarier eller lösningar för nätverks övervakning.
- Som i Azure erbjuder en stor instans av HANA stor tillgänglighet stöd för hög tillgänglighet och katastrof återställning.

**Nästa steg**
- Se [tillgängliga SKU: er för HLI](hana-available-skus.md) 