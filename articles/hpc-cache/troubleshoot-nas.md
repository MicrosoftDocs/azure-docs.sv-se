---
title: Felsöka Azure HPC cache NFS-lagrings mål
description: Tips för att undvika och åtgärda konfigurations fel och andra problem som kan orsaka fel när du skapar ett NFS-lagrings mål
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: v-erkel
ms.openlocfilehash: bb17918774d23dbeb2747fa55eefc4956812e254
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775705"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Felsök problem med NAS-konfiguration och NFS-lagring

Den här artikeln innehåller lösningar på några vanliga konfigurations fel och andra problem som kan förhindra att Azure HPC cache lägger till ett NFS-lagringssystem som ett lagrings mål.

Den här artikeln innehåller information om hur du kontrollerar portar och hur du aktiverar rot åtkomst till ett NAS-system. Den innehåller också detaljerad information om mindre vanliga problem som kan orsaka att NFS-lagrings mål inte kan skapas.

> [!TIP]
> Innan du använder den här guiden måste du läsa [kraven för NFS-lagrings mål](hpc-cache-prerequisites.md#nfs-storage-requirements).

Om lösningen på problemet inte ingår här kan du [öppna ett support ärende](hpc-cache-support-ticket.md) så att Microsoft-tjänsten och supporten kan arbeta tillsammans med dig för att undersöka och lösa problemet.

## <a name="check-port-settings"></a>Kontrol lera port inställningar

Azure HPC-cachen behöver Läs-/skriv åtkomst till flera UDP/TCP-portar på backend-serverns NAS-lagringssystem. Se till att dessa portar är tillgängliga i NAS-systemet och även att trafiken tillåts till dessa portar genom brand väggar mellan lagrings systemet och cache-undernätet. Du kan behöva arbeta med brand Väggs-och nätverks administratörer för ditt data Center för att verifiera konfigurationen.

Portarna skiljer sig från lagrings system från olika leverantörer, så kontrol lera systemets krav när du konfigurerar ett lagrings mål.

I allmänhet behöver cacheminnet åtkomst till dessa portar:

| Protokoll | Port  | Tjänst  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | monterad   |
| TCP/UDP  | 4047  | status   |

Använd följande kommando för att ta reda på vilka portar som behövs för ditt system ``rpcinfo`` . Det här kommandot nedan visar portarna och formaterar relevanta resultat i en tabell. (Använd systemets IP-adress i stället för *<storage_IP>* term.)

Du kan skicka det här kommandot från valfri Linux-klient som har NFS-infrastruktur installerad. Om du använder en klient i klustrets undernät kan du också kontrol lera anslutningen mellan under nätet och lagrings systemet.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Se till att alla portar som returneras av ``rpcinfo`` frågan tillåter obegränsad trafik från Azure HPC-nätets undernät.

Kontrol lera inställningarna både på själva nätverks servern och även på alla brand väggar mellan lagrings systemet och cache-undernätet.

## <a name="check-root-access"></a>Kontrol lera rot åtkomst

Azure HPC-cachen behöver åtkomst till lagrings systemets export för att skapa lagrings målet. Mer specifikt monteras exporten som användar-ID 0.

Olika lagrings system använder olika metoder för att aktivera den här åtkomsten:

* Linux-servrar läggs vanligt vis till i ``no_root_squash`` den exporterade sökvägen i ``/etc/exports`` .
* NetApp och EMC-system styr vanligt vis åtkomst med export regler som är kopplade till vissa IP-adresser eller nätverk.

Om du använder export regler måste du komma ihåg att cachen kan använda flera olika IP-adresser från cache-undernätet. Tillåt åtkomst från alla möjliga IP-adresser för undernät.

> [!NOTE]
> Även om cachen behöver rot åtkomst till Server dels lagrings systemet, kan du begränsa åtkomsten för klienter som ansluter via cacheminnet. Läs [kontrol lera klient åtkomst](access-policies.md#root-squash) för mer information.

Arbeta med din NAS-lagringsenhet för att aktivera rätt åtkomst nivå för cacheminnet.

### <a name="allow-root-access-on-directory-paths"></a>Tillåt rot åtkomst på katalog Sök vägar
<!-- linked in prereqs article -->

För NAS-system som exporterar hierarkiska kataloger behöver Azure HPC-cache rot åtkomst till varje export nivå.

Ett system kan till exempel Visa tre exporter som följande:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

Exporten ``/ifs/accounting/payroll`` är underordnad ``/ifs/accounting`` , och ``/ifs/accounting`` är i själva fallet underordnad ``/ifs`` .

Om du lägger till ``payroll`` ett lagrings mål för att exportera som ett HPC-cacheminne monterar cacheminnet i själva verket ``/ifs/`` och använder löne katalogen därifrån. Därför behöver Azure HPC-cache rot åtkomst till för ``/ifs`` att komma åt ``/ifs/accounting/payroll`` exporten.

Detta krav är relaterat till hur cacheminnet indexerar filer och undviker fil kollisioner med hjälp av fil referenser som lagrings systemet tillhandahåller.

Ett NAS-system med hierarkiska exporter kan ge olika fil referenser för samma fil om filen hämtas från olika exporter. En-klient kan till exempel montera ``/ifs/accounting`` och komma åt filen ``payroll/2011.txt`` . En annan klient monterar ``/ifs/accounting/payroll`` och kommer åt filen ``2011.txt`` . Beroende på hur lagrings systemet tilldelar fil referenser, kan dessa två klienter få samma fil med olika fil referenser (en för ``<mount2>/payroll/2011.txt`` och en ``<mount3>/2011.txt`` ).

Server dels lagrings systemet behåller interna alias för fil referenser, men Azure HPC-cachen kan inte avgöra vilka fil referenser i index referensen till samma objekt. Det är därför möjligt att cachen kan ha olika skrivningar i cacheminnet för samma fil och tillämpa ändringarna på fel sätt eftersom det inte vet att de är samma fil.

För att undvika den här möjliga filkollisionen för filer i flera exporter monterar Azure HPC cache automatiskt den mest tillgängliga exporten i sökvägen ( ``/ifs`` i exemplet) och använder fil referensen från den exporten. Om flera exporter använder samma bas Sök väg behöver Azure HPC-cache rot åtkomst till den sökvägen.

<!-- ## Enable export listing

The NAS must list its exports when the Azure HPC Cache queries it.

On most NFS storage systems, you can test this by sending the following query from a Linux client: ``showmount -e <storage IP address>``

Use a Linux client from the same virtual network as your cache, if possible.

If that command doesn't list the exports, the cache will have trouble connecting to your storage system. Work with your NAS vendor to enable export listing.  -->

## <a name="adjust-vpn-packet-size-restrictions"></a>Ändra storleks begränsningar för VPN-paket
<!-- link in prereqs article and configuration article -->

Om du har en VPN-anslutning mellan cachen och NAS-enheten kan VPN-nätverket blockera byte i full storlek 1500 byte. Du kan ha det här problemet om stora byten mellan NAS och Azure HPC-instansen inte slutförs, men mindre uppdateringar fungerar som förväntat.

Det finns inget enkelt sätt att avgöra om systemet har det här problemet om du inte känner till informationen om VPN-konfigurationen. Här följer några metoder som kan hjälpa dig att söka efter det här problemet.

* Använd paket-sniffer på båda sidor av VPN för att identifiera vilka paket som överförs.
* Om ditt VPN tillåter ping-kommandon kan du testa att skicka ett paket med full storlek.

  Kör ett ping-kommando via VPN till NAS med dessa alternativ. (Använd lagrings systemets IP-adress i stället för *<storage_IP>* värde.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Detta är alternativen i kommandot:

  * ``-M do`` -Fragmentera inte
  * ``-c 1`` -Skicka bara ett paket
  * ``-s 1472`` – Ange storleken på nytto lasten till 1472 byte. Detta är den maximala nytto lasten för ett 1500 byte-paket efter det att Ethernet-belastningen har ändrats.

  Ett lyckat svar ut så här:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Om pingen Miss lyckas med 1472 byte, beror det förmodligen på problem med paket storlek.

För att åtgärda problemet kan du behöva konfigurera MSS-ihopfogning på VPN för att fjärrsystemet ska kunna identifiera den maximala ram storleken. Läs mer i [dokumentationen för VPN gateway IPSec/IKE-parametrarna](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) .

I vissa fall kan det hjälpa att ändra MTU-inställningen för Azure HPC-cachen till 1400. Men om du begränsar MTU för cacheminnet måste du också begränsa MTU-inställningarna för klienter och backend-lagringsenheter som interagerar med cachen. Mer information finns i [Konfigurera ytterligare Azure HPC cache-inställningar](configuration.md#adjust-mtu-value) .

## <a name="check-for-acl-security-style"></a>Kontrol lera formatet ACL-säkerhet

Vissa NAS-system använder en hybrid säkerhets stil som kombinerar åtkomst kontrol listor (ACL: er) med traditionell POSIX-eller UNIX-säkerhet.

Om systemet rapporterar sin säkerhets stil som UNIX eller POSIX utan att inkludera förkortningen "ACL", påverkar det här problemet inte.

För system som använder ACL: er måste Azure HPC-cachen spåra ytterligare användarspecifika värden för att kontrol lera fil åtkomsten. Detta görs genom att aktivera cache för åtkomst. Det finns ingen användar kontroll för att aktivera åtkomst-cachen, men du kan öppna ett support ärende om du vill begära att den aktive ras för de lagrings mål som påverkas i ditt cache-system.

## <a name="next-steps"></a>Nästa steg

Om du har problem som inte har åtgärd ATS i den här artikeln [öppnar du ett support ärende](hpc-cache-support-ticket.md) för att få expert hjälp.
