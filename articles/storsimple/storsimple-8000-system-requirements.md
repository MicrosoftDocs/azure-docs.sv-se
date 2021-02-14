---
title: System krav för StorSimple 8000-serien | Microsoft Docs
description: Beskriver program vara, nätverk och krav för hög tillgänglighet och bästa praxis för en Microsoft Azure StorSimple lösning.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: fa7616a740e8246fa08e950494428095f41ee404
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382862"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>StorSimple 8000-seriens program vara, hög tillgänglighet och nätverks krav

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Välkommen till Microsoft Azure StorSimple. I den här artikeln beskrivs viktiga system krav och metod tips för din StorSimple-enhet och för de lagrings klienter som använder enheten. Vi rekommenderar att du läser igenom informationen noggrant innan du distribuerar StorSimple-systemet och sedan går tillbaka till den vid behov under distributionen och efterföljande åtgärder.

System kraven är:

* **Program varu krav för lagrings klienter** – beskriver de operativ system som stöds och eventuella ytterligare krav för dessa operativ system.
* **Nätverks krav för StorSimple-enheten** – ger information om de portar som måste vara öppna i brand väggen för att tillåta iSCSI, moln eller hanterings trafik.
* **Krav för hög tillgänglighet för StorSimple** – beskriver krav på hög tillgänglighet och bästa praxis för din StorSimple-enhet och värddator.

## <a name="software-requirements-for-storage-clients"></a>Program varu krav för lagrings klienter

Följande program varu krav gäller för de lagrings klienter som har åtkomst till din StorSimple-enhet.

| Operativsystem som stöds | Version som krävs | Ytterligare krav/anteckningar |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |StorSimple iSCSI-volymer stöds endast för användning på följande Windows-disk typer:<ul><li>Enkel volym på standard disk</li><li>Enkel och speglad volym på dynamisk disk</li></ul>Endast de iSCSI-initierare för program vara som finns inbyggt i operativ systemet stöds. ISCSI-initierare för maskin vara stöds inte.<br></br>Windows Server 2012 och 2016 tunn allokering och ODX-funktioner stöds om du använder en StorSimple iSCSI-volym.<br><br>StorSimple kan skapa tunt etablerade och helt etablerade volymer. Det går inte att skapa delvis allokerade volymer.<br><br>Det kan ta lång tid att formatera om en tunt allokerad volym. Vi rekommenderar att du tar bort volymen och sedan skapar en ny i stället för att formatera om. Men om du ändå föredrar att formatera om en volym:<ul><li>Kör följande kommando innan omformateringen för att undvika förskjutningar i utrymmesåtertagningen:  <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>När formateringen är klar använder du följande kommando för att återaktivera utrymmesåtertagningen: <br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Använd snabb korrigeringen för Windows Server 2012 enligt beskrivningen i [KB 2878635](https://support.microsoft.com/kb/2870270) till Windows Server-datorn.</li></ul></li></ul></ul> Om du konfigurerar StorSimple Snapshot Manager eller StorSimple Adapter för SharePoint kan du gå till [program varu krav för valfria komponenter](#software-requirements-for-optional-components). <br> Om din Windows Server-klient använder SMB-protokollet för att komma åt StorSimple-enheten går du till [prestanda justering för SMB-filservrar](/windows-server/administration/performance-tuning/role/file-server/smb-file-server) för att få vägledning om ökande parallell bearbetning.|
| VMware ESX |5,5 och 6,0 |Stöds med VMware vSphere som iSCSI-klient. Funktionen VAAI-block stöds med VMware vSphere på StorSimple-enheter. |
| Linux-RHEL/CentOS |5, 6 och 7 |Stöd för Linux iSCSI-klienter med Open-iSCSI Initiator version 5, 6 och 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX stöds för närvarande inte med StorSimple.


## <a name="software-requirements-for-optional-components"></a>Program varu krav för valfria komponenter

Följande program varu krav gäller för de valfria StorSimple-komponenterna (StorSimple Snapshot Manager och StorSimple Adapter för SharePoint).

| Komponent | Värd plattform | Ytterligare krav/anteckningar |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Användning av StorSimple Snapshot Manager på Windows Server krävs för säkerhets kopiering/återställning av speglade dynamiska diskar och för alla programkonsekventa säkerhets kopieringar.<br> StorSimple Snapshot Manager stöds endast på Windows Server 2008 R2 SP1 (64-bitars), Windows Server 2012 R2 och Windows Server 2012.<ul><li>Om du använder Window Server 2012 måste du installera .NET 3.5 – 4.5 innan du installerar StorSimple Snapshot Manager.</li><li>Om du använder Windows Server 2008 R2 SP1 måste du installera Windows Management Framework 3,0 innan du installerar StorSimple Snapshot Manager.</li></ul> |
| StorSimple-adapter för SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>StorSimple-Adapter för SharePoint stöds endast på SharePoint 2010 och SharePoint 2013.</li><li>RBS kräver SQL Server Enterprise Edition, version 2008 R2 eller 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Nätverkskrav för din StorSimple-enhet

Din StorSimple-enhet är en låst enhet. Portar måste dock öppnas i brand väggen för att tillåta iSCSI-, moln-och hanterings trafik. I följande tabell visas de portar som måste öppnas i brand väggen. I den här tabellen avser *i* eller *inkommande* den riktning som inkommande klient begär åtkomst till din enhet. *Out* eller *utgående* avser i vilken riktning din StorSimple-enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

| Port nr<sup>1, 2</sup> | In eller ut | Port omfång | Obligatorisk | Kommentarer |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Ut |WAN |Inga |<ul><li>Utgående port används för Internet åtkomst för att hämta uppdateringar.</li><li>Den utgående webbproxyn är användare konfigurerbar.</li><li>Om du vill tillåta system uppdateringar måste den här porten också vara öppen för styrenhetens fasta IP-adresser.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Ut |WAN |Ja |<ul><li>Utgående port används för att komma åt data i molnet.</li><li>Den utgående webbproxyn är användare konfigurerbar.</li><li>Om du vill tillåta system uppdateringar måste den här porten också vara öppen för styrenhetens fasta IP-adresser.</li><li>Den här porten används också både på styrenheten för skräp insamling.</li></ul> |
| UDP 53 (DNS) |Ut |WAN |I vissa fall, Se kommentarer. |Den här porten krävs bara om du använder en Internetbaserad DNS-server. |
| UDP 123 (NTP) |Ut |WAN |I vissa fall, Se kommentarer. |Den här porten krävs bara om du använder en Internetbaserad NTP-server. |
| TCP 9354 |Ut |WAN |Ja |Den utgående porten används av StorSimple-enheten för att kommunicera med StorSimple Enhetshanteraren-tjänsten. |
| 3260 (iSCSI) |I |LAN |Inga |Den här porten används för att få åtkomst till data via iSCSI. |
| 5985 |I |LAN |Inga |Inkommande port används av StorSimple Snapshot Manager för att kommunicera med StorSimple-enheten.<br>Den här porten används också när du fjärransluter till Windows PowerShell för StorSimple över HTTP. |
| 5986 |I |LAN |Inga |Den här porten används vid fjärr anslutning till Windows PowerShell för StorSimple över HTTPS. |

<sup>1</sup> inga inkommande portar måste öppnas på det offentliga Internet.

<sup>2</sup> om flera portar har en gateway-konfiguration, bestäms den utgående trafik trafiken baserat på den port som används för att dirigera trafik som beskrivs i [port routning](#routing-metric)nedan.

<sup>3</sup> de fasta IP-adresserna på StorSimple-enheten måste dirigeras och kunna ansluta till Internet direkt eller via den konfigurerade webbproxyn. De fasta IP-adresserna används för att betjäna uppdateringar av enheten och skräp insamling. Om enhetens styrenheter inte kan ansluta till Internet via de fasta IP-adresserna kan du inte uppdatera din StorSimple-enhet och skräp insamling fungerar inte som den ska.

> [!IMPORTANT]
> Se till att brand väggen inte ändrar eller dekrypterar TLS-trafik mellan StorSimple-enheten och Azure.


### <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brand Väggs regler

Nätverks administratörer kan ofta konfigurera avancerade brand Väggs regler baserat på URL-mönster för att filtrera inkommande och utgående trafik. Din StorSimple-enhet och tjänsten StorSimple Enhetshanteraren är beroende av andra Microsoft-program som Azure Service Bus, Azure Active Directory Access Control, lagrings konton och Microsoft Update-servrar. URL-mönstren som är kopplade till dessa program kan användas för att konfigurera brand Väggs regler. Det är viktigt att förstå att URL-mönstren som är kopplade till dessa program kan ändras. Detta kräver att nätverks administratören övervakar och uppdaterar brand Väggs regler för din StorSimple som och när det behövs.

Vi rekommenderar att du ställer in brand Väggs regler för utgående trafik, baserat på StorSimple fasta IP-adresser, i de flesta fall. Du kan dock använda informationen nedan för att ange avancerade brand Väggs regler som behövs för att skapa säkra miljöer.

> [!NOTE]
> IP-adresserna för enheten (källa) ska alltid anges till alla aktiverade nätverks gränssnitt. Mål-IP-adresser ska anges till [IP-intervall för Azure-datacenter](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>URL-mönster för Azure Portal

| URL-mönster | Komponent/funktion | Enhets-IP |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple Device Manager-tjänsten<br>Access Control Service<br>Azure Service Bus<br>Autentiseringstjänst |Molnbaserade nätverks gränssnitt |
| `https://*.backup.windowsazure.com` |Enhetsregistrering |Endast DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Återkallande av certifikat |Molnbaserade nätverks gränssnitt |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure Storage-konton och-övervakning |Molnbaserade nätverks gränssnitt |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update servrar<br> |Kontrollant endast fasta IP-adresser |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Kontrollant endast fasta IP-adresser |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Support paket |Molnbaserade nätverks gränssnitt |

#### <a name="url-patterns-for-azure-government-portal"></a>URL-mönster för Azure Government Portal

| URL-mönster | Komponent/funktion | Enhets-IP |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple Device Manager-tjänsten<br>Access Control Service<br>Azure Service Bus<br>Autentiseringstjänst |Molnbaserade nätverks gränssnitt |
| `https://*.backup.windowsazure.us` |Enhetsregistrering |Endast DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Återkallande av certifikat |Molnbaserade nätverks gränssnitt |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure Storage-konton och-övervakning |Molnbaserade nätverks gränssnitt |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update servrar<br> |Kontrollant endast fasta IP-adresser |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Kontrollant endast fasta IP-adresser |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Support paket |Molnbaserade nätverks gränssnitt |

### <a name="routing-metric"></a>Flödes mått

Ett Dirigerings mått är kopplat till gränssnitten och den gateway som dirigerar data till de angivna nätverken. Routnings mått används av routningsprotokollet för att beräkna den bästa sökvägen till ett specifikt mål, om det lär sig att flera sökvägar finns på samma mål. Ju lägre vägens mått, desto högre prioritet.

Om flera nätverks gränssnitt och gatewayer har kon figurer ATS för kanal trafik i kontexten för StorSimple, kommer routing-måtten att tas i spel för att avgöra i vilken ordning gränssnitten ska användas. Dirigerings måtten kan inte ändras av användaren. Du kan dock använda `Get-HcsRoutingTable` cmdleten för att skriva ut routningstabellen (och mått) på din StorSimple-enhet. Mer information om Get-HcsRoutingTable-cmdlet vid [fel sökning av StorSimple-distribution](./storsimple-8000-troubleshoot-deployment.md).

Algoritmen för routnings mått som används för uppdatering 2 och senare versioner kan förklaras på följande sätt.

* En uppsättning förinställda värden har tilldelats till nätverks gränssnitt.
* Överväg en exempel tabell som visas nedan med värden som tilldelats de olika nätverks gränssnitten när de är molnbaserade eller moln inaktiverade, men med en konfigurerad Gateway. Observera att värdena som tilldelas här endast är exempel värden.

    | Nätverksgränssnitt | Cloud-aktiverad | Moln – inaktiverat med Gateway |
    |-----|---------------|---------------------------|
    | Data 0  | 1            | -                        |
    | Data 1  | 2            | 20                       |
    | Data 2  | 3            | 30                       |
    | Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* Ordningen i vilken moln trafiken dirigeras via nätverks gränssnitten är:
  
    *Data 0 > data 1 > datum 2 > data 3 > data 4 > data 5*
  
    Detta kan förklaras i följande exempel.
  
    Överväg en StorSimple-enhet med två moln aktiverade nätverks gränssnitt, data 0 och data 5. Data 1 till data 4 är moln-inaktiverade men har en konfigurerad Gateway. Den ordning i vilken trafik dirigeras för den här enheten blir:
  
    *Data 0 (1) > data 5 (6) > data 1 (20) > data 2 (30) > data 3 (40) > data 4 (50)*
  
    *Siffrorna i parentesen visar respektive routing-mått.*
  
    Om data 0 Miss lyckas kommer moln trafiken att dirigeras genom data 5. Med tanke på att en gateway har kon figurer ATS på alla andra nätverk, om både data 0 och data 5 var felaktiga, så går moln trafiken igenom data 1.
* Om ett moln aktiverat nätverks gränssnitt Miss lyckas, är 3 försök igen med 30 sekunders fördröjning för att ansluta till gränssnittet. Om alla återförsök inte fungerar dirigeras trafiken till nästa tillgängliga molnbaserade gränssnitt som fastställs av routningstabellen. Om alla moln aktiverade nätverks gränssnitten Miss fungerar växlar enheten över till den andra styrenheten (ingen omstart i det här fallet).
* Om det uppstår ett VIP-haveri för ett iSCSI-aktiverat nätverks gränssnitt kommer det att finnas tre återförsök med en fördröjning på 2 sekunder. Det här beteendet har inte samma resultat som tidigare versioner. Om alla iSCSI-nätverks gränssnitten Miss lyckas sker en redundansväxling (åtföljd av en omstart).
* En avisering aktive ras också på din StorSimple-enhet när ett VIP-haveri inträffar. Mer information finns i [snabb referens för aviseringar](storsimple-8000-manage-alerts.md).
* Vid nya försök prioriteras iSCSI i molnet.
  
    Tänk på följande exempel: en StorSimple-enhet har två nätverks gränssnitt aktiverade, data 0 och data 1. Data 0 är moln-aktiverat medan data 1 är både molnet och iSCSI-aktiverat. Inga andra nätverks gränssnitt på den här enheten har Aktiver ATS för molnet eller iSCSI.
  
    Om data 1 Miss lyckas, eftersom det är det sista iSCSI-användargränssnittet, leder det till en styrenhets växling till data 1 på den andra styrenheten.

### <a name="networking-best-practices"></a>Metod tips för nätverk

Utöver de nätverks krav som ställs för den optimala prestandan för din StorSimple-lösning följer du följande bästa praxis:

* Se till att din StorSimple-enhet har en dedikerad 40 Mbit/s-bandbredd (eller mer) tillgänglig hela tiden. Den här bandbredden ska inte delas (eller så bör allokeringen garanteras genom användning av QoS-principer) med andra program.
* Se till att nätverks anslutningen till Internet alltid är tillgänglig. Sporadiska eller otillförlitliga Internet anslutningar till enheterna, inklusive ingen Internet anslutning alls, leder till en konfiguration som inte stöds.
* Isolera iSCSI-och moln trafiken genom att ha särskilda nätverks gränssnitt på din enhet för iSCSI-och moln åtkomst. Mer information finns i så här [ändrar du nätverks gränssnitt](storsimple-8000-modify-device-config.md#modify-network-interfaces) på din StorSimple-enhet.
* Använd inte en LACP-konfiguration (Link Aggregation Control Protocol) för dina nätverks gränssnitt. Konfigurationen stöds inte.

## <a name="high-availability-requirements-for-storsimple"></a>Krav på hög tillgänglighet för StorSimple

Den maskin varu plattform som ingår i StorSimple-lösningen har tillgänglighets-och Tillförlitlighets funktioner som ger en grund för en hög tillgänglig, feltolerant lagrings infrastruktur i ditt data Center. Det finns dock krav och bästa praxis som du bör följa för att säkerställa tillgängligheten för din StorSimple-lösning. Innan du distribuerar StorSimple bör du noggrant läsa igenom följande krav och metod tips för StorSimple-enheten och anslutna värddatorer.

Om du vill ha mer information om övervakning och underhåll av maskin varu komponenter för din StorSimple-enhet går du till [använda StorSimple Enhetshanteraren-tjänsten för att övervaka maskin varu komponenter och](storsimple-8000-monitor-hardware-status.md) [byte och StorSimple](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Krav och procedurer för hög tillgänglighet för din StorSimple-enhet

Granska följande information noggrant för att säkerställa hög tillgänglighet för din StorSimple-enhet.

#### <a name="pcms"></a>PCMs

StorSimple-enheter innehåller redundanta, frekventa och utbytbara Power-och kylnings moduler (PCMs). Varje PCM har tillräckligt med kapacitet för att tillhandahålla tjänster för hela chassit. Både PCMs måste vara installerade för att säkerställa hög tillgänglighet.

* Anslut din PCMs till olika ström källor för att tillhandahålla tillgänglighet om en ström källa Miss lyckas.
* Om en PCM Miss lyckas, begär en ersättning omedelbart.
* Ta endast bort en misslyckad PCM när du har ersatt och är redo att installera den.
* Ta inte bort båda PCMs samtidigt. Modulen PCM innehåller modulen för säkerhets kopierings batteri. Att ta bort båda PCMs resulterar i en avstängning utan batteri skydd och enhetens tillstånd kommer inte att sparas. Om du vill ha mer information om batteriet går du till [underhålla modulen för säkerhets kopierings batteri](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Controller-moduler

StorSimple-enheter innehåller redundanta moduler som kan växlas under drift. Controller-modulerna körs på ett aktivt/passivt sätt. Vid en specifik tidpunkt är en Controller-modul aktiv och tillhandahåller tjänsten, medan den andra Controller-modulen är passiv. Modulen passiv styrenhet är påslagen och aktive ras om modulen Active Controller Miss lyckas eller tas bort. Varje styrenhets modul har tillräckligt med kapacitet för att tillhandahålla tjänster för hela chassit. Båda Controller-modulerna måste vara installerade för att säkerställa hög tillgänglighet.

* Kontrol lera att båda Controller-modulerna är installerade hela tiden.
* Om en Controller-modul Miss lyckas, begär en ersättning omedelbart.
* Ta bort en modul för misslyckad kontrollant när du har ersatt och är redo att installera den. Att ta bort en modul för längre perioder påverkar luft flödet och därmed systemets kylning.
* Kontrol lera att nätverks anslutningarna till båda Controller-modulerna är identiska och att de anslutna nätverks gränssnitten har en identisk nätverks konfiguration.
* Om en Controller-modul Miss lyckas eller behöver bytas ut, se till att den andra Controller-modulen är i ett aktivt tillstånd innan du ersätter modulen för styrenheten som misslyckades. För att kontrol lera att en kontrollant är aktiv, går du till [identifiera den aktiva styrenheten på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Ta inte bort båda Controller-modulerna samtidigt. Om redundansväxling av en styrenhet pågår ska du inte stänga av modulen för vilo läge eller ta bort den från chassit.
* Efter en redundansväxling väntar du minst fem minuter innan du tar bort någon av Controller-modulerna.

#### <a name="network-interfaces"></a>Nätverksgränssnitt

StorSimple för enhets styrenheten har 4 1 Gigabit och 2 10 Gigabit Ethernet-nätverks gränssnitt.

* Kontrol lera att nätverks anslutningarna till båda Controller-modulerna är identiska och att nätverks gränssnitten som Controller-modulens gränssnitt är anslutna till har en identisk nätverks konfiguration.
* När det är möjligt kan du distribuera nätverks anslutningar över olika växlar för att säkerställa tjänstens tillgänglighet om det uppstår ett nätverks enhets haveri.
* När du kopplar från det enda eller det senast återstående iSCSI-aktiverade gränssnittet (med tilldelade IP-adresser) inaktiverar du först gränssnittet och kopplar sedan bort kablarna. Om gränssnittet kopplas från först kommer det att leda till att den aktiva styrenheten växlar över till den passiva styrenheten. Om den passiva styrenheten också har tillhör ande gränssnitt frånkopplad, kommer båda styrenheterna att starta om flera gånger innan de kan lösas på en kontrollant.
* Anslut minst två DATA gränssnitt till nätverket från varje styrenhets-modul.
* Om du har aktiverat 2 10 GbE-gränssnitten distribuerar du dem över olika växlar.
* När det är möjligt ska du använda MPIO på servrar för att säkerställa att servrarna kan tolerera en länk, ett nätverk eller ett gränssnitts haveri.

Om du vill ha mer information om nätverk på enheten för hög tillgänglighet och prestanda går du till [installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) eller [installerar din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSD och hård diskar

StorSimple-enheter inkluderar Solid State disks (SSD) och hård diskar (HDD) som skyddas med speglade utrymmen. Användning av speglade utrymmen säkerställer att enheten kan tolerera en eller flera SSD eller hård diskar.

* Kontrol lera att alla SSD-och HDD-moduler är installerade.
* Om en SSD eller hård disk kraschar, begär en ny ersättning omedelbart.
* Om en SSD eller HDD Miss lyckas eller behöver bytas ut, se till att du bara tar bort SSD eller hård disk som kräver ersättning.
* Ta inte bort fler än en SSD eller hård disk från systemet vid någon tidpunkt.
  Ett fel på 2 eller flera diskar av viss typ (HDD, SSD) eller på varandra följande fel inom en kort tids ram kan leda till systemfel och potentiell data förlust. Om detta inträffar [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att få hjälp.
* Under ersättningen övervakar du de **delade komponenterna** på bladet **maskin varu hälsa** för enheterna i SSD och hård diskar. En grön kontroll status indikerar att diskarna är felfria eller OK, medan ett rött utrops tecken indikerar en misslyckad SSD eller hård disk.
* Vi rekommenderar att du konfigurerar moln ögonblicks bilder för alla volymer som du behöver skydda i händelse av ett systemfel.

#### <a name="ebod-enclosure"></a>EBOD-hölje

StorSimple Device Model 8600 innehåller ett EBOD-hölje (Extended) förutom den primära inne slutningen. En EBOD innehåller EBOD-styrenheter och hård diskar som skyddas med speglade utrymmen. Användning av speglade utrymmen säkerställer att enheten kan tolerera en eller flera hård diskar som misslyckats. EBOD-kammaren är ansluten till den primära inne slutningen via redundanta SAS-kablar.

* Se till att båda modulerna för EBOD hölje-styrenhet, både SAS-kablar och alla hård diskar installeras hela tiden.
* Om en modul för EBOD höljen Miss lyckas, begär en ersättning omedelbart.
* Om en modul för EBOD höljen Miss lyckas, se till att den andra Controller-modulen är aktiv innan du byter ut den felande modulen. För att kontrol lera att en kontrollant är aktiv, går du till [identifiera den aktiva styrenheten på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Under en EBOD kan du kontinuerligt övervaka status för komponenten i StorSimple Enhetshanteraren-tjänsten genom att komma åt **övervakaren**  >  **maskin varu hälsa**.
* Om en SAS-kabel Miss lyckas eller kräver en ersättning (Microsoft Support bör vara engagerad för att göra en sådan bestämning), se till att du bara tar bort SAS-kabeln som kräver ersättning.
* Ta inte bort både SAS-kablarna samtidigt från systemet vid någon tidpunkt.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Rekommendationer för hög tillgänglighet för värddatorer

Läs noggrant igenom de här metod tipsen för att säkerställa hög tillgänglighet för värdar som är anslutna till din StorSimple-enhet.

* Konfigurera StorSimple med [konfigurationer för fil Server kluster med två noder][1]. Genom att ta bort enskilda fel punkter och skapa redundans på värd sidan, blir hela lösningen hög tillgänglig.
* Använd kontinuerligt tillgängliga (CA) resurser som är tillgängliga med Windows Server 2012 (SMB 3,0) för hög tillgänglighet under redundansväxlingen av lagrings styrenheterna. Mer information om hur du konfigurerar fil Server kluster och ständigt tillgängliga resurser med Windows Server 2012 finns i den här [demonstrationen av videon](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om StorSimple system gränser](storsimple-8000-limits.md).
* [Lär dig hur du distribuerar din StorSimple-lösning](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731844(v=ws.10)