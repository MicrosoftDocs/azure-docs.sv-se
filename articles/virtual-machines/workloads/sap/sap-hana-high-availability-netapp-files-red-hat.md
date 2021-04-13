---
title: Hög tillgänglighet för SAP HANA med ANF på RHEL-| Microsoft Docs
description: Upprätta hög tillgänglighet för SAP HANA med ANF på virtuella Azure-datorer (VM).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 774344c4215088482b110de91f8951bae4a41d25
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365832"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Hög tillgänglighet för SAP HANA uppskalning med Azure NetApp Files på Red Hat Enterprise Linux

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

I den här artikeln beskrivs hur du konfigurerar SAP HANA systemreplikering i uppskalningsdistribution när HANA-filsystemen monteras via NFS med hjälp av Azure NetApp Files (ANF). I exempelkonfigurationerna och installationskommandona används **instansnummer 03** och HANA System ID **HN1.** SAP HANA Replikering består av en primär nod och minst en sekundär nod.

När stegen i det här dokumentet är markerade med följande prefix är innebörden följande:

- **[A]**: Steget gäller för alla noder
- **[1]**: Steget gäller endast node1
- **[2]**: Steget gäller endast node2
 
Läs följande SAP-anteckningar och -artiklar först:

- [SAP-anteckning 1928533](https://launchpad.support.sap.com/#/notes/1928533), som har:
    - Listan över storlekar på virtuella Azure-datorer som stöds för distribution av SAP-programvara.
    - Viktig kapacitetsinformation för storlekar på virtuella Azure-datorer.
    - SAP-programvara som stöds, operativsystem (OS) och databaskombinationer.
    - Den sap kernel-version som krävs för Windows och Linux på Microsoft Azure.
- SAP Note [2015553 innehåller](https://launchpad.support.sap.com/#/notes/2015553) en lista över förhandskrav för SAP-programvarudistributioner som stöds i Azure.
- [SAP-anteckning 405827](https://launchpad.support.sap.com/#/notes/405827) visar rekommenderade filsystem för HANA-miljö.
- SAP Note [2002167](https://launchpad.support.sap.com/#/notes/2002167) har rekommenderade os-inställningar för Red Hat Enterprise Linux.
- SAP Note [2009879](https://launchpad.support.sap.com/#/notes/2009879) har SAP HANA Guidelines for Red Hat Enterprise Linux.
- [SAP-anteckningen 2178632](https://launchpad.support.sap.com/#/notes/2178632) innehåller detaljerad information om alla övervakningsmått som rapporteras för SAP i Azure.
- SAP Note [2191498](https://launchpad.support.sap.com/#/notes/2191498) har den version av SAP-värdagenten som krävs för Linux i Azure.
- SAP Note [2243692 innehåller](https://launchpad.support.sap.com/#/notes/2243692) information om SAP-licensiering på Linux i Azure.
- [SAP-anteckningen 1999351](https://launchpad.support.sap.com/#/notes/1999351) innehåller ytterligare felsökningsinformation för Azure Enhanced Monitoring Extension för SAP.
- [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP-anteckningar för Linux.
- [Azure Virtual Machines planering och implementering för SAP på Linux][planning-guide]
- [Azure Virtual Machines distribution för SAP på Linux][deployment-guide]
- [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
- [SAP HANA systemreplikering i pacemakerkluster.](https://access.redhat.com/articles/3004101)
- Allmän RHEL-dokumentation
    - [Översikt över Add-On tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Administration med Add-On tillgänglighet.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Referens för Add-On tillgänglighet.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [Konfigurera SAP HANA systemreplikering i Scale-Up i ett Pacemaker-kluster när HANA-filsystemen finns på NFS-resurser](https://access.redhat.com/solutions/5156571)
- Azure-specifik RHEL-dokumentation:
    - [Stödprinciper för RHEL-kluster med hög tillgänglighet – Microsoft Azure Virtual Machines som klustermedlemmar.](https://access.redhat.com/articles/3131341)
    - [Installera och konfigurera ett Red Hat Enterprise Linux 7.4 (och senare) High-Availability kluster på Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Installera SAP HANA på Red Hat Enterprise Linux för Använd i Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [Konfigurera SAP HANA skala upp systemreplikering av pacemakerkluster när HANA-filsystemen finns på NFS-resurser](https://access.redhat.com/solutions/5156571)
- [NetApp SAP-program på Microsoft Azure med Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
- [NFS v4.1-volymer på Azure NetApp Files för SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Översikt

Traditionellt i uppskalningsmiljön monteras alla filsystem för SAP HANA från lokal lagring. Konfigurera hög tillgänglighet för SAP HANA systemreplikering på Red Hat Enterprise Linux publiceras i guiden [Konfigurera SAP HANA systemreplikering på RHEL](./sap-hana-high-availability-rhel.md)

För att uppnå SAP HANA hög tillgänglighet för uppskalningssystem på [Azure NetApp Files](../../../azure-netapp-files/index.yml) NFS-resurser behöver vi viss ytterligare resurskonfiguration i klustret för att HANA-resurser ska kunna återställas när en nod förlorar åtkomst till NFS-resurserna på ANF.  Klustret hanterar NFS-monteringarna så att det kan övervaka resursernas hälsotillstånd. Beroendena mellan filsystemets monteringar och de SAP HANA-resurserna tillämpas.  

![SAP HANA HA-uppskalning på ANF](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA på NFS-resurser med hjälp av Azure NetApp Files på varje nod. Filsystemen /hana/data, /hana/log och /hana/shared är unika för varje nod. 

Monterad på node1 (**hanadb1**)

- 10.32.2.4:/**hanadb1**-data-mnt00001 på /hana/data
- 10.32.2.4:/**hanadb1**-log-mnt00001 på /hana/log
- 10.32.2.4:/**hanadb1**-shared-mnt00001 på /hana/shared

Monterad på node2 (**hanadb2**)

- 10.32.2.4:/**hanadb2**-data-mnt00001 på /hana/data
- 10.32.2.4:/**hanadb2**-log-mnt00001 på /hana/log
- 10.32.2.4:/**hanadb2**-shared-mnt00001 på /hana/shared

> [!NOTE]
> Filsystem /hana/shared, /hana/data och /hana/log delas inte mellan de två noderna. Varje klusternod har sina egna, separata filsystem.   

Konfigurationen SAP HANA systemreplikering använder ett dedikerat virtuellt värdnamn och virtuella IP-adresser. I Azure krävs en lastbalanserare för att använda en virtuell IP-adress. I följande lista visas konfigurationen av lastbalanseraren:

- Frontend-konfiguration: IP-adress 10.32.0.10 för hn1-db
- Serverdelskonfiguration: Ansluten till primära nätverksgränssnitt för alla virtuella datorer som ska ingå i HANA-systemreplikering
- Avsökningsport: Port 62503
- Lastbalanseringsregler: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (om du använder en grundläggande Azure Load Balancer)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Konfigurera Azure NetApp-filinfrastrukturen

Innan du fortsätter med att konfigurera för Azure NetApp Files infrastruktur bör du bekanta dig med dokumentationen för Azure [NetApp Files.](../../../azure-netapp-files/index.yml)

Azure NetApp Files finns i flera [Azure-regioner.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Kontrollera om din valda Azure-region erbjuder Azure NetApp Files.

Information om tillgängligheten för tjänster Azure NetApp Files Azure-region finns i [Azure NetApp Files tillgänglighet per Azure-region.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)

Innan du distribuerar Azure NetApp Files begär du registrering till Azure NetApp Files genom att gå [till Registrera för Azure NetApp Files anvisningar](../../../azure-netapp-files/azure-netapp-files-register.md).

### <a name="deploy-azure-netapp-files-resources"></a>Distribuera Azure NetApp Files resurser

Följande anvisningar förutsätter att du redan har distribuerat det virtuella [Azure-nätverket](../../../virtual-network/virtual-networks-overview.md). De Azure NetApp Files och virtuella datorerna, där Azure NetApp Files-resurserna ska monteras, måste distribueras i samma virtuella Azure-nätverk eller i peer-peer-erade virtuella Azure-nätverk.

1. Om du inte redan har distribuerat resurserna begär du [registrering till Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Skapa ett NetApp-konto i din valda Azure-region genom att följa anvisningarna i [Skapa ett NetApp-konto](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

3.  Konfigurera en Azure NetApp Files kapacitetspool genom att följa anvisningarna i [Konfigurera en Azure NetApp Files-kapacitetspool](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

    HANA-arkitekturen som presenteras i den här artikeln använder en Azure NetApp Files en kapacitetspool på *Ultra* Service-nivå. För HANA-arbetsbelastningar i Azure rekommenderar vi att du använder Azure NetApp Files *Ultra-* eller [Premium-servicenivå.](../../../azure-netapp-files/azure-netapp-files-service-levels.md) 

4.  Delegera ett undernät till Azure NetApp Files enligt beskrivningen i anvisningarna i [Delegera ett undernät till Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5.  Distribuera Azure NetApp Files volymer genom att följa anvisningarna i [Skapa en NFS-volym för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

    När du distribuerar volymerna måste du välja NFSv4.1-versionen. Distribuera volymerna i det Azure NetApp Files undernätet. IP-adresserna för Azure NetApp-volymerna tilldelas automatiskt.

    Tänk på att resurserna Azure NetApp Files Azure och de virtuella Azure-datorerna måste finnas i samma virtuella Azure-nätverk eller i peer-erade virtuella Azure-nätverk. Till exempel är hanadb1-data-mnt00001, hanadb1-log-mnt00001 och så vidare volymnamnen och nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 och så vidare filsökvägarna för Azure NetApp Files-volymerna.
    
    På **hanadb1**

    - Volym hanadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volym hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volym hanadb1-shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    På **hanadb2**

    - Volym hanadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volym hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volym hanadb2-shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Att tänka på

När du skapar dina Azure NetApp Files för SAP HANA uppskalningssystem bör du tänka på följande:

- Den minsta kapacitetspoolen är 4 tebibyte (TiB).
- Den minsta volymstorleken är 100 gibibyte (GiB).
- Azure NetApp Files och alla virtuella datorer där Azure NetApp Files-volymerna ska monteras måste finnas i samma virtuella Azure-nätverk eller i [peer-ade](../../../virtual-network/virtual-network-peering-overview.md) virtuella nätverk i samma region.
- Det valda virtuella nätverket måste ha ett undernät som har delegerats till Azure NetApp Files.
- Dataflödet för en Azure NetApp Files volym är en funktion av volymkvoten och tjänstnivån, enligt vad som beskrivs i [Servicenivå för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). När du storleksändring av HANA Azure NetApp-volymerna ser du till att det resulterande dataflödet uppfyller SYSTEMkraven för HANA.
- Med den Azure NetApp Files [exportera principen](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)kan du styra de tillåtna klienterna, åtkomsttypen (skrivskyddade, skrivskyddade och så vidare).
- Funktionen Azure NetApp Files är inte zonmedveten ännu. För närvarande distribueras inte funktionen i alla tillgänglighetszoner i en Azure-region. Tänk på de potentiella fördröjningskonsekvenserna i vissa Azure-regioner.

> [!IMPORTANT]
> För SAP HANA arbetsbelastningar är låg latens kritisk. Arbeta med din Microsoft-representant för att säkerställa att de virtuella Azure NetApp Files och volymerna distribueras nära varandra.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Storlek på HANA-databas på Azure NetApp Files

Dataflödet för en Azure NetApp Files volym är en funktion av volymstorlek och servicenivå, enligt vad som beskrivs i [Servicenivå för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md).

När du utformar infrastrukturen för SAP i Azure bör du vara medveten om vissa minimikrav för lagring av SAP, som översätts till minsta dataflödesegenskaper:

- Läsning/skrivning på /hana/logg på 250 megabyte per sekund (MB/s) med 1 MB I/O-storlekar.
- Läsaktivitet på minst 400 MB/s för /hana/data för I/O-storlekar på 16 MB och 64 MB.
- Skrivaktivitet på minst 250 MB/s för /hana/data med 16 MB och 64 MB I/O-storlekar.

De [Azure NetApp Files dataflödesgränserna](../../../azure-netapp-files/azure-netapp-files-service-levels.md) per 1 TiB volymkvot är:

- Premium Storage nivå – 64 MiB/s.
- Ultra Storage-nivå – 128 MiB/s.

För att uppfylla minimikraven för SAP-dataflöde för /hana/data och /hana/log och riktlinjerna för /hana/shared är de rekommenderade storlekarna:

|    Volym    | Storlek på Premium Storage nivå | Storlek på Ultra Storage-nivå | NFS-protokoll som stöds |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 eller v4.1    |


> [!NOTE]
> Rekommendationerna Azure NetApp Files storlek som anges här är avsedda att uppfylla minimikraven som SAP rekommenderar för sina infrastrukturleverantörer. I verkliga kunddistributioner och arbetsbelastningsscenarier är dessa storlekar kanske inte tillräckliga. Använd dessa rekommendationer som utgångspunkt och anpassa dem baserat på kraven för din specifika arbetsbelastning.

> [!TIP]
> Du kan ändra Azure NetApp Files volymerna dynamiskt,  utan att behöva demontera volymerna, stoppa de virtuella datorerna eller stoppa SAP HANA. Den här metoden ger flexibilitet för att uppfylla både förväntade och oförutsedda dataflödeskrav för ditt program.

> [!NOTE]
> Alla kommandon för att montera /hana/shared i den här artikeln visas för NFSv4.1 /hana/shared volumes.
> Om du har distribuerat /hana/delade volymer som NFSv3-volymer ska du inte glömma att justera monteringskommandona för /hana/shared för NFSv3.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Distribuera en virtuell Linux-dator via Azure Portal 

Först måste du skapa Azure NetApp Files volymerna. Gör sedan följande:

1.  Skapa en resursgrupp.
2.  Skapa ett virtuellt nätverk.
3.  Skapa en tillgänglighetsuppsättning. Ange högsta uppdateringsdomän.
4.  Skapa en lastbalanserare (intern). Vi rekommenderar standardlastbalanserare.
    Välj det virtuella nätverk som skapades i steg 2.
5.  Skapa virtuell dator 1 (**hanadb1**). 
6.  Skapa virtuell dator 2 (**hanadb2**).  
7.  När vi skapar en virtuell dator lägger vi inte till någon disk eftersom alla monteringspunkter finns på NFS-resurser från Azure NetApp Files. 

> [!IMPORTANT]
> Flytande IP stöds inte på en sekundär IP-konfiguration för nätverkskort i scenarier för belastningsutjämning. Mer information finns i [Begränsningar för Azure Load Balancer.](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.    

> [!NOTE] 
> När virtuella datorer utan offentliga IP-adresser placeras i serverpoolen för intern (ingen offentlig IP-adress) Standard Azure-lastbalanserare, kommer det inte att finnas någon utgående Internetanslutning, såvida inte ytterligare konfiguration utförs för att tillåta routning till offentliga slutpunkter. Mer information om hur du uppnår utgående anslutningar finns i Offentlig slutpunktsanslutning [för Virtual Machines med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet.](./high-availability-guide-standard-load-balancer-outbound-connections.md)

8.  Om du använder en standardlastbalanserare följer du dessa konfigurationssteg:
    1.  Skapa först en IP-adresspool på frontend-sidan:
        1.  Öppna lastbalanseraren, välj **IP-pool på frontend-sidan** och välj Lägg **till**.
        1.  Ange namnet på den nya IP-adresspoolen för frontend (till **exempel hana-frontend**).
        1.  Ställ in **Tilldelning** på **Statisk** och ange IP-adressen (till exempel **10.32.0.10**).
        1.  Välj **OK**.
        1.  När den nya IP-adresspoolen på frontend-sidan har skapats noterar du poolens IP-adress.
    1.  Skapa sedan en backend-pool:
        1.  Öppna lastbalanseraren, välj **backend-pooler** och välj Lägg **till**.
        1.  Ange namnet på den nya backend-poolen (till exempel **hana-backend**).
        1.  Välj **Lägg till en virtuell dator.**
        1.  Välj ** Virtuell dator**.
        1.  Välj de virtuella datorerna i SAP HANA och deras IP-adresser.
        1.  Välj **Lägg till**.
    1.  Skapa sedan en hälsoavsökning:
        1.  Öppna lastbalanseraren, välj **hälsoavsökningar och** välj Lägg **till**.
        1.  Ange namnet på den nya hälsoavsökningen (till exempel **hana-hp**).
        1.  Välj TCP som protokoll och port 625 **03.** Behåll värdet **Intervall** inställt på 5 och **tröskelvärdet Inte feltillstånd** inställt på 2.
        1.  Välj **OK**.
    1.  Skapa sedan reglerna för belastningsutjämning:
        1.  Öppna lastbalanseraren, välj **lastbalanseringsregler och** välj Lägg **till**.
        1.  Ange namnet på den nya lastbalanseringsregeln (till exempel **hana-lb**).
        1.  Välj IP-adressen för frontend, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**, **hana-backend** **och hana-hp**).
        1.  Välj **HA-portar.**
        1.  Se till att **aktivera flytande IP.**
        1.  Välj **OK**.


9. Om ditt scenario kräver att du använder en grundläggande lastbalanserare kan du också följa dessa konfigurationssteg:
    1.  Konfigurera lastbalanseraren. Skapa först en IP-pool på frontend-sidan:
        1.  Öppna lastbalanseraren, välj **IP-pool på frontend-sidan** och välj Lägg **till**.
        1.  Ange namnet på den nya IP-adresspoolen på frontend-sidan (till **exempel hana-frontend**).
        1.  Ställ in **Tilldelning** på **Statisk** och ange IP-adressen (till exempel **10.32.0.10**).
        1.  Välj **OK**.
        1.  Observera poolens IP-adress när den nya IP-adresspoolen på frontend-sidan har skapats.
    1.  Skapa sedan en backend-pool:
        1.  Öppna lastbalanseraren, välj **backend-pooler** och välj Lägg **till**.
        1.  Ange namnet på den nya backend-poolen (till exempel **hana-backend**).
        1.  Välj **Lägg till en virtuell dator.**
        1.  Välj den tillgänglighetsuppsättning som skapades i steg 3.
        1.  Välj de virtuella datorerna i SAP HANA klustret.
        1.  Välj **OK**.
    1.  Skapa sedan en hälsoavsökning:
        1.  Öppna lastbalanseraren, välj **hälsoavsökningar och** välj Lägg **till**.
        1.  Ange namnet på den nya hälsoavsökningen (till exempel **hana-hp**).
        1.  Välj **TCP** som protokoll och port 625 **03.** Behåll värdet **Intervall** inställt på 5 och **tröskelvärdet Inte feltillstånd** inställt på 2.
        1.  Välj **OK**.
    1.  Skapa SAP HANA 1.0 för belastningsutjämning:
        1.  Öppna lastbalanseraren, välj **lastbalanseringsregler och** välj Lägg **till**.
        1.  Ange namnet på den nya lastbalanseringsregeln (till exempel hana-lb-3 **03** 15).
        1.  Välj frontend-IP-adressen, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**).
        1.  Behåll Protokollet **inställt** på **TCP** och ange port 3 **03** 15.
        1.  Öka **tidsgränsen för inaktivitet** till 30 minuter.
        1.  Se till att **aktivera flytande IP.**
        1.  Välj **OK**.
        1.  Upprepa dessa steg för port 3 **03** 17.
    1.  För SAP HANA 2.0 skapar du belastningsutjämningsregler för systemdatabasen:
        1.  Öppna lastbalanseraren, välj **lastbalanseringsregler och** välj Lägg **till**.
        1.  Ange namnet på den nya lastbalanseringsregeln (till exempel hana-lb-3 **03** 13).
        1.  Välj IP-adressen för frontend, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**).
        1.  Behåll Protokollet **inställt** på **TCP** och ange port 3 **03** 13.
        1.  Öka **tidsgränsen för inaktivitet** till 30 minuter.
        1.  Se till att **aktivera flytande IP.**
        1.  Välj **OK**.
        1.  Upprepa dessa steg för port 3 **03** 14.
    1.  För SAP HANA 2.0 skapar du först belastningsutjämningsregler för klientdatabasen:
        1.  Öppna lastbalanseraren, välj **lastbalanseringsregler och** välj Lägg **till**.
        1.  Ange namnet på den nya lastbalanseringsregeln (till exempel hana-lb-3 **03** 40).
        1.  Välj ip-adressen för frontend, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **hana-frontend**).
        1.  Behåll Protokollet **inställt** på **TCP** och ange port 3 **03** 40.
        1.  Öka **tidsgränsen för inaktivitet** till 30 minuter.
        1.  Se till att **aktivera flytande IP.**
        1.  Välj **OK**.
        1.  Upprepa dessa steg för portarna **3 03** 41 och 3 **03** 42.

Mer information om de portar som krävs för [](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) SAP HANA finns i kapitlet Anslutningar till klientdatabaser i [guiden för SAP HANA-klientdatabaser](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) eller [SAP-anteckningen 2388694.](https://launchpad.support.sap.com/#/notes/2388694)

> [!IMPORTANT]
> Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placerats bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar misslyckas hälsoavsökningarna. Ange **parametern net.ipv4.tcp_timestamps** **till 0**. Mer information finns [i Load Balancer hälsoavsökningar](../../../load-balancer/load-balancer-custom-probe-overview.md). Se även [SAP-anteckningen 2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Montera Azure NetApp Files volymen

1. **[A]** Skapa monteringspunkter för HANA-databasvolymerna. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** Kontrollera NFS-domäninställningen. Kontrollera att domänen är konfigurerad som standarddomänen Azure NetApp Files, det vill **säga defaultv4iddomain.com** och mappningen är inställd på **ingen**.

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
    > Se till att ange NFS-domänen i /etc/idmapd.conf på den virtuella datorn så att den matchar standarddomänkonfigurationen på Azure NetApp Files: **defaultv4iddomain.com**. Om det finns ett matchningsfel mellan domänkonfigurationen på NFS-klienten (dvs. den virtuella datorn) och NFS-servern, d.v.s. Azure NetApp-konfigurationen, visas behörigheterna för filer på Azure NetApp-volymer som är monterade på de virtuella datorerna som ingen.
    

3. **[1]** Montera de nodspecifika volymerna på node1 (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** Montera de nodspecifika volymerna på node2 (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** Kontrollera att alla HANA-volymer är monterade med NFS-protokollversion NFSv4.

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

6. **[A]** Verifiera **nfs4_disable_idmapping**. Det ska vara inställt på **Y**. Kör monteringskommandot **för att nfs4_disable_idmapping** katalogstrukturen där den finns. Du kommer inte att kunna skapa katalogen manuellt under /sys/modules eftersom åtkomst är reserverad för kerneln/drivrutinerna.

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

1. **[A]** Konfigurera värdnamnsmatchning för alla värdar.

   Du kan antingen använda en DNS-server eller ändra filen /etc/hosts på alla noder. Det här exemplet visar hur du använder filen /etc/hosts. Ersätt IP-adressen och värdnamnet i följande kommandon:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL för HANA-konfiguration

   Konfigurera RHEL enligt beskrivningen i SAP Note nedan baserat på din RHEL-version

   - [2292690 – SAP HANA DB: Rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 – SAP HANA DB: Rekommenderade OS-inställningar för RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: Köra SAP-program som kompilerats med GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 – Linux: Köra SAP-program som kompilerats med GCC 7.x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: Köra SAP-program som kompilerats med GCC 9.x](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** Installera SAP HANA

   MDC är standardalternativet som startades med HANA 2.0 SPS 01. När du installerar HANA-systemet skapas SYSTEMDB och en klientorganisation med samma SID tillsammans. I vissa fall vill du inte ha standardklienten. Om du inte vill skapa en första klient tillsammans med installationen kan du följa [SAP-anteckningen 2629711](https://launchpad.support.sap.com/#/notes/2629711)

    Kör **hdblcm-programmet** från HANA-DVD:n. Ange följande värden i prompten:  
    Välj installation: Ange **1** (för installation)  
    Välj ytterligare komponenter för installation: Ange **1**.  
    Ange installationssökväg [/hana/shared]: tryck på Retur för att acceptera standardinställningen  
    Ange Lokalt värdnamn [..]: Tryck på Retur för att acceptera standardvärdet  
    Vill du lägga till ytterligare värdar i systemet? (j/n) [n]: **n**  
    Ange SAP HANA system-ID: Ange **HN1.**  
    Ange instansnummer [00]: Ange **03**  
    Välj Databasläge/Ange index [1]: tryck på Retur för att acceptera standardvärdet  
    Välj Systemanvändning/Ange index [4]: **ange 4** (för anpassad)  
    Ange Plats för datavolymer [/hana/data]: tryck på Retur för att acceptera standardvärdet  
    Ange plats för loggvolymer [/hana/log]: tryck på Retur för att acceptera standardvärdet  
    Begränsa den maximala minnesallokeringen? [n]: Tryck på Retur för att acceptera standardvärdet  
    Ange certifikatets värdnamn som värd "..."" –: tryck på Retur för att acceptera standardvärdet  
    Ange lösenord för SAP-värdagentanvändare (sapadm): Ange användarlösenordet för värdagenten  
    Bekräfta lösenordet för SAP-värdagentanvändaren (sapadm): Ange värdagentens användarlösenord igen för att bekräfta  
    Ange lösenord för systemadministratör (hn1adm): Ange systemadministratörens lösenord  
    Bekräfta systemadministratörslösenordet (hn1adm): Ange systemadministratörens lösenord igen för att bekräfta  
    Ange systemadministratörens hemkatalog [/usr/sap/HN1/home]: tryck på Retur för att acceptera standardinställningen  
    Ange systemadministratörens inloggningsgränssnitt [/bin/sh]: tryck på Retur för att acceptera standardvärdet  
    Ange användar-ID för systemadministratör [1001]: tryck på Retur för att acceptera standardvärdet  
    Ange ID för användargruppen (sapsys) [79]: tryck på Retur för att acceptera standardvärdet  
    Ange lösenord för databasanvändare (SYSTEM): Ange databasanvändarlösenordet  
    Bekräfta lösenord för databasanvändare (SYSTEM): Ange databasanvändarlösenordet igen för att bekräfta  
    Starta om systemet efter omstart av datorn? [n]: Tryck på Retur för att acceptera standardvärdet  
    Vill du fortsätta? (j/n): Validera sammanfattningen. Ange **y för** att fortsätta  

4. **[A] Uppgradera** SAP-värdagenten

   Ladda ned det senaste SAP-värdagentarkivet från [SAP Software Center](https://launchpad.support.sap.com/#/softwarecenter) och kör följande kommando för att uppgradera agenten. Ersätt sökvägen till arkivet så att den pekar på filen som du laddade ned:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A] Konfigurera** brandväggen

   Skapa brandväggsregeln för avsökningsporten för Azure Load Balancer.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>Konfigurera SAP HANA systemreplikering

Följ stegen i Konfigurera SAP HANA [systemreplikering för](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) att konfigurera SAP HANA systemreplikering. 

## <a name="cluster-configuration"></a>Klusterkonfiguration

I det här avsnittet beskrivs de steg som krävs för att klustret ska fungera sömlöst SAP HANA installeras på NFS-resurser med Azure NetApp Files. 

### <a name="create-a-pacemaker-cluster"></a>Skapa ett pacemakerkluster

Följ stegen i Konfigurera [pacemaker på en Red Hat Enterprise Linux](./high-availability-guide-rhel-pacemaker.md) Azure för att skapa ett grundläggande pacemakerkluster för den här HANA-servern.

### <a name="implement-the-python-system-replication-hook-saphanasr"></a>Implementera Python-systemreplikerings hooken SAPHanaSR

Det här är ett viktigt steg för att optimera integreringen med klustret och förbättra identifieringen när ett kluster redundans krävs. Vi rekommenderar starkt att du konfigurerar PYTHON-hooken SAPHanaSR.    

1. **[A]** Installera HANA "systemreplikerings hook". Hooken måste installeras på båda HANA DB-noderna.           

   > [!TIP]
   > Python-hooken kan bara implementeras för HANA 2.0.        

   1. Förbered hooken som `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Stoppa HANA på båda noderna. Kör som <sid \> adm:  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Justera `global.ini` på varje klusternod.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** Klustret kräver sudoers-konfiguration på varje klusternod för <sid \> adm. I det här exemplet uppnår vi detta genom att skapa en ny fil. Kör kommandona som `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```

3. **[A]** Starta SAP HANA på båda noderna. Kör som <sid \> adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** Kontrollera installationen av hooken. Kör som <sid \> adm på den aktiva replikeringsplatsen för HANA-systemet.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

Mer information om implementeringen av SAP HANA för systemreplikering finns [i Aktivera SAP HA/DR-providern hook](https://access.redhat.com/articles/3004101#enable-srhook).  

### <a name="configure-filesystem-resources"></a>Konfigurera filsystemresurser

I det här exemplet har varje klusternod sina egna HANA NFS-filsystem /hana/shared, /hana/data och /hana/log.   

1. **[1]** Placera klustret i underhållsläge.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** Skapa filsystemresurser för **hanadb1-monteringarna.**

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** Skapa filsystemresurser för **hanadb2-monteringarna.**

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` -attributet läggs till i övervakningsåtgärden så att varje övervakare utför ett läs-/skrivtest på filsystemet. Utan det här attributet verifierar övervakningsåtgärden endast att filsystemet är monterat. Detta kan vara ett problem eftersom när anslutningen går förlorad kan filsystemet förbli monterat trots att det inte går att komma åt.

    `on-fail=fence` -attributet läggs också till i övervakningsåtgärden. Om övervakaråtgärden misslyckas på en nod med det här alternativet stängs noden omedelbart av. Utan det här alternativet är standardbeteendet att stoppa alla resurser som är beroende av den misslyckade resursen, sedan starta om den misslyckade resursen och sedan starta alla resurser som är beroende av den misslyckade resursen. Det här beteendet kan inte bara ta lång tid när en SAPHana-resurs är beroende av den misslyckade resursen, utan kan också misslyckas helt och hållet. SAPHana-resursen kan inte stoppas om NFS-servern som har HANA-körbara filer inte är tillgänglig.

4. **[1] Konfigurera** platsbegränsningar

   Konfigurera platsbegränsningar för att säkerställa att de resurser som hanterar unika hanadb1-monteringar aldrig kan köras på hanadb2 och vice versa.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    Alternativet `resource-discovery=never` anges eftersom de unika monteringarna för varje nod delar samma monteringspunkt. Använder till exempel `hana_data1` monteringspunkten `/hana/data` och använder även `hana_data2` monteringspunkten `/hana/data` . Detta kan orsaka ett falskt positivt resultat för en avsökningsåtgärd när resurstillståndet kontrolleras vid klusterstart och detta i sin tur kan orsaka onödigt återställningsbeteende. Du kan undvika detta genom att ange `resource-discovery=never`

5. **[1] Konfigurera** attributresurser

   Konfigurera attributresurser. Dessa attribut anges till true om alla NFS-monteringar för en nod (/hana/data, /hana/log och /hana/data) är monterade och ställs in på falskt annars.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1] Konfigurera** platsbegränsningar

   Konfigurera platsbegränsningar för att säkerställa att hanadb1-attributresursen aldrig körs på hanadb2 och vice versa.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1] Skapa** ordningsbegränsningar

   Konfigurera ordningsbegränsningar så att en nods attributresurser endast startar när alla nodens NFS-monteringar har monterats.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Om konfigurationen innehåller filsystem utanför grupp eller inkluderar du alternativet så att det inte finns några `hanadb1_nfs` `hanadb2_nfs` `sequential=false` ordningsberoenden mellan filsystemen. Alla filsystem måste starta före `hana_nfs1_active` , men de behöver inte starta i någon ordning i förhållande till varandra. Mer information finns i Hur gör jag för att konfigurera SAP HANA systemreplikering i Scale-Up ett Pacemaker-kluster när [HANA-filsystemen finns på NFS-resurser](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>Konfigurera SAP HANA klusterresurser

1. Följ stegen i [Skapa SAP HANA klusterresurser](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) för att skapa SAP HANA resurser i klustret. När SAP HANA har skapats måste vi skapa en platsregelbegränsning mellan SAP HANA och filsystem (NFS-monteringar)

2. **[1]** Konfigurera begränsningar mellan SAP HANA och NFS-monteringar

   Platsregelbegränsningar anges så att SAP HANA kan köras på en nod endast om alla nodens NFS-monteringar är monterade.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Kontrollera status för klustret och alla resurser
   > [!NOTE]
   > Den här artikeln innehåller referenser till termen *slave*, en term som Microsoft inte längre använder. När termen tas bort från programvaran tar vi bort den från den här artikeln.
   
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

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Konfigurera aktiv/läsaktiverad systemreplikering i Pacemaker-kluster i HANA

Från och med SAP HANA 2.0 SPS 01 tillåter SAP aktiva/läsaktiverade konfigurationer för SAP HANA System Replication, där de sekundära systemen för SAP HANA-systemreplikering kan användas aktivt för läsintensiva arbetsbelastningar. För att stödja sådan konfiguration i ett kluster krävs en andra virtuell IP-adress som ger klienter åtkomst till den sekundära läsaktiverade SAP HANA databasen. För att säkerställa att den sekundära replikeringsplatsen fortfarande kan nås efter ett övertagande måste klustret flytta runt den virtuella IP-adressen med den sekundära för SAPHana-resursen.

Den ytterligare konfiguration som krävs för att hantera aktiv/läs-aktiverad systemreplikering i ett Red Hat-kluster med hög tillgänglighet med andra virtuella IP-adresser beskrivs i Konfigurera aktiv/läsaktiverad systemreplikering för HANA i [pacemakerkluster.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster)  

Innan du fortsätter kontrollerar du att du har konfigurerat red hat-kluster med hög tillgänglighet som hanterar SAP HANA databas enligt beskrivningen i ovanstående avsnitt i dokumentationen.    


## <a name="test-the-cluster-setup"></a>Testa klusterkonfigurationen

I det här avsnittet beskrivs hur du kan testa konfigurationen. 

1. Innan du startar ett test kontrollerar du att Pacemaker inte har någon misslyckad åtgärd (via pcs-status), att det inte finns några oväntade platsbegränsningar (till exempel rester av ett migreringstest) och att HANA-systemreplikeringen är synkroniseringstillstånd, till exempel med systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Kontrollera klusterkonfigurationen för ett felscenario när en nod förlorar åtkomst till NFS-resursen (/hana/delad)

   Resursagenterna SAP HANA binärfiler som lagras på för `/hana/shared` att utföra åtgärder under redundans. Filsystemet  `/hana/shared` monteras över NFS i det scenario som visas.  
   Det är svårt att simulera ett fel där en av servrarna förlorar åtkomst till NFS-resursen. Ett test som kan utföras är att montera filsystemet på nytt som skrivskyddade.
   Den här metoden verifierar att klustret kommer att kunna redundans, om åtkomsten `/hana/shared` till går förlorad på den aktiva noden.     


   **Förväntat resultat:** Vid skrivning som skrivskyddade filsystem misslyckas attributet för resursen som utför läs-/skrivåtgärd på filsystemet eftersom det inte går att skriva något på filsystemet och utför `/hana/shared` `OCF_CHECK_LEVEL` `hana_shared1` HANA-resurs redundans.  Samma resultat förväntas när HANA-noden förlorar åtkomst till NFS-resurser. 

   Resurstillstånd innan du startar testet:

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

   Du kan placera /hana/shared i skrivskyddade läge på den aktiva klusternoden med hjälp av följande kommando:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 startar antingen om eller startar om baserat på den åtgärd som angetts på stonith ( `pcs property show stonith-action` ).  När servern (hanadb1) är nere flyttas HANA-resursen till hanadb2. Du kan kontrollera status för klustret från hanadb2.

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

   Vi rekommenderar att du noggrant testar SAP HANA klusterkonfigurationen genom att även utföra de tester som beskrivs i Konfigurera [SAP HANA systemreplikering på RHEL.](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)

## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Machines planering och implementering för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* [NFS v4.1-volymer på Azure NetApp Files för SAP HANA](./hana-vm-operations-netapp.md)