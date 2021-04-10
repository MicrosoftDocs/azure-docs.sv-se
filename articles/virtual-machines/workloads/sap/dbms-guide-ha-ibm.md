---
title: Konfigurera IBM DB2-HADR på virtuella Azure-datorer (VM) | Microsoft Docs
description: Upprätta hög tillgänglighet för IBM DB2-LUW på virtuella datorer i Azure.
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 10/16/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: faafce32c3452a5c4ff08783ec2edd28f7f961e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671881"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Hög tillgänglighet för IBM DB2-LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med pacemaker

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
| [1984787] | SUSE LINUX Enterprise Server 12: installations information |
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
| [SUSE Linux Enterprise Server för SAP-program 12 SP4 Best Practices-guider][sles-for-sap-bp] |
| [SUSE Linux Enterprise-tillägg för hög tillgänglighet 12 SP4][sles-ha-guide] |
| [DBMS-distribution för SAP-arbetsbelastning för IBM Db2 på virtuella Azure-datorer][dbms-db2] |
| [IBM DB2 HADR 11,1][db2-hadr-11.1] |
| [IBM DB2 HADR R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Översikt
För att uppnå hög tillgänglighet installeras IBM DB2 LUW med HADR på minst två virtuella Azure-datorer, som distribueras i en [Azures tillgänglighets uppsättning](../../windows/tutorial-availability-sets.md) eller mellan [Azure-tillgänglighetszoner](./sap-ha-availability-zones.md). 

Följande grafik visar en installation av två virtuella Azure-datorer i databas server. Både de virtuella Azure-datorerna har sin egen lagring ansluten och är igång. I HADR har en databas instans i en av de virtuella Azure-datorerna rollen för den primära instansen. Alla klienter är anslutna till den här primära instansen. Alla ändringar i databas transaktioner sparas lokalt i DB2-transaktionshanteraren. När transaktions logg posterna sparas lokalt överförs posterna via TCP/IP till databas instansen på den andra databas servern, standby-servern eller vänte läges instansen. Standby-instansen uppdaterar den lokala databasen genom att vidarebefordra överförda transaktions logg poster. På så sätt hålls standby-servern synkroniserad med den primära servern.

HADR är bara en replikeringsprincip. Den har ingen identifiering av fel och ingen automatisk övertag ande eller redundans. En övertag ande eller överföring till standby-servern måste initieras manuellt av en databas administratör. Om du vill uppnå en automatisk Övertagning och fel identifiering kan du använda kluster funktionen för Linux-pacemaker. Pacemaker övervakar de två databas server instanserna. När den primära databas Server instansen kraschar initierar pacemaker en *Automatisk* hadr-Övertagning från standby-servern. Pacemaker säkerställer också att den virtuella IP-adressen tilldelas till den nya primära servern.

![Översikt över IBM DB2 hög tillgänglighet](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Om du vill att SAP-program servrarna ska ansluta till den primära databasen behöver du ett virtuellt värdnamn och en virtuell IP-adress. I händelse av en redundansväxling ansluter SAP-programservrarna till den nya primära databas instansen. I en Azure-miljö krävs en [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) för att använda en virtuell IP-adress på det sätt som krävs för hadr av IBM DB2. 

För att hjälpa dig att förstå hur IBM DB2 LUW med HADR och pacemaker passar in i en konfiguration med hög tillgänglighet för SAP-system, visar följande bild en översikt över en hög tillgänglig installation av ett SAP-system baserat på IBM DB2-databas. Den här artikeln beskriver bara IBM DB2, men innehåller referenser till andra artiklar om hur du konfigurerar andra komponenter i ett SAP-system.

![Översikt över fullständig miljö för IBM DB2-hög tillgänglighet](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Översikt över nödvändiga steg på hög nivå
Om du vill distribuera en IBM DB2-konfiguration måste du följa dessa steg:

  + Planera din miljö.
  + Distribuera de virtuella datorerna.
  + Uppdatera SUSE Linux och konfigurera fil system.
  + Installera och konfigurera pacemaker.
  + Installera [NFS med hög][nfs-ha]tillgänglighet.
  + Installera [ASCS/ers på ett separat kluster][ascs-ha].
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
| Azure-staket | Azure-staket eller SBD staket (rekommenderas). Metod för att undvika delade hjärna-situationer. |
| SBD VM | SBD virtuell dator storlek, lagring, nätverk. |
| Azure Load Balancer | Användning av Basic eller standard (rekommenderas), avsöknings port för DB2 Database (vår rekommendation 62500) **avsöknings port**. |
| Namnmatchning| Hur namn matchning fungerar i miljön. DNS-tjänsten rekommenderas starkt. Den lokala värd filen kan användas. |
    
Mer information om Linux-pacemaker i Azure finns i [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure](./high-availability-guide-suse-pacemaker.md).

## <a name="deployment-on-suse-linux"></a>Distribution på SUSE Linux

Resurs agenten för IBM DB2 LUW ingår i SUSE Linux Enterprise Server för SAP-program. För den konfiguration som beskrivs i det här dokumentet måste du använda SUSE Linux server för SAP-program. Azure Marketplace innehåller en avbildning för SUSE Enterprise Server för SAP-program 12 som du kan använda för att distribuera nya virtuella Azure-datorer. Tänk på de olika support-och tjänst modeller som erbjuds av SUSE via Azure Marketplace när du väljer en VM-avbildning på Azures Marketplace för virtuella datorer. 

### <a name="hosts-dns-updates"></a>Värdar: DNS-uppdateringar
Skapa en lista med alla värdnamn, inklusive virtuella värdnamn, och uppdatera dina DNS-servrar för att aktivera rätt IP-adress till värd namns matchning. Om det inte finns någon DNS-server eller om du inte kan uppdatera och skapa DNS-poster, måste du använda de lokala värddatorns filer för de enskilda virtuella datorerna som deltar i det här scenariot. Om du använder poster för värd filer kontrollerar du att posterna tillämpas på alla virtuella datorer i SAP-systemmiljön. Vi rekommenderar dock att du använder din DNS som, helst, ökar till Azure


### <a name="manual-deployment"></a>Manuell distribution

Kontrol lera att det valda operativ systemet stöds av IBM/SAP för IBM DB2 LUW. Listan över OS-versioner som stöds för virtuella Azure-datorer och DB2-versioner finns i SAP NOTE [1928533]. Listan över OS-versioner av enskilda DB2-versioner finns i produkt tillgänglighets matrisen för SAP. Vi rekommenderar starkt minst SLES 12 SP4 på grund av Azure-relaterade prestanda förbättringar i den här eller senare SUSE Linux-versionen.

1. Skapa eller Välj en resurs grupp.
1. Skapa eller Välj ett virtuellt nätverk och undernät.
1. Skapa en Azures tillgänglighets uppsättning eller distribuera en tillgänglighets zon.
    + För tillgänglighets uppsättningen anger du maximalt 2 för uppdaterings domäner.
1. Skapa virtuell dator 1.
    + Använd SLES för SAP-avbildningar på Azure Marketplace.
    + Välj den Azure tillgänglighets uppsättning som du skapade i steg 3, eller Välj tillgänglighets zon.
1.  Skapa virtuell dator 2.
    + Använd SLES för SAP-avbildningar på Azure Marketplace.
    + Välj den Azure tillgänglighets uppsättning som du skapade i steg 3, eller Välj tillgänglighets zon (inte samma zon som i steg 3).
1. Lägg till data diskar i de virtuella datorerna och kontrol lera sedan rekommendationen för ett fil system installations program i artikeln [IBM DB2 Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Skapa pacemaker-klustret
    
Information om hur du skapar ett grundläggande pacemaker-kluster för den här IBM DB2-servern finns i [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure][sles-pacemaker]. 

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

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installations tips för att konfigurera IBM DB2-LUW med HADR

Konfigurera den primära IBM DB2 LUW-databas instansen:

- Använd alternativet hög tillgänglighet eller distribuerat.
- Installera SAP ASCS/ERS och databas instansen.
- Gör en säkerhets kopia av den nyligen installerade databasen.


> [!IMPORTANT] 
> Skriv ner "databas kommunikations porten" som anges under installationen. Det måste vara samma port nummer för båda databas instanserna

Gör så här för att konfigurera vänte läges databas servern med hjälp av en SAP homogen system kopierings procedur:

1. Välj **system kopierings** alternativet > **mål systemets**  >  **distribuerade**  >  **databas instans**.
1. Som en kopierings metod väljer du **homogent system** så att du kan använda säkerhets kopiering för att återställa en säkerhets kopia på vänte läges Server instansen.
1. När du kommer till steget avsluta för att återställa databasen för homogen system kopia avslutar du installations programmet. Återställ databasen från en säkerhets kopia av den primära värden. Alla efterföljande installations faser har redan körts på den primära databas servern.
1. Konfigurera HADR för IBM DB2.

   > [!NOTE]
   > För installation och konfiguration som är specifika för Azure och pacemaker: under installations proceduren via SAP Software Provisioning Manager finns det en explicit fråga om hög tillgänglighet för IBM DB2 LUW:
   >+ Välj inte **IBM DB2 pureScale**.
   >+ Välj inte **Installera IBM Tivoli system Automation för multiplattformar**.
   >+ Välj inte skapa konfigurationsfiler för **kluster**.

   När du använder en SBD-enhet för Linux-pacemaker anger du följande DB2 HADR-parametrar:
   + Varaktighet för HADR-peer-fönstret (sekunder) (HADR_PEER_WINDOW) = 300  
   + Timeout-värde för HADR (HADR_TIMEOUT) = 60

   När du använder en pacemaker-agent för Azure-anger du följande parametrar:
   + Varaktighet för HADR-peer-fönstret (sekunder) (HADR_PEER_WINDOW) = 900  
   + Timeout-värde för HADR (HADR_TIMEOUT) = 60

Vi rekommenderar föregående parametrar baserat på inledande redundans/övertag ande testning. Det är obligatoriskt att du testar rätt funktioner för redundans och övertag ande med dessa parameter inställningar. Eftersom enskilda konfigurationer kan variera kan parametrarna kräva justeringar. 

> [!IMPORTANT]
> Särskilt för IBM DB2 med HADR-konfiguration med normal start: den sekundära databasen eller vänte läges instansen måste vara igång innan du kan starta den primära databas instansen.

I demonstrations syfte och de procedurer som beskrivs i den här artikeln är databasens SID **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Kontroll av IBM DB2-HADR
När du har konfigurerat HADR och statusen är PEER och ansluten på den primära och den vänte noden, utför du följande kontroll:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Konfiguration av DB2-pacemaker

När du använder pacemaker för automatisk redundansväxling i händelse av ett nodfel måste du konfigurera dina DB2-instanser och pacemaker. I det här avsnittet beskrivs den här typen av konfiguration.

Följande objekt föregås av antingen:

- **[A]**: gäller för alla noder
- **[1]**: gäller endast nod 1 
- **[2]**: gäller endast nod 2

**[A]** krav för pacemaker-konfiguration:
1. Stäng båda databas servrarna med User DB2 \<sid> med db2stop.
1. Ändra skal miljön för DB2- \<sid> användare till */bin/ksh*. Vi rekommenderar att du använder YaST-verktyget. 


### <a name="pacemaker-configuration"></a>Pacemaker-konfiguration

> [!IMPORTANT]
> De senaste testerna visade situationer, där netcat slutar svara på begär Anden på grund av en efter släpning och dess begränsning av hantering av endast en anslutning. Netcat-resursen slutar lyssna på Azure Load Balancer-begäranden och den flytande IP-adressen blir otillgänglig.  
> För befintliga pacemaker-kluster rekommenderar vi att du tidigare ersätter netcat med socat. För närvarande rekommenderar vi att du använder Azure-lb Resource agent, som är en del av paketets resurs agenter, med följande paket versions krav:
> - För SLES 12 SP4/SP5 måste versionen vara minst resurs agenter-4.3.018. a7fb5035-3.30.1.  
> - För SLES 15/15 SP1 måste versionen vara minst resurs agenter-4.3.0184.6 ee15eb2-4.13.1.  
>
> Observera att ändringen kräver kortare stillestånds tid.  
> För befintliga pacemaker-kluster, om konfigurationen redan har ändrats för att använda socat enligt beskrivningen i [azure Load-Balancer Avkännings härdning](https://www.suse.com/support/kb/doc/?id=7024128), finns det inget krav på att växla direkt till Azure-lb Resource agent.

**[1]** IBM DB2 hadr-Specific pacemaker-konfiguration:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** skapa IBM DB2-resurser:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** starta IBM DB2-resurser:
* Sätt pacemaker ur underhålls läge.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** kontrol lera att klustrets status är OK och att alla resurser har startats. Det är inte viktigt vilken nod resurserna körs på.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 konfigurerade noder
# <a name="5-resources-configured"></a>5 resurser har kon figurer ATS

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Fullständig lista över resurser:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-SBD (stonith: external/SBD): startade azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Resurs grupp: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (OCF:: pulsslag: IPaddr2): startade azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (OCF:: pulsslag: Azure-lb): startade azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Huvud-/slav uppsättning: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Mallsidor: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Slavar: [azibmdb01]
</pre>

> [!IMPORTANT]
> Du måste hantera den pacemaker klustrade DB2-instansen med hjälp av pacemaker-verktyg. Om du använder DB2-kommandon som db2stop, identifierar pacemaker åtgärden som ett resurs haveri. Om du utför underhåll kan du spara noderna eller resurserna i underhålls läge. Pacemaker pausar övervaknings resurser och du kan sedan använda vanliga administrations kommandon för DB2.


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
 
1. Logga in på den primära program servern för J2EE-instansen och kör:   `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. I den vänstra rutan väljer du **säkerhets lager**.
1. I den högra rutan väljer du nyckeln JDBC/pool/ \<SAPSID> /URL.
1. Ändra värd namnet i JDBC-URL: en till det virtuella värd namnet.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Välj **Lägg till**.
1. Om du vill spara ändringarna väljer du disk ikonen längst upp till vänster.
1. Stäng konfigurations verktyget.
1. Starta om Java-instansen.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurera logg arkivering för HADR-installation
Om du vill konfigurera DB2-HADR för installation av DB2 rekommenderar vi att du konfigurerar både den primära databasen och vänte läges databasen till automatisk logg hämtnings funktion från alla logg Arkiv platser. Både den primära databasen och standby-databasen måste kunna hämta loggfiler från alla logg Arkiv platser som antingen en av databas instanserna kan arkivera loggfiler. 

Logg arkiveringen utförs endast av den primära databasen. Om du ändrar HADR-rollerna för databas servrarna eller om ett fel inträffar är den nya primära databasen ansvarig för logg arkivering. Om du har konfigurerat flera logg Arkiv platser kan loggarna arkiveras två gånger. I händelse av lokal eller fjärran sluten fångst kan du också behöva kopiera arkiverade loggar manuellt från den gamla primära servern till den aktiva logg platsen för den nya primära servern.

Vi rekommenderar att du konfigurerar en gemensam NFS-resurs där loggar skrivs från båda noderna. NFS-resursen måste ha hög tillgänglighet. 

Du kan använda befintliga NFS-resurser med hög tillgänglighet för transporter eller en profil katalog. Mer information finns i:

- [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server][nfs-ha] 
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program](./high-availability-guide-suse-netapp-files.md)
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (för att skapa NFS-resurser)


## <a name="test-the-cluster-setup"></a>Testa kluster konfigurationen

I det här avsnittet beskrivs hur du kan testa installationen av DB2-HADR. *Varje test förutsätter att du är inloggad som användar rot* och att den primära IBM DB2-datorn körs på den virtuella *azibmdb01* -datorn.

Den inledande statusen för alla test fall förklaras här: (crm_mon-r-eller CRM-status)

- **CRM-status** är en ögonblicks bild av pacemaker status vid körnings tillfället 
- **crm_mon-r** är kontinuerlig utdata av pacemaker status

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Den ursprungliga statusen i ett SAP-system dokumenteras i transaktion DBACOCKPIT > konfiguration > översikt, som du ser i följande bild:

![DBACockpit – för migrering](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Testa övertag ande av IBM DB2


> [!IMPORTANT] 
> Innan du börjar testet måste du kontrol lera att:
> * Pacemaker har inte några misslyckade åtgärder (CRM-status).
> * Det finns inga plats begränsningar (rester av migrations test)
> * Synkroniseringen av IBM DB2-HADR fungerar. Kontrol lera med User DB2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrera noden som kör den primära DB2-databasen genom att köra följande kommando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

När migreringen är färdig ser CRM-status utdata ut så här:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Den ursprungliga statusen i ett SAP-system dokumenteras i transaktion DBACOCKPIT > konfiguration > översikt, som du ser i följande bild:

![DBACockpit-post-migrering](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Resursallokering med "CRM-resurs-migrering" skapar plats begränsningar. Plats begränsningar ska tas bort. Om plats begränsningar inte tas bort kan resursen inte återställas eller också kan du uppleva oönskade övertag Ande. 

Migrera tillbaka resursen till *azibmdb01* och avmarkera plats begränsningarna
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **CRM-resurs-migrering \<res_name> \<host> :** skapar plats begränsningar och kan orsaka problem med övertag Ande
- **resurs rensning \<res_name> i CRM**: rensar plats begränsningar
- **CRM-resurs \<res_name> rensning**: rensar alla fel i resursen

### <a name="test-the-fencing-agent"></a>Testa avgränsnings agenten

I det här fallet testar vi SBD staket, som vi rekommenderar när du använder SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Klusternodens *azibmdb01* måste startas om. Den primära HADR-rollen för IBM DB2 kommer att flyttas till *azibmdb02*. När *azibmdb01* är online igen, kommer DB2-instansen att flyttas i rollen för en sekundär databas instans. 

Om pacemaker-tjänsten inte startar automatiskt vid den omstartade tidigare primära enheten, måste du starta den manuellt med:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testa en manuell övertag Ande

Du kan testa en manuell övertag ande genom att stoppa pacemaker-tjänsten på *azibmdb01* -noden:
<pre><code>service pacemaker stop</code></pre>

status för *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Efter redundansväxlingen kan du starta tjänsten igen på *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Avsluta DB2-processen på den nod som kör den primära HADR-databasen

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

DB2-instansen kommer att Miss sen och pacemaker kommer att rapportera följande status:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker startar om den primära DB2-databas instansen på samma nod, eller växlar över till noden som kör den sekundära databas instansen och ett fel rapporteras.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Avsluta DB2-processen på noden som kör den sekundära databas instansen

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Noden får ett felaktigt angivet och rapporterat fel
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

DB2-instansen startas om i den sekundära rollen som den tilldelades tidigare.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Stoppa DB via db2stop-tvång på noden som kör den primära databas instansen HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Som user DB2 \<sid> execute kommandot db2stop Force:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Det gick inte att identifiera
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Den sekundära databas instansen för DB2-HADR har höjts till den primära rollen
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Virtuell dator i virtuell dator med omstart på noden som kör den primära databas instansen HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker kommer att befordra den sekundära instansen till den primära instans rollen. Den gamla primära instansen flyttas till den sekundära rollen när den virtuella datorn och alla tjänster är helt återställda efter den virtuella datorn:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Krascha den virtuella datorn som kör den primära databas instansen HADR med "stoppad"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

I sådana fall kommer pacemaker att identifiera att noden som kör den primära databas instansen inte svarar.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Nästa steg är att söka efter en *delad hjärna* . När den kvarvarande noden har fastställt att noden som senast körde den primära databas instansen inte är igång körs en redundansväxling av resurserna.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


Om noden är "stoppad" måste den felande noden startas om via Azures hanterings verktyg (i Azure Portal, PowerShell eller Azure CLI). När den felaktiga noden är online startar den DB2-instansen i den sekundära rollen.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Nästa steg
- [Arkitektur och scenarier med hög tillgänglighet för SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
- [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure](./high-availability-guide-suse-pacemaker.md)

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
