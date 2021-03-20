---
title: Diagnostikverktyg för fel sökning av StorSimple 8000-enhet | Microsoft Docs
description: Den här artikeln innehåller en översikt över verktyget diagnostik och beskriver hur verktyget kan användas med en StorSimple-enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: bf0570cd62a2c329407cfb6dd087020f53cdc991
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954077"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Använd verktyget StorSimple Diagnostics för att felsöka enhets problem i 8000-serien

## <a name="overview"></a>Översikt

StorSimple Diagnostics-verktyget diagnostiserar problem som rör system-, prestanda-, nätverks-och maskin varu komponent hälsa för en StorSimple-enhet. Diagnostikverktyget kan användas i olika scenarier. Dessa scenarier innefattar arbets belastnings planering, distribution av en StorSimple-enhet, bedömning av nätverks miljön och fast ställande av prestanda för en fungerande enhet. Den här artikeln innehåller en översikt över verktyget diagnostik och beskriver hur verktyget kan användas med en StorSimple-enhet.

Diagnostikverktyget är främst avsett för StorSimple 8000-seriens lokala enheter (8100 och 8600).

## <a name="run-diagnostics-tool"></a>Kör diagnostik-verktyget

Det här verktyget kan köras via Windows PowerShell-gränssnittet på din StorSimple-enhet. Det finns två sätt att komma åt enhetens lokala gränssnitt:

* [Använd SparaTillFil för att ansluta till enhetens serie konsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* Fjärråtkomst till [verktyget via Windows PowerShell för StorSimple](storsimple-8000-remote-connect.md).

I den här artikeln förutsätter vi att du har anslutit till enhetens serie konsol via SparaTillFil.

#### <a name="to-run-the-diagnostics-tool"></a>Köra diagnostik-verktyget

När du har anslutit till Windows PowerShell-gränssnittet på enheten utför du följande steg för att köra cmdleten.
1. Logga in på enhetens serie konsol genom att följa stegen i [använda SparaTillFil för att ansluta till enhetens serie konsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Ange följande kommando:

    `Invoke-HcsDiagnostics`

    Om omfångs parametern inte anges kör cmdleten alla diagnostiska tester. Dessa tester omfattar system, maskin varu komponent hälsa, nätverk och prestanda. 
    
    Om du bara vill köra ett speciellt test anger du omfattnings parametern. Om du till exempel bara vill köra nätverks testet skriver du

    `Invoke-HcsDiagnostics -Scope Network`

3. Markera och kopiera utdata från fönstret SparaTillFil till en textfil för ytterligare analys.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scenarier för att använda verktyget diagnostik

Använd diagnostikverktyget för att felsöka nätverks-, prestanda-, system-och maskin varu hälsan i systemet. Här följer några möjliga scenarier:

* **Enheten offline** – din StorSimple 8000-serie enhet är offline. Men från Windows PowerShell-gränssnittet verkar det som om båda styrenheterna är igång.
    * Du kan använda det här verktyget för att fastställa nätverks tillstånd.
         
         > [!NOTE]
         > Använd inte det här verktyget för att utvärdera prestanda-och nätverks inställningar på en enhet innan registreringen (eller konfigureras via installations guiden). En giltig IP-adress tilldelas enheten under installations guiden och registreringen. Du kan köra den här cmdleten på en enhet som inte är registrerad för maskin varu hälsa och system. Använd omfattnings parametern, till exempel:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problem med beständig enhet** – du har problem med enheten som verkar vara kvar. Till exempel fungerar inte registreringen. Det kan också uppstå enhets problem när enheten har registrerats och fungerar en stund.
    * I det här fallet ska du använda det här verktyget för preliminär fel sökning innan du loggar en tjänstbegäran med Microsoft Support. Vi rekommenderar att du kör det här verktyget och fångar in utdata från det här verktyget. Du kan sedan ange dessa utdata som stöd för att påskynda fel sökningen.
    * Om det finns maskin varu komponenter eller kluster fel, bör du logga in på en Supportbegäran.

* **Låg enhets prestanda** – din StorSimple-enhet är långsam.
    * I det här fallet kör du denna cmdlet med omfattnings parametern inställd på prestanda. Analysera utdata. Du får en svars tids fördröjning för molnet. Använd de rapporterade svars tiderna som maximalt mål som kan nås, faktor i vissa kostnader för intern data bearbetning och distribuera sedan arbets belastningarna i systemet. Om du vill ha mer information går du till [Använd Nätverks testet för att felsöka enhetens prestanda](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Test av diagnostik och exempel på utdata

### <a name="hardware-test"></a>Maskin varu test

Det här testet bestämmer status för maskin varu komponenter, den inbyggda program varan för USM POLICYEGENSKAPER och diskens inbyggda program vara som körs på systemet.

* Maskin varu komponenter som rapporteras är de komponenter som inte kunde utföra testet eller som inte finns i systemet.
* Versionerna av den inbyggda program varan och USM POLICYEGENSKAPER för diskarna rapporteras för styrenhet 0, styrenhet 1 och delade komponenter i systemet. En fullständig lista över maskin varu komponenter finns på:

    * [Komponenter i primär kabinett](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Komponenter i EBOD-kabinettet](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Om maskin varu testet rapporterar felaktiga komponenter loggar du in [en tjänstbegäran med Microsoft Support](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Exempel på utdata från maskin varu test på en 8100-enhet

Här är ett exempel på utdata från en StorSimple 8100-enhet. EBOD-kabinettet finns inte i 8100-modell enheten. Därför rapporteras inte EBOD-styrenhetens komponenter.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>System test

Det här testet rapporterar system information, tillgängliga uppdateringar, kluster information och tjänst information för enheten.

* System informationen inkluderar modellen, enhetens serie nummer, tidszon, styrenhets status och den detaljerade program varu version som körs på systemet. Om du vill förstå de olika system parametrarna som rapporteras som utdata går du till [tolka system information](#appendix-interpreting-system-information).

* Uppdaterings tillgänglighet rapporterar om de vanliga och underhålls lägena är tillgängliga och deras associerade paket namn. Om `RegularUpdates` och `MaintenanceModeUpdates` är `false` , indikerar detta att uppdateringarna inte är tillgängliga. Enheten är uppdaterad.
* Kluster informationen innehåller information om olika logiska komponenter i alla HCS UPPDATERINGSKLIENTEN-kluster grupper och deras respektive status. Om du ser en frånkopplad kluster grupp i det här avsnittet av rapporten [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Tjänst informationen innehåller namn och status för alla HCS UPPDATERINGSKLIENTEN-och CiS-tjänster som körs på enheten. Den här informationen är användbar för Microsoft Support vid fel sökning av enhets problemet.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Exempel på utdata från systemtesten som körs på en 8100-enhet

Här är ett exempel på utdata från system testet som körs på en 8100-enhet.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Nätverks test

Det här testet kontrollerar status för nätverks gränssnitt, portar, DNS-och NTP-serverrollen, TLS/SSL-certifikat, autentiseringsuppgifter för lagrings konto, anslutning till uppdaterings servrar och webbproxy-anslutning på din StorSimple-enhet.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Exempel på utdata från nätverks test när endast DATA0 har Aktiver ATS

Här är ett exempel på utdata från 8100-enheten. Du kan se i utdata som:
* Endast nätverks gränssnitten DATA 0 och DATA 1 är aktiverade och konfigurerade.
* DATA 2-5 är inte aktiverade i portalen.
* DNS-serverkonfigurationen är giltig och enheten kan ansluta via DNS-servern.
* NTP-serverns anslutning är också bra.
* Portarna 80 och 443 är öppna. Port 9354 är dock blockerad. Baserat på [systemets nätverks krav](./storsimple-8000-system-requirements.md)måste du öppna porten för Service Bus-kommunikationen.
* TLS/SSL-certifieringen är giltig.
* Enheten kan ansluta till lagrings kontot: _myss8000storageacct_.
* Anslutningen till uppdaterings servrarna är giltig.
* Webbproxyn har inte kon figurer ATS på den här enheten.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Exempel på utdata från nätverks test när DATA0 och fil1 är aktiverade

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Prestandatest

Det här testet rapporterar moln prestanda via molnets Läs-och skriv fördröjningar för din enhet. Det här verktyget kan användas för att upprätta en bas linje för moln prestanda som du kan uppnå med StorSimple. Verktyget rapporterar högsta prestanda (bästa fall scenariot för Läs-och skriv fördröjning) som du kan hämta för anslutningen.

När verktyget rapporterar högsta prestanda som kan uppnås kan vi använda de rapporterade Läs-och skriv fördröjningarna som mål när du distribuerar arbets belastningarna.

Testet simulerar BLOB-storlekarna som är kopplade till de olika volym typerna på enheten. Regelbundna nivåer och säkerhets kopieringar av lokalt fästa volymer använder en BLOB-storlek på 64 KB. De skiktade volymerna med alternativet Arkiv markerat Använd 512 KB BLOB-data storlek. Om enheten har nivå-och lokalt fästa volymer konfigurerade, körs bara det test som motsvarar 64 KB BLOB-datastorlek.

Utför följande steg för att använda det här verktyget:

1.  Börja med att skapa en blandning av skiktade volymer och skiktade volymer med arkiverat alternativ markerat. Den här åtgärden säkerställer att verktyget kör testerna för både 64 KB-och 512 KB-BLOB-storlekar.

2. Kör cmdleten när du har skapat och konfigurerat volymerna. Ange:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Anteckna den Läs-och skriv fördröjning som rapporteras av verktyget. Det här testet kan ta flera minuter att köra innan det rapporterar resultatet.

4. Om tids fördröjningen för anslutningen är under det förväntade intervallet, kan svars tiderna som rapporteras av verktyget användas som maximalt mål när du distribuerar arbets belastningarna. Faktor i viss kostnad för intern data bearbetning.

    Om den Läs-och skriv fördröjning som rapporteras av diagnostikverktyget är hög:

    1. Konfigurera Lagringsanalys för BLOB Services och analysera utdata för att förstå fördröjningarna för Azure Storage-kontot. Detaljerade anvisningar finns i [Aktivera och konfigurera Lagringsanalys](../storage/blobs/monitor-blob-storage.md). Om de fördröjningarna också är höga och jämförbara med de nummer som du fick från StorSimple-diagnostikverktyget måste du logga en tjänstbegäran med Azure Storage.

    2. Om lagrings konto fördröjningen är låg kontaktar du nätverks administratören och undersöker eventuella latens problem i nätverket.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Exempel på utdata från prestanda test på en 8100-enhet

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Bilaga: tolkning av system information

Här är en tabell som beskriver de olika Windows PowerShell-parametrarna i system information Map till. 

| PowerShell-parameter    | Beskrivning  |
|-------------------------|------------------|
| Instance ID             | Varje styrenhet har en unik identifierare eller ett GUID som är associerat med den.|
| Name                    | Det egna namnet på enheten enligt konfigurationen i Azure Portal under enhets distributionen. Det egna standard namnet är enhetens serie nummer. |
| Modell                   | Modellen för din enhet med StorSimple 8000-serien. Modellen kan vara 8100 eller 8600.|
| Serienummer            | Enhetens serie nummer tilldelas till fabriken och är 15 tecken långt. Till exempel visar 8600-SHX0991003G44HT:<br> 8600 – är enhets modellen.<br>SHX – är webbplatsen för produktion.<br> 0991003 – är en enskild produkt. <br> G44HT – de sista 5 siffrorna ökar för att skapa unika serie nummer. Det får inte vara en sekventiell uppsättning.|
| TimeZone                | Enhetens tidszon enligt konfigurationen i Azure Portal under enhets distributionen.|
| CurrentController       | Den kontroll enhet som du är ansluten till via Windows PowerShell-gränssnittet på din StorSimple-enhet.|
| ActiveController        | Den kontrollant som är aktiv på enheten och som styr alla nätverks-och disk åtgärder. Detta kan vara styrenhet 0 eller styrenhet 1.  |
| Controller0Status       | Status för kontrollant 0 på enheten. Status för kontrollanten kan vara normal, i återställnings läge eller inte kan kontaktas.|
| Controller1Status       | Status för kontrollant 1 på enheten.  Status för kontrollanten kan vara normal, i återställnings läge eller inte kan kontaktas.|
| SystemMode              | Den övergripande statusen för din StorSimple-enhet. Enhetens status kan vara normal, underhåll eller inaktive rad (motsvarar inaktive rad i Azure Portal).|
| FriendlySoftwareVersion | Den egna strängen som motsvarar enhetens program varu version. För ett system som kör uppdatering 4 skulle den egna program varu versionen vara StorSimple 8000-serien Update 4,0.|
| HcsSoftwareVersion      | HCS UPPDATERINGSKLIENTEN program varu version som körs på enheten. Till exempel är HCS UPPDATERINGSKLIENTEN-programvaran som motsvarar StorSimple 8000-serien uppdatering 4,0 6.3.9600.17820. |
| API version              | Program varu versionen av Windows PowerShell API för HCS UPPDATERINGSKLIENTEN-enheten.|
| VhdVersion              | Den program varu version av fabriks avbildningen som enheten levererades med. Om du återställer enheten till fabriks inställningarna körs den här program versionen.|
| OSVersion               | Program varu versionen av Windows Server-operativsystemet som körs på enheten. StorSimple-enheten baseras på Windows Server 2012 R2 som motsvarar 6.3.9600.|
| CisAgentVersion         | Versionen för din CIS-agent som körs på din StorSimple-enhet. Den här agenten hjälper till att kommunicera med StorSimple Manager tjänsten som körs i Azure.|
| MdsAgentVersion         | Den version som motsvarar MDS-agenten som körs på din StorSimple-enhet. Den här agenten flyttar data till övervaknings-och diagnos tjänsten (MDS).|
| Lsisas2Version          | Den version som motsvarar LSI-drivrutinerna på din StorSimple-enhet.|
| Kapacitet                | Enhetens totala kapacitet i byte.|
| RemoteManagementMode    | Anger om enheten kan fjärrhanteras via Windows PowerShell-gränssnittet. |
| FipsMode                | Anger om FIPS-läget (USA Federal Information Processing Standard) är aktiverat på enheten. FIPS 140-standarden definierar kryptografiska algoritmer som godkänns för användning av amerikanska federala myndighets dator system för skydd av känsliga data. För enheter som kör uppdatering 4 eller senare är FIPS-läge aktiverat som standard. |

## <a name="next-steps"></a>Nästa steg

* Lär dig [syntaxen för Invoke-HcsDiagnostics-cmdleten](/previous-versions/windows/powershell-scripting/mt795371(v=wps.630)).

* Läs mer om hur du [felsöker distributions problem](./storsimple-8000-troubleshoot-deployment.md) på din StorSimple-enhet.