---
title: Testa data flöde för Azure VM-nätverk
titlesuffix: Azure Virtual Network
description: Använd NTTTCP för att rikta in nätverket för testning och minimera användningen av andra resurser som kan påverka prestandan.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: 7a2f6750a4d0a48c6971f60241976fb55410b65c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221450"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Test av bandbredd/data flöde (NTTTCP)

När du testar nätverks data flödes prestanda i Azure är det bäst att använda ett verktyg som är avsett för nätverket för testning och minimerar användningen av andra resurser som kan påverka prestandan. NTTTCP rekommenderas.

Kopiera verktyget till två virtuella Azure-datorer med samma storlek. En virtuell dator fungerar som avsändare och den andra som mottagare.

#### <a name="deploying-vms-for-testing"></a>Distribuera virtuella datorer för testning
För det här testet ska de två virtuella datorerna finnas i samma [närhets placerings grupp](../virtual-machines/co-location.md) eller samma tillgänglighets uppsättning så att vi kan använda sina interna IP-adresser och undanta belastningsutjämnaren från testet. Det går att testa med VIP men den här typen av testning är utanför det här dokumentets omfattning.

Anteckna MOTTAGAREns IP-adress. Vi kallar IP "a. b. c. r"

Anteckna antalet kärnor på den virtuella datorn. Vi kallar " \# antal \_ kärnor"

Kör NTTTCP-testet i 300 sekunder (eller 5 minuter) på den virtuella datorn och mottagarens VM.

Tips: när du konfigurerar det här testet för första gången kan du prova en kortare test period för att få feedback tidigare. När verktyget fungerar som förväntat utökar du test perioden till 300 sekunder för de mest exakta resultaten.

> [!NOTE]
> Avsändaren **och** mottagaren måste ange **samma** test varaktighets parameter (-t).

Så här testar du en enskild TCP-ström i 10 sekunder:

Mottagar parametrar: ntttcp-r-t 10-P 1

Avsändar parametrar: ntttcp-s 10.27.33.7-t 10-n 1-P 1

> [!NOTE]
> Föregående exempel bör endast användas för att bekräfta konfigurationen. Giltiga exempel på testning beskrivs senare i det här dokumentet.

## <a name="testing-vms-running-windows"></a>Testa virtuella datorer som kör WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Hämta NTTTCP till de virtuella datorerna.

Hämta den senaste versionen: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Eller Sök efter den om den har flyttats: <https://www.bing.com/search?q=ntttcp+download> \< -

Överväg att placera NTTTCP i separata mappar, t. ex. c: \\ verktyg

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Tillåt NTTTCP via Windows-brandväggen
På mottagaren skapar du en Tillåt-regel i Windows-brandväggen så att NTTTCP-trafiken kan tas emot. Det är enklast att tillåta hela NTTTCP-programmet med namn istället för att tillåta vissa TCP-portar inkommande.

Tillåt ntttcp via Windows-brandväggen så här:

netsh advfirewall Firewall Add Rule program = \<PATH\> \\ntttcp.exe Name = "ntttcp" Protocol = any dir = in Action = Allow Enable = Ja Profile = any

Om du till exempel har kopierat ntttcp.exe till mappen "c: \\ Tools", är detta kommandot: 

netsh advfirewall Firewall Add Rule program = c: \\ tools \\ntttcp.exe Name = "ntttcp" Protocol = any dir = in Action = Allow Enable = Ja Profile = any

#### <a name="running-ntttcp-tests"></a>Köra NTTTCP-test

Starta NTTTCP på mottagaren (**Kör från cmd**, inte från PowerShell):

ntttcp-r – m [2 \* \# NUM \_ kärnor], \* , a. b. c. r-t 300

Om den virtuella datorn har fyra kärnor och en IP-adress för 10.0.0.4, ser det ut så här:

ntttcp-r – m 8, \* , 10.0.0.4-t 300


Starta NTTTCP på avsändaren (**Kör från cmd**, inte från PowerShell):

ntttcp – s – m 8, \* , 10.0.0.4-t 300 

Vänta på resultaten.


## <a name="testing-vms-running-linux"></a>Testa virtuella datorer som kör LINUX:

Använd nttcp – för-Linux. Den är tillgänglig från <https://github.com/Microsoft/ntttcp-for-linux>

På virtuella Linux-datorer (både sändare och mottagare) kör du dessa kommandon för att förbereda ntttcp-för-Linux på dina virtuella datorer:

CentOS – Installera git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu – Installera git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Gör och installera på båda:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Som i Windows-exemplet antar vi att Linux-MOTTAGAREns IP-adress är 10.0.0.4

Starta NTTTCP – för – Linux på mottagaren:

``` bash
ntttcp -r -t 300
```

Och på avsändaren kör:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Test längden är som standard 60 sekunder om ingen tids parameter anges

## <a name="testing-between-vms-running-windows-and-linux"></a>Testning mellan virtuella datorer som kör Windows och LINUX:

I det här scenariot ska vi aktivera läget No-Sync så att testet kan köras. Detta görs med flaggan **-N** för Linux och **-ns flagga** för Windows.

#### <a name="from-linux-to-windows"></a>Från Linux till Windows:

Mottagare \<Windows> :

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Avsändare \<Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Från Windows till Linux:

Mottagare \<Linux> :

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Avsändare \<Windows> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testa moln tjänst instanser:
Du måste lägga till följande avsnitt i din service definition. csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Nästa steg
* Beroende på resultaten kan det finnas utrymme för att [optimera nätverks data flödes datorer](virtual-network-optimize-network-bandwidth.md) för ditt scenario.
* Läs om hur [bandbredd allokeras till virtuella datorer](virtual-machine-network-throughput.md)
* Lär dig mer med [Azure Virtual Network vanliga frågor och svar](virtual-networks-faq.md)