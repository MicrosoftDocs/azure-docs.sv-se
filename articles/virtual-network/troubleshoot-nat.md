---
title: Felsöka Azure Virtual Network NAT-anslutning
titleSuffix: Azure Virtual Network
description: Felsök problem med Virtual Network NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2020
ms.author: allensu
ms.openlocfilehash: 70410e58acb30c7694e6fe4a6dcaff57bee98607
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223439"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Felsöka Azure Virtual Network NAT-anslutning

Den här artikeln hjälper administratörer att diagnostisera och lösa anslutnings problem när de använder Virtual Network NAT.

## <a name="problems"></a>Problem

* [SNAT-belastning](#snat-exhaustion)
* [ICMP-Ping fungerar inte](#icmp-ping-is-failing)
* [Anslutnings problem](#connectivity-failures)
* [IPv6-samexistens](#ipv6-coexistence)
* [Anslutningen kommer inte från NAT-gatewayens IP-adress (er)](#connection-doesnt-originate-from-nat-gateway-ips)

Följ stegen i följande avsnitt för att lösa problemen.

## <a name="resolution"></a>Lösning

### <a name="snat-exhaustion"></a>SNAT-belastning

En enda [NAT-gateway-resurs](nat-gateway-resource.md) stöder från 64 000 upp till 1 000 000 samtidiga flöden.  Varje IP-adress ger 64 000 SNAT-portar till tillgängligt lager. Du kan använda upp till 16 IP-adresser per NAT-gateway-resurs.  SNAT-mekanismen beskrivs [här](nat-gateway-resource.md#source-network-address-translation) i detalj.

Ofta är rotor saken av SNAT-belastning ett antimönster för hur utgående anslutningar upprättas, hanteras eller konfigurerbara timers som ändras från standardvärdena.  Granska det här avsnittet noggrant.

#### <a name="steps"></a>Steg

1. Kontrol lera om du har ändrat standard tids gränsen för inaktivitet till ett värde som är högre än 4 minuter.
2. Undersök hur programmet skapar utgående anslutning (till exempel kod granskning eller paket fångst). 
3. Avgöra om den här aktiviteten är förväntad eller om programmet är felgenererat.  Använd [mått](nat-metrics.md) i Azure Monitor för att styrka dina resultat. Använd kategorin "misslyckades" för måttet för SNAT-anslutningar.
4. Utvärdera om lämpliga mönster följs.
5. Utvärdera om SNAT-portens överbelastning ska begränsas med ytterligare IP-adresser tilldelade till NAT-gateway-resursen.

#### <a name="design-patterns"></a>Designmönster

Dra alltid nytta av anslutnings åter användning och anslutningspoolen närhelst det är möjligt.  De här mönstren kommer att undvika problem med resurs överbelastning och resultera i förutsägbara beteenden. Primitiver för dessa mönster finns i många utvecklings bibliotek och ramverk.

_**Lösning:**_ Använd lämpliga mönster och bästa metoder

- NAT-gatewayens resurser har en förinställd timeout för TCP-inaktivitet på 4 minuter.  Om den här inställningen ändras till ett högre värde, kommer NAT att vänta på flöden längre och kan orsaka [onödigt tryck på SNAT-port inventeringen](nat-gateway-resource.md#timers).
- Atomiska förfrågningar (en förfrågan per anslutning) är ett dåligt design val. Sådana kant mönster gränser skalar, minskar prestandan och minskar tillförlitligheten. I stället kan du använda HTTP/S-anslutningar för att minska antalet anslutningar och associerade SNAT-portar. Program skalan ökar och förbättrar prestandan på grund av minskad hand skakning, kostnader för omkostnader och kryptografiska åtgärder när du använder TLS.
- DNS kan introducera många enskilda flöden på volymen när klienten inte cachelagrar resultatet av DNS-matcharen. Använd cachelagring.
- UDP-flöden (till exempel DNS-sökningar) allokera SNAT-portar för varaktigheten för tids gränsen för inaktivitet. Ju längre tids gräns för inaktivitet, desto högre belastning på SNAT-portar. Använd kort tids gräns för inaktivitet (till exempel 4 minuter).
- Använd anslutningspooler för att forma din anslutnings volym.
- Överge aldrig ett TCP-flöde och Använd TCP-timers för att rensa flödet. Om du inte tillåter TCP att uttryckligen stänga anslutningen är tillstånd fortfarande allokerat på mellan system och slut punkter och gör SNAT-portar otillgängliga för andra anslutningar. Det här mönstret kan utlösa program haverier och SNAT-belastningar. 
- Ändra inte TCP/Close-relaterade timer-värden i OS-nivå utan expert kunskaper om påverkan. Även om TCP-stacken kommer att återställas kan program prestandan påverkas negativt när slut punkterna för en anslutning har fel matchnings förväntningar. De vill ändra timers är vanligt vis ett tecken på ett underliggande design problem. Granska följande rekommendationer.

SNAT-belastningen kan också förstärkas med andra anti-mönster i det underliggande programmet. Granska dessa ytterligare mönster och bästa metoder för att förbättra tjänstens omfattning och tillförlitlighet.

- Utforska effekten av att minska timeout-värdet för [TCP-inaktivitet](nat-gateway-resource.md#timers) till lägre värden, inklusive standard tids gräns för inaktivitet på 4 minuter för att frigöra antalet SNAT
- Överväg att använda [asynkrona avsöknings mönster](/azure/architecture/patterns/async-request-reply) för långvariga åtgärder för att frigöra anslutnings resurser för andra åtgärder.
- Långvariga flöden (till exempel återanvända TCP-anslutningar) bör använda TCP keepalive-eller Application Layer-keepalive för att undvika tids gränser för mellanliggande system. Att öka tids gränsen för inaktivitet är en sista utväg och kan inte lösa rotor saken. En lång timeout kan orsaka låg hastighets fel när tids gränsen upphört och orsakar fördröjning och onödiga fel.
- Återställnings [mönster](/azure/architecture/patterns/retry) bör användas för att undvika aggressiva återförsök/burst vid tillfälliga fel eller återställning av fel.
Att skapa en ny TCP-anslutning för varje HTTP-åtgärd (kallas även "atomiska anslutningar") är en anti-mönster.  Atomiska anslutningar förhindrar att ditt program skalar bra och slöseri med resurser.  Pipelinerar alltid flera åtgärder i samma anslutning.  Ditt program kommer att ha nytta av transaktions hastighet och resurs kostnader.  När ditt program använder kryptering av transport skikt (t. ex. TLS), är det en betydande kostnad för bearbetningen av nya anslutningar.  Granska [moln design mönster i Azure](/azure/architecture/patterns/) om du vill ha ytterligare tips.

#### <a name="additional-possible-mitigations"></a>Ytterligare eventuella begränsningar

_**Lösning:**_ Skala utgående anslutningar på följande sätt:

| Scenario | Grund |Åtgärd |
|---|---|---|
| Du har konkurrens för SNAT-portar och antalet SNAT-portar under perioder med hög användning. | Kategorin "misslyckades" för [mått](nat-metrics.md) i SNAT-anslutningar i Azure Monitor visar tillfälliga eller permanenta fel över tid och hög anslutnings volym.  | Avgör om du kan lägga till ytterligare offentliga IP-adressresurser eller resurser för offentliga IP-prefix. Detta tillägg tillåter att upp till 16 IP-adresser sammanlagt till din NAT-gateway. Det här tillägget ger mer lager för tillgängliga SNAT-portar (64 000 per IP-adress) och gör att du kan skala ditt scenario ytterligare.|
| Du har redan fått 16 IP-adresser och har fortfarande överbelastning av SNAT-portar. | Försök att lägga till ytterligare IP-adress misslyckades. Det totala antalet IP-adresser från offentliga IP-adressresurser eller resurser för offentliga IP-prefix överskrider totalt 16. | Distribuera din program miljö över flera undernät och ange en NAT-gateway-resurs för varje undernät.  Utvärdera dina design mönster på ett optimerat sätt baserat på föregående [vägledning](#design-patterns). |

>[!NOTE]
>Det är viktigt att förstå varför SNAT-belastning sker. Se till att du använder rätt mönster för skalbara och tillförlitliga scenarier.  Att lägga till fler SNAT-portar i ett scenario utan att förstå orsaken till behovet bör vara en sista utväg. Om du inte förstår varför ditt scenario använder trycket på SNAT-port inventeringen och lägger till fler SNAT-portar i inventeringen genom att lägga till fler IP-adresser, fördröjs bara samma överbelastnings problem när ditt program skalas.  Du kan maskera andra ineffektiva och anti-mönster.

### <a name="icmp-ping-is-failing"></a>ICMP-Ping fungerar inte

[Virtual Network NAT](nat-overview.md) stöder IPv4 UDP-och TCP-protokoll. ICMP stöds inte och förväntas inte fungera.  

_**Lösning:**_ Använd i stället TCP-anslutnings test (till exempel "TCP-ping") och UDP-specifika program lager för att validera slut punkt till slut punkt anslutning.

Följande tabell kan användas som start punkt för vilka verktyg som ska användas för att starta tester.

| Operativsystem | Allmän test av TCP-anslutning | Test av TCP-program | UDP |
|---|---|---|---|
| Linux | NC (allmän anslutnings test) | Sväng (TCP-programnivå test) | programspecifik |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [Invoke-webbegäran](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | programspecifik |

### <a name="connectivity-failures"></a>Anslutnings problem

Anslutnings problem med [Virtual Network NAT](nat-overview.md) kan bero på flera olika problem:

* permanenta fel på grund av konfigurations fel.
* tillfällig eller beständig [SNAT-belastning](#snat-exhaustion) på NAT-gatewayen
* tillfälliga haverier i Azure-infrastrukturen, 
* tillfälliga haverier i sökvägen mellan Azure och det offentliga Internet målet 
* tillfälliga eller permanenta haverier på det offentliga Internet målet.

Använd verktyg som följande för att verifiera anslutningen. [ICMP-Ping stöds inte](#icmp-ping-is-failing).

| Operativsystem | Allmän test av TCP-anslutning | Test av TCP-program | UDP |
|---|---|---|---|
| Linux | NC (allmän anslutnings test) | Sväng (TCP-programnivå test) | programspecifik |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [Invoke-webbegäran](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | programspecifik |

#### <a name="configuration"></a>Konfiguration

Kontrol lera konfigurationen:
1. Har NAT-gatewayresursen minst en offentlig IP-resurs eller en resurs för offentliga IP-prefix? Du måste ha minst en IP-adress som är kopplad till NAT-gatewayen för att den ska kunna ge en utgående anslutning.
2. Är det virtuella nätverkets undernät konfigurerat för att använda NAT-gatewayen?
3. Använder du UDR (användardefinierad väg) och åsidosätter du målet?  NAT-gatewayens resurser blir standard vägen (0/0) på konfigurerade undernät.

#### <a name="snat-exhaustion"></a>SNAT-belastning

Granska avsnittet om [SNAT-inblåsning](#snat-exhaustion) i den här artikeln.

#### <a name="azure-infrastructure"></a>Azure-infrastruktur

Azure övervakar och arbetar med sin infrastruktur med gott om service. Tillfälliga fel kan uppstå, det finns ingen garanti för att överföringen är förlustfri.  Använd design mönster som tillåter SYN återöverföringar för TCP-program. Använd anslutnings-timeout tillräckligt stor för att tillåta TCP-dataöverföring för att minska den tillfälliga påverkan som orsakas av ett förlorat SYN-paket.

_**Lösa**_

* Sök efter [SNAT-belastning](#snat-exhaustion).
* Konfigurations parametern i en TCP-stack som styr SYN återöverförings beteendet kallas RTO ([timeout för återöverföring](https://tools.ietf.org/html/rfc793)). RTO-värdet är justerbart, men vanligt vis 1 sekund eller högre som standard med exponentiella säkerhets kopieringar.  Om programmets anslutnings-timeout är för kort (till exempel 1 sekund) kan du se sporadisk tids gräns för anslutning.  Öka tids gränsen för program anslutningen.
* Om du anser att längre, oväntade tids gränser med standard program beteenden, öppnar du ett support ärende för ytterligare fel sökning.

Vi rekommenderar inte artificiellt minska TCP-anslutningens tids gräns eller justera RTO-parametern.

#### <a name="public-internet-transit"></a>Offentlig Internet överföring

Risken för tillfälliga haverier ökar med en längre sökväg till målet och fler mellanliggande system. Det förväntas att tillfälliga haverier kan öka i frekvens över [Azure-infrastrukturen](#azure-infrastructure). 

Följ samma rikt linjer som i avsnittet föregående [Azure-infrastruktur](#azure-infrastructure) .

#### <a name="internet-endpoint"></a>Internet slut punkt

Föregående avsnitt gäller, tillsammans med Internet slut punkten som kommunikationen upprättas med. Andra faktorer som kan påverka anslutningen lyckas är:

* trafik hantering på mål sidan, inklusive
- API-hastighet som begränsas av mål Sidan
- DDoS-åtgärder för volym begränsning eller transport skikt
* brand vägg eller andra komponenter på målet 

Paket som samlas in på källan och målet (om det är tillgängligt) måste vanligt vis ta reda på vad som sker.

_**Lösa**_

* Sök efter [SNAT-belastning](#snat-exhaustion). 
* Verifiera anslutningen till en slut punkt i samma region eller någon annan stans för jämförelse.  
* Om du skapar hög volym-eller transaktions frekvens test bör du undersöka om du minskar hastigheten för felaktiga händelser.
* Om ändrings takten påverkar antalet fel, kontrollerar du om begränsningar för API-hastighet eller andra begränsningar på mål sidan kan ha nåtts.
* Om din undersökning är tveksam kan du öppna ett support ärende för ytterligare fel sökning.

#### <a name="tcp-resets-received"></a>Mottagna TCP-reuppsättningar

NAT-gatewayen genererar TCP-återställning på den virtuella käll datorn för trafik som inte är identifierad som pågående.

En möjlig orsak är att TCP-anslutningen har nått tids gränsen för inaktivitet.  Du kan justera tids gränsen för inaktivitet från 4 minuter till upp till 120 minuter.

TCP-återställning genereras inte på den offentliga sidan av NAT-gatewayens resurser. TCP-återställning på mål sidan genereras av den virtuella käll datorn, inte NAT-gateway-resursen.

_**Lösa**_

* Granska [design mönster](#design-patterns) rekommendationer.  
* Öppna ett support ärende om du behöver ytterligare fel sökning.

### <a name="ipv6-coexistence"></a>IPv6-samexistens

[Virtual Network NAT](nat-overview.md) stöder IPv4 UDP-och TCP-protokoll och distribution på ett [undernät med ett IPv6-prefix stöds inte](nat-overview.md#limitations).

_**Lösning:**_ Distribuera NAT-gateway i ett undernät utan IPv6-prefix.

Du kan ange intresse för ytterligare funktioner via [Virtual Network NAT UserVoice](https://aka.ms/natuservoice).

### <a name="connection-doesnt-originate-from-nat-gateway-ips"></a>Anslutningen kommer inte från NAT-gatewayens IP-adress (er)

Du konfigurerar NAT-gateway, IP-adress (er) som ska användas och vilket undernät som ska använda en NAT-gateway-resurs. Men anslutningar från virtuella dator instanser som fanns innan NAT-gatewayen distribuerades använder inte IP-adressen (ES).  De verkar använda IP-adress (er) som inte används med NAT gateway-resursen.

_**Lösa**_

[Virtual Network NAT](nat-overview.md) ersätter den utgående anslutningen för under nätet som den är konfigurerad på. När du övergår från standard SNAT eller belastningsutjämnaren utgående SNAT till att använda NAT-gatewayer börjar nya anslutningar omedelbart använda de IP-adresser som är associerade med NAT-gateway-resursen.  Men om en virtuell dator fortfarande har en upprättad anslutning under växeln till NAT gateway-resurs fortsätter anslutningen att använda den gamla SNAT-IP-adressen som tilldelades när anslutningen upprättades.  Se till att du verkligen upprättar en ny anslutning i stället för att återanvända en anslutning som redan funnits på grund av att operativ systemet eller webbläsaren cachelagrade anslutningarna i en anslutningspool.  När du använder _sväng_ i PowerShell ska du till exempel se till att ange parametern _-DisableKeepalive_ för att tvinga en ny anslutning.  Om du använder en webbläsare kan anslutningarna också placeras i pooler.

Du behöver inte starta om en virtuell dator som konfigurerar ett undernät för en NAT gateway-resurs.  Men om en virtuell dator startas om, töms anslutnings läget.  När anslutnings statusen har tömts kommer alla anslutningar att börja använda NAT-gatewayens IP-adress (er).  Detta är dock en sido effekt på den virtuella datorn som startas om och inte en indikator som kräver en omstart.

Om du fortfarande har problem kan du öppna ett support ärende för ytterligare fel sökning.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Virtual Network NAT](nat-overview.md)
* Lär dig om [NAT gateway-resurs](nat-gateway-resource.md)
* Lär dig mer om [mått och aviseringar för NAT gateway-resurser](nat-metrics.md).
* [Berätta för oss vad du ska bygga härnäst för Virtual Network NAT i UserVoice](https://aka.ms/natuservoice).