---
title: Omvärdering av stordator på Azure Virtual Machines
description: Revara värd för dina stordator arbets belastningar, till exempel IBM Z-baserade system med virtuella datorer (VM) på Microsoft Azure.
services: virtual-machines
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 7ae20b484b4c22c19b856c237d85efe31dc01407
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553552"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Omvärdering av stordator på Azure Virtual Machines

Genom att migrera arbets belastningar från stordator miljöer till molnet kan du modernisera infrastrukturen och ofta Spara pengar. Många arbetsbelastningar kan överföras till Azure med bara mindre kodändringar, till exempel uppdatering av namn på databaser.

I allmänhet kan termen *stordatorer* betyda ett stort dator system. Mer specifikt är den enorma majoriteten som används för närvarande IBM-system Z-servrar eller IBM plug-kompatibla system som kör MVS, DOS, VSE, OS/390 eller Z/OS.

En virtuell Azure-dator (VM) används för att isolera och hantera resurser för ett visst program på en enda instans. Stordatorer som IBM z/OS använder logiska partitioner (LPARS) för detta ändamål. En stordator kan använda en LPAR för en CICS-region med tillhör ande COBOL-program och en separat LPAR för IBM DB2-databasen. Ett typiskt [n-Tier-program på Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) distribuerar virtuella Azure-datorer till ett virtuellt nätverk som kan segmenteras i undernät för varje nivå.

Virtuella Azure-datorer kan köra stordator-emuleringsklienter och kompilatorer som stöder lyft och Skift-scenarier. Utveckling och testning är ofta bland de första arbets belastningarna som migreras från en stordator till en Azure dev/test-miljö. Vanliga Server komponenter som du kan emulera inkluderar OLTP-, batch-och data inmatnings system som visas i följande bild.

![Emuleringsklienter i Azure gör det möjligt att köra z/OS-baserade system.](media/01-overview.png)

Vissa stordator arbets belastningar kan migreras till Azure med relativt enkelt, medan andra kan vara värd för Azure med hjälp av en partner lösning. För detaljerad information om hur du väljer en partner lösning, kan [Azures stordatorer-flyttnings Center](https://azure.microsoft.com/migration/mainframe/) hjälpa dig.

## <a name="mainframe-migration"></a>Stordatormigrering

Rehost, återskapa, ersätta eller ta ur bruk? IaaS eller PaaS? För att fastställa rätt migrations strategi för stordator programmet, se Guide för [stordatorer](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)  i Azure Architecture Center.

## <a name="micro-focus-rehosting-platform"></a>Omvärdering av Micro Focus-plattform

Micro Focus Enterprise Server är en av de största plattformarna som är tillgängliga för stordator värdar. Du kan använda den för att köra dina arbets belastningar i z/OS på en billigare x86-plattform på Azure.

Så här kommer du igång:

- [Installera Enterprise Server och Enterprise Developer på Azure](./microfocus/set-up-micro-focus-azure.md)
- [Konfigurera CICS BankDemo för företags utvecklare på Azure](./microfocus/demo.md)
- [Kör Enterprise Server i en Docker-behållare på Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame på Azure

TmaxSoft OpenFrame är en populär lösning för att revärda stordatorer som används i hiss-och-Shift-scenarier. En OpenFrame-miljö i Azure är lämplig för utveckling, demonstrationer, testning eller produktions arbets belastningar.

Så här kommer du igång:

- [Kom igång med TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Ladda ned eBook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12,0

IBM Z-utveckling och test miljö (IBM zD&T) konfigurerar en icke-produktions miljö på Azure som du kan använda för utveckling, testning och demonstrationer av Z/OS-baserade program.

Emulerings miljön på Azure kan vara värd för olika typer av Z-instanser via programutvecklare kontrollerade distributioner (ADCDs). Du kan köra zD&T personal Edition, zD&T Parallel Sysplex och zD&T Enterprise Edition på Azure och Azure Stack.

Så här kommer du igång:

- [Konfigurera IBM zD&T 12,0 på Azure](./ibm/install-ibm-z-environment.md)
- [Konfigurera ADCD på zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2-pureScale på Azure

IBM DB2 pureScale-miljön tillhandahåller ett databas kluster för Azure. Den är inte identisk med den ursprungliga miljön, men den ger liknande tillgänglighet och skalning som IBM DB2 för z/OS som körs i en parallell Sysplex-installation.

För att komma igång, se [IBM DB2 pureScale på Azure](.//ibm/ibm-db2-purescale-azure.md).

## <a name="considerations"></a>Överväganden

När du migrerar stordator belastningar till Azure Infrastructure as a Service (IaaS) kan du välja mellan flera olika typer av skalbara dator resurser på begäran, inklusive virtuella Azure-datorer. Azure erbjuder en rad virtuella [Linux](../../linux/overview.md) -och [Windows](../../windows/overview.md) -datorer.

### <a name="compute"></a>Compute

Azure Compute Power jämför favorably med en stordator kapacitet. Om du tänker flytta en stordator till Azure ska du jämföra stordator måttet med 1 000 000-instruktioner per sekund (MIPS) till virtuella processorer. 

Lär dig hur du [flyttar stordator beräkning till Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Hög tillgänglighet och redundans

Azure erbjuder utfästelsebaserade service nivå avtal (service avtal). Multiple-nio tillgänglighet är standard, och service avtal kan optimeras med lokal eller geo-baserad replikering av tjänster. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

Med Azure IaaS, till exempel en virtuell dator, ger vissa systemfunktioner stöd för redundans, till exempel instanser av redundanskluster och tillgänglighets uppsättningar. När du använder Azure Platform as a Service (PaaS)-resurser hanterar plattformen redundans automatiskt. Exempel på detta är [Azure SQL Database](../../../azure-sql/database/sql-database-paas-overview.md) och [Azure Cosmos DB](../../../cosmos-db/introduction.md).

### <a name="scalability"></a>Skalbarhet

Stordatorer skalar vanligt vis upp, medan moln miljöerna skalas ut. Azure erbjuder ett utbud av [Linux](../../sizes.md) -och [Windows](../../sizes.md) -storlekar för att uppfylla dina behov. Molnet kan också skalas upp eller ned för att matcha exakta specifikationer för användare. Beräkning [av strömförsörjning](/azure/architecture/best-practices/auto-scaling) , lagring och tjänster på begäran under en användnings-baserad fakturerings modell.

### <a name="storage"></a>Storage

I molnet har du ett intervall med flexibla, skalbara lagrings alternativ och du betalar bara för det du behöver. [Azure Storage](../../../storage/common/storage-introduction.md) erbjuder en massivt skalbar objekt lagring för data objekt, en fil system tjänst för molnet, ett Reliable Messaging Store och ett NoSQL-lager. För virtuella datorer, hanterade och ohanterade diskar finns beständiga, säkra disk lagring.

Lär dig hur du [flyttar stordator lagring till Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

Att underhålla en egen katastrof återställnings webbplats kan vara ett kostsamt förslag. Azure har lättanvända och kostnads effektiva alternativ för [säkerhets kopiering](../../../backup/backup-overview.md), [återställning](../../../site-recovery/site-recovery-overview.md)och [redundans](../../../storage/common/storage-redundancy.md) på lokala eller regionala nivåer eller via GEO-redundans.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government för stordator-migreringar

Många offentliga sektor enheter skulle älska att flytta sina stordator program till en mer modern och flexibel plattform. Microsoft Azure Government är en fysiskt separerad instans av den globala Microsoft Azures plattformen – paketerade för federala, statliga och lokala myndighets system. Tjänsten tillhandahåller säkerhets-, skydds-och efterlevnads tjänster i världs klass som är specifika för USA myndigheter och deras partner.

Azure Government förtjänat en tillfällig myndighet för att genomföra (P-ATO) för att FedRAMP hög påverkan på system som behöver den här typen av miljö.

Kom igång genom att ladda ned [Microsoft Azure Government Cloud för stordator program](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Nästa steg

Be våra [partner](partner-workloads.md) att hjälpa dig att migrera eller vara värd för dina stordator program. 

Se även:

- [Fakta blad om stordator ämnen](mainframe-white-papers.md)
- [Stordatormigrering](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Felsökning](../../troubleshooting/index.yml)
- [Avmystifiera-stordator till Azure-migrering](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md