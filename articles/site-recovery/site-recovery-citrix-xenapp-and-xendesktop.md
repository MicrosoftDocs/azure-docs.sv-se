---
title: Konfigurera Citrix XenDesktop/XenApp haveri beredskap med Azure Site Recovery
description: Den här artikeln beskriver hur du konfigurerar haveri beredskap på Citrix XenDesktop-och XenApp-distributioner med hjälp av Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 90d54a8ded99dd8ab43aed688036add6aede20ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134838"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>Konfigurera katastrof återställning för en Citrix-XenApp och XenDesktop-distribution med flera nivåer



Citrix XenDesktop är en lösning för Skriv bords virtualisering som levererar skriv bord och program som en OnDemand-tjänst till alla användare, var som helst. Med FlexCast leverans teknik kan XenDesktop snabbt och säkert leverera program och skriv bord till användare.
I dag tillhandahåller Citrix XenApp inte några funktioner för haveri beredskap.

En felfri lösning för haveri beredskap bör möjliggöra modellering av återställnings planer runt de komplexa applikations arkitekturerna och även ha möjlighet att lägga till anpassade steg för att hantera program mappningar mellan olika nivåer, vilket ger en enda säker bild av lösning i händelse av en katastrof som leder till en lägre RTO.

Det här dokumentet innehåller steg-för-steg-anvisningar om hur du skapar en katastrof återställnings lösning för dina lokala Citrix XenApp-distributioner på Hyper-V och VMware vSphere plattformar. Det här dokumentet beskriver också hur du utför en redundanstest (haveri beredskap) och oplanerad redundansväxling till Azure med hjälp av återställnings planer, konfigurationer som stöds och nödvändiga komponenter.


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du förstår följande:

1. [Replikera en virtuell dator till Azure](./vmware-azure-tutorial.md)
1. Så här [skapar du ett återställnings nätverk](./concepts-on-premises-to-azure-networking.md)
1. [Utföra en redundanstest till Azure](site-recovery-test-failover-to-azure.md)
1. [Göra en redundansväxling till Azure](site-recovery-failover.md)
1. Så här [replikerar du en domänkontrollant](site-recovery-active-directory.md)
1. Så här [replikerar du SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Distributions mönster

En Citrix XenApp-och XenDesktop-grupp har vanligt vis följande distributions mönster:

**Distributions mönster**

Citrix XenApp-och XenDesktop-distribution med AD DNS-server, SQL-databasserver, Citrix Delivery Controller, butik Server, XenApp Master (VDA), Citrix XenApp License Server

![Distributions mönster 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery-stöd

I den här artikeln har Citrix-distributioner på virtuella VMware-datorer som hanteras av vSphere 6,0/System Center VMM 2012 R2 använts för att konfigurera DR.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Inte inom omfånget | Ja
**VMware** | Inte inom omfånget | Ja
**Fysisk server** | Inte inom omfånget | Ja

### <a name="versions"></a>Versioner
Kunder kan distribuera XenApp-komponenter som Virtual Machines som körs på Hyper-V eller VMware eller som fysiska servrar. Azure Site Recovery kan skydda både fysiska och virtuella distributioner till Azure.
Eftersom XenApp 7,7 eller senare stöds i Azure kan endast distributioner med dessa versioner växlas över till Azure för haveri beredskap eller migrering.

### <a name="things-to-keep-in-mind"></a>Saker att tänka på

1. Det finns stöd för skydd och återställning av lokala distributioner med hjälp av server-OS-datorer för att leverera XenApp publicerade appar och XenApp publicerade skriv bord.

2. Skydd och återställning av lokala distributioner som använder Desktop OS-datorer för att leverera Desktop VDI för virtuella datorer i klienter, inklusive Windows 10, stöds inte. Detta beror på att Site Recovery inte stöder återställning av datorer med Skriv bords OS'es.  Vissa operativ system för virtuella datorer för klienter (t. ex. Windows 7) stöds inte ännu för licensiering i Azure. [Lär dig mer](https://azure.microsoft.com/pricing/licensing-faq/) om licensiering för klient/server-datorer i Azure.

3.  Azure Site Recovery kan inte replikera och skydda befintliga lokala MCS-eller PVS-kloner.
Du måste återskapa dessa kloner med Azure RM-etablering från leverans kontroll enheten.

4. NetScaler kan inte skyddas med Azure Site Recovery eftersom NetScaler är baserat på FreeBSD och Azure Site Recovery inte stöder skydd av FreeBSD OS. Du måste distribuera och konfigurera en ny NetScaler-installation från Azures marknads plats efter redundansväxlingen till Azure.


## <a name="replicating-virtual-machines"></a>Replikera virtuella datorer

Följande komponenter i Citrix XenApp-distributionen måste vara skyddade för att aktivera replikering och återställning.

* Skydd av AD DNS-Server
* Skydd av SQL Database-Server
* Skydd av Citrix Delivery Controller
* Skydd av butik-servern.
* Skydd av XenApp Master (VDA)
* Skydd av Citrix XenApp-licensserver


**AD DNS-Server-replikering**

Se [skydda Active Directory och DNS med Azure Site Recovery](site-recovery-active-directory.md) för att få hjälp med att replikera och konfigurera en domänkontrollant i Azure.

**SQL Database Server Replication**

Se [skydda SQL Server med SQL Server haveri beredskap och Azure Site Recovery](site-recovery-sql.md) för detaljerad teknisk vägledning om de rekommenderade alternativen för att skydda SQL-servrar.

Följ [den här vägledningen](./vmware-azure-tutorial.md) för att börja replikera de andra virtuella datorerna till Azure.

![Skydd av XenApp-komponenter](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Beräknings-och nätverks inställningar**

När datorerna har skyddats (status visas som "skyddade" under replikerade objekt) måste inställningarna för beräkning och nätverk konfigureras.
I beräknings egenskaper för beräknings-och nätverks > kan du ange namnet på den virtuella Azure-datorn och mål storleken.
Ändra namnet så att det uppfyller kraven för Azure om det behövs. Du kan också visa och lägga till information om mål nätverket, under nätet och IP-adressen som ska tilldelas den virtuella Azure-datorn.

. Tänk på följande:

* Du kan ange IP-måladressen. Om du inte anger någon adress använder den redundansväxlade datorn DHCP. Om du anger en adress som inte är tillgänglig vid redundans fungerar inte redundansväxlingen. Samma mål-IP-adress kan användas för att testa redundans om adressen är tillgänglig i nätverket för redundanstestet.

* För AD/DNS-servern behåller den lokala adressen att du kan ange samma adress som DNS-servern för det virtuella Azure-nätverket.

Antalet nätverkskort beror på storleken som du anger för den virtuella måldatorn enligt följande:

*   Om antalet nätverkskort på källdatorn är mindre än eller lika med antalet nätverkskort som tillåts för måldatorns storlek så kommer målet att ha samma antal kort som källan.
*   Om antalet nätverkskort för den virtuella källdatorn överskrider det tillåtna antalet för målstorleken så används den högsta målstorleken.
* Om en källdator exempelvis har två nätverkskort och måldatorn stöder fyra så kommer måldatorn att ha två kort. Om källdatorn har två nätverkskort men målstorleken endast stöder ett så kommer måldatorn bara att ha ett kort.
*   Om den virtuella datorn har flera nätverkskort kommer alla att ansluta till samma nätverk.
*   Om den virtuella datorn har flera nätverkskort blir den första som visas i listan standard nätverkskortet på den virtuella Azure-datorn.


## <a name="creating-a-recovery-plan"></a>Skapa en återställnings plan

När replikering har Aktiver ATS för XenApp-komponentens virtuella datorer är nästa steg att skapa en återställnings plan.
En återställnings plan grupper samman virtuella datorer med liknande krav för redundans och återställning.  

**Steg för att skapa en återställnings plan**

1. Lägg till XenApp-komponentens virtuella datorer i återställnings planen.
2. Klicka på återställnings planer-> + återställnings plan. Ange ett intuitivt namn för återställnings planen.
3. För virtuella VMware-datorer: Välj källa som VMware processerver, mål som Microsoft Azure och distributions modell som Resource Manager och klicka på Välj objekt.
4. För virtuella Hyper-V-datorer: Välj källa som VMM-Server, mål som Microsoft Azure och distributions modell som Resource Manager och klicka på Välj objekt och välj sedan de virtuella datorerna för XenApp-distributionen.

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i grupper för redundans

Återställnings planer kan anpassas för att lägga till failover-grupper för en speciell start ordning, skript eller manuella åtgärder. Följande grupper måste läggas till i återställnings planen.

1. Redundans för redundans: AD DNS
2. Group2 för redundans: SQL Server virtuella datorer
2. Redundans Group3: VDA Master image VM
3. Group4: leverans styrenhet och butik Server-VM: ar


### <a name="adding-scripts-to-the-recovery-plan"></a>Lägga till skript i återställnings planen

Skript kan köras före eller efter en angiven grupp i en återställnings plan. Manuella åtgärder kan också inkluderas och utföras under redundansväxlingen.

Den anpassade återställnings planen ser ut så här:

1. Redundans för redundans: AD DNS
2. Group2 för redundans: SQL Server virtuella datorer
3. Redundans Group3: VDA Master image VM

   >[!NOTE]     
   >Steg 4, 6 och 7 som innehåller manuella eller skript åtgärder kan bara användas på en lokal XenApp >-miljö med MCS/PVS-kataloger.

4. Grupp 3 manuell eller skript åtgärd: Stäng av huvud VDA VM.
Huvud-VDA VM vid växling till Azure är i ett körnings läge. Om du vill skapa nya MCS-kataloger med Azure-värd måste den virtuella VDA-datorn vara i stoppat läge (de har allokerats). Stäng av den virtuella datorn från Azure Portal.

5. Group4: leverans styrenhet och butik Server-VM: ar
6. Group3 manuell eller skript åtgärd 1:

    ***Lägg till Azure RM-värd anslutning***

    Skapa en Azure-värd anslutning på leverans styrenhets datorn för att etablera nya MCS-kataloger i Azure. Följ stegen som beskrivs i den här [artikeln](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Group3 manuell eller skript åtgärd 2:

    ***Återskapa MCS-kataloger i Azure***

    Befintliga MCS-eller PVS-kloner på den primära platsen kommer inte att replikeras till Azure. Du måste återskapa dessa kloner med hjälp av den replikerade huvud-VDA och Azure-etableringen från leverans kontroll enheten. Följ stegen som beskrivs i den här [artikeln](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) för att skapa MCS-kataloger i Azure.

![Återställnings plan för XenApp-komponenter](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Du kan använda skript på plats för att uppdatera DNS med de nya IP- [adresserna](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) för de misslyckade >virtuella datorerna eller för att koppla en belastningsutjämnare på den misslyckade över virtuella datorn, om det behövs.


## <a name="doing-a-test-failover"></a>Utföra en redundanstest

Följ [den här vägledningen](site-recovery-test-failover-to-azure.md) för att utföra ett redundanstest.

![Återställningsplan](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Utföra en redundansväxling

Följ [den här vägledningen](site-recovery-failover.md) när du utför en redundansväxling.

## <a name="next-steps"></a>Nästa steg

Du kan [lära dig mer](https://aka.ms/citrix-xenapp-xendesktop-with-asr) om att replikera Citrix XenApp-och XenDesktop-distributioner i det här White Paper. Titta närmare på vägledningen för att [Replikera andra program](site-recovery-workload.md) med hjälp av Site Recovery.
