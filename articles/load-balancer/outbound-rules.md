---
title: Regler för utgående Azure Load Balancer
description: Den här artikeln förklarar hur du konfigurerar utgående regler för att styra utgående Internettrafik med Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 339bbd7edf48737113de360812165dc8148c5b93
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375873"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Regler för utgående Azure Load Balancer

Med regler för utgående trafik kan du uttryckligen definiera SNAT (källnätverksadressöversättning) för en offentlig standardlastbalanserare. Med den här konfigurationen kan du använda de offentliga IP-adresserna för din lastbalanserare för att tillhandahålla utgående Internetanslutning för dina serverinstanser.

Den här konfigurationen möjliggör:

* IP-maskering
* Förenkla listan över tillåtna.
* Minskar antalet offentliga IP-resurser för distribution.

Med regler för utgående trafik har du fullständig deklarativ kontroll över utgående Internetanslutning. Med regler för utgående trafik kan du skala och justera den här möjligheten efter dina specifika behov. 

Regler för utgående trafik följs endast om den virtuella datorn i backend-instansen inte har en offentlig IP-adress på instansnivå (ILPIP).

![Load Balancer regler för utgående trafik](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Med regler för utgående trafik kan du uttryckligen definiera utgående **SNAT-beteende.**

Med regler för utgående trafik kan du styra:

* **Vilka virtuella datorer som översätts till vilka offentliga IP-adresser.**
     * Två regler var backend pool 1 använder den blå IP-adressen 1 och 2, backend pool 2 använder det gula IP-prefixet.
* **Hur utgående SNAT-portar allokeras.**
     * Om backend-pool 2 är den enda poolen som gör utgående anslutningar ger du alla SNAT-portar till backend-poolen 2 och ingen till backend-poolen 1.
* **Vilka protokoll som utgående översättning ska tillhandahållas för.**
     * Om backend pool 2 behöver UDP-portar för utgående och backend-pool 1 behöver TCP, ge TCP-portar till 1- och UDP-portar till 2.
* **Vilken varaktighet ska användas för utgående tidsgräns för inaktivitet i anslutning (4–120 minuter).**
     * Om det finns långvariga anslutningar med keepalives reserverar du inaktiva portar för långvariga anslutningar i upp till 120 minuter. Anta att inaktuella anslutningar har övergivits och släpper portarna på 4 minuter för nya anslutningar 
* **Om du vill skicka en TCP-återställning vid tidsgräns för inaktivitet.**
     * Skickar vi en TCP RST till klienten och servern så att de vet att flödet är övergivet när vi tar tid på inaktiva anslutningar?

## <a name="outbound-rule-definition"></a>Regeldefinition för utgående trafik

Regler för utgående trafik följer samma välbekanta syntax som belastningsutjämning och inkommande NAT-regler:  +    +  **backend-parametrarna i backend-poolen**. 

En regel för utgående trafik konfigurerar utgående NAT för alla virtuella datorer som identifieras av _serverpoolen_ som ska översättas _till frontend_.  

_Parametrarna_ ger ytterligare ingående kontroll över den utgående NAT-algoritmen.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Skala utgående NAT med flera IP-adresser

Varje ytterligare IP-adress som tillhandahålls av en frontend ger ytterligare 64 000 tillfälliga portar som lastbalanserare kan använda som SNAT-portar. 

Använd flera IP-adresser för att planera för storskaliga scenarier. Använd regler för utgående trafik för att [minimera SNAT-utmattning.](troubleshoot-outbound-connection.md#snatexhaust) 

Du kan också använda ett offentligt [IP-prefix](./load-balancer-outbound-connections.md#outboundrules) direkt med en regel för utgående trafik. 

Ett offentligt IP-prefix ökar skalningen av distributionen. Prefixet kan läggas till i listan över tillåtna flöden som kommer från dina Azure-resurser. Du kan konfigurera en IP-konfiguration för serversidan i lastbalanseraren för att referera till ett offentligt IP-adressprefix.  

Lastbalanseraren har kontroll över det offentliga IP-prefixet. Regeln för utgående trafik använder automatiskt alla offentliga IP-adresser som finns i det offentliga IP-prefixet för utgående anslutningar. 

Var och en av IP-adresserna inom det offentliga IP-prefixet ger ytterligare 64 000 tillfälliga portar per IP-adress som lastbalanserare kan använda som SNAT-portar.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Tidsgräns för inaktivitet i utgående flöde och TCP-återställning

Regler för utgående trafik innehåller en konfigurationsparameter för att styra tidsgränsen för inaktivitet i utgående flöde och matcha den efter behoven i ditt program. Utgående tidsgräns för inaktivitet är som standard 4 minuter. Mer information finns i Konfigurera [tidsgränser för inaktivitet.](load-balancer-tcp-idle-timeout.md) 

Standardbeteendet för lastbalanserare är att tyst släppa flödet när den utgående tidsgränsen för inaktivitet har uppnåtts. Parametern `enableTCPReset` möjliggör ett förutsägbart programbeteende och kontroll. Parametern anger om du vill skicka dubbelriktad TCP-återställning (TCP RST) vid tidsgränsen för den utgående tidsgränsen för inaktivitet. 

Mer [information om bland annat tillgänglighet för regioner finns i TCP-återställning](./load-balancer-tcp-reset.md) vid tidsgräns för inaktivitet.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Skydda och kontrollera utgående anslutningar explicit

Belastningsutjämningsregler ger automatisk programmering av utgående NAT. Vissa scenarier drar nytta av eller kräver att du inaktiverar automatisk programmering av utgående NAT med belastningsutjämningsregeln. Om du inaktiverar via regeln kan du styra eller förfina beteendet.  

Du kan använda den här parametern på två sätt:

1. Skydd av den inkommande IP-adressen för utgående SNAT. Inaktivera utgående SNAT i belastningsutjämningsregeln.
  
2. Justera de utgående **SNAT-parametrarna** för en IP-adress som används för inkommande och utgående samtidigt. Den automatiska utgående NAT:n måste inaktiveras för att tillåta att en regel för utgående trafik får kontroll. Om du vill ändra SNAT-portallokeringen för en adress som också används för inkommande `disableOutboundSnat` trafik måste parametern anges till true. 

Åtgärden för att konfigurera en regel för utgående trafik misslyckas om du försöker omdefiniera en IP-adress som används för inkommande trafik.  Inaktivera den utgående NAT:en för belastningsutjämningsregeln först.

>[!IMPORTANT]
> Den virtuella datorn har ingen utgående anslutning om du anger den här parametern till true och inte har en regel för utgående trafik för att definiera utgående anslutning.  Vissa åtgärder på den virtuella datorn eller programmet kan vara beroende av att du har en tillgänglig utgående anslutning. Se till att du förstår beroendena i ditt scenario och har övervägt effekten av att göra den här ändringen.

Ibland är det oönskat för en virtuell dator att skapa ett utgående flöde. Det kan finnas ett krav på att hantera vilka mål som tar emot utgående flöden eller vilka mål som börjar med inkommande flöden. Använd [nätverkssäkerhetsgrupper](../virtual-network/network-security-groups-overview.md) för att hantera de mål som den virtuella datorn når. Använd NSG:er för att hantera vilka offentliga mål som startar inkommande flöden.

När du tillämpar en NSG på en belastningsutjämnad virtuell dator bör du vara uppmärksam på [tjänsttaggar](../virtual-network/network-security-groups-overview.md#service-tags) och [standardsäkerhetsregler.](../virtual-network/network-security-groups-overview.md#default-security-rules) 

Kontrollera att den virtuella datorn kan ta emot hälsoavsökningsbegäranden från Azure Load Balancer.

Om en NSG blockerar hälsoavsökningsbegäranden från standardtaggen AZURE_LOADBALANCER den virtuella datorns hälsoavsökning misslyckas och den virtuella datorn markeras som otillgänglig. Lastbalanseraren slutar skicka nya flöden till den virtuella datorn.

## <a name="scenarios-with-outbound-rules"></a>Scenarier med regler för utgående trafik
        

### <a name="outbound-rules-scenarios"></a>Scenarier för regler för utgående trafik


* Konfigurera utgående anslutningar till en specifik uppsättning offentliga IP-adresser eller prefix.
* Ändra [SNAT-portallokering.](load-balancer-outbound-connections.md)
* Aktivera endast utgående.
* Utgående NAT endast för virtuella datorer (ingen inkommande).
* Utgående NAT för intern standardlastbalanserare.
* Aktivera båda TCP& UDP-protokollen för utgående NAT med en offentlig standardlastbalanserare.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Scenario 1: Konfigurera utgående anslutningar till en specifik uppsättning offentliga IP-adresser eller prefix


#### <a name="details"></a>Information


Använd det här scenariot för att skräddarsy utgående anslutningar som kommer från en uppsättning offentliga IP-adresser. Lägg till offentliga IP-adresser eller prefix i en lista för att tillåta eller neka baserat på ursprung.


Den här offentliga IP-adressen eller prefixet kan vara samma som den som används av en belastningsutjämningsregel. 


Så här använder du en annan offentlig IP-adress eller ett annat prefix än vad som används av en belastningsutjämningsregel: 


1. Skapa ett offentligt IP-prefix eller en offentlig IP-adress.
2. Skapa en offentlig standardlastbalanserare 
3. Skapa en frontend som refererar till det offentliga IP-prefixet eller den offentliga IP-adress som du vill använda. 
4. Återanvända en backend-pool eller skapa en backend-pool och placera de virtuella datorerna i en backend-pool för den offentliga lastbalanseraren
5. Konfigurera en regel för utgående trafik på den offentliga lastbalanseraren för att aktivera utgående NAT för de virtuella datorerna med hjälp av frontend. Vi rekommenderar inte att du använder en belastningsutjämningsregel för utgående, inaktivera utgående SNAT på belastningsutjämningsregeln.


### <a name="scenario-2-modify-snat-port-allocation"></a><a name="scenario2out"></a>Scenario 2: Ändra [SNAT-portallokering](load-balancer-outbound-connections.md)


#### <a name="details"></a>Information


Du kan använda regler för utgående trafik för att justera [den automatiska SNAT-portallokeringen baserat på storleken på backend-poolen.](load-balancer-outbound-connections.md#preallocatedports) 


Om SNAT är slut ökar du antalet [SNAT-portar](load-balancer-outbound-connections.md) från standardvärdet 1024. 


Varje offentlig IP-adress bidrar med upp till 64 000 tillfälliga portar. Antalet virtuella datorer i backend-poolen avgör antalet portar som distribueras till varje virtuell dator. En virtuell dator i backend-poolen har åtkomst till högst 64 000 portar. För två virtuella datorer kan högst 32 000 [SNAT-portar](load-balancer-outbound-connections.md) anges med en utgående regel (2x 32 000 = 64 000). 


Du kan använda regler för utgående trafik för att justera de SNAT-portar som anges som standard. Du anger mer eller mindre än standardportallokeringen för [SNAT.](load-balancer-outbound-connections.md) Varje offentlig IP-adress från en frontend av en utgående regel bidrar med upp till 64 000 tillfälliga portar för användning som [SNAT-portar.](load-balancer-outbound-connections.md) 


Lastbalanserare ger [SNAT-portar](load-balancer-outbound-connections.md) i multiplar av 8. Om du anger ett värde som inte är delbart med 8 avvisas konfigurationsåtgärden. Varje belastningsutjämningsregel och inkommande NAT-regel förbrukar ett intervall på 8 portar. Om en belastningsutjämning eller inkommande NAT-regel delar samma intervall på 8 som en annan, kommer inga ytterligare portar att användas.


Om du försöker ge fler [SNAT-portar](load-balancer-outbound-connections.md) än vad som är tillgängligt baserat på antalet offentliga IP-adresser avvisas konfigurationsåtgärden. Om du till exempel ger 10 000 portar per virtuell dator och sju virtuella datorer i en serverpool delar en enda offentlig IP-adress, avvisas konfigurationen. Sju multiplicerat med 10 000 överskrider portgränsen på 64 000. Aktivera scenariot genom att lägga till fler offentliga IP-adresser i frontend för den utgående regeln. 


Återgå till [standardportallokeringen](load-balancer-outbound-connections.md#preallocatedports) genom att ange 0 för antalet portar. De första 50 VM-instanserna får 1 024 portar, 51–100 VIRTUELLA datorinstanser får upp till maximalt 512 instanser. Mer information om standardportallokering för SNAT finns i [allokeringstabellen för SNAT-portar.](./load-balancer-outbound-connections.md#preallocatedports)


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Scenario 3: Aktivera endast utgående


#### <a name="details"></a>Information


Använd en offentlig standardlastbalanserare för att tillhandahålla utgående NAT för en grupp med virtuella datorer. I det här scenariot använder du en regel för utgående trafik på egen hand, utan att konfigurera några ytterligare regler.


> [!NOTE]
> **Azure Virtual Network NAT** kan ge utgående anslutning för virtuella datorer utan behov av en lastbalanserare. Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Scenario 4: Utgående NAT endast för virtuella datorer (ingen inkommande)


> [!NOTE]
> **Azure Virtual Network NAT** kan ge utgående anslutning för virtuella datorer utan behov av en lastbalanserare. Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

#### <a name="details"></a>Information


I det här scenariot Azure Load Balancer utgående regler och Virtual Network NAT alternativ tillgängliga för utgående trafik från ett virtuellt nätverk.


1. Skapa en offentlig IP-adress eller ett prefix.
2. Skapa en offentlig standardlastbalanserare. 
3. Skapa en frontend som är associerad med den offentliga IP-adressen eller prefixet dedikerad för utgående trafik.
4. Skapa en backend-pool för de virtuella datorerna.
5. Placera de virtuella datorerna i backend-poolen.
6. Konfigurera en regel för utgående trafik för att aktivera utgående NAT.



Använd ett prefix eller en offentlig IP-adress för att [skala SNAT-portar.](load-balancer-outbound-connections.md) Lägg till källan för utgående anslutningar i en lista över tillåtna eller nekade.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Scenario 5: Utgående NAT för intern standardlastbalanserare


> [!NOTE]
> **Azure Virtual Network NAT** kan ge utgående anslutningar för virtuella datorer som använder en intern standardlastbalanserare. Se [Vad Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

#### <a name="details"></a>Information


Utgående anslutningar är inte tillgängliga för en intern standardlastbalanserare förrän den uttryckligen har deklarerats via offentliga IP-adresser eller Virtual Network NAT på instansnivå, eller genom att serverpoolmedlemmarna associeras med en konfiguration för endast utgående lastbalanserare. 


Mer information finns i Konfiguration [av lastbalanserare med endast utgående trafik.](./egress-only.md)




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Scenario 6: Aktivera båda TCP& UDP-protokollen för utgående NAT med en offentlig standardlastbalanserare


#### <a name="details"></a>Information


När du använder en offentlig standardlastbalanserare matchar den automatiskt utgående NAT som tillhandahålls transportprotokollet för lastbalanseringsregeln. 


1. Inaktivera utgående [SNAT på](load-balancer-outbound-connections.md) belastningsutjämningsregeln. 
2. Konfigurera en regel för utgående trafik på samma lastbalanserare.
3. Återanvänd den backend-pool som redan används av dina virtuella datorer. 
4. Ange "protokoll": "Alla" som en del av regeln för utgående trafik. 


När endast inkommande NAT-regler används tillhandahålls ingen utgående NAT. 


1. Placera virtuella datorer i en backend-pool.
2. Definiera en eller flera IP-konfigurationer på serversidan med offentliga IP-adresser eller offentliga IP-prefix 
3. Konfigurera en regel för utgående trafik på samma lastbalanserare. 
4. Ange "protokoll": "Alla" som en del av regeln för utgående trafik


## <a name="limitations"></a>Begränsningar

- Det maximala antalet användbara tillfälliga portar per IP-adress för frontend är 64 000.
- Intervallet för den konfigurerbara tidsgränsen för utgående inaktivitet är 4 till 120 minuter (240 till 7 200 sekunder).
- Lastbalanserare stöder inte ICMP för utgående NAT.
- Regler för utgående trafik kan bara tillämpas på den primära IP-konfigurationen för ett nätverkskort.  Du kan inte skapa en regel för utgående trafik för den sekundära IP-adressen för en virtuell dator eller NVA. Flera nätverkskort stöds.
- Alla virtuella datorer i en **tillgänglighetsuppsättning** måste läggas till i serverpoolen för utgående anslutning. 
- Alla virtuella datorer i en **VM-skalningsuppsättning** måste läggas till i serverpoolen för utgående anslutning.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Standard Load Balancer](load-balancer-overview.md)
- Se våra [vanliga frågor och svar om Azure Load Balancer](load-balancer-faqs.md)
