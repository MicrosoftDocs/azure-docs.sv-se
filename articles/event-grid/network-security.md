---
title: Nätverks säkerhet för Azure Event Grid resurser
description: Den här artikeln beskriver hur du använder service märken för utgående IP-brandvägg för ingångs-och privata slut punkter för inträngande med Azure Event Grid.
author: VidyaKukke
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: vkukke
ms.openlocfilehash: d58e8b5ce9fb444fa501f897cca722613d9c51fe
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967582"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Nätverks säkerhet för Azure Event Grid resurser
I den här artikeln beskrivs hur du använder följande säkerhetsfunktioner med Azure Event Grid: 

- Service märken för utgående
- Regler för IP-brandvägg för ingångs händelser
- Privata slut punkter för inkommande trafik


## <a name="service-tags"></a>Tjänsttaggar
En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras, vilket minimerar komplexiteten vid frekventa uppdateringar av nätverks säkerhets regler. Mer information om service märken finns i [Översikt över tjänst Taggar](../virtual-network/service-tags-overview.md).

Du kan använda service märken för att definiera nätverks åtkomst kontroller för [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md#security-rules) eller [Azure-brandväggen](../firewall/service-tags.md). Använd tjänst Taggar i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet (till exempel **AzureEventGrid**) i lämpligt *käll* -eller *mål* fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst.

| Tjänsttagg | Syfte | Kan använda inkommande eller utgående? | Kan regionala? | Kan använda med Azure-brandväggen? |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Azure Event Grid. | Båda | Inga | Inga |


## <a name="ip-firewall"></a>IP-brandvägg 
Azure Event Grid stöder IP-baserade åtkomst kontroller för publicering till ämnen och domäner. Med IP-baserade kontroller kan du begränsa utgivare till ett ämne eller en domän till endast en uppsättning godkända datorer och moln tjänster. Den här funktionen kompletterar de [autentiseringsmekanismer](security-authentication.md) som stöds av event Grid.

Som standard är ämne och domän tillgängligt från Internet så länge förfrågan levereras med giltig autentisering och auktorisering. Med IP-brandvägg kan du begränsa den ytterligare till en uppsättning IP-adresser eller IP-adressintervall i [CIDR-notation (classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Utgivare som härstammar från andra IP-adresser avvisas och får ett 403-svar (förbjuden).

Steg-för-steg-instruktioner för att konfigurera IP-brandvägg för ämnen och domäner finns i [Konfigurera IP-brandvägg](configure-firewall.md).

## <a name="private-endpoints"></a>Privata slutpunkter
Du kan använda [privata slut punkter](../private-link/private-endpoint-overview.md) för att tillåta ingress av händelser direkt från ditt virtuella nätverk till dina ämnen och domäner på ett säkert sätt över en [privat länk](../private-link/private-link-overview.md) utan att gå via det offentliga Internet. En privat slut punkt är ett särskilt nätverks gränssnitt för en Azure-tjänst i ditt VNet. När du skapar en privat slut punkt för ditt ämne eller din domän ger den säker anslutning mellan klienter i ditt VNet och din Event Grid-resurs. Den privata slut punkten tilldelas en IP-adress från det virtuella nätverkets IP-adressintervall. Anslutningen mellan den privata slut punkten och Event Grid tjänsten använder en säker privat länk.

![Arkitekturdiagram](./media/network-security/architecture-diagram.png)

Med hjälp av privata slut punkter för din Event Grid-resurs kan du:

- Säker åtkomst till ditt ämne eller din domän från ett VNet över Microsoft stamnät nätverket i stället för det offentliga Internet.
- Anslut säkert från lokala nätverk som ansluter till det virtuella nätverket med VPN eller Express vägar med privat peering.

När du skapar en privat slut punkt för ett ämne eller en domän i ditt VNet, skickas en medgivande förfrågan för godkännande till resurs ägaren. Om användaren som begär att den privata slut punkten ska skapas även är ägare till resursen, godkänns den här medgivande förfrågningen automatiskt. Annars är anslutningen i **vänte** läge tills den godkänts. Program i det virtuella nätverket kan ansluta till den Event Grid tjänsten över den privata slut punkten sömlöst med samma anslutnings strängar och auktoriseringsbeslut som de skulle använda i övrigt. Resurs ägare kan hantera medgivande förfrågningar och privata slut punkter via fliken **privata slut punkter** för resursen i Azure Portal.

### <a name="connect-to-private-endpoints"></a>Anslut till privata slut punkter
Utgivare i ett VNet som använder den privata slut punkten bör använda samma anslutnings sträng för ämnet eller domänen som klienter som ansluter till den offentliga slut punkten. DNS-matchning dirigerar automatiskt anslutningar från VNet till ämnet eller domänen via en privat länk. Event Grid skapar en [privat DNS-zon](../dns/private-dns-overview.md) som är kopplad till VNet med den nödvändiga uppdateringen för privata slut punkter som standard. Men om du använder en egen DNS-server kan du behöva göra ytterligare ändringar i DNS-konfigurationen.

### <a name="dns-changes-for-private-endpoints"></a>DNS-ändringar för privata slut punkter
När du skapar en privat slut punkt uppdateras DNS CNAME-posten för resursen till ett alias i en under domän med prefixet `privatelink` . Som standard skapas en privat DNS-zon som motsvarar den privata länkens under domän. 

När du löser ämnet eller domänens slut punkts-URL från utanför det virtuella nätverket med den privata slut punkten matchas den offentliga slut punkten för tjänsten. DNS-resursposterna för ",", när de matchas från **utanför det virtuella** nätverket som är värd för den privata slut punkten, blir:

| Namn                                          | Typ      | Värde                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

Du kan neka eller kontrol lera åtkomsten för en klient utanför VNet via den offentliga slut punkten med hjälp av [IP-brandväggen](#ip-firewall). 

Vid matchning från det VNet som är värd för den privata slut punkten matchas ämnet eller domänens slut punkts-URL till den privata slut punktens IP-adress. DNS-resursposterna för ämnet "ämnea", när de löses in i **det virtuella nätverk** som är värd för den privata slut punkten, kommer att vara:

| Namn                                          | Typ      | Värde                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Den här metoden ger åtkomst till ämnet eller domänen med samma anslutnings sträng för klienter på det virtuella nätverk som är värd för privata slut punkter och klienter utanför VNet.

Om du använder en anpassad DNS-server i nätverket kan klienterna matcha FQDN för ämnet eller domän slut punkten till den privata slut punktens IP-adress. Konfigurera DNS-servern så att den delegerar din privata länk under domän till den privata DNS-zonen för det virtuella nätverket eller konfigurera A-posterna för `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` med den privata slut punktens IP-adress.

Namnet på den rekommenderade DNS-zonen är `privatelink.eventgrid.azure.net` .

### <a name="private-endpoints-and-publishing"></a>Privata slut punkter och publicering

I följande tabell beskrivs olika tillstånd för den privata slut punkts anslutningen och effekterna av publicering:

| Anslutnings status   |  Publiceringen lyckades (Ja/Nej) |
| ------------------ | -------------------------------|
| Godkända           | Ja                            |
| Avslagen           | Inga                             |
| Väntar            | Inga                             |
| Frånkopplad       | Inga                             |

För att publiceringen ska lyckas bör anslutnings statusen för den privata slut punkten **godkännas**. Om en anslutning avvisas kan den inte godkännas med hjälp av Azure Portal. Den enda möjligheten är att ta bort anslutningen och skapa en ny i stället.

## <a name="pricing-and-quotas"></a>Priser och kvoter
**Privata slut punkter** finns på både Basic-och Premium-nivån för Event Grid. Event Grid tillåter att upp till 64 privata slut punkts anslutningar skapas per ämne eller domän. 

Funktionen **IP-brandvägg** finns på både Basic-och Premium-nivån för Event Grid. Vi tillåter att upp till 16 IP-brandväggar skapas per ämne eller domän.

## <a name="next-steps"></a>Nästa steg
Du kan konfigurera IP-brandväggen för din Event Grid resurs för att begränsa åtkomsten via det offentliga Internet från endast en Välj uppsättning IP-adresser eller IP-adressintervall. Stegvisa instruktioner finns i [Konfigurera IP-brandvägg](configure-firewall.md).

Du kan konfigurera privata slut punkter för att begränsa åtkomsten från enbart från valda virtuella nätverk. Stegvisa instruktioner finns i [Konfigurera privata slut punkter](configure-private-endpoints.md).

Information om hur du felsöker problem med nätverks anslutningen finns i [Felsöka problem med nätverks anslutningen](troubleshoot-network-connectivity.md)
