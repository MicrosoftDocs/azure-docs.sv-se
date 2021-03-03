---
title: Drifts modell för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Drifts modell för SAP HANA på Azure (stora instanser).
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
ms.openlocfilehash: 9660540c60c6b3a734dc507a6056af9f1f8d6dc9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675566"
---
# <a name="operations-model-and-responsibilities"></a>Driftmodell och ansvarsområden

Tjänsten som tillhandahålls med SAP HANA på Azure (stora instanser) är anpassad till Azure IaaS Services. Du får en instans av en HANA stor instans med ett installerat operativ system som är optimerat för SAP HANA. Precis som med virtuella Azure IaaS-datorer, de flesta av aktiviteterna för att skärpa operativ systemet, installera ytterligare program vara, installera HANA, använda operativ systemet och HANA och uppdatera operativ systemet och HANA är ditt ansvar. Microsoft tvingar inte uppdateringar av operativ system eller HANA-uppdateringar.

![Ansvar för SAP HANA på Azure (stora instanser)](./media/hana-overview-architecture/image2-responsibilities.png)

Som du ser i diagrammet är SAP HANA på Azure (stora instanser) ett IaaS-erbjudande för flera innehavare. För det mesta är andelen av ansvaret i operativ systemets infrastrukturs gränser. Microsoft ansvarar för alla aspekter av tjänsten under operativ systemets rad. Du ansvarar för alla aspekter av tjänsten ovanför linjen. Operativ systemet är ditt ansvar. Du kan fortsätta att använda de flesta aktuella lokala metoder som du kan använda för efterlevnad, säkerhet, program hantering, bas och operativ system hantering. Systemen ser ut som om de är i ditt nätverk i alla avseenden.

Den här tjänsten är optimerad för SAP HANA, så det finns områden där du behöver arbeta med Microsoft för att kunna använda de underliggande infrastruktur funktionerna för bästa möjliga resultat.

Följande lista innehåller mer information om varje lager och dina ansvars områden:

**Nätverk**: alla interna nätverk för den stora instans stämpling som kör SAP HANA. Ditt ansvar omfattar åtkomst till lagring, anslutning mellan instanserna (för skalbara funktioner och andra funktioner), anslutning till landskap och anslutning till Azure där SAP-programlagret finns på virtuella datorer. Den innehåller även WAN-anslutning mellan Azure-datacenter för replikering av haveri beredskap. Alla nätverk partitioneras av klienten och har tjänst kvalitet som tillämpas.

**Lagring**: virtualiserad partitionerad lagring för alla volymer som krävs av SAP HANA-servrar, samt för ögonblicks bilder. 

**Servrar**: de dedikerade fysiska servrarna för att köra SAP HANA-databaser som tilldelats till klienter. Servrarna i typ I-klassen för SKU: er är maskin vara abstrakta. Med dessa typer av servrar samlas och underhålls Server konfigurationen i profiler, som kan flyttas från en fysisk maskin vara till en annan fysisk maskin vara. En sådan (manuell) flyttning av en profil efter åtgärder kan jämföras med en bit till Azure Service relagning. Servrarna i typ II-klassen SKU: er erbjuder inte sådan funktion.

**SDDC**: hanterings program varan som används för att hantera data Center som program varu definierade entiteter. Det gör att Microsoft kan pool resurser för skalnings-, tillgänglighets-och prestanda skäl.

**O/S**: det operativ system du väljer (SUSE Linux eller Red Hat Linux) som körs på servrarna. De OS-avbildningar som du medföljer tillhandahölls av den enskilda Linux-leverantören till Microsoft för att köra SAP HANA. Du måste ha en prenumeration på Linux-leverantören för den speciella SAP HANA-optimerade avbildningen. Du ansvarar för att registrera avbildningarna med OS-leverantören. 

Från överlämnande från Microsoft ansvarar du för eventuell ytterligare korrigering av Linux-operativsystemet. Den här uppdateringen innehåller ytterligare paket som kan behövas för att lyckas SAP HANA installationen och som inte ingår i den aktuella Linux-leverantören i SAP HANA optimerade OS-avbildningar. (Mer information finns i SAP: s HANA installations dokumentation och SAP-anteckningar.) 

Du är ansvarig för OS-korrigering på grund av fel eller optimering av operativ systemet och dess driv rutiner i förhållande till den aktuella server maskin varan. Du ansvarar också för säkerhets-eller funktions korrigeringar av operativ systemet. 

Ditt ansvar omfattar även övervakning och kapacitets planering av:

- Användning av processor resurser.
- Minnes användning.
- Disk volymer relaterade till ledigt utrymme, IOPS och latens.
- Nätverks volym trafik mellan HANA stor instans och SAP-program skiktet.

Den underliggande infrastrukturen i HANA stor instans tillhandahåller funktioner för säkerhets kopiering och återställning av OS-volymen. Det är också ditt ansvar att använda den här funktionen.

**Mellanprogram**: SAP HANA-instansen, främst. Administration, åtgärder och övervakning är ditt ansvar. Du kan använda den tillhandahållna funktionen för att använda lagrings ögonblicks bilder för säkerhets kopiering och återställning och haveri beredskap. Dessa funktioner tillhandahålls av infrastrukturen. Dina ansvars områden omfattar också utformning av hög tillgänglighet eller haveri beredskap med dessa funktioner, med hjälp av dem och övervakning för att avgöra om lagrings ögonblicks bilder har körts.

**Data**: dina data som hanteras av SAP HANA och andra data, till exempel säkerhets kopierings filer som finns på volymer eller fil resurser. Ditt ansvar omfattar att övervaka ledigt disk utrymme och hantera innehållet på volymerna. Du ansvarar också för att övervaka lyckad körning av säkerhets kopior av disk volymer och ögonblicks bilder av lagring. Microsoft har ansvarat för att utföra datareplikering till haveri beredskaps platser.

**Program:** SAP-programinstanserna eller, om det gäller icke-SAP-program, program lagret i dessa program. Dina ansvars områden omfattar distribution, administration, åtgärder och övervakning av dessa program. Du ansvarar för kapacitets planering av förbrukning av processor resurser, minnes förbrukning, Azure Storage användning och användning av nätverks bandbredd i virtuella nätverk. Du ansvarar också för kapacitets planering för resursförbrukning från virtuella nätverk till SAP HANA på Azure (stora instanser).

**WAN**: de anslutningar som du upprättar från lokala datorer till Azure-distributioner för arbets belastningar. Alla kunder med en stor instans av HANA använder Azure-ExpressRoute för anslutning. Den här anslutningen är inte en del av lösningen SAP HANA på Azure (stora instanser). Du ansvarar för installationen av den här anslutningen.

**Arkiv**: du kanske föredrar att arkivera kopior av data genom att använda dina egna metoder i lagrings konton. Arkivering kräver hantering, efterlevnad, kostnader och åtgärder. Du ansvarar för att skapa arkiverade kopior och säkerhets kopior på Azure och lagra dem på ett kompatibelt sätt.

Se [SLA för SAP HANA på Azure (stora instanser)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Nästa steg**
- Referera [SAP HANA (stora instanser) arkitektur i Azure](hana-architecture.md)
