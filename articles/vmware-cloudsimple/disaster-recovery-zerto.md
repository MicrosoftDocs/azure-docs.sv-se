---
title: Azure VMware-lösning av CloudSimple – Använd privat moln som katastrof webbplats för lokala arbets belastningar
description: Beskriver hur du konfigurerar ditt CloudSimple privata moln som en katastrof återställnings plats för lokala VMware-arbetsbelastningar
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5dd1f157984cdb300cc3b375d71654e5da54d1ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898138"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Konfigurera CloudSimple privata moln som en katastrof återställnings plats för lokala VMware-arbetsbelastningar

Ditt privata CloudSimple-moln kan konfigureras som en återställnings plats för lokala program för att ge affärs kontinuitet i händelse av en katastrof. Återställnings lösningen baseras på Zerto virtuella replikering som replikerings-och Dirigerings plattform. Kritiska infrastruktur-och program virtuella datorer kan replikeras kontinuerligt från din lokala vCenter till ditt privata moln. Du kan använda ditt privata moln för testning av redundans och se till att programmet är tillgängligt under en katastrof. En liknande metod kan följas för att konfigurera det privata molnet som en primär plats som skyddas av en återställnings plats på en annan plats.

> [!NOTE]
> Mer information om hur du ändrar storlek på din katastrof återställnings miljö hittar du i Zerto dokument [storleks överväganden för Zerto virtuell replikering](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) .

CloudSimple-lösningen:

* Eliminerar behovet av att konfigurera ett Data Center specifikt för haveri beredskap (DR).
* Gör att du kan använda Azure-platser där CloudSimple distribueras för internationell geografisk återhämtning.
* Ger dig ett alternativ för att minska kostnaderna för distribution och den totala ägande kostnaden för DR.

Lösningen kräver att du:

* Installera, konfigurera och hantera Zerto i ditt privata moln.
* Ange egna licenser för Zerto när det privata molnet är den skyddade platsen. Du kan para ihop Zerto som körs på CloudSimple-platsen med din lokala plats för licensiering.

Följande bild visar arkitekturen för Zerto-lösningen.

![Arkitektur](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Så här distribuerar du lösningen

I följande avsnitt beskrivs hur du distribuerar en DR-lösning med Zerto Virtual Replication i ditt privata moln.

1. [Förutsättningar](#prerequisites)
2. [Valfri konfiguration i CloudSimple privat moln](#optional-configuration-on-your-private-cloud)
3. [Konfigurera ZVM och VRA i privat CloudSimple-moln](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Konfigurera Zerto virtuella skydds grupp](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Förutsättningar

Om du vill aktivera Zerto virtuell replikering från din lokala miljö till ditt privata moln måste du uppfylla följande krav.

1. [Konfigurera en plats-till-plats-VPN-anslutning mellan ditt lokala nätverk och ditt CloudSimple privata moln](set-up-vpn.md).
2. [Konfigurera DNS-sökning så att dina hanterings komponenter för privata moln vidarebefordras till privata moln DNS-servrar](on-premises-dns-setup.md).  Om du vill aktivera vidarebefordran av DNS-sökning skapar du en post för vidarebefordran i din lokala DNS-server för `*.cloudsimple.io` att CloudSimple DNS-servrar.
3. Konfigurera DNS-sökning så att lokala vCenter-komponenter vidarebefordras till lokala DNS-servrar.  DNS-servrarna måste gå att komma åt från ditt CloudSimple privata moln över plats-till-plats-VPN. Om du behöver hjälp kan du skicka en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)och ange följande information.  

    * Lokalt DNS-domännamn
    * Lokala DNS-servrar IP-adresser

4. Installera en Windows Server i ditt privata moln. Servern används för att installera Zerto Virtual Manager.
5. [Eskalera dina CloudSimple-privilegier](escalate-private-cloud-privileges.md).
6. Skapa en ny användare i ditt privata moln vCenter med den administrativa rollen som ska användas som tjänst konto för Zerto Virtual Manager.

### <a name="optional-configuration-on-your-private-cloud"></a>Valfri konfiguration i ditt privata moln

1. Skapa en eller flera resurspooler i ditt privata moln vCenter för att använda som mål resurspooler för virtuella datorer från din lokala miljö.
2. Skapa en eller flera mappar i ditt privata moln vCenter för att använda som målmappar för virtuella datorer från din lokala miljö.
3. Skapa VLAN för nätverks växlings nätverk och konfigurera brand Väggs regler. Öppna en [support förfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) om du behöver hjälp.
4. Skapa distribuerade Port grupper för nätverk för växling vid fel och testa nätverk för att testa redundansväxlingen av virtuella datorer.
5. Installera [DHCP-och DNS-servrar](dns-dhcp-setup.md) eller använd en Active Directory domänkontrollant i din privata moln miljö.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Konfigurera ZVM och VRA i ditt privata moln

1. Installera Zerto Virtual Manager (ZVM) på Windows Server i ditt privata moln.
2. Logga in på ZVM med det tjänst konto som skapades i föregående steg.
3. Konfigurera licensiering för Zerto Virtual Manager.
4. Installera Zerto Virtual Replication Installer (VRA) på ESXi-värdar för ditt privata moln.
5. Para ihop ditt privata moln ZVM med din lokala ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Konfigurera Zerto virtuella skydds grupp

1. Skapa en ny virtuell skydds grupp (VPG) och ange prioriteten för VPG.
2. Välj de virtuella datorer som kräver skydd för affärs kontinuitet och anpassa start ordningen om det behövs.
3. Välj återställnings platsen som ditt privata moln och standard återställnings servern som det privata moln klustret eller resurs gruppen som du skapade. Välj **vsanDatastore** för återställnings data lagret i ditt privata moln.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Du kan anpassa värd alternativet för enskilda virtuella datorer under alternativet VM-inställningar.

4. Anpassa lagrings alternativ efter behov.
5. Ange de återställnings nätverk som ska användas för nätverks-och redundanstest som de distribuerade port grupperna som skapades tidigare och anpassa återställnings skripten efter behov.
6. Anpassa nätverks inställningarna för enskilda virtuella datorer vid behov och skapa VPG.
7. Testa redundans när replikeringen har slutförts.

## <a name="reference"></a>Referens

[Dokumentation om Zerto](https://www.zerto.com/myzerto/technical-documentation/)
