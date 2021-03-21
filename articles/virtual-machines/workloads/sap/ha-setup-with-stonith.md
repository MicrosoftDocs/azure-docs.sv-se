---
title: Hög tillgänglighet konfigureras med STONITH för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Upprätta hög tillgänglighet för SAP HANA på Azure (stora instanser) i SUSE med hjälp av STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83bf6b6123cf7e0d57296f1f344a264c8a18ed77
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671061"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Konfigurera hög tillgänglighet i SUSE med hjälp av STONITH
Det här dokumentet innehåller detaljerade steg-för-steg-instruktioner för att konfigurera hög tillgänglighet på ett SUSEt operativ system med hjälp av STONITH-enheten.

**Fri skrivning:** *den här guiden härleds genom att testa installationen i Microsoft Hana Large instances-miljön, som fungerar. Eftersom Microsoft Service Management-teamet för HANA-stora instanser inte stöder operativ system kan du behöva kontakta SUSE för att få ytterligare fel sökning eller för tydligande på operativ system skiktet. Microsoft Service Management-teamet konfigurerar STONITH-enheten och har fullt stöd för fel sökning av problem med STONITH-enheter.*
## <a name="overview"></a>Översikt
Om du vill konfigurera hög tillgänglighet med hjälp av SUSE klustring måste följande krav uppfyllas.
### <a name="pre-requisites"></a>Förutsättningar
- HANA-stora instanser har allokerats
- Operativ system har registrerats
- HANA-servrar med stora instanser är anslutna till SMT-Server för att hämta korrigeringar/paket
- De senaste korrigeringarna har installerats för operativ systemet
- NTP (tids Server) har kon figurer ATS
- Läs och förstå den senaste versionen av SUSE-dokumentationen om HA-installationen

### <a name="setup-details"></a>Installations information
I den här guiden används följande konfiguration:
- Operativ system: SLES 12 SP1 för SAP
- HANA stora instanser: 2xS192 (fyra Sockets, 2 TB)
- HANA-version: HANA 2,0 SP1
- Server Namn: sapprdhdb95 (Nod1) och sapprdhdb96 (NOD2)
- STONITH-enhet: iSCSI-baserad STONITH-enhet
- NTP-inställningar på en av noderna HANA stor instans

När du konfigurerar HANA-stora instanser med HSR kan du be Microsoft Service Management-teamet att ställa in STONITH. Om du redan är en befintlig kund som har HANA-stora instanser etablerade och behöver STONITH enhets konfiguration för dina befintliga blad, måste du ange följande information för Microsoft Service Management-teamet i formuläret för tjänstbegäran (SRF). Du kan begära SRF-formulär via teknisk konto ansvarig eller din Microsoft-kontakt för HANA-stor instans onboarding. De nya kunderna kan begära STONITH-enhet vid etablerings tillfället. Indata är tillgängliga i formuläret för etablering av förfrågningar.

- Server namn och Server-IP-adress (till exempel myhanaserver1, 10.35.0.1)
- Plats (till exempel USA, öst)
- Kund namn (till exempel Microsoft)
- SID-HANA system identifierare (till exempel H11)

När STONITH-enheten har kon figurer ATS tillhandahåller Microsoft Service Management-teamet det SBD enhets namnet och IP-adressen för iSCSI-lagringen, som du kan använda för att konfigurera STONITH-installationsprogrammet. 

Om du vill konfigurera slut punkt till slut punkt med hjälp av STONITH måste följande steg följas:

1.  Identifiera SBD-enheten
2.  Initiera SBD-enheten
3.  Konfigurera klustret
4.  Konfigurera Softdog-övervaknings enheten
5.  Anslut noden till klustret
6.  Verifiera klustret
7.  Konfigurera resurserna till klustret
8.  Testa redundansväxlingen

## <a name="1---identify-the-sbd-device"></a>1. identifiera SBD-enheten
I det här avsnittet beskrivs hur du fastställer SBD-enheten för installationen när Microsoft Service Management-teamet har konfigurerat STONITH. **Det här avsnittet gäller endast för den befintliga kunden**. Om du är en ny kund tillhandahåller Microsoft Service Management-teamet SBD-enhetens namn och du kan hoppa över det här avsnittet.

1,1 ändra */etc/iSCSI/initiatorname.iSCI* till 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Microsoft Service Management tillhandahåller den här strängen. Ändra filen på **båda** noderna, men Node-numret är olika på varje nod.

![Skärm bild som visar en initiatorname-fil med InitiatorName-värden för en nod.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1,2 ändra */etc/iSCSI/iscsid.conf*: ange *Node.session.Timeo.replacement_timeout = 5* och *Node. Startup = automatisk*. Ändra filen på **båda** noderna.

1,3 kör identifierings kommandot. det visar fyra sessioner. Kör det på båda noderna.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![Skärm bild som visar ett konsol fönster med resultat från isciadm Discovery-kommandot.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1,4 kör kommandot för att logga in på iSCSI-enheten. det visar fyra sessioner. Kör det på **båda** noderna.

```
iscsiadm -m node -l
```
![Skärm bild som visar ett konsol fönster med resultatet av kommandot iscsiadm Node.](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1,5 kör omsöknings skriptet: *Rescan-SCSI-Bus.sh*.  Det här skriptet visar de nya diskar som skapats åt dig.  Kör det på båda noderna. Du bör se ett LUN-nummer som är större än noll (till exempel: 1, 2 osv.)

```
rescan-scsi-bus.sh
```
![Skärm bild som visar ett konsol fönster med resultat från skriptet.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1,6 om du vill hämta enhets namnet kör du kommandot *fdisk – l*. Kör det på båda noderna. Välj enheten med storleken **178 MIB**.

```
  fdisk –l
```

![Skärm bild som visar ett konsol fönster med resultat från kommandot f disk.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. initiera SBD-enheten

2,1 initiera SBD-enheten på **båda** noderna

```
sbd -d <SBD Device Name> create
```
![Skärm bild som visar ett konsol fönster med resultatet av kommandot s b d Create.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2,2 kontrol lera vad som har skrivits till enheten. Gör det på **båda** noderna

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. Konfigurera klustret
Det här avsnittet beskriver stegen för att konfigurera SUSEt HA-klustret.
### <a name="31-package-installation"></a>3,1 paket installation
3.1.1 kontrol lera att ha_sles-och SAPHanaSR-mönster är installerade. Om den inte är installerad installerar du dem. Installera den på **båda** noderna.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![Skärm bild som visar ett konsol fönster med resultatet av mönster kommandot. ](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
 ![ Skärm bild visar ett konsol fönster med resultatet från kommandot SAPHanaSR-doc.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3,2 Konfigurera klustret
3.2.1 du kan antingen använda kommandot *ha-Cluster-init* eller använda guiden YaST2 för att konfigurera klustret. I det här fallet används guiden YaST2. Du utför bara det här steget **på den primära noden**.

Följ YaST2> hög tillgänglighet >s kluster ![ skärm bild visar kontroll Center för YaST med hög tillgänglighet och valt kluster. ](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
 ![ Skärm bild som visar en dialog ruta med alternativ för att installera och avbryta.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Klicka på **Avbryt** eftersom halk2-paketet redan är installerat.

![Skärm bild som visar ett meddelande om ditt avbrott-alternativ.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klicka på **Fortsätt**

Förväntat värde = antal noder som har distribuerats (i det här fallet 2) ![ skärm bilden visar kluster säkerhet med kryss rutan Aktivera säkerhetsautentisering.](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)
Klicka på **Nästa** 
 ![ skärm bild som visar klustret Konfigurera fönster med listor över synkroniserade värdar och synkroniserade filer.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
Lägg till nodnamn och klicka sedan på Lägg till föreslagna filer

Klicka på Aktivera csync2 på

Klicka på Skapa i förväg delade nycklar, som visas under popup

![Skärm bild som visar ett meddelande om att din nyckel har skapats.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klicka på **OK**

Autentiseringen utförs med hjälp av IP-adresser och i förväg delade nycklar i Csync2. Nyckel filen genereras med csync2-k/etc/csync2/key_hagroup. Filen key_hagroup bör kopieras till alla medlemmar i klustret manuellt när den har skapats. **Se till att kopiera filen från nod 1 till NOD2**.

![Skärm bild som visar dialog rutan Konfigurera kluster med alternativ som krävs för att kopiera nyckeln till alla medlemmar i klustret.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klicka på **Nästa** 
 ![ skärm bild som visar fönstret kluster tjänst.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

I standard alternativet var starten inaktive rad, ändra den till "på" så att pacemaker startas vid start. Du kan välja baserat på dina installations krav.
Klicka på **Nästa** så är kluster konfigurationen slutförd.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Konfigurera Softdog-övervaknings enheten
I det här avsnittet beskrivs konfigurationen av övervaknings enheten (softdog).

4,1 Lägg till följande rad i */etc/init.d/Boot.local* på **båda** noderna.
```
modprobe softdog
```
![Skärm bild som visar en start fil med softdog-raden tillagd.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4,2 uppdatera filen */etc/sysconfig/SBD* på **båda** noderna enligt följande:
```
SBD_DEVICE="<SBD Device Name>"
```
![Skärm bild som visar s b d-filen med värdet S B D_DEVICE tillagt.](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4,3 Ladda kernel-modulen på **båda** noderna genom att köra följande kommando
```
modprobe softdog
```
![Skärm bild som visar en del av ett konsol fönster med kommandot modprobe softdog.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4,4 kontrol lera och se till att softdog körs enligt följande på **båda** noderna:
```
lsmod | grep dog
```
![Skärm bild som visar en del av ett konsol fönster med resultatet av att köra kommandot l s mod.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4,5 Starta SBD-enheten på **båda** noderna
```
/usr/share/sbd/sbd.sh start
```
![Skärm bild som visar en del av ett konsol fönster med start kommandot.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4,6 testa daemon för SBD på **båda** noderna. Du ser två poster efter att du har konfigurerat den på **båda** noderna
```
sbd -d <SBD Device Name> list
```
![Skärm bild som visar en del av ett konsol fönster som visar två poster.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,7 skicka ett test meddelande till **någon** av noderna
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![Skärm bild som visar en del av ett konsol fönster som visar två poster.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,8 på den **andra** noden (NOD2) kan du kontrol lera meddelandets status
```
sbd  -d <SBD Device Name> list
```
![Skärm bild som visar en del av ett konsol fönster med en av medlemmarna som visar ett testvärde för den andra medlemmen.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4,9 om du vill införa SBD-konfigurationen uppdaterar du filen */etc/sysconfig/SBD* enligt följande. Uppdatera filen på **båda** noderna
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4,10 starta pacemaker-tjänsten på den **primära noden** (Nod1)
```
systemctl start pacemaker
```
![Skärm bild visar ett konsol fönster som visar status när pacemaker har startats.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Om pacemaker-tjänsten *Miss lyckas*, se *Scenario 5: pacemaker-tjänsten Miss lyckas*

## <a name="5---joining-the-cluster"></a>5. ansluter till klustret
I det här avsnittet beskrivs hur du ansluter noden till klustret.

### <a name="51-add-the-node"></a>5,1 Lägg till noden
Kör följande kommando på **NOD2** för att låta NOD2 ansluta till klustret.
```
ha-cluster-join
```
Om du får ett *fel* när du ansluter till klustret, se *Scenario 6: nod 2 det går inte att ansluta klustret*.

## <a name="6---validating-the-cluster"></a>6. verifiera klustret

### <a name="61-start-the-cluster-service"></a>6,1 Starta kluster tjänsten
För att kontrol lera och eventuellt starta klustret för första gången på **båda** noderna.
```
systemctl status pacemaker
systemctl start pacemaker
```
![Skärm bild som visar ett konsol fönster med status pacemaker.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6,2 övervaka status
Kör kommandot *crm_mon* för att se till att **båda** noderna är online. Du kan köra den på **någon av noderna** i klustret
```
crm_mon
```
![Skärm bild som visar ett konsol fönster med resultaten från c r m_mon.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
Du kan också logga in på Hawk för att kontrol lera kluster status *https:// \<node IP> : 7630*. Standard användaren är hacluster och lösen ordet är Linux. Om det behövs kan du ändra lösen ordet med hjälp av kommandot *passwd* .

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurera kluster egenskaper och-resurser 
Det här avsnittet beskriver stegen för att konfigurera kluster resurserna.
I det här exemplet konfigurerar du följande resurs, men resten kan konfigureras (om det behövs) genom att referera till SUSE HA-guiden. Utför bara konfigurationen på **en av noderna** . Gör på primär nod.

- Start av kluster
- STONITH-enhet
- Den virtuella IP-adressen


### <a name="71-cluster-bootstrap-and-more"></a>7,1 kluster start med mera
Lägg till kluster start. Skapa filen och Lägg till texten enligt följande:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Lägg till konfigurationen i klustret.
```
crm configure load update crm-bs.txt
```
![Skärm bild som visar en del av ett konsol fönster som kör kommandot c r m.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7,2 STONITH-enhet
Lägg till resurs STONITH. Skapa filen och Lägg till texten enligt följande.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Lägg till konfigurationen i klustret.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7,3 virtuell IP-adress
Lägg till resursens virtuella IP-adress. Skapa filen och Lägg till texten enligt nedan.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Lägg till konfigurationen i klustret.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7,4 verifiera resurserna

När du kör kommandot *crm_mon* kan du se de två resurserna där.
![Skärm bild som visar ett konsol fönster med två resurser.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Du kan också se status på *https:// \<node IP address> : 7630/CIB/Live/State*

![Skärm bild visar status för de två resurserna.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. testa redundansväxlingen
Om du vill testa redundansväxlingen stoppar du pacemaker-tjänsten på Nod1 och resurserna redundansväxlas till NOD2.
```
Service pacemaker stop
```
Stoppa nu pacemaker-tjänsten på **NOD2** och resurser har redundansväxlats till **Nod1**

**Före redundans**  
![Skärm bild som visar status för de två resurserna före redundans.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Efter redundans**  
![Skärm bild visar status för de två resurserna efter redundansväxlingen.](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![Skärm bild som visar ett konsol fönster med statusen resurser efter redundansväxlingen.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. fel sökning
I det här avsnittet beskrivs några fel scenarier som kan påträffas under installationen. Du kanske inte nödvändigt vis möter problemen.

### <a name="scenario-1-cluster-node-not-online"></a>Scenario 1: klusternoden är inte online
Om någon av noderna inte visas online i kluster hanteraren kan du prova följande för att ta den online.

Starta iSCSI-tjänsten
```
service iscsid start
```

Nu bör du kunna logga in på iSCSI-noden
```
iscsiadm -m node -l
```
Förväntade utdata ser ut ungefär så här
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scenario 2: YaST2 visar inte grafisk vy
Den grafiska skärmen YaST2 används för att konfigurera klustret för hög tillgänglighet i det här dokumentet. Om YaST2 inte öppnas med det grafiska fönstret som det visas och genererar qt-fel, utför du stegen enligt följande. Om den öppnas med det grafiska fönstret kan du hoppa över stegen.

**Fel**

![Skärm bild som visar en del av ett konsol fönster med ett fel meddelande.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Förväntade utdata**

![Skärm bild som visar YaST-kontroll Center med hög tillgänglighet och kluster markerat.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Om YaST2 inte öppnas med den grafiska vyn följer du stegen nedan.

Installera de paket som behövs. Du måste vara inloggad som användare rot och ha SMT konfigurerat för att ladda ned/installera paketen.

Om du vill installera paketen använder du YaST>program vara>Software Management>beroenden> alternativ "installera rekommenderade paket...". Följande skärm bild illustrerar de förväntade skärmarna.
>[!NOTE]
>Du måste utföra stegen på båda noderna, så att du kan komma åt den grafiska YaST2-vyn från båda noderna.

![Skärm bild som visar ett konsol fönster som visar YaST-kontroll centret.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Under beroenden väljer du skärm bilden "installera rekommenderade paket" ![ visar ett konsol fönster med installerade rekommenderade paket valda.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Granska ändringarna och tryck på OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Skärm bild av paket installation ![ visar ett konsol fönster som visar förloppet för installationen.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Klicka på Nästa

![Skärm bild visar ett konsol fönster med ett meddelande om att det är klart.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klicka på Slutför

Du måste också installera libqt4-och libyui-qt-paketen.
```
zypper -n install libqt4
```
![Skärm bild som visar ett konsol fönster som installerar libqt4-paketet.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![Skärm bild som visar ett konsol fönster som installerar libyui-qt-paketet. ](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
 ![ Skärm bild som visar ett konsol fönster som installerar libyui-qt-paketet, fortsatte.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
Yast2 ska kunna öppna den grafiska vyn nu som visas här.
![Skärm bild som visar YaST-kontroll Center med program vara och online-uppdatering vald.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scenario 3: alternativet YaST2 inte hög tillgänglighet
För att alternativet hög tillgänglighet ska synas i YaST2-kontroll centret måste du installera de ytterligare paketen.

Använda Yast2>program vara>program hantering>Välj följande mönster

- SAP HANA Server Base
- C/C++-kompilator och-verktyg
- Hög tillgänglighet
- SAP Application Server Base

Följande skärm bild visar stegen för att installera mönstren.

Använda YaST2 > program vara > program varu hantering

![Skärm bild som visar YaST-kontroll Center med program vara och online Update valt för att påbörja installationen.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Välj mönster

![Skärm bild som visar det första mönstret i objektet C/C++ compiler och tools. ](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
 ![ Skärm bild som visar hur du väljer det andra mönstret i objektet C/C++ compiler och tools.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klicka på **acceptera**

![Skärm bild som visar dialog rutan ändrade paket med paket som har ändrats för att lösa beroenden.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klicka på **Fortsätt**

![Skärm bild som visar sidan installations status.](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Klicka på **Nästa** när installationen är klar

![Skärm bilden visar installations rapporten.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scenario 4: HANA-installationen Miss lyckas med gcc sammansättnings fel
HANA-installationen Miss lyckas med följande fel.

![Skärm bilden visar ett fel meddelande om att operativ systemet inte är redo att utföra g c 5 5-sammansättningar.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

För att åtgärda problemet måste du installera bibliotek (libgcc_sl och libstdc + + 6) enligt följande.

![Skärm bild som visar ett konsol fönster som installerar nödvändiga bibliotek.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scenario 5: pacemaker-tjänsten Miss lyckas

Följande problem uppstod när pacemaker-tjänsten startades.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Åtgärda det genom att ta bort följande rad från filen */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Skärm bild som visar f s trimnings fil med värdet beständigt = sant som ska tas bort.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scenario 6: nod 2 kan inte ansluta till klustret

När du ansluter NOD2 till det befintliga klustret med hjälp av *ha-klustret-Join* -kommandot, har följande fel uppstått.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![Skärm bild som visar ett konsol fönster med fel meddelandet kan inte hämta S S-nycklar från en P-adress.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Åtgärda problemet genom att köra följande på båda noderna

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![Skärm bild som visar en del av ett konsol fönster som kör kommandot på den första noden.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![Skärm bild som visar en del av ett konsol fönster som kör kommandot på den andra noden.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Efter den föregående korrigeringen ska NOD2 läggas till i klustret

![Skärm bild som visar ett konsol fönster med kommandot slutfört ha-Cluster-Join.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Allmän dokumentation
Du hittar mer information om SUSE HA-installationen i följande artiklar: 

- [Optimerat scenario för SAP HANA SR-prestanda](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Lagring-baserad staket](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blogg – använda pacemaker-kluster för SAP HANA-del 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blogg – använda pacemaker-kluster för SAP HANA del 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
