---
title: Hög tillgänglighet för Azure Large Instances för SAP på RHEL
description: Lär dig hur du automatiserar en SAP HANA databas redundans med hjälp av ett pacemakerkluster i Red Hat Enterprise Linux.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-sap
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: f7b6e6efbbd17655b4f68d79ac26ee34ae754a3b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728454"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>Hög tillgänglighet för Azure Large Instances för SAP på RHEL

> [!NOTE]
> Den här artikeln innehåller referenser till termen *svartlista*, en term som Microsoft inte längre använder. När den här termen tas bort från programvaran tar vi bort den från den här artikeln.

I den här artikeln får du lära dig hur du konfigurerar pacemakerklustret i RHEL 7.6 för att automatisera en SAP HANA databas redundans. Du måste ha en god förståelse för Linux, SAP HANA och Pacemaker för att slutföra stegen i den här guiden.

Följande tabell innehåller de värdnamn som används i den här artikeln. Kodblocken i artikeln visar de kommandon som måste köras, samt utdata från dessa kommandon. Var noga med vilken nod som refereras i varje kommando.

| Typ | Värdnamn | Nod|
|-------|-------------|------|
|Primär värd|`sollabdsm35`|nod 1|
|Sekundär värd|`sollabdsm36`|nod 2|

## <a name="configure-your-pacemaker-cluster"></a>Konfigurera pacemakerklustret


Innan du kan börja konfigurera klustret konfigurerar du SSH-nyckelutbyte för att upprätta förtroende mellan noder.

1. Använd följande kommandon för att skapa identiska `/etc/hosts` på båda noderna.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    10.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    10.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    10.20.251.150 sollabdsm36-st
    10.20.251.151 sollabdsm35-st
    10.20.252.151 sollabdsm36-back
    10.20.252.150 sollabdsm35-back
    10.20.253.151 sollabdsm36-node
    10.20.253.150 sollabdsm35-node
    ```

2.  Skapa och byt ut SSH-nycklarna.
    1. Generera ssh-nycklar.

    ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
    ```
    2. Kopiera nycklar till de andra värdarna för lösenordslös ssh.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  Inaktivera selinux på båda noderna.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. Starta om servrarna och använd sedan följande kommando för att verifiera statusen för selinux.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. Konfigurera NTP (Network Time Protocol). Tids- och tidszonerna för båda klusternoderna måste matcha. Använd följande kommando för att `chrony.conf` öppna och verifiera innehållet i filen.
    1. Följande innehåll ska läggas till i konfigurationsfilen. Ändra de faktiska värdena enligt din miljö.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. Aktivera kronologisk tjänst. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. Uppdatera systemet
    1. Installera först de senaste uppdateringarna på systemet innan du börjar installera SBD-enheten.
    1. Om du inte vill ha en fullständig uppdatering av systemet, även om det rekommenderas, bör du minst uppdatera följande paket.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```

7. Installera lagringsplatsen SAP HANA RHEL-HA.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. Installera pacemaker, SBD, OpenIPMI, ipmitool och fencing_sbd på alla noder.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitool
    ```

  ## <a name="configure-watchdog"></a>Konfigurera Watchdog

I det här avsnittet får du lära dig hur du konfigurerar Watchdog. I det här avsnittet används samma två värdar, `sollabdsm35` `sollabdsm36` och , som refereras till i början av den här artikeln.

1. Kontrollera att watchdog-daemonen inte körs på några system.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. Standardinställningen för Linux Watchdog, som installeras under installationen, är iTCO watchdog som inte stöds av UCS- och HPE SDFlex-system. Därför måste den här watchdogen inaktiveras.
    1. Fel watchdog installeras och läses in i systemet:
       ```
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. Ta bort fel drivrutin från miljön:
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. Drivrutinen måste vara blockerad för att se till att drivrutinen inte läses in vid nästa systemstart. Om du vill blockera iTCO-modulerna lägger du till följande i slutet av `50-blacklist.conf` filen:
       ```
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. Kopiera filen till den sekundära värden.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. Testa om IPMI-tjänsten har startats. Det är viktigt att IPMI-timern inte körs. Timerhanteringen utförs från SBD-pacemakertjänsten.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. Som standard är den enhet som krävs /dev/watchdog kommer inte att skapas.

    ```
    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. Konfigurera IPMI watchdog.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. Kopiera watchdog-konfigurationsfilen till den sekundära.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  Aktivera och starta ipmi-tjänsten.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     Nu har IPMI-tjänsten startats och enheten /dev/watchdog har skapats – men timern är fortfarande stoppad. Senare hanterar SBD watchdog-återställningen och aktiverar IPMI-timern.
7.  Kontrollera att /dev/watchdog finns men inte används.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>SBD-konfiguration
I det här avsnittet får du lära dig hur du konfigurerar SBD. I det här avsnittet används samma två värdar, `sollabdsm35` `sollabdsm36` och , som refereras till i början av den här artikeln.

1.  Kontrollera att iSCSI- eller FC-disken är synlig på båda noderna. I det här exemplet används en FC-baserad SBD-enhet. Mer information om SBD-avstängning finns i [Designvägledning för RHEL-kluster med hög tillgänglighet – SBD-överväganden.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Faccess.redhat.com%2Farticles%2F2941601&data=04%7C01%7Cralf.klahr%40microsoft.com%7Cd49d7a3e3871449cdecc08d8c77341f1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637478645171139432%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=c%2BUAC5gmgpFNWZCQFfiqcik8CH%2BmhH2ly5DsOV1%2FE5M%3D&reserved=0)
2.  LUN-ID:t måste vara identiskt på alla noder.
  
3.  Kontrollera multipath-status för sbd-enheten.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  Skapa SBD-diskarna och konfigurera klustrets primitiva avstängning. Det här steget måste köras på den första noden.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  Kopiera SBD-konfiguration till node2.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  Kontrollera att SBD-disken visas från båda noderna.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  Lägg till SBD-enheten i SBD-konfigurationsfilen.

    ```
    # SBD_DEVICE specifies the devices to use for exchanging sbd messages
    # and to monitor. If specifying more than one path, use ";" as
    # separator.
    #

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    ## Type: yesno
     Default: yes
     # Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>Kluster initiering
I det här avsnittet initierar du klustret. I det här avsnittet används samma två värdar, `sollabdsm35` `sollabdsm36` och , som refereras till i början av den här artikeln.

1.  Konfigurera klusteranvändarlösenordet (alla noder).
    ```
    passwd hacluster
    ```
2.  Starta PCS på alla system.
    ```
    systemctl enable pcsd
    ```
  

3.  Stoppa brandväggen och inaktivera den på (alla noder).
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Starta pcsd-tjänsten.
    ```
    systemctl start pcsd
    ```

5.  Kör endast klusterautentisering från node1.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36

        Username: hacluster

            Password:
            sollabdsm35.localdomain: Authorized
            sollabdsm36.localdomain: Authorized

     ``` 

6.  Skapa klustret.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  Kontrollera klusterstatusen.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. Om en nod inte ansluter till klustret kontrollerar du om brandväggen fortfarande körs.

9. Skapa och aktivera SBD-enheten
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  
10. Stoppa klustret och starta om klustertjänsterna (på alla noder).

    ```
    pcs cluster stop --all
    ```

11. Starta om klustertjänsterna (på alla noder).

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. Sync måste starta SBD-tjänsten.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. Starta om klustret (om det inte har startats automatiskt från pcsd).

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Aktivera Stonith-inställningar.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. Kontrollera den nya klusterstatusen med nu en resurs.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. Nu måste IPMI-timern köras och /dev/watchdog-enheten måste öppnas av sbd.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm35 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. Kontrollera SBD-status.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. Testa SBD-avstängningen genom att krascha kerneln.

    * Utlösa kernelkrasch.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * Det andra testet att köra är att stängs av en nod med hjälp av PCS-kommandon.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. För resten av SAP HANA kan du inaktivera STONITH genom att ange:

   * pcs-egenskapsuppsättning `stonith-enabled=false`
   * Det är ibland enklare att hålla STONITH inaktiverat under installationen av klustret, eftersom du undviker oväntade omstarter av systemet.
   * Den här parametern måste anges till true för produktiv användning. Om den här parametern inte är inställd på true stöds inte klustret.
   * pcs-egenskapsuppsättning `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>HANA-integrering i klustret

I det här avsnittet integrerar du HANA i klustret. I det här avsnittet används samma två värdar, `sollabdsm35` `sollabdsm36` och , som refereras till i början av den här artikeln.

Det finns två alternativ för att integrera HANA. Det första alternativet är en kostnadsoptimerad lösning där du kan använda det sekundära systemet för att köra QAS-systemet. Vi rekommenderar inte den här metoden eftersom det inte lämnar något system för att testa uppdateringar av klusterprogramvaran, operativsystemet eller HANA, och konfigurationsuppdateringar kan leda till oplanerade driftstopp för PRD-systemet. Om PRD-systemet måste aktiveras på det sekundära systemet måste QAS dessutom stängas av på den sekundära noden. Det andra alternativet är att installera QAS-systemet på ett kluster och använda ett andra kluster för PRD. Med det här alternativet kan du också testa alla komponenter innan de förs in i produktion. Den här artikeln visar hur du konfigurerar det andra alternativet.


* Den här processen bygger på RHEL-beskrivningen på sidan:

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>Steg att följa för att konfigurera HSR

1.  Det här är de åtgärder som ska köras på node1 (primär).
    1. Kontrollera att databasloggläget är inställt på normalt.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p $YourPass -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. SAP HANA systemreplikering fungerar bara när den inledande säkerhetskopieringen har utförts. Följande kommando skapar en första säkerhetskopia i `/tmp/` katalogen . Välj ett korrekt säkerhetskopieringsfilsystem för databasen. 
       ```
       * hdbsql -i 00 -u system -p $YourPass "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```  

    3. Säkerhetskopiera alla databascontainrar i den här databasen.
       ``` 
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"     
   
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. Aktivera HSR-processen i källsystemet.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. Kontrollera status för det primära systemet.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. Det här är de åtgärder som ska köras på node2 (sekundär).
     1. Stoppa databasen.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. Endast för SAP HANA2.0 kopierar du SAP HANA och filer från `PKI SSFS_HR2.KEY` `SSFS_HR2.DAT` den primära noden till den sekundära noden.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. Aktivera sekundär som replikeringsplats.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. Starta databasen.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. Kontrollera databastillståndet.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. Du kan också få mer information om replikeringsstatusen:
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>Beskrivning av loggreplikeringsläge

Mer information om loggreplikeringsläget finns i den officiella [SAP-dokumentationen.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/627bd11e86c84ec2b9fcdf585d24011c.html)
  

#### <a name="network-setup-for-hana-system-replication"></a>Nätverkskonfiguration för HANA-systemreplikering


För att säkerställa att replikeringstrafiken använder rätt VLAN för replikeringen måste det konfigureras korrekt i `global.ini` . Om du hoppar över det här steget använder HANA access VLAN för replikeringen, som kan vara oönskad.


I följande exempel visas konfigurationen för värdnamnmatchning för systemreplikering till en sekundär plats. Tre olika nätverk kan identifieras:

* Offentligt nätverk med adresser i intervallet 10.0.1.*

* Nätverk för intern SAP HANA mellan värdar på varje plats: 192.168.1.*

* Dedikerat nätverk för systemreplikering: 10.5.1.*

I det första exemplet har `[system_replication_communication]listeninterface` parametern angetts till och `.global` endast värdarna för den närliggande replikeringsplatsen anges.

I följande exempel har `[system_replication_communication]listeninterface` parametern angetts till och `.internal` alla värdar för båda platserna har angetts.

  

Mer information finns i [Nätverkskonfiguration för SAP HANA systemreplikering.](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)

  

För systemreplikering är det inte nödvändigt att redigera filen, interna (virtuella) värdnamn måste mappas till IP-adresser i filen för att skapa ett dedikerat nätverk för `/etc/hosts` `global.ini` systemreplikering. Syntaxen för detta är följande:

global.ini

[system_replication_hostname_resolution]

<ip-address_site>=<internal-host-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Konfigurera SAP HANA i ett Pacemaker-kluster
I det här avsnittet får du lära dig hur du konfigurerar SAP HANA i ett Pacemaker-kluster. I det här avsnittet används samma två värdar, `sollabdsm35` `sollabdsm36` och , som refereras till i början av den här artikeln.

Kontrollera att du har uppfyllt följande krav:  

* Pacemakerklustret konfigureras enligt dokumentationen och har rätt och fungerande stängsel

* SAP HANA start vid start är inaktiverat på alla klusternoder eftersom start och stopp hanteras av klustret

* SAP HANA systemreplikering och övertagande med hjälp av verktyg från SAP fungerar korrekt mellan klusternoder

* SAP HANA innehåller övervakningskonto som kan användas av klustret från båda klusternoderna

* Båda noderna prenumererar på kanalerna "Hög tillgänglighet" och "RHEL for SAP HANA" (RHEL 6,RHEL 7)

  

* I allmänhet kör du bara alla pcs-kommandon från på noden eftersom CIB uppdateras automatiskt från pcs-gränssnittet.

* [Mer information om kvorumprincip](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>Steg för att konfigurera 
1. Konfigurera datorer.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Konfigurerasync.
    Mer information finns i Hur [konfigurerar jag mitt RHEL 7-kluster med hög tillgänglighet med pacemaker ochsync.](https://access.redhat.com/solutions/1293523)
    ```
    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

3.  Skapa klonad SAPHanaTopology-resurs.
    SAPHanaTopology-resursen samlar in status och konfiguration av SAP HANA systemreplikering på varje nod. SAPHanaTopology kräver att följande attribut konfigureras.
       ```
       pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 InstanceNumber=00 --clone clone-max=2 clone-node-max=1    interleave=true
       ```

    | Attributnamn | Description  |
    |---|---|
    | SID | SAP System Identifier (SID) för SAP HANA installation. Måste vara samma för alla noder. |
    | InstanceNumber | Tvåsiffrig SAP-instansidentifierare.|

    * Resursstatus
       ```
       pcs resource show SAPHanaTopology_HR2_00
   
       InstanceNumber 2-digit SAP Instance identifier.
       pcs resource show SAPHanaTopology_HR2_00-clone
   
       Clone: SAPHanaTopology_HR2_00-clone
   
        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true
   
        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat
       type=SAPHanaTopology)
   
        Attributes: InstanceNumber=00 SID=HR2
   
        Operations: monitor interval=60 timeout=60
       (SAPHanaTopology_HR2_00-monitor-interval-60)
   
        start interval=0s timeout=180
       (SAPHanaTopology_HR2_00-start-interval-0s)
   
        stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)
   
       ```

4.  Skapa primär/sekundär SAPHana-resurs.
    * SAPHana-resursen ansvarar för att starta, stoppa och flytta SAP HANA databasen. Den här resursen måste köras som en primär/sekundär klusterresurs. Resursen har följande attribut.

| Attributnamn            | Obligatoriskt? | Standardvärde | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|---------------------------|-----------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SID                       | Yes       | Inget          | SAP System Identifier (SID) för SAP HANA installation. Måste vara samma för alla noder.                                                                                                                                                                                                                                                                                                                                                                                       |
| InstanceNumber            | Yes       | inget          | 2-siffrig SAP-instansidentifierare.                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| PREFER_SITE_TAKEOVER      | nej        | ja           | Bör klustret föredra att växla över till en sekundär instans i stället för att starta om den primära lokalt? ("nej": Föredrar att starta om lokalt; "ja": Föredrar övertagande till fjärrplats)                                                                                                                                                                                                                                                                                            |
|                           |           |               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| AUTOMATED_REGISTER        | nej        | FALSE         | Bör den tidigare SAP HANA vara registrerad som sekundär efter övertagande och DUPLICATE_PRIMARY_TIMEOUT? ("false": nej, manuella åtgärder krävs; "true": ja, den tidigare primära registreras av resursagenten som sekundär)                                                                                                                                                                                                                        |
| DUPLICATE_PRIMARY_TIMEOUT | nej        | 7200          | Tidsskillnad (i sekunder) som behövs mellan primära tidsstämplar, om en dubbel primär situation inträffar. Om tidsskillnaden är mindre än tidsskillnaden har klustret en eller båda instanserna statusen "VÄNTAR". Det här är för att ge en administratör möjlighet att reagera på en redundans. En tidigare misslyckad primär registreras när tidsskillnaden har passerat. Efter den här registreringen till den nya primära, skrivs alla data över av systemreplikeringen. |

5.  Skapa HANA-resursen.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200   AUTOMATED_REGISTER=true primary notify=true clone-max=2 clone-node-max=1 interleave=true

    pcs resource show SAPHana_HR2_00-primary


    Primary: SAPHana_HR2_00-primary

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true

        Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)

        Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200
    InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2

        Operations: demote interval=0s timeout=320
    (SAPHana_HR2_00-demote-interval-0s)

        monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)

        monitor interval=121 role=Secondary timeout=60
    (SAPHana_HR2_00-monitor-

        interval-121)

        monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-

        interval-119)

        promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)

        start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)

        stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  Skapa en resurs för virtuell IP-adress.
    Klustret innehåller en virtuell IP-adress för att nå den primära instansen av SAP HANA. Nedan visas exempelkommandot för att skapa en IPaddr2-resurs med IP-adressen 10.7.0.84/24.
    ```
    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  Skapa begränsningar.
    * För att åtgärden ska fungera korrekt måste vi se till att SAPHanaTopology-resurser startas innan du startar SAPHana-resurserna och att den virtuella IP-adressen finns på noden där den primära resursen för SAPHana körs. För att uppnå detta måste följande två begränsningar skapas.
       ```
       pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
       pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
       ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>Testa den manuella flytten av SAPHana-resursen till en annan nod

#### <a name="sap-hana-takeover-by-cluster"></a>(SAP Hana-övertagande efter kluster)


Om du vill testa flytten av SAPHana-resursen från en nod till en annan använder du kommandot nedan. Observera att alternativet `--primary` inte ska användas när du kör följande kommando på grund av hur SAPHana-resursen fungerar internt.
```pcs resource move SAPHana_HR2_00-primary```

Efter varje anrop av resursflyttningskommandot skapar klustret platsbegränsningar för att flytta resursen. Dessa begränsningar måste tas bort för att tillåta automatisk redundans i framtiden.
Om du vill ta bort dem kan du använda kommandot nedan.
```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* Logga in på HANA som verifiering.

  * nedgraderad värd:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * Uppflyttad värd:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

Med alternativet `AUTOMATED_REGISTER=false` kan du inte växla fram och tillbaka.

Om det här alternativet är inställt på false måste du omregistrera noden:
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```

Nu fungerar node2, som var den primära, som den sekundära värden.

Överväg att ange det här alternativet till sant för att automatisera registreringen av den nedgraderade värden.
  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true
pcs cluster node clear node1
```

Om du föredrar automatisk registrering beror på kundscenariot. Det blir enklare för driftteamet att automatiskt registrera om noden efter ett övertagande. Men du kanske vill registrera noden manuellt för att först köra ytterligare tester för att se till att allt fungerar som förväntat.
