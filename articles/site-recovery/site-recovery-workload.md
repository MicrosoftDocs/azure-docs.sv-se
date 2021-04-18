---
title: Om haveriberedskap för lokala appar med Azure Site Recovery
description: Beskriver de arbetsbelastningar som kan skyddas med haveriberedskap med Azure Site Recovery-tjänsten.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 1a5d20e6feacfe72052142c07dc45753b9bc3138
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599119"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Om haveriberedskap för lokala appar

I den här artikeln beskrivs lokala arbetsbelastningar och appar [](site-recovery-overview.md) som du kan skydda för haveriberedskap med Azure Site Recovery tjänsten.

## <a name="overview"></a>Översikt

Organisationer behöver ha en BCDR-strategi för affärskontinuhet och haveriberedskap för att hålla arbetsbelastningar och data säkra och tillgängliga under planerade och oplanerade avbrott. Och återställ till vanliga arbetsförhållanden.

Site Recovery är en Azure-tjänst som bidrar till din BCDR-strategi. Med Site Recovery kan du distribuera programmedveten replikering till molnet eller till en sekundär plats. Du kan använda Site Recovery för att hantera replikering, utföra haveriberedskapstestning och köra redundans och återställning efter fel. Dina appar kan köras på Windows- eller Linux-baserade datorer, fysiska servrar, VMware eller Hyper-V.

Site Recovery integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory. Microsoft har ett nära samarbete med ledande leverantörer som Oracle, SAP och Red Hat. Du kan anpassa replikeringslösningar på appbasis.

## <a name="why-use-site-recovery-for-application-replication"></a>Varför ska man använda Site Recovery för programreplikering?

Site Recovery erbjuder skydd och återställning på programnivå enligt följande:

- Appoberoende och tillhandahåller replikering för alla arbetsbelastningar som körs på en dator som stöds.
- Nära synkron replikering, med återställningspunktmål (RPO) så låga som 30 sekunder för att uppfylla behoven hos de mest kritiska affärsapparna.
- Appkonsekventa ögonblicksbilder för program med en eller flera nivåer.
- Integrering med SQL Server AlwaysOn och partnerskap med andra replikeringstekniker på programnivå. Till exempel Active Directory-replikering, SQL AlwaysOn och Exchange Database-tillgänglighetsgrupper (DAG).
- Flexibla återställningsplaner som gör att du kan återställa en hel programstack med ett enda klick och inkludera externa skript och manuella åtgärder i planen.
- Avancerad nätverkshantering i Site Recovery Azure för att förenkla kraven på appnätverk. Nätverkshantering, till exempel möjligheten att reservera IP-adresser, konfigurera belastningsutjämning och integrering med Azure Traffic Manager för nätverksväxel för låg återställningstid (RTO).
- Ett omfattande automationsbibliotek som ger tillgång till produktionsklara, programspecifika skript som kan ladas ned och integreras med återställningsplaner.

## <a name="workload-summary"></a>Översikt över arbetsbelastningar

Site Recovery kan replikera alla appar som körs på en dator som stöds. Vi har samarbetat med produktteam för att göra ytterligare tester för de appar som anges i följande tabell.

| **Arbetsbelastning** |**Replikera virtuella Azure-datorer till Azure** |**Replikera virtuella Hyper-V-datorer till en sekundär plats** | **Replikera Hyper-V-VM:ar till Azure** | **Replikera VMware-VM:ar till en sekundär plats** | **Replikera virtuella VMware-datorer till Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Ja |Ja |Ja |Ja |Ja|
| Webbappar (IIS, SQL) |Ja |Ja |Ja |Ja |Ja|
| System Center Operations Manager |Ja |Ja |Ja |Ja |Ja|
| SharePoint |Ja |Ja |Ja |Ja |Ja|
| SAP<br/><br/>Replikera en SAP-plats till Azure för icke-kluster |Ja (testat av Microsoft) |Ja (testat av Microsoft) |Ja (testat av Microsoft) |Ja (testat av Microsoft) |Ja (testat av Microsoft)|
| Exchange (icke-DAG) |Ja |Ja |Ja |Ja |Ja|
| Fjärrskrivbord/VDI |Ja |Ja |Ja |Ja |Ja|
| Linux (operativsystem och appar) |Ja (testat av Microsoft) |Ja (testat av Microsoft) |Ja (testat av Microsoft) |Ja (testat av Microsoft) |Ja (testat av Microsoft)|
| Dynamics AX |Ja |Ja |Ja |Ja |Ja|
| Windows-filserver |Ja |Ja |Ja |Ja |Ja|
| Citrix XenApp och XenDesktop |No|Ej tillämpligt |No |Ej tillämpligt |No |

## <a name="replicate-active-directory-and-dns"></a>Replikera Active Directory och DNS

En Active Directory- och DNS-infrastruktur är fundamentalt för de flesta företagsappar. Under haveriberedskap måste du skydda och återställa dessa infrastrukturkomponenter innan du återställer arbetsbelastningar och appar.

Du kan använda Site Recovery för att skapa en fullständig automatiserad haveriberedskapsplan för Active Directory och DNS. Om du till exempel vill redundans växla över SharePoint och SAP från en primär till en sekundär plats kan du konfigurera en återställningsplan som först redundansar Över Active Directory. Använd sedan ytterligare en appspecifik återställningsplan för redundans för de andra apparna som förlitar sig på Active Directory.

[Läs mer](site-recovery-active-directory.md) om haveriberedskap för Active Directory och DNS.

## <a name="protect-sql-server"></a>Skydda SQL Server

SQL Server tillhandahåller en datatjänstgrund för många företagsappar i ett lokalt datacenter. Site Recovery kan användas med SQL Server HA/DR-tekniker för att skydda företagsappar med flera nivåer som använder SQL Server.

Site Recovery tillhandahåller:

- En enkel och kostnadseffektiv haveriberedskapslösning för SQL Server. Replikera flera versioner och utgåvor av fristående SQL Server-servrar och SQL Server-kluster till Azure eller till en sekundär plats.
- Integrering med SQL AlwaysOn-tillgänglighetsgrupper för att hantera redundans och återställning efter fel med Azure Site Recovery-återställningsplaner.
- Slutpunkt till slutpunkt-återställningsplaner för alla nivåer i ett program, inklusive SQL Server-databaser.
- Skalning av SQL Server belastningstoppar med Site Recovery, genom att _burst-skala_ dem till större storlekar för virtuella IaaS-datorer i Azure.
- Enkel testning av SQL Server-haveriberedskap. Du kan köra redundanstester för att analysera data och köra efterlevnadskontroller utan att påverka din produktionsmiljö.

[Läs mer om](site-recovery-sql.md) haveriberedskap för SQL Server.

## <a name="protect-sharepoint"></a>Skydda SharePoint

Azure Site Recovery hjälper dig att skydda SharePoint-distributioner på följande sätt:

- Eliminerar behovet av och associerade infrastrukturkostnader för en reservservergrupp för haveriberedskap. Använd Site Recovery för att replikera en hel servergrupp (webb-, app- och databasnivåer) till Azure eller till en sekundär plats.
- Förenklar programdistribution och programhantering. Uppdateringar som distribueras till den primära platsen replikeras automatiskt. Uppdateringarna är tillgängliga efter redundans och återställning av en servergrupp på en sekundär plats. Sänker hanteringskomplexiteten och kostnaderna för att hålla en fristående servergrupp uppdaterade.
- Förenklar utvecklingen och testningen av SharePoint-program genom att på begäran skapa en produktionslik kopia av replikmiljön för testning och felsökning.
- Förenklar övergången till molnet genom att använda Site Recovery för att migrera SharePoint-distributioner till Azure.

[Läs mer om](site-recovery-sharepoint.md) haveriberedskap för SharePoint.

## <a name="protect-dynamics-ax"></a>Skydda Dynamics AX

Azure Site Recovery hjälper dig att skydda din Dynamics AX ERP-lösning genom att:

- Hantera replikering av hela Dynamics AX-miljön (webb- och AOS-nivåer, databasnivåer, SharePoint) till Azure eller till en sekundär plats.
- Förenkla migreringen av Dynamics AX-distributioner till molnet (Azure).
- Förenkla utvecklingen och testningen av Dynamics AX-program genom att på begäran skapa en produktionslik kopia för testning och felsökning.

[Läs mer om](site-recovery-dynamicsax.md) haveriberedskap för Dynamic AX.

## <a name="protect-remote-desktop-services"></a>Skydda Fjärrskrivbordstjänster

Fjärrskrivbordstjänster (RDS) aktiverar VDI (Virtual Desktop Infrastructure), sessionsbaserade skrivbord och program som gör att användarna kan arbeta var som helst.

Med Azure Site Recovery kan du replikera följande tjänster:

- Replikera hanterade eller ohanterade poolade virtuella skrivbord till en sekundär plats.
- Replikera fjärrprogram och sessioner till en sekundär plats eller Azure.

I följande tabell visas replikeringsalternativen:

| **Fjärrskrivbordstjänster** |**Replikera virtuella Azure-datorer till Azure** | **Replikera virtuella Hyper-V-datorer till en sekundär plats** | **Replikera Hyper-V-VM:ar till Azure** | **Replikera VMware-VM:ar till en sekundär plats** | **Replikera virtuella VMware-datorer till Azure** | **Replikera fysiska servrar till en sekundär plats** | **Replikera fysiska servrar till Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Poolat virtuellt skrivbord (ohanterat)** |Inga|Ja |Inga |Ja |Inga |Ja |Inga |
| **Poolat virtuellt skrivbord (hanterat och utan UPD)** |Inga|Ja |Inga |Ja |Inga |Ja |Inga |
| **Fjärrprogram och skrivbordssessioner (utan UPD)** |Ja|Ja |Ja |Ja |Ja |Ja |Ja |

[Läs mer om](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) haveriberedskap för FJÄRRSKRIVBORDSTJÄNSTER.

## <a name="protect-exchange"></a>Skydda Exchange

Site Recovery skyddar Exchange på följande sätt:

- För små Exchange-distributioner, till exempel en enskild eller fristående server, kan Site Recovery replikera och redundansväxla till Azure eller till en sekundär plats.
- För större distributioner integrerar Site Recovery med Exchange DAG:ar.
- Exchange-databastillgänglighetsgrupper är den rekommenderade lösningen för haveriberedskap på större företag.  Site Recovery-återställningsplaner kan innehålla DAG:ar för att samordna DAG-redundans mellan platser.

Mer information om haveriberedskap för Exchange finns i [Exchange-DAG:er](/Exchange/high-availability/database-availability-groups/database-availability-groups) och [Haveriberedskap för Exchange.](/Exchange/high-availability/disaster-recovery/disaster-recovery)

## <a name="protect-sap"></a>Skydda SAP

Använd Site Recovery för att skydda din SAP-distribution på följande sätt:

- Aktivera skydd av SAP NetWeaver och icke-NetWeaver-produktionsprogram som körs lokalt genom att replikera komponenter till Azure.
- Aktivera skydd av SAP NetWeaver och icke-NetWeaver-produktionsprogram som kör Azure, genom att replikera komponenter till ett annat Azure-datacenter.
- Förenkla molnmigreringen genom att använda Site Recovery för att migrera din SAP-distribution till Azure.
- Förenkla SAP-projektuppgradering, testning och prototyper genom att skapa en produktionsklon på begäran för att testa SAP-program.

[Läs mer om](site-recovery-sap.md) haveriberedskap för SAP.

## <a name="protect-internet-information-services"></a>Skydda Internet Information Services

Använd Site Recovery för att skydda din Internet Information Services-distribution (IIS), enligt följande:

Azure Site Recovery erbjuder haveriberedskap genom att replikera viktiga komponenter i din miljö till en fjärrplats eller ett offentligt moln, till exempel Microsoft Azure. Eftersom de virtuella datorerna med webbservern och databasen replikeras till återställningsplatsen finns det inget krav på separat säkerhetskopiering för konfigurationsfiler eller certifikat. Programmappningar och bindningar som är beroende av miljövariabler som ändras efter redundans kan uppdateras via skript som är integrerade i haveriberedskapsplanerna. Virtuella datorer finns bara på återställningsplatsen under en redundans. Azure Site Recovery hjälper dig också att orkestrera redundansen från end-to-end genom att tillhandahålla följande funktioner:

- Användning av ordningsföljd för avstängning och start av virtuella datorer i olika nivåer.
- Lägga till skript för att tillåta uppdateringar av programberoenden och bindningar på de virtuella datorerna när de har startat. Skripten kan också användas för att uppdatera DNS-servern så att den pekar på återställningsplatsen.
- Allokera IP-adresser till virtuella datorer före redundans genom att mappa de primära nätverken och återställningsnätverken och använda skript som inte behöver uppdateras efter redundans.
- Möjlighet till redundansväxling med ett klick för flera webbprogram som eliminerar risken för förvirring under en katastrof.
- Möjlighet att testa återställningsplaner i en isolerad miljö för DR-test.

[Läs mer om](site-recovery-iis.md) haveriberedskap för IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Skydda Citrix XenApp och XenDesktop

Från och med mars 2020 har Citrix tillkännagivit utfasning och slut på stödet för arbetsbelastningar i offentliga moln. Därför rekommenderar vi inte att du använder Site Recovery för att skydda Citrix-arbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

[Läs mer om](azure-to-azure-quickstart.md) haveriberedskap för en virtuell Azure-dator.
