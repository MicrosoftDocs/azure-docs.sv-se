---
title: Verifiera VPN-dataflöde till en Microsoft Azure Virtual Network
description: Den här artikeln hjälper dig att validera nätverks data flödet från lokala resurser till en virtuell Azure-dator.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: 2d5b51e8cfbfcb5f771e9da524231f8ddfc40a9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660941"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Verifiera VPN-dataflöde till ett virtuellt nätverk

Med en VPN gateway-anslutning kan du upprätta säker anslutning mellan olika platser mellan dina Virtual Network i Azure och den lokala IT-infrastrukturen.

Den här artikeln visar hur du verifierar nätverks data flödet från lokala resurser till en virtuell Azure-dator (VM).

> [!NOTE]
> Den här artikeln är avsedd att hjälpa till att diagnostisera och åtgärda vanliga problem. Om du inte kan lösa problemet med hjälp av följande information kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Översikt

VPN gateway-anslutningen omfattar följande komponenter:

* Lokal VPN-enhet (Visa en lista över [verifierade VPN-enheter](vpn-gateway-about-vpn-devices.md#devicetable).)
* Offentligt Internet
* Azure VPN-gateway
* Azure VM

Följande diagram visar den logiska anslutningen för ett lokalt nätverk till ett virtuellt Azure-nätverk via VPN.

![Logisk anslutning för kund nätverk till MSFT-nätverk med VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Beräkna det maximala förväntade inkommande/utgående

1. Fastställ programmets krav på data flödes flöden.
1. Fastställ dina data flödes gränser för Azure VPN-gatewayen. Mer information finns i avsnittet om Gateway-SKU: er i [om VPN gateway](vpn-gateway-about-vpngateways.md#gwsku).
1. Fastställ [vägledning för Azure VM-dataflöde](../virtual-machines/sizes.md) för din VM-storlek.
1. Fastställ din Internet leverantörs bandbredd (ISP).
1. Beräkna det förväntade data flödet genom att ta den minsta bandbredden för antingen den virtuella datorn, VPN Gateway eller Internet leverantören. som mäts i megabit per sekund (/) dividerat med åtta (8).

Om det beräknade data flödet inte uppfyller programmets krav på data flöde för original vara måste du öka bandbredden för den resurs som du har identifierat som Flask hals. Om du vill ändra storlek på en Azure-VPN Gateway, se [ändra en gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Om du vill ändra storlek på en virtuell dator kan du läsa [ändra storlek på en](../virtual-machines/windows/resize-vm.md)virtuell dator. Om du inte har den förväntade Internet bandbredden kan du också kontakta din Internet leverantör.

> [!NOTE]
> VPN Gateway data flöde är en mängd olika Site-to-Site\VNET-to-VNET, eller punkt-till-plats-anslutningar.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Verifiera nätverks data flöde med hjälp av prestanda verktyg

Den här verifieringen bör utföras under perioder med låg belastning, eftersom data flödes mättnaden för VPN-tunnel under testningen inte ger korrekta resultat.

Verktyget som vi använder för det här testet är iPerf, som fungerar både i Windows och Linux och har både klient-och server läge. Den är begränsad till 3Gbps för virtuella Windows-datorer.

Det här verktyget utför inga Läs-/skriv åtgärder på disken. Den genererar bara en själv genererad TCP-trafik från ena änden till den andra. Den genererar statistik baserat på experimentering som mäter bandbredden som är tillgänglig mellan klient-och Server-noder. När du testar mellan två noder fungerar en nod som-servern och den andra noden fungerar som en klient. När det här testet har slutförts rekommenderar vi att du ändrar nodernas roller för att testa både överförings-och nedladdnings data flödet på båda noderna.

### <a name="download-iperf"></a>Ladda ned iPerf

Ladda ned [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Mer information finns i [iPerf-dokumentationen](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > Produkter från tredje part som diskuteras i den här artikeln tillverkas av företag som är oberoende av Microsoft. Microsoft ger ingen garanti, underförstådd eller övrigt, om prestandan eller tillförlitligheten för dessa produkter.

### <a name="run-iperf-iperf3exe"></a>Kör iPerf (iperf3.exe)

1. Aktivera en NSG/ACL-regel som tillåter trafiken (för offentlig IP-adress testning på virtuella Azure-datorer).

1. Aktivera ett brand Väggs undantag för port 5001 på båda noderna.

   **Windows:** Kör följande kommando som administratör:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Kör följande kommando för att ta bort regeln när testningen är klar:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Azure Linux-avbildningar har tillåtna brand väggar. Om det finns ett program som lyssnar på en port tillåts trafiken via. Anpassade avbildningar som skyddas kan kräva att portar öppnas explicit. Vanliga brand väggar för Linux OS-lager inkluderar `iptables` , `ufw` , eller `firewalld` .

1. På noden Server ändrar du till den katalog där iperf3.exe extraheras. Kör sedan iPerf i server läge och Ställ in den så att den lyssnar på port 5001 som följande kommandon:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > Port 5001 är anpassningsbar för att kunna redovisa särskilda brand Väggs begränsningar i din miljö.

1. På noden klient ändrar du till katalogen där verktyget iperf extraheras och kör sedan följande kommando:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   Klienten dirigerar trettio sekunders trafik på port 5001 till-servern. Flaggan-P visar att vi gör 32 samtidiga anslutningar till noden Server.

   Följande skärm bild visar utdata från det här exemplet:

   ![Utdata](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. VALFRITT Kör följande kommando för att bevara test resultaten:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. När du har slutfört de föregående stegen kör du samma steg med de roller som har återförts, så att-noden kommer nu att vara klient-noden och vice versa.

> [!Note]
> Iperf är inte det enda verktyget. [NTTTCP är en alternativ lösning för testning](../virtual-network/virtual-network-bandwidth-testing.md).

## <a name="test-vms-running-windows"></a>Testa virtuella datorer som kör Windows

### <a name="load-latteexe-onto-the-vms"></a>Läs in Latte.exe på de virtuella datorerna

Hämta den senaste versionen av [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Överväg att placera Latte.exe i en separat mapp, till exempel `c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Tillåt Latte.exe via Windows-brandväggen

På mottagaren skapar du en Tillåt-regel i Windows-brandväggen så att Latte.exe trafiken kan komma. Det är enklast att tillåta hela Latte.exe program efter namn istället för att tillåta vissa TCP-portar inkommande.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Tillåt Latte.exe via Windows-brandväggen som detta

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Om du till exempel har kopierat latte.exe till mappen "c:\Tools", är detta kommandot

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Kör svars tids test

Starta latte.exe på mottagaren (kör från CMD, inte från PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Cirka 65 k-iterationer är tillräckligt långt för att returnera representativa resultat.

Alla tillgängliga port nummer är fina.

Om den virtuella datorn har en IP-adress för 10.0.0.4, ser det ut så här

`latte -c -a 10.0.0.4:5005 -i 65100`

Starta latte.exe på avsändaren (kör från CMD, inte från PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Det resulterande kommandot är detsamma som på mottagaren, förutom vid tillägg av "-c" för att indikera att detta är "klienten" eller avsändaren

`latte -c -a 10.0.0.4:5005 -i 65100`

Vänta på resultaten. Beroende på hur långt isär de virtuella datorerna är, kan det ta några minuter att slutföra. Överväg att börja med färre iterationer för att testa framgång innan du kör längre tester.

## <a name="test-vms-running-linux"></a>Testa virtuella datorer som kör Linux

Använd [SockPerf](https://github.com/mellanox/sockperf) för att testa virtuella datorer.

### <a name="install-sockperf-on-the-vms"></a>Installera SockPerf på de virtuella datorerna

Kör de här kommandona på de virtuella Linux-datorerna (både sändare och mottagare) för att förbereda SockPerf på dina virtuella datorer:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS/RHEL – installera GIT och andra användbara verktyg

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu – installera GIT och andra användbara verktyg

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash – alla

Från bash kommando rad (förutsätter git är installerat)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Det kan ta flera minuter att göra det

`make`

Gör installationen snabb

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Kör SockPerf på de virtuella datorerna

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Exempel kommandon efter installationen. Server/mottagare – förutsätter att serverns IP-adress är 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Klient-antar att serverns IP-adress är 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Se till att det inte finns några mellanliggande hopp (t. ex. virtuell installation) under data flödes testningen mellan den virtuella datorn och gatewayen.
> Om det finns dåliga resultat (vad gäller det totala data flödet) som kommer från iPERF/NTTTCP-testerna ovan kan du läsa följande artikel för att förstå viktiga faktorer som ligger bakom de möjliga rotor orsakerna till problemet: https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

I synnerhet kan analyser av paket fångst spår (wireshark/Network Monitor) som samlats in parallellt från klienten och servern under dessa tester hjälpa till att utvärdera dåliga prestanda. De här spårningarna kan omfatta paket förlust, hög latens, MTU-storlek. fragmentering, TCP 0-fönster, färdiga fragment och så vidare.

## <a name="address-slow-file-copy-issues"></a>Åtgärda problem med långsam fil kopiering

Även om det totala data flödet som utvärderas med föregående steg (iPERF/NTTTCP/osv.) var korrekt, kan det hända att du får långsamma fil Kopiera när du antingen använder Utforskaren eller drar och släpper den via en RDP-session. Det här problemet beror vanligt vis på en eller båda av följande faktorer:

* Fil kopierings program, till exempel Utforskaren och RDP, använder inte flera trådar vid kopiering av filer. Använd ett program med flera trådar som [RichCopy](/previous-versions/technet-magazine/dd547088(v=msdn.10)) för att kopiera filer med hjälp av 16 eller 32 trådar för bättre prestanda. Om du vill ändra tråd numret för fil kopiering i RichCopy klickar **du på**  >  **alternativet**  >  **Kopiera fil** kopia.

   ![Problem med långsam fil kopiering](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Alla program fungerar inte, och alla program/processer använder inte alla trådar. Om du kör testet kan du se att vissa trådar är tomma och inte ger korrekta data flödes resultat.
   > Om du vill kontrol lera program filens överförings prestanda använder du flera trådar genom att öka antalet trådar i följd eller minskning för att hitta det optimala genomflödet i programmet eller fil överföringen.

* Det finns inte tillräckligt med Läs/skriv hastighet för virtuella datorer. Mer information finns i [Azure Storage fel sökning](/previous-versions/azure/storage/common/storage-e2e-troubleshooting).

## <a name="on-premises-device-external-facing-interface"></a>Externt gränssnitt för lokalt enhet

Nämnde de undernät i lokala intervall som du vill att Azure ska uppnå via VPN på en lokal nätverksgateway. Definiera samtidigt det virtuella nätverkets adress utrymme i Azure till den lokala enheten.

* **Route-baserad Gateway**: principen eller trafik väljaren för vägbaserade VPN: er konfigureras som alla-till-alla (eller jokertecken).

* **Principbaserad Gateway**: principbaserad VPN krypterar och dirigerar paket via IPSec-tunnlar baserat på kombinationer av adress prefix mellan ditt lokala nätverk och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-konfigurationen.

* **UsePolicyBasedTrafficSelector** -anslutningar: ("UsePolicyBasedTrafficSelectors" till $true på en anslutning kommer att konfigurera Azure VPN-gatewayen att ansluta till principbaserad VPN-brandvägg lokalt. Om du aktiverar PolicyBasedTrafficSelectors måste du se till att VPN-enheten har de matchande trafik väljare som definierats med alla kombinationer av ditt lokala nätverk (lokal nätverksgateway) prefix till och från de virtuella Azure-nätverks prefixen i stället för alla-till-alla.

Olämplig konfiguration kan leda till frekventa från kopplingar i tunneln, paket droppar, dåligt data flöde och svars tid.

## <a name="check-latency"></a>Kontrol lera svars tid

Du kan kontrol lera svars tiden genom att använda följande verktyg:

* WinMTR
* TCPTraceroute
* `ping` och `psping` (dessa verktyg kan ge en klar uppskattning av efter klar Ande, men de kan inte användas i samtliga fall.)

![Kontrol lera svars tid](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Om du ser en hög latens insamling vid något av hoppen innan du anger ett stamnät till nätverks stamnät, kanske du vill fortsätta med ytterligare undersökningar med Internet leverantören.

Om en stor, ovanlig svars tids ökning observeras från hopp inom "msn.net", kan du kontakta MS support för ytterligare undersökningar.

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer information eller hjälp kan du läsa följande länk:

* [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)