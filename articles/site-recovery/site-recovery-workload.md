---
title: Om haveri beredskap för lokala appar med Azure Site Recovery
description: Beskriver de arbetsbelastningar som kan skyddas med haveriberedskap med Azure Site Recovery-tjänsten.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80062841"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Om haveriberedskap för lokala appar

Den här artikeln beskriver lokala arbets belastningar och appar som du kan skydda för haveri beredskap med [Azure Site Recovery](site-recovery-overview.md) -tjänsten.

## <a name="overview"></a>Översikt

Organisationer behöver en strategi för affärs kontinuitet och haveri beredskap (BCDR) för att hålla arbets belastningar och data säkra och tillgängliga under planerade och oplanerade drift stopp. Och Återställ till vanliga arbets förhållanden.

Site Recovery är en Azure-tjänst som bidrar till din BCDR-strategi. Med Site Recovery kan du distribuera programmedveten replikering till molnet eller till en sekundär plats. Du kan använda Site Recovery för att hantera replikering, utföra test av haveri beredskap och köra redundans och återställning efter fel. Dina appar kan köras på Windows-eller Linux-baserade datorer, fysiska servrar, VMware eller Hyper-V.

Site Recovery integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory. Microsoft arbetar tätt med ledande leverantörer, inklusive Oracle, SAP och Red Hat. Du kan anpassa replikeringslösningar på appbasis.

## <a name="why-use-site-recovery-for-application-replication"></a>Varför ska man använda Site Recovery för programreplikering?

Site Recovery erbjuder skydd och återställning på programnivå enligt följande:

- App-oberoende och tillhandahåller replikering för alla arbets belastningar som körs på en dator som stöds.
- Nära synkron replikering, med återställnings punkt mål (återställnings punkt mål) så lågt som 30 sekunder för att uppfylla behoven hos de mest kritiska affärsapparna.
- Appkonsekventa ögonblicksbilder för program med en eller flera nivåer.
- Integrering med SQL Server AlwaysOn och partnerskap med andra tekniker på program nivå. Till exempel Active Directory replikering, SQL AlwaysOn och Exchange databas tillgänglighets grupper (dag).
- Flexibla återställnings planer som gör att du kan återställa en hel programs tack med en enda klickning och ta med externa skript och manuella åtgärder i planen.
- Avancerad nätverks hantering i Site Recovery och Azure för att förenkla appens nätverks krav. Nätverks hantering, till exempel möjligheten att reservera IP-adresser, konfigurera belastnings utjämning och integrering med Azure Traffic Manager för låga återställnings tider (RTO) nätverks växlingar.
- Ett omfattande automationsbibliotek som ger tillgång till produktionsklara, programspecifika skript som kan ladas ned och integreras med återställningsplaner.

## <a name="workload-summary"></a>Översikt över arbetsbelastningar

Site Recovery kan replikera alla appar som körs på en dator som stöds. Vi samarbetar med produkt team för att utföra ytterligare tester för de appar som anges i följande tabell.

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
| Citrix XenApp och XenDesktop |Ja|Saknas |Ja |Saknas |Ja |

## <a name="replicate-active-directory-and-dns"></a>Replikera Active Directory och DNS

En Active Directory- och DNS-infrastruktur är fundamentalt för de flesta företagsappar. Vid haveri beredskap måste du skydda och återställa dessa infrastruktur komponenter innan du återställer arbets belastningar och appar.

Du kan använda Site Recovery för att skapa en fullständig automatiserad haveriberedskapsplan för Active Directory och DNS. Om du till exempel vill redundansväxla SharePoint och SAP från en primär till en sekundär plats kan du skapa en återställnings plan som först växlar över Active Directory. Använd sedan en ytterligare app-speciell återställnings plan för att redundansväxla de andra appar som förlitar sig på Active Directory.

[Läs mer](site-recovery-active-directory.md) om haveri beredskap för Active Directory och DNS.

## <a name="protect-sql-server"></a>Skydda SQL Server

SQL Server tillhandahåller en Data Services Foundation för många affärs program i ett lokalt Data Center. Site Recovery kan användas med SQL Server HA/DR-teknik för att skydda företags program med flera nivåer som använder SQL Server.

Site Recovery tillhandahåller:

- En enkel och kostnadseffektiv haveriberedskapslösning för SQL Server. Replikera flera versioner och utgåvor av fristående SQL Server-servrar och SQL Server-kluster till Azure eller till en sekundär plats.
- Integrering med SQL AlwaysOn-tillgänglighetsgrupper för att hantera redundans och återställning efter fel med Azure Site Recovery-återställningsplaner.
- Slutpunkt till slutpunkt-återställningsplaner för alla nivåer i ett program, inklusive SQL Server-databaser.
- Skalning av SQL Server för hög belastnings belastning med Site Recovery genom att _överföra dem till_ större IaaS virtuella dator storlekar i Azure.
- Enkel testning av SQL Server-haveriberedskap. Du kan köra redundanstester för att analysera data och köra efterlevnadskontroller utan att påverka din produktionsmiljö.

[Läs mer](site-recovery-sql.md) om haveri beredskap för SQL Server.

## <a name="protect-sharepoint"></a>Skydda SharePoint

Azure Site Recovery hjälper dig att skydda SharePoint-distributioner på följande sätt:

- Eliminerar behovet av och associerade infrastrukturkostnader för en reservservergrupp för haveriberedskap. Använd Site Recovery för att replikera en hel Server grupp (webb-, app-och databas nivåer) till Azure eller till en sekundär plats.
- Förenklar programdistribution och programhantering. Uppdateringar som distribueras till den primära platsen replikeras automatiskt. Uppdateringarna är tillgängliga efter redundansväxling och återställning av en Server grupp på en sekundär plats. Sänker hanterings komplexiteten och kostnaderna för att hålla en upprättad Server grupp uppdaterad.
- Förenklar utvecklingen och testningen av SharePoint-program genom att på begäran skapa en produktionslik kopia av replikmiljön för testning och felsökning.
- Förenklar övergången till molnet genom att använda Site Recovery för att migrera SharePoint-distributioner till Azure.

[Läs mer](site-recovery-sharepoint.md) om haveri beredskap för SharePoint.

## <a name="protect-dynamics-ax"></a>Skydda Dynamics AX

Azure Site Recovery hjälper dig att skydda din Dynamics AX ERP-lösning genom att:

- Hantera replikeringen av hela Dynamics AX-miljön (webb-och AOS-nivåer, databas nivåer, SharePoint) till Azure eller till en sekundär plats.
- Förenkla migreringen av Dynamics AX-distributioner till molnet (Azure).
- Förenkla utvecklingen och testningen av Dynamics AX-program genom att på begäran skapa en produktionslik kopia för testning och felsökning.

[Läs mer](site-recovery-dynamicsax.md) om haveri beredskap för dynamisk AX.

## <a name="protect-remote-desktop-services"></a>Skydda Fjärrskrivbordstjänster

Fjärrskrivbordstjänster (RDS) aktiverar Virtual Desktop Infrastructure (VDI), sessionsbaserade skriv bord och program som gör att användarna kan arbeta var som helst.

Med Azure Site Recovery kan du replikera följande tjänster:

- Replikera hanterade eller ohanterade virtuella skriv bord till en sekundär plats.
- Replikera fjärranslutna program och sessioner till en sekundär plats eller Azure.

Följande tabell visar alternativen för replikering:

| **Fjärrskrivbordstjänster** |**Replikera virtuella Azure-datorer till Azure** | **Replikera virtuella Hyper-V-datorer till en sekundär plats** | **Replikera Hyper-V-VM:ar till Azure** | **Replikera VMware-VM:ar till en sekundär plats** | **Replikera virtuella VMware-datorer till Azure** | **Replikera fysiska servrar till en sekundär plats** | **Replikera fysiska servrar till Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Poolat virtuellt skrivbord (ohanterat)** |Inga|Ja |Inga |Ja |Inga |Ja |Inga |
| **Poolat virtuellt skrivbord (hanterat och utan UPD)** |Inga|Ja |Inga |Ja |Inga |Ja |Inga |
| **Fjärrprogram och skrivbordssessioner (utan UPD)** |Ja|Ja |Ja |Ja |Ja |Ja |Ja |

[Läs mer](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) om haveri beredskap för fjärr skrivbords tjänster.

## <a name="protect-exchange"></a>Skydda Exchange

Site Recovery skyddar Exchange på följande sätt:

- För små Exchange-distributioner, till exempel en enskild eller fristående server, kan Site Recovery replikera och redundansväxla till Azure eller till en sekundär plats.
- För större distributioner integrerar Site Recovery med Exchange DAG:ar.
- Exchange-databastillgänglighetsgrupper är den rekommenderade lösningen för haveriberedskap på större företag.  Site Recovery-återställningsplaner kan innehålla DAG:ar för att samordna DAG-redundans mellan platser.

Mer information om haveri beredskap för Exchange finns i [Exchange-dag](/Exchange/high-availability/database-availability-groups/database-availability-groups) och [återställning av Exchange-haveri](/Exchange/high-availability/disaster-recovery/disaster-recovery).

## <a name="protect-sap"></a>Skydda SAP

Använd Site Recovery för att skydda din SAP-distribution på följande sätt:

- Aktivera skydd av SAP NetWeaver och icke-NetWeaver-produktionsprogram som körs lokalt genom att replikera komponenter till Azure.
- Aktivera skydd av SAP NetWeaver och icke-NetWeaver-produktionsprogram som kör Azure, genom att replikera komponenter till ett annat Azure-datacenter.
- Förenkla molnmigreringen genom att använda Site Recovery för att migrera din SAP-distribution till Azure.
- Förenkla SAP-projektuppgradering, testning och prototyper genom att skapa en produktionsklon på begäran för att testa SAP-program.

[Läs mer](site-recovery-sap.md) om haveri beredskap för SAP.

## <a name="protect-internet-information-services"></a>Skydda Internet Information Services

Använd Site Recovery för att skydda din Internet Information Services-distribution (IIS) på följande sätt:

Azure Site Recovery erbjuder haveriberedskap genom att replikera viktiga komponenter i din miljö till en fjärrplats eller ett offentligt moln, till exempel Microsoft Azure. Eftersom de virtuella datorerna med webb servern och databasen replikeras till återställnings platsen finns det inga krav på en separat säkerhets kopia för konfigurationsfiler eller certifikat. Programmappningar och bindningar som är beroende av miljövariabler som ändras efter redundans kan uppdateras via skript som är integrerade i haveriberedskapsplanerna. Virtuella datorer hämtas bara på återställnings platsen under en redundansväxling. Azure Site Recovery hjälper dig också att dirigera den kompletta redundansväxlingen genom att tillhandahålla följande funktioner:

- Användning av ordningsföljd för avstängning och start av virtuella datorer i olika nivåer.
- Lägga till skript för att tillåta uppdateringar av program beroenden och bindningar på de virtuella datorerna när de har startat. Skripten kan också användas för att uppdatera DNS-servern så att den pekar på återställningsplatsen.
- Allokera IP-adresser till virtuella datorer för redundans genom att mappa de primära och återställnings nätverken och använda skript som inte behöver uppdateras efter redundansväxlingen.
- Möjlighet att använda redundans för flera webb program som eliminerar omfattningen för förvirring under en katastrof.
- Möjlighet att testa återställningsplaner i en isolerad miljö för DR-test.

[Läs mer](site-recovery-iis.md) om haveri beredskap för IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Skydda Citrix XenApp och XenDesktop

Använd Site Recovery för att skydda dina Citrix XenApp- och XenDesktop-distributioner på följande sätt:

- Aktivera skydd av Citrix XenApp-och XenDesktop-distributionen. Replikera olika distributions lager till Azure: Active Directory, DNS-server, SQL-databasserver, Citrix Delivery Controller, butik Server, XenApp Master (VDA), Citrix XenApp License Server.
- Förenkla molnmigreringen genom att använda Site Recovery för att migrera din Citrix XenApp och XenDesktop SAP-distribution till Azure.
- Förenkla Citrix XenApp-/XenDesktop-testningen genom att skapa en produktionslik kopia på begäran för testning och felsökning.
- Den här lösningen gäller endast för virtuella Windows Server-datorer och inte för virtuella datorer i klienter. Klientens virtuella skriv bord har ännu inte stöd för licensiering i Azure. [Lär dig mer](https://azure.microsoft.com/pricing/licensing-faq/) om licensiering för klient/server-datorer i Azure.

[Läs mer](site-recovery-citrix-xenapp-and-xendesktop.md) om haveri beredskap för Citrix XenApp-och XenDesktop-distributioner. Eller så kan du läsa dokumentet om [Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr).

## <a name="next-steps"></a>Nästa steg

[Läs mer](azure-to-azure-quickstart.md) om haveri beredskap för en virtuell Azure-dator.
