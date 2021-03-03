---
title: Hög tillgänglighet och haveri beredskap för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Upprätta hög tillgänglighet och planera för haveri beredskap för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e1fd4d53a642052c55bbe5d31bf80133ae89d35
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675455"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA – stora instanser hög tillgänglighet och haveri beredskap på Azure 

>[!IMPORTANT]
>Den här dokumentationen ersätter inte SAP HANA administrations dokumentation eller SAP-anteckningar. Det är förväntat att läsaren har en solid förståelse för och expertis i SAP HANA administration och åtgärder, särskilt i avsnitten om säkerhets kopiering, återställning, hög tillgänglighet och haveri beredskap.

Det är viktigt att du följer de steg och processer som har vidtagits i din miljö och med dina HANA-versioner och-versioner. Vissa processer som beskrivs i den här dokumentationen är förenklade för en bättre allmän förståelse och är inte avsedda att användas som detaljerade steg för att utföra drifts handböcker. Om du vill skapa åtgärds handböcker för dina konfigurationer måste du testa och träna dina processer och dokumentera processerna som är relaterade till dina speciella konfigurationer. 


Hög tillgänglighet och haveri beredskap (DR) är viktiga aspekter på att köra verksamhets kritiska SAP HANA på Azure (stora instanser)-servern. Det är viktigt att arbeta med SAP, din system integrerare eller Microsoft för att bygga och implementera rätt strategier för hög tillgänglighet och katastrof återställning. Det är också viktigt att tänka på återställnings punkt mål och återställnings tid, vilket är särskilt för din miljö.

Microsoft stöder vissa SAP HANA funktioner med hög tillgänglighet med HANA-stora instanser. Dessa funktioner är:

- **Storage-replikering**: lagrings systemets möjlighet att replikera alla data till en annan Hana stor instans stämpel i en annan Azure-region. SAP HANA fungerar oberoende av den här metoden. Den här funktionen är standard mekanismen för haveri beredskap som erbjuds för HANA-stora instanser.
- **Hana-systemreplikering**: [replikeringen av alla data i SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) till ett separat SAP HANA system. Tids målet för återställnings tiden minimeras genom datareplikering med jämna mellanrum. SAP HANA stöder asynkrona, synkrona InMemory-och synkrona lägen. Synkront läge används endast för SAP HANA system som är inom samma data Center eller mindre än 100 km. Med den aktuella designen av HANA-instanser med stora instanser kan HANA-systemreplikering endast användas för hög tillgänglighet inom en region. HANA-systemreplikering kräver en omvänd proxy-eller vägvals komponent från tredje part för haveri beredskap i en annan Azure-region. 
- **Automatisk redundans för värd**: en lokal fel återställnings lösning för SAP HANA som är ett alternativ till Hana-systemreplikering. Om huvudnoden blir otillgänglig, konfigurerar du en eller flera vänte läges SAP HANA noder i skalbart läge och SAP HANA växlar automatiskt över till en nod i vänte läge.

SAP HANA på Azure (stora instanser) erbjuds i två Azure-regioner i fyra politiska områden (USA, Australien, Europa och Japan). Två regioner i ett politiskt område som är värd för HANA-stora instanser är anslutna till separata dedikerade nätverks kretsar. Dessa används för att replikera lagrings ögonblicks bilder för att tillhandahålla metoder för haveri beredskap. Replikeringen är inte upprättad som standard men har kon figurer ATS för kunder som beställer katastrof återställnings funktioner. Storage Replication är beroende av användningen av ögonblicks bilder av lagring för HANA-stora instanser. Det går inte att välja en Azure-region som en DR-region som finns i ett annat politiskt område. 

I följande tabell visas de metoder och kombinationer som stöds för närvarande och haveri beredskap:

| Scenario som stöds i HANA-stora instanser | Alternativ för hög tillgänglighet | Katastrof återställnings alternativ | Kommentarer |
| --- | --- | --- | --- |
| Enkel nod | Inte tillgängligt. | Dedikerad DR-konfiguration.<br /> Konfiguration av multifunktions-DR. | |
| Automatisk redundans för värd: skala ut (med eller utan vänte läge)<br /> inklusive 1 + 1 | Möjligt med vänte läge som tar den aktiva rollen.<br /> HANA styr roll växeln. | Dedikerad DR-konfiguration.<br /> Konfiguration av multifunktions-DR.<br /> DR-synkronisering med Storage-replikering. | HANA-volym uppsättningar är anslutna till alla noder.<br /> DR-platsen måste ha samma antal noder. |
| HANA-systemreplikering | Möjligt med primär eller sekundär installation.<br /> Sekundär flyttar till primär roll i ett failover-fall.<br /> HANA-systemreplikering och OS-kontroll av redundans. | Dedikerad DR-konfiguration.<br /> Konfiguration av multifunktions-DR.<br /> DR-synkronisering med Storage-replikering.<br /> DR med hjälp av HANA-systemreplikering är ännu inte möjligt utan komponenter från tredje part. | En separat uppsättning disk volymer är anslutna till varje nod.<br /> Endast disk volymer för sekundär replik på produktions platsen replikeras till DR-platsen.<br /> En uppsättning volymer krävs på DR-platsen. | 

En dedikerad DR-installation är där den stora instans enheten HANA i DR-platsen inte används för att köra andra arbets belastnings-eller icke-produktionssystem. Enheten är passiv och distribueras endast om redundansväxlingen utförs. Den här installationen är dock inte ett önskat val för många kunder.

Se [HLI-scenarier som stöds](hana-supported-scenario.md) för att lära dig mer om lagrings-och Ethernet-information för din arkitektur.

> [!NOTE]
> [SAP HANA MCOD-distributioner](https://launchpad.support.sap.com/#/notes/1681092) (flera Hana-instanser på en enhet) som överlappande scenarier fungerar med metoderna ha och Dr som anges i tabellen. Ett undantag är att använda HANA-systemreplikering med ett automatiskt redundanskluster baserat på pacemaker. Sådana fall stöder bara en HANA-instans per enhet. För [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) -distributioner fungerar endast icke-lagrings ha och Dr-metoder om fler än en klient distribueras. När en klient har distribuerats är alla metoder som anges giltiga.  

Ett konfigurations program för flera ändamål är där den stora instans enheten HANA på DR-platsen kör en arbets belastning som inte är en produktion. Om det är en katastrof stänger du av icke-produktions systemet, monterar lagrings replikerade (ytterligare) volym uppsättningar och startar sedan produktion HANA-instansen. De flesta kunder som använder funktionen HANA stor instansen haveri beredskap använder den här konfigurationen. 


Du hittar mer information om SAP HANA hög tillgänglighet i följande SAP-artiklar: 

- [Fakta blad om hög tillgänglighet SAP HANA](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA administrations guide](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy-video om SAP HANA system replikering](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Stöd för SAP-support #1999880 – vanliga frågor och svar om SAP HANA systemreplikering](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [Stöd för SAP-support #2165547 – SAP HANA säkerhetskopiera och återställa inom SAP HANA systemets replikeringspartner](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Stöd för SAP-support #1984882 – använda SAP HANA systemreplikering för maskin varu utbyte med minst/noll stillestånds tid](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Nätverks överväganden för haveri beredskap med stora instanser av HANA

Om du vill dra nytta av Disaster Recovery-funktionen hos HANA-stora instanser måste du utforma nätverks anslutningen till de två Azure-regionerna. Du behöver en Azure ExpressRoute-krets-anslutning lokalt i din huvudsakliga Azure-region och en annan krets anslutning från lokal plats till din katastrof återställnings region. Det här måttet avser en situation där det finns ett problem i en Azure-region, inklusive en Microsoft MSEE: N-plats (Enterprise Edge router).

Som ett andra mått kan du ansluta alla virtuella Azure-nätverk som ansluter till SAP HANA på Azure (stora instanser) i en region till en ExpressRoute-krets som ansluter HANA-stora instanser i den andra regionen. Med den här *kors anslutningen* kan tjänster som körs på ett virtuellt Azure-nätverk i region 1 ansluta till Hana-stora instans enheter i region 2 och det andra sättet runt. Det här måttet åtgärdar ett fall där endast en av MSEE: N-platserna som ansluter till din lokala plats med Azure går offline.

Följande bild illustrerar en elastisk konfiguration för katastrof återställnings fall:

![Optimal konfiguration för haveri beredskap](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Övriga krav med HANA stora instanser lagrings replikering för haveri beredskap

Förutom de föregående kraven för en haveri beredskaps installation med HANA-stora instanser måste du:

- Beställ SAP HANA på Azure (stora instanser) SKU: er av samma storlek som dina produktions-SKU: er och distribuera dem i Disaster Recovery-regionen. I de aktuella kund distributionerna används dessa instanser för att köra HANA-instanser som inte är produktion. De här konfigurationerna kallas för konfiguration av *multianvändnings Dr*.   
- Beställ ytterligare lagrings utrymme på DR-platsen för var och en av dina SAP HANA på Azure (stora instanser) SKU: er som du vill återställa i haveri beredskaps webbplatsen. Genom att köpa ytterligare lagrings utrymme kan du allokera lagrings volymerna. Du kan allokera de volymer som är målet för lagringsprovidern från din produktions-Azure-region till Azure-regionen haveri beredskap.
- I fall där du har HSR-installationen på primär och du konfigurerar lagring baserad på DR-platsen måste du köpa ytterligare lagrings utrymme på DR-platsen så att både primära och sekundära Zondata replikeras till DR-platsen.

  **Nästa steg**
- Referera [till säkerhets kopiering och återställning](hana-backup-restore.md).













