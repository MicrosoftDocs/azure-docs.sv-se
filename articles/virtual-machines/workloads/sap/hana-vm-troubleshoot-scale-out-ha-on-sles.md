---
title: SAP HANA skala ut HSR-Pacemaker med SLES på fel sökning av virtuella Azure-datorer | Microsoft Docs
description: Guide för att kontrol lera och felsöka en komplex SAP HANA konfiguration med hög tillgänglighet baserat på SAP HANA system replikering (HSR) och pacemaker på SLES 12 SP3 som körs på virtuella Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: f4c1de484ce2659a7e84a1546a7c49c1d77a7d56
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674490"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Verifiera och Felsök SAP HANA skalnings-och hög tillgänglighets installation på SLES 12 SP3 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Den här artikeln hjälper dig att kontrol lera kluster konfigurationen för pacemaker för SAP HANA skalbarhet som körs på virtuella Azure-datorer (VM: ar). Kluster konfigurationen genomfördes i kombination med SAP HANA System Replication (HSR) och SUSE RPM-paketet SAPHanaSR-Scale. Alla tester utfördes på SUSE SLES 12 SP3. Artikelns avsnitt omfattar olika områden och inkluderar exempel kommandon och utdrag från konfigurationsfiler. Vi rekommenderar dessa exempel som en metod för att verifiera och kontrol lera hela kluster konfigurationen.



## <a name="important-notes"></a>Viktiga meddelanden

Alla tester för SAP HANA utskalning i kombination med SAP HANA system replikering och pacemaker skedde med SAP HANA 2,0. Operativ system versionen var SUSE Linux Enterprise Server 12 SP3 för SAP-program. Det senaste RPM-paketet SAPHanaSR-ScaleOut från SUSE användes för att konfigurera pacemaker-klustret.
SUSE har publicerat en [detaljerad beskrivning av den här Prestandaoptimerad installationen][sles-hana-scale-out-ha-paper].

För typer av virtuella datorer som stöds för SAP HANA utskalning kontrollerar du [SAP HANA Certified IaaS-katalogen][sap-hana-iaas-list].

> [!NOTE]
> Den här artikeln innehåller referenser till villkors *huvud* och *slav*, och villkor som Microsoft inte längre använder. När de här villkoren tas bort från program varan tar vi bort dem från den här artikeln.

Det uppstod ett tekniskt problem med SAP HANA utskalning i kombination med flera undernät och virtuella nätverkskort och konfiguration av HSR. Det är obligatoriskt att använda de senaste SAP HANA 2,0-korrigeringarna där problemet har åtgärd ATS. Följande SAP HANA-versioner stöds: 

* rev 2.00.024.04 eller högre 
* rev 2.00.032 eller högre

Om du behöver support från SUSE följer du den här [hand boken][suse-pacemaker-support-log-files]. Samla in all information om SAP HANA hög tillgänglighet (HA)-klustret enligt beskrivningen i artikeln. SUSE-support behöver den här informationen för ytterligare analys.

Under intern testning har kluster konfigurationen förväxlats av en normal korrekt avstängning av virtuella datorer via Azure Portal. Vi rekommenderar därför att du testar en redundanskluster på andra sätt. Använd metoder som tvinga en kernel-panik eller Stäng av nätverken eller migrera **MSL** -resursen. Se informationen i följande avsnitt. Antagandet är att en vanlig avstängning sker med avsikt. Det bästa exemplet på en avsiktlig avstängning är för underhåll. Se information i [planerat underhåll](#planned-maintenance).

Under intern testning förväxlas även kluster installationen efter en manuell SAP HANA övertag medan klustret var i underhålls läge. Vi rekommenderar att du byter tillbaka den igen manuellt innan du slutar klustrets underhålls läge. Ett annat alternativ är att utlösa redundans innan du försätter klustret i underhålls läge. Mer information finns i [planerat underhåll](#planned-maintenance). Dokumentationen från SUSE beskriver hur du kan återställa klustret på det här sättet med hjälp av **CRM** -kommandot. Men den metod som nämnts tidigare var robust under intern testning och visade aldrig några oväntade sido effekter.

När du använder kommandot **CRM Migrate** , se till att rensa kluster konfigurationen. Den lägger till plats begränsningar som du kanske inte känner till. Dessa begränsningar påverkar kluster beteendet. Se mer information i [planerat underhåll](#planned-maintenance).



## <a name="test-system-description"></a>Beskrivning av test system

 För SAP HANA Scale-Out-verifiering och certifiering, användes en installation. Den består av två system med tre SAP HANA noder vardera: en huvud server och två arbets tagare. I följande tabell visas VM-namn och interna IP-adresser. Alla verifierings exempel som följer har utförts på de här virtuella datorerna. Genom att använda dessa VM-namn och IP-adresser i kommando exemplen kan du bättre förstå kommandona och deras utdata:


| Nodtyp | VM-namn | IP-adress |
| --- | --- | --- |
| Huvud nod på plats 1 | HSO-Hana-VM-S1-0 | 10.0.0.30 |
| Arbetsnoden 1 på plats 1 | HSO-Hana-VM-S1-1 | 10.0.0.31 |
| Arbetsnoden 2 på plats 1 | HSO-Hana-VM-S1-2 | 10.0.0.32 |
| | | |
| Huvud nod på plats 2 | HSO-Hana-VM-S2-0 | 10.0.0.40 |
| Arbetsnoden 1 på plats 2 | HSO-Hana-VM-S2-1 | 10.0.0.41 |
| Arbetsnoden 2 på plats 2 | HSO-Hana-VM-S2-2  | 10.0.0.42 |
| | | |
| Majoritets Maker-nod | HSO-Hana-DM | 10.0.0.13 |
| SBD enhets Server | HSO-Hana-SBD | 10.0.0.19 |
| | | |
| NFS-server 1 | HSO-NFS-VM-0 | 10.0.0.15 |
| NFS-server 2 | HSO-NFS-VM-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Flera undernät och virtuella nätverkskort

Följande SAP HANA nätverks rekommendationer skapades tre undernät i ett virtuellt Azure-nätverk. SAP HANA skala ut på Azure måste installeras i ett ej delat läge. Det innebär att varje nod använder lokala disk volymer för **/Hana/data** och **/Hana/log**. Eftersom noderna endast använder lokala disk volymer, är det inte nödvändigt att definiera ett separat undernät för lagring:

- 10.0.2.0/24 för SAP HANA kommunikation mellan noder
- 10.0.1.0/24 för SAP HANA System Replication (HSR)
- 10.0.0.0/24 för allt annat

Information om SAP HANA konfiguration som är relaterad till att använda flera nätverk finns i [SAP HANA global.ini](#sap-hana-globalini).

Varje virtuell dator i klustret har tre virtuella nätverkskort som motsvarar antalet undernät. [Hur du skapar en virtuell Linux-dator i Azure med flera nätverkskort som][azure-linux-multiple-nics] beskriver ett möjligt problem med routning i Azure när du distribuerar en virtuell Linux-dator. Den här artikeln gäller endast för användning av flera virtuella nätverkskort. Problemet löses med SUSE per standard i SLES 12 SP3. Mer information finns i [multi-NIC med Cloud-netconfig i EC2 och Azure][suse-cloud-netconfig].


Kontrol lera att SAP HANA har kon figurer ATS korrekt för att använda flera nätverk genom att köra följande kommandon. Börja med att kontrol lera operativ Systems nivån att alla tre interna IP-adresser för alla tre undernät är aktiva. Om du har definierat undernät med olika IP-adressintervall måste du anpassa kommandona:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Följande exempel på utdata kommer från den andra Worker-noden på plats 2. Du kan se tre olika interna IP-adresser från eth0, eth1 och ETH2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Kontrol lera sedan SAP HANA portar för namnserver-och HSR. SAP HANA bör lyssna på motsvarande undernät. Beroende på SAP HANA instans numret måste du anpassa kommandona. För test systemet var instans numret **00**. Det finns olika sätt att ta reda på vilka portar som används. 

Följande SQL-uttryck returnerar instans-ID, instans nummer och annan information:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Om du vill hitta rätt port nummer kan du se, till exempel, i HANA Studio under **konfigurationen** eller via ett SQL-uttryck:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Om du vill hitta alla portar som används i SAP-programs Tacken, inklusive SAP HANA, söker du igenom [TCP/IP-portarna för alla SAP-produkter][sap-list-port-numbers].

Med instans nummer **00** i test systemet SAP HANA 2,0 är port numret för namn servern **30001**. Port numret för HSR metadata-kommunikation är **40002**. Ett alternativ är att logga in på en arbetsnoden och kontrol lera huvud Node-tjänsterna. I den här artikeln har vi kontrollerat Work Node 2 på plats 2 som försöker ansluta till huvud-noden på plats 2.

Kontrol lera namnet på Server porten:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

För att bevisa att kommunikationen mellan noder använder undernät **10.0.2.0/24**, bör resultatet se ut som i följande exempel på utdata.
Endast anslutningen via undernät **10.0.2.0/24** ska lyckas:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Sök nu efter HSR-port **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

För att bevisa att HSR-kommunikationen använder undernät **10.0.1.0/24** bör resultatet se ut som i följande exempel på utdata.
Endast anslutningen via undernät **10.0.1.0/24** ska lyckas:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


Konfigurations filen för **corosync** måste vara korrekt på varje nod i klustret, inklusive majoritetsnoduppsättning-noden. Om klustrets koppling till en nod inte fungerar som förväntat skapar du eller kopierar **/etc/corosync/corosync.conf** manuellt till alla noder och startar om tjänsten. 

Innehållet i **corosync. conf** från test systemet är ett exempel.

Det första avsnittet är **totem**, som beskrivs i [kluster installationen](./high-availability-guide-suse-pacemaker.md#cluster-installation), steg 11. Du kan ignorera värdet för **mcastaddr**. Behåll bara den befintliga posten. Posterna för **token** och **konsensus** måste anges i enlighet med [Microsoft Azure SAP HANA-dokumentationen][sles-pacemaker-ha-guide].

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

Det andra avsnittet, **loggning**, ändrades inte från de aktuella standardvärdena:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

I det tredje avsnittet visas **NodeList**. Alla noder i klustret måste visas med sina **nodeID**:

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

I det sista avsnittet, **kvorum** är det viktigt att ange värdet för **expected_votes** korrekt. Det måste vara antalet noder, inklusive majoritetsnoduppsättning-noden. Och värdet för **two_node** måste vara **0**. Ta inte bort posten helt. Ställ bara in värdet på **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Starta om tjänsten via **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD-enhet

Hur du konfigurerar en SBD-enhet på en virtuell Azure-dator beskrivs i [SBD-staket](./high-availability-guide-suse-pacemaker.md#sbd-fencing).

Börja med att kontrol lera den virtuella SBD-serverdatorn om det finns ACL-poster för varje nod i klustret. Kör följande kommando på den virtuella SBD-servern:


<pre><code>
targetcli ls
</code></pre>


I test systemet ser kommandots utdata ut som i följande exempel. ACL-namn som **IQN. 2006-04. HSO-dB-0. local: HSO-dB-0** måste anges som motsvarande namn på initierare på de virtuella datorerna. Varje virtuell dator behöver en annan.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Kontrol lera sedan att initierar namnen på alla virtuella datorer är olika och motsvarar de poster som visades tidigare. Det här exemplet är från arbetsnoden 1 på plats 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

Utdata ser ut som i följande exempel:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Kontrol lera sedan att **identifieringen** fungerar korrekt. Kör följande kommando på varje klusternod med IP-adressen för den virtuella SBD-servern:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

Utdata bör se ut som i följande exempel:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Nästa korrektur punkt är att kontrol lera att noden ser SDB-enheten. Kontrol lera det på varje nod, inklusive majoritetsnoduppsättning-noden:

<pre><code>
lsscsi | grep dbhso
</code></pre>

Utdata bör se ut som i följande exempel. Namnen kan dock skilja sig åt. Enhets namnet kan också ändras efter att den virtuella datorn har startats om:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

Beroende på systemets status hjälper det ibland att starta om iSCSI-tjänsterna för att lösa problem. Kör sedan följande kommandon:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Från valfri nod kan du kontrol lera om alla noder är **tydliga**. Kontrol lera att du använder rätt enhets namn på en speciell nod:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Resultatet bör vara **klart** för varje nod i klustret:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


En annan SBD kontroll är alternativet **dump** i **SBD** -kommandot. I det här exempel kommandot och utdata från majoritetsnoduppsättning-noden var enhets namnet **SDD**, inte **SDM**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Utdata, förutom enhets namnet, bör se likadant ut på alla noder:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

En mer kontroll för SBD är möjligheten att skicka ett meddelande till en annan nod. Om du vill skicka ett meddelande till Work Node 2 på plats 2 kör du följande kommando på arbetsnoden 1 på plats 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

På den virtuella mål datorn, **HSO-Hana-VM-S2-2** i det här exemplet kan du hitta följande post i **/var/log/Messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Kontrol lera att posterna i **/etc/sysconfig/SBD** motsvarar beskrivningen i ställa in [pacemaker på SUSE Linux Enterprise Server i Azure](./high-availability-guide-suse-pacemaker.md#sbd-fencing). Kontrol lera att start inställningen i **/etc/iSCSI/iscsid.conf** är inställd på automatisk.

Följande poster är viktiga i **/etc/sysconfig/SBD**. Anpassa **ID-** värdet vid behov:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Kontrol lera start inställningen i **/etc/iSCSI/iscsid.conf**. Den obligatoriska inställningen ska ha hänt med följande **iscsiadm** -kommando, som beskrivs i dokumentationen. Kontrol lera och anpassa den manuellt med **vi** om den är annorlunda.

Det här kommandot anger start beteendet:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Gör den här posten i **/etc/iSCSI/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Vid testning och verifiering, efter omstart av en virtuell dator, var SBD-enheten inte längre synlig i vissa fall. Det uppstod en avvikelse mellan start inställningen och vilken YaST2 som visades. Gör så här för att kontrol lera inställningarna:

1. Starta YaST2.
2. Välj **Network Services** till vänster.
3. Rulla ned till höger till iSCSI- **initieraren** och markera den.
4. På nästa skärm under fliken **tjänst** visas det unika initierar-namnet för noden.
5. Ovanför initierarens namn ser du till att **tjänstens start** värde är inställt på **vid start**.
6. Om den inte är det anger du den till **när du startar** i stället för **manuellt**.
7. Sedan växlar du den översta fliken till **anslutna mål**.
8. På skärmen **anslutna mål** bör du se en post för SBD-enheten som det här exemplet: **10.0.0.19:3260 IQN. 2006-04. dbhso. local: dbhso**.
9. Kontrol lera om **Start-** värdet är inställt **på vid start**.
10. Annars väljer du **Redigera** och ändrar det.
11. Spara ändringarna och avsluta YaST2.



## <a name="pacemaker"></a>Pacemaker

När allt är korrekt konfigurerat kan du köra följande kommando på varje nod för att kontrol lera statusen för pacemaker-tjänsten:

<pre><code>
systemctl status pacemaker
</code></pre>

Den översta delen av utdata bör se ut som i följande exempel. Det är viktigt att statusen efter **aktiv** visas som **inläst** och **aktiv (körs)**. Status efter **inläsning** måste visas som **aktive rad**.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Om inställningen fortfarande är **inaktive rad** kör du följande kommando:

<pre><code>
systemctl enable pacemaker
</code></pre>

Om du vill se alla konfigurerade resurser i pacemaker kör du följande kommando:

<pre><code>
crm status
</code></pre>

Utdata bör se ut som i följande exempel. Det är bra att **CLN** -och **MSL** -resurserna visas som stoppad i den virtuella datorns virtuella dator, **HSO-Hana-DM**. Det finns ingen SAP HANA installation på den flesta Maker-noden. Så att **CLN** -och **MSL** -resurserna visas som stoppade. Det är viktigt att det visar rätt totala antal virtuella datorer, **7**. Alla virtuella datorer som ingår i klustret måste anges med statusen **online**. Den aktuella primära huvud-noden måste identifieras korrekt. I det här exemplet är det **HSO-Hana-VM-S1-0**:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

En viktig funktion i pacemaker är underhålls läge. I det här läget kan du göra ändringar utan att anropa en omedelbar kluster åtgärd. Ett exempel är en VM-omstart. Ett vanligt användnings fall skulle vara planerat med underhåll av operativ system eller Azure-infrastruktur. Se [planerat underhåll](#planned-maintenance). Använd följande kommando för att sätt pacemaker i underhålls läge:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

När du kontrollerar med **CRM-status** visas ett meddelande i utdata om att alla resurser är markerade som **ohanterade**. I det här läget reagerar inte klustret på några ändringar som att starta eller stoppa SAP HANA.
Följande exempel visar utdata från kommandot CRM- **status** när klustret är i underhålls läge:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


Det här kommando exemplet visar hur du slutför klustrets underhålls läge:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Ett annat **CRM** -kommando hämtar den fullständiga kluster konfigurationen till en redigerare, så att du kan redigera den. När ändringarna har sparats startar klustret lämpliga åtgärder:

<pre><code>
crm configure edit
</code></pre>

Använd **CRM show** -alternativet för att titta på den fullständiga kluster konfigurationen:

<pre><code>
crm configure show
</code></pre>



Efter fel i kluster resurser visar CRM- **status** kommandot en lista över **misslyckade åtgärder**. Se följande exempel på följande utdata:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

Du måste göra en kluster rensning efter fel. Använd **CRM** -kommandot igen och Använd kommando alternativet **Rensa** för att ta bort dessa misslyckade åtgärds poster. Namnge motsvarande kluster resurs på följande sätt:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Kommandot ska returnera utdata som följande exempel:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Redundans eller övertag Ande

Som det beskrivs i [viktiga anteckningar](#important-notes)bör du inte använda en vanlig standard avstängning för att testa kluster växling vid fel eller SAP HANA HSR övertag. I stället rekommenderar vi att du utlöser en kernel-panik, tvingar fram en resurstilldelning eller kan stänga av alla nätverk på OS-nivån för en virtuell dator. En annan metod är **i \<node\> standby** -kommandot för CRM. Se [SUSE-dokumentet][sles-12-ha-paper]. 

Följande tre exempel kommandon kan framtvinga en redundanskluster:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Som det beskrivs i [planerat underhåll](#planned-maintenance)är ett bra sätt att övervaka kluster aktiviteterna att köra **SAPHanaSR-showAttr** med **Watch** -kommandot:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Det hjälper också att titta på den SAP HANA landskaps status som kommer från ett SAP Python-skript. Kluster konfigurationen söker efter detta status värde. Det blir tydligt när du tycker att det är ett problem med en arbetsnod. Om en arbetsnoden slutar fungera returnerar SAP HANA omedelbart ett fel för hela det skalbara systemets hälso tillstånd. 

Det finns vissa försök att undvika onödig redundans. Klustret agerar bara om status ändras från **OK**, returnera värde **4**, till **fel**, returnera värde **1**. Så det är korrekt om utdata från **SAPHanaSR-showAttr** visar en virtuell dator med statusen **offline**. Men det finns ingen aktivitet som ännu inte kan växla mellan primär och sekundär. Ingen kluster aktivitet utlöses så länge SAP HANA inte returnerar ett fel.

Du kan övervaka SAP HANA landskaps hälso status som användar- **\<HANA SID\> ADM** genom att anropa SAP python-skriptet enligt följande. Du kan behöva anpassa sökvägen:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Utdata från det här kommandot bör se ut som i följande exempel. Kolumnen **värd status** och den **övergripande värd statusen** är både viktiga. Faktiska utdata är bredare, med ytterligare kolumner.
För att det ska gå att läsa tabellen i det här dokumentet har de flesta kolumner på den högra sidan tagits bort:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Det finns ett annat kommando för att kontrol lera aktuella kluster aktiviteter. Se följande kommando och utdata-änden efter att huvud noden för den primära platsen har stoppats. Du kan se listan över över gångs åtgärder som att **befordra** den tidigare sekundära huvudnoden, **HSO-Hana-VM-S2-0**, som den nya primära huvud servern. Om allt är bra och alla aktiviteter är klara måste den här **över gångs sammanfattnings** listan vara tom.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Planerat underhåll 

Det finns olika användnings fall när det kommer till planerat underhåll. En fråga är om det är bara infrastruktur underhåll som ändringar på operativ Systems nivån och disk konfigurationen eller en HANA-uppgradering.
Du hittar mer information i dokument från SUSEt, t. ex. [noll stillestånd][sles-zero-downtime-paper] eller [SAP HANA optimalt prestanda optimerings scenario][sles-12-for-sap]. Dessa dokument innehåller också exempel som visar hur du migrerar en primär enhet manuellt.

Intensiv intern testning utfördes för att verifiera användnings fallet för infrastruktur underhåll. För att undvika problem som rör migrering av den primära, bestämde vi att alltid migrera en primär innan ett kluster placeras i underhålls läge. På så sätt behöver du inte göra klustret glömmt om den tidigare situationen: vilken sida som var primär och som var sekundär.

Det finns två olika situationer i detta avseende:

- **Planerat underhåll av den aktuella sekundära**. I det här fallet kan du bara försätta klustret i underhålls läge och göra arbetet på den sekundära utan att påverka klustret.

- **Planerat underhåll på den aktuella primära servern**. För att användarna ska kunna fortsätta arbeta under underhållet måste du framtvinga en redundansväxling. Med den här metoden måste du utlösa klustrets redundans av pacemaker och inte bara på SAP HANA HSR-nivån. Pacemaker-installationen utlöser automatiskt SAP HANA övertag Ande. Du måste också utföra redundansväxlingen innan du försätter klustret i underhålls läge.

Proceduren för underhåll på den aktuella sekundära platsen är följande:

1. Sätt klustret i underhålls läge.
2. Utför arbetet på den sekundära platsen. 
3. Avsluta klustrets underhålls läge.

Proceduren för underhåll på den aktuella primära platsen är mer komplex:

1. Utlösa en redundansväxling manuellt eller SAP HANA uppköpsen via en pacemaker resurs-migrering. Se information som följer.
2. SAP HANA på den tidigare primära platsen stängs av kluster konfigurationen.
3. Sätt klustret i underhålls läge.
4. När underhålls arbetet är slutfört registrerar du den tidigare primära platsen som den nya sekundära platsen.
5. Rensa kluster konfigurationen. Se information som följer.
6. Avsluta klustrets underhålls läge.


När du migrerar en resurs läggs posten till i kluster konfigurationen. Ett exempel som tvingar fram en redundansväxling. Du måste rensa dessa poster innan du slutar underhålls läge. Se följande exempel.

Börja med att framtvinga en redundanskluster genom att migrera **MSL** -resursen till den aktuella sekundära huvud noden. Det här kommandot ger en varning om att ett **flytt villkor** har skapats:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Kontrol lera redundansväxlingen via kommandot **SAPHanaSR-showAttr**. Om du vill övervaka kluster statusen öppnar du ett dedikerat Shell-fönster och startar kommandot med **Se**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Utdata bör visa manuell redundans. Den tidigare sekundära huvudnoden har **uppgraderats**, i det här exemplet **HSO-Hana-VM-S2-0**. Den tidigare primära platsen stoppades, **LSS** värde **1** för den tidigare primära huvudnoden **HSO-Hana-VM-S1-0**: 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

När klustret har redundans och SAP HANA övertag ska klustret försättas i underhålls läge enligt beskrivningen i [pacemaker](#pacemaker).

**SAPHanaSR-showAttr** och CRM- **status** indikerar inte något om begränsningarna som skapats av resursallokeringen. Ett alternativ för att göra dessa begränsningar synliga är att visa den fullständiga kluster resurs konfigurationen med följande kommando:

<pre><code>
crm configure show
</code></pre>

I kluster konfigurationen hittar du en ny plats begränsning som orsakas av den tidigare manuella resursallokeringen. Den här exempel posten börjar med **plats-CLI-**:

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Sådana begränsningar kan tyvärr påverka det övergripande kluster beteendet. Det är därför obligatoriskt att ta bort dem igen innan du tar hela system säkerhets kopieringen. Med kommandot Ta bort **migrering** är det möjligt att rensa de plats begränsningar som skapades tidigare. Namngivningen kan vara lite förvirrande. Det försöker inte att migrera resursen tillbaka till den ursprungliga virtuella dator som den migrerades från. Det tar bara bort plats begränsningarna och returnerar även motsvarande information när du kör kommandot:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

I slutet av underhålls arbetet stoppar du klustrets underhålls läge som visas i [pacemaker](#pacemaker).



## <a name="hb_report-to-collect-log-files"></a>hb_report att samla in loggfiler

För att analysera problem med pacemaker-kluster är det till hjälp och kan även begäras av SUSE support för att köra **hb_report** -verktyget. Den samlar in alla viktiga loggfiler som du behöver för att analysera vad som hände. Det här exempel anropet använder en start-och slut tid där en angiven incident inträffade. Se även [viktiga anteckningar](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Kommandot visar var de komprimerade loggfilerna ska läggas till:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Du kan sedan extrahera de enskilda filerna via kommandot standard **tar** :

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

När du tittar på de extraherade filerna hittar du alla loggfiler. De flesta av dem lades i separata kataloger för varje nod i klustret:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


Inom det angivna tidsintervallet har den aktuella huvudnoden **HSO-Hana-VM-S1-0** stoppats. Du hittar poster relaterade till den här händelsen i **journal. log**:

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Ett annat exempel är logg filen pacemaker på den sekundära huvud servern, som blev den nya primära huvud servern. Detta utdrag visar att statusen för den stoppade primära huvudnoden har angetts till **offline**:

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


Följande utdrag är från SAP HANA **global.ini** -filen på kluster plats 2. I det här exemplet visas matchnings poster för värdnamn för användning av olika nätverk för SAP HANA kommunikation mellan noder och HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

Kluster lösningen tillhandahåller ett webb läsar gränssnitt som ger ett användar gränssnitt för användare som föredrar menyer och grafik för att få alla kommandon på Shell-nivå.
Om du vill använda webb läsar gränssnittet ersätter du **\<node\>** med en faktisk SAP HANA-nod i följande URL. Ange sedan autentiseringsuppgifterna för klustret (användar **kluster**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Den här skärm bilden visar kluster instrument panelen:


![Instrument panel för Hawk-kluster](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Det här exemplet visar de plats begränsningar som orsakas av en migrering av en kluster resurs som förklaras i [planerat underhåll](#planned-maintenance):


![Begränsningar för Hawk-lista](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Du kan också ladda upp **hb_report** utdata i Hawk under **Historik**, som visas på följande sätt. Se hb_report för att samla in loggfiler: 

![Hawk Ladda upp hb_report utdata](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Med **Historik Utforskaren** kan du gå igenom alla kluster över gångar som ingår i **hb_report** utdata:

![Hawk-över gångar i hb_report utdata](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Den sista skärm bilden visar **detalj** avsnittet i en enda över gång. Klustret reagerar på en primär huvudnods krasch, Node **HSO-Hana-VM-S1-0**. Nu marknadsförs den sekundära noden som den nya Master- **HSO-Hana-VM-S2-0**:

![Hawk enskild över gång](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Nästa steg

Den här fel söknings guiden beskriver hög tillgänglighet för SAP HANA i en skalbar konfiguration. Förutom-databasen är en annan viktig komponent i ett SAP-landskap den NetWeaver stacken. Lär dig mer om [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer som använder SUSE Enterprise Linux-server][sap-nw-ha-guide-sles].
