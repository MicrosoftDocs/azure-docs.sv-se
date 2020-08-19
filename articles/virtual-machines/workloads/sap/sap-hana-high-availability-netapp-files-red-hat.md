---
title: Hög tillgänglighet för SAP HANA skala upp med ANF på RHEL | Microsoft Docs
description: Upprätta hög tillgänglighet för SAP HANA med ANF på virtuella Azure-datorer (VM).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/11/2020
ms.author: radeltch
ms.openlocfilehash: 030677276fa077c06a95e7c677fec956b9c2a947
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556406"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Hög tillgänglighet för SAP HANA skala upp med Azure NetApp Files på Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Den här artikeln beskriver hur du konfigurerar SAP HANA systemreplikering i storskalig distribution, när HANA-filsystem monteras via NFS, med Azure NetApp Files (ANF). I exempel på konfigurationer och installations kommandon används instans nummer **03**och Hana system-ID **HN1** . SAP HANA replikeringen består av en primär nod och minst en sekundär nod.

När steg i det här dokumentet är markerade med följande prefix är innebörden följande:

- **[A]**: steget gäller för alla noder
- **[1]**: steget gäller endast Nod1
- **[2]**: steget gäller endast för NOD2
 
Läs följande SAP-anteckningar och dokument först:

- SAP anmärkning [1928533](https://launchpad.support.sap.com/#/notes/1928533), som har:
    - Listan med virtuella Azure-storlekar som stöds för distribution av SAP-program.
    - Viktig kapacitets information för storlekar på virtuella Azure-datorer.
    - De SAP-program och-operativ system och databas kombinationer som stöds.
    - Den nödvändiga SAP kernel-versionen för Windows och Linux på Microsoft Azure.
- SAP NOTE [2015553](https://launchpad.support.sap.com/#/notes/2015553) visar krav för SAP-program distributioner som stöds i Azure.
- SAP NOTE [405827](https://launchpad.support.sap.com/#/notes/405827) visar ett rekommenderat fil system för Hana-miljö.
- SAP NOTE [2002167](https://launchpad.support.sap.com/#/notes/2002167) har rekommenderade OS-inställningar för Red Hat Enterprise Linux.
- SAP NOTE [2009879](https://launchpad.support.sap.com/#/notes/2009879) har SAP HANA rikt linjer för Red Hat Enterprise Linux.
- SAP NOTE [2178632](https://launchpad.support.sap.com/#/notes/2178632) innehåller detaljerad information om alla övervaknings mått som rapporter ATS för SAP i Azure.
- SAP NOTE [2191498](https://launchpad.support.sap.com/#/notes/2191498) har den version av SAP host agent som krävs för Linux i Azure.
- SAP NOTE [2243692](https://launchpad.support.sap.com/#/notes/2243692) innehåller information om SAP-licensiering på Linux i Azure.
- SAP anmärkning [1999351](https://launchpad.support.sap.com/#/notes/1999351) innehåller ytterligare felsöknings information för Azure Enhanced Monitoring-tillägget för SAP.
- [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP-anteckningar för Linux.
- [Azure Virtual Machines planera och implementera SAP på Linux][planning-guide]
- [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
- [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
- [SAP HANA system replikering i pacemaker-kluster.](https://access.redhat.com/articles/3004101)
- Allmän dokumentation om RHEL
    - [Översikt över hög tillgänglighets tillägg](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Administrations tillägg med hög tillgänglighet.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Referens för hög tillgänglighets tillägg.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [Konfigurera SAP HANA systemreplikering i skala upp i ett pacemaker-kluster när HANA-filsystem är på NFS-resurser](https://access.redhat.com/solutions/5156571)
- Azure-speciell RHEL-dokumentation:
    - [Support principer för RHEL-kluster med hög tillgänglighet – Microsoft Azure Virtual Machines som kluster medlemmar.](https://access.redhat.com/articles/3131341)
    - [Installera och konfigurera ett kluster med hög tillgänglighet för Red Hat Enterprise Linux 7,4 (och senare) på Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Installera SAP HANA på Red Hat Enterprise Linux för användning i Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [Konfigurera SAP HANA pacemaker kluster när HANA-filsystemet finns på NFS-resurser](https://access.redhat.com/solutions/5156571)
- [NetApp SAP-program på Microsoft Azure med Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)

## <a name="overview"></a>Översikt

Traditionellt i skalbar miljö alla fil system för SAP HANA monteras från lokal lagring. Att konfigurera hög tillgänglighet för SAP HANA systemreplikering på Red Hat Enterprise Linux publiceras i guiden [konfigurera SAP HANA systemreplikering på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)

För att uppnå SAP HANA hög tillgänglighet för att skala upp systemet på [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) NFS-resurser behöver vi en del ytterligare resurs konfiguration i klustret, så att Hana-resurser kan återställas när en nod förlorar åtkomst till NFS-resurserna på ANF.  Klustret hanterar NFS-monteringar som gör det möjligt för IT att övervaka hälsan för resurserna. Beroenden mellan fil systemet monteras och de SAP HANA resurserna tillämpas.  

![SAP HANA HA skalning på ANF](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA fil system monteras på NFS-resurser med Azure NetApp Files på varje nod. Fil systemen/Hana/data,/Hana/log och/Hana/Shared är unika för varje nod. 

Monterad på Nod1 (**hanadb1**)

- 10.32.2.4:/**hanadb1**-data-mnt00001 på/Hana/data
- 10.32.2.4:/**hanadb1**-log-mnt00001 på/Hana/log
- 10.32.2.4:/**hanadb1**-Shared-mnt00001 på/Hana/Shared

Monterad på NOD2 (**hanadb2**)

- 10.32.2.4:/**hanadb2**-data-mnt00001 på/Hana/data
- 10.32.2.4:/**hanadb2**-log-mnt00001 på/Hana/log
- 10.32.2.4:/**hanadb2**-Shared-mnt00001 på/Hana/Shared

> [!NOTE]
> Fil systemen/Hana/Shared,/Hana/data och/Hana/log delas inte mellan de två noderna. Varje klusternod har egna, separata fil system.   

Konfigurationen av SAP HANA System Replication använder ett dedikerat virtuellt värdnamn och virtuella IP-adresser. I Azure krävs en belastningsutjämnare för att använda en virtuell IP-adress. I följande lista visas belastnings Utjämnings konfigurationen:

- Konfiguration av klient sidan: IP-10.32.0.10 för HN1-DB
- Konfiguration på Server sidan: anslutna till primära nätverks gränssnitt för alla virtuella datorer som ska ingå i HANA-systemreplikering
- Avsöknings port: port 62503
- Belastnings Utjämnings regler: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (om du använder Basic Azure Load Balancer)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Konfigurera fil infrastrukturen i Azure NetApp

Innan du fortsätter med inställningarna för Azure NetApp Files-infrastrukturen kan du bekanta dig med dokumentationen om Azure [NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/).

Azure NetApp Files finns i flera [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Kontrol lera om din valda Azure-region erbjuder Azure NetApp Files.

För information om tillgängligheten för Azure NetApp Files av Azure-regionen, se [Azure NetApp Files tillgänglighet per Azure-region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all).

Innan du distribuerar Azure NetApp Files kan du begära onboarding till Azure NetApp Files genom att gå till [Registrera dig för Azure NetApp Files instruktioner](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).

### <a name="deploy-azure-netapp-files-resources"></a>Distribuera Azure NetApp Files-resurser

Följande instruktioner förutsätter att du redan har distribuerat ditt [virtuella Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Azure NetApp Files resurser och virtuella datorer, där Azure NetApp Files resurserna ska monteras, måste distribueras i samma virtuella Azure-nätverk eller i peer-anslutna virtuella Azure-nätverk.

1. Om du inte redan har distribuerat resurserna begär du [att registrera dig för att Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).

2. Skapa ett NetApp-konto i din valda Azure-region genom att följa instruktionerna i [skapa ett NetApp-konto](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).

3.  Konfigurera en pool för Azure NetApp Files kapacitet genom att följa anvisningarna i [Konfigurera en Azure NetApp Files kapacitets pool](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).

    Den HANA-arkitektur som presenteras i den här artikeln använder en pool med enskild Azure NetApp Files kapacitet på *Ultra* service-nivå. För HANA-arbetsbelastningar i Azure rekommenderar vi att du använder en Azure NetApp Files *Ultra* eller *Premium* [service nivå](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels).

4.  Delegera ett undernät till Azure NetApp Files, enligt beskrivningen i instruktionerna i [delegera ett undernät till Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).

5.  Distribuera Azure NetApp Files-volymer genom att följa anvisningarna i [skapa en NFS-volym för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).

    När du distribuerar volymerna, måste du välja NFSv 4.1-versionen. Distribuera volymerna i det angivna Azure NetApp Files-undernätet. IP-adresserna för Azure NetApp-volymerna tilldelas automatiskt.

    Tänk på att Azure NetApp Files-resurser och de virtuella Azure-datorerna måste finnas i samma virtuella Azure-nätverk eller i peer-nätverk med peer-nätverk. Till exempel är hanadb1-data-mnt00001, hanadb1-log-mnt00001 och så vidare, volym namnen och nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 och så vidare, fil Sök vägarna för Azure NetApp Files volymerna.
    
    På **hanadb1**

    - Volym hanadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volym hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volym hanadb1 – delad – mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    På **hanadb2**

    - Volym hanadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volym hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volym hanadb2 – delad – mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Att tänka på

Tänk på följande när du skapar din Azure NetApp Files för SAP HANA skala upp system:

- Den minsta kapacitets poolen är 4 tebibyte (TiB).
- Den minsta volym storleken är 100 gibibyte (GiB).
- Azure NetApp Files och alla virtuella datorer där Azure NetApp Files volymer ska monteras måste finnas i samma virtuella Azure-nätverk eller i peer-anslutna [virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) i samma region.
- Det valda virtuella nätverket måste ha ett undernät som är delegerat till Azure NetApp Files.
- Data flödet för en Azure NetApp Files volym är en funktion av volym kvoten och tjänst nivån, enligt beskrivningen i [service nivå för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). När du ändrar storlek på HANA Azure NetApp-volymer ser du till att det resulterande data flödet uppfyller HANA-system kraven.
- Med Azure NetApp Files [export policy](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)kan du kontrol lera tillåtna klienter, åtkomst typ (Läs-och Skriv behörighet, skrivskyddad och så vidare).
- Den Azure NetApp Files funktionen är inte Zone-medveten än. Funktionen distribueras för närvarande inte i alla tillgänglighets zoner i en Azure-region. Var medveten om potentiella fördröjnings konsekvenser i vissa Azure-regioner.

> [!IMPORTANT]
> För SAP HANA arbets belastningar är låg latens kritiskt. Arbeta med din Microsoft-representant för att säkerställa att de virtuella datorerna och Azure NetApp Files volymerna distribueras i närheten.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Storlek på HANA-databas på Azure NetApp Files

Data flödet för en Azure NetApp Files volym är en funktion av volymens storlek och tjänst nivå, enligt beskrivningen i [service nivå för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels).

När du utformar infrastrukturen för SAP i Azure bör du vara medveten om några lägsta lagrings krav för SAP, som översätter minsta data flödes egenskaper:

- Läs-och skriv åtgärder på/Hana/log med 250 megabyte per sekund (MB/s) med 1 MB I/O-storlekar.
- Läs aktivitet minst 400 MB/s för/Hana/data för I/O-storlekarna på 16 MB och 64 MB.
- Skriv aktivitet minst 250 MB/s för/Hana/data med I/O-storlekar på 16 MB och 64 MB.

[Azure NetApp Files data flödes gränser](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TIB volym kvot:

- Premium Storage nivå – 64 MiB/s.
- Ultra Storage-nivå – 128 MiB/s.

För att uppfylla de lägsta data flödes kraven för SAP för/Hana/data och/Hana/log, och rikt linjerna för/Hana/Shared, skulle de rekommenderade storlekarna vara:

|    Volym    | Storlek på Premium Storage nivå | Storlek på Ultra Storage-nivå | NFS-protokoll som stöds |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v 4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v 4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 eller v 4.1    |


> [!NOTE]
> De Azure NetApp Files storleks rekommendationer som anges här är avsedda att uppfylla de minimi krav som SAP rekommenderar för sina infrastruktur leverantörer. I verkliga kund distributioner och arbets belastnings scenarier är det inte säkert att dessa storlekar är tillräckliga. Använd de här rekommendationerna som en start punkt och anpassa baserat på kraven för din särskilda arbets belastning.

> [!TIP]
> Du kan ändra storlek på Azure NetApp Files volymer dynamiskt, utan att behöva *demontera* volymerna, stoppa de virtuella datorerna eller stoppa SAP HANA. Den här metoden gör det möjligt att uppfylla både förväntade och oförutsedda data flödes krav för ditt program.

> [!NOTE]
> Alla kommandon för att montera/Hana/Shared i den här artikeln visas för NFSv 4.1/Hana/Shared-volymer.
> Om du har distribuerat/Hana/Shared-volymerna som NFSv3-volymer ska du inte glömma att justera monterings kommandona för/Hana/Shared för NFSv3.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Distribuera en virtuell Linux-dator via Azure Portal 

Först måste du skapa Azure NetApp Files volymerna. Utför sedan följande steg:

1.  Skapa en resursgrupp.
2.  Skapa ett virtuellt nätverk.
3.  Skapa en tillgänglighets uppsättning. Ange Max uppdaterings domän.
4.  Skapa en belastningsutjämnare (intern). Vi rekommenderar standard Load Balancer.
    Välj det virtuella nätverk som skapades i steg 2.
5.  Skapa virtuell dator 1 (**hanadb1**). 
6.  Skapa virtuell dator 2 (**hanadb2**).  
7.  När du skapar en virtuell dator kommer vi inte att lägga till någon disk eftersom alla våra monterings punkter kommer att finnas på NFS-resurser från Azure NetApp Files. 
8.  Följ dessa konfigurations steg om du använder standard Load Balancer:
    1.  Börja med att skapa en IP-pool på klient sidan:
        1.  Öppna belastningsutjämnaren, Välj **klient delens IP-pool**och välj **Lägg till**.
        1.  Ange namnet på den nya frontend-IP-poolen (till exempel **Hana-frontend**).
        1.  Ange **tilldelningen** till **statisk** och ange IP-adressen (till exempel **10.32.0.10**).
        1.  Välj **OK**.
        1.  När den nya frontend-IP-poolen har skapats noterar du poolens IP-adress.
    1.  Skapa sedan en backend-pool:
        1.  Öppna belastningsutjämnaren, Välj **backend-pooler**och välj **Lägg till**.
        1.  Ange namnet på den nya backend-poolen (till exempel **Hana-backend**).
        1.  Välj **Lägg till en virtuell dator**.
        1.  Välj * * virtuell dator * *.
        1.  Välj de virtuella datorerna i SAP HANA klustret och deras IP-adresser.
        1.  Välj **Lägg till**.
    1.  Skapa sedan en hälso avsökning:
        1.  Öppna belastningsutjämnaren, Välj **hälso avsökningar**och välj **Lägg till**.
        1.  Ange namnet på den nya hälso avsökningen (till exempel **Hana-HP**).
        1.  Välj TCP som protokoll och port 625**03**. Behåll värdet för **Interval** inställt på 5 och **tröskelvärdet för tröskelvärdet** har värdet 2.
        1.  Välj **OK**.
    1.  Skapa sedan reglerna för belastnings utjämning:
        1.  Öppna belastningsutjämnaren, Välj **belastnings Utjämnings regler**och välj **Lägg till**.
        1.  Ange namnet på den nya belastnings Utjämnings regeln (till exempel **Hana-lb**).
        1.  Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **Hana-frontend**, **Hana-backend** och **Hana-HP**).
        1.  Välj **ha-portar**.
        1.  Öka **tids gränsen för inaktivitet** till 30 minuter.
        1.  Se till att **Aktivera flytande IP**.
        1.  Välj **OK**.

> [!NOTE] 
> När virtuella datorer utan offentliga IP-adresser placeras i backend-poolen för intern (ingen offentlig IP-adress) standard Azure-belastningsutjämnare, kommer det inte att finnas någon utgående Internet anslutning, om inte ytterligare konfiguration utförs för att tillåta routning till offentliga slut punkter. Mer information om hur du uppnår utgående anslutningar finns i Översikt över [offentliga slut punkter för Virtual Machines med Azure standard Load Balancer i SAP-scenarier med hög tillgänglighet](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).

9. Om ditt scenario till exempel använder Basic Load Balancer, följer du dessa konfigurations steg:
    1.  Konfigurera belastningsutjämnaren. Börja med att skapa en IP-pool på klient sidan:
        1.  Öppna belastningsutjämnaren, Välj **klient delens IP-pool**och välj **Lägg till**.
        1.  Ange namnet på den nya frontend-IP-poolen (till exempel **Hana-frontend**).
        1.  Ange **tilldelningen** till **statisk** och ange IP-adressen (till exempel **10.32.0.10**).
        1.  Välj **OK**.
        1.  När den nya frontend-IP-poolen har skapats noterar du poolens IP-adress.
    1.  Skapa sedan en backend-pool:
        1.  Öppna belastningsutjämnaren, Välj **backend-pooler**och välj **Lägg till**.
        1.  Ange namnet på den nya backend-poolen (till exempel **Hana-backend**).
        1.  Välj **Lägg till en virtuell dator**.
        1.  Välj den tillgänglighets uppsättning som skapades i steg 3.
        1.  Välj de virtuella datorerna i SAP HANA klustret.
        1.  Välj **OK**.
    1.  Skapa sedan en hälso avsökning:
        1.  Öppna belastningsutjämnaren, Välj **hälso avsökningar**och välj **Lägg till**.
        1.  Ange namnet på den nya hälso avsökningen (till exempel **Hana-HP**).
        1.  Välj **TCP** som protokoll och port 625**03**. Behåll värdet för **Interval** inställt på 5 och **tröskelvärdet för tröskelvärdet** har värdet 2.
        1.  Välj **OK**.
    1.  Skapa regler för belastnings utjämning för SAP HANA 1,0:
        1.  Öppna belastningsutjämnaren, Välj **belastnings Utjämnings regler**och välj **Lägg till**.
        1.  Ange namnet på den nya belastnings Utjämnings regeln (till exempel Hana – lb-3**03**).
        1.  Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **Hana-frontend**).
        1.  Behåll **protokollet** inställt på **TCP**och ange Port 3**03**15.
        1.  Öka **tids gränsen för inaktivitet** till 30 minuter.
        1.  Se till att **Aktivera flytande IP**.
        1.  Välj **OK**.
        1.  Upprepa de här stegen för Port 3**03**17.
    1.  Skapa regler för belastnings utjämning för system databasen för SAP HANA 2,0:
        1.  Öppna belastningsutjämnaren, Välj **belastnings Utjämnings regler**och välj **Lägg till**.
        1.  Ange namnet på den nya belastnings Utjämnings regeln (till exempel Hana-lb-3**03**13).
        1.  Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **Hana-frontend**).
        1.  Behåll **protokollet** inställt på **TCP**och ange Port 3**03**13.
        1.  Öka **tids gränsen för inaktivitet** till 30 minuter.
        1.  Se till att **Aktivera flytande IP**.
        1.  Välj **OK**.
        1.  Upprepa dessa steg för Port 3**03**.
    1.  För SAP HANA 2,0 skapar du först reglerna för belastnings utjämning för klient databasen:
        1.  Öppna belastningsutjämnaren, Välj **belastnings Utjämnings regler**och välj **Lägg till**.
        1.  Ange namnet på den nya belastnings Utjämnings regeln (till exempel Hana – lb-3**03**40).
        1.  Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **Hana-frontend**).
        1.  Behåll **protokollet** inställt på **TCP**och ange Port 3**03**40.
        1.  Öka **tids gränsen för inaktivitet** till 30 minuter.
        1.  Se till att **Aktivera flytande IP**.
        1.  Välj **OK**.
        1.  Upprepa de här stegen för portarna 3**03**41 och 3**03**42.

Om du vill ha mer information om de portar som krävs för SAP HANA kan du läsa kapitel [anslutningarna till klient databaserna](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) i guiden för [SAP HANA klient databaser](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) eller SAP NOTE [2388694](https://launchpad.support.sap.com/#/notes/2388694).

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placerats bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar kommer hälso avsökningarna att Miss skadas. Ange parametern **net. IPv4. tcp_timestamps** till **0**. Mer information finns i [Load Balancer hälso avsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview). Se även SAP anmärkning [2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Montera Azure NetApp Files volym

1. **[A]** skapa monterings punkter för Hana-databasens volymer. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** verifiera inställningen för NFS-domänen. Kontrol lera att domänen har kon figurer ATS som standard Azure NetApp Files domän, t. ex. **defaultv4iddomain.com** och mappningen är inställd på **ingen**.

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Se till att ange NFS-domänen i/etc/idmapd.conf på den virtuella datorn så att den matchar standard domän konfigurationen på Azure NetApp Files: **defaultv4iddomain.com**. Om det finns ett matchnings fel mellan domän konfigurationen på NFS-klienten (dvs. den virtuella datorn) och NFS-servern, d.v.s. Azure NetApp-konfigurationen, så visas behörigheterna för filer på Azure NetApp-volymer som är monterade på de virtuella datorerna som ingen.
    

3. **[1]** montera de Node-/regionsspecifika volymerna på Nod1 (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** montera de Node-/regionsspecifika volymerna på NOD2 (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** kontrol lera att alla Hana-volymer monteras med NFS-NFSv4.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** verifiera **nfs4_disable_idmapping**. Den måste anges till **Y**. Kör monterings kommandot för att skapa katalog strukturen där **nfs4_disable_idmapping** finns. Du kan inte skapa katalogen manuellt under/sys/modules eftersom åtkomst är reserverad för kernel/driv rutiner.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Mer information om hur du ändrar **nfs_disable_idmapping** parameter finns i [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>SAP HANA installation

1. **[A]** konfigurera värd namns matchning för alla värdar.

   Du kan antingen använda en DNS-server eller ändra/etc/hosts-filen på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen. Ersätt IP-adressen och värdnamnet i följande kommandon:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL för Hana-konfiguration

   Konfigurera RHEL enligt beskrivningen i nedanstående SAP-anteckning baserat på din RHEL-version

   - [2292690-SAP HANA DB: rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: rekommenderade OS-inställningar för RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: köra SAP-program som kompileras med GCC 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 – Linux: köra SAP-program som kompileras med GCC 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: köra SAP-program som kompileras med GCC 9. x](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** installera SAP HANA

   Startade med HANA 2,0 SPS 01, MDC är standard alternativet. När du installerar HANA-system skapas SYSTEMDB och en klient med samma SID tillsammans. I vissa fall vill du inte använda standard klient organisationen. Om du inte vill skapa en första klient tillsammans med installationen kan du följa SAP NOTE [2629711](https://launchpad.support.sap.com/#/notes/2629711)

    Kör **hdblcm** -programmet från Hana-DVD: n. Ange följande värden i prompten:  
    Välj installation: ange **1** (för installation)  
    Välj ytterligare komponenter för installation: ange **1**.  
    Ange installations Sök väg [/Hana/Shared]: Tryck på RETUR för att acceptera standardvärdet  
    Ange det lokala värd namnet [..]: Tryck på RETUR för att acceptera standardvärdet  
    Vill du lägga till fler värdar i systemet? (j/n) [n]: **n**  
    Ange SAP HANA system-ID: ange **HN1**.  
    Ange instans nummer [00]: ange **03**  
    Välj databas läge/ange index [1]: Tryck på RETUR för att acceptera standardvärdet  
    Välj system användning/ange index [4]: ange **4** (för anpassad)  
    Ange plats för data volymer [/Hana/data]: Tryck på RETUR för att acceptera standardvärdet  
    Ange plats för logg volymer [/Hana/log]: Tryck på RETUR för att acceptera standard  
    Begränsa maximal minnesallokering? [n]: Tryck på RETUR om du vill acceptera standardvärdet  
    Ange ett certifikat värd namn för värden '... ' [...]: Tryck på RETUR för att acceptera standardvärdet  
    Ange lösen ord för SAP host agent-användare (sapadm): Ange värd agentens användar lösen ord  
    Bekräfta lösen ordet för SAP host agent-användare (sapadm): Ange värd agentens användar lösen ord igen för att bekräfta  
    Ange system administratörs lösen ord (hn1adm): Ange system administratörens lösen ord  
    Bekräfta lösen ord för system administratör (hn1adm): Ange system administratörens lösen ord igen för att bekräfta  
    Ange system administratörens Hem Katalog [/usr/sap/HN1/home]: Tryck på RETUR för att acceptera standardvärdet  
    Ange system administratör inloggnings gränssnitt [/bin/sh]: Tryck på RETUR för att acceptera standardvärdet  
    Ange användar-ID för system administratör [1001]: Tryck på RETUR för att acceptera standardvärdet  
    Ange ID för användar gruppen (sapsys) [79]: Tryck på RETUR för att acceptera standardvärdet  
    Ange lösen ord för databas användare (SYSTEM): Ange användar lösen ordet för databasen  
    Bekräfta lösen ord för databas användare (SYSTEM): Ange användar lösen ordet för databasen igen för att bekräfta  
    Vill du starta om systemet efter omstart av datorn? [n]: Tryck på RETUR om du vill acceptera standardvärdet  
    Vill du fortsätta? (j/n): verifiera sammanfattningen. Fortsätt genom att ange **y**  

4. **[A]** uppgradera SAP-värda Gent

   Hämta det senaste SAP-värd agent arkivet från [SAP Software Center](https://launchpad.support.sap.com/#/softwarecenter) och kör följande kommando för att uppgradera agenten. Ersätt sökvägen till arkivet så att den pekar på den fil som du laddade ned:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** konfigurera brand vägg

   Skapa brand Väggs regeln för avsöknings porten för Azure Load Balancer.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>Konfigurera SAP HANA system replikering

Följ stegen i Konfigurera [SAP HANA systemreplikering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-sap-hana-20-system-replication) för att konfigurera SAP HANA system replikering. 

## <a name="cluster-configuration"></a>Klusterkonfiguration

I det här avsnittet beskrivs de steg som krävs för att klustret ska kunna köras smidigt när SAP HANA installeras på NFS-resurser med hjälp av Azure NetApp Files. 

### <a name="create-a-pacemaker-cluster"></a>Skapa ett pacemaker-kluster

Följ stegen i [Konfigurera pacemaker på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) i Azure för att skapa ett grundläggande pacemaker-kluster för den här Hana-servern.

### <a name="configure-filesystem-resources"></a>Konfigurera fil Systems resurser

I det här exemplet har varje klusternod sina egna HANA NFS-filsystem/Hana/Shared,/Hana/data och/Hana/log.   

1. **[1]** sätt klustret i underhålls läge.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** skapa fil Systems resurserna för **hanadb1** -monteringar.

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** skapa fil Systems resurserna för **hanadb2** -monteringar.

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` attributet läggs till i övervaknings åtgärden så att varje övervakare utför ett Läs-och skriv test i fil systemet. Utan det här attributet verifierar övervaknings åtgärden bara att fil systemet är monterat. Detta kan vara ett problem eftersom när anslutningen bryts, kan fil systemet förbli monterat trots att det inte går att komma åt dem.

    `on-fail=fence` attributet läggs också till i övervaknings åtgärden. Med det här alternativet, om övervaknings åtgärden Miss lyckas på en nod, stängs den noden omedelbart. Utan det här alternativet är standard beteendet att stoppa alla resurser som är beroende av den felande resursen, sedan starta om den misslyckade resursen och sedan starta alla resurser som är beroende av den felande resursen. Det är inte bara det här beteendet som kan ta lång tid när en SAPHana-resurs är beroende av den felande resursen, men den kan också Miss lyckas helt. SAPHana-resursen kan inte stoppas om den NFS-server som innehåller HANA-körbara filer inte är tillgänglig.

4. **[1]** konfigurera plats begränsningar

   Konfigurera plats begränsningar för att säkerställa att de resurser som hanterar hanadb1 unika monteringar aldrig kan köras på hanadb2 och vice versa.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    `resource-discovery=never`Alternativet är inställt eftersom de unika monteringarna för varje nod delar samma monterings punkt. Exempel: `hana_data1` använder monterings punkt `/hana/data` och `hana_data2` använder också monterings punkt `/hana/data` . Detta kan orsaka ett falskt positivt värde för en avsöknings åtgärd när resurs status kontrol leras vid kluster Start, och detta kan i sin tur orsaka onödig återställnings beteende. Detta kan undvikas genom att ställa in `resource-discovery=never`

5. **[1]** konfigurera attribut resurser

   Konfigurera attributets resurser. Attributen ställs in på sant om alla noders NFS-monteringar (/Hana/data,/Hana/log och/Hana/data) är monterade och anges till false i annat fall.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** konfigurera plats begränsningar

   Konfigurera plats begränsningar för att säkerställa att hanadb1's inte körs på hanadb2 och vice versa.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** skapar sorterings begränsningar

   Konfigurera sorterings begränsningar så att nodens attribut resurser bara startar efter att alla noders NFS-monteringar är monterade.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Om konfigurationen omfattar fil system, utanför gruppen `hanadb1_nfs` eller `hanadb2_nfs` , inkludera `sequential=false` alternativet, så att det inte finns några ordnings beroenden mellan fil systemen. Alla fil system måste starta innan `hana_nfs1_active` , men de behöver inte börja i någon ordning i förhållande till varandra. Mer information finns i [Hur gör jag för att konfigurera SAP HANA systemreplikering i skala upp i ett pacemaker-kluster när Hana-fil system finns på NFS-resurser](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>Konfigurera SAP HANA kluster resurser

1. Följ stegen i [skapa SAP HANA kluster resurser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#create-sap-hana-cluster-resources) för att skapa SAP HANA-resurserna i klustret. När SAP HANA-resurser har skapats måste vi skapa en plats regel begränsning mellan SAP HANA resurser och fil system (NFS-monteringar)

2. **[1]** konfigurera begränsningar mellan SAP HANA resurser och NFS-monteringar

   Plats regel begränsningar ställs in så att SAP HANA resurser bara kan köras på en nod om alla nodens NFS-monteringar är monterade.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Kontrol lera status för klustret och alla resurser

    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="test-the-cluster-setup"></a>Testa kluster konfigurationen

I det här avsnittet beskrivs hur du kan testa installationen. 

1. Innan du startar ett test kontrollerar du att pacemaker inte har någon misslyckad åtgärd (via dator status). det finns inga oväntade plats begränsningar (till exempel rester av ett migreringsjobb) och att HANA-systemreplikering är Sync-tillstånd, till exempel med systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Verifiera kluster konfigurationen för ett fel scenario när en nod förlorar åtkomst till NFS-resursen (/Hana/Shared)

   SAP HANA resurs agenter är beroende av binärfiler, lagrade på `/hana/shared` för att utföra åtgärder under redundansväxlingen. Fil systemet  `/hana/shared` är monterat över NFS i det presenterade scenariot.  
   Det är svårt att simulera ett haveri där en av servrarna förlorar åtkomst till NFS-resursen. Ett test som kan utföras är att montera fil systemet på nytt som skrivskyddat.
   Den här metoden kontrollerar att klustret kan redundansväxla, om åtkomst till `/hana/shared` går förlorat på den aktiva noden.     


   **Förväntat resultat:** Om du skapar `/hana/shared` ett skrivskyddat fil system `OCF_CHECK_LEVEL` Miss lyckas attributet för den resurs `hana_shared1` som utför läsning/skrivning på fil systemet eftersom det inte går att skriva något i fil systemet och kommer att utföra Hana-resurs växling vid fel.  Samma resultat förväntas när HANA-noden förlorar åtkomst till NFS-resurserna. 

   Resurs tillstånd innan du startar testet:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   Du kan placera/Hana/Shared i skrivskyddat läge på den aktiva klusternoden med hjälp av följande kommando:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 startas antingen om eller avstängnings läge baserat på åtgärds uppsättningen för stonith ( `pcs property show stonith-action` ).  När servern (hanadb1) är nere flyttas HANA-resursen till hanadb2. Du kan kontrol lera status för klustret från hanadb2.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   Vi rekommenderar att du testar SAP HANA kluster konfigurationen noga, genom att även utföra de tester som beskrivs i [installations SAP HANA systemreplikering på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#test-the-cluster-setup).   