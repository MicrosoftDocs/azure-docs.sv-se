---
title: Konfigurera MPIO på StorSimple Linux-värd
description: Lär dig de steg som krävs för att konfigurera multipath i/o (MPIO) på din StorSimple Linux-värd Server (CentOS 6,6).
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: how-to
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 2b7ddf6423db4c471ee2065635f4e3e89f7eb7b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745741"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Konfigurera MPIO på en StorSimple-värd som kör CentOS
I den här artikeln beskrivs de steg som krävs för att konfigurera multipath i/o (MPIO) på din CentOS 6,6-värd Server. Värd servern är ansluten till din Microsoft Azure StorSimple enhet för hög tillgänglighet via iSCSI-initierare. Den beskriver i detalj den automatiska identifieringen av flera Sök vägs enheter och den speciella installationen enbart för StorSimple volymer.

Den här proceduren gäller för alla modeller av enheter med StorSimple 8000-serien.

> [!NOTE]
> Det går inte att använda den här proceduren för en StorSimple Cloud Appliance. Mer information finns i så här konfigurerar du värd servrar för moln installationen.

## <a name="about-multipathing"></a>Om flera sökvägar
Med funktionen flera sökvägar kan du konfigurera flera I/O-sökvägar mellan en värd Server och en lagrings enhet. Dessa I/O-sökvägar är fysiska SAN-anslutningar som kan innehålla separata kablar, växlar, nätverks gränssnitt och styrenheter. Med flera sökvägar sammanställs i/O-sökvägar för att konfigurera en ny enhet som är associerad med alla aggregerade sökvägar.

Syftet med flera sökvägar är två vik:

* **Hög tillgänglighet**: den innehåller en alternativ sökväg om något element i i/O-sökvägen (till exempel en kabel, växel, ett nätverks gränssnitt eller en styrenhet) Miss lyckas.
* **Belastnings utjämning**: beroende på konfigurationen av lagrings enheten kan du förbättra prestanda genom att identifiera belastningar i i/O-sökvägar och dynamiskt ombalansera de belastningarna.

### <a name="about-multipathing-components"></a>Om multivägsing-komponenter
Flera sökvägar i Linux består av kernel-komponenter och komponenter för användar utrymme som tabellen nedan.

* **Kernel**: huvud komponenten är *enhets mappningen* som dirigerar om I/O och stöder redundans för sökvägar och Sök vägs grupper.

* **Användar utrymme**: det här är *flera Sök vägs verktyg* som hanterar enheter med flera sökvägar genom att instruera modulen enhets mappning över flera sökvägar vad du ska göra. Verktygen består av:
   
   * **Flera sökvägar**: visar och konfigurerar enheter med flera sökvägar.
   * **Flera** sökvägar: daemon som kör flera sökvägar och övervakar Sök vägarna.
   * **Devmap-Name**: ger ett meningsfullt enhets namn till udev för devmaps.
   * **Kpartx**: mappar linjär devmaps till diskpartitioner för att göra att flera sökvägar kan partitioneras.
   * **Multisökväg. conf**: konfigurations fil för daemon för flera sökvägar som används för att skriva över den inbyggda konfigurations tabellen.

### <a name="about-the-multipathconf-configuration-file"></a>Om konfigurations filen multipath. conf
Konfigurations filen `/etc/multipath.conf` innehåller många av funktionerna för flera sökvägar som kan konfigureras av användaren. `multipath`Kommandot och kernel daemon `multipathd` använder information som finns i den här filen. Filen visas endast under konfigurationen av flera Sök vägs enheter. Se till att alla ändringar görs innan du kör `multipath` kommandot. Om du ändrar filen efteråt måste du stoppa och starta flera Sök vägar igen för att ändringarna ska börja gälla.

Multisökväg. conf har fem avsnitt:

- **Standardinställningar för system nivå** *(standard)*: du kan åsidosätta system nivåns standardinställningar.
- Svartlistade **enheter** *(Black)*: du kan ange en lista över enheter som inte ska kontrol leras av enhets mappning.
- **Black List-undantag** *(blacklist_exceptions)*: du kan identifiera vilka enheter som ska hanteras som flera Sök vägs enheter även om de anges i blockeringslistan.
- **Inställningar för lagrings styrenhet** *(enheter)*: du kan ange de konfigurations inställningar som ska tillämpas på enheter som har leverantörs-och produkt information.
- **Enhets specifika inställningar** *(flera sökvägar)*: du kan använda det här avsnittet för att finjustera konfigurations inställningarna för enskilda LUN.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Konfigurera flera sökvägar på StorSimple som är anslutna till Linux-värden
En StorSimple-enhet som är ansluten till en Linux-värd kan konfigureras för hög tillgänglighet och belastnings utjämning. Om Linux-värden till exempel har två gränssnitt som är anslutna till SAN och enheten har två gränssnitt som är anslutna till SAN-nätverket, så att dessa gränssnitt finns i samma undernät, så finns det fyra sökvägar som är tillgängliga. Men om varje DATA gränssnitt på enhets-och värd gränssnittet finns i ett annat IP-undernät (och inte dirigeras), kommer bara 2 sökvägar att vara tillgängliga. Du kan konfigurera flera sökvägar för att automatiskt identifiera alla tillgängliga sökvägar, välja en algoritm för belastnings utjämning för de Sök vägarna, tillämpa vissa konfigurations inställningar för StorSimple volymer och sedan aktivera och verifiera flera sökvägar.

Följande procedur beskriver hur du konfigurerar flera sökvägar när en StorSimple-enhet med två nätverks gränssnitt är ansluten till en värd med två nätverks gränssnitt.

## <a name="prerequisites"></a>Förutsättningar
I det här avsnittet beskrivs konfigurations kraven för CentOS-servern och din StorSimple-enhet.

### <a name="on-centos-host"></a>På CentOS-värd
1. Se till att CentOS-värden har två nätverks gränssnitt aktiverade. Ange:
   
    `ifconfig`
   
    I följande exempel visas utdata när två nätverks gränssnitt ( `eth0` och `eth1` ) finns på värden.
   
    ```output
    [root@centosSS ~]# ifconfig
    eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
        inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
        TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)

    eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
        inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
        TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)

    loLink encap:Local Loopback  
        inet addr:127.0.0.1  Mask:255.0.0.0
        inet6 addr: ::1/128 Scope:Host
        UP LOOPBACK RUNNING  MTU:65536  Metric:1
        RX packets:12 errors:0 dropped:0 overruns:0 frame:0
        TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:0
        RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
    ```
1. Installera *iSCSI-Initiator-utils* på din CentOS-Server. Utför följande steg för att installera *iSCSI-Initiator-utils*.
   
   1. Logga in som `root` CentOS-värd.
   1. Installera *iSCSI-Initiator-utils*. Ange:
      
       `yum install iscsi-initiator-utils`
   1. Starta iSCSI *-tjänsten när iSCSI-initieraren-utils* har installerats. Ange:
      
       `service iscsid start`
      
       `iscsid`Ibland kan det hända att det inte går att starta och att `--force` alternativet kan krävas
   1. För att säkerställa att iSCSI-initieraren är aktive rad under start tiden använder du `chkconfig` kommandot för att aktivera tjänsten.
      
       `chkconfig iscsi on`
   1. Verifiera att installationen har slutförts korrekt genom att köra kommandot:
      
       `chkconfig --list | grep iscsi`
      
       Ett exempel på utdata visas nedan.
      
        ```output
        iscsi   0:off   1:off   2:on3:on4:on5:on6:off
        iscsid  0:off   1:off   2:on3:on4:on5:on6:off
        ```
      
       I ovanstående exempel kan du se att din iSCSI-miljö körs vid start på körnings nivå 2, 3, 4 och 5.
1. Installera *device-mapper-multipath*. Ange:
   
    `yum install device-mapper-multipath`
   
    Installationen startar. Skriv **Y** om du vill fortsätta när du uppmanas att bekräfta.

### <a name="on-storsimple-device"></a>På StorSimple-enhet
Din StorSimple-enhet bör ha:

* Minst två gränssnitt som är aktiverade för iSCSI. Kontrol lera att två gränssnitt är iSCSI-aktiverade på din StorSimple-enhet genom att utföra följande steg i den klassiska Azure-portalen för din StorSimple-enhet:
  
  1. Logga in på den klassiska portalen för din StorSimple-enhet.
  1. Välj din StorSimple Manager-tjänst, klicka på **enheter** och välj den speciella StorSimple-enheten. Klicka på **Konfigurera** och verifiera inställningarna för nätverks gränssnittet. En skärm bild med två iSCSI-aktiverade nätverks gränssnitt visas nedan. Dessa DATA 2 och DATA 3 är de båda 10 GbE-gränssnitten aktiverade för iSCSI.
     
      ![MPIO StorsSimple DATA 2-konfiguration](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple DATA 3-konfiguration](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      På sidan **Konfigurera**
     
     1. Se till att båda nätverks gränssnitten är iSCSI-aktiverade. Fältet för **iSCSI-aktiverat** ska vara inställt på **Ja**.
     1. Se till att nätverks gränssnitten har samma hastighet, båda ska vara 1 GbE eller 10 GbE.
     1. Notera IPv4-adresserna för iSCSI-aktiverade gränssnitt och spara för senare användning på värden.
* ISCSI-gränssnitten på din StorSimple-enhet bör kunna kontaktas från CentOS-servern.
      För att verifiera detta måste du ange IP-adresserna för dina StorSimple-iSCSI-aktiverade nätverks gränssnitt på värd servern. De kommandon som används och motsvarande utdata med DATA2 (10.126.162.25) och DATA3 (10.126.162.26) visas nedan:
  
    ```console
    [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    ```

### <a name="hardware-configuration"></a>Maskin varu konfiguration
Vi rekommenderar att du ansluter de två iSCSI-nätverks gränssnitten på separata sökvägar för redundans. Figuren nedan visar den rekommenderade maskin varu konfigurationen för hög tillgänglighet och belastnings utjämning av flera sökvägar för CentOS-servern och StorSimple-enheten.

![MPIO-maskinvarubaserad konfiguration för StorSimple till Linux-värd](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Som du ser i föregående figur:

* Din StorSimple-enhet är i en aktiv-passiv konfiguration med två styrenheter.
* Två SAN-växlar är anslutna till dina enhets styrenheter.
* Två iSCSI-initierare har Aktiver ATS på din StorSimple-enhet.
* Två nätverks gränssnitt är aktiverade på din CentOS-värd.

Konfigurationen ovan kommer att ge 4 separata sökvägar mellan enheten och värden om värden och data gränssnitten är flyttbara.

> [!IMPORTANT]
> * Vi rekommenderar att du inte blandar 1 GbE-och 10 GbE-nätverkskort för flera sökvägar. När du använder två nätverks gränssnitt bör båda gränssnitten vara identiska.
> * På din StorSimple-enhet är DATA0, fil1, DATA4 och DATA5 1 GbE-gränssnitt, medan DATA2 och DATA3 är 10 GbE-nätverks gränssnitt. |
> 
> 

## <a name="configuration-steps"></a>Konfigurationssteg
Konfigurations stegen för flera sökvägar innefattar att konfigurera tillgängliga sökvägar för automatisk identifiering, ange den algoritm för belastnings utjämning som ska användas, aktivera flera sökvägar och slutligen verifiera konfigurationen. Vart och ett av dessa steg beskrivs i detalj i följande avsnitt.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Steg 1: Konfigurera flera sökvägar för automatisk identifiering
Enheter som stöds för flera sökvägar kan identifieras och konfigureras automatiskt.

1. Initiera `/etc/multipath.conf` fil. Ange:
   
     `mpathconf --enable`
   
    Kommandot ovan kommer att skapa en `sample/etc/multipath.conf` fil.
1. Starta multipath-tjänsten. Ange:
   
    `service multipathd start`
   
    Du ser följande utdata:
   
    `Starting multipathd daemon:`
1. Aktivera automatisk identifiering av flera sökvägar. Ange:
   
    `mpathconf --find_multipaths y`
   
    Detta ändrar standard avsnittet i `multipath.conf` som visas nedan:
   
    ```config
    defaults {
    find_multipaths yes
    user_friendly_names yes
    path_grouping_policy multibus
    }
    ```

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Steg 2: Konfigurera flera sökvägar för StorSimple-volymer
Som standard är alla enheter blocklisted i filen multipath. conf och kommer att kringgås. Du måste skapa blockeringslistan-undantag för att tillåta flera sökvägar för volymer från StorSimple-enheter.

1. Redigera filen `/etc/mulitpath.conf`. Ange:
   
    `vi /etc/multipath.conf`
1. Leta upp avsnittet blacklist_exceptions i filen multipath. conf. Din StorSimple-enhet måste anges som ett blockeringslistan-undantag i det här avsnittet. Du kan ta bort kommentaren till relevanta rader i den här filen om du vill ändra den enligt nedan (Använd endast den enhets modell som du använder):
   
    ```config
    blacklist_exceptions {
        device {
                    vendor  "MSFT"
                    product "STORSIMPLE 8100*"
        }
        device {
                    vendor  "MSFT"
                    product "STORSIMPLE 8600*"
        }
    }
    ```

### <a name="step-3-configure-round-robin-multipathing"></a>Steg 3: Konfigurera resursallokering (Round-Robin) för flera sökvägar
Den här belastnings Utjämnings algoritmen använder alla tillgängliga flera sökvägar till den aktiva styrenheten i ett balanserat, resursallokering.

1. Redigera filen `/etc/multipath.conf`. Ange:
   
    `vi /etc/multipath.conf`
1. Under `defaults` avsnittet ställer du in på `path_grouping_policy` `multibus` . `path_grouping_policy`Anger den standard Sök vägs princip som ska användas för ospecificerade flera sökvägar. Standard avsnittet ser ut som visas nedan.
   
    ```config
    defaults {
            user_friendly_names yes
            path_grouping_policy multibus
    }
    ```

> [!NOTE]
> De vanligaste värdena `path_grouping_policy` är:
> 
> * redundans = 1 sökväg per prioritets grupp
> * multibus = alla giltiga sökvägar i 1 prioritets grupp
> 
> 

### <a name="step-4-enable-multipathing"></a>Steg 4: aktivera flera sökvägar
1. Starta om `multipathd` daemonen. Ange:
   
    `service multipathd restart`
1. Utdata visas nedan:
   
    ```output
    [root@centosSS ~]# service multipathd start
    Starting multipathd daemon:  [OK]
    ```

### <a name="step-5-verify-multipathing"></a>Steg 5: kontrol lera flera sökvägar
1. Se först till att iSCSI-anslutningen upprättas med StorSimple-enheten enligt följande:
   
   a. Identifiera din StorSimple-enhet. Ange:
      
    `iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`
    
    Utdata när IP-adressen för DATA0 är 10.126.162.25 och port 3260 öppnas på StorSimple-enheten för utgående iSCSI-trafik enligt nedan:
    
    ```output
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Kopiera IQN för din StorSimple-enhet, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` från föregående utdata.

   b. Anslut till enheten med mål-IQN. StorSimple-enheten är iSCSI-målet här. Ange:

      `iscsiadm -m node --login -T <IQN of iSCSI target>`

    I följande exempel visas utdata med ett mål-IQN för `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` . Utdata indikerar att du har anslutit till de två iSCSI-aktiverade nätverks gränssnitten på enheten.

    ```output
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Om du bara ser ett värd gränssnitt och två sökvägar här måste du aktivera båda gränssnitten på värden för iSCSI. Du kan följa de [detaljerade anvisningarna i Linux-dokumentationen](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. En volym exponeras för CentOS-servern från StorSimple-enheten. Mer information finns i [steg 6: skapa en volym](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) via Azure Portal på din StorSimple-enhet.

1. Verifiera tillgängliga sökvägar. Ange:

    `multipath -l`

      I följande exempel visas utdata för två nätverks gränssnitt på en StorSimple-enhet som är ansluten till ett enda värd nätverks gränssnitt med två tillgängliga sökvägar.

    ```output
    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 7:0:0:1 sdc 8:32 active undef running
    `- 6:0:0:1 sdd 8:48 active undef running
    ```

    I följande exempel visas utdata för två nätverks gränssnitt på en StorSimple-enhet som är ansluten till två värd nätverks gränssnitt med fyra tillgängliga sökvägar.

    ```output
    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 17:0:0:0 sdb 8:16 active undef running
    |- 15:0:0:0 sdd 8:48 active undef running
    |- 14:0:0:0 sdc 8:32 active undef running
    `- 16:0:0:0 sde 8:64 active undef running
    ```

    När Sök vägarna har kon figurer ATS, se de instruktioner som finns på värd operativ systemet (CentOS 6,6) för att montera och formatera volymen.

## <a name="troubleshoot-multipathing"></a>Felsöka flera sökvägar
Det här avsnittet innehåller några användbara tips om du stöter på problem under konfigurationen av flera sökvägar.

F. Jag ser inte ändringarna i `multipath.conf` filen börjar att fungera.

A. Om du har gjort ändringar i `multipath.conf` filen måste du starta om tjänsten flera sökvägar. Ange följande kommando:

`service multipathd restart`

F. Jag har aktiverat två nätverks gränssnitt på StorSimple-enheten och två nätverks gränssnitt på värden. När jag visar en lista över tillgängliga sökvägar visas bara två sökvägar. Jag förväntade dig att se fyra tillgängliga sökvägar.

A. Kontrol lera att de två Sök vägarna finns i samma undernät och dirigeras. Om nätverks gränssnitten finns på olika VLAN och inte kan dirigeras, visas bara två sökvägar. Ett sätt att kontrol lera detta är att se till att du kan komma åt båda värd gränssnitten från ett nätverks gränssnitt på StorSimple-enheten. Du måste [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) eftersom den här verifieringen bara kan göras via en support-session.

F. När jag visar en lista över tillgängliga sökvägar visas inga utdata.

A. Om du inte ser några sökvägar till flera sökvägar föreslår vi vanligt vis ett problem med daemonen för flera sökvägar, och det är troligt vis att det finns problem här i `multipath.conf` filen.

Det kan också vara värt att kontrol lera att du faktiskt kan se vissa diskar efter att ha anslutit till målet, eftersom inget svar från flera Sök vägs listor kan innebära att du inte har några diskar.

* Använd följande kommando för att genomsöka SCSI-bussen:
  
    `$ rescan-scsi-bus.sh` (del av sg3_utils-paketet)
* Ange följande kommandon:
  
    `$ dmesg | grep sd*`
     
     Eller
  
    `$ fdisk -l`
  
    Dessa kommer att returnera information om nyligen tillagda diskar.
* Använd följande kommandon för att avgöra om det är en StorSimple-disk:
  
    `cat /sys/block/<DISK>/device/model`
  
    Detta returnerar en sträng som avgör om det är en StorSimple-disk.

En mindre sannolik men möjlig orsak kan också vara inaktuellt iSCSI-PID. Använd följande kommando för att logga ut från iSCSI-sessionerna:

`iscsiadm -m node --logout -p <Target_IP>`

Upprepa det här kommandot för alla anslutna nätverks gränssnitt på iSCSI-målet, som är din StorSimple-enhet. När du har loggat ut från alla iSCSI-sessioner använder du iSCSI-målets IQN för att återupprätta iSCSI-sessionen. Ange följande kommando:

`iscsiadm -m node --login -T <TARGET_IQN>`


F. Jag är osäker på om enheten är tillåten.

A. För att kontrol lera om enheten tillåts, Använd följande interaktiva kommando för fel sökning:

```console
multipathd -k
multipathd> show devices
available block devices:
ram0 devnode blacklisted, unmonitored
ram1 devnode blacklisted, unmonitored
ram2 devnode blacklisted, unmonitored
ram3 devnode blacklisted, unmonitored
ram4 devnode blacklisted, unmonitored
ram5 devnode blacklisted, unmonitored
ram6 devnode blacklisted, unmonitored
ram7 devnode blacklisted, unmonitored
ram8 devnode blacklisted, unmonitored
ram9 devnode blacklisted, unmonitored
ram10 devnode blacklisted, unmonitored
ram11 devnode blacklisted, unmonitored
ram12 devnode blacklisted, unmonitored
ram13 devnode blacklisted, unmonitored
ram14 devnode blacklisted, unmonitored
ram15 devnode blacklisted, unmonitored
loop0 devnode blacklisted, unmonitored
loop1 devnode blacklisted, unmonitored
loop2 devnode blacklisted, unmonitored
loop3 devnode blacklisted, unmonitored
loop4 devnode blacklisted, unmonitored
loop5 devnode blacklisted, unmonitored
loop6 devnode blacklisted, unmonitored
loop7 devnode blacklisted, unmonitored
sr0 devnode blacklisted, unmonitored
sda devnode whitelisted, monitored
dm-0 devnode blacklisted, unmonitored
dm-1 devnode blacklisted, unmonitored
dm-2 devnode blacklisted, unmonitored
sdb devnode whitelisted, monitored
sdc devnode whitelisted, monitored
dm-3 devnode blacklisted, unmonitored
```


Mer information finns i [fel sökning för flera sökvägar](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot).

## <a name="list-of-useful-commands"></a>Lista över användbara kommandon
| Typ | Kommando | Beskrivning |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Starta iSCSI-tjänsten |
| &nbsp; |`service iscsid stop` |Stoppa iSCSI-tjänsten |
| &nbsp; |`service iscsid restart` |Starta om iSCSI-tjänsten |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Identifiera tillgängliga mål på den angivna adressen |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Logga in på iSCSI-målet |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Logga ut från iSCSI-målet |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Skriv ut iSCSI-initierarens namn |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Kontrol lera statusen för iSCSI-sessionen och den volym som identifierats på värden |
| &nbsp; |`iscsi -m session` |Visar alla iSCSI-sessioner som upprättats mellan värden och StorSimple-enheten |
|  | | |
| **Multipathing** |`service multipathd start` |Starta daemon för flera sökvägar |
| &nbsp; |`service multipathd stop` |Stoppa daemon för flera sökvägar |
| &nbsp; |`service multipathd restart` |Starta om Multipath daemon |
| &nbsp; |`chkconfig multipathd on` </br> ELLER </br> `mpathconf -with_chkconfig y` |Aktivera daemon för flera sökvägar till start vid start |
| &nbsp; |`multipathd -k` |Starta den interaktiva konsolen för fel sökning |
| &nbsp; |`multipath -l` |Visa en lista över flera sökvägar anslutningar och enheter |
| &nbsp; |`mpathconf --enable` |Skapa en Sample mulitpath. conf-fil i `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Nästa steg
När du konfigurerar MPIO på Linux-värden kan du också behöva referera till följande CentoS 6,6-dokument:

* [Konfigurera MPIO på CentOS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Tränings guide för Linux](http://linux-training.be/linuxsys.pdf)
