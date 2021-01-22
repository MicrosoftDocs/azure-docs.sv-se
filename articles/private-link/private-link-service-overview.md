---
title: Vad är Azure Private Link service?
description: Lär dig mer om Azure Private Link service.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 7983a80da8a5ca9d900e44515b5e078cc9d70d79
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684194"
---
# <a name="what-is-azure-private-link-service"></a>Vad är Azure Private Link service?

Tjänsten Azure Private Link är referensen till din egen tjänst som drivs av en privat Azure-länk. Din tjänst som körs bakom [Azure standard Load Balancer](../load-balancer/load-balancer-overview.md) kan aktive ras för åtkomst till privat länk så att konsumenter till tjänsten kan komma åt den privat från sina egna virtuella nätverk. Dina kunder kan skapa en privat slut punkt i sitt VNet och mappa den till den här tjänsten. I den här artikeln beskrivs begrepp som är relaterade till tjänst leverantörs sidan. 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="Arbets flöde för privata länk tjänster" border="true":::

*Bild: Azure Private Link service.*

## <a name="workflow"></a>Arbetsflöde

![Arbets flöde för privata länk tjänster](media/private-link-service-overview/private-link-service-workflow.png)


*Bild: Azure Private Link service-arbetsflöde.*

### <a name="create-your-private-link-service"></a>Skapa en privat länk-tjänst

- Konfigurera ditt program så att det körs bakom en standard belastningsutjämnare i det virtuella nätverket. Om du redan har konfigurerat ditt program bakom en standard belastningsutjämnare kan du hoppa över det här steget.   
- Skapa en privat länk-tjänst som refererar till belastningsutjämnaren ovan. I urvals processen för belastnings utjämning väljer du den IP-konfiguration för klient delen där du vill ta emot trafiken. Välj ett undernät för NAT IP-adresser för den privata länk tjänsten. Vi rekommenderar att du har minst åtta NAT IP-adresser tillgängliga i under nätet. All konsument trafik ser ut att härstamma från den här poolen av privata IP-adresser till tjänst leverantören. Välj lämpliga egenskaper/inställningar för tjänsten för privat länk.    

    > [!NOTE]
    > Azure Private Link service stöds endast på Standard Load Balancer. 
    
### <a name="share-your-service"></a>Dela din tjänst

När du har skapat en privat länk-tjänst genererar Azure en globalt unik namngiven moniker som kallas "alias" baserat på det namn som du anger för din tjänst. Du kan dela antingen alias eller resurs-URI för din tjänst med dina kunder offline. Konsumenter kan starta en privat länk anslutning med hjälp av aliaset eller resurs-URI: n.
 
### <a name="manage-your-connection-requests"></a>Hantera dina anslutnings förfrågningar

När en konsument har initierat en anslutning kan tjänste leverantören acceptera eller avvisa anslutningsbegäran. Alla anslutnings begär Anden visas under egenskapen **privateendpointconnections** i tjänsten Private Link.
 
### <a name="delete-your-service"></a>Ta bort din tjänst

Om den privata länk tjänsten inte längre används kan du ta bort den. Men innan du tar bort tjänsten bör du kontrol lera att det inte finns några privata slut punkts anslutningar kopplade till den. Du kan avvisa alla anslutningar och ta bort tjänsten.

## <a name="properties"></a>Egenskaper

En privat länk-tjänst anger följande egenskaper: 

|Egenskap |Förklaring  |
|---------|---------|
|Etablerings status (provisioningState)  |En skrivskyddad egenskap som visar det aktuella etablerings läget för tjänsten för privat länk. Tillämpliga etablerings tillstånd är: "ta bort; Bröt Brutit Uppdaterar ". När etablerings statusen har slutförts har du slutfört den privata länk tjänsten.        |
|Alias (alias)     | Alias är en globalt unik sträng som är skrivskyddad för din tjänst. Det hjälper dig att maskera kund informationen för din tjänst och samtidigt skapar ett namn som är enkelt att dela för din tjänst. När du skapar en privat länk-tjänst genererar Azure alias för din tjänst som du kan dela med dina kunder. Dina kunder kan använda det här aliaset för att begära en anslutning till din tjänst.          |
|Synlighet (synlighet)     | Visibility är den egenskap som styr exponerings inställningarna för din privata länk tjänst. Tjänste leverantörer kan välja att begränsa exponeringen för deras tjänster till prenumerationer med Azure RBAC-behörigheter (rollbaserad åtkomst kontroll), en begränsad uppsättning prenumerationer eller alla Azure-prenumerationer.          |
|Automatiskt godkännande (automatiskt godkännande)    |   Automatiskt godkännande styr automatisk åtkomst till den privata länk tjänsten. De prenumerationer som anges i listan med automatiskt godkännande godkänns automatiskt när en anslutning begärs från privata slut punkter i dessa prenumerationer.          |
|Load Balancer IP-konfiguration för klient del (loadBalancerFrontendIpConfigurations)    |    Privata länk tjänsten är kopplad till IP-adressen för klient delen för en Standard Load Balancer. All trafik som är avsedd för tjänsten når klient delen för SLB. Du kan konfigurera SLB-regler för att dirigera trafiken till lämpliga Server dels grupper där dina program körs. IP-konfigurationer för belastningsutjämnare i klient delen skiljer sig från NAT IP-konfigurationer.      |
|NAT IP-konfiguration (ipConfigurations)    |    Den här egenskapen refererar till IP-konfigurationen för NAT (Network Address Translation) för den privata länk tjänsten. NAT-IP kan väljas från alla undernät i tjänst leverantörens virtuella nätverk. Tjänsten för privata länkar utför NAT-ing på den privata länk trafiken. Detta säkerställer att det inte finns någon IP-konflikt mellan käll-och mål adress utrymmet (tjänst leverantör). På mål sidan (tjänst leverantörs sidan) visas NAT-IP-adressen som käll-IP för alla paket som tas emot av din tjänst och mål-IP för alla paket som skickas av din tjänst.       |
|Anslutningar för privata slut punkter (privateEndpointConnections)     |  Den här egenskapen listar de privata slut punkter som ansluter till tjänsten för privata länkar. Flera privata slut punkter kan ansluta till samma privata länk tjänst och tjänst leverantören kan styra statusen för enskilda privata slut punkter.        |
|TCP-proxy v2 (EnableProxyProtocol)     |  Med den här egenskapen kan tjänst leverantören använda TCP proxy v2 för att hämta anslutnings information om tjänste konsumenten. Service providern ansvarar för att konfigurera mottagares konfigurationerna för att kunna parsa proxy Protocol v2-huvudet.        |
|||


### <a name="details"></a>Information

- Private Link service kan nås från godkända privata slut punkter i valfri offentlig region. Den privata slut punkten kan nås från samma virtuella nätverk, regionalt peer-virtuella nätverk, globalt peered virtuella nätverk och lokalt med privata VPN-eller ExpressRoute-anslutningar. 
 
- När du skapar en privat länk-tjänst skapas ett nätverks gränssnitt för resursens livs cykel. Det här gränssnittet kan inte hanteras av kunden.
 
- Den privata länk tjänsten måste distribueras i samma region som det virtuella nätverket och Standard Load Balancer.  
 
- En enda privat länk-tjänst kan nås från flera privata slut punkter som tillhör olika virtuella nätverk, prenumerationer och/eller Active Directory klienter. Anslutningen upprättas via ett anslutnings arbets flöde. 
 
- Det går att skapa flera privata länk tjänster på samma Standard Load Balancer att använda olika klient dels-IP-konfigurationer. Det finns gränser för antalet privata länk tjänster som du kan skapa per Standard Load Balancer och per prenumeration. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
 
- Privata länk tjänster kan ha mer än en länkad NAT-IP-konfiguration. Om du väljer fler än en NAT IP-konfiguration kan tjänst leverantörer skalas. Idag kan tjänst leverantörer tilldela upp till åtta NAT IP-adresser per privat länk tjänst. Med varje NAT-IP-adress kan du tilldela fler portar för dina TCP-anslutningar och därmed skala ut. När du har lagt till flera NAT IP-adresser i en privat länk-tjänst kan du inte ta bort NAT-IP-adresserna. Detta görs för att säkerställa att aktiva anslutningar inte påverkas när du tar bort NAT-IP-adresser.


## <a name="alias"></a>Alias

**Alias** är ett globalt unikt namn för din tjänst. Det hjälper dig att maskera kund informationen för din tjänst och samtidigt skapar ett namn som är enkelt att dela för din tjänst. När du skapar en privat länk-tjänst genererar Azure ett alias för tjänsten som du kan dela med dina kunder. Dina kunder kan använda det här aliaset för att begära en anslutning till din tjänst.

Aliaset består av tre delar: *prefix*. *GUID*. *Suffix*

- Prefix är tjänstens namn. Du kan välja ett eget prefix. När du har skapat aliaset kan du inte ändra det, så välj ditt prefix på lämpligt sätt.  
- GUID kommer att tillhandahållas av plattformen. Detta gör att namnet blir globalt unikt. 
- Suffixet läggs till av Azure: *region*. Azure. privatelinkservice 

Fullständigt alias:  *prefix*. {GUID}. *region*. Azure. privatelinkservice  

## <a name="control-service-exposure"></a>Kontrol lera tjänstens exponering

Med Private Link service kan du kontrol lera exponeringen för tjänsten via "synlighet"-inställningen. Du kan göra tjänsten privat för användning från olika virtuella nätverk som du äger (endast Azure RBAC-behörigheter), begränsa exponeringen till en begränsad uppsättning prenumerationer som du litar på, eller göra den offentlig så att alla Azure-prenumerationer kan begära anslutningar i den privata länk tjänsten. Dina Synlighets inställningar avgör om en konsument kan ansluta till din tjänst eller inte. 

## <a name="control-service-access"></a>Kontrol lera tjänst åtkomst

Konsumenter som har exponering (styrs av Synlighets inställningen) till din privata länk tjänst kan skapa en privat slut punkt i sina virtuella nätverk och begära en anslutning till din privata länk tjänst. Den privata slut punkts anslutningen kommer att skapas i ett väntande tillstånd för objektet för den privata länk tjänsten. Tjänste leverantören ansvarar för att agera på anslutningsbegäran. Du kan antingen godkänna anslutningen, avvisa anslutningen eller ta bort anslutningen. Endast anslutningar som godkänns kan skicka trafik till den privata länk tjänsten.

Åtgärden att godkänna anslutningarna kan automatiseras med hjälp av egenskapen för automatiskt godkännande i tjänsten Private Link. Automatiskt godkännande är en möjlighet för tjänste leverantörer att förgodkänna en uppsättning prenumerationer för automatisk åtkomst till tjänsten. Kunderna måste dela sina prenumerationer offline för att tjänst leverantörer ska kunna läggas till i listan med automatiskt godkännande. Automatiskt godkännande är en delmängd av Synlighets mat ris. Synlighet styr exponerings inställningarna medan automatiskt godkännande styr godkännande inställningarna för din tjänst. Om en kund begär en anslutning från en prenumeration i listan med automatiskt godkännande godkänns anslutningen automatiskt och anslutningen upprättas. Tjänste leverantörer behöver inte längre godkänna begäran manuellt. Å andra sidan, om en kund begär en anslutning från en prenumeration i Synlighets mat ris och inte i matrisen för automatiskt godkännande, kommer begäran att kontakta tjänst leverantören men tjänst leverantören måste godkänna anslutningarna manuellt.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Hämta anslutnings information med TCP-proxy v2

När du använder Private Link service, är käll-IP-adressen för de paket som kommer från privat slut punkt nätverks adressen översatt (NAT) på tjänst leverantörs sidan med den NAT-IP som tilldelas från leverantörens virtuella nätverk. Därför tar programmen emot den allokerade NAT-IP-adressen i stället för den faktiska käll-IP-adressen för tjänste konsumenter. Om ditt program behöver faktisk käll-IP-adress från konsument sidan, kan du aktivera Proxy-protokollet på tjänsten och hämta informationen från proxy-protokollets huvud. Förutom käll-IP-adressen bär även proxy Protocol-huvudet LinkID för den privata slut punkten. Kombinationen av käll-IP-adress och LinkID kan hjälpa tjänst leverantörer att identifiera sina konsumenter unikt. Mer information om proxy-protokoll finns [här](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Den här informationen kodas med en anpassad Vector-Vector (Type-Length-Value) enligt följande:

Anpassad TLV-information:

|Fält |Längd (oktetter)  |Beskrivning  |
|---------|---------|----------|
|Typ  |1        |PP2_TYPE_AZURE (0xEE)|
|Längd  |2      |Värdets längd|
|Värde  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 byte) som representerar LINKID för den privata slut punkten. Kodat i little endian format.|

 > [!NOTE]
 > Tjänste leverantören ansvarar för att se till att tjänsten bakom standard belastningsutjämnaren är konfigurerad för att parsa proxy-protokollets huvud enligt [specifikationen](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) när proxy-protokollet är aktiverat i Private Link service. Begäran Miss fungerar om inställningen proxy-protokoll är aktive rad i tjänsten för privata länkar men tjänst leverantörens tjänst inte har kon figurer ATS för att parsa huvudet. På samma sätt går det inte att utföra begäran om tjänst leverantörens tjänst förväntar sig ett proxy Protocol-huvud medan inställningen inte är aktive rad i den privata länk tjänsten. När proxy Protocol-inställningen är aktive rad inkluderas även protokollets huvud i HTTP/TCP-hälsosökningar från värd till Server delens virtuella datorer, även om det inte kommer att finnas någon klient information i huvudet. 

## <a name="limitations"></a>Begränsningar

Följande är de kända begränsningarna när du använder den privata länk tjänsten:
- Stöds endast på Standard Load Balancer 
- Stöder endast IPv4-trafik
- Stöder endast TCP-och UDP-trafik

## <a name="next-steps"></a>Nästa steg
- [Skapa en privat länk-tjänst med hjälp av Azure PowerShell](create-private-link-service-powershell.md)
- [Skapa en privat länk-tjänst med Azure CLI](create-private-link-service-cli.md)
