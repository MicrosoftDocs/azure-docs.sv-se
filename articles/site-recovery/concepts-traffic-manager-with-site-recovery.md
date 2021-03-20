---
title: Azure Traffic Manager med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du använder Azure Traffic Manager med Azure Site Recovery för haveri beredskap och migrering
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bb112e0b2d1c64e65ecaf6749a25707d8632c0cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86134964"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager med Azure Site Recovery

Med Azure Traffic Manager kan du styra trafiken mellan dina program slut punkter. En slutpunkt är en Internetansluten tjänst i eller utanför Azure.

Traffic Manager använder Domain Name System (DNS) för att dirigera klient begär anden till den lämpligaste slut punkten, baserat på en Traffic-routningsmetod och tillståndet för slut punkterna. Traffic Manager tillhandahåller en uppsättning [trafikdirigeringsmetoder](../traffic-manager/traffic-manager-routing-methods.md) och [alternativ för slutpunktsövervakning](../traffic-manager/traffic-manager-monitoring.md) som passar olika programbehov och modeller för automatisk redundansväxling. Klienterna ansluter direkt till den valda slutpunkten. Traffic Manager är inte en proxy eller gateway, och den ser inte trafiken som passerar mellan klienten och tjänsten.

Den här artikeln beskriver hur du kan kombinera Azure Traffic Monitors intelligenta routning med Azure Site Recovery kraftfulla funktioner för haveri beredskap och migrering.

## <a name="on-premises-to-azure-failover"></a>Lokalt till Azure-redundans

I det första scenariot bör du överväga **företag A** som har all sin program infrastruktur som körs i den lokala miljön. För affärs kontinuitet och efterföljandekrav beslutar **företaget A** att använda Azure Site Recovery för att skydda sina program.

**Företag A** kör program med offentliga slut punkter och vill kunna dirigera om trafik till Azure i en katastrof händelse. [Prioritets](../traffic-manager/traffic-manager-configure-priority-routing-method.md) metoden för trafik hantering i Azure Traffic Manager gör det möjligt för företag att enkelt implementera det här mönstret för växling vid fel.

Inställningarna är följande:
- **Företag a** skapar en [Traffic Manager profil](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Med hjälp av metoden för **prioritets** cirkulation skapar **företag A** två slut punkter – **primärt** för lokal och **redundansväxling** för Azure. **Primär** tilldelas prioritet 1 och **redundans** tilldelas prioritet 2.
- Eftersom den **primära** slut punkten är värdbaserad utanför Azure skapas slut punkten som en [extern](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) slut punkt.
- Med Azure Site Recovery har Azure-webbplatsen inga virtuella datorer eller program som körs före redundansväxlingen. Därför skapas även slut punkten för **redundans** som en **extern** slut punkt.
- Som standard dirigeras användar trafiken till det lokala programmet eftersom den slut punkten har den högsta prioritet som är kopplad till den. Ingen trafik dirigeras till Azure om den **primära** slut punkten är felfri.

![Lokal-till-Azure före redundans](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

I en katastrof händelse kan företag A utlösa en [redundansväxling](site-recovery-failover.md) till Azure och återställa sina program på Azure. När Azure Traffic Manager upptäcker att den **primära** slut punkten inte längre är felfri använder den automatiskt slut punkten för **växling vid fel** i DNS-svaret och användare ansluter till programmet som återställts på Azure.

![Lokalt till Azure efter redundans](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Beroende på affärs kraven kan **företag a** välja en högre eller lägre [sökfrekvens](../traffic-manager/traffic-manager-monitoring.md) för att växla mellan lokalt till Azure i en katastrof händelse och säkerställa minimal nedtid för användare.

När haveriet finns kan **företagets A** återställas från Azure till den lokala miljön ([VMware](vmware-azure-failback.md) eller [Hyper-V](hyper-v-azure-failback.md)) med hjälp av Azure Site Recovery. När Traffic Manager nu upptäcker att den **primära** slut punkten är felfri igen, använder den automatiskt den **primära** slut punkten i sina DNS-svar.

## <a name="on-premises-to-azure-migration"></a>Lokal Azure-migrering

Förutom haveri beredskap aktiverar Azure Site Recovery även [migreringar till Azure](migrate-overview.md). Med hjälp av Azure Site Recovery kraftfulla funktioner för redundanstest kan kunderna utvärdera program prestanda på Azure utan att påverka den lokala miljön. Och när kunderna är redo att migreras kan de välja att migrera hela arbets belastningarna eller välja att migrera och skala gradvis.

Azure Traffic Managers [viktad](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) routningsmetod kan användas för att dirigera delar av inkommande trafik till Azure samtidigt som den förbrukar majoriteten i den lokala miljön. Den här metoden kan hjälpa till att utvärdera skalnings prestanda eftersom du kan fortsätta att öka vikten som tilldelas till Azure när du migrerar fler och fler arbets belastningar till Azure.

**Företag B** väljer till exempel att migrera i faser, flytta en del av dess program miljö och samtidigt behålla resten lokalt. Under de första stegen när merparten av miljön är lokal, tilldelas en större vikt till den lokala miljön. Traffic Manager returnerar en slut punkt baserat på vikter tilldelade till tillgängliga slut punkter.

![Lokal-till-Azure-migrering](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Under migreringen är båda slut punkterna aktiva och det mesta av trafiken dirigeras till den lokala miljön. När migreringen fortsätter kan en större vikt tilldelas till slut punkten på Azure och slutligen kan den lokala slut punkten inaktive ras efter migreringen.

## <a name="azure-to-azure-failover"></a>Redundans i Azure till Azure

I det här exemplet bör du tänka på **företag C** som har alla dess program infrastruktur som kör Azure. **Företag C** väljer att använda Azure Site Recovery för att skydda sina program för affärs kontinuitet och efterföljandekrav.

**Företag C** kör program med offentliga slut punkter och vill ha möjlighet att smidigt omdirigera trafik till en annan Azure-region i en katastrof händelse. Med [prioritets](../traffic-manager/traffic-manager-configure-priority-routing-method.md) metoden för trafik-routning kan **företag C** enkelt implementera det här mönstret för redundans.

Inställningarna är följande:
- **Företag C** skapar en [Traffic Manager-profil](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Med hjälp av metoden för **prioritets** cirkulation skapar **företag C** två slut punkter – **primärt** för käll regionen (Azure Asien, östra) och **redundans** för återställnings regionen (Azure Sydostasien). **Primär** tilldelas prioritet 1 och **redundans** tilldelas prioritet 2.
- Eftersom den **primära** slut punkten är värd för Azure kan slut punkten vara en [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) -slutpunkt.
- Med Azure Site Recovery har återställnings-Azure-webbplatsen inga virtuella datorer eller program som körs före redundansväxlingen. Därför kan slut punkten för **redundans** skapas som en [extern](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) slut punkt.
- Som standard dirigeras användar trafiken till käll regionen (Asien, östra) när slut punkten har den högsta prioritet som är kopplad till den. Ingen trafik dirigeras till återställnings regionen om den **primära** slut punkten är felfri.

![Azure till Azure före redundans](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

I en katastrof händelse kan **företag C** utlösa en [redundansväxling](azure-to-azure-tutorial-failover-failback.md) och återställa sina program på Azure-regionen för återställning. När Azure Traffic Manager upptäcker att den primära slut punkten inte längre är felfri använder den automatiskt slut punkten för **växling vid fel** i DNS-svaret och användare ansluter till programmet som återställs på Azure-regionen för återställning (Sydostasien).

![Azure till Azure efter redundans](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Beroende på affärs kraven kan **företag C** välja en högre eller lägre [sökfrekvens](../traffic-manager/traffic-manager-monitoring.md) för att växla mellan käll-och återställnings regioner och se till att användarna har minimala stillestånds tid.

När haveriet finns kan **företagets C** återställas från Azure-regionen för Azure till Azures käll Azure-region med hjälp av Azure Site Recovery. När Traffic Manager nu upptäcker att den **primära** slut punkten är felfri igen, använder den automatiskt den **primära** slut punkten i sina DNS-svar.

## <a name="protecting-multi-region-enterprise-applications"></a>Skydda företags program i flera regioner

Globala företag förbättrar ofta kund upplevelsen genom att skräddarsy sina program för att hantera regionala behov. En minskning av lokalisering och fördröjning kan leda till att program infrastrukturen delas mellan regioner. Företag är också kopplade till regionala data lagar inom vissa områden och väljer att isolera en del av sin program infrastruktur inom regionala gränser.  

Nu ska vi tänka på ett exempel där **företag D** har delat sin program slut punkter för att separat betjäna Tyskland och resten av världen. **Företag D** använder Azure Traffic Managers [geografiska](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) routningsmetod för att konfigurera detta. All trafik som härstammar från Tyskland dirigeras till **slut punkt 1** och all trafik som härstammar utanför Tyskland dirigeras till **slut punkt 2**.

Problemet med installationen är att om **slut punkten 1** slutar fungera av någon anledning sker ingen omdirigering av trafik till **slut punkt 2**. Trafik från Tyskland fortsätter att dirigeras till **slut punkt 1** oavsett tillståndet för slut punkten och lämnar tyska användare utan åtkomst till **företagets D**-program. På samma sätt sker det ingen omdirigering av trafik till **slut punkt 1** om **slut punkt 2** går offline.

![Program med flera regioner innan](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

För att undvika att det här problemet uppstår och garantera program återhämtning, använder **företag D** [kapslade Traffic Manager profiler](../traffic-manager/traffic-manager-nested-profiles.md) med Azure Site Recovery. I en kapslad profil installation dirigeras inte trafiken till enskilda slut punkter, utan i stället till andra Traffic Manager profiler. Så här fungerar installationen:
- I stället för att använda geografisk routning med enskilda slut punkter, använder **företag D** geografisk routning med Traffic Manager profiler.
- Varje underordnad Traffic Manager-profil använder **prioriterad** routning med en primär och en återställnings slut punkt, vilket innebär att **prioriterad** routning kapslas i **geografisk** routning.
- För att aktivera program återhämtning använder varje arbets belastnings distribution Azure Site Recovery för att redundansväxla till en återställnings region baserat på en katastrof händelse.
- När den överordnade Traffic Manager tar emot en DNS-fråga dirigeras den till relevant underordnad Traffic Manager som svarar på frågan med en tillgänglig slut punkt.

![Program med flera regioner efter](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Om till exempel slut punkten i Tyskland i Tyskland Miss lyckas kan programmet snabbt återställas till Germany nordöstra. Den nya slut punkten hanterar trafik som härstammar från Tyskland med minimal stillestånds tid för användare. På samma sätt kan ett slut punkts avbrott i Västeuropa i Västeuropa hanteras genom att program arbets belastningen återställs till Europa, med Azure Traffic Manager hantering av DNS-omdirigeringar till tillgänglig slut punkt.

Ovanstående inställningar kan utökas för att inkludera så många regioner och slut punkts kombinationer som krävs. Traffic Manager tillåter upp till 10 nivåer av kapslade profiler och tillåter inte slingor i den kapslade konfigurationen.

## <a name="recovery-time-objective-rto-considerations"></a>Överväganden för återställnings tid (RTO)

I de flesta organisationer hanteras tillägg eller ändring av DNS-poster antingen av ett separat team eller av någon utanför organisationen. Detta gör att det blir mycket svårt att ändra DNS-poster. Den tid det tar att uppdatera DNS-poster av andra team eller organisationer som hanterar DNS-infrastrukturen varierar från organisation till organisation och påverkar RTO för programmet.

Genom att använda Traffic Manager kan du Frontload det arbete som krävs för DNS-uppdateringar. Ingen manuell eller skriptad åtgärd krävs vid tidpunkten för den faktiska redundansväxlingen. Den här metoden hjälper till att snabbt växla (och därmed sänka RTO) och undvika kostsam tids krävande tids krävande DNS-ändringar i en katastrof händelse. Med Traffic Manager, även om steget för återställning efter fel är automatiserat, vilket annars skulle behöva hanteras separat.

Om du ställer in rätt [avsöknings intervall](../traffic-manager/traffic-manager-monitoring.md) via grundläggande eller snabba intervall hälso kontroller kan du avsevärt ta bort RTO under redundansväxlingen och minska stillestånds tiden för användare.

Du kan också optimera värdet för DNS-Time to Live (TTL) för Traffic Manager-profilen. TTL är värdet som en DNS-post cachelagras av en klient. För en post kommer DNS inte att frågas två gånger inom TTL-intervallet. Varje DNS-post har ett associerat TTL-värde. Genom att minska det här värdet resulterar det i fler DNS-frågor till Traffic Manager men kan minska RTO genom att identifiera avbrott snabbare.

Den TTL som klienten upplever ökar inte heller om antalet DNS-matchare mellan klienten och den auktoritativa DNS-servern ökar. DNS-matchare räknas ned och TTL-värdet skickas bara på ett TTL-värde som motsvarar den förflutna tiden sedan posten cachelagrades. Detta säkerställer att DNS-posten uppdateras på klienten efter TTL, oavsett antalet DNS-matchare i kedjan.

## <a name="next-steps"></a>Nästa steg
- Läs mer om Traffic Manager [routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md).
- Läs mer om [kapslade Traffic Manager-profiler](../traffic-manager/traffic-manager-nested-profiles.md).
- Läs mer om [slut punkts övervakning](../traffic-manager/traffic-manager-monitoring.md).
- Läs mer om [återställnings planer](site-recovery-create-recovery-plans.md) för att automatisera programredundans.
