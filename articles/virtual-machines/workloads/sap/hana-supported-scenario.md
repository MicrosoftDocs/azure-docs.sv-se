---
title: Scenarier som stöds för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Scenarier som stöds och deras arkitektur information för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a49c55da0bb8018b3faa7c6f70b02e79d9478603
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666688"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Scenarier som stöds för HANA-stora instanser
I den här artikeln beskrivs scenarier och arkitektur information som stöds för HANA Large instances (HLI).

>[!NOTE]
>Om det nödvändiga scenariot inte nämns i den här artikeln kan du kontakta Microsoft Service Management-teamet för att bedöma dina krav.
Innan du konfigurerar HLI-enheten verifierar du designen med SAP eller tjänst implementerings partnern.

## <a name="terms-and-definitions"></a>Villkor och definitioner
Nu ska vi förstå de termer och definitioner som används i den här artikeln:

- **Sid**: en system IDENTIFIERARE för Hana-systemet
- **HLI**: Hana stora instanser
- **Dr**: haveri beredskap
- **Normal Dr**: en systeminstallation med en dedikerad resurs för enbart Dr
- **Interpurpose Dr**: ett Dr-platssystem som är konfigurerat för att använda en icke-produktions miljö tillsammans med en produktions instans som har kon figurer ATS för en Dr-händelse 
- **Single-sid**: ett system med en instans installerad
- **Flera sid**: ett system med flera konfigurerade instanser. kallas även för en MCOS-miljö
- **HSR**: SAP HANA systemets replikering

## <a name="overview"></a>Översikt
HANA stora instanser har stöd för en rad arkitekturer som hjälper dig att uppnå dina affärs behov. Följande avsnitt beskriver arkitektur scenarier och konfigurations information. 

Den härledda arkitektur designen är helt ur ett infrastruktur perspektiv och du måste kontakta SAP eller implementerings partner för HANA-distributionen. Om dina scenarier inte visas i den här artikeln kan du kontakta Microsoft-konto-teamet för att granska arkitekturen och härleda en lösning.

> [!NOTE]
> Dessa arkitekturer är helt kompatibla med en anpassad data integrerings design (TDI) och stöds av SAP.

I den här artikeln beskrivs information om de två komponenterna i varje arkitektur som stöds:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Varje etablerad server levereras förkonfigurerad med uppsättningar med Ethernet-gränssnitt. De Ethernet-gränssnitt som kon figurer ATS på varje HLI-enhet kategoriseras i fyra typer:

- **A**: används för eller av klient åtkomst.
- **B**: används för nod-till-nod-kommunikation. Det här gränssnittet har kon figurer ATS på alla servrar (oavsett vilken topologi som begärs), men används endast för skalnings scenarier.
- **C**: används för nod-till-lagring-anslutning.
- **D**: används för STONITH-installation för nod-till-iSCSI-enhet. Det här gränssnittet är endast konfigurerat när en HSR-installation begärs.  

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Nod-till-nod|
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | STONITH |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Nod-till-nod|
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | STONITH |

Du väljer gränssnittet baserat på den topologi som är konfigurerad på HLI-enheten. Till exempel konfigureras gränssnittet "B" för kommunikation mellan noder, vilket är användbart när en skalbar topologi har kon figurer ATS. Det här gränssnittet används inte för en nod, skala upp konfigurationer. Om du vill ha mer information om gränssnitts användningen granskar du dina nödvändiga scenarier (senare i den här artikeln). 

Om det behövs kan du definiera ytterligare NIC-kort på egen hand. Konfigurationer av befintliga nätverkskort *kan dock inte* ändras.

>[!NOTE]
>Du kan hitta ytterligare gränssnitt som är fysiska gränssnitt eller kopplingar. Du bör endast tänka på de tidigare nämnda gränssnitten för ditt användnings fall. Andra kan ignoreras.

Distributionen av enheter med två tilldelade IP-adresser bör se ut så här:

- Ethernet "A" ska ha en tilldelad IP-adress som ligger inom adress intervallet för serverns IP-adresspool som du skickade till Microsoft. Den här IP-adressen bör ligga kvar i */etc/hosts* -katalogen för operativ systemet.

- Ethernet "C" ska ha en tilldelad IP-adress som används för kommunikation till NFS. Den här adressen behöver *inte* underhållas i katalogen *osv/hosts* för att tillåta instans-till-instans-trafik i klienten.

För HANA-systemreplikering eller HANA-utskalning av distribution är en blads konfiguration med två tilldelade IP-adresser inte lämplig. Kontakta SAP HANA på Azure Service Management om du bara har två tilldelade IP-adresser och du vill distribuera en sådan konfiguration. De kan tilldela en tredje IP-adress i ett tredje VLAN. Följande användnings regler gäller för HANA Large instance-enheter med tre tilldelade IP-adresser på tre NIC-portar:

- Ethernet "A" ska ha en tilldelad IP-adress som ligger utanför adress intervallet för serverns IP-adresspool som du skickade till Microsoft. Den här IP-adressen ska inte behållas i katalogen *osv/hosts* i operativ systemet.

- Ethernet "B" bör endast upprätthållas i katalogen för *etc/hosts* för kommunikation mellan olika instanser. Det här är IP-adresserna som ska upprätthållas vid skalbara HANA-konfigurationer som de IP-adresser som HANA använder för konfiguration mellan noder.

- Ethernet "C" ska ha en tilldelad IP-adress som används för kommunikation till NFS-lagring. Den här typen av adress ska inte behållas i katalogen för *etc/hosts* .

- Ethernet "D" ska användas enbart för åtkomst till STONITH-enheter för pacemaker. Det här gränssnittet krävs när du konfigurerar HANA-systemreplikering och vill uppnå automatisk redundans för operativ systemet med hjälp av en SBD-baserad enhet.


### <a name="storage"></a>Storage
Lagringen är förkonfigurerad baserat på den begärda topologin. Volym storlekarna och monterings punkterna varierar beroende på antalet servrar, antalet SKU: er och den konfigurerade topologin. Om du vill ha mer information granskar du dina nödvändiga scenarier (senare i den här artikeln). Om du behöver mer lagrings utrymme kan du köpa det i steg om 1 TB.

>[!NOTE]
>Monterings punkten/usr/SAP/ \<SID> är en symbolisk länk till/Hana/Shared-monterings punkten.


## <a name="supported-scenarios"></a>Scenarier som stöds

Arkitektur diagrammen i följande avsnitt använder följande format:

[![Tabell över arkitektur diagram](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Här följer de scenarier som stöds:

* En nod med en SID
* MCOS för enskild nod
* Enkel nod med DR (normal)
* Enkel nod med DR (flera syften)
* HSR med STONITH
* HSR med DR (normal/multisyften) 
* Automatisk redundans för värd (1 + 1) 
* Skala ut med vänte läge
* Skala ut utan vänte läge
* Skala ut med DR

## <a name="single-node-with-one-sid"></a>En nod med en SID

Den här topologin har stöd för en nod i en skalnings konfiguration med en SID.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![En nod med en SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|/hana/shared/SID | HANA-installation | 
|/hana/data/SID/mnt00001 | Installation av datafiler | 
|/hana/log/SID/mnt00001 | Logga filer-installation | 
|/hana/logbackups/SID | Gör om loggar |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.

## <a name="single-node-mcos"></a>MCOS för enskild nod

Den här topologin har stöd för en nod i en skalbar konfiguration med flera sid: er.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![MCOS för enskild nod](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|/hana/shared/SID1 | HANA-installation för SID1 | 
|/hana/data/SID1/mnt00001 | Installation av datafiler för SID1 | 
|/hana/log/SID1/mnt00001 | Logg fils installation för SID1 | 
|/hana/logbackups/SID1 | Gör om loggar för SID1 |
|/hana/shared/SID2 | HANA-installation för SID2 | 
|/hana/data/SID2/mnt00001 | Installation av datafiler för SID2 | 
|/hana/log/SID2/mnt00001 | Logg fils installation för SID2 | 
|/hana/logbackups/SID2 | Gör om loggar för SID2 |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- Distribution av volym storlek baseras på databasens storlek i minnet. Information om vilka databas storlekar i minnet som stöds i en miljö med flera-SID finns i [Översikt och arkitektur](./hana-overview-architecture.md).

## <a name="single-node-with-dr-using-storage-replication"></a>Enkel nod med DR med Storage Replication
 
Den här topologin har stöd för en nod i en skalbar konfiguration med en eller flera sid: er, med Storage-baserad replikering till DR-platsen för ett primärt SID. I diagrammet visas bara ett enda-SID-system på den primära platsen, men MCOS-system stöds också.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Enkel nod med DR med Storage Replication](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|/hana/shared/SID | HANA-installation för SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för SID | 
|/hana/logbackups/SID | Gör om loggar för SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- För MCOS: distribution av volym storlek baseras på databasens storlek i minnet. Information om vilka databas storlekar i minnet som stöds i en miljö med flera-SID finns i [Översikt och arkitektur](./hana-overview-architecture.md).
- På DR-platsen: volymerna och monterings punkterna konfigureras (markerade som "krävs för HANA-installation") för produktion HANA-instansen installation på DR HLI-enheten. 
- På DR-platsen: replikeras data, logg säkerhets kopior och delade volymer (markerade som "lagrings replikering") via ögonblicks bild från produktions platsen. Dessa volymer monteras endast under redundans. Mer information finns i [förfarandet vid haveri beredskap](./hana-overview-high-availability-disaster-recovery.md).
- Start volymen för *SKU-typ I klass* replikeras till Dr-noden.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Enkel nod med DR (flera syften) med Storage Replication
 
Den här topologin har stöd för en nod i en skalbar konfiguration med en eller flera sid: er, med Storage-baserad replikering till DR-platsen för ett primärt SID. I diagrammet visas bara ett enskilt-SID-system på den primära platsen, men MCOS-system (Multi-SID) stöds också. På DR-platsen används HLI-enheten för instansen frågor och svar medan produktions åtgärderna körs från den primära platsen. Vid DR-redundans (eller redundanstestning) tas frågor och svar-instansen på DR-platsen ned.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Enkel nod med DR (flera syften) med Storage Replication](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära platsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På DR-webbplatsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/shared/QA-SID | HANA-installation för frågor och svar-SID | 
|/hana/data/QA-SID/mnt00001 | Installation av datafiler för frågor och svar-SID | 
|/hana/log/QA-SID/mnt00001 | Loggfil installation för frågor och svar-SID |
|/hana/logbackups/QA-SID | Gör om loggar för säkerhets frågor och svar |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- För MCOS: distribution av volym storlek baseras på databasens storlek i minnet. Information om vilka databas storlekar i minnet som stöds i en miljö med flera-SID finns i [Översikt och arkitektur](./hana-overview-architecture.md).
- På DR-platsen: volymerna och monterings punkterna konfigureras (markerade som "krävs för HANA-installation") för produktion HANA-instansen installation på DR HLI-enheten. 
- På DR-platsen: replikeras data, logg säkerhets kopior och delade volymer (markerade som "lagrings replikering") via ögonblicks bild från produktions platsen. Dessa volymer monteras endast under redundans. Mer information finns i [förfarandet vid haveri beredskap](./hana-overview-high-availability-disaster-recovery.md). 
- På DR-platsen: konfigureras data, logg säkerhets kopior, logg och delade volymer för frågor och svar (markerade som "frågor och svar om instans installation") för instans installationen av frågor och svar.
- Start volymen för *SKU-typ I klass* replikeras till Dr-noden.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR med STONITH för hög tillgänglighet
 
Den här topologin har stöd för två noder för konfigurationen av HANA-systemreplikering. Den här konfigurationen stöds bara för enstaka HANA-instanser på en nod. Det innebär att MCOS-scenarier *inte* stöds.

> [!NOTE]
> Från och med december 2019 stöds den här arkitekturen bara för operativ systemet SUSE.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![HSR med STONITH för hög tillgänglighet](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Används för STONITH |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Används för STONITH |

### <a name="storage"></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På den sekundära noden**|
|/hana/shared/SID | HANA-installation för sekundär-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för sekundärt SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för sekundärt SID | 
|/hana/logbackups/SID | Gör om loggar för sekundärt SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- För MCOS: distribution av volym storlek baseras på databasens storlek i minnet. Information om vilka databas storlekar i minnet som stöds i en miljö med flera-SID finns i [Översikt och arkitektur](./hana-overview-architecture.md).
- STONITH: en SBD har kon figurer ATS för installationen av STONITH. Användningen av STONITH är dock valfri.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Hög tillgänglighet med HSR och DR med Storage Replication
 
Den här topologin har stöd för två noder för konfigurationen av HANA-systemreplikering. Både normal och DRs stöds. Dessa konfigurationer stöds endast för enstaka HANA-instanser på en nod. Det innebär att MCOS-scenarier *inte* stöds med de här konfigurationerna.

I diagrammet illustreras ett scenario för flera syften på DR-platsen, där HLI-enheten används för instansen frågor och svar medan produktions åtgärderna körs från den primära platsen. Vid DR-redundans (eller redundanstestning) tas frågor och svar-instansen på DR-platsen ned. 

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Hög tillgänglighet med HSR och DR med Storage Replication](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Används för STONITH |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Används för STONITH |

### <a name="storage"></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära noden på den primära platsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På den sekundära noden på den primära platsen**|
|/hana/shared/SID | HANA-installation för sekundär-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för sekundärt SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för sekundärt SID | 
|/hana/logbackups/SID | Gör om loggar för sekundärt SID |
|**På DR-webbplatsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/shared/QA-SID | HANA-installation för frågor och svar-SID | 
|/hana/data/QA-SID/mnt00001 | Installation av datafiler för frågor och svar-SID | 
|/hana/log/QA-SID/mnt00001 | Loggfil installation för frågor och svar-SID |
|/hana/logbackups/QA-SID | Gör om loggar för säkerhets frågor och svar |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- För MCOS: distribution av volym storlek baseras på databasens storlek i minnet. Information om vilka databas storlekar i minnet som stöds i en miljö med flera-SID finns i [Översikt och arkitektur](./hana-overview-architecture.md).
- STONITH: en SBD har kon figurer ATS för installationen av STONITH. Användningen av STONITH är dock valfri.
- På DR-platsen: *två uppsättningar lagrings volymer krävs* för replikering av primära och sekundära noder.
- På DR-platsen: volymerna och monterings punkterna konfigureras (markerade som "krävs för HANA-installation") för produktion HANA-instansen installation på DR HLI-enheten. 
- På DR-platsen: replikeras data, logg säkerhets kopior och delade volymer (markerade som "lagrings replikering") via ögonblicks bild från produktions platsen. Dessa volymer monteras endast under redundans. Mer information finns i [förfarandet vid haveri beredskap](./hana-overview-high-availability-disaster-recovery.md). 
- På DR-platsen: konfigureras data, logg säkerhets kopior, logg och delade volymer för frågor och svar (markerade som "frågor och svar om instans installation") för instans installationen av frågor och svar.
- Start volymen för *SKU-typ I klass* replikeras till Dr-noden.


## <a name="host-auto-failover-11"></a>Automatisk redundans för värd (1 + 1)
 
Den här topologin har stöd för två noder i en konfiguration för automatisk redundans för värd. Det finns en nod med en huvud-eller arbets roll och en annan som vänte läge. *SAP stöder bara det här scenariot för S/4 HANA.* Mer information finns i [oss note 2408419-SAP S/4HANA-stöd för flera noder](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Automatisk redundans för värd (1 + 1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet&quot;></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Nod-till-nod-kommunikation |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name=&quot;storage&quot;></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På huvud-och vänte noder**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |



### <a name=&quot;key-considerations&quot;></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- I vänte läge: volymerna och monterings punkterna konfigureras (markerade som &quot;krävs för HANA-installation") för HANA-instansen-installationen på vänte läges enheten.
 

## <a name="scale-out-with-standby"></a>Skala ut med vänte läge
 
Den här topologin stöder flera noder i en skalbar konfiguration. Det finns en nod med en huvud roll, en eller flera noder med en arbets roll och en eller flera noder som vänte läge. Det kan dock bara finnas en huvud nod vid en enskild tidpunkt.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Skala ut med vänte läge](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Nod-till-nod-kommunikation |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På huvud-, arbets-och vänte noder**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |


## <a name="scale-out-without-standby"></a>Skala ut utan vänte läge
 
Den här topologin stöder flera noder i en skalbar konfiguration. Det finns en nod med en huvud roll och en eller flera noder med en arbets roll. Det kan dock bara finnas en huvud nod vid en enskild tidpunkt.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Skala ut utan vänte läge](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Nod-till-nod-kommunikation |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På Master-och Worker-noderna**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Skala ut med DR med hjälp av Storage Replication
 
Den här topologin stöder flera noder i en skalbarhet med en DR. Både normal och DRs stöds. I diagrammet visas endast det enda syftet med DR. Du kan begära den här topologin med eller utan noden vänte läge.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Skala ut med DR med hjälp av Storage Replication](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet&quot;></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Nod-till-nod-kommunikation |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name=&quot;storage&quot;></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På DR-noden**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 


### <a name=&quot;key-considerations&quot;></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
-  På DR-platsen: volymerna och monterings punkterna konfigureras (markerade som &quot;krävs för HANA-installation") för produktion HANA-instansen installation på DR HLI-enheten. 
- På DR-platsen: replikeras data, logg säkerhets kopior och delade volymer (markerade som "lagrings replikering") via ögonblicks bild från produktions platsen. Dessa volymer monteras endast under redundans. Mer information finns i [förfarandet vid haveri beredskap](./hana-overview-high-availability-disaster-recovery.md). 
- Start volymen för *SKU-typ I klass* replikeras till Dr-noden.


## <a name="single-node-with-dr-using-hsr"></a>Enkel nod med DR med HSR
 
Den här topologin har stöd för en nod i en skalnings konfiguration med en SID, med HANA-systemreplikering till DR-platsen för ett primärt SID. I diagrammet visas bara ett enskilt-SID-system på den primära platsen, men MCOS-system (Multi-SID) stöds också.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Enkel nod med DR med HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI/HSR |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI/HSR |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande monterings punkter är förkonfigurerade på både HLI-enheter (primär och DR):

| Monterings punkt | Användningsfall | 
| --- | --- |
|/hana/shared/SID | HANA-installation för SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för SID | 
|/hana/logbackups/SID | Gör om loggar för SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- För MCOS: distribution av volym storlek baseras på databasens storlek i minnet. Information om vilka databas storlekar i minnet som stöds i en miljö med flera-SID finns i [Översikt och arkitektur](./hana-overview-architecture.md).
- Den primära noden synkroniseras med DR-noden med hjälp av HANA-systemreplikering. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) används för att länka ExpressRoute-kretsarna tillsammans för att skapa ett privat nätverk mellan dina regionala nätverk.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>Enkel nod HSR till DR (kostnads optimerad) 
 
 Den här topologin har stöd för en nod i en skalnings konfiguration med en SID, med HANA-systemreplikering till DR-platsen för ett primärt SID. I diagrammet visas bara ett enskilt-SID-system på den primära platsen, men MCOS-system (Multi-SID) stöds också. På DR-platsen används en HLI-enhet för instansen frågor och svar medan produktions åtgärderna körs från den primära platsen. Vid DR-redundans (eller redundanstestning) tas frågor och svar-instansen på DR-platsen ned.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Enkel nod HSR till DR (kostnads optimerad)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI/HSR |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI/HSR |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära platsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På DR-webbplatsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|/hana/shared/QA-SID | HANA-installation för frågor och svar-SID | 
|/hana/data/QA-SID/mnt00001 | Installation av datafiler för frågor och svar-SID | 
|/hana/log/QA-SID/mnt00001 | Loggfil installation för frågor och svar-SID |
|/hana/logbackups/QA-SID | Gör om loggar för säkerhets frågor och svar |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- För MCOS: distribution av volym storlek baseras på databasens storlek i minnet. Information om vilka databas storlekar i minnet som stöds i en miljö med flera-SID finns i [Översikt och arkitektur](./hana-overview-architecture.md).
- På DR-platsen: volymerna och monterings punkterna konfigureras (markeras som "produktions instans på DR-plats") för den produktion HANA-instansen som installeras på DR HLI-enheten. 
- På DR-platsen: konfigureras data, logg säkerhets kopior, logg och delade volymer för frågor och svar (markerade som "frågor och svar om instans installation") för instans installationen av frågor och svar.
- Den primära noden synkroniseras med DR-noden med hjälp av HANA-systemreplikering. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) används för att länka ExpressRoute-kretsarna tillsammans för att skapa ett privat nätverk mellan dina regionala nätverk.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Hög tillgänglighet och haveri beredskap med HSR 
 
 Den här topologin har stöd för två noder för konfigurationen av HANA-systemreplikering för de lokala regionerna hög tillgänglighet. Den tredje noden i DR-regionen synkroniseras med den primära platsen med hjälp av HSR (asynkront läge) för DR. 

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Hög tillgänglighet och haveri beredskap med HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet&quot;></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI/HSR |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI/HSR |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name=&quot;storage&quot;></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära platsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På DR-webbplatsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |


### <a name=&quot;key-considerations&quot;></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- På DR-platsen: volymerna och monterings punkterna har kon figurer ATS (markerade som &quot;PROD. DR-instans") för produktion HANA-instansen installation på DR HLI-enheten. 
- Noden för den primära platsen synkroniseras med DR-noden med hjälp av HANA-systemreplikering. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) används för att länka ExpressRoute-kretsarna tillsammans för att skapa ett privat nätverk mellan dina regionala nätverk.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Hög tillgänglighet och haveri beredskap med HSR (kostnads optimerad)
 
 Den här topologin har stöd för två noder för konfigurationen av HANA-systemreplikering för de lokala regionerna hög tillgänglighet. Den tredje noden i DR-regionen synkroniseras med den primära platsen med hjälp av HSR (asynkront läge) för DR, medan en annan instans (till exempel frågor och svar) redan körs från DR-noden. 

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Hög tillgänglighet och haveri beredskap med HSR (kostnads optimerad)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet&quot;></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI/HSR |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI/HSR |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name=&quot;storage&quot;></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära platsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På DR-webbplatsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|/hana/shared/QA-SID | HANA-installation för frågor och svar-SID | 
|/hana/data/QA-SID/mnt00001 | Installation av datafiler för frågor och svar-SID | 
|/hana/log/QA-SID/mnt00001 | Loggfil installation för frågor och svar-SID |
|/hana/logbackups/QA-SID | Gör om loggar för säkerhets frågor och svar |

### <a name=&quot;key-considerations&quot;></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- På DR-platsen: volymerna och monterings punkterna har kon figurer ATS (markerade som &quot;PROD. DR-instans") för produktion HANA-instansen installation på DR HLI-enheten. 
- På DR-platsen: konfigureras data, logg säkerhets kopior, logg och delade volymer för frågor och svar (markerade som "frågor och svar om instans installation") för instans installationen av frågor och svar.
- Noden för den primära platsen synkroniseras med DR-noden med hjälp av HANA-systemreplikering. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) används för att länka ExpressRoute-kretsarna tillsammans för att skapa ett privat nätverk mellan dina regionala nätverk.

## <a name="scale-out-with-dr-using-hsr"></a>Skala ut med DR med hjälp av HSR
 
Den här topologin stöder flera noder i en skalbarhet med en DR. Du kan begära den här topologin med eller utan noden vänte läge. Noden för den primära platsen synkroniseras med noden för DR-platser med hjälp av HANA-systemreplikering (asynkront läge).


### <a name="architecture-diagram"></a>Arkitekturdiagram  

[![Skala ut med Dr med hjälp av HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| Logiskt gränssnitt för nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | ETH0. Tenant | eno1. Tenant | Klient-till-HLI/HSR |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod-till-lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| A | TYP II | lokalt\<tenantNo> | team0. Tenant | Klient-till-HLI/HSR |
| B | TYP II | lokalt\<tenantNo+2> | team0. Tenant + 2 | Nod-till-nod-kommunikation |
| C | TYP II | lokalt\<tenantNo+1> | team0. klient organisation + 1 | Nod-till-lagring |
| D | TYP II | lokalt\<tenantNo+3> | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande monterings punkter är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På DR-noden**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktions-SID | 
|/hana/log/SID/mnt00001 | Logg fils installation för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- På DR-platsen: volymerna och monterings punkterna har kon figurer ATS för produktion HANA-instansen-installation på DR HLI-enheten. 
- Noden för den primära platsen synkroniseras med DR-noden med hjälp av HANA-systemreplikering. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) används för att länka ExpressRoute-kretsarna tillsammans för att skapa ett privat nätverk mellan dina regionala nätverk.


## <a name="next-steps"></a>Nästa steg
- [Infrastruktur och anslutningar](./hana-overview-infrastructure-connectivity.md) för Hana-stora instanser
- [Hög tillgänglighet och haveri beredskap](./hana-overview-high-availability-disaster-recovery.md) för Hana-stora instanser
