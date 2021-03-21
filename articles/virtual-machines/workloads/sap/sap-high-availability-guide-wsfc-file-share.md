---
title: Kluster-SAP ASCS/SCS på WSFC med fil resurs i Azure | Microsoft Docs
description: Lär dig att klustra en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en fil resurs i Azure.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2021
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a51f874d09aebfcb2c0b73e0b484f68042d1bb6d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496209"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Klustra en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en fil resurs i Azure

> ![Windows-logotyp.][Logo_Windows] Windows
>

Windows Server-redundanskluster är grunden för en hög tillgänglig SAP-ASCS/SCS-installation och DBMS i Windows.

Ett redundanskluster är en grupp med 1 + n oberoende servrar (noder) som arbetar tillsammans för att öka tillgängligheten för program och tjänster. Om ett nodfel inträffar beräknar Windows Server-redundanskluster antalet fel som kan uppstå och fortfarande upprätthåller ett felfritt kluster för att tillhandahålla program och tjänster. Du kan välja mellan olika kvorumresurser för att nå redundanskluster.

## <a name="prerequisites"></a>Förutsättningar
Läs igenom den här artikeln innan du påbörjar de uppgifter som beskrivs i den här artikeln:

* [Azure Virtual Machines hög tillgänglighets arkitektur och scenarier för SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Att klustra SAP ASCS/SCS-instanser med hjälp av en fil resurs stöds för SAP NetWeaver 7,40 (och senare), med SAP kernel 7,49 (och senare).
>


## <a name="windows-server-failover-clustering-in-azure"></a>Kluster för växling vid fel i Windows Server i Azure

Jämfört med distributioner av Bare Metal eller privata moln kräver Azure Virtual Machines ytterligare steg för att konfigurera Windows Server-redundanskluster. När du skapar ett kluster måste du ange flera IP-adresser och virtuella värdnamn för SAP ASCS/SCS-instansen.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Namn matchning i Azure och klustrets virtuella värd namn

Azure Cloud Platform erbjuder inte alternativet att konfigurera virtuella IP-adresser, till exempel flytande IP-adresser. Du behöver en alternativ lösning för att skapa en virtuell IP-adress för att komma åt kluster resursen i molnet. 

Tjänsten Azure Load Balancer tillhandahåller en *intern belastningsutjämnare* för Azure. Med den interna belastningsutjämnaren når klienterna klustret via klustrets virtuella IP-adress. 

Distribuera den interna belastningsutjämnaren i resurs gruppen som innehåller klusternoderna. Konfigurera sedan alla nödvändiga regler för port vidarebefordran med hjälp av avsöknings portarna för den interna belastningsutjämnaren. Klienterna kan ansluta via det virtuella värd namnet. DNS-servern matchar klustrets IP-adress. Den interna belastningsutjämnaren hanterar port vidarebefordran till klustrets aktiva nod.

![Bild 1: konfiguration av redundanskluster för Windows Server i Azure utan en delad disk][sap-ha-guide-figure-1001]

_**Bild 1:** Konfiguration av redundanskluster för Windows Server i Azure utan en delad disk_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS HA med fil resurs

SAP utvecklade en ny metod och ett alternativ till klusterdelade diskar för klustring av en SAP ASCS/SCS-instans i ett Windows-redundanskluster. I stället för att använda klusterdelade diskar kan du använda en SMB-filresurs för att distribuera globala SAP-databasfiler.

> [!NOTE]
> En SMB-filresurs är ett alternativ till att använda delade kluster diskar för klustring av SAP ASCS/SCS-instanser.  
>

Den här arkitekturen är speciell på följande sätt:

* De centrala SAP-tjänsterna (med en egen fil struktur och meddelanden och köer) är åtskilda från SAP global Host-filer.
* SAP Central Services körs under en SAP ASCS/SCS-instans.
* SAP ASCS/SCS-instansen är klustrad och kan nås med hjälp av det \<ASCS/SCS virtual host name\> virtuella värd namnet.
* Globala SAP-filer placeras på SMB-filresursen och nås med hjälp av \<SAP global host\> värd namnet: \\ \\ &lt; SAP global Host &gt; \sapmnt \\ &lt; sid &gt; \SYS \. ..
* SAP ASCS/SCS-instansen är installerad på en lokal disk på båda klusternoderna.
* \<ASCS/SCS virtual host name\>Nätverks namnet skiljer sig från &lt; SAP global-värden &gt; .

![Bild 2: SAP ASCS/SCS HA-arkitektur med SMB-filresurs][sap-ha-guide-figure-8004]

_**Bild 2:** Ny SAP ASCS/SCS HA-arkitektur med en SMB-filresurs_

Krav för en SMB-fil resurs:

* SMB 3,0-protokoll (eller senare).
* Möjlighet att ange Active Directory åtkomst kontrol listor (ACL: er) för Active Directory användar grupper och `computer$` datorobjektet.
* Fil resursen måste ha stöd för:
    * Diskar som används för att lagra filer får inte vara en enskild felpunkt.
    * Server-eller VM-stillestånd orsakar ingen stillestånds tid på fil resursen.

Rollen SAP- \<SID\> kluster innehåller inte klusterdelade diskar eller en resurs för en allmän fil resurs.


![Bild 3: \< resurser för SAP sid- \> kluster roll för användning av en fil resurs][sap-ha-guide-figure-8005]

_**Bild 3:** SAP &lt; sid- &gt; kluster roll resurser för att använda en fil resurs_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Skalbara fil resurser med Lagringsdirigering i Azure som en SAPMNT fil resurs

Du kan använda en skalbar fil resurs för att vara värd för och skydda SAP global Hosts-filer. En skalbar fil resurs erbjuder också en SAPMNT fil resurs tjänst med hög tillgänglighet.

![Bild 4: skalbar fil resurs som används för att skydda SAP global Hosts-filer][sap-ha-guide-figure-8006]

_**Bild 4:** En skalbar fil resurs som används för att skydda SAP global Hosts-filer_

> [!IMPORTANT]
> Skalbara fil resurser stöds fullt ut i Microsoft Azure molnet och i lokala miljöer.
>

En skalbar fil resurs erbjuder en hög tillgänglig och vågrätt skalbar SAPMNT fil resurs.

Lagringsdirigering används som en delad disk för en skalbar fil resurs. Du kan använda Lagringsdirigering för att bygga hög tillgänglig och skalbar lagring med hjälp av servrar med lokal lagring. Delad lagring som används för en skalbar fil resurs, till exempel för globala SAP-databasfiler, är inte en enskild felpunkt.

Tänk på följande när du väljer Lagringsdirigering:

- De virtuella datorer som används för att skapa Lagringsdirigering-klustret måste distribueras i en Azures tillgänglighets uppsättning.
- För haveri beredskap för ett Lagringsdirigering-kluster kan du använda [Azure Site Recovery-tjänster](../../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage).
- Det finns inte stöd för att sträcka ut lagrings utrymmes Dirigerings klustret mellan olika Azure-tillgänglighetszoner.

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>SAP-krav för skalbara fil resurser i Azure

Om du vill använda en skalbar fil resurs måste systemet uppfylla följande krav:

* Minst två klusternoder för en skalbar fil resurs.
* Varje nod måste ha minst två lokala diskar.
* Av prestanda skäl måste du använda *speglings återhämtning*:
    * Dubbelriktad spegling för en skalbar fil resurs med två klusternoder.
    * Tre-vägs spegling för en skalbar fil resurs med tre (eller fler) klusternoder.
* Vi rekommenderar tre (eller fler) klusternoder för en skalbar fil resurs med tre vägs spegling.
    Den här installationen ger större skalbarhet och mer lagrings återhämtning än den skalbara fil resurs konfigurationen med två klusternoder och dubbelriktad spegling.
* Du måste använda Azure Premium-diskar.
* Vi rekommenderar att du använder Azure Managed Disks.
* Vi rekommenderar att du formaterar volymer med hjälp av ReFS (elastiskt fil system).
    * Mer information finns i [SAP Note 1869038-SAP-stöd för ReFs-filsystem][1869038] och [Välj fil system][planning-volumes-s2d-choosing-filesystem] kapitlet i artikeln Planera volymer i Lagringsdirigering.
    * Se till att du installerar den [kumulativa uppdateringen av Microsoft KB4025334][kb4025334].
* Du kan använda DS-Series eller DSv2-Series storlekar för virtuella Azure-datorer.
* Använd en VM-typ som har minst en "hög" nätverks bandbredd för bästa nätverks prestanda mellan virtuella datorer, vilket krävs för att Lagringsdirigering synkronisering av disk.
    Mer information finns i specifikationer för [DSv2-serien][dv2-series] och [DS-serien][ds-series] .
* Vi rekommenderar att du reserverar en del ej allokerad kapacitet i lagringspoolen. Om du lämnar en icke-allokerad kapacitet i lagringspoolen får du volym utrymme att reparera på plats. om en enhet kraschar. Detta förbättrar data säkerhet och prestanda.  Mer information finns i [välja volym storlek][choosing-the-size-of-volumes-s2d].
* Du behöver inte konfigurera den interna Azure-belastningsutjämnaren för den skalbara fil resursens nätverks namn, till exempel för \<SAP global host\> . Detta görs för \<ASCS/SCS virtual host name\> SAP ASCS/SCS-instansen eller för DBMS. En skalbar fil resurs skalar upp belastningen på alla klusternoder. \<SAP global host\> använder den lokala IP-adressen för alla klusternoder.


> [!IMPORTANT]
> Du kan inte byta namn på den SAPMNT fil resurs som pekar på \<SAP global host\> . SAP stöder endast resurs namnet "sapmnt".
>
> Mer information finns i [SAP Note 2492395-kan du ändra resurs namnet sapmnt?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Konfigurera SAP ASCS/SCS-instanser och en skalbar fil resurs i två kluster

Du måste distribuera SAP ASCS/SCS-instanserna i ett separat kluster med deras egna SAP- \<SID\> kluster roll. I det här fallet kan du konfigurera den skalbara fil resursen i ett annat kluster med en annan kluster roll.


> [!IMPORTANT]
> Installationen måste uppfylla följande krav: SAP ASCS/SCS-instanserna och SOFS-resursen måste distribueras i separata kluster.    
>
> [!IMPORTANT] 
> I det här scenariot konfigureras SAP ASCS/SCS-instansen för åtkomst till den globala SAP-värden med hjälp av UNC-sökväg \\ \\ &lt; SAP global Host &gt; \sapmnt \\ &lt; sid &gt; \SYS\.
>

![Bild 5: SAP ASCS/SCS-instans och en skalbar fil resurs som distribueras i två kluster][sap-ha-guide-figure-8007]

_**Figur 5:** En SAP ASCS/SCS-instans och en skalbar fil resurs som distribueras i två kluster_

> [!IMPORTANT]
> I Azure-molnet måste varje kluster som används för SAP och skalbara fil resurser distribueras i en egen Azure-tillgänglighets uppsättning eller mellan Azure-tillgänglighetszoner. Detta säkerställer distribuerad placering av de virtuella datorerna i klustret över den underliggande Azure-infrastrukturen. Distributioner av tillgänglighets zoner stöds med den här tekniken.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Allmän fil resurs med SIOS DataKeeper som klusterdelade diskar


En allmän fil resurs är ett annat alternativ för att uppnå en fil resurs med hög tillgänglighet.

I det här fallet kan du använda en SIOS-lösning från tredje part som en klusterdelad disk.

## <a name="next-steps"></a>Nästa steg

* [Förbered Azure-infrastrukturen för SAP HA genom att använda ett Windows-redundanskluster och en fil resurs för en SAP ASCS/SCS-instans][sap-high-availability-infrastructure-wsfc-file-share]
* [Installera SAP NetWeaver HA på ett Windows-redundanskluster och en fil resurs för en SAP ASCS/SCS-instans][sap-high-availability-installation-wsfc-shared-disk]
* [Distribuera en skalbar fil server med två noder Lagringsdirigering för UPD-lagring i Azure][deploy-sofs-s2d-in-azure]
* [Lagringsdirigering i Windows Server 2016][s2d-in-win-2016]
* [Djupet: volymer i Lagringsdirigering][deep-dive-volumes-in-s2d]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:../../dv2-dsv2-series.md
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID-konfiguration med hög tillgänglighet)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 
