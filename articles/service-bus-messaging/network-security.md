---
title: Nätverks säkerhet för Azure Service Bus
description: I den här artikeln beskrivs funktioner för nätverks säkerhet, till exempel service märken, IP-brandvägg, tjänst slut punkter och privata slut punkter.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: db0dd89d1f902699c27b724609505ba681757454
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92310467"
---
# <a name="network-security-for-azure-service-bus"></a>Nätverks säkerhet för Azure Service Bus 
I den här artikeln beskrivs hur du använder följande säkerhetsfunktioner med Azure Service Bus: 

- Tjänsttaggar
- Regler för IP-brandvägg
- Nätverks tjänst slut punkter
- Privata slutpunkter


## <a name="service-tags"></a>Tjänsttaggar
En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras, vilket minimerar komplexiteten vid frekventa uppdateringar av nätverks säkerhets regler. Mer information om service märken finns i [Översikt över tjänst Taggar](../virtual-network/service-tags-overview.md).

Du kan använda service märken för att definiera nätverks åtkomst kontroller för [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md#security-rules) eller [Azure-brandväggen](../firewall/service-tags.md). Använd tjänst Taggar i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet (till exempel **Service Bus**) i lämpligt *käll* -eller *mål* fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst.

| Tjänsttagg | Syfte | Kan använda inkommande eller utgående? | Kan regionala? | Kan använda med Azure-brandväggen? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Azure Service Bus trafik som använder Premium-tjänstens nivå. | Utgående | Ja | Ja |


> [!NOTE]
> Du kan endast använda service märken för **Premium** -namnområden. Om du använder ett **standard** namn område använder du den IP-adress som du ser när du kör följande kommando: `nslookup <host name for the namespace>` . Exempel: `nslookup contosons.servicebus.windows.net`. 

## <a name="ip-firewall"></a>IP-brandvägg 
Som standard är Service Bus-namnrymder tillgängliga från Internet så länge förfrågan levereras med giltig autentisering och auktorisering. Med IP-brandvägg kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adress intervall i [CIDR-notation (classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Den här funktionen är användbar i scenarier där Azure Service Bus bör endast vara tillgängliga från vissa välkända webbplatser. Med brand Väggs regler kan du konfigurera regler för att acceptera trafik som kommer från vissa IPv4-adresser. Om du till exempel använder Service Bus med [Azure Express Route] [Express-Route], kan du skapa en **brand Väggs regel** för att tillåta trafik från enbart den lokala infrastrukturens IP-adresser eller adresser för en Corporate NAT-gateway. 

IP-brandväggens regler tillämpas på Service Bus namn områdes nivå. Reglerna gäller därför för alla anslutningar från klienter som använder ett protokoll som stöds. Eventuella anslutnings försök från en IP-adress som inte matchar en tillåten IP-regel på Service Bus namn området nekas som obehörig. Svaret innehåller ingen IP-regel. IP filter regler tillämpas i ordning och den första regeln som matchar IP-adressen avgör vilken åtgärd som godkänns eller nekas.

Mer information finns i [så här konfigurerar du IP-brandvägg för en Service Bus namnrymd](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Nätverks tjänst slut punkter
Integreringen av Service Bus med [tjänst slut punkter för virtuella datorer med Virtual Network (VNet)](service-bus-service-endpoints.md) ger säker åtkomst till meddelande funktioner från arbets belastningar som virtuella datorer som är kopplade till virtuella nätverk, med den nätverks trafik väg som skyddas i båda ändar.

När den har kon figurer ATS för att bindas till minst en tjänst slut punkt för ett virtuellt nätverk, kommer Service Bus namn området inte längre att acceptera trafik från var som helst, men auktoriserade virtuella nätverk (n). I det virtuella nätverkets perspektiv binder du en Service Bus namnrum till en tjänst slut punkt konfigurerar en isolerad nätverks tunnel från det virtuella nätverkets undernät till meddelande tjänsten.

Resultatet är en privat och isolerad relation mellan arbets belastningarna som är kopplade till under nätet och respektive Service Bus-namnrymd, trots att den observerade nätverks adressen för meddelande tjänstens slut punkt är i ett offentligt IP-adressintervall.

> [!IMPORTANT]
> Virtuella nätverk stöds endast på [Premium-nivå](service-bus-premium-messaging.md) Service Bus namn områden.
> 
> När du använder VNet-tjänstens slut punkter med Service Bus bör du inte aktivera de här slut punkterna i program som blandar standard-och Premium-nivån Service Bus namn områden. Eftersom standard nivån inte stöder virtuella nätverk. Slut punkten är begränsad till endast Premium-nivåns namn område.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade säkerhets scenarier som aktive ras av VNet-integrering 

Lösningar som kräver tätt och compartmentalized säkerhet, och där undernät för virtuella nätverk tillhandahåller segmentering mellan compartmentalized-tjänsterna, behöver vanligt vis kommunikations vägar mellan tjänster som finns i dessa fack.

Alla omedelbara IP-vägar mellan avdelningarna, inklusive de som driver HTTPS över TCP/IP, medför risk för utnyttjande av sårbarheter från nätverks skiktet. Meddelande tjänster tillhandahåller fullständigt isolerade kommunikations vägar, där meddelanden är jämnt skrivna till disk när de övergår mellan parter. Arbets belastningar i två distinkta virtuella nätverk som båda är kopplade till samma Service Bus instans kan kommunicera effektivt och tillförlitligt via meddelanden, medan respektive gräns för nätverks isolerings gräns bevaras.
 
Det innebär att dina säkerhets känsliga moln lösningar inte bara får till gång till Azure-branschledande pålitliga och skalbara funktioner för asynkrona meddelanden, men de kan nu använda meddelande hantering för att skapa kommunikations vägar mellan säkra lösnings avdelningar som är mycket säkrare än vad som kan uppnås med valfri peer-to-peer-kommunikations läge, inklusive HTTPS och andra TLS-säkrade socket-protokoll.

### <a name="bind-service-bus-to-virtual-networks"></a>Binda Service Bus till virtuella nätverk

*Virtuella nätverks regler* är brand Väggs säkerhetsfunktionen som styr om din Azure Service Bus-Server accepterar anslutningar från ett visst virtuellt nätverks under nät.

Att binda ett Service Bus namn område till ett virtuellt nätverk är en två stegs process. Du måste först skapa en **Virtual Network tjänst slut punkt** i ett Virtual Network undernät och aktivera den för **Microsoft. Service Bus** enligt beskrivningen i [Översikt över tjänstens slut punkt](service-bus-service-endpoints.md). När du har lagt till tjänst slut punkten binder du Service Bus namn området till den med en **regel för virtuellt nätverk**.

Den virtuella nätverks regeln är en associering av Service Bus-namnrymden med ett virtuellt nätverks under nät. Även om regeln finns beviljas alla arbets belastningar som är kopplade till under nätet åtkomst till Service Bus namn området. Service Bus själva upprättar aldrig utgående anslutningar, behöver inte få åtkomst och har därför aldrig beviljat åtkomst till ditt undernät genom att aktivera den här regeln.

Mer information finns i [Konfigurera tjänst slut punkter för virtuella nätverk för ett Service Bus-namnområde](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Privata slutpunkter

Azure Private Link service ger dig åtkomst till Azure-tjänster (till exempel Azure Service Bus, Azure Storage och Azure Cosmos DB) och Azure-värdbaserade kund-/partner tjänster via en **privat slut punkt** i det virtuella nätverket.

En privat slut punkt är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute-eller VPN-anslutningar eller offentliga IP-adresser krävs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta nivån av granularitet i åtkomst kontroll.

Mer information finns i [Vad är en privat Azure-länk?](../private-link/private-link-overview.md)

> [!NOTE]
> Den här funktionen stöds med **Premium** -nivån för Azure Service Bus. Mer information om Premium-nivån finns i artikeln [Service Bus Premium-och standard meddelande nivåer](service-bus-premium-messaging.md) .


Mer information finns i [så här konfigurerar du privata slut punkter för ett Service Bus-namnområde](private-link-service.md)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Konfigurera IP-brandvägg för ett Service Bus-namnområde](service-bus-ip-filtering.md)
- [Konfigurera tjänst slut punkter för virtuella nätverk för ett Service Bus-namnområde](service-bus-service-endpoints.md)
- [Konfigurera privata slut punkter för ett Service Bus-namnområde](private-link-service.md)