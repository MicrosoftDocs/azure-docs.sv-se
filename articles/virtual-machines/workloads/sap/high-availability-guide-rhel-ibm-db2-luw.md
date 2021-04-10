---
title: Konfigurera IBM DB2-HADR på virtuella Azure-datorer (VM) på RHEL | Microsoft Docs
description: Upprätta hög tillgänglighet för IBM DB2-LUW på virtuella datorer med Azure Virtual Machines (VM) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: juergent
ms.openlocfilehash: 979de230655b4804fa65a4bddb678e21b59c4ea2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674461"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Hög tillgänglighet för IBM Db2 LUW på virtuella Azure-datorer på Red Hat Enterprise Linux Server

IBM DB2 för Linux, UNIX och Windows (LUW) i [konfigurationen för hög tillgänglighet och haveri beredskap (hadr)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) består av en nod som kör en primär databas instans och minst en nod som kör en sekundär databas instans. Ändringar av den primära databas instansen replikeras till en sekundär databas instans synkront eller asynkront, beroende på din konfiguration. 

> [!NOTE]
> Den här artikeln innehåller referenser till villkors *huvud* och *slav*, och villkor som Microsoft inte längre använder. När de här villkoren tas bort från program varan tar vi bort dem från den här artikeln.

Den här artikeln beskriver hur du distribuerar och konfigurerar virtuella datorer i Azure, installerar kluster ramverket och installerar IBM DB2-LUW med HADR-konfigurationen. 

Artikeln beskriver inte hur du installerar och konfigurerar IBM DB2-LUW med HADR eller SAP-programinstallation. För att hjälpa dig att utföra dessa uppgifter tillhandahåller vi referenser till installations handböcker för SAP och IBM. Den här artikeln fokuserar på delar som är speciella för Azure-miljön. 

De IBM DB2-versioner som stöds är 10,5 och senare, enligt beskrivningen i SAP NOTE [1928533].

Innan du påbörjar en installation kan du läsa följande SAP-anteckningar och dokumentation:

| SAP-anteckning | Beskrivning |
| --- | --- |
| [1928533] | SAP-program på Azure: produkter och typer av virtuella Azure-datorer som stöds |
| [2015553] | SAP på Azure: support krav |
| [2178632] | Nyckel övervaknings mått för SAP på Azure |
| [2191498] | SAP på Linux med Azure: förbättrad övervakning |
| [2243692] | Linux på Azure (IaaS) VM: SAP-licens problem |
| [2002167] | Red Hat Enterprise Linux 7. x: installation och uppgradering |
| [2694118] | Red Hat Enterprise Linux HA Add-On på Azure |
| [1999351] | Felsöka utökad Azure-övervakning för SAP |
| [2233094] | DB6: SAP-program på Azure som använder IBM DB2 för Linux, UNIX och Windows – Ytterligare information |
| [1612105] | DB6: vanliga frågor och svar om DB2 med HADR |


| Dokumentation | 
| --- |
| [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): har alla de SAP-anteckningar som krävs för Linux |
| [Azure Virtual Machines planera och implementera för SAP på Linux][planning-guide] -guide |
| [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide] (den här artikeln) |
| [Distributions system för Azure Virtual Machines Database Management system (DBMS) för SAP på Linux-][dbms-guide] guiden |
| [SAP-arbetsbelastning på Azure planering och distribution check lista][azr-sap-plancheck] |
| [Översikt över Add-On med hög tillgänglighet för Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Add-On administration med hög tillgänglighet][rhel-ha-admin] |
| [Add-On referens för hög tillgänglighet][rhel-ha-ref] |
| [Support principer för RHEL-kluster med hög tillgänglighet – Microsoft Azure Virtual Machines som kluster medlemmar][rhel-azr-supp]
| [Installera och konfigurera en Red Hat Enterprise Linux 7,4 (och senare) High-Availability kluster på Microsoft Azure][rhel-azr-inst]
| [DBMS-distribution för SAP-arbetsbelastning för IBM Db2 på virtuella Azure-datorer][dbms-db2] |
| [IBM DB2 HADR 11,1][db2-hadr-11.1] |
| [IBM DB2 HADR 10,5][db2-hadr-10.5] |
| [Support policy för RHEL-kluster med hög tillgänglighet – hantering av IBM DB2 för Linux, UNIX och Windows i ett kluster][rhel-db2-supp]



## <a name="overview"></a>Översikt
För att uppnå hög tillgänglighet installeras IBM DB2 LUW med HADR på minst två virtuella Azure-datorer, som distribueras i en [Azures tillgänglighets uppsättning](../../windows/tutorial-availability-sets.md) eller mellan [Azure-tillgänglighetszoner](./sap-ha-availability-zones.md). 

Följande grafik visar en installation av två virtuella Azure-datorer i databas server. Både de virtuella Azure-datorerna har sin egen lagring ansluten och är igång. I HADR har en databas instans i en av de virtuella Azure-datorerna rollen för den primära instansen. Alla klienter är anslutna till den primära instansen. Alla ändringar i databas transaktioner sparas lokalt i DB2-transaktionshanteraren. När transaktions logg posterna sparas lokalt överförs posterna via TCP/IP till databas instansen på den andra databas servern, standby-servern eller vänte läges instansen. Standby-instansen uppdaterar den lokala databasen genom att vidarebefordra överförda transaktions logg poster. På så sätt hålls standby-servern synkroniserad med den primära servern.

HADR är bara en replikeringsprincip. Den har ingen identifiering av fel och ingen automatisk övertag ande eller redundans. En övertag ande eller överföring till standby-servern måste initieras manuellt av en databas administratör. Om du vill uppnå en automatisk Övertagning och fel identifiering kan du använda kluster funktionen för Linux-pacemaker. Pacemaker övervakar de två databas server instanserna. När den primära databas Server instansen kraschar initierar pacemaker en *Automatisk* hadr-Övertagning från standby-servern. Pacemaker säkerställer också att den virtuella IP-adressen tilldelas till den nya primära servern.

![Översikt över IBM DB2 hög tillgänglighet](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Om du vill att SAP-program servrarna ska ansluta till den primära databasen behöver du ett virtuellt värdnamn och en virtuell IP-adress. I händelse av en redundansväxling ansluter SAP-programservrarna till den nya primära databas instansen. I en Azure-miljö krävs en [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) för att använda en virtuell IP-adress på det sätt som krävs för hadr av IBM DB2. 

För att hjälpa dig att förstå hur IBM DB2 LUW med HADR och pacemaker passar in i en konfiguration med hög tillgänglighet för SAP-system, visar följande bild en översikt över en hög tillgänglig installation av ett SAP-system baserat på IBM DB2-databas. Den här artikeln beskriver bara IBM DB2, men innehåller referenser till andra artiklar om hur du konfigurerar andra komponenter i ett SAP-system.

![Översikt över fullständig miljö för IBM DB2-hög tillgänglighet](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Översikt över nödvändiga steg på hög nivå
Om du vill distribuera en IBM DB2-konfiguration måste du följa dessa steg:

  + Planera din miljö.
  + Distribuera de virtuella datorerna.
  + Uppdatera RHEL Linux och konfigurera fil system.
  + Installera och konfigurera pacemaker.
  + Konfigurera [glusterfs-kluster][glusterfs] eller [Azure NetApp Files][anf-rhel]
  + Installera [ASCS/ers på ett separat kluster][ascs-ha-rhel].
  + Installera IBM DB2 Database med alternativet distribuerad/hög tillgänglighet (SWPM).
  + Installera och skapa en sekundär databas nod och instans och konfigurera HADR.
  + Bekräfta att HADR fungerar.
  + Använd pacemaker-konfigurationen för att kontrol lera IBM DB2.
  + Konfigurera Azure Load Balancer.
  + Installera primära program servrar och-dialog.
  + Kontrol lera och anpassa konfigurationen av SAP-program servrar.
  + Utför redundans och övertag ande test.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planera Azure-infrastrukturen för att vara värd för IBM DB2-LUW med HADR

Slutför planerings processen innan du kör distributionen. Planering skapar grunden för att distribuera en konfiguration av DB2 med HADR i Azure. Viktiga element som måste vara en del av planeringen för IMB DB2-LUW (databas delen av SAP-miljön) visas i följande tabell:

| Avsnitt | Kort beskrivning |
| --- | --- |
| Definiera Azure-resurs grupper | Resurs grupper där du distribuerar VM, VNet, Azure Load Balancer och andra resurser. Kan vara befintlig eller ny. |
| Definition av virtuellt nätverk/undernät | Där virtuella datorer för IBM DB2 och Azure Load Balancer distribueras. Kan vara befintlig eller nyligen skapad. |
| Virtuella datorer som är värdar för IBM DB2 LUW | VM-storlek, lagring, nätverk, IP-adress. |
| Virtuellt värdnamn och virtuell IP för IBM DB2-databas| Den virtuella IP-adressen eller värd namnet som används för anslutning av SAP-program servrar. **db-virt-hostname**, **db-virt-IP**. |
| Azure-staket | Metoden för att undvika delade hjärna-situationer förhindras. |
| Azure Load Balancer | Användning av Basic eller standard (rekommenderas), avsöknings port för DB2 Database (vår rekommendation 62500) **avsöknings port**. |
| Namnmatchning| Hur namn matchning fungerar i miljön. DNS-tjänsten rekommenderas starkt. Den lokala värd filen kan användas. |
    
Mer information om Linux-pacemaker i Azure finns i [Konfigurera pacemaker på Red Hat Enterprise Linux i Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Distribution på Red Hat Enterprise Linux

Resurs agenten för IBM DB2 LUW ingår i Red Hat Enterprise Linux server HA-tillägg. För den konfiguration som beskrivs i det här dokumentet bör du använda Red Hat Enterprise Linux för SAP. Azure Marketplace innehåller en avbildning för Red Hat Enterprise Linux 7,4 för SAP eller senare som du kan använda för att distribuera nya virtuella Azure-datorer. Var medveten om de olika support-och tjänst modeller som erbjuds av Red Hat via Azure Marketplace när du väljer en VM-avbildning på Azure VM Marketplace.

### <a name="hosts-dns-updates"></a>Värdar: DNS-uppdateringar
Skapa en lista med alla värdnamn, inklusive virtuella värdnamn, och uppdatera dina DNS-servrar för att aktivera rätt IP-adress till värd namns matchning. Om det inte finns någon DNS-server eller om du inte kan uppdatera och skapa DNS-poster, måste du använda de lokala värddatorns filer för de enskilda virtuella datorerna som deltar i det här scenariot. Om du använder poster för värd filer kontrollerar du att posterna tillämpas på alla virtuella datorer i SAP-systemmiljön. Vi rekommenderar dock att du använder din DNS som, helst, ökar till Azure


### <a name="manual-deployment"></a>Manuell distribution

Kontrol lera att det valda operativ systemet stöds av IBM/SAP för IBM DB2 LUW. Listan över OS-versioner som stöds för virtuella Azure-datorer och DB2-versioner finns i SAP NOTE [1928533]. Listan över OS-versioner av enskilda DB2-versioner finns i produkt tillgänglighets matrisen för SAP. Vi rekommenderar starkt minst Red Hat Enterprise Linux 7,4 för SAP på grund av Azure-relaterade prestanda förbättringar i den här eller senare Red Hat Enterprise Linux versionerna.

1. Skapa eller Välj en resurs grupp.
1. Skapa eller Välj ett virtuellt nätverk och undernät.
1. Skapa en Azures tillgänglighets uppsättning eller distribuera en tillgänglighets zon.
    + För tillgänglighets uppsättningen anger du maximalt 2 för uppdaterings domäner.
1. Skapa virtuell dator 1.
    + Använd Red Hat Enterprise Linux för SAP-avbildning på Azure Marketplace.
    + Välj den Azure tillgänglighets uppsättning som du skapade i steg 3, eller Välj tillgänglighets zon.
1.  Skapa virtuell dator 2.
    + Använd Red Hat Enterprise Linux för SAP-avbildning på Azure Marketplace.
    + Välj den Azure tillgänglighets uppsättning som du skapade i steg 3, eller Välj tillgänglighets zon (inte samma zon som i steg 3).
1. Lägg till data diskar i de virtuella datorerna och kontrol lera sedan rekommendationen för ett fil system installations program i artikeln [IBM DB2 Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar][dbms-db2].

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Installera IBM DB2 LUW och SAP-miljön

Innan du påbörjar installationen av en SAP-miljö baserat på IBM DB2-LUW bör du läsa följande dokumentation:

+ Azure-dokumentation
+ SAP-dokumentation
+ IBM-dokumentation

Länkar till den här dokumentationen finns i introduktions avsnittet i den här artikeln.

Se installations handböcker för SAP om hur du installerar NetWeaver-baserade program på IBM DB2 LUW.
Du hittar guiderna på SAP Help-portalen med hjälp av [SAP-installations guide Finder][sap-instfind].

Du kan minska antalet guider som visas i portalen genom att ange följande filter:
- Jag vill: "installera ett nytt system"
- Min databas: "IBM DB2 för Linux, UNIX och Windows"
- Ytterligare filter för SAP NetWeaver-versioner, stack-konfiguration eller operativ system

#### <a name="red-hat-firewall-rules"></a>Regler för Red Hat-brandvägg
Red Hat Enterprise Linux har brand vägg aktive rad som standard. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installations tips för att konfigurera IBM DB2-LUW med HADR

Konfigurera den primära IBM DB2 LUW-databas instansen:

- Använd alternativet hög tillgänglighet eller distribuerat.
- Installera SAP ASCS/ERS och databas instansen.
- Gör en säkerhets kopia av den nyligen installerade databasen.

> [!IMPORTANT] 
> Skriv ner "databas kommunikations porten" som anges under installationen. Det måste vara samma port nummer för båda databas instanserna.
>![Port definition för SAP SWPM](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Inställningar för IBM DB2-HADR för Azure

   När du använder en pacemaker-agent för Azure-anger du följande parametrar:

   - Varaktighet för HADR-peer-fönstret (sekunder) (HADR_PEER_WINDOW) = 240  
   - Timeout-värde för HADR (HADR_TIMEOUT) = 45

Vi rekommenderar föregående parametrar baserat på inledande redundans/övertag ande testning. Det är obligatoriskt att du testar rätt funktioner för redundans och övertag ande med dessa parameter inställningar. Eftersom enskilda konfigurationer kan variera kan parametrarna kräva justeringar. 

> [!NOTE]
> Särskilt för IBM DB2 med HADR-konfiguration med normal start: den sekundära databasen eller vänte läges instansen måste vara igång innan du kan starta den primära databas instansen.

   
> [!NOTE]
> För installation och konfiguration som är specifika för Azure och pacemaker: under installations proceduren via SAP Software Provisioning Manager finns det en explicit fråga om hög tillgänglighet för IBM DB2 LUW:
>+ Välj inte **IBM DB2 pureScale**.
>+ Välj inte **Installera IBM Tivoli system Automation för multiplattformar**.
>+ Välj inte skapa konfigurationsfiler för **kluster**.
>![SAP SWPM – DB2 HA-alternativ](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Gör så här för att konfigurera vänte läges databas servern med hjälp av en SAP homogen system kopierings procedur:

1. Välj **system kopierings** alternativet > **mål systemets**  >  **distribuerade**  >  **databas instans**.
1. Som en kopierings metod väljer du **homogent system** så att du kan använda säkerhets kopiering för att återställa en säkerhets kopia på vänte läges Server instansen.
1. När du kommer till steget avsluta för att återställa databasen för homogen system kopia avslutar du installations programmet. Återställ databasen från en säkerhets kopia av den primära värden. Alla efterföljande installations faser har redan körts på den primära databas servern.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat brand Väggs regler för DB2 HADR
Lägg till brand Väggs regler som tillåter trafik till DB2 och mellan DB2 för att HADR ska fungera:
+ Kommunikations port för databas. Om du använder partitioner bör du även lägga till dessa portar.
+ HADR-port (värde för DB2-parameter HADR_LOCAL_SVC)
+ Azure PROBE-port
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>Kontroll av IBM DB2-HADR
I demonstrations syfte och de procedurer som beskrivs i den här artikeln är databasens SID **ID2**.

När du har konfigurerat HADR och statusen är PEER och ansluten på den primära och den vänte noden, utför du följande kontroll:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N


#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N
</code></pre>

### <a name="configure-azure-load-balancer"></a>Konfigurera Azure Load Balancer

Om du vill konfigurera Azure Load Balancer rekommenderar vi att du använder [Azure standard load BALANCER SKU](../../../load-balancer/load-balancer-overview.md) och gör sedan följande:

> [!NOTE]
> Standard Load Balancer SKU: n har begränsningar med åtkomst till offentliga IP-adresser från noderna under Load Balancer. I artikeln [offentlig slut punkts anslutning för Virtual Machines med Azure standard Load Balancer i SAP-scenarier med hög tillgänglighet](./high-availability-guide-standard-load-balancer-outbound-connections.md) beskrivs olika sätt att aktivera noderna för att få åtkomst till offentliga IP-adresser

> [!IMPORTANT]
> Flytande IP stöds inte på en sekundär NIC-IP-konfiguration i belastnings Utjämnings scenarier. Mer information finns i [begränsningar för Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.  

1. Skapa en IP-adresspool på klient sidan:

   a. I Azure Portal öppnar du Azure Load Balancer, väljer **IP-pool för klient** del och väljer sedan **Lägg till**.

   b. Ange namnet på den nya frontend-IP-poolen (till exempel **DB2-Connection**).

   c. Ange **tilldelningen** till **statisk** och ange IP-adressen för den **virtuella-IP** -adress som definierats i början.

   d. Välj **OK**.

   e. När den nya frontend-IP-poolen har skapats noterar du poolens IP-adress.

1. Skapa en backend-pool:

   a. I Azure Portal öppnar du Azure Load Balancer, väljer backend- **pooler** och väljer sedan **Lägg till**.

   b. Ange namnet på den nya backend-poolen (till exempel **DB2-backend**).

   c. Välj **Lägg till en virtuell dator**.

   d. Välj den tillgänglighets uppsättning eller de virtuella datorer som är värd för IBM DB2-databasen som skapades i föregående steg.

   e. Välj de virtuella datorerna i IBM DB2-klustret.

   f. Välj **OK**.

1. Skapa en hälso avsökning:

   a. I Azure Portal öppnar du Azure Load Balancer, väljer **hälso avsökningar** och väljer **Lägg till**.

   b. Ange namnet på den nya hälso avsökningen (till exempel **DB2-HP**).

   c. Välj **TCP** som protokoll och port **62500**. Behåll **intervallvärdet** inställt på **5** och behåll **tröskelvärdet för tröskelvärdet** som är satt till **2**.

   d. Välj **OK**.

1. Skapa regler för belastnings utjämning:

   a. I Azure Portal öppnar du Azure Load Balancer, väljer **belastnings Utjämnings regler** och väljer sedan **Lägg till**.

   b. Ange namnet på den nya Load Balancers regeln (till exempel **DB2-sid**).

   c. Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **DB2-frontend**).

   d. Behåll **protokollet** inställt på **TCP** och ange *kommunikations port* för port databas.

   e. Öka **tids gränsen för inaktivitet** till 30 minuter.

   f. Se till att **Aktivera flytande IP**.

   ex. Välj **OK**.

**[A]** Lägg till brand Väggs regel för avsöknings port:

<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

## <a name="create-the-pacemaker-cluster"></a>Skapa pacemaker-klustret
    
Information om hur du skapar ett grundläggande pacemaker-kluster för den här IBM DB2-servern finns i [Konfigurera pacemaker på Red Hat Enterprise Linux i Azure][rhel-pcs-azr]. 

## <a name="db2-pacemaker-configuration"></a>Konfiguration av DB2-pacemaker

När du använder pacemaker för automatisk redundansväxling i händelse av ett nodfel måste du konfigurera dina DB2-instanser och pacemaker. I det här avsnittet beskrivs den här typen av konfiguration.

Följande objekt föregås av antingen:

- **[A]**: gäller för alla noder
- **[1]**: gäller endast nod 1 
- **[2]**: gäller endast nod 2

**[A]** förutsättning för pacemaker-konfiguration:
1. Stäng båda databas servrarna med User DB2 \<sid> med db2stop.
1. Ändra skal miljön för DB2- \<sid> användare till */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>  

### <a name="pacemaker-configuration"></a>Pacemaker-konfiguration

**[1]** IBM DB2 hadr-Specific pacemaker-konfiguration:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** skapa IBM DB2-resurser:

Om du skapar ett kluster på **RHEL 7. x**, använder du följande kommandon:

<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

Om du skapar ett kluster på **RHEL 8. x** använder du följande kommandon:

<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' promotable meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-clone meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-clone

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-clone then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** starta IBM DB2-resurser:
* Sätt pacemaker ur underhålls läge.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt vilken nod resurserna körs på.
<pre><code>sudo pcs status
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

> [!IMPORTANT]
> Du måste hantera den pacemaker klustrade DB2-instansen med hjälp av pacemaker-verktyg. Om du använder DB2-kommandon som db2stop, identifierar pacemaker åtgärden som ett resurs haveri. Om du utför underhåll kan du spara noderna eller resurserna i underhålls läge. Pacemaker pausar övervaknings resurser och du kan sedan använda vanliga administrations kommandon för DB2.

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Gör ändringar i SAP-profiler för att använda virtuell IP-adress för anslutning
För att ansluta till den primära instansen av HADR-konfigurationen måste SAP-programlagret använda den virtuella IP-adress som du definierade och konfigurerat för Azure Load Balancer. Följande ändringar krävs:

/sapmnt/ \<SID> /Profile/default. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/ \<SID> /global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>

## <a name="install-primary-and-dialog-application-servers"></a>Installera primära program servrar och-dialog

När du installerar primära och dialog program servrar mot en DB2 HADR-konfiguration, använder du det virtuella värd namnet som du har valt för konfigurationen.

Om du utförde installationen innan du skapade DB2 HADR-konfigurationen gör du ändringarna enligt beskrivningen i föregående avsnitt och enligt följande för SAP Java-stackar.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java eller Java stack system JDBC URL-kontroll

Använd verktyget J2EE config för att kontrol lera eller uppdatera JDBC-URL: en. Eftersom verktyget J2EE config är ett grafiskt verktyg måste du ha installerat X-servern:
 
1. Logga in på den primära program servern för J2EE-instansen och kör:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. I den vänstra rutan väljer du **säkerhets lager**.
1. Välj nyckeln i den högra rutan `jdbc/pool/\<SAPSID>/url` .
1. Ändra värd namnet i JDBC-URL: en till det virtuella värd namnet.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Välj **Lägg till**.
1. Om du vill spara ändringarna väljer du disk ikonen längst upp till vänster.
1. Stäng konfigurations verktyget.
1. Starta om Java-instansen.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurera logg arkivering för HADR-installation

Om du vill konfigurera DB2-HADR för installation av DB2 rekommenderar vi att du konfigurerar både den primära databasen och vänte läges databasen till automatisk logg hämtnings funktion från alla logg Arkiv platser. Både den primära databasen och standby-databasen måste kunna hämta loggfiler från alla logg Arkiv platser som antingen en av databas instanserna kan arkivera loggfiler. 

Logg arkiveringen utförs endast av den primära databasen. Om du ändrar HADR-rollerna för databas servrarna eller om ett fel inträffar är den nya primära databasen ansvarig för logg arkivering. Om du har konfigurerat flera logg Arkiv platser kan loggarna arkiveras två gånger. I händelse av lokal eller fjärran sluten fångst kan du också behöva kopiera arkiverade loggar manuellt från den gamla primära servern till den aktiva logg platsen för den nya primära servern.

Vi rekommenderar att du konfigurerar en gemensam NFS-resurs eller GlusterFS, där loggar skrivs från båda noderna. NFS-resursen eller GlusterFS måste ha hög tillgänglighet. 

Du kan använda befintliga NFS-resurser med hög tillgänglighet eller GlusterFS för transporter eller en profil katalog. Mer information finns i:

- [GlusterFS på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP NetWeaver][glusterfs] 
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på Red Hat Enterprise Linux med Azure NetApp Files för SAP-program][anf-rhel]
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (för att skapa NFS-resurser)

## <a name="test-the-cluster-setup"></a>Testa kluster konfigurationen

I det här avsnittet beskrivs hur du kan testa installationen av DB2-HADR. Varje test förutsätter att IBM DB2 Primary körs på den virtuella datorn *AZ-idb01* . Användare med sudo-behörigheter eller rot (rekommenderas inte) måste användas.

Den inledande statusen för alla test fall förklaras här: (crm_mon-r eller PC-status)

- **PC-status** är en ögonblicks bild av pacemaker status vid körnings tillfället 
- **crm_mon-r** är kontinuerlig utdata av pacemaker status

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

Den ursprungliga statusen i ett SAP-system dokumenteras i transaktion DBACOCKPIT > konfiguration > översikt, som du ser i följande bild:

![DBACockpit – för migrering](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)

### <a name="test-takeover-of-ibm-db2"></a>Testa övertag ande av IBM DB2


> [!IMPORTANT] 
> Innan du börjar testet måste du kontrol lera att:
> * Pacemaker har inga misslyckade åtgärder (dator status).
> * Det finns inga plats begränsningar (rester av migrations test)
> * Synkroniseringen av IBM DB2-HADR fungerar. Kontrol lera med User DB2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>

Migrera noden som kör den primära DB2-databasen genom att köra följande kommando:
<pre><code># On RHEL 7.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-clone --master
</code></pre>

När migreringen är färdig ser CRM-status utdata ut så här:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

Den ursprungliga statusen i ett SAP-system dokumenteras i transaktion DBACOCKPIT > konfiguration > översikt, som du ser i följande bild:

![DBACockpit-post-migrering](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Resursallokering med "resurser för resurs flytt" skapar plats begränsningar. Plats begränsningar i det här fallet förhindrar inte att IBM DB2-instansen körs på AZ-idb01. Om plats begränsningar inte tas bort kan resursen inte återställas.

Ta bort plats begränsningen och vänte läges noden kommer att startas på AZ-idb01.
<pre><code># On RHEL 7.x
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource clear Db2_HADR_<b>ID2</b>-clone</code></pre>

Och kluster status ändras till:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit – begränsning för borttagen plats](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)

Migrera tillbaka resursen till *AZ-idb01* och ta bort plats begränsningarna
<pre><code># On RHEL 7.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-clone --master
sudo pcs resource clear Db2_HADR_<b>ID2</b>-clone</code></pre>

- **På RHEL 7. x-PC-resurs flytt \<res_name> <host> :** skapar plats begränsningar och kan orsaka problem med övertag Ande
- **På RHEL 8. x-PC-resurs Move \<res_name> -Master:** skapar plats begränsningar och kan orsaka problem med övertag Ande
- **resurs rensning \<res_name> av datorer**: rensar plats begränsningar
- **resurs rensning \<res_name> för datorer**: rensar alla fel i resursen

### <a name="test-a-manual-takeover"></a>Testa en manuell övertag Ande

Du kan testa en manuell övertag ande genom att stoppa pacemaker-tjänsten på *AZ-idb01-* noden:
<pre><code>systemctl stop pacemaker</code></pre>

status för *AZ-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Efter redundansväxlingen kan du starta tjänsten igen på *AZ-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Avsluta DB2-processen på den nod som kör den primära HADR-databasen

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

DB2-instansen kommer att Miss sen och pacemaker kommer att flytta huvud-noden och rapportera följande status:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker startar om den primära DB2-databas instansen på samma nod, eller växlar över till noden som kör den sekundära databas instansen och ett fel rapporteras.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Avsluta DB2-processen på noden som kör den sekundära databas instansen

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Noden får ett felaktigt angivet och rapporterat fel
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

DB2-instansen startas om i den sekundära rollen som den tilldelades tidigare.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Stoppa DB via db2stop-tvång på noden som kör den primära databas instansen HADR

Som user DB2 \<sid> execute kommandot db2stop Force:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Det gick inte att identifiera:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Den sekundära databas instansen DB2 HADR har befordrats till den primära rollen.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Krascha den virtuella datorn som kör den primära databas instansen HADR med "stoppad"

<pre><code>#Linux kernel panic.
sudo echo b > /proc/sysrq-trigger</code></pre>

I sådana fall kommer pacemaker att identifiera att noden som kör den primära databas instansen inte svarar.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

Nästa steg är att söka efter en *delad hjärna* . När den kvarvarande noden har fastställt att noden som senast körde den primära databas instansen inte är igång körs en redundansväxling av resurserna.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


I händelse av en kernel-panik, kommer den felande noden att omfås av staket-agenten. När den felande noden är online igen måste du starta pacemaker-kluster med
<pre><code>sudo pcs cluster start</code></pre> den startar DB2-instansen i den sekundära rollen.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Nästa steg
- [Arkitektur och scenarier med hög tillgänglighet för SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
- [Konfigurera pacemaker på Red Hat Enterprise Linux i Azure][rhel-pcs-azr]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
