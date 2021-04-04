---
title: Azure VMware-lösning av CloudSimple-migrera virtuella arbets datorer till ett privat moln
description: Beskriver hur du migrerar virtuella datorer från lokala vCenter till CloudSimple Private Cloud vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 851305775d14ed371440434b2fbe9334ec0a21b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899328"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Migrera virtuella arbets belastnings datorer från lokal vCenter till privat moln vCenter-miljö

Det finns flera tillgängliga alternativ för att migrera virtuella datorer från ett lokalt Data Center till ditt privata CloudSimple-moln.  Det privata molnet ger inbyggd åtkomst till VMware vCenter och verktyg som stöds av VMware kan användas för migrering av arbets belastning. I den här artikeln beskrivs några av alternativen för vCenter-migrering.

## <a name="prerequisites"></a>Förutsättningar

Migrering av virtuella datorer och data från ditt lokala data Center kräver nätverks anslutning från data centret till din privata moln miljö.  Använd någon av följande metoder för att upprätta en nätverks anslutning:

* [Plats-till-plats-VPN-anslutning](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) mellan din lokala miljö och ditt privata moln.
* ExpressRoute Global Reach anslutning mellan din lokala ExpressRoute-krets och en CloudSimple ExpressRoute-krets.

Nätverks Sök vägen från din lokala vCenter-miljö till ditt privata moln måste vara tillgänglig för migrering av virtuella datorer med vMotion.  VMotion-nätverket på din lokala vCenter måste ha routningsfunktioner.  Kontrol lera att brand väggen tillåter all vMotion trafik mellan din lokala vCenter och det privata molnet vCenter. (I det privata molnet är routning på vMotion-nätverket konfigurerat som standard.)

## <a name="migrate-isos-and-templates"></a>Migrera ISO och mallar

Använd ISO och mallar för virtuella datorer för att skapa nya virtuella datorer i ditt privata moln.  Använd följande metod för att ladda upp ISO och mallar till ditt privata moln vCenter och göra dem tillgängliga.

1. Överför ISO till det privata molnet vCenter från vCenter-ANVÄNDARGRÄNSSNITTET.
2. [Publicera ett innehålls bibliotek](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) i ditt privata moln vCenter:

    1. Publicera ditt lokala innehålls bibliotek.
    2. Skapa ett nytt innehålls bibliotek i det privata molnet vCenter.
    3. Prenumerera på det publicerade lokala innehålls biblioteket.
    4. Synkronisera innehålls biblioteket för åtkomst till det innehåll som du prenumererar på.

## <a name="migrate-vms-using-powercli"></a>Migrera virtuella datorer med PowerCLI

Om du vill migrera virtuella datorer från den lokala vCenter-platsen till det privata molnets vCenter, använder du VMware PowerCLI eller Cross vCenter-arbetsbelastnings verktyget för migrering från VMware Labs.  Följande exempel skript visar migrerings kommandona för PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Om du vill använda namnen på mål vCenter-servern och ESXi-värdar konfigurerar du DNS-vidarebefordran från lokal plats till ditt privata moln.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Migrera virtuella datorer med NSX Layer 2 VPN

Med det här alternativet aktive ras direktmigrering av arbets belastningar från din lokala VMware-miljö till det privata molnet i Azure.  Med detta utsträckta Layer 2-nätverk kommer under nätet från lokalt att vara tillgängligt i det privata molnet.  Efter migreringen krävs ingen ny tilldelning av IP-adresser för de virtuella datorerna.

[Migrera arbets belastningar med Layer 2 utsträckta nätverk](migration-layer-2-vpn.md) beskriver hur du använder ett Layer 2 VPN för att sträcka ut ett Layer 2-nätverk från din lokala miljö till ditt privata moln.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Migrera virtuella datorer med säkerhets kopierings-och katastrof återställnings verktyg

Migrering av virtuella datorer till privata moln kan göras med verktyg för säkerhets kopiering/återställning och haveri beredskap.  Använd det privata molnet som mål för återställning från säkerhets kopior som skapas med hjälp av ett verktyg från tredje part.  Det privata molnet kan också användas som mål för haveri beredskap med VMware SRM eller ett verktyg från tredje part.

Mer information med dessa verktyg finns i följande avsnitt:

* [Säkerhetskopiera virtuella arbets belastnings datorer på CloudSimple privata moln med Veeam B&R](backup-workloads-veeam.md)
* [Konfigurera CloudSimple privata moln som en katastrof återställnings plats för lokala VMware-arbetsbelastningar](disaster-recovery-zerto.md)
