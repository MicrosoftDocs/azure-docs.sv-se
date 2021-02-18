---
title: Virtuella Azure-datorer HA för SAP NW på Windows med Azure NetApp Files (SMB) | Microsoft Docs
description: Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i Windows med Azure NetApp Files (SMB) för SAP-program
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.openlocfilehash: b04dacfdedded417e2557d1568e01bc9fa8f5745
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590120"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i Windows med Azure NetApp Files (SMB) för SAP-program

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Den här artikeln beskriver hur du distribuerar, konfigurerar de virtuella datorerna, installerar kluster ramverket och installerar ett SAP NetWeaver 7,50-system med hög tillgänglighet på virtuella Windows-datorer med hjälp av [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) på [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

Databas lagret beskrivs inte i detalj i den här artikeln. Vi förutsätter att det [virtuella](../../../virtual-network/virtual-networks-overview.md) Azure-nätverket redan har skapats.  

Läs följande SAP-anteckningar och dokument först:

* [Azure NetApp Files dokumentation][anf-azure-doc] 
* SAP anmärkning [1928533][1928533], som innehåller:  
  * En lista med storlekar för virtuella Azure-datorer som stöds för distribution av SAP-program
  * Viktig kapacitets information för Azure VM-storlekar
  * Stöd för SAP-program och operativ system (OS) och databas kombinationer
  * Nödvändig SAP kernel-version för Windows på Microsoft Azure
* SAP NOTE [2015553][2015553] visar krav för SAP-program distributioner som stöds i Azure.
* SAP NOTE [2178632][2178632] innehåller detaljerad information om alla övervaknings mått som rapporter ATS för SAP i Azure.
* SAP anmärkning [1999351][1999351] innehåller ytterligare felsöknings information för Azure Enhanced Monitoring-tillägget för SAP.
* SAP anmärkning [2287140](https://launchpad.support.sap.com/#/notes/2287140) visar krav för en SAP-kompatibel ca-funktion i SMB 3. x-protokollet.
* SAP NOTE [2802770](https://launchpad.support.sap.com/#/notes/2802770) innehåller felsöknings information för den långsamma SAP-transaktionen AL11 på Windows 2012 och 2016.
* SAP NOTE [1911507](https://launchpad.support.sap.com/#/notes/1911507) innehåller information om funktionen transparent redundans för en fil resurs i Windows Server med SMB 3,0-protokollet.
* SAP NOTE [662452](https://launchpad.support.sap.com/#/notes/662452) har rekommendation (inaktivera generering av 8,3-namn) för att lösa dåliga fil system prestanda/fel vid data åtkomst.
* [Installera SAP NetWeaver med hög tillgänglighet på ett Windows-redundanskluster och en fil resurs för SAP ASCS/SCS-instanser på Azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Azure Virtual Machines hög tillgänglighets arkitektur och scenarier för SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
* [Lägg till avsöknings port i ASCS kluster konfiguration](sap-high-availability-installation-wsfc-file-share.md)
* [Installation av en (A) SCS-instans i ett redundanskluster](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Skapa en SMB-volym för Azure NetApp Files](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)
* [NetApp SAP-program på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Översikt

SAP utvecklade en ny metod och ett alternativ till klusterdelade diskar för klustring av en SAP ASCS/SCS-instans i ett Windows-redundanskluster. I stället för att använda klusterdelade diskar kan du använda en SMB-filresurs för att distribuera globala SAP-databasfiler. Azure NetApp Files stöder SMBv3 (tillsammans med NFS) med NTFS-ACL med Active Directory. Azure NetApp Files har automatiskt hög tillgänglighet (eftersom det är en PaaS-tjänst). Dessa funktioner gör Azure NetApp Files bra alternativ för att vara värd för SMB-filresursen för SAP global.  
Både [Azure Active Directory (AD) Domain Services](../../../active-directory-domain-services/overview.md) och [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) stöds. Du kan använda befintliga Active Directory domänkontrollanter med Azure NetApp Files. Domänkontrollanter kan vara i Azure som virtuella datorer eller lokalt via ExpressRoute eller S2S VPN. I den här artikeln kommer vi att använda domänkontrollanten i en virtuell Azure-dator.  
Hög tillgänglighet för SAP NetWeaver Central Services kräver delad lagring. För att uppnå detta i Windows, så hittills var det nödvändigt att skapa antingen SOFS-kluster eller Använd klusterdelad disk s/w som SIOS. Nu är det möjligt att uppnå SAP NetWeaver HA med hjälp av delad lagring, distribuerat på Azure NetApp Files. Om du använder Azure NetApp Files för delad lagring elimineras behovet av antingen SOFS eller SIOS.  

> [!NOTE]
> Att klustra SAP ASCS/SCS-instanser med hjälp av en fil resurs stöds för SAP NetWeaver 7,40 (och senare), med SAP kernel 7,49 (och senare).  

![SAP ASCS/SCS HA-arkitektur med SMB-resurs](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Kraven för en SMB-filresurs är:
* SMB 3,0-protokoll (eller senare).
* Möjlighet att ange Active Directory åtkomst kontrol listor (ACL: er) för Active Directory användar grupper och objektet $ Computer.
* Fil resursen måste ha stöd för-aktiverat.

Resursen för de centrala SAP-tjänsterna i denna referens arkitektur erbjuds av Azure NetApp Files:

![SAP ASCS/SCS HA-arkitektur med SMB-resursinformation](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Skapa och montera SMB-volym för Azure NetApp Files

Utför följande steg, som förberedelse för användning av Azure NetApp Files.  

1. Följ stegen för att [Registrera dig för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md)  
2. Skapa ett Azure NetApp-konto genom att följa stegen som beskrivs i  [skapa ett NetApp-konto](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)  
3. Konfigurera kapacitetsutnyttjandet genom att följa anvisningarna i [Konfigurera en pool för kapacitet](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
4. Azure NetApp Files-resurser måste finnas i delegerade undernät. Följ instruktionerna i [delegera ett undernät till Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) för att skapa delegerade undernät.  

   > [!IMPORTANT]
   > Du måste skapa Active Directory anslutningar innan du skapar en SMB-volym. Granska [kraven för Active Directory anslutningar](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections).  

5. Skapa Active Directory anslutning enligt beskrivningen i [skapa en Active Directory anslutning](../../../azure-netapp-files/create-active-directory-connections.md#create-an-active-directory-connection)  
6. Skapa SMB-Azure NetApp Files SMB-volym genom att följa anvisningarna i [lägga till en SMB-volym](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)  
7. Montera SMB-volymen på den virtuella Windows-datorn.

> [!TIP]
> Du hittar anvisningar om hur du monterar Azure NetApp Files volym, om du navigerar i [Azure Portal](https://portal.azure.com/#home) till Azure NetApp Files-objektet, klickar på bladet **volymer** och sedan **monterar instruktionerna**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Förbereda infrastrukturen för SAP-HA med hjälp av ett Windows-redundanskluster 

1. [Ange belastnings Utjämnings reglerna för ASCS/SCS för den interna Azure-belastningsutjämnaren](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716).
2. [Lägg till virtuella Windows-datorer i domänen](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
3. [Lägg till register poster på båda klusternoderna för SAP ASCS/SCS-instansen](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [Konfigurera ett Windows Server-redundanskluster för en SAP ASCS/SCS-instans](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Om du använder Windows Server 2016 rekommenderar vi att du konfigurerar [Azure Cloud-vittnet](/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Installera SAP ASCS-instans på båda noderna

Du behöver följande program vara från SAP:
   * SAP Software Provisioning Manager (SWPM)-installations verktyg version SPS25 eller senare.
   * SAP kernel 7,49 eller senare
   * Skapa ett virtuellt värdnamn (kluster nätverks namn) för den klustrade SAP ASCS/SCS-instansen, enligt beskrivningen i [skapa ett virtuellt värdnamn för den klustrade SAP-ASCS/SCS-instansen](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Att klustra SAP ASCS/SCS-instanser med hjälp av en fil resurs stöds för SAP NetWeaver 7,40 (och senare), med SAP kernel 7,49 (och senare).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Installera en ASCS/SCS-instans på den första ASCS/SCS-klusternoden

1. Installera en SAP ASCS/SCS-instans på den första klusternoden. Starta installations verktyget för SAP-SWPM och gå sedan till: **produkt**-  >  **DBMS** > installation > program Server ABAP (eller Java) > High-Availability system > ASCS/SCS-instans > första klusternoden.  

2. Välj **fil resurs kluster** som kluster resurs konfiguration i SWPM.  
3. När du uppmanas till steg **SAP-systemets kluster parametrar** anger du värd namnet för den Azure NetApp Files SMB-resurs som du redan har skapat som **fil resurs värd namn**.  I det här exemplet är SMB-resursens värdnamn **anfsmb-9562**. 

   > [!IMPORTANT]
   > Om krav kontrollen i SWPM visar att funktions villkoret för kontinuerlig tillgänglighet inte uppfylls, kan du lösa det genom att följa anvisningarna i [fördröjt fel meddelande när du försöker komma åt en delad mapp som inte längre finns i Windows](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

   > [!TIP]
   > Om krav kontrollen i SWPM visar att växlings storleks villkoret inte uppfylls, kan du justera VÄXLINGs storleken genom att gå till den här datorn>system egenskaper>prestanda inställningar> avancerad> virtuellt minne> ändring.  

4. Konfigurera en SAP-kluster resurs, `SAP-SID-IP` avsöknings porten med hjälp av PowerShell. Kör den här konfigurationen på en av SAP-ASCS/SCS-klusternoderna, enligt beskrivningen i [Konfigurera avsöknings port](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Installera en ASCS/SCS-instans på den andra ASCS/SCS-klusternoden

1. Installera en SAP ASCS/SCS-instans på den andra klusternoden. Starta installations verktyget för SAP-SWPM och navigera sedan till **produkt**  >  -**DBMS** > installation > program Server ABAP (eller Java) > High-Availability system > ASCS/SCS-instans > ytterligare klusternod.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Installera en DBMS-instans och SAP-program servrar

Slutför din SAP-installation genom att installera:

   * En DBMS-instans  
   * En primär SAP-Programserver  
   * Ytterligare en SAP-Programserver  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testa redundansväxling av SAP ASCS/SCS-instansen 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Redundansväxla från klusternod A till klusternod B och tillbaka
I det här test scenariot kommer vi att referera till klusternoden sapascs1 som Node A, och till klusternoden sapascs2 som Node B.

1. Kontrol lera att kluster resurserna körs på nod A. ![ Bild 1: Windows Server-redundanskluster som körs på nod A före redundans testet](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Starta om klusternod A. SAP-kluster resurserna flyttas till klusternod B. ![ Bild 2: kluster resurser för Windows Server som körs på nod B efter redundansväxlingen](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Lås post-test

1. kontrol lera att tjänsten SAP Queue Replication (ERS) är aktiv  
2. Logga in på SAP-systemet, kör Transaction SU01 och öppna ett användar-ID i ändrings läge. Som genererar SAP-lås.  
3. När du är inloggad i SAP-systemet visar du Lås posten genom att gå till Transaction ST12.  
4. Redundansväxla ASCS-resurser från klusternod A till klusternod B.  
5. Kontrol lera att Lås posten genererades innan redundansväxlingen för SAP ASCS/SCS-kluster resurser bevaras.  

![Bild 3: Lås posten behålls efter redundansväxlingen](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Mer information finns i [fel sökning för att köa redundans i ASCS med ers](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* För att lära dig att upprätta hög tillgänglighet och planera för haveri beredskap för SAP 
* HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md).
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på virtuella Azure-datorer finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha]
