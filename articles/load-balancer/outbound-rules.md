---
title: Utgående regler Azure Load Balancer
description: Den här artikeln förklarar hur du konfigurerar utgående regler för att styra utgående av Internet trafik med Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 6b73eb51831238f23400ef60d0a6162bca38ea85
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033161"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Utgående regler Azure Load Balancer

Med utgående regler kan du uttryckligen definiera SNAT (käll Network Address Translation) för en offentlig standard belastnings utjämning. Med den här konfigurationen kan du använda en eller flera offentliga IP-adresser för din belastningsutjämnare för att tillhandahålla utgående Internet-anslutning för Server dels instanserna.

Den här konfigurationen aktiverar:

* IP-maskerad
* Förenkla dina listor över tillåtna.
* Minskar antalet offentliga IP-resurser för distribution.

Med utgående regler har du fullständig deklarativ kontroll över utgående Internet anslutning. Med utgående regler kan du skala och finjustera den här funktionen för dina behov. 

Utgående regler kommer bara att följas om den virtuella datorns VM inte har en offentlig IP-adress på instans nivå (ILPIP).

![Load Balancer utgående regler](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Med utgående regler kan du uttryckligen definiera beteendet för utgående **SNAT** .

Med utgående regler kan du styra:

* **Vilka virtuella datorer som översätts till offentliga IP-adresser.**
     * Två regler var backend-pool A använder IP-adress A och B, backend-pool B använder IP-adress C och D.
* **Hur utgående SNAT-portar allokeras.**
     * Backend-pool B är den enda poolen som gör utgående anslutningar, ge alla SNAT-portar till backend-pool B och ingen till backend-pool A.
* **Vilka protokoll som ska tillhandahålla utgående översättning för.**
     * Backend-pool B behöver UDP-portar för utgående. Backend-poolen kräver TCP. Ge TCP-portar till A-och UDP-portar till B.
* **Vilken varaktighet som ska användas för utgående timeout för utgående anslutning (4-120 minuter).**
     * Om det finns tids krävande anslutningar med keepalive-anslutningar ska du reservera inaktiva portar för tids krävande anslutningar i upp till 120 minuter. Anta att inaktuella anslutningar är övergivna och frigör portar på 4 minuter för nya anslutningar 
* **Om du vill skicka en TCP-återställning vid inaktivitet.**
     * När tids gränsen för inaktiva anslutningar är slut, skickar vi en TCP-till-klient och-server så att de vet att flödet överges?

## <a name="outbound-rule-definition"></a>Regel definition för utgående trafik

Utgående regler följer samma välbekanta syntax som belastnings utjämning och inkommande NAT- **regler:**  +  Server dels **parametrar** i  +  **backend-poolen**. 

En utgående regel konfigurerar utgående NAT för _alla virtuella datorer som identifieras av backend-poolen_ som ska översättas till _klient delen_.  

_Parametrarna_ ger ytterligare detaljerad kontroll över den utgående NAT-algoritmen.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Skala utgående NAT med flera IP-adresser

Varje ytterligare IP-adress som tillhandahålls av en klient del ger ytterligare 64 000 tillfälliga portar för belastningsutjämnare som ska användas som SNAT-portar. 

Använd flera IP-adresser för att planera för storskaliga scenarier. Använd utgående regler för att minska [SNAT-belastningen](troubleshoot-outbound-connection.md#snatexhaust). 

Du kan också använda ett [offentligt IP-prefix](./load-balancer-outbound-connections.md#outboundrules) direkt med en utgående regel. 

Ett offentligt IP-prefix ökar skalningen av distributionen. Prefixet kan läggas till i listan över tillåtna flöden för flöden som kommer från dina Azure-resurser. Du kan konfigurera en IP-konfiguration för klient delen i belastningsutjämnaren för att referera till ett offentligt IP-adressprefix.  

Belastningsutjämnaren har kontroll över det offentliga IP-prefixet. Regeln för utgående trafik använder automatiskt alla offentliga IP-adresser som finns i det offentliga IP-prefixet för utgående anslutningar. 

Var och en av IP-adresserna inom det offentliga IP-prefixet ger ytterligare 64 000 tillfälliga portar per IP-adress för belastningsutjämnare som ska användas som SNAT-portar.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Utgående tids gräns för flödes inaktivitet och TCP-återställning

Utgående regler tillhandahåller en konfigurations parameter för att kontrol lera utgående tids gräns för utgående trafik och matcha den mot ditt programs behov. Timeout för utgående inaktivitet är som standard 4 minuter. Mer information finns i [Konfigurera tids gränser för inaktivitet](load-balancer-tcp-idle-timeout.md). 

Standard beteendet för belastningsutjämnare är att släppa flödet tyst när tids gränsen för utgående inaktivitet har nåtts. `enableTCPReset`Parametern möjliggör ett förutsägbart program beteende och kontroll. Parametern avgör om dubbelriktad TCP-återställning (TCP per-) ska skickas vid tids gränsen för timeout vid utgående inaktivitet. 

Granska [TCP-återställning vid inaktivitet](./load-balancer-tcp-reset.md) för mer information, inklusive region tillgänglighet.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Skydda och kontrol lera utgående anslutningar explicit

Regler för belastnings utjämning ger automatisk programmering av utgående NAT. Vissa scenarier eller kräver att du inaktiverar automatisk programmering av utgående NAT med belastnings Utjämnings regeln. Genom att inaktivera via regeln kan du styra eller förfina beteendet.  

Du kan använda den här parametern på två sätt:

1. Förebyggande av inkommande IP-adress för utgående SNAT. Inaktivera utgående SNAT i belastnings Utjämnings regeln.
  
2. Justera de utgående **SNAT** -parametrarna för en IP-adress som används för inkommande och utgående samtidigt. Den automatiska utgående NAT-filen måste inaktive ras för att tillåta en utgående regel att ta kontroll. Om du vill ändra SNAT-port tilldelningen för en adress som också används för inkommande `disableOutboundSnat` måste parametern vara inställd på True. 

Det går inte att konfigurera en utgående regel om du försöker omdefiniera en IP-adress som används för inkommande.  Inaktivera utgående NAT för belastnings Utjämnings regeln först.

>[!IMPORTANT]
> Den virtuella datorn har inte någon utgående anslutning om du anger den här parametern till true och inte har någon utgående regel för att definiera utgående anslutning.  Vissa åtgärder för din virtuella dator eller ditt program kan bero på att det finns en utgående anslutning tillgänglig. Se till att du förstår beroendena för ditt scenario och att du anser att du har gjort den här ändringen.

Ibland är det inte oönskat för en virtuell dator att skapa ett utgående flöde. Det kan finnas ett krav för att hantera vilka destinationer som tar emot utgående flöden eller vilka destinationer som startar inkommande flöden. Använd [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md) för att hantera de destinationer som den virtuella datorn når. Använd NSG: er för att hantera vilka offentliga destinationer som startar inkommande flöden.

När du tillämpar en NSG på en belastningsutjämnad virtuell dator, bör du tänka på [tjänst taggarna](../virtual-network/network-security-groups-overview.md#service-tags) och [Standard säkerhets reglerna](../virtual-network/network-security-groups-overview.md#default-security-rules). 

Se till att den virtuella datorn kan ta emot hälso avsöknings begär Anden från Azure Load Balancer.

Om en NSG blockerar hälso avsöknings begär Anden från AZURE_LOADBALANCER-Standardtaggen Miss lyckas din VM-avsökning och den virtuella datorn är markerad som ej tillgänglig. Belastningsutjämnaren slutar skicka nya flöden till den virtuella datorn.

## <a name="scenarios-with-outbound-rules"></a>Scenarier med utgående regler
        

### <a name="outbound-rules-scenarios"></a>Scenarier för utgående regler


* Konfigurera utgående anslutningar till en speciell uppsättning offentliga IP-adresser eller prefix.
* Ändra [SNAT](load-balancer-outbound-connections.md) -port tilldelning.
* Aktivera endast utgående.
* Utgående NAT enbart för virtuella datorer (ingen inkommande).
* Utgående NAT för intern standard belastningsutjämnare.
* Aktivera både TCP & UDP-protokoll för utgående NAT med en offentlig standard belastnings utjämning.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Scenario 1: Konfigurera utgående anslutningar till en angiven uppsättning offentliga IP-adresser eller prefix


#### <a name="details"></a>Information


Använd det här scenariot för att skräddarsy utgående anslutningar till härstammar från en uppsättning offentliga IP-adresser. Lägg till offentliga IP-adresser eller prefix till en lista över tillåtna eller nekade baserade på ursprung.


Den här offentliga IP-adressen eller prefixet kan vara samma som används av en belastnings Utjämnings regel. 


Använda en annan offentlig IP-adress eller prefix än vad som används av en belastnings Utjämnings regel: 


1. Skapa ett offentligt IP-prefix eller en offentlig IP-adress.
2. Skapa en offentlig standard Load Balancer 
3. Skapa en klient del som refererar till det offentliga IP-prefixet eller den offentliga IP-adress som du vill använda. 
4. Återanvänd en backend-pool eller skapa en backend-pool och placera de virtuella datorerna i en backend-pool för den offentliga belastningsutjämnaren
5. Konfigurera en utgående regel på den offentliga belastningsutjämnaren för att aktivera utgående NAT för de virtuella datorerna med hjälp av klient delen. Du bör inte använda en regel för belastnings utjämning för utgående, inaktivera utgående SNAT i belastnings Utjämnings regeln.


### <a name="scenario-2-modify-snatport-allocation"></a><a name="scenario2out"></a>Scenario 2: ändra [SNAT](load-balancer-outbound-connections.md)-port tilldelning


#### <a name="details"></a>Information


Du kan använda utgående regler för att finjustera den [automatiska SNAT-port tilldelningen baserat på storleken på backend-poolen](load-balancer-outbound-connections.md#preallocatedports). 


Om du upplever SNAT-belastning ökar du antalet [SNAT](load-balancer-outbound-connections.md)-portar som tilldelas från standardvärdet 1024. 


Varje offentlig IP-adress bidrar med upp till 64 000 tillfälliga portar. Antalet virtuella datorer i backend-poolen bestämmer antalet portar som distribueras till varje virtuell dator. En virtuell dator i backend-poolen har åtkomst till maximalt 64 000 portar. För två virtuella datorer kan högst 32 000 [SNAT](load-balancer-outbound-connections.md)-portar ges med en utgående regel (2x 32 000 = 64 000). 


Du kan använda utgående regler för att finjustera SNAT-portarna som har angetts som standard. Du får mer eller mindre än standardvärdet för [SNAT](load-balancer-outbound-connections.md)-port tilldelning. Varje offentlig IP-adress från en klient del för en utgående regel bidrar upp till 64 000 tillfälliga portar för användning som [SNAT](load-balancer-outbound-connections.md)-portar. 


Load Balancer ger [SNAT](load-balancer-outbound-connections.md)-portar i multipler av 8. Om du anger ett värde som inte är delbart med 8 avvisas konfigurations åtgärden. Varje belastnings Utjämnings regel och inkommande NAT-regel använder ett intervall på 8 portar. Om en belastnings utjämning eller inkommande NAT-regel delar samma intervall om 8 som en annan används inga ytterligare portar.


Om du försöker ge fler [SNAT](load-balancer-outbound-connections.md)-portar än vad som är tillgängligt baserat på antalet offentliga IP-adresser avvisas konfigurations åtgärden. Om du till exempel ger 10 000 portar per virtuell dator och sju virtuella datorer i en backend-pool delar en offentlig IP-adress, så avvisas konfigurationen. Sju multiplicerat med 10 000 överskrider gränsen på 64 000-porten. Lägg till fler offentliga IP-adresser till klient delen för den utgående regeln för att aktivera scenariot. 


Återgå till [standard port tilldelningen](load-balancer-outbound-connections.md#preallocatedports) genom att ange 0 för antalet portar. De första 50 VM-instanserna får 1024 portar, 51-100 VM-instanser kommer att få 512 upp till maximalt antal instanser. Mer information om standard tilldelning av SNAT-portar finns i [tilldelnings tabellen för SNAT-portar](./load-balancer-outbound-connections.md#preallocatedports).


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Scenario 3: Aktivera endast utgående


#### <a name="details"></a>Information


Använd en offentlig standard belastningsutjämnare för att tillhandahålla utgående NAT för en grupp med virtuella datorer. I det här scenariot använder du en utgående regel i sig själv, utan att några ytterligare regler har kon figurer ATS.


> [!NOTE]
> **Azure Virtual Network NAT** kan tillhandahålla utgående anslutningar för virtuella datorer utan att det krävs någon belastningsutjämnare. Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Scenario 4: utgående NAT enbart för virtuella datorer (ingen inkommande)


> [!NOTE]
> **Azure Virtual Network NAT** kan tillhandahålla utgående anslutningar för virtuella datorer utan att det krävs någon belastningsutjämnare. Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

#### <a name="details"></a>Information


För det här scenariot: Azure Load Balancer utgående regler och Virtual Network NAT är alternativ som är tillgängliga för utgående från ett virtuellt nätverk.


1. Skapa en offentlig IP-adress eller ett prefix.
2. Skapa en offentlig standard Load Balancer. 
3. Skapa en klient del som är kopplad till den offentliga IP-adressen eller prefixet dedikerat för utgående.
4. Skapa en backend-pool för de virtuella datorerna.
5. Placera de virtuella datorerna i backend-poolen.
6. Konfigurera en regel för utgående trafik för att aktivera utgående NAT.



Använd ett prefix eller en offentlig IP-adress för att skala [SNAT](load-balancer-outbound-connections.md)-portar. Lägg till källan för utgående anslutningar till en lista över tillåtna eller nekade.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Scenario 5: utgående NAT för intern standard belastnings utjämning


> [!NOTE]
> **Azure Virtual Network NAT** kan tillhandahålla utgående anslutning för virtuella datorer som använder en intern standard belastnings utjämning. Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

#### <a name="details"></a>Information


Utgående anslutningar är inte tillgängliga för en intern standard belastningsutjämnare förrän den uttryckligen har deklarerats via offentliga IP-adresser på instans nivå eller Virtual Network NAT, eller genom att associera medlemmar i Server delens pool med en konfiguration för utgående belastnings utjämning. 


Mer information finns i [konfiguration av utgående belastnings utjämning](./egress-only.md).




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Scenario 6: Aktivera både TCP & UDP-protokoll för utgående NAT med en offentlig standard belastnings utjämning


#### <a name="details"></a>Information


När du använder en offentlig standard belastningsutjämnare, matchar den automatiskt utgående NAT-överförings protokollet för belastnings Utjämnings regeln. 


1. Inaktivera utgående [SNAT](load-balancer-outbound-connections.md)i belastnings Utjämnings regeln. 
2. Konfigurera en utgående regel på samma belastningsutjämnare.
3. Återanvänd backend-poolen som redan används av dina virtuella datorer. 
4. Ange "protokoll": "alla" som en del av regeln för utgående trafik. 


När endast inkommande NAT-regler används tillhandahålls ingen utgående NAT. 


1. Placera virtuella datorer i en backend-pool.
2. Definiera en eller flera klient dels-IP-konfigurationer med offentliga IP-adresser eller offentliga IP-prefix 
3. Konfigurera en utgående regel på samma belastningsutjämnare. 
4. Ange "protokoll": "alla" som en del av regeln för utgående trafik


## <a name="limitations"></a>Begränsningar

- Det maximala antalet användbara tillfälliga portar per IP-adress för klient delen är 64 000.
- Intervallet för den konfigurerbara tids gränsen för utgående inaktivitet är 4 till 120 minuter (240 till 7200 sekunder).
- Load Balancer stöder inte ICMP för utgående NAT.
- Utgående regler kan bara tillämpas på en primär IP-konfiguration för ett nätverkskort.  Du kan inte skapa en utgående regel för den sekundära IP-adressen för en virtuell dator eller NVA. Det finns stöd för flera nätverkskort.
- Alla virtuella datorer i en **tillgänglighets uppsättning** måste läggas till i backend-poolen för utgående anslutning. 
- Alla virtuella datorer i en **skalnings uppsättning för virtuella** datorer måste läggas till i backend-poolen för utgående anslutning.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure standard Load Balancer](load-balancer-overview.md)
- Se våra [vanliga frågor och svar om Azure Load Balancer](load-balancer-faqs.md)