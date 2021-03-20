---
title: Testa nätverks fördröjning i Azure Virtual Machine i ett virtuellt Azure-nätverk | Microsoft Docs
description: Lär dig hur du testar nätverks fördröjningen mellan virtuella Azure-datorer i ett virtuellt nätverk
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 7dc8aac730fdf46cab47a3297b8c001cb0b8e314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226413"
---
# <a name="test-vm-network-latency"></a>Testa svarstid för VM-nätverk

För att uppnå de mest exakta resultaten kan du mäta din nätverks fördröjning för din virtuella Azure-dator (VM) med ett verktyg som har utformats för uppgiften. Offentligt tillgängliga verktyg som SockPerf (för Linux) och latte.exe (för Windows) kan isolera och mäta nätverks fördröjningar och utesluta andra typer av svars tider, till exempel program svars tid. Dessa verktyg fokuserar på den typ av nätverks trafik som påverkar program prestanda (dvs. Transmission Control Protocol [TCP] och UDP]-trafik (User Datagram Protocol)). 

Andra vanliga anslutnings verktyg, till exempel ping, kan mäta svars tider, men deras resultat kanske inte representerar nätverks trafiken som används i verkliga arbets belastningar. Det beror på att de flesta av dessa verktyg använder Internet Control Message Protocol (ICMP), som kan behandlas annorlunda än program trafik och vars resultat inte kan tillämpas på arbets belastningar som använder TCP och UDP. 

För korrekt testning av nätverks fördröjning av de protokoll som används av de flesta program, SockPerf (för Linux) och latte.exe (för Windows) ger de mest relevanta resultaten. I den här artikeln beskrivs båda dessa verktyg.

## <a name="overview"></a>Översikt

Genom att använda två virtuella datorer, en som avsändare och en som mottagare, skapar du en tvåvägs kommunikations kanal. Med den här metoden kan du skicka och ta emot paket i båda riktningarna och mäta tur och retur-tiden.

Du kan använda den här metoden för att mäta nätverks fördröjningen mellan två virtuella datorer eller till och med mellan två fysiska datorer. Tids fördröjnings mått kan vara användbara i följande scenarier:

- Upprätta ett riktmärke för nätverks fördröjning mellan de distribuerade virtuella datorerna.
- Jämför effekterna av ändringar i nätverks fördröjningen efter att relaterade ändringar har gjorts i:
  - Operativ system (OS) eller program vara för nätverks stack, inklusive konfigurations ändringar.
  - En distributions metod för virtuella datorer, t. ex. distribution till en tillgänglighets zon eller närhets placerings grupp (PPG).
  - Egenskaper för virtuell dator, till exempel accelererat nätverk eller storleks ändringar.
  - Ett virtuellt nätverk, t. ex. routning eller filtrering av ändringar.

### <a name="tools-for-testing"></a>Verktyg för testning
Du kan mäta svars tiden med två olika verktygs alternativ:

* För Windows-baserade system: [latte.exe (Windows)](https://github.com/microsoft/latte/releases/download/v0/latte.exe)
* För Linux-baserade system: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Genom att använda dessa verktyg kan du se till att endast leverans tider för TCP-eller UDP-nyttolasten mäts och inte ICMP (ping) eller andra paket typer som inte används av program och inte påverkar deras prestanda.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Tips för att skapa en optimal VM-konfiguration

Tänk på följande rekommendationer när du skapar din VM-konfiguration:
- Använd den senaste versionen av Windows eller Linux.
- Aktivera accelererat nätverk för bästa möjliga resultat.
- Distribuera virtuella datorer med en [placerings grupp för Azure närhet](../virtual-machines/co-location.md).
- Större virtuella datorer utför vanligt vis bättre än mindre virtuella datorer.

### <a name="tips-for-analysis"></a>Tips för analys

Tänk på följande rekommendationer när du analyserar test resultaten:

- Upprätta en bas linje tidigt så snart distributionen, konfigurationen och Optimeringarna har slutförts.
- Jämför alltid nya resultat till en bas linje eller, i annat fall, från ett test till ett annat med kontrollerade ändringar.
- Upprepa tester när ändringar observeras eller planeras.


## <a name="test-vms-that-are-running-windows"></a>Testa virtuella datorer som kör Windows

### <a name="get-latteexe-onto-the-vms"></a>Hämta latte.exe på de virtuella datorerna

Ladda ned den [senaste versionen av latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Överväg att lägga latte.exe i en separat mapp, till exempel *c:\Tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Tillåt latte.exe via Windows Defender-brandväggen

På *mottagaren* skapar du en Tillåt-regel på Windows Defender-brandväggen så att latte.exe trafiken kan komma. Det är enklast att tillåta hela latte.exe program efter namn istället för att tillåta vissa TCP-portar inkommande.

Tillåt latte.exe via Windows Defender-brandväggen genom att köra följande kommando:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Om du till exempel har kopierat latte.exe till mappen *c:\Tools* , är detta kommandot:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Kör svars tids test

* På *mottagaren* startar du latte.exe (kör den från cmd-fönstret, inte från PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Cirka 65 000 iterationer är tillräckligt långt för att returnera representativa resultat.

    Alla tillgängliga port nummer är fina.

    Om den virtuella datorn har en IP-adress för 10.0.0.4 skulle kommandot se ut så här:

    `latte -a 10.0.0.4:5005 -i 65100`

* På *avsändaren* startar du latte.exe (kör den från cmd-fönstret, inte från PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Det resulterande kommandot är detsamma som på mottagaren, förutom vid tillägg av &nbsp; *-c* för att indikera att detta är *klienten* eller *avsändaren*:

    `latte -c -a 10.0.0.4:5005 -i 65100`

Vänta på resultaten. Beroende på hur långt isär de virtuella datorerna är, kan det ta några minuter innan testet slutförs. Överväg att börja med färre iterationer för att testa framgång innan du kör längre tester.

## <a name="test-vms-that-are-running-linux"></a>Testa virtuella datorer som kör Linux

Använd [SockPerf](https://github.com/mellanox/sockperf)om du vill testa virtuella datorer som kör Linux.

### <a name="install-sockperf-on-the-vms"></a>Installera SockPerf på de virtuella datorerna

På virtuella Linux-datorer, både *sändare* och *mottagare*, kör du följande kommandon för att förbereda SockPerf på de virtuella datorerna. Det finns kommandon för det större distributioner.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>För Red Hat Enterprise Linux (RHEL)/CentOS

Kör följande kommandon:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>För Ubuntu

Kör följande kommandon:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>För alla distributioner

Kopiera, kompilera och installera SockPerf enligt följande steg:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Kör SockPerf på de virtuella datorerna

När SockPerf-installationen är klar är de virtuella datorerna redo att köra svars tids testen. 

Starta först SockPerf på *mottagaren*.

Alla tillgängliga port nummer är fina. I det här exemplet använder vi port 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Nu när servern lyssnar kan klienten börja skicka paket till servern på den port där den lyssnar (i det här fallet 12345).

Cirka 100 sekunder är tillräckligt lång för att returnera representativa resultat, som du ser i följande exempel:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Vänta på resultaten. Beroende på hur långt isär de virtuella datorerna är, varierar antalet iterationer. Om du vill testa framgång innan du kör längre tester bör du överväga att starta med kortare tester på cirka 5 sekunder.

I det här SockPerf-exemplet används en meddelande storlek på 350 byte, vilket är vanligt för ett genomsnittligt paket. Du kan justera storleken högre eller lägre för att uppnå resultat som bättre visar arbets belastningen som körs på dina virtuella datorer.


## <a name="next-steps"></a>Nästa steg
* Förbättra svars tiden med en grupp för en [Azure närhets placering](../virtual-machines/co-location.md).
* Lär dig hur du [optimerar nätverk för virtuella datorer](../virtual-network/virtual-network-optimize-network-bandwidth.md) för ditt scenario.
* Läs om [hur bandbredd allokeras till virtuella datorer](../virtual-network/virtual-machine-network-throughput.md).
* Mer information finns i [vanliga frågor och svar om Azure Virtual Network](../virtual-network/virtual-networks-faq.md).