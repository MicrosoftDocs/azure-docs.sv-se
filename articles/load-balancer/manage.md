---
title: Portalinställningar för Azure Load Balancer
description: Kom igång med att lära dig om Azure Load Balancer Portal inställningar
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/8/2020
ms.author: allensu
ms.openlocfilehash: e1080aea12e70f4312fbee07b063d5a5cfbd1201
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89596314"
---
# <a name="azure-load-balancer-portal-settings"></a>Portalinställningar för Azure Load Balancer

När du skapar Azure Load Balancer hjälper informationen i den här artikeln dig att lära dig mer om de enskilda inställningarna och hur rätt konfiguration är för dig.

## <a name="create-load-balancer"></a>Skapa en lastbalanserare

Azure Load Balancer är en utjämning av nätverks belastning som distribuerar trafik mellan VM-instanser i backend-poolen.

### <a name="basics"></a>Grundläggande inställningar

På fliken **grundläggande** på Portal sidan Skapa belastnings utjämning visas följande information:



| Inställningen |  Information |
| ---------- | ---------- |
| Prenumeration  | Välj din prenumeration. Det här valet är den prenumeration som du vill att belastningsutjämnaren ska distribueras i. |
| Resursgrupp | Välj **Skapa nytt** och skriv namnet på resurs gruppen i text rutan. Om du har skapat en befintlig resurs grupp väljer du den. |
| Name | Den här inställningen är namnet på din Azure Load Balancer. |
| Region | Välj en Azure-region som du vill distribuera belastningsutjämnaren i. |
| Typ | Belastnings utjämning har två typer: </br> **Intern (privat)** </br> **Offentlig (extern)**.</br> En intern belastningsutjämnare (ILB) dirigerar trafik till medlemmar i backend-poolen via en privat IP-adress.</br> En offentlig belastningsutjämnare dirigerar begär Anden från klienter via Internet till backend-poolen.</br> Läs mer om [belastnings Utjämnings typer](components.md#frontend-ip-configuration-).|
| SKU  | Välj **standard**. </br> Load Balancer har två SKU: er: **Basic** och **standard**. </br> Basic har begränsad funktionalitet. </br> **Standard** rekommenderas för produktions arbets belastningar. </br> Läs mer om [SKU: er](skus.md). |

Om du väljer **offentlig** som typ ser du följande information:

| Inställningen |  Information |
| ---------- | ---------- |
| Offentlig IP-adress | Välj **Skapa ny** för att skapa en offentlig IP-adress för din offentliga belastningsutjämnare. </br> Om du har en befintlig offentlig IP-adress väljer du **Använd befintlig**.  |
| Namn på offentlig IP-adress | Namnet på den offentliga IP-adressen.|
| SKU för offentlig IP-adress | Offentliga IP-adresser har två SKU: er: **Basic** och **standard**. </br> Basic stöder inte zon återhämtnings-och zonindelade-attribut. </br> **Standard** rekommenderas för produktions arbets belastningar. </br> Belastnings utjämning och offentliga IP-adresser **måste vara identiska**. |
| Tilldelning | **Statisk** väljs automatiskt för standard. </br> Grundläggande offentliga IP-adresser har två typer: **dynamiska** och **statiska**. </br> Dynamiska offentliga IP-adresser tilldelas inte förrän de skapas. </br> IP-adresser kan gå förlorade om resursen tas bort. </br> Statiska IP-adresser rekommenderas. |
| Tillgänglighetszon | Välj **zon-redundant** för att skapa en elastisk belastningsutjämnare. </br> Om du vill skapa en zonindelade-belastningsutjämnare väljer du en speciell zon från **1**, **2** eller **3**. </br> Standard Load Balancer och offentliga IP-adresser för support zoner. </br> Läs mer om [belastningsutjämnare och tillgänglighets zoner](load-balancer-standard-availability-zones.md). </br> Du kan inte se val av zon för Basic. En Basic Load Balancer stöder inte zoner. |
| Lägg till en offentlig IPv6-adress | Load Balancer stöder **IPv6-** adresser för klient delen. </br> Läs mer om [Load Balancer och IPv6](load-balancer-ipv6-overview.md)
| Routningsprioritet | Välj **Microsoft-nätverk**. </br> Microsoft-nätverk innebär att trafiken dirigeras via Microsofts globala nätverk. </br> Internet innebär att trafiken dirigeras via Internet leverantörens nätverk. </br> Läs mer om [Inställningar för routning](../virtual-network/routing-preference-overview.md)|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="Skapa offentlig belastningsutjämnare." border="true":::

Om du väljer **intern** i typ visas följande information:

| Inställningen |  Information |
| ---------- | ---------- |
| Virtuellt nätverk | Det virtuella nätverk som du vill att den interna belastningsutjämnaren ska ingå i. </br> Den privata IP-adressen för klient delen som du väljer för den interna belastningsutjämnaren kommer från det här virtuella nätverket. |
| Tilldelning av IP-adress | Alternativen är **statiska** eller **dynamiska**. </br> Statisk säkerställer att IP-adressen inte ändras. En dynamisk IP-adress kan ändras. |
| Tillgänglighetszon | Alternativen är: </br> **Zonen är redundant** </br> **Zon 1** </br> **Zon 2** </br> **Zon 3** </br> Om du vill skapa en belastningsutjämnare som är hög tillgänglig och som är elastiska till tillgänglighets zon felen väljer du en **zon-redundant** IP. |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="Skapa belastningsutjämnare internt." border="true":::

## <a name="frontend-ip-configuration"></a>IP-konfiguration för klient del

IP-adressen för din Azure Load Balancer. Det är kontakt punkten för-klienter. 

Du kan ha en eller många IP-konfigurationer för klient delen. Om du gick igenom grunderna ovan skulle du redan ha skapat en klient del för belastningsutjämnaren. 

Om du vill lägga till en IP-konfiguration för klient delen i belastningsutjämnaren går du till belastningsutjämnaren i Azure Portal, väljer **IP-konfiguration för klient** del och väljer sedan **+ Lägg till**.

| Inställningen |  Information |
| ---------- | ---------- |
| Name | Namnet på klient delens IP-konfiguration. |
| IP-version | Den IP-protokollversion som du vill att din klient del ska ha. </br> Belastnings utjämning stöder både IPv4-och IPv6-klient dels-IP-konfigurationer. |
| IP-typ | IP-typ avgör om en enskild IP-adress är kopplad till din klient del eller ett intervall med IP-adresser med hjälp av ett IP-prefix. </br> Ett [offentligt IP-prefix](../virtual-network/public-ip-address-prefix.md) underlättar när du behöver ansluta till samma slut punkt upprepade gånger. Prefixet ser till att det finns tillräckligt många portar för att hjälpa till med SNAT-port problem. |
| Offentlig IP-adress (eller prefix om du valde prefix ovan) | Välj eller skapa en ny offentlig IP-adress (eller prefix) för klient delen för belastningsutjämnaren. |

:::image type="content" source="./media/manage/frontend.png" alt-text="Sidan skapa IP-konfiguration för klient delen." border="true":::

## <a name="backend-pools"></a>Backend-pooler

En backend-adresspool innehåller IP-adresserna för de virtuella nätverks gränssnitten i backend-poolen. 

Om du vill lägga till en backend-pool i belastningsutjämnaren går du till belastningsutjämnaren i Azure Portal, väljer **backend-pooler** och väljer sedan **+ Lägg till**.

| Inställningen | Information |
| ---------- |  ---------- |
| Name | Namnet på din backend-pool. |
| Virtuellt nätverk | Det virtuella nätverket för Server dels instanserna. |
| IP-version | Alternativen är **IPv4** eller **IPv6**. |

Du kan lägga till virtuella datorer eller skalnings uppsättningar för virtuella datorer i backend-poolen för Azure Load Balancer. Skapa virtuella datorer eller skalnings uppsättningar för virtuella datorer först. Lägg sedan till dem i belastningsutjämnaren i portalen.

:::image type="content" source="./media/manage/backend.png" alt-text="Sidan skapa backend-pool." border="true":::

## <a name="health-probes"></a>Hälsotillståndsavsökningar

En hälso avsökning används för att övervaka status för dina Server dels virtuella datorer eller instanser. Statusen för hälso avsökningen avgör när nya anslutningar skickas till en instans baserat på hälso kontroller. 

Om du vill lägga till en hälso avsökning i belastningsutjämnaren går du till belastningsutjämnaren i Azure Portal, väljer **hälso avsökningar** och väljer sedan **+ Lägg till**.

| Inställningen | Information |
| ---------- | ---------- |
| Name | Namnet på hälso avsökningen. |
| Protokoll | Protokollet du väljer avgör vilken typ av kontroll som används för att fastställa om Server dels instanserna är felfria. </br> Alternativen är: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Se till att du använder rätt protokoll. Det här valet beror på programmets typ. </br> Konfigurationen av hälso avsökningen och avsöknings Svaren avgör vilka instanser av backend-poolen som tar emot nya flöden. </br> Du kan använda hälso avsökningar för att identifiera ett programs fel på en backend-slutpunkt. </br> Läs mer om [hälso avsökningar](load-balancer-custom-probe-overview.md). |
| Port | Mål porten för hälso avsökningen. </br> Den här inställningen är porten på backend-instansen som hälso avsökningen ska använda för att fastställa instansens hälsa. |
| Intervall | Antalet sekunder i mellan avsöknings försök. </br> Intervallet avgör hur ofta hälso avsökningen kommer att försöka komma åt backend-instansen. </br> Om du väljer 5 skapas det andra avsöknings försöket efter 5 sekunder och så vidare. |
| Tröskelvärde för ej felfri | Antalet avsöknings fel i följd som måste inträffa innan en virtuell dator betraktas som ej felfri.</br> Om du väljer 2 kommer inga nya flöden att ställas in på den här server dels instansen efter två på varandra följande försök. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Lägg till hälso avsökning." border="true":::

## <a name="load-balancing-rules"></a>Belastnings Utjämnings regler

Definierar hur inkommande trafik distribueras till alla instanser i backend-poolen. En belastnings Utjämnings regel mappar en bestämd IP-konfiguration och port för klient delen till flera Server dels IP-adresser och portar.

Om du vill lägga till en regel för belastnings utjämning i belastningsutjämnaren går du till belastningsutjämnaren i Azure Portal, väljer **regler för belastnings utjämning** och väljer sedan **+ Lägg till**.
    
| Inställningen | Information |
| ---------- | ---------- |
| Name | Namnet på belastnings Utjämnings regeln. |
| IP-version | Alternativen är **IPv4** eller **IPv6**.  |
| IP-adress för klient del | Välj IP-adress för klient delen. </br> Klient delens IP-adress för belastningsutjämnaren som du vill att belastnings Utjämnings regeln ska kopplas till.|
| Protokoll | Azure Load Balancer är en Layer 4-belastnings utjämning för nätverk. </br> Alternativen är: **TCP** eller **UDP**. |
| Port | Den här inställningen är den port som är kopplad till klient delens IP-adress som du vill att trafiken ska distribueras baserat på den här belastnings Utjämnings regeln. |
| Serverdelsport | Den här inställningen är porten på instanserna i backend-poolen som du vill att belastningsutjämnaren ska skicka trafik till. Den här inställningen kan vara samma som klient dels porten eller en annan om du behöver flexibiliteten för ditt program. |
| Serverdelspool | Den backend-pool som den här belastnings Utjämnings regeln ska tillämpas på. |
| Hälsoavsökning | Hälso avsökningen som du skapade för att kontrol lera instansernas status i backend-poolen. </br> Endast felfria instanser får ny trafik. |
| Sessionspermanens |  Alternativen är: </br> **Ingen** </br> **Klient-IP** </br> **Klient-IP och protokoll**</br> </br> Upprätthålla trafik från en klient till samma virtuella dator i backend-poolen. Trafiken kommer att behållas under sessionens varaktighet. </br> **Ingen** anger att efterföljande begär Anden från samma klient kan hanteras av alla virtuella datorer. </br> **Klientens IP-** adress anger att efterföljande begär Anden från samma klient-IP-adress ska hanteras av samma virtuella dator. </br> **Klientens IP och protokoll** säkerställer att efterföljande förfrågningar från samma klient-IP-adress och protokoll hanteras av samma virtuella dator. </br> Läs mer om [distributions lägen](load-balancer-distribution-mode.md). |
| Tids gräns för inaktivitet (minuter) | Håll en **TCP** -eller **http-** anslutning öppen utan att lita på att klienter skickar Keep-Alive-meddelanden |  
| TCP-återställning | Load Balancer kan skicka **TCP** -återställningar för att skapa ett mer förutsägbart program beteende när anslutningen är inaktiv. </br> Läs mer om [TCP-återställning](load-balancer-tcp-reset.md)|
| Flytande IP | Flytande IP är Azures terminologi för en del av vad som kallas för **direkt Server retur (DSR)**. </br> DSR består av två delar: <br> 1. Flow-topologi </br> 2. ett mappnings schema för IP-adresser på en plattforms nivå. </br></br> Azure Load Balancer fungerar alltid i en topologi för DSR-flöde om flytande IP är aktiverat eller inte. </br> Den här åtgärden innebär att den utgående delen av ett flöde alltid skrivs om korrekt för att flöda direkt tillbaka till ursprunget. </br> Utan flytande IP exponerar Azure ett traditionellt IP-mappnings schema för belastnings utjämning, de virtuella dator instanserna. </br> Att aktivera flytande IP ändrar IP-adress mappningen till belastningsutjämnaren för klient delen för att möjliggöra ytterligare flexibilitet. </br> Mer information finns i [flera klient delar för Azure Load Balancer](load-balancer-multivip-overview.md).|
| Skapa implicit utgående regler | Välj **Nej**. </br> Standard: **disableOutboundSnat = false**  </br> I detta fall sker utgående via samma klient dels-IP. </br></br> **disableOutboundSnat = True** </br>I det här fallet behövs utgående regler för utgående trafik. |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Lägg till belastnings Utjämnings regel." border="true":::

## <a name="inbound-nat-rules"></a>Ingående NAT-regler

En inkommande NAT-regel vidarebefordrar inkommande trafik som skickas till klient delens IP-adress och port kombination. 

Trafiken skickas till en speciell virtuell dator eller instans i backend-poolen. Vidarebefordran av portar utförs av samma hash-baserade distribution som belastnings utjämning.

Om scenariot kräver Remote Desktop Protocol (RDP) eller Secure Shell-sessioner (SSH) för att separera VM-instanser i en backend-pool. Flera interna slut punkter kan mappas till portar på samma IP-adress för klient delen. 

Klient delens IP-adresser kan användas för att fjärradministrera dina virtuella datorer utan någon ytterligare hopp ruta.

Om du vill lägga till en inkommande NAT-regel i belastningsutjämnaren går du till belastningsutjämnaren i Azure Portal, väljer **inkommande NAT-regler** och väljer sedan **+ Lägg till**.

| Inställningen | Information |
| ---------- | ---------- |
| Name | Namnet på din inkommande NAT-regel |
| IP-adress för klient del | Välj IP-adress för klient delen. </br> Klient delens IP-adress för den belastningsutjämnare som du vill använda för inkommande NAT-regel. |
| IP-version | Alternativen är IPv4 och IPv6. |
| Tjänst | Den typ av tjänst som du kommer att köra på Azure Load Balancer. </br> Ett val här uppdaterar portinformation på rätt sätt. |
| Protokoll | Azure Load Balancer är en Layer 4-belastnings utjämning för nätverk. </br> Alternativen är: TCP eller UDP. |
| Tids gräns för inaktivitet (minuter) | Håll en TCP-eller HTTP-anslutning öppen utan att lita på att klienter skickar Keep-Alive-meddelanden. |
| TCP-återställning | Load Balancer kan skicka TCP-återställningar för att hjälpa dig att skapa ett mer förutsägbart program beteende när anslutningen är inaktiv. </br> Läs mer om [TCP-återställning](load-balancer-tcp-reset.md) |
| Port | Den här inställningen är den port som är kopplad till klient delens IP-adress som du vill att trafiken ska distribueras baserat på den här inkommande NAT-regeln. |
| Virtuell mål dator | Den virtuella dator delen av den backend-pool som du vill att regeln ska kopplas till. |
| Port mappning | Den här inställningen kan vara standard eller anpassad baserat på dina program inställningar. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="Lägg till inkommande NAT-regel." border="true":::

## <a name="outbound-rules"></a>Regler för utgående trafik

Utgående regler för belastningsutjämnare konfigurerar utgående SNAT för virtuella datorer i backend-poolen.

Om du vill lägga till en utgående regel i belastningsutjämnaren går du till belastningsutjämnaren i Azure Portal, väljer **utgående regler** och väljer sedan **+ Lägg till**.

| Inställningen | Information |
| ------- | ------ |
| Name | Namnet på din utgående regel. |
| IP-adress för klient del | Välj IP-adress för klient delen. </br> Klient delens IP-adress för den belastningsutjämnare som du vill att regeln för utgående trafik ska kopplas till. |
| Protokoll | Azure Load Balancer är en Layer 4-belastnings utjämning för nätverk. </br> Alternativen är: **all**, **TCP** eller **UDP**. |
| Tids gräns för inaktivitet (minuter) | Håll en **TCP** -eller **http-** anslutning öppen utan att lita på att klienter skickar Keep-Alive-meddelanden. |
| TCP-återställning | Load Balancer kan skicka **TCP** -återställningar för att skapa ett mer förutsägbart program beteende när anslutningen är inaktiv. </br> Läs mer om [TCP-återställning](load-balancer-tcp-reset.md) |
| Serverdelspool | Den backend-pool som den utgående regeln ska tillämpas på. |

### <a name="port-allocation"></a>Port tilldelning

| Inställningen | Information |
| ------- | ------ |
| Port tilldelning | Vi rekommenderar att du väljer **manuellt Välj antal utgående portar**.|

### <a name="outbound-ports"></a>Utgående portar

| Inställningen | Information |
| ------- | ------ |
| Välj efter | Välj **portar per instans** |
| Portar per instans | Ange **10 000**. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Lägg till inkommande utgående regel." border="true":::

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de olika villkoren och inställningarna i Azure Portal för Azure Load Balancer.

* [Läs](./load-balancer-overview.md) mer om Azure Load Balancer.
* [Vanliga frågor och svar](./load-balancer-faqs.md) om Azure Load Balancer.