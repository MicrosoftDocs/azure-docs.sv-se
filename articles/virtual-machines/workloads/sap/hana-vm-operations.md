---
title: SAP HANA infrastruktur konfiguration och åtgärder på Azure | Microsoft Docs
description: Drifts guide för SAP HANA system som distribueras på virtuella Azure-datorer.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 93587c6bbed20982bd96d04f58106ec1617542d3
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506438"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer
Det här dokumentet innehåller rikt linjer för att konfigurera Azure-infrastruktur och operativ SAP HANA system som distribueras på virtuella Azure-datorer (VM: ar). Dokumentet innehåller också konfigurations information för SAP HANA skala ut för VM-SKU: n för M128s. Detta dokument är inte avsett att ersätta standard-SAP-dokumentationen, som innehåller följande innehåll:

- [Administrations guide för SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Installations guider för SAP](https://service.sap.com/instguides)
- [SAP-anteckningar](https://service.sap.com/notes)

## <a name="prerequisites"></a>Krav
Om du vill använda den här guiden behöver du grundläggande kunskaper om följande Azure-komponenter:

- [Virtuella Azure-datorer](../../linux/tutorial-manage-vm.md)
- [Azure-nätverk och virtuella nätverk](../../linux/tutorial-virtual-network.md)
- [Azure Storage](../../linux/tutorial-manage-disks.md)

Mer information om SAP NetWeaver och andra SAP-komponenter i Azure finns i avsnittet [SAP on Azure](./get-started.md) i Azure- [dokumentationen](../../../index.yml).

## <a name="basic-setup-considerations"></a>Grundläggande konfigurations överväganden
I följande avsnitt beskrivs grundläggande installations överväganden för distribution av SAP HANA system på virtuella Azure-datorer.

### <a name="connect-into-azure-virtual-machines"></a>Ansluta till virtuella Azure-datorer
Som dokumenteras i [planerings guiden för Azure Virtual Machines](./planning-guide.md)finns det två grundläggande metoder för att ansluta till virtuella Azure-datorer:

- Anslut via Internet och offentliga slut punkter på en virtuell hopp dator eller på den virtuella dator som kör SAP HANA.
- Anslut via en [VPN-](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) eller Azure- [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Plats-till-plats-anslutning via VPN eller ExpressRoute krävs för produktions scenarier. Den här typen av anslutning behövs också för icke-produktions scenarier som matas in i produktions scenarier där SAP-programvaran används. Följande bild visar ett exempel på en anslutning mellan platser:

![Anslutning mellan platser](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Välj Azure VM-typer
De typer av virtuella Azure-datorer som kan användas för produktions scenarier finns i [SAP-dokumentationen för IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). För icke-produktions scenarier är en större mängd inbyggda virtuella Azure VM-typer tillgängliga.

>[!NOTE]
> För icke-produktions scenarier använder du de VM-typer som anges i [SAP-anteckningen #1928533](https://launchpad.support.sap.com/#/notes/1928533). För användning av virtuella Azure-datorer för produktions scenarier söker du efter SAP HANA certifierade virtuella datorer i listan SAP publicerade [certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Distribuera de virtuella datorerna i Azure med hjälp av:

- Azure-portalen.
- Azure PowerShell-cmdletar.
- Azure CLI.

Du kan också distribuera en komplett installerad SAP HANA-plattform på Azure VM-tjänsterna via [SAP Cloud Platform](https://cal.sap.com/). Installations processen beskrivs i [distribuera SAP S/4HANA eller BW/4HANA på Azure](./cal-s4h.md) eller med automatiseringen som publiceras [här](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

>[!IMPORTANT]
> För att kunna använda M208xx_v2 virtuella datorer måste du vara noga med att välja Linux-avbildningen från Azures avbildnings Galleri för virtuella Azure-datorer. För att läsa informationen läser du artikeln [minnes optimerade storlekar för virtuella datorer](../../mv2-series.md).
> 


### <a name="storage-configuration-for-sap-hana"></a>Lagrings konfiguration för SAP HANA
För lagringspooler och lagrings typer som ska användas med SAP HANA i Azure läser du dokumentet [SAP HANA Azure VM Storage Configurations](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Konfigurera virtuella Azure-nätverk
När du har plats-till-plats-anslutning till Azure via VPN eller ExpressRoute måste du ha minst ett virtuellt Azure-nätverk som är anslutet via en virtuell gateway till VPN-eller ExpressRoute-kretsen. I enkla distributioner kan den virtuella gatewayen distribueras i ett undernät för det virtuella Azure-nätverket (VNet) som är värd för de SAP HANA instanserna. Om du vill installera SAP HANA skapar du två ytterligare undernät i det virtuella Azure-nätverket. Ett undernät är värd för de virtuella datorer som kör SAP HANA instanser. Det andra under nätet kör byglar eller hantering av virtuella datorer som värd SAP HANA Studio, annan hanterings program vara eller program vara.

> [!IMPORTANT]
> Slut på funktioner, men viktiga av prestanda skäl är att det inte går att konfigurera [virtuella Azure-enheter](https://azure.microsoft.com/solutions/network-appliances/) i kommunikations vägen mellan SAP-programmet och DBMS-skiktet i ett SAP NetWeaver-, hybris-eller S/4HANA-baserat SAP-system. Kommunikationen mellan SAP-program skiktet och DBMS-skiktet måste vara en direkt ett. Begränsningen omfattar inte [Azure-grupperna och NSG-regler](../../../virtual-network/security-overview.md) förutsatt att de grupperna och NSG reglerna tillåter en direkt kommunikation. Ytterligare scenarier där NVA inte stöds finns i kommunikations vägar mellan virtuella Azure-datorer som representerar Linux pacemaker-klusternoder och SBD-enheter enligt beskrivningen i [hög tillgänglighet för SAP-NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](./high-availability-guide-suse.md). Eller i kommunikations vägar mellan virtuella Azure-datorer och Windows Server-SOFS som beskrivs i skapa [en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en fil resurs i Azure](./sap-high-availability-guide-wsfc-file-share.md). NVA i kommunikations vägar kan enkelt dubblera nätverks fördröjningen mellan två kommunikations partner, kan begränsa data flödet i kritiska sökvägar mellan SAP-program skiktet och DBMS-skiktet. I vissa scenarier med kunder kan NVA orsaka att pacemaker Linux-kluster kraschar i fall där kommunikationen mellan Linux pacemaker-klusternoderna måste kommunicera med sin SBD-enhet via en NVA.  
> 

> [!IMPORTANT]
> En annan design som **inte** stöds är en uppdelning av SAP-programlagret och DBMS-skiktet i olika virtuella Azure-nätverk som inte är [peer](../../../virtual-network/virtual-network-peering-overview.md) -kopplade till varandra. Vi rekommenderar att du åtskiljer SAP-program lagret och DBMS-skiktet med undernät i ett virtuellt Azure-nätverk i stället för att använda olika virtuella Azure-nätverk. Om du väljer att inte följa rekommendationen och i stället åtskiljer de två lagren i olika virtuella nätverk, måste de två virtuella nätverken vara [peer-datorer](../../../virtual-network/virtual-network-peering-overview.md). Observera att nätverks trafiken mellan två [peer-datorer](../../../virtual-network/virtual-network-peering-overview.md) i Azure omfattas av överförings kostnader. Med stor data volym i många terabyte som utbyts mellan SAP-program skiktet och betydande kostnader för DBMS-skikt kan du samla in om SAP-programlagret och DBMS-lagret är åtskiljt mellan två peer-kopplade virtuella Azure-nätverk. 

När du installerar de virtuella datorerna för att köra SAP HANA behöver de virtuella datorerna:

- Två virtuella nätverkskort installerade: ett nätverkskort för att ansluta till hanterings under nätet och ett nätverkskort för att ansluta från det lokala nätverket eller andra nätverk till SAP HANA-instansen på den virtuella Azure-datorn.
- Statiska privata IP-adresser som distribueras för båda virtuella nätverkskorten.

> [!NOTE]
> Du bör tilldela statiska IP-adresser via Azure till enskilda virtuella nätverkskort. Du bör inte tilldela statiska IP-adresser i gäst operativ systemet till en vNIC. Vissa Azure-tjänster som Azure Backup-tjänsten är beroende av att minst primär vNIC är inställd på DHCP och inte statiska IP-adresser. Se även dokumentet [Felsöka säkerhets kopiering av virtuella Azure-datorer](../../../backup/backup-azure-vms-troubleshoot.md#networking). Om du behöver tilldela flera statiska IP-adresser till en virtuell dator måste du tilldela flera virtuella nätverkskort till en virtuell dator.
>
>

Men för distributioner som är i drift måste du skapa en virtuell nätverks arkitektur för data Center i Azure. Den här arkitekturen rekommenderar separering av den virtuella Azure VNet-gatewayen som ansluter till lokalt till ett separat Azure VNet. Detta separata VNet ska vara värd för all trafik som lämnar antingen lokalt eller till Internet. Med den här metoden kan du distribuera program vara för gransknings-och loggnings trafik som går in i det virtuella data centret i Azure i det här separata hubb-VNet. Det innebär att du har ett VNet som är värd för all program vara och konfigurationer som relaterar till inkommande trafik till Azure-distributionen.

Artiklarna [Azure Virtual Data Center: ett nätverks perspektiv](/azure/architecture/vdc/networking-virtual-datacenter) och [Azure Virtual Data Center och företags kontroll planet](/azure/architecture/vdc/) ger mer information om den virtuella Data Center metoden och den relaterade Azure VNet-designen.


>[!NOTE]
>Trafik som flödar mellan ett nav-VNet och ekrar VNet med hjälp av [Azure VNet-peering](../../../virtual-network/virtual-network-peering-overview.md) omfattas av ytterligare [kostnader](https://azure.microsoft.com/pricing/details/virtual-network/). Utifrån dessa kostnader kan du behöva överväga att göra kompromisser mellan att köra en strikt hubb och eker-nätverks design och köra flera [Azure ExpressRoute-gatewayer](../../../expressroute/expressroute-about-virtual-network-gateways.md) som du ansluter till ekrar för att kringgå VNet-peering. Azure ExpressRoute-gatewayer introducerar dock även ytterligare [kostnader](https://azure.microsoft.com/pricing/details/vpn-gateway/) . Du kan också stöta på ytterligare kostnader för program från tredje part som du använder för loggning, granskning och övervakning av nätverks trafik. Beroende på kostnaderna för data utbyte via VNet-peering på en sida och kostnader som skapats av ytterligare Azure ExpressRoute-gatewayer och ytterligare program varu licenser, kan du besluta om mikrosegmentering i ett VNet med hjälp av undernät som isolerade enheter i stället för virtuella nätverk.


En översikt över olika metoder för att tilldela IP-adresser finns i [IP-diagramtyper och autentiseringsmetoder i Azure](../../../virtual-network/public-ip-addresses.md). 

För virtuella datorer som kör SAP HANA bör du arbeta med statiska IP-adresser tilldelade. Orsak är att vissa konfigurationsfiler för HANA-referens-IP-adresser.

[Azure nätverks säkerhets grupper (NSG: er)](../../../virtual-network/virtual-network-vnet-plan-design-arm.md) används för att dirigera trafik som dirigeras till SAP HANA-instansen eller hoppet. NSG: er och slutligen [program säkerhets grupper](../../../virtual-network/security-overview.md#application-security-groups) är kopplade till SAP HANA under nätet och hanterings under nätet.

Följande bild visar en översikt över ett grovt distributions schema för SAP HANA som följer en nav och ekrar VNet-arkitektur:

![Schema för grov distribution för SAP HANA](media/hana-vm-operations/hana-simple-networking-dmz.png)

Om du vill distribuera SAP HANA i Azure utan en plats-till-plats-anslutning vill du fortfarande skydda SAP HANA-instansen från det offentliga Internet och dölja den bakom en vidarebefordrande proxy. I det här grundläggande scenariot använder distributionen Azures inbyggda DNS-tjänster för att matcha värdnamn. I en mer komplex distribution där offentliga IP-adresser används är Azure inbyggda DNS-tjänster särskilt viktiga. Använd Azure-NSG: er och [Azure-NVA](https://azure.microsoft.com/solutions/network-appliances/) för att styra och övervaka routningen från Internet till din Azure VNet-arkitektur i Azure. Följande bild visar ett grovt schema för att distribuera SAP HANA utan en plats-till-plats-anslutning i en hubb och ekrar VNet-arkitektur:
  
![Grovt distributions schema för SAP HANA utan en plats-till-plats-anslutning](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

En annan beskrivning av hur du använder Azure NVA för att kontrol lera och övervaka åtkomst från Internet utan hubben hubb och eker VNet finns i artikeln [distribuera virtuella nätverks enheter med hög tillgänglighet](/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurera Azure-infrastruktur för SAP HANA utskalning
För att ta reda på vilka typer av virtuella Azure-datorer som är certifierade för OLAP-out eller S/4HANA-utskalning, kontrollerar du [SAP HANA maskin varu katalogen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). En bock markering i kolumnen "Clustering" indikerar stöd för skalbarhet. Program typ anger om utskalning av OLAP-out eller S/4HANA stöds. Mer information om noder som certifierats för att skala ut för var och en av de virtuella datorerna finns i informationen om posterna i den aktuella VM-SKU: n som anges i SAP HANA maskin varu katalog.

De lägsta OS-versionerna för distribution av skalbara konfigurationer i virtuella Azure-datorer finns i informationen om posterna i den aktuella VM-SKU: n som anges i SAP HANA maskin varu katalog. För en OLAP-baserad konfiguration i n nod fungerar en nod som huvud nod. De andra noderna upp till gränsen för certifieringen fungerar som arbetsnoden. Ytterligare noder i vänte läge räknas inte in i antalet certifierade noder

>[!NOTE]
> Det går bara att skala ut distributioner av virtuella Azure-datorer med SAP HANA med noden vänte läge med hjälp av [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) Storage. Inga andra SAP HANA Certified Azure Storage gör det möjligt att konfigurera SAP HANA standby-noder
>

För/Hana/Shared rekommenderar vi också användningen av [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). 

En typisk grundläggande design för en enskild nod i en skalbar konfiguration kommer att se ut så här:

![Skala ut grunderna för en enskild nod](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

Den grundläggande konfigurationen av en VM-nod för SAP HANA utskalning ser ut så här:

- För **/Hana/Shared**använder du den inbyggda NFS-tjänsten som tillhandahålls via Azure NetApp Files. 
- Alla andra disk volymer delas inte mellan de olika noderna och baseras inte på NFS. Installations konfiguration och steg för att skala ut HANA-installationer med icke-delade **/Hana/data** och **/Hana/log** ges ytterligare senare i det här dokumentet. För HANA-certifierad lagring som kan användas, se artikeln [SAP HANA Storage-konfigurationer för virtuella Azure-datorer](./hana-vm-operations-storage.md).


Genom att ändra storlek på volymerna eller diskarna måste du kontrol lera dokumentet [SAP HANA TDI Storage-krav](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)för den storlek som krävs beroende på antalet arbetsnoder. Dokumentet släpper en formel som du måste tillämpa för att hämta den kapacitet som krävs för volymen

De andra design villkoren som visas i grafiken i konfigurationen av en enskild nod för en skalbar SAP HANA virtuell dator är det virtuella nätverket eller bättre under näts konfigurationen. SAP rekommenderar starkt en separation av klient/program som riktas mot trafik från kommunikationen mellan HANA-noderna. Som det visas i grafiken uppnås målet genom att två olika virtuella nätverkskort är kopplade till den virtuella datorn. Båda virtuella nätverkskort finns i olika undernät och har två olika IP-adresser. Sedan styr du flödet av trafik med regler för routning med NSG: er eller användardefinierade vägar.

Särskilt i Azure finns det inga medel och metoder för att genomdriva tjänst kvalitet och kvoter för vissa virtuella nätverkskort. Det innebär att separationen av klient/program som riktas mot och kommunikationen mellan noderna inte öppnar några möjligheter att prioritera en trafik ström över det andra. I stället är separationen ett mått på säkerheten i avskärmningen av kommunikationen mellan olika noder i de skalbara konfigurationerna.  

>[!NOTE]
>SAP rekommenderar att nätverks trafiken skiljs till klienten/program sidan och trafiken mellan noder enligt beskrivningen i det här dokumentet. Därför rekommenderar vi att du placerar en arkitektur på plats som du ser i den senaste grafiken. Se även din säkerhets-och efterlevnads team för krav som avviker från rekommendationen 
>

Från en nätverks plats i vyn ser minimi kravet för nätverks arkitektur att se ut så här:

![Skala ut grunderna för en enskild nod](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>Installera SAP HANA Scale-Out n Azure
Genom att installera en skalbar SAP-konfiguration måste du utföra grova steg i:

- Distribuera ny eller anpassa en befintlig Azure VNet-infrastruktur
- Distribuera de nya virtuella datorerna med hjälp av Azure Managed Premium Storage, Ultra disk-volymer och/eller NFS-volymer baserade på ANF
- - Anpassa nätverks routning för att se till att till exempel kommunikation mellan virtuella datorer mellan virtuella datorer inte dirigeras via en [NVA](https://azure.microsoft.com/solutions/network-appliances/). 
- Installera SAP HANA huvud-noden.
- Anpassa konfigurations parametrar för SAP HANA huvud noden
- Fortsätt med installationen av SAP HANA Worker-noder

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Installation av SAP HANA i en skalbar konfiguration
När din Azure VM-infrastruktur har distribuerats, och alla andra förberedelser är klara, måste du installera SAP HANA skalbara konfigurationer i följande steg:

- Installera SAP HANA Master-noden enligt SAP-dokumentationen
- Om du använder Azure Premium Storage eller Ultra disk Storage med icke-delade diskar av/Hana/data och/Hana/log måste du ändra global.ini-filen och lägga till parametern "basepath_shared = nej" i global.ini-filen. Med den här parametern kan SAP HANA köras i skalbart utan "delade" **/Hana/data** -och **/Hana/log** -volymer mellan noderna. Information finns dokumenterad i [SAP obs #2080991](https://launchpad.support.sap.com/#/notes/2080991). Om du använder NFS-volymer baserade på ANF för/Hana/data och/Hana/log behöver du inte göra den här ändringen
- Efter ändringen i global.ini-parametern startar du om SAP HANA-instansen
- Lägg till ytterligare arbetsnoder. Se även <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html> . Ange det interna nätverket för SAP HANA kommunikation mellan noder under installationen eller efteråt med, till exempel, den lokala hdblcm. Mer detaljerad dokumentation finns även i [SAP obs #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Information om hur du konfigurerar ett SAP HANA skalbart system med noden vänte läge på SUSE Linux beskrivs i detalj i [distribuera ett SAP HANA skalbart system med noden vänte läge på virtuella Azure-datorer genom att använda Azure NetApp Files på SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md). Motsvarande dokumentation för Red Hat finns i artikeln [distribuera ett SAP HANA skalbart system med noden vänte läge på virtuella Azure-datorer genom att använda Azure NetApp Files på Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md). 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA dynamisk Nivåing 2,0 för virtuella Azure-datorer

Förutom SAP HANA-certifieringar på virtuella datorer i Azure M-serien stöds SAP HANA dynamisk nivå 2,0 också på Microsoft Azure (se SAP HANA dokumentations Länkar för dynamisk lagring längre ned). Även om det inte finns någon skillnad i att installera produkten eller använda den, till exempel via SAP HANA cockpit i en virtuell Azure-dator, finns det några viktiga saker som är obligatoriska för statsstöd i Azure. Dessa viktiga punkter beskrivs nedan. I artikeln kommer förkortningen "DT 2,0" att användas i stället för det fullständiga namnet dynamisk Nivåing 2,0.

SAP HANA dynamisk nivå 2,0 stöds inte av SAP BW eller S4HANA. De viktigaste användnings fallen är just nu interna HANA-program.


### <a name="overview"></a>Översikt

Bilden nedan ger en översikt över stödet för DT 2,0 på Microsoft Azure. Det finns en uppsättning obligatoriska krav som måste följas för att uppfylla den officiella certifieringen:

- DT 2,0 måste vara installerat på en dedikerad virtuell Azure-dator. Den kan inte köras på samma virtuella dator där SAP HANA körs
- SAP HANA-och DT 2,0-VM måste distribueras inom samma virtuella Azure-nätverk
- SAP HANA-och DT 2,0-VM måste distribueras med Azure-accelererat nätverk aktiverat
- Lagrings typen för de virtuella DT 2,0-datorerna måste vara Azure Premium Storage
- Flera Azure-diskar måste kopplas till den virtuella DT 2,0-datorn
- Det krävs att du skapar en programvaru-RAID/stripe-volym (antingen via LVM eller mdadm) med randning över Azure-diskarna

Mer information kommer att förklaras i följande avsnitt.

![Översikt över SAP HANA DT 2,0-arkitektur](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dedikerad virtuell Azure-dator för SAP HANA DT 2,0

I Azure IaaS stöds endast DT 2,0 på en dedikerad virtuell dator. Det går inte att köra DT 2,0 på samma virtuella Azure-dator där HANA-instansen körs. Två första VM-typer kan användas för att köra SAP HANA DT 2,0:

- M64-32ms 
- E32sv3 

Se beskrivning av VM-typ [här](../../sizes-memory.md)

Med tanke på den grundläggande idén om DT 2,0, som är om att avlasta "varm"-data för att spara kostnader är det klokt att använda motsvarande VM-storlekar. Det finns ingen strikt regel för möjliga kombinationer. Det beror på den specifika kundens arbets belastning.

Rekommenderade konfigurationer är:

| SAP HANA VM-typ | DT 2,0 VM-typ |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Alla kombinationer av virtuella datorer med SAP HANA Certified M-serien med stöd för DT 2,0 virtuella datorer (M64-32ms och E32sv3) är möjliga.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure-nätverk och SAP HANA DT 2,0

Installationen av DT 2,0 på en dedikerad virtuell dator kräver nätverks data flöde mellan den virtuella DT 2,0-datorn och den SAP HANA virtuella datorn med minst 10 GB. Därför är det obligatoriskt att placera alla virtuella datorer i samma virtuella Azure-nätverk och aktivera Azure-accelererat nätverk.

Se ytterligare information om Azure-accelererat nätverk [här](../../../virtual-network/create-vm-accelerated-networking-cli.md)

### <a name="vm-storage-for-sap-hana-dt-20"></a>VM-lagring för SAP HANA DT 2,0

Enligt vägledningen för bästa praxis i DT 2,0 bör diskens IO-genomflöde vara minst 50 MB/SEK per fysisk kärna. Titta närmare på specifikationen för de två typerna av virtuella Azure-datorer, som stöds för DT 2,0 den maximala data flödet för disk-i/o för den virtuella datorn ser ut så här:

- E32sv3:768 MB/SEK (ej cachelagrat) vilket innebär en kvot på 48 MB per sekund per fysisk kärna
- M64-32ms: 1000 MB/SEK (ej cachelagrat) vilket innebär en kvot på 62,5 MB per sekund per fysisk kärna

Det krävs att du kopplar flera Azure-diskar till den virtuella datorn DT 2,0 och skapar en programvaru-RAID (striping) på OS-nivå för att uppnå Max gränsen för disk data flöde per virtuell dator. En enskild Azure-disk kan inte tillhandahålla genomflödet för att uppnå den maximala VM-gränsen i detta hänseende. Azure Premium Storage är obligatoriskt för att köra DT 2,0. 

- Information om tillgängliga Azure-disk typer finns [här](../../windows/disks-types.md)
- Information om hur du skapar programvaru-RAID via mdadm finns [här](../../linux/configure-raid.md)
- Information om hur du konfigurerar LVM för att skapa en stripe-volym för maximalt data flöde hittar du [här](../../linux/configure-lvm.md)

Beroende på storleks krav finns det olika alternativ för att uppnå högsta data flöde för en virtuell dator. Här är möjliga data volym diskkonfigurationer för varje DT 2,0 VM-typ för att uppnå den övre gränsen för VM-dataflöde. Den virtuella E32sv3-datorn bör betraktas som en ingångs nivå för mindre arbets belastningar. Om det skulle vara så att det inte är tillräckligt snabbt kan det vara nödvändigt att ändra storlek på den virtuella datorn till M64-32ms.
Eftersom den virtuella M64-32ms-datorn har mycket minne, kanske inte IO-belastningen når gränsen, särskilt för Läs intensiva arbets belastningar. Därför kan färre diskar i stripe-uppsättningen vara tillräckligt beroende på kundens specifika arbets belastning. Men för att vara på den säkra sidan har disk konfigurationerna nedan valts för att garantera maximalt data flöde:


| VM-SKU | Disk konfiguration 1 | Disk konfiguration 2 | Disk konfiguration 3 | Disk konfiguration 4 | Disk konfiguration 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50-> 16 TB | 4 x P40-> 8 TB | 5 x P30-> 5 TB | 7 x P20 – > 3,5 TB | 8 x p15 – > 2 TB | 
| E32sv3 | 3 x P50 – > 12 TB | 3 x P40-> 6 TB | 4 x P30-> 4 TB | 5 x P20 – > 2,5 TB | 6 x p15 – > 1,5 TB | 


Särskilt om arbets belastningen är Read-stark kan det öka IO-prestandan för att aktivera Azure-värd-cachen "skrivskyddad" enligt rekommendationer för databas program varans data volymer. För transaktions loggens cache för Azure-värddatorn måste det vara ingen. 

När det gäller logg volymens storlek är en rekommenderad start punkt en tumregel på 15% av data storleken. Du kan skapa logg volymen genom att använda olika typer av Azure-diskar beroende på krav på kostnad och data flöde. För logg volymen krävs högt I/O-genomflöde.  Om du använder VM-typen M64-32ms är det obligatoriskt att aktivera [Skrivningsaccelerator](../../how-to-enable-write-accelerator.md). Azure Skrivningsaccelerator ger optimal disk Skriv fördröjning för transaktions loggen (endast tillgängligt för M-serien). Det finns några objekt att överväga, till exempel det högsta antalet diskar per VM-typ. Information om Skrivningsaccelerator hittar du [här](../../how-to-enable-write-accelerator.md)


Här följer några exempel på hur du ändrar storlek på logg volymen:

| data volym storlek och disk typ | logg volym och disk typs konfiguration 1 | logg volym och disk typs konfiguration 2 |
| --- | --- | --- |
| 4 x P50-> 16 TB | 5 x P20 – > 2,5 TB | 3 x P30-> 3 TB |
| 6 x p15 – > 1,5 TB | 4 x P6 – > 256 GB | 1 x p15 – > 256 GB |


Precis som för SAP HANA-utskalning måste katalogen/Hana/Shared delas mellan SAP HANA VM och den virtuella datorn DT 2,0. Samma arkitektur som för SAP HANA skala ut med dedikerade virtuella datorer, vilket fungerar som en NFS-server med hög tillgänglighet rekommenderas. Den identiska designen kan användas för att tillhandahålla en delad säkerhets kopierings volym. Men det är upp till kunden om det skulle vara nödvändigt eller om det räcker att bara använda en dedikerad virtuell dator med tillräckligt med lagrings kapacitet för att fungera som en säkerhets kopierings Server.



### <a name="links-to-dt-20-documentation"></a>Länkar till DT 2,0-dokumentation 

- [SAP HANA installations-och uppdaterings guide för dynamisk nivå](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Självstudier och resurser för SAP HANA dynamisk nivå](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [PoC för SAP HANA dynamisk Nivåing](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [Förbättringar i SAP HANA 2,0 SPS 02 dynamisk nivåer](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Åtgärder för att distribuera SAP HANA på virtuella Azure-datorer
I följande avsnitt beskrivs några av de åtgärder som rör distribution av SAP HANA system på virtuella Azure-datorer.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Säkerhetskopiera och återställa åtgärder på virtuella Azure-datorer
I följande dokument beskrivs hur du säkerhetskopierar och återställer din SAP HANA-distribution:

- [Översikt över SAP HANA-säkerhetskopiering](./sap-hana-backup-guide.md)
- [SAP HANA säkerhets kopiering på filnivå](./sap-hana-backup-file-level.md)
- [SAP HANA prestandatest för lagring av ögonblicks bilder](./sap-hana-backup-guide.md)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Starta och starta om virtuella datorer som innehåller SAP HANA
En framträdande funktion i det offentliga Azure-molnet är att du bara debiteras för dina dator minuter. När du till exempel stänger av en virtuell dator som kör SAP HANA debiteras du bara för lagrings kostnaderna under den tiden. En annan funktion är tillgänglig när du anger statiska IP-adresser för dina virtuella datorer i den första distributionen. När du startar om en virtuell dator som har SAP HANA startas den virtuella datorn om med föregående IP-adresser. 


### <a name="use-saprouter-for-sap-remote-support"></a>Använda SAProuter för SAP-fjärrsupport
Om du har en plats-till-plats-anslutning mellan dina lokala platser och Azure, och du kör SAP-komponenter, kör du förmodligen redan SAProuter. I det här fallet slutför du följande objekt för fjärrsupport:

- Behåll den privata och statiska IP-adressen för den virtuella dator som är värd för SAP HANA i SAProuter-konfigurationen.
- Konfigurera NSG för under nätet som är värd för den virtuella HANA-datorn att tillåta trafik via TCP/IP-port 3299.

Om du ansluter till Azure via Internet och du inte har en SAP-router för den virtuella datorn med SAP HANA måste du installera komponenten. Installera SAProuter i en separat virtuell dator i hanterings under nätet. Följande bild visar ett grovt schema för att distribuera SAP HANA utan en plats-till-plats-anslutning och med SAProuter:

![Grovt distributions schema för SAP HANA utan en plats-till-plats-anslutning och SAProuter](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Se till att installera SAProuter i en separat virtuell dator och inte i din virtuella dator i hoppet. Den separata virtuella datorn måste ha en statisk IP-adress. Kontakta SAP för en IP-adress om du vill ansluta din SAProuter till SAProuter som finns i SAP. (SAProuter som är värd för SAP är motsvarigheten till SAProuter-instansen som du installerar på den virtuella datorn.) Använd IP-adressen från SAP för att konfigurera SAProuter-instansen. I konfigurations inställningarna är den enda nödvändiga porten TCP-port 3299.

Mer information om hur du konfigurerar och underhåller support anslutningar via SAProuter finns i [SAP-dokumentationen](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Hög tillgänglighet med SAP HANA på virtuella Azure-datorer
Om du kör SUSE Linux Enterprise Server eller Red Hat kan du upprätta ett pacemaker-kluster med STONITH-enheter. Du kan använda enheterna för att konfigurera en SAP HANA-konfiguration som använder synkron replikering med HANA-systemreplikering och automatisk redundans. Mer information finns i avsnittet "nästa steg".

## <a name="next-steps"></a>Efterföljande moment
Bekanta dig med artiklarna som de visas i listan
- [Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA](./hana-vm-operations-storage.md)
- [Distribuera ett SAP HANA skalbart system med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Distribuera ett SAP HANA skalbart system med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
- [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)

 
