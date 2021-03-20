---
title: Nätverks säkerhet för Azure Event Hubs
description: Den här artikeln beskriver hur du konfigurerar åtkomst från privata slut punkter
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 9503fc26c22d7dbff13c5754288f577b7bb3242f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011004"
---
# <a name="network-security-for-azure-event-hubs"></a>Nätverks säkerhet för Azure Event Hubs 
I den här artikeln beskrivs hur du använder följande säkerhetsfunktioner med Azure Event Hubs: 

- Tjänsttaggar
- Regler för IP-brandvägg
- Nätverks tjänst slut punkter
- Privata slutpunkter


## <a name="service-tags"></a>Tjänsttaggar
En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras, vilket minimerar komplexiteten vid frekventa uppdateringar av nätverks säkerhets regler. Mer information om service märken finns i [Översikt över tjänst Taggar](../virtual-network/service-tags-overview.md).

Du kan använda service märken för att definiera nätverks åtkomst kontroller för [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md#security-rules) eller [Azure-brandväggen](../firewall/service-tags.md). Använd tjänst Taggar i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet (till exempel **EventHub**) i lämpligt *käll* -eller *mål* fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst.

| Tjänsttagg | Syfte | Kan använda inkommande eller utgående? | Kan regionala? | Kan använda med Azure-brandväggen? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure-Event Hubs. | Utgående | Ja | Ja |


## <a name="ip-firewall"></a>IP-brandvägg 
Som standard är Event Hubs-namnrymder tillgängliga från Internet så länge förfrågan levereras med giltig autentisering och auktorisering. Med IP-brandvägg kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adress intervall i [CIDR-notation (classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Den här funktionen är användbar i scenarier där Azure Event Hubs bör endast vara tillgängligt från vissa välkända webbplatser. Med brand Väggs regler kan du konfigurera regler för att acceptera trafik som kommer från vissa IPv4-adresser. Om du till exempel använder Event Hubs med [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), kan du skapa en **brand Väggs regel** som tillåter trafik från enbart lokala infrastruktur-IP-adresser. 

IP-brandväggens regler tillämpas på Event Hubs namn områdes nivå. Reglerna gäller därför för alla anslutningar från klienter som använder ett protokoll som stöds. Eventuella anslutnings försök från en IP-adress som inte matchar en tillåten IP-regel på Event Hubs namn området nekas som obehörig. Svaret innehåller ingen IP-regel. IP filter regler tillämpas i ordning och den första regeln som matchar IP-adressen avgör vilken åtgärd som godkänns eller nekas.

Mer information finns i [så här konfigurerar du IP-brandvägg för en Event Hub](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Nätverks tjänst slut punkter
Integreringen av Event Hubs [Virtual Network med tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) för virtuella nätverk ger säker åtkomst till meddelande funktioner från arbets belastningar, till exempel virtuella datorer som är kopplade till virtuella nätverk, med sökvägen till nätverks trafiken som skyddas i båda ändar.

När de har kon figurer ATS för att bindas till minst en tjänst slut punkt för virtuellt nätverk, accepterar respektive Event Hubs namn området inte längre trafik från var som helst men auktoriserade undernät i virtuella nätverk. I det virtuella nätverkets perspektiv binder du en Event Hubs namnrum till en tjänst slut punkt konfigurerar en isolerad nätverks tunnel från det virtuella nätverkets undernät till meddelande tjänsten. 

Resultatet är en privat och isolerad relation mellan arbets belastningarna som är kopplade till under nätet och respektive Event Hubs-namnrymd, trots att den observerade nätverks adressen för meddelande tjänstens slut punkt är i ett offentligt IP-adressintervall. Det finns ett undantag för det här beteendet. Genom att aktivera en tjänst slut punkt aktiverar som standard `denyall` regeln i [IP-brandväggen](event-hubs-ip-filtering.md) som är associerad med det virtuella nätverket. Du kan lägga till vissa IP-adresser i IP-brandväggen för att ge åtkomst till den offentliga slut punkten för Händelsehubben. 

> [!IMPORTANT]
> Virtuella nätverk stöds på **standardnivå** och **dedikerade** nivåer för Event Hubs. Det stöds inte på **Basic** -nivån.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade säkerhets scenarier som aktive ras av VNet-integrering 

Lösningar som kräver tätt och compartmentalized säkerhet, och där undernät för virtuella nätverk tillhandahåller segmentering mellan compartmentalized-tjänsterna, behöver fortfarande kommunikations vägar mellan tjänster som finns i dessa fack.

Alla omedelbara IP-vägar mellan avdelningarna, inklusive de som driver HTTPS över TCP/IP, medför risk för utnyttjande av sårbarheter från nätverks skiktet. Meddelande tjänster tillhandahåller isolerade kommunikations vägar, där meddelanden skrivs till disk som över gången mellan parter. Arbets belastningar i två distinkta virtuella nätverk som båda är kopplade till samma Event Hubs instans kan kommunicera effektivt och tillförlitligt via meddelanden, medan respektive gräns för nätverks isolerings gräns bevaras.
 
Det innebär att dina säkerhets känsliga moln lösningar inte bara får till gång till Azure-branschledande pålitliga och skalbara funktioner för asynkrona meddelanden, men de kan nu använda meddelande hantering för att skapa kommunikations vägar mellan säkra lösnings avdelningar som är mycket säkrare än vad som kan uppnås med valfri peer-to-peer-kommunikations läge, inklusive HTTPS och andra TLS-säkrade socket-protokoll.

### <a name="bind-event-hubs-to-virtual-networks"></a>Binda Event Hub till virtuella nätverk

**Virtuella nätverks regler** är säkerhets funktionen för brand vägg som styr om ditt Azure Event Hubs-namnområde accepterar anslutningar från ett visst virtuellt nätverks under nät.

Att binda ett Event Hubs namn område till ett virtuellt nätverk är en två stegs process. Du måste först skapa en **tjänst slut punkt för virtuellt nätverk** i ett virtuellt nätverks undernät och aktivera det för **Microsoft. EventHub** enligt beskrivningen i artikeln [Översikt över tjänstens slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) . När du har lagt till tjänst slut punkten binder du Event Hubs namn området till den med en **regel för virtuellt nätverk**.

Den virtuella nätverks regeln är en associering av Event Hubs-namnrymden med ett virtuellt nätverks under nät. Även om regeln finns beviljas alla arbets belastningar som är kopplade till under nätet åtkomst till Event Hubs namn området. Event Hubs aldrig upprättar utgående anslutningar, behöver inte få åtkomst och har därför aldrig beviljat åtkomst till ditt undernät genom att aktivera den här regeln.

Mer information finns i [Konfigurera tjänst slut punkter för virtuella nätverk för en Event Hub](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Privata slutpunkter

[Azure Private Link service](../private-link/private-link-overview.md) ger dig åtkomst till Azure-tjänster (till exempel Azure Event Hubs, Azure Storage och Azure Cosmos dB) och Azure-värdbaserade kund-/partner tjänster via en **privat slut punkt** i det virtuella nätverket.

En privat slut punkt är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute-eller VPN-anslutningar eller offentliga IP-adresser krävs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta nivån av granularitet i åtkomst kontroll.

> [!IMPORTANT]
> Den här funktionen stöds för både **standard** -och **dedikerade** nivåer. Det stöds inte på **Basic** -nivån.

Mer information finns i [så här konfigurerar du privata slut punkter för en Event Hub](private-link-service.md)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Så här konfigurerar du IP-brandvägg för en Event Hub](event-hubs-ip-filtering.md)
- [Konfigurera tjänst slut punkter för virtuella nätverk för en Event Hub](event-hubs-service-endpoints.md)
- [Konfigurera privata slut punkter för en Event Hub](private-link-service.md)