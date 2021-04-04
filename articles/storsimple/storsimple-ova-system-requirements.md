---
title: System krav för Microsoft Azure StorSimple virtuell matris
description: Lär dig mer om program-och nätverks kraven för din virtuella StorSimple-matris
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 5970e82619667a47ba160c84df2cdeb145b0dab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966181"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Systemkrav för StorSimple Virtual Array

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Översikt

I den här artikeln beskrivs viktiga system krav för din Microsoft Azure StorSimple virtuella matris och för de lagrings klienter som har åtkomst till matrisen. Vi rekommenderar att du läser igenom informationen noggrant innan du distribuerar StorSimple-systemet och sedan går tillbaka till den vid behov under distributionen och efterföljande åtgärder.

System kraven är:

* **Program varu krav för lagrings klienter** – beskriver de Virtualization-plattformar som stöds, webbläsare, iSCSI-initierare, SMB-klienter, lägsta krav på virtuell enhet och eventuella ytterligare krav för dessa operativ system.
* **Nätverks krav för StorSimple-enheten** – ger information om de portar som måste vara öppna i brand väggen för att tillåta iSCSI, moln eller hanterings trafik.

Informationen om system krav för StorSimple som publiceras i den här artikeln gäller endast StorSimple-virtuella matriser.

* För enheter med 8000-serien går du till [system krav för din enhet med StorSimple 8000-serien](./storsimple-8000-system-requirements.md).
* För enheter med 7000-serien går du till [system krav för din enhet med StorSimple 5000-7000-serien](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Programvarukrav
Program varu kraven innehåller information om de webbläsare som stöds, SMB-versioner, virtualiseringsplattformen och minimi kraven för virtuella enheter.

### <a name="supported-virtualization-platforms"></a>Plattformar som stöds
| **Hypervisor** | **Version** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 och senare |
| VMware ESXi |5,0, 5,5, 6,0 och 6,5. |

> [!IMPORTANT]
> Installera inte VMware-verktyg på din virtuella StorSimple-matris; Detta leder till en konfiguration som inte stöds.

### <a name="virtual-device-requirements"></a>Krav för virtuell enhet
| **Komponent** | **Krav** |
| --- | --- |
| Minsta antal virtuella processorer (kärnor) |4 |
| Minsta minne (RAM) |8 GB <br> För en fil server, 8 GB för mindre än 2 000 000 filer och 16 GB för 2-4 miljoner filer|
| Disk utrymme<sup>1</sup> |OS-disk – 80 GB <br></br>Data disk – 500 GB till 8 TB |
| Minsta antal nätverks gränssnitt |1 |
| Internet bandbredd<sup>2</sup> |Minsta bandbredd som krävs: 5 Mbit/s <br> Rekommenderad bandbredd: 100 Mbit/s <br> Hastigheten för data överföring skalas med Internet bandbredden. 100 GB data tar till exempel två dagar att överföra med 5 Mbit/s, vilket kan leda till säkerhets kopierings problem, eftersom dagliga säkerhets kopieringar inte skulle slutföras under en dag. Med en bandbredd på 100 Mbit/s kan 100 GB data överföras i 2,5 timmar.   |

<sup>1</sup> – tunn etablerad

<sup>2</sup> – nätverks kraven kan variera beroende på den dagliga data ändrings hastigheten. Om en enhet till exempel behöver säkerhetskopiera 10 GB eller fler ändringar under en dag, kan den dagliga säkerhets kopieringen över en anslutning på 5 Mbit/s ta upp till 4,25 timmar (om data inte kunde komprimeras eller dedupliceras).

### <a name="supported-web-browsers"></a>Webbläsare som stöds
| **Komponent** | **Version** | **Ytterligare krav/anteckningar** |
| --- | --- | --- |
| Microsoft Edge |Senaste version | |
| Internet Explorer |Senaste version |Testat med Internet Explorer 11 |
| Google Chrome |Senaste version |Testat med Chrome 46 |

### <a name="supported-storage-clients"></a>Lagrings klienter som stöds
Följande program varu krav gäller för iSCSI-initierare som har åtkomst till din virtuella StorSimple-matris (konfigurerad som en iSCSI-server).

| **Operativsystem som stöds** | **Version som krävs** | **Ytterligare krav/anteckningar** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |StorSimple kan skapa tunt etablerade och helt etablerade volymer. Det går inte att skapa delvis allokerade volymer. StorSimple iSCSI-volymer stöds endast för: <ul><li>Enkla volymer på Windows Basic-diskar.</li><li>Windows NTFS för formatering av en volym.</li> |

Följande program varu krav gäller för SMB-klienter som har åtkomst till din virtuella StorSimple-matris (konfigurerad som en fil Server).

| **SMB-version** |
| --- |
| SMB 2. x |
| SMB 3.0 |
| SMB 3,02 |

> [!IMPORTANT]
> Kopiera eller lagra inte filer som skyddas av Windows krypterande filsystem (EFS) till den virtuella StorSimple-arrayens fil server. Detta leder till en konfiguration som inte stöds.


### <a name="supported-storage-format"></a>Lagrings format som stöds
Endast Azure Block Blob-lagring stöds. Page blobbar stöds inte. Mer information [om block-blobar och Page blobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Nätverkskrav
I följande tabell visas de portar som måste öppnas i brand väggen för att tillåta iSCSI-, SMB-, moln-eller hanterings trafik. I den här tabellen avser *i* eller *inkommande* den riktning som inkommande klient begär åtkomst till din enhet. *Out* eller *utgående* avser i vilken riktning din StorSimple-enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

| **Port nr <sup>1</sup>** | **In eller ut** | **Port omfång** | **Obligatoriskt** | **Kommentarer** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Ut |WAN |Inga |Utgående port används för Internet åtkomst för att hämta uppdateringar. <br></br>Den utgående webbproxyn är användare konfigurerbar. |
| TCP 443 (HTTPS) |Ut |WAN |Ja |Utgående port används för att komma åt data i molnet. <br></br>Den utgående webbproxyn är användare konfigurerbar. |
| UDP 53 (DNS) |Ut |WAN |I vissa fall, Se kommentarer. |Den här porten krävs bara om du använder en Internetbaserad DNS-server. <br></br> Observera att om du distribuerar en fil server rekommenderar vi att du använder den lokala DNS-servern. |
| UDP 123 (NTP) |Ut |WAN |I vissa fall, Se kommentarer. |Den här porten krävs bara om du använder en Internetbaserad NTP-server.<br></br> Observera att om du distribuerar en fil server rekommenderar vi att du synkroniserar tid med Active Directory domän kontrol Lanterna. |
| TCP 80 (HTTP) |I |LAN |Ja |Det här är den inkommande porten för lokalt användar gränssnitt på StorSimple-enheten för lokal hantering. <br></br> Observera att åtkomst till det lokala användar gränssnittet över HTTP omdirigeras automatiskt till HTTPS. |
| TCP 443 (HTTPS) |I |LAN |Ja |Det här är den inkommande porten för lokalt användar gränssnitt på StorSimple-enheten för lokal hantering. |
| TCP 3260 (iSCSI) |I |LAN |Inga |Den här porten används för att få åtkomst till data via iSCSI. |

<sup>1</sup> inga inkommande portar måste öppnas på det offentliga Internet.

> [!IMPORTANT]
> Se till att brand väggen inte ändrar eller dekrypterar TLS-trafik mellan StorSimple-enheten och Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brand Väggs regler
Nätverks administratörer kan ofta konfigurera avancerade brand Väggs regler baserat på URL-mönster för att filtrera inkommande och utgående trafik. Den virtuella matrisen och tjänsten StorSimple Enhetshanteraren är beroende av andra Microsoft-program som Azure Service Bus, Azure Active Directory Access Control, lagrings konton och Microsoft Update-servrar. URL-mönstren som är kopplade till dessa program kan användas för att konfigurera brand Väggs regler. Det är viktigt att förstå att URL-mönstren som är kopplade till dessa program kan ändras. Detta kräver att nätverks administratören övervakar och uppdaterar brand Väggs regler för din StorSimple som och när det behövs. 

Vi rekommenderar att du ställer in brand Väggs regler för utgående trafik, baserat på StorSimple fasta IP-adresser, i de flesta fall. Du kan dock använda informationen nedan för att ange avancerade brand Väggs regler som behövs för att skapa säkra miljöer.

> [!NOTE]
> 
> * IP-adresserna för enheten (källa) ska alltid anges till alla molnbaserade nätverks gränssnitt. 
> * Mål-IP-adresser ska anges till [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| URL-mönster | Komponent/funktion |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|StorSimple Device Manager-tjänsten<br>Access Control Service<br>Azure Service Bus<br>Autentiseringstjänst|
| `http://*.backup.windowsazure.com` |Enhetsregistrering |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Återkallande av certifikat |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure Storage-konton och-övervakning |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update servrar<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Support paket |
| `https://*.data.microsoft.com` |Telemetri-tjänsten i Windows, se [uppdateringen för kund upplevelse och diagnostisk telemetri](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Nästa steg
* [Förbered portalen för att distribuera din virtuella StorSimple-matris](storsimple-virtual-array-deploy1-portal-prep.md)