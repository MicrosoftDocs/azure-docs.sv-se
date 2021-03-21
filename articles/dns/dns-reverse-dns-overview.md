---
title: Översikt över omvänd DNS i Azure – Azure DNS
description: I den här utbildnings vägen kommer du igång med att lära dig hur omvänd DNS fungerar och hur det kan användas i Azure
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 8af9549efc3e8dab54f55dd404346d87201dee2c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965620"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Översikt över omvänd DNS och support i Azure

Den här artikeln ger en översikt över hur omvänd DNS fungerar och de omvända DNS-scenarier som stöds i Azure.

## <a name="what-is-reverse-dns"></a>Vad är omvänd DNS?

Konventionella DNS-poster möjliggör mappning från ett DNS-namn (t. ex. "www.contoso.com") till en IP-adress (t. ex. 64.4.6.100).  Omvänd DNS aktiverar översättning av en IP-adress (64.4.6.100) tillbaka till ett namn (' www.contoso.com ').

Omvända DNS-poster används i olika situationer. Omvända DNS-poster används till exempel ofta för att bekämpa e-postspam genom att verifiera avsändaren av ett e-postmeddelande.  Den mottagande e-postservern hämtar den omvända DNS-posten för den sändande serverns IP-adress och kontrollerar om värden har behörighet att skicka e-post från den ursprungliga domänen. 

## <a name="how-reverse-dns-works"></a>Så här fungerar omvänd DNS

Omvända DNS-poster finns i särskilda DNS-zoner, som kallas "ARPA"-zoner.  Dessa zoner utgör en separat DNS-hierarki parallellt med den normala hierarkin som är värd för domäner som "contoso.com".

DNS-posten "www.contoso.com" implementeras till exempel med en DNS-post med namnet "www" i zonen "contoso.com".  Den här posten pekar på motsvarande IP-adress, i det här fallet 64.4.6.100.  Den omvända sökningen implementeras separat med hjälp av en PTR-post med namnet 100 i zonen 6.4.64.in-addr. arpa (Observera att IP-adresser återförs i ARPA-zoner.)  Den här PTR-posten, om den har kon figurer ATS korrekt, pekar på namnet "www.contoso.com".

När en organisation tilldelas ett IP-adressblock, kan de också få behörighet att hantera motsvarande ARPA-zon. De ARPA-zoner som motsvarar de IP-adressblock som används av Azure finns och hanteras av Microsoft. Din Internet leverantör kan vara värd för ARPA-zonen för dina egna IP-adresser åt dig, eller så kan du vara värd för ARPA-zonen i en DNS-tjänst som du väljer, till exempel Azure DNS.

> [!NOTE]
> Vidarebefordring av DNS-sökningar och omvänd DNS-sökning implementeras i separata, parallella DNS-hierarkier. Omvänd sökning efter ' www.contoso.com ' finns **inte** i zonen ' contoso.com ', i stället finns den i arpa-zonen för motsvarande IP-adressblock. Separata zoner används för IPv4-och IPv6-Adressblock.

### <a name="ipv4"></a>IPv4

Namnet på en IPv4-zon för omvänd sökning ska ha följande format: `<IPv4 network prefix in reverse order>.in-addr.arpa` .

När du till exempel skapar en omvänd zon som värd för poster för värdar med IP-adresser som finns i 192.0.2.0/24-prefixet, skapas zon namnet genom att isolera adressens nätverksprefix (192.0.2) och sedan återställa ordningen (2.0.192) och lägga till suffixet `.in-addr.arpa` .

|Under näts klass|Nätverksprefix  |Omvänt nätverksprefix  |Standard-suffix  |Namn på omvänd zon |
|-------|----------------|------------|-----------------|---------------------------|
|Klass A|203.0.0.0/8     | 203        | . in-addr. arpa   | `203.in-addr.arpa`        |
|Klass B|198.51.0.0/16   | 51,198     | . in-addr. arpa   | `51.198.in-addr.arpa`     |
|Klass C|192.0.2.0/24    | 2.0.192    | . in-addr. arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Klass avklassificerad IPv4-delegering

I vissa fall är det IP-adressintervall som tilldelas en organisation mindre än ett intervall för klass C (/24). I det här fallet faller IP-intervallet inte över en zon gränser inom `.in-addr.arpa` zonfilen och kan därför inte delegeras som en underordnad zon.

I stället används en annan mekanism för att överföra kontroll av PTR-poster (individuell omvänd sökning) till en dedikerad DNS-zon. Den här mekanismen delegerar en underordnad zon för varje IP-intervall och mappar sedan varje IP-adress i intervallet individuellt till den underordnade zonen med CNAME-poster.

Anta till exempel att en organisation beviljas IP-intervallet 192.0.2.128/26 av sin Internet leverantör. Detta representerar 64 IP-adresser från 192.0.2.128 till 192.0.2.191. Omvänd DNS för intervallet implementeras på följande sätt:
- Organisationen skapar en zon för omvänd sökning som heter 128-26.2.0.192.in-addr. arpa. Prefixet "128-26" representerar det nätverks segment som tilldelats organisationen inom klass C-intervallet (/24).
- Internet leverantören skapar NS-poster för att ställa in DNS-delegeringen för zonen ovan från den överordnade klass C-zonen. Det skapar också CNAME-poster i den överordnade zonen (klass C) omvänd sökning, och mappar varje IP-adress i IP-intervallet till den nya zon som skapats av organisationen:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- Organisationen hanterar sedan de enskilda PTR-posterna i den underordnade zonen.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
En omvänd sökning efter IP-adressen "192.0.2.129" för en PTR-post med namnet "129.2.0.192.in-addr. arpa". Den här frågan matchar via CNAME i den överordnade zonen till PTR-posten i den underordnade zonen.

### <a name="ipv6"></a>IPv6

Namnet på en IPv6-zon för omvänd sökning ska ha följande format: `<IPv6 network prefix in reverse order>.ip6.arpa`

Till exempel. När du skapar en omvänd zon som värd för poster för värdar med IP-adresser som finns i avsnittet 2001: db8:1 000: ABDC::/64, skulle zon namnet skapas genom att isolera-adressens nätverksprefix (2001: db8: ABDC::). Nästa steg är att expandera IPv6-nätverksprefix för att ta bort [noll](/previous-versions/windows/it-pro/windows-server-2003/cc781672(v=ws.10)), om det användes för att förkorta IPv6-adressprefixet (2001:0DB8: ABDC: 0000::). Ändra ordningen genom att använda en period som avgränsare mellan varje hexadecimalt tal i prefixet för att skapa det omvända nätverksprefix ( `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2` ) och lägga till suffixet `.ip6.arpa` .


|Nätverksprefix  |Expanderat och omvänt nätverksprefix |Standard-suffix |Namn på omvänd zon  |
|---------|---------|---------|---------|
|2001: db8: ABDC::/64    | 0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2        | . ip6. arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001: db8:1 000:9102::/64    | 2.0.1.9.0.0.0.1.8. b. d. 0.1.0.0.2        | . ip6. arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Azure-stöd för omvänd DNS

Azure har stöd för två separata scenarier som rör omvänd DNS:

**Värd för den zon för omvänd sökning som motsvarar ditt IP-adressblock.**
Azure DNS kan användas som [värd för zoner för omvänd sökning och hantera PTR-posterna för varje omvänd DNS-sökning](dns-reverse-dns-hosting.md)för både IPv4 och IPv6.  Processen för att skapa zonen för omvänd sökning (ARPA), konfigurera delegeringen och konfigurera PTR-poster är samma som för vanliga DNS-zoner.  De enda skillnaderna är att delegeringen måste konfigureras via din Internet leverantör i stället för din DNS-registrator, och det är bara PTR-posttypen som ska användas.

**Konfigurera den omvända DNS-posten för den IP-adress som tilldelats din Azure-tjänst.** Med Azure kan du [Konfigurera omvänd sökning efter de IP-adresser som allokeras till din Azure-tjänst](dns-reverse-dns-for-azure-services.md).  Den här omvända sökningen konfigureras av Azure som en PTR-post i motsvarande ARPA-zon.  Dessa ARPA-zoner, som motsvarar alla IP-intervall som används av Azure, är värdbaserade av Microsoft

## <a name="next-steps"></a>Nästa steg

Mer information om omvänd DNS finns i [Omvänd DNS-sökning på Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig hur du kan vara [värd för zonen för omvänd sökning för det IP-adressintervall som tilldelas Internet leverantör i Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Lär dig att [Hantera omvända DNS-poster för dina Azure-tjänster](dns-reverse-dns-for-azure-services.md).